ï»¿<p align="center">
  <img src="assets/logo2.png" alt="SkyReels Logo" width="40%">
</p>

<h1 align="center">SkyReels V3 ê¢â¬â INEMA Web UI</h1>

<p align="center">
  Fork do <a href="https://github.com/SkyworkAI/SkyReels-V3">SkyworkAI/SkyReels-V3</a> com Web UI completa para produêÂ§êÂ£o de episêÂ³dios em batch.
</p>

---

## O que êÂ© este projeto

Este repositêÂ³rio êÂ© um fork do modelo de geraêÂ§êÂ£o de vêÂ­deo **SkyReels V3**, com uma interface Web (Web UI) construêÂ­da sobre ele para produêÂ§êÂ£o de vêÂ­deos em escala ê¢â¬â especialmente sêÂ©ries animadas com mêÂºltiplos personagens e cenas.

> **Em uso:** este projeto êÂ© consumido hoje pela plataforma
> [`imkt4`](https://github.com/inematds/imkt4) (orquestrador multi-tenant de
> pipelines de mêÂ­dia) como worker macro de geraêÂ§êÂ£o de vêÂ­deo por roteiro.
> A partir da v3.49, a Web UI expêÂµe `/health`, webhook callback em
> `/nqueues/<id>/run?callback_url=...` e `resume-from-error` justamente
> para esse caso de uso ê¢â¬â ver [CHANGELOG 3.49.19](doc/CHANGELOG.md).
> A interface continua 100% utilizêÂ¡vel standalone.

### Funcionalidades adicionadas neste fork

- **Web UI completa** ê¢â¬â geraêÂ§êÂ£o individual ou em fila, sem linha de comando
- **Named Queues** ê¢â¬â filas nomeadas com dezenas de cenas, controle por cena, retomada apêÂ³s erro
- **Suporte a `talking_avatar` na UI** ê¢â¬â imagem de personagem + êÂ¡udio MP3 ê¢â â vêÂ­deo falante
- **ImportaêÂ§êÂ£o de episêÂ³dio via JSON** ê¢â¬â uma cena por objeto, suporte a tipos mistos
- **EdiêÂ§êÂ£o de cenas em runtime** ê¢â¬â altere prompt, seed, resoluêÂ§êÂ£o sem reiniciar a fila
- **FinalizaêÂ§êÂ£o automêÂ¡tica** ê¢â¬â concatenaêÂ§êÂ£o das cenas concluêÂ­das em um vêÂ­deo final via ffmpeg
- **Galeria de vêÂ­deos** ê¢â¬â visualizaêÂ§êÂ£o de todos os vêÂ­deos gerados com player e privacidade por card

---

## Modelos suportados (SkyReels V3)

