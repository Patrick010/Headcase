# PRINCE2 Project Registry

**Date:** 2025-08-17
**Status:** Live Document

## 1. Purpose

This document serves as the master file, or single source of truth, for tracking all key documents, records, and artifacts for the Zotify API project. It provides a centralized index for all stakeholders to ensure traceability and transparency. To maintain this document's value, it is mandatory that any new markdown documentation file created anywhere in the project is added to this registry.

---

## 2. Core Project Planning Documents

| Document | Location | Description |
|---|---|---|
| **Project Registry** | [`PROJECT_REGISTRY.md`](./PROJECT_REGISTRY.md) | This document, the master index for all project artifacts. |
| **Template Registry** | [`../templates/REGISTRY.md`](../templates/REGISTRY.md) | A registry of all reusable documentation templates. |
| **Onboarding Guide** | [`ONBOARDING.md`](./ONBOARDING.md) | The primary entry point and guide for new developers to get up to speed on the project. |
| **Current State** | [`CURRENT_STATE.md`](./logs/CURRENT_STATE.md) | A live snapshot of the project's most recent status, goals, and pending work. |
| **Session Log** | [`SESSION_LOG.md`](./logs/SESSION_LOG.md) |Log of activities and findings from sessions. |
| **Live Activity Log** | [`ACTIVITY.md`](./logs/ACTIVITY.md) | A live, chronological log of all major tasks and audit activities. |
| **Project Brief** | [`PROJECT_BRIEF.md`](./PROJECT_BRIEF.md) | A high-level summary of the project's purpose, scope, and justification (PRINCE2). |
| **Project Initiation Document (PID)** | [`PID.md`](./PID.md) | The formal 'living document' that defines the project's scope, plans, and controls (PRINCE2). |
| **High-Level Design (HLD)** | [`HIGH_LEVEL_DESIGN.md`](./HIGH_LEVEL_DESIGN.md) | Outlines the high-level architecture, scope, and principles. |
| **Low-Level Design (LLD)** | [`LOW_LEVEL_DESIGN.md`](./LOW_LEVEL_DESIGN.md) | Describes specific work items and detailed implementation designs. |
| **Roadmap** | [`ROADMAP.md`](./ROADMAP.md) | Outlines the high-level phases and major milestones of development. |
| **Execution Plan** | [`EXECUTION_PLAN.md`](./EXECUTION_PLAN.md) | Provides a detailed breakdown of tasks required to fulfill the roadmap. |
| **Endpoints Reference** | [`ENDPOINTS.md`](./ENDPOINTS.md) | A canonical reference for all public API endpoints for both the Zotify and Snitch projects. |
| **Future Enhancements** | [`FUTURE_ENHANCEMENTS.md`](./FUTURE_ENHANCEMENTS.md) | A "parking lot" for new ideas and long-term ambitions not on the current roadmap. |
| **Lessons Learnt Log** | [`LESSONS-LEARNT.md`](./LESSONS-LEARNT.md) | A log of key takeaways and insights from each project phase. |
| **Logging System Design** | [`LOGGING_SYSTEM_DESIGN.md`](./LOGGING_SYSTEM_DESIGN.md) | The detailed architectural design for the centralized logging system. |
| **Logging Phased Implementation** | [`LOGGING_PHASES.md`](./LOGGING_PHASES.md) | The authoritative document tracking the phased design and implementation of the Extendable Logging System. |
| **Logging Traceability Matrix** | [`LOGGING_TRACEABILITY_MATRIX.md`](./LOGGING_TRACEABILITY_MATRIX.md) | Maps logging system requirements to design documents and backlog tasks. |
| **Dynamic Plugin Proposal** | [`proposals/DYNAMIC_PLUGIN_PROPOSAL.md`](./proposals/DYNAMIC_PLUGIN_PROPOSAL.md) | A formal proposal for adding a dynamic plugin system for custom logging sinks. |
| **Low-Code Integration Proposal** | [`proposals/LOW_CODE_PROPOSAL.md`](./proposals/LOW_CODE_PROPOSAL.md) | A formal proposal for integrating with low-code platforms like Node-RED. |
| **Home Automation Proposal** | [`proposals/HOME_AUTOMATION_PROPOSAL.md`](./proposals/HOME_AUTOMATION_PROPOSAL.md) | A formal proposal for integrating with home automation platforms like Home Assistant. |
| **Multi-Source Metadata Proposal** | [`proposals/MULTI_SOURCE_METADATA_PROPOSAL.md`](./proposals/MULTI_SOURCE_METADATA_PROPOSAL.md) | A formal proposal for a plugin-driven, multi-source metadata ingestion and querying system. |
| **Project Backlog** | [`BACKLOG.md`](./BACKLOG.md) | A tactical backlog of tasks managed by the formal qualification process defined in the PID. |
| **Traceability Matrix** | [`TRACEABILITY_MATRIX.md`](./TRACEABILITY_MATRIX.md) | A live matrix mapping requirements from use cases and design docs to implementation and test status. |
| **Use Cases** | [`USECASES.md`](./USECASES.md) | A collection of user-driven scenarios and requirements for the API. |
| **Use Case Gap Analysis** | [`USECASES_GAP_ANALYSIS.md`](./USECASES_GAP_ANALYSIS.md) | An analysis of the gaps between the desired use cases and the current implementation. |
| **Task Checklist** | [`TASK_CHECKLIST.md`](./TASK_CHECKLIST.md) | A checklist to be used for every task to ensure compliance with project standards. |
| **Dependency Policy** | [`DEPENDENCIES.md`](./DEPENDENCIES.md) | The policy and registry for managing third-party dependencies. |
| **Security Document** | [`SECURITY.md`](./SECURITY.md) | The definitive security reference for the project. |
| **Project CI/CD Guide** | [`CICD.md`](./CICD.md) | A high-level guide to CI/CD philosophy for project management. |

