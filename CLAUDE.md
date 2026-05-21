# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is an **Obsidian knowledge base** — a collection of Markdown notes on backend engineering and distributed systems topics. There is no build system, test suite, or application code. The `.obsidian/` directory holds Obsidian app configuration (plugins, graph layout, workspace state).

## Content Structure

Notes are organized into topic folders, each containing Markdown files and an `imgs/` subdirectory for screenshots and diagrams:

- **AI/** — Knowledge graph concepts
- **Change Data Capture/** — CDC overview, Debezium setup with Kafka + Elasticsearch
- **Data structure & algorithm/** — DS&A roadmap
- **Microservices/Message Queues/** — Kafka, RabbitMQ, and general message queue theory
- **Microservices/gRPC/** — gRPC overview
- **Search engine/** — Elasticsearch
- **System design/** — High-level system design notes
- **Websocket/** — WebSocket protocol overview

## Note Conventions

- **Wikilinks** (`[[Note Name]]`) connect related notes — Obsidian resolves these across the vault.
- `**Related documents**` sections at the top of notes list linked pages.
- External images are embedded inline with standard Markdown image syntax.
- `alwaysUpdateLinks: true` is set in `.obsidian/app.json`, so Obsidian auto-updates wikilinks on file renames.

## Working with This Repo

When adding or editing notes, follow the existing pattern: a topic folder with a primary `.md` file, an `imgs/` folder for local images, and wikilinks to related notes. Keep headings hierarchical (`##`, `###`) as Obsidian uses them for the outline panel.
