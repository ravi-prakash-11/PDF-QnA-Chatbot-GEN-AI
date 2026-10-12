# PDF Question-Answering Chatbot

A Flask web app that lets you upload a PDF and ask questions about its contents. It extracts and splits the PDF text, creates embeddings, stores them in a Chroma vector store, and uses a retrieval QA chain to answer questions.

The project includes two language-model backends:

- Hugging Face Hub (`worker_huggingFace.py`)
- IBM watsonx.ai (`Worker_completed.py`)

## Requirements

- Python 3.9 or later
- Credentials for the backend you intend to use
- A Hugging Face token for the Hugging Face backend, or IBM watsonx.ai credentials for the watsonx backend

## Setup

Run these commands from the project directory in PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If PowerShell prevents virtual-environment activation, either allow script execution for your user or run the environment's Python directly:

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

## Select a model backend

`server.py` imports a module named `worker`, so one of the backend files must be available as `worker.py` before starting the server. Keep the original backend files; copy the one you want to use:

```powershell
# Hugging Face
Copy-Item .\worker_huggingFace.py .\worker.py

# OR, for IBM watsonx.ai
Copy-Item .\Worker_completed.py .\worker.py
```

Use only one backend at a time. If `worker.py` already exists, replace it with the chosen backend copy.

## Configure credentials

### Hugging Face

The Hugging Face worker calls `load_dotenv()`, which reads a `.env` file when the worker is imported. Copy the example and set your token:

```powershell
Copy-Item .\.env.example .\.env
```

Edit `.env` and set:

```dotenv
HUGGINGFACEHUB_API_TOKEN=your_hugging_face_access_token
```

Do not commit `.env` or put a real token in source code.

### IBM watsonx.ai

Set the environment variables used by `Worker_completed.py` before starting the app:

```powershell
$env:MODEL_ID = "your-watsonx-model-id"
$env:WATSONX_URL = "your-watsonx-url"
$env:PROJECT_ID = "your-watsonx-project-id"
$env:WATSONX_API_KEY = "your-watsonx-api-key"
```

Although `.env.example` lists these variables, the watsonx worker currently does not call `load_dotenv()`. Therefore, simply placing these values in `.env` will not load them; set them in the process environment as shown above.

## Run

With the chosen backend copied to `worker.py` and its credentials configured, start the Flask server:

```powershell
python server.py
```

Open [http://localhost:8000](http://localhost:8000) in a browser. Upload a PDF first, then ask questions about that document in the chat.

The Flask development server runs with debug mode enabled in `server.py`; use a production WSGI server and appropriate security settings before deployment.
