# Learn++ Project Documentation Index

## 📋 Quick Navigation

**New to this project?** Start with → [PHASED_ROADMAP.md](#1-start-here)

**Looking for specific topics?** Jump to → [Topic Index](#topic-index)

**Need definitions?** See → [GLOSSARY.md](GLOSSARY.md)

---

## 🎯 Reading Paths by Role

### Path A: Project Manager / Stakeholder
*Goal: Understand scope, timeline, and deliverables*

1. [PHASED_ROADMAP.md](#1-start-here) - Project phases and timeline
2. [EXPERIMENT_PLAN.md](#3-experiment-design) - What experiments will run
3. [DATA_STRATEGY.md](#4-data-planning) - Concrete dataset selections
4. [RISKS.md](#2-project-foundation) - Known risks and mitigations
5. [IMPLEMENTATION_HANDOFF.md](#6-handoff-and-deliverables) - Phase 1 → Phase 2 transition

**Estimated reading time**: 30-45 minutes

---

### Path B: ML Engineer / Implementer
*Goal: Understand technical details for implementation*

1. [architecture.md](#1-start-here) - System architecture overview
2. [LEARNPP_EQUATION_FLOW_MAP.md](#5-algorithm-and-equations) - Learn++ algorithm flow
3. [CLASSIFIER_EQUATIONS_DETAILED.md](#5-algorithm-and-equations) - All mathematical equations
4. [EXPERIMENT_PLAN.md](#3-experiment-design) - Experiment matrix and metrics
5. [DATA_STRATEGY.md](#4-data-planning) - Dataset details and preprocessing
6. [IMPLEMENTATION_HANDOFF.md](#6-handoff-and-deliverables) - Phase 2 requirements
7. [GLOSSARY.md](#7-reference-materials) - Technical terminology

**Estimated reading time**: 2-3 hours

---

### Path C: Data Scientist / Researcher
*Goal: Understand modeling choices and evaluation strategy*

1. [CLASSIFIER_PROFILES.md](#3-experiment-design) - All candidate models profiled
2. [CLASSIFIER_PAIRING_STRATEGY.md](#3-experiment-design) - Weak-strong pairing rationale
3. [CLASSIFIER_REFINEMENT_GUIDE.md](#3-experiment-design) - Selection process
4. [EXPERIMENT_PLAN.md](#3-experiment-design) - Metrics and comparison methodology
5. [LEARNPP_EQUATION_FLOW_MAP.md](#5-algorithm-and-equations) - Algorithm details
6. [CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md](#appendix) - Numeric examples

**Estimated reading time**: 2-3 hours

---

### Path D: Quick Reference
*Goal: Look up specific information fast*

1. [GLOSSARY.md](#7-reference-materials) - All term definitions
2. [SCORECARD_TEMPLATE.md](#7-reference-materials) - Experiment tracking format
3. [TODO.md](#2-project-foundation) - Action items checklist
4. [DECISION_LOG.md](#2-project-foundation) - Historical decisions

**Estimated reading time**: As needed

---

## 📚 Complete Document Listing

### 1. Start Here

#### [PHASED_ROADMAP.md](PHASED_ROADMAP.md) 🌟
**Purpose**: High-level project structure and timeline
**Contains**:
- Phase 1: Classifier selection and documentation (current)
- Phase 2: Medical imaging implementation
- Phase 3A-C: Expansion to drone/photo, mapping, swarm control
- Exit gates and governance rules

**When to read**: First document for all new team members

---

#### [architecture.md](architecture.md) 🌟
**Purpose**: Technical system architecture
**Contains**:
- 7-layer architecture (data intake → evaluation → artifacts)
- Weak vs strong classifier strategy
- Learn++ input/output contracts
- CompositeScore ranking equation

**When to read**: After roadmap, before diving into technical details

---

### 2. Project Foundation

#### [ASSUMPTIONS.md](ASSUMPTIONS.md)
**Purpose**: Document working assumptions
**Contains**:
- Project assumptions (documentation-only phase, Learn++ framework)
- Data assumptions (format, availability, quality)
- Evaluation assumptions (metrics, ranking)
- Operational assumptions (handoff, budget)

**When to update**: When validating or discovering assumption violations

---

#### [RISKS.md](RISKS.md)
**Purpose**: Risk register with mitigations
**Contains**:
- Risk table (ID, probability, impact, signals, mitigations)
- Top 3 priority risks
- Immediate risk actions

**When to update**: During risk reviews and after experiments

---

#### [TODO.md](TODO.md)
**Purpose**: Actionable task checklist
**Contains**:
- Immediate tasks (dataset selection, compute budget)
- Learn++ preparation tasks
- Documentation completion tasks
- Phase transition gates

**When to update**: Continuously as tasks complete

---

#### [DECISION_LOG.md](DECISION_LOG.md)
**Purpose**: Historical record of key decisions
**Contains**:
- Decision table (date, description, rationale, owner)

**When to update**: After making any significant project decision

---

### 3. Experiment Design

#### [EXPERIMENT_PLAN.md](EXPERIMENT_PLAN.md) ⭐
**Purpose**: Complete experiment specification
**Contains**:
- Full candidate pool (6 weak + 5 strong)
- Recommended shortlist (3 weak + 3 strong)
- **Experiment matrix**: 6-8 planned experiments (not all 18 combinations)
- Metrics, CompositeScore formula, exit criteria
- **KEY FIX**: Clarifies that we test selected PAIRS, not all models

**When to read**: Before designing experiments

---

#### [CLASSIFIER_PROFILES.md](CLASSIFIER_PROFILES.md)
**Purpose**: Detailed profiles of all 11 candidate classifiers
**Contains**:
- Candidate overview table
- Per-model profiles: why it fits Learn++, cost, strengths, risks
- Keep/drop signals for each candidate

**When to read**: When selecting shortlist or understanding model tradeoffs

---

#### [CLASSIFIER_PAIRING_STRATEGY.md](CLASSIFIER_PAIRING_STRATEGY.md) ⭐
**Purpose**: Weak-strong pairing recommendations
**Contains**:
- **WHEN to use**: Phase 1 planning, after longlist created
- **WHY pairing matters**: Reduces 30 combinations to 6-9 experiments
- 4 recommended pair sets (Efficiency, Balanced, Margin-based, Representation)
- Track-specific priorities
- **KEY FIX**: Now explains purpose and timing clearly

**When to read**: When designing experiment matrix

---

#### [CLASSIFIER_REFINEMENT_GUIDE.md](CLASSIFIER_REFINEMENT_GUIDE.md) ⭐
**Purpose**: Rigorous selection process
**Contains**:
- 7 gates (Eligibility → Role → Quality → Stability → Cost → Readiness → Selection)
- Keep/Watch/Drop rules
- **KEY FIX**: Clarifies "strong enough" means "good quality", not "strong classifier type"

**When to read**: During and after experiments for model evaluation

---

### 4. Data Planning

#### [DATA_STRATEGY.md](DATA_STRATEGY.md) ⭐
**Purpose**: Dataset selection with concrete examples
**Contains**:
- **Public track options**: CIFAR-10, Fashion-MNIST, UCI HAR (RECOMMENDED)
- **Medical track options**: OASIS-3 MRI (RECOMMENDED), ChestX-ray14, BraTS
- Pros/cons for each dataset
- Data contract schema (10 required fields)
- Batch design guidance
- Access timeline
- **KEY FIX**: Concrete datasets with URLs, not vague descriptions

**When to read**: Immediately for dataset selection

---

### 5. Algorithm and Equations

#### [LEARNPP_EQUATION_FLOW_MAP.md](LEARNPP_EQUATION_FLOW_MAP.md) ⭐
**Purpose**: Visual Learn++ algorithm flow with equations
**Contains**:
- **Mermaid flowchart** showing full pipeline
- Equation-by-equation explanation at each step
- Where classifier equations plug in
- **KEY FIX**: Flowchart now includes MAX_RETRIES to prevent infinite loop

**When to read**: To understand Learn++ algorithm step-by-step

---

#### [CLASSIFIER_EQUATIONS_DETAILED.md](CLASSIFIER_EQUATIONS_DETAILED.md)
**Purpose**: Every equation with symbol definitions
**Contains**:
- Part A: Learn++ equations (A1-A8)
- Part B: CompositeScore equation
- Part C: Weak classifier equations (6 models)
- Part D: Strong classifier equations (5 models)
- Symbol tables for each equation

**When to read**: When implementing classifiers or debugging math

---

#### [CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md](CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md)
**Purpose**: Numeric walkthroughs of key equations
**Contains**:
- Worked examples with real numbers
- Step-by-step calculations

**When to read**: To verify understanding or debug implementations

---

#### [CLASSIFIER_EQUATIONS.md](CLASSIFIER_EQUATIONS.md)
**Purpose**: Concise equation reference
**Contains**:
- Summary of key equations without extensive detail

**When to read**: Quick reference after reading detailed version

---

### 6. Handoff and Deliverables

#### [IMPLEMENTATION_HANDOFF.md](IMPLEMENTATION_HANDOFF.md) 🆕 ⭐
**Purpose**: Phase 1 → Phase 2 transition document
**Contains**:
- Executive summary (classifier/dataset selections)
- Detailed classifier specifications with performance
- Dataset specifications with preprocessing
- Learn++ configuration
- Updated assumptions and risks
- Open questions for Phase 2
- Reproducibility checklist
- Sign-off section
- **NEW DOCUMENT**: Fills critical gap in handoff process

**When to fill**: End of Phase 1, before Phase 2 begins
**When to read**: Phase 2 implementation team starting point

---

### 7. Reference Materials

#### [GLOSSARY.md](GLOSSARY.md) 🆕 ⭐
**Purpose**: Single-source term definitions
**Contains**:
- Project terms (batch, composite hypothesis, pairing, etc.)
- Learn++ terms (beta, epsilon, voting weight, etc.)
- Metrics (MacroF1, Balanced Accuracy, etc.)
- Data terms (BIDS, NIfTI, radiomics, etc.)
- Model names (all 11 candidates)
- Common confusions to avoid
- **NEW DOCUMENT**: Quick lookup for any technical term

**When to read**: Anytime you encounter unfamiliar terminology

---

#### [SCORECARD_TEMPLATE.md](SCORECARD_TEMPLATE.md)
**Purpose**: Template for experiment tracking
**Contains**:
- Table structure for recording results
- Required fields per experiment

**When to use**: During Phase 1 experiments

---

#### [SKILLS.md](SKILLS.md)
**Purpose**: Team capability assessment
**Contains**:
- Required skills for each phase
- Team readiness evaluation

**When to read**: For team planning and hiring decisions

---

### Appendix

#### [appendix/CLASSIFIER_EQUATIONS_SELF_CHECK.md](appendix/CLASSIFIER_EQUATIONS_SELF_CHECK.md)
**Purpose**: Practice exercises for equation understanding
**Contains**:
- 6 exercises with answer keys
- Interpretation tips

**When to use**: Optional - for team members learning equations

**Note**: Moved to appendix as it's educational supplement, not core documentation

---

## 📖 Topic Index

### Algorithm & Implementation
- Learn++ algorithm flow: [LEARNPP_EQUATION_FLOW_MAP.md](#5-algorithm-and-equations)
- All equations explained: [CLASSIFIER_EQUATIONS_DETAILED.md](#5-algorithm-and-equations)
- Worked examples: [CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md](#5-algorithm-and-equations)

### Classifiers
- Profiles of all 11 candidates: [CLASSIFIER_PROFILES.md](#3-experiment-design)
- Selection process: [CLASSIFIER_REFINEMENT_GUIDE.md](#3-experiment-design)
- Weak-strong pairing: [CLASSIFIER_PAIRING_STRATEGY.md](#3-experiment-design)

### Data
- Dataset selection: [DATA_STRATEGY.md](#4-data-planning)
- Batch design: [DATA_STRATEGY.md](#4-data-planning)
- Data contract: [DATA_STRATEGY.md](#4-data-planning)

### Experiments
- Experiment matrix: [EXPERIMENT_PLAN.md](#3-experiment-design)
- Metrics and scoring: [EXPERIMENT_PLAN.md](#3-experiment-design)
- Results tracking: [SCORECARD_TEMPLATE.md](#7-reference-materials)

### Phase Management
- Overall roadmap: [PHASED_ROADMAP.md](#1-start-here)
- Phase 1→2 handoff: [IMPLEMENTATION_HANDOFF.md](#6-handoff-and-deliverables)
- Action items: [TODO.md](#2-project-foundation)

### Risk & Assumptions
- Risk register: [RISKS.md](#2-project-foundation)
- Assumptions: [ASSUMPTIONS.md](#2-project-foundation)
- Decisions: [DECISION_LOG.md](#2-project-foundation)

### Terms & Definitions
- Glossary: [GLOSSARY.md](#7-reference-materials)

---

## 🔍 Finding Information Fast

### "I need to..."

- **Understand the project scope** → [PHASED_ROADMAP.md](#1-start-here)
- **Design experiments** → [EXPERIMENT_PLAN.md](#3-experiment-design)
- **Select classifiers** → [CLASSIFIER_PROFILES.md](#3-experiment-design) + [CLASSIFIER_REFINEMENT_GUIDE.md](#3-experiment-design)
- **Choose datasets** → [DATA_STRATEGY.md](#4-data-planning)
- **Understand Learn++ math** → [LEARNPP_EQUATION_FLOW_MAP.md](#5-algorithm-and-equations)
- **Look up a term** → [GLOSSARY.md](#7-reference-materials)
- **Start Phase 2 implementation** → [IMPLEMENTATION_HANDOFF.md](#6-handoff-and-deliverables)
- **Track experiment results** → [SCORECARD_TEMPLATE.md](#7-reference-materials)
- **See what's left to do** → [TODO.md](#2-project-foundation)

---

## 📁 File Organization

```
output_improved/
├── INDEX.md (this file)                     # Start here - navigation guide
├── PHASED_ROADMAP.md                        # High-level project phases
├── architecture.md                          # System architecture
│
├── ASSUMPTIONS.md                           # Working assumptions
├── RISKS.md                                 # Risk register
├── TODO.md                                  # Task checklist
├── DECISION_LOG.md                          # Historical decisions
│
├── EXPERIMENT_PLAN.md                       # Complete experiment specification
├── CLASSIFIER_PROFILES.md                   # All 11 candidate profiles
├── CLASSIFIER_PAIRING_STRATEGY.md          # Pairing recommendations
├── CLASSIFIER_REFINEMENT_GUIDE.md          # Selection process
│
├── DATA_STRATEGY.md                         # Dataset selection (concrete examples)
│
├── LEARNPP_EQUATION_FLOW_MAP.md            # Algorithm flow + equations
├── CLASSIFIER_EQUATIONS_DETAILED.md        # All equations with symbols
├── CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md # Numeric examples
├── CLASSIFIER_EQUATIONS.md                 # Concise equation reference
│
├── IMPLEMENTATION_HANDOFF.md               # Phase 1→2 handoff (NEW)
│
├── GLOSSARY.md                              # Term definitions (NEW)
├── SCORECARD_TEMPLATE.md                   # Results tracking template
├── SKILLS.md                                # Team capability assessment
│
└── appendix/
    └── CLASSIFIER_EQUATIONS_SELF_CHECK.md  # Practice exercises (optional)
```

---

## ✨ Key Improvements in This Version

### Documents Fixed
1. **LEARNPP_EQUATION_FLOW_MAP.md**: Mermaid flowchart now includes MAX_RETRIES (no infinite loop)
2. **CLASSIFIER_REFINEMENT_GUIDE.md**: Fixed "strong enough" terminology confusion
3. **CLASSIFIER_PAIRING_STRATEGY.md**: Added purpose/when-to-use context
4. **EXPERIMENT_PLAN.md**: Clarified experiment scope (6-8 experiments, not 18)
5. **DATA_STRATEGY.md**: Added concrete dataset examples with URLs

### Documents Created
6. **IMPLEMENTATION_HANDOFF.md**: NEW - Phase 1→2 transition template
7. **GLOSSARY.md**: NEW - Single-source term definitions
8. **INDEX.md**: NEW - This navigation guide

### Documents Reorganized
9. **CLASSIFIER_EQUATIONS_SELF_CHECK.md**: Moved to appendix/ (optional educational content)

---

## 🔄 Document Version Control

**Folder**: `output_improved/`
**Based on**: `output_gemini_3_1_pro/` + `output_claude_sonnet/`
**Review date**: 2026-05-03
**Status**: ✅ Critical issues fixed, ready for Phase 1 use

**Previous versions**:
- `output_gemini_3_1_pro/`: Original Gemini output
- `output_claude_sonnet/`: Original Claude output + FIXES_APPLIED.md

---

## 📞 Questions?

If you can't find what you're looking for:
1. Check [GLOSSARY.md](#7-reference-materials) for term definitions
2. Use Ctrl+F to search this INDEX.md for keywords
3. See [TODO.md](#2-project-foundation) for current action items

---

**Happy reading! 🚀**
