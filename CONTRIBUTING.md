# Contributing

1. Fork or branch from `main`.
2. Use Java 17 and run `mvn clean verify` before opening a pull request.
3. Keep controller, service, repository, DTO, and entity responsibilities separated.
4. Add tests for new business rules and error paths.
5. Never commit `.env` files, database dumps, tokens, or real customer data.
6. Describe schema changes in `docs/database-design.md`; production schema changes should be versioned as migrations.
