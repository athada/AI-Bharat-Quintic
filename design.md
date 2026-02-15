# Quintic: AI for Bharat - System Design Document

## 1. System Overview
The Quintic system is architected as a **Serverless, Event-Driven AI Agent**. It leverages a layered architecture to separate edge access, orchestration, cognitive reasoning, and external integration. This design prioritizes "Durable Execution" to handle the unreliability of rural connectivity and legacy government portals.

---

## 2. High-Level Architecture
The system is divided into five logical layers:

### Layer 1: Edge & Access
* **Entry Point:** Amazon Pinpoint acts as the WhatsApp Gateway for user interaction.
* **Security:** AWS WAF (Web Application Firewall) and Amazon CloudFront protect the entry points, while Amazon Cognito handles user authentication and identity pools.

### Layer 2: Durable Orchestration (The "Spine")
* **Workflow Engine:** AWS Step Functions is the core orchestrator. It manages state transitions, retries, and the "Wait-for-Callback" logic required for asynchronous document corrections.
* **State Persistence:** Amazon DynamoDB stores session state, ensuring users can resume workflows after connectivity drops.
* **Event Bus:** Amazon EventBridge triggers workflows based on external events (e.g., new policy release).

### Layer 3: AI & Reasoning (The "Brain")
* **Speech Processing:** AWS Transcribe converts vernacular voice notes into text.
* **Document Vision:** AWS Textract performs OCR on Indian identity documents (Aadhaar/PAN).
* **Cognitive Engine:** Amazon Bedrock (Claude 3.5 Sonnet) handles reasoning, intent analysis, and data cross-verification.
* **Knowledge Base:** Amazon Kendra provides RAG capabilities by indexing government scheme PDFs for accurate eligibility checks.

### Layer 4: Integration (The "Hands")
* **Compute:** AWS Lambda executes specific "Action Groups" (e.g., fetching a bank status, formatting an ONDC JSON).
* **Network Security:** All Lambda functions operate within a private Amazon VPC. Outbound traffic is routed through a NAT Gateway to ensure secure communication with external APIs.
* **Credential Management:** AWS Secrets Manager rotates and stores API keys for Bhashini and ONDC.

### Layer 5: External Ecosystem
* **Targets:** The system integrates with Bhashini (Translation), Govt Portals (myScheme/DBT), ONDC Seller Gateway, and NPCI/Bank APIs.

---

## 3. Data Flow Design

### 3.1 Onboarding & Document Doctor Flow
1.  **Ingest:** User sends voice/image -> WhatsApp Gateway -> Step Functions Trigger.
2.  **Process:** Step Functions invokes Transcribe (for voice) and Textract (for images).
3.  **Reason:** Extracted data is sent to Bedrock. The agent compares fields (Name, DOB) across docs.
4.  **Decision:**
    * *If Mismatch:* Workflow enters a `WAIT` state, triggers a Lambda to generate a correction form, and alerts the user.
    * *If Verified:* Workflow proceeds to parallel execution branches.

### 3.2 Parallel Execution Flow (Rights & Revenue)
* **Branch A (Welfare):** Bedrock queries Kendra for schemes matching the verified profile -> Auto-fills application -> Submits to Govt Portal.
* **Branch B (Commerce):** Bedrock generates product metadata from voice input -> Formats JSON -> Publishes to ONDC Gateway.

---

## 4. Technology Stack

| Component | Technology | Rationale |
| :--- | :--- | :--- |
| **Orchestration** | AWS Step Functions | Handles long-running workflows and retries without server maintenance. |
| **LLM / AI** | Amazon Bedrock (Claude 3.5 Sonnet) | Industry standard for complex reasoning and multi-lingual translation. |
| **OCR** | Amazon Textract | Specialized for handling noisy/blurry images of Indian IDs. |
| **Search / RAG** | Amazon Kendra | Enterprise-grade indexing of PDF policies to prevent hallucinations. |
| **Database** | Amazon DynamoDB | Serverless, millisecond latency for session state storage. |
| **Interface** | WhatsApp API (via Pinpoint) | Ubiquitous interface for rural India; low bandwidth requirement. |

---

## 5. Security Architecture
* **Zero Trust Network:** No direct internet access for compute resources. All egress is controlled via NAT Gateways.
* **Data Protection:** Session data in DynamoDB is encrypted at rest. PII processed in memory (Lambda) is transient and not persistently logged.
* **Compliance:** Architecture is deployed in the AWS Mumbai Region (ap-south-1) to comply with Indian data residency laws.
