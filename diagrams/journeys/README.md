# Journey Diagrams

P04 delivered three of these. They are **owner-facing and therefore bilingual Spanish + English**, and each cites its source documents and evidence status inside the file.

| File | Diagram | What it shows |
|---|---|---|
| `customer-journey.md` | B | Need/event → intake → results → provider profile → RFQ → quote / clarify / decline / no response → conversation → outcome, with the exception branches drawn as clearly secondary paths. |
| `customer-journey.excalidraw` | B | A hand-authored editable starting scene for Diagram B. It is a **subset** of the Markdown build specification and says so on the canvas; the specification is the complete source. |
| `provider-journey.md` | — | Register → business → offering → service area → profile and media → publication requirements → published → discovery → request → clarify / decline / offer → conversation → outcome signal, with `Stale` → reconfirm drawn as a visible maintenance loop. |
| `decision-overlay.md` | C | *What changes if…* — payments, fan-out, guaranteed calendar availability, native mobile, and AI-assisted intake, each as an **overlay on the primary design**. Every branch states what does **not** change first. |

There is no admin or promotion journey diagram. Operator work is exception-only queues rather than a journey (`docs/04-ux/operator-surfaces.md`), and sponsored placement is `FUTURE` with no V1 surface to draw (`ADR-008`).

**Constraints these diagrams carry.** No booking or payment step. No fan-out. No calendar or availability claim. No map. No `Suspended` state visible to a customer. Each file repeats its own never-show list, because a diagram is the artifact most likely to be reused out of context.

**Tooling.** This repository contains no diagram toolchain. The Markdown files carry Mermaid plus a precise Excalidraw build specification; the one `.excalidraw` scene was hand-authored and validated as JSON. **The Mermaid was reviewed by hand and not machine-validated**, because installing a renderer would add tooling to a repository that deliberately has none.
