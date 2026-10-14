<p align="center">
  <img src="app/assets/images/logomark.svg" width="160" alt="Forse logo" />
</p>

# Forse

**Forse** is a personal-finance app for people who want to self-host their own money
tracker, own their data, and adapt the app to their own country instead of a
US-centric default. It started as a fork of [Maybe Finance](https://github.com/maybe-finance/maybe)
(a great open-source personal finance app whose original team stopped maintaining it),
localized for Italy — but the underlying changes are a useful starting point for
anyone who wants to fork it again for their own market, language, or workflow.

> [!NOTE]
> This project is **not affiliated with, or endorsed by, Maybe Finance Inc.** "Maybe" remains
> a trademark of Maybe Finance Inc. This fork ships under its own name and logo ("Forse")
> specifically to avoid any confusion with the original product. The upstream project is no
> longer actively maintained (see their
> [final release](https://github.com/maybe-finance/maybe/releases/tag/v0.6.0)); this fork is
> maintained independently.

## Why fork it

Maybe Finance is built around the US market: dollars, 401(k), IRA, HSA, US bank
integrations. If you want to actually use it as a day-to-day money tool outside the US,
you typically need:

- a fully localized UI, not just a translated subset;
- account types and categories that match your own market (pension funds, local bank
  accounts, mortgages, local credit cards, etc.) instead of US-specific concepts;
- an AI assistant (auto-categorization, chat) that doesn't require a paid OpenAI
  subscription to be useful;
- a self-hosted setup you can run locally via Docker without depending on paid third-party
  services;
- your own name and branding, so you're not running a stranger's trademark on your own
  machine.

This repo shows one way to do all of that for Italy. Fork it again, swap the locale files,
account types, and branding for your own country, and you have a working starting point.

## What's different from upstream Maybe

- **Full rebrand**: renamed to "Forse", new logo and icon set (favicon, PWA icons, apple
  touch icon), page titles, PWA manifest, and all "Maybe AI" references renamed.
- **Full Italian localization**: every UI string (views, emails, validation messages, etc.)
  translated; `Family#locale` constrained to `it`. Swapping this for another locale means
  adding a new `config/locales/.../<locale>.yml` tree and relaxing that constraint.
- **Market-specific account model**: new account/asset types and subtypes tailored to Italy
  (e.g. an "Other Assets" type with a "Receivable credit" subtype for money owed but not yet
  collected) instead of the US-centric categories in the original project.
- **Free AI providers instead of paid-only OpenAI**: two alternative providers, both using
  OpenAI's Chat Completions-compatible API:
  - [Groq](https://console.groq.com) (`app/models/provider/groq.rb`) — preferred provider,
    much higher free daily quota;
  - [Google Gemini](https://aistudio.google.com/apikey) (`app/models/provider/gemini.rb`) —
    free fallback (capped at 20 requests/day on the free tier).

  If neither key is configured, the app falls back to OpenAI (paid), same as upstream. Both
  free providers retry automatically with backoff on transient errors (429/503).
- **Fixed a real data-corruption bug in the amount field**: it used a native
  `<input type="number">`, which silently drops the decimal comma when typing
  locale-formatted numbers (e.g. "1,235.31" in a comma-decimal locale could turn into a
  wrong number with no visible error). Fixed with a text input that normalizes comma/dot
  client-side (`app/javascript/controllers/money_field_controller.js`).
- **Docker-based self-hosted setup**, with a local image build and full stack (web, worker,
  PostgreSQL, Redis) meant for running locally.

## Running it locally (Docker)

```sh
git clone <this-repo>
cd forse
cp .env.example .env
# Fill in .env with your own keys (GROQ_API_KEY and/or GEMINI_API_KEY for AI features,
# SYNTH_API_KEY for exchange rates, etc. — see the comments in the file)

docker compose build
docker compose up -d
docker compose exec web bin/rails db:prepare
```

The app will be available on the port configured in `compose.yml` (e.g.
`http://localhost:3001`). On first load you'll be asked to create the admin user.

## Forking this for your own country or use case

If you want to adapt this for yourself rather than Italy specifically, the places to look
are:

- `config/locales/` — translation strings;
- `app/models/*.rb` (`SUBTYPES` constants on account/asset models) — account and asset
  types;
- `app/models/provider/` — AI provider integrations;
- `app/assets/images/` + `public/*.ico`/`*.png` — logo and icon set;
- `app/views/layouts/shared/_head.html.erb`, `app/views/pwa/manifest.json.erb` — branding and
  app name.

## License & attribution

Like the original project, this fork is distributed under the
[AGPLv3 license](https://github.com/maybe-finance/maybe/blob/main/LICENSE). "Maybe" remains a
trademark of Maybe Finance, Inc.; this fork makes no commercial use of it and doesn't present
itself as an official or affiliated product — hence its own name ("Forse") and logo.
