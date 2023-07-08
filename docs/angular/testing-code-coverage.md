# Узнайте, какой объем кода вы тестируете

:date: 17.01.2023

Angular CLI может запускать модульные тесты и создавать отчеты о покрытии кода. Отчеты о покрытии кода покажут вам все части вашей кодовой базы, которые не могут быть должным образом проверены вашими модульными тестами.

!!!note ""

    Если вы хотите поэкспериментировать с приложением, которое описано в этом руководстве, [запустите его в браузере](https://angular.io/generated/live-examples/testing/stackblitz.html) или скачайте и запустите его локально.

Чтобы сгенерировать отчет о покрытии, выполните следующую команду в корне вашего проекта.

```shell
ng test --no-watch --code-coverage
```

После завершения тестов команда создает в проекте новый каталог `/coverage`. Откройте файл `index.html`, чтобы увидеть отчет с вашим исходным кодом и значениями покрытия кода.

Если вы хотите создавать отчеты о покрытии кода при каждом тестировании, установите следующий параметр в файле конфигурации Angular CLI, `angular.json`:

```json
"test": {
  "options": {
    "codeCoverage": true
  }
}
```

## Обеспечение покрытия кода

Проценты покрытия кода позволяют вам оценить, какая часть вашего кода протестирована. Если ваша команда решила установить минимальный объем, который должен быть протестирован, обеспечьте соблюдение этого минимума с помощью Angular CLI.

Например, предположим, вы хотите, чтобы кодовая база имела минимум 80% покрытия кода. Чтобы это обеспечить, откройте файл конфигурации тестовой платформы [Karma](https://karma-runner.github.io), `karma.conf.js`, и добавьте свойство `check` в ключ `coverageReporter:`.

```js
coverageReporter: {
  dir: require('path').join(__dirname, './coverage/<project-name>'),
  subdir: '.',
  reporters: [
    { type: 'html' },
    { type: 'text-summary' }
  ],
  check: {
    global: {
      statements: 80,
      branches: 80,
      functions: 80,
      lines: 80
    }
  }
}
```

!!!note ""

    Подробнее о создании и тонкой настройке конфигурации Karma читайте в [руководстве по тестированию](testing.md#configuration).

Свойство `check` заставляет инструмент обеспечивать покрытие кода не менее 80% при выполнении модульных тестов в проекте.

Подробнее о параметрах конфигурации покрытия читайте в [документации по покрытию кармы](https://github.com/karma-runner/karma-coverage/blob/master/docs/configuration.md).