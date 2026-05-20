# 📈 Curvas de Lactancia — Modelo Wood

Aplicación web independiente para ajustar el **Modelo Gamma Incompleto de Wood (1967)** a datos de producción de leche bovina. Desarrollada para uso docente e investigativo en sistemas de producción lechera especializada.

> Desarrollada por el [Grupo Leche UdeA](https://www.udea.edu.co) · Departamento de Producción Agropecuaria · Facultad de Ciencias Agrarias · Universidad de Antioquia

---

## ¿Qué hace?

Dado un conjunto de observaciones de producción de leche a lo largo de la lactancia, la app estima los parámetros del modelo:

$$Y(t) = a \cdot t^{b} \cdot e^{-ct}$$

| Parámetro | Significado |
|-----------|-------------|
| **a** | Nivel de producción al inicio de la lactancia |
| **b** | Tasa de aceleración hacia el pico (fase ascendente) |
| **c** | Tasa de declinación post-pico (fase descendente) |
| **t_pico** = b/c | Día o semana en que se alcanza la producción máxima |
| **y_max** = a·(b/c)^b·e^−b | Producción estimada en el pico |
| **s** = −(b+1)·ln(c) | Índice de persistencia de Wood |

El ajuste se realiza por **mínimos cuadrados linealizados** mediante la transformación:

$$\ln(Y) = \ln(a) + b \cdot \ln(t) - c \cdot t$$

lo que reduce el problema a una regresión lineal múltiple resuelta con eliminación gaussiana con pivoteo parcial — sin dependencias externas de optimización.

---

## Características

- **Sin instalación** — un único archivo `.html` que corre en cualquier navegador moderno
- **Sin dependencias de servidor** — toda la lógica corre en el cliente (JavaScript puro)
- **Múltiples vacas** en pestañas independientes dentro de la misma sesión
- **Dos modos de entrada de tiempo:**
  - DEL numérico (días o semanas en leche)
  - Fechas de control lechero + fecha de parto (calcula DEL automáticamente)
- **Pega desde Excel** — acepta columnas separadas por tabulador, coma o punto y coma; admite coma o punto como separador decimal
- **Resultados:**
  - Ecuación ajustada con coeficientes
  - Tarjetas de parámetros: a, b, c, t\_pico, y\_max, s
  - Métricas de bondad de ajuste: R² (escala original), RMSE, n
  - Gráfica interactiva: datos observados + curva ajustada + línea vertical en el pico
  - Tabla de interpretación con rangos de referencia para Holstein y BON×Holstein en trópico alto colombiano
- **Exportar CSV** con parámetros, valores ajustados y residuos por observación
- **Dato de ejemplo** precargable para explorar la app de inmediato

---

## Uso rápido

1. Descargue el archivo `curvas_lactancia_wood.html`
2. Ábralo en Chrome, Firefox, Edge o Safari
3. Pegue sus datos en el área de texto (dos columnas: DEL y producción)
4. Presione **▶ Ajustar curva de Wood**

### Formato de entrada esperado

Dos columnas separadas por tab, coma o punto y coma. La primera columna es el período (DEL en días o semanas); la segunda, la producción de leche (L/día, L/semana o kg/día).

```
7     18.5
14    22.3
21    24.1
28    23.8
35    23.0
...
```

También se acepta con comas:

```
7,18.5
14,22.3
21,24.1
```

### Modo fechas de control

Active **Fechas de control**, ingrese la fecha de parto y pegue la fecha de cada control junto a su producción:

```
2025-01-08    18.5
2025-01-15    22.3
2025-01-22    24.1
```

Los DEL se calculan automáticamente desde la fecha de parto.

---

## Fundamento matemático

### Modelo de Wood (1967)

El modelo Gamma Incompleto propuesto por Wood es el más utilizado para describir la curva de lactancia en bovinos. Cada coeficiente tiene interpretación biológica directa:

- **a**: producción secretada antes de que los mecanismos de regulación hormonal alcancen su máxima expresión
- **b**: relacionado con el gradiente de la fase ascendente; valores altos indican picos más pronunciados
- **c**: relacionado con la tasa de involución mamaria post-pico; vacas más persistentes tienen valores menores de c

Las fórmulas derivadas del modelo son:

$$t_{pico} = \frac{b}{c}$$

$$y_{max} = a \cdot \left(\frac{b}{c}\right)^b \cdot e^{-b}$$

$$s = -(b+1) \cdot \ln(c)$$

La persistencia **s** mide cuánto tiempo la vaca mantiene niveles altos de producción post-pico; valores más altos indican curvas más planas y persistentes.

### Método de estimación

La linealización logarítmica convierte el modelo en una regresión lineal múltiple:

$$\ln(Y_t) = \underbrace{\ln(a)}_{\beta_0} + \underbrace{b}_{\beta_1} \cdot \ln(t) + \underbrace{(-c)}_{\beta_2} \cdot t$$

Los coeficientes β se estiman resolviendo el sistema de ecuaciones normales (X'X)β = X'Y mediante eliminación gaussiana con pivoteo parcial.

> **Nota:** El R² reportado se calcula en la **escala original** (no en la escala log), lo que refleja de forma más realista el ajuste visual de la curva a los datos observados.

---

## Valores de referencia — Trópico alto colombiano

Basados en Cañas et al. (2009) para la Hacienda La Montaña (UdeA), San Pedro de los Milagros, Antioquia (2300–2500 msnm):

| Parámetro | Holstein | BON × Holstein |
|-----------|----------|----------------|
| a (L/día) | 15 – 22 | 10 – 16 |
| b | 0.10 – 0.25 | 0.15 – 0.30 |
| c | 0.025 – 0.045 | 0.030 – 0.050 |
| t_pico (días) | 28 – 42 | 30 – 50 |
| Producción total (L) | 4 300 – 6 200 | 2 800 – 4 200 |
| Duración lactancia (sem) | 44 – 60 | 35 – 48 |

---

## Dependencias

| Librería | Versión | Uso | Carga |
|----------|---------|-----|-------|
| [Chart.js](https://www.chartjs.org/) | 4.4.1 | Visualización de la curva | CDN (cdnjs) |

La app funciona completamente **sin conexión a internet** una vez que Chart.js ha sido cacheado por el navegador. Para uso 100% offline, reemplace la etiqueta `<script src="...chart.umd.min.js">` por una copia local de la librería.

---

## Estructura del repositorio

```
.
├── curvas_lactancia_wood.html   # Aplicación completa (único archivo)
└── README.md
```

---

## Citar este trabajo

Si utiliza esta herramienta en publicaciones académicas o trabajos de grado, cite también el modelo original y el estudio de referencia para trópico alto colombiano:

> **Wood, P.D.P.** (1967). Algebraic model of the lactation curve in cattle. *Nature*, 216, 164–165.

> **Cañas A., J.J.; Restrepo B., L.F.; Ochoa S., J.; Echeverri, A.; Cerón-Muñoz, M.** (2009). Estimación de las curvas de lactancia en ganado Holstein y BON x Holstein en trópico alto colombiano. *Revista Lasallista de Investigación*, 6(1), 35–42.

---

## Licencia

MIT License — libre para uso académico, docente e investigativo.

---

## Contacto

**Prof. José Fernando Guarín Montoya**  
Departamento de Producción Agropecuaria · Facultad de Ciencias Agrarias  
Universidad de Antioquia · Medellín, Colombia  
Grupo de Investigación Grupo Leche UdeA
