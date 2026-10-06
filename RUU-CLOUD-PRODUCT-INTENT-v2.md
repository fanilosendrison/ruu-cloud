# Ruu Cloud — Product Intent

## Status

This document defines the governing product intent for **Ruu Cloud**.

It is not a detailed architecture or implementation specification. Its purpose is to state the product-level objective that future architecture, protocols, APIs, UX, pricing, and implementation decisions must preserve.

If a lower-level design is internally coherent but conflicts with this intent, the lower-level design is wrong.

---

## 0. Core / Cloud invariants

The Core / Cloud boundary must remain principled.

> **Never weaken Core to create Cloud value.**

> **Cloud may extend the coordination domain and operate it for you; it must not become the source of fundamental correctness properties that Core lacks.**

> **Given the same facts and policies, Core and Cloud must derive the same mechanical truth.**

Ruu Core provides the complete **single-host** version-control capability and all fundamental guarantees required to trust Ruu:

- safety;
- exact-state reasoning;
- authority;
- provenance;
- causal replay;
- responsibility attribution;
- inspection;
- machine-readable evidence;
- deterministic recovery semantics and mechanics.

Ruu Cloud extends the coordination domain across the team and operates the resulting shared system as a managed service.

Cloud may know more because its observation domain is wider.

It must not reason differently.

```text
                   RUU SEMANTICS
                        │
               same correctness model
                 ┌──────┴──────┐
                 │             │
              CORE           CLOUD
                 │             │
           local facts    team-wide facts
                 │             │
           local result   broader result
```

If Core and Cloud are given the same facts and policies, their mechanical conclusion must be the same.

Cloud value must come from the expanded coordination domain and the operational burden it removes, not from withholding correctness, evidence, inspection, or recovery guarantees from Core.

---

## 1. Product thesis

Ruu Core makes aggressive concurrent software development safe within a single coordination domain on one host.

Ruu Cloud extends that same model from **one host** to **an entire development team operating across multiple hosts**.

The central product idea is:

> **Every developer in a team should be able to work concurrently from their own machine, with humans and coding agents modifying the same repositories — including the same files — without having to coordinate Git ownership in advance and without creating operational collisions.**

The deeper goal is not merely safer concurrency.

> **Ruu Cloud should make software development more fluid by preventing branch, PR, and merge boundaries from becoming artificial scheduling barriers between pieces of work.**

Work that is already safely available should become usable by dependent work before it has completed its full publication lifecycle.

The operational consequence is fundamental:

> **Every authoritative checkpoint should be propagated to the furthest safely reachable shared state as soon as possible, so useful work becomes available to the rest of the team immediately rather than waiting for contribution completion.**

Ruu Cloud is not primarily a repository host, a code review product, a project-management tool, or a replacement for GitHub/GitLab.

It is the shared coordination layer that turns multiple independent Ruu hosts into one coherent Ruu convergence domain and allows team development state to **continuously converge while work is still in flight**.

---

## 2. Relationship to Ruu Core

Ruu Cloud must not exist because Ruu Core is intentionally crippled.

Ruu Core is the complete local product.

A user running Ruu Core on one host must retain the full local guarantees of Ruu:

- multiple concurrent coding sessions;
- multiple agents;
- multiple repositories;
- overlapping work on the same logical files;
- isolated authoring contexts;
- checkpointing and convergence;
- deterministic Git progression where mechanically possible;
- explicit reconciliation when semantic authoring is required;
- crash/retry recovery;
- repository-specific promotion policy;
- publication through Git remotes/providers;
- safe simultaneous local invocations.

Ruu Core remains free/fair-code.

Ruu Core must also remain fully inspectable and recoverable at local scale. Human-readable inspection, machine-readable causal state, deterministic recovery planning/execution, and the interfaces required to connect external agents or models must not be withheld to create Cloud value.

A capable Core installation may expose locally:

```text
CLI
local UI
inspection API
causal query API
recovery-plan API
structured schemas
tool definitions for external agents/models
```

This allows users to understand and operate Ruu locally at high agent counts without requiring Ruu Cloud.

Future local authoring substrates may evolve. For example, Ruu Core V2 may allow isolated authoring environments to be backed by VMs instead of, or in addition to, Git worktrees.

That remains Ruu Core.

The commercial boundary is **not**:

> worktrees are free, VMs are paid.

The meaningful boundary is:

> **one host-local coordination domain versus one coordination domain spanning multiple independent hosts.**

Conceptually:

```text
RUU CORE

one host
├── session A
├── session B
├── session C
├── local worktrees / VMs
└── one host-local Ruu coordination domain
```

versus:

```text
RUU CLOUD

one team
├── developer host A
│   ├── sessions
│   └── agents
├── developer host B
│   ├── sessions
│   └── agents
├── developer host C
│   ├── sessions
│   └── agents
└── one shared Ruu coordination domain
```

Ruu Cloud therefore scales the **coordination domain**, not the feature set.

---

## 3. The problem with the conventional team workflow

The conventional Git workflow handles team concurrency largely by combining technical isolation with human coordination.

A simplified form is:

```text
stand-up / Slack / issue tracker
        ↓
decide who works on what
        ↓
developer A creates branch A
developer B creates branch B
developer C creates branch C
        ↓
each develops mostly independently
        ↓
each opens a PR
        ↓
GitHub / GitLab discovers integration state
        ↓
review / CI / merge queue / conflict resolution
```

This works, but it carries an important hidden assumption:

> **humans help schedule concurrency before the version-control system has to deal with it.**

Teams routinely reduce conflict by coordinating ownership informally:

