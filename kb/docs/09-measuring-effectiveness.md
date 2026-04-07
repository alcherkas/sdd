# Measuring SDD Effectiveness

> Last verified: 2026-04-04

---

## Principle

If you cannot measure it, you cannot improve it. SDD effectiveness should be tracked through concrete metrics tied to observable outcomes -- not gut feeling, not anecdotes, not "the team feels more productive." Every metric must have a collection method, a target, and a cadence.

---

## Key Metrics

| Metric | What It Measures | How to Collect | Target |
|--------|-----------------|----------------|--------|
| **First-pass acceptance rate** | % of generated code passing review without changes | PR review data (approved without revision requests) | >70% |
| **Spec-to-code time** | Time from spec completion to working, reviewed code | Task tracking (timestamps on spec-complete and PR-merge) | <50% of equivalent manual coding time |
| **Rework rate** | % of generated code requiring manual fixes post-generation | Commit history (count fix/refactor commits after initial generation) | <30% |
| **Bug density** | Defects per KLOC in generated vs manually written code | Bug tracking system, tagged by code origin | Equal to or lower than manual baseline |
| **Spec coverage** | % of codebase with associated specifications | File mapping (spec-to-code index) | >80% for critical paths |
| **First-generation test pass rate** | % of spec-derived tests passing on the first generation run | CI/CD logs (first pipeline run per generation) | >60% |

### Metric Collection Notes

**First-pass acceptance rate** is the single most informative metric. A high first-pass rate means specs are clear, constraints are sufficient, and the generation pipeline is producing output that matches intent. A low rate indicates problems upstream -- ambiguous requirements, missing constraints, or inadequate context.

**Rework rate** captures the hidden cost of SDD done poorly. If developers spend as much time fixing generated code as they would writing it manually, SDD is adding overhead without value. Track this by tagging commits as `generated`, `manual-fix`, or `manual-authored` and computing the ratio.

**Spec coverage** is not about 100% coverage everywhere. It is about ensuring that the parts of the codebase where correctness matters most -- API contracts, data models, security-critical paths -- have associated specifications. Utility functions and glue code may not warrant specs.

---

## Before/After Comparison Framework

Adopting SDD without a baseline measurement makes it impossible to demonstrate impact. Capture these metrics BEFORE writing your first spec.

### Baseline Metrics (Capture Before Adoption)

| Metric | Collection Method | Notes |
|--------|------------------|-------|
| Average PR cycle time | VCS analytics (time from PR open to merge) | Measures review + revision overhead |
| Bug introduction rate | Bug tracker (new bugs per sprint/week) | Baseline for generated-code comparison |
| Developer hours per feature | Time tracking or estimation data | Denominator for ROI calculation |
| Rework frequency | Commit history (fix commits within 48h of initial implementation) | Captures "it works, but..." revisions |
| Onboarding time | Time for new developer to deliver first feature | Baseline for knowledge-preservation benefit |
| Test coverage | CI/CD coverage reports | Baseline for spec-derived test comparison |

### Comparison Cadence

| Checkpoint | What to Measure | What to Look For |
|-----------|----------------|-----------------|
| **30 days** | Spec-to-code time, first-pass acceptance rate | Early signal. Expect 20-30% improvement in generation speed. First-pass rate may initially drop as the team learns to write effective specs. |
| **60 days** | Rework rate, bug density | Meaningful comparison. Rework rate should show decline. Bug density comparison requires sufficient sample size -- at least 5-10 generated features. |
| **90 days** | All metrics, full ROI calculation | Comprehensive assessment. Team has adapted to the workflow. Compare all metrics against baseline. This is the decision point for expanding SDD adoption. |

### What "Worse Before Better" Looks Like

Teams commonly see a productivity dip in the first 2-3 weeks. Writing specs takes time the team is not accustomed to spending. First-pass acceptance rates may be low while the team calibrates spec granularity. This is expected. The 30-day checkpoint exists to distinguish "learning curve" from "fundamentally not working."

---

## ROI Model

SDD requires upfront investment (writing and maintaining specs) in exchange for downstream returns (reduced rework, fewer bugs, faster onboarding). The model below provides a framework for estimating net impact.

### Investment vs Return

| Category | Investment (Cost) | Return (Savings) |
|----------|------------------|------------------|
| **Spec writing** | 15-25% of total feature time spent on specification | Reduced ambiguity eliminates 60-80% of "that's not what I meant" rework cycles |
| **Spec maintenance** | 5-10% ongoing overhead to keep specs current | Living documentation eliminates stale-doc maintenance and onboarding friction |
| **Tooling and workflow** | Initial setup of spec templates, CI integration | Automated verification catches issues at generation time rather than in production |
| **Team training** | 1-2 weeks of reduced velocity during adoption | Consistent methodology reduces coordination overhead across team members |

### Typical ROI by Project Type

