# Requirements: Quintic — Kalyan Agent

## Project Overview

**Product Name:** Kalyan Agent  
**Team Name:** Quintic  
**Team Leader:** Ayush Thada  
**Problem Statement:** Build an AI-powered solution that supports rural ecosystems, sustainability, or resource-efficient systems.

### Vision

Kalyan is a proactive AI agent that collapses the distance between a rural citizen's "Rights" and their "Revenue." By using minimal identifiers like Aadhaar or PAN, the system builds a live economic profile that autonomously secures government benefits and fast-tracks sellers into the ONDC marketplace.

### Core Axiom — The "Multi-Hat" Rural Household

In rural Bharat, economic identities are not siloed. A farmer in the morning is a weaver in the afternoon and a small-business owner (MSME) by evening. This "Multi-Hat" reality means a single family must navigate 50+ different government departments, portals, and compliance norms simultaneously. Quintic collapses this complexity into a single, unified agent.

---

## User Stories

### Epic 1: Voice-First Onboarding

#### Story 1.1: Voice-Based Profile Creation

**As a** rural artisan with limited digital literacy,  
**I want to** introduce myself via a voice message on WhatsApp in my local dialect,  
**so that** the agent understands my occupation, location, and basic identity without requiring me to fill out any text forms.

**Acceptance Criteria:**

- Given a user sends a voice note in any of 22 supported Indian languages (e.g., Bhojpuri, Kannada, Hindi), the system transcribes the audio using AWS Transcribe + Bhashini API with ≥90% accuracy.
- Given the transcribed text, the Bedrock Agent (Claude Sonnet 4.5) extracts structured intent fields: occupation, location, and missing identity documents.
- Given the intent is classified as "Artisan Profile" with missing ID, the agent responds in the user's dialect asking for an Aadhaar photo.
- Given the entire interaction happens over WhatsApp, no app installation or web browser is required.

#### Story 1.2: Document Upload via WhatsApp

**As a** rural user,  
**I want to** upload photos of my Aadhaar, PAN, and Bank Passbook directly via WhatsApp,  
**so that** my identity is verified without visiting a government office or cyber cafe.

**Acceptance Criteria:**

- Given a user sends a photo of an identity document, the system accepts JPEG/PNG images including low-quality, blurry photos taken on basic smartphones.
- Given the uploaded image, AWS Textract extracts structured data (Name, DOB, Address, ID Number) with field-level confidence scores.
- Given successful extraction, documents are stored in the Multi-Modal Identity Vault (S3 + DynamoDB) with encryption at rest.
- Given the user has uploaded documents once ("Scan Once, Use Anywhere"), no re-submission is required for future applications.

---

### Epic 2: Document Doctor — Pre-emptive Diagnosis

#### Story 2.1: Cross-Document Mismatch Detection

**As a** rural citizen applying for government benefits,  
**I want to** be alerted about name/DOB mismatches across my Aadhaar, PAN, and Bank passbook before I apply,  
**so that** my Direct Benefit Transfer (DBT) application is not rejected due to clerical errors.

**Acceptance Criteria:**

- Given extracted data from multiple documents, the system cross-references Name, DOB, and Address fields across Aadhaar, PAN, and Bank records.
- Given a mismatch is detected (e.g., "Ramesh Kumar" on Aadhaar vs. "Ramesh K." on Bank passbook), the user is alerted via WhatsApp with a clear visual comparison.
- Given a mismatch, the system auto-generates the appropriate government correction form (e.g., Aadhaar Update Form) pre-filled with the user's data.
- Given the mismatch detection, the workflow enters a durable WAIT_FOR_CORRECTION state (AWS Step Functions) and resumes automatically when the corrected document is uploaded.

#### Story 2.2: Document Health Report

**As a** rural user,  
**I want to** see a summary of my document verification status (verified, mismatched, missing),  
**so that** I know exactly what needs to be fixed before applying for any scheme.