- "Alice is already changing that subsystem."
- "Wait until Bob's PR lands."
- "Don't touch that file yet."
- "Base your branch on mine."
- "Rebase after this PR merges."
- "We should split these tasks so we don't step on each other."
- "Let's discuss who owns which part in the morning."

Some of that coordination is genuinely semantic and useful.

Some of it exists only because concurrent version state is difficult to manage.

Ruu Cloud targets the second category.

---

## 4. Coordination that Ruu Cloud should remove

Ruu Cloud does **not** aim to eliminate product coordination.

A team may still need to decide:

- which features matter;
- what the architecture should be;
- who is responsible for a product area;
- what should ship;
- what trade-offs are acceptable;
- whether two intentions are semantically compatible.

Those are development and product decisions.

Ruu Cloud aims to eliminate or greatly reduce **version-control coordination imposed by concurrent authoring**.

The team should not have to coordinate in advance merely to answer questions such as:

- Who is currently touching this file?
- May I start working here?
- Should I wait for another branch to merge first?
- Which branch do I need to base my work on?
- Which developer owns this mutable Git state?
- In which order do we need to merge these independent branches?
- Will starting this work now corrupt or collide with another active authoring context?

The default should become:

> **Start the work. Ruu will isolate it, observe it, converge what is mechanically compatible, and surface only the incompatibilities that actually require semantic resolution.**

---

## 5. From preventive coordination to actual-conflict coordination

The conventional workflow often uses **preventive coordination**:

```text
possible future conflict
        ↓
humans coordinate beforehand
        ↓
reduce concurrency
```

Ruu Cloud should instead enable:

```text
independent concurrent authoring
        ↓
Ruu continuously reasons over exact managed state
        ↓
compatible work converges
        ↓
only real incompatibilities become reconciliation obligations
```

The goal is not to pretend conflicts do not exist.

If two developers or agents make genuinely incompatible changes to the same behavior, the conflict is real and must be resolved.

For example:

```text
Developer A:
login() now returns User

Developer B:
login() now returns Session
```

No version-control system can truthfully declare both semantics correct without understanding or changing the intended design.

Ruu Cloud's responsibility is different:

> **No operational collision, shared-checkout corruption, stale-authority race, or manual Git scheduling should be required merely because both pieces of work started concurrently.**

The semantic incompatibility should be surfaced when it becomes real.

---

## 6. Same repositories, same files, different hosts

The defining Ruu Cloud scenario is intentionally aggressive.

Assume three developers on three machines:

```text
Fanilo / Host A
Alice  / Host B
Bob    / Host C
```

At the same time:

```text
Fanilo changes:
src/auth.rs
src/api.rs

Alice changes:
src/auth.rs
src/database.rs

Bob changes:
src/auth.rs
```

All three may also have coding agents working concurrently.

Ruu Cloud must not require them to reserve `src/auth.rs`, serialize their work manually, or share one mutable checkout.

Each active contribution must retain isolated mutable authoring state.

Conceptually:

```text
Host A ── Contribution A ─┐
                          │
Host B ── Contribution B ─┼── shared ConvergenceUnit
                          │
Host C ── Contribution C ─┘
```

If the exact changes are mechanically compatible, they should converge without human intervention.

If semantic authoring is required, the affected state should become an explicit reconciliation obligation while unrelated progress continues.

The important product property is:

> **Concurrent overlap is allowed by default. Corruption is not.**

---

## 7. Safe checkpoints should propagate immediately

A checkpoint is not merely a private save point for the developer who produced it.

Once a checkpoint becomes authoritative, Ruu Cloud should treat it as **potentially useful team state**.

The default behavior should therefore be:

```text
authoritative checkpoint appears
        ↓
advance it through every currently safe Ruu transition
        ↓
publish the resulting managed Git state to the shared remote
        ↓
make that exact state discoverable by the rest of the team
        ↓
stop only at the first real blocker
```

In other words:

> **A commit should become useful to the rest of the team as soon as safely possible.**

This does not mean "merge everything immediately."

It means:

```text
if checkpoint is safe:
    adopt checkpoint

if checkpoint publication is safe:
    publish checkpoint

if convergence is mechanically safe:
    converge

if the converged state can be published safely:
    publish it

if promotion is currently authorized:
    continue

otherwise:
    stop at the exact blocking boundary
```

Ruu Cloud should continuously push managed state to the **furthest safely reachable point**, not wait for a human to decide that a contribution is "finished enough" to share.

### 7.1 Checkpoint availability is not contribution completion

These concepts must remain distinct:

```text
checkpoint exists and is globally usable
≠
ContributionUnit CLOSED
≠
task complete
≠
PR approved
≠
merged to main
```

For example:

```text
Alice ContributionUnit: OPEN

A0
 ↓
A1  ← authoritative checkpoint
 ↓
A2  ← later checkpoint
 ↓
A3
```

As soon as `A1` is authoritative and safely publishable, Ruu Cloud should make it available to the rest of the team.

Alice may continue working toward `A2` and `A3`.

Other work should not be forced to wait for Alice's ContributionUnit to close merely because her implementation is still in progress.

### 7.2 GitHub/GitLab as shared transport for live development state

The shared Git provider can carry more than final branches and PR-ready state.

Conceptually:

```text
Alice / Host A
      │
      ▼
checkpoint A1
      │
      ▼
Ruu Cloud progression
      │
      ▼
shared managed Git state
      │
      ▼
GitHub / GitLab
      │
  ┌───┼────┐
  ▼   ▼    ▼
Host B   Host C   Host D
```

The provider remains authoritative for the remote Git facts it owns, but Ruu Cloud uses that shared Git substrate to make useful managed development state available across hosts.

