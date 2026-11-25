# Задание 6. Promotion: staging → release

**Что делаем:** заводим в Artifactory два локальных docker-репозитория
и проводим образ через контроль качества: сначала staging, после
проверки — release. Деплой в прод должен ходить только в release.

## Что сделать

1. Создайте локальные репозитории `docker-staging-local` и
   `docker-release-local` (Administration → Repositories → Add →
   Local Repository → Docker).
2. Доставьте образ в staging. Адрес — тот же `localhost:8081`,
   имя репозитория — часть пути:

   ```bash
   docker login localhost:8081     # креды Artifactory
   docker pull localhost:8081/docker/<логин>/pipeline-lab:latest
   docker tag localhost:8081/docker/<логин>/pipeline-lab:latest \
              localhost:8081/docker-staging-local/pipeline-lab:____   # версия релиза
   docker push localhost:8081/docker-staging-local/pipeline-lab:____
   ```

3. Проведите promotion через jfrog CLI. Разберитесь с синтаксисом
   `jf rt docker-promote --help`: какие три аргумента у команды,
   что делает `--source-tag` и зачем нам `--copy` (по умолчанию
   образ **перемещается**, а мы хотим копию).
4. Убедитесь, что образ появился в release-репозитории
   (UI или `jf rt s "docker-release-local/"`).
5. Переключите деплой: в `docker-compose.deploy.yml` замените
   репозиторий на release (`localhost:8081/docker-release-local/...`,
   тег — версия), поднимите и проверьте `curl`.

## Вопрос на подумать (письменно)

Чем promotion отличается от простого `docker push` сразу в release?
Что мы выигрываем?

## В отчёт

- вывод `jf rt docker-promote` и список файлов release-репозитория;
- обновлённый `docker-compose.deploy.yml`;
- ответ на вопрос.
