<p align="center">
  <img src="assets/logo2.png" alt="SkyReels Logo" width="40%">
</p>

<h1 align="center">SkyReels V3 â€” INEMA Web UI</h1>

<p align="center">
  Fork do <a href="https://github.com/SkyworkAI/SkyReels-V3">SkyworkAI/SkyReels-V3</a> com Web UI completa para produÃ§Ã£o de episÃ³dios em batch.
</p>

---

## O que Ã© este projeto

Este repositÃ³rio Ã© um fork do modelo de geraÃ§Ã£o de vÃ­deo **SkyReels V3**, com uma interface Web (Web UI) construÃ­da sobre ele para produÃ§Ã£o de vÃ­deos em escala â€” especialmente sÃ©ries animadas com mÃºltiplos personagens e cenas.

> **Em uso:** este projeto Ã© consumido hoje pela plataforma
> [`imkt4`](https://github.com/inematds/imkt4) (orquestrador multi-tenant de
> pipelines de mÃ­dia) como worker macro de geraÃ§Ã£o de vÃ­deo por roteiro.
> A partir da v3.49, a Web UI expÃµe `/health`, webhook callback em
> `/nqueues/<id>/run?callback_url=...` e `resume-from-error` justamente
> para esse caso de uso â€” ver [CHANGELOG 3.49.19](doc/CHANGELOG.md).
> A interface continua 100% utilizÃ¡vel standalone.

### Funcionalidades adicionadas neste fork

- **Web UI completa** â€” geraÃ§Ã£o individual ou em fila, sem linha de comando
- **Named Queues** â€” filas nomeadas com dezenas de cenas, controle por cena, retomada apÃ³s erro
- **Suporte a `talking_avatar` na UI** â€” imagem de personagem + Ã¡udio MP3 â†’ vÃ­deo falante
- **ImportaÃ§Ã£o de episÃ³dio via JSON** â€” uma cena por objeto, suporte a tipos mistos
- **EdiÃ§Ã£o de cenas em runtime** â€” altere prompt, seed, resoluÃ§Ã£o sem reiniciar a fila
- **FinalizaÃ§Ã£o automÃ¡tica** â€” concatenaÃ§Ã£o das cenas concluÃ­das em um vÃ­deo final via ffmpeg
- **Galeria de vÃ­deos** â€” visualizaÃ§Ã£o de todos os vÃ­deos gerados com player e privacidade por card

---

## Modelos suportados (SkyReels V3)

