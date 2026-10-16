# ScamShield SA — Software Requirements Specification

**Document version:** 0.3

**Status:** Draft

**Project:** ScamShield SA

## 1. Purpose

This document defines the functional and non-functional requirements for ScamShield SA, a web-based tool that helps users assess suspicious messages and job advertisements.

The application will combine a machine learning text classifier with a transparent rule-based assessment engine. It will provide structured findings and practical safety recommendations without claiming to prove that content is fraudulent or legitimate.

The backend will be implemented entirely in Python. The initial architecture will use one FastAPI application containing the API, model-inference pipeline, rule engine, and assessment aggregation logic.

These requirements will be refined as dataset suitability, model feasibility, and implementation constraints are validated.

## 2. Product Scope

### 2.1 Included

- Browser-based submission of suspicious text.
- Server-side input validation.
- Dataset investigation and documented data preparation.
- Machine learning model training and evaluation.
- Reproducible model inference.
- Rule-based warning-sign detection.
- Documented aggregation of model and rule findings.
- Structured risk assessments and evidence.
- Practical safety recommendations.
- Error handling, privacy-conscious processing, and automated testing.
- Deployment and technical documentation.

### 2.2 Excluded from the Initial Release

- User registration and authentication.
- Persistent assessment history.
- Automatic URL fetching, crawling, or website inspection.
- Screenshot analysis and OCR.
- Native mobile applications and browser extensions.
- Automatic reporting to third parties.
- Definitive fraud verification.
- Claims of reliable detection across languages or message types that have not been evaluated.

## 3. Users and Operating Environment

### 3.1 Intended Users

The initial users are members of the public in South Africa who want help assessing suspicious messages, including job advertisements, SMS messages, emails, and instant messages.

### 3.2 Operating Environment

- The frontend will run in a modern web browser.
- The frontend will communicate with a Python FastAPI backend over HTTP or HTTPS, depending on the environment.
- The backend will run in a supported Python environment.
- The model artefact and preprocessing pipeline will be loaded by the backend.
- The initial system will not require a database.
- Development and deployment instructions will document the required dependencies and configuration.

## 4. Functional Requirements

Requirements are assigned identifiers so that implementation and testing can be tracked.

### 4.1 Data and Model Feasibility

**FR-001 — Dataset provenance**

The project shall document the source, licence or permitted use, collection context where known, labelling method, and limitations of each dataset used.

**FR-002 — Dataset suitability**

The project shall investigate missing data, duplicate records, label quality, class distribution, potential leakage, and the relevance of the dataset to the intended use case before selecting it for training.

**FR-003 — Data preparation**

The project shall implement and document the data-preparation steps required for the selected dataset. Transformations used for model training shall be applied consistently during inference.

**FR-004 — Reproducible evaluation**

The project shall separate training and evaluation data appropriately and use a documented evaluation strategy. Where appropriate, splitting shall account for class imbalance, duplicates, and potential overlap between related messages.

**FR-005 — Baseline model**

The project shall train and evaluate a baseline text-classification model if the available data supports a meaningful experiment. TF-IDF with Logistic Regression is the initial candidate.

**FR-006 — Model comparison**

The project should compare the baseline with at least one reasonable alternative, such as a Linear Support Vector Machine, when the dataset and evaluation design permit a fair comparison.

**FR-007 — Model evaluation**

The project shall report suitable evaluation metrics, including precision, recall, F1-score, and a confusion matrix where applicable. Results shall be interpreted in the context of class balance and the consequences of false positives and false negatives.

**FR-008 — Model selection**

The selected model shall be justified using evaluation results, practical constraints, and documented limitations. The project shall not claim that the model is reliable solely because training completed successfully.

**FR-009 — Reproducible inference**

The application shall use the same fitted preprocessing and feature-transformation pipeline used during model training. Model artefacts and their expected versions or dependencies shall be documented.

### 4.2 Text Assessment

**FR-010 — Text submission**

The frontend shall allow a user to submit text for assessment.

**FR-011 — Input validation**

The backend shall validate submitted text, reject empty or whitespace-only input, and enforce a documented maximum input length.

**FR-012 — Rule-based assessment**

The backend shall evaluate submitted text against a documented set of explicit warning-sign rules.

**FR-013 — Evidence generation**

Each triggered rule shall return a human-readable explanation and, where practical, a relevant excerpt from the submitted text. The system shall not invent evidence that is absent from the input.

**FR-014 — Machine learning assessment**

When a valid model is available and operational, the backend shall use its trained preprocessing and classification pipeline to assess the submitted text.

If the model cannot be loaded or used, the system shall follow a documented failure policy rather than silently fabricate a model result.

**FR-015 — Assessment aggregation**

The backend shall combine available model and rule findings according to a documented and tested aggregation policy.

The policy shall define how conflicting findings, missing model results, and rule-only results are handled. It shall not imply that multiple weak signals automatically establish fraud.

**FR-016 — Risk classification**

Where the available evidence supports a classification, the system shall return one of the following categories:

- `LOW`
- `MEDIUM`
- `HIGH`
- `INCONCLUSIVE`

