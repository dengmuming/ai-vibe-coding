# Third-Party Skill Notices

## mattpocock/skills

- Source: <https://github.com/mattpocock/skills>
- Upstream commit: `ed37663cc5fbef691ddfecd080dff42f7e7e350d`
- Imported productivity skills: `grill-me`, `grilling`, `teach`, `writing-great-skills`
- Imported engineering main chain: `grill-with-docs`, `to-spec`, `to-tickets`, `implement`, `grill-code-review`
- Imported engineering dependencies: `domain-modeling`, `tdd`, `setup-matt-pocock-skills`
- Imported engineering exploration: `research` (from `research`)
- Main chain: `grill-with-docs -> to-spec -> to-tickets -> implement -> grill-code-review`
- Local adaptations: normalized frontmatter for cross-agent compatibility, added source metadata, replaced Claude Code slash-command references with skill names, added a sequential fallback when parallel sub-agents are unavailable, and merged the original `code-review` correctness, security, performance, and actionable-feedback baseline into `grill-code-review`. The `code-review` name has been reused for a new skill adapted from deepseek-harness (see below). `research` additionally falls back to in-session research when the agent cannot delegate to a sub-agent.

### MIT License

Copyright (c) 2026 Matt Pocock

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## deepseek-ai/deepseek-harness

- Source: <https://github.com/deepseek-ai/deepseek-harness>
- Upstream paths: `.agents/skills/dsh-code-review/SKILL.md`, `.agents/skills/dsh-prose-standard/SKILL.md`, `.agents/skills/dsh-trim-cot-leakage/SKILL.md`, `.agents/skills/dsh-pre-push-checks/SKILL.md`, `.agents/skills/dsh-find-simplifications/SKILL.md`
- Imported skills: `code-review` (from `dsh-code-review`), `prose-standard` (from `dsh-prose-standard`), `trim-cot-leakage` (from `dsh-trim-cot-leakage`), `pre-push-checks` (from `dsh-pre-push-checks`), `find-simplifications` (from `dsh-find-simplifications`)
- Local adaptations: removed deepseek-harness-specific path references (AGENTS.md, docs/defensive-patterns.md, packages/AGENTS.md, Agent Notes, docs/i18n/) and replaced with generic equivalents, normalized frontmatter for cross-agent compatibility. `find-simplifications` had Agent Notes-specific sections (Coalesce Superseded Agent Notes, Write The Agent Note) removed and remaining Agent Notes references generalized to design notes / ADRs. Content otherwise preserved from upstream.

## KKKKhazix/khazix-skills

- Source: <https://github.com/KKKKhazix/khazix-skills>
- Upstream path: `neat-freak/`
- Upstream commit: `4f2db09802736ac8130ddf8dd6121435b5a41b55`
- Imported skills: `neat-freak` (from `neat-freak`)
- Local adaptations: renamed the skill to `neat-freak` (frontmatter `name`, trigger word in `description`, and the banner/comment in `scripts/audit-inventory.sh`), added `license` and source metadata to frontmatter, and did not import the upstream `evals/` directory (self-test fixtures not shipped with our skills). Content otherwise preserved from upstream, including the "洁癖" and "/neat" trigger words.

### MIT License

Copyright (c) 2026 数字生命卡兹克

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## charlie947/show-me

- Source: <https://github.com/charlie947/show-me>
- Upstream path: `skills/show-me/`
- Upstream commit: `69e488e301e8d6f43b8ddc883cb2392b1dd45264`
- Imported skills: `show-me` (from `show-me`)
- Local adaptations: renamed the skill to `show-me` (frontmatter `name`), added `license` and source metadata to frontmatter. Content otherwise preserved from upstream.

### MIT License

Copyright (c) 2026 Charlie Hills

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## cloudflare/security-audit-skill

- Source: <https://github.com/cloudflare/security-audit-skill>
- Upstream path: `skills/security-audit/`
- Upstream commit: `c1c8a8c1471069fb0e188eeaff69b8e8db6564a8`
- Imported skills: `security-audit` (from `security-audit`)
- Local adaptations: renamed the skill to `security-audit` (frontmatter `name`), added `license` and source metadata to frontmatter, and did not import the upstream `validate-coverage-ledger.test.cjs` / `validate-findings.test.cjs` (self-test fixtures that `node --test` would pick up and that require POSIX-only file APIs). Content otherwise preserved from upstream, including the companion domain files, `report-schema.json`, and the findings/coverage validators.

### MIT License

Copyright (c) 2025-2026 Cloudflare, Inc.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
