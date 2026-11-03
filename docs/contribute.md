# Contribute

Add focused pages that explain one backend concept and connect it to a
practical Rails or Ruby example.

Before opening a change:

- check for an existing page or link;
- state the intended audience and prerequisites;
- include a runnable example or concrete failure mode;
- link related pages;
- verify Markdown locally with `mkdocs build --strict`;
- prefer authoritative documentation and dated external resources.

Keep examples safe to copy: use fake credentials, bounded queries, and explicit
error handling. Open an issue when a topic needs a larger design discussion.

## Page standard

Use this structure for a substantial page:

1. Mental model and vocabulary.
2. Minimal working example.
3. Boundary and ownership.
4. Trade-offs and alternatives.
5. Failure modes and operational signals.
6. Testing strategy.
7. Practice exercise and self-check questions.
8. Related pages and authoritative resources.

Use headings that answer real questions. Define an acronym the first time it
appears. Prefer diagrams and tables when they clarify a flow or comparison.
Keep examples small enough to run and explain line by line.

## Review checklist

- Does the page distinguish correctness from performance?
- Are security, authorization, retries, and failure behavior addressed?
- Are code examples syntactically plausible and free of secrets?
- Are claims linked to official documentation where appropriate?
- Does the exercise test an invariant rather than repeat the prose?
- Are internal links valid and related pages named?

For video resources, prefer official conference or maintainer channels. Link
to a specific talk when it is stable; otherwise link to a topic search and
explain what the reader should evaluate.