The category shall represent the system's assessment of the submitted content, not a verified determination of fraud or legitimacy.

Thresholds and aggregation rules shall be documented and evaluated before being treated as operational.

**FR-017 — Assessment status**

The API shall distinguish between completed, partial, inconclusive, and failed assessments.

The response shall make it possible for the frontend to distinguish a valid assessment from an unavailable model, an incomplete assessment, or a technical error.

**FR-018 — Recommended next steps**

The system shall provide relevant safety recommendations, such as independently verifying an organisation through official contact channels, avoiding upfront payments to unverified recruiters, and not sharing sensitive information with unverified parties.

Recommendations shall be phrased as guidance rather than as proof that the submitted content is fraudulent.

### 4.3 API and Frontend

**FR-019 — Assessment API**

The backend shall expose a versioned assessment endpoint:

`POST /api/v1/assessments`

The endpoint shall accept a documented JSON request and return a structured JSON response.

**FR-020 — Health endpoint**

The backend shall expose `GET /health` to indicate whether the API process is responding.

**FR-021 — Readiness endpoint**

The backend shall expose `GET /ready` to report whether the required assessment components are ready for use. Readiness checks shall not expose sensitive configuration or internal details.

**FR-022 — Frontend results**

The frontend shall display the assessment status, risk category where available, relevant evidence, limitations, and recommended next steps.

**FR-023 — Loading and error states**

The frontend shall communicate when an assessment is in progress and display understandable messages when a request fails or an assessment cannot be completed.

**FR-024 — Response validation**

The backend shall return documented response fields and appropriate HTTP status codes. The frontend shall handle valid responses and expected error responses without assuming that every request produces a completed assessment.

### 4.4 Privacy and Security

**FR-025 — No default content retention**

The initial application shall not persist submitted message content or assessment history by default.

**FR-026 — Privacy-conscious logging**

Logs shall not contain complete submitted messages, credentials, or unnecessary personal information. Operational logging shall be limited to what is needed for troubleshooting and monitoring.

**FR-027 — Safe input handling**

The backend shall treat submitted text as untrusted input. It shall not execute submitted content, automatically visit submitted URLs, or treat instructions contained in a message as trusted system instructions.

**FR-028 — Configuration management**

Secrets, credentials, and environment-specific configuration shall not be committed to the repository. Required configuration shall be documented without exposing secret values.

## 5. API Contract

The following is the proposed initial contract. Exact optional fields and error details may be refined during implementation, but the documented contract must remain consistent across the frontend, backend, and tests.

### 5.1 Assessment Request

Endpoint: `POST /api/v1/assessments`

Content type: `application/json`

Example request:

    {
      "text": "You have been selected for a job. Pay a registration fee today to secure your position."
    }

The `text` field is required and must contain non-whitespace text within the configured length limit.

### 5.2 Successful Assessment Response

Example response structure:

    {
      "assessment_id": "example-id",
      "status": "COMPLETED",
      "risk_level": "HIGH",
      "summary": "Several warning signs were identified.",
      "model_assessment": {
        "available": true,
        "label": "suspicious",
        "score": null
      },
      "rule_findings": [
        {
          "rule_id": "upfront_payment",
          "description": "The message requests an upfront payment.",
          "evidence": "Pay a registration fee today"
        }
      ],
      "recommendations": [
        "Verify the vacancy through the organisation's official website or contact details obtained independently.",
        "Do not pay a recruitment or registration fee until the organisation and request have been independently verified."
      ],
      "limitations": [
        "This assessment is not proof of fraud or legitimacy."
      ]
    }

This example illustrates the intended structure, not an actual model result. A real response must reflect the results actually produced by the model and rules.

The example's `score` is `null` because the score's semantics have not yet been defined. If a score is introduced, its meaning must be documented. An uncalibrated model score shall not be described as a probability of fraud.

An assessment identifier may be generated for request correlation without storing the submitted text or creating persistent user history.

### 5.3 Assessment Statuses

The API shall use documented status values with the following intended meanings:

- `COMPLETED`: The configured assessment workflow completed successfully.
- `PARTIAL`: Some assessment components produced results, but one or more expected components were unavailable.
- `INCONCLUSIVE`: The available evidence does not support a sufficiently defensible risk classification.
- `FAILED`: The assessment could not be completed because of a technical or operational failure.

The exact combination of HTTP status codes and body-level assessment statuses shall be documented during API implementation.

### 5.4 Input and Error Responses

The backend shall return a validation error for missing, empty, whitespace-only, or oversized text.

Unexpected server errors shall return a safe error response without exposing stack traces, filesystem paths, secrets, or internal implementation details.

## 6. Non-Functional Requirements

### 6.1 Security

**NFR-001:** All submitted text shall be treated as untrusted input.

**NFR-002:** The backend shall validate requests independently of frontend validation.

**NFR-003:** Error messages shall not expose secrets, stack traces, or unnecessary internal details.

**NFR-004:** The application shall not automatically fetch submitted URLs in the MVP.

### 6.2 Privacy

**NFR-005:** Submitted text shall not be stored persistently by default.

