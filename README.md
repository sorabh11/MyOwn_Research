# MyOwn_Research

A personal knowledge and resource repository with automated documentation systems for research content organization and analysis.

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

## Key Resources

### 📊 ResearchNotes.md
A comprehensive overview of all research content, automatically generated and regularly updated. Perfect for quickly understanding what research materials are available.

### 🤖 Research Automation
The repository includes a Python script that automatically generates research summaries from PDFs and documents. Run `python3 generate_research_notes.py` to update summaries after adding new files.

## Getting Started

1. **Browse Research:** Start with `ResearchNotes.md` for an overview of all research content
2. **Explore Categories:** Navigate through the organized folder structure 
3. **Use Workflows:** Import n8n workflow JSON files for automation projects
4. **Update Summaries:** Run the research generator script when adding new files

## Recent Updates

**October 2025**
- Added automated research documentation system
- Generated comprehensive research summaries 
- Implemented process for keeping content easily accessible

---

*This repository serves as a centralized knowledge base for business research, automation workflows, and learning resources.*
