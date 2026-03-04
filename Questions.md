# Meeting Agenda: QA & Automation Project Brainstorming

**Date:** March 4, 2026

**Topic:** Aligning my Co-op project with QA and testing priorities.

## 1. Understanding Current QA Workflows
* What are the most time-consuming or repetitive parts of the QA and testing lifecycle right now?
* Where do you usually see the biggest disconnect between the code developers submit and the original Jira acceptance criteria?
* When PRs reach the QA stage, what is the most common reason they get kicked back to the developers?

## 2. Validating PR Agent
Goal is to catch issues earlier in the pipeline and reduce manual overhead.

**The Concept:** An Azure DevOps integration that listens for the `/ask` command in PR comments. It allows developers or QA to query an LLM about the specific code diffs (e.g., `/ask explain the logic in this module` or `/ask review this against Jira ticket PROJ-123`). It would also automatically sync PR states (Opened, Merged) with Jira.

**Feedback:**
* Do you think QA engineers would find value in an `/ask` command to quickly understand how a code change might impact existing test coverage?
* Would automating the sync between Azure DevOps PR states and Jira tickets save your team administrative time?
* Are there specific Databricks or Python anti-patterns you wish were automatically flagged *before* the code ever reaches the QA testing phase?

## 3. Alternative Automation Opportunities
I am open to pivoting to what the team actually needs by mid-May.

* If the QA team had a dedicated resource to build one automation or testing tool for you between now and May, what would be at the top of your wish list?
* Given our heavy use of Azure DevOps and Databricks, is there a gap in how we currently test our data pipelines that I could help automate?