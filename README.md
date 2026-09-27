# Java Project — Сборка и жизненный цикл (Maven)

## Описание

Проект собирается с помощью **Apache Maven**. Ниже описаны фазы жизненного цикла сборки и инструменты (плагины), которые их выполняют.

## Требования

- JDK 17+
- Maven 3.9+

## Команды сборки

```bash
mvn clean              # очистка target/
mvn compile            # компиляция
mvn test               # тесты
mvn package            # сборка JAR/WAR
mvn verify             # интеграционные тесты + проверки
mvn install            # установка в локальный репозиторий
mvn deploy             # публикация в удалённый репозиторий
```

## Фазы сборки и инструменты

| Фаза       | Что делает                                              | Инструмент / плагин                          |
|------------|---------------------------------------------------------|----------------------------------------------|
| `validate` | Проверяет корректность проекта и POM                    | Maven Core                                   |
| `compile`  | Компилирует `src/main/java` → `target/classes`          | `maven-compiler-plugin`                      |
| `test`     | Компилирует и запускает unit-тесты (`src/test/java`)    | `maven-surefire-plugin`                      |
| `package`  | Упаковывает `.class` в JAR / WAR                        | `maven-jar-plugin`, `maven-war-plugin`       |
| `verify`   | Интеграционные тесты, проверки качества, покрытие       | `maven-failsafe-plugin`, `jacoco-maven-plugin` |
| `install`  | Кладёт артефакт в локальный репозиторий `~/.m2`         | `maven-install-plugin`                       |
| `deploy`   | Публикует артефакт в удалённый репозиторий (Nexus)      | `maven-deploy-plugin`                        |

## Артефакты сборки

После `mvn package` в `target/` появляются:

- `app-1.0.0.jar` — основной бинарный артефакт
- `app-1.0.0-sources.jar` — исходники (если подключён `maven-source-plugin`)
- `app-1.0.0-javadoc.jar` — документация (если подключён `maven-javadoc-plugin`)
- `surefire-reports/` — отчёты о тестах
- `site/jacoco/` — отчёт о покрытии кода

## Управление зависимостями

Зависимости объявляются декларативно в `pom.xml`:

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
```

- **Транзитивные зависимости** подтягиваются автоматически.
- **Конфликты версий** разрешаются через *dependency mediation* (побеждает ближайшая к корню версия; можно зафиксировать через `<dependencyManagement>`).

## Репозитории

- Локальный кэш: `~/.m2/repository`
- Центральный: Maven Central
- Приватный: Nexus / Artifactory (указывается в `<distributionManagement>`)

# Java Project — Сборка и жизненный цикл (Gradle)

## Описание

Проект собирается с помощью **Gradle**. Жизненный цикл представлен задачами (tasks), которые объединяются в фазы.

## Команды сборки

```bash
./gradlew clean        # очистка build/
./gradlew compileJava  # компиляция
./gradlew test         # тесты
./gradlew jar          # сборка JAR
./gradlew build        # полная сборка (compile + test + jar + check)
./gradlew publish      # публикация в репозиторий
```

## Фазы сборки и инструменты

| Фаза              | Что делает                                       | Задача / плагин                          |
|-------------------|--------------------------------------------------|------------------------------------------|
| `compileJava`     | Компилирует `src/main/java`                      | Java Plugin                              |
| `compileTestJava` | Компилирует тесты                                 | Java Plugin                              |
| `test`            | Запускает unit-тесты (JUnit/TestNG)              | `Test` task, JUnit Platform              |
| `jar` / `war`     | Упаковывает `.class` в JAR/WAR                   | Java Plugin / War Plugin                 |
| `check`           | Тесты + статический анализ + покрытие            | `JacocoPlugin`, `Checkstyle`, `SpotBugs` |
| `build`           | Полная сборка (assemble + check)                 | Агрегирующая задача                      |
| `publish`         | Публикация в Maven-репозиторий                   | `maven-publish` plugin                   |

## Артефакты сборки

После `./gradlew build` в `build/libs/`:

- `app-1.0.0.jar`
- `app-1.0.0-sources.jar` (если включён `withSourcesJar()`)
- `app-1.0.0-javadoc.jar`
- `build/reports/tests/` — отчёты о тестах
- `build/reports/jacoco/` — покрытие

## Управление зависимостями

```groovy
dependencies {
    implementation 'org.apache.commons:commons-lang3:3.14.0'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
}
```

- **Транзитивные зависимости** подтягиваются автоматически.
- **Конфликты версий** разрешаются стратегией Gradle (по умолчанию — максимальная версия).

## Репозитории

```groovy
repositories {
    mavenCentral()
    maven { url 'https://nexus.company.local/repository/maven-public/' }
}
```

- Локальный кэш: `~/.gradle/caches`
- Центральный: Maven Central
- Приватный: Nexus / Artifactory