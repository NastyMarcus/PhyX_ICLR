# PhyX: Does Your Model Have the "Wits" for Physical Reasoning?

<p align="center">
    <img src="assets/PhyX_Logo.png" width="20%"> <br>
</p>

## 📖 Outlines

- [PhyX: Does Your Model Have the "Wits" for Physical Reasoning?](#phyx-does-your-model-have-the-wits-for-physical-reasoning)
  - [📖 Outlines](#-outlines)
  - [🔔 News](#-news)
  - [🔮 Usage](#-usage)
    - [📦 Dataset Versions](#-dataset-versions)
    - [Sample Format and Field Definitions](#sample-format-and-field-definitions)
    - [🔨 Evaluation on PhyX](#-evaluation-on-phyx)
      - [VLMEvalKit (Official)](#vlmevalkit-official)
      - [Code in this repository](#code-in-this-repository)

## 🔔 News

-  💥 Skywork-R1V3 Outperforms Qwen2.5-VL-72B with 32.0% on SeePhys! Learn more at the Skywork-R1V3 blog.
-  🎉 PhyX is officially supported by [lmms-eval](https://github.com/EvolvingLMMs-Lab/lmms-eval) for easy evalution.
-  🎉 PhyX is officially supported by [VLMEvalKit](https://github.com/open-compass/VLMEvalKit) for easy evalution.

## 🔮 Usage

### 📦 Dataset Versions

PhyX contains two subsets: `testmini` (1,000 questions) and `test` (3,000 questions). Each subset includes 12 versions tailored for different evaluation settings:

| File Name                  | Type & Input Style                                                         | Description                                                   |
| -------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------- |
| `PhyX_mini_OE.tsv`       | OE / Text-DeRedundancy (Image + Simplified Description + Question)         | OE version with simplified description                        |
| `PhyX_mini_MC.tsv`       | MC / Text-DeRedundancy (Image + Simplified Description + Question)         | MC version with simplified description                        |
| `PhyX_mini_OE_FT.tsv`    | OE / Full-Text (Image + Full Description + Question)                       | Open-ended questions with full original description and image |
| `PhyX_mini_MC_FT.tsv`    | MC / Full-Text (Image + Full Description + Question)                       | Multiple-choice version with original description and image   |
| `PhyX_mini_OE_TM.tsv`    | OE / Text-Minimal (Image + Question)                                       | OE version with image only (description removed)              |
| `PhyX_mini_MC_TM.tsv`    | MC / Text-Minimal (Image + Question)                                       | MC version with image only                                    |
| `PhyX_mini_TL_OE.tsv`    | OE / Text-DeRedundancy (Image Caption + Simplified Description + Question) | OE version with image caption and simplified description      |
| `PhyX_mini_TL_MC.tsv`    | MC / Text-DeRedundancy (Image Caption + Simplified Description + Question) | MC version with image caption and simplified description      |
| `PhyX_mini_TL_OE_FT.tsv` | OE / Full-Text (Image Caption + Full Description + Question)               | OE version with image converted to text (`image_caption`)   |
| `PhyX_mini_TL_MC_FT.tsv` | MC / Full-Text (Image Caption + Full Description + Question)               | MC version with image converted to text                       |
| `PhyX_mini_TL_OE_TM.tsv` | OE / Text-Minimal (Image Caption + Question)                               | OE version with image caption only (no description)           |
| `PhyX_mini_TL_MC_TM.tsv` | MC / Text-Minimal (Image Caption + Question)                               | MC version with image caption only (no description)           |
| **Default Setting**  | ✅ Text-DeRedundancy (MC & OE)                                             | `PhyX_mini_OE.tsv` and `PhyX_mini_MC.tsv` are default.    |

- 🔍 mini stands for the 1,000-questions testmini set.
- MC: multiple-choice
- OE: open-ended (OE)
- TL: text-only (image converted to image_caption)
- FT: full-text (image + full description + Question)
- TM: description removed (image + question, without description)

### Sample Format and Field Definitions

📘 Each entry in PhyX is stored as a JSON object with the following fields:

| Field                    | Type   | Description                                                                  |
| ------------------------ | ------ | ---------------------------------------------------------------------------- |
| `index`                | int    | Index of the question                                                        |
| `question`             | string | Question                                                                     |
| `question_description` | string | Original full description of the problem                                     |
| `question_simply`      | string | Simplified version of the question description (used in `SIMPLY` versions) |
| `options`              | string | Answer options, format:`A:"...", B:"...", ...`                             |
| `answer`               | string | Ground truth answer                                                          |
| `image`                | string | Image filename (e.g.,`200.png`)                                            |
| `image_caption`        | string | Textual description of the image (only in `TL` versions)                   |
| `category`             | string | Physics category (e.g.,`"Optics"`)                                         |
| `subfield`             | string | Fine-grained physics subfield (e.g.,`"Geometrical Optics"`)                |
| `reasoning_type`       | string | Type(s) of Physical Reasoning                                                |

You can use this format to load and evaluate different question versions based on your model’s capability (e.g., multimodal, text-only).

### 🔨 Evaluation on PhyX

#### VLMEvalKit (Official)

PhyX is officially supported by [VLMEvalKit](https://github.com/open-compass/VLMEvalKit).
You can use the official code at https://github.com/open-compass/VLMEvalKit.
Please follow the official guidance to create a pip/conda environment.

For a quick start, just use:

```
#*********judge based on rules*********
python -u run.py --data PhyX_mini_MC \
  --model GPT4o_20241120 \
  --judge-args '{"valid_type": "STR"}'


#*********deepseek v3 from siliconflow as judger*********
python -u run.py --data PhyX_mini_MC \
  --model GPT4o_20241120 \
  --judge deepseek --judge-args '{"valid_type": "LLM"}'
```

#### Code in this repository

Also, in this repository, we implement more evaluation settings. The evaluation codes are based on [VLMEvalKit](https://github.com/open-compass/VLMEvalKit), and we thank the authors for their efforts.
Please follow the [readme](README_vlmeval.md) to create a pip/conda environment.

We use DeepSeek-V3 as the LLM-based judger, and we add support for the official API.
Please set the `SiliconFlow_API_KEY` or `Deepseek_API` to use it.
The former one would employ the DeepSeek-V3 provided by SiliconFlow, and the latter one would be for official servers.

Alternatively, you can perform rule-based judgment, which is **free**.
We carefully design rules to extract the answer from outputs and then compare it with the ground truth.

➡️ VLM

To evaluate a VLM on PhyX, please refer to the examples in `examples/VLM/`, such as:

```
export LMUData=dataset  # using local dataset file folder

#*********judge based on rules*********
python -u run.py --data PhyX_mini_OE \
    --model GPT4o_20241120 \
    --judge-args '{"valid_type": "STR"}'



#*********deepseek v3 from siliconflow as judger*********
## export SiliconFlow_API_KEY=

# valid_type: STR or LLM
python -u run.py --data PhyX_mini_OE \
    --model GPT4o_20241120 \
    --judge deepseek-v3-si --judge-args '{"valid_type": "LLM"}'


#*********official deepseek v3 as judger*********

# export Deepseek_API=

python -u run.py --data PhyX_mini_OE \
    --model GPT4o_20241120 \
    --judge deepseek-v3 --judge-args '{"valid_type": "LLM"}'

```

This example shows how to evaluate `GPT4o_20241120` using DeepSeek-V3 as the judge.

Details for these parameters:

- `--data`: The dataset configuration to evaluate, e.g., `PhyX_mini_MC` for multiple-choice or  `PhyX_mini_OE` for open-ended.
- `--model`: The model to be evaluated. Please refer to [this link](https://aicarrier.feishu.cn/wiki/Qp7wwSzQ9iK1Y6kNUJVcr6zTnPe?table=tblsdEpLieDoCxtb) for supported models.
- `--valid_type`: Judgment method — `LLM` for LLM-based evaluation or `STR` for rule-based matching.
- `--judge`: judger,  `deepseek-v3-si` for deepseek-v3 provided by SiliconFlow (set SiliconFlow_API_KEY) and `deepseek-v3` for official (set Deepseek_API).

➡️ LLM

In this repository, we support more LLMs for evaluation.
If you want to evaluate on LLM (i.e., in text only setting), please refer to examples in `examples/LLM_textonly/`, where we add an extra environment variable `PHYX_TEXT_ONLY=true`.

➡️ Custom Models

To support your custion model, we would suggest to deploy your model as API and then add setting in the `vlmeval/config.py`.
It is also avaibale if your model is a tuned version of one supported model.

To add support for your models, you can refer to our commits:

- VLM + API (via OpenAI): https://github.com/NastyMarcus/PhyX/commit/4a47eaf397abfe78f9ecaf0ecc2d7c91f08aa67e
- LLM + Local Model: https://github.com/NastyMarcus/PhyX/commit/c5b7ae15414660a5085548694d0d363563cd9561
- LLM + API: https://github.com/NastyMarcus/PhyX/commit/d66987f372d467dd4b7caed820eb58ec3e9a7c1e

Please refer to [official guidance](https://github.com/open-compass/VLMEvalKit/blob/main/docs/zh-CN/Development.md#%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AA%E6%96%B0%E7%9A%84%E6%A8%A1%E5%9E%8B) for more details.

After evaluation, results will be saved in the `outputs` folder.