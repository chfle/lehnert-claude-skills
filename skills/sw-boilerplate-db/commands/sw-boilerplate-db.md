# /sw-boilerplate-db

Creates database and ORM foundation files directly in the workspace root based on `requirements/tech-stack.yaml`.

## Usage

```
/sw-boilerplate-db
```

## Creates

`prisma/schema.prisma`, Flyway migrations, SQLAlchemy setup, TypeORM config, or GORM init – based on detected ORM.

## Requires

`requirements/tech-stack.yaml` (run `/sw-tech-stack-planner` first)
