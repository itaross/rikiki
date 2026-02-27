# rikiki
# 🃏 Rikiki — Multiplayer Card Game

Webapp multiplayer del gioco di carte Rikiki, costruita con **React + TypeScript** (frontend) e **Python + FastAPI + WebSocket** (backend).

---

## 🚀 Avvio Rapido

### Backend

```bash
cd backend
uv venv
uv pip install -r requirements.txt
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

Apri `http://localhost:5173` nel browser.

> Per cambiare l'URL del backend WebSocket, modifica `VITE_WS_URL` in un file `.env` nella cartella `frontend/`:
> ```
> VITE_WS_URL=ws://localhost:8000/ws
> ```

---

## 🧪 Test

```bash
cd backend
pytest tests/ -v
```

Copertura test:
- Generazione mazzo (104 carte, 4 Re Rossi = 0 punti)
- Discard matching (stesso rank → successo)
- Discard fail (rank diverso → carta tenuta)
- Effetto Jack (spia carta avversaria)
- Effetto Donna (scambia due carte cieche)
- Effetto Re (peek obbligatorio + swap obbligatorio)
- Calcolo punteggio
- Rikiki con >7 punti → sconfitta automatica

---

## 🏗️ Struttura

```
rikiki/
├── backend/
│   ├── main.py          # FastAPI + WebSocket server
│   ├── game.py          # Logica di gioco (GameRoom)
│   ├── models.py        # Modelli Pydantic (Card, Player, ecc.)
│   ├── requirements.txt
│   └── tests/
│       └── test_game.py
└── frontend/
    ├── index.html
    ├── vite.config.ts
    ├── tsconfig.json
    └── src/
        ├── App.tsx
        ├── main.tsx
        ├── types/game.ts
        ├── hooks/useWebSocket.ts
        ├── utils/cardUtils.ts
        └── components/
            ├── CardView.tsx
            ├── Lobby.tsx
            ├── WaitingRoom.tsx
            ├── GameTable.tsx
            ├── Modals.tsx
            └── EndGame.tsx
```

---

## 🎮 Come Giocare

1. **Crea una stanza** → ottieni un codice a 4 lettere
2. **Condividi il codice** con gli altri giocatori
3. Il creatore **avvia la partita**
4. All'inizio hai **5 secondi** per memorizzare le 2 carte inferiori
5. Ogni turno: **pesca una carta**, poi:
   - Tenta di **scartare la coppia** (stesso rank)
   - **Tieni** la carta in mano
6. Carte speciali: **J** (spia), **Q** (scambia cieco), **K** (spia + scambia obbligatorio)
7. Quando pensi di avere il punteggio più basso, chiama **RIKIKI!**
   - Se hai >7 punti → perdi automaticamente
   - Altrimenti vince chi ha il punteggio più basso

---

## ⚙️ Assunzioni

| Assunzione | Valore di default | Configurabile |
|---|---|---|
| Max giocatori per stanza | 8 | Modifica `GameRoom.add_player` |
| Carte per giocatore | 4 (griglia 2×2) | Modifica `GRID_SIZE` in `game.py` |
| Durata reveal iniziale | 5 secondi | Modifica timeout in `GameTable.tsx` |
| RNG seed | Random | Passare `seed` al join |
| Re Rossi = 0 punti | K♥ e K♦ | Modello `Card.is_red_king` |
