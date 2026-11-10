# Формат `.aipack`

`.aipack` — переносимый аудиопак AutoInformant Pro. Это обычный ZIP-архив
(без пароля и сжатия специальными алгоритмами), внутри которого лежат
`manifest.json`, иконка и аудиоресурсы. Текущая версия схемы — **v1**.

## Структура архива

```
manifest.json                              описание пака
pack_icon.png                              иконка (необязательна)
assets/
  maps/
    <Карта>/                               основная библиотека карты
      All/                                 общие звуки остановок
      <маршрут>/                           папка маршрута (route.txt + звуки)
      _AutoInformantCustom/                аудио, импортированное пользователем
  categories/
    <Карта>/<Категория>/                   библиотека категории транспорта
```

Все пути в манифесте относительны корня пака. Абсолютные пути не допускаются:
при импорте они проверяются и отклоняются.

## Манифест

```json
{
  "format": "autoinformant-pack",
  "schema_version": 1,
  "id": "tver-2026",
  "name": "Информатор Твери",
  "author": "Левандий",
  "description": "Аудиопак для карты Тверь 2026.",
  "icon": "pack_icon.png",
  "map_profiles": [
    {
      "id": "map-main",
      "map_aliases": ["Tver_2026"],
      "asset_root": "assets/maps/Tver_2026",
      "settings": {
        "auto_arrival": true,
        "auto_departure": true,
        "arrival_radius": 18.0,
        "max_arrival_speed": 2.5,
        "exit_speed": 4.0,
        "play_next_prefix": true,
        "play_terminus": true,
        "anons_enabled": true,
        "anons_mode": "minutes",
        "anons_interval": 2,
        "average_speed_kmh": 25.0,
        "anons_safety_seconds": 5.0,
        "play_jingle": true,
        "doors_enabled": false
      },
      "stop_overrides": {
        "id:25725": "Мамулино.mp3"
      },
      "ignored_stops": [],
      "announcement_schedules": {}
    }
  ],
  "category_profiles": [
    {
      "id": "trolleybus",
      "name": "Троллейбусы",
      "map_aliases": ["Tver_2026"],
      "asset_root": "assets/categories/Tver_2026/Троллейбусы",
      "replace_map_bank": true,
      "vehicle_assignments": ["ZiU-682 | V", "ZiU | 682G g00"],
      "settings": {},
      "stop_overrides": {},
      "ignored_stops": [],
      "announcement_schedules": {}
    }
  ]
}
```

### Поля верхнего уровня

| Поле | Тип | Описание |
| --- | --- | --- |
| `format` | строка | Всегда `autoinformant-pack` |
| `schema_version` | число | Версия схемы, сейчас `1` |
| `id` | строка | Уникальный идентификатор пака (slug) |
| `name` | строка | Отображаемое название |
| `author` | строка | Автор пака |
| `description` | строка | Описание для карточки пака |
| `icon` | строка | Путь к иконке внутри пака |
| `map_profiles` | массив | Профили карт |
| `category_profiles` | массив | Профили категорий транспорта |

### Профиль карты

| Поле | Описание |
| --- | --- |
| `id` | Идентификатор профиля внутри пака |
| `map_aliases` | Имена карты, которые сообщает openOMSI |
| `asset_root` | Папка с аудиоресурсами профиля |
| `settings` | Настройки объявлений (радиусы, режим, интервал и т. д.) |
| `stop_overrides` | Ручные назначения звука остановкам |
| `ignored_stops` | Остановки, исключённые из озвучивания |
| `announcement_schedules` | Расписания объявлений |

### Профиль категории

Дополнительно к полям профиля карты:

| Поле | Описание |
| --- | --- |
| `replace_map_bank` | `true` — категория полностью заменяет основной банк для назначенных моделей |
| `vehicle_assignments` | Модели в формате `Производитель \| Модель` |

### Ключи `stop_overrides`

Ключ строится по остановке из игры:

- `id:<ID>` — если openOMSI передаёт идентификатор остановки;
- `name:<нормализованное имя>` — если идентификатора нет.

Значение — путь к аудиофайлу относительно `asset_root` профиля.
Файлы, импортированные пользователем, сохраняются в `_AutoInformantCustom/`.

## Совместимость

- Схема v1 используется с AutoInformant Pro 1.10 и не менялась в 2.0.x.
- Неизвестные поля игнорируются, обязательные проверяются при импорте.
- Для новой версии схемы потребуется отдельная миграция; текущие паки
  останутся совместимыми.
