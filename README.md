# llms-txt-validator

> Validate `llms.txt` against the proposed spec. CLI + GitHub Action.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![CI](https://img.shields.io/badge/CI-passing-green.svg)]()

## Status: honest

As of May 2026, **no major LLM provider has publicly confirmed they fetch `llms.txt`** for retrieval, citation, or training. Multiple independent studies (Codersera, SE Ranking, aeo.press) show no measurable causal lift from deploying it. Adoption sits at ~10% of indexed domains.

So why validate it? Three reasons:

1. **Optionality.** If OpenAI / Anthropic / Perplexity start consuming it tomorrow, you want a valid file already deployed.
2. **IDE-side agents** (Cursor, Cline, Aider, Continue) increasingly consume `llms.txt` for codebase / documentation navigation. This use case is real and growing.
3. **Hygiene signals** — a malformed `llms.txt` looks worse on a technical audit than no file at all.

If a vendor sells you `llms.txt optimization` as a citation-lift tactic, they are selling vapor in 2026. We built this validator partly so you can verify your own file is well-formed without paying anyone.

Read our full anti-hype take: [surgio.pages.dev/en/blog/llms-txt-implementation-guide/](https://surgio.pages.dev/en/blog/llms-txt-implementation-guide/)

## What it does

Validates a `llms.txt` file against the Answer.AI proposed spec (September 2024 baseline + community-accepted extensions):

1. **Structure**: required title (`# Title`), summary paragraph, optional H2 sections with bullet lists
2. **URLs**: every linked URL is reachable (HEAD request, configurable timeout), HTTPS preferred, no redirects to unrelated origins
3. **Markdown**: valid CommonMark, no broken links
4. **Size**: warns if `llms.txt` is over 100KB (LLM context windows have improved but this is still a soft ceiling for retrieval)
5. **Coverage**: optional check that `llms-full.txt` exists if `llms.txt` is short
6. **Sitemap parity**: optional check that URLs in `llms.txt` also appear in `sitemap.xml`

## Install

```bash
npm install -g @surgio-aeo/llms-txt-validator
# or use without install:
npx @surgio-aeo/llms-txt-validator https://example.com/llms.txt
```

## Use

```bash
# Validate a remote file
llms-txt-validator https://surgio.pages.dev/llms.txt

# Validate a local file
llms-txt-validator ./llms.txt

# Strict mode (warnings become errors, useful for CI)
llms-txt-validator --strict https://example.com/llms.txt

# Check sitemap parity
llms-txt-validator --sitemap https://example.com/sitemap.xml https://example.com/llms.txt
```

## Use as GitHub Action

Add to `.github/workflows/llms-txt.yml`:

```yaml
name: Validate llms.txt
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: surgio-aeo/llms-txt-validator-action@v1
        with:
          file: ./llms.txt
          strict: true
```

## Output

Exit code 0 = valid. Exit code 1 = errors. Exit code 2 = warnings only (strict mode treats as failure).

```
$ llms-txt-validator https://surgio.pages.dev/llms.txt
✓ Title found: "Surgio — Performance SEO Agency"
✓ Summary paragraph present (143 chars)
✓ 7 H2 sections detected
✓ 38 URLs total, all reachable (HEAD 200)
✓ Markdown valid
✓ File size: 9.6 KB (under 100 KB threshold)
⚠ llms-full.txt not detected at /llms-full.txt
✓ Sitemap parity: 38/38 URLs in llms.txt also appear in sitemap.xml

Result: PASS (1 warning)
```

## Spec compliance

We track the Answer.AI proposed spec at https://github.com/AnswerDotAI/llms-txt (no IETF RFC exists as of May 2026). When that spec moves, we move with it. Breaking changes are flagged in CHANGELOG.md.

## Contributing

Anonymous contributions accepted. PRs welcome for:
- Additional validation rules (with citation to spec section)
- New language localizations for error messages
- Performance improvements for parsing large files

Issues > PRs for feature requests.

## License

MIT.

## Topics

`llms-txt` `validator` `ai-search` `aeo` `seo` `markdown` `cli` `github-action`
