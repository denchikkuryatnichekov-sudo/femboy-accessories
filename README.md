# Femboy Accessories 0.4.0

Косметический мод для Minecraft **1.21.1 (NeoForge)**: ошейники, чокер, кошачьи ушки, хвосты, бант, колокольчик и одежда.

- modid: `femboy`
- Java 21, NeoForge **21.1.265**
- 17 предметов, вкладка в креативе, ru/en локализация
- ошейники и ушки рисуются на голове, хвост — отдельным слоем, одежда — слоем брони
- эмоции и комплекты читаются из JSON
- клиентский конфиг `config/femboy-client.json`

**Инструкция для игроков: [GUIDE.md](GUIDE.md)** — установка, крафты, команды, проверка.

## Сборка

```bash
gradlew build
```

Jar появится в `build/libs/femboy-0.4.0.jar`. Нужен **JDK 21** (`JAVA_HOME`), интернет для первой загрузки зависимостей.
Проверено: `gradlew build` проходит, `BUILD SUCCESSFUL`.

Установка: `.minecraft/mods` в профиле NeoForge 1.21.1.

## Что в 0.4.0

**Эмоции** — `data/femboy/emotes.json`, загрузчик `Emotes`, команды:

```
/emote heart      — сердечки над игроком + action bar
/emote wave       — «машет рукой»
/emote note       — ноты
/emote blush      — смущение
/emote sparkle    — искры
/femboy emotes    — список доступных эмоций
```

**Одежда** — 4 предмета со слоем 64x32:

| Предмет | Слот | Что рисуется |
|---|---|---|
| `hoodie` | грудь | торс |
| `skirt` | ноги | верхняя часть ног |
| `thigh_highs` | ноги | нижняя часть ног |
| `shorts` | ноги | короткий верх |

**Комплекты** — `data/femboy/outfits.json`:

```
/femboy outfit pastel
/femboy outfit dark
/femboy outfits
```

**Конфиг** — `config/femboy-client.json` создаётся при первом запуске:

```json
{
  "renderEars": true,
  "renderTail": true
}
```

## Предметы

| Группа | Предметы | Слот | Рендер |
|---|---|---|---|
| Ошейники | `collar_black`, `collar_pink`, `collar_red`, `collar_white`, `collar_lilac`, `choker_lace` | голова | слой брони |
| Ушки | `cat_ears_black`, `cat_ears_pink`, `cat_ears_white` | голова | своя модель |
| Хвосты | `cat_tail_black`, `cat_tail_pink` | тело | RenderLayer |
| Одежда | `hoodie`, `skirt`, `thigh_highs`, `shorts` | грудь/ноги | слой брони |
| Мелочи | `ribbon_pink`, `bell` | — | — |

## Как это устроено

В 1.21.1 ещё нет компонента `minecraft:equippable` (он появился в 1.21.2), поэтому:

- ошейники, ушки и одежда — `ArmorItem` + свой `ArmorMaterial` (`ModArmorMaterials`), по материалу на каждый цвет;
- текстуры брони — `assets/femboy/textures/models/armor/<id>_layer_1.png` (шлем/нагрудник) и `_layer_2.png` (ноги);
- хвосты — обычный предмет со слотом `BODY` и своим `RenderLayer`;
- ушки подменяют модель брони через `IClientItemExtensions#getHumanoidArmorModel`.

## Структура

```
src/main/java/com/femboy/
  FemboyMod.java                    — точка входа, загрузка JSON, LOGGER
  item/WearableItem.java            — надевание в слот (хвосты), тултип
  registry/ModItems.java            — 17 предметов
  registry/ModArmorMaterials.java   — 13 материалов брони
  registry/ModCreativeTabs.java     — вкладка
  client/ModModelLayers.java        — слои моделей
  client/EarsModel.java             — голова + уши
  client/TailModel.java             — хвост
  client/TailLayer.java             — рендер хвоста (+ конфиг)
  client/EarsClientExtensions.java  — подмена модели (+ конфиг)
  client/ClientSetup.java           — регистрация
  emote/Emote.java, Emotes.java     — эмоции из JSON
  outfit/Outfits.java               — комплекты из JSON
  command/FemboyCommands.java       — /emote, /femboy
  config/FemboyClientConfig.java    — config/femboy-client.json
src/main/resources/
  META-INF/neoforge.mods.toml, pack.mcmeta
  assets/femboy/models/item/*.json          — 17 моделей
  assets/femboy/textures/item/*.png         — 17 иконок 16x16
  assets/femboy/textures/models/armor/*.png — 13 слоёв 64x32
  assets/femboy/textures/entity/tail_*.png  — 2 текстуры
  assets/femboy/lang/ru_ru.json, en_us.json
  data/femboy/recipe/*.json                 — 17 рецептов
  data/femboy/tags/item/*.json              — collars, ears, tails, trinkets
  data/femboy/emotes.json, data/femboy/outfits.json
tools/gen-assets.cjs    — генератор всех JSON и PNG
tools/check-project.cjs — сквозная проверка ассетов и кода
```

## Проверки

```bash
multitool-node tools/check-project.cjs   # ассеты, ссылки, версии
gradlew build                            # компиляция и jar
```

`check-project.cjs` сверяет предметы ↔ модели ↔ иконки ↔ рецепты ↔ lang ↔ теги ↔ комплекты и размеры PNG.

## Лицензия

MIT.
