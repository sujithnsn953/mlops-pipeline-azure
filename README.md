# MLOps Pipeline — Azure

> **End-to-end MLOps platform on Azure** — automated CI/CD with data-triggered model retraining, quality gates, MLflow experiment tracking, and Docker-based staging-to-production promotion. Cut deployment time from 2 weeks to 4 hours.

---

## Overview

Built and operated at **Happiest Minds Technologies** to productionise ML models at scale. The platform automates the full model lifecycle: data monitoring → retraining trigger → experiment tracking → quality gate → staging → production.

Before this platform, model deployments were manual, error-prone, and took 2 weeks. After: **4 hours**, fully automated, with rollback capability.

---

## Architecture

```
                    ┌─────────────────────────────────┐
                    │         Data Layer              │
                    │  Azure Databricks + Delta Lake  │
                    │  Great Expectations (DQ gates)  │
                    └──────────────┬──────────────────┘
                                   │ Data drift / schedule trigger
                                   ▼
                    ┌─────────────────────────────────┐
                    │       Retraining Pipeline       │
                    │  Azure ML Pipeline (SDK v2)     │
                    │  MLflow autolog + model reg     │
                    └──────────────┬──────────────────┘
                                   │ Model registered
                                   ▼
                    ┌─────────────────────────────────┐
                    │         CI/CD (GitHub Actions)  │
                    │  Quality gates (AUC/F1 checks)  │
                    │  Docker build + push to ACR     │
                    │  Deploy to Staging (ACI)        │
                    └──────────────┬──────────────────┘
                                   │ Integration tests pass
                                   ▼
                    ┌─────────────────────────────────┐
                    │      Production (AKS)           │
                    │  Blue/green deployment          │
                    │  Automated rollback on errors   │
                    └─────────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Orchestration | Azure ML Pipelines (SDK v2) |
| Experiment Tracking | MLflow (autolog + custom metrics) |
| Data Processing | Azure Databricks, Delta Lake |
| Data Quality | Great Expectations |
| CI/CD | GitHub Actions |
| Containerisation | Docker, Azure Container Registry |
| Staging | Azure Container Instances (ACI) |
| Production | Azure Kubernetes Service (AKS) |
| Model Registry | Azure ML Model Registry |
| Monitoring | Azure Monitor, Application Insights |

---

## Key Features

### Data-Triggered Retraining
- **Schedule-based**: weekly retraining on fresh data
- **Drift-based**: evidently AI monitors feature distributions; triggers retraining when PSI > 0.2

### Quality Gates
Automated checks before any model promotion:
- ROC-AUC ≥ baseline (champion model)
- Data quality pass (Great Expectations suite)
- Integration test pass (held-out golden dataset)

If any gate fails, the pipeline halts and sends a Slack alert.

### MLflow Tracking
All training runs tracked with:
- Hyperparameters, metrics (AUC, F1, precision, recall)
- Feature importance plots
- Model artifacts + conda environment
- Dataset version (hash)

### Blue/Green Deployment
Zero-downtime production promotion:
1. New model deployed to "green" slot
2. 10% traffic routed via weighted load balancer
3. Error rate monitored for 15 min
4. Automatic 100% cutover or rollback

---

## Pipeline Stages

```
1. data_validation      → Great Expectations DQ checks
2. feature_engineering  → Databricks feature pipeline
3. model_training       → XGBoost/sklearn fit + MLflow log
4. model_evaluation     → Metrics vs. champion comparison
5. model_registration   → Azure ML Model Registry (if better)
6. docker_build         → Build + push to ACR
7. deploy_staging       → ACI deployment + smoke test
8. integration_tests    → Golden dataset validation
9. deploy_production    → AKS blue/green promotion
```

---

## Results

| Metric | Before | After |
|---|---|---|
| Deployment time | 2 weeks | 4 hours |
| Manual steps | ~30 | 0 |
| Training-data incidents | baseline | -45% (Great Expectations) |
| Rollback time | hours | < 5 min (automated) |
| Compute cost | baseline | -30% (spot instances + caching) |

---

## Quick Start

```bash
git clone https://github.com/sujithnsn953/mlops-pipeline-azure.git
cd mlops-pipeline-azure

pip install -r requirements.txt

# Configure Azure credentials
az login
az ml workspace show --name <workspace> --resource-group <rg>

# Run the full pipeline
python pipelines/run_pipeline.py --config configs/pipeline_config.yaml

# Or trigger via CLI
az ml job create -f pipeline.yaml
```

---

## Project Structure

```
mlops-pipeline-azure/
├── pipelines/
│   ├── run_pipeline.py         # Pipeline entry point (Azure ML SDK v2)
│   └── pipeline.yaml           # Pipeline definition
├── components/
│   ├── data_validation/        # Great Expectations checks
│   ├── feature_engineering/    # PySpark feature transforms
│   ├── train/                  # Model training + MLflow logging
│   ├── evaluate/               # Champion vs. challenger comparison
│   └── deploy/                 # ACI / AKS deployment scripts
├── .github/
│   └── workflows/
│       └── mlops_ci_cd.yaml    # GitHub Actions CI/CD workflow
├── configs/
│   └── pipeline_config.yaml    # Environment, compute, thresholds
├── monitoring/
│   └── drift_detector.py       # Evidently AI drift detection
├── Dockerfile
└── requirements.txt
```

---

## Skills Demonstrated

`Azure ML` `MLflow` `Docker` `Kubernetes` `CI/CD` `GitHub Actions` `Azure Databricks` `Great Expectations` `LLMOps` `Python`

---

*Part of Sujith Nagella's ML/GenAI portfolio — [LinkedIn](https://linkedin.com/in/sujith-nagella)*