This changes the role of the remote from:

```text
place where finished branches are eventually published
```

toward:

```text
shared substrate through which exact useful development state
can become available while work is still in flight
```

### 7.3 Dependent work consumes the newest safe state, not merely `main`

Assume Bob is implementing B and B depends on work Alice is producing in A.

Traditional workflow:

```text
Alice works on A
        ↓
Alice finishes
        ↓
PR
        ↓
CI / review
        ↓
merge to main
        ↓
Bob updates
        ↓
Bob can proceed
```

Ruu Cloud target behavior:

```text
Alice produces checkpoint A1
        ↓
Ruu Cloud advances A1 as far as safely possible
        ↓
A1 becomes shared exact managed state
        ↓
Bob's agent can consume A1
        ↓
Bob proceeds with B
        ↓
Alice later produces A2
        ↓
Ruu Cloud reconverges
        ↓
Bob can consume A2 when safe
```

The governing principle is:

> **Dependent work should consume the newest safe state that satisfies its dependency, not wait for the contributing work to reach `main` merely because that is the traditional publication boundary.**

This is one of the central mechanisms by which Ruu Cloud makes development continuously executable.

---

## 8. Development should not wait for publication boundaries

A major source of friction in conventional team development is not an actual semantic dependency between two pieces of work.

It is a workflow dependency created by Git publication boundaries.

A common pattern is:

```text
Alice implements A
        ↓
Bob needs A in order to implement B
        ↓
Bob waits
        ↓
Alice finishes branch
        ↓
opens PR
        ↓
CI / review
        ↓
merge to main
        ↓
Bob updates his branch
        ↓
Bob can safely continue
```

The fact that Bob needs the result of A may be real.

The requirement that Bob wait for:

```text
branch completion
→ PR
→ review
→ merge to main
```

is often not.

If Alice has already produced an exact, usable checkpoint of A, and that state can be safely composed into Bob's development context, the version-control system should not force Bob to wait for A's publication lifecycle to finish.

Therefore:

> **`main` should not have to be the first place where one developer's in-flight work becomes safely consumable by another developer's work.**

Publication remains important.

It simply stops being the clock that determines when development may continue.

---

## 9. Development System + Ruu Cloud

Ruu Cloud becomes substantially more powerful when the team's Development System has an explicit machine-readable work graph.

That work graph may come from:

- GitHub Issues / GitHub Projects;
- Linear or another issue tracker;
- structured implementation tickets;
- specifications;
- explicit dependencies;
- priorities;
- another orchestration layer.

The Development System and Ruu Cloud have different responsibilities.

The Development System answers:

> **What work should become executable next?**

Ruu answers:

> **What exact version state can safely progress and compose right now?**

Conceptually:

```text
              WORK GRAPH
      tickets / specs / priorities
             / dependencies
                   │
                   ▼
          DEVELOPMENT SYSTEM
        selects executable work
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       agent A  agent B  agent C
          │        │        │
          └────────┼────────┘
                   ▼
               RUU CLOUD
        continuously converges
          exact version state
                   │
                   ▼
          new usable state
                   │
                   └──────────────→ unlocks more work
```

This creates a feedback loop rather than a sequence of isolated PR batches.

A ticket dependency can be explicit:

```text
Ticket A — implement auth primitive

Ticket B — implement session refresh
depends_on: A

Ticket C — add logging
independent
```

The system does not need to wait until `Ticket A` has completed its full PR and merge lifecycle before considering B.

If A produces a sufficiently usable exact state, the Development System may determine that B is now executable.

Ruu Cloud then provides the version-state substrate that allows B to consume that state safely.

---

## 10. From implicit human scheduling to machine-readable dependency scheduling

Traditional teams often carry important development dependencies implicitly in human conversation:

```text
"Alice is doing that first."

"Bob is blocked on Alice."

"Don't start this until PR #42 merges."

"We need to decide the API before Charlie can continue."
```

Some of these are genuine semantic dependencies.

Others are merely Git workflow dependencies.

A sufficiently explicit Development System should progressively turn the former into machine-readable work structure:

```text
intent
+
priority
+
dependencies
+
current executable state
```

For example, instead of:

```text
Bob cannot start until Alice decides the API
```

the work graph may expose:

```text
Ticket A1 — decide API contract
Ticket A2 — implement API
depends_on: A1

Ticket B — implement client
depends_on: A1
```

Once A1 is resolved, A2 and B may become independently executable.

Ruu Cloud does not invent missing product decisions.

It allows the system to stop confusing:

```text
semantic dependency
```

with:

```text
waiting for Git publication
```

The long-term goal is that humans increasingly define:

- intent;
- priorities;
- architectural decisions;
- real dependency constraints;

while the Development System and Ruu continuously determine what work can progress now.

---

## 11. Continuously executable software development

The larger product direction is not merely "more parallel coding."

It is:

> **Software development should become continuously executable.**

Whenever:

```text
an intent is sufficiently defined
+
its true dependencies are satisfied
+
the required exact code state exists
```

the system should be able to advance that work.

It should not also require:

```text
the previous branch is merged
+
the corresponding PR is closed
+
a human has manually updated another branch
```

unless those publication boundaries are themselves real policy requirements for the transition being attempted.

This changes the team's operating model from:

```text
assign
→ implement
→ PR
→ wait
→ merge
→ unblock
→ start next work
```

toward:

```text
work graph
→ executable work starts
→ exact usable states appear
→ Ruu continuously converges them
→ dependencies become satisfied
→ more work becomes executable
→ repeat
```

At sufficient agentic scale, this distinction becomes critical.

