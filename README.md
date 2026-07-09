# VIVERE

*A self-optimizing landing page with autonomous conversion optimization powered by Claude.*

## Overview

Vivere is a closed-loop system that watches real visitor behavior on a live landing page, reasons about what's underperforming, and rewrites the page's copy without a human in the loop.

The core hypothesis is that an LLM with the right behavioral context can perform continuous conversion optimization more effectively than static A/B testing or manual iteration.

The system is built around a pipeline of specialized agents, each with a narrow, well-defined responsibility. Claude sits at the center of the reasoning layer. Rather than simply generating copy, it interprets structured analytics data and produces a verdict that a downstream agent is constrained to execute.

---

## What's Working Today (MVP1)

The current build is a functioning two-agent loop running end-to-end.

### Data Layer

A background trigger service polls PostHog every 30 seconds using HogQL queries to collect:

- Session counts
- CTA click rates
- Scroll depth distribution
- Section-level engagement

Once enough behavioral signal has accumulated, it automatically starts an optimization cycle.

### Analyst Agent (Claude)

The Analyst Agent receives both the behavioral metrics and the current landing page copy. It identifies underperforming elements and returns a strictly typed verdict containing:

- A high-level summary
- Underperforming elements
- Up to three proposed copy changes
- A rationale for each recommendation

### Builder Agent

The Builder Agent applies only validated, schema-checked copy changes.

Its permissions are intentionally restricted:

- Cannot modify source files
- Cannot restart the server
- Can only write to two database tables
- Preserves complete before/after history for every change

### Reporting

Every completed optimization cycle automatically generates a self-contained HTML report and emails it out, making every autonomous change fully auditable.

### Live Updates

The frontend polls for updated copy and optimization logs in near real time, allowing visitors to see changes appear as the system reasons about performance.

The current implementation has successfully completed multiple end-to-end optimization cycles against simulated traffic, with Claude consistently identifying weak CTAs and headline copy before proposing constrained, on-brand rewrites.

---

## Where It's Going

MVP1 validates the reasoning loop. The long-term architecture expands this into a four-agent system.

### 1. Fetch Agent

Pulls and normalizes behavioral data from PostHog.

*Status: Implemented.*

### 2. Analyst Agent

Interprets behavioral data into a diagnosis of what is underperforming and why.

*Status: Implemented (powered by Claude).*

### 3. Architect / Designer Agent

A dedicated conversion optimization and UX specialist that transforms the Analyst's diagnosis into a complete improvement plan.

Instead of suggesting only copy changes, it proposes improvements such as:

- Layout changes
- User flow improvements
- Onboarding optimizations
- Conversion-focused UX refinements

### 4. Builder Agent (E2B Sandboxed)

Rather than updating database copy strings, this agent operates on the full frontend codebase inside an isolated E2B sandbox.

It:

- Implements the Architect's proposed changes
- Runs entirely inside an isolated environment
- Must pass an automated evaluation pipeline
- Only promotes successful changes to production

This transition from editing database values to safely modifying production code is the primary technical challenge currently under development.

It also fundamentally changes the system's safety model by introducing:

- Sandboxed execution
- Automated evaluation gates
- Sequential statistical testing
- Minimum sample size requirements
- Holdout traffic
- Confidence-based deployment before any change is trusted without supervision

---

## Why I'm Not Sharing the Repository

This is the one area where I'm intentionally cautious.

The core pipeline, including the Analyst Agent's prompting strategy, the verdict schema, the Builder Agent's constrained execution model, and the broader architecture for sandboxed codebase-level optimization, represents what I believe is the product's primary intellectual property rather than simply a hackathon implementation.

Because of that, I'd prefer not to publish the implementation details in a public repository before I've had the opportunity to develop the product further.

I'm more than happy to walk through:

- The system architecture
- The prompting strategy
- The schema design
- A live demonstration of the optimization loop

I simply don't want the implementation documented in a way that's easily reproducible before the product reaches a more mature stage.

If repository access is a hard requirement for the application, I completely understand. I wanted to explain my reasoning openly rather than avoid the question.
