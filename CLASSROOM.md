# The Classroom: Shared Educational Platform

**Owner:** Lightning Courier (Integration Specialist)  
**Architecture Lead:** Captain Campbell (Architect)  
**Repository:** `epyonai/epyonai-university`  
**Deployment:** university.epyonai.org, epyontax.com/classroom, the-epyon.com/classroom  

---

## 1. Vision

The Classroom is a **unified, adaptable educational platform** that bridges EpyonAI University, EpyonTax, and The Epyon. It delivers AI fundamentals education with domain-specific customization:

- **EpyonAI.org/Classroom**: Universal AI education for all learners
- **EpyonTax.com/Classroom**: Tax-specific lessons (hallucinations, context windows, legal AI)
- **The_Epyon.com/Classroom**: Internal Crew member training and mentorship

**Guiding Principle:** One codebase, one content platform, infinite domains.

---

## 2. Architecture

### 2.1 Shared Content Layer

All lessons live in a **single repository** (`lib/classroom/lessons/`) and are routed to different domains based on environment configuration:

```
lib/classroom/
├── lessons/
│   ├── ai-models/
│   │   ├── index.md (shared metadata)
│   │   ├── content.md (universal content)
│   │   ├── why-chatgpt-changed.md (AI Models lesson)
│   │   └── assets/ (images, videos, diagrams)
│   ├── hallucination-context/
│   │   ├── index.md
│   │   ├── universal.md (general context windows lesson)
│   │   ├── tax-version.md (tax-specific implications)
│   │   ├── crew-version.md (internal training version)
│   │   └── assets/
│   └── crew-intro/
│       ├── index.md
│       ├── content.md (Introduction to the Crew)
│       ├── claude-explanation.md
│       ├── chatgpt-codex-comparison.md
│       └── assets/
├── components/
│   ├── LessonPlayer.tsx (universal lesson renderer)
│   ├── ProgressTracker.tsx (user progress display)
│   ├── CourseCatalog.tsx (course listing & filtering)
│   ├── QuizComponent.tsx (assessments & quizzes)
│   ├── DiscussionThread.tsx (Q&A and comments)
│   ├── InstructorProfile.tsx (Cr3w member profiles)
│   └── DomainBadge.tsx (visual domain indicator)
├── hooks/
│   ├── useLessonProgress.ts (fetch & track user progress)
│   ├── useLessonContent.ts (fetch lesson by slug + domain)
│   ├── useCoursesByDomain.ts (filter courses per domain)
│   └── useAuthenticatedUser.ts (cross-domain authentication)
├── types/
│   ├── lesson.ts (Lesson, Course, Module interfaces)
│   ├── progress.ts (UserProgress, Assessment interfaces)
│   └── domain.ts (Domain type, routing rules)
├── styles/
│   ├── classroom.css (universal styling)
│   └── domain-overrides.css (per-domain theming)
└── utils/
    ├── contentLoader.ts (load lessons by domain)
    ├── progressTracker.ts (save/retrieve progress)
    └── domainRouter.ts (route based on EPYON_DOMAIN)
```

### 2.2 Deployment Strategy

Each domain deploys the **same codebase** but with different environment configuration:

| Domain | URL | Build Target | Env Var `EPYON_DOMAIN` | Featured Content |
|--------|-----|--------------|----------------------|------------------|
| EpyonAI University | university.epyonai.org | K0nstruct/DUO | `epyonai` | All lessons (universal) |
| EpyonTax | epyontax.com/classroom | External (federated) | `epyontax` | Tax + Universal + Hallucination (tax-version) |
| The Epyon | the-epyon.com/classroom | External (federated) | `the-epyon` | Crew Training + Universal + Advanced Topics |

**Build artifact:** Single Docker image + environment config determines what's visible per domain.

### 2.3 Content Structure: Lesson Format

Each lesson is a **markdown file with YAML frontmatter**, processed by `next-mdx-remote` or similar:

```markdown
---
id: ai-models-001
slug: why-chatgpt-changed
title: "Why Did ChatGPT Start Talking Differently Recently?"
domain: [universal, epyonai, epyontax, the-epyon]
difficulty: beginner
duration_minutes: 15
instructor: Volta
tags: [ai, language-models, updates]
prerequisites: []
learning_objectives:
  - Understand what ChatGPT is and how it works
  - Learn why models update over time
  - Recognize the difference between model versions
assessment_type: quiz
---

# Why Did ChatGPT Start Talking Differently Recently?

ChatGPT was updated... [content continues]
```

**Fields:**
- `domain`: which domains this lesson appears in
- `instructor`: Cr3w member or external instructor
- `difficulty`: beginner / intermediate / advanced
- `assessment_type`: quiz / essay / discussion / project / none
- `tags`: for filtering and search

---

## 3. Content: Seed Lessons (Phase 1)

