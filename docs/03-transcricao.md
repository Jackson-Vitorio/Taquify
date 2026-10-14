# 03 — Transcrição (VAD + faster-whisper)

## Decisão

**Transcrever localmente, de graça, com [faster-whisper](https://github.com/SYSTRAN/faster-whisper) + filtro VAD (Silero).**

- faster-whisper reimplementa o Whisper sobre o motor CTranslate2: até **4× mais rápido** que `openai/whisper` com a mesma acurácia, com **quantização int8** para CPU.
- `vad_filter=True` usa o **Silero VAD** embutido — descarta trechos sem fala **antes** de gastar compute (num dia inteiro, a maior parte é silêncio/ruído ⇒ economia direta).
- 100% offline; modelos (~150 MB a ~3 GB) baixados 1× do Hugging Face.

**Alternativas descartadas (por enquanto):**

| Alternativa | Por que não (agora) |
|---|---|
| Nuvem paga (Groq ~US$ 0,04/h, OpenAI ~US$ 0,36/h, AssemblyAI ~US$ 0,15/h) | sai do "grátis/local" — pode voltar como comparação ([preços](https://speechtotext.dev/pricing/)) |
| whisper.cpp "cru" | mesmo modelo, menos conveniente em CPU que CTranslate2 |
| Vosk | modelos leves, mas qualidade pt-BR inferior ao Whisper |
| Transcrição on-device | ver [ADR-0001](adr/0001-captura-com-echo.md) |

## Fluxo técnico (Fase 2)

```
audio/bruto/<data>/*.wav
   │
   ├─ 1. ingestão: ordena arquivos, valida, calcula duração total
   ├─ 2. VAD: vad_filter=True (Silero), min_silence_duration_ms ≈ 500
   ├─ 3. STT: WhisperModel(modelo, device=cpu, compute_type=int8), language="pt"
   ├─ 4. montagem: segments → transcricoes/<data>.md (contrato em 01-arquitetura)
   └─ 5. higiene: apaga os .wav processados (apenas se a etapa 4 foi 100%)
```

Esboço (referência para a implementação):

```python
from faster_whisper import WhisperModel

model = WhisperModel("small", device="cpu", compute_type="int8")

segments, info = model.transcribe(
    "audio/bruto/2026-10-14/gravacao.wav",
    language="pt",
    vad_filter=True,
    vad_parameters={"min_silence_duration_ms": 500},
    initial_prompt="Transcrição pessoal em português do Brasil.",
)

for seg in segments:
    ...  # acumula por trecho HH:MM → escreve no .md
```

## Modelo: qual escolher? (decisão aberta — medir na Fase 1/2)

| Modelo | Tamanho | CPU (int8) | pt-BR |
|---|---|---|---|
| tiny / base | 75 / 142 M | muito rápido | fraco |
| **small** | 244 M | rápido | razoável ← primeiro candidato |
| medium | 769 M | lento em CPU | bom |
| large-v3 | 1,5 B | só confortável com GPU | excelente |

Critério: para 8–10 h de áudio, o que importa é tempo de processamento por hora de áudio — medir com amostra real do seu dia (fala, ruído de rua).

## Integração com Gradle (decisão aberta — ver ADR-0002)

Você constrói apps com **Gradle**, então `./gradlew transcrever` deve ser o ponto de entrada. Duas formas:

### Opção B — Gradle orquestra Python (recomendada)
- `build.gradle.kts` registra tasks `Exec` que rodam um script Python (venv do projeto) com o faster-whisper.
- **Prós:** faster-whisper é Python-only; mais rápido em CPU; Gradle continua como entrypoint e dono do fluxo (inputs/outputs/caching).
- **Contras:** dois runtimes no repo (Kotlin DSL + Python) e gerência da venv como task.

### Opção A — 100% Kotlin/Gradle
- [Bindings Java oficiais do whisper.cpp](https://github.com/ggml-org/whisper.cpp/tree/master/bindings/java) (JNA, MIT, testados em Linux) direto numa task/app Kotlin.
- **Prós:** um runtime só, sem Python.
- **Contras:** bindings de baixo nível — decodificação de áudio e VAD por conta própria (mais código de casa); em CPU o CTranslate2 costuma superar o whisper.cpp.

Nenhuma das duas muda o resto do pipeline (o `.md` é igual) — dá para decidir no início da Fase 2 sem retrabalho.

## Saída

Formato do `.md`: ver [01-arquitetura.md](01-arquitetura.md#contrato-do-md-de-saída).

## Requisitos (Fase 2)

- Opção B: Python 3.10+ · `pip install faster-whisper` · modelos baixados 1×
- Opção A: JDK 17+ · lib nativa whisper.cpp no classpath/JNA path
- Gradle 8+ (wrapper do projeto)
- Nenhuma conta, nenhuma chave de API
