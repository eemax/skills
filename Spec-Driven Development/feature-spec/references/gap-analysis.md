# Gap Analysis Taxonomy

After drafting the spec, scan it against every category below. For each, assess: **Clear** (well-defined), **Partial** (some detail missing), or **Missing** (not addressed at all).

Only escalate a gap to [NEEDS CLARIFICATION] if:
- Impact is high (changes scope, security, or UX significantly)
- Uncertainty is high (multiple reasonable interpretations exist)
- No industry-standard default applies

For everything else, make a reasonable default and document it in Assumptions.

---

## 1. Functional Scope (highest impact)

- Are the core user goals explicitly stated?
- Is out-of-scope declared? (What this feature deliberately does NOT do)
- Are user roles/personas differentiated? (If multiple actor types exist)
- Are success/failure outcomes defined for each goal?

**Common reasonable defaults**: Single user role unless multi-tenancy is mentioned. Standard CRUD unless specific workflows described.

## 2. Domain & Data

- Are entities, attributes, and relationships identified?
- Are identity and uniqueness rules clear? (What makes entity X unique?)
- Are lifecycle/state transitions documented? (draft → published → archived)
- Are data volume and scale assumptions stated?

**Common reasonable defaults**: Standard relational model. Soft deletes. Reasonable scale for the domain (e.g., thousands for internal tools, millions for consumer apps).

## 3. User Journeys

- Are critical paths (happy paths) fully described?
- Are error states defined? (What does the user see when something fails?)
- Are empty states defined? (First-time user, no data yet)
- Are loading/transition states mentioned?
- Are accessibility or localization requirements noted?

**Common reasonable defaults**: Standard error messages with recovery guidance. Empty states with call-to-action. No specific accessibility beyond WCAG 2.1 AA.

## 4. Security & Compliance

- Is the authentication model specified?
- Are authorization rules clear? (Who can do what?)
- Are data protection requirements stated? (PII handling, encryption at rest/transit)
- Are regulatory constraints mentioned? (GDPR, HIPAA, SOC2)

**Common reasonable defaults**: Session-based or OAuth2 auth for web apps. HTTPS. PII handling per standard practices. No specific compliance unless domain implies it (healthcare → HIPAA).

## 5. Non-Functional Quality

- Are performance targets stated? (Response time, throughput)
- Are scalability limits discussed? (Max users, data volume)
- Are reliability expectations set? (Uptime, recovery time)
- Are observability needs mentioned? (Logging, monitoring, alerting)

**Common reasonable defaults**: Sub-second response for interactive operations. 99.9% uptime for production services. Structured logging.

## 6. Integration & Dependencies

- Are external services/APIs identified?
- Are failure modes for external dependencies addressed?
- Are data import/export formats specified?
- Are versioning or backward-compatibility needs stated?

**Common reasonable defaults**: Retry with exponential backoff for external calls. JSON for data exchange. Graceful degradation on dependency failure.

## 7. Edge Cases & Failure Handling

- Are negative scenarios covered? (Invalid input, unauthorized access)
- Are concurrency conflicts addressed? (Two users editing the same thing)
- Are rate limiting or throttling behaviors defined?
- Are recovery procedures described? (What happens after a failure?)

**Common reasonable defaults**: Input validation with user-friendly error messages. Last-write-wins for concurrent edits unless collaboration is a feature. Standard rate limiting.

## 8. Terminology & Consistency (lowest impact)

- Are domain terms used consistently throughout?
- Are ambiguous adjectives quantified? ("fast" → under 500ms, "large" → over 10,000 items)
- Are deprecated or synonym terms identified?

**Common reasonable defaults**: Use the most specific term available. Replace every vague adjective with a number.

---

## Prioritization

When multiple gaps exist, prioritize questions by **Impact x Uncertainty**:

| | High Uncertainty | Low Uncertainty |
|---|---|---|
| **High Impact** | Ask immediately (NEEDS CLARIFICATION) | Make default, document prominently |
| **Low Impact** | Make default, document in Assumptions | No action needed |

Maximum 3 questions total. If more than 3 high-impact uncertainties exist, resolve the top 3 and document the rest as assumptions with explicit callouts.