### 3.1 Lesson 1: AI Models — "Why Did ChatGPT Start Talking Differently Recently?"

**Scope:**
- What is ChatGPT and how does it work?
- Why do AI models change and improve?
- How to tell if a model has been updated
- Real-world impact of model updates

**Audience:** Beginners (all domains)

**Instructor:** Volta (EUL-3)

**Content Outline:**
1. Introduction: What makes ChatGPT "different"?
2. Model architecture basics (transformer, tokens, attention)
3. Training data and knowledge cutoffs
4. Fine-tuning and instruction alignment
5. Updates and rollouts (GPT-3.5 → GPT-4 → etc.)
6. How to check which model you're using
7. Impact on workflows (reproducibility, API contracts)
8. Quiz: Identify model version from behavior

**Duration:** 15 minutes

---

### 3.2 Lesson 2: Hallucination & Context Windows

**Scope (Universal):**
- What is hallucination in LLMs?
- Why do models hallucinate?
- Context windows and token limits
- How to reduce hallucination risk
- Best practices for reliable AI

**Scope (Tax Version — Additional):**
- Legal implications of AI hallucinations in tax documents
- Case study: When an AI gave bad tax advice
- Verification procedures for AI-generated tax content
- Disclosure requirements when using AI
- Liability and malpractice considerations

**Audience:**
- Universal: All learners
- Tax: Tax professionals, accountants, CPAs

**Instructors:** Volta (universal), TBD (tax specialist)

**Content Outline (Universal):**
1. Definition: What is hallucination?
2. Mechanisms: Why models generate false information
3. Context windows: How models "remember" (and forget)
4. Token counting and limits
5. Probability & confidence (models don't "know" they're wrong)
6. Examples: Common hallucination types
7. Mitigation strategies (prompting, RAG, verification loops)
8. Quiz & Assessment

**Additional Content (Tax Version):**
- Section 5.5: Legal implications in U.S. tax law
- Section 5.6: Case study (AI tax errors)
- Section 5.7: Verification workflows for tax professionals
- Section 5.8: Disclosure and liability

**Duration:** 
- Universal: 20 minutes
- Tax (with additional sections): 35 minutes

---

### 3.3 Lesson 3: Introduction to the Crew