**Acceptance Criteria:**

- Given all uploaded documents have been scanned, the system displays a Document Health Report showing counts: Verified ✓, Mismatch ✗, Missing ⚠.
- Given the report, each document type (Aadhaar, PAN, Bank, Caste Certificate, etc.) is listed with its individual status.
- Given missing documents are identified, the agent proactively informs the user which documents are needed for their eligible schemes.

---

### Epic 3: Welfare Benefits — Proactive Push Rights Engine

#### Story 3.1: Automatic Scheme Eligibility Matching

**As a** verified rural citizen,  
**I want to** be automatically matched to all government schemes I'm eligible for,  
**so that** I don't miss out on benefits due to information asymmetry across 5,000+ policies.

**Acceptance Criteria:**

- Given a verified user profile (occupation, location, caste, income bracket, documents), Amazon Kendra performs a Policy RAG query against an indexed database of 5,000+ central and state government schemes.
- Given matching schemes are found, the user receives a WhatsApp alert: "You are now eligible for [Scheme Name] — ₹[Amount]. Send a thumbprint to authorize application."
- Given the matching, the system ranks schemes by benefit amount and application success probability.

#### Story 3.2: Auto-Fill and One-Tap Application

**As a** rural citizen eligible for a government scheme,  
**I want to** apply for the scheme with a single thumbprint authorization,  
**so that** I don't need to manually fill out complex government application forms.

**Acceptance Criteria:**

- Given an eligible scheme, the application form is auto-filled from the Identity Vault data (Name, DOB, Address, Bank Account, Occupation).
- Given the auto-filled form, the user authorizes submission via biometric (thumbprint) confirmation on WhatsApp.
- Given successful submission, the user receives a confirmation with a Tracking ID (e.g., REF-UP-2026-4821).
- Given the application is submitted, the agent monitors status updates and pushes notifications on approval/disbursement.

#### Story 3.3: Life-Event Based Proactive Alerts

**As a** rural citizen,  
**I want to** receive automatic alerts when new schemes matching my profile are launched or when my life events trigger new eligibility,  
**so that** I never miss a benefit I'm entitled to.

**Acceptance Criteria:**

- Given a new government scheme is published or an existing scheme's criteria change, Amazon EventBridge triggers a re-evaluation of all matching user profiles.
- Given a life event (e.g., birth of child, harvest season, new state policy), the Rights Engine pushes an eligibility alert within 24 hours.
- Given the alert, the user can authorize application with a single action (thumbprint or voice confirmation).

---

### Epic 4: ONDC Marketplace — Welfare-to-Wealth Pivot

#### Story 4.1: Vernacular Voice-to-Catalog

**As a** rural artisan,  
**I want to** describe my product in my local dialect via voice message,  
**so that** a professional ONDC product listing is created without needing digital literacy or technical cataloging skills.

**Acceptance Criteria:**

- Given a voice description in any supported dialect (e.g., "Yeh Banarasi silk saree hai, pure zari ka kaam hai..."), the system transcribes and processes the input.
- Given the transcription, AWS Bedrock generates: a professional SEO-optimized product title and description, AI-benchmarked pricing based on comparable ONDC listings, and correct ONDC category mapping (e.g., Textiles › Sarees).
- Given the generated catalog, the user reviews and confirms or edits via WhatsApp before publishing.

#### Story 4.2: One-Click ONDC Publishing

**As a** rural artisan with a verified identity,  
**I want to** publish my product to the ONDC marketplace with a single confirmation,  
**so that** I can sell my craft to buyers across India without managing separate seller platforms.

**Acceptance Criteria:**

- Given a confirmed product catalog, the system generates an ONDC-compliant Product JSON and publishes to the ONDC Seller Gateway.
- Given publication, the same verified identity from the Identity Vault is used (no separate seller registration).
- Given the listing is live, the user receives a WhatsApp confirmation: "Your saree is LIVE on ONDC!"
- Given ongoing sales, the agent sends periodic updates: order count, revenue, customer ratings.

