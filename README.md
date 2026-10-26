# Reto B · Pronóstico del IPAP-BMC

Pronóstico mensual del Índice de Precios Agropecuarios de la Bolsa Mercantil de Colombia (Total agropecuario), con prueba histórica de 12 meses (MAPE) y pronóstico a 12 meses que incluye octubre de 2026.

## Contenido

|Archivo|Descripción|
|-|-|
|`IPAP\_RetoB\_Colab.ipynb`|Código completo: datos, modelos, prueba histórica, diagnóstico, pronóstico y gráficas|
|`requirements.txt`|Dependencias de Python|
|`data/`|Colocar aquí los dos archivos de la BMC (ver abajo)|

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

## Modelo

Promedio simple de cuatro componentes sobre ln(IPAP):

1. ARIMA(1,1,0) con deriva.
2. Estacional (efecto promedio de cada mes).
3. Telaraña: regresión ridge directa por horizonte con momentum del IPAP e índice mediano de precios de transacciones BMC.
4. Bottom-up sectorial: ETS amortiguado por sector + pesos NNLS.

Línea base: modelo ingenuo (último valor observado).

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

