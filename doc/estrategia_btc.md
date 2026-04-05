# 🚀 Chivo Wallet Alerts — Documentación Completa

### Versión 5.6 · BTC/USD · Timeframe 4H · Solo Spot · El Salvador

> **Principio central:** La estrategia prioriza **NO perder** por encima de ganar.  
> Un dip que no se opera es mejor que una entrada en el momento equivocado.

---

## 📋 Tabla de Contenido

1. [Filosofía y Objetivo](#1-filosofía-y-objetivo)
2. [Configuración Técnica](#2-configuración-técnica)
3. [Las 8 Señales — Resumen Rápido](#3-las-8-señales--resumen-rápido)
4. [Detalle de Cada Señal](#4-detalle-de-cada-señal)
5. [Gestión de Capital](#5-gestión-de-capital)
6. [Flujo Real de Operación](#6-flujo-real-de-operación)
7. [Historial de Versiones](#7-historial-de-versiones)
8. [Código Completo — Pine Script V5.6](#8-código-completo--pine-script-v56)

---

## 1. Filosofía y Objetivo

Esta estrategia fue construida específicamente para operar Bitcoin en **Chivo Wallet** en modo 100% spot (`USD → BTC → USD`).

- ❌ Sin shorts
- ❌ Sin apalancamiento
- ❌ Sin órdenes pendientes automatizadas
- ✅ Comprar BTC barato cuando la tendencia mayor sigue alcista
- ✅ Vender BTC caro cuando el mercado da señales de agotamiento
- ✅ Proteger capital por encima de cualquier otra consideración
- ✅ Pocas operaciones bien ejecutadas, no muchas a ciegas

**Estilo:** pocas entradas (3-5 alertas por día máximo), entradas parciales, disciplina total, sin intuiciones.

---

## 2. Configuración Técnica

### 2.1 Timeframes

| Timeframe | Uso | Rol |
|-----------|-----|-----|
| **4H** | Principal | Donde se generan todas las alertas |
| **Diario (D)** | Confirmación | Filtro de tendencia mayor via EMA 50/200 |
| **1H** | Opcional | Confirmación manual antes de entrar (no genera alertas) |

### 2.2 Indicadores Usados

| Indicador | Parámetros | Función |
|-----------|-----------|---------|
| EMA 50 | 50 periodos (4H) | Soporte dinámico — zona de pullbacks y entradas |
| EMA 200 | 200 periodos (4H) | Tendencia de fondo — la línea de la vida |
| EMA 50 Diaria | 50 periodos (D) | Confirmación multi-timeframe alcista |
| EMA 200 Diaria | 200 periodos (D) | Tendencia macro — filtro definitivo |
| RSI | 14 periodos (4H) | Momentum, sobrecompra y sobreventa |
| RSI Diario | 14 periodos (D) | Evitar entrar en techos del timeframe mayor |
| ATR | 14 periodos | Volatilidad dinámica para proximidad a EMA50 |
| ADX/DMI | 14 periodos | Confirmar que hay tendencia real, no lateral |
| Volumen | SMA 10 periodos | Distinguir ruido de señal con fuerza real |

### 2.3 Filtros Globales Anti-Ruido

| Filtro | Valor | Propósito |
|--------|-------|-----------|
| Volumen mínimo | `>= 1.0x promedio` | Evita entradas en velas sin interés |
| Cuerpo de vela | `>= 40% (o Martillo)` | Elimina doji, acepta pin bars alcistas |
| ADX mínimo | `> 15` (solo COMPRA) | Solo entra si hay tendencia real |
| RSI Diario | `< 72` | No comprar si el mayor ya está sobrecomprado |
| Cooldown PELIGRO | `10 barras` | Anti-spam de señales repetidas |
| Cooldown VENTA | `8 barras` | Anti-spam en reversiones prolongadas |
| Cooldown EXTREMO | `15 barras` | Anti-spam en rupturas de EMA200 |

---

## 3. Las 8 Señales — Resumen Rápido

| # | Señal | Entrada | Stop | Cuándo entra |
|---|-------|---------|------|-------------|
| 1 | 🟢 **COMPRA NORMAL** | 40-50% | -4% | Pullback sano en tendencia alcista 4H + Daily |
| 2 | 🟢 **DIP EN CORRECCIÓN** | 50-60% | -4% | Caída ≥4%, RSI rebotando 2 barras |
| 3 | 🔥 **DIP BRUTAL** | 60-70% | -4% | Dip fuerte, diario alcista, sin PELIGRO previo |
| 3B | 🔥💀 **POST-CAPITULACIÓN** | 40-50% | -3% | 1-4 barras después del PELIGRO |
| 4 | ⚡ **REBOTE BAJISTA** | 20-30% | -3% | Caída ≥6% en tendencia diaria bajista |
| 4B | 🚀 **BREAKOUT TENDENCIA** | 20-30% | -5% | Reversión Bear→Bull: 3 velas + vol 2x + Golden Cross |
| 5 | 🔴 **VENTA** | Salida | — | Sobrecompra RSI>72 o reversión EMA50 |
| 6 | 🟡 **PELIGRO** | Salir YA | — | Pérdida de fuerza (3+ puntos de riesgo) |
| 7 | 💀 **EXTREMO** | Salir TODO | — | Rompe EMA200 con volumen |

---

## 4. Detalle de Cada Señal

---

### 4.1 🟢 COMPRA NORMAL — La más segura

La señal de mayor calidad. Requiere que **todo esté alineado**: tendencia en 4H y en Diario, precio haciendo pullback a la EMA50 dentro de un rango dinámico basado en ATR, RSI en zona sana subiendo, vela con cuerpo real y volumen sobre el promedio.

**Condiciones (todas deben cumplirse):**

- Precio > EMA200 (4H)
- EMA50 > EMA200 (4H)
- EMA50 Diaria > EMA200 Diaria
- Precio dentro de `ATR × 1.25` de la EMA50 *(proximidad dinámica ampliada)*
- RSI entre 40 y 55, y subiendo
- Vela alcista con cuerpo ≥ 40% del rango (o Martillo)
- Volumen >= 1.0× promedio
- ADX > 15 *(filtrado de tendencia relajado para pullbacks)*
- RSI Diario < 72 *(no sobrecomprado en mayor)*

> 💡 **Acción en Chivo:** Entrás **40-50%** de la liquidez planificada. Si la siguiente vela de 4H confirma con cierre alcista, agregás el resto.

---

### 4.2 🟢 DIP EN CORRECCIÓN

Para cuando el precio ya cayó moderadamente y empieza a mostrar signos de rebote. Puede estar por debajo de la EMA200 pero no muy lejos. El RSI debe confirmar el giro subiendo **2 barras consecutivas**.

**Condiciones:**

- Caída ≥ 4% contra el máximo de las últimas 15 velas
- Precio < EMA200 × 1.02 *(cerca o por debajo de la EMA200)*
- RSI ≤ 42 y subiendo 2 barras consecutivas
- Vela alcista con cuerpo real (o Martillo)
- Volumen >= 1.0× promedio
- Sin PELIGRO en las últimas 5 barras

> 💡 **Acción en Chivo:** Entrás **50-60%** de la liquidez. Más agresivo que COMPRA porque ya hubo caída, pero el rebote está empezando.

---

### 4.3 🔥 DIP BRUTAL — La que más paga

La entrada más agresiva. Solo se activa cuando hay una caída fuerte **mientras la tendencia mayor diaria sigue siendo alcista**. Está totalmente prohibido entrar si hubo PELIGRO en las últimas 5 barras.

**Condiciones:**

- Caída ≥ 4% desde el máximo de las últimas 15 velas
- RSI ≤ 38 *(sobreventa real)*
- Tendencia diaria alcista: EMA50D > EMA200D
- Volumen >= 1.0× promedio
- Vela alcista sana o Martillo
- **SIN PELIGRO en las últimas 5 barras** ← condición excluyente
- Precio > 98% de la EMA200 *(estructura no colapsó)*
- RSI Diario < 72

> 💡 **Acción en Chivo:** Entrás **60-70%** de la liquidez. Es la entrada con mayor peso. Stop mental en -4% desde precio promedio de entrada.

---

### 4.4 🔥💀 DIP POST-CAPITULACIÓN — El rebote del flush

**Este es el problema que más costaba plata:** el PELIGRO aparece, salís bien, y 2-3 velas después el precio explota al alza sin que hayas vuelto a entrar. Esta señal detecta exactamente ese patrón.

```
🟡 PELIGRO dispara → salís (capital protegido) ✅
        ↓  1-4 barras después
🔥💀 RE-ENTRADA → RSI sube 3 barras + vela fuerte + volumen 60% sobre promedio ✅
        ↓
🔴 VENTA → ganancia ✅
```

**Condiciones:**

- PELIGRO ocurrió entre **1 y 4 barras atrás**
- RSI ≤ 36 y subiendo **3 barras consecutivas**
- Vela alcista con cuerpo ≥ **55%** del rango *(o Martillo fuerte)*
- Volumen > **1.6×** promedio *(el mercado está comprando en serio)*
- Precio > 96% de la EMA200 *(no hubo ruptura total)*
- EMA50D > EMA200D × 0.97 *(tendencia diaria no colapsó)*

> 💡 **Acción en Chivo:** Entrás **40-50%** (no 60-70% porque acabás de salir posiblemente con pérdida y el mercado sigue nervioso). **Stop ajustado en -3%.**

---

### 4.5 ⚡ REBOTE BAJISTA — Para aprovechar tendencias bajistas

Cuando la tendencia diaria es bajista, el mercado igual hace rebotes técnicos del 6-10%. Esta señal los captura con **entradas pequeñas y salidas rápidas**.

**Condiciones:**

- Caída ≥ **6%** desde máximo de últimas 15 velas
- RSI ≤ 35 y subiendo 2 barras consecutivas
- Tendencia Diaria **BAJISTA** ← condición opuesta a las otras
- Vela alcista sana o Martillo
- Volumen >= 1.0× promedio
- Sin PELIGRO reciente

**Reglas especiales para esta señal:**

- Máximo **1 operación de este tipo por semana**
- Salís en la **PRIMERA** señal de VENTA o PELIGRO sin esperar
- Stop mental en **-3%**
- Máximo **20-30%** de liquidez

> ⚠️ Los rebotes bajistas son rápidos y traicioneros. Nunca esperés un segundo rebote.

---

### 4.9 🚀 BREAKOUT DE TENDENCIA — La reversión de bear a bull

La señal más exigente del sistema. **AISLADA de la lógica de PELIGRO** para que no se cancelen entre sí. Detecta cuando el mercado genuinamente cambia de estructura. Por eso mismo, es la de mayor riesgo: los fakeouts son comunes. La entrada es reducida.

**Condiciones (TODAS deben cumplirse):**

- Precio lleva **3 velas 4H consecutivas** por encima de la EMA200 *(no basta con un spike)*
- Máximo volumen de esas 3 velas > **2.0×** promedio *(hay dinero institucional detrás)*
- RSI entre 50 y 65, subiendo 2 barras consecutivas *(momentum real, no sobrecomprado)*
- EMA50 a menos del **3%** de cruzar hacia arriba la EMA200 *(Golden Cross inminente)*
- EMA50 Diaria acercyándose a EMA200 Diaria *(estructura macro recuperándose)*
- Vela de calidad: cuerpo ≥ **55%** del rango *(confirmación sólida, no mecha)*
- Cooldown de **20 barras** (80 horas) entre disparos

> ⚠️ **Acción en Chivo:** Máximo **20-30%** de liquidez. Es una apuesta a la reversión, no una certeza. Stop mental en **-5%** más amplio porque el precio puede hacer un retest de la EMA200 antes de explotar. Si el Golden Cross no se confirma en los próximos 3 días, salís.

---

### 4.6 🔴 VENTA — Take profit y reversión

Se activa por cualquiera de dos condiciones. El **cooldown de 8 barras** evita la cascada de señales repetidas en tendencias bajistas prolongadas.

**Condiciones (cualquiera):**

- **Condición A:** RSI cruza hacia arriba de 72 *(sobrecompra extrema)*
- **Condición B:** Precio rompe hacia abajo la EMA50 + vela roja + RSI < 62 + volumen creciente
- Cooldown: **8 barras** entre disparos

> 💡 **Acción en Chivo:** Salida **parcial (50-70%)** si la tendencia mayor sigue sólida. Salida **total** si hay duda o si coincide con PELIGRO.

---

### 4.7 🟡 PELIGRO — Tu salvavidas

Sistema de puntos. Cada condición vale puntos. Se necesitan **3+ puntos** para disparar, más cooldown de 10 barras.

| Condición | Puntos |
|-----------|--------|
| Rompe EMA50 hacia abajo (cierre < 99% EMA50) | 1 |
| RSI < 40 | 1 |
| Dos velas rojas consecutivas + volumen | 1 |
| **Rompe EMA200 con volumen** | **2** |

- **Threshold:** 3+ puntos para activar
- **Cooldown:** 10 barras entre disparos

> 🚨 **Regla de oro:** Si ya estás dentro → vendés **TODO** inmediatamente aunque estés en pérdida. Si no estás → **NO comprás NUNCA** con esta señal activa.

---

### 4.8 💀 PELIGRO EXTREMO — Salida de emergencia

La señal más grave. Indica un posible cambio de tendencia mayor, no solo una corrección.

**Condiciones:**

- Precio cruza hacia abajo la EMA200 *(crossunder)*
- Volumen > 1.2× promedio
- Cooldown: **15 barras**

> 🚨 **Acción en Chivo:** Salir el **100% de la posición inmediatamente**. No esperás la siguiente vela. No importa el precio.

---

## 5. Gestión de Capital

### 5.1 Distribución de Liquidez por Señal

| Señal | % de Liquidez | Stop Mental | Condición especial |
|-------|-------------|------------|-------------------|
| 🟢 COMPRA NORMAL | 40-50% | -4% | Agregar resto si siguiente vela confirma |
| 🟢 DIP | 50-60% | -4% | — |
| 🔥 DIP BRUTAL | 60-70% | -4% | Nunca si hubo PELIGRO reciente |
| 🔥💀 RE-ENTRADA | 40-50% | -3% | Solo 1-4 barras después de PELIGRO |
| ⚡ REBOTE BAJISTA | 20-30% | -3% | Máx. 1 por semana |
| 🚀 BREAKOUT TENDENCIA | 20-30% | -5% | Solo en transición Bear→Bull confirmando |

### 5.2 Reglas de Oro

1. **NUNCA operás sin alerta.** Cero intuiciones, cero "creo que va a subir".
2. Siempre dejás **30-40% en USD reservado** por si aparece un dip mejor después.
3. Stop loss mental **FIJO**: -4% desde precio de entrada promedio. Se respeta sin discutir.
4. **Máximo 3-4 alertas de entrada por día.** Si hay más → pausás 24 horas.
5. No promediás bajando si ya tenés posición y aparece **PELIGRO**.
6. Una sola entrada por señal clara. No doblás sin confirmación.

---

## 6. Flujo Real de Operación

Actualmente el sistema soporta dos metodologías de ejecución dependiendo de tu tiempo y plataforma:

### 6.1 Modalidad A: Ejecución Manual en Chivo Wallet (Activa)

Usando el script principal `btcPlanAlert.pine` en temporalidad de 4H.

1. Recibís pre-alerta 📡 **RADAR ZONA CALIENTE**. El precio está a 1.5% de gatillar compras. Prestás atención a tu teléfono en las próximas horas.
2. Si se dan las condiciones, TradingView escupe Alerta de Entrada (🟢 / 🔥 / ⚡ / 🚀).
3. Entrás a Chivo Wallet y ejecutás la compra a mercado (USD → BTC).
4. El script dibuja la línea 🛡️ **Stop Loss Activo**. Monitoreala de reojo.
5. Si el precio cruza esa línea, salta la alerta ❌ **SALIR YA (STOP HIT)**. Tragate el orgullo y vendé manual asumiendo el -4%.
6. Si va bien, esperás la alerta 🔴 **VENTA** para hacer Take Profit parcial o total.

### 6.2 Modalidad B: Ejecución Límite Pasiva (Asíncrona Pro)

Usando el nuevo script auxiliar `btcPlanLimitOrders.pine` en Exchanges como Binance/ByBit.

1. Solo abrís TradingView 1 vez al día (ej. cierre de vela diaria).
2. Mirás el panel (Dashboard) superior derecho en pantalla.
3. Copiá el precio explícito de **📌 LIMIT BUY** (ya trae el ATR proyectado).
4. Andá a Binance y clavá una "Buy Limit Order" estática en ese precio.
5. Copiá los valores de **SL** y **TP** del panel y seteá tus cierres automáticos (OCO).
6. Cerrás la compu y te vas. El mercado compra, estopea o toma ganancias solo.

### 6.3 Modalidad C: Validación Estadística (Backtesting Pro)

Usando el script de estrategia `btcPlanBacktest.pine` para auditar la rentabilidad del sistema en el "Probador de estrategias".

1. Este script usa `strategy()` y emula un capital de inicio de **$10,000 USD** con una **comisión (fricción/spread) de 0.5%** por cada trade, muy realista para la realidad de Chivo Wallet.
2. Agregás `btcPlanBacktest.pine` al gráfico (no verás alertas ni dashboard visual).
3. Hacés clic en la pestaña inferior **"Probador de estrategias"**.
4. Ahí vas a ver métricas reales: Win Rate (%), Beneficio Neto, Factor de Beneficio y el Drawdown Máximo que hubiera sufrido tu cuenta.
5. Usa esto **únicamente para estudiar al sistema** en el pasado, no para operar en vivo (para operar en vivo usar Modalidades A o B).

---

## 7. Historial de Versiones

| Versión | Cambios principales |
|---------|-------------------|
| V1-V3 | Versiones iniciales: EMAs básicas, RSI simple, sin filtros de volumen |
| V4 | Agregado DIP EN CORRECCIÓN, filtros de volumen, multi-timeframe diario |
| V5.0 | DIP BRUTAL más estricto, `peligro_reciente` como filtro, refactor general |
| V5.1 | Volumen dinámico (1.2×), ATR para proximidad EMA50, filtro ADX, `vela_con_cuerpo`, `daily_no_top`, alerta 💀 PELIGRO EXTREMO |
| V5.2 | Cooldowns anti-spam (PELIGRO: 10b, VENTA: 8b, EXTREMO: 15b), alerta ⚡ REBOTE BAJISTA |
| V5.3 | Alerta 🔥💀 POST-CAPITULACIÓN, RSI 3-barras para re-entrada, `vol_capitulacion` 1.6×, `vela_fuerte` 55% |
| **V5.4** | Flexibilización: ADX>15, Vol>1.0x, Martillos tolerados, DIP con `highest(15)`, EMA50 a 1.25 ATR. |
| **V5.5** | Stop Loss visual dinámico. Radares Preventivos (Pre-Alertas 1.5%). Script Limit Orders secundario. |
| **V5.6** | **Breakout de Cambio de Tendencia (Bear→Bull): 5 filtros simultaneáneos, vol 2x, Golden Cross inminente, cooldown 20 barras. Radar de Salida (⚠️ PRE-OUT) contra sobrecompra y amenaza EMA50.** |

---

## 8. Código Completo — Pine Script V5.6

> Copiar y pegar en TradingView → Pine Editor → Nuevo indicador / estrategia

1. `btcPlanAlert.pine` (Indicador Principal con Dashboard y Alertas)
2. `btcPlanLimitOrders.pine` (Generador estático de Limit Orders para Binance)
3. `btcPlanBacktest.pine` (Motor de validación estadística en Probador de Estrategias)

*Documento generado el 29 de marzo de 2026 · Chivo Wallet Alerts V5.6*
