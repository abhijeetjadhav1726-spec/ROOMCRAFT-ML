# 🏠 Dream Space — AI-Based Interior Designer

An AI-powered interior design platform that turns a room photo and a plain-English description into a fully realized design — combining **image segmentation**, **natural language processing**, and **AI image generation** in a single Flask app.

> 🎓 Final-year B.Tech project (2026).

---

## ✨ Features

- 🎨 **AI-Generated Designs** — Describe your dream room in plain English and get a photorealistic render back, powered by Stable Diffusion.
- 🔍 **Room Segmentation** — YOLOv8 detects walls, furniture, and structural elements in an uploaded photo, and the app estimates each wall's dominant color and direction.
- 💬 **Natural Language Prompt Understanding** — A custom NLP module parses free-text prompts into structured design data: room type, style, dimensions, colors, furniture, materials, lighting, and mood.
- 🏠 **Multiple Room Types** — Bedroom, kitchen, bathroom, living room, office, lab, classroom, auditorium, hall, and more.
- 🎯 **Directional Color Control** — Set different wall colors for North / South / East / West.
- 🔁 **Iterative Redesign & Enhancement** — Regenerate a design with edits, or enhance an existing render without starting from scratch.
- 🖼️ **Top-View Generation** — Produce a top-down layout view of the designed room.
- ⚡ **Fast / Quality Modes** — Switch between a fast draft mode and a higher-fidelity quality mode for image generation.
- 💰 **Cost Estimation & Comparison** — Get a budget breakdown for a design and compare costs across options.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python, Flask |
| Room Segmentation | YOLOv8 (`ultralytics`) |
| Depth Estimation | MiDaS (via `torch.hub`) |
| Image Generation | Stable Diffusion (Hugging Face `diffusers`) |
| Prompt Understanding | Custom rule-based NLP (`utils/nlp_processor.py`) |
| Frontend | HTML, CSS, JavaScript |

> This project runs entirely on local/open-source models (YOLOv8 + Stable Diffusion + MiDaS) — the only external credential required is a free Hugging Face access token. No paid API keys are needed to run it.

---

## 🗂️ Project Structure

```
dream-space-ai-interior-designer/
├── app.py                     # Flask app & API routes
├── cost_estimator.py          # Budget estimation & comparison logic
├── verify_models.py           # Sanity-check script for model downloads
├── requirements.txt
├── .env.example
├── models/
│   └── segmentation.py        # YOLOv8-based room/wall segmentation
├── utils/
│   ├── nlp_processor.py       # Prompt → structured design data
│   └── image_generator.py     # Stable Diffusion generation, redesign, enhancement
├── templates/
│   └── index.html
├── static/
│   ├── css/style.css
│   ├── js/main.js
│   └── generated/             # Generated design images
├── uploads/                   # User-uploaded room photos
└── outputs/                   # Saved outputs (masks, reports, etc.)
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- ~5 GB free disk space (for model weights)
- A GPU is recommended for Stable Diffusion, but the app also runs on CPU in *fast* mode
- A free [Hugging Face](https://huggingface.co/settings/tokens) account & access token

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/dream-space-ai-interior-designer.git
cd dream-space-ai-interior-designer

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows

# 3. Install PyTorch first (choose the line for your setup)
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu   # CPU only
# pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118  # NVIDIA GPU

# 4. Install the remaining dependencies
pip install -r requirements.txt

# 5. Download the YOLOv8 segmentation model
python -c "from ultralytics import YOLO; YOLO('yolov8n-seg.pt')"

# 6. Create the required runtime directories
mkdir uploads outputs static/generated

# 7. Configure environment variables
cp .env.example .env
# then open .env and add your Hugging Face token

# 8. Run the app
python app.py
```

Open your browser at 👉 `http://localhost:5000`

> ℹ️ On first run, MiDaS depth-estimation weights (~80 MB) will auto-download via `torch.hub`, and Stable Diffusion weights will download via Hugging Face — this can take a few minutes depending on your connection.

