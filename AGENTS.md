# Binance Trader — instrucciones para agentes

App web personal para operar en Binance: Spot, Futuros USDⓈ-M y monitor de arbitraje P2P (USDT/ARS) vs Spot.
Corre en local, un solo usuario. El plan por fases está en `PLAN.md`; trabaja una fase a la vez.

## Stack

- Backend: Python 3.12, FastAPI, `binance-connector` (Spot) y `binance-futures-connector` (USDⓈ-M), SQLite con SQLAlchemy, `pandas` para indicadores.
- Frontend: React + TypeScript + Vite, `lightweight-charts` para velas.
- Arranque: `docker compose up` levanta backend (puerto 8000) y frontend (puerto 5173).
- Estructura:
  - `backend/app/` — `main.py`, `config.py`, `exchange/` (clientes spot, futures, p2p), `bots/` (estrategias), `risk.py`, `db.py`, `api/` (routers).
  - `backend/tests/` — pytest.
  - `frontend/src/` — `pages/`, `components/`, `api.ts`.

## Reglas

- **Testnet por defecto.** `BINANCE_ENV=testnet` en `.env`. Pasar a `live` exige confirmación explícita en la UI.
- Testnet Spot: `https://testnet.binance.vision`. Testnet Futuros: `https://testnet.binancefuture.com`.
- Las API keys solo se leen desde `.env` (nunca en código, logs ni respuestas de la API). `.env` está en `.gitignore`; mantén `.env.example` actualizado.
- Nunca implementes retiros ni transferencias fuera de la cuenta.
- P2P: Binance no tiene API para crear órdenes P2P. Solo se leen anuncios públicos (`https://p2p.binance.com/bapi/c2c/v2/friendly/c2c/adv/search`) para calcular el spread; la operación P2P la hace el usuario a mano.
- Toda orden pasa por `risk.py` (pérdida máxima diaria, tamaño máximo por orden, interruptor de parar todo).
- Cada bot registra sus acciones en la base de datos y se puede pausar desde la UI.
- Textos de la UI en español.

## Verificación antes de dar una tarea por terminada

- `cd backend && pytest` pasa. Los tests mockean Binance; no llaman a la red.
- `cd frontend && npm run build` compila sin errores.
