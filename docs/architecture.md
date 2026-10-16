# ScamShield SA — System Architecture

**Document version:** 0.3

**Status:** Draft

**Project:** ScamShield SA

## 1. Purpose

This document describes the proposed architecture for ScamShield SA, a web-based application that helps users assess suspicious messages and job advertisements.

The system combines a machine learning text-classification pipeline with explicit rule-based checks. It returns a structured assessment containing the available findings, an appropriate risk category where justified, and practical safety recommendations.

The backend will be implemented entirely in Python. The initial design uses one FastAPI application for request handling, model inference, rule evaluation, and assessment aggregation. The frontend will communicate with this application through a documented REST API.

The architecture will evolve as dataset suitability, model performance, application requirements, and deployment constraints are validated.

## 2. Architectural Principles

The system will follow these principles:

1. **Model-first development:** Investigate dataset suitability and establish a defensible baseline before treating the model as operational.
2. **Simplicity:** Begin with a single Python backend rather than separate services for model inference and business rules.
3. **Separation of responsibilities:** Keep API handling, model inference, rule evaluation, and assessment aggregation in distinct modules.
4. **Reproducibility:** Use a consistent preprocessing and inference pipeline and document model artefacts and dependencies.
5. **Transparency:** Preserve the distinction between model predictions, rule findings, and the final assessment.
6. **Privacy by default:** Do not persist submitted message content or assessment history in the initial release.
7. **Failure-aware design:** Represent unavailable components and inconclusive results explicitly instead of inventing missing findings.
8. **Testability:** Design components so they can be tested independently and together.
9. **Evidence-based decisions:** Select models, thresholds, and aggregation policies using documented reasoning and evaluation results.

## 3. High-Level Architecture

The system consists of a browser-based frontend and a single Python backend.

```text
+-----------------------------------------+
|              User's Browser             |
|                                         |
|       HTML + CSS + JavaScript           |
|                                         |
|  Text Form -> Validation -> Results     |
+--------------------+--------------------+
                     |
                     | HTTP/HTTPS
                     | JSON requests/responses
                     v
+--------------------------------------------------+
|              Python FastAPI Backend              |
|                                                  |
|  +-------------------+                           |
|  | API and Validation|                           |
|  +---------+---------+                           |
|            |                                     |
|            v                                     |
|  +-----------------------+                       |
|  | Assessment Coordinator|                       |
|  +------+------------+---+                       |
|         |            |                           |
|         v            v                           |
|  +-------------+ +------------------+            |
|  | ML Inference| | Rule-Based Engine|            |
|  +------+------+ +--------+---------+            |
|         |                 |                      |
|         +--------+--------+                      |
|                  v                               |
|        +---------------------+                   |
|        | Assessment Aggregator|                  |
|        +----------+----------+                   |
|                   |                              |
|                   v                              |
|          Structured API Response                 |
+--------------------------------------------------+

          Model artefact and pipeline
          loaded by the Python backend
```

The diagram represents logical responsibilities, not separate deployed services. The model, rule engine, and assessment aggregator initially run within the same Python application.

## 4. Technology Stack

| Component | Proposed technology |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Backend API | Python and FastAPI |
| Request validation | Pydantic through FastAPI |
| Data analysis | pandas and Jupyter |
| Machine learning | scikit-learn |
| Text representation | TF-IDF |
| Initial classifier | Logistic Regression |
| Candidate model | Linear Support Vector Machine |
| Model persistence | joblib, if appropriate |
| Automated tests | pytest |
| Version control | Git and GitHub |
| Development environment | Visual Studio Code and Python virtual environment |
| Persistence | None in the initial MVP |
| Deployment | To be determined after resource and operational requirements are assessed |

These technologies are proposed starting points. The final model and deployment choices must be supported by experimental results and practical requirements.

## 5. Component Responsibilities

### 5.1 Frontend

The frontend provides the user-facing interface.

Responsibilities:

- Accept text for assessment.
- Perform basic client-side validation.
- Submit requests to the backend.
- Display loading, success, partial, inconclusive, and error states.
- Present the risk category, findings, limitations, and recommendations.
- Avoid describing an assessment as definitive proof of fraud or legitimacy.

Client-side validation improves usability but does not replace backend validation.

### 5.2 API Layer

The API layer receives requests and returns structured responses.

