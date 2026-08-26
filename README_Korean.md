# 멀티모달 AI 기반 여행지 추천 시스템

> **이미지 → CLIP + LoRA → 256차원 Embedding → pgvector → Top-K 여행지 추천**

사용자가 업로드한 이미지를 기반으로 시각적으로 유사한 국내 여행지를 추천하는 멀티모달 AI 시스템입니다.

**데이터 구축, 모델 fine-tuning, retrieval 설계, 평가, 서비스 통합**까지 프로젝트 전반을 주도했습니다.

**역할:** Project Lead & AI Systems Engineer · 6인 팀

---

## 🔎 한눈에 보기

| 항목                      | 결과                               |
| ----------------------- | -------------------------------- |
| 데이터셋                    | 직접 수집·정제한 국내 관광 이미지 **약 4,700장** |
| Base Model              | **CLIP ViT-B/32**                |
| Fine-tuning             | **LoRA / Hugging Face PEFT**     |
| Scene Classes           | **13개**                          |
| Classification Accuracy | **74.46%**                       |
| Random Baseline         | 7.7%                             |
| Retrieval Embedding     | **256차원 normalized vector**      |
| Vector Search           | **PostgreSQL + pgvector**        |
| Serving                 | **FastAPI + Docker**             |
| 사용자 평가                  | **100명**                         |

---

## 🏗️ 시스템 아키텍처

```mermaid
flowchart LR
    A[사용자 이미지] --> B[FastAPI]
    B --> C[CLIP ViT-B/32 + LoRA]
    C --> D[Projection Head]
    D --> E[256-d Embedding]
    E --> F[PostgreSQL + pgvector]
    F --> G[Cosine Top-K Search]
    G --> H[여행지 추천 결과]
```

### 요청 처리 흐름

1. FastAPI가 사용자가 업로드한 이미지를 입력받습니다.
2. 서버 시작 시 미리 로드한 CLIP + LoRA 모델을 재사용합니다.
3. 이미지를 **256차원 L2-normalized embedding**으로 변환합니다.
4. pgvector에서 여행지 임베딩과 cosine distance 기반 검색을 수행합니다.
5. 가장 유사한 **Top-K 여행지**를 반환합니다.

---

## 🧠 구현 내용

### 1. 데이터 파이프라인

* 한국관광공사 Open API와 네이버 검색 API를 활용해 약 **4,700장의 국내 관광 이미지**를 수집했습니다.
* 비관련·저품질·중복 이미지를 직접 검수하고 제거했습니다.
* 장소, 계절, 날씨, scene, 감성 관련 메타데이터를 구축했습니다.

### 2. 멀티모달 모델

* **CLIP ViT-B/32**를 base encoder로 사용했습니다.
* **Stage 1:** CLIP backbone을 freeze하고 classification / projection head를 학습했습니다.
* 약 **922K trainable parameters**를 head에서 학습했습니다.
* **Stage 2:** **Hugging Face PEFT LoRA**를 적용해 CLIP vision encoder를 fine-tuning했습니다.
* 다음 loss 조합을 비교 실험했습니다.

  * CLIP Contrastive Loss
  * Cross-Entropy Loss
  * Supervised Contrastive Loss

### 3. Retrieval System

* CLIP feature를 **256차원 retrieval embedding**으로 projection했습니다.
* Cosine similarity 기반 검색을 위해 L2 normalization을 적용했습니다.
* 여행지 embedding을 **PostgreSQL + pgvector**에 저장했습니다.
* Cosine similarity 기반 **Top-K 여행지 retrieval**을 구현했습니다.

### 4. Model Serving

* **FastAPI** 기반 inference API를 구현했습니다.
* 요청마다 모델을 다시 불러오지 않고 서버 시작 시 모델을 한 번 로드하도록 구성했습니다.
* Backend와 PostgreSQL/pgvector database를 **Docker 및 Docker Compose**로 구성했습니다.

---

## 📊 결과

### Offline Evaluation

| Metric                        |     Result |
| ----------------------------- | ---------: |
| Scene Classification Accuracy | **74.46%** |
| Random Baseline               |       7.7% |
| Scene Classes                 |         13 |

### 사용자 평가

Offline model metric에만 의존하지 않고, **100명 대상 blind recommendation evaluation**을 설계해 실제 추천 품질을 검증했습니다.

> **Recall@K / NDCG** 등 retrieval 전용 metric은 후속 평가 항목으로 계획하고 있습니다.

---

## 🛠️ Tech Stack

### AI / ML

`PyTorch` `CLIP ViT-B/32` `Hugging Face PEFT` `LoRA` `Contrastive Learning`

### Retrieval / Data

`PostgreSQL` `pgvector` `NumPy` `Pandas` `Cosine Similarity`

### Serving / Engineering

`FastAPI` `Docker` `Docker Compose` `Python` `Git`

---

## 👩‍💻 담당 역할

**Project Lead & AI Systems Engineer**로서 다음을 담당했습니다.

* End-to-end 멀티모달 추천 시스템 아키텍처 설계
* 약 4,700장 규모 데이터셋 구축 및 큐레이션
* 2-stage CLIP 학습 전략 설계
* LoRA / PEFT fine-tuning 실험
* Projection 및 vector retrieval pipeline 설계
* 100명 대상 blind recommendation evaluation 설계
* 6인 팀 일정 및 역할 분담
* 모델 및 retrieval 관련 주요 기술 의사결정 주도
* 모델 / retrieval / 서비스 통합 및 최종 데모 총괄

---

## 📁 Repository Structure

```text
travel-recommendation/
├── AI/                 # 모델 학습 및 평가
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

## 🚀 개선 계획

* **Recall@K / NDCG** retrieval evaluation 추가
* **P50 / P95 inference latency** 측정
* Exact search와 **HNSW / ANN indexing** 비교
* Throughput 및 batching 실험
* Authentication 및 production security 강화

---

## 💡 프로젝트를 통해 배운 점

모델 학습에서 끝나지 않고 다음 전체 AI 시스템 흐름을 직접 경험했습니다.

**데이터 구축 → Fine-tuning → Embedding → Retrieval → API Serving → 사용자 평가**
