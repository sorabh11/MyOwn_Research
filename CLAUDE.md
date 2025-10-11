# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a personal knowledge and resource repository organized into the following categories:

### Research/
Business research and analysis for field sales force automation (SFA) in the Indian market:
- **MVP/**: Sales Buddy WhatsApp bot specifications and features
- **Competition/**: Competitor analysis (Bizom, FieldAssist, QuickSell) including decks, demos, and pricing
- **Market Sizing/**: Industry breakdowns for Indian retail and field force

### n8n workflows/
Automation workflow configurations for n8n platform:
- **sorabhvij-workflows/**: Active workflows including Handsfree (multiple versions), Telegram Q&A, Geo-tagging, and AI agents
- **Versioning/**: Dated backups and execution logs
- Workflows are stored as JSON files with IDs in format: `{ID}-{Name}.json`

### Evaluations/
AI/ML evaluation resources and documentation

### Others/
Mixed resources including AI guides (agents, evals, LangChain, audio transcription), context engineering docs, and personal materials

### Vibe coding/
Programming learning materials covering beginner concepts, Git workflows, and advanced coding patterns

## Working with n8n Workflows

- Workflow files are JSON exports from n8n that can be imported back into the platform
- The "Handsfree" workflow has multiple versions tracking feature evolution
- Check `Versioning/` folder for historical workflow states and execution outputs
- Workflow naming convention: `{workflow-id}-{Descriptive_Name}.json`

## Document Types

This repository contains primarily PDFs and JSON files. When asked to analyze or summarize content:
- Research documents focus on SFA market analysis for Indian SMBs
- n8n workflow JSONs contain node configurations and automation logic
- Reference materials cover AI/ML topics and programming fundamentals

## Research Notes Generation

The repository includes an automated system for generating research summaries:
- **ResearchNotes.md**: Auto-generated summary of all Research/ folder contents
- **generate_research_notes.py**: Script to regenerate research summaries
- Run `python3 generate_research_notes.py` to update research notes after adding new files
- Supports PDF, Markdown, and text file extraction

### Technical Implementation Details
- **PDF Processing**: Uses PyPDF2 library, extracts first 3 pages for summaries
- **Content Extraction**: Handles text files, markdown files, and PDFs
- **Output Format**: Structured markdown with table of contents and category grouping
- **File Organization**: Groups files by subfolder (Competition, MVP, Market Sizing, etc.)
- **Summary Generation**: Limits summaries to ~500 characters for readability
- **Error Handling**: Gracefully handles corrupted or unreadable files

### Commands for AI Assistance
When working with this repository:
1. Always check ResearchNotes.md first for context about available research
2. Use `python3 generate_research_notes.py` to refresh summaries after file additions
3. Install PyPDF2 if missing: `pip3 install PyPDF2`
4. Verify research content by reading specific files in Research/ subdirectories
