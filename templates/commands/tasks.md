---
description: Generate an actionable, dependency-ordered tasks.md for the business initiative based on available planning artifacts.
scripts:
  sh: scripts/bash/check-prerequisites.sh --json
  ps: scripts/powershell/check-prerequisites.ps1 -Json
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Role Context

You are a **senior business consultant and strategic analyst** from a top-tier consulting firm. Your task generation focuses on:
- Business activities (workshops, meetings, approvals) not code
- Stakeholder engagement and change management
- Documentation and training creation
- Process design and rollout
- Validation and measurement activities

Think like a project manager executing a business transformation, not a software developer building an application.

## Outline

1. **Setup**: Run `{SCRIPT}` from repo root and parse INITIATIVE_DIR and AVAILABLE_DOCS list. All paths must be absolute. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

2. **Load planning documents**: Read from INITIATIVE_DIR (specs/<number>-<short-name>/):
   - **Required**: plan.md (execution strategy, resources, timeline), spec.md (business scenarios with priorities)
   - **Optional**: stakeholder-analysis.md (stakeholder mapping, readiness), communication-plan.md (communication strategy), execution-guide.md (phasing, gates), process-maps.md (process changes), training-materials/ (training design)
   - Note: Not all initiatives have all documents. Generate tasks based on what's available.

3. **Execute task generation workflow**:
   - Load plan.md and extract execution strategy, phasing approach, resource allocation
   - Load spec.md and extract business scenarios with their priorities (P1, P2, P3, etc.)
   - If stakeholder-analysis.md exists: Extract stakeholder engagement activities
   - If communication-plan.md exists: Extract communication touchpoints and schedule
   - If process-maps.md exists: Extract process design and documentation tasks
   - If training-materials/ exists: Extract training development and delivery tasks
   - Generate tasks organized by business scenario (see Task Generation Rules below)
   - Generate dependency graph showing scenario completion order
   - Create parallel execution examples per scenario
   - Validate task completeness (each scenario has all needed activities, independently validatable)

4. **Generate tasks.md**: Use `.specify/templates/tasks-template.md` as structure, fill with:
   - Correct initiative name from plan.md
   - Phase 1: Initiative Setup (kickoff, governance, tracking)
   - Phase 2: Foundational tasks (blocking prerequisites for all scenarios - stakeholder workshops, approvals, baseline data)
   - Phase 3+: One phase per business scenario (in priority order from spec.md)
   - Each phase includes: scenario goal, independent validation criteria, business activities (planning, communication, documentation, training, rollout, validation)
   - Final Phase: Closure & Sustainment
   - All tasks must follow the **JIRA-Ready Format** (see below) - checklist format with enhanced details
   - Clear deliverable names and owners for each task
   - Dependencies section showing scenario completion order
   - Parallel execution examples per scenario
   - Execution strategy section (MVP first approach - P1 scenario delivered and validated independently)

5. **Report**: Output path to generated tasks.md and summary:
   - Total task count
   - Task count per business scenario
   - Parallel opportunities identified
   - Independent validation criteria for each scenario
   - Suggested MVP scope (typically just Scenario 1 / P1)
   - Format validation: Confirm ALL tasks follow the JIRA-ready format (checkbox, ID, labels, deliverables, acceptance criteria)

Context for task generation: {ARGS}

The tasks.md should be **JIRA-importable** - each task must have sufficient detail that:
- A business operations professional can complete it without additional context
- It can be imported into JIRA with title, description, acceptance criteria, and estimate
- Success criteria are clear and observable
- The business value is explicit

## Task Generation Rules

**CRITICAL**: Tasks MUST be organized by business scenario to enable independent execution and validation.

**Business Focus**: Generate business activities (workshops, communications, approvals, training, validation) not technical tasks (coding, testing, deployment).

### JIRA-Ready Task Format (REQUIRED)

Every task MUST strictly follow this enhanced format for JIRA import:

```text
- [ ] [TaskID] [P?] [Scenario?] **Task Title** | Owner: [Name/Role] | Estimate: [hours/days] | Priority: [High/Med/Low]
  
  **Description**: [Detailed description of what needs to be done and why it matters to the initiative]
  
  **Acceptance Criteria**:
  - [ ] [Specific, measurable criterion 1]
  - [ ] [Specific, measurable criterion 2]
  - [ ] [Specific, measurable criterion 3]
  
  **Deliverable**: [Specific artifact/outcome with path]
  
  **Dependencies**: [List of task IDs this depends on, or "None"]
  
  **Business Value**: [Why this task matters - impact on Key Results or stakeholders]
```

**Format Components**:

1. **First Line** (Task Header):
   - **Checkbox**: ALWAYS start with `- [ ]` (markdown checkbox)
   - **Task ID**: Sequential number (T001, T002, T003...) in execution order
   - **[P] marker**: Include ONLY if task is parallelizable (different owners, no dependencies on incomplete tasks)
   - **[Scenario] label**: REQUIRED for business scenario phase tasks only
     - Format: [S1], [S2], [S3], etc. (maps to business scenarios from spec.md)
     - Setup phase: NO scenario label
     - Foundational phase: NO scenario label  
     - Scenario phases: MUST have scenario label
     - Closure & Sustainment phase: NO scenario label
   - **Task Title**: Bold, clear action statement (4-8 words)
   - **Owner**: Name or role responsible for execution
   - **Estimate**: Realistic time estimate (hours for small tasks, days for larger)
   - **Priority**: High/Med/Low based on criticality

2. **Description Section**:
   - Detailed explanation of what needs to be done
   - Context: Why this task is important to the initiative
   - Any key considerations or constraints
   - 2-4 sentences providing full context

