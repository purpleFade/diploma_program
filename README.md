# Practice 2024 Project (monorepo)

This repository is the **single entry‑point** for the Diploma 2025 deliverable.  
It glues together two sub‑projects—

| Folder | Description | Tech |
|--------|-------------|------|
| `backend/` | Flask microservice that calls the Roboflow *military_objects/1* model and hosts results. | Python 3 · Flask · OpenCV |
| `frontend/` | React + TypeScript SPA that uploads images and visualises the detections. | React 18 · Vite · Axios |

A ready‑to‑run **`docker‑compose.yml`** spins up both services so you can test everything with a single command.

---

## 🗺️ Repo Layout

```
.
├── backend/               # Practice_2024_backend  (Flask)
├── frontend/              # Practice_2024_frontend (React)
├── docker-compose.yml     # Multi‑service orchestration
└── README.md              # (this file)
```

---

## ⚡ Quick Start (Docker Compose)

> Requirements: **Docker 20+** and **docker‑compose v2** (or Docker Desktop).

1. **Clone the monorepo**

   ```bash
   git clone https://github.com/<you>/Practice_2024_monorepo.git
   cd Practice_2024_monorepo
   ```

2. **Set your Roboflow API key**

   Create `.env.backend` in the project root (the compose file loads it automatically):

   ```bash
   # .env.backend
   ROBOFLOW_API_KEY=YOUR_KEY_HERE
   ```

3. **Launch the stack**

   ```bash
   docker compose up --build
   ```

   After the images build, you will have:

   | Service | URL | Description |
   |---------|-----|-------------|
   | **backend** | <http://localhost:5000> | Flask REST API |
   | **frontend** | <http://localhost:3000> | React SPA (served by Vite preview) |

   The frontend is already configured (via Docker network) to call the backend at `http://backend:5000`.

---

## 🔧 Development Workflow

### Run services locally without Docker

```bash
# Terminal 1 – backend
cd backend
export ROBOFLOW_API_KEY=YOUR_KEY
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
python app.py                    # http://127.0.0.1:5000

# Terminal 2 – frontend
cd frontend
npm install
npm run dev                      # http://localhost:5173
```

Update `VITE_APP_API_BASE` in `frontend/.env` to `http://127.0.0.1:5000`.

---

## 📄 docker‑compose.yaml (excerpt)

```yaml
version: "3.9"

services:
  backend:
    build: ./backend
    env_file: .env.backend
    ports:
      - "5000:5000"

  frontend:
    build: ./frontend
    environment:
      - VITE_APP_API_BASE=http://backend:5000
    ports:
      - "3000:3000"
    depends_on:
      - backend
```

> The actual file may include caching layers or production optimisations.  
> Feel free to tweak the port mapping if 3000/5000 are occupied.

---

## 🙋 FAQ

| Question | Answer |
|----------|--------|
| **What happens to results?** | The backend writes outputs to `/results` inside its container and exposes them via `/results/<path>` so the frontend can link directly. |
| **Can I use my own model?** | Yes—change `ROBOFLOW_MODEL_ID` in `backend/app.py` and redeploy. |
| **How do I deploy to the cloud?** | Any Docker‑compatible host (Render, Railway, AWS ECS, etc.) can run `docker compose up`. Make sure to set the same env vars. |

---

## 🤝 Contributing

1. Fork this repo  
2. Create a branch  
   ```bash
   git checkout -b fix/awesome-feature
   ```
3. Commit & push  
4. Open a pull request

---

## 📝 License

The monorepo is released under the **MIT License**.  
Each subfolder inherits the same license unless stated otherwise.
