# Flow Music API (`flowmusic`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/audio-waveform.svg" width="820" alt="Music generation request path through the APIMart audio endpoint">
</p>

> **from $0.01 per generation** on the APIMart route — un seul endpoint compatible OpenAI sur `https://api.apimart.ai/v1`, sans abonnement mensuel. *(relevé le 2026-10-12)*

**[Obtenir une clé API](https://go.apimart.ai/k-560d58)** · **[Tarifs en direct](https://go.apimart.ai/k-526d98)** · **[Page du modèle](https://go.apimart.ai/k-3962ef)** · [⚡ Démarrage en 60 secondes](#quickstart)

**Pourquoi les équipes passent par APIMart pour Flow Music (`flowmusic`)**

- **Une clé, tout le catalogue.** La même base URL `https://api.apimart.ai/v1` et le même en-tête `Authorization` donnent accès à Flow Music (`flowmusic`) et à plus de 300 autres modèles d'image, de vidéo et de langage — changez le champ `model`, pas votre client.
- **$1 minimum, paiement à l'usage.** Ni abonnement ni forfait prépayé à dimensionner à l'avance : rechargez à partir de $1 et consommez-le en appels.
- **Le montant revient dans la réponse.** Chaque tâche terminée indique le montant facturé (`cost` / `credits_cost`) ; une tâche marquée `failed` est remboursée.
- **Asynchrone par conception.** Soumettez, récupérez le `task_id`, interrogez `GET /v1/tasks/{id}` — le traitement par lots et les retries relèvent d'une file d'attente ordinaire, pas d'une intégration sur mesure.

<!-- /conv-kit:v1 -->



Flow Music API on APIMart provides a production-ready AI music generation workflow for creating full songs from lyrics and style prompts, with vocals or instrumental output and asynchronous task polling.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Music generation (async task) | `flowmusic` | per generated track, by operation | see the [model page](https://go.apimart.ai/k-3962ef) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/music/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.075 | 0.06 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-526d98) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| model | string | — | the music model id |
| title | string | — | track title |
| sound_prompt | string | — | style / instrumentation description (one of sound_prompt or lyrics is required) |
| lyrics | string | — | lyrics text with section tags; leave empty for instrumental |
| bpm | string | number | — | tempo of the generated track |
| length | integer | — | target duration in seconds |
| audio_url | string | — | source audio for extend / cover / stem operations |
| callback_url | string | — | webhook called when the task completes |

The table lists the fields accepted by the APIMart `music/generations` endpoint. Check the [model page](https://go.apimart.ai/k-3962ef) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/music/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"flowmusic","title":"My Song","sound_prompt":"upbeat pop music with piano","bpm":"120","length":60}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/music/generations", headers=HEADERS, timeout=60, json={
    "model": "flowmusic", "title": "My Song", "sound_prompt": "upbeat pop music with piano", "bpm": "120", "length": 60,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["audio"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Quel est l'id du modèle Flow Music ?**

`flowmusic`. Voir le tableau des routes ci-dessus.

**Comment la facturation fonctionne-t-elle ?**

**from $0.01 per generation** on the APIMart route. La tâche terminée indique le montant exact dans son champ `cost` ; une tâche échouée est remboursée.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-560d58) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [flow-music-prompts-fr](https://github.com/zuzmf1d/flow-music-prompts-fr)
- **Same model, other languages** — 10 more language builds: English [flow-music-api](https://github.com/pxxezcu4cnmhmi/flow-music-api) · 中文 [flow-music-api-cn](https://github.com/yjdu5jxvsupfl/flow-music-api-cn) · Deutsch [flow-music-api-de](https://github.com/ow2s8cc1h/flow-music-api-de) · Español [flow-music-api-es](https://github.com/bg10uuih/flow-music-api-es) · Relay/gateway `flow-music-api-gateway` · Bahasa [flow-music-api-id](https://github.com/jyp5o6wi5bbg1/flow-music-api-id) · 日本語 `flow-music-api-ja` · 한국어 [flow-music-api-ko](https://github.com/bqiqlq4/flow-music-api-ko) · Português [flow-music-api-pt](https://github.com/qmxld2m6gcm/flow-music-api-pt) · Русский [flow-music-api-ru](https://github.com/tnscfa/flow-music-api-ru)
- **More models in this language** — [midjourney-api-fr](https://github.com/apimartt1q/midjourney-api-fr) · [gemini-2-5-flash-image-preview-api-fr](https://github.com/haftslut7ha/gemini-2-5-flash-image-preview-api-fr) · [nano-banana-api-fr](https://github.com/f84q06w110/nano-banana-api-fr) · [gemini-3-1-flash-image-preview-api-fr](https://github.com/ok71ckyc/gemini-3-1-flash-image-preview-api-fr) · [nano-banana-2-api-fr](https://github.com/ms0p12t/nano-banana-2-api-fr) · [gemini-3-pro-image-preview-api-fr](https://github.com/w-44vba/gemini-3-pro-image-preview-api-fr)
- **Topic hubs** — every build of this model, all languages: [flow-music](https://github.com/topics/flow-music) · [flow-music-api](https://github.com/topics/flow-music-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-3962ef> | `apimart.ai/fr/model/flow-music` |
| Live pricing | <https://go.apimart.ai/k-526d98> | `apimart.ai/fr/pricing` |
| Get an API key | <https://go.apimart.ai/k-560d58> | `apimart.ai/fr/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Flow Music is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
