# Automating Veterinary Claims Processing at Barkibu

## 🐾 Introduction

At Barkibu, we're helping pet parents protect their furry companions through smart insurance. A crucial step in this journey is the veterinary claims process—where users submit documents for reimbursement after a vet visit. While this process works today, it relies heavily on manual effort from our veterinary team.

This creates a unique opportunity: can we automate parts of this process without losing the human touch where it matters?

# 1. Problem Analysis

## Understanding the Problem

The current system requires users to upload:
* An invoice detailing treatment costs
* A veterinary report describing diagnosis and treatment
* In some cases, the pet’s medical history

Then, our team decides:

* Whether the treatment is covered
* If any condition is pre-existing
* If waiting periods apply
* And finally, what amount is reimbursable
* This is complex. But parts of it follow patterns and rules—making it a great candidate for thoughtful automation.

## Key Areas to Solve
1. Extracting data from documents: These come in all shapes and formats.
2. Evaluating claims automatically: Based on policies, timelines, and medical details.
3. Improving user experience: Faster answers, better guidance.
4. Supporting the vet team: Let automation handle the easy stuff; give experts tools for complex cases.

## Constraints & Risks

* Document variety: Every clinic formats things differently.
* Accuracy is critical: Mistakes in coverage could cost money—or trust.
* Medical complexity: Pre-existing conditions can be subtle or unclear.
* Data sensitivity: We're handling private health data.

# Problem Decomposition

To tackle the automation of the veterinary claims process, we can break down the challenge into smaller, manageable problems. These subproblems can then be prioritized based on impact and feasibility.

1. Document Ingestion and Processing (High Impact / Medium Feasibility)
   
* Enable users to upload PDFs, images, or scans of invoices, reports, and medical histories.
* Use OCR (Optical Character Recognition) to convert documents to text.
* Classify the type of document automatically (invoice, vet report, history).
* Extract structured data: invoice lines, diagnosis codes, treatments, dates, etc.
  

2. Rules Engine for Claims Evaluation (High Impact / High Feasibility)

* Define and encode the policy rules in a declarative format (e.g. YAML, JSON).
* Evaluate:
  * Coverage applicability
  * Waiting period constraints
  * Pre-existing conditions
* Output a decision or route to manual review if conditions are not met.

3. Medical History Linking (Medium Impact / Medium Feasibility)
   
* Store historical claims and health data per pet.
* Use NLP to match new diagnoses with previously known conditions.
* Flag possible pre-existing conditions.

4. Decision Aggregator and Risk Layer (High Impact / Medium Feasibility)

* Aggregate the output from:
  * Document processor
  * Rules engine
  * History checker
* Determine confidence score.
* Approve automatically if score is high, or escalate to human vet otherwise.

5. Human-in-the-Loop Tools (Medium Impact / High Feasibility)
   
* Dashboard for veterinarians to:
  * Review flagged claims
  * Override system decisions
  * Provide feedback to improve models/rules

6. User Feedback Layer (Medium Impact / High Feasibility)
   
* Show claim status in real time
* Inform the user about next steps, missing documents, or decisions
* Collect satisfaction feedback

7. Monitoring and Learning (Medium Impact / Medium Feasibility)
* Track metrics like:
  * Accuracy of auto-approvals
  * Percentage of claims processed automatically
  * Time to resolution
* Feed errors or overrides back into system to improve logic or ML models

# System Design
The architecture focuses on modularity, auditability, and ease of human oversight. The system is composed of several services and components that interact to process a claim end-to-end.

## Components Overview
1. Claims Intake API

* Receives user-submitted documents from the mobile app.
* Associates them with the correct policy and pet profile.
* Stores raw files for audit and reprocessing.

2. Document Processing Pipeline

* Runs OCR (e.g. Tesseract, Google Vision API) on each file.
* Classifies the document type (invoice, vet report, history).
* Uses NLP to extract relevant entities:
   * Symptoms, diagnosis, treatments
   * Cost breakdowns, dates, vet info

3. Structured Data Store

* Holds extracted data in a normalized, queryable format (e.g. PostgreSQL or Elasticsearch).
* Linked to original document versions for traceability.

4. Policy Rules Engine

* Encodes coverage logic, waiting periods, exclusions, etc.
* Evaluates structured claims data against the pet’s policy.
* Returns a decision: approved, partially approved, rejected, or escalate.

5. Medical History Analyzer

* Uses historical claims and vet reports to flag potential pre-existing conditions.
* Connects treatments and diagnoses across time using fuzzy matching and NLP.
* May trigger escalation to human vet for review.

6. Decision Orchestrator

* Aggregates all outputs and determines:
   * Whether the claim can be automatically approved
   * The amount to reimburse
   * Whether human review is needed

7. Veterinary Review Dashboard

* Provides context-rich interface for vets to review complex cases.
* Includes original documents, extracted data, history, and suggested decision.
* Allows override and feedback to improve future performance.

8. Notification and Payments Layer

* Notifies users via the app/email of claim decisions.
* Triggers reimbursements if approved.
* Provides transparency through claim timelines and breakdowns.

## Example Workflow

```
1. Pet owner submits invoice + vet report via mobile app
2. Claims API stores documents and triggers processing
3. Document Pipeline extracts data and classifies documents
4. Structured data is passed to:
    - Policy Rules Engine → checks coverage, waiting period
    - Medical History Analyzer → flags known conditions
5. Decision Orchestrator makes final judgment:
    - Auto-approves → goes to Payment
    - Needs review → goes to Vet Dashboard
6. User gets notified of outcome
```

## Pros and Cons of the Approach
### ✅ Pros
 
