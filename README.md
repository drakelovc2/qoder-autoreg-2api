# qoder-autoreg-2api

Qoder в одном репозитории:

- **`register/`** — регистратор аккаунтов Qoder (web signup, код из catch-all почты);
- **`bridge/`** — **Qoder2Api** (Go): OpenAI/Anthropic-совместимый шлюз к пулу Qoder PAT.

```
qoder-autoreg-2api/
├── register/   ← создаёт аккаунты Qoder
└── bridge/     ← Qoder2Api 1.0.0 (upstream D3-vin/Qoder2Api, MIT)
```

## Цепочка

1. `register/` создаёт аккаунт Qoder (email + пароль + подтверждение кодом).
2. В настройках Qoder (`https://qoder.com/account`) берёшь **PAT** (`pt-…`).
3. PAT кладёшь в пул `bridge/` (`QODER_PAT_LIST` или дашборд).
4. Клиенты ходят в `bridge/` как в OpenAI/Anthropic API.

## Быстрый старт

### Реггер

```bash
cd register
cp config.example.json config.json   # mail.api_base / admin_key / domain, password
pip install -r requirements.txt
python3 -m playwright install chromium
bash start.sh
```

Статус: **assisted**. AliyunCaptcha в форме Qoder проходит человек в открытом
окне; форма, код из письма и сохранение аккаунта автоматизированы. Полностью
unattended требует солвера AliyunCaptcha.

### Мост

```bash
cd bridge
cp .env.example .env                 # QODER_PAT или QODER_PAT_LIST
go build -trimpath -ldflags="-s -w" -o qoder2api .
./qoder2api                          # дашборд http://127.0.0.1:8963/
```

Или возьми готовый бинарник из upstream-релизов.

## Эндпоинты моста

| Метод | Путь |
|---|---|
| POST | `/v1/chat/completions` (stream / non-stream) |
| POST | `/v1/messages` (Anthropic, Claude Code) |
| GET | `/v1/models` |
| GET | `/v1/usage` |
| GET | `/` — дашборд |
| GET/POST | `/api/accounts*`, `/api/quota/refresh`, `/api/settings` |

Модели — живые имена Qoder: `qwen3.8-max`, `qwen3.7-max`, `qwen3.7-plus`,
`deepseek-v4-pro`, `deepseek-v4-flash`, `lite`, `auto`.

## Claude Code

```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:8963
export ANTHROPIC_AUTH_TOKEN=any
export ANTHROPIC_DEFAULT_HAIKU_MODEL=qwen3.8-max
export ANTHROPIC_DEFAULT_SONNET_MODEL=qwen3.8-max
export ANTHROPIC_DEFAULT_OPUS_MODEL=qwen3.8-max
```

## Ограничения

- Реггер assisted: капча Aliyun вручную.
- Мост — upstream Qoder2Api 1.0.0, код не менялся; см. `bridge/UPSTREAM.md`.
- Только собственные/разрешённые аккаунты. PAT и почта — ваши, в репозитории их нет.

## Лицензия

MIT + условие добросовестного использования — см. [LICENSE](LICENSE).
Qoder2Api — MIT, автор D3-vin.
---

🔥 Telegram: [@antivibedrake](https://t.me/antivibedrake) · [@zakurulife](https://t.me/zakurulife)

Только для личного использования — см. [DISCLAIMER.md](DISCLAIMER.md).
