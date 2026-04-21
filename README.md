# 🧬 GenRx — AI-Powered Precision Drug Response Predictor

> Adverse drug reactions kill an estimated 100,000+ patients annually. In Southern Africa, where over 17 million people across Zimbabwe, South Africa, Botswana, Namibia, Zambia and neighbouring countries are on antiretroviral therapy, personalized medicine is nearly inaccessible. GenRx uses machine learning, pharmacogenomics, and explainable AI to predict how individual patients respond to drugs — before they take them.

<p align="center">
  <img src="docs/screenshots/dashboard-preview.png" alt="GenRx Dashboard" width="800"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Status-Phase%201%20%E2%80%94%20In%20Development-yellow" />
  <img src="https://img.shields.io/badge/Python-3.10+-blue" />
  <img src="https://img.shields.io/badge/License-MIT-green" />
  <img src="https://img.shields.io/badge/Made%20for-Southern%20Africa-red" />
</p>

---

## The Problem

Every drug prescription is a calculated risk. A clinician prescribes based on population-level averages — but patients are individuals. Their genetics, renal function, liver health, weight, and concurrent medications all influence how a drug behaves in their body.

The consequences of getting it wrong are severe:

- Adverse drug reactions (ADRs) are responsible for 100,000+ deaths annually in the US alone
- Studies show that **up to 94% of ADRs go unreported globally** — meaning we are training models on incomplete ground truth
- In Southern Africa, over **17 million people** across Zimbabwe, South Africa, Botswana, Namibia, and Zambia are on antiretroviral therapy — a population with uniquely high polypharmacy complexity and genetic diversity that most existing models ignore
- The number of possible drug pair combinations runs into the millions, yet documented interaction data covers only a small fraction of those pairs

The hardest problems in precision medicine are not ML problems. They are data problems, trust problems, and infrastructure problems.

---

## The Solution

GenRx is a full-stack, production-oriented clinical decision support system that predicts:

- **ADR risk score** per drug per patient, with confidence intervals
- **Severity classification** — mild / moderate / severe / contraindicated
- **Drug-drug interaction flags** with mechanistic explanations
- **Plain-English clinical reasoning** powered by a knowledge-grounded LLM

GenRx is designed explainability-first. A risk score alone will not change clinical behavior. Clinicians need to see _why_ a drug combination is flagged as dangerous. Every prediction is accompanied by a SHAP waterfall visualization showing the exact patient factors driving the risk.

---

## Why Southern Africa

Most ADR prediction models are trained on data from Western populations. Genetic variants that affect drug metabolism — such as CYP2B6 variants that impact Efavirenz used in HIV treatment — are distributed very differently across African populations. Building GenRx on data that ignores this is not just a technical limitation. It is a form of embedded bias that produces worse predictions for the exact patients GenRx is meant to serve.

GenRx includes a dedicated **African Population Genetics Module** with preloaded allele frequency profiles for Southern African populations, enabling more accurate metabolizer phenotype inference for patients in Zimbabwe, Zambia, South Africa, Botswana, and Namibia.

---

## Architecture

<p align="center">
  <img src="docs/screenshots/architecture-diagram.png" alt="GenRx System Architecture" width="800"/>
</p>

| Layer          | Components                                               |
| -------------- | -------------------------------------------------------- |
| Frontend       | React + TypeScript + Vite + Tailwind CSS                 |
| API Gateway    | FastAPI — auth, rate limiting, validation                |
| Core Services  | Prediction service, Patient service, Explanation service |
| ML Pipeline    | XGBoost → TabNet → BentoML serving                       |
| Explainability | SHAP TreeExplainer + LLM clinical narrative              |
| Data Pipeline  | Apache Airflow + dbt + Great Expectations                |
| Feature Store  | Feast — patient vectors, SMILES molecular features       |
| Storage        | PostgreSQL + Redis + MinIO + Qdrant                      |
| Monitoring     | Evidently AI + Grafana — data drift + model performance  |
| CI/CD          | GitHub Actions — lint → test → build → deploy            |

---

## Key Insights from Phase 1 Research