Responsibilities:

- Expose versioned endpoints.
- Validate incoming request fields and input length.
- Reject malformed or invalid requests.
- Call the assessment coordinator.
- Return documented response structures and appropriate HTTP status codes.
- Avoid exposing internal stack traces or sensitive configuration.

Proposed endpoints:

- `POST /api/v1/assessments` — assess submitted text.
- `GET /health` — report whether the API process is responding.
- `GET /ready` — report whether required assessment components are ready.

### 5.3 Assessment Coordinator

The assessment coordinator orchestrates the assessment workflow.

Responsibilities:

- Receive validated text from the API layer.
- Invoke the available model and rule-based components.
- Collect their outputs.
- Send the outputs to the aggregation component.
- Handle expected component failures according to documented policies.
- Return a structured internal assessment result.

The coordinator should not contain model-training code or duplicate the implementation of individual rules.

### 5.4 Machine Learning Pipeline

The machine learning pipeline predicts a label or classification signal from the submitted text.

The proposed initial pipeline is:

1. Load the trained preprocessing and classification pipeline.
2. Validate that the input is suitable for inference.
3. Transform the text using the fitted feature-extraction pipeline.
4. Obtain the model's prediction and any supported score.
5. Return a structured model result with documented semantics.

The initial candidate is TF-IDF with Logistic Regression. A Linear SVM may be evaluated as an alternative.

Training and evaluation shall occur through a reproducible development workflow, not automatically during normal API requests.

The fitted pipeline must be reused consistently during inference. An uncalibrated decision score or model output must not be presented as a probability of fraud.

The application must not claim that the model is operational until dataset suitability and evaluation results justify its use.

### 5.5 Rule-Based Engine

The rule engine checks for explicit warning signs in the submitted text.

Potential warning-sign categories include:

- Requests for upfront payments or recruitment fees.
- Unusual urgency or pressure to act immediately.
- Requests for sensitive information.
- Suspicious claims about guaranteed employment or rewards.
- Language that discourages independent verification.

These are candidate rules, not proof that a message is fraudulent. Some legitimate communications may contain similar language.

Each rule should have a stable identifier, a description, a defined matching condition, and a documented test strategy. Where practical, a triggered rule should return a relevant excerpt from the submitted text.

Rules should be implemented as maintainable, testable Python components rather than as one large collection of hard-coded conditions inside the API endpoint.

### 5.6 Assessment Aggregator

The aggregator combines model findings and rule results into the final assessment.

Responsibilities:

- Evaluate the available model output and rule findings.
- Apply the documented aggregation policy.
- Assign a risk category only where the available evidence supports one under that policy.
- Preserve the distinction between model predictions and explicit rule evidence.
- Identify incomplete or inconclusive assessments.
- Attach suitable limitations and recommendations.

The aggregation policy must be documented and tested before being treated as operational. It must not assume that several warning signs necessarily establish fraud or that a low model score proves legitimacy.

The system should support inconclusive results where evidence is insufficient or assessment components disagree in a way the policy cannot resolve defensibly.

### 5.7 Response Builder

The response builder converts internal assessment results into the documented API response.

Responsibilities:

- Return a stable response structure.
- Include the assessment status and risk category where available.
- Include model and rule findings when available.
- Include practical safety recommendations and relevant limitations.
- Avoid fabricated results when a component fails.
- Exclude unnecessary internal implementation details.

The response format must remain consistent with the requirements document and frontend implementation.

## 6. Data and Model Development Workflow

The model development process will be separated from normal request processing.

### 6.1 Dataset Investigation

Before selecting a dataset, investigate:

- Source and permitted use.
- Label definitions and quality.
- Missing values and malformed records.
- Class distribution and imbalance.
- Duplicate and near-duplicate messages.
- Potential overlap between training and evaluation data.
- Relevance to South African scams and communication patterns.
- Language coverage and known representativeness limitations.

Dataset suitability must be documented before the model is treated as a meaningful detection component.

### 6.2 Exploratory Data Analysis

Use Python and suitable analysis tools to understand the data, including:

- Record counts and label distribution.
- Missing and duplicate records.
- Text lengths and common patterns.
- Differences between classes.
- Possible sources of bias or leakage.

Visualisations and summary statistics should support decisions about preparation and evaluation rather than merely decorate the project.

