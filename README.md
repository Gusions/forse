<p align="center">
  <img src="app/assets/images/logomark.svg" width="160" alt="Forse logo" />
</p>

# Forse

**Forse** è un **fork personale** di [Maybe Finance](https://github.com/maybe-finance/maybe),
un'app di finanza personale open source. Non è un progetto generico: è la mia installazione
privata, adattata, rinominata e localizzata per gestire i miei conti reali in Italia.

> [!NOTE]
> Questo fork **non è affiliato con, né approvato da, Maybe Finance Inc.** "Maybe" resta un
> marchio di Maybe Finance Inc.; questo progetto usa un nome e un logo propri (Forse) proprio per
> evitare qualsiasi confusione con il prodotto originale. Il progetto originale non è più
> mantenuto attivamente (vedi la loro
> [release finale](https://github.com/maybe-finance/maybe/releases/tag/v0.6.0)); questo fork
> continua a essere usato e modificato in autonomia, per uso personale.

## Perché questo fork

Maybe Finance è pensato per il mercato USA: dollari, 401(k), IRA, HSA, integrazioni bancarie
americane. Per usarlo davvero come strumento di finanza personale in Italia serviva:

- un'app completamente in italiano, senza residui di inglese nell'interfaccia;
- conti e categorie pensati per il mercato italiano (fondi pensione, conti correnti, mutui,
  carte di credito italiane, TFR, ecc.) invece dei concetti USA;
- un assistente AI (categorizzazione automatica, chat) che non richieda per forza un abbonamento
  a pagamento OpenAI;
- un'istanza self-hosted via Docker, utilizzabile in locale senza dipendere da servizi esterni
  a pagamento;
- un nome e un'identità propri, per non girare in locale con marchio e branding di terzi.

## Cosa è stato modificato rispetto al progetto originale

- **Rebranding completo**: nome cambiato in "Forse", nuovo logo e icone (favicon, PWA, apple
  touch icon) in tutta l'app, titoli di pagina, manifest PWA e testi "Maybe AI" → "Forse AI".
- **Localizzazione completa in italiano**: tutte le stringhe dell'interfaccia (viste, email,
  messaggi di validazione, ecc.) tradotte; `Family#locale` vincolato a `it`.
- **Modello dati adattato al mercato italiano**: nuovi tipi/sottotipi di conto e asset pensati per
  l'Italia (es. "Altri Attivi" con sottotipo "Credito da riscuotere" per crediti non ancora
  riscossi), al posto delle categorie USA-centriche del progetto originale.
- **AI gratuita al posto di OpenAI a pagamento**: aggiunti due provider alternativi, entrambi
  compatibili con l'API Chat Completions di OpenAI:
  - [Groq](https://console.groq.com) (`app/models/provider/groq.rb`) — provider preferito, quota
    gratuita giornaliera molto più alta;
  - [Google Gemini](https://aistudio.google.com/apikey) (`app/models/provider/gemini.rb`) —
    fallback gratuito (limitato a 20 richieste/giorno sul piano free).

  Se nessuna delle due chiavi è configurata, l'app ricade su OpenAI (a pagamento) come nel
  progetto originale. Entrambi i provider gratuiti hanno retry automatico con backoff sugli
  errori temporanei (429/503).
- **Bug fix sul campo importo**: il campo "importo" usava un `<input type="number">` nativo, che
  scarta silenziosamente la virgola quando si scrive in formato italiano (es. "1.235,31" diventava
  "500050" senza alcun errore visibile). Corretto con un campo di testo che normalizza virgola e
  punto lato client (`app/javascript/controllers/money_field_controller.js`).
- **Setup self-hosted via Docker**, con build locale dell'immagine e stack completo
  (web, worker, PostgreSQL, Redis) pensato per l'uso in locale.

## Avvio in locale (Docker)

```sh
git clone <questo-repo>
cd forse
cp .env.example .env
# Compila .env con le tue chiavi (GROQ_API_KEY e/o GEMINI_API_KEY per l'AI, SYNTH_API_KEY per i
# cambi valuta, ecc. — vedi i commenti nel file)

docker compose build
docker compose up -d
docker compose exec web bin/rails db:prepare
```

L'app sarà disponibile sulla porta configurata in `compose.yml` (es. `http://localhost:3001`).
Alla prima apertura verrà chiesto di creare l'utente amministratore.

## Licenza e attribuzione

Come il progetto originale, questo fork è distribuito con licenza
[AGPLv3](https://github.com/maybe-finance/maybe/blob/main/LICENSE). "Maybe" resta un marchio di
Maybe Finance, Inc.; questo fork non ne fa uso commerciale né si presenta come prodotto ufficiale
o affiliato — per questo ha un proprio nome ("Forse") e un proprio logo.
