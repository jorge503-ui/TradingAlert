# TradingAlert — Reglas del Proyecto

## 1. Engram MCP — OBLIGATORIO

**Al inicio de CADA sesión que involucre este proyecto:**

```
1. mcp_engram_mem_session_start(id="trading-alert-{fecha}", project="TradingAlert")
2. mcp_engram_mem_context(project="TradingAlert") — leer contexto previo
3. mcp_engram_mem_search(query="...", project="TradingAlert") — buscar decisiones relevantes
```

**Al finalizar CADA sesión o bloque de trabajo significativo:**

```
1. mcp_engram_mem_save() — guardar decisiones arquitecturales, bugs, patrones nuevos
2. mcp_engram_mem_session_summary() — resumen de lo logrado
3. mcp_engram_mem_session_end(id="...", summary="...")
```

**Durante el trabajo:** guardar con `mcp_engram_mem_save()` después de:
- Toda modificación de lógica de señales
- Bugs encontrados y corregidos
- Cambios en parámetros de indicadores (umbrales, cooldowns, etc.)
- Nuevas decisiones de diseño de la estrategia

---

## 2. Estructura del Proyecto

```
TradingAlert/
├── TradingViewer/
│   ├── btcPlanAlert.pine          ← Indicador principal V5.6 (4H) — NO tocar sin entender todo el flujo
│   ├── btcPlanBacktest.pine       ← Backtest con strategy() — $10K inicial, 0.5% comisión
│   ├── btcPlanBacktest_V2_Scalper.pine  ← Variante scalper experimental
│   └── btcPlanLimitOrders.pine    ← Limit orders para Binance/ByBit (pasivo)
├── doc/
│   └── estrategia_btc.md          ← FUENTE DE VERDAD de la estrategia. Siempre actualizar.
└── .agent/
    └── rules.md                   ← Este archivo
```

---

## 3. Convenciones Pine Script V5

- **Versión**: Pine Script V5 (`//@version=5`). NO usar sintaxis V6.
- **Indicadores**: `indicator()` para alertas. `strategy()` SOLO para backtest.
- **Multi-timeframe**: `request.security()` con `barmerge.gaps_off` siempre.
- **Persistencia**: usar `var` para variables que persisten entre barras.
- **Alertas**: `alertcondition()` con `alert.freq_once_per_bar_close`.
- **Cooldowns**: implementar con `barssince(condicion) > N`.

### Indicadores usados en el proyecto

| Indicador | Función                            | Parámetros  |
|-----------|------------------------------------|-------------|
| EMA       | `ta.ema(close, length)`            | 50, 200     |
| RSI       | `ta.rsi(close, 14)`                | 14 periodos |
| ATR       | `ta.atr(14)`                       | 14 periodos |
| ADX/DMI   | `ta.dmi(14, 14)`                   | 14 periodos |
| Volumen   | `ta.sma(volume, 10)`               | SMA 10      |

---

## 4. Reglas de Modificación de Señales

### Antes de modificar cualquier señal:
1. Leer `doc/estrategia_btc.md` completo para entender el contexto.
2. Buscar en engram decisiones previas relacionadas.
3. Entender el PROPÓSITO de la señal (filosofía "NO perder > ganar").

### Señales y sus parámetros críticos (NO cambiar sin justificación documental):

| Señal              | % Liquidez | Stop | Cooldown     | Restricción clave                       |
|--------------------|------------|------|--------------|----------------------------------------|
| 🟢 COMPRA NORMAL   | 40-50%     | -4%  | —            | ADX > 15, Vol >= 1.0x                   |
| 🟢 DIP CORRECCIÓN  | 50-60%     | -4%  | —            | Sin PELIGRO últimas 5 barras            |
| 🔥 DIP BRUTAL      | 60-70%     | -4%  | —            | Sin PELIGRO últimas 5 barras            |
| 🔥💀 POST-CAP      | 40-50%     | -3%  | —            | 1-4 barras post-PELIGRO, Vol > 1.6x     |
| ⚡ REBOTE BAJISTA  | 20-30%     | -3%  | —            | Caída >= 6%, tendencia diaria BAJISTA   |
| 🚀 BREAKOUT        | 20-30%     | -5%  | 20 barras    | 3 velas sobre EMA200, Vol > 2.0x        |
| 🔴 VENTA           | —          | —    | 8 barras     | RSI > 72 ó precio < EMA50              |
| 🟡 PELIGRO         | —          | —    | 10 barras    | Sistema de puntos >= 3                  |
| 💀 EXTREMO         | —          | —    | 15 barras    | Crossunder EMA200 + Vol > 1.2x          |

---

## 5. Flujo de Commits

```bash
# Siempre desde: c:\dev\proyectos\TradingAlert
git add .
git commit -m "tipo(scope): descripción corta"
git push origin main
```

### Tipos de commit (conventional commits):
- `feat`: nueva señal o funcionalidad
- `fix`: corrección de lógica o bug
- `docs`: actualización de doc/estrategia_btc.md
- `refactor`: reorganización de código sin cambio de lógica
- `test`: cambios en archivos de backtest

### Ejemplos:
```
feat(alert): add pre-alert radar for EMA50 proximity
fix(dip-brutal): relax volume filter to 1.0x average
docs(estrategia): update V5.6 signal parameters table
refactor(peligro): extract point system to separate variables
```

### NUNCA:
- Commits con mensajes genéricos como "update" o "fix stuff"
- Co-Authored-By ni atribuciones de IA
- Push sin haber actualizado `doc/estrategia_btc.md` si se modificó lógica de señales

---

## 6. Remote Git

```
origin: https://github.com/jorge503-ui/TradingAlert.git
```

---

## 7. Filosofía de la Estrategia (contexto para el AI)

> **"NO perder > ganar"** — La estrategia prioriza preservación de capital.

- ❌ Sin shorts, sin apalancamiento, sin órdenes automatizadas
- ✅ Solo operaciones spot BTC/USD en Chivo Wallet (El Salvador)
- ✅ Máximo 3-4 alertas de entrada por día
- ✅ 30-40% de liquidez siempre en USD como reserva
- ✅ Stop loss mental FIJO: -4% desde precio de entrada promedio

Cada modificación al código DEBE respetar esta filosofía. Si una "mejora" aumenta señales indiscriminadamente → RECHAZAR.