---

## 3. API & Module Documentation

### 3.1. Core Technical Documentation and Manuals
| Document | Location | Description |
|---|---|---|
| **Changelog** | [`docs/CHANGELOG.md`](../docs/CHANGELOG.md) | A log of all user-facing changes for each version. |
| **Feature Specifications** | [`docs/reference/FEATURE_SPECS.md`](../docs/reference/FEATURE_SPECS.md) | The master index for detailed, standardized specifications for all system features. |
| **Operator Manual** | [`docs/manuals/OPERATOR_MANUAL.md`](../docs/manuals/OPERATOR_MANUAL.md) | Provides guidance for deploying, configuring, and maintaining the system in a production environment. |
| **System Integration Guide** | [`docs/manuals/SYSTEM-INTEGRATION-GUIDE.md`](../docs/manuals/SYSTEM-INTEGRATION-GUIDE.md) | A guide for external developers on how to integrate with and consume the solution. |
| **Technical Developer Guide** | [`docs/manuals/TECHNICAL-DEVELOPER-GUIDE.md`](../docs/manuals/TECHNICAL-DEVELOPER-GUIDE.md) | A guide for internal developers on setting up a local environment, running tests, and contributing to the codebase. |
| **Developer CI/CD Guide**| [`docs/manuals/CICD.md`](../api/docs/manuals/CICD.md) | A detailed technical guide aimed at developers to the CI/CD pipeline and local setup. |
| **User Manual** | [`docs/manuals/USER_MANUAL.md`](../docs/manuals/USER_MANUAL.md) | A manual for end-users of the application, explaining the core workflow for downloading tracks and the standard error response format. |
| **Error Handling Guide** | [`docs/manuals/ERROR_HANDLING_GUIDE.md`](../docs/manuals/ERROR_HANDLING_GUIDE.md) | A developer guide for the Generic Error Handling Module. |
| **Logging Guide** | [`docs/manuals/LOGGING_GUIDE.md`](../docs/manuals/LOGGING_GUIDE.md) | A comprehensive developer guide for the Flexible Logging Framework. |
| **Error Handling Design** | [`docs/system/ERROR_HANDLING_DESIGN.md`](../docs/system/ERROR_HANDLING_DESIGN.md) | The technical design specification for the Generic Error Handling Module. |
| **Installation Guide** | [`docs/system/INSTALLATION.md`](../docs/system/INSTALLATION.md) | A guide detailing the steps to install the application from source. |
| **System Requirements** | [`docs/system/REQUIREMENTS.md`](../docs/system/REQUIREMENTS.md) | Lists the system and software requirements for running the application. |
| **Flexible Logging Framework Design** | [`docs/reference/features/developer_flexible_logging_framework.md`](../docs/reference/features/developer_flexible_logging_framework.md) | A design document for a developer-facing, programmable logging framework. |
| **Privacy Compliance** | [`docs/system/PRIVACY_COMPLIANCE.md`](../docs/system/PRIVACY_COMPLIANCE.md) | An overview of how the project complies with data protection laws like GDPR. |

---

## 4. Audit & Alignment Documents
| Document | Location | Description |
|---|---|---|
| **First Audit** | [`audit/FIRST_AUDIT.md`](./audit/FIRST_AUDIT.md) | The initial audit report for the project. |
| **HLD/LLD Alignment Plan** | [`audit/AUDIT_ALIGNMENT_PLAN.md`](./audit/AUDIT_ALIGNMENT_PLAN.md) | The phased plan for bringing design documents into alignment with the codebase. |
| **Audit Log: Phase 1** | [`audit/AUDIT-phase-1.md`](./audit/AUDIT-phase-1.md) | Log of activities and findings from Phase 1 of the alignment plan. |
| **Audit Traceability Matrix** | [`audit/AUDIT_TRACEABILITY_MATRIX.md`](./audit/AUDIT_TRACEABILITY_MATRIX.md) | A matrix for tracking audit-related requirements and their implementation status. |
| **Audit Prompt** | [`audit/audit-prompt.md`](./audit/audit-prompt.md) | The prompt used for audit processes. |

### File Naming Conventions
To maintain consistency, this project follows a strict naming convention:
-   **Markdown Files:** All markdown documentation files (`.md`) **must** be in full uppercase (e.g., `README.md`, `HIGH_LEVEL_DESIGN.md`).
-   **Other Files:** All other files (source code, configuration, scripts, etc.) **must** be in lowercase (e.g., `main.py`, `pyproject.toml`).