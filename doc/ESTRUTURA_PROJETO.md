# Estrutura de Pastas — Projetos SkyReels V3

Este documento define o padrêo oficial de organizaçêo de arquivos dentro de cada projeto criado na WebUI.

---

## Estrutura raiz

```
projetos/<nome-do-projeto>/
├── imagens/
├── audios/
├── docs/
├── temp/
└── episodios/
    └── <nome-do-episodio>/
        ├── imagens/
        └── audios/
```

---

## Descriçêo de cada pasta

### `imagens/`
Imagens de referência **permanentes** do projeto.

- Fotos e ilustrações dos personagens
- Cenários e ambientes recorrentes
- Paletas de cores, style guides
- Qualquer imagem que serve de base para todos os episódios

> Usadas pela IA como referência visual para manter consistência entre episódios.

---

### `audios/`
Áudios de referência **permanentes** do projeto.

- Amostras de voz dos personagens (para clonagem de voz)
- Efeitos sonoros recorrentes
- Trilha base da série

---

### `docs/`
Documentos base do universo da série.

- Bíblia da série (personagens, mundo, regras do universo)
- Arco geral da temporada
- Lista de episódios
- Qualquer documento que a IA deve ler como contexto fixo

> Todos os docs sêo sempre incluídos no prompt da IA ao gerar episódios.

---

### `temp/`
Arquivos **temporários** e de trabalho em andamento.

- Roteiros em rascunho
- Descrições de episódios antes de serem aprovadas
- Notas de produçêo avulsas
- Qualquer arquivo que o usuário quer carregar na modal de geraçêo sem commitar como doc oficial

> O dropdown "Carregar doc" na modal de geraçêo de episódio lista os arquivos desta pasta.

---

### `episodios/<nome-do-episodio>/`
Assets **gerados automaticamente** para cada episódio específico.

Criada automaticamente ao clicar em **🖼 Imagens** ou **🎵 Áudios** dentro do episódio.

O nome da subpasta é derivado do nome da fila/episódio (slugificado).

#### `episodios/<nome>/imagens/`
- Imagens geradas via `fal-ai/nano-banana/edit` (com refs de personagem) ou `fal-ai/nano-banana` (só texto)
- Uma imagem por cena, nomeada pelo título da cena
- Após geraçêo, o path é salvo como primeiro `ref_imgs` da cena (para uso no vídeo)

#### `episodios/<nome>/audios/`
- Áudios gerados via ElevenLabs TTS
- Um arquivo `.mp3` por cena, nomeado pelo título da cena
- Após geraçêo, o path é salvo como `input_audio` da cena

---

## Fluxo de geraçêo de um episódio

```
1. Criar projeto
   └── projetos/<nome>/ com imagens/, audios/, docs/, temp/, episodios/

2. Popular imagens/ e docs/ com as referências base do projeto

3. Abrir modal ⚡ IA → escrever descriçêo do episódio
   └── Descriçêo salva automaticamente em docs/<titulo>.md

4. IA gera cenas JSON com:
   - prompt       → descriçêo cinemática em inglês (para vídeo)
   - image_prompt → prompt para fal.ai (imagem estática)
   - audio_text   → narraçêo/diálogos em PT-BR (para ElevenLabs)
   - ref_imgs     → paths das imagens de personagens em imagens/

5. Aprovar → Criar Fila (episódio vinculado ao projeto)

6. 🖼 Imagens → gera via nano-banana → salva em episodios/<ep>/imagens/
7. 🎵 Áudios  → gera via ElevenLabs  → salva em episodios/<ep>/audios/
8. 🎬 Vídeos  → roda fila SkyReels   → salva em result/<task>/<seed>_<ts>.mp4
```

---

## Modelos de IA utilizados

| Etapa | Modelo | Endpoint |
|-------|--------|----------|
| Geraçêo de prompts | Claude (Anthropic) | CLI subprocess |
| Imagem com referência | Gemini 2.5 Flash | `fal-ai/nano-banana/edit` |
| Imagem sem referência | Gemini 2.5 Flash | `fal-ai/nano-banana` |
| Áudio / TTS | ElevenLabs | `eleven_multilingual_v2` |
| Vídeo | SkyReels V3 (local) | `reference_to_video` / `single_shot_extension` / etc. |

---

## Exemplo real — Projeto INETUSX

```
projetos/INETUSX/
├── imagens/
│   ├── valen.png
│   ├── lumi.png
│   ├── maya.png
│   ├── caio.png
│   └── escola.png
├── audios/
├── docs/
│   ├── INETUSX_BASE_COMPLETA.md   ← bíblia da série
│   └── descricao_episodio.md
├── temp/
│   └── roteiro_ep02_rascunho.md
└── episodios/
    └── Ep01_Primeiro_Dia/
        ├── imagens/
        │   ├── Cena_01_Primeiro_Olhar.png
        │   ├── Cena_02_Mesa_Compartilhada.png
        │   └── ...
        └── audios/
            ├── Cena_01_Primeiro_Olhar.mp3
            ├── Cena_02_Mesa_Compartilhada.mp3
            └── ...
```
