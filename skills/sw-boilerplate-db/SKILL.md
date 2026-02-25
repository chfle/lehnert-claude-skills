---
name: sw-boilerplate-db
description: Use when user wants to create database and ORM files (Prisma schema, Flyway migration, SQLAlchemy models, TypeORM config) based on requirements/tech-stack.yaml directly in the workspace root.
version: 1.0.0
author: Lehnert
---

# SW Boilerplate DB

## Overview

Creates database schema, ORM config, and migration foundation files directly in the workspace root based on `requirements/tech-stack.yaml`. Silent – nothing is printed in chat except the completion line.

If `database.primary` is "None" in tech-stack.yaml, this skill does nothing and prints the completion line.

---

## Required Input

`requirements/tech-stack.yaml` must exist. If missing, stop:
> `requirements/tech-stack.yaml` not found. Run `/sw-tech-stack-planner` first.

---

## Files to Create

Read `database.primary`, `database.orm`, `database.migrations`, and `database.cache` from tech-stack.yaml.

### Prisma

| Path | Content |
|------|---------|
| `prisma/schema.prisma` | `datasource db` block (postgresql/mysql/sqlite from tech-stack.yaml), `generator client` block, placeholder `// Add models here` comment |

### TypeORM

| Path | Content |
|------|---------|
| `src/database/database.module.ts` | NestJS TypeOrmModule.forRootAsync() with ConfigService |
| `src/database/database.config.ts` | TypeORM DataSourceOptions from env vars |

### Hibernate / JPA (Spring Boot)

JPA config goes into `src/main/resources/application.yml` (already created by sw-boilerplate-structure). Add datasource and JPA sections if not already present. No extra files.

### jOOQ + Flyway (Spring Boot)

| Path | Content |
|------|---------|
| `src/main/resources/db/migration/V1__init.sql` | Empty migration with header comment: `-- V1: initial schema` |

### SQLAlchemy + Alembic

| Path | Content |
|------|---------|
| `database.py` | SQLAlchemy engine + SessionLocal + Base setup from env DATABASE_URL |
| `models/__init__.py` | Empty (already created by sw-boilerplate-structure if FastAPI) |
| `alembic.ini` | Alembic config pointing to `alembic/` directory |
| `alembic/env.py` | Alembic env with target_metadata from Base |
| `alembic/versions/.gitkeep` | Placeholder |

### GORM (Go)

| Path | Content |
|------|---------|
| `internal/database/database.go` | GORM db init function with postgres/mysql DSN from env |

### Redis (cache)

If `database.cache` is set (Redis, Valkey, etc.), add:

| Path | Content |
|------|---------|
| `src/cache/cache.module.ts` | NestJS: CacheModule.registerAsync() – only if NestJS detected |
| `cache/redis.go` | Go: Redis client init – only if Go detected |

---

## Output Rules

- Write all files silently to the workspace root.
- Never output file content, code snippets, or file lists in chat.
- At the end, print exactly one line:

```
✅ sw-boilerplate-db completed
```
