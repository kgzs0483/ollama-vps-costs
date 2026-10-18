# Ollama Hosting: Which VPS Size Fits Llama, Qwen or Mistral — and What It Really Costs to Run

Search for Ollama hosting and two different products show up. Managed hosts install Ollama on infrastructure they control and hand you a working URL, starting at $16.99 a month. The other route is a plain VPS: you rent a server, run Ollama's one-line install script, and keep root access to the whole stack. Vultr sits in that second camp, and Cybernews's Ollama VPS guide (last updated September 2026) ranks it fourth out of eight providers and names it the best pick for scalable deployments.

Which route wins comes down to two things: how much RAM your model actually needs, and how much maintenance you want to own. Both are easier to pin down than most landing pages admit. Below: the sizing numbers straight from Ollama's own model pages, Vultr's current Cloud Compute plans mapped onto them, what the managed alternatives charge for the same job, and the networking default that catches out most first-time self-hosters.

> **Quick picks**
>
> - Quantized 7–8B models (Llama 3.1 8B, Qwen 7B, Mistral 7B): the 8 GB tiers — $40/mo on Regular Performance, $48/mo on High Performance. 👉 [Check Vultr's current Cloud Compute pricing](https://www.vultr.com/pricing/?ref=7268283)
> - Experiments and 1–3B models: $5–12/mo depending on model size.
> - Zero maintenance, with backups and patching handled for you: managed Ollama hosting from around $16.99/mo.
> - 70B-class models or realtime latency: GPU servers, currently $400–1,000/mo plus egress fees.

## What Ollama hosting actually involves

Ollama is a runtime for open-weight language models — Llama, Qwen, Gemma, DeepSeek, Mistral, Phi and hundreds more. One command downloads a model; after that you have an OpenAI-compatible API on the machine. Hosting that setup means moving it from your laptop to a server that stays online around the clock.

Three things change. The endpoint stops depending on your laptop's battery, Wi-Fi and free RAM, which matters once agents or automations need to reach a model at 3 a.m. Prompts and documents stay on hardware you control instead of going to a metered API. And because the server is a standard Linux box, Open WebUI, n8n or Dify can sit on the same instance and share one model endpoint across a team.

No special product is required for any of this. Any Linux VPS will do. The managed Ollama hosts on the market exist to skip the install and the ongoing maintenance — that's the entire value they add, and it's worth paying for only if you'd rather not do those things yourself.

## The spec that decides everything: RAM

Ollama's own model pages list minimum requirements — 8 GB of RAM for 7B models, 16 GB for 13B, 64 GB for 70B, current as of October 2026. Everything else is adjustable later: CPU cores, disk, region. Run out of RAM and the model either won't load or crawls through swap.

Quantization works in your favor. A Q4_K_M quantized build cuts memory needs roughly in half with minimal quality loss, which is why quantized 7–8B models are the sweet spot for CPU hosting: they fit in 8 GB and answer at a pace fine for chat, RAG and background automation.

Realistic speed expectations: a 7–8B Q4 model on dedicated AMD EPYC cores does about 5–15 tokens per second. Comfortable for chatbots and internal tools. Not realtime, and no amount of landing-page enthusiasm changes that — realtime means a GPU.

Disk is the second thing to size. Model files run several GB each, and you'll try more than you keep. They land under /usr/share/ollama/.ollama by default; if the root volume gets tight, the OLLAMA_MODELS environment variable moves storage elsewhere. As a rule of thumb, 50 GB of disk covers small-model setups, 100 GB if you'll keep several 13B-class files around, 200 GB+ for 70B territory.

## Vultr plans for Ollama, mapped to model sizes

Pick by RAM and the table writes itself: quantized 7–8B models want the 8 GB tiers ($40/mo Regular Performance or $48/mo High Performance), 13B models want 16 GB ($80–96/mo), and the entry tiers are for small models and deploy testing.

| Plan family | vCPU | RAM | Storage | Monthly price | Deploy |
| --- | --- | --- | --- | --- | --- |
| Regular Performance | 1 | 1 GB | 25 GB SSD | $5/mo | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Regular Performance | 2 | 4 GB | 80 GB SSD | $20/mo | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| Regular Performance | 4 | 8 GB | 160 GB SSD | $40/mo | [ Deploy 8 GB](https://www.vultr.com/pricing/?ref=7268283) |
| Regular Performance | 8 | 16 GB | 320 GB SSD | $80/mo | [ Deploy 16 GB](https://www.vultr.com/pricing/?ref=7268283) |
| High Performance | 1 | 1 GB | 25 GB NVMe | $6/mo | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| High Performance | 2 | 4 GB | 80 GB NVMe | $24/mo | [ Deploy](https://www.vultr.com/pricing/?ref=7268283) |
| High Performance | 4 | 8 GB | 160 GB NVMe | $48/mo | [ Deploy 8 GB](https://www.vultr.com/pricing/?ref=7268283) |
| High Performance | 8 | 16 GB | 320 GB NVMe | $96/mo | [ Deploy 16 GB](https://www.vultr.com/pricing/?ref=7268283) |

Two things worth knowing about this table. Every Vultr instance bills hourly, capped at the listed monthly rate — destroy the server and billing stops. That makes it unusually good for experiments: a weekend spent testing whether an 8B model handles your documents costs a few dollars, not a month of a flat plan.

Second, these are the shared-CPU lines. Vultr also sells High Frequency instances (same $6 entry point, faster clocks, bigger NVMe volumes at the small sizes), Optimized Cloud Compute with dedicated vCPUs for steadier throughput, bare metal for big-RAM builds, and hourly-rented GPUs. The 2 GB tiers sit between the rows above at $10–12/mo, and larger 32 GB tiers exist if you want headroom for several models at once. 👉 [See every Vultr Cloud Compute size on the pricing page](https://www.vultr.com/pricing/?ref=7268283)

## What Ollama hosting costs elsewhere

For a working 7–8B box you're choosing between roughly $40–48/mo on Vultr, $13–17/mo at the budget self-managed and managed hosts, and $400–1,000/mo for rented cloud GPUs. The comparison below uses prices the providers published in September 2026.

| Option | Price | What it is | Trade-off |
| --- | --- | --- | --- |
| Contabo Cloud VPS | $5.28–13/mo (8 GB) | Self-managed VPS; Cybernews recommends the $13 Cloud VPS Plus 4 (6 EPYC vCPU, 8 GB RAM, 150 GB NVMe) for Ollama | Best raw specs per dollar; manual install; flat monthly billing |
| Hostinger KVM 2 | from $8.09/mo | Self-managed VPS with a one-click Ollama installer | Easiest first deploy, less RAM per dollar than Contabo |
| GGameHost | from $16.99/mo | Managed Ollama on dedicated EPYC cores, live in under 5 minutes | Zero maintenance, but flat monthly pricing and CPU only |
| AIHostBox | $16.99–27.99/mo | Managed, single-tenant Ollama with nightly backups | Publishes patching SLAs — critical fixes within 24 hours |
| Elestio | from $30/mo | Managed Ollama | The priciest of the managed options |
| Rented cloud GPUs | $400–1,000/mo + egress | GPU inference | The realistic option for 70B models and realtime latency |

Where Vultr earns its keep is flexibility. You pay only while an instance exists, so staging endpoints and temporary test servers cost fractions of their flat-rate equivalents. Locations span North America, Europe, Asia and Australia, so the endpoint can sit near whoever calls it. And one account scales from a $6 instance to bare metal and GPUs — no migration to another provider when a prototype turns into production.

Where it doesn't win: raw price. If you want the cheapest possible permanent home for one 8B model and don't mind applying updates yourself, Contabo's 8 GB tiers cost a third of Vultr's. If you never want to open a terminal, the $16.99 managed plans remove the work entirely. Vultr makes sense for the middle: developers and small teams running automations, staging environments and multi-region setups who value paying only for what's actually running.

## Setting up Ollama on a Vultr server

Fifteen-odd minutes from empty account to working API.

1. **Deploy an instance.** Pick Ubuntu LTS, the region closest to your users, and a plan sized by the RAM table above.
2. **Install Ollama.** One command does it — the script sets up the service and detects any GPU automatically:

   
   curl -fsSL https://ollama.com/install.sh | sh
   

3. **Pull and test a model.** `ollama pull llama3.1` downloads the quantized 8B build; `ollama run llama3.1` opens a chat in your terminal. Applications talk to the OpenAI-compatible endpoint at /v1/chat/completions, so anything built for the ChatGPT API works by pointing the base URL at your server.
4. **Add a frontend.** Open WebUI (deployed with Docker Compose next to Ollama) gives a ChatGPT-style interface with accounts and document search. AnythingLLM and Dify handle RAG; n8n wires the endpoint into automations. Pull nomic-embed-text or bge-m3 if you need embeddings for semantic search.
5. **Secure the endpoint.** By default Ollama listens on 127.0.0.1:11434 and its local API needs no authentication. On a public server, keep it that way: leave the port firewalled and reach Ollama through an authenticated reverse proxy (Nginx or Caddy with TLS) or an SSH tunnel. Opening 11434 to the internet raw hands your model to whoever finds the IP, on your dime.
6. **Housekeeping.** Point OLLAMA_MODELS at your biggest volume if the default path fills up, and snapshot the server before swapping in large models.

When you're ready to try it, the whole flow starts at deployment: 👉 [spin up a Vultr instance](https://bit.ly/VulTR)

## Ollama hosting FAQ

**Does Ollama need a GPU?**
No. CPU inference works, and a quantized 7–8B model runs in 8 GB of RAM. Some hosts recommend 16 GB for the same models — that's headroom advice for boxes also running Open WebUI and embedding models, not a hard requirement. 13B models want 16 GB, 70B models want 64 GB, and that last size is where GPUs or bare metal start making sense. A GPU with 8+ GB of VRAM delivers roughly 10x CPU inference speed.

**How fast is CPU inference in practice?**
About 5–15 tokens per second for 7–8B Q4 models on dedicated EPYC cores. Shared vCPUs can land lower. Fine for chat, RAG and background automation; the wrong tool for latency-critical products.

**Is the Ollama API private on a VPS?**
It can be, and it's on you to make it so. The default bind is localhost with no authentication — safe by itself, but it also means the API is unreachable from your other servers until you put an authenticated, TLS-terminating proxy in front or tunnel over SSH. After that, prompts and outputs never leave your hardware.

**Can I run more than one model at a time?**
Yes, limited by memory. On GPU, 24 GB of VRAM fits two 7B models or one 13B. On CPU the same logic applies to system RAM — a 16 GB server running a 13B model doesn't have much left for a second one.

**Do Open WebUI, SillyTavern and n8n work with it?**
Yes. Any Ollama-compatible frontend connects to the endpoint, and the OpenAI-compatible API means apps built against the ChatGPT SDK need only a base-URL change.

**What happens to billing if I shut the server down?**
Vultr bills hourly with a monthly cap, so destroying an instance stops charges the same day. Take a snapshot first if you want the configuration back later — snapshots cost pennies and save you re-downloading multi-GB model files.

## Before you pick a plan

Match the tier to the model, not to your ambitions:

- Just testing the deploy flow or running 1–3B models: the $5–12 entry tiers.
- One quantized 7–8B model for chat and RAG: 8 GB — $40/mo Regular Performance or $48/mo High Performance.
- 13B quantized: 16 GB, $80–96/mo.
- 70B models or realtime latency: GPUs at market rates of $400–1,000/mo plus egress, or CPU-speed patience on bare metal.
- Cheapest permanent 8 GB box, happy to do your own updates: budget flat-rate VPS providers beat Vultr on RAM per dollar.
- Never touching a terminal: managed Ollama hosting from $16.99/mo does the whole job for you.

Two checks before checkout: pick the region nearest whoever or whatever calls the API — agent workloads make thousands of small requests, and latency adds up — and be honest about whether the model needs to run 24/7, because hourly billing rewards people who destroy idle servers.

👉 [Open Vultr's pricing page and match a tier to your model](https://www.vultr.com/pricing/?ref=7268283)
