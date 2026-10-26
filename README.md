# Data Intelligence Challenge: ETL y analítica de datos empresariales

Proyecto individual de la **Learning Activity 3 (Tercer periodo)** de Ingeniería de Sistemas, Universidad EAN. Aplica procesos **ETL** (extraer, transformar y cargar) a cuatro conjuntos de datos de tres organizaciones, calcula indicadores verificables y formula decisiones gerenciales con base en los resultados.

| Organización | Sector | Archivo | Herramienta |
|---|---|---|---|
| NovaMarket | Comercio minorista | `01_NovaMarket_Ventas.csv` | Power BI |
| VidaPlus | Salud | `02_VidaPlus_Atenciones.csv` | Power BI |
| RutaExpress | Logística | `03_RutaExpress_Envios.csv` | Python |
| NovaMarket | Comercio minorista | `04_NovaMarket_Clientes.csv` | Python |

Cada archivo tiene 36 registros (144 en total) con anomalías intencionales que se identificaron, trataron y documentaron **sin inventar valores**: lo inválido se marcó como faltante (`NaN` o `null`) o se etiquetó.

## Estructura del repositorio

```
act3-data-intelligence/
├── data/
│   ├── original/     # CSV originales, sin modificar
│   └── limpio/       # CSV depurados (resultado del ETL)
├── notebooks/        # Notebooks de Python con código comentado y salidas
│   ├── ej3_rutaexpress.ipynb
│   └── ej4_clientes.ipynb
├── graficos/         # Gráficos generados por los notebooks (PNG)
├── powerbi/          # Archivos .pbix de los ejercicios 1 y 2
├── informe/          # Informe final y capturas numeradas (E1.1 a E4.4)
└── README.md
```

## Proceso ETL aplicado

1. **Extraer:** importar el CSV, revisar filas, columnas, tipos y faltantes, y conservar el estado original.
2. **Transformar:** normalizar textos, convertir tipos, validar rangos, marcar anomalías con un criterio explícito y crear columnas de análisis.
3. **Cargar:** Power BI con *Cerrar y aplicar* al modelo; Python con exportación de un CSV depurado a `data/limpio/`.
4. **Analizar y comunicar:** indicadores, gráficos y una decisión justificada con cifras.

Los originales nunca se modifican, así que cada cambio es trazable: Power Query conserva los *Pasos aplicados*, los notebooks conservan el código y se usan columnas de marca (`Discrepancia`, `Validacion`, `Categoria`) en lugar de borrar filas.

## Anomalías detectadas y tratamiento

| Ejercicio | Registro | Anomalía | Tratamiento |
|---|---|---|---|
| 1 Ventas | V009, V028 | Región y canal con espacios o minúsculas | Recortar y capitalizar |
| 1 Ventas | V020 | Ingreso registrado distinto del esperado (+25.000 COP) | Marcar en `Discrepancia`, sin corregir |
| 2 VidaPlus | A006 | Sede `centro ` con espacio y minúscula | Normalizar |
| 2 VidaPlus | A018 | Tiempo de espera vacío | Dejar `null`; excluir del promedio |
| 2 VidaPlus | A026 | Satisfacción 6 (fuera de 1 a 5) | Marcar como inválida |
| 3 RutaExpress | E010 | Zona ` urbana ` con espacios y minúscula | Normalizar |
| 3 RutaExpress | E022 | Costo negativo (−5.000 COP) | Marcar como `NaN` |
| 3 RutaExpress | E030 | `Dias_Reales` vacío | Retraso en `NaN` |
| 4 Clientes | C011 | Ciudad `bogotá ` | Normalizar |
| 4 Clientes | C021 | Gasto vacío | `NaN`, categoría *No clasificable* |
| 4 Clientes | C032 | `Compras_90d` = −1 | `NaN`, categoría *No clasificable* |

Detalle técnico en Power BI: al leer `Ingreso_Registrado` con la configuración regional de Colombia, el punto decimal (`532000.0`) se interpretaba como separador de miles y el total salía 10 veces mayor. Se corrigió leyendo la columna con cultura `en-US`.

## Resultados principales

**Ejercicio 1: NovaMarket, ventas**
- Ingreso esperado 12.723.750 COP, registrado 12.748.750 COP, diferencia +25.000 COP (1 fila: V020).
- Top 3 productos: Monitor (4.455.000), Mouse (3.251.250) y Teclado (2.992.500).
- Región líder: Centro (5.114.500). Canal líder: Distribuidor (5.114.500).

**Ejercicio 2: VidaPlus, oportunidad del servicio**
- 36 atenciones: 24 atendidas y 12 canceladas.
- Espera promedio: Sur 51,0 · Centro 37,8 · Norte 33,0 minutos.
- 51,4 % de esperas mayores a 40 minutos (18 de 35 con tiempo válido).
- Satisfacción promedio: Consulta 3,25 · Laboratorio 3,08 · Urgencias 2,64.
- Prioridad: sede Sur y Urgencias.

**Ejercicio 3: RutaExpress, retrasos y costos**
- 35 envíos con tiempos válidos: 14 a tiempo y 21 retrasados (60,0 %); retraso promedio de 1,20 días.
- Retrasos por transportista: T1 63,6 %, T2 58,3 %, T3 58,3 %.
- Costo promedio válido: Mixta 29.500 · Rural 26.000 · Urbana 23.577 COP.
- T3 tiene sus 12 envíos devueltos, y es la prioridad del plan de mejora.

**Ejercicio 4: NovaMarket, clientes**
- Alto valor 14 · Otros 15 · En seguimiento 5 · No clasificable 2.
- Gasto total válido 9.880.000 COP; promedio 282.286 COP sobre 35 clientes.
- Los 14 clientes de Alto valor concentran el 69,6 % del gasto.
- Mejor oportunidad de campaña: C006, C014, C022 y C030 (7 compras y 245.000 COP, a 5.000 COP del umbral de Alto valor).

## Cómo ejecutar los notebooks

Requisitos: Python 3.10 o superior.

```bash
pip install pandas numpy matplotlib jupyter
cd notebooks
jupyter notebook
```

Abre `ej3_rutaexpress.ipynb` o `ej4_clientes.ipynb` y ejecuta las celdas en orden. Los notebooks usan rutas relativas (`../data/original/...`), por eso deben ejecutarse desde la carpeta `notebooks/`. En Google Colab, sube los CSV y ajusta las rutas.

Los ejercicios 1 y 2 se abren con **Power BI Desktop** (Windows) desde la carpeta `powerbi/`.

## Informe

El informe completo (objetivo, desarrollo de los cuatro ejercicios, tabla comparativa de calidad de datos, respuestas a las tres preguntas ETL, conclusiones y recomendaciones) está en la carpeta `informe/`.

## Tecnologías

Python · pandas · NumPy · matplotlib · Jupyter · Power BI Desktop · Power Query · DAX

## Autor

Daniel · Ingeniería de Sistemas, Universidad EAN (Bogotá, Colombia)

> Los conjuntos de datos fueron suministrados por el curso con fines académicos.
