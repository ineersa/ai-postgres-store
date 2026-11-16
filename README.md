PostgreSQL pgvector Store
=========================

Provides [PostgreSQL with pgvector](https://github.com/pgvector/pgvector) vector store integration for Symfony AI Store.

`Store::setup()` creates the table, the vector index, and a matching GIN expression
index on `to_tsvector(<lang>, metadata->>'_text')` for text and hybrid queries that
use that same expression. Existing tables created before this change need that GIN
index added separately; calling `setup()` again on a populated table creates it
without `CONCURRENTLY`.

This package names the GIN index `<table>_text_gin` when that fits PostgreSQL's
63-byte identifier limit, otherwise a deterministic SHA-256 prefix of the full
table name plus `_text_gin`. Future upstream packages may use a different
`COALESCE(...)` expression under the same deterministic name for a given table.
Upgrading must replace or rebuild that GIN index with the new expression;
`CREATE INDEX IF NOT EXISTS` keeps the old index. Concurrent replacement of live
tables belongs outside transactions and is not performed by `setup()`.

pgvector Documentation
----------------------

 * [pgvector (operators, indexes, vector/halfvec types)](https://github.com/pgvector/pgvector)
 * [PostgreSQL full-text search](https://www.postgresql.org/docs/current/textsearch.html)
 * [Creating matching full-text expression indexes](https://www.postgresql.org/docs/current/textsearch-tables.html#TEXTSEARCH-TABLES-INDEX)
 * [Preferred full-text index types (GIN)](https://www.postgresql.org/docs/current/textsearch-indexes.html)

Resources
---------

 * [Contributing](https://symfony.com/doc/current/contributing/index.html)
 * [Report issues](https://github.com/symfony/ai/issues) and
   [send Pull Requests](https://github.com/symfony/ai/pulls)
   in the [main Symfony AI repository](https://github.com/symfony/ai)
