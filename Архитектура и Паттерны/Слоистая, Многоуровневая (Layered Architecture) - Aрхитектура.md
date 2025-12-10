Такая архитектура подразумевает разделение информационной системы на уровни абстракции, где каждый уровень:
- отвечает только за свою задачу; 
- не зависит от уровня выше;
- не имеет понятия от деталей реализации уровней ниже.

Распространенные уровни слоев информационной сисиемы:
1. Слой представления.
2. Слой приложения.
3. Слой бизнес-логики.
4. Слой доступа к данным.

### 1. Слой представления (Presentation Layer)

Ответственен за:
- Взаимодействие с внешними пользователями - API Gateway, веб, мобильное приложение;
- Прием запросов и возвращение ответов - JSON, HTML, gRPC, WebSocket;
- Валидация входных данных на уровне формы/контракта (не бизнес правил);
- Не содержит бизнес логику - только преобразование данных и маршрутизация запроса;

Как пример: React, DRF (Serializer, View)

### 2. Слой приложения (Application Layer)

Ответственен за:
- Последовательность выполнения бизнес кейсов
- Определение use cases -- сценариев выполнения
- Не принимает бизнес решения и не хранит состояния доменной модели

Ключевая идея: Application layer знает что должно быть сделано, но не знает как → реализация “как” лежит в доменном слое.

Пример

```python
user = self.user_repo.get(command.user_id)
slot = self.slot_repo.get(command.slot_id)

booking = Booking.create(user, slot)  # доменная логика
booking.cancel()
booking.validate_availability()

price.calculate_total()
user.can_access(resource)
```

### 3. Слой бизнес-логики (Domain / Business Logic Layer)

Ответственен за:
- Хранение всей предметной логики системы.
- Определение доменных моделей, агрегатов, правил, инвариантов, ограничений.

Примеры: Domain models, domain services, value objects, entities, правила расчётов, ограничения состояний.

```python
class Booking:
    def __init__(self, user, specialist, slot, status="created"):
        self.user = user
        self.specialist = specialist
        self.slot = slot
        self.status = status

    @staticmethod
    def create(user, specialist, slot):
        if not slot.is_available:
            raise DomainError("Slot is not available")

        if user.has_overlapping_booking(slot):
            raise DomainError("User already has a booking for this time")

        return Booking(user, specialist, slot)

    def cancel(self, now):
        if self.status != "created":
            raise DomainError("Only created bookings can be cancelled")

        if self.slot.starts_in_less_than(hours=2, now=now):
            raise DomainError("Too late to cancel without penalty")

        self.status = "cancelled"

```

### 4. Слой доступа к данным (Data Access / Infrastructure Layer)

Ответственен за:
- Работа с внешними ресурсами: база данных, кеш, очереди, файловые хранилища, сторонние API.
- Реализация интерфейсов репозиториев [Репозиторий (Repository) - Паттерн], конкретных адаптеров.
- Конфигурация ORM (Django ORM, SQLAlchemy), драйверов, подключения.
- Слой может менять реализацию без изменения домена (например, PostgreSQL → MongoDB).

Ключевой принцип: Инфраструктура адаптируется под бизнес, а не наоборот.

Примеры: Repository implementations, Data mappers, ORM models, S3 adapters, mail providers.