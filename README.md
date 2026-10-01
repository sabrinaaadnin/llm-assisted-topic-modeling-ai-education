# LLM-Assisted Topic Modeling for AI in Education

## Project Overview

This project explores the use of a Large Language Model (LLM) to identify and interpret discussion topics in Indonesian social media data related to Artificial Intelligence (AI) in Education.

The workflow combines a conventional topic-modeling baseline with LLM-based topic discovery. An upstream LDA analysis was used to determine the target number of topics. Based on that analysis, the LLM workflow was configured to generate **3 topics**.

Because LLM outputs are generative and may vary between runs, topic discovery was repeated **10 times**. Each run generated 3 topics, producing **30 topic instances**. Jaccard similarity was then used to identify closely related topics across runs. Related topic instances were consolidated through keyword aggregation and frequency analysis to obtain a final set of 3 topics.

This project was conducted as part of a faculty research project on AI in Education.

---

## Objectives

- Identify major discussion topics related to AI in Education.
- Explore LLM-assisted topic discovery using the GPT API.
- Generate representative keywords and topic labels.
- Assess the consistency of LLM-generated topics across repeated runs.
- Consolidate related topics into a final topic representation.
- Assign individual comments to the final topic catalog for downstream analysis.

---

## Methodology

```text
Indonesian Social Media Data
            |
            v
      Text Preprocessing
            |
            v
       LDA Baseline
            |
            v
   Determine Topic Count (K=3)
            |
            v
     GPT API + Prompting
            |
            v
      Generate 3 Topics
            |
            v
       Repeat 10 Runs
            |
            v
      30 Topic Instances
            |
            v
    Jaccard Similarity
            |
            v
   Best Topic Matching
            |
            v
    Keyword Aggregation
            |
            v
   Keyword Frequency Analysis
            |
            v
      Final 3 Topics
            |
            v
     Topic Assignment
```

### 1. Topic Count Selection with LDA

An LDA-based analysis was used as the baseline to determine the target number of topics. The selected number of topics was **3**.

The LLM stage therefore did not independently choose the number of topics. Instead, it was instructed to generate the same number of topics determined by the preceding analysis.

### 2. Text Preprocessing

The cleaned social media comments were processed before being passed to the LLM. The notebook applies:

- Lowercasing
- Tokenization with NLTK
- Indonesian stopword removal
- Retention of alphabetic tokens

The resulting text is used as the input for topic discovery and later topic evaluation.

### 3. LLM-Based Topic Discovery

The GPT API is prompted to analyze the prepared comments and generate global topics.

For each topic, the prompt requests:

- Topic ID
- Topic label
- Topic description
- 10 representative keywords

The prompt also instructs the model to select keywords from the provided text rather than inventing synonyms or unrelated terms.

### 4. Repeated Topic Generation

LLM output can vary across generations. To reduce reliance on a single generation, topic discovery was repeated **10 times**.

Each run generated 3 topics:

**10 runs × 3 topics = 30 topic instances**

The outputs from all runs were consolidated into `iterasi_topic.xlsx`.

### 5. Topic Similarity with Jaccard

Jaccard similarity was calculated between topic labels across the generated topic instances.

For two sets of words A and B:

```text
J(A,B) = |A ∩ B| / |A ∪ B|
```

The resulting similarity matrix is stored in `jaccard.xlsx`.

For each topic, the highest-similarity topic in each other run was identified as its best match. The matching results are stored in `best_match_per_run.xlsx`.

### 6. Keyword Consolidation

Keywords from related topic instances were combined. Keyword frequencies were then calculated to identify the most frequently recurring keywords within each consolidated topic group.

The intermediate result is stored in `merged_keywords_with_frequencies.xlsx`.

### 7. Final Topic Representation

After matching and consolidating related topic instances, the analysis produced **3 final topic groups**. The final output contains the consolidated keywords and dominant topic labels.

The cleaned final result is stored in:

`results/final_top20_keywords_clean.xlsx`

### 8. Topic Assignment

The final topic catalog was subsequently used to classify individual comments. GPT was prompted to assign each comment to exactly one final topic and provide a confidence score.