A team may have:

```text
5 developers
× 5 active coding agents
= 25 concurrent producers
```

or eventually:

```text
5 developers
× 20 agents
= 100 concurrent producers
```

Human beings cannot practically act as the scheduler for every dependency and every overlapping Git state at that scale.

The system must absorb that scheduling burden.

Ruu Cloud is the version-control half of that system.

---

## 12. The team becomes the coordination domain

In Ruu Core, the physical host is the boundary of authoritative coordination.

Multiple local invocations may be coalesced into one coherent progression of managed state.

Ruu Cloud extends that concept:

```text
Ruu Core:
many callers
→ one host-local coordination domain

Ruu Cloud:
many hosts
→ one team coordination domain
```

An invocation from Host A and an invocation from Host B must not behave as two unrelated Ruu universes merely because they originated on different machines.

They are convergence demands against the same managed team domain.

Conceptually:

```text
Host A ── ruu ──┐
Host B ── ruu ──┼── one team-wide managed state
Host C ── ruu ──┘
```

The implementation may remain highly distributed internally.

The product behavior must nevertheless appear coherent.

---

## 13. What must remain local

Ruu Cloud should not centralize things merely because a cloud service exists.

Some state is intrinsically host-local.

Examples include:

- dirty working-tree contents;
- local filesystem state;
- local authoring environments;
- worktree or VM execution state;
- local Git observations that must be made at the point of mutation;
- agent processes and their transient execution context.

A cloud control plane must not pretend to be authoritative over facts it cannot directly observe.

The local Ruu instance remains responsible for local truth.

---

## 14. What becomes shared

Ruu Cloud exists to provide the durable shared authority required when independent hosts participate in the same Ruu domain.

The exact architecture is not fixed by this document, but the shared layer may need to coordinate concepts such as:

- team-wide managed identities;
- convergence demand;
- authoritative current managed state;
- cross-host claims;
- fencing generations/tokens;
- operation identity;
- attempt/observation/adoption history;
- host participation;
- recovery after host/process/network failure;
- cross-host progression of ConvergenceUnits;
- shared PromotionGroups;
- publication obligations;
- exact-state conflict and reconciliation obligations.

The governing requirement is not that every one of these concepts must live in a central database.

The requirement is:

> **No two hosts may independently believe they hold incompatible authoritative rights over the same managed transition.**

---

## 15. GitHub/GitLab remain complementary

Ruu Cloud is not intended to replace GitHub or GitLab.

Git providers already own valuable provider-level concerns such as:

- remote Git hosting;
- pull requests / merge requests;
- reviews;
- protected branches;
- status checks;
- merge queues;
- repository permissions;
- organization governance.

Ruu should continue to use and respect those systems.

A possible flow remains:

```text
team authoring
     ↓
Ruu Cloud coordination
     ↓
Ruu convergence
     ↓
GitHub/GitLab publication
     ↓
review / CI / governance
     ↓
target branch
```

The distinction is temporal and semantic:

> GitHub typically becomes the common coordination point once branches/PRs are published.

> Ruu Cloud provides a common convergence domain **during active concurrent authoring**, across the team's hosts.

Ruu Cloud therefore attacks a different layer of the workflow.

---

## 16. Pull requests are not the problem

Ruu Cloud must not be justified by claiming that Pull Requests are obsolete.

PRs can remain useful for:

- human review;
- approval;
- audit;
- CI;
- policy enforcement;
- discussion;
- external contribution boundaries.

The product intent is not:

> eliminate PRs.

It is:

> **do not use PRs and human branch scheduling as the first mechanism for making independent concurrent authoring coexist.**

And, critically:

> **do not make PR completion the default prerequisite for dependent development work to become executable.**

By the time work reaches a PR boundary, Ruu should already have done as much mechanical convergence as safely possible, and other development work may already be consuming exact in-flight states when its true dependencies allow it.

---

## 17. Why this matters more in an agentic development world

Human-only teams have historically made preventive coordination tolerable because the number of simultaneous producers was relatively small.

For example:

```text
5 developers
≈ 5 primary concurrent work streams
```

Agentic development changes the scale:

```text
5 developers
× 5 active coding agents
= 25 concurrent work streams
```

or eventually:

```text
5 developers
× 20 agents
= 100 concurrent work streams
```

At that scale, "let's make sure nobody touches the same thing" stops being a viable scheduling strategy.

The team cannot hold a morning meeting with every autonomous producer.

The version-control layer must absorb much more concurrency mechanically.

Ruu Cloud therefore exists for a world where:

> **the number of software-producing actors can grow much faster than the number of humans coordinating them.**

---

## 18. Ruu Cloud is not a collaboration dashboard

The core value is not a dashboard showing which developer is working where.

Awareness may be useful, but visibility is not the product.

The product must not regress into:

```text
Alice is editing auth.rs
→ warn Bob not to touch it
```

That would simply digitize the old coordination model.

The desired model is:

```text
Alice edits auth.rs
Bob edits auth.rs
agents edit auth.rs

→ isolated authoring
→ safe convergence
→ explicit reconciliation only if necessary
```

The system should make concurrency safe, not merely make concurrency visible.

---

## 19. Ruu Cloud is not a distributed shared filesystem

The product must not solve team concurrency by giving everyone one remotely shared mutable checkout.

That would recreate the collision problem at a larger scale.

Isolation remains fundamental.

Ruu Cloud coordinates **independent mutable authoring contexts** and their progression toward shared canonical state.

It does not make independent actors write into one shared mutable working tree.

---

## 20. Failure model

Multi-host coordination is valuable only if it remains correct under realistic distributed failures.

