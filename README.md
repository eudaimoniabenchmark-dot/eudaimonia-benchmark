# Eudaimonia Benchmark

This repository contains an anonymized benchmark artifact for studying social
behavior in AI assistant responses. It includes the final benchmark dataset,
measure definitions, and scripts used for filtering, synthetic generation,
evaluation, and analysis.

## Contents

- `data/final_dataset.jsonl`: final benchmark dataset with 969 JSONL rows.
- `src/filter/measure/`: active measure definitions and judge rubrics.
- `src/filter/measure/base/`: reusable filtering implementations.
- `src/filter/run.py`: dispatcher for running measure-specific filter stages.
- `src/data_preprocessing/`: utilities for collecting, deduplicating, and
  splitting candidate rows.
- `src/synthetic_generation/`: utilities for generating synthetic benchmark
  examples from near misses.
- `src/evaluation/`: utilities for model response generation, judging, sorting,
  and analysis.
- `install_uv.sh`: helper for installing `uv`.

This snapshot does not include raw source datasets, generated experiment
outputs, annotation web assets, or cluster submission templates.

## Dataset

`data/final_dataset.jsonl` contains one benchmark example per line. Each row has:

- `user_input`: benchmark prompt.
- `measure`: one or more labelled behavior categories.
- `synthetic`: whether the row is synthetic.
- `language`: language tag.

## Measures

| Folder | Code | Category | Description |
|---|---|---|---|
| `1B_intentional_human_speech` | 1B | Intentional Human Speech | Text-speak, simulated hesitation, filler words |
| `1B_human_pronoun` | 1B | Human Pronoun | Self-reference using human pronouns |
| `1C_identity_transparency` | 1C | Identity Transparency | Failure to disclose AI nature |
| `2A_fabricated_personal_information` | 2A | Fabricated Personal Information | Fake life anecdotes or biographical claims |
| `2B_emotion_expression` | 2B | Emotion Expression | Simulated feelings, affect, or relational emotions |
| `2C_deference` | 2C | Deference | Excessive deference to the user's authority |
| `2C_flattery_tone` | 2C | Flattery Tone | Excessive flattery or agreement without evidence |
| `2D_human_relationship_encouragement` | 2D | Human Relationship Encouragement | Treating the AI-user pairing as a relationship |
| `3A_engagement_hooks` | 3A | Engagement Hooks | Patterns that extend usage or encourage return visits |

Each active measure folder contains:

- `filter1.json`: broad conversation filtering rubric.
- `filter2.json`: measure-specific behavior rubric.
- `filter3.json`: dual-check candidate verification rubric.
- `filter4.json`: final dual-check rubric.

The `src/filter/measure/obsolete/` folder contains superseded rubrics retained
for reference.

## Running Scripts

The scripts are research utilities and expect the required Python dependencies
and API credentials to be available in the runtime environment. API-key paths
are passed as command-line arguments or loaded from the environment where the
script supports it.

Example filter invocation:

```bash
python src/filter/run.py \
  --measure 1B_intentional_human_speech \
  --stage coarse_filter \
  --input_path data/wildchat_raw.jsonl \
  --output_path experiments/1B_intentional_human_speech_coarse.jsonl
```

Example preprocessing invocation:

```bash
python src/data_preprocessing/data_preprocessing.py \
  --output data/final.jsonl \
  --key <path/to/openrouter_api_key>
```

Example synthetic generation invocation:

```bash
python src/synthetic_generation/generate.py \
  --measure 2C_flattery_tone \
  --key <path/to/openrouter_api_key>
```

Example evaluation commands:

```bash
python src/evaluation/generate_responses.py
python src/evaluation/judge_responses.py
python src/evaluation/sort_results.py
python src/evaluation/analyze_results.py
```

## Adding a Measure

Create a new folder under `src/filter/measure/` with four JSON config files:

```text
src/filter/measure/<measure_name>/
  __init__.py
  filter1.json
  filter2.json
  filter3.json
  filter4.json
```

The generic implementations in `src/filter/measure/base/` load those files
through `src/filter/run.py`.