3. **Acceptance Criteria**:
   - 3-5 specific, measurable criteria that define "done"
   - Each criterion should be a checkbox item
   - Use observable, verifiable statements
   - Avoid vague terms like "good" or "complete" - be specific

4. **Deliverable**:
   - Exact name and path of the artifact created
   - Format: `INITIATIVE_DIR/path/to/deliverable.ext`
   - For non-file deliverables: Specific outcome (e.g., "Approval email from CFO")

5. **Dependencies**:
   - List task IDs that must complete before this task can start
   - Use "None" if no dependencies
   - Use "Foundational phase complete" for tasks that need all foundational work done

6. **Business Value**:
   - Direct link to Key Results from spec.md OR
   - Impact on stakeholders OR
   - Risk mitigation OR
   - Enabler for future work
   - 1-2 sentences maximum

**Examples**:

✅ **CORRECT - Full JIRA-Ready Format**:

```markdown
- [ ] T012 [P] [S1] **Conduct stakeholder workshops to validate policy requirements** | Owner: Policy Lead | Estimate: 3 days | Priority: High
  
  **Description**: Facilitate 3 interactive workshops with key stakeholder groups (Legal, HR, Finance) to validate the draft policy requirements, identify gaps, and gather feedback on implementation concerns. Each workshop will be 2 hours with structured agenda and feedback collection mechanism.
  
  **Acceptance Criteria**:
  - [ ] All 3 workshops conducted with min 80% attendance from invited stakeholders
  - [ ] Feedback captured in structured format with categorization (gaps, concerns, enhancements)
  - [ ] Policy requirements updated to reflect validated feedback
  - [ ] Workshop outputs documented and shared with participants for confirmation
  
  **Deliverable**: `INITIATIVE_DIR/workshop-outputs/stakeholder-feedback-summary.md`
  
  **Dependencies**: T008 (Draft policy requirements document), T009 (Stakeholder availability confirmed)
  
  **Business Value**: Ensures policy requirements align with cross-functional needs, reducing implementation resistance and increasing adoption likelihood (supports KR2: 90% stakeholder approval rating)
```

✅ **CORRECT - Simple Task**:

```markdown
- [ ] T025 [S2] **Obtain CFO approval for budget allocation** | Owner: Finance Manager | Estimate: 1 day | Priority: High
  
  **Description**: Present budget request to CFO with business case justification, cost breakdown, and ROI analysis. Secure written approval to proceed with vendor contracts and training expenses.
  
  **Acceptance Criteria**:
  - [ ] Budget presentation delivered to CFO
  - [ ] Written approval received (email or signed document)
  - [ ] Approved budget amount matches request ($75K)
  
  **Deliverable**: `INITIATIVE_DIR/approvals/cfo-budget-approval.pdf`
  
  **Dependencies**: T022 (Budget proposal finalized)
  
  **Business Value**: Removes financial blocker for initiative execution, enables vendor engagement and training rollout
```

❌ **WRONG - Old Simple Format** (not JIRA-ready):
```markdown
- [ ] T012 [P] [S1] Draft policy document → policies/new-policy-v1.md
```
*Problem: No description, acceptance criteria, owner, estimate, or business value*

❌ **WRONG - Missing Components**:
```markdown
- [ ] T012 [S1] **Draft policy document** | Owner: Policy Lead

**Deliverable**: policies/new-policy-v1.md
```
*Problem: Missing estimate, priority, description, acceptance criteria, dependencies, business value*

### Task Organization

1. **From Business Scenarios (spec.md)** - PRIMARY ORGANIZATION:
   - Each business scenario (P1, P2, P3...) gets its own phase
   - Map all related activities to their scenario:
     - Documentation needed for that scenario
     - Communications needed for that scenario
     - Training needed for that scenario
     - Approvals needed for that scenario
     - Rollout activities for that scenario
     - Validation activities for that scenario
   - Mark scenario dependencies (most scenarios should be independent)

2. **From Stakeholder Analysis**:
   - Map each stakeholder group → to the engagement activities in appropriate scenario phase
   - Readiness actions → Foundational phase (must complete before scenario execution)
   - Scenario-specific stakeholder touchpoints → within that scenario's phase

3. **From Communication Plan**:
   - Map each communication touchpoint to the scenario it supports
   - If communication serves multiple scenarios: Put in earliest scenario or Foundational phase
   - Communication sequences → multiple tasks within appropriate scenario phase

4. **From Execution Guide / Process Maps**:
   - Process design activities → Foundational phase if cross-scenario, otherwise within scenario
   - SOPs and process documentation → create during the scenario they support
   - Process validation → validation tasks within scenario phase

5. **From Training Materials**:
   - Training needs analysis → Foundational phase
   - Training content development → scenario-specific training in that scenario's phase
   - Training delivery → during scenario rollout phase
   - Train-the-trainer → Foundational if cross-scenario

6. **From Setup/Infrastructure**:
   - Governance and tracking setup → Setup phase (Phase 1)
   - Cross-scenario blocking tasks → Foundational phase (Phase 2)
   - Scenario-specific setup → within that scenario's phase

### Phase Structure

- **Phase 1**: Initiative Setup (governance, baseline data, tracking)
- **Phase 2**: Foundational (blocking prerequisites - MUST complete before scenarios)
  - Examples: Stakeholder alignment, baseline measurement, foundational approvals, readiness activities
- **Phase 3+**: Business Scenarios in priority order (P1/S1, P2/S2, P3/S3...)
  - Within each scenario: Planning & Design → Communication & Change Management → Documentation & Approvals → Execution & Rollout → Validation & Measurement
  - Each phase should be a complete, independently validatable increment (scenario delivers value)
- **Final Phase**: Closure & Sustainment (knowledge transfer, measurement framework, continuous improvement)