**Scope:**
- Who is Claude? (Anthropic's AI)
- Who is ChatGPT? (OpenAI's AI)
- What is Codex? (OpenAI's code model)
- How are they different?
- When to use which model
- Overview of the Epyon Cr3w

**Audience:** All learners (universal)

**Instructor:** Volta, Captain Campbell

**Content Outline:**
1. Meet the Crew: The Epyon aboard the Zero System
   - Who are we? (Cr3w members and their roles)
   - What is our mission? (Really Fucking Big Problems)
   - How do we work? (Humans + machines collaborating)

2. Claude (Anthropic)
   - Background & creators
   - Specialties (reasoning, safety, writing)
   - How Claude works (Constitutional AI)
   - When to use Claude (analysis, code, creative)

3. ChatGPT (OpenAI)
   - Background & creators
   - Specialties (versatility, conversation, plugins)
   - How ChatGPT works (reinforcement learning)
   - When to use ChatGPT (general purpose, code, chat)

4. Codex (OpenAI)
   - Specialized for code generation
   - Limitations vs. ChatGPT
   - When to use (IDE integration, code completion)

5. Comparative Table
   - Accuracy, speed, cost, specialization, bias, safety

6. The Future: Working with AI
   - AI is a tool, not a replacement
   - Collaboration is key
   - Critical thinking still matters

7. Quiz: Match AI to best use case

**Duration:** 20 minutes

---

## 4. User Progress & Authentication

### 4.1 Progress Tracking

Each user's progress is stored in a database (PostgreSQL via Drizzle ORM):

```sql
users
├── id
├── email
├── created_at
└── updated_at

enrollments
├── id
├── user_id
├── course_id
├── enrolled_at
├── completed_at (nullable)
└── progress_percent

lesson_progress
├── id
├── user_id
├── lesson_id
├── started_at
├── completed_at (nullable)
├── time_spent_seconds
└── quiz_score (nullable)

quiz_responses
├── id
├── user_id
├── lesson_id
├── question_id
├── answer
├── correct (boolean)
└── submitted_at
```

### 4.2 Cross-Domain Authentication

All three domains (epyonai.org, epyontax.com, the-epyon.com) share a **single authentication provider** (OpenID Connect or Supabase):

```yaml
# Environment variables (shared across domains)
EPYON_OIDC_ISSUER_URL=https://auth.epyonai.org
EPYON_OIDC_CLIENT_ID=classroom-<domain>
EPYON_OIDC_CLIENT_SECRET=<secret>

# Session cookie domain (allows SSO across subdomains)
SESSION_COOKIE_DOMAIN=.epyonai.org
```

Users log in once and can access lessons across all domains (with appropriate permissions for their domain).

---

## 5. Content Management: Phase 1 vs. Phase 2

### Phase 1 (Now): Repo-Based

- Lessons stored as markdown files in `lib/classroom/lessons/`
- Content edits via Git commits and PRs
- Requires GitHub access to contribute
- Simple, version-controlled, auditable

### Phase 2 (Q3 2026): CMS Integration

- Headless CMS (Strapi, Contentful, or custom)
- Non-technical staff can author lessons
- Visual editor with preview
- Workflow: Draft → Review (Architect) → Publish
- API-driven content delivery

**Plan:** Create CMS abstraction layer now so both Phase 1 and Phase 2 can coexist.

---

## 6. Instructor Profiles (Cr3w Members)

Each lesson is taught by a Cr3w member with a featured profile:

```yaml
# lib/classroom/instructors/volta.yml
id: volta
name: Volta
role: Embedded Intelligence Officer
bio: |
  EUL-3, running on Claude Haiku. Volta orchestrates engineering
  operations aboard the Epyon and teaches AI fundamentals.
avatar_url: https://...
expertise:
  - AI & Language Models
  - Software Engineering
  - Systems Design
social_links:
  github: volta
  twitter: VOLTA_Best_Claude
teaching:
  - ai-models-001
  - crew-intro-001
```

On lesson pages, display instructor bio, links to other lessons they teach, and option to follow/contact.

---

## 7. Discussion & Q&A

Each lesson has a discussion thread where learners can ask questions and Cr3w members can answer.

- Powered by GitHub Discussions (free, integrated with repos)
- Each lesson has a discussion category: `classroom-ai-models`, `classroom-hallucination`, etc.
- Cr3w members are notified of unanswered questions
- Best answers are pinned

---

## 8. Assessments & Quizzes

Each lesson concludes with a quiz to reinforce learning:

```yaml
quiz:
  - question: "What is a context window?"
    options:
      - The physical window of your office
      - The maximum number of tokens a model can process
      - How fast a model processes text
    correct_answer: 1
    explanation: "Context windows define how much text a model can read at once..."

  - question: "Why do models hallucinate?"
    type: open
    rubric: |
      Score based on understanding of probability, training data, and token prediction...
```

Quizzes are auto-graded (multiple choice) or manually reviewed (essay).

---

## 9. Deployment & Scaling

### 9.1 Build & Deploy

All three domains deploy from the **same codebase**:

```bash
# Single build artifact (Docker image)
docker build -t epyonai/classroom:latest .

# Deploy to each domain with different env config
docker run \
  -e EPYON_DOMAIN=epyonai \
  -e DATABASE_URL=postgres://... \
  epyonai/classroom:latest

docker run \
  -e EPYON_DOMAIN=epyontax \
  -e DATABASE_URL=postgres://... \
  epyonai/classroom:latest
```

### 9.2 Database Considerations

- **Shared database:** Single PostgreSQL instance with domain-based row-level security
  - Pros: Unified data, cross-domain reporting
  - Cons: Larger blast radius if compromised
  
- **Federated databases:** Each domain has its own DB, syncs user progress
  - Pros: Domain isolation, independent scaling
  - Cons: Complex sync logic, data consistency challenges

**Recommendation (Phase 1):** Shared database with RBAC and audit logging. Revisit at scale.

---

## 10. Success Metrics (Phase 1 & 2)

### Phase 1 (Launch)
- ✅ 3 seed lessons published and accessible
- ✅ User progress tracking working (quiz scores, time spent)
- ✅ All 3 domains (epyonai, epyontax, the-epyon) routing correctly
- ✅ Cr3w member instructor profiles visible
- ✅ Discussion threads active on each lesson

### Phase 2 (CMS)
- ✅ Non-technical staff authoring lessons without Git access
- ✅ Lesson approval workflow (Draft → Architect review → Publish)
- ✅ 10+ lessons published
- ✅ >100 learners enrolled
- ✅ >50% lesson completion rate

---

## 11. Next Steps

1. **Initialize repository** (EpyonAI-University)
   - Create `lib/classroom/` structure
   - Create first 3 seed lessons (markdown files)

2. **Build lesson player UI**
   - React components (LessonPlayer, QuizComponent, etc.)
   - MDX rendering for markdown content

3. **Implement progress tracking**
   - Database schema (Drizzle ORM)
   - API routes for progress updates

4. **Set up authentication**
   - OpenID Connect integration
   - Session management

5. **Deploy to Vercel**
   - vercel.json configuration
   - Environment setup (3 domains)

6. **Test cross-domain routing**
   - Verify content filtering per domain
   - Test SSO across domains

---

**Document Status:** Living specification. Update as implementation reveals needs.
