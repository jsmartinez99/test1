# Plan por fases

Cada fase es una tarea para opencode. Pégala en el agente `build` (o `build-pro` si el gratuito se atasca).
Usa el agente `plan` para revisar el enfoque antes de una fase grande.

## Fase 1 — Base

Crea la estructura descrita en `AGENTS.md`: backend FastAPI, frontend React/Vite, `docker-compose.yml`, `.env.example`, `.gitignore`.
- `config.py` lee `.env` (keys de testnet y live por separado, `BINANCE_ENV`).
- Clientes Spot y Futuros apuntando a testnet o live según `BINANCE_ENV`.
- Endpoints: `GET /api/status` (entorno, conexión OK), `GET /api/balances` (spot y futuros), `GET /api/ticker/{symbol}`.
- WebSocket `/ws/prices` que reenvía precios en vivo de Binance al frontend.
- Frontend: página inicial con indicador de entorno (TESTNET en verde, LIVE en rojo), saldos y precio en vivo de BTCUSDT.
- Tests de los endpoints con el cliente de Binance mockeado.

## Fase 2 — Trading manual

- Gráfico de velas con selector de símbolo e intervalo.
- Formulario de órdenes market, limit y stop-limit para Spot y Futuros.
- Futuros: apalancamiento, margen aislado/cruzado, posiciones abiertas con PnL, cerrar posición.
- Tablas de órdenes abiertas (cancelar) e historial.
- Todas las órdenes pasan por `risk.py`.

## Fase 3 — Arbitraje P2P vs Spot (USDT/ARS)

- Cliente que consulta los mejores anuncios BUY y SELL de USDT/ARS (filtros: método de pago, monto).
- Cálculo de spread bruto y neto (comisiones de Spot incluidas) frente a la referencia Spot.
- Página con tabla de anuncios, spread actual, histórico guardado en SQLite y aviso en el dashboard cuando el spread supera un umbral configurable.

## Fase 4 — Bots

- Motor de bots asíncrono con estado en SQLite (crear, iniciar, pausar, eliminar).
- Estrategias: DCA, Grid, señales por indicadores (RSI, cruce de medias, MACD) y SL/TP sobre posiciones.
- Panel de bots con estado, registro de acciones y PnL.
- Tests de cada estrategia con datos de velas simulados.

## Fase 5 — Seguridad y riesgo

- Configuración en la UI de pérdida máxima diaria y tamaño máximo por orden.
- Botón "parar todo": pausa bots y cancela órdenes abiertas.
- Cambio a `live` con confirmación escrita y banner permanente.