---

## ⚙️ Environment Variables

Create a `.env` file in the project root (a template is provided in `.env.example`):

```env
# Hugging Face token — required to download Stable Diffusion weights
HUGGINGFACE_TOKEN=your_huggingface_token_here

# Flask configuration
FLASK_SECRET_KEY=your_secret_key_here
FLASK_DEBUG=True

# Upload configuration
MAX_FILE_SIZE=your_max_file_size_here
UPLOAD_FOLDER=uploads

# Use local models (no external API costs)
USE_LOCAL_MODELS=True
```

⚠️ **Never commit your real `.env` file.** Only `.env.example` (with placeholder values) should be tracked in Git.

---

## 📡 API Reference

| Method | Endpoint | Description |
|---|---|---|
| `GET`  | `/` | Serves the web UI |
| `POST` | `/api/segment` | Segment an uploaded room image into walls/furniture and detect wall colors |
| `POST` | `/api/analyze-prompt` | Parse a free-text design prompt into structured data |
| `POST` | `/api/generate-design` | Generate a new interior design from a prompt |
| `POST` | `/api/redesign-room` | Regenerate a design based on edits to a previous one |
| `POST` | `/api/edit-room` | Apply targeted edits to an existing design |
| `POST` | `/api/enhance-design` | Enhance/upscale an already-generated design |
| `POST` | `/api/generate-top-view` | Generate a top-down layout view of the room |
| `POST` | `/api/estimate-cost` | Estimate the budget for a generated design |
| `POST` | `/api/cost-comparison` | Compare estimated costs across design options |
| `GET`  | `/api/room-types` | List supported room types, furniture options, styles, and color palettes |
| `POST` | `/api/switch-mode` | Switch image generation between `fast` and `quality` mode |
| `GET`  | `/api/get-mode` | Get the current generation mode |
| `GET`  | `/uploads/<filename>` | Serve an uploaded image |

---

## 🖥️ How It Works

1. **Upload** a photo of your room (optional) — YOLOv8 segments walls and furniture and estimates each wall's current color by direction.
2. **Describe** your desired design in natural language (e.g. *"a cozy modern bedroom with a warm beige north wall and wooden furniture"*).
3. The **NLP module** parses your prompt into structured fields: room type, style, dimensions, per-wall colors, furniture, materials, lighting, and mood.
4. The **image generator** assembles a detailed Stable Diffusion prompt from that structured data and renders your design.
5. **Redesign, enhance, or generate a top-view** of the result, and get a **cost estimate** for the furniture and materials used.

---

## 🐞 Troubleshooting

- **App won't start / import errors** — make sure PyTorch was installed *before* `requirements.txt` (see step 3 above).
- **`CUDA out of memory`** — switch to `fast` mode via `/api/switch-mode`, or reduce the input image resolution.
- **Slow generation on CPU** — this is expected; use `fast` mode, or run on a machine with an NVIDIA GPU for `quality` mode.
- **Model download fails** — check your internet connection and that `HUGGINGFACE_TOKEN` in `.env` is valid.
- **Change the port** — edit the `app.run(...)` call at the bottom of `app.py`.

---

## 🌟 Roadmap

- [ ] 3D model generation & VR walkthroughs
- [ ] Real-time collaborative design sessions
- [ ] Furniture retailer integration for direct purchase links
- [ ] Multi-room floor plan support
- [ ] Mobile app version

---

## 📜 License

This project was built for educational purposes as part of a B.Tech final-year project. Please comply with the license terms of all third-party models and libraries used (YOLOv8, Stable Diffusion, MiDaS).

---

## 🙏 Acknowledgements

- [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics) for room/object segmentation
- [Hugging Face Diffusers](https://github.com/huggingface/diffusers) for Stable Diffusion image generation
- [Intel ISL MiDaS](https://github.com/isl-org/MiDaS) for monocular depth estimation
