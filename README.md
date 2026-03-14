# Premiere Agent

[![CI](https://github.com/sylphiette269/Premiere--agent/actions/workflows/ci.yml/badge.svg)](https://github.com/sylphiette269/Premiere--agent/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)

English README. For Chinese, see [README.zh-CN.md](./README.zh-CN.md).

`Premiere Agent` is a Windows-first monorepo for using `Claude Code` or
`Codex` with MCP tools to drive Adobe Premiere Pro for rough-cut video editing.

It is designed for assisted rough cuts, not final fully autonomous finishing.
The repository combines three execution layers under one runtime:

- `packages/premiere-mcp`
  Premiere Pro MCP server and CEP bridge
- `packages/audio-beat-mcp`
  Beat analysis and edit timing planning
- `packages/video-research-mcp`
  Reference-video research and blueprint generation
- `agent/`
  Gateway, planner, orchestrator, memory, critic, and reporter

## What This Project Is For

- Let `Claude Code` or `Codex` call MCP tools to inspect and operate Premiere
  Pro
- Build rough-cut timelines from a brief, a reference video, or a direct prompt
- Help with assembly planning, clip placement, beat-driven pacing, and review
- Keep the process inspectable through plans, checkpoints, and reports

## Supported Inputs

You can drive the workflow in three main ways:

- A Word document that describes the video requirements or editing steps
- A reference video that the system analyzes into a style blueprint
- A direct prompt such as `make a 15-second product rough cut`

In all cases, you should also give the AI the **source material folder path**.
Without the material directory, the agent does not know what local assets it is
allowed to plan with or import.

## How It Is Used

1. Start `Claude Code` or `Codex` with this MCP server connected.
2. Give the AI the local material folder path.
3. Optionally provide one of:
   - a `.docx` file with the brief or editing steps
   - a reference video path
   - a direct prompt
4. Let the AI scan the material folder, generate a plan, and call Premiere MCP
   tools for rough-cut work.
5. Review the result inside Premiere Pro and manually polish the final edit.

Typical examples:

```bash
npm run agent:dev -- "make a 15-second TikTok-style product video" --asset "D:/projects/product-assets"
npm run agent:dev -- "assemble a rough cut from the guide" --docx "D:/briefs/spot.docx" --manifest "D:/projects/product-assets/media.json"
npm run agent:dev -- "match the reference style" --editing-blueprint "D:/research/blueprint.json" --asset "D:/projects/product-assets"
```

## Requirements

To execute real Premiere steps, you need:

- Windows
- Node.js 18+
- Adobe Premiere Pro
- CEP enabled
- The CEP panel installed from `packages/premiere-mcp`

Recommended setup flow:

```bash
npm install
npm run build
npm test
npm run agent:dev -- "make a 15-second TikTok-style product video" --asset "D:/your-material-folder"
```

## Repository Layout

```text
repo-root/
├── agent/                  # orchestration and reporting layer
├── cli/                    # command-line entrypoint
├── scenarios/              # runnable end-to-end examples
├── packages/
│   ├── premiere-mcp/       # Premiere execution package
│   ├── audio-beat-mcp/     # beat-analysis package
│   └── video-research-mcp/ # research and blueprint package
└── .github/workflows/ci.yml
```

## Current Limitations

This project is still best described as a rough-cut assistant. These parts are
not fully stable yet:

- Keyframe animation adjustments are not reliable enough for unattended fine
  editing
- Transition insertion still depends on Premiere host behavior and QE DOM, so
  some transitions can fail or drift
- Effect application and effect parameter writes are not yet stable across all
  Premiere states and builds
- The output still requires manual checking and finishing inside Premiere Pro

If you want polished final delivery, treat this repository as a planning and
rough-cut system first, not as a guaranteed one-click finishing pipeline.

## Verification Status

This repository currently has:

- workspace build and test commands wired at the root
- CI configured on GitHub Actions
- MCP packages for Premiere, beat analysis, and reference research
- a root orchestration layer for planning and reporting

## Verification

Run from the repository root:

```bash
npm install
npm run build
npm test
npm audit --json
```

## Package Focus

- `packages/premiere-mcp` handles the actual Premiere bridge and MCP surface
- `packages/audio-beat-mcp` handles beat analysis and cut timing suggestions
- `packages/video-research-mcp` handles reference-video ingestion and blueprint
  generation
- `agent/` and `cli/` provide the top-level orchestration layer

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).

## Security

See [SECURITY.md](./SECURITY.md).
