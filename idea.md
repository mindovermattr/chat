# Slack-lite Chat Platform (GraphQL + WebSocket)

## Overview

Проект представляет собой real-time чат-платформу для команд (аналог Slack/Discord-lite), реализованную с использованием GraphQL и WebSocket (subscriptions). Основная цель — построить production-приближенную архитектуру с упором на real-time взаимодействие, консистентность данных и масштабируемость.

Проект предназначен для прокачки навыков middle fullstack-разработчика и может быть реализован с применением агентского программирования (AI agents для генерации кода, тестов, схем и рефакторинга).

---

## Goals

* Освоить GraphQL на уровне архитектуры (schema design, subscriptions, caching)
* Реализовать real-time функциональность через WebSocket
* Построить масштабируемую backend-архитектуру
* Научиться работать с event-driven подходом
* Прокачать frontend state management в условиях real-time обновлений

---

## Core Features

### Authentication

* Регистрация
* Логин (JWT или session-based)
* Logout

### Workspace

* Создание workspace
* Приглашение пользователей
* Список участников

### Channels

* Создание каналов
* Публичные / приватные каналы
* Список каналов workspace

### Messaging

* Отправка сообщений
* Редактирование сообщений
* Удаление сообщений (soft delete)
* История сообщений (pagination)

### Threads

* Ответы на сообщения (thread)
* Отдельный поток сообщений для треда

### Reactions

* Добавление/удаление реакций
* Агрегация реакций

---

## Real-time Features (WebSocket / GraphQL Subscriptions)

* Новые сообщения в канале
* Новые сообщения в треде
* Typing indicator (start/stop)
* Online/offline статус пользователей (presence)
* Обновления реакций
* Read receipts

---

## Advanced Features (Optional, но сильно усиливают проект)

* Cursor-based pagination
* Optimistic UI (frontend)
* Поиск сообщений
* Уведомления
* Roles & permissions (admin/user)
* Pinned messages
* Draft messages
* Rate limiting
* Reconnect logic для WebSocket
* Idempotency для отправки сообщений

---

## Tech Stack (Recommended)

### Frontend

* React
* TypeScript
* GraphQL Client (Apollo / urql)
* Zustand или Redux Toolkit
* WebSocket client (graphql-ws)

### Backend

* Node.js
* GraphQL (Apollo Server / GraphQL Yoga)
* WebSocket (graphql-ws)
* PostgreSQL
* Prisma ORM

### Infra

* Redis (pub/sub, presence, scaling)
* Docker / docker-compose

---

## Architecture

### Backend Layers

* **API Layer**

  * GraphQL schema (queries, mutations, subscriptions)
  * Resolvers

* **Service Layer**

  * Бизнес-логика
  * Обработка событий

* **Data Layer**

  * Prisma / SQL
  * Репозитории

* **Realtime Layer**

  * Pub/Sub (Redis)
  * WebSocket connections

---

## GraphQL Schema (High-level)

### Queries

* `me`
* `workspaces`
* `channels(workspaceId)`
* `messages(channelId, cursor, limit)`
* `thread(messageId)`

### Mutations

* `sendMessage`
* `editMessage`
* `deleteMessage`
* `addReaction`
* `removeReaction`
* `createChannel`
* `markAsRead`

### Subscriptions

* `messageCreated(channelId)`
* `threadMessageCreated(messageId)`
* `userTyping(channelId)`
* `userPresenceChanged`
* `reactionUpdated(messageId)`

---

## Data Model (Simplified)

* User
* Workspace
* Channel
* ChannelMember
* Message
* Reaction
* Thread (или parentMessageId)
* ReadReceipt

---

## Development Plan

### Phase 1 — MVP

* Auth
* Workspace
* Channels
* Basic messaging (queries + mutations)

### Phase 2 — Real-time

* Subscriptions для сообщений
* WebSocket подключение
* Live обновление чата

### Phase 3 — Core UX

* Threads
* Reactions
* Typing indicator

### Phase 4 — Advanced

* Read receipts
* Presence
* Pagination (cursor-based)

### Phase 5 — Production polish

* Redis pub/sub
* Error handling
* Reconnect logic
* Rate limiting
* Docker

---

## Non-functional Requirements

* Масштабируемость (горизонтальная через Redis)
* Низкая задержка обновлений
* Консистентность сообщений
* Защита от дубликатов
* Обработка reconnection

---

## Agent-based Development Ideas

Можно разбить разработку на агентов:

* **Schema Agent**

  * Генерирует GraphQL schema
  * Поддерживает типы и связи

* **Backend Agent**

  * Пишет resolvers и сервисы
  * Следит за архитектурой

* **Frontend Agent**

  * Генерирует компоненты
  * Интегрирует GraphQL

* **Realtime Agent**

  * Отвечает за subscriptions и WebSocket

* **QA Agent**

  * Генерирует тесты
  * Проверяет edge cases

---

## What Will Make This Project Stand Out

* Чистая архитектура (разделение слоев)
* Использование subscriptions, а не polling
* Optimistic UI
* Cursor pagination
* Работа с reconnect
* Документированная архитектура
* Demo (deploy + README + screenshots)

---

## Deliverables

* GitHub repository
* README (архитектура + решения)
* Запуск через docker-compose
* Демо (Vercel / Render / Fly.io)
* Минимальные тесты

---

## Final Note

Если реализовать хотя бы 70% этого ТЗ с хорошим качеством — это уже уровень уверенного middle+ проекта. Если добавить Redis, subscriptions и аккуратную архитектуру — это будет выглядеть как production-ready система.
