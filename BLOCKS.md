# ExOS blocks

Goals, blocks and tasks for Concierge, neo-home, the harnesses and the host.
Written 2026-11-15 as a proposal. Nothing here is a commitment until the user
ratifies it; block IDs are stable so each can become a Concierge record.

## The bet

Chat makes a person re-explain their work to one model at a time. ExOS bets on
the opposite arrangement: the work is a shared medium, every intelligence
(frontier model, local model, tool, person) reads and writes it under the same
rules, and the interface is that medium made touchable. Intelligences become
interchangeable parts of one instrument. The more the record holds, the less
anyone has to be told, and that is where the compounding comes from.

Three numbers say whether the bet is paying:

| Number | Meaning | Direction |
| --- | --- | --- |
| Ratification rate | Share of proposed records the user accepts unedited | Up |
| Re-ask rate | Questions asked that the record already answered | To zero |
| Leverage | Verified work completed per minute of the user's attention | Up |

## G1. The record becomes a live medium

Concierge today is a store agents consult at session start. It should be the
thing everything is happening on.

| Block | Build | Done when |
| --- | --- | --- |
| G1.B1 Captures | Content-addressed store for raw material of any kind (voice, paste, screenshot, shell transcript); records cite it as `capture:<sha>` | A 20-minute voice note is stored once and five records point into it by offset |
| G1.B2 Event stream | `concierge watch`: a socket that emits every operation as it lands, plus `diff <head>..<head>` | A surface and an agent both react to a ratification within a second, with no polling |
| G1.B3 Usage ledger | Append-only log of packet reads, context injections and gate decisions | "Times the record answered instead of the user" is a query, not an estimate |
| G1.B4 Operations | A lifecycle log for runs: started, checkpoint, ended, failed, unknown, each tied to a packet | `running` shown anywhere is backed by a live executor heartbeat |
| G1.B5 Signed actors | Every participant holds a key; operations are signed; a commitment requires the user's key | Unsetting an environment variable no longer makes an agent the user |
| G1.B6 Grants | Autonomy as a record: actions, scope, budget, expiry, return-to-user conditions | One project runs unattended for a day inside a grant and stops at its edge |

Tasks to start: define the capture ref format and offsets; pick the signing
scheme (SSH keys via `ssh-keygen -Y sign` are already on every host); write the
operation lifecycle as a state machine with `unknown` as a first-class state.

## G2. Distillation: raw material in, structure out

The return on a drop has to be bigger than the drop, within seconds.

| Block | Build | Done when |
| --- | --- | --- |
| G2.B1 Distiller | A role that reads a capture plus the compiled entry and emits an import batch: open questions, claims, proposals, links. Never commitments | A raw note comes back as ratifiable proposals in under ten seconds |
| G2.B2 Recall | Hybrid search over records and captures (full text, embeddings, link graph), run locally | "You said the opposite three weeks ago" appears unprompted, with the source |
| G2.B3 Watchers | Background intelligences that file `contradicts` and mark claims stale when their grounds move | A changed dependency flags every record resting on it before a person notices |
| G2.B4 Precedent | When a new question resembles a resolved one, offer the earlier answer | "Same as CON-0xx?" settles a decision in one gesture |
| G2.B5 Evals | A golden set built from real captures and the user's actual ratify/edit/reject history | Ratification rate is tracked per model and per area, on every distiller change |

Tasks to start: export every past ratify/edit/reject as labelled data (this is
the most valuable dataset in the system); write the distiller's output schema
as a Concierge import batch so its work is checked by the store's own rules.

## G3. Composition: many intelligences, one piece of work

No model is the product. The contract between them is.

| Block | Build | Done when |
| --- | --- | --- |
| G3.B1 Packet contract | A written adapter spec: any harness starts from a packet and returns claims with evidence; a conformance suite | Claude Code, Codex, Antigravity, DSH and a local model all pass the same suite |
| G3.B2 Structural independence | Verifier must differ from author by actor and by model family, enforced by the store | Self-graded evidence is impossible, including through an `observed:` source |
| G3.B3 Track records | Per-intelligence, per-area history derived from evidence: claims verified, contradicted, ratified | The participants panel shows earned standing, and routing uses it |
| G3.B4 Router | Assign blocks by track record, cost and latency; small local model for watch and distill, frontier models for hard work | Routing choices are explainable from the ledger |
| G3.B5 Replacement test | Automated: kill a run mid-task, swap model and harness, resume from the packet | Runs nightly; scores commitments kept, rejections respected, re-asks |
| G3.B6 Productive disagreement | Run two different intelligences on consequential questions and surface where they differ | What reaches the user is the disagreement, not two essays |