1. **The combinatorial gap** — The number of possible drug pair combinations runs into the millions. Documented interaction data covers only a small fraction. GenRx cannot rely purely on training examples. It must reason about mechanisms it has never seen before.

2. **Class imbalance is a patient safety problem** — Most ADR training data is overwhelmingly "no reaction." Models learn to say "you're fine" and score 95% accuracy while being clinically useless. GenRx is evaluated on AUPRC and recall on severe events — not accuracy.

3. **94% of ADRs go unreported** — The class imbalance exists because of systemic underreporting, not random noise. This shaped the entire data strategy.

4. **Causality is probabilistic** — The WHO Uppsala Monitoring Centre's scale runs from "certain" to "unclassifiable." Training labels are not clean ground truth. GenRx handles uncertainty explicitly in its model outputs.

5. **Polypharmacy is the real clinical problem** — Patients don't take one drug. They take many. GenRx evaluates drug combinations, not individual drugs in isolation.

---

## Model Performance

> Results will be updated as training progresses through each phase.

| Model                          | AUROC | AUPRC | Brier Score | Notes   |
| ------------------------------ | ----- | ----- | ----------- | ------- |
| Logistic Regression (baseline) | TBD   | TBD   | TBD         | Phase 1 |
| XGBoost                        | TBD   | TBD   | TBD         | Phase 2 |
| TabNet                         | TBD   | TBD   | TBD         | Phase 3 |

**Evaluation priority:** AUPRC and recall on severe ADR events — not accuracy. A model that is good at predicting "nothing happens" is clinically useless.

---

## Key Features

| Feature                                        | Status         |
| ---------------------------------------------- | -------------- |
| ADR risk score with confidence interval        | 🔄 In progress |
| Severity classification (mild/moderate/severe) | 🔄 In progress |
| Drug-drug interaction checker                  | 🔄 In progress |
| SHAP waterfall explainability                  | 🔄 In progress |
| Plain-English clinical narrative (LLM)         | 📋 Planned     |
| African population genetics module             | 📋 Planned     |
| Polypharmacy safety checker                    | 📋 Planned     |
| Dosage risk simulator                          | 📋 Planned     |
| FHIR R4 integration                            | 📋 Planned     |
| Model drift monitoring dashboard               | 📋 Planned     |

---

## Data Sources

| Dataset                 | Purpose                                           | Access                   |
| ----------------------- | ------------------------------------------------- | ------------------------ |
| MIMIC-IV                | ICU patient records, prescriptions, outcomes      | PhysioNet (credentialed) |
| PharmGKB                | Gene-drug relationships, pharmacogenomic variants | Free                     |
| SIDER 4.1               | Drug → side effect associations (1,430 drugs)     | Free                     |
| DrugBank 5.0            | Drug-drug interactions, pharmacokinetics          | Free (academic)          |
| FDA FAERS               | Adverse event reporting database                  | Free                     |
| 1000 Genomes / H3Africa | African population genetic variation              | Free / Requestable       |

---

## Tech Stack

| Layer               | Technology              | Why                                           |
| ------------------- | ----------------------- | --------------------------------------------- |
| ML models           | XGBoost + TabNet + SHAP | Best-in-class tabular ML + interpretability   |
| Experiment tracking | MLflow                  | Industry standard, free to self-host          |
| Molecular features  | RDKit + SMILES          | Encode drug chemistry as computable features  |
| Backend             | FastAPI (Python)        | Async, auto OpenAPI docs, Python ML ecosystem |
| Frontend            | React + TypeScript      | Type safety, component reuse                  |
| Database            | PostgreSQL + Redis      | Reliable relational + fast cache              |
| Serving             | BentoML                 | Production ML serving with versioning         |
| Containers          | Docker + Docker Compose | One-command reproducible environment          |
| CI/CD               | GitHub Actions          | Free, native GitHub integration               |

---

## Quick Start

```bash
# Clone the repo
git clone https://github.com/Russell-Mazambara/GenRx.git
cd GenRx

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Start all services
docker-compose up

# API docs available at http://localhost:8000/docs
```

