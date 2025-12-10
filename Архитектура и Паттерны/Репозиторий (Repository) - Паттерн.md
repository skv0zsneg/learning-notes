Репозиторий (Repository) — это паттерн проектирования, используемый в архитектуре приложения для отделения слоя бизнес-логики от слоя доступа к данным. Он выступает в роли посредника между доменными объектами (бизнес-логикой) и источником данных (например, базой данных), абстрагируя приложение от конкретной реализации хранения данных.

### Назначение:
- Скрыть сложность взаимодействия с базой данных.
- Обеспечить тестируемость, так как можно легко подменить репозиторий моком.
- Упростить поддержку и масштабирование кода.

```python
from typing import List

class User:
    def __init__(self, user_id: int, name: str):
        self.user_id = user_id
        self.name = name

# Интерфейс репозитория
class UserRepository:
    def get_by_id(self, user_id: int) -> User:
        raise NotImplementedError

    def get_all(self) -> List[User]:
        raise NotImplementedError

    def save(self, user: User):
        raise NotImplementedError

# Реализация репозитория (например, для работы с БД)
class InMemoryUserRepository(UserRepository):
    def __init__(self):
        self._users = {1: User(1, "Alice"), 2: User(2, "Bob")}

    def get_by_id(self, user_id: int) -> User:
        return self._users.get(user_id)

    def get_all(self) -> List[User]:
        return list(self._users.values())

    def save(self, user: User):
        self._users[user.user_id] = user

# Использование
repo: UserRepository = InMemoryUserRepository()
user = repo.get_by_id(1)
print(user.name)  # Выведет: Alice

```