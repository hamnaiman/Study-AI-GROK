# AI Study Assistant using RAG

A small beginner-friendly FastAPI project. Upload five Python PDF chapters, save their embeddings in a local ChromaDB database, and ask Grok questions using retrieved notes.

## Run on Windows

Install Python 3.11 from python.org and select **Add Python to PATH**. Extract this project, open its folder in VS Code, and open the terminal there.

```powershell
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Open `.env` and add your own xAI API key after `XAI_API_KEY=`. Get a key from https://console.x.ai. The key needs API access and credits. Grok and Groq are different services; this assignment uses **Grok by xAI**. Never upload `.env` to GitHub or show it in screenshots. The model name is configurable; use a model available to your account if the default is unavailable.

```powershell
.venv\Scripts\python -m uvicorn main:app --reload
```

Open http://127.0.0.1:8000 in your browser. Select **all five files** from `sample_pdfs` in one upload. Click **Create embeddings**. The first upload downloads Chroma's default local ONNX all-MiniLM-L6-v2 embedding model, so allow time and internet access. Later runs reuse its model cache. No paid embedding API is required.

Ask these questions separately:

1. What are Python variables?
2. Explain the difference between a list and a tuple.
3. What is a for loop?
4. How do functions work in Python?
5. What are conditional statements?

The sample PDFs are original short practice notes supplied with the project. You can replace them with your teacher's five text-based chapters. Scanned PDFs require OCR before upload.

## What the code does

`main.py` reads PDF text with pypdf, splits each page into chunks of 150 words with 30 words of overlap, and calls Chroma's `add()` method. Chroma automatically generates embeddings using its default model and saves vectors, text and file/page metadata under `data/chroma_db`.

For a question, Chroma embeds the question using the same model and retrieves the four nearest chunks. The app sends only those chunks and the question to Grok using xAI's Responses API. The answer appears with numbered retrieved sources. A prompt asks Grok to use only the notes and admit when the answer is absent; generated answers can still need checking.

`index.html` is the simple web page. It sends uploads and questions to FastAPI using a little JavaScript. `requirements.txt` lists Python packages. `.env.example` shows the required settings. `Dockerfile` is for deployment.

The five-PDF upload replaces the previous chapters. PDFs are processed in memory; original uploads are not saved. The supplied sample PDFs remain in `sample_pdfs`. The database persists between app restarts. This is one shared classroom demo database, so run one application worker and avoid sharing it with unrelated users.

## Screenshots to submit

Take actual browser screenshots after running the application:

1. **PDF upload:** five selected chapter filenames in the file picker or its upload selection display.
2. **Embeddings:** the success message showing five PDFs and the embedded chunk count.
3. **Saved database:** the database status card and File Explorer showing `data/chroma_db`, including `chroma.sqlite3`. Restart the app and show that the saved count remains.
4. **Answer 1:** variables question, Grok's answer and sources.
5. **Answer 2:** list-versus-tuple question, answer and sources.
6. **Answer 3:** for-loop question, answer and sources.

Do not submit sample text or simulated answers as real Grok screenshots. This delivery does not include screenshots of real Grok responses because no xAI key was supplied.

## GitHub repository

Create a new empty repository named `ai-study-assistant` on GitHub. In this project folder run:

```powershell
git init
git add .
git commit -m "Add AI Study Assistant assignment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/ai-study-assistant.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your actual username. Submit the resulting repository URL. `.gitignore` keeps the API key, environment and local vector database out of GitHub. Include your generated database in the private project-folder submission if your teacher requires saved vectors.

## Deploy the Python app

Use a Python/Docker-capable hosting service, such as Render. A static website host cannot run this FastAPI/Chroma backend.

1. Push the project to GitHub, sign into your hosting account and create a web service from the repository.
2. Use the included Dockerfile. For a native Python service, use Python 3.11, build command `pip install -r requirements.txt`, and start command `uvicorn main:app --host 0.0.0.0 --port $PORT`.
3. Add secret environment variable `XAI_API_KEY` and variable `GROK_MODEL=grok-4.7` (or your available model).
4. Add a persistent disk mounted at `/var/data` and set `DATA_DIR=/var/data`. Disk availability depends on the host and plan. Without a persistent disk, the local database may disappear after restart or redeployment, and you must upload the PDFs again.
5. Deploy, open the service's HTTPS URL, upload the five PDFs, and test three questions. Submit that actual URL.

Choose a service with enough memory for Chroma and its embedding model. First-use model download also needs outgoing internet. Hosting plans and limits can change; check the host's setup before deployment. No public deployment or repository URL has been created as part of this folder.

## Common problems

- **Python not recognized:** install Python and reopen the terminal.
- **Missing Grok key:** edit `.env`, then restart the server. A ChatGPT subscription is not an xAI API key.
- **Grok request failed:** check the xAI key, credits and model access. The app does not create fake answers when Grok fails.
- **Embedding download failed:** check internet and available disk space, then retry the upload.
- **Empty text PDF:** use a text-based PDF or apply OCR first.
- **Port already used:** add `--port 8001` and open the matching address.

## Short viva preparation (Roman Urdu)

- **RAG kya hai?** Pehle apni PDFs se relevant text dhoondte hain, phir woh text AI ko answer banane ke liye dete hain.
- **Embedding kya hai?** Text ka numerical vector jo us ke meaning ko represent karta hai.
- **Chunks kyun?** Bari PDF ko chhote hisson mein divide karte hain taake relevant hissa dhoondna asaan ho.
- **ChromaDB kyun?** Embeddings aur unka text locally save aur search karne ke liye.
- **Overlap kyun?** Do chunks ke darmiyan baat ka connection kam tootay.
- **FastAPI kya karta hai?** Browser se files/questions leta hai aur Python functions ka result wapas bhejta hai.
- **Grok ka role?** Retrieved context ko use karke simple answer generate karta hai.
- **Kya model train kiya?** Nahi. Existing embedding model aur Grok use kiye; apni PDFs ko index kiya hai.

## Reference documentation

- FastAPI uploads: https://fastapi.tiangolo.com/tutorial/request-files/
- Chroma clients: https://docs.trychroma.com/docs/run-chroma/cloud-client
- Chroma embeddings: https://docs.trychroma.com/docs/embeddings/embedding-functions
- xAI text generation: https://docs.x.ai/developers/model-capabilities/text/generate-text

## Submission status

Included: source code, five sample chapter PDFs, dependencies, environment template, Dockerfile and setup guide.

Still requires a real run with your xAI key: three Grok answer screenshots. GitHub repository and deployed URL require your accounts. See `VERIFICATION.md` for what was actually tested during preparation.
