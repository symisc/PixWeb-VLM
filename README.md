# PixWeb & pixVLM Small

Two small vision language models from PixLab: **PixWeb** for web tasks and browser automation, and **pixVLM Small** for general-purpose vision.

[Product page](https://pixlab.io/pix-web-small-vision-models) · [Hugging Face organization](https://huggingface.co/symiscsys) · [Commercial enquiries](mailto:licensing@pixlab.io)

> **In development.** This repository currently contains project documentation. Model weights, training code, browser demos and measured benchmarks are not yet available here.

## Models

| Model | Focus | Intended tasks |
|---|---|---|
| **PixWeb** | Web understanding | Read UI text, describe screenshots, identify visible controls and page states, and supply observations for browser automation |
| **pixVLM Small** | General-purpose vision | OCR, common-object recognition, image questions, captions and selected fields as JSON |

PixWeb supplies observations to a separate planner and browser controller. The integrating application chooses and executes actions, then checks the resulting page.

pixVLM Small targets photographs, receipts, labels and simple forms. Fine-grained OCR needs its own accuracy testing; object recognition does not imply a released detector, segmentation model or tracker.

## Deployment targets

- **WebGPU:** local GPU inference in compatible browsers.
- **WebAssembly (WASM):** a separate CPU inference path, subject to actual operator, memory and performance validation.
- **ONNX:** exports for browser and compatible native runtimes.
- **Quantization:** evaluate INT4/q4 and INT8 profiles against reference outputs.
- **Edge and embedded:** capable computers, kiosks and gateways, with support documented per tested device.

Model inference should remain local unless the application explicitly selects a remote route. A browser agent's planner may have its own network and data requirements.

## Development direction

Use pretrained models and small task-specific fine-tunes on free public datasets with suitable licenses. Share training, evaluation and export tools while keeping the two models' weights, datasets and results separate.

1. Compare candidate checkpoints on the tasks each model is intended to perform.
2. Validate native inference, ONNX, WebGPU and genuine CPU/WASM execution.
3. Run a modest LoRA pilot on one 24GB NVIDIA GPU.
4. Publish only evaluated checkpoints, deployment profiles and reproducible results.

See the [roadmap](plan.md), [training approach](TRAINING.md) and [dataset policy](DATASETS.md). These documents describe work to implement; they are not installation instructions for an existing package.

## Model releases

The intended Hugging Face repositories are `symiscsys/pixweb` and `symiscsys/pixvlm-small`. They are publication targets, not current download links.

GitHub will hold code and documentation. Hugging Face will hold model artifacts, model cards, processor files and validated exports. Model cards will link back to this repository and the PixLab product page.

Keep model weights, downloaded datasets, local credentials and private marketing drafts out of this Git repository.

## Licensing

PixLab-owned source code uses [AGPL-3.0-only](LICENSE). Model artifacts have separate, upstream-dependent terms; see [model licensing](MODEL_LICENSE.md) and [attribution notes](NOTICE.txt).

For integration, OEM support or alternative licensing of PixLab-owned contributions, see [commercial enquiries](COMMERCIAL.md). PixLab terms do not replace third-party licenses.
