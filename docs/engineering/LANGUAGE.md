# Language Policy

Defines which natural language is used where. Prevents mixed-language codebases and inconsistent user experience.

This policy distinguishes between **developer-facing content** (always one language) and **user-facing content** (the product's audience language). The two never mix.

---

## Rule of thumb

**English by default. A second language only when a human user will read the text in the product.**

If you hesitate, it's English.

---

## English — developer-facing content

All developer-facing content stays in English, regardless of which language the user-facing product uses:

- Folder and file names
- Code: variables, classes, function names, module names
- Comments and docstrings
- Exception class names (`OrderNotFoundError`)
- Internal error messages, log messages, event names (`"order.created"`)
- API endpoint paths, query parameter names, request/response field names
- Database table and column names
- Migration names
- Git commit messages (Conventional Commits in English: `feat(orders): add streaming`)
- Pull request titles and descriptions
- Project documentation (this `docs/` folder, including ADRs)
- README.md
- Tests: test names, assertion messages

The reasoning: developer-facing content is read by an international audience (current contributors, future hires, open-source visitors, LLMs), benefits from the consistency of one language, and English has the largest pool of technical terminology.

---

## User-facing content

Content that a human user will read in the product follows the product's audience language.

<!-- BOOTSTRAP:USER_FACING_LANGUAGE:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> If the project is **monolingual (English only)**, this section can be left as: "User-facing content is also in English; no translation layer needed."
>
> If the project is **bilingual or multilingual**, this section defines:
>
> - Which language(s) the product targets and the locale code (e.g., `pt-BR`, `es-MX`, `fr-FR`).
> - Where user-facing translations live (i18n catalog, localization layer, dedicated files).
> - What surfaces are translated: UI labels, buttons, placeholders, tooltips, user-facing error messages, emails/SMS/notifications, dates/numbers/currency formatting, help text, legal text.
> - How third-party data and user-generated content are handled (typically stored as-is, no translation).
<!-- BOOTSTRAP:USER_FACING_LANGUAGE:END -->

---

## Domain terms that stay in another language

Technical or regulatory terms with no meaningful English equivalent stay in their original language, even in code.

When in doubt, ask: does this term have a precise meaning in its original language that translation would dilute? If yes, keep it.

<!-- BOOTSTRAP:DOMAIN_TERMS:START -->
> 📝 Filled by bootstrap prompt `BS-05-conventions.md` during the conventions step.
>
> List domain or regulatory terms specific to this project's jurisdiction or industry that should stay in their original language. For each, explain briefly why translation would lose precision.
>
> Examples of categories:
> - Tax and fiscal document types
> - Regulatory framework names (privacy law, financial regulation)
> - Industry-specific terminology that is recognized internationally in its original form
>
> Business terms with accepted translations follow English. The "ubiquitous language" of the domain is maintained in English throughout the code, except for terms that genuinely don't translate.
<!-- BOOTSTRAP:DOMAIN_TERMS:END -->

---

## LLM prompts

Prompts in `app/prompts/` (or equivalent) follow specific rules:

- **System prompts: written in English.** Models perform better in English, tokens are cheaper, and instructions are more consistent across providers.
- **Output instruction: explicit when the response must be in another language.** If the user-facing response must be in the product's language, the prompt ends with an explicit instruction — e.g., `"Respond in Brazilian Portuguese (pt-BR)."`. Never assume the model will infer the output language from the input.
- **User input passed to the LLM: untouched.** Whatever language the user wrote in. Do not pre-translate.

---

## Errors: the bridge between internal and user-facing

This is where developer-facing and user-facing policies meet. The pattern is consistent regardless of which languages are involved:

- Internal: exception classes, log events, error codes — all in English.
- User-facing: translation happens at the API or messaging boundary, mapping error codes to user-language messages.
- Domain and application layers never carry user-language strings.

<!--
PYTHON_REFERENCE_EXAMPLE — Error translation pattern

The author's project is bilingual (English internal, Portuguese pt-BR user-facing).

```python
# Internal (English) — exception, log, code
class OrderNotFoundError(DomainError):
    code = "order.not_found"

logger.warning("order.not_found", order_id=str(id))

# User-facing (Portuguese) — translation at the API boundary
ERROR_MESSAGES = {
    "order.not_found": "Pedido não encontrado.",
    ...
}
```

Translation lives in `app/api/exceptions/` — the HTTP boundary. Domain and application layers stay English-only.
-->

---

## Not covered by this policy

- **Third-party data** (supplier names, product descriptions, external API responses) — stored as received, in whatever language it arrived.
- **User-generated content** (chat messages, uploaded documents, form submissions) — stored as-is.
- **Analytics event names sent to external services** — English (consistency with internal events).

<!--
PYTHON_REFERENCE_EXAMPLE — Full bilingual policy

The author's project (Portuguese pt-BR user-facing, English internal) extends
the universal policy as follows. Use as reference for the level of detail
expected when filling the bilingual sections.

### User-facing content (Portuguese pt-BR)

Only content a user will see in the product:

- UI labels, buttons, placeholders, tooltips
- User-facing error messages (translated from internal English codes/exceptions)
- Emails, SMS, and notifications sent to users
- User-facing dates, numbers, and currency formatting
- Help text, onboarding copy, legal text (ToS, privacy policy)

### Brazilian domain terms — kept in Portuguese

Technical terms with no meaningful English equivalent stay in Portuguese, even in code:

- `CNPJ`, `CPF`, `RG`, `CEP`
- `NFe`, `NFSe`, `CTe` (fiscal documents)
- `Pix`
- Regulatory terms: `LGPD`, `ANPD`

These appear in class names, fields, and documentation as-is. Do not translate "CNPJ" to "tax ID" or similar — it loses precision and confuses everyone.

Business terms with an accepted translation follow English: `Order` (not `Pedido`), `Supplier` (not `Fornecedor`). The ubiquitous language of the domain is maintained in English throughout the code.
-->