Tasks to start: turn the brief's "private prototype, do not deploy" scenario
into the first replacement-test fixture; write G3.B1 from the hook code that
already exists and move runtime adapters out of `concierge.py` behind it.

## G4. The surface: an instrument, not a chat window

Every gesture is an operation on the record. Conversation is one input among
several.

| Block | Build | Done when |
| --- | --- | --- |
| G4.B1 Arrival | Home rendered from the compiled entry plus the diff since this person's last seen head | Opening neo-home after a day away takes under ten seconds to understand |
| G4.B2 Drop anywhere | Global hotkey, phone share sheet, voice, `nh drop` in a shell | Capturing a thought never requires opening a project first |
| G4.B3 Direct manipulation | Ratify, reject with reason, park with condition, branch, contest, mark exemplar: one gesture each, on the record itself | No form stands between the user and a decision |
| G4.B4 Living direction | One evolving statement per project, with its diff over time | The user can watch their own intent sharpen week over week |
| G4.B5 Composed views | Intelligences assemble a view for the work at hand from a fixed grammar of components whose meanings never change | A new kind of work gets a fitting surface without a new route being coded |
| G4.B6 Presence and steering | Running work is visible where it happens; interrupt, instruct and revise in place | Redirecting an agent mid-run is one action and leaves an attributable record |
| G4.B7 Leverage panel | True numbers from the usage ledger where Ascent had stats | The user can see what their past decisions are saving them |
| G4.B8 Phone and terminal | The same operations over SSH; reconnect restores real state first | Start at a desk, decide from a phone, return to a desk that already knows |

Tasks to start: write the component grammar for G4.B5 (standing, grounds,
action, scope are the invariants); rebuild Home on G4.B1 against a real scope;
keep the Ascent material and the four standing words from ADE v2.

## G5. ExOS on NixOS: the environment is part of the record

A declarative host means the system itself can be read, diffed, branched and
rolled back like any other contribution.

| Block | Build | Done when |
| --- | --- | --- |
| G5.B1 Flake | `nix run` targets for Concierge and neo-home; a NixOS module `services.exos` | A fresh machine becomes an ExOS host from one flake |
| G5.B2 Real boundary | Agents run as sandboxed systemd units; the store is read-only to them except through the CLI; grants compile to unit, filesystem and network policy | The permission check happens in the OS, and the regex gate is retired to a hint |
| G5.B3 Reproducible evidence | Each run records its system closure and flake lock hash | "Verified" names the environment, and goes stale when that environment changes |
| G5.B4 Generations as branches | A branch of work can carry its own system generation | Trying an alternative includes its toolchain; abandoning it is a rollback |
| G5.B5 Many hosts | One flake for the DGX Spark, NixOS desktops and macOS via nix-darwin; microVMs for untrusted runs | Work moves between hosts without anyone restating the setup |
| G5.B6 Local intelligence | Always-on small models on the Spark for distill, recall and watch | The loop keeps working with no network |

Tasks to start: package Concierge (standard library only, so this is small);
write the sandbox profile for one harness and prove a store write is refused
by the kernel, not by a pattern.

## G6. Trust that grows with use

Autonomy should be earned from evidence and offered back to the user as a
choice.

| Block | Build | Done when |
| --- | --- | --- |
| G6.B1 Earned grants | The system proposes widening or narrowing a grant from the track record | The user ratifies autonomy changes; they never drift |
| G6.B2 Taste | Exemplars and ratify/reject history condition every distiller and maker; later, a small adapter tuned locally on that history | Ratification rate rises on work the system has never seen |
| G6.B3 Self-review | A weekly pass over the usage ledger: what was asked that the record held, what was resurfaced after rejection | Each review ends in proposed record edits, not a report |
| G6.B4 Gardener | Proposes supersessions and merges to hold the compiled entry inside its word budget | The record gets sharper as it grows, not longer |

## First slice

One real loop, end to end, before anything else broadens:

1. G1.B1 captures and G4.B2 drop from a shell and a phone.
2. G2.B1 distiller writing an import batch.
3. G4.B1 arrival and G4.B3 ratify/reject/exemplar on a real scope.
4. G1.B3 usage ledger, so the first leverage number is true.

Proof: a voice note dropped from a phone becomes proposals on arrival, the
user ratifies two and rejects one with a reason, and the next agent session,
on a different model, starts with all three and does not ask about them.

## Carried from review

- Re-run the live hook check for `CON-062` under a different actor, or mark it `claimed`.
- Label the gate advisory everywhere it is shown until G1.B5 and G5.B2 land.
- Unverified here: commit `98cb350` (reopen-condition fix, separated `accepted`/`verified`, command-position deploy matching, SSH URL exemption).