| Project Type | Spec Investment | Time Saved | Net Effect | Break-Even Point |
|-------------|----------------|-----------|------------|-----------------|
| **Greenfield application** | High (25% of feature time) | High (50-70% faster implementation after spec) | Strongly positive | 2-3 features |
| **API/integration layer** | Medium (20% of feature time) | Very high (contracts map directly to specs) | Strongly positive | 1-2 features |
| **Legacy system extension** | High (30%+ due to documenting existing behavior) | Medium (40-50% faster for new additions) | Positive after initial investment | 4-6 features |
| **Data pipeline** | Medium (15-20% of feature time) | High (schema specs prevent data-quality issues downstream) | Positive | 2-4 features |
| **UI/frontend** | Low-medium (15% of feature time) | Medium (visual specs are harder to formalize) | Modest positive | 3-5 features |

### Simplified ROI Formula

```
Net ROI = (Hours saved from reduced rework + Hours saved from fewer bugs +
           Hours saved from faster onboarding) - (Hours spent writing specs +
           Hours spent maintaining specs + Hours spent on tooling)
```

For a team of 5 developers on a 6-month project, typical observed values:

| Factor | Hours per Month |
|--------|----------------|
| Spec writing and maintenance | 40-60 hours (team total) |
| Reduced rework | 30-50 hours saved |
| Reduced bug investigation and fixing | 15-25 hours saved |
| Faster onboarding (amortized) | 5-15 hours saved |
| **Net monthly savings (after ramp-up)** | **10-30 hours** |

These numbers compound. As spec coverage increases and the team's spec-writing improves, the investment decreases while returns increase.

---

## Leading vs Lagging Indicators

Leading indicators predict future outcomes. Lagging indicators confirm past results. Track both -- but act on leading indicators, because by the time lagging indicators show problems, the damage is done.

### Leading Indicators

| Indicator | What It Predicts | How to Measure |
|-----------|-----------------|----------------|
| **Spec quality score** | First-pass acceptance rate, rework rate | Rubric-based scoring: completeness of acceptance scenarios, constraint specificity, absence of [NEEDS CLARIFICATION] markers |
| **Spec review thoroughness** | Bug density, specification drift | Review metrics: time spent on spec review, number of comments per spec, revision count before approval |
| **Constraint completeness** | Generation accuracy, edge-case coverage | Count of explicitly stated constraints vs constraints discovered during implementation (the latter indicates spec gaps) |
| **Spec-to-test traceability** | Test coverage, verification confidence | Percentage of acceptance scenarios with corresponding executable tests |
| **Context provision completeness** | Generation relevance, hallucination rate | Whether specs include all referenced interfaces, dependencies, and examples needed for generation |

### Lagging Indicators

| Indicator | What It Confirms | How to Measure |
|-----------|-----------------|----------------|
| **Production bug rate** | Overall SDD effectiveness | Bugs per release in spec-driven vs non-spec-driven code |
| **Rework cycles** | Spec and constraint quality | Number of generate-review-fix cycles before acceptance |
| **Deployment frequency** | Development velocity impact | Deployments per week/month, compared to baseline |
| **Mean time to resolution** | Specification clarity and debuggability | Time from bug report to fix, comparing spec-driven and manual code |
| **Developer satisfaction** | Workflow sustainability | Periodic surveys on perceived productivity and frustration points |

### The Feedback Relationship

Leading indicators drive lagging outcomes:

```
Spec quality score (leading) --> First-pass acceptance rate (lagging)
Constraint completeness (leading) --> Bug density (lagging)
Spec review thoroughness (leading) --> Rework cycles (lagging)
```

When lagging indicators deteriorate, investigate leading indicators first. A rising bug density is a symptom -- the cause is usually declining spec quality or insufficient constraint coverage.

---

## Anti-Patterns in Measurement

| Anti-Pattern | Why It Fails | What to Do Instead |
|-------------|-------------|-------------------|
| Measuring only lines of code generated | Volume without quality is meaningless | Measure first-pass acceptance rate and rework rate |
| Comparing AI speed to human speed without quality adjustment | Faster generation of buggy code is not faster development | Include rework time in all speed comparisons |
| Treating all code as equal | A 50-line authentication module matters more than a 500-line CRUD controller | Weight metrics by criticality of the code area |
| Measuring spec coverage without spec quality | 100% coverage with vague specs is worse than 50% with precise ones | Combine coverage with quality scoring |
| Abandoning measurement after initial adoption | SDD effectiveness changes as the team and codebase evolve | Maintain monthly metric reviews indefinitely |

---

## References

1. Forsgren, N., Humble, J., & Kim, G. *Accelerate: The Science of Lean Software and DevOps* -- DORA metrics framework adapted for AI-assisted development measurement.
2. CodeRabbit. "State of AI in Code Review." December 2025 -- Empirical data on AI-generated code quality and issue rates.
3. Veracode. "State of Software Security 2026." -- Security defect density in AI-generated vs human-authored code.
4. GitHub. "Octoverse 2025: The State of Open Source and AI." -- Developer productivity metrics with AI tooling.
5. Martin Fowler. "Measuring Developer Productivity." -- Framework for avoiding vanity metrics in software measurement.
