# sticker-audio

Figurinhas com áudio (campo 134 do WhatsApp) via Baileys, para uso pessoal. Tem dois modos: **bot** (comandos por prefixo no seu próprio WhatsApp) e **CLI** (envia uma figurinha e sai).

## Instalar

Requisitos: Node.js 18+ (20+ recomendado), ffmpeg e ffprobe no PATH com libwebp e libopus.

```
npm install
```

## Primeira execução (QR, sem número fixo)

```
node bot.js
```

Escaneie o QR em: WhatsApp → Aparelhos conectados → Conectar um aparelho. A sessão fica em `./auth`. Para trocar de conta, apague `./auth`.

## Comandos (prefixo padrão `.`)

Quem pode usar: **só você** (a conta conectada) e quem estiver em `passes.json`. Admin de grupo **não** ganha acesso automático.

|Comando|Quem|O que faz|
|---|---|---|
|`.menu`|todos com passe|lista os comandos|
|`.ping`|todos com passe|confirma que está online|
|`.fig`|todos com passe|imagem, GIF ou vídeo, na mensagem ou respondendo|
|`.figaud`|todos com passe|vídeo (usa o som dele), ou imagem com legenda `.figaud` respondendo a um áudio|
|`.toimg` / `.tovid`|todos com passe|figurinha → imagem / vídeo (respondendo a figurinha)|
|`.parar` / `.iniciar`|dono|no chat atual, obedece só ao dono (e à whitelist) / volta ao normal|
|`.whitelist parar @quem`|dono|libera alguém no chat parado (precisa ter passe também)|
|`.whitelist remover @quem` / `listar`|dono|tira da whitelist / mostra a whitelist|
|`.darpasse @quem`|dono|dá passe (aceita menção, resposta ou número)|
|`.removerpasse @quem`|dono|tira o passe|
|`.listapasse`|dono|lista os passes (resposta chega no seu privado)|
|`.setprefix <novo>`|dono|troca o prefixo (1 a 3 símbolos)|
|`.status`|dono|prefixo, tempo online, passes, fila, modo do áudio, log|
|`.logs [n]`|dono|últimas linhas do log (no seu privado)|
|`.reiniciar`|dono|recarrega config.json e passes.json sem reiniciar o bot|

### Argumentos das figurinhas

- `-crop`: corta no centro até 1:1 (padrão)
- `-stretch`: estica até 1:1
- `-nome "Pack|Autor"`: nome e autor da figurinha
- `-ini 3`: começa no segundo 3 do vídeo/áudio
- `-dur 5`: duração máxima (padrão `maxSeconds` de config.json)

Exemplos: .fig -stretch .figaud -ini 2 -dur 4 -nome "Meu Pack|Eu"

Os metadados padrão são `Feita por: <seu nome>` (`$username` é trocado pelo nome de quem enviou).

## Arquivos editáveis à mão

Ficam na pasta do projeto. O bot recarrega sozinho quando você salva.

- `config.json`: prefixo, `stickerPackName` (aceita `$username`), `stickerPublisher`, `maxSeconds`, `maxInputMB`, `cooldownMs`, `reactions`, `logLevel`
- `passes.json`: lista de quem pode usar. Formato: `{"passes": [{"id": "5588992332121"}]}` (número com DDI, sem `@`)
- `state.json`: chats em modo `parar` e suas whitelists (normalmente gerenciado pelos comandos)

## Log

- `logs/bot-AAAA-MM-DD.log`: um arquivo por dia, com cada comando (quem, onde, papel), resultado, tempo e erros completos
- `logs/baileys.log`: avisos internos da biblioteca
- Ignorados também aparecem no log (`⛔ ignorado`), com o motivo

## CLI (uma figurinha, sem bot)

```
node sticker-audio.js --img foto.png --audio voz.mp3 --chat 5511999999999
node sticker-audio.js --video clipe.mp4 --start 2 --max-seconds 6 --chat 5511999999999
node sticker-audio.js --img foto.png --mic 5 --chat 5511999999999
node sticker-audio.js --img foto.png --audio voz.mp3 --dry-run --out ./out
```

Use `--fit stretch` para esticar, `--skip-check` para não conferir o número antes de enviar. Não rode o CLI e o bot ao mesmo tempo com a mesma pasta `./auth`.

## Como o campo 134 é enviado

Se o WAProto do Baileys instalado já conhece `audioStickerMessage` (134) e `StickerMessage.audioMessage` (26), envia nativo. Senão, o script anexa os bytes do campo 134 no `proto.Message.encode` (patch só neste processo). `.status` mostra qual modo está ativo.

## Limitações conhecidas

- Testei a lógica de comandos e a conversão com WhatsApp simulado. O envio real só foi validado na sua conta, e depende do destinatário ter o recurso de figurinha com áudio.
- Duração oficial máxima do áudio não confirmada: use `maxSeconds`.
- API não oficial: use só na sua conta e sem spam, para evitar bloqueio.# sticker-audio-bot
