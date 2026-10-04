# Training approach

Status: implementation plan. This repository does not yet contain a trainer or trained PixLab checkpoints.

## Starting points

**PixWeb:** evaluate the [Liquid native checkpoint](https://huggingface.co/LiquidAI/LFM2.5-VL-450M) as the pretrained starting point. Adapt it directly using free public screenshot datasets and evaluate on held-out sites and layouts.

**pixVLM Small:** evaluate a general pretrained VLM against OCR, object recognition, captions, image questions and extraction. Keep the total merged model at or below 700M parameters. Do not assume a browser-specialist fine-tune is a suitable general-vision base.

The Liquid candidate contains approximately 448.7M parameters. Its model card warns about fine-grained OCR, so evaluate general vision and text-reading quality before selecting pixVLM Small's base.

Use complete pretrained VLMs. Custom architecture construction, random projector alignment and compulsory teacher distillation are outside the first pilot.

## Before training

1. Pin the checkpoint and matching processor, tokenizer and chat template.
2. Run the untouched model on the task-specific development and held-out sets.
3. Verify native inference, ONNX export, WebGPU and a real CPU/WASM route.
4. Record task scores, failure examples and deployment measurements.
5. Confirm the selected upstream model and dataset terms.

Follow [DATASETS.md](DATASETS.md) for data selection and split rules.

## Initial LoRA pilot

Run each model separately on one 24GB NVIDIA GPU. The following settings are starting values for an LFM-based pilot, not a claim that a PixLab training run has succeeded.

| Setting | Initial value |
|---|---|
| Training method | Supervised LoRA fine-tuning |
| Training size | Up to 5,000 reviewed examples per model |
| Epochs | 1 |
| Rank / alpha / dropout | 16 / 32 / 0.05 |
| Batch / accumulation | 1 / 16 |
| Learning rate | 1e-4, cosine schedule, 3% warmup |
| Precision | BF16 with gradient checkpointing |
| Attention implementation | SDPA for the LFM/SigLIP2 candidate |
| Inputs | One image and a task prompt |
| Image budget | Explicitly cap tiles and image tokens; measure peak memory |

These are initial pilot settings to validate, not measured training results. Use the [Liquid fine-tuning implementation](https://github.com/Liquid4All/liquid-finetune) as the implementation reference for an LFM-based run. Measure peak memory with the actual image budget; a per-tile token limit is not the entire image budget.

## Implementation requirements

- Use the architecture's supported adapter targets, including relevant convolution, attention, vision and projector modules.
- Train on assistant answers; mask prompt and image inputs from the loss.
- Preserve the processor and image preprocessing used for each checkpoint.
- Check a short run, finite loss, checkpoint reload and adapter merge before the full pilot.
- Avoid silently truncating image tokens or structured extraction answers.
- Use existing public annotations and derive task prompts from them. The initial pilot does not require paid labeling APIs or a custom synthetic-data pipeline.

## Evaluation and export

Compare the baseline and adapted model on unchanged held-out inputs.

- **PixWeb:** UI text, fields, states, invented controls and observation completeness. Measure end-to-end automation separately with a fixed planner and browser controller.
- **pixVLM Small:** OCR error rate, object/question accuracy, grounded captions, JSON validity and extracted-field accuracy.
- **Export:** compare matching preprocessing, prefill and teacher-forced steps before comparing free-running answers.
- **Quantization:** check per-task quality and real runtime behavior for each profile.

Merge only a useful adapter, then export and validate the merged checkpoint. For LFM, start with [Liquid's ONNX exporter](https://github.com/Liquid4All/onnx-export). Save exact revisions, training settings, data provenance, results and licenses with any release.
