# Taquify

> Segundo cérebro a partir da gravação contínua do seu dia.

**Missão atual:** gravar o dia com o Echo (celular) → transferir pro PC → transcrever com Silero VAD + faster-whisper → entregar um `.md` por dia.

```
celular (Echo) ──USB──▶ PC ──▶ Silero VAD ──▶ faster-whisper ──▶ transcricoes/2026-10-14.md
WAV ~345 MB/h        (transitório,         descarta silêncio    transcreve pt-BR      ~60–100 KB
                      apagado após          antes do Whisper    e o áudio some
                      transcrever)
```

Tudo **local e gratuito** — sem nuvem, sem API key.

## Status

| Fase | Entrega | Status |
|:---:|---|---|
| 0 | Documentação | ✅ em andamento |
| 1 | Spike Echo (medir MB/h, bateria, mecânica) | ⬜ |
| 2 | Pipeline PC: pasta de áudio → `.md` | ⬜ |
| 3 | Geração de ensaio | 🗣️ a discutir |
| 4 | Notas conectadas / segundo cérebro | 🗣️ a discutir |

## Documentação

- [docs/00-visao-geral.md](docs/00-visao-geral.md) — problema, escopo, decisões e pendências
- [docs/01-arquitetura.md](docs/01-arquitetura.md) — fluxo, componentes e contrato do `.md`
- [docs/02-captura-audio.md](docs/02-captura-audio.md) — Echo: como funciona, espaço, bateria, rotina
- [docs/03-transcricao.md](docs/03-transcricao.md) — VAD + faster-whisper, modelos, integração Gradle
- [docs/04-roadmap.md](docs/04-roadmap.md) — fases e critérios de aceite
- [docs/adr/](docs/adr/) — decisões arquiteturais (ADR-0001 a 0003)

## Privacidade

Áudio (`*.wav`) e transcrições (`transcricoes/`) **nunca são versionados** (ver `.gitignore`). Nada sai da sua máquina.

## Como rodar (previsto na Fase 2)

```bash
./gradlew transcrever   # pasta do dia → transcricoes/<data>.md
```

O projeto é Gradle-first (mesmo ecossistema dos seus apps). A integração exata entre Gradle e o motor de transcrição está em decisão — ver [ADR-0002](docs/adr/0002-transcricao-local.md).