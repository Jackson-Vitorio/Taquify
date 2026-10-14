# ADR-0001: Captura contínua com o Echo + transferência manual por USB

- **Status:** aceito
- **Data:** 2026-10-14

## Contexto

O projeto precisa capturar tudo o que o usuário fala ao longo do dia no celular, com pouco armazenamento livre, preferência por soluções gratuitas e o trabalho pesado no PC. O usuário está em Android.

## Decisão

Usar o **Echo** (F-Droid, GPL-3.0) como gravador contínuo:

1. Rotina manual: ▶ de manhã, ■ à noite → grava WAV PCM16 contínuo (~345 MB/h).
2. Transferência **manual por USB** no fim do dia (sem Syncthing/sync automático).
3. Áudio é **transitório**: apagar do celular após copiar e do PC após transcrever.
4. Fluxo de "gravar só quando há fala" (estilo replay-buffer do OBS) **não será implementado**.

## Alternativas consideradas

| Alternativa | Por que recusada |
|---|---|
| App Android custom com replay-buffer VAD | projeto extra de manutenção; a economia de GB/dia não compensa frente ao fluxo transfere-e-apaga |
| Transcrição on-device (celular transcreve sozinho) | Transcribro e total_recall só suportam inglês; Whisper IME (F-Droid) limita gravações a 30 s; exigiria app custom para pt-BR e custo de bateria alto |
| Syncthing / sync automático | usuário prefere passar manualmente no fim do dia — menos peças móveis |
| iOS | usuário está em Android |

## Consequências

- **Positivo:** zero código de celular; app FOSS; áudio nunca acumula; WAV sem compressão é o formato ideal para o Whisper.
- **Negativo:** ~8 GB/dia transitórios exigem disciplina diária de cópia+apagar; esquecer o ▶ ou a cópia perde o dia; bateria drena como "áudio tocando sempre".
- **Risco:** o comportamento do app foi lido do código-fonte — confirmar no spike da Fase 1.