Ruu Cloud must be designed with the assumption that:

- a host can disappear;
- a process can crash;
- a network request can time out after the remote effect actually occurred;
- acknowledgements can be lost;
- a machine can pause and later resume;
- two hosts can race;
- provider state can change concurrently;
- observations can become stale.

The product must fail closed where authority is uncertain.

A previously authoritative host must not regain the right to perform stale managed progression merely because it wakes up later.

Distributed correctness is part of the product, not an optional enterprise add-on.

---

## 21. Managed-service value proposition

A sufficiently capable engineering organization could potentially build its own multi-host coordination backend around Ruu Core, Git, a shared database, or provider primitives.

Ruu Cloud does not need to make that impossible.

Its value proposition can be:

> **You can build and operate the distributed coordination layer yourself, or you can connect your Ruu hosts to Ruu Cloud and obtain one maintained team-wide Ruu domain.**

The customer is not merely paying for hidden source code.

The customer is paying to avoid owning the operational burden of:

- distributed coordination;
- fencing;
- durable shared state;
- failure recovery;
- protocol upgrades;
- compatibility;
- service availability;
- migrations;
- monitoring;
- distributed race-condition debugging.

This is a legitimate managed-infrastructure boundary rather than an artificial feature gate.

The governing commercial philosophy is:

> **Software capability being available does not make operating the distributed service free.**

An organization that chooses not to buy Ruu Cloud may build and operate its own distributed coordination system around Core. If it does so, it is choosing to own distributed coordination, shared persistence, cross-host identity, fencing/failure handling, retention, monitoring, backups, upgrades, security, hosted UI, and LLM integration.

Ruu Cloud sells relief from that operational burden.

---

## 22. Recovery and AI boundaries

Ruu Cloud must preserve the same fundamental recovery semantics as Ruu Core.

```text
CORE                       CLOUD
────────────────────────────────────────────────────────────
Recovery engine            complete / same semantics
Recovery proof             local exact evidence
                            team-wide exact evidence

Impact analysis            local causal domain
                            cross-host causal domain

UX                         local CLI / local UI
                            hosted team UI

AI                         BYO model / tools
                            managed Ask Ruu
```

Cloud may expose a broader recovery impact because it sees a broader causal domain.

It must not possess a different or more privileged definition of recovery correctness.

Cloud can therefore know **more facts** without having a better mechanical truth function.

### 22.1 AI capability versus AI service

Ruu Core should be machine-understandable by design.

Core may expose structured state, causal queries, inspection/recovery APIs, stable schemas, and tool definitions so that a user can connect Claude Code, Codex, Pi, a local model, or a company agent without paying Ruu for inference.

> **Do not charge for the capability required to understand Ruu. Charge for operating that capability as a managed service.**

Ruu Cloud may provide a hosted assistant such as `Ask Ruu`, including:

- no-setup model access;
- team-wide context;
- context selection;
- model routing;
- hosted inference;
- managed credentials;
- usage accounting;
- continuous availability.

The AI service must not become an authority.

It may query, explain, summarize, navigate, compare, identify relevant causal nodes, and translate natural language into a structured request candidate.

If a user asks to recover an operation, the path must remain:

```text
human intent
     ↓
LLM interpretation
     ↓
structured recovery request
     ↓
Ruu deterministic recovery engine
     ↓
exact current state
     ↓
recovery plan
     ↓
authority / policy checks
     ↓
execution
```

Never:

```text
LLM opinion
↓
direct Git mutation
```

> **Ruu AI may explain and request. Ruu mechanics decide and execute.**

---

## 23. Developer and team-lead UX

Ruu Cloud must preserve the hands-off experience of Ruu Core.

Multi-host coordination is a property of the system, not a new operational responsibility for developers.

For developers, the UX should be **the same as Ruu Core**.

Conceptually:

```text
open coding harness
↓
implement
↓
ruu
```

A developer should not need to understand or operate:

- distributed locks;
- cross-host claims;
- fencing generations;
- coordination databases;
- lease renewal;
- remote state-machine ownership.

Those are Ruu Cloud concerns.

A team should be able to go from:

```text
we use Ruu locally
```

to:

```text
our whole team shares one Ruu domain
```

through a simple setup such as:

1. create a Ruu Cloud team;
2. connect the Git provider;
3. select repositories;
4. invite members;
5. define the minimal Ruu-specific permissions/policies;
6. install/use Ruu normally on each host.

Ruu Cloud should reuse GitHub/GitLab repository access and permissions wherever possible rather than forcing the team to recreate provider governance inside Ruu.

The team-lead UI should remain intentionally simple.

Its role is team administration and visibility into the live Ruu domain, not project management.

---

## 24. Canonical team development state

Ruu Cloud must not treat monitoring as a human-only dashboard concern.

The team-wide development state must exist first as a **canonical, structured, machine-readable model**.

The web UI, CLI, APIs, automations, Development System, and future autonomous agents must all consume projections of the same underlying state.

Conceptually:

```text
                  RUU CLOUD
                      │
                      ▼
          Canonical Team Development State
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        Web UI      API/CLI      Agents
        humans      machines     reasoning
```

The governing rule is:

> **Every state that is meaningful to team development must have a stable machine-readable representation before it has a human-facing UI representation.**

No critical Ruu Cloud state should exist only as prose rendered in a dashboard.

For example, this is insufficient:

```text
"Bob appears to be waiting for Alice."
```

The underlying state should instead be explicit:

```text
work_id: PAYMENTS-54
state: BLOCKED
blocker_type: UNSATISFIED_DEPENDENCY
dependency: AUTH-142
required_condition: SAFE_STATE_AVAILABLE
attention_required: false
```

The UI may render that as:

```text
PAYMENTS-54
Waiting for usable state from AUTH-142
```

An agent may reason directly over the structured form.

### 23.1 The primary abstraction is work progression, not branches

Branches, commits, refs, checkpoints, hosts, and GitHub PRs remain important technical facts.

They should not be the primary abstraction presented to humans or agents when reasoning about team development.

A branch such as:

```text
alice/auth-refactor
```

does not directly answer:

- Is the work still active?
- Is its latest safe state available team-wide?
- Is another contribution already consuming it?
- Is it blocked?
- Does it require attention?
- Is it ready for promotion?
- Has it already unlocked downstream work?

Likewise, a raw checkpoint OID is precise but not sufficient as a development-level concept.

The canonical model should instead expose concepts such as:

- work identity;
- contribution identity;
- current progression state;
- latest safe available state;
- availability scope;
- dependencies;
- dependents;
- consumers;
- blockers;
- obligations;
- attention requirements;
- promotion state;
- provenance to exact Git state.

The technical Git objects remain the exact evidence underneath that model.

The relationship is:

```text
development-level meaning
        │
        ▼
work / dependency / availability / blocker / progression
        │
        ▼
exact Ruu identities and obligations
        │
        ▼
checkpoint / commit / ref / branch / provider state
```

Ruu Cloud must preserve access to every lower layer for inspection and debugging.

It should not force higher-level decision makers to reason at that layer by default.

### 23.2 Monitoring exists to support decisions

The purpose of team-wide monitoring is not to display activity for its own sake, and not to provide a second decision engine on top of Ruu.

It exists to answer observable-state questions such as:

- What work is currently progressing?
- What work is blocked?
- Why is it blocked?
- What requires human or agent attention?
- What new safe state has become available?
- Which downstream work has just become executable?
- What work is already consuming another contribution's checkpoint?
- Where is development flow slowing down?
- Where has Ruu reached a boundary that requires external intervention?

Therefore, the default monitoring surface should emphasize **exceptions, newly available state, dependency changes, and progression** rather than a flat list of branches.

A useful high-level representation may look like:

```text
Team Development

Progressing        14
Blocked             2
Needs attention     1

Recently unlocked
AUTH-142 → API-91
AUTH-142 → WEB-203

Needs attention
PAYMENTS-54 — semantic reconciliation required

Recently available
AUTH-142 — checkpoint A4 available team-wide
```

The same information must be queryable mechanically.

### 23.3 Human, machine, and agent readability are equally important

Ruu Cloud should be designed for three first-class consumers:

```text
human
machine
agent
```

A human may need a concise visual explanation.

A machine may need stable schemas, event streams, IDs, and deterministic queries.

An agent may need enough semantic structure to understand Ruu's current state and react when an explicitly external decision or action is required.

None of these consumers should depend on scraping another consumer's representation.

In particular:

> **Agents must never need to read the human dashboard in order to understand team development state.**

The dashboard is a client.

The agent interface is a client.

The Development System is a client.

They all consume the same authoritative model.

### 23.4 Machine-readable state is for observation and external reaction

Ruu Cloud should not introduce an AI or agent decision layer between managed state and mechanically valid Ruu transitions.

Within Ruu's responsibility boundary, progression should remain mechanical:

```text
exact state
+
policy
+
obligations
        ↓
deterministic Ruu progression
```

Ruu should continue to advance everything that can safely advance and stop only when it reaches a boundary that cannot be resolved mechanically.

The canonical team development state exists so that humans, tools, and external Development Systems can understand that boundary precisely.

For example, an external system may query:

```text
What is progressing?
What is already usable?
What is blocked?
Why is it blocked?
What requires external intervention?
```

and receive structured answers such as:

```text
PAYMENTS-54
state: BLOCKED
blocker_type: SEMANTIC_RECONCILIATION_REQUIRED
attention_required: true
```

Ruu Cloud does not decide how the semantic conflict should be resolved.

It exposes the exact condition so that the responsible Development System, coding agent, or human can react.

Likewise, if several tickets are executable but only one can be scheduled because of external resource constraints or product priority, that scheduling decision belongs to the Development System, not to Ruu Cloud.

The responsibility split is:

```text
RUU CLOUD
exact version-development state
mechanical progression
explicit blockers / obligations
        │
        ▼
DEVELOPMENT SYSTEM / HUMANS / AGENTS
semantic decisions
priority decisions
resource scheduling
exception handling
```

The governing principle is:

> **Ruu Cloud exposes canonical machine-readable development state so humans and external development systems can observe progression and react only when Ruu reaches a boundary that cannot be resolved mechanically.**

Ruu Cloud is therefore not only a multi-host coordinator.

It is also:

> **the authoritative observable model of the team's live version-development state.**

### 23.5 UI responsibility

The Ruu Cloud UI should remain intentionally thin.

Its role is to project the canonical state for humans and provide the small amount of team administration required by the product.

For an initial team-focused product, the UI may include:

```text
Team
Repositories
Members
Permissions
Hosts
Development Flow
Billing
```

The `Development Flow` view should primarily expose:

- progression;
- blockers;
- attention required;
- newly available checkpoints;
- dependencies becoming satisfied;
- downstream work being unlocked;
- promotion state.

Branches, commits, refs, checkpoints, ContributionUnits, ConvergenceUnits, operations, and provider objects should remain available as drill-down details where technically useful.

Ruu Cloud must not become a project-management product.

It should not own:

- roadmap;
- sprint planning;
- ticket priority;
- product requirements;
- business scheduling.

Those remain responsibilities of the Development System or external work-management system.

