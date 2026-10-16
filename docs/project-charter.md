# ScamShield SA — Project Charter

**Project status:** Initial planning

**Document version:** 0.2

**Project type:** AI-assisted web application

**Primary market:** South Africa

## 1. Project Overview

ScamShield SA is a web-based digital safety tool designed to help people in South Africa assess suspicious messages and job advertisements. It will combine machine learning with an explicit, rule-based risk assessment system to identify potential warning signs, explain the evidence behind its findings, and recommend safer next steps.

The application is intended to support informed decision-making rather than replace human judgement. It will not claim to prove that a message, job advertisement, person, or organisation is fraudulent or legitimate.

The project will use Python for its backend, data-processing pipeline, machine learning implementation, and rule-based assessment logic. A browser-based frontend will provide the interface through which users submit text and review assessment results.

Development will follow a model-first approach: dataset suitability and model feasibility will be investigated before the machine learning component is treated as operational or integrated into the completed assessment workflow.

## 2. Problem Statement

Scams can imitate legitimate businesses, recruitment agencies, financial institutions, government departments, and individuals. People may struggle to distinguish legitimate communication from suspicious content, particularly when messages use urgency, impersonation, requests for payment, misleading claims, or offers that appear unusually attractive.

ScamShield SA aims to make scam-awareness guidance more accessible by allowing users to submit suspicious text and receive a structured assessment with understandable explanations and practical verification advice.

The usefulness of this solution depends on the quality and relevance of available training data, the reliability of its detection methods, the transparency of its assessment process, and the clarity with which uncertainty is communicated.

The project must therefore demonstrate not only that a model can be trained, but also that its performance has been evaluated appropriately and that its limitations are understood.

## 3. Project Goals

The project aims to:

1. Allow users to submit suspicious messages and job advertisements for assessment.
2. Identify potentially suspicious language and known warning signs.
3. Develop and evaluate a text-classification model using suitable labelled data.
4. Combine machine learning findings with transparent, rule-based checks.
5. Explain assessment findings using evidence from the submitted content.
6. Recommend practical actions users can take to verify suspicious communication safely.
7. Measure model performance using appropriate evaluation metrics and held-out data.
8. Communicate uncertainty and avoid presenting model predictions as proof of fraud.
9. Deliver a secure, usable, maintainable, and deployable web application.
10. Demonstrate sound software engineering, data analysis, machine learning, testing, documentation, and deployment practices.
11. Produce a portfolio-quality project that demonstrates the complete process from data investigation to a working AI-assisted application.

## 4. Target Users

The initial target users are people in South Africa who want help evaluating suspicious digital communication, including:

- Job seekers assessing potentially fraudulent job advertisements.
- Individuals receiving suspicious SMS messages, emails, or instant messages.
- Members of the public seeking understandable scam-awareness guidance.

The initial version is intended for individual use, educational demonstration, and portfolio presentation. Organisational and enterprise use may be considered in later versions after the system has been evaluated further.

The initial language coverage will depend on the languages and types of messages represented in the available dataset. The project will not claim reliable support for languages or communication styles that have not been adequately evaluated.

## 5. Minimum Viable Product (MVP)

The first release will provide a focused text-assessment workflow:

1. A user pastes suspicious text into a browser-based form.
2. The frontend performs basic input validation.
3. A Python backend built with FastAPI validates the request and coordinates the assessment.
4. A trained machine learning pipeline analyses the submitted text.
5. A Python rule engine checks for relevant warning signs.
6. An assessment component combines the available findings according to a documented policy.
7. The API returns a structured result containing an assessment status, risk category where appropriate, supporting evidence, limitations, and recommended next steps.
8. The frontend presents the findings in a clear and understandable format.

The MVP will prioritise reliable behaviour, transparent explanations, privacy-conscious processing, and honest communication of uncertainty over a large number of features.

