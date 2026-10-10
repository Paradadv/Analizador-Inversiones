# Analizador de inversiones

Proyecto para comparar el riesgo y el retorno de algunas acciones (AAPL, MSFT, KO) contra el S&P 500 (SPY) entre 2020 y 2023. Los datos se bajan de Yahoo Finance con Python y los resultados se ven en un dashboard de Power BI.

## Estructura

```
notebooks/01_analisis_activos.ipynb    descarga, limpieza, metricas y exportacion
data/processed/                        aca se genera el Excel (no se sube al repo)
dashboards/analizador_inversiones.pbix dashboard de Power BI
```

## Como correrlo

```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Despues se abre el notebook y se ejecutan todas las celdas. Eso crea `data/processed/datos_historicos_bolsa.xlsx` con tres hojas:

- `Precios_Historicos`: precio de cierre ajustado diario de cada activo.
- `Resumen_Sharpe`: metricas por activo.
- `Correlaciones`: correlacion entre los retornos diarios.

El dashboard lee ese Excel, asi que hay que correr el notebook antes de abrir el `.pbix`. Si la ruta no coincide, se cambia en Power BI desde Transformar datos > Configuracion de origen de datos.

## Metricas

| Metrica | Como se calcula |
|---|---|
| Retorno anual | retorno diario promedio x 252 |
| CAGR | crecimiento anual compuesto entre el primer y el ultimo precio |
| Volatilidad anual | desviacion estandar diaria x raiz de 252 |
| Ratio de Sharpe | (retorno anual - tasa libre de riesgo) / volatilidad |
| Max drawdown | peor caida desde un maximo anterior |
| Beta | covarianza con SPY / varianza de SPY |

Como tasa libre de riesgo se usa el promedio del periodo de los T-bills a 13 semanas (`^IRX`), que para 2020-2023 da 1.84%.

## Resultados (2020-2023)

| Activo | Retorno anual | Volatilidad | Sharpe | CAGR | Max drawdown | Beta |
|---|---|---|---|---|---|---|
| AAPL | 29.9% | 33.6% | 0.84 | 27.5% | -31.4% | 1.21 |
| MSFT | 27.6% | 32.6% | 0.79 | 24.9% | -37.1% | 1.19 |
| SPY | 13.7% | 22.6% | 0.52 | 11.8% | -33.7% | 1.00 |
| KO | 7.4% | 22.4% | 0.25 | 5.0% | -37.0% | 0.67 |

AAPL y MSFT dieron bastante mas retorno por unidad de riesgo que el indice, aunque se mueven mas que el mercado (beta mayor a 1). KO casi no le gano a la tasa libre de riesgo en el periodo, pero es la menos correlacionada con el resto (alrededor de 0.47 con AAPL y MSFT), asi que sirve para diversificar.

## Dashboard

Tiene dos paginas:

- **Monitoreo**: evolucion de los precios de los cuatro activos.
- **Comparativa**: tabla con retorno, volatilidad y Sharpe, tarjetas con los valores y un filtro por activo.

Las fechas y los activos se cambian en la primera celda del notebook (`tickers`, `fecha_inicio`, `fecha_fin`). Si se cambian los tickers hay que ajustar tambien los visuales del dashboard, porque usan las columnas de cada activo.
