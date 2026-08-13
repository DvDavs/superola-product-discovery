# Context Diagrams

| File | Diagram | What it shows |
|---|---|---|
| `superola-ux-map.md` | A | *Superola in one page* — every named surface in `docs/04-ux/surface-inventory.md`, grouped into public, customer, provider, channel, and operator lanes, with the `FUTURE` surfaces visually separated. Owner-facing and therefore bilingual. |

**A surface is not a service, a module, or a deployment unit**, and this diagram must never be read as a topology. The module boundaries are in `docs/02-architecture/domain-map.md`; the deployment shape is one artifact (`ADR-001`, `ADR-013`). The same rule the domain and architecture diagram stubs carry applies here in the other direction: do not draw a screen as a system.

No system or stakeholder context diagram exists yet. P00 created none because boundaries were under discovery, and P04 had no reason to add one — it would restate `docs/02-architecture/architecture-overview.md` in a less precise medium.
