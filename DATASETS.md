# Public dataset plan

Use free public datasets and their existing annotations for PixWeb and pixVLM Small. Keep separate training and evaluation pools for the two models. The first pilot does not require private image collection, paid labeling APIs or a custom data generator.

## Initial shortlist

These are candidate sources to sample and check, not datasets already used for training.

| Model / task | Public dataset | Available supervision | Published terms |
|---|---|---|---|
| PixWeb: page layout and visible text | [WebSight](https://huggingface.co/datasets/HuggingFaceM4/WebSight) | Website screenshots paired with HTML/CSS; derive only targets visible in the screenshot | CC BY 4.0, source-content terms and disclosure of dataset use |
| PixWeb: UI elements and text | [ScreenParse](https://huggingface.co/datasets/docling-project/screenparse) | Element labels, boxes, visible text, interactability and reading order | CC BY 4.0; retain source-page provenance |
| pixVLM Small: scene OCR | [TextOCR](https://textvqa.org/textocr/dataset/) | Word transcriptions and text regions in natural images | CC BY 4.0 annotations; verify underlying Open Images licenses |
| pixVLM Small: receipt extraction | [CORD v2](https://huggingface.co/datasets/naver-clova-ix/cord-v2) | Indonesian receipt images, OCR annotations and structured item/amount fields | CC BY 4.0 |
| pixVLM Small: captions and objects | [COCO](https://cocodataset.org/) | Existing captions, object categories and instance annotations | CC BY 4.0 annotations; image-specific licenses |
| pixVLM Small: object recognition | [Open Images](https://storage.googleapis.com/openimages/web/index.html) | Image labels, object boxes and relationships | CC BY 4.0 annotations; verify image-specific CC BY 2.0 records |

WebSight contains publicly released synthetic pages; using those records does not require building a new generator. Screenshot/HTML pairs and UI-element labels need conversion into PixWeb observation tasks. They do not provide complete browser-action trajectories.

CORD's public release covers receipt items and amounts but removes store and payment information. Do not invent merchant, address or date labels that the source does not provide. Document these coverage limits when evaluating extraction.

## Selection and licensing

- Download from the original publisher or its official dataset repository.
- Use small subsets first rather than entire image archives.
- Free access does not imply unrestricted reuse. Retain dataset terms, image licenses, attribution and modification records.
- Check source-image rights separately when the annotation license does not cover them.
- Exclude unclear rights or restrictions incompatible with the intended model release.

Sources for those distinctions: [WebSight terms](https://huggingface.co/datasets/HuggingFaceM4/WebSight#terms-of-use), [COCO terms](https://github.com/cocodataset/cocodataset.github.io/blob/master/dataset/termsofuse.htm), [Open Images licensing](https://storage.googleapis.com/openimages/web/factsfigures_v7.html) and [CORD's public label specification](https://github.com/clovaai/cord).

## Pilot size and splits

Start with up to **5,000 training examples per model**, plus **200 development** and **400 held-out evaluation examples** across the relevant tasks. These are total pilot limits, not a requirement for every source dataset.

- Preserve official train/validation/test boundaries; never move official test examples into training.
- When a dataset has only a training split, separate source images, sites and page-template families before deriving task variants.
- Deduplicate by image hash and source identity across datasets and splits.
- Keep held-out inputs unchanged while comparing candidate models and adapters.
- Include readable and difficult cases; report small-text and degraded-image results separately.
- Expand the training pool only when measured failures justify it.

## Provenance record

Retain the source URL, pinned dataset revision, sample ID, image hash, annotation version, license and attribution for each example. Also record its task, prompt, target, source split, split group and any transformations used to produce the training record.

Keep downloaded data outside Git. Commit small manifests, preparation code and permitted test fixtures when implemented.

## Quality checks

**PixWeb:** compare UI text, labels and element claims with the screenshot. HTML may include hidden or off-screen content; do not treat all source text as visible. Keep observation labels separate from planner decisions and action sequences.

**pixVLM Small:** check OCR against the published transcription, exclude illegible/placeholder targets, and map extraction fields only from real source annotations. Use grounded object questions and captions; missing annotations are not proof of absence or an exact count.

Reject corrupted images, mismatched targets, duplicate records and invalid extraction JSON. Review a sample visually before a training run.

## Release record

List only the datasets actually used, their revisions, split construction, required attribution and coverage limits in each model card.