| Task | Modelo | HuggingFace |
|---|---|---|
| `reference_to_video` | 14B | [SkyReels-V3-R2V-14B](https://huggingface.co/Skywork/SkyReels-V3-R2V-14B) |
| `single_shot_extension` | 14B | [SkyReels-V3-V2V-14B](https://huggingface.co/Skywork/SkyReels-V3-V2V-14B) |
| `shot_switching_extension` | 14B | [SkyReels-V3-V2V-14B](https://huggingface.co/Skywork/SkyReels-V3-V2V-14B) |
| `talking_avatar` | 19B | [SkyReels-V3-A2V-19B](https://huggingface.co/Skywork/SkyReels-V3-A2V-19B) |

---

## InstalaêÂ§êÂ£o

```bash
git clone https://github.com/inematds/skyreelsv3
cd skyreelsv3

python3.12 -m venv .venv
source .venv/bin/activate

pip install -r requirements.txt

# Opcional mas recomendado
pip install flash-attn --no-build-isolation  # atenêÂ§êÂ£o flash (hardware compatêÂ­vel)
pip install xfuser                           # multi-GPU
pip install torchao                          # FP8 / Low VRAM mode
```

Os modelos sêÂ£o baixados automaticamente do HuggingFace na primeira execuêÂ§êÂ£o.

---

## Rodando a Web UI

```bash
source .venv/bin/activate
python webui/app.py
# ê¢â â http://localhost:7860
```

Acesse de qualquer dispositivo na rede local via `http://<ip-do-servidor>:7860`.

---

## Web UI ê¢â¬â Painel de GeraêÂ§êÂ£o

### Campos do formulêÂ¡rio

| Campo | DescriêÂ§êÂ£o |
|---|---|
| **Task Type** | `reference_to_video` êÂ· `single_shot_extension` êÂ· `shot_switching_extension` êÂ· `talking_avatar` |
| **Prompt** | DescriêÂ§êÂ£o textual do vêÂ­deo desejado |
| **Reference Images** | Caminhos separados por vêÂ­rgula ê¢â¬â 1 a 4 imagens (`reference_to_video`) |
| **Input Video** | Caminho local ou URL para tasks de extensêÂ£o |
| **Portrait Image** | Imagem de retrato para `talking_avatar` (jpg / png / gif / bmp) |
| **Audio File** | êÂudio de conduêÂ§êÂ£o para `talking_avatar` (mp3 / wav êÂ· mêÂ¡x 200 s) |
| **Resolution** | `480P` / `540P` / `720P` ê¢â¬â `talking_avatar` sêÂ³ aceita `480P` ou `720P` |
| **Duration** | Segundos de saêÂ­da ê¢â¬â ignorado em `talking_avatar` (definido pelo êÂ¡udio) |
| **Seed** | Semente para reprodutibilidade |
| **Offload CPU** | Move modelos para CPU entre passes ê¢â¬â reduz VRAM |
| **Low VRAM** | QuantizaêÂ§êÂ£o FP8 + block offload ê¢â¬â necessêÂ¡rio para `talking_avatar` em GPUs < 24 GB |

### BotêÂµes de aêÂ§êÂ£o

- **Gerar VêÂ­deo** ê¢â¬â inicia geraêÂ§êÂ£o imediata
- **+ Fila** ê¢â¬â adiciona êÂ  fila simples (tab Fila)

---

## Web UI ê¢â¬â Named Queues (ProduêÂ§êÂ£o em Batch)

A aba **Fila** suporta filas nomeadas com mêÂºltiplas cenas. Ideal para produêÂ§êÂ£o de episêÂ³dios com dezenas de takes.

### AêÂ§êÂµes por fila

| BotêÂ£o | AêÂ§êÂ£o |
|---|---|
| **ê¢â â Importar** | Carrega um arquivo `.json` como nova fila nomeada |
| **ê¢âÂ¶ Continuar** | Executa todas as cenas `idle` em sequêÂªncia, pula erros |
| **ê¢â Â© Repetir do erro** | Reseta cenas com erro e retoma daquele ponto |
| **ê¢â Âº Reiniciar do zero** | Reseta todas (incluindo `done`) e comeêÂ§a do inêÂ­cio |
| **ê°Å¸Å½Â¬ Finalizar** | Concatena todos os vêÂ­deos `done` em um `.mp4` final via ffmpeg |
| **ê°Å¸ââ** | Exclui a fila |

Cada card de cena exibe status, tipo, resoluêÂ§êÂ£o e seed. Ao abrir a fila as cenas aparecem expandidas. Controles ê¢Å Å¾ (expandir todas) / ê¢Å Å¸ (colapsar todas) disponêÂ­veis.

### EdiêÂ§êÂ£o de cena em runtime

Clique em ê¢ÅÂê¯Â¸Â em qualquer cena com status `idle` ou `error` para editar prompt, seed, resoluêÂ§êÂ£o, arquivos de referêÂªncia e outros campos ê¢â¬â sem precisar reiniciar o servidor.

### API REST (PATCH)

```bash
# Alterar parêÂ¢metros de uma cena especêÂ­fica
curl -X PATCH http://localhost:7860/nqueues/<fila_id>/jobs/<job_id> \
  -H "Content-Type: application/json" \
  -d '{"resolution": "720P", "seed": 9999, "prompt": "novo prompt"}'
```

Campos protegidos (nêÂ£o alterêÂ¡veis): `id`, `nq_id`, `status`, `task_type`, `created_at`, `output_video`.

---

## Formato JSON de EpisêÂ³dio

Importe um arquivo `.json` com um array de cenas para criar uma fila nomeada. Tipos de task podem ser misturados no mesmo arquivo.

```json
[
  {
    "label": "C01-A êÂ· Corredor ê¢â¬â Estabelecimento",
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
    "label": "C01-B êÂ· Personagem A ê¢â¬â 'Foi mal!'",
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

| Campo | `reference_to_video` | `talking_avatar` | extensêÂ£o |
|---|---|---|---|
| `label` | recomendado | recomendado | recomendado |
| `task_type` | obrigatêÂ³rio | obrigatêÂ³rio | obrigatêÂ³rio |
| `prompt` | obrigatêÂ³rio | obrigatêÂ³rio | obrigatêÂ³rio |
| `resolution` | `480P`/`540P`/`720P` | **`480P` ou `720P`** | `480P`/`540P`/`720P` |
| `duration` | obrigatêÂ³rio (segundos) | ê¢â¬â (definido pelo êÂ¡udio) | obrigatêÂ³rio |
| `seed` | opcional | opcional | opcional |
| `offload` | opcional | opcional | opcional |
| `low_vram` | opcional | **obrigatêÂ³rio** em < 24 GB | opcional |
| `ref_imgs` | obrigatêÂ³rio (1ê¢â¬â4 caminhos) | ê¢â¬â | ê¢â¬â |
| `input_image` | ê¢â¬â | obrigatêÂ³rio | ê¢â¬â |
| `input_audio` | ê¢â¬â | obrigatêÂ³rio | ê¢â¬â |
| `input_video` | ê¢â¬â | ê¢â¬â | obrigatêÂ³rio |

> ê¢Å¡Â ê¯Â¸Â **AtenêÂ§êÂ£o:** caminhos em `ref_imgs` nêÂ£o podem conter vêÂ­rgulas ê¢â¬â a CLI usa vêÂ­rgula como separador.

---

## OrganizaêÂ§êÂ£o de Assets

```
uploads/
ê¢ââê¢ââ¬ê¢ââ¬ <projeto>/
    ê¢ââê¢ââ¬ê¢ââ¬ <episodio>/
        ê¢âÅê¢ââ¬ê¢ââ¬ cena01.png          # cenêÂ¡rios / planos de estabelecimento
        ê¢âÅê¢ââ¬ê¢ââ¬ cena02.png
        ê¢âÅê¢ââ¬ê¢ââ¬ personagem_a.png    # retratos de personagens (reutilizados entre cenas)
        ê¢âÅê¢ââ¬ê¢ââ¬ personagem_b.png
        ê¢ââê¢ââ¬ê¢ââ¬ c01_a_linha1.mp3    # êÂ¡udios (um por fala)

doc/
ê¢ââê¢ââ¬ê¢ââ¬ ep01_primeiro_dia.json      # exemplo: 59 cenas (21 r2v + 38 talking_avatar)
```

---

## Modos de MemêÂ³ria

| Modo | Flag | VRAM aprox. | Quando usar |
|---|---|---|---|
| Completo | _(nenhuma)_ | ~49 GB (14B) / ~38 GB (19B) | Multi-GPU de alto nêÂ­vel |
| Offload | `--offload` | ~20 GB (14B) | GPU êÂºnica ê¢â°Â¥ 20 GB |
| Low VRAM | `--low_vram` | ~12 GB (14B) / ~19 GB (19B) | GPU êÂºnica < 24 GB |

Para Low VRAM, defina tambêÂ©m:

```bash
export PYTORCH_CUDA_ALLOC_CONF="expandable_segments:True"
```

### Notas sobre `talking_avatar` em Low VRAM

- Usar sempre `--low_vram` (quantizaêÂ§êÂ£o FP8 + block offload)
- Somente `480P` e `720P` sêÂ£o suportados (nêÂ£o `540P`)
- Tempo estimado: ~50 s/step êâ 4 steps êâ N chunks (~3 chunks para 5 s de vêÂ­deo)

---

## CLI ê¢â¬â ReferêÂªncia rêÂ¡pida

```bash
python3 generate_video.py \
  --task_type reference_to_video \
  --ref_imgs "img1.png,img2.png" \
  --prompt "descriêÂ§êÂ£o do vêÂ­deo" \
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

SaêÂ­da salva em `result/<task_type>/<seed>_<timestamp>.mp4`.
Para `talking_avatar`, tambêÂ©m êÂ© gerado `<seed>_<timestamp>_with_audio.mp4` com o êÂ¡udio mixado.

---

## CrêÂ©ditos

Baseado em [SkyReels V3](https://github.com/SkyworkAI/SkyReels-V3) da [Skywork AI](https://skywork.ai).
Agradecimentos aos projetos: [Wan 2.1](https://github.com/Wan-Video/Wan2.1) êÂ· [MultiTalk](https://github.com/MeiGen-AI/MultiTalk) êÂ· [xDiT](https://github.com/xdit-project/xDiT) êÂ· [diffusers](https://github.com/huggingface/diffusers).

---

Projeto  - ARCKHÉe