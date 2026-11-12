# Migration Counseling Bot

A Telegram consultation assistant for questions about migrating to Germany (skilled-worker routes, recognition of qualifications, visas). It answers with **retrieval-augmented generation (RAG)** over official sources and real community Q&A, and is built to avoid guessing on legal or procedural topics.

> The source code of this project is private (client work). This page describes what it does and how it is built. I am happy to walk through the design in an interview.

## How it answers
- **Official-source retrieval:** a translated copy of official government/portal content is indexed with sentence embeddings and searched with **FAISS**. For sensitive topics (visa, recognition, legal) an official source is required before the bot gives a firm answer.
- **Community Q&A retrieval:** a second index built from real Telegram community discussions finds direct question/answer pairs, with near-duplicate detection based on text similarity, token overlap and semantic similarity.
- **Strict mode with a second verification pass:** an LLM judge checks whether the retrieved evidence directly supports the exact process and stage the user asked about. A second pass verifies the draft answer before it is sent.
- **Graceful fallback:** when no direct source exists, the bot gives guidance based on related sources and states its limits, instead of an empty refusal or a made-up rule.

## Conversation handling
- **Per-user conversation memory** (SQLite) with a time limit and a cap on stored turns.
- **Semantic frame and topic-boundary detection:** decides whether a message continues the current topic or starts a new one, and resolves follow-ups such as "what about for my case?" against recent turns.
- **LLM turn adjudicator:** compares each message against the bot's recent clarifying questions and pending state, with local rules kept as a safe fallback.
- **Consultation-first UX:** the bot gives useful guidance before asking for more information and asks at most one clarifying question.

## Bot features
- Built with **aiogram** (async) and finite-state-machine flows for user registration and profile editing.
- Long answers are split for Telegram's message limits, with a processing notice for slow queries.
- An operator command shows the retrieved context behind an answer (used for testing).
- A built-in **CLI test suite** (retrieval, routing, semantic frame, topic boundary, conversation fallback, consultant policy) that runs without Telegram or LLM credentials.

## Stack
Python, aiogram, FAISS, sentence-transformers (MiniLM embeddings), SQLite, an OpenAI-compatible LLM API (DeepSeek), NumPy, openpyxl.

## How it is run
- Runs as its own service on a Linux server.
- Maintenance procedures are written up as a runbook, and the project was handed over with documentation.

## Context
Built at Arika (2024 - 2026) as one of five production services I delivered and handed over.

## Contact
Amir Hossein Fotoohi - ahfotoohi@gmail.com - [LinkedIn](https://linkedin.com/in/amir-hossein-fotoohi-411429390)
