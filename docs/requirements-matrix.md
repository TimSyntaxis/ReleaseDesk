# Матрица требований ReleaseDesk

Курс: Б1.В.ДЭ.01.02.02 · КГЭУ · 2026/2027 · ЛР2  
Студент: Шафиков Тимур Фидаилевич · Группа: ТРИС-3-23

Связывает требования и критерии из [ТЗ ЛР1](lab-01-requirements.md) с [моделью данных](data-model.md) и [API](api-contract.md).

## Три обязательных критерия

| Требование ЛР1 | Поле или сущность | Запрос | Критерий |
|----------------|-------------------|--------|----------|
| Менеджер проекта создаёт заявку на публикацию (ФТ-2, ФТ-3) | `Release`: `title`, `storeId` → `Store`, `description`; сервер ставит `number`, `status = New`, `createdByUserId` | `POST /api/releases` → `201` / `400` | 1. Создать |
| Менеджер проекта назначает релиз-инженера (ФТ-4) | `Release.assigneeUserId` → `User` (роль `ReleaseEngineer`) | `PATCH /api/releases/{id}/assignee` → `200` / `409` | 2. Назначить |
| Назначенный релиз-инженер переводит свою заявку в работу (ФТ-6, ФТ-9) | `Release.status`: `New` → `InProgress`; проверка `assigneeUserId` = вошедший пользователь | `PATCH /api/releases/{id}/status` → `200` / `403` | 3. В работу |

## Остальные функциональные требования

| Требование ЛР1 | Поле или сущность | Запрос |
|----------------|-------------------|--------|
| ФТ-1. Вход по логину и паролю | `User.login`, `User.passwordHash`, `User.role` | появится в ЛР11 (вход и доступ) |
| ФТ-5. Релиз-инженер видит свои заявки, фильтр по статусу | `Release.assigneeUserId`, `Release.status` | `GET /api/releases` (фильтр на клиенте) |
| ФТ-7. Релиз-инженер переводит заявку в «Опубликовано» | `Release.status`: `InProgress` → `Closed` | `PATCH /api/releases/{id}/status` |
| ФТ-8. Отмена заявки | `Release.status` → `Cancelled` | `PATCH /api/releases/{id}/status` |
| ФТ-10. Конечные статусы не меняются | `Release.status` ∈ {`Closed`, `Cancelled`} | `PATCH …/assignee` и `PATCH …/status` → `409` |
| ФТ-11. Сводка руководителя | `Release.status`, `storeId`, `assigneeUserId` | `GET /api/releases` (подсчёт по статусам) |
| Выбор магазина в форме | `Store.id`, `Store.name` | `GET /api/stores` |
| НФТ-1. Данные после перезапуска | таблицы `releases`, `stores`, `users` в PostgreSQL | — |
| НФТ-3. Права проверяет сервер | `User.role`, `Release.assigneeUserId` | `403` на `PATCH` в обход интерфейса |
