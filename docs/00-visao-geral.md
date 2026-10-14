# 00 — Visão geral

## O problema

Ideias e frases interessantes acontecem ao longo do dia e somem. A proposta é capturar tudo o que é falado no celular e transformar em texto pesquisável e, no futuro, conectado a um segundo cérebro.

## Pipeline em dois tempos

1. **Captura + transcrição** *(escopo atual)* — áudio do dia vira um `.md` por dia no PC.
2. **Geração + anotações** *(futuro, a discutir)* — o `.md` vira ensaio formatado e notas conectadas (Notion, Obsidian ou afins).

## Escopo atual (Fases 0–2)

**Dentro:**
- Gravar o dia com o **Echo** (FOSS, F-Droid).
- Transferir o áudio manualmente por USB no fim do dia.
- Transcrever no PC, de graça e offline: **Silero VAD + faster-whisper**.
- Entregar um `.md` por dia com timestamps.
- Apagar o áudio após a transcrição (nada acumula).

**Fora (discutir depois):**
- Geração de texto (ensaio do dia) — ferramenta **não decidida**.
- Notas conectadas / segundo cérebro — ferramenta **não decidida**.

## Decisões já tomadas

| # | Decisão | Referência |
|---|---|---|
| 1 | Captura com **Echo** + USB manual + áudio transitório | [ADR-0001](adr/0001-captura-com-echo.md) |
| 2 | Transcrição **local e gratuita**: VAD + faster-whisper (sem nuvem) | [ADR-0002](adr/0002-transcricao-local.md) |
| 3 | Missão da fase atual termina no `.md` | [ADR-0003](adr/0003-escopo-ate-o-md.md) |

## Pendências

1. **Integração Gradle ↔ motor de transcrição** — Opção A (Kotlin + whisper.cpp) vs Opção B (Gradle orquestra Python/faster-whisper). Ver [ADR-0002](adr/0002-transcricao-local.md).
2. **Modelo Whisper** (small/medium/large) — medir na Fase 1/2 com áudio real.
3. **Ferramenta de geração de texto** — Fase 3.
4. **Ferramenta de anotações** — Fase 4.

## Restrições do usuário

- Constrói apps com **Gradle** — o pipeline deve se encaixar nesse ecossistema.
- Pouco armazenamento livre — áudio nunca acumula (transfere → transcreve → apaga).
- Soluções gratuitas e locais.
