# Zeno AI Backend

A small FastAPI server that sits in front of Zeno's model. Your frontend talks to this backend, and this backend talks to a model server (Ollama, vLLM, or any OpenAI-compatible server) running locally or on Kaggle.

```
Frontend  ->  Zeno backend (FastAPI)  ->  model server (local or Kaggle GPU)
```

## Endpoints

- `GET /health`: backend status and whether the model server is reachable
- `POST /chat`: send a list of messages, get Zeno's reply

Example request:

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"messages": [{"role": "user", "content": "Hi Zeno"}]}'
```

## Run locally

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env             # then edit MODEL_URL and MODEL_NAME
uvicorn app.main:app --reload --port 8000
```

Open http://localhost:8000/docs for the interactive API page.

## Using Kaggle for GPU/CPU

Kaggle notebooks can run the model, but they can't open a public port by themselves. The usual approach:

1. In a Kaggle notebook with GPU on, start your model server (for example Ollama or vLLM) on port 11434 or 8000.
2. Expose that port with a tunnel such as `cloudflared` or `ngrok`. The tunnel gives you a public URL.
3. Set `MODEL_URL` in your backend's `.env` to that tunnel URL plus `/v1`.
4. Run the backend locally or on a server. It forwards chats to Kaggle.

Kaggle sessions time out, so the tunnel URL changes each session. Update `MODEL_URL` when you restart.

## Put it on GitHub

```bash
cd zeno-backend
git init
git add .
git commit -m "Add Zeno AI backend"
git branch -M main
git remote add origin https://github.com/<your-username>/zeno-backend.git
git push -u origin main
```

Create the empty repo on GitHub first. `.env` is in `.gitignore`, so your keys stay off GitHub.

## Next steps

- Add a `/chat/stream` route for token-by-token replies
- Add conversation history storage
- Add tests under `tests/`
