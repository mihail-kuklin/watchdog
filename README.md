# watchdog

Внешний watchdog бокса **capiros** ([zenmoney-tools](https://github.com/mihail-kuklin/zenmoney-tools)):
GitHub-hosted джоба каждые 30 минут зондит nginx+fpm+Laravel и Grafana снаружи;
при провале — алерт в Telegram. Репо публичное, потому что в нём нет секретов в
коде (токен бота — в Actions Secrets), а public-раннеры бесплатны.

- Workflow: [.github/workflows/box-watch.yml](.github/workflows/box-watch.yml)
- Зонды: `POST /ops/alerts` → 403 (Laravel жив), `/app` → 200 (PWA жива),
  `grafana.capiros.ru/api/health` → 200 (Grafana жив).
- Внутри бокса юниты стережёт `watchdog.sh` (cron `*/30`, см. zenmoney-tools →
  deploy/observability/) и Loki-алерт «Тишина»; этот репо ловит случай «весь
  бокс лёг», когда внутренние сторожа молчат вместе с боксом.

Секреты: `WATCHDOG_BOT_TOKEN`, `WATCHDOG_CHAT_ID` (Settings → Secrets → Actions).

Расписание не уснёт: [keepalive.yml](.github/workflows/keepalive.yml) 1-го и
15-го коммитит дату в `keepalive.txt`, сбрасывая 60-дневное окно GitHub для
всех расписаний репо (пуш от встроенного GITHUB_TOKEN, без ключей и cron на VPS).
