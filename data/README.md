# SignVision

### AI-Powered Speech-to-Indian Sign Language Translation

SignVision is an assistive-technology application that converts typed text or speech into Indian Sign Language (ISL) expressions and presents them through an animated 3D avatar.

The project combines a React Native mobile interface, a FastAPI backend, natural-language processing, semantic sign lookup, SiGML assets, and CWASA-compatible avatar playback.

> **Team:** GoldMiners<br>
> **Team ID:** KH037<br>
> **Project:** VaaniMudra — An AI-Powered Speech-to-Indian Sign Language Translation System

## Why SignVision?

Communication becomes difficult when a hearing person and an ISL user do not share a common language. SignVision provides a simple bridge:

```text
Speech or text → NLP analysis → ISL GLOSS → Sign lookup → SiGML → 3D avatar playback
```

## Key Features

- **Word mode:** Search for an individual word and play its corresponding sign.
- **Sentence mode:** Process complete sentences instead of translating word-by-word.
- **Speech input:** Convert recorded speech to text and process it through the same pipeline.
- **ISL-aware processing:** Extract subject, object, tense, negation, question type, and important sentence meaning.
- **GLOSS preview:** Display the generated ISL GLOSS sequence before or during playback.
- **Semantic fallback:** Use exact, anchor, partial, and semantic matching when a direct sign match is unavailable.
- **3D avatar rendering:** Play sign animations using SiGML assets inside the mobile application.
- **Search history:** Save recent searches locally for quick reuse.
- **Accessibility-first direction:** The architecture is designed to support future assistive modules such as Vision Assist for visually impaired users.

## System Architecture

![SignVision system architecture](docs/architecture.png)

## Technology Stack

### Mobile application

- React Native
- Expo
- TypeScript
- React Native WebView
- Expo Speech Recognition
- AsyncStorage

### Backend

- Python
- FastAPI
- Uvicorn
- spaCy
- OpenAI-compatible LLM API / Groq
- Sentence Transformers
- ChromaDB
- SpeechRecognition and pydub

### Sign and avatar layer

- ISL GLOSS generation
- HamNoSys/SiGML sign assets
- CWASA-compatible avatar playback
- Local sign metadata and SiGML files

## Repository Structure

```text
Signvision/
├── Application/                  # React Native + Expo mobile application
│   ├── src/components/           # Search bar and avatar WebView
│   ├── src/screens/              # Application screens
│   ├── src/services/             # API and local sign lookup services
│   ├── src/assets/               # Avatar player and frontend assets
│   └── public/sigml/             # Frontend SiGML assets
├── backend/                      # FastAPI backend
│   ├── app/core/                 # Configuration and NLP engine
│   ├── app/services/             # Sign lookup and semantic search
│   ├── data/                     # Backend sign metadata and SiGML assets
│   └── requirements.txt          # Python dependencies
├── data/                         # Project-level datasets and sign assets
├── dataset/                      # Supporting ISL and HamNoSys datasets
├── docs/                         # Architecture and project documentation
└── README.md
```

## Prerequisites

- Node.js 20 or newer
- npm or pnpm
- Python 3.10 or newer
- FFmpeg in `PATH` for formats such as MP3, M4A, OGG, or AAC
- Android Studio and an Android device/emulator for mobile testing
- A Groq API key for sentence-level semantic processing

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/Pruthviraj141/Signvision.git
cd Signvision
```

### 2. Configure the backend

```bash
cd backend
python -m venv .venv
```

Activate the virtual environment:

**Windows PowerShell**

```powershell
.\.venv\Scripts\Activate.ps1
```

**macOS/Linux**

```bash
source .venv/bin/activate
```

Install dependencies and the spaCy language model:

```bash
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

Create the environment file:

**Windows PowerShell**

```powershell
Copy-Item .env.example .env
```

**macOS/Linux**

```bash
cp .env.example .env
```

Add your API key to `backend/.env`:

```env
GROQ_API_KEY=gsk_your_api_key_here
GPT_BASE_URL=https://api.groq.com/openai/v1
GPT_MODEL=llama-3.3-70b-versatile
```

Never commit `.env` or expose API keys publicly.

### 3. Build semantic-search embeddings

Run this once from the `backend` directory if semantic fallback is required:

```bash
python -c "from app.services.semantic_search import build_embeddings; build_embeddings()"
```

This creates the local ChromaDB store under `backend/data/chroma_db`.

### 4. Start the backend

From the `backend` directory:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at:

- API root: <http://localhost:8000>
- Swagger documentation: <http://localhost:8000/docs>
- ReDoc documentation: <http://localhost:8000/redoc>

### 5. Start the mobile application

Open a second terminal:

```bash
cd Application
pnpm install
pnpm start
```

Then press `a` to open the application on an Android emulator or connected device.

For a physical device, keep the phone and development computer on the same Wi-Fi network. If the backend cannot be reached, update the development API address in `Application/src/services/apiService.ts`.

## Backend API

| Endpoint | Method | Purpose |
|---|---:|---|
| `/process` | `POST` | Convert text into GLOSS and sign lookup results |
| `/process_text` | `POST` | Convert text into GLOSS without sign lookup |
| `/transcribe` | `POST` | Convert an audio file into text, GLOSS, and sign results |
| `/lookup/{word}` | `GET` | Look up one sign by word |
| `/search` | `GET` | Search semantically similar sign entries |
| `/sigml/{filename}` | `GET` | Serve a SiGML asset |
| `/health` | `GET` | Check backend health and loaded sign count |

### Example text request

```bash
curl -X POST http://localhost:8000/process \
  -H "Content-Type: application/json" \
  -d '{"text":"I am going to school","use_semantic_fallback":true}'
```

## Processing Pipeline

1. Receive typed text or audio input.
2. Convert audio to text when speech input is used.
3. Normalize contractions, sentences, and punctuation.
4. Extract linguistic features using spaCy.
5. Use the configured LLM to extract structured meaning.
6. Generate ISL-oriented GLOSS using rule-based processing.
7. Resolve signs through exact, anchor, partial, or semantic matching.
8. Load the matching SiGML asset.
9. Render the sign sequence through the 3D avatar.

## Responsible Use

SignVision is a research and prototype project. It should support communication, not replace qualified ISL interpreters or community-validated language resources. Sign mappings and generated GLOSS should be reviewed with Deaf and hard-of-hearing users and ISL experts before production deployment.

## Future Development

- Vision Assist: capture a short sequence of images and describe the main scene through audio.
- Larger, community-validated ISL vocabulary.
- Improved sentence-level grammar and regional variation support.
- Offline speech recognition and on-device inference.
- Production deployment with secure API configuration.
- User testing and formal accessibility evaluation.

## Team

**GoldMiners — KH037**

- Sanket Mahajan — Team Leader
- Onkar Sathe
- Pruthviraj Gavhane
- Sakshi Khutwad

## License

No open-source license has been added yet. Until a license is included in the repository, all rights are reserved by the project team.

## Project Links

- GitHub: <https://github.com/Pruthviraj141/Signvision>
- Documentation: [docs/](docs/)
- Architecture: [docs/architecture.png](docs/architecture.png)
