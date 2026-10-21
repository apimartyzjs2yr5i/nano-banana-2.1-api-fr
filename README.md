# Gemini Nano Banana 2.1 API (`gemini-nano-banana-2.1`) — passerelle API (Français)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **$0.02 per image** at 1K on the Ext route (2K $0.025, 4K $0.03) — one OpenAI-compatible endpoint at `https://api.apimart.ai/v1`, no monthly plan required. *(observed 2026-10-12)*

**[Obtenir une clé API](https://go.apimart.ai/k-ac44a5)** · **[Tarifs à jour](https://go.apimart.ai/k-e4755a)** · **[Page du modèle](https://go.apimart.ai/k-d0d295)** · [⚡ 60-second quickstart](#quickstart)

**Why teams call Gemini Nano Banana 2.1 (`gemini-nano-banana-2.1`) through APIMart**

- **One key, entire catalog.** The same `https://api.apimart.ai/v1` base URL and `Authorization` header reach Gemini Nano Banana 2.1 and 300+ other image, video and language models — switch the `model` field, not your client.
- **$1 minimum, pay as you go.** No subscription and no prepaid plan to size up front: top up from $1 and spend it on calls. There is no free quota to burn through first, so the price in this table is the price you pay.
- **The charge comes back in the response.** Every completed task reports the amount billed (`cost` / `credits_cost`), so a spend number is read per call instead of guessed at month end. A task marked `failed` is refunded.
- **Async by design.** Submit, take the `task_id`, poll `GET /v1/tasks/{id}` — batching and retries are ordinary queue work, not a bespoke integration.

<!-- /conv-kit:v1 -->

Gemini Nano Banana 2.1 : une seule `base_url` compatible OpenAI et une seule clé donnent accès à Gemini Nano Banana 2.1 (`gemini-nano-banana-2.1`) et à plus de 300 modèles image, vidéo et langage — facturation en USD, à l'usage, à partir de 1 $.

Gemini Nano Banana 2.1 handles text-to-image generation and reference-image editing at 1K, 2K or 4K, with `auto` plus fourteen aspect ratios including the extra-wide and extra-tall `1:4`, `4:1`, `1:8` and `8:1`.

## Model id and routes

| Route | `model` value | Billing | Images per task | Notes |
| --- | --- | --- | --- | --- |
| Official (Google route) | `gemini-nano-banana-2.1` | per million tokens (input + output, thinking included) | `n` = 1–4 | an image-output figure is an estimate, not the full task price |
| Ext (third-party route) | `gemini-nano-banana-2.1-ext` | per delivered image, by resolution | `n` = 1 | supports `official_fallback`; a fallback task bills by official tokens |

Endpoint: `POST https://api.apimart.ai/v1/images/generations` (OpenAI-compatible), then poll `GET /v1/tasks/{task_id}`.
Result links expire **72 hours** after completion.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| default | $0.0375 | $0.02 |
| 4K | $0.05 | $0.03 |

The token-billed official route bills per million tokens instead: `text_input` $1.60, `cached_text_input` free, `image_input` $1.60, `cached_image_input` free, `text_output` $9.60, `image_output` $96.00.
<!-- pricing:model:end -->

Nano Banana 2.1 does **not** support 0.5K. Prices are a snapshot; the [Tarifs à jour](https://go.apimart.ai/k-e4755a) page and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | `gemini-nano-banana-2.1` (official) or `gemini-nano-banana-2.1-ext` (per-image) |
| `prompt` | string | required | text-to-image description, or what to change when editing |
| `size` | string | `auto` | `auto`, `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`, `1:4`, `4:1`, `1:8`, `8:1` |
| `resolution` | string | `1K` | `1K`, `2K`, `4K` — cost rises with resolution |
| `n` | integer | `1` | official route accepts 1–4; the Ext route accepts only `1` |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs; PNG, JPEG or WEBP |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting, adding latency |
| `official_fallback` | boolean | `false` | Ext only: if the third-party route fails, complete on the official route and switch to token billing |

**Reference images.** The official route allows up to 20 MB per reference; the Ext route allows 30 MB per reference and 50 MB combined. In a reference-image workflow `auto` follows the reference ratio.

Supported aspect ratios: `auto`, `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`, `1:4`, `4:1`, `1:8`, `8:1`.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"gemini-nano-banana-2.1","prompt":"A bamboo forest path under moonlight","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "gemini-nano-banana-2.1", "prompt": "A bamboo forest path under moonlight",
    "size": "1:1", "resolution": "1K",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

```javascript
const res = await fetch("https://api.apimart.ai/v1/images/generations", {
  method: "POST",
  headers: { Authorization: `Bearer ${process.env.APIMART_API_KEY}`, "Content-Type": "application/json" },
  body: JSON.stringify({ model: "gemini-nano-banana-2.1", prompt: "A bamboo forest path under moonlight",
                          size: "1:1", resolution: "1K" }),
});
const { data } = await res.json();      // data.id is the task id — poll /v1/tasks/<id>
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## Sample outputs

Each row is one API call. Costs follow the price table above.

| Output | Recipe | Ratio | Cost | Prompt |
| --- | --- | --- | --- | --- |
| <img src="assets/01-product-hero-perfume.jpg" width="220" alt="Sample render via the APIMart image API"> | Product / e-commerce | 1:1 | $0.02 | `Studio hero shot of a frosted glass perfume bottle on a wet black stone slab, single softbox from the left, faint mist, deep charcoal background, crisp label text, commercial product photography` |
| <img src="assets/02-food-overhead-ramen.jpg" width="220" alt="Sample render via the APIMart image API"> | Food photography | 4:3 | $0.02 | `Overhead flat lay of a spicy miso ramen bowl with soft boiled egg, nori and scallions, dark ceramic table, chopsticks resting on the rim, natural window light, editorial food photography` |
| <img src="assets/03-cinematic-street-rain.jpg" width="220" alt="Sample render via the APIMart image API"> | Cinematic still | 16:9 | $0.02 | `Rain soaked Kyoto alley at night, paper lantern reflections on wet stone, a lone figure with a transparent umbrella walking away, cinematic 35mm still, shallow depth of field, film grain` |
| <img src="assets/04-architectural-dusk.jpg" width="220" alt="Sample render via the APIMart image API"> | Architecture | 16:9 | $0.02 | `Minimalist concrete villa on a cliff at dusk, warm interior light, infinity pool reflecting a violet sky, architectural photography, 24mm perspective, ultra sharp` |
| <img src="assets/05-fashion-editorial-studio.jpg" width="220" alt="Sample render via the APIMart image API"> | Fashion portrait | 3:4 | $0.02 | `Editorial fashion portrait of a model in an oversized ivory wool coat, seamless light grey studio backdrop, crisp high key lighting, medium format detail, calm expression` |
| <img src="assets/06-illustration-layered-paper.jpg" width="220" alt="Sample render via the APIMart image API"> | Illustration | 1:1 | $0.02 | `Layered paper cut illustration of a mountain lake at sunrise, five depth layers, soft pastel palette, subtle drop shadows, art print composition, clean vector edges` |
| <img src="assets/07-infographic-dashboard.jpg" width="220" alt="Sample render via the APIMart image API"> | Design / UI | 16:9 | $0.02 | `Flat vector dashboard panel showing three gauge dials and abstract latency curves, muted blue and sand palette, generous white space, crisp geometric cards, no placeholder text` |
| <img src="assets/08-macro-botanical-detail.jpg" width="220" alt="Sample render via the APIMart image API"> | Macro nature | 3:2 | $0.02 | `Macro photograph of a dew covered dandelion seed head against deep black background, focus stacked detail, iridescent droplets, studio lighting` |

Sources and costs are also in [`data/samples.json`](data/samples.json).

## FAQ

**What is the Nano Banana 2.1 model id?**

`gemini-nano-banana-2.1`. See the route table above for the exact values this model accepts.

**How is a Nano Banana 2.1 image billed?**

The official route is token-billed: `text_input` $1.20, `image_input` $1.20, `text_output` $6.00, `image_output` $24.00 per million tokens.

**How long do result links last?**

72 hours after completion — read `expires_at` and copy the files out.

**What happens if a task fails?**

A task marked `failed` is refunded and reports `cost = 0`. A network error while polling status does not by itself mean the task failed — re-query the task id.

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Page du modèle | <https://go.apimart.ai/k-d0d295> | `apimart.ai/fr/model/nano-banana-2-1` |
| Tarifs à jour | <https://go.apimart.ai/k-e4755a> | `apimart.ai/fr/pricing` |
| Obtenir une clé API | <https://go.apimart.ai/k-ac44a5> | `apimart.ai/fr/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Gemini Nano Banana 2.1 is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners. The renders in this README were produced through the APIMart image API and are shown as examples
of the API's output.

## Repository map

```text
README.md             model id, routes, pricing, parameters, quickstart, samples, FAQ
data/model.json       the pricing record for this model (CI-refreshed)
data/samples.json     prompt recipes used for the sample renders
tools/check_links.py  attribution guard
examples/             curl, Python and JavaScript clients
assets/               sample renders (JPEG, resized for the README)
```

## License

MIT — see [LICENSE](LICENSE).
