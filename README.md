# Buenas Prácticas en Power BI: Uso de Variables y Filtros Acumulativos

## Descripción

En este documento se explica cómo utilizar **medidas robustas** en Power BI aplicando **filtros acumulativos** para depurar datos desde el inicio. Esta lógica facilita el manejo de grandes volúmenes de datos, mejora el rendimiento y hace más flexible el proceso de añadir condiciones adicionales a lo largo del análisis.

## Objetivo

El objetivo es mostrar cómo trabajar con filtros acumulativos en DAX para crear medidas que operen sobre un conjunto de datos más manejable, simplificando el análisis y evitando errores al agregar condiciones extra.

## Beneficios

- **Medidas robustas y eficientes**: Utilizando variables y filtros acumulativos, tus medidas se vuelven más fáciles de mantener y comprender.
- **Filtros desde el principio**: Al depurar los datos desde el inicio, el análisis se realiza sobre un conjunto de datos más pequeño y manejable.
- **Escalabilidad**: La aplicación de filtros acumulativos permite agregar nuevas condiciones sin complicar el modelo de datos o la fórmula.
- **Reducción de errores**: Al tener pasos bien definidos en las variables, se minimizan posibles errores y se facilita el mantenimiento.

## Ejemplo de DAX


### 1. **Medida Base: `Medida.TotalPlatos`**
```DAX
Medida.TotalPlatos = SUM('VentasPlatos'[Cantidad])
```

### 2. **Medida filtrada: `Medida.FiltradaAvanzada`**
```DAX
Medida.FiltradaAvanzada = 

// Paso 1: Filtro general para depurar datos
VAR FiltroGeneral = 
    FILTER(
        'VentasPlatos',
        'VentasPlatos'[Estado] = "Servido" &&
        NOT('VentasPlatos'[Plato] IN { "Pizza", "Ensalada" })
    )

// Paso 2: Filtrar registros por sección del restaurante
VAR FiltroSeccion = 
    FILTER(
        FiltroGeneral,
        NOT('VentasPlatos'[Sección] = "Terraza")
    )

// Paso 3: Filtrar por un rango de cantidades (mínimo 20 platos vendidos)
VAR FiltroCantidadMinima = 
    FILTER(
        FiltroSeccion,
        'VentasPlatos'[Cantidad] >= 20
    )

// Paso 4: Resultado final utilizando una medida base
RETURN 
CALCULATE(
    [Medida.TotalPlatos], // Medida base
    FiltroCantidadMinima
)

```
## Explicación de la Lógica

*FiltroGeneral:* Excluye los platos "Pizza" y "Ensalada" y solo incluye pedidos con estado "Servido".

*FiltroSeccion:* Excluye los pedidos realizados en la sección "Terraza".

*FiltroCantidadMinima:* Solo considera los pedidos donde la cantidad vendida es mayor o igual a 20 unidades.

*Resultado Final:* Utiliza la medida [Medida.TotalPlatos] para calcular el total de platos vendidos sobre el conjunto final de datos depurados.

| Plato       | Sección   | Estado    | Cantidad |
|-------------|-----------|-----------|----------|
| Pasta       | Salón     | Servido   | 20       |
| Sopa        | Salón     | Servido   | 25       |
| Hamburguesa | Salón     | Servido   | 30       |

## Conclusión
Esta lógica asegura que las medidas en tu tablero sean robustas y eficientes, permitiéndote trabajar con un conjunto de datos depurado desde el principio. Al aplicar filtros acumulativos de manera organizada, se facilita la incorporación de nuevas condiciones sin generar confusión o cálculos redundantes. 
Esto no solo simplifica el mantenimiento, sino que también reduce posibles errores, haciendo que tu tablero sea más flexible y fácil de escalar.