The UI shows **what the version-development system is doing now** and **where Ruu has reached a non-mechanical boundary**.

Ruu determines mechanically **what version-state progression is valid now**.

The Development System determines **what should be worked on next** and handles decisions outside Ruu's responsibility.

---


## 25. Degraded and offline behavior

Ruu Cloud must never become a prerequisite for correct local authoring.

A developer must not lose the ability to continue safe local work merely because the shared cloud coordination service is temporarily unavailable.

The desired failure behavior is:

```text
Ruu Cloud available
        ↓
team-wide convergence and propagation

Ruu Cloud unavailable
        ↓
local Ruu authoring remains correct
        ↓
team-wide progression pauses where shared authority is required
        ↓
shared progression resumes when authority becomes available again
```

The governing principle is:

> **Cloud unavailability may pause team-wide progression, but it must not corrupt or invalidate correct local authoring state.**

Ruu Cloud must therefore fail closed where shared authority is uncertain.

It must not guess.

It must not allow a host to invent team-wide authority merely because the control plane is unreachable.

At the same time, purely local work that does not require new shared authority should remain possible.

This distinction is critical for developer trust.

---

### 24.1 Ruu Cloud should coordinate, not own source code by default

The cloud service should receive only the state necessary to perform its coordination responsibility.

By default, the authoritative ownership of source and mutable authoring state remains with the existing development infrastructure:

```text
source repository
→ Git provider / repository

dirty worktree or VM state
→ developer host

agent runtime
→ developer / execution host

team-wide coordination state
→ Ruu Cloud
```

Ruu Cloud should not require central possession of:

- full dirty working trees;
- developer filesystems;
- source code that is not otherwise required for coordination;
- coding-agent prompts or model context;
- arbitrary local execution state.

If future capabilities require additional data, that expansion must be explicit rather than assumed.

---

### 24.2 No hidden semantic authority

Ruu Cloud must not become an implicit semantic decision maker.

It must not decide that:

```text
Alice's intent is better than Bob's
```

or:

```text
this product behavior should win
```

unless that result is already determined by explicit policy or exact managed state.

Within Ruu's responsibility:

```text
exact state
+
policy
+
mechanical rules
        ↓
valid progression
```

Outside Ruu's responsibility:

```text
product intent
semantic trade-offs
priority choices
architecture decisions
exception approval
```

Those remain responsibilities of humans, agents, or the Development System.

---

## 26. Responsibility boundaries and non-goals

Ruu Cloud should stay narrow enough that its core responsibility remains understandable.

It is:

> **the multi-host coordination and observable live-state layer for Ruu-managed team development.**

It is not intended to become:

- a project-management product;
- an issue tracker;
- a roadmap tool;
- a team chat;
- a source-code hosting provider;
- a replacement for GitHub/GitLab;
- a replacement for Pull Requests or code review;
- a CI platform;
- a coding agent;
- an AI authority or autonomous correctness engine;
- a shared remote filesystem;
- a generic workflow automation product;
- an organization-wide multi-team governance platform in the initial product scope.

For the initial product:

> **one Ruu Cloud domain corresponds to one development team.**

Multi-team federation, enterprise-wide hierarchy, and organization-level orchestration are future concerns and must not distort the first product.

---

### 25.1 Provider independence

GitHub may be the first or primary integration.

Ruu Cloud must not be conceptually defined as "Ruu for GitHub."

Its product model should remain compatible with:

```text
GitHub
GitLab
other Git forges
bare Git remotes
future providers
```

Provider-specific features may exist in adapters.

The governing abstractions must remain provider-independent.

---

### 25.2 Branches are not the product model

Branches, refs, commits, and PRs remain essential Git/provider facts.

They must not become the conceptual center of Ruu Cloud.

The product should remain defined primarily in terms of:

```text
work
contributions
checkpoints
availability
dependencies
convergence
obligations
blockers
promotion
```

Git artifacts provide exact technical evidence underneath those concepts.

This separation is important for both human readability and future machine reasoning.

---

### 25.3 No additional developer workflow

Preserving the Ruu Core UX means more than preserving the CLI command.

The developer must not be required to perform new cloud-specific workflow steps such as:

```text
publish to Ruu Cloud
approve synchronization
reserve a file
claim a branch
open the Ruu dashboard before coding
manually mark a checkpoint as shareable
```

If an authoritative state is safe and eligible to progress, Ruu Cloud should progress it automatically.

The desired developer experience remains:

```text
implement
↓
ruu
```

The existence of a multi-host control plane must remain invisible during ordinary work.

---

### 25.4 Portability and exit

Ruu Cloud must not make the customer's repositories dependent on an opaque hosted format.

If an organization stops using Ruu Cloud:

- its Git repositories must remain valid;
- its code must remain accessible;
- its canonical Git history must remain understandable;
- provider-side state must remain usable;
- leaving Ruu Cloud must not require reconstructing source state from proprietary cloud storage.

Ruu Cloud may own coordination metadata.

It must not become the only place from which the software itself can be recovered or understood.

---

## 27. Useful-state latency is a product objective

Ruu Cloud should optimize for **useful-state latency**, not merely synchronization latency.

The important metric is not:

```text
checkpoint created
→ metadata reaches Ruu Cloud in 200 ms
```

The important metric is:

```text
checkpoint created
→ safely consumable by another developer or agent
```

This interval captures the actual product promise.

Call it conceptually:

```text
Useful-State Latency
```

or:

```text
time-to-safe-consumption
```

The exact product metric may evolve, but the governing idea should remain.

A system that receives checkpoints instantly but leaves them unusable by dependent work for ten minutes has not delivered fluid development.

