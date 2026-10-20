---
description: "Company support tickets system using multi-agent architecture"
tools:
  - bash
  - read
  - grep
  - glob
---

# Support Ticket System

This file configures Claude code for an AI-powered company support tickets system capable of analyzing, classifiying, routing and monitoring SLA. using specialized subagents based on the requirements of the tickets

## Project Overview

An intelligent company Support tickets system that automatically analize, classifies and routes the tickets for a faster resolution. It targets bot support teams by assigning the right tickets to the right teams and customers to speed up resolution or resolve the tickets (if possible without human intervention)

- What it does (analyzes and routes 5,000+ tickets daily)
- Main features (classification, routing, SLA monitoring)
- Target users (support teams, customers)

## Architecture

<!-- TODO: Document your architecture -->

This system uses specialized subagents for efficient parallel analysis, clasification, routing and monitoring:

- **TODO**: Describe your subagent(s)
  **triage**: Gathers incomming tickets, determines urgency P0-P3, type (tech/billing/general), customer tier (Enterprise/Standard) and assigns it (Haiku - fast - always)
  **technical**: Analyze error messages, stack traces, api issues, integration problems (sonnet - if required)
  **billing**: Analyze payment failures, subscription issues, invoice questions account changes (sonnet - if required)
  **base-knowledge**: Searchs for similar cases, documentation, common solutions, FAQS (haiku - fast - always run)
  **routing**: decide can auto resolve? Which human team? Escalation needed? (sonnet - always run)
  **sla-monitor**: monitoring open tickets, time remaining, auto escalate if less than 20 mins (haiku - fast - every 10 minutes)

- **tickets-analysis skill**: Teaches Claude how to structure each ticket should flow through the system

- **TODO**: Describe your skill(s)

## How to Use

<!-- TODO: Add usage examples -->

```bash
# TODO: Add example command to analyze a single ticket
claude "Analyze [billing.txt] ticket"

# TODO: Add example command to classify all tickets
claude "Analyze and classify all tickets in sample-tickets"

# TODO: Add example command to check SLA status
claude "Check SLA status of the tickets"
```

## Ticket Categories

- **Technical**: Bugs, errors, API issues, integrations, performance problems
- **Billing**: Payments, invoices, refunds, subscriptions, pricing questions
- **General**: How-to questions, account settings, feature inquiries

## SLA Requirements

- **Enterprise customers**: < 1 hour response time
- **Standard customers**: < 4 hours response time
- **All URGENT tickets**: Immediate escalation regardless of tier
- **Business hours**: Mon-Fri 9am-5pm (add 24/7 for enterprise critical)

## System Instructions

When analyzing support tickets:

1. **Use the ticket-classification skill** to apply the classification matrix
2. **Delegate to ticket-analyzer subagent** for structured analysis
3. **Check KB with kb-searcher** to find existing solutions before routing
4. **Calculate SLA deadline** using the sla-calculation skill
5. **Escalate immediately** if SLA is at risk (<20% time remaining)

Always:

- Provide actionable categorization (not just analysis)
- Include routing recommendation
- Calculate exact SLA deadline
- Check for escalation triggers
- Note the source ticket file for tracking

## Urgency Classification Guide

| Level      | Response Time | Triggers                                              |
| ---------- | ------------- | ----------------------------------------------------- |
| **URGENT** | Immediate     | "system down", "can't login", "breach", "data loss"   |
| **HIGH**   | < 1 hour      | "error", "broken", "not working", enterprise customer |
| **MEDIUM** | < 4 hours     | "issue", "slow", "problem" with workaround            |
| **LOW**    | < 24 hours    | "how do I", "question", "feature request"             |

## Routing Rules

- **Technical issues** → Engineering team
- **Billing questions** → Finance team
- **General questions** → L1 Support (check KB first)
- **URGENT anything** → Escalation + team lead alert
- **Enterprise + HIGH** → Senior support + team alert

## Build Commands

```bash
# If this were a real project
npm install
npm test
npm run classify-tickets
```

## Key Takeaway

<!-- TODO: Write what this configuration demonstrates -->

This configuration shows how to:

- TODO: First learning point
- TODO: Second learning point
- TODO: Third learning point
