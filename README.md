# artifact-format

A small, instruction-only skill for readable ML experiment artifacts without unnecessary metadata or broken machine interfaces.

Keep a format that already works. When inspection is difficult, make the smallest useful change: expose the requested content, reuse existing provenance, and add a reading view only when needed.

## What it does

- Improves saved samples, generations, prompts, messages, and evaluation results intended for human inspection.
- Uses a reader-first layout: requested content before incidental provenance, unless the user asks for another order. Only a title and brief source identification belong before the content.
- Distinguishes payload from provenance by purpose, not field name: `seed`, `model_id`, and `split` may be the content being analyzed.
- Preserves required JSON/JSONL schemas, full text, whitespace, literal escapes, message order, and tool-related structure.
- Reuses existing provenance records and resolvable keys instead of mandating new IDs, manifests, hashes, or directory layouts.
- Respects the scope of authorized edits to existing files.

This is not an experiment tracker, trace collector, migration script, or general-purpose JSON formatter. It does not apply to unrelated configuration files or logs merely because of their format. It cannot enforce behavior independently of the agent using it.

## Install

For a fresh installation, clone the repository into the shared user skill directory:

```sh
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/zyecs/artifact-format.git "$HOME/.agents/skills/artifact-format"
```

If that destination or a host entry below already exists, inspect and back it up before replacing anything. Do not overwrite a locally customized skill. The link commands below intentionally do not use force.

### Codex

Codex discovers user skills in `~/.agents/skills`, so the clone above is sufficient. Use `/skills` to check discovery, or explicitly request:

```text
$artifact-format Save these evaluation outputs so I can inspect them without changing the consumer schema.
```

Codex can also select the skill implicitly when the task matches its description. Implicit selection is not guaranteed; this skill does not disable it. See the [Codex skills documentation](https://developers.openai.com/codex/skills/).

### Claude Code

Link the same copy into Claude Code's user skill directory:

```sh
mkdir -p "$HOME/.claude/skills"
ln -s "$HOME/.agents/skills/artifact-format" "$HOME/.claude/skills/artifact-format"
```

Then invoke `/artifact-format` with your task, or let Claude select it for a matching request. See the [Claude Code skills documentation](https://code.claude.com/docs/en/skills).

### Oh My Pi (OMP)

For the user-directory layout checked with OMP 18.2.5:

```sh
mkdir -p "$HOME/.omp/agent/skills"
ln -s "$HOME/.agents/skills/artifact-format" "$HOME/.omp/agent/skills/artifact-format"
omp read skill://artifact-format
```

Ask the agent to read `skill://artifact-format` and apply it to your artifact task. Other OMP versions or profiles may use a different skill directory.

If a newly installed skill is not visible, start a fresh host session. File discovery, explicit invocation, and automatic selection are different behaviors.

## Example

Suppose a downstream consumer requires this JSON record:

```json
{"prompt":"First line\nSecond line","response":"Keep literal \\n unchanged.","score":0.5}
```

Request:

> Make this saved evaluation record easy to read. Keep the machine file unchanged.

A suitable outcome is to keep that JSON untouched and create a nearby reading view identifying its source:

````markdown
Source: results.json

## prompt

```text
First line
Second line
```

## response

```text
Keep literal \n unchanged.
```

## score

0.5
````

The real newline is rendered, the literal backslash followed by `n` stays literal, and no extra provenance field is invented. If the original file is already easy to inspect, no companion is needed.

## Validation and limits

The reader-first revision was exercised through explicit file loading in Claude Code 2.1.270 (Opus 5), Codex CLI 0.154.0 (host default model), and OMP 18.2.5 (gpt-6-astra). Each host ran two independent trials on a nested artifact containing top-level provenance, system/user messages, and tool definitions inside message text. Each also ran a three-case boundary batch: metadata fields as analysis content, an explicit provenance-first request, and an already-readable file.

All six final nested trials placed the complete messages before long provenance, with source files unchanged and message strings preserved verbatim. Five also met the strict title/source-only preamble rule. One Claude trial retained a short preservation/safety note before the messages, so strict layout conformance is not fully achieved. All nine boundary cases passed their content, ordering, and source-preservation checks. Earlier failed outputs were retained locally rather than replaced by successful reruns.

These are bounded smoke observations, not a benchmark or a guarantee of automatic invocation. Claude Code's runs used safe mode; OMP disabled other skill/rule/extension discovery; Codex retained other installed skill context and encountered sandbox namespace errors before successful retries. No matched no-skill control was run. Automatic selection, large datasets, concurrent writes, and historical-file migration remain unverified. Raw local execution logs are not included in this repository.

## License

[MIT](LICENSE).
