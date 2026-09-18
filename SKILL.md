---
name: artifact-format
description: Improves readability of ML experiment artifacts saved for researchers to inspect, including samples, generations, prompts, messages, and probe or evaluation results, while preserving needed provenance and machine interfaces. Applies when producing such artifacts or fixing metadata clutter and hard-to-read text in them; not to arbitrary JSON, configuration, or logs merely because of their format.
---

# Artifact format

Make the requested content easy to find and read without losing its meaning or traceability. Keep an already adequate format; extra files and metadata are costs, not goals.

## Choose the smallest useful change

- Follow the user's requested fields, project conventions, and downstream readers first. Inspect relevant examples or consumers when needed. This skill does not authorize changing an existing schema or rewriting files outside the requested scope.
- Distinguish payload from provenance by its role in this task, not its name. A `seed`, `model_id`, or `split` being compared is payload. Do not remove, relocate, or add fields by a blanket key list.
- When the format is flexible, put the requested content ahead of incidental metadata. Move provenance only when it obstructs reading and the change is permitted. Reuse existing run records and identifiers; an existing key that resolves provenance is sufficient, without adding a redundant pointer field. If information is separated, keep its link to the artifact or record unambiguous. Do not mandate new IDs, manifests, hashes, or a directory layout. Preserve necessary existing provenance; report missing information rather than inventing it.

## Make text readable without changing it

- Use a human-readable format directly when no machine format is required. Keep required JSON/JSONL intact; add a nearby reading view only when escaping or structure materially hinders inspection. Do not create a companion for every file by default or duplicate a view that already serves the need.
- Render text with its actual line breaks. Decode the enclosing serialization once; a literal backslash followed by `n` inside the decoded text must stay literal. Do not summarize, truncate, trim, or rewrite content as a formatting step.
- For messages, preserve order, roles, and relevant structured content such as tool calls, content blocks, and tool responses. Use headings and safely delimited blocks where useful. Keep JSON-looking text as text unless its interpretation is established; render genuinely structured values legibly without dropping fields. Do not fetch referenced media just to format it.
- A reading view is derived, not a second source of truth. Make its source identifiable and regenerate it when that source changes within the task; do not independently edit the two copies.

## Existing artifacts

Only modify the files and aspects the user authorized. Inspect the format and consumers before an in-place change; prefer a separate reading view when the machine format must remain untouched. If an authorized change separates provenance, preserve it and its linkage before removing it from the original. Avoid overwriting unrelated files.

Check the resulting artifact, not just the formatting code: requested fields and full text remain, required parsers still accept it, and any provenance link or reading view points to the right source. Report changed paths and any unresolved format or traceability limitation briefly.
