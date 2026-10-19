# Verification during preparation

Passed:

- Python source parses without syntax errors.
- Browser JavaScript parses without syntax errors.
- Groq migration checked using mocked dependencies: endpoint and model, context passed to Chat Completions, answer and sources, matching UI/status field, missing key, empty question/database, API failure and empty answer handling. These checks do not call the real API or ChromaDB.
- Chunking checks: 150-word chunk size, 30-word overlap, final-word coverage, empty input.
- All five sample PDFs contain extractable text (one page each).
- Rendered all five PDF pages and visually reviewed layout.

Not run:

- FastAPI HTTP routes and multipart upload integration.
- Actual embedding generation, semantic retrieval and database persistence across restart.
- Real Groq Groq calls and screenshots of answers.
- Container build, remote deployment and public GitHub publication.

Reason: FastAPI, ChromaDB and the API SDK were not available in the preparation runtime. Dependency installation was attempted but the environment blocked outbound package connections (Windows socket error 10013). No Groq API key or hosting destination was provided. The deliverable contains runnable source and instructions, but these runtime checks must be completed after installing requirements with internet access. No saved database, answer screenshots, repository URL or deployed URL is claimed as completed.

After setup, check invalid uploads (four files, duplicate filenames, unreadable PDFs), a successful five-file upload, three Groq questions, and a server restart with the same saved chunk count. Also confirm a question outside the notes is handled appropriately.
