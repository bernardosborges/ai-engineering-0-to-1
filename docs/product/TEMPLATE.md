# Product document template

This template defines the structure for any document under `docs/product/`. It covers two kinds of documents:

- **pd-***: Product discovery documents. One per discovery dimension (motivation, audience, scope, business model, risks, workflows, MVP).
- **ft-***: Feature or transversal model documents. One per significant feature or shared entity (auth, billing, the core data model, etc).

Use the structure below for any new document in this folder.

---

## Structure

# <Document title>

> **Status:** Draft | v1 | v2
> **Owner:** product

## Purpose

<One paragraph: what this document answers and why it exists>

## <Section 1>

<Detailed content>

## <Section 2>

<Detailed content>

...

## Open questions

<What is still unresolved, marked explicitly>

## Related

- `<other-doc>.md` — why it's related
- `<another-doc>.md` — why it's related

---

## Notes per document kind

**For pd-* documents:**
- Sections mirror the questions of the corresponding discovery dimension
- Section titles are the questions themselves (e.g., "Why should this system be built?")
- Each section is a 2-4 paragraph answer with concrete details

**For ft-* documents:**
- Standard sections: Purpose, Actors, Vocabulary, Entities, Policies, Edge cases, Open questions, Related
- See examples in any existing ft-* file

## Naming convention

- Lowercase with dashes
- pd-<dimension>.md for discovery (`pd-motivation.md`, `pd-audience.md`, ...)
- ft-<feature>.md for features (`ft-authentication.md`, `ft-billing.md`, ...)
