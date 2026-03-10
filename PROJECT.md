# PR Review & Jira Sync Agent

## 1. Summary

**Objective:** Engineer an event-driven automation tool that accelerates the code review process, project management tracking, and automates QA test generation.

**Description:** This project leverages enterprise Large Language Models (LLMs) to provide interactive `/ask` and `/test` agents directly within Azure DevOps Pull Requests. Developers can query the AI to explain code diffs or suggest optimizations. Furthermore, the tool supports QA by analyzing code diffs to automatically generate structured Zephyr Squad test cases in Jira. Additionally, the tool synchronizes PR lifecycle events with Jira to maintain an accurate audit trail.

**Business Value:** 
* Empowers developers with on-demand, context-aware AI assistance during code reviews.
* Reduces context-switching by keeping Q&A directly inside the Azure DevOps PR timeline.
* Eliminates manual administrative overhead through automated Jira state transitions.
* Reduces manual QA documentation by auto-generating Test Steps, Test Data, and Expected Results based on code changes.

## 2. System Architecture & Tech Stack

**Core Technology Stack:**
* **Compute Layer:** Serverless Azure Functions (Python 3.x)
* **AI Integration:** Azure OpenAI API (Enterprise tier for data privacy)
* **Version Control & CI/CD:** Azure DevOps REST API, Azure DevOps Service Hooks
* **Project Management & QA:** Jira REST API, Zephyr Squad ZAPI
* **Security & Secrets:** Azure Key Vault (for PATs and API keys)



**Event-Driven Flow:**
1. **Trigger:** An Azure DevOps Service Hook fires on `Pull Request Commented On` or PR state changes.
2. **Orchestration:** The JSON payload is routed to an Azure Function endpoint.
3. **Context Gathering:** The function uses the Azure DevOps API to fetch the code diff and comment thread context.
4. **AI Processing:** A dynamically constructed prompt (containing the user's `/ask` or `/test` query and the diff) is sent to Azure OpenAI.
5. **Action:** The parsed LLM response is posted back to the ADO comment thread. For `/test` commands, it initiates API chaining to Jira and Zephyr.
6. **Sync:** PR lifecycle changes trigger Jira API calls to update ticket statuses.

## 3. Core Features & Scope

* **The Interactive `/ask` Agent:** Listens for PR comments prefixed with `/ask`. The AI acts as a versatile pair-programmer. Examples:
  * `/ask summarize the changes in this PR for the Jira ticket.`
  * `/ask review this diff for any Databricks/Pandas performance bottlenecks.`
  * `/ask explain the logic in the newly added calculation module.`
* **The Automated `/test` Case Generator:** Listens for the `/test` command. The AI generates a structured QA matrix based on the code diff. The tool then performs API chaining:
  * *Step 1:* Uses the Jira REST API to create a new "Test" issue type.
  * *Step 2:* Uses the Zephyr Squad API to inject the AI-generated Test Steps, Test Data, and Expected Results into that new ticket, replying in ADO with a direct link.
* **Bi-Directional Jira Synchronization:** Automatically transitions Jira tickets based on PR events (e.g., moving a ticket to "In Review" when a PR is opened) and logs audit trail comments linking back to Azure DevOps.

## 4. Implementation Timeline (March 9, 2026 – May 1, 2026)

### Phase 1: Foundation & Access (Weeks 1-2)
* **Week 1 (Mar 9 - Mar 13): Project Kickoff & Provisioning**
  * Secure architectural approval and necessary access (Azure DevOps PAT, Jira/Zephyr Service Account, Azure OpenAI endpoint).
  * Initialize repository and local Python development environment.
* **Week 2 (Mar 16 - Mar 20): API Prototyping**
  * Implement authentication modules for Azure DevOps and Jira.
  * Develop logic to fetch PR diffs (added/removed lines) and extract Jira IDs from branch names using Regex.

### Phase 2: Core AI & Command Routing (Weeks 3-5)
* **Week 3 (Mar 23 - Mar 27): Prompt Engineering & LLM Integration**
  * Connect Python application to Azure OpenAI.
  * Design system prompts for the `/ask` Q&A functionality and the `/test` JSON generation functionality.
  * Test AI outputs locally with hardcoded Databricks/Python PR diffs.
* **Week 4 (Mar 30 - Apr 3): Webhook Orchestration**
  * Deploy core code to an Azure Function.
  * Configure Azure DevOps Service Hook for `Pull Request Commented On`.
  * Build the routing logic: Parse comments starting with `/ask` or `/test` and ignore all others.
* **Week 5 (Apr 6 - Apr 10): Threading & UI Polish**
  * Ensure the Azure Function posts the AI's answer as a direct reply in the originating comment thread.
  * Apply markdown formatting to the AI's output so code suggestions render cleanly in Azure DevOps.

### Phase 3: Jira/Zephyr Sync, Hardening & Handoff (Weeks 6-8)
* **Week 6 (Apr 13 - Apr 17): Jira State Machine & Zephyr Integration**
  * Implement Jira API calls for state transitions triggered by PR lifecycle events.
  * Build the two-step API chaining logic for the `/test` command (Jira Issue Creation -> Zephyr Step Injection).
* **Week 7 (Apr 20 - Apr 24): Hardening & Error Handling**
  * Implement robust error handling (API rate limits, token expirations, handling failed Zephyr step injections).
  * Add token truncation logic for oversized PR diffs exceeding LLM context windows.
  * Write `pytest` coverage for core extraction and parsing functions.
* **Week 8 (Apr 27 - May 1): Documentation & Final Handoff**
  * Create comprehensive Confluence documentation (architecture, prompt maintenance, webhook management).
  * Record a video demonstration of both the `/ask` and `/test` functionality in action.
  * Final code merge and project presentation preparation.