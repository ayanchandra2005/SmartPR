# PR Review & Jira Sync Agent

## 1. Summary

**Objective:** Engineer an event-driven automation tool that accelerates the code review process and streamlines project management tracking.

**Description:** This project leverages enterprise Large Language Models (LLMs) to provide an interactive `/ask` agent directly within Azure DevOps Pull Requests. Developers can query the AI to explain code diffs, suggest optimizations, or generate PR summaries. Additionally, the tool automatically synchronizes PR lifecycle events with Jira to maintain an accurate audit trail.

**Business Value:** 
* Empowers developers with on-demand, context-aware AI assistance during code reviews.
* Reduces context-switching by keeping Q&A directly inside the Azure DevOps PR timeline.
* Eliminates manual administrative overhead through automated Jira state transitions.

## 2. System Architecture & Tech Stack

**Core Technology Stack:**
* **Compute Layer:** Serverless Azure Functions (Python 3.x)
* **AI Integration:** Azure OpenAI API (Enterprise tier for data privacy)
* **Version Control & CI/CD:** Azure DevOps REST API, Azure DevOps Service Hooks
* **Project Management:** Jira REST API
* **Security & Secrets:** Azure Key Vault (for PATs and API keys)

**Event-Driven Flow:**
1. **Trigger:** An Azure DevOps Service Hook fires on `Pull Request Commented On` or PR state changes.
2. **Orchestration:** The JSON payload is routed to an Azure Function endpoint.
3. **Context Gathering:** The function uses the Azure DevOps API to fetch the code diff and the specific comment thread context.
4. **AI Processing:** A dynamically constructed prompt (containing the user's `/ask` query and the diff) is sent to Azure OpenAI.
5. **Action:** The parsed LLM response is posted back to the exact Azure DevOps PR comment thread.
6. **Sync:** PR lifecycle changes (e.g., Opened, Merged) trigger Jira API calls to update ticket statuses.

## 3. Core Features & Scope
* **The Interactive `/ask` Agent:** Listens for PR comments prefixed with `/ask`. The AI acts as a versatile pair-programmer. Examples of supported developer workflows:
  * `/ask summarize the changes in this PR for the Jira ticket.`
  * `/ask review this diff for any Databricks/Pandas performance bottlenecks.`
  * `/ask explain the logic in the newly added calculation module.`
* **Bi-Directional Jira Synchronization:** Automatically transitions Jira tickets based on PR events (e.g., moving a ticket to "In Review" when a PR is opened, or "Done" when merged) and logs an audit trail comment in Jira linking back to the Azure DevOps PR.

## 4. Implementation Timeline (March 9, 2026 – May 1, 2026)

### Phase 1: Foundation & Access (Weeks 1-2)
* **Week 1 (Mar 9 - Mar 13): Project Kickoff & Provisioning**
  * Secure architectural approval and necessary access (Azure DevOps PAT, Jira Service Account, Azure OpenAI endpoint).
  * Initialize repository and local Python development environment.
* **Week 2 (Mar 16 - Mar 20): API Prototyping**
  * Implement authentication modules for Azure DevOps and Jira.
  * Develop logic to fetch PR diffs (added/removed lines) and extract Jira IDs from branch names using Regex.

### Phase 2: Core AI & The `/ask` Command (Weeks 3-5)
* **Week 3 (Mar 23 - Mar 27): Prompt Engineering & LLM Integration**
  * Connect Python application to Azure OpenAI.
  * Design system prompts that securely package the code diff alongside the user's custom query.
  * Test AI outputs locally with hardcoded Databricks/Python PR diffs and simulated user questions.
* **Week 4 (Mar 30 - Apr 3): Webhook Orchestration**
  * Deploy core code to an Azure Function.
  * Configure Azure DevOps Service Hook for `Pull Request Commented On`.
  * Build the routing logic: Parse comments starting with `/ask` and ignore all other comments.
* **Week 5 (Apr 6 - Apr 10): Threading & UI Polish**
  * Ensure the Azure Function posts the AI's answer as a direct reply in the originating comment thread.
  * Apply markdown formatting to the AI's output so code suggestions render cleanly in Azure DevOps.

### Phase 3: Jira Sync, Hardening & Handoff (Weeks 6-8)
* **Week 6 (Apr 13 - Apr 17): Jira State Machine Integration**
  * Implement Jira API calls for state transitions triggered by PR lifecycle events.
  * Append audit comments to Jira tickets linking to the Azure DevOps PR.
* **Week 7 (Apr 20 - Apr 24): Hardening & Error Handling**
  * Implement robust error handling (API rate limits, token expirations).
  * Add token truncation logic for oversized PR diffs exceeding LLM context windows.
  * Write `pytest` coverage for core extraction and parsing functions.
* **Week 8 (Apr 27 - May 1): Documentation & Final Handoff**
  * Create comprehensive Confluence documentation (architecture, prompt maintenance, webhook management).
  * Record a video demonstration of the `/ask` functionality in action.
  * Final code merge and project presentation preparation.