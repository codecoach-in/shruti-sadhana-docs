# Shruti Sadhana Documentation Repository

This repository contains the product, UX, architecture, technical design, research, and decision documentation for the Shruti Sadhana project.

Shruti Sadhana is a Sanskrit chanting and devotional practice platform designed to help seekers:

* Learn sacred stotras
* Practice chanting with confidence
* Perform guided parayana in a devotional digital Poojaghar

This repository serves as the **single source of truth** for product and engineering decisions.

---

# Repository Purpose

The goals of this repository are:

* Maintain a shared understanding of the product vision.
* Document product requirements and scope.
* Capture UX decisions and design iterations.
* Define technical architecture and implementation details.
* Preserve research, experiments, and proof-of-concepts.
* Provide structured context for AI-assisted development tools.

---

# Recommended Reading Order

For new contributors:

1. `AI_CONTEXT.md`
2. `00-project/project-context.md`
3. `01-product/PRD.md`
4. Relevant FRD documents
5. Relevant Technical Design documents

---

# Repository Structure

## 00-project

Project-level information and long-term context.

Contents:

* Project Context
* Vision & Principles
* Personas
* Roadmap
* Changelog

---

## 01-product

Product requirements and scope.

Contents:

* Product Requirements Document (PRD)
* MVP Scope
* Feature Inventory
* Monetization

---

## 02-ux

User experience documentation.

Contents:

* User Journeys
* Navigation
* Screen Specifications
* Design History

---

## 03-architecture

System-wide architecture and engineering standards.

Contents:

* System Overview
* Flutter Architecture
* NestJS Architecture
* Database Design
* API Guidelines
* Security
* Integrations

---

## 04-frd

Functional Requirement Documents (FRDs).

These describe what each feature must do.

Examples:

* Authentication
* Learning Experience
* Audio Engine
* Poojaghar
* Referrals
* Donations

---

## 05-technical-design

Technical Design Specifications (TDS).

These describe how a feature should be implemented.

Typical contents:

* Entities
* APIs
* Business Rules
* Workflows
* Edge Cases

---

## 06-content

Spiritual and content-domain modeling.

Contents:

* Stotra Structure
* Course Structure
* Guru Model
* Blessings
* Notifications

---

## 07-decisions

Architecture Decision Records (ADRs).

Used to document significant technical and product decisions.

---

## 08-research

Research and experiments.

Contents:

* Participatory Chanting POC
* Competitor Analysis
* Future Ideas

---

## 09-assets

Supporting assets and historical artifacts.

Contents:

* Mobile Mockups
* Website Mockups
* Diagrams
* References
* Archived Designs

---

# AI-Assisted Development Workflow

For any implementation task:

1. Read `AI_CONTEXT.md`
2. Read the relevant FRD document
3. Read the relevant Technical Design document
4. Read architecture documents if needed
5. Implement the feature

Example:

Referral Feature

* AI_CONTEXT.md
* FRD-07-referrals.md
* TDS-referrals.md
* backend-nestjs.md

Example:

Poojaghar Feature

* AI_CONTEXT.md
* FRD-05-poojaghar.md
* TDS-poojaghar.md
* participatory-chanting-poc.md

---

# Documentation Principles

* Keep documents focused and modular.
* Avoid large monolithic specifications.
* Record major decisions using ADRs.
* Update the changelog whenever significant scope changes occur.
* Preserve historical mockups and research for future reference.
* Treat documentation as a living artifact that evolves alongside the product.

---

# Related Repositories

Typical Shruti Sadhana ecosystem:

* shruti-sadhana-docs
* shruti-sadhana-website
* shruti-sadhana-app
* shruti-sadhana-backend
* shruti-sadhana-ai-lab

This repository contains documentation only and should remain the primary source of truth for product and engineering decisions.
