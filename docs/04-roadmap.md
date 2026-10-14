# 04 — Roadmap

| Fase | Objetivo | Critério de aceite | Status |
|---|---|---|---|
| **0** | Documentação | README + docs + ADRs descrevem o plano e as decisões | ✅ em andamento |
| **1** | Spike de captura | 1 dia real com Echo: MB/h, bateria e mecânica gravar/parar medidos e anotados | ⬜ |
| **2** | Pipeline → `.md` | Comando Gradle transforma a pasta do dia em `transcricoes/<data>.md` e remove o áudio | ⬜ |
| **3** | Geração de ensaio | A discutir — o `.md` vira ensaio formatado | 🗣️ |
| **4** | Segundo cérebro | A discutir — notas conectadas no app de anotações escolhido | 🗣️ |

## Ordem de execução

Fase 0 → Fase 1 (valida as premissas da captura) → Fase 2 (entrega a missão atual) → Fases 3 e 4 (discutir juntas ao chegar aqui).

## Fora de escopo por ora

- Gravação inteligente (VAD no celular) e transcrição on-device — [ADR-0001](adr/0001-captura-com-echo.md)
- Qualquer nuvem paga — [ADR-0002](adr/0002-transcricao-local.md)
- Definição de ferramenta de geração/anotações — [ADR-0003](adr/0003-escopo-ate-o-md.md)
