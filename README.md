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

Unrelated configuration files and logs are outside its scope.

## Install

For a fresh installation, clone the repository into the shared user skill directory:

```sh
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/zyecs/artifact-format.git "$HOME/.agents/skills/artifact-format"
```

If an installation target already exists, inspect and back it up before replacement.

### Codex

Codex discovers user skills in `~/.agents/skills`, so the clone above is sufficient. Use `/skills` to check discovery, or explicitly request:

```text
$artifact-format Save these evaluation outputs so I can inspect them without changing the consumer schema.
```

Codex can also select the skill implicitly when the task matches its description. See the [Codex skills documentation](https://developers.openai.com/codex/skills/).

### Claude Code

Link the same copy into Claude Code's user skill directory:

```sh
mkdir -p "$HOME/.claude/skills"
ln -s "$HOME/.agents/skills/artifact-format" "$HOME/.claude/skills/artifact-format"
```

Then invoke `/artifact-format` with your task, or let Claude select it for a matching request. See the [Claude Code skills documentation](https://code.claude.com/docs/en/skills).

### Oh My Pi (OMP)

Link the shared copy into OMP's user skill directory:

```sh
mkdir -p "$HOME/.omp/agent/skills"
ln -s "$HOME/.agents/skills/artifact-format" "$HOME/.omp/agent/skills/artifact-format"
omp read skill://artifact-format
```

Ask the agent to read `skill://artifact-format` and apply it to your artifact task. Other OMP versions or profiles may use a different skill directory.

If a newly installed skill is not visible, start a fresh host session.

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

## License

[MIT](LICENSE).
