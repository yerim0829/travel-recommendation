[한국어 README](README_Korean.md)

# Multimodal Travel Recommendation System

> **Image → CLIP + LoRA → 256-d Embedding → pgvector → Top-K Travel Recommendation**

A multimodal AI system that recommends Korean travel destinations from a user-uploaded image.

I led the project across **data curation, model fine-tuning, retrieval design, evaluation, and service integration**.

**Role:** Project Lead & AI Systems Engineer · 6-person team

---

## 🔎 At a Glance

| Item                    | Result                                  |
| ----------------------- | --------------------------------------- |
| Dataset                 | **~4,700** curated Korean travel images |
| Base Model              | **CLIP ViT-B/32**                       |
| Fine-tuning             | **LoRA / Hugging Face PEFT**            |
| Scene Classes           | **13**                                  |
| Classification Accuracy | **74.46%**                              |
| Random Baseline         | 7.7%                                    |
| Retrieval Embedding     | **256-d normalized vector**             |
| Vector Search           | **PostgreSQL + pgvector**               |
| Serving                 | **FastAPI + Docker**                    |
| User Evaluation         | **100 participants**                    |

---

## 🏗️ System Architecture

```mermaid
flowchart LR
    A[User Image] --> B[FastAPI]
    B --> C[CLIP ViT-B/32 + LoRA]
    C --> D[Projection Head]
    D --> E[256-d Embedding]
    E --> F[PostgreSQL + pgvector]
    F --> G[Cosine Top-K Search]
    G --> H[Travel Recommendations]
```

### Request Flow

1. FastAPI receives a user-uploaded image.
2. The server reuses the CLIP + LoRA model loaded at startup.
3. The image is converted into a **256-dimensional L2-normalized embedding**.
4. pgvector performs cosine-distance retrieval over destination embeddings.
5. The API returns the **Top-K most similar travel destinations**.

---

## 🧠 What I Built

### 1. Data Pipeline

* Collected approximately **4,700 travel images** using the Korea Tourism Organization Open API and Naver Search API.
* Manually removed irrelevant, low-quality, and duplicate images.
* Built metadata around destination, season, weather, scene, and mood-related attributes.

### 2. Multimodal Model

* Used **CLIP ViT-B/32** as the base encoder.
* **Stage 1:** froze the CLIP backbone and trained classification and projection heads.
* Trained approximately **922K trainable parameters** in the heads.
* **Stage 2:** fine-tuned the CLIP vision encoder using **Hugging Face PEFT LoRA**.
* Compared combinations of:

  * CLIP Contrastive Loss
  * Cross-Entropy Loss
  * Supervised Contrastive Loss

### 3. Retrieval System

* Projected CLIP features into a **256-dimensional retrieval embedding**.
* Applied L2 normalization for cosine-based retrieval.
* Stored destination embeddings in **PostgreSQL + pgvector**.
* Implemented cosine-similarity-based **Top-K recommendation retrieval**.

### 4. Model Serving

* Built the inference API with **FastAPI**.
* Loaded model weights once when the server starts instead of loading them for every request.
* Containerized the backend and PostgreSQL/pgvector database using **Docker and Docker Compose**.

---

## 📊 Results

### Offline Evaluation

| Metric                        |     Result |
| ----------------------------- | ---------: |
| Scene Classification Accuracy | **74.46%** |
| Random Baseline               |       7.7% |
| Number of Scene Classes       |         13 |

### User Evaluation

Designed a **blind recommendation evaluation with 100 participants** to validate downstream recommendation quality beyond offline model metrics.

> Retrieval-specific metrics such as **Recall@K** and **NDCG** are planned as follow-up evaluation.

---

## 🛠️ Tech Stack

### AI / ML

`PyTorch` `CLIP ViT-B/32` `Hugging Face PEFT` `LoRA` `Contrastive Learning`

### Retrieval / Data

`PostgreSQL` `pgvector` `NumPy` `Pandas` `Cosine Similarity`

### Serving / Engineering

`FastAPI` `Docker` `Docker Compose` `Python` `Git`

---

## 👩‍💻 My Contributions

As **Project Lead & AI Systems Engineer**, I was responsible for:

* Designing the end-to-end multimodal recommendation architecture
* Building and curating the ~4,700-image dataset
* Designing the two-stage CLIP training strategy
* Running LoRA / PEFT fine-tuning experiments
* Designing the projection and vector-retrieval pipeline
* Designing the 100-participant blind recommendation evaluation
* Leading sprint planning and task allocation for a 6-person team
* Driving major technical decisions across model and retrieval components
* Coordinating model, retrieval, and service integration for the final demo

> This repository originates from a team project.
> The items above describe the parts I personally owned or led.

---

## 📁 Repository Structure

```text
travel-recommendation/
├── AI/                 # Model training & evaluation
├── backend/
│   ├── app/            # FastAPI inference + pgvector retrieval
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── requirements.txt
├── frontend/
├── dump.sql
└── README.md
```

---

## 🚀 Next Improvements

* Add **Recall@K / NDCG** retrieval evaluation
* Measure **P50 / P95 inference latency**
* Compare exact search with **HNSW / ANN indexing**
* Benchmark throughput and batching
* Strengthen authentication and production security

---

## 💡 Takeaway

This project helped me connect the full AI system lifecycle:

**Data Construction → Fine-tuning → Embedding → Retrieval → API Serving → User Evaluation**
