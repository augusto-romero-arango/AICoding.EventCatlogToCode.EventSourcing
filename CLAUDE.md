# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Structure

This repository contains a hybrid project combining EventCatalog documentation with a .NET event-sourced application template:

- **EventCatalog Documentation**: Located in `{application-name}/{application-name}-catalog/` directories
- **.NET Solution**: Located in `{application-name}/src/{solution-name}/` directories
- **Claude Commands**: Custom slash commands in `.claude/commands/` for EventCatalog operations
- **Specifications**: Detailed implementation specs in `.claude/specs/eventcatalog/` for domain creation

## EventCatalog Domain Management

Normalization rules for Eventcatalog objects:
- Names are automatically normalized (lowercase, underscores, no special characters)
