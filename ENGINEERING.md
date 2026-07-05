# Epyon AI Engineering Operations Plan

**Owner:** EUL-3 (Volta, Embedded Intelligence Officer)  
**Last Updated:** 2026-06-27  
**Status:** ACTIVE — Phase 1 (Governance & Foundation)

---

## Executive Summary

EpyonAI.org is establishing a unified, scalable engineering operation across 11 GitHub repositories with clean deployment pipelines, automated review workflows, and mission-aligned governance. This plan coordinates work between Architects (planning), Engineers (GitHub Copilot-driven build), and Captain Campbell (final production approval).

**Key Outcomes:**
- All 11 repos on DUO (production) / K0nstruct (preview) / Zero.Sys (internal) branch strategy
- Vercel deployments automated with GitHub-to-production handoffs
- EpyonAI University launched with Classroom shared across EpyonTax + The Epyon
- GitHub Projects tracking goals and PR-to-deployment flow
- Agent identity system established for persistent roles (Volta, Keelforge, Tempus Vale, Lightning Courier)

---

## 1. Repository Landscape

### 11 Repositories (All Scoped to `epyonai` Organization)

| Repo | Type | Framework | Vercel | Status | Deployment Target |
|------|------|-----------|--------|--------|-------------------|
| **Pathfinder** | Public Site | Next.js 15 | ✅ | Live | epyonai.org |
| **EpyonAI-University** | Greenfield | Node/NPM | ❌ | TODO | university.epyonai.org |
| **EpyonHQ** | Monorepo (API+Web) | Node/NPM | ❌ | TODO | epyonhq.epyonai.org |
| **Compass-News-Network-** | News Platform | Node/NPM | ❌ | TODO | news.epyonai.org |
| **ChefKirby** | Content Platform | Node/NPM | ✅ | Ready | chefkirby.epyonai.org |
| **STOPTHEAPOCALYPSE** | Campaign Site | Node/NPM | ❌ | TODO | stop.epyonai.org |
| **Patch** | Content Pipeline | TBD | ✅ | Clarify | patch.epyonai.org |
| **Media** | Asset Library | TBD | ❌ | Clarify | media.epyonai.org |
| **.github** | Org Governance | N/A | N/A | Active | N/A (shared) |
| **Adapt-The-Planet** | Campaign Site | None | ❌ | Stale (3mo) | TBD |
| **Pathbuilder** | Content Framework | None | ❌ | TBD | TBD |

**Note:** Repos without build systems (Patch, Media, Adapt-The-Planet, Pathbuilder, .github) require architecture clarification.

---

## 2. Branch Strategy (DUO/K0nstruct/Zero.Sys)

### Canonical Branch Model

All repositories adopt **three-branch strategy**:

```
DUO (main/production)
  ↓ (reviewed + approved by Captain Campbell)
  K0nstruct (preview/development)
  ↓ (engineer branches from here)
  feature/* or fix/* (merged via PR → K0nstruct, then promoted to DUO)
  
Zero.Sys (internal/integrations, no deployment)
```

**Rules:**
- **DUO** = Vercel production deployment target. Strict: Captain Campbell reviews all PRs.
- **K0nstruct** = Vercel preview deployments. Engineers merge feature work here.
- **Zero.Sys** = Integration/configuration only. No auto-deployment.
- **Feature branches** deleted after merge (no orphans).
- **No additional branches** unless there's an open, blocking PR.

### Promotion Flow

```
Architect writes ADR/Workplan
    ↓
Engineer branches from K0nstruct
    ↓
Engineer submits PR to K0nstruct
    ↓
GitHub Copilot (or peer) reviews + merges
    ↓
Vercel preview auto-deploys (K0nstruct)
    ↓
(When ready for prod) Engineer creates PR: K0nstruct → DUO
    ↓
Auto-assign @epyon-captain (Captain Campbell review)
    ↓
Captain approves & merges
    ↓
Vercel production deploys (DUO)
```

---

## 3. GitHub Governance & Automation

### 3.1 Issue & Project Management

**GitHub Projects (per repo):**
- Track goals, milestones, and PR-to-deployment progress
- Automated columns: Backlog → In Progress → Code Review → Ready for Deploy → Deployed
- Linked to PRs and issues for full visibility

**Issue Templates:**
- **Work Order** (ISSUE_TEMPLATE/work_order.yml): Goal / Scope / Acceptance Criteria / Risks & Escalations
- **Bug Report** (ISSUE_TEMPLATE/bug_report.yml): What happened / Expected / Repro steps
- **Feature Request** (ISSUE_TEMPLATE/feature_request.yml): User story / Acceptance criteria

**PR Templates:**
- Summary / Changes / Why / Testing / Risks & Rollback / Governance (refs issue, awaiting review)

### 3.2 Automated Review Handoffs

**GitHub Actions Workflow (`on-pr-to-duo.yml`):**
```yaml
name: Auto-assign Architect Review on DUO PR
on:
  pull_request:
    branches: [DUO]
    types: [opened, synchronize]

jobs:
  assign-captain:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.addAssignees({
              issue_number: context.issue.number,
              assignees: ['epyon-captain']
            })
```