### 6.3 Preprocessing and Splitting

Create a reproducible preprocessing workflow.

- Define the target labels clearly.
- Separate training and evaluation data appropriately.
- Consider stratification when class balance requires it.
- Investigate duplicates before splitting.
- Keep learned transformations, including TF-IDF vocabulary fitting, within the training pipeline.
- Apply the fitted transformations consistently to validation, test, and inference data.

If related or duplicated messages could appear across data splits, the evaluation design should account for that risk.

### 6.4 Training and Evaluation

Train the baseline model and compare alternatives where appropriate.

Evaluate using metrics suited to the problem, such as precision, recall, F1-score, and a confusion matrix. Consider class imbalance and the practical costs of false positives and false negatives.

Record the dataset version, split strategy, model configuration, evaluation results, and known limitations.

Model selection must be based on evidence rather than an arbitrary target score.

### 6.5 Model Artefact Management

Save the fitted pipeline only when its use is justified and its dependencies are understood.

Document:

- Model and pipeline version.
- Training-data source or version.
- Relevant training configuration.
- Evaluation results.
- Required software dependencies.
- Known limitations and expected input format.

Treat serialised model files as trusted project artefacts. Only load artefacts from controlled, trusted sources, because Python serialisation formats such as pickle and joblib can execute code when loaded from untrusted files.

## 7. Assessment Request and Response Flow

The intended request flow is:

1. The user enters text in the browser.
2. The frontend performs basic validation.
3. The frontend sends a JSON request to `POST /api/v1/assessments`.
4. FastAPI validates the request and enforces the configured input-length limit.
5. The assessment coordinator invokes the model and rule engine according to the documented execution policy.
6. Available findings are passed to the aggregator.
7. The response builder creates the structured result.
8. FastAPI returns the response.
9. The frontend displays the findings, limitations, and recommendations.

The backend must handle invalid input before model inference. It must not treat submitted content as executable instructions or automatically visit any URLs contained in the message.

## 8. Assessment Results and Failure Handling

The application must distinguish assessment outcomes from technical failures.

### 8.1 Statuses

- `COMPLETED`: The configured assessment workflow completed successfully.
- `PARTIAL`: Some expected assessment components were unavailable, but usable findings were returned.
- `INCONCLUSIVE`: The available evidence does not support a sufficiently defensible classification.
- `FAILED`: The assessment could not be completed because of a technical or operational failure.

### 8.2 Risk Categories

The proposed risk categories are:

- `LOW`
- `MEDIUM`
- `HIGH`
- `INCONCLUSIVE`

Risk categories describe the application's assessment under its documented policy. They do not establish whether a person, organisation, or message is fraudulent.

The thresholds and mapping from model and rule findings to categories remain open design decisions until data, evaluation, and testing support a defensible policy.

### 8.3 Failure Scenarios

The implementation should account for:

- Invalid or oversized input.
- Missing or incompatible model artefacts.
- Model inference errors.
- Rule-engine errors.
- Incomplete component results.
- Unexpected internal errors.
- API unavailability.

The system must not silently substitute a fabricated model result or misrepresent a technical failure as a valid low-risk assessment.

If the model is unavailable, any rule-only result must be clearly identified as partial and handled according to the documented policy. If the system cannot produce a defensible assessment, it should return an inconclusive or failed result as appropriate.

## 9. API Contract

### 9.1 Assessment Endpoint

`POST /api/v1/assessments`

Example request:

```json
{
  "text": "You have been selected for a job. Pay a registration fee today to secure your position."
}
```

The request must contain a non-empty string within the configured length limit.

### 9.2 Response Structure

The response contract should include fields for:

- Assessment identifier, if generated.
- Assessment status.
- Risk category, when available.
- Human-readable summary.
- Model findings and availability.
- Rule findings and supporting evidence.
- Recommendations.
- Limitations.

A model score is optional. If included, its meaning must be documented, and it must not be described as a probability unless calibration and interpretation justify that claim.

The final schema should be defined with Pydantic models and covered by automated tests. The frontend must follow the same contract.

### 9.3 Health and Readiness

`GET /health` indicates whether the API process is responding.

`GET /ready` indicates whether the required components for the configured assessment mode are ready.

Readiness must reflect the actual state of required components without exposing sensitive configuration, filesystem paths, or internal errors.

