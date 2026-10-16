# Logging, monitoring and alerting

## Principle

**(secure by design)**

Systems should generate appropriate security-relevant logs, retain them for a defined period and protect them from tampering, so that they can support investigation and compliance needs.

## Objective

Provide sufficient visibility to detect misuse, investigate incidents and maintain system integrity over time. The focus is on a small set of high-signal security events, reliable log retention and actionable alerts that do not overwhelm teams.

## Checklist

### Define the “must-log” security events

- Authentication: login success/failure; MFA events; token issuance/refresh/revocation.
- Authorisation: access denied, privilege or role changes, admin actions.
- Boundary events: external API calls, device onboarding or pairing, OTA/update events.
- Data or security changes: configuration changes, key or secret changes, user or service enabled or disabled.
- Do not log personal or secret data by default. In general, log only the bare minimum required to understand events in default log level

### Standardise access and audit log structure and attribution

- For access and authorisation logging, use consistent fields capturing who (actor), what (action or event type) and when (timestamp), with additional contextual fields as appropriate.
- Ensure that admin actions are attributable to a named identity (no shared accounts).

### Attribute actions taken by non-human actors

- Where an automated component takes actions in the product on its own determination, including an AI component that invokes tools, writes to storage or executes code, record which component acted and under whose authority, in addition to the credential used.
- Do not rely on a service account alone for attribution. A shared service credential identifies the channel and not the actor, and several components behind one credential produce records that cannot be told apart afterwards.
- Record, for each such action: the component and its version, the credential used, the human or system authority the action was taken under, the action, the outcome and the time.
- Keep the record of what an automated component did outside the write path of that component. A component that can modify its own record produces evidence about itself that nobody outside can rely on.
- Where a record of automated actions may be read by a party outside the organisation, for example a customer, an integrator or an auditor, make its integrity and origin checkable without asking the organisation that produced it. Signed structured records over a canonical serialisation are one existing means.

### Centralise collection and protect log integrity

- Central log store (SIEM/log platform or managed logging).
- Separate logs based on type and/or service.
- Restrict access to logs; separate duties and apply write-only permissions where feasible.
- Set retention targets (e.g. 30 days or 90 days, longer for archival material if required). Plan storage accordingly.
- Ensure time sync (NTP) across systems.

### Implement actionable monitoring and alerts

- Start with a small alert set (high confidence, high impact): repeated auth failures, new admin creation, privilege escalation, unusual API error spikes, updates failing, disabled logging, access to sensitive endpoints from new locations.
- Tune alerts to reduce noise (rate thresholds, suppression windows, environment filters).

### Practice incident triage and response

- Define on-call duties / ownership for security alerts.
- Document a minimal triage playbook covering: validate, scope, contain, remediate and communicate.
- Carry out a quarterly tabletop exercise or dry run for one common scenario (account takeover or API key leak).

## Minimum evidence

- Logging baseline: list of must-log events and required fields (one page)
- Central logging proof: screenshot/export/config showing logs from key components arriving centrally
- Retention setting: configured retention is in place and there is an access-control policy for logs
- Alert catalogue: list of enabled alerts with thresholds and owners (initial set can consist of around 5 to 10 alerts)
- Triage runbook: one-page steps and escalation contacts, plus one recent test (tabletop note)
- Automated-actor inventory: list of components that take actions in the product without a person in the loop, the credential each uses and the record each produces (one page)

## Release gate

- [ ] Must-log security events implemented for new/changed components (auth, admin, updates, boundary events)
- [ ] Logs include attribution fields (actor, request/session ID, source) and are centralised
- [ ] Log access restricted, retention configured, time sync verified
- [ ] High-signal alerts enabled and assigned an owner (at least brute force events, new admin / role change, key/secret change, update failures)
- [ ] Triage runbook exists and has been tested recently (tabletop exercise or test alert)
- [ ] Actions taken by automated components are attributable to the component and not only to a shared credential, and their records are outside those components' write path
- [ ] Logging/alerting exceptions are documented, with owner and review/expiry date