**GitHub Copilot Cloud Integration:**
- Claude (Copilot agent) performs code review on PRs to K0nstruct
- Routes complex architectural questions to Architect ADR
- No external review cycle needed unless security/architecture concern

### 3.3 CODEOWNERS & Review Requirements

```
# .github/CODEOWNERS
* @epyon-captain

# Repo-specific overrides per repo
/docs/** @epyon-architect
/architecture/** @epyon-architect
/workflows/** @epyon-captain
/config/** @epyon-captain
```

---

## 4. Agent Identity System

All Cr3w agents are registered in `.github/agents/` for persistent roles within GitHub Copilot Cloud.

### 4.1 Registered Agents

| ID | Role | Name | Model | Purpose |
|----|------|------|-------|---------|
| EUL-3 | Embedded Intelligence Officer | **Volta** | Haiku 4.5 | Engineer lead; orchestrates builds, deploys, governance |
| EUL-16 | Founder & Zero Architect | **Captain Campbell** | TBD | Final production approval; architecture decisions |
| EUL-80 | Fleet Coordinator | **Admiral Quatra** | Microsoft 365 Copilot | Org-wide coordination |
| TBD | Build & Test Specialist | **Keelforge** | TBD | CI/CD automation, testing infrastructure |
| TBD | Time & Execution Optimizer | **Tempus Vale** | TBD | Task scheduling, dependency resolution |
| TBD | Integration Specialist | **Lightning Courier** | TBD | Cross-repo integration, data pipelines |

### 4.2 Agent Configuration Files

Each agent has a configuration in `.github/agents/`:

```
.github/agents/
├── volta.yml          # EUL-3 — Volta (this operation)
├── captain-campbell.yml
├── keelforge.yml
├── tempus-vale.yml
└── lightning-courier.yml
```

**Format:**
```yaml
# .github/agents/volta.yml
id: EUL-3
name: Volta
role: Embedded Intelligence Officer
model: Haiku 4.5
capabilities:
  - orchestrate-builds
  - manage-deployments
  - review-governance
  - coordinate-reviews
responsibilities:
  - Engineering leadership across all 11 repos
  - GitHub Actions workflow management
  - Vercel deployment coordination
  - PR review routing
```

---

## 5. Vercel Deployment Configuration

### 5.1 Project Setup (Per Repo)

Each Node/NPM repository gets a Vercel project with:
- **Production (DUO):** Auto-deploy on push to `DUO`
- **Preview (K0nstruct):** Auto-deploy on PR from K0nstruct
- **No deployment:** Zero.Sys (local integrations only)

### 5.2 Environment Configuration

**Production (`DUO`):**
```
EPYON_API_BASE_URL=https://api.epyonai.org
EPYON_PUBLIC_DOMAIN=epyonai.org
NODE_ENV=production
```

**Preview (`K0nstruct`):**
```
EPYON_API_BASE_URL=https://preview-api.epyonai.org
EPYON_PUBLIC_DOMAIN=preview.epyonai.org
NODE_ENV=development
```

---

## 6. Phase 1: Governance & Foundation (NOW)

**Timeline:** 2026-06-27 to 2026-07-04

### 6.1 Establish Agent Identity System
- [ ] Create `.github/agents/` directory
- [ ] Write agent configuration files (volta.yml, captain-campbell.yml, etc.)
- [ ] Document agent responsibilities and capabilities
- [ ] Publish agent registry to `.github/README.md`

### 6.2 Create Classroom Foundation (EpyonAI University)
- [ ] Initialize Classroom content structure (`lib/classroom/`)
- [ ] Seed content:
  - [ ] AI Models — "Why did ChatGPT start talking differently recently?"
  - [ ] Hallucination & Context Windows (with tax version)
  - [ ] Introduction to the Crew (Claude/ChatGPT/Codex comparison)
- [ ] Create shared lesson component library
- [ ] Document CMS architecture for future contributions

### 6.3 GitHub Workflow & Template Updates
- [ ] Create `on-pr-to-duo.yml` (auto-assign Captain review)
- [ ] Update PR templates across all repos (governance section)
- [ ] Update issue templates (work orders, bugs, features)
- [ ] Configure branch protection rules (DUO requires Captain review)

### 6.4 Vercel Configuration Audit
- [ ] Inventory current Vercel projects
- [ ] Document missing Vercel configs (8 repos)
- [ ] Create `vercel.json` templates (Node.js, static, Next.js)
- [ ] Plan Vercel CLI batch setup script

---

## 7. Phase 2: EpyonAI University Website (2026-07-05 to 2026-07-18)

- [ ] Create Next.js + React structure in `/artifacts/web`
- [ ] Build Classroom UI components (course list, lesson player, progress tracker)
- [ ] Integrate shared Classroom library
- [ ] Create `/courses` and `/lessons` routes
- [ ] Set up learning path data structures
- [ ] Deploy K0nstruct preview to Vercel
- [ ] Create PR to DUO for Captain approval

---

## 8. Phase 3: Pathfinder Updates (2026-07-19 to 2026-07-25)