---

### 26.1 Furthest-safe progression

Ruu Cloud should continuously attempt to drive every authoritative checkpoint to the furthest safely reachable shared state.

That progression may include:

```text
checkpoint adoption
→ shared publication
→ synchronization
→ convergence
→ integrated shared state
→ promotion when authorized
```

The system should stop only when it encounters a real boundary.

That boundary may be:

- an unsatisfied semantic dependency;
- an explicit policy;
- required review;
- unavailable shared authority;
- a true convergence conflict;
- a failed validation;
- another mechanically explicit obligation.

The governing principle is:

> **Progress as far as safely possible, as early as safely possible.**

---

### 26.2 Correctness dominates latency

Low useful-state latency is important.

Correctness is more important.

Ruu Cloud must never interpret the product objective as:

```text
share aggressively
```

It means:

```text
share as early as all required guarantees permit
```

A checkpoint that is available slightly later but is correct is preferable to a checkpoint propagated early under uncertain authority.

Therefore:

> **Fast propagation is always subordinate to exactness, fencing, policy, and safety.**

---

### 26.3 Observability should expose flow quality

Because useful-state latency is central to the product, the canonical development state should make flow degradation observable.

Humans and machines should be able to distinguish:

```text
checkpoint exists locally
checkpoint shared
checkpoint converged
checkpoint safely consumable
checkpoint blocked
```

This makes it possible to understand whether Ruu Cloud is actually making development more fluid rather than merely moving Git objects around.

A future monitoring view may therefore expose:

```text
Recently available
AUTH-142 → safe state available team-wide

Blocked propagation
PAYMENTS-54 → semantic reconciliation required

Dependency unlocked
AUTH-142 → API-91
```

The purpose remains operational understanding, not activity surveillance.

---

## 28. Product success criterion

The decisive test for Ruu Cloud is not whether it can synchronize metadata between hosts.

It is whether it changes the team's actual development behavior.

Ruu Cloud succeeds if developers can increasingly stop asking:

- "Who is already working on this?"
- "Should I wait before touching this file?"
- "Which branch must merge first?"
- "Can I start this change now?"
- "Will my agent collide with someone else's agent?"
- "Do I need to wait for Alice's PR before my work can start?"

and instead safely default to:

> **Start the work.**

And, when work depends on another contribution:

> **Use the newest safe state that satisfies the dependency; do not wait for contribution completion or final Git publication merely because the work originated elsewhere.**

Every authoritative checkpoint should be driven to the furthest safely reachable shared state so that other developers and agents can benefit from it as early as possible.

The system should absorb ordinary concurrency mechanically, continuously expose usable exact development state, and surface only the cases where the development intent itself is incompatible or where an explicit policy truly requires waiting.

If a team using Ruu Cloud still has to partition files, serialize branches, wait for unrelated PR publication boundaries, and manually schedule Git exactly as before, then Ruu Cloud has failed to deliver its product intent.

If the team-wide state is understandable only through the human UI, or if future agents must scrape dashboards, infer state from branch names, or reconstruct development meaning from raw Git history in order to understand progression and blockers, then Ruu Cloud has also failed to expose the right product abstraction.

If Ruu Cloud requires an agent or human to choose among mechanically valid Ruu transitions that should have been determined by exact state and policy, then Ruu Cloud has also violated the intended responsibility boundary.

If temporary Ruu Cloud unavailability makes correct local authoring impossible, the product has coupled developers too tightly to the control plane.

If Ruu Cloud optimizes for synchronization speed while useful checkpoints remain unnecessarily unavailable to dependent work, it has missed the product objective.

If leaving Ruu Cloud makes the team's repositories, code, or canonical Git history unusable without proprietary reconstruction, it has violated the portability boundary.

If Cloud possesses a correctness, evidence, inspection, or recovery property that Core fundamentally lacks for the same facts and policies, then the Core / Cloud boundary is wrong.

If Cloud and Core derive different mechanical truths from the same facts and policies, then the architecture is wrong.

If a Core user must purchase Cloud merely to make Ruu understandable to a human or external agent at local scale, then Core has been artificially weakened.

---

## 29. Governing product statement

The governing product statement is:

> **Ruu Core provides the complete single-host version-control capability and all fundamental safety, evidence, inspection, and recovery guarantees. Ruu Cloud extends that coordination domain across the team and operates the resulting shared system as a managed service. Every developer and coding agent may author concurrently from independent hosts, against the same repositories and even the same files, without requiring preventive Git coordination or creating operational collisions. Every authoritative checkpoint is propagated to the furthest safely reachable shared state as soon as correctness, policy, and authority permit, so other developers and agents can consume useful in-flight work without waiting for contribution completion, PR completion, or merge to `main`. Ruu Cloud maintains one coherent team-wide convergence domain and one canonical machine-readable model of live team development state, mechanically converges compatible contributions using the same Ruu semantics as Core, preserves correct local authoring when shared coordination is unavailable, exposes progression and blockers equally to humans, machines, and agents, and escalates only genuine semantic incompatibilities or explicit policy boundaries to the Development System.**

The deeper product ambition is:

> **Software development should stop being batch-oriented around branch, PR, and merge boundaries and become continuously executable: as soon as an intent is defined, its true dependencies are satisfied, and the required exact code state exists, the system should be able to advance it.**

A concise expression of the intended user-level outcome is:

> **Developers stop waiting for Git.**

And the governing Core / Cloud design rule remains:

> **Never weaken Core to create Cloud value. Cloud value should emerge from expanding the coordination domain and removing the operational burden.**

Everything else is secondary.
