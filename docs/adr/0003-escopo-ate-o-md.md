# ADR-0003: A fase atual termina no `.md` (geração e anotações ficam para depois)

- **Status:** aceito
- **Data:** 2026-10-14

## Contexto

A visão de produto tem quatro estágios: captura → transcrição → geração de ensaio → notas conectadas (segundo cérebro, tipo Notion/Obsidian). O usuário pediu para **focar apenas em: Echo grava → PC → faster-whisper + VAD transcreve → `.md` no computador**, e discutir geração de texto + anotação depois. Ficou claro também que **nenhuma ferramenta de geração está escolhida** (Ollama não está comprometido — era apenas um candidato mencionado em conversa, para o estágio de geração, não de transcrição).

## Decisão

1. **Escopo da fase atual:** da gravação no celular até o `.md` por dia no PC.
2. **Geração de texto (ensaio):** ferramenta não decidida — discutir na Fase 3.
3. **Anotações / segundo cérebro:** ferramenta não decidida (Notion, Obsidian, outras) — discutir na Fase 4.
4. Nenhuma decisão de fornecedor/modelo para geração é tomada nesta fase.

## Consequências

- O formato do `.md` (ver [01-arquitetura.md](../01-arquitetura.md#contrato-do-md-de-saída)) preserva `frontmatter` e timestamps por trecho — exatamente o que a fase de geração vai precisar para referenciar momentos do dia.
- Risco: refazer o `.md` se a fase de geração exigir campos extras — mitigado pelo contrato ser versionado e explícito.
