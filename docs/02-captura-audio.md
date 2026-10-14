# 02 — Captura de áudio (Echo)

## O app

- **Echo — Time travelling audio recorder** (pacote `eu.mrogalski.saidit`)
- Licença GPL-3.0 · [F-Droid](https://f-droid.org/packages/eu.mrogalski.saidit/) · versão 2.0.0 (jun/2025) · Android 11+
- Serviço em primeiro plano com tipo `microphone` (`FOREGROUND_SERVICE_MICROPHONE`), sobrevive a reboot (`RECEIVE_BOOT_COMPLETED`) e a swipe dos apps recentes (START_STICKY + restart via AlarmManager)

## Como ele grava (análise do código-fonte)

1. **"Ouvindo" o tempo todo:** `AudioRecord` captura PCM mono na taxa nativa do aparelho (normalmente **48 kHz**) e alimenta um **buffer circular em memória** (`AudioMemory`) — o "rádio tempo viagem". Por padrão guarda os últimos ~20 minutos (configurável); nada vai a disco ainda.
2. **Apertou "gravar":** o conteúdo do buffer é despejado num arquivo **WAV (PCM16)** via `WavFileWriter` e a gravação segue contínua até você parar.
3. **Não salvou?** o buffer é circular — o que não foi salvo se apaga sozinho (o app nunca cresce sozinho).

> **Consequência prática:** para capturar o dia inteiro a rotina é
> **apertar ▶ de manhã → parar à noite → transferir → apagar**.
> Se esquecer de apertar, só existe o buffer (~20 min).

⚠️ Leitura feita no código-fonte da v2.0.0 — validar no spike da Fase 1 com o app instalado.

## Espaço ocupado

WAV **não comprimido** — 2 bytes por amostra, mono (`FILL_RATE = 2 × SAMPLE_RATE` no código):

| Taxa | Por hora | 24 horas |
|---|---|---|
| 48 kHz (comum) | **~345 MB** | **~8,3 GB** |
| 44,1 kHz | ~317 MB | ~7,6 GB |
| 16 kHz (se configurável) | ~115 MB | ~2,8 GB |

**Regra do projeto: o áudio é transitório.**
- Pico de espaço = 1 dia (~8 GB) — transfira e apague **todo dia**.
- O `.md` final do dia inteiro: ~60–100 KB.
- O Whisper aceita WAV direto — não há ganho em recompressar.

## Bateria

O próprio app avisa: drenagem comparável a "áudio tocando o tempo todo". Medir de verdade é objetivo da Fase 1.

**Mitigações:** manter carregado parte do dia, colocar o Echo na whitelist de otimização de bateria do sistema, atenção a fabricantes agressivos (Xiaomi/MIUI etc.) que matam serviços em segundo plano.

## Rotina diária proposta

1. **Manhã:** abrir Echo → apertar ▶ gravar.
2. **Noite:** parar → conectar o cabo → copiar os `.wav` para `audio/bruto/<data>/` no PC.
3. **Confirmou a cópia?** apaga os `.wav` do celular.
4. Roda o pipeline (Fase 2) → some com o áudio do PC também.

## Checklist da Fase 1 (spike)

- [ ] Confirmar formato real dos `.wav` (taxa, canais, extensão)
- [ ] Medir MB/h reais e total de 1 dia
- [ ] Medir bateria: % por hora com Echo ligado
- [ ] Validar mecânica gravar/parar e tamanho do buffer "tempo viagem"
- [ ] Testar transferência USB (MTP e `adb pull`) com ~8 GB
- [ ] Testar interrupções: reinício, chamada recebida, app morto pelo sistema

## Descartados

- **App custom com replay-buffer VAD** (estilo OBS): economia não compensa o custo de manter um app Android — ver [ADR-0001](adr/0001-captura-com-echo.md)
- **Transcrição on-device:** apps existentes não servem para pt-BR em fundo contínuo (Transcribro e total_recall são EN-only; Whisper IME limita a 30 s) + bateria
