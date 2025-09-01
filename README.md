
# 📊 Metadata for 40 million GitHub repositories

[![License](https://img.shields.io/badge/license-MIT-green.svg)](#license)
[![Dataset](https://img.shields.io/badge/format-parquet-blue)]()
[![HF Hub](https://img.shields.io/badge/HuggingFace-available-yellow.svg)]()

A cleaned, analysis-ready dataset with per-repository statistics aggregated from **GH Archive** events: stars, forks, pull requests, open issues, visibility, language signals, and more. Column names mirror the GH Archive / GitHub API semantics where possible.

> Source: [GH Archive](https://www.gharchive.org/) (public GitHub event stream).


## 🚀 Quickstart

```python
from datasets import load_dataset

ds = load_dataset("ibragim-bad/github-repos-metadata-40M", split="full")  # or 'sample' for random sample of 1 million repositories.

```


## 📈 EDA Notebook

- `data_visualisation.ipynb`: Simple exploratory data analysis of the dataset (column overview, basic distributions, missing-value checks, sample visualizations).

## 💡 Use cases

* Trend analysis of stars/forks/PRs
* Language mix & repo growth signals
* Ranking by activity / stability
* Input features for ML models (quality or popularity prediction)

## 📦 What’s inside

Each row aggregates repository-level stats derived from GH Archive events and GitHub metadata snapshots.

**Schema (columns & types)**

| Column              | Type           | Description                                                                 |
| ------------------- | -------------- | --------------------------------------------------------------------------- |
| `repo_name`         | `string`      | `owner/name`                                                                |
| `language`          | `string`      | Primary language (human-readable)                                           |
| `created_at`        | `timestamp`   | Repo creation time (UTC) if available                                       |
| `description`       | `string`      | Repo description                                                            |
| `description_language`         | `string`   | Detected natural-language code of the repository description (ISO 639-1/2) |
| `description_language_score`   | `float32`  | Confidence score for description_language (0–1)                             |
| `license_key`       | `string`      | SPDX-like license key                                                       |
| `forks_count`       | `int64`       | Current forks count                                                         |
| `watchers_count`    | `int64`       | Watchers/stars count (note: GitHub’s legacy “watchers” vs “stargazers”)    |
| `size`              | `int64`       | Repo size (KB, as reported by GitHub)                                      |
| `last_pr_id`        | `int64`       | Latest available PR id                                                      |

Values may be `null` when unavailable from the source.

 
## 🏗️ How it’s built (ETL)

- **Data source:** GH Archive public event stream; window through 2025-07-23 (UTC).
- **Event filter:**
    - CreateEvent (ref_type=repository) to capture initial repo creation and timestamps.
    - PullRequestEvent to obtain rich repo snapshots embedded in PR payloads.

- **Extraction:**
    - Parse event.repo and payload.*.repo snapshots for: repo_name (owner/name), description, language, license_key, size, visibility, created_at, open_issues_count, forks_count, watchers_count.
    - Track latest observed PR identifier as last_pr_id.
- **Aggregation:**
    - Group by repo_name; keep the most recent snapshot within the window.


**Notes**
- Coverage primarily comes from PullRequestEvent snapshots; repositories without any PRs are generally missing.
- Stars/watchers and forks are taken from event-time snapshots and may be slightly stale versus live GitHub.
- Values can be null if not present in the underlying event payloads.


## ⚠️ Limitations

* GH Archive reflects **public** events only; private repos are out of scope.
* Some GitHub fields (e.g., watchers vs stargazers) have historical quirks.
* Missing values where source is unavailable.



## 📚 Citation

If you use this dataset, please cite:

```bibtex
@misc{github_repos_activity_stats_2025,
  title        = {GitHub Repos Activity Stats (from GH Archive)},
  author       = {Ibragim Badertdinov},
  year         = {2025},
  howpublished = {\url{https://huggingface.co/datasets/ibraigm-bad/github-repos-metadata-40M}}
}
```


## 📜 License

Distributed under the MIT License. See LICENSE for more information.
