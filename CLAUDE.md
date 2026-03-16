# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a collection of **Claude Code skills** — reusable prompt-based tools that extend Claude Code's capabilities. Each skill lives in its own directory with a `SKILL.md` file defining its behavior, triggers, and reference materials.

## Repository Structure

- Each top-level directory (e.g., `architecture-designer/`, `bruno-api-client/`) is an independent skill
- `.agents/skills/` contains installed skills from external sources (e.g., `skill-creator` from `anthropics/skills`)
- `skills-lock.json` tracks installed external skill versions and hashes

## Skill Anatomy

A skill is a directory containing:
- **`SKILL.md`** — The skill definition with YAML frontmatter (`name`, `description`, `metadata`) and markdown body describing the role, workflow, constraints, and output templates
- **`references/`** (optional) — Supporting reference documents loaded on-demand based on context

### SKILL.md Frontmatter

```yaml
---
name: skill-name
description: When to trigger this skill (used for matching user intent)
license: MIT
metadata:
  author: github-url
  version: "1.0.0"
  triggers: comma, separated, trigger, keywords
---
```

The `description` field is critical — it controls when Claude Code activates the skill. It should describe user intent patterns, not what the skill does internally.

## Current Skills

- **architecture-designer** — System design, architecture reviews, ADRs, scalability planning. Has reference docs for patterns, database selection, NFR checklists, and ADR templates.
- **bruno-api-client** — Generates Bruno API client collection files (`.bru`) from API route definitions.

## Creating/Modifying Skills

Use the `skill-creator` skill (`/skill-creator`) which provides an iterative workflow: draft a skill, run eval prompts, review results, refine, and optimize the description for triggering accuracy.
