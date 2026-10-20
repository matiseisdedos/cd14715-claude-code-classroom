---
name: ticket-classification
description: Classify support tickets by urgency and category and decide routing and SLA. Use when analyzing tickets, determining priority, categorizing customer requests, or routing tickets to a team.
allowed-tools: Read, Grep, Glob
---

# Ticket Classification

This skill teaches Claude how to classify a customer support ticket by urgency (URGENT, HIGH, MEDIUM, LOW) and category (technical, billing, general), then route it and compute its SLA deadline.

## When to Use This Skill

Use this skill when the user requests:

- "Analyze the ticket in sample-tickets/technical.txt"
- "Classify all tickets in sample-tickets/ and route appropriately"
- "What priority is this ticket?"
- "Which team should handle this request?"

## Classification Matrix

### Urgency Levels

| Level      | Criteria                                                                             | Response Time |
| ---------- | ------------------------------------------------------------------------------------ | ------------- |
| **URGENT** | System down, security breach, data loss, users cannot log in                         | Immediate     |
| **HIGH**   | Core feature broken or production errors, or Enterprise customer with a real problem | < 1 hour      |
| **MEDIUM** | Feature degraded, workaround available, billing discrepancy                          | < 4 hours     |
| **LOW**    | How-to questions, minor bugs, feature requests                                       | < 24 hours    |

### Urgency Keywords

**URGENT indicators:**

- "system down", "outage", "can't login", "cannot log in"
- "breach", "unauthorized access", "data loss", "data deleted"

**HIGH indicators:**

- "error", "500", "broken", "not working", "production"
- "blocking", "ASAP", "affecting users"
- Customer is labeled Enterprise

**MEDIUM indicators:**

- "issue", "problem", "slow", "unexpected charge"
- "workaround", "intermittent", "I was expecting"

**LOW indicators:**

- "how do I", "question", "where can I find"
- "feature request", "not urgent", "planning"

### Category Classification

**Technical:**

- Error messages, stack traces, HTTP status codes
- API, endpoint, integration, database or performance problems

**Billing:**

- Invoices, charges, payments, refunds
- Subscription plans, pricing, usage limits

**General:**

- How-to and "where is" questions
- Account settings, feature inquiries, data export

## Classification Process

1. Read the ticket and extract subject, customer, tier (Enterprise or Standard), timestamp and description.
2. Ignore the ticket's own "Priority" field if it is "Not Set" or contradicts the content.
3. Match urgency keywords and assess real business impact (users affected, production, workaround).
4. Pick the category from the indicators; if mixed, choose the one that blocks the customer most.
5. Route: Technical → Engineering, Billing → Finance, General → Support (check KB first). URGENT → Escalation plus team lead alert. Enterprise + HIGH → senior support plus team alert.
6. Compute the SLA deadline: timestamp + 1 hour (Enterprise) or + 4 hours (Standard); URGENT is immediate.

## Examples

### Example 1: Technical - HIGH

```
Subject: 500 Errors on API Endpoint
Body: Acme Corp (Enterprise). /api/v2/users returns 500 for 2 hours,
about 5,000 users cannot reach their dashboards. "Database connection timeout".
```

**Classification:**

- Urgency: HIGH
- Category: technical
- Routing: Engineering (plus senior support alert, Enterprise + HIGH)
- Reasoning: Production errors with large user impact for an Enterprise customer. Not URGENT since the system is degraded for one endpoint rather than fully down.

### Example 2: Billing - MEDIUM

```
Subject: Question about invoice charges
Body: Small Business Co (Standard). Invoice is $500 but the Professional plan
is $400/month. Asks what the extra $100 is. Payment processes next week.
```

**Classification:**

- Urgency: MEDIUM
- Category: billing
- Routing: Finance
- Reasoning: Billing discrepancy with a deadline (next week), but no service impact. Standard tier gives a 4 hour SLA.

### Example 3: General - LOW

```
Subject: How to export data to CSV
Body: TechStartup Inc (Standard). Cannot find the export option; also asks
about JSON. "Not urgent, planning for next month."
```

**Classification:**

- Urgency: LOW
- Category: general
- Routing: Support (check KB for export docs first)
- Reasoning: How-to question with the customer stating it is not urgent.

## Output Format

Return the classification as JSON:

```json
{
  "urgency": "URGENT|HIGH|MEDIUM|LOW",
  "category": "technical|billing|general",
  "routing": "Engineering|Finance|Support|Escalation",
  "customer_tier": "Enterprise|Standard",
  "sla_deadline": "Timestamp or window when a response is due"
}
```

## Quality Checklist

Before finalizing classification, verify:

- [ ] Urgency is justified by evidence in the ticket, not the "Priority" field
- [ ] Category matches the main problem the customer needs solved
- [ ] Routing follows the rules, including escalation for URGENT and Enterprise + HIGH
- [ ] SLA deadline uses the correct tier window and the ticket's timestamp

## Tips

**Finding urgency indicators:**

- Look for numbers (users affected, hours of downtime, dollar amounts)
- Check for explicit customer statements like "not urgent" or "ASAP"

**Categorizing accurately:**

- Stack traces and status codes mean technical, even if the customer mentions money
- A charge question about plan limits is billing, not general

**Routing decisions:**

- Escalation overrides the normal team
- General tickets go to the KB check before a human

## Common Pitfalls

❌ **Don't:**

- Trust "Priority: Not Set" or a customer's tone alone
- Mark everything from Enterprise customers URGENT
- Invent timestamps or tiers that are not in the ticket

✅ **Do:**

- Use tier and impact together to choose HIGH vs. MEDIUM
- Include the exact SLA deadline
- Note the source ticket file for tracking
