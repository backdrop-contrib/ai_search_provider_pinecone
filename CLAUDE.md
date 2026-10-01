# AI Search Pinecone — Dev Notes

Vector storage backend for the `ai_search` framework (see `../ai_search/CLAUDE.md` for the overall architecture). This module is its own git repo/project (`backdrop-contrib/ai_search_provider_pinecone`), a sibling of `ai_search`, not a submodule.

See `README.md` in this directory for requirements and server configuration (Key-module credentials, namespace, top-K) — don't duplicate that here.

## Code Map

- `ai_search_pinecone.module` — registers the `pinecone` Search API service backend
- `includes/AIPineconeService.inc` — `AIPineconeService extends SearchApiAbstractService` (note: does **not** implement `AiSearchBackendInterface`, unlike the other three provider backends — check this if adding shared-interface logic in `ai_search` core)
- `includes/AiSearchPineconeVectorClient.php` — `AiSearchPineconeVectorClient extends AiSearchVectorClientBase`; HTTP client for the Pinecone API (`query()`, `upsert()`, `stats()`)

## Known Constraints

- Requires the Key module for API key and base URL storage — no raw secrets in config.
- Namespace is a prefix; the Search API index machine name is always appended automatically, so namespaces aren't reusable across indexes as-is.
- Embedding dimension must match the target Pinecone index's configured dimension exactly (Pinecone indexes are fixed-dimension).

## Query Embedding

`AIPineconeService::search()` obtains its query embedding through the shared `ai_search_resolve_query_embedding()` helper (in `ai_search`), which applies `ai_search_apply_embedding_task_prefix($text, $model, 'query')` for asymmetric embedding models. Don't reimplement the embedding call here; keep it in the shared helper so all provider backends stay in sync.

**Last Updated:** 2026-07-07 by Claude
