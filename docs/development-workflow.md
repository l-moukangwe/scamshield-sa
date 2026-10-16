# Development Workflow — ScamShield SA

**Document version:** 0.2  
**Status:** Draft  
**Project:** ScamShield SA  
**Backend:** Python, FastAPI  
**Frontend:** HTML, CSS, JavaScript

## 1. Purpose

This document defines the standard development process for ScamShield SA. It establishes how code, documentation, datasets, machine-learning experiments, testing, and Git changes should be managed.

The workflow is designed to keep development organised, reproducible, secure, and understandable while prioritising a working and properly evaluated machine-learning model.

## 2. Development Principles

The project will follow these principles:

1. **Model feasibility before integration:** Investigate available datasets, their suitability, licensing, and limitations before committing to a modelling approach.
2. **Evaluate before integrating:** Establish a baseline model and evaluate it before integrating it into the application.
3. **Python-only backend:** Use one Python backend for the API, machine-learning inference, rule-based analysis, and result aggregation. Do not introduce a Java backend or a separate model service for the MVP.
4. **Incremental development:** Complete and verify manageable pieces of work before moving to the next milestone.
5. **Reproducibility:** Document dataset sources, preprocessing, model configurations, random seeds where applicable, evaluation methods, and dependency versions.
6. **Security and privacy by design:** Avoid unnecessary collection, storage, or exposure of potentially sensitive message content.
7. **Evidence-based decisions:** Select models, thresholds, and evaluation metrics based on observed results rather than assumptions.
8. **Clear documentation:** Keep the project documentation consistent with the actual implementation.

## 3. Repository and Branch Management

### 3.1 Repository structure

The repository should progressively adopt a structure similar to the following:

```text
scamshield-sa/
├── docs/
│   ├── project-charter.md
│   ├── requirements.md
│   ├── architecture.md
│   └── development-workflow.md
├── data/
│   ├── raw/
│   ├── processed/
│   └── README.md
├── notebooks/
├── src/
│   └── scamshield/
│       ├── api/
│       ├── core/
│       ├── models/
│       ├── rules/
│       └── schemas/
├── tests/
├── .gitignore
├── requirements.txt
└── README.md
```

This is the intended direction, not a requirement to create every directory immediately. Add directories when the corresponding phase needs them.

The final package structure may change as implementation decisions are validated. Update `docs/architecture.md` if a meaningful structural change is made.

### 3.2 Branch strategy

Use `main` for stable, reviewed project work. Create a separate branch for a focused feature, fix, experiment, or documentation change.

Branch naming conventions:

- `docs/<description>` — documentation
- `feat/<description>` — new functionality
- `fix/<description>` — bug fixes
- `test/<description>` — testing improvements
- `exp/<description>` — machine-learning experiments

Examples:

```text
docs/phase-00-foundation
feat/message-assessment-api
feat/baseline-model
fix/category-mapping
test/model-evaluation
exp/tfidf-logistic-regression
```

Before creating a branch, check the current branch and working-tree status. Do not discard uncommitted changes to switch branches without first deciding how to preserve them.

### 3.3 Commit conventions

Write concise commit messages using the following format:

```text
<type>: <description>
```

Recommended types:

- `docs` — documentation changes
- `feat` — new functionality
- `fix` — bug fixes
- `test` — tests added or updated
- `refactor` — code restructuring without intended behaviour changes
- `data` — dataset documentation or approved data-processing changes
- `model` — model training, evaluation, or model configuration changes
- `chore` — maintenance and configuration

Examples:

```text
docs: establish phase 00 foundation
data: document candidate dataset sources
model: add baseline text classifier
test: add assessment API tests
fix: handle inconclusive model predictions
```

Keep commits focused on a coherent change. Avoid committing unrelated work together.

### 3.4 Standard Git workflow

From the repository directory:

```powershell
git status
git branch --show-current
git diff
```

Review the changes before staging them.

Stage specific files where practical:

```powershell
git add docs/project-charter.md docs/requirements.md
git diff --cached
git commit -m "docs: update project foundation"
```

Push the branch when ready:

```powershell
git push -u origin <branch-name>
```

