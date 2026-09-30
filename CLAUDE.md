# CLAUDE.md

Інструкції для Claude Code при роботі з цим Minecraft-модом.

## Стиль коду

### Java
- Java 21, Fabric/Forge/NeoForge (визначається `build.gradle`).
- Відступ — 4 пробіли, LF, UTF-8, без trailing whitespace.
- Максимальна довжина рядка — 120 символів.
- Іменування: `PascalCase` для класів, `camelCase` для методів і полів, `UPPER_SNAKE_CASE` для констант, `snake_case` для реєстрових ID (`mod_id:copper_hammer`).
- Пакети — тільки нижній регістр: `com.spxr3dox.externalminecraft.<domain>`.
- `final` за замовчуванням для полів і локальних змінних, де можливо.
- Використовувати `Optional`, `Stream`, `record` там, де це доречно; не зловживати.
- Ніяких `System.out.println` — тільки логер (`LoggerFactory.getLogger(...)`).
- Реєстрація контенту — через `DeferredRegister` (або еквівалент для лоадера); один клас на категорію (`ModItems`, `ModBlocks`, `ModEntities`, `ModSounds`).
- Мікшити клієнтський і серверний код заборонено — клієнтські класи в `client/`, гейти через `Env`/`@Environment`/`DistExecutor`.

### Ресурси
- `assets/<modid>/` — моделі, текстури, ленги, звуки.
- `data/<modid>/` — рецепти, теги, лут-таблиці, `worldgen`.
- Ключі перекладів: `item.<modid>.<name>`, `block.<modid>.<name>`, `entity.<modid>.<name>`.
- JSON-файли з 2-пробільним відступом.

### Комміти
- Conventional Commits: `feat:`, `fix:`, `refactor:`, `chore:`, `docs:`, `test:`.
- Заголовок ≤ 72 символи, у наказовому способі.

## Структура моду

```
external-minecraft/
├── build.gradle
├── gradle.properties
├── settings.gradle
├── src/
│   ├── main/
│   │   ├── java/com/spxr3dox/externalminecraft/
│   │   │   ├── ExternalMinecraft.java        // точка входу, MODID
│   │   │   ├── registry/                     // ModItems, ModBlocks, ...
│   │   │   ├── item/                         // класи предметів
│   │   │   ├── block/                        // класи блоків + BE
│   │   │   ├── entity/                       // сутності, AI
│   │   │   ├── world/                        // worldgen, features
│   │   │   ├── network/                      // пакети C2S/S2C
│   │   │   ├── event/                        // обробники подій
│   │   │   ├── mixin/                        // міксини (тільки якщо треба)
│   │   │   └── util/                         // хелпери
│   │   ├── resources/
│   │   │   ├── assets/externalminecraft/
│   │   │   ├── data/externalminecraft/
│   │   │   └── <mod>.mixins.json
│   │   └── client/java/com/spxr3dox/externalminecraft/client/
│   └── test/java/                            // JUnit 5
└── CLAUDE.md
```

## Заборонено писати в коді

**НЕ** додавати в код:
- `// TODO`, `// FIXME`, `// FIX`, `// XXX`, `// HACK`, `// NOTE`.
- Коментарі-пояснення того, що код і так очевидно робить (`// increment i`, `// return result`).
- Розділові коментарі-банери (`// ===== items =====`, `// --- helpers ---`).
- Ремарки про історію змін (`// added for issue #12`, `// old code below`, `// removed on 2026-...`).
- Закоментований мертвий код — видаляти повністю.
- JavaDoc-заглушки (`/** */`, `@param foo the foo`) без реальної цінності.

Дозволено лише коментар, який пояснює **НЕОЧЕВИДНЕ ЧОМУ**: прихований інвайрант, обхід конкретного бага з посиланням, тонка поведінка Minecraft/лоадера, яка здивує читача.

Незакінчену роботу вести в issue/PR, а не в коментарях коду.

## Робочий процес

- Гілка розробки: `claude/lucid-keller-amkt8b`.
- Перед комітом: `./gradlew build` має бути зеленим.
- Не змінювати `gradle/wrapper/*` без явного запиту.
- Не додавати залежності без явного запиту.
