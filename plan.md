# PixWeb & pixVLM Small roadmap

Status: documentation and baseline evaluation planning. No PixLab model weights or runtime are released yet.

[Product page](https://pixlab.io/pix-web-small-vision-models) · [Hugging Face organization](https://huggingface.co/symiscsys)

## Product scope

| Model | Purpose | First evaluation tasks |
|---|---|---|
| PixWeb | Visual observations for web tasks and browser automation | Visible UI text, controls, form states, dialogs and changes after browser actions |
| pixVLM Small | General-purpose vision under a 700M total-parameter ceiling | OCR, common-object recognition, captions, visual questions and selected fields as JSON |

A separate planner and browser controller choose and execute PixWeb's actions. Evaluate the observation model separately from the complete automation workflow.

Share training, evaluation and export tooling. Keep each model's checkpoint, processor, dataset and results separate.

## 1. Establish the baselines

- Evaluate the [Liquid native checkpoint](https://huggingface.co/LiquidAI/LFM2.5-VL-450M) as PixWeb's pretrained starting point, using public screenshot datasets.
- Evaluate general pretrained candidates for pixVLM Small. Liquid is a candidate, not a settled choice: its model card cautions against fine-grained OCR.
- Pin each candidate's revision and matching processor, tokenizer and chat template.
- Build a small held-out set for each model from free public datasets before adapting either checkpoint.

## 2. Prove deployment before training

- Run native inference and the existing upstream ONNX exports.
- Validate WebGPU and genuine CPU/WASM inference independently.
- Start from existing export and generation support; do not build custom model architecture or runtime kernels for the pilot.
- Record download size, loading time, generation time, memory where measurable, and task quality.
- Stop and reassess a candidate if required deployment paths do not work.

The [official Liquid ONNX exporter](https://github.com/Liquid4All/onnx-export) is the starting point for LFM-based candidates. Its generation path must preserve both convolution state and attention caches.

## 3. Adapt each model to its own tasks

- Use the modest LoRA pilot described in [TRAINING.md](TRAINING.md), with one 24GB NVIDIA GPU.
- Build separate training pools from public datasets and their existing annotations under the [dataset policy](DATASETS.md).
- Compare adapted checkpoints with their untouched baselines on the held-out tasks.
- Keep an adapter only when it improves the intended tasks without unacceptable regressions.
- Merge successful adapters, export again and repeat deployment checks.

## 4. Package a usable release

- GitHub holds source, instructions and evaluation reports; Hugging Face holds model artifacts.
- Intended model IDs are `symiscsys/pixweb` and `symiscsys/pixvlm-small`. These repositories are not established by this document.
- Include the matching processor, tokenizer, template, artifact checksums, license notices and actual evaluation results.
- Document which precision profiles, browsers and devices were tested.
- Add a small image/screenshot demo after inference works. Keep model inference distinct from browser-agent execution.

## Completion criteria

- The intended task works from a reproducible native example.
- Exported outputs are checked against native outputs using identical inputs and decoding settings.
- WebGPU and CPU/WASM run real model inference; unsupported targets remain clearly marked.
- Quantization results are reported per task rather than hidden in one aggregate score.
- Model terms and required upstream notices are included before publishing artifacts.
- No unsupported speed, memory, OCR accuracy or device-compatibility claims appear in release materials.
