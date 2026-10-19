# ROOMCRAFT-ML

An AI-powered interior design web app for creating and refining room concepts from a natural-language description or an uploaded room photo. ROOMCRAFT-ML combines a Flask web application, rule-based prompt analysis, YOLOv8 image segmentation, and Stable Diffusion image generation.

> Educational project. Model inference runs locally, but downloading the configured Stable Diffusion models requires a Hugging Face account and read token. Model license terms and hardware requirements apply.

## Features

- Generate interior design images from plain-English prompts.
- Analyze design prompts and extract room type, style, colors, furniture, materials, lighting, and other details.
- Upload a room photo and segment objects with YOLOv8.
- Redesign, edit, enhance, and create a top-down view of designs.
- Estimate project costs and compare basic, standard, premium, and luxury tiers.
- Choose fast or quality image-generation modes.
- Use the browser-based interface to explore supported room types, styles, and color palettes.

## Tech stack

| Area | Technology |
| --- | --- |
| Web application | Python, Flask |
| Prompt analysis | Custom rule-based Python NLP |
| Image segmentation | YOLOv8, Ultralytics |
| Image generation | Stable Diffusion, Hugging Face Diffusers |
| Image processing | OpenCV, Pillow, NumPy |
| Frontend | HTML, CSS, JavaScript |

## Requirements

- Python 3.10 or newer. Python 3.11 is recommended for the pinned dependencies.
- Internet access to install dependencies and download model weights.
- Several GB of free disk space for packages, models, and generated images.
- A Hugging Face account and a **Read** access token for model downloads.
- An NVIDIA GPU is recommended. CPU inference is supported but can be slow and memory-intensive.

## Installation

### Windows PowerShell

```powershell
git clone https://github.com/abhijeetjadhav1726-spec/ROOMCRAFT-ML.git
Set-Location ROOMCRAFT-ML

py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1

# Install PyTorch before the rest of the requirements.
python -m pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r requirements.txt

# Download the YOLOv8 segmentation weights.
python -c "from ultralytics import YOLO; YOLO('yolov8n-seg.pt')"
```

If PowerShell prevents virtual-environment activation, you can run commands directly with `.venv\Scripts\python.exe` instead. For an NVIDIA GPU, install the PyTorch build that matches your CUDA setup using the instructions at [pytorch.org/get-started/locally](https://pytorch.org/get-started/locally/), then install `requirements.txt`.

### macOS or Linux

```bash
git clone https://github.com/abhijeetjadhav1726-spec/ROOMCRAFT-ML.git
cd ROOMCRAFT-ML

python3 -m venv .venv
source .venv/bin/activate

# Install PyTorch before the rest of the requirements.
python -m pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r requirements.txt

# Download the YOLOv8 segmentation weights.
python -c "from ultralytics import YOLO; YOLO('yolov8n-seg.pt')"
```

## Hugging Face setup

1. Create an account at [huggingface.co/join](https://huggingface.co/join).
2. Open [Hugging Face Access Tokens](https://huggingface.co/settings/tokens) and create a token with **Read** access.
3. Sign in and visit the model pages used by the app. Accept any access conditions shown there:
   - [LCM Stable Diffusion v1.5](https://huggingface.co/rupeshs/LCM-runwayml-stable-diffusion-v1-5) (fast mode)
   - [Stable Diffusion v1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5) (quality mode)
4. Create a file named `.env` in the project root, next to `app.py`, and add:

   ```env
   HUGGINGFACE_TOKEN=hf_replace_this_with_your_read_token
   FLASK_SECRET_KEY=replace_with_a_long_random_secret
   FLASK_DEBUG=False
   MAX_FILE_SIZE=16777216
   ```

   Replace the example token and secret with your own values. **Never commit or share `.env`**; it contains credentials.

## Run

From the project root, with the virtual environment activated:

```bash
python app.py
```

Open [http://127.0.0.1:5000](http://127.0.0.1:5000). The app creates its runtime directories when it starts. Model files are downloaded on first use, so the first generation can take several minutes. The Flask development server is for local development and should not be exposed directly to the public internet.

## API endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/` | Web interface |
| `POST` | `/api/segment` | Segment an uploaded room photo |
| `POST` | `/api/analyze-prompt` | Parse a design prompt |
| `POST` | `/api/generate-design` | Generate a design image |
| `POST` | `/api/redesign-room` | Redesign a room from an uploaded image |
| `POST` | `/api/edit-room` | Apply targeted edits |
| `POST` | `/api/enhance-design` | Enhance an existing design |
| `POST` | `/api/generate-top-view` | Generate a top-down room view |
| `POST` | `/api/estimate-cost` | Estimate design costs |
| `POST` | `/api/cost-comparison` | Compare cost tiers |
| `GET` | `/api/room-types` | List room types and design options |
| `POST` | `/api/switch-mode` | Switch between `fast` and `quality` generation modes |
| `GET` | `/api/get-mode` | Get the current generation mode |
| `GET` | `/uploads/<filename>` | Serve an uploaded image |

## Troubleshooting

- **Missing Hugging Face token:** Check that `.env` is in the project root, contains `HUGGINGFACE_TOKEN=...`, and restart the app.
- **Model access or download errors:** Check internet access, token validity, and any conditions on the model pages.
- **Slow CPU generation:** This is expected for Stable Diffusion. A supported NVIDIA GPU can substantially reduce generation time.
- **CUDA out of memory:** Close other GPU applications or use a GPU with more available VRAM.
- **Python package installation errors:** Use Python 3.10 or 3.11, install PyTorch first, then install `requirements.txt`.

Generated images, user uploads, local model weights, and environment files are runtime data and should not be committed to Git.

## Acknowledgements

- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) for object segmentation.
- [Hugging Face Diffusers](https://github.com/huggingface/diffusers) for Stable Diffusion pipelines.
- [Hugging Face Hub](https://huggingface.co/docs/huggingface_hub) for model hosting and downloads.
