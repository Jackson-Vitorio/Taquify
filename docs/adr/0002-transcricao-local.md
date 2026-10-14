# ADR-0002: Transcrição local com faster-whisper + Silero VAD (integração Gradle em decisão)

- **Status:** proposto (motor decidido; integração Gradle em decisão antes da Fase 2)
- **Data:** 2026-10-14

## Contexto

A transcrição precisa ser **gratuita e eficiente** (preferência explícita do usuário), rodar no PC e entregar um `.md` por dia. O áudio de um dia são ~8 GB de WAV.

## Decisão

1. **Motor:** [faster-whisper](https://github.com/SYSTRAN/faster-whisper) (CTranslate2) local — até 4× mais rápido que o Whisper original, quantização int8 para CPU, gratuito.
2. **VAD:** Silero VAD embutido (`vad_filter=True`) — só transcreve onde há fala.
3. **Idioma:** `language="pt"` (pt-BR).
4. **Sem nuvem:** nenhuma API paga no fluxo.

## Alternativas consideradas

| Alternativa | Custo | Por que (não) |
|---|---|---|
| Groq whisper-large-v3-turbo | ~US$ 0,04/h | mais barato da nuvem, mas exige rede/chave e sai de "local e grátis" |
| OpenAI Whisper API | ~US$ 0,36/h | idem, mais caro |
| whisper.cpp + binding Java (JNA) | grátis | 100% Kotlin/Gradle (Opção A abaixo); mais código de casa e em CPU perde para CTranslate2 |
| Vosk | grátis | qualidade pt-BR inferior |

Preços: [speechtotext.dev/pricing](https://speechtotext.dev/pricing/)

## Integração com Gradle — EM DECISÃO

O usuário constrói apps com Gradle; o ponto de entrada deve ser `./gradlew ...`.

- **Opção B (recomendada):** Gradle orquestra um script Python do faster-whisper (tasks `Exec`, venv do projeto). Prós: desempenho e maturidade do faster-whisper. Contras: segundo runtime no repo.
- **Opção A:** task/app Kotlin usando os [bindings Java oficiais do whisper.cpp](https://github.com/ggml-org/whisper.cpp/tree/master/bindings/java) (JNA, MIT). Prós: runtime único. Contras: bindings de baixo nível; VAD e decodificação de áudio por conta própria.

As duas opções produzem o mesmo `.md` — decisão pode ser adiada para o início da Fase 2 sem retrabalho.

## Consequências

- Grátis, offline, privado, sem rate limit.
- Modelo inicial: `small` int8 (candidato), ajustar após medir na Fase 1/2.
- Fallback possível: se qualidade/velocidade em CPU insatisfatória, avaliar Groq (~US$ 0,04/h) só como comparação — exigiria nova decisão.
