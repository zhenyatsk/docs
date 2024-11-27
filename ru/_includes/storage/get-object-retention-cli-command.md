Убедитесь, что настройки блокировки версии объекта изменились:

```bash
yc storage s3api get-object-retention \
  --bucket <имя_бакета> \
  --key <ключ_объекта> \
  --version-id <идентификатор_версии>
```

Где:

* `--bucket` — имя вашего бакета.
* `--key` — ключ объекта.
* `--version-id` — идентификатор версии объекта