The raw assignment output is intentionally not included in this public repository because it contains the underlying social media comments.

---

## Technologies

- Python
- OpenAI API
- GPT-5
- Pandas
- NumPy
- NLTK
- Gensim
- Jaccard Similarity
- Excel / XLSX

---

## Repository Structure

```text
llm-assisted-topic-modeling-ai-education/
|
|-- README.md
|-- requirements.txt
|-- .gitignore
|
|-- notebooks/
|   `-- LLM_Topic_Modeling_AI_Education.ipynb
|
|-- data/
|   `-- README.md
|
`-- results/
    |-- iterasi_topic.xlsx
    |-- jaccard.xlsx
    |-- best_match_per_run.xlsx
    |-- merged_keywords_with_frequencies.xlsx
    `-- final_top20_keywords_clean.xlsx
```

---

## Key Technical Challenge: LLM Output Variability

A key challenge in this project is the variability of generative LLM outputs. Even when the same dataset and general prompting strategy are used, the generated topic labels can differ between runs.

The project addresses this by:

1. Repeating topic discovery 10 times.
2. Comparing generated topics across runs.
3. Measuring lexical similarity using Jaccard similarity.
4. Identifying the closest topic matches across runs.
5. Aggregating keywords from related topic instances.
6. Constructing a final set of stable topic representations.

This makes the analysis less dependent on a single LLM generation.

---

## Evaluation

The final topic representation was evaluated using topic-quality measures implemented in the notebook, including:

- Coherence (C_v)
- Topic Uniqueness
- Document Coverage
- Factuality

These measures were used to assess the quality of the final topic keywords against the underlying text corpus.

---

## Results

### Final Topics

After running the LLM-based topic discovery process for 10 iterations and comparing the generated topics using Jaccard similarity, the recurring topic groups were consolidated into three final topics.

| Topic | Label | Representative Keywords |
|---|---|---|
| Topic 1 | Personalisasi dan efisiensi pembelajaran | belajar, efisiensi, kemajuan, memantau, menganalisis, pengalaman, personal, siswa, menghemat, global |
| Topic 2 | Debat AI vs peran guru | guru, menggantikan, peran, empati, emosi, simpati, AI, pendidik, sosial, karakter |
| Topic 3 | Otomatisasi tugas administratif | belajar, efisiensi, kemajuan, memantau, menganalisis, pengalaman, personal, administratif, lesson |

The final topics represent recurring themes identified across the repeated LLM generations.

### Topic Evaluation

The final topic representation was evaluated using coherence, topic uniqueness, document coverage, and factuality.

| Metric | Score |
|---|---:|
| C_v Coherence | 0.6783 |
| Topic Uniqueness | 1.0000 |
| Document Coverage | 0.9890 |
| Factuality | 1.0000 |

The results indicate that the final topic keywords have strong coherence and high coverage of the processed corpus, while all selected topic keywords were present in the corpus.

### Topic Distribution

The final topic catalog was subsequently used to classify 2,000 comments.

Debat AI vs Peran Guru              66.4% █████████████████████████████████

Personalisasi & Efisiensi           25.8% █████████████

Otomatisasi Tugas Administratif      7.9% ████

The largest group of comments focused on the relationship between AI and the role of teachers, followed by personalization and learning efficiency, while administrative task automation represented a smaller proportion of the dataset.

## Limitations

- LLM-generated topics can vary between runs.
- Jaccard similarity measures lexical overlap and does not capture semantic similarity when different words express the same concept.
- Topic consolidation depends on the similarity-based matching procedure.
- API-based processing introduces computational and financial costs.
- Prompt design can influence the generated topic representation.
- LLM-generated topic interpretations should be validated against the underlying data.

---

## Future Development

Potential extensions include:

- Comparing the LLM-based approach with BERTopic and LDA.
- Replacing or complementing Jaccard similarity with embedding-based semantic similarity.
- Exploring Retrieval-Augmented Generation (RAG) for domain-specific topic discovery.
- Developing an interactive interface for exploring topics and comments.
- Exploring agentic workflows for automated topic analysis.

---

## Author

**Sabrina Adnin Kamila**  
