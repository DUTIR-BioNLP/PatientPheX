[ [中文](./README.md)  | **English** ]

# Patient-Specific Phenotype Concept Recognition from Full-Text Biomedical Literature

<p align="center">
        ⭐ <a href="https://tianchi.aliyun.com/competition/entrance/532512">Tianchi Evaluation Platform</a>&nbsp;&nbsp; | &nbsp;&nbsp;📅 <a href="http://cips-chip.org.cn/2026/">CHIP 2026 Evaluation Website</a>&nbsp;&nbsp; | &nbsp;&nbsp;🗂️ <a href="https://tianchi.aliyun.com/competition/entrance/532512/information">Evaluation Data</a>&nbsp;&nbsp; | &nbsp;&nbsp;📃 <a href="https://doi.org/10.64898/2026.10.07.757319">Dataset Paper (preprint)</a> | &nbsp&nbsp📃 <a href="https://arxiv.org/abs/2610.29606">Overview paper(preprint)</a>
<br>
<br>
</p>

- [Patient-Specific Phenotype Concept Recognition from Full-Text Biomedical Literature](#patient-specific-phenotype-concept-recognition-from-full-text-biomedical-literature)
  - [Background](#background)
  - [Task Description](#task-description)
  - [Dataset](#dataset)
    - [1. Dataset Statistics](#1-dataset-statistics)
    - [2. Data Example](#2-data-example)
    - [3. Field Descriptions](#3-field-descriptions)
  - [Submission](#submission)
    - [1. Submission Procedure](#1-submission-procedure)
    - [2. Submission Format](#2-submission-format)
  - [Evaluation Metrics](#evaluation-metrics)
    - [Subtask 1: Full-Text Phenotype Concept Recognition](#subtask-1-full-text-phenotype-concept-recognition)
      - [(1) Mention-Level Evaluation](#1-mention-level-evaluation)
      - [(2) Document-Level Evaluation](#2-document-level-evaluation)
      - [Handling Negated, Composite, and ID-less Entities](#handling-negated-composite-and-id-less-entities)
    - [Subtask 2: Patient-Specific Phenotype Concept Recognition](#subtask-2-patient-specific-phenotype-concept-recognition)
      - [(1) Micro-F1](#1-micro-f1)
      - [(2) Macro-F1](#2-macro-f1)
    - [Overall Score](#overall-score)
    - [Baseline System](#baseline-system)
  - [Awards](#awards)
  - [Participation Rules](#participation-rules)
  - [Materials Required from Award-Winning Teams](#materials-required-from-award-winning-teams)
  - [Schedule](#schedule)
  - [Organizers](#organizers)
  - [References](#references)

## Background

A human phenotype refers to a clinical characteristic that results from the interaction between genotype and environment and can be observed or measured in a patient, such as height, blood pressure, combinations of symptoms, or drug responses. Phenotypes are not only descriptions of the observable manifestations of disease, but also an important basis for rare disease diagnosis, disease mechanism research, gene discovery, and precision medicine. With the rapid growth of the biomedical literature, automatically extracting, normalizing, and organizing patient phenotype knowledge from large volumes of unstructured text has become an important research direction in biomedical natural language processing.

Phenotype Concept Recognition aims to automatically identify phenotype entities in biomedical text and normalize them to standard concepts in an ontology. This task remains challenging for several reasons. First, full-text biomedical articles are long and contain highly diverse phenotype descriptions, including abbreviations, elliptical expressions, negation, and composite entities. Second, the Human Phenotype Ontology (HPO) [1] contains a large number of concepts organized in a complex hierarchy, making accurate phenotype normalization difficult using string matching alone. In addition, most existing studies [2-4] focus on recognizing all phenotype concepts from short input texts, while relatively little work has investigated the extraction of phenotype concepts for specific patients from full-text articles. Biomedical publications often describe multiple patients or family members, each of whom may present different clinical manifestations. Extracting phenotype entities alone is therefore insufficient for applications such as patient profiling, family-based genetic analysis, and personalized medicine.

Accordingly, this shared task focuses on **Patient-Specific Phenotype Concept Recognition from Full-Text Biomedical Articles**. The goal is to automatically identify phenotype concepts associated with a given patient and establish explicit patient-phenotype correspondences, thereby supporting downstream applications such as patient-level knowledge base construction, genotype-phenotype association studies, and precision medicine.

## Task Description

This shared task is based on publicly available full-text biomedical articles from PubMed Central (PMC) and is designed to evaluate and compare participating systems for patient-specific phenotype information extraction. Given the full text of a biomedical article, participating systems are expected to identify phenotype concepts associated with specific patients. The task consists of two subtasks:

- **Subtask 1 – Full-Text Phenotype Concept Recognition:** Identify all phenotype concepts in the full text regardless of patient identity, including their exact locations in the article, and normalize them to concepts in the Human Phenotype Ontology (HPO). All systems must use HPO version **v2026-06-23**, restricted to the **Phenotypic abnormality (HP:0000118)** branch.
- **Subtask 2 – Patient-Specific Phenotype Concept Recognition:** Given a specific patient, identify the phenotype concepts associated with that patient and return the corresponding set of HPO identifiers.

Participating teams may use traditional machine learning methods, deep learning models, pretrained language models, or large language models, provided that the model contains no more than **10B parameters**. Techniques such as prompt learning, supervised fine-tuning, and retrieval-augmented generation (RAG) are permitted. However, all models and external knowledge resources used must be disclosed in the technical report. **Externally human-annotated phenotype datasets are not allowed.**

## Dataset

We constructed **PatientPheX**, a dataset specifically designed to evaluate patient-specific phenotype information extraction. The dataset is derived from publicly available full-text biomedical articles in PMC and currently contains **200 manually annotated full-text articles**. Each article includes annotations for patient information, phenotype entities, and patient-phenotype associations, providing a unified benchmark for patient-specific phenotype concept recognition from full-text biomedical literature.

### 1. Dataset Statistics

PatientPheX contains 200 full-text PMC biomedical articles, including **80 training articles**, **20 development articles (Leaderboard A test set)**, and **100 test articles (Leaderboard B test set)**. In total, the dataset contains annotations for **506 patients**, **15,193 phenotype entities**, and **2,874 unique HPO concepts**, including complex phenomena such as negated and composite phenotypes. Gold-standard annotations are not provided for the development and test sets; predictions must be submitted online for evaluation.

| **Statistic** | **Training Set** | **Development Set** | **Test Set** | **Total** |
|---|---:|---:|---:|---:|
| Number of articles | 80 | 20 | 100 | 200 |
| Number of patients | 209 | 53 | 244 | 506 |
| Number of phenotype entities | 6,027 | 1,563 | 7,603 | 15,193 |
| Number of unique HPO IDs | 1,662 | 551 | 1,863 | 2,874 |
| Number of negated phenotypes | 171 | 38 | 226 | 435 |
| Number of composite phenotypes | 69 | 5 | 138 | 212 |
| Number of patient-phenotype associations | 2,307 | 563 | 2,786 | 5,656 |
| Number of unique patient-associated HPO IDs | 1,091 | 351 | 1,252 | 1,995 |

### 2. Data Example

```json
{
  "pmc_id": "12652105",
  "pmid": "41300704",
  "patient": [
    {
      "patient_id": "OII.1",
      "mention": [
        {
          "text": "an 18-year-old female",
          "offset": 10434,
          "length": 21
        },
        {
          "text": "patient one (II.1)",
          "offset": 12645,
          "length": 18
        }
      ]
    },
    {
      "patient_id": "OII.2",
      "mention": [
        {
          "text": "Patient II.2",
          "offset": 13059,
          "length": 12
        }
      ]
    },
    ...
  ],
  "full_text": [
    {
      "section_type": "TITLE",
      "type": "front",
      "offset": 0,
      "text": "Rare Duplication in the RYR1 Gene Causing Malignant Hyperthermia and Clinical Variability"
    },
    {
      "section_type": "ABSTRACT",
      "type": "abstract",
      "offset": 90,
      "text": "..."
    },
    ...
  ],
  "entities": [
    {
      "identifier": "HP:0002047",
      "type": "Phenotype",
      "offset": 42,
      "length": 22,
      "text": "Malignant Hyperthermia",
      "note": null
    },
    ...
  ],
  "association": [
    {
      "patient_id": "OII.1",
      "phenotype": ["HP:0003938", "HP:0001945", "HP:0002063", ...]
    },
    {
      "patient_id": "OII.2",
      "phenotype": ["HP:0001762", "HP:0000508", ...]
    },
    ...
  ]
}
```

### 3. Field Descriptions

The dataset is provided in **JSONL** format, with one article per line. Each record contains document metadata, information about the specified patients, full-text content, all phenotype entities in the article, and phenotype information associated with individual patients. The main fields are described below.

| **Field** | **Subfield** | **Example** | **Description** |
|---|---|---|---|
| `pmc_id` |  | `12652105` | PMC full-text article identifier |
| `pmid` |  | `41300704` | PubMed identifier |
| `patient` |  |  | Specified patients in the article |
|  | `patient_id` | `OII.1` | Unique patient identifier. If the article provides an explicit patient number, the identifier is `O` followed by that number; otherwise, identifiers begin with `P` and are assigned in order of appearance. |
|  | `mention` | `patient one (II.1)` | Explicit textual mentions of the patient, including positional information. These represent a subset of valid patient references in the article rather than an exhaustive list. |
| `full_text` |  |  | Full text of the article |
|  | `section_type` | `TITLE` | Section category |
|  | `type` | `front` | Fine-grained BioC passage type |
|  | `offset` | `0` | Global starting character offset of the section in the original full text |
|  | `text` |  | Original section text |
| `entities` |  |  | All phenotype information in the article body |
|  | `identifier` | `HP:0002047` | Normalized HPO ID. Multiple IDs for a composite phenotype are separated by semicolons; `-1` indicates that no valid HPO ID is available. |
|  | `type` | `Phenotype` | Entity type; currently always `Phenotype` |
|  | `offset` | `42` | Global character offset of the entity in the original full text |
|  | `length` | `22` | Character length of the entity text |
|  | `text` | `Malignant Hyperthermia` | Exact entity text appearing in the source article |
|  | `note` | `null` / `NO` | Additional annotation. The default is `null`; `NO` indicates a negated phenotype. |
| `association` |  |  | All patient-phenotype associations in the article |
|  | `patient_id` | `OII.1` | Unique patient identifier |
|  | `association.phenotype` |  | List of phenotype strings associated with the patient. Valid phenotypes are represented by HPO IDs; for phenotypes whose ID is `-1`, the original entity text is retained. |

## Submission

### 1. Submission Procedure

The competition consists of two stages: **Leaderboard A** and **Leaderboard B**.

- **Leaderboard A (Development Stage):** After successful registration, teams may download the training data and the Leaderboard A test set (development set) from the Tianchi platform, develop and train their models locally, and submit predictions online. Leaderboard A scores are intended solely for model development and tuning and **will not count toward the final competition ranking**.
- **Leaderboard B (Final Evaluation Stage):** The Leaderboard B test set will be released for download during the final evaluation stage. Teams must generate predictions for this dataset and submit them to the Tianchi platform. **Each team's best historical score on Leaderboard B will be used for the final ranking and award determination.**

**Submission Notes**

- Each team may submit up to **5 times per day** during Leaderboard A and up to **3 times per day** during Leaderboard B.
- The leaderboard is updated hourly and ranked from highest to lowest according to the evaluation score.
- Failed evaluations do not count against the daily submission limit.

### 2. Submission Format

For both Leaderboard A and Leaderboard B, teams must submit prediction results for the corresponding test set as a **UTF-8 encoded JSONL file**. The format must be consistent with the result format used in the training data. A sample submission is shown below:

```json
{
  "pmc_id": "...",
  "pmid": "...",
  "entities": [
    {
      "identifier": "HP:0000519",
      "type": "Phenotype",
      "offset": 206,
      "length": 30,
      "text": "bilateral congenital cataracts",
      "note": null
    },
    ...
  ],
  "association": [
    {
      "patient_id": "P1",
      "phenotype": ["HP:0011410", ...]
    },
    ...
  ]
}
```

Field descriptions:

- `entities`: Predictions for all phenotype entities in the full text.
- `association`: Predictions for phenotype concepts associated with specific patients.

## Evaluation Metrics

### Subtask 1: Full-Text Phenotype Concept Recognition

For full-text phenotype concept recognition and normalization, system performance is evaluated at two levels: **mention level** and **document level**.

#### (1) Mention-Level Evaluation

Mention-level evaluation measures a model's ability to identify and normalize specific phenotype mentions in the text.

A predicted phenotype entity is counted as a true positive (TP) only when both its predicted entity span exactly matches the gold-standard annotation and its corresponding HPO concept identifier is correct. A predicted entity that does not exist in the gold-standard annotation set is counted as a false positive (FP), while a gold-standard entity that is not correctly identified by the model is counted as a false negative (FN).

Mention-level Precision, Recall, and F1 are defined as follows:

$$P_{men} = \frac{TP}{TP+FP}$$

$$R_{men} = \frac{TP}{TP+FN}$$

$$F1_{men} = \frac{2 \times P_{men} \times R_{men}}{P_{men}+R_{men}}$$

#### (2) Document-Level Evaluation

Document-level evaluation measures whether a model can identify the set of phenotype concepts appearing in each article.

For each document, all HPO IDs associated with gold-standard phenotype entities are deduplicated to form the gold phenotype set:

$$G_d = \{h_1,h_2,\dots,h_n\}$$

Likewise, all HPO IDs in the model predictions are deduplicated to form the predicted phenotype set:

$$P_d = \{h_1,h_2,\dots,h_m\}$$

At the document level, evaluation considers only whether an HPO concept is identified in the document; the exact mention location, span boundaries, and number of occurrences are ignored. An HPO ID is considered correctly predicted if it appears in both the gold set $G_d$ and the predicted set $P_d$.

Document-level TP, FP, and FN are defined as follows, where $N$ is the number of test documents:

$$TP_{doc} = \sum_{d=1}^{N}|G_d \cap P_d|$$

$$FP_{doc} = \sum_{d=1}^{N}|P_d-G_d|$$

$$FN_{doc} = \sum_{d=1}^{N}|G_d-P_d|$$

Document-level Precision, Recall, and F1 are then calculated as:

$$P_{doc} = \frac{TP_{doc}}{TP_{doc}+FP_{doc}}$$

$$R_{doc} = \frac{TP_{doc}}{TP_{doc}+FN_{doc}}$$

$$F1_{doc} = \frac{2 \times P_{doc} \times R_{doc}}{P_{doc}+R_{doc}}$$

#### Handling Negated, Composite, and ID-less Entities

- **Negated phenotypes (`note="NO"`):** These indicate clinical manifestations that are explicitly negated in the text and therefore do not represent actual patient phenotypes. They are not treated as positive instances during evaluation. Gold-standard entities marked with `note="NO"` do not contribute to FN. For predictions, an entity correctly marked as `note="NO"` is excluded from evaluation; if a negated phenotype is predicted as a regular phenotype (i.e., without `note="NO"`), it is counted as an FP. If the model does not predict the negated phenotype, no score is assigned for that entity.
- **Composite phenotypes:** When one phenotype mention maps to multiple HPO IDs, the IDs are separated by semicolons, e.g., `HP:xxxxxx;HP:xxxxxxx`. During evaluation, the IDs are split on semicolons and treated as independent evaluation units.
- **Phenotypes without a valid HPO ID (`identifier = -1`):** A prediction is considered correct if the entity span is predicted correctly.

### Subtask 2: Patient-Specific Phenotype Concept Recognition

For patient-specific phenotype relation extraction, the evaluation target is the association between a patient and a phenotype. A patient-phenotype mapping is counted as a true positive (TP) only when the model correctly associates a phenotype with the specified patient. Assigning a phenotype to the wrong patient is counted as a false positive (FP), while failing to identify a gold-standard phenotype associated with the patient is counted as a false negative (FN).

#### (1) Micro-F1

Micro-F1 evaluates overall extraction performance across all patient-phenotype mappings globally. The entire test set is treated as a single collection, and TP, FP, and FN are accumulated across all documents:

$$P_{micro} = \frac{\sum TP}{\sum TP+\sum FP}$$

$$R_{micro} = \frac{\sum TP}{\sum TP+\sum FN}$$

$$F1_{micro} = \frac{2 \times P_{micro} \times R_{micro}}{P_{micro}+R_{micro}}$$

#### (2) Macro-F1

Macro-F1 calculates Precision, Recall, and F1 separately for each patient and then averages the resulting scores equally across all patients in the test set. This metric is intended to evaluate the model's ability to identify individualized phenotype profiles for patients.

For patient $i$, let the gold phenotype set be $y_i$ and the predicted phenotype set be $\hat{y}_i$. Precision, Recall, and F1 are defined as:

$$P_i = \frac{|y_i \cap \hat{y}_i|}{|\hat{y}_i|}$$

$$R_i = \frac{|y_i \cap \hat{y}_i|}{|y_i|}$$

$$F1_i = \frac{2 \times P_i \times R_i}{P_i+R_i}$$

Macro-Precision, Macro-Recall, and Macro-F1 over the test set are defined as:

$$P_{macro} = \frac{1}{K}\sum_{i=1}^{K}P_i$$

$$R_{macro} = \frac{1}{K}\sum_{i=1}^{K}R_i$$

$$F1_{macro} = \frac{1}{K}\sum_{i=1}^{K}F1_i$$

where $K$ denotes the total number of gold-standard patients in the test set. If both the gold phenotype set and the predicted phenotype set for a patient are empty, that patient's Precision, Recall, and F1 are all defined as **1**.

### Overall Score

The final evaluation score combines performance on both subtasks:

$$Score = 0.25 \times (F1_{men} + F1_{doc}) + 0.25 \times (F1_{micro} + F1_{macro})$$

### Baseline System

The baseline system first applies **PhenoTagger**, configured with the specified HPO version, to perform Subtask 1. It then uses the **Qwen3-8B** large language model with prompt engineering to identify relevant mentions of the specified patient in the text. Contextual information is subsequently used to associate each patient with the phenotype entities identified in Subtask 1, producing predictions for Subtask 2. The baseline system does **not** use the training set for optimization.

## Awards

This is an academic shared task. Award-winning teams will receive certificates, present their work at the conference, and be invited to publish an English evaluation paper indexed by EI.

## Participation Rules

1. Each participant may register only one account. If a participant is found to have registered multiple accounts for submissions, all associated accounts will be disqualified.
2. Complete the registration form before the end of Leaderboard A: [click here](https://docs.qq.com/form/page/DRE5IVEJJbnRvZkhK).
3. External resources, including but not limited to code, tools, and knowledge bases, are permitted. However, externally human-annotated phenotype datasets are not allowed. All resources used must be publicly accessible and properly cited.
4. Award-winning teams must register for and attend the CHIP conference, give an oral presentation, and submit a technical paper. Otherwise, their competition result will be revoked and the ranking will pass to the next eligible team. *Note: conference registration fees, accommodation, meals, and travel expenses must be covered by the participating team.*
5. Students and industry participants from China and abroad are eligible to participate. Individuals must register through an affiliated institution. Members of the organizing committee are not eligible to participate. **Real-name verification:** all team members, including the team leader, must complete real-name verification before the registration deadline (Tianchi website → upper-right **Personal Center** → **Verification**). Teams that fail to complete verification before the deadline will be disqualified.
6. Each team may consist of **1-5 members**.
7. The task data may be used only for this evaluation. For any other use, please contact the task organizers.

## Materials Required from Award-Winning Teams

Award-winning teams must submit the following materials:

1. Relevant source code and documentation.
2. An evaluation paper. Previous CHIP shared-task papers may be used as references: [CHIP 2024 Accepted Papers](http://www.cips-chip.org.cn/2024/accepted).
3. If additional publicly available resources are used, they must be described and the corresponding resource files or URLs must be provided. Such resources include, but are not limited to, code, tools, and knowledge bases. All resources used must be publicly accessible.

The above materials must be emailed to **dutir_bionlp@163.com** before the evaluation paper submission deadline. The email subject should be:

`CHIP2026Task-Evaluation Materials-Team Name`

Code and documentation should be packaged into a single archive file (e.g., ZIP or RAR) and attached to the email.

**Note:** Failure to submit the required materials will be considered a voluntary withdrawal from award consideration. Award eligibility will automatically pass to the next highest-ranked eligible team.

## Schedule

| **Item** | **Date** |
|---|---|
| Registration | August 20 – September 27 |
| Leaderboard A evaluation begins | August 20 |
| Leaderboard A evaluation ends | September 27 |
| Leaderboard B evaluation begins | September 28 |
| Leaderboard B evaluation ends | October 1 |
| Evaluation paper submission deadline | October 15 |
| Conference | October 30 – November 1 |

## Organizers

**Organizing Institution**  
Dalian University of Technology — Ling Luo, Jian Wang, Yuanyuan Sun, Hongfei Lin

**Contact**  
Xuanlv Zhu (xlzhu@mail.dlut.edu.cn)

**Related Websites**  
CHIP official website: http://cips-chip.org.cn/  
Tianchi platform: https://tianchi.aliyun.com/competition/entrance/532512

**DingTalk Group**  
Please scan the QR code below to join the official DingTalk group. Important competition announcements will be posted there as soon as they are available.

<img src="https://static.tianchi.aliyun.com/tianchi/race/images/prod/532512/1242990779776242/otzc88vq6rj6nqcs.png" width="300">

## References

[1] Gargano MA, Matentzoglu N, Coleman B, et al. The Human Phenotype Ontology in 2024: phenotypes around the world. *Nucleic Acids Research*. 2024;52(D1):D1333-46.  
[2] Luo L, Yan S, Lai PT, et al. PhenoTagger: a hybrid method for phenotype concept recognition using human phenotype ontology. *Bioinformatics*. 2021;37(13):1884-90.  
[3] Feng Y, Qi L, Tian W. PhenoBERT: a combined deep learning method for automated recognition of human phenotype ontology. *IEEE/ACM Transactions on Computational Biology and Bioinformatics*. 2022;20(2):1269-77.  
[4] Tao Y, Huang Y, Wang Y, et al. AutoPCR: automated phenotype concept recognition by prompting. *Bioinformatics*. 2026;42(Supplement_1):btag304.

## Citation

If you use the repository of this project, please cite them.

```
@article{PatientPheX,
  title="{PatientPheX: A Full-Text Biomedical Corpus for Patient-Specific Phenotype Information Extraction}",
  author={Xuanlyu Zhu, Ling Luo, Zhijun Wang, Chentao Huang, Yunxiang Li, Yang Zhong, Tengxiao Lv, Juntao Li, Chao Liu, Feng Tian, Yuanyuan Sun, Jian Wang, Hongfei Linn},
  journal={bioRxiv},
  year={2026},
  doi = {10.64898/2026.10.07.757319},
  url = {https://doi.org/10.64898/2026.10.07.757319},
}
@article{OverviewCHIP,
  title="{Overview of CHIP 2026 Shared Task 1: Patient-Specific Phenotype Concept Recognition in Full-Text Biomedical Literature}",
  author={Xuanlyu Zhu, Feng Tian, Yang Zhong, Pengqi Zheng, Xiwen Bi, Ling Luo, Yuanyuan Sun, Jian Wang, Hongfei Lin},
  journal={arxiv},
  year={2026},
  doi = {10.48550/arXiv.2610.29606},
  url = {https://doi.org/10.48550/arXiv.2610.29606},
}
```