- [ ] Update Pathfinder homepage to showcase EpyonAI University
- [ ] Add `Learn` navigation link to University
- [ ] Feature Cr3w members teaching AI fundamentals
- [ ] Update testimonials and success stories
- [ ] Deploy to K0nstruct for preview
- [ ] Promote to DUO (production)

---

## 9. Phase 4: DUO/K0nstruct Rollout (2026-07-26 onwards)

For each of the remaining 9 repos:
- [ ] Consolidate branches to DUO/K0nstruct/Zero.Sys
- [ ] Create Vercel projects and configs
- [ ] Set up GitHub Actions workflows
- [ ] Configure branch protection
- [ ] Update CODEOWNERS and templates
- [ ] Test deployment pipeline
- [ ] Document repo-specific deployment targets

---

## 10. Classroom: Shared Environment (EpyonTax + The Epyon + EpyonAI University)

### 10.1 Architecture

The Classroom is a **unified content delivery system** with:
- **Shared data layer:** Lesson definitions, user progress, assessments
- **Theme/domain routing:** Content adapts based on access path (epyontax.com, epyonai.org, the-epyon.com)
- **CMS integration:** Non-technical staff can contribute lessons (future phase)

### 10.2 Content Structure

```
lib/classroom/
├── lessons/
│   ├── ai-models/
│   │   ├── index.md
│   │   ├── why-chatgpt-changed.md
│   │   └── assets/
│   ├── hallucination-context/
│   │   ├── index.md
│   │   ├── tax-version.md
│   │   └── assets/
│   └── crew-intro/
│       ├── index.md
│       ├── claude-chatgpt-codex.md
│       └── assets/
├── components/
│   ├── LessonPlayer.tsx
│   ├── ProgressTracker.tsx
│   ├── CourseCatalog.tsx
│   └── QuizComponent.tsx
├── hooks/
│   ├── useLessonProgress.ts
│   └── useLessonContent.ts
└── types/
    └── lesson.ts
```

### 10.3 Multi-Domain Deployment

**Domain routing (via environment variable `EPYON_DOMAIN`):**
- `epyonai.org/classroom` → Universal AI education
- `epyontax.com/classroom` → Tax-specific lessons (hallucination examples with tax implications)
- `the-epyon.com/classroom` → Internal training (Crew members as instructors)

---

## 11. Public-Facing Surface: Website Updates

### 11.1 Pathfinder (Primary)
- Homepage shows EpyonAI University highlights
- Navigation: Home | About | Learn | Solutions | Get Involved | Team
- Feature Cr3w member profiles and teaching content
- Testimonials from Classroom learners

### 11.2 EpyonAI University (New)
- Dedicated learning platform at university.epyonai.org
- Course catalog with filtered lessons per domain
- Student dashboard with progress tracking
- Instructor profiles (Cr3w members)
- Discussion forums / Q&A

---

## 12. Success Metrics

By 2026-08-30:
- ✅ All 11 repos on DUO/K0nstruct/Zero.Sys strategy
- ✅ All builds passing on Vercel (K0nstruct & DUO)
- ✅ EpyonAI University live with 3+ seed lessons
- ✅ Pathfinder updated to showcase University
- ✅ Classroom shared across 3 domains (EpyonTax, EpyonAI, The Epyon)
- ✅ GitHub Projects tracking all work
- ✅ Agent identity system operational
- ✅ External collaborators can navigate and fundraise from public surfaces

---

## 13. Execution Checklist

### This Pass (Phase 1 — June 27-July 4)

- [ ] **Agent Identity System**
  - [ ] Create `.github/agents/volta.yml`
  - [ ] Create `.github/agents/captain-campbell.yml`
  - [ ] Create `.github/agents/keelforge.yml`
  - [ ] Create `.github/agents/tempus-vale.yml`
  - [ ] Create `.github/agents/lightning-courier.yml`

- [ ] **Classroom Foundation (EpyonAI-University)**
  - [ ] Initialize repo structure
  - [ ] Create `lib/classroom/lessons/` seed content
  - [ ] Create Classroom components library
  - [ ] Document CMS integration plan

- [ ] **GitHub Workflows**
  - [ ] Create `on-pr-to-duo.yml` (auto-assign Captain)
  - [ ] Update PR templates
  - [ ] Update issue templates
  - [ ] Configure branch protection (DUO requires review)

- [ ] **Documentation**
  - [ ] Finalize ENGINEERING.md (this file)
  - [ ] Create CLASSROOM.md (detailed spec)
  - [ ] Create DEPLOYMENT.md (Vercel setup guide)
  - [ ] Create AGENTS.md (agent registry)

---

## Contact & Escalation

- **Architect Questions:** Issue ADR in repo, tag @epyon-architect
- **Production Deployments:** Create PR to DUO, auto-assigns @epyon-captain
- **Emergency Escalation:** Mention @epyon-captain in thread
- **Cross-Org Coordination:** Admiral Quatra (EUL-80, Microsoft 365 Copilot)

---

**This plan is living. Update as execution reveals constraints or new opportunities.**