The model must undergo data suitability checks and meaningful evaluation before it is presented as an operational detection component. If suitable data cannot be obtained, or evaluation reveals serious limitations, the project will document those findings and revise its approach rather than make unsupported performance claims.

## 6. Project Scope

### 6.1 Included in the MVP

- Text-based assessment of suspicious messages and job advertisements.
- A browser-based interface built with HTML, CSS, and JavaScript.
- A Python REST API implemented with FastAPI.
- Dataset discovery, provenance review, and suitability assessment.
- Exploratory data analysis and documented data preparation.
- A baseline text-classification model, subject to data feasibility.
- Comparison of suitable model candidates where the data supports a meaningful comparison.
- A reproducible model-training and evaluation workflow.
- Explicit rule-based warning-sign detection implemented in Python.
- An assessment component that combines model and rule-based findings.
- Evidence-based explanations and practical safety recommendations.
- Input validation, predictable error handling, and privacy-conscious logging.
- Automated tests for data processing, model behaviour, business rules, and API endpoints.
- Technical documentation, version control, and deployment instructions.
- Evaluation of model performance, limitations, and known failure cases.

### 6.2 Excluded from the Initial Release

- User accounts and authentication.
- Persistent user assessment history.
- Automatic website crawling or opening submitted URLs.
- Screenshot uploads and optical character recognition (OCR).
- Browser extensions and native mobile applications.
- Automated reporting to law enforcement or third parties.
- Guaranteed fraud detection or definitive legitimacy verification.
- Complex dashboards and enterprise administration.
- A claim of comprehensive support for every South African language.
- A claim that the application can independently verify the identity of a sender or the legitimacy of an organisation.

These exclusions may be reconsidered after the MVP has been implemented and evaluated.

## 7. Proposed Technology Stack

| Component | Proposed technology |
|---|---|
| Frontend | HTML, CSS, and JavaScript |
| Backend API | Python with FastAPI |
| Data manipulation and analysis | pandas and Jupyter |
| Machine learning | scikit-learn |
| Text representation | TF-IDF |
| Initial classification model | Logistic Regression |
| Candidate comparison model | Linear Support Vector Machine (Linear SVM) |
| Model persistence | joblib, if appropriate |
| Request and response validation | Pydantic through FastAPI |
| Automated testing | pytest and suitable API/integration tests |
| Database | None required for the initial stateless MVP |
| Version control | Git and GitHub |
| Development environment | Visual Studio Code and a Python virtual environment |
| Deployment | To be selected after application and model resource requirements are validated |

This is a proposed starting stack, not a commitment to use every tool regardless of need.

The initial model candidates will be assessed using the available data, suitable metrics, and a leakage-conscious evaluation strategy. The final model choice must be supported by results rather than convenience or assumptions.

The model, rule engine, and assessment logic will initially run within a single Python backend. This avoids unnecessary service complexity while the project is being developed.

The frontend will communicate with the backend through a documented API contract. A database will only be introduced if a demonstrated requirement justifies persistent storage.

## 8. Success Criteria

The MVP will be considered successful when the following criteria have been met or their limitations have been explicitly documented.

### 8.1 Data and Model

- The dataset's source, licence or permitted use, labelling method, class distribution, and limitations have been assessed.
- Duplicate records, data leakage risks, missing values, and potential label-quality issues have been investigated.
- Training and evaluation data are separated appropriately.
- Preprocessing and feature transformations are fitted using training data only and applied consistently during evaluation and inference.
- A reproducible baseline model has been trained and evaluated.
- Appropriate metrics are reported, including precision, recall, F1-score, and a confusion matrix where applicable.
- Class imbalance and the consequences of false positives and false negatives are considered.
- The selected model and its limitations are documented.
- No numerical performance target is claimed without supporting evaluation results.

### 8.2 Application