| Task | Modelo | HuggingFace |
|---|---|---|
| `reference_to_video` | 14B | [SkyReels-V3-R2V-14B](https://huggingface.co/Skywork/SkyReels-V3-R2V-14B) |
| `single_shot_extension` | 14B | [SkyReels-V3-V2V-14B](https://huggingface.co/Skywork/SkyReels-V3-V2V-14B) |
| `shot_switching_extension` | 14B | [SkyReels-V3-V2V-14B](https://huggingface.co/Skywork/SkyReels-V3-V2V-14B) |
| `talking_avatar` | 19B | [SkyReels-V3-A2V-19B](https://huggingface.co/Skywork/SkyReels-V3-A2V-19B) |

---

## InstalaÃ§Ã£o

```bash
git clone https://github.com/inematds/skyreelsv3
cd skyreelsv3

python3.12 -m venv .venv
source .venv/bin/activate

pip install -r requirements.txt

# Opcional mas recomendado
pip install flash-attn --no-build-isolation  # atenÃ§Ã£o flash (hardware compatÃ­vel)
pip install xfuser                           # multi-GPU
pip install torchao                          # FP8 / Low VRAM mode
```

Os modelos sÃ£o baixados automaticamente do HuggingFace na primeira execuÃ§Ã£o.

---

## Rodando a Web UI

```bash
source .venv/bin/activate
python webui/app.py
# â†’ http://localhost:7860
```

Acesse de qualquer dispositivo na rede local via `http://<ip-do-servidor>:7860`.

---

## Web UI â€” Painel de GeraÃ§Ã£o

### Campos do formulÃ¡rio

| Campo | DescriÃ§Ã£o |
|---|---|
| **Task Type** | `reference_to_video` Â· `single_shot_extension` Â· `shot_switching_extension` Â· `talking_avatar` |
| **Prompt** | DescriÃ§Ã£o textual do vÃ­deo desejado |
| **Reference Images** | Caminhos separados por vÃ­rgula â€” 1 a 4 imagens (`reference_to_video`) |
| **Input Video** | Caminho local ou URL para tasks de extensÃ£o |
| **Portrait Image** | Imagem de retrato para `talking_avatar` (jpg / png / gif / bmp) |
| **Audio File** | Ãudio de conduÃ§Ã£o para `talking_avatar` (mp3 / wav Â· mÃ¡x 200 s) |
| **Resolution** | `480P` / `540P` / `720P` â€” `talking_avatar` sÃ³ aceita `480P` ou `720P` |
| **Duration** | Segundos de saÃ­da â€” ignorado em `talking_avatar` (definido pelo Ã¡udio) |
| **Seed** | Semente para reprodutibilidade |
| **Offload CPU** | Move modelos para CPU entre passes â€” reduz VRAM |
| **Low VRAM** | QuantizaÃ§Ã£o FP8 + block offload â€” necessÃ¡rio para `talking_avatar` em GPUs < 24 GB |

### BotÃµes de aÃ§Ã£o

- **Gerar VÃ­deo** â€” inicia geraÃ§Ã£o imediata
- **+ Fila** â€” adiciona Ã  fila simples (tab Fila)

---

## Web UI â€” Named Queues (ProduÃ§Ã£o em Batch)

A aba **Fila** suporta filas nomeadas com mÃºltiplas cenas. Ideal para produÃ§Ã£o de episÃ³dios com dezenas de takes.

### AÃ§Ãµes por fila

| BotÃ£o | AÃ§Ã£o |
|---|---|
| **â†‘ Importar** | Carrega um arquivo `.json` como nova fila nomeada |
| **â–¶ Continuar** | Executa todas as cenas `idle` em sequÃªncia, pula erros |
| **â†© Repetir do erro** | Reseta cenas com erro e retoma daquele ponto |
| **â†º Reiniciar do zero** | Reseta todas (incluindo `done`) e comeÃ§a do inÃ­cio |
| **ðŸŽ¬ Finalizar** | Concatena todos os vÃ­deos `done` em um `.mp4` final via ffmpeg |
| **ðŸ—‘** | Exclui a fila |

Cada card de cena exibe status, tipo, resoluÃ§Ã£o e seed. Ao abrir a fila as cenas aparecem expandidas. Controles âŠž (expandir todas) / âŠŸ (colapsar todas) disponÃ­veis.

### EdiÃ§Ã£o de cena em runtime

Clique em âœï¸ em qualquer cena com status `idle` ou `error` para editar prompt, seed, resoluÃ§Ã£o, arquivos de referÃªncia e outros campos â€” sem precisar reiniciar o servidor.

### API REST (PATCH)

```bash
# Alterar parÃ¢metros de uma cena especÃ­fica
curl -X PATCH http://localhost:7860/nqueues/<fila_id>/jobs/<job_id> \
  -H "Content-Type: application/json" \
  -d '{"resolution": "720P", "seed": 9999, "prompt": "novo prompt"}'
```

Campos protegidos (nÃ£o alterÃ¡veis): `id`, `nq_id`, `status`, `task_type`, `created_at`, `output_video`.

---

## Formato JSON de EpisÃ³dio

Importe um arquivo `.json` com um array de cenas para criar uma fila nomeada. Tipos de task podem ser misturados no mesmo arquivo.

```json
[
  {
    "label": "C01-A Â· Corredor â€” Estabelecimento",
    "task_type": "reference_to_video",
    "prompt": "A modern school hallway on the first day of school...",
    "resolution": "540P",
    "duration": 4,
    "seed": 1001,
    "offload": true,
    "low_vram": false,
    "ref_imgs": [
      "uploads/projeto/cena01.png",
      "uploads/projeto/personagem_a.png",
      "uploads/projeto/personagem_b.png"
    ]
  },
  {
    "label": "C01-B Â· Personagem A â€” 'Foi mal!'",
    "task_type": "talking_avatar",
    "prompt": "A teenage girl, composed but slightly embarrassed, speaking a brief apology.",
    "resolution": "480P",
    "seed": 1002,
    "low_vram": true,
    "input_image": "uploads/projeto/personagem_a.png",
    "input_audio": "uploads/projeto/audio/c01_a_linha1.mp3"
  }
]
```

### Campos por tipo de task

| Campo | `reference_to_video` | `talking_avatar` | extensÃ£o |
|---|---|---|---|
| `label` | recomendado | recomendado | recomendado |
| `task_type` | obrigatÃ³rio | obrigatÃ³rio | obrigatÃ³rio |
| `prompt` | obrigatÃ³rio | obrigatÃ³rio | obrigatÃ³rio |
| `resolution` | `480P`/`540P`/`720P` | **`480P` ou `720P`** | `480P`/`540P`/`720P` |
| `duration` | obrigatÃ³rio (segundos) | â€” (definido pelo Ã¡udio) | obrigatÃ³rio |
| `seed` | opcional | opcional | opcional |
| `offload` | opcional | opcional | opcional |
| `low_vram` | opcional | **obrigatÃ³rio** em < 24 GB | opcional |
| `ref_imgs` | obrigatÃ³rio (1â€“4 caminhos) | â€” | â€” |
| `input_image` | â€” | obrigatÃ³rio | â€” |
| `input_audio` | â€” | obrigatÃ³rio | â€” |
| `input_video` | â€” | â€” | obrigatÃ³rio |

> âš ï¸ **AtenÃ§Ã£o:** caminhos em `ref_imgs` nÃ£o podem conter vÃ­rgulas â€” a CLI usa vÃ­rgula como separador.

---

## OrganizaÃ§Ã£o de Assets

```
uploads/
â””â”€â”€ <projeto>/
    â””â”€â”€ <episodio>/
        â”œâ”€â”€ cena01.png          # cenÃ¡rios / planos de estabelecimento
        â”œâ”€â”€ cena02.png
        â”œâ”€â”€ personagem_a.png    # retratos de personagens (reutilizados entre cenas)
        â”œâ”€â”€ personagem_b.png
        â””â”€â”€ c01_a_linha1.mp3    # Ã¡udios (um por fala)

doc/
â””â”€â”€ ep01_primeiro_dia.json      # exemplo: 59 cenas (21 r2v + 38 talking_avatar)
```

---

## Modos de MemÃ³ria

| Modo | Flag | VRAM aprox. | Quando usar |
|---|---|---|---|
| Completo | _(nenhuma)_ | ~49 GB (14B) / ~38 GB (19B) | Multi-GPU de alto nÃ­vel |
| Offload | `--offload` | ~20 GB (14B) | GPU Ãºnica â‰¥ 20 GB |
| Low VRAM | `--low_vram` | ~12 GB (14B) / ~19 GB (19B) | GPU Ãºnica < 24 GB |

Para Low VRAM, defina tambÃ©m:

```bash
export PYTORCH_CUDA_ALLOC_CONF="expandable_segments:True"
```

### Notas sobre `talking_avatar` em Low VRAM

- Usar sempre `--low_vram` (quantizaÃ§Ã£o FP8 + block offload)
- Somente `480P` e `720P` sÃ£o suportados (nÃ£o `540P`)
- Tempo estimado: ~50 s/step Ã— 4 steps Ã— N chunks (~3 chunks para 5 s de vÃ­deo)

---

## CLI â€” ReferÃªncia rÃ¡pida

```bash
python3 generate_video.py \
  --task_type reference_to_video \
  --ref_imgs "img1.png,img2.png" \
  --prompt "descriÃ§Ã£o do vÃ­deo" \
  --resolution 540P \
  --duration 5 \
  --seed 42 \
  --offload
```

```bash
python3 generate_video.py \
  --task_type talking_avatar \
  --input_image personagem.png \
  --input_audio fala.mp3 \
  --resolution 480P \
  --seed 42 \
  --low_vram
```

SaÃ­da salva em `result/<task_type>/<seed>_<timestamp>.mp4`.
Para `talking_avatar`, tambÃ©m Ã© gerado `<seed>_<timestamp>_with_audio.mp4` com o Ã¡udio mixado.

---

## CrÃ©ditos

Baseado em [SkyReels V3](https://github.com/SkyworkAI/SkyReels-V3) da [Skywork AI](https://skywork.ai).
Agradecimentos aos projetos: [Wan 2.1](https://github.com/Wan-Video/Wan2.1) Â· [MultiTalk](https://github.com/MeiGen-AI/MultiTalk) Â· [xDiT](https://github.com/xdit-project/xDiT) Â· [diffusers](https://github.com/huggingface/diffusers).

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe