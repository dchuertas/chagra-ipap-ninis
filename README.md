# Reto B · Pronóstico del IPAP-BMC

Pronóstico mensual del Índice de Precios Agropecuarios de la Bolsa Mercantil de Colombia (Total agropecuario), con prueba histórica de 12 meses (MAPE) y pronóstico a 12 meses que incluye octubre de 2026.

**Resultado clave:** MAPE de **2,98 %** en la prueba histórica (35 % menor que el modelo ingenuo, 4,62 %) y pronóstico para **octubre de 2026 de 252,93 puntos** (banda del 80 %: 240,95 – 272,44).

## Contenido

|Archivo|Descripción|
|-|-|
|`IPAP\_RetoB\_Colab.ipynb`|Código completo: datos, modelos, prueba histórica, diagnóstico, pronóstico y gráficas|
|`requirements.txt`|Dependencias de Python|
|`data/`|Colocar aquí los dos archivos de la BMC (ver abajo)|
| `informe/` | Informe completo (`Informe_IPAP_RetoB.docx`) y figuras usadas en este README |
| `outputs/` | Resultados generados por el notebook (detalle abajo) |
| `outputs/Resultados_IPAP_RetoB.xlsx` | Todas las tablas de resultados en un solo Excel |
| `outputs/prueba_historica.csv` | Real, predicho y error por mes (ago-2025 a jul-2026) |
| `outputs/pronostico_futuro.csv` | Pronóstico a 12 meses con banda del 80 % |
| `outputs/fig1_datos.png` | Gráfica de los datos |
| `outputs/fig2_prueba.png` | Gráfica de la prueba histórica |
| `outputs/fig3_ingenuo.png` | Gráfica de comparación con el modelo ingenuo |
| `outputs/fig4_pronostico.png` | Gráfica del pronóstico a 12 meses |
| `informe/` | Informe completo (`Informe_IPAP_RetoB.docx`), informe adicional (`Informe_Adicional_IPAP_RetoB.docx`) y figuras usadas en este README |

## Tabla de contenidos
 
1. [Objetivo](#objetivo)
2. [Datos](#datos-bmc)
3. [Cómo ejecutar](#cómo-ejecutar)
4. [Metodología](#metodología)
5. [Salidas](#salidas-outputs)

## Objetivo
Pronosticar el IPAP-BMC Total agropecuario a 12 meses, incluido octubre de 2026, con un modelo reproducible cuya precisión se verifica en una prueba histórica sobre los últimos 12 meses observados. El pronóstico anticipa la dirección y la magnitud de los precios agropecuarios para apoyar decisiones de compra, venta y cobertura en el mercado.

## Datos (BMC)

1. `Comparativo niveles IPAP por sector.xlsx`: IPAP total y 32 sectores, ene-2012 a jul-2026.
2. `Solicitud de información Datalab Challenge - Los Ninis.xlsx`: transacciones (cantidades y valor negociado), ene-2014 a sep-2026.

## Cómo ejecutar

**Google Colab (recomendado):**

1. Abrir `IPAP\_RetoB\_Colab.ipynb` en Colab.
2. Arrastrar los dos Excel al panel de archivos 📁.
3. `Entorno de ejecución → Ejecutar todo` (≈ 2 minutos).
4. otra manera de hacerlo es correr linea por linea y subir las bases de datos en la sección 2 y 3 

**Local:**

```bash
pip install -r requirements.txt
jupyter notebook IPAP\_RetoB\_Colab.ipynb   # con los dos Excel en la misma carpeta
```

## Metodología
 
Todos los modelos trabajan con el logaritmo del índice, de modo que los cambios son porcentuales y el pronóstico nunca es negativo:

$$\ell_t = \ln(\mathrm{IPAP}_t), \qquad \Delta\ell_t = \ell_t - \ell_{t-1} \qquad (1)$$

### Línea base: modelo ingenuo
 
Repite el último valor observado en todos los meses futuros. Equivale a suponer que el IPAP sigue una caminata aleatoria, donde el mejor pronóstico es el dato de hoy. Es el estándar mínimo en pronóstico de precios: un modelo solo aporta valor si se equivoca menos que el ingenuo.
 
$$\widehat{\mathrm{IPAP}}_{T+h} = \mathrm{IPAP}_T, \qquad h = 1,\dots,12 \qquad (2)$$

## Chagra: modelo final

Promedio simple de cuatro componentes sobre ln(IPAP):

1. ARIMA(1,1,0) con deriva.
2. Estacional (efecto promedio de cada mes).
3. Telaraña: regresión ridge directa por horizonte con momentum del IPAP e índice mediano de precios de transacciones BMC.
4. Bottom-up sectorial: ETS amortiguado por sector + pesos NNLS.

**Combinación.** El pronóstico final es el promedio simple de los cuatro componentes. Cada uno capta un rasgo distinto (tendencia, estacionalidad, ciclo y mercado, sectores) y cuando uno sobreestima otro suele subestimar; los pesos iguales no se estiman, así que no sobreajustan el pasado:
 
$$\widehat{\mathrm{IPAP}}_{T+h} = \tfrac14\left(\hat y^{ARIMA}_{T+h} + \hat y^{Est}_{T+h} + \hat y^{Tel}_{T+h} + \hat y^{BU}_{T+h}\right) \qquad (17)$$
 
## Resultados

||Modelo final|Ingenuo|
|-|-|-|
|MAPE prueba histórica (ago-2025 a jul-2026)|**2,98 %**|4,62 %|
|Pronóstico octubre 2026|**252,93**|—|

## Salidas (`outputs/`)

* `prueba\_historica.csv`: real, predicho y error por mes.
* `pronostico\_futuro.csv`: 12 meses con banda del 80 %.
* `Resultados\_IPAP\_RetoB.xlsx`: todas las tablas.
* `figuras/`: gráficas.

