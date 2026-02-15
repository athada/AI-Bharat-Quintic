# Quintic: Kalyan Agent AI for Bharat - System Requirements Document

## 1. Introduction
**Quintic** is a proactive AI agent designed to bridge the gap between rural citizens, government welfare schemes (Rights), and digital marketplaces (Revenue). The system operates as a unified "Economic Concierge," utilizing voice-first interactions and vernacular language support to assist users with limited digital literacy.

### 1.1 Purpose
The purpose of this system is to:
* Eliminate "Discovery Fatigue" by proactively pushing relevant government schemes to users.
* Reduce rejection rates in Direct Benefit Transfers (DBT) caused by clerical errors in identity documents.
* Enable rural artisans and farmers to list products on the ONDC network without technical barriers.

### 1.2 Scope
The system encompasses a WhatsApp-based user interface, an AWS-powered backend for document verification and reasoning, and integrations with external ecosystems including Government Portals (myScheme), ONDC Seller Gateway, and Banking APIs.

---

## 2. User Personas
* **Rural Citizen (User):** A "Multi-hat" individual (Farmer/Artisan/Homemaker) with low digital literacy, interacting primarily via voice in local dialects.
* **System Administrator:** Manages the infrastructure, monitors system health via CloudFront/WAF, and handles high-level overrides.

---

## 3. Functional Requirements

### 3.1 Onboarding & Identity Verification
* **FR-01 Voice Registration:** The system must accept voice notes in vernacular languages (e.g., Bhojpuri, Kannada) to initiate user registration.
* **FR-02 Document Upload:** Users must be able to upload images of identity documents (Aadhaar, PAN, Bank Passbooks) via WhatsApp.
* **FR-03 Document Diagnosis (The "Document Doctor"):**
    * The system must extract structured data from uploaded documents using OCR.
    * The system must cross-reference data across documents to identify clerical mismatches (e.g., Name spelling differences).
    * **Critical Check:** If a mismatch is detected, the system must auto-generate a correction form and alert the user *before* proceeding.

### 3.2 Welfare (G2C) Capabilities
* **FR-04 Eligibility Check:** The system must match user profiles against a database of 5,000+ government schemes using RAG (Retrieval-Augmented Generation).
* **FR-05 Auto-Application:** The system must autonomously fill application forms for eligible schemes once authorized by the user.
* **FR-06 Proactive Alerts:** The system must monitor policy updates and "push" notifications to users when they become eligible for new benefits.

### 3.3 Commerce (B2B) Capabilities
* **FR-07 Voice-to-Catalog:** The system must allow users to describe products via voice and automatically generate professional SEO descriptions and pricing benchmarks.
* **FR-08 ONDC Listing:** The system must publish product catalogs to the ONDC network using the verified identity established during onboarding.

---

## 4. Non-Functional Requirements

### 4.1 Reliability & Availability
* **NFR-01 Durable Execution:** The system must handle long-running workflows (up to days) without data loss, utilizing orchestration to manage state during government API downtimes.
* **NFR-02 Fault Tolerance:** The system must implement retry mechanisms and Dead Letter Queues (DLQ) for failed transactions.

### 4.2 Security & Privacy
* **NFR-03 Data Sovereignty:** All PII (Personally Identifiable Information) processing must occur within a secure VPC environment.
* **NFR-04 Secure Transmission:** Data in transit must be encrypted, and external API calls must be routed through a NAT Gateway.
* **NFR-05 Access Control:** API keys must be managed via a secrets manager, not hardcoded.

### 4.3 Scalability & Performance
* **NFR-06 Scalability:** The architecture must be serverless to support scaling from 100 to 50,000+ users without manual provisioning.
* **NFR-07 Vernacular Support:** The system must support at least 22 regional Indian languages via translation APIs.
* **NFR-08 Low Bandwidth Optimization:** The user interface must be optimized for "Bharat" connectivity (2G/3G) using lightweight messaging protocols (WhatsApp).