#### Story 4.3: Welfare-to-Wealth Transition

**As a** rural artisan who just received a handloom subsidy,  
**I want to** be automatically prompted to list my products on ONDC,  
**so that** the subsidy investment is monetized and I transition from "Beneficiary" to "Merchant."

**Acceptance Criteria:**

- Given a welfare subsidy application is approved (e.g., PM Hathkargha Yojana), the ONDC Seller Module is automatically triggered.
- Given the trigger, the agent asks: "Now let's monetize your craft on ONDC! Ready?" and initiates the Voice-to-Catalog flow.
- Given both welfare and commerce paths execute in parallel (Phase 3: Dual Unlock), the user sees unified status for both.

---

### Epic 5: Dashboard and Ongoing Engagement

#### Story 5.1: Unified Kalyan Dashboard

**As a** rural user,  
**I want to** see a single dashboard showing my welfare applications, ONDC store performance, and total financial summary,  
**so that** I can track all my economic activity in one place.

**Acceptance Criteria:**

- Given a user requests "dashboard" or "status", the agent responds with: Total Claimable amount vs. Received amount with a progress bar, Welfare section (active schemes with status: Processing/Received), and ONDC Store section (product listings, orders, revenue, ratings).
- Given the dashboard, all amounts are displayed in INR (₹) with Hindi/regional number formatting.
- Given the dashboard is delivered via WhatsApp, it renders as structured text messages optimized for low-bandwidth.

---

## Non-Functional Requirements

### NFR-1: Language Support

- The system must support a minimum of 22 Indian languages via Bhashini API integration.
- Voice transcription accuracy must be ≥85% for Tier-2 dialects and ≥90% for major languages.

### NFR-2: Low-Bandwidth Optimization

- The WhatsApp interface must function on 2G connections with message payloads <50KB.
- All high-compute operations (Policy RAG, Document Analysis) are processed asynchronously on AWS cloud; the user receives results via push notification.

### NFR-3: Reliability and Durability

- AWS Step Functions provides durable state management: if a server crashes or API times out, the workflow retries exactly where it left off.
- Government API response wait times of up to 72 hours must be supported without data loss.

### NFR-4: Security and Privacy

- All identity documents are encrypted at rest (AES-256) and in transit (TLS 1.3).
- Aadhaar data handling complies with UIDAI guidelines.
- Amazon VPC with NAT Gateway ensures secure outbound connections to government portals.
- AWS Secrets Manager handles all API keys and credentials.
- Amazon Cognito manages user authentication and session tokens.

### NFR-5: Scalability

- The system must scale from 10,000 to 100,000+ users without architecture changes.
- Per-user infrastructure cost must remain below ₹15/month at 10K users and ₹5/month at 100K users.

### NFR-6: Availability

- Target uptime: 99.5% for core workflows (onboarding, document scanning, scheme matching).
- SQS Dead Letter Queue captures all failed workflow executions for manual review.

---

## Glossary

| Term | Definition |
|------|-----------|
| **DBT** | Direct Benefit Transfer — government subsidy disbursement system |
| **ONDC** | Open Network for Digital Commerce — India's open e-commerce protocol |
| **Aadhaar** | India's 12-digit unique identity number linked to biometrics |
| **PAN** | Permanent Account Number — India's tax identification number |
| **MSME** | Micro, Small, and Medium Enterprise |
| **RAG** | Retrieval-Augmented Generation — AI technique combining search with LLM reasoning |
| **Bhashini** | India's National Language Translation Mission API |
| **Phygital** | Physical + Digital hybrid service delivery model |
| **Identity Vault** | Quintic's encrypted document storage system (S3 + DynamoDB) |
| **Document Doctor** | Quintic's pre-emptive document mismatch detection feature |
| **Rights Engine** | Quintic's proactive scheme eligibility matching system |