**NFR-006:** Logs shall avoid unnecessary personal information and complete message contents.

**NFR-007:** Dataset use and any later collection or retention of user information shall be reviewed against applicable privacy and licensing requirements.

### 6.3 Reliability

**NFR-008:** The API shall handle invalid input and expected component failures predictably.

**NFR-009:** The system shall not report fabricated model outputs when inference fails.

**NFR-010:** The system shall represent partial and inconclusive outcomes explicitly.

### 6.4 Maintainability

**NFR-011:** The implementation shall use clear modules and responsibilities for API handling, model inference, rules, and assessment aggregation.

**NFR-012:** Dependencies and environment setup shall be documented.

**NFR-013:** Significant architectural and model-selection decisions shall be recorded.

### 6.5 Testability

**NFR-014:** Data-processing functions, model inference, rule logic, aggregation, and API endpoints shall be testable independently where practical.

**NFR-015:** Automated tests shall cover important success, validation, failure, and disagreement scenarios.

**NFR-016:** Model evaluation shall be reproducible using documented data splits and configuration.

### 6.6 Usability and Transparency

**NFR-017:** Results shall use understandable language suitable for non-specialist users.

**NFR-018:** The interface shall distinguish warning signs from confirmed facts.

**NFR-019:** The system shall communicate uncertainty and avoid absolute claims about fraud or legitimacy.

**NFR-020:** The interface shall remain usable on common desktop and mobile browser sizes.

### 6.7 Performance

**NFR-021:** The application should return assessments promptly for inputs within the configured length limit under the intended deployment conditions.

**NFR-022:** Performance shall be measured in the target environment before numerical response-time targets are committed.

### 6.8 Model Quality and Reproducibility

**NFR-023:** Model performance claims shall be based on appropriate held-out evaluation data.

**NFR-024:** The evaluation shall consider class imbalance, leakage, duplicates, and the impact of false positives and false negatives.

**NFR-025:** Model selection shall be justified by evidence rather than by a predetermined target score.

**NFR-026:** The fitted preprocessing pipeline and model shall be versioned and loaded consistently during inference.

**NFR-027:** The system shall document important data limitations and avoid claiming support for untested languages or message categories.

## 7. Testing Requirements

The project shall include the following categories of testing:

1. **Data tests:** Validate expected fields, labels, preprocessing behaviour, and duplicate-handling assumptions.
2. **Model tests:** Verify that the trained pipeline loads correctly and returns outputs in the documented format.
3. **Rule tests:** Confirm that rules trigger for intended patterns and do not claim evidence that is absent.
4. **Aggregation tests:** Cover conflicting findings, unavailable model results, partial assessments, and inconclusive cases.
5. **API tests:** Verify valid requests, invalid input, response structure, error handling, health, and readiness.
6. **Integration tests:** Verify that the frontend and backend use the same API contract and that the configured assessment workflow behaves as expected.
7. **Privacy and security tests:** Check input limits, safe error responses, and the absence of submitted message contents in application logs.
8. **Model evaluation:** Report performance on held-out data using suitable metrics and document known limitations.

Testing shall not rely solely on a few manually selected examples. The evaluation dataset and test fixtures shall be kept separate from training data where appropriate.

## 8. Acceptance Criteria

The initial release can be considered ready for demonstration when:

- Dataset suitability has been investigated and documented.
- The baseline model has been evaluated, or a documented feasibility limitation explains why a suitable model could not be established.
- Model-selection decisions and relevant limitations are recorded.
- The API accepts valid text and rejects invalid or oversized input.
- The configured assessment workflow produces a structured result.
- Rule findings provide evidence grounded in the submitted text.
- The assessment aggregation policy is documented and tested.
- Model failures and incomplete assessments are represented honestly.
- The frontend displays results and error states correctly.
- Automated tests cover critical application behaviour.
- Submitted content is not persisted by default.
- The application does not automatically fetch submitted URLs.
- Local setup, testing, and deployment instructions are documented.
- No unsupported claims are made about accuracy, language coverage, or fraud verification.

## 9. Assumptions and Open Decisions

The following decisions remain open until supported by implementation evidence:

- Which dataset or combination of datasets is suitable for the intended South African use case.
- Whether the available labels support a binary classifier or require a different formulation.
- Which model performs best under a defensible evaluation strategy.
- Whether model scores need calibration and how they should be interpreted.
- How model and rule findings should be combined into risk categories.
- Which thresholds are appropriate for each risk category.
- Which languages can be supported reliably.
- Which deployment environment best fits the application's resource requirements.
- Whether future requirements justify persistence, authentication, or additional assessment types.

These decisions shall be resolved through data investigation, experimentation, testing, and documented design choices rather than unsupported assumptions.

## 10. Requirements Governance

Requirements shall be updated when the project scope, data feasibility, evaluation results, security considerations, or implementation constraints materially change.

Changes should retain requirement identifiers where practical so that tests and implementation references remain traceable.

The project charter, requirements, architecture, and development workflow must remain consistent. Requirements that cannot be met should be documented with a reason and a revised plan rather than silently omitted.