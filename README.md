# lensflare-proto

Единый репозиторий protobuf-контрактов для микросервисов Lensflare.

---

## 📦 Структура

- `proto/` — все `.proto`-файлы по сервисам (`auth`, `org` и др.)
- `buf.yaml`, `buf.gen.yaml` — настройки lint/generate ([buf.build](https://docs.buf.build/))
- `go.mod` — для публикации proto как Go-модуля
- `gen/` — сгенерированные артефакты **НЕ хранятся в git!** Генерируются локально при сборке сервисов

---

## 🛠️ Использование в микросервисах

1. **Добавить proto-репозиторий в зависимости вашего микросервиса:**

    ```sh
    go get github.com/pyatnitsev/lensflare-proto@v1.0.0
    ```

2. **Сгенерировать gRPC/Go артефакты:**

    - Установить [buf](https://docs.buf.build/installation) (рекомендуется >= v1.32)
    - Запустить генерацию в корне микросервиса:
      ```sh
      buf generate github.com/pyatnitsev/lensflare-proto
      ```
      Или, если proto-файлы лежат как submodule/vendor:
      ```sh
      buf generate
      ```

3. **Импортировать сгенерированные пакеты:**

    ```go
    import authpb "github.com/pyatnitsev/lensflare-proto/gen/auth"
    import orgpb "github.com/pyatnitsev/lensflare-proto/gen/org"
    ```

---

## 🚦 Best Practices

- **/gen/** не коммитим в git.  
  Генерация всегда происходит локально или в CI при сборке приложения.
- **Только `.proto` — источник правды.**  
  Любые изменения сначала в proto, потом в go-модели.
- **Версионируем через git tags:**  
  После изменений делайте `git tag vX.Y.Z` и пушьте тег, чтобы сервисы могли обновлять go-модуль.
- **Совместимость:**  
  Добавляйте новые поля в messages, но не удаляйте и не переиспользуйте номера старых полей.

---

## 🧰 Для Go-проектов на Docker

- В Dockerfile включайте шаг генерации proto через buf перед `go build`.
- Пример:

    ```dockerfile
    FROM ...
    # Клонируем proto-репозиторий
    RUN git clone --branch v1.0.0 https://github.com/pyatnitsev/lensflare-proto.git /proto
    WORKDIR /app
    # Генерируем gRPC-артефакты
    RUN buf generate /proto
    ```

---

## 🌐 Поддерживаемые языки

- Основной язык: Go (go, go-grpc)
- Могут добавляться плагины для других языков — смотрите/меняйте `buf.gen.yaml`

---

## ⚡ Быстрый старт

```sh
go get github.com/pyatnitsev/lensflare-proto@latest
buf generate github.com/pyatnitsev/lensflare-proto
```

## ⚠️ Важно

- Не храните сгенерированные `.pb.go` в этом репозитории.
- Все сервисы должны генерировать свой `/gen/` сами (это просто, быстро и надёжно).

## 📝 Пример buf.yaml

```yaml
version: v1
lint:
  use:
    - DEFAULT
breaking:
  use:
    - FILE
```

`buf.gen.yaml` смотри в корне репозитория или дорабатывай под свои языки.