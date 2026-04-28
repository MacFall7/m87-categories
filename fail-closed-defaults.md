# Fail-Closed Defaults

A category property of AI Execution Risk Architecture.

## The Claim

When an AI agent encounters ambiguity, missing constraints, or conditions outside its sanctioned scope, the system halts. Not warns. Not escalates. Halts.

Every resume requires explicit human re-authorization, and that authorization becomes part of the cryptographic record.

This is not a behavior the model is asked to follow. It is a structural property of the runtime that the model cannot override.

## Why Fail-Open Is the Default

Production AI agents today are, almost universally, fail-open systems. They encounter ambiguity and proceed. They hit unclear policy and interpret. They receive conflicting signals and resolve them with best-guess completion.

Three reasons this default persists:

**Language models are optimized to produce output.** The training objective rewards completion. "I don't know" is a lower-probability token sequence than confident continuation. A model that halts on uncertainty is, by its own loss function, a worse model.

**Agent frameworks prioritize task completion.** Most production frameworks — LangChain, AutoGPT-pattern orchestrations, custom implementations — measure success by whether the agent finished. Half-completed tasks register as failures. The instrumentation rewards fail-open.

**The heritage is chat.** Chat systems are fail-open by design. If the model does not know the answer, it responds with its best attempt, and the human reads it and decides. This is appropriate for conversational assistance. It is catastrophic for autonomous action.

The failure mode is not visible at training time. It only appears in post-incident review: the agent hit an ambiguous instruction, proceeded with a best-guess interpretation, executed an action outside sanctioned scope, and the audit trail shows completion with no trace of the moment uncertainty became irreversible.

## The Three-Category Reference

Fail-closed is not new. It is load-bearing in three categories of consequential systems, each of which learned the lesson through catastrophe.

**Aerospace.** A commercial airliner's autopilot does not guess under degraded conditions. When GPS signal drops below threshold, when sensor disagreement exceeds tolerance, when input ambiguity crosses a defined line — the autopilot disengages and hands control back to the human pilot. The system would rather pause than confidently continue. This is why we trust commercial aviation: not because the systems are infallible, but because they fail in a shape that humans can recover from.

**Nuclear.** A reactor control system does not interpret unexpected sensor readings. It SCRAMs — drops control rods, halts the reaction, requires human re-authorization to restart. Every SCRAM is a documented event. Every restart produces a record. The control surface physically cannot continue operation outside validated parameters.

**Financial settlement.** A clearing system does not approximate ambiguous transactions. It rejects, halts, escalates. Every rejected transaction produces a receipt. Every resolved exception produces an audit trail. The system is deliberately more conservative than its users would prefer, because the cost of incorrect settlement compounds faster than the cost of delayed settlement.

All three categories share the same principle: systems with consequential authority cannot default to "proceed under uncertainty." The cost of fail-closed is felt in every operation; the cost of fail-open is felt once, catastrophically.

AI agents now sit in the same category. They execute actions with real-world consequences — code commits, financial transactions, customer communications, API calls to external services. But their default heritage pulls toward chat, and chat is fail-open.

## Runtime Enforcement Pattern

Fail-closed, as a structural property, has three components at runtime. All three are required; none are separable.

**Ambiguity detection at the control surface, not in the model.** The model may or may not recognize its own uncertainty. The runtime must have explicit tests — policy coverage, manifest alignment, scope validation — that run before execution. If any test fails, the runtime halts regardless of what the model proposes.

**Halt must be a structural state, not a soft warning.** The runtime cannot log a warning and proceed. It cannot escalate asynchronously and continue. It must physically stop the execution path and require authorization to resume. Soft warnings are fail-open in disguise.

**Resume requires authorization, and authorization is recorded.** When a human overrides a halt, the override is a first-class event in the audit trail: who authorized, when, under what constraints, with what rationale. The resumption itself produces a receipt. This closes the loop that makes audit possible.

A system with ambiguity detection but no structural halt is fail-open with observability. A system with halt but no authorization record is fail-closed with no audit chain. Only the full pattern produces the property.

## Reference Implementation

Spine Lite, M87 Studio's open-source governance layer for Claude Code, implements fail-closed defaults at the session boundary. Every tool invocation is validated against a declared manifest before execution. Tools outside the manifest do not execute. Ambiguous matches halt. Every halt and every override produces a receipt in the session log.

The manifest is declarative — not a policy document, but a runtime constraint. The model proposing the tool invocation cannot modify the manifest. The control surface enforcing the manifest cannot execute without its approval. This is the Proposal ≠ Execution split applied at the session level.

Source: github.com/MacFall7/spine-lite (MIT license).

## The Cultural Cost

Fail-closed is operationally expensive in a way fail-open is not.

Users hit halts more often. Operators get paged for ambiguity events. Agents appear "broken" when they actually encountered exactly the conditions they were designed to handle. Teams deploying fail-closed systems consistently report the same pattern: for the first six to eight weeks, halt events dominate the operational load. After that, one of two things happens. Either the policy coverage matures and halts decrease, or the team realizes how much unsanctioned action the prior fail-open system was taking.

Both outcomes are wins.

The first produces a stable, production-ready system with a provable authorization chain. The second produces evidence — often dramatic evidence — that the organization was operating with a much larger execution risk than anyone recognized.

A system that never halts is a system that cannot prove when it should have halted. Every unrestricted execution is an unprovable authorization chain. Every unprovable authorization chain is a latent audit failure.

## Sources

- Gravitee, *State of AI Agent Security Report 2026* — "57.4% of builders cite lack of logging and audit trails as a primary obstacle to agent governance." https://www.gravitee.io/state-of-ai-agent-security

- Nancy Leveson, *Engineering a Safer World: Systems Thinking Applied to Safety* (MIT Press, 2011) — Foundational text on fail-closed design in safety-critical systems; extensive aerospace and nuclear case studies.

- Cloud Security Alliance, *Unknown AI Agents in Enterprise Environments Survey* (April 2026) — "82% of enterprises have unknown AI agents operating in their environments." https://cloudsecurityalliance.org/press-releases/2026/04/21/new-cloud-security-alliance-survey-reveals-82-of-enterprises-have-unknown-ai-agents-in-their-environments

---

M87 Studio LLC — Category Property Series, 2 of 5