---

## Project Roadmap

| Phase   | Focus                                         | Status         |
| ------- | --------------------------------------------- | -------------- |
| Phase 1 | Data pipeline + EDA + baseline model          | 🔄 In progress |
| Phase 2 | XGBoost + SHAP explainability + FastAPI       | 📋 Planned     |
| Phase 3 | TabNet + Frontend + Auth                      | 📋 Planned     |
| Phase 4 | Polypharmacy module + African genetics module | 📋 Planned     |
| Phase 5 | Deployment + CI/CD + model monitoring         | 📋 Planned     |

---

## Future Vision

Beyond the core prediction engine, GenRx is designed to evolve into a fully interactive clinical simulation and decision-support platform.

**Digital Twin Simulation (Phase 6+)**

The long-term vision for GenRx is a system where clinicians don't just receive a risk score — they can _see_ what happens inside a patient's body before a drug is administered.

A clinician inputs patient data. GenRx generates an interactive digital representation of that patient's physiology. The clinician selects a drug, dosage, and route of administration. The system then visualizes — in real time — the step-by-step interaction of that drug with the patient's receptors, metabolism pathways, and organ systems. What would have taken hours of manual deliberation happens in minutes of interactive simulation.

**Intelligent Gap-Filling**

When the simulation surfaces an unexpected side effect, GenRx doesn't just flag it — it identifies what information is missing and suggests the exact questions the clinician should ask the patient to fill the gaps. Travel history. Regional disease exposures. Environmental factors. Dietary interactions. The system connects symptoms to root causes and helps doctors ask the right questions before prescribing.

**The goal:** compress hours of clinical deliberation into minutes of interactive, evidence-grounded simulation — making the kind of precision medicine currently available only in top research hospitals accessible to clinicians in Harare, Lusaka, and Cape Town.

---

## Repository Structure

```
GenRx/
├── README.md
├── ARCHITECTURE.md
├── MODEL_CARD.md
├── docker-compose.yml
├── requirements.txt
├── Makefile
├── backend/
│   ├── app/
│   │   ├── api/routes/
│   │   ├── core/
│   │   ├── models/
│   │   ├── services/
│   │   └── ml/
│   └── tests/
├── frontend/
│   └── src/
│       ├── components/
│       ├── pages/
│       └── hooks/
├── ml/
│   ├── notebooks/
│   ├── training/
│   ├── evaluation/
│   └── experiments/
├── data_pipeline/
│   ├── dags/
│   ├── processors/
│   └── validators/
├── data/
│   ├── raw/
│   ├── processed/
│   └── features/
└── docs/
    └── screenshots/
```

---

## Skills Demonstrated

| Skill               | Where                                                                               |
| ------------------- | ----------------------------------------------------------------------------------- |
| ML engineering      | XGBoost + TabNet, SHAP explainability, MLflow, model versioning, drift monitoring   |
| Data engineering    | Airflow pipeline, feature store, Great Expectations validation, multi-source fusion |
| Backend engineering | FastAPI async API, PostgreSQL schema, Redis caching, JWT auth, Pydantic validation  |
| System design       | Microservices architecture, ML serving layer, event-driven prediction queue         |
| Frontend            | React + TypeScript, D3 custom SHAP visualizer, clinical UX                          |
| DevOps              | Docker Compose, GitHub Actions CI/CD, model CI pipeline                             |
| Domain knowledge    | Pharmacogenomics, clinical ADR prediction, African population genetics              |

---

## Building in Public

I'm documenting this project on LinkedIn as I build it — sharing what I learn, what surprises me, and what I get wrong.

- [LinkedIn — Phase 1: The data problem is harder than the ML problem](#)
- [LinkedIn — Phase 1: Technical deep dive](#)

---

## Author

**Russell Mazambara**
AI/ML Engineer in training — building for Southern Africa

[https://www.linkedin.com/in/russell-mazambara-63b635350/](#) · [https://github.com/Russell-Mazambara](#)

---

## License

MIT License — see [LICENSE](LICENSE) for details.
