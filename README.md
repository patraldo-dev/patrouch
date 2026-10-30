# patrouch.ca

A personal, trilingual (es · en · fr) writing room on the web — built by [Christophe R Patraldo](https://patrouch.ca) for his own daily writing practice, shared here as architecture, not product.

> Un cuarto de escritura personal en tres idiomas. Une pièce d'écriture personnelle en trois langues.

This is **not a business venture**. It's one writer's tool that grew a community-shaped garden. If it appeals to you, that's a gift, not a funnel.

## What it is

- **Daily estímulo** — one community writing prompt per day per language, AI-generated on demand, cached in the database; accept it or pass (three passes max).
- **Sappho, a resident AI writer** — publishes weekly in all three languages, answering the community prompt alongside the humans.
- **The Oulipo game** — any member can *reserve* a word (from their own published writing), and from that moment Sappho may never use it again — in any piece, in any form, forever. The pool only grows. She writes under the full cumulative ban list, a verification pass scans her output, violations trigger rewrites, and anything that still slips through is reported rather than hidden. She's our Gulliver; the community are Lilliputians, tying her down — in vain, sometimes.
- **«La palabra prohibida»** — Sappho's weekly serialized story, where every reserved word has *disappeared from the world* of the story. The constraint is diegetic.
- **Dictée** — classic listen-and-write practice: Sappho's latest text read slowly (browser speech synthesis, three-reading protocol), typed or **handwritten on paper** — photograph your page, an AI vision model transcribes it, you review, then a word-level LCS diff scores you and draws approximate red-ink marks over your own handwriting on a canvas.
- **Handwriting import for writers** — photograph or scan any handwritten page, review the AI transcription (with a suggested title), and publish with the original page attached as a facsimile.
- **Hands-free dictation** — a wake-word voice engine ("patraldo") built on the Web Speech API, so you can write by voice in three languages.
- **Projects & serials** — writings grouped into ordered, followable sequences; followers get a bell notification when the next part publishes.

## How it's built

| Layer | Choice |
|---|---|
| App | SvelteKit 3 (Svelte 5 runes), deployed to Cloudflare Workers |
| Database | Cloudflare D1 (SQLite at the edge) |
| Objects | Cloudflare R2 + Cloudflare Images (manuscript facsimiles, avatars) |
| Auth | better-auth (email + GitHub + Google OAuth) |
| AI | Cloudflare Workers AI — `llama-4-scout` (Sappho, prompts), `llama-3.2-11b-vision` (handwriting OCR + moderation), `bge-m3` embeddings (search via Vectorize) |
| Voice | Web Speech API — `SpeechRecognition` (dictation, wake word), `speechSynthesis` (dictée) |
| i18n | Flat dot-key dictionaries, three locales, ~1,200 keys each |
| Cron | A separate cron worker fans out to API endpoints (Sappho's weekly run, reindexing, drift simulations) |

Interesting implementation details:

- The **Oulipo verification** is deliberately simple and public: accent- and case-insensitive exact-word scanning of generated text, with the matched forms surfaced. Inflection stemming (plurals, conjugations) is planned via Snowball stemmers for es/en/fr.
- The **red-ink overlay** on handwritten dictées is fully client-side canvas work: adaptive thresholding via integral images, connected-component letter blobs, line clustering, word merging, then the diff verdicts are mapped back onto word positions. No external OCR service.
- **Escaped words** — when Sappho uses a forbidden word despite retries — are reported, not hidden. The AI failing in public is part of the game.

## The code

The full source runs this site in production, but it isn't packaged for self-hosting (it contains account wiring, and its migrations don't yet build a complete database — the production schema evolved ahead of them). **If you'd like to run your own instance or read the source, ask** — [ishmael@patrouch.ca](mailto:ishmael@patrouch.ca) — and it's yours to read or fork informally.

## License

The architecture and ideas here are free to steal, kindly. The name and the garden are mine.
