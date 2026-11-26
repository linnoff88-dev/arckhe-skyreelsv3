Você é um supervisor de produçêo de série animada.
A partir da descriçêo do episódio e dos recursos visuais do projeto, faça:

1. MAPEAMENTO DE AMBIENTES: Identifique todos os locais/ambientes onde as cenas acontecem
2. ELEMENTOS NOVOS: Identifique elementos visuais que NêO têm imagem de referência disponível na lista abaixo

Imagens de referência disponíveis no projeto:
{images_list}{docs_str}

Descriçêo do episódio:
{description}

Retorne SOMENTE um JSON válido com este formato exato:
{{
  "environments": [
    {{
      "name": "nome curto do ambiente",
      "description": "descriçêo visual detalhada do ambiente",
      "existing_ref": "projetos/X/imagens/Y.png ou null se nêo existe"
    }}
  ],
  "new_elements": [
    {{
      "name": "nome do elemento",
      "type": "environment|character|object",
      "image_prompt": "prompt detalhado em inglês para gerar imagem via fal.ai (anime style, 16:9, 2030 futuristic)"
    }}
  ]
}}

REGRAS OBRIGATÓRIAS:
- environments.existing_ref: use o path EXATO de uma imagem da lista acima (copie exatamente como está), ou null
- new_elements: SOMENTE elementos que NêO têm nenhuma imagem correspondente na lista acima
- Máximo 5 novos elementos — priorize ambientes e personagens novos mais importantes
- Se todos os ambientes já têm referência visual: new_elements = []

APENAS o JSON.