Replace `<branch-name>` with the actual branch name.

Before opening a pull request or merging into `main`, verify that the changes are intentional, relevant checks pass, and documentation remains consistent. Do not force-push, reset, or restore files merely to eliminate warnings without understanding their effects.

## 4. Python Environment and Dependencies

### 4.1 Environment setup

Use a project-specific virtual environment to isolate dependencies from other Python projects.

From the repository root in PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python --version
python -m pip install --upgrade pip
```

If PowerShell prevents environment activation, use an appropriate permitted activation method or invoke the environment's Python executable directly.

### 4.2 Dependency management

Use `requirements.txt` as the initial dependency manifest.

Add dependencies when the project needs them, such as:

- FastAPI for the backend API
- Uvicorn for running the API
- pandas and NumPy for data processing
- scikit-learn for baseline machine-learning models
- joblib for loading approved model artefacts when appropriate
- pytest for automated tests
- HTTPX for API testing through FastAPI's test client

Jupyter and visualisation libraries may also be required during exploration and evaluation.

Install project dependencies using:

```powershell
python -m pip install -r requirements.txt
```

After deliberately changing the dependencies, update `requirements.txt` with the packages and compatible versions needed to reproduce the environment. Avoid repeatedly replacing it with an uncontrolled dump of every package installed on the computer.

Do not commit the virtual environment itself.

### 4.3 Environment configuration

Keep environment-specific settings outside source code when they may contain secrets or vary between machines.

- Never commit passwords, API keys, access tokens, or private credentials.
- Provide safe examples in a file such as `.env.example` if environment variables are required.
- Ensure actual secret-bearing environment files are ignored by Git.
- Do not introduce external services or credentials unless they are necessary for an approved feature.

## 5. Dataset Governance and Preparation

Dataset investigation is a prerequisite to selecting and training the model.

### 5.1 Dataset selection

Before using a dataset, document:

- Name and source
- Original download or access location
- Licence and permitted uses
- Collection method, where known
- Available labels and their meanings
- Language and geographic relevance
- Data quality issues and missing values
- Potential class imbalance
- Duplicates and possible overlap with evaluation data
- Known biases, limitations, and suitability for South African scam-related messages

Do not assume that a publicly accessible dataset is automatically licensed for every intended use.

### 5.2 Data storage

Keep large, sensitive, restricted, or unlicensed datasets out of the Git repository unless there is a clear, authorised reason to version specific files.

The `.gitignore` file should exclude, as appropriate:

- `.venv/`
- Python cache files and test caches
- Local environment files containing secrets
- Raw and processed datasets that should not be versioned
- Generated model artefacts and experiment outputs that are too large or inappropriate for ordinary source control
- Temporary files and local development logs

Small, non-sensitive, legally reusable fixtures may be committed when they are needed for reproducible tests. Document their source and permitted use.

The `data/README.md` file should explain where approved datasets come from, how to obtain them, and how the project expects them to be organised. Never invent dataset provenance.

### 5.3 Data preparation

Use notebooks for exploratory analysis and experimentation. Move stable, reusable preprocessing steps into tested Python modules when they become part of the production pipeline.

Record important transformations, including:

- Missing-value handling
- Duplicate removal
- Label normalisation
- Text cleaning and normalisation
- Class distribution analysis
- Feature extraction
- Dataset splitting

Avoid removing meaningful information from suspicious messages through excessive text cleaning.

Keep a clear distinction between raw data, processed data, training data, validation data, and final test data.

## 6. Machine-Learning Development Workflow

### 6.1 Feasibility assessment

Before training, establish whether the available data can support the intended problem.

Investigate whether the dataset contains relevant scam and legitimate examples, whether labels are reliable, and whether its limitations permit meaningful evaluation.

If the available data is insufficient, document the gap and investigate alternatives before presenting a model as reliable.

### 6.2 Establish a baseline

Start with a simple, interpretable text-classification baseline. A candidate is TF-IDF feature extraction combined with Logistic Regression.

Other models, including a linear Support Vector Machine, may be compared if the data and evaluation results justify doing so.

Do not assume that a more complex model will perform better.

### 6.3 Split and prepare the data correctly

Create suitable training and evaluation splits before fitting data-dependent preprocessing.

- Fit text vectorisers, scalers, feature selectors, and other learned preprocessing only on the training data.
- Use a validation set or cross-validation for model and threshold selection where appropriate.
- Keep the final test set separate from decisions made during model development.
- Use stratified splitting when appropriate for the class distribution.
- Investigate duplicate and near-duplicate messages that could leak across splits.
- Group related examples before splitting when they could otherwise appear in both training and evaluation data.

Use fixed random seeds where supported and appropriate. Record the splitting method and any important limitations.

### 6.4 Evaluate the model

Choose metrics based on the cost of different classification errors.

At minimum, examine:

- Precision
- Recall
- F1-score
- Confusion matrix
- Class distribution
- Performance by class

Accuracy may be reported but must not be the only metric, particularly if classes are imbalanced.

For ScamShield SA, false negatives may allow scams to be missed, while false positives may incorrectly flag legitimate messages. Discuss this trade-off when selecting thresholds.

Do not describe an uncalibrated model score as a reliable probability of fraud. Do not invent performance figures or claim that the model generalises to South African scams without supporting evidence.

### 6.5 Model selection and reproducibility

Choose a model based on measured performance, appropriate error trade-offs, reproducibility, and suitability for the application.

Document:

- Dataset version or source details
- Preprocessing steps
- Features and model type
- Relevant configuration and random seeds
- Evaluation method and results
- Model limitations
- The reason for selecting the final candidate

Save the fitted preprocessing and model components together when needed so that inference uses the same transformations as training.

Only load trusted model artefacts. Do not load arbitrary files from untrusted sources using Python serialisation tools.

### 6.6 Model acceptance gate

Before integrating a trained model into the API, verify that:

- Its data source and intended use are documented.
- Its evaluation procedure is credible and reproducible.
- Its limitations and error patterns are understood.
- It can be loaded and used consistently for inference.
- It has a defined behaviour for unsupported inputs or uncertain predictions.
- Automated tests cover essential inference behaviour.

If these conditions are not met, record the outstanding issues and continue evaluation rather than representing the model as production-ready.

## 7. Backend and API Development

### 7.1 Implementation order

Develop the application in manageable stages:

1. Complete dataset feasibility work.
2. Build and evaluate the baseline model.
3. Establish the Python application structure and API contracts.
4. Implement input validation and the rule-based analysis layer.
5. Integrate the evaluated model into the backend.
6. Implement result aggregation and response handling.
7. Connect the frontend to the API.
8. Perform integrated testing, security checks, and deployment preparation.

Some scaffolding and small experiments may occur earlier when they help validate feasibility. However, avoid building extensive interface functionality around an untested assumption that the model will work.

### 7.2 API standards

The main assessment endpoint is:

```text
POST /api/v1/assessments
```

Health and readiness endpoints are:

```text
GET /health
GET /ready
```

Follow the API contracts defined in `docs/requirements.md`.

The backend should validate incoming requests, apply appropriate analysis, handle errors safely, and return consistent structured responses.

Distinguish between a completed assessment, a partial assessment, an inconclusive result, and a failed request. Do not silently substitute a model failure with a confident risk classification.

### 7.3 Error handling

- Reject invalid input with a suitable client error.
- Return consistent error structures.
- Avoid exposing stack traces, filesystem paths, secrets, or internal implementation details to users.
- Handle unavailable model artefacts and inference errors explicitly.
- Ensure that unexpected errors do not produce misleading assessment results.

## 8. Security and Privacy Workflow

Treat user-submitted messages and URLs as untrusted input.

### 8.1 Input handling

- Validate input types, lengths, and required fields.
- Reject unsupported or excessively large inputs.
- Escape or safely render user-provided text in the frontend.
- Avoid executing submitted content or treating it as trusted instructions.
- Do not automatically fetch user-submitted URLs in the MVP.

### 8.2 Privacy

- Do not persist submitted message content by default.
- Do not log full messages, credentials, tokens, or other sensitive content.
- Keep logs focused on operational information such as request outcomes, latency, and error types.
- If additional storage or analytics are proposed, assess their purpose, necessity, access controls, retention, and privacy implications first.

### 8.3 Security checks

Before release, review dependency vulnerabilities, secret exposure, input validation, error handling, CORS configuration where applicable, and any other relevant deployment settings.

Document security limitations that cannot reasonably be resolved within the MVP.

## 9. Testing and Quality Assurance

### 9.1 Testing levels

**Unit tests**

Test individual functions and components, including preprocessing, rules, aggregation, input validation, and error handling.

**Model tests**

Test preprocessing and inference consistency, supported input cases, empty or malformed inputs, and defined behaviour for uncertain predictions.

**API tests**

Test successful requests, invalid requests, response schemas, model availability, error handling, and health/readiness endpoints.

**Integration tests**

Verify that the API, rule engine, model pipeline, and aggregation layer work together correctly.

**Frontend tests**

Verify input validation, API communication, loading states, error messages, and the presentation of results without misleading users.

**Manual acceptance tests**

Check realistic examples, edge cases, accessibility basics, and whether the displayed explanations accurately reflect the available evidence.

### 9.2 Running tests

When the relevant dependencies and tests have been added, run:

```powershell
python -m pytest
```

Run focused tests during development and the relevant test suite before committing changes. Record failures and resolve or explicitly document known limitations.

Do not claim that tests pass without running them.

### 9.3 Regression testing

When fixing a bug, add or update a test that reproduces the issue where practical. Re-run the relevant tests to confirm that the fix works and has not broken related behaviour.

When changing the model, preprocessing, risk rules, or thresholds, evaluate the impact on previously tested examples and documented performance.

## 10. Documentation Workflow

Update documentation when a change affects requirements, architecture, setup, behaviour, evaluation, security, or known limitations.

- `docs/project-charter.md`: update only when the project goals, scope, or milestones materially change.
- `docs/requirements.md`: update when functional or non-functional requirements change.
- `docs/architecture.md`: update when components, responsibilities, data flow, or deployment structure change.
- `docs/development-workflow.md`: update when the team's development practices materially change.
- `README.md`: document the current project status, setup instructions, usage, and known limitations.
- `data/README.md`: document dataset provenance, access, and organisation.
- Model evaluation documentation: record experimental methods, results, model selection, and limitations.

Keep documentation factual. Clearly distinguish planned features from implemented features and measured results from targets.

## 11. Definition of Done

A task is considered complete when all applicable criteria below are satisfied:

- [ ] The change addresses a defined requirement or documented task.
- [ ] The implementation follows the current architecture and development conventions.
- [ ] Relevant tests have been added or updated.
- [ ] Relevant tests have been executed, with failures resolved or explicitly documented.
- [ ] Input validation and error handling are appropriate.
- [ ] Privacy and security implications have been considered.
- [ ] Documentation reflects any meaningful changes.
- [ ] No secrets, prohibited data, virtual environments, or unintended generated files are included.
- [ ] The Git diff has been reviewed.
- [ ] The change is committed with a clear message when it is ready to be committed.

For machine-learning tasks, also document data provenance, preprocessing, evaluation, and model limitations as applicable.

For release tasks, also verify the relevant end-to-end workflow and deployment configuration.

## 12. Phase 00 Foundation Completion

The foundation phase is complete when:

- [ ] The project charter is saved and consistent with the Python-only direction.
- [ ] The requirements document defines the MVP and expected system behaviour.
- [ ] The architecture document describes the intended components and data flow.
- [ ] This development workflow is documented.
- [ ] Git status and the current branch have been checked.
- [ ] The four documents have been reviewed together for contradictions.
- [ ] The documentation changes have been reviewed and committed on the intended branch.

Completion of Phase 00 establishes the documented foundation. It does not mean the dataset, model, API, or frontend has already been implemented.

## 13. Working Agreement

ScamShield SA will be developed in deliberate, verifiable increments. Decisions should be based on evidence, documentation should reflect the actual state of the project, and model performance must never be exaggerated.

The priority is to deliver a functioning, evaluated, and explainable MVP before expanding into additional features.