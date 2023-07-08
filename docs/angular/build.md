# Создание и обслуживание приложений Angular

На этой странице обсуждаются специфические для сборки параметры конфигурации для проектов Angular.

<a id="app-environments"></a>

## Конфигурирование окружений приложений

Вы можете определить различные именованные конфигурации сборки для вашего проекта, такие как `development` и `staging`, с различными значениями по умолчанию.

Каждая именованная конфигурация может иметь значения по умолчанию для любых опций, которые применяются к различным [builder targets](guide/glossary#target), таким как `build`, `serve` и `test`. Команды [Angular CLI](cli) `build`, `serve` и `test` могут затем заменить файлы на соответствующие версии для вашего целевого окружения.

### Настройка параметров по умолчанию для конкретного окружения

Используя Angular CLI, начните с выполнения команды [generate environments](cli/generate#environments-command), показанной здесь, чтобы создать каталог `src/environments/` и настроить проект на использование этих файлов.

<code-example format="shell" language="shell">

ng генерировать среду

</code-example>

Каталог `src/environments/` проекта содержит базовый конфигурационный файл `environment.ts`, который обеспечивает конфигурацию для `production`, среды по умолчанию. Вы можете переопределить значения по умолчанию для дополнительных окружений, таких как `development` и `staging`, в конфигурационных файлах для конкретной цели.

Например:

<div class="filetree">     <div class="file">
        myProject/src/environments
    </div>
    <div class="children">
        <div class="file">
          environment.ts
        </div>
        <div class="file">
          environment.development.ts
        </div>
        <div class="file">
          environment.staging.ts
        </div>
    </div>
</div>

Базовый файл `environment.ts` содержит настройки окружения по умолчанию. Например:

<code-example format="typescript" language="typescript">

export const environment = { production: true
};

</code-example>

Команда `build` использует его в качестве цели сборки, если окружение не указано. Вы можете добавить дополнительные переменные, либо как дополнительные свойства объекта окружения, либо как отдельные объекты.
Например, следующее добавляет значение по умолчанию для переменной в среду по умолчанию:

<code-example format="typescript" language="typescript">

export const environment = { production: true,
apiUrl: 'http://my-prod-url'

};

</code-example>

Вы можете добавить файлы конфигурации, специфичные для конкретной цели, такие как `environment.development.ts`. Следующее содержимое устанавливает значения по умолчанию для цели сборки разработки:

<code-example format="typescript" language="typescript">

export const environment = { production: false,
apiUrl: 'http://my-api-url'

};

</code-example>

### Использование переменных, специфичных для среды, в вашем приложении

Следующая структура приложения настраивает цели сборки для сред `development` и `staging`:

<div class="filetree">     <div class="file">
        src
    </div>
    <div class="children">
        <div class="file">
          app
        </div>
        <div class="children">
            <div class="file">
              app.component.html
            </div>
            <div class="file">
              app.component.ts
            </div>
        </div>
        <div class="file">
          environments
        </div>
        <div class="children">
            <div class="file">
              environment.ts
            </div>
            <div class="file">
              environment.development.ts
            </div>
            <div class="file">
              environment.staging.ts
            </div>
        </div>
    </div>
</div>

Чтобы использовать определенные вами конфигурации окружения, ваши компоненты должны импортировать исходный файл окружения:

<code-example format="typescript" language="typescript">

import { environment } from './../environments/environment';

</code-example>

Это гарантирует, что команды build и serve смогут найти конфигурации для конкретных целей сборки.

Следующий код в файле компонента \(`app.component.ts`\) использует переменную окружения, определенную в конфигурационных файлах.

<code-example format="typescript" language="typescript">

import { Component } from '&commat;angular/core'; import { environment } from './../environments/environment';

&commat;Component({ selector: 'app-root',

templateUrl: './app.component.html',

styleUrls: ['./app.component.css']

})

export class AppComponent {

constructor() {

console.log(environment.production); // Записывает false для среды разработки

}

    title = 'app works!';

}

</code-example>

<a id="file-replacement"></a>

## Настройка замены файлов для конкретной цели

Основной файл конфигурации CLI, `angular.json`, содержит секцию `fileReplacements` в конфигурации для каждой цели сборки, которая позволяет вам заменить любой файл в программе TypeScript на версию этого файла для конкретной цели. Это полезно для включения специфичного для конкретной цели кода или переменных в сборку, предназначенную для конкретной среды, такой как production или staging.

По умолчанию никакие файлы не заменяются. Вы можете добавить замену файлов для определенных целей сборки.

Например:

<code-example format="json" language="json">

"конфигурации": { "development": {
"fileReplacesments": [

{

"replace": "src/environments/environment.ts",

"с": "src/environments/environment.development.ts"

}

],

&hellip;

</code-example>

Это означает, что когда вы собираете конфигурацию разработки с помощью команды `ng build --configuration development`, файл `src/environments/environment.ts` заменяется на версию файла `src/environments/environment.development.ts` для конкретной цели.

При необходимости вы можете добавить дополнительные конфигурации. Чтобы добавить окружение staging, создайте копию `src/environments/environment.ts` под названием `src/environments/environment.staging.ts`, затем добавьте конфигурацию `staging` в `angular.json`:

<code-example format="json" language="json">

"конфигурации": { "development": { &hellip; },
"production": { &hellip; },

"staging": {

"fileReplacesments": [

{

"replace": "src/environments/environment.ts",

"with": "src/environments/environment.staging.ts"

}

]

}

}

</code-example>

Вы можете добавить дополнительные параметры конфигурации в эту целевую среду. Любая опция, которую поддерживает ваша сборка, может быть переопределена в целевой конфигурации сборки.

Чтобы выполнить сборку с использованием конфигурации staging, выполните следующую команду:

<code-example format="shell" language="shell">

ng build --configuration=staging

</code-example>

Вы также можете настроить команду `serve` на использование целевой конфигурации сборки, если добавите ее в раздел "serve:configurations" в файле `angular.json`:

<code-example format="json" language="json">

"serve": { "builder": "&commat;angular-devkit/build-angular:dev-server",
"options": {

"browserTarget": "your-project-name:build".

},

"configurations": {

"development": {

"browserTarget": "your-project-name:build:development".

},

"production": {

"browserTarget": "your-project-name:build:production"

},

"staging": {

"browserTarget": "your-project-name:build:staging".

}

}

},

</code-example>

<a id="size-budgets"></a> <a id="configure-size-budgets"></a>

## Настройка бюджетов размеров

По мере роста функциональности приложений увеличивается и их размер. CLI позволяет установить пороговые значения размеров в конфигурации, чтобы гарантировать, что части приложения не выйдут за пределы определенных вами границ.

Определите границы размеров в конфигурационном файле CLI, `angular.json`, в разделе `budgets` для каждой [настроенной среды] (#app-environments).

<code-example format="json" language="json">

{ &hellip;
"configurations": {

"production": {

&hellip;

"бюджеты": []

}

}

}

</code-example>

Вы можете задать бюджеты размеров для всего приложения и для отдельных частей. Каждая запись бюджета настраивает бюджет определенного типа.
Укажите значения размеров в следующих форматах:

| Значение размера | Подробности | | :-------------- | :-------------------------------------------------------------------------- |.

| `123` или `123b` | Размер в байтах. |

| `123kb` | Размер в килобайтах. |

| | `123mb` | Размер в мегабайтах. |

| | `12%` | Процент размера относительно базового уровня. \(Недействительно для базовых значений.\)|.

Когда вы настраиваете бюджет, система сборки предупреждает или сообщает об ошибке, когда определенная часть приложения достигает или превышает установленный вами граничный размер.

Каждая запись бюджета представляет собой объект JSON со следующими свойствами:

| Property | Value | | :------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type | The type of budget. One of: <table> <thead> <tr> <th> Value </th> <th> Details </th> </tr> </thead> <tbody> <tr> <td> <code>bundle</code> </td> <td> The size of a specific bundle. </td> </tr> <tr> <td> <code>initial</code> </td> <td> The size of JavaScript needed for bootstrapping the application. Defaults to warning at 500kb and erroring at 1mb. </td> </tr> <tr> <td> <code>allScript</code> </td> <td> The size of all scripts. </td> </tr> <tr> <td> <code>all</code> </td> <td> The size of the entire application. </td> </tr> <tr> <td> <code>anyComponentStyle</code> </td> <td> This size of any one component stylesheet. Defaults to warning at 2kb and erroring at 4kb. </td> </tr> <tr> <td> <code>anyScript</code> </td> <td> The size of any one script. </td> </tr> <tr> <td> <code>any</code> </td> <td> The size of any file. </td> </tr> </tbody> </table> |
| name | The name of the bundle \(for `type=bundle`\). |
| baseline | The baseline size for comparison. |
| maximumWarning | The maximum threshold for warning relative to the baseline. |
| maximumError | The maximum threshold for error relative to the baseline. |
| minimumWarning | The minimum threshold for warning relative to the baseline. |
| minimumError | The minimum threshold for error relative to the baseline. |
| warning | The threshold for warning relative to the baseline \(min &amp max\). |
| error | The threshold for error relative to the baseline \(min &amp max\). |

<a id="commonjs"></a>

## Настройка зависимостей CommonJS

<div class="alert is-important">

Рекомендуется избегать использования модулей CommonJS в приложениях Angular. Зависимость от модулей CommonJS может помешать пакетным и минификаторам оптимизировать ваше приложение, что приведет к увеличению размера пакета.
Вместо этого рекомендуется использовать [ECMAScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import) во всех приложениях.

Для получения дополнительной информации смотрите [Как CommonJS делает ваши пакеты больше](https://web.dev/commonjs-larger-bundles).

</div>

Angular CLI выводит предупреждения, если обнаруживает, что ваше браузерное приложение зависит от модулей CommonJS. Чтобы отключить эти предупреждения, добавьте имя модуля CommonJS в опцию `allowedCommonJsDependencies` в опциях `build`, расположенных в файле `angular.json`.

<code-example language="json">

"build": { "builder": "&commat;angular-devkit/build-angular:browser",
"options": {

"allowedCommonJsDependencies": [

"lodash"

]

&hellip;

}

&hellip;

},

</code-example>

<a id="browser-compat"></a>

## Настройка совместимости с браузерами

Angular CLI использует [Browserslist](https://github.com/browserslist/browserslist) для обеспечения совместимости с различными версиями браузеров. [Autoprefixer](https://github.com/postcss/autoprefixer) используется для префиксации поставщиков CSS и [@babel/preset-env](https://babeljs.io/docs/en/babel-preset-env) для преобразования синтаксиса JavaScript.

Внутри Angular CLI используется приведенная ниже конфигурация `browserslist`, которая соответствует [поддерживаемым браузерам](guide/browser-support) Angular.

<code-example format="none" language="text"> last 2 Chrome versions
last 1 Firefox version
last 2 Edge major versions
last 2 Safari major versions
last 2 iOS major versions
Firefox ESR
</code-example>

Чтобы переопределить внутреннюю конфигурацию, выполните команду [`ng generate config browserslist`](cli/generate#config-command), которая создаст файл конфигурации `.browserslistrc` в каталоге проекта.

Смотрите репозиторий [browserslist repository](https://github.com/browserslist/browserslist) для более подробных примеров того, как нацеливаться на определенные браузеры и версии.

<div class="alert is-helpful">

Используйте [browsersl.ist](https://browsersl.ist) для отображения совместимых браузеров для запроса `browserslist`.

</div>

<a id="proxy"></a>

## Проксирование на внутренний сервер

Используйте [proxying support](https://webpack.js.org/configuration/dev-server/#devserverproxy) в сервере разработки `webpack` для перенаправления определенных URL на внутренний сервер, передавая файл в опцию сборки `--proxy-config`. Например, чтобы перенаправить все обращения к `http://localhost:4200/api` на сервер, работающий на `http://localhost:3000/api`, выполните следующие действия.

1.  Создайте файл `proxy.conf.json` в папке `src/` вашего проекта.

1.  Добавьте следующее содержимое в новый файл proxy:

    <code-example format="json" language="json">.

    {

    "/api": {

    "target": "http://localhost:3000",

    "secure": false

    }

    }

    </code-example>

1.  В конфигурационном файле CLI, `angular.json`, добавьте опцию `proxyConfig` к цели `serve`:

    <code-example format="json" language="json">

    &hellip;

    "architect": {

    "serve": {

    "builder": "&commat;angular-devkit/build-angular:dev-server",

    "options": {

    "browserTarget": "your-application-name:build",

    "proxyConfig": "src/proxy.conf.json"

    },

    &hellip;

    </code-example>

1.  Чтобы запустить сервер разработки с данной конфигурацией прокси, вызовите `ng serve`.

Отредактируйте файл конфигурации прокси, чтобы добавить опции конфигурации; ниже приведены примеры. Описание всех опций приведено в [документации webpack DevServer](https://webpack.js.org/configuration/dev-server/#devserverproxy).

<div class="alert is-helpful">

**NOTE**: <br /> If you edit the proxy configuration file, you must relaunch the `ng serve` process to make your changes effective.

</div>

### Переписать путь к URL

Опция конфигурации прокси `pathRewrite` позволяет вам переписывать путь URL во время выполнения. Например, укажите следующее значение `pathRewrite` в конфигурации прокси, чтобы удалить "api" из конца пути.

<code-example format="json" language="json">

{ "/api": {
"target": "http://localhost:3000",

"secure": false,

"pathRewrite": {

"^/api": ""

}

}

}

</code-example>

Если вам нужно получить доступ к бэкенду, который находится не на `localhost`, установите также параметр `changeOrigin`. Например:

<code-example format="json" language="json">

{ "/api": {
"target": "http://npmjs.org",

"secure": false,

"pathRewrite": {

"^/api": ""

},

"changeOrigin": true

}

}

</code-example>

Чтобы определить, работает ли ваш прокси как положено, установите параметр `logLevel`. Например:

<code-example format="json" language="json">

{ "/api": {
"target": "http://localhost:3000",

"secure": false,

"pathRewrite": {

"^/api": ""

},

"logLevel": "debug"

}

}

</code-example>

Уровни журнала прокси: `info` \(по умолчанию\), `debug`, `warn`, `error` и `silent`.

### Проксирование нескольких записей

Вы можете проксировать несколько записей на одну и ту же цель, определив конфигурацию в JavaScript.

Установите файл конфигурации прокси в `proxy.conf.mjs`\ (вместо `proxy.conf.json`\), и укажите файлы конфигурации, как показано в следующем примере.

<code-example format="javascript" language="javascript">

export default [ {
контекст: [

'/my',

'/many',

'/endpoints',

'/i',

'/need',

'/to',

'/proxy'

],

target: 'http://localhost:3000',

безопасный: false

}

];

</code-example>

В конфигурационном файле CLI, `angular.json`, укажите на файл конфигурации JavaScript-прокси:

<code-example format="json" language="json">

&hellip; "architect": {
"serve": {

"builder": "&commat;angular-devkit/build-angular:dev-server",

"options": {

"browserTarget": "your-application-name:build",

"proxyConfig": "src/proxy.conf.mjs"

},

&hellip;

</code-example>

### Обход прокси

Если вам нужно по желанию обойти прокси или динамически изменить запрос перед отправкой, добавьте опцию обхода, как показано в этом примере JavaScript.

<code-example format="javascript" language="javascript">

export default { '/api/proxy': {
"target": 'http://localhost:3000',

"secure": false,

"bypass": function (req, res, proxyOptions) {

if (req.headers.accept.includes('html')) {

console.log('Пропуск прокси для запроса браузера.');

return '/index.html';

}

req.headers['X-Custom-Header'] = 'yes';

}

}

};

</code-example>

### Использование корпоративного прокси-сервера

Если вы работаете за корпоративным прокси, бэкенд не может напрямую проксировать вызовы на любой URL за пределами вашей локальной сети. В этом случае вы можете настроить внутренний прокси-сервер на перенаправление вызовов через ваш корпоративный прокси-сервер с помощью агента:

<code-example format="shell" language="shell">

npm install --save-dev https-proxy-agent

</code-example>

Когда вы определяете переменную окружения `http_proxy` или `HTTP_PROXY`, автоматически добавляется агент для передачи вызовов через ваш корпоративный прокси при запуске `npm start`.

Используйте следующее содержание в конфигурационном файле JavaScript.

<code-example format="javascript" language="javascript">

import HttpsProxyAgent from 'https-proxy-agent';

const proxyConfig = [{ { context: '/api',

target: 'http://your-remote-server.com:3000',

безопасный: false

}];

export default (proxyConfig) => { const proxyServer = process.env.http_proxy &verbar;&verbar; process.env.HTTP_PROXY;

if (proxyServer) {

const agent = new HttpsProxyAgent(proxyServer);

console.log('Использование корпоративного прокси-сервера: ' + proxyServer);

    for (const entry of proxyConfig) {       entry.agent = agent;
    }

}

return proxyConfig; }

</code-example>

<!-- links -->

<!-- external links -->

<!-- end links -->

:date: 17.01.2023