## 10. Security and Privacy

### 10.1 Input Handling

- Treat all submitted text as untrusted.
- Validate requests on the backend.
- Apply a documented maximum input length.
- Avoid executing, interpreting, or rendering submitted text as trusted code or HTML.
- Return safe error messages.

### 10.2 URL Handling

The MVP will not automatically fetch, crawl, or open URLs submitted in messages.

If URL analysis is considered later, it will require a separate security review and explicit controls against server-side request forgery, unsafe redirects, and access to internal network resources.

### 10.3 Data Retention

- Do not persist submitted message content by default.
- Do not create persistent assessment history in the initial MVP.
- Avoid logging complete messages or unnecessary personal information.
- Review any future data-collection feature for privacy, consent, security, and applicable legal requirements.

### 10.4 Configuration and Dependencies

- Keep secrets and local configuration out of version control.
- Document environment variables without exposing secret values.
- Use a reproducible Python environment.
- Document dependencies and review their compatibility.
- Load only trusted model artefacts.
- Keep development and production configuration appropriately separated.

## 11. Testing Strategy

Testing will cover individual components and the end-to-end workflow.

### 11.1 Unit Tests

Test:

- Input validation and normalisation.
- Data preparation functions.
- Model output structure.
- Individual rule behaviour.
- Evidence extraction.
- Risk-category mapping.
- Assessment aggregation.
- Response construction.

### 11.2 API Tests

Test:

- Valid assessment requests.
- Empty and whitespace-only input.
- Oversized input.
- Malformed JSON or invalid request structures.
- Model unavailability.
- Rule and aggregation failures where relevant.
- Health and readiness responses.
- Safe error responses.

### 11.3 Integration Tests

Verify that:

- The API invokes the expected assessment components.
- The model and rule engine produce compatible results.
- The aggregator handles conflicting and incomplete findings.
- The response follows the documented contract.
- The frontend correctly displays completed, partial, inconclusive, and failed results.

### 11.4 Model Evaluation

Evaluate the model separately from ordinary API correctness.

Use appropriate held-out data, document the split strategy, report relevant metrics, and investigate leakage and duplicate risks. Application tests alone do not establish that a model is effective at detecting scams.

## 12. Deployment Architecture

The initial deployment will use a single Python backend and a static browser frontend.

The frontend may be served separately or through an appropriate static-file hosting arrangement. The backend will load the required model artefact and serve the API.

The final hosting choice will depend on:

- Available memory and CPU resources.
- Model size and inference requirements.
- Dependency and Python-version compatibility.
- Deployment cost and operational complexity.
- HTTPS and configuration support.
- Logging and privacy requirements.

The project will document local setup, environment configuration, testing, model-artifact placement, and deployment steps.

The architecture should not introduce containers, databases, background workers, or separate inference services unless a demonstrated requirement justifies the additional complexity.

## 13. Open Architectural Decisions

The following decisions remain open:

1. Dataset selection and permitted use.
2. Final target labels and supported assessment categories.
3. Baseline model suitability and candidate comparison.
4. Model-score interpretation and whether calibration is required.
5. Rule definitions and evidence format.
6. Aggregation policy and risk-category thresholds.
7. Supported languages and known limitations.
8. Final API response schema.
9. Deployment platform and runtime resource requirements.
10. Whether future requirements justify persistent storage or separate services.

Each decision should be resolved through investigation, experiments, testing, or documented design reasoning.

## 14. Architecture Acceptance Criteria

The initial architecture is ready to guide implementation when:

- The backend is consistently defined as Python-only.
- The model, rule engine, and assessment aggregator have clear responsibilities.
- The dataset and model feasibility workflow is documented.
- The API contract is consistent with the requirements document.
- Assessment statuses and risk categories have distinct meanings.
- Privacy, input validation, and URL-handling boundaries are explicit.
- Model artefacts and dependencies can be managed reproducibly.
- Testing responsibilities are defined for components and integration.
- Deployment decisions are deferred until practical constraints are understood.

## 15. Architecture Status

This architecture is a draft. It establishes a deliberately simple starting design while leaving data-dependent and deployment-dependent decisions open.

Implementation details may change as dataset suitability, evaluation results, testing, and operational requirements become clearer. Changes must be reflected in the requirements and development documentation.