1. Scalable: Once deployed, the system can handle increasing claim volumes without growing the vet team proportionally.
2. Consistent: Automated rules ensure that similar cases receive the same decisions — reducing human error and bias.
3. Fast for Users: Many claims can be approved in minutes instead of days, improving customer satisfaction.
4. Feedback Loop: The vet review dashboard enables learning from edge cases, improving automation over time.
5. Auditability: Structured storage of decisions and extracted data allows for easy tracing and compliance auditing.
6. Modular by Design: Each component can evolve independently (e.g. swapping out the OCR tool or adding new policy rules).
7. Future-proof: It lays the groundwork for ML-based personalization, fraud detection, and preventive care insights.

### ❌ Cons
1. Initial Complexity: Requires significant engineering and data science investment up front to reach acceptable accuracy.
2. Document Variability: Vet invoices and reports are unstandardized, making extraction fragile at first.
3. Cold Start Problem: NLP models and rule engines require training data — which may be limited in early stages.
4. Edge Case Handling: Some cases will always require human review due to medical complexity or unclear data.
5. Integration Cost: Need to integrate with internal tools, insurance systems, and payment providers — can slow down delivery.

## Alternatives Considered

|Aternative | Why we didn't choose it|
|:-----------------------------------------------------|:------------------------------------------------------------------------------------------------------|
| Manual review with better tools	                   | Easier to implement but doesn’t scale, and still bottlenecks on human capacity.                       |
| Rules-only system without data extraction	          | Requires users to input data manually (error-prone and a poor UX).                                    |
| Outsourcing document parsing to a third-party API	 | Faster start, but limited flexibility and domain-specific tuning for veterinary cases.                |
| Machine learning only from the start	                | Promising long-term, but high risk without labeled data and transparency. Hard to debug in edge cases.|


## Implementation Plan
To manage complexity and deliver value early, we’ll use a phased, iterative approach. Each phase includes usable outputs while laying the foundation for the next.

### Phase 1: Foundation (Months 1–2)
Goal: Build the core infrastructure for document processing and rules-based automation.

### Deliverables
* Document Intake Pipeline
   * Upload UI + backend API
   * Store uploaded files securely (e.g., S3 with encryption)

* Basic OCR and Data Extraction
   * Use tools like Tesseract, Google Vision, or AWS Textract
   * Target common invoice fields (date, amount, vet name, treatments)

* Structured Data Schema
   * Define claim, invoice, report, and treatment line models
   * Create storage (e.g., PostgreSQL, Firestore)

* Policy Rules Engine MVP
   * Encode basic coverage rules
   * Apply deductible and co-pay logic

### Phase 2: Core Automation (Months 3–4)
Goal: Automate straightforward claims and introduce human review workflows.

### Deliverables

* Claims Orchestration Engine
 * Receives structured data, calls rules engine, returns decision

* Human Review Routing
   * Automatically flag claims needing vet attention (e.g., missing info, low confidence)

* Veterinary Review Dashboard MVP
   * Claim queue + interface to approve/reject with comments
   * Feed review outcomes back to system

* End-to-End Workflow (Basic)
   * Upload → extraction → rules → decision or review → response to user

### Phase 3: Intelligence + Analysis (Months 5–6)
Goal: Enhance accuracy with historical context and smarter classification.

### Deliverables

* Medical History Aggregator
   * Associate historical documents per pet
   * Extract relevant diagnosis/treatment patterns

* Pre-existing Condition Detection
   * Compare current claims against medical history
   * Flag potential conflicts with coverage

* Confidence Scoring for Extraction
   * Score extracted fields
   * Route low-confidence data for manual review

* Partial Line-Item Rejection
   * Approve some invoice lines and reject others based on policy logic

### Phase 4: Optimization + Scaling (Months 7–8)
Goal: Improve performance, reduce manual reviews, and integrate with external systems.

### Deliverables

* Model Tuning with Feedback Data
   * Use vet feedback to improve NLP and rules accuracy

* Notifications + Status Tracking
   * Let users track claim status and provide missing documents

* Payment Integration
   * Connect to payment API for reimbursements (e.g. Stripe, SEPA)

* Analytics Dashboard (Internal)
   * Monitor automation rate, claim volume, error rate, review load
 
## 🧑‍💻 Frontend Developer's Role in the Solution
To align with the overall architecture and phased delivery, here’s how the frontend development could be planned and executed:

### Phase 1: Foundation (Months 1–3)

*Focus: MVP claims submission experience*

* Build a multi-step form to upload invoice and vet report documents
* Integrate with the initial API endpoints (document upload & basic OCR results)
* Add inline file validation and UX feedback
* Design and implement basic UI components using a design system or component library
* Show a claim confirmation screen with estimated review time

### Phase 2: Core Automation (Months 4–6)
*Focus: Visibility and interaction with automated decisions*

* Introduce claim status tracker with real-time updates (polling or WebSocket)
* Visualize structured data extracted from documents for user review
* Display automatic decision results when available (e.g., covered amounts)
* Begin work on vet review dashboard with table layout for flagged cases

### Phase 3: Advanced Features (Months 7–9)
*Focus: Advanced review tools and personalization*

* Complete vet dashboard with document viewer, comments, and override actions
* Enable manual claim adjustment interface
* Display insights from medical history analysis when available
* Show coverage breakdown and waiting period explanations to the user

### Phase 4: Refinement and UX Enhancements (Months 10–12)
*Focus: Speed, accessibility, and polish*

* Performance improvements (lazy loading, code splitting)
* Accessibility (ARIA roles, keyboard nav, screen reader support)
* Add in-app notifications and email updates
* Collect user feedback on the UI and iterate