- A user can submit valid text and receive a structured assessment.
- Invalid or oversized input is rejected safely and predictably.
- The frontend communicates with the Python API using a documented request and response format.
- The model and rule engine can be tested independently.
- Relevant rule-based findings include evidence supporting each warning.
- Partial, inconclusive, and failed assessments are handled without inventing missing results.
- The system communicates uncertainty and does not present predictions as proof.
- Practical safety guidance is displayed with the assessment.

### 8.3 Quality, Security, and Delivery

- Automated tests cover critical data-processing, model, rule-engine, and API functionality.
- Sensitive configuration, credentials, and unauthorised datasets are excluded from version control.
- Submitted message content is not retained by default.
- Deployment and local setup instructions are documented and tested.
- The application can be started and used by following the repository documentation.
- The architecture, development workflow, and significant design decisions are documented.
- A new developer can understand the project structure and reproduce the documented workflow.

## 9. Risks and Constraints

### 9.1 Data Quality and Availability

Suitable labelled scam-related text may be difficult to obtain. Available datasets may be imbalanced, outdated, duplicated, biased toward particular message types, restricted by licence, or poorly representative of South African communication.

**Mitigation:** Investigate data sources, provenance, permitted use, labelling quality, privacy implications, representativeness, and suitability before committing to a model design. If the data is unsuitable, document the limitation and investigate alternatives.

### 9.2 False Positives and False Negatives

Legitimate messages may be flagged as suspicious, while sophisticated scams may not be detected.

**Mitigation:** Evaluate both error types, select metrics appropriate to the problem, communicate uncertainty, and provide verification guidance rather than definitive verdicts. The impact of model errors must be considered when defining risk categories and combining model and rule-based findings.

### 9.3 Misleading Confidence

A model score may be mistaken for the probability that a message is fraudulent. A high score does not necessarily mean a scam is present, and a low score does not establish legitimacy.

**Mitigation:** Document the meaning and limitations of model outputs. Do not describe an uncalibrated model score as a probability. Ensure the assessment policy and user interface communicate uncertainty accurately.

### 9.4 Limited Development Time and Resources

The project is being developed incrementally alongside other commitments, and available computing resources and development time may be limited.

**Mitigation:** Prioritise a small end-to-end MVP, begin with lightweight text-classification models, use clear milestones, and defer optional features until the core workflow is reliable.

### 9.5 Privacy and Security

Submitted text may contain names, contact details, financial information, or other personal data. Logs, training datasets, or external integrations could expose sensitive information if handled carelessly.

**Mitigation:** Minimise collection and retention, avoid storing submitted content by default, avoid unnecessary logging, protect configuration and credentials, assess dataset permissions, and consider applicable South African privacy requirements, including POPIA where relevant.

### 9.6 API and Application Security

Invalid input, oversized requests, unsafe future URL-processing features, or improperly exposed services could introduce security risks.

**Mitigation:** Validate input on the server, set reasonable request-size limits, return safe error messages, restrict unnecessary network access, and avoid automatically fetching submitted URLs in the MVP. Review future features for risks such as server-side request forgery before implementation.

### 9.7 Model and Rule Disagreement

The machine learning model and explicit rules may produce conflicting findings. A simple combination strategy could produce misleading risk categories.

**Mitigation:** Define and document the aggregation policy, test disagreement cases, preserve the distinction between model output and rule evidence, and support inconclusive outcomes where available evidence is insufficient.

### 9.8 Integration and Deployment Complexity

The frontend, Python dependencies, trained model artefacts, and deployment environment must work together consistently.

**Mitigation:** Use a documented API contract, a reproducible Python environment, automated tests, and a minimal application skeleton after the data and model feasibility milestone. Keep the initial backend within one Python application unless later evidence justifies separating services.

## 10. Development Principles

The project will follow these principles:

1. **Model-first, evidence-led development:** Investigate data feasibility and establish a defensible baseline before treating the model as operational.
2. **Incremental delivery:** Build in small, verifiable milestones and maintain working software as the project progresses.
3. **Reproducibility:** Document dataset versions, preprocessing decisions, training configuration, evaluation results, and relevant dependencies.
4. **Separation of responsibilities:** Keep the frontend, API, model pipeline, rule engine, and assessment aggregation logic conceptually distinct.
5. **Explainability:** Provide evidence-based explanations for rule findings and communicate the limitations of model predictions.
6. **Privacy by design:** Minimise data collection and retention and avoid unnecessary exposure of submitted content.
7. **Testing and verification:** Use automated tests and review AI-generated code before accepting it.
8. **Honest reporting:** Report evaluation results and limitations accurately, including negative results and unresolved problems.
9. **Maintainability:** Prefer clear interfaces, understandable code, and documented decisions over unnecessary complexity.
10. **User safety:** Provide practical verification guidance without claiming certainty that the system cannot establish.

## 11. Development Milestones

### Milestone 1: Project Foundation

Establish the project charter, requirements, architecture, development workflow, repository conventions, and initial acceptance criteria.

**Completion evidence:** The core documents agree on the project scope, technology stack, and development approach.

### Milestone 2: Dataset and Model Feasibility

Identify candidate datasets, investigate permitted use and data quality, perform exploratory analysis, examine class distribution and duplicates, and determine whether the data can support a meaningful baseline.

**Completion evidence:** A documented data assessment, a defensible data-preparation strategy, and a decision to proceed, revise the scope, or investigate alternative data sources.

### Milestone 3: Baseline Model and Evaluation

Build a reproducible text-classification pipeline, train a baseline model, compare suitable candidates where appropriate, and evaluate performance on held-out data.

**Completion evidence:** Reproducible evaluation results, appropriate metrics, documented limitations, and a justified decision about whether the model is suitable for further integration.

### Milestone 4: Application Skeleton

Build the minimal browser interface and Python FastAPI backend. Establish the API contract, request validation, health checks, and basic error handling.

**Completion evidence:** The frontend can communicate with the backend and receive a predictable response without requiring the completed model.

### Milestone 5: Assessment Engine and Integration

Integrate the trained model, implement the Python rule engine, define the assessment aggregation policy, and return structured evidence and recommendations.

**Completion evidence:** The model and rules can be tested independently and together, including conflicting, partial, and inconclusive cases.

### Milestone 6: Quality, Privacy, and Security

Expand automated tests, review error handling, check privacy and logging behaviour, validate model artefact loading, and review common application security risks.

**Completion evidence:** Critical test cases pass, known limitations are documented, and the application handles expected failure scenarios safely.

### Milestone 7: Deployment

Choose an appropriate deployment approach, configure the runtime and dependencies, document setup, and perform deployment smoke tests.

**Completion evidence:** The application can be deployed and used according to the documented instructions.

### Milestone 8: Evaluation and Release

Review the end-to-end workflow, record model and application limitations, collect appropriate usability feedback where feasible, and prepare the documentation and demonstration.

**Completion evidence:** The project is reproducible, demonstrable, documented, and ready for portfolio presentation without unsupported claims about detection performance.

## 12. Project Governance

The developer is responsible for the final design, implementation, testing, and release decisions. AI coding assistants may support implementation and review, but their outputs must be understood, verified, and tested before adoption.

Significant changes to scope, architecture, data sources, model selection, and assessment policy should be recorded in the relevant project documentation or decision records.

The project charter, requirements, and architecture documents should be kept consistent as decisions evolve. Proposed technologies and milestones may be revised when data feasibility, evaluation results, security considerations, or resource constraints justify a change.

## 13. Charter Status

This charter defines the initial direction of ScamShield SA. It remains subject to refinement as requirements are validated, suitable data is investigated, and model feasibility is established.

The project's success will be measured by the quality and transparency of its engineering process, the defensibility of its evaluation, and the reliability of its implemented workflow—not merely by whether a model can be trained or a web interface can be demonstrated.