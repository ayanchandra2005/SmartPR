# PR Review & Jira Sync Agent

## 1. Executive Summary
**Objective:** Engineer an event-driven automation tool that accelerates the code review process and enforces strict governance within the Risk Technology environment. 
**Description:** Project Sentinel leverages enterprise Large Language Models (LLMs) to perform context-aware code reviews on Azure DevOps Pull Requests, answer developer queries via an interactive `/ask` command, and automatically synchronize PR statuses with Jira.
**Business Value:** * Reduces review bottlenecks by providing immediate, automated feedback.
* Enforces compliance by cross-referencing code diffs against Jira acceptance criteria.
* Eliminates manual administrative overhead through automated Jira state transitions.

## 2. System Architecture & Tech Stack

**Core Technology Stack:**
* **Compute Layer:** Serverless Azure Functions (Python 3.x)
* **AI Integration:** Azure OpenAI API (Enterprise tier for data privacy)
* **Version Control & CI/CD:** Azure DevOps REST API, Azure DevOps Service Hooks
* **Project Management:** Jira REST API
* **Security & Secrets:** Azure Key Vault (for PATs and API keys)

**Event-Driven Flow:**
1. **Trigger:** An Azure DevOps Service Hook fires on `Pull Request Created` or `Pull Request Commented On`.
2. **Orchestration:** The payload is routed to an Azure Function endpoint.
3. **Context Gathering:** The function uses the Azure DevOps API to fetch the code diff and the Jira API to fetch the linked ticket's acceptance criteria.
4. **AI Processing:** A dynamically constructed prompt (containing the diff and criteria) is sent to Azure OpenAI.
5. **Action:** The parsed LLM response is posted back to the Azure DevOps PR timeline, and if necessary, a state transition is triggered in Jira.

## 3. Core Features & Scope
* **Automated Requirements Validation:** Automatically compares PR code diffs against the linked Jira ticket's acceptance criteria and flags deviations, security flaws, or unoptimized data transformations (e.g., inefficient Spark/Pandas operations).
* **Interactive `/ask` Agent:** Listens for PR comments prefixed with `/ask`. The AI parses the developer's question, analyzes the specific code lines in the diff, and replies directly in the comment thread.
* **Bi-Directional Jira Synchronization:** Automatically transitions Jira tickets based on PR events (e.g., moving a ticket back to "In Progress" if the automated review detects critical failures) and logs an audit trail comment in Jira.

## 4. Implementation Timeline (March 9, 2026 – May 1, 2026)

### Phase 1: Foundation & Access (Weeks 1-2)
* **Week 1 (Mar 9 - Mar 13): Project Kickoff & Provisioning**
  * Secure architectural approval and necessary access (Azure DevOps PAT, Jira Service Account, Azure OpenAI endpoint).
  * Initialize repository and local Python development environment.
* **Week 2 (Mar 16 - Mar 20): API Prototyping**
  * Implement authentication modules for Azure DevOps and Jira.
  * Develop logic to fetch PR diffs (added/removed lines) and extract Jira IDs from branch names using Regex.

### Phase 2: Core AI & Automation Logic (Weeks 3-5)
* **Week 3 (Mar 23 - Mar 27): Prompt Engineering & LLM Integration**
  * Connect Python application to Azure OpenAI.
  * Design and refine system prompts to strictly evaluate code diffs against Jira acceptance criteria.
  * Test AI outputs locally with hardcoded Databricks/Python PR diffs.
* **Week 4 (Mar 30 - Apr 3): Webhook Orchestration (Automated Review)**
  * Deploy core code to an Azure Function.
  * Configure Azure DevOps Service Hook for `Pull Request Created`.
  * Validate end-to-end flow: Trigger -> Fetch Diff -> AI Review -> Post PR Comment.
* **Week 5 (Apr 6 - Apr 10): The Interactive `/ask` Command**
  * Configure Service Hook for `Pull Request Commented On`.
  * Implement routing logic: Parse comments starting with `/ask`, bundle query with PR context, and post the AI's answer in the exact thread.

### Phase 3: Integration, Hardening & Handoff (Weeks 6-8)
* **Week 6 (Apr 13 - Apr 17): Jira State Machine Integration**
  * Implement Jira API calls for state transitions.
  * Automate moving tickets from "In Review" to "In Progress" upon critical AI review failure.
  * Append audit comments to Jira tickets linking to the Azure DevOps PR.
* **Week 7 (Apr 20 - Apr 24): Hardening & Error Handling**
  * Implement robust error handling (API rate limits, token expirations).
  * Add token truncation logic for oversized PR diffs exceeding LLM context windows.
  * Write `pytest` coverage for core extraction and parsing functions.
* **Week 8 (Apr 27 - May 1): Documentation & Final Handoff**
  * Create comprehensive Confluence documentation (architecture, prompt maintenance, webhook management).
  * Record a video demonstration of the `/ask` functionality.
  * Final code merge and project presentation preparation.