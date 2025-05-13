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
