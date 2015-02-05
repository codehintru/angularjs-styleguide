# Руководство по стилям для AngularJS

*AngularJS соглашения по стилям для команд разработчиков, предложенные [@john_papa](//twitter.com/john_papa)*

Если вам нужны стандарты написания кода, соглашения, и руководства структурирования приложений AngularJS, то вы находитесь в правильном месте. Эти соглашения основаны на моем опыте программирования на [AngularJS](//angularjs.org), на моих презентациях [Pluralsight training courses](http://pluralsight.com/training/Authors/Details/john-papa), а также на совместной работе в командах разработчиков.

Главной целью этого документа является, желание предоставить вам наиболее полные инструкции для построения приложений AngularJS. Рекомендуя данные соглашения, я стараюсь акцентировать ваше внимание, на то зачем их нужно придерживаться. 
>Если данное руководство вам понравится, то вы можете также оценить мой курс [AngularJS Patterns: Clean Code](http://jpapa.me/ngclean), который размещен на сайте Pluralsight.

  [![AngularJs Patterns: Clean Code](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/ng-clean-code-banner.png)](http://jpapa.me/ngclean)
  
## Признательность сообществу и коллегам
Никогда не работайте в вакууме. Я считаю AngularJS-сообщество невероятно открытым, которое активно обменивается опытом и заботиться об этом. Также как и мой друг Todd Motto (отличный AngularJS эксперт), я работал со многими стилями и соглашениями. Мы с ним сходимся во многом, но иногда и противоречим друг другу. Я призываю вас ознакомиться с курсом [Todd's guidelines](https://github.com/toddmotto/angularjs-styleguide) дабы почувствовать разницу подходов.

Многие из моих стилей взяты в ходе моих программистских сессий с [Ward Bell](http://twitter.com/wardbell). А так как мы не всегда были согласны друг с другом, то мой друг Ward оказал очень сильное влияние на окончательную эволюцию этого документа.

## Смотрите стили и шаблоны в приложении-примере
Пока данное руководство объясняет *что*, *почему* и *как*, я сразу же показываю, как это работает на практике. Все что я предлагаю и описываю сопровождается примером-приложения, которое соблюдает и демонстрирует стили и шаблоны. Вы можете найти [пример приложения (имя modular) здесь](https://github.com/johnpapa/ng-demos) в папке `modular`. Свободно скачивайте, клонируйте и перемещайте эти примеры в свои хранилища. [Инструкциии по запуску примеров находятся в файлах readme](https://github.com/johnpapa/ng-demos/tree/master/modular).

## Переводы 
[Переводы данного руководства по Angular-стилям](https://github.com/johnpapa/angularjs-styleguide/tree/master/i18n) поддерживаются соообществом разработчиков и могут быть найдены здесь.

## Содержание

  1. [Single Responsibility](#single-responsibility)
  1. [IIFE](#iife)
  1. [Modules](#modules)
  1. [Controllers](#controllers)
  1. [Services](#services)
  1. [Factories](#factories)
  1. [Data Services](#data-services)
  1. [Directives](#directives)
  1. [Resolving Promises for a Controller](#resolving-promises-for-a-controller)
  1. [Manual Annotating for Dependency Injection](#manual-annotating-for-dependency-injection)
  1. [Minification and Annotation](#minification-and-annotation)
  1. [Exception Handling](#exception-handling)
  1. [Naming](#naming)
  1. [Application Structure LIFT Principle](#application-structure-lift-principle)
  1. [Application Structure](#application-structure)
  1. [Modularity](#modularity)
  1. [Startup Logic](#startup-logic)
  1. [Angular $ Wrapper Services](#angular--wrapper-services)
  1. [Testing](#testing)
  1. [Animations](#animations) 
  1. [Comments](#comments)
  1. [JSHint](#js-hint)
  1. [Constants](#constants)
  1. [File Templates and Snippets](#file-templates-and-snippets)
  1. [AngularJS Docs](#angularjs-docs)
  1. [Contributing](#contributing)
  1. [License](#license)

## Single Responsibility

### Правило 1 
###### [Style [Y001](#style-y001)]

  - Определяйте 1 компонент в одном файле.

  В следующем примере в одном и том же файле определяется модуль(module) `app` вместе с его зависимостями, определяется контроллер(controller), а также сервис(factory).

  ```javascript
  /* избегайте этого */
  angular
      .module('app', ['ngRoute'])
      .controller('SomeController', SomeController)
      .factory('someFactory', someFactory);
    
  function SomeController() { }

  function someFactory() { }
  ```

  А теперь каждый компонент разнесен в свой отдельный файл.

  ```javascript
  /* рекомендовано */

  // app.module.js
  angular
      .module('app', ['ngRoute']);
  ```

  ```javascript
  /* рекомендовано */

  // someController.js
  angular
      .module('app')
      .controller('SomeController', SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* рекомендовано */

  // someFactory.js
  angular
      .module('app')
      .factory('someFactory', someFactory);
    
  function someFactory() { }
  ```

**[К содержанию](#cодержание)**


## IIFE
### Замыкания JavaScript 
###### [Style [Y010](#style-y010)]

  - Оборачивайте компоненты AngularJS в Немедленно Исполняемые Функции(IIFE - Immediately Invoked Function Expression). 

  *Зачем?*: IIFE удаляют переменные из глобальной области видимости. Этот прием не дает существовать переменным и функциям дольше, чем это необходимо в глобальной области видимости. Иначе это может вызвать непредсказуемые коллизии во время исполнения всего приложения.

  *Зачем?*: Когда ваш код будет сжат и упакован (bundled and minified) в один файл для размещения его на рабочем сервере, то коллизий станет намного больше чем их было до минификации. IIFE защитит ваш код обеспечивая область видимости переменных только в немедленно исполняемых функциях(IIFE) которые оборачивают ваш код.

  ```javascript
  /* избегайте этого */
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  // функция logger добавлена как глобальная переменная
  function logger() { }

  // storage.js
  angular
      .module('app')
      .factory('storage', storage);

  // функция storage добавлена как глобальная переменная
  function storage() { }
  ```

  ```javascript
  /**
   * рекомендовано 
   *
   * больше нет глобальных переменных 
   */

  // logger.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('logger', logger);

      function logger() { }
  })();

  // storage.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('storage', storage);

      function storage() { }
  })();
  ```

  - Замечание: Только для краткости, в остальных примерах мы не будем прописывать синтаксис с функциями IIFE. 

  - Замечание: IIFE не дает тестировать приватный код, так как предотвращает доступ к нему, например, регулярные выражения или вспомогательные функции, которые нужно оттестировать в модульных тестах (unit test) напрямую. Как выход вы можете тестировать через специальные методы используемые только в тестах (accessible members) или выставить нужные внутренние функции в собственном компоненте. Например, поместите вспомогательные функции, регулярные выражения или константы в собственную фабрику(factory) или константу(constant).

**[Back to top](#table-of-contents)**

## Модули

### Избегайте коллизий имен
###### [Style [Y020](#style-y020)]

  - Используйте конвенцию уникальных имен с разделителями для подмодулей.

  *Почему?*: Уникальные имена помогают избежать коллизий в именах модулей. Разделители определяет сам модуль и его подмодульную иерархию. Например, `app` может быть вашим корневым модулем, а модули `app.dashboard` и `app.users` могут использоваться как имена модулей, зависимые от `app`.

### Определения (они же Сеттеры(Setters))
###### [Style [Y021](#style-y021)]

  - Объявляйте модули без переменных, используйте сеттеры (setters). 

  *Почему?*: Так как обычно в файле 1 компонент, поэтому едва ли потребуется вводить переменную для модуля.
  
  ```javascript
  /* избегайте этого */
  var app = angular.module('app', [
      'ngAnimate',
      'ngRoute',
      'app.shared',
      'app.dashboard'
  ]);
  ```

  Используйте простой синтаксис сеттера.

  ```javascript
  /* рекомендовано */
  angular
      .module('app', [
          'ngAnimate',
          'ngRoute',
          'app.shared',
          'app.dashboard'
      ]);
  ```

### Геттеры (Getters)
###### [Style [Y022](#style-y022)]

  - Когда нужно использовать модуль, избегайте использования переменных а используйте вместо этого цепочку геттеров.

  *Почему?*: Так вы производите более читаемый код, а также избегаете утечек и коллизий переменных.

  ```javascript
  /* избегайте этого */
  var app = angular.module('app');
  app.controller('SomeController', SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* рекомендовано */
  angular
      .module('app')
      .controller('SomeController', SomeController);

  function SomeController() { }
  ```

### Определение и получение модулей
###### [Style [Y023](#style-y023)]

  -  Определите один раз и получайте во всех других сущностях.

  *Почему?*: Модуль должен быть определен только один раз, а потом его используйте во всех остальных местах.

    - Используйте `angular.module('app', []);` для определения модуля.
    - Используйте `angular.module('app');` чтобы получить модуль. 

### Именованные или Анонимные Функции
###### [Style [Y024](#style-y024)]

  - Используйте именованные функции, не передавайте анонимные функции обратного вызова в качестве параметров. 

  *Почему?*: Так вы производите более читаемый код, его легче отлаживать, и это уменьшает число вложенных функций обратного вызова.

  ```javascript
  /* избегайте этого */
  angular
      .module('app')
      .controller('Dashboard', function() { })
      .factory('logger', function() { });
  ```

  ```javascript
  /* рекомендовано */

  // dashboard.js
  angular
      .module('app')
      .controller('Dashboard', Dashboard);

  function Dashboard() { }
  ```

  ```javascript
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  function logger() { }
  ```

**[Back to top](#table-of-contents)**

## Контроллеры

### Синтаксис controllerAs в представлении
###### [Style [Y030](#style-y030)]

  - Используйте синтаксис [`controllerAs`](http://www.johnpapa.net/do-you-like-your-angular-controllers-with-or-without-sugar/), который работает поверх синтаксиса `классический контроллер со $scope`. 

  *Почему?*: Контроллер создается, также как с ключевым словом "new" и затем предоставляется единственный экземпляр объекта, то есть синтаксис `controllerAs` намного ближе и похожее на конструктор языка JavaScript, чем `классический синтаксис $scope`.

  *Почему?*: Это позволяет использовать в представлении связывание на свойство объекта "через точку" (например вместо `name` будет `customer.name`), что является более концептуальным, проще для чтения, и помогает избегать многих ссылочных проблем, которые могут возникнуть без использования "точки".

  *Почему?*: Помогает избежать использование вызовов `$parent` в представлениях с вложенными контроллерами.

  ```html
  <!-- избегайте этого -->
  <div ng-controller="Customer">
      {{ name }}
  </div>
  ```

  ```html
  <!-- рекомендовано -->
  <div ng-controller="Customer as customer">
      {{ customer.name }}
  </div>
  ```

### Синтаксис controllerAs в контроллере
###### [Style [Y031](#style-y031)]

  - Используйте синтаксис `controllerAs` поверх синтаксиса `классический контроллер со $scope`. 

  - Синтаксис `controllerAs` использует внутри контроллеров ключевую переменную `this`, которая привязывается к `$scope`.

  *Почему?*: `controllerAs` это только синтаксический сахар поверх `$scope`. Вы все равно можете использовать связывание в представлениях и все равно имеете доступ к методам `$scope`. 

  *Почему?*: Избавляет от искушения использования методов `$scope` внутри контроллера, когда это не требуется явно, и это позволяет перенести методы в фабрику(factory). Предпочтительнее использовать `$scope` в фабрике, в контроллере же только если это необходимо. Например, когда мы подписываемся и рассылаем события с помощью [`$emit`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$emit), [`$broadcast`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$broadcast), or [`$on`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$on) перенесите эти сервисы в фабрику, и вызываете методы фабрики в контроллере.

  ```javascript
  /* избегайте этого */
  function Customer($scope) {
      $scope.name = {};
      $scope.sendMessage = function() { };
  }
  ```

  ```javascript
  /* рекомендовано - но изучите следующую секцию */
  function Customer() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

### Синтаксис controllerAs с переменной vm
###### [Style [Y032](#style-y032)]

  - Сохраните `this` в переменную, когда используете синтаксис `controllerAs`. Выберите постоянное имя для переменной, такое как `vm`, что будет значить ViewModel.
  
  *Почему?*: Ключевое слово `this` контекстное, и когда его потребуется использовать внутри любой другой функции контроллера, то его содержимое будет другим. Сохранение `this` в переменной `vm` позволит избежать этих проблем.

  ```javascript
  /* избегайте этого */
  function Customer() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

  ```javascript
  /* рекомендовано */
  function Customer() {
      var vm = this;
      vm.name = {};
      vm.sendMessage = function() { };
  }
  ```

  Замечание: Вы можете избежать любые [jshint](http://www.jshint.com/) предупреждения, если разместите над строкой кода комментарий, приведенный ниже в примере. Это не требуется, если функция названа с помощью ВерхнегоРегистра(UpperCasing), так как согласно этой конвециии, это значит, что функция является контруктором контроллера Angular.

  ```javascript
  /* jshint validthis: true */
  var vm = this;
  ```

  Замечание: Когда создаете наблюдающие функции(watcher) в контроллерах типа `controller as`, вы можете наблюдать за переменной следующего вида `vm.*`. (Создавайте наблюдающие функции с предупреждением, что они создают дополнительную нагрузку на цикл digest.)

  ```html
  <input ng-model="vm.title"/>
  ```

  ```javascript
  function SomeController($scope, $log) {
      var vm = this;
      vm.title = 'Some Title';

      $scope.$watch('vm.title', function(current, original) {
          $log.info('vm.title was %s', original);
          $log.info('vm.title is now %s', current);
      });
  }
  ```

### Привязываемые Члены Сверху
###### [Style [Y033](#style-y033)]

  - Помещайте привязываемые члены в верхней части контроллера, в алфавитном порядке, и не раскидывайте их в коде контроллера где попало.

    *Почему?*: Размещение привязываемых членов наверху упрощает чтение и позволяет мгновенно определить какие члены контроллера привязаны и используются в представлении.

    *Почему?*: Написание анонимных функций по месту использования может конечно и проще, но когда такие функции содержат много строк кода, то это значительно снижает читабельность кода. Определяйте функции ниже привязываемых членов, тем самым вы перенесете детали реализации вниз отдельно. Функции определяйте как hoisted, то есть они будут подняты наверх области видимости. А привязываемые члены наверху повысят читабельность кода. 

  ```javascript
  /* избегайте этого */
  function Sessions() {
      var vm = this;

      vm.gotoSession = function() {
        /* ... */
      };
      vm.refresh = function() {
        /* ... */
      };
      vm.search = function() {
        /* ... */
      };
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

  ```javascript
  /* рекомендовано */
  function Sessions() {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = refresh;
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';

      ////////////

      function gotoSession() {
        /* */
      }

      function refresh() {
        /* */
      }

      function search() {
        /* */
      }
  ```

    ![Controller Using "Above the Fold"](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/above-the-fold-1.png)

  Замечание: Если функция однострочная, то разместите ее и наверху, но так чтобы это не усложняло читабельность.

  ```javascript
  /* избегайте этого */
  function Sessions(data) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = function() {
          /**
           * эти  
           * строки  
           * кода
           * ухудшают
           * читабельность
           */
      };
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

  ```javascript
  /* рекомендовано */
  function Sessions(dataservice) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = dataservice.refresh; // одна строка, все OK
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

### Определения Функций Для Скрытия Деталей Реализации
###### [Style [Y034](#style-y034)]

  - Используйте определения функций для скрытия деталей реализации. Держите свои привязываемые члены наверху. Если нужно в контроллере сделать функцию привязываемой, укажите это в группе привязываемых членов и ссылайтесь на данную функцию, которая реализована ниже. Это подробно описано в секции Привязываемые Члены Сверху. Подробнее смотрите  [здесь](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).

    *Почему?*: Размещение привязываемых членов наверху делает код читабельнее, и позволяет мгновенно определить какие члены привязаны и используются в представлении. (Выше описано тоже самое.)  

    *Почему?*: Размещение деталей реализации функции внизу скрывает эту сложность ниже и таким образом все важные вещи находятся на видном месте сверху.

    *Почему?*: Функции определены как hoisted (определены в самом верху области видимости), поэтому не надо заботиться об их использовании перед объявлением, так как это было бы с объявлениями выражений функций (function expressions).

    *Почему?*: Вам не надо волноваться, о том в каком порядке объявлены функции. Также как и изменение порядка функций не будет ломать код из-за зависимостей. 

    *Почему?*: С выражениями функций(function expressions) порядок будет критичен.

  ```javascript
  /** 
   * избегайте этого 
   * Использование выражений функций (function expressions).
   */
  function Avengers(dataservice, logger) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      var activate = function() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      var getAvengers = function() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }

      vm.getAvengers = getAvengers;

      activate();
  }
  ```

  Заметьте, в предыдущем примере важный материал размещен в контроллере в разных местах. А в примере ниже, все важные моменты размещены сверху, в нашем случае, это члены, привязанные к контроллеру такие как `vm.avengers` и `vm.title`. Подробности реализации смещены вниз. Такой код проще читать.

  ```javascript
  /*
   * рекомендовано
   * Объявления функций и
   * привязанные члены смещены вверх.
   */
  function Avengers(dataservice, logger) {
      var vm = this;
      vm.avengers = [];
      vm.getAvengers = getAvengers;
      vm.title = 'Avengers';

      activate();

      function activate() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      function getAvengers() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Логика Контроллера Отдельно
###### [Style [Y035](#style-y035)]

  - Переносите логику контроллера в сервисы и фабрики.

    *Почему?*: Логика может использоваться несколькими контроллерами, если она помещена в сервис и выставлена в виде функции.

    *Почему?*: Вся логика в сервисе может быть легко изолирована в модульном тесте, а вызовы этой логики в контроллере могут фиктивно реализованы (mocked).

    *Почему?*:  Из контроллера удаляются зависимости и скрываются подробности реализации.

  ```javascript

  /* избегайте этого */
  function Order($http, $q, config, userInfo) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.isCreditOk;
      vm.total = 0;

      function checkCredit() {
          var settings = {};
          // Get the credit service base URL from config
          // Set credit service required headers
          // Prepare URL query string or data object with request data
          // Add user-identifying info so service gets the right credit limit for this user.
          // Use JSONP for this browser if it doesn't support CORS
          return $http.get(settings)
              .then(function(data) {
               // Unpack JSON data in the response object
                 // to find maxRemainingAmount
                 vm.isCreditOk = vm.total <= maxRemainingAmount
              })
              .catch(function(error) {
                 // Interpret error
                 // Cope w/ timeout? retry? try alternate service?
                 // Re-reject with appropriate error for a user to see
              });
      };
  }
  ```

  ```javascript
  /* рекомендовано */
  function Order(creditService) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.isCreditOk;
      vm.total = 0;

      function checkCredit() { 
         return creditService.isOrderTotalOk(vm.total)
      .then(function(isOk) { vm.isCreditOk = isOk; })
            .catch(showServiceError);
      };
  }
  ```

### Один Контроллер - Одно Представление
###### [Style [Y037](#style-y037)]

  - Определяйте контроллер для одного представления, и не пытайтесь использовать этот контроллер для других представлений. Вместо этого, выносите повторно используемую логику в фабрики. Старайтесь держать контроллер простым и сфокусированным только на свое представление.

    *Почему?*: Использование контроллера с несколькими представлениями хрупко и ненадежно. А для больших приложений  требуется хорошее покрытие тестами end to end (e2e) для проверки стабильности. 

### Присваивание Контроллеров
###### [Style [Y038](#style-y038)]

  - Когда контроллер и его представление уже создано, и если нужно что-то повторно использовать (контроллер и представление), определяйте экземпляр контроллера вместе с его маршрутом (route).

    Замечание: Если представление загружается не через маршрут, тогда используйте синтаксис `ng-controller="Avengers as vm"`.

    *Почему?*: Указание экземпляра контроллера в определении маршрута позволяет различным маршрутам вызывать различные пары контроллеров и представлений. А когда контроллеры указаны в представлении с помощью [`ng-controller`](https://docs.angularjs.org/api/ng/directive/ngController), то представление будет всегда ассоциировано с одним и тем же контроллером. 

 ```javascript
  /* избегайте этого - когда используется маршрут и необходимо динамическое назначение контроллера и представления */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
            templateUrl: 'avengers.html'
          });
  }
  ```

  ```html
  <!-- avengers.html -->
  <div ng-controller="Avengers as vm">
  </div>
  ```

  ```javascript
  /* рекомендовано */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'Avengers',
              controllerAs: 'vm'
          });
  }
  ```

  ```html
  <!-- avengers.html -->
  <div>
  </div>
  ```

**[Back to top](#table-of-contents)**

## Сервисы

### Синглтоны
###### [Style [Y040](#style-y040)]

  - Сервисы создаются с помощью ключевого слова `new`, используйте `this` для публичных методов и переменных. Так как они очень похожи на фабрики, то используйте фабрики для согласованности.

    Замечание: [Все AngularJS сервисы являются синглтонами](https://docs.angularjs.org/guide/services). Это значит, что создается только один экземпляр сервиса на один инжектор.

  ```javascript
  // service
  angular
      .module('app')
      .service('logger', logger);

  function logger() {
    this.logError = function(msg) {
      /* */
    };
  }
  ```

  ```javascript
  // factory
  angular
      .module('app')
      .factory('logger', logger);

  function logger() {
      return {
          logError: function(msg) {
            /* */
          }
     };
  }
  ```

**[Back to top](#table-of-contents)**

## Фабрики

### Единственная Обязанность (Single Responsibility)
###### [Style [Y050](#style-y050)]

  - Фабрики дожны иметь [единственную обязанность](http://en.wikipedia.org/wiki/Single_responsibility_principle), это следует из их контекста. Если фабрика начинает превышать ту цель для которой она создана, то нужно создать другую фабрику. 

### Синглтон
###### [Style [Y051](#style-y051)]

  - Фабрики это синглтоны, которые возвращают объект, содержащий свойства и методы сервиса.

    Замечание: [Все AngularJS сервисы являются синглтонами](https://docs.angularjs.org/guide/services).

### Доступные Члены Наверх
###### [Style [Y052](#style-y052)]

  - Помещайте вызываемые члены сервиса (интерфейс) наверх, используя технику  [Revealing Module Pattern](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript). 

    *Почему?*: Размещение вызываемых членов в верхней части улучшает читабельность и дает вам возможность быстро определить какие члены сервиса могут быть вызваны и должны быть модульно оттестированы (либо фиктивно имплементированы - mocked).   
    
    *Почему?*: Это особенно полезно, когда файл становится очень длинным, и вынуждает прокручивать текст кода, чтобы посмотреть, какие методы или свойства сервис предоставляет.

    *Почему?*: Размещение функций в обычном прямом порядке может быть и проще, но когда эти функции становятся многострочными, это снижает читабельность и вынуждает много скроллить. Определяя вызываемый интерфейс(в виде возвращаемого сервиса), вы убираете детали реализации вниз. А размещенный сверху интерфейс улучшает читабельность. 

  ```javascript
  /* избегайте этого */
  function dataService() {
    var someValue = '';
    function save() { 
      /* */
    };
    function validate() { 
      /* */
    };

    return {
        save: save,
        someValue: someValue,
        validate: validate
    };
  }
  ```

  ```javascript
  /* рекомендовано */
  function dataService() {
      var someValue = '';
      var service = {
          save: save,
          someValue: someValue,
          validate: validate
      };
      return service;

      ////////////

      function save() { 
          /* */
      };

      function validate() { 
          /* */
      };
  }
  ```

  This way bindings are mirrored across the host object, primitive values cannot update alone using the revealing module pattern

    ![Factories Using "Above the Fold"](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/above-the-fold-2.png)

### Определения Функций для Скрытия Деталей Реализации
###### [Style [Y053](#style-y053)]

  - Используйте определения функций, чтобы скрыть детали реализации. Держите вызываемые члены фабрики в верхней части. Свяжите их с определениями функций, которые распололжены ниже в файле. Подробную информацию смотрите [здесь](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).

    *Почему?*: Размещение вызываемых членов в верхней части улучшает читабельность и помогает быстро определить какие функции фабрики могут быть вызваны извне. 

    *Почему?*: Размещение функций с деталями реализации в нижней части файла выносит сложные вещи из поля зрения, так что только важные детали вы видите в верхней части файла.

    *Почему?*: Функции определены в самом верху области видимости (hoisted), то есть их можно вызывать до их определения. (что было бы не возможно с выражениями функций)

    *Почему?*: Вам не надо беспокоится о порядке определений функций. Перестановка зависимых друг от друга функций не ломает код.

    *Why?*: А для выражений функций порядок критичен.

  ```javascript
  /**
   * избегайте этого
   * Использование выражений функций
   */
   function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var getAvengers = function() {
          // детали реализации здесь
      };

      var getAvengerCount = function() {
          // детали реализации здесь
      };

      var getAvengersCast = function() {
         // детали реализации здесь
      };

      var prime = function() {
         // детали реализации здесь
      };

      var ready = function(nextPromises) {
          // детали реализации здесь
      };

      var service = {
          getAvengersCast: getAvengersCast,
          getAvengerCount: getAvengerCount,
          getAvengers: getAvengers,
          ready: ready
      };

      return service;
  }
  ```

  ```javascript
  /**
   * рекомендовано
   * Использование определений функций
   * и вызываемые члены расположены в верхней части.
   */
  function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var service = {
          getAvengersCast: getAvengersCast,
          getAvengerCount: getAvengerCount,
          getAvengers: getAvengers,
          ready: ready
      };

      return service;

      ////////////

      function getAvengers() {
          // детали реализации здесь
      }

      function getAvengerCount() {
          // детали реализации здесь
      }

      function getAvengersCast() {
          // детали реализации здесь
      }

      function prime() {
          // детали реализации здесь
      }

      function ready(nextPromises) {
          // детали реализации здесь
      }
  }
  ```

**[Back to top](#table-of-contents)**

## Сервисы данных

### Отделите вызовы данных
###### [Style [Y060](#style-y060)]

  - Делайте рефакторинг логики работы с данными и взаимодействия этих данных с фабрикой. Создавайте сервисы данных ответственных за вызовы XHR, локальное хранилище(local storage), работу с памятью или за любые другие операции с данными.  

    *Почему?*: Ответственность контроллера - это предоставление или сбор информации для представления. Он не должен заботиться о том, как работать с данными, он только должен знать кого попросить об этом. В сервисы для данных переносится вся логика работы с данными. Это делает контроллер более простым и более сфокусированным для работы с представлением.

    *Почему?*: Это позволяет более проще тестировать (фиктивно или реально) вызовы данных в контроллере, который использует сервис для данных. 

    *Почему?*: Реализация сервиса данных может иметь очень специфичный код для операций с хранилищем данных. Могут использоваться заголовки для взаимодействовия с данными, или другие сервисы типа $http. Логика в сервисе данных инкапсулируется в одном месте и скрывает реализацию для внешних потребителей (контроллеров), также будет гораздо проще изменить реализацию в случае необходимости.

  ```javascript
  /* рекомендовано */

  // фабрика сервиса данных
  angular
      .module('app.core')
      .factory('dataservice', dataservice);

  dataservice.$inject = ['$http', 'logger'];

  function dataservice($http, logger) {
      return {
          getAvengers: getAvengers
      };

      function getAvengers() {
          return $http.get('/api/maa')
              .then(getAvengersComplete)
              .catch(getAvengersFailed);

          function getAvengersComplete(response) {
              return response.data.results;
          }

          function getAvengersFailed(error) {
              logger.error('XHR Failed for getAvengers.' + error.data);
          }
      }
  }
  ```
    
    Замечание: Сервис данных вызывается потребителем (контроллером), и скрывает реализацию от потребителя. Это показано ниже.

  ```javascript
  /* рекомендовано */

  // контроллер вызывает фабрику сервиса данных
  angular
      .module('app.avengers')
      .controller('Avengers', Avengers);

  Avengers.$inject = ['dataservice', 'logger'];

  function Avengers(dataservice, logger) {
      var vm = this;
      vm.avengers = [];

      activate();

      function activate() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      function getAvengers() {
          return dataservice.getAvengers()
              .then(function(data) {
                  vm.avengers = data;
                  return vm.avengers;
              });
      }
  }      
  ```

### Возвращайте Promise при Запросах Данных
###### [Style [Y061](#style-y061)]

  - Если сервис данных возвращает promise типа $http, то в вызывающей функции возвращайте promise тоже.

    *Почему?*: Вы можете объединить в цепочку объекты promise, и после того как вызов данных закончится, выполнить дальнейшие действия принятия или отклонения объекта promise.  

  ```javascript
  /* рекомендовано */

  activate();

  function activate() {
      /**
       * Шаг 1
       * Запрашиваем функцию getAvengers function 
       * для получения данных и ждем promise 
       */
      return getAvengers().then(function() {
          /**
           * Шаг 4
           * Выполняем действие принятия финального объекта promise
           */
          logger.info('Activated Avengers View');
      });
  }

  function getAvengers() {
        /**
         * Шаг 2
         * Запрашиваем сервис для данных 
         * и ждем promise
         */
        return dataservice.getAvengers()
            .then(function(data) {
                /**
                 * Шаг 3
                 * инициализируем данные и принимаем promise
                 */
                vm.avengers = data;
                return vm.avengers;
        });
  }
  ```

    **[Back to top](#table-of-contents)**

## Директивы
### Одна Директива - Один Файл  
###### [Style [Y070](#style-y070)]

  - Создавайте только одну директиву в файле. Называйте файл именем директивы.

    *Почему?*: Конечно можно поместить директивы в один файл. Но потом их трудно будет разделить по приложениям, и по модулям. Например, нужна будет только одна из директив для определенного модуля.

    *Почему?*: Одну директиву в файле легче поддерживать.

  ```javascript
  /* избегайте этого */
  /* directives.js */

  angular
      .module('app.widgets')

      /* директива для заказа, которая специфична для модуля заказов */
      .directive('orderCalendarRange', orderCalendarRange)

      /* директива продажи, которая может быть использована везде в модуле продаж */
      .directive('salesCustomerInfo', salesCustomerInfo)

      /* директива крутилки (spinner), которая может быть использована во всех модулях */
      .directive('sharedSpinner', sharedSpinner);

  function orderCalendarRange() {
      /* детали реализации */
  }

  function salesCustomerInfo() {
      /* детали реализации */
  }

  function sharedSpinner() {
      /* детали реализации */
  }
  ```

  ```javascript
  /* рекомендовано */
  /* calendarRange.directive.js */

  /**
   * @desc директива заказа, которая специфична модулю заказов в компании Acme
   * @example <div acme-order-calendar-range></div>
   */
  angular
      .module('sales.order')
      .directive('acmeOrderCalendarRange', orderCalendarRange);

  function orderCalendarRange() {
      /* детали реализации */
  }
  ```

  ```javascript
  /* рекомендовано */
  /* customerInfo.directive.js */

  /**
   * @desc директива продажи, которая может быть использована везде в модуле продаж компании Acme
   * @example <div acme-sales-customer-info></div>
   */    
  angular
      .module('sales.widgets')
      .directive('acmeSalesCustomerInfo', salesCustomerInfo);

  function salesCustomerInfo() {
      /* implementation details */
  }
  ```

  ```javascript
  /* рекомендовано */
  /* spinner.directive.js */

  /**
   * @desc директива крутилки (spinner), которая может быть использована во всех модулях компании Acme
   * @example <div acme-shared-spinner></div>
   */
  angular
      .module('shared.widgets')
      .directive('acmeSharedSpinner', sharedSpinner);

  function sharedSpinner() {
      /* детали реализации */
  }
  ```

    Замечание: Существует много способов для именований директив, особенно это зависит от широты области использования (локально или глобально). Выбирайте тот способ, который определяет директиву и ее файл четко и ясно. Нексолько примеров будет ниже, но в основном смотрите рекомендации в секции именований. 

### Манипулирование Элементами DOM в Директиве
###### [Style [Y072](#style-y072)]

  - Используйте директивы, если нужно манипулировать элементами DOM напрямую. Но если существуют альтернативные способы, то используйте лучше их. Например для изменения стилей используйте CSS, для эффектов [сервисы анимации](https://docs.angularjs.org/api/ngAnimate), для управления видимостью используйте [`ngShow`](https://docs.angularjs.org/api/ng/directive/ngShow) и [`ngHide`](https://docs.angularjs.org/api/ng/directive/ngHide). 

    *Почему?*: Манипулирование элементами DOM тяжело тестить, отлаживать, и зачастую существуют более лучшие способы для реализации поставленной задачи (например CSS, анимации, шаблоны)

### Добавляйте Директивам Уникальный Префикс
###### [Style [Y073](#style-y073)]

  - Добавляйте директивам короткий, уникальный, пояснительный префикс, такой как `acmeSalesCustomerInfo`, которая будет объявлена в HTML как `acme-sales-customer-info`.

    *Почему?*: Уникальный короткий префикс говорит о контексте и происхождении директивы. Например, префикс `cc-` может рассказать нам, что директива является частью приложения CodeCamper, а `acme-` это директива для компании Acme. 

    Замечание: Не используйте префикс `ng-` для своих директив, так как он зарезервирован для директив AngularJS. Также исследуйте префиксы широко используемых директив для избежания конфликтов имен, например префикс `ion-` используется для директив [Ionic Framework](http://ionicframework.com/).
    
### Ограничивайте Элементы и Атрибуты
###### [Style [Y074](#style-y074)]

  - При создании директивы, которая планируется как самостоятельный элемент, применяйте ограничение `E` (разработано как элемент) или по необходимости ограничение `A` (разработано как атрибут). В основном, если директива разрабатывается как элемент, ограничения `E` вполне достаточно. Хотя AngularJS позволяет использовать `EA`, но все же лучше определится, реализовывать директиву, либо как самостоятельный отдельный элемент, либо как атрибут для улучшения функциональности существующего DOM-элемента. 

    *Почему?*: Это имееет смысл.

    *Почему?*: Конечно мы можем использовать директиву в атрибуте class, но если директива действует как элемент, то лучше объявлять ее как элемент, ну или по крайней мере как атрибут.

    Замечание: EA используется по умолчанию для AngularJS 1.3 +

  ```html
  <!-- избегайте этого -->
  <div class="my-calendar-range"></div>
  ```

  ```javascript
  /* избегайте этого */
  angular
      .module('app.widgets')
      .directive('myCalendarRange', myCalendarRange);

  function myCalendarRange() {
      var directive = {
          link: link,
          templateUrl: '/template/is/located/here.html',
          restrict: 'C'
      };
      return directive;

      function link(scope, element, attrs) {
        /* */
      }
  }
  ```

  ```html
  <!-- рекомендовано -->
  <my-calendar-range></my-calendar-range>
  <div my-calendar-range></div>
  ```
  
  ```javascript
  /* рекомендовано */
  angular
      .module('app.widgets')
      .directive('myCalendarRange', myCalendarRange);

  function myCalendarRange() {
      var directive = {
          link: link,
          templateUrl: '/template/is/located/here.html',
          restrict: 'EA'
      };
      return directive;

      function link(scope, element, attrs) {
        /* */
      }
  }
  ```

### Директивы и ControllerAs
###### [Style [Y075](#style-y075)]

  - Используйте синтактис`controller as` в директиве, чтобы директива была согласована с использованием синтаксиса `controller as` в паре контроллера и представлении.

    *Почему?*: Это имет смысл и не так сложно.

    Замечание: Директива ниже демонстрирует некоторые способы, при которых вы можете использовать объект $scope внутри ссылки и контроллере директивы. Я сделал шаблон инлайновый для того, чтобы держать все в одном месте.

    Замечание: Что касается внедренной зависимости, смотрите [Определение зависимостей вручную](#manual-annotating-for-dependency-injection).

    Замечание: Заметьте, что контроллер директивы находится снаружи самой директивы. Такой подход исключает проблемы, когда инжектор создается в недосягаемом код после, например, 'return'.

  ```html
  <div my-example max="77"></div>
  ```

  ```javascript
  angular
      .module('app')
      .directive('myExample', myExample);

  function myExample() {
      var directive = {
          restrict: 'EA',
          templateUrl: 'app/feature/example.directive.html',
          scope: {
              max: '='
          },
          link: linkFunc,
          controller: ExampleController,
          controllerAs: 'vm'
      };
      
      return directive;

      function linkFunc(scope, el, attr, ctrl) {
          console.log('LINK: scope.max = %i', scope.max);
          console.log('LINK: scope.vm.min = %i', scope.vm.min);
          console.log('LINK: scope.vm.max = %i', scope.vm.max);
      }
  }

  ExampleController.$inject = ['$scope'];

  function ExampleController($scope) {
      // Внедрение $scope сразу для параметра сравнения
      var vm = this;

      vm.min = 3; 
      vm.max = $scope.max; 
      console.log('CTRL: $scope.max = %i', $scope.max);
      console.log('CTRL: vm.min = %i', vm.min);
      console.log('CTRL: vm.max = %i', vm.max);
  }
  ```

  ```html
  /* example.directive.html */
  <div>hello world</div>
  <div>max={{vm.max}}<input ng-model="vm.max"/></div>
  <div>min={{vm.min}}<input ng-model="vm.min"/></div>
  ```

**[Back to top](#table-of-contents)**

##  Работа с Объектами Promise в Контроллерах

###  Активация объектов promise в контроллере
###### [Style [Y080](#style-y080)]

  - Размещайте стартовую начальную логику для контроллера в функции `activate`.   
     
    *Почему?*: Размещение стартовой логики в согласованом месте контроллера позволяет ее быстрее находить, более согласованно тестить, и позволить не разбразывать по всему контроллеру логику активации.

    *Почему?*: Функция `activate` делает удобным повторное использование логики для обновления контроллера/представления, держит все логику вместе, делает более быстрой работу пользователя с представлением, делает анимацию более простой в директивами `ng-view` и `ui-view`, ну и делает пользователя более энергичным и быстрым, ориентируясь в коде.

    Замечание: Если вам нужно при каком-то условии отменить маршрут перед началом использования контроллера, используйте для этого [route resolve](#style-y081).
    
  ```javascript
  /* избегайте этого */
  function Avengers(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      dataservice.getAvengers().then(function(data) {
          vm.avengers = data;
          return vm.avengers;
      });
  }
  ```

  ```javascript
  /* рекомендовано */
  function Avengers(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      activate();

      ////////////

      function activate() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Работа с Объектами Promise в Маршрутах
###### [Style [Y081](#style-y081)]

  - Если контроллер зависит от объекта promise, от результата которого зависит активация контроллера, то разрешайте/получайте эти зависимости в `$routeProvider`, перед тем как логика контроллера будет выполена. Если вам нужно по какому-то условию отменить маршрут перед активацией контроллера, то используйте обработчик маршрутов.    

  - Используйте обработчик маршрутов, если вы решили в последствии отменить маршут до перехода к представлению.

    *Почему?*: Контроллер может потребовать данные перед своей загрузкой.  Эти данные могут прийти из promise объекта через пользовательскую фабрику или [$http](https://docs.angularjs.org/api/ng/service/$http). Использование [route resolve](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider) дает возможность объекту promise разрешиться перед тем как логика контроллера выполнится, тогда мы сможем выполнить действие, зависящее от результата объекта promise.

    *Почему?*: Код выполняется после маршрута и в активационной функции контроллера. После этого сразу же начинает загружаться представление. Связывание данных начинается, когда активационнный объект promisе разрешился/выполнился. Анимация "прогресса" может быть показана время передачи представления (via ng-view or ui-view).

    Замечание: Код запускается перед маршрутом через объект promise. Отклонение promise отменяет маршрут. Разрешение заставляет ожидать новое представление, когда маршрут разрешится. Анимация “прогресса” может быть показана перед разрешением и через передачу представления. Если вам нужно получить представление быстрее и вам не нужна точка решения получать ли представление, используйте тогда [controller `activate` technique](#style-y080).

  ```javascript
  /* избегайте этого */
  angular
      .module('app')
      .controller('Avengers', Avengers);

  function Avengers(movieService) {
      var vm = this;
      // не определено
      vm.movies;
      // определено асинхронно
      movieService.getMovies().then(function(response) {
          vm.movies = response.movies;
      });
  }
  ```

  ```javascript
  /* это лучше */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'Avengers',
              controllerAs: 'vm',
              resolve: {
                  moviesPrepService: function(movieService) {
                      return movieService.getMovies();
                  }
              }
          });
  }

  // avengers.js
  angular
      .module('app')
      .controller('Avengers', Avengers);

  Avengers.$inject = ['moviesPrepService'];
  function Avengers(moviesPrepService) {
        var vm = this;
        vm.movies = moviesPrepService.movies;
  }
  ```

    Замечание: В примере ниже показано, как разрешение маршрута указывает на именованную функцию, которую легче отлаживать и легче оперировать встроенной зависимостью. 

  ```javascript
  /* еще лучше */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'Avengers',
              controllerAs: 'vm',
              resolve: {
                  moviesPrepService: moviesPrepService
              }
          });
  }

  function moviePrepService(movieService) {
      return movieService.getMovies();
  }

  // avengers.js
  angular
      .module('app')
      .controller('Avengers', Avengers);

  Avengers.$inject = ['moviesPrepService'];
  function Avengers(moviesPrepService) {
        var vm = this;
        vm.movies = moviesPrepService.movies;
  }
  ```
    Замечание: Пример кода зависимости от `movieService` не безопасен для минификации. Подробности от том как сделать этот код бесопасным для минификации, смотрите секции [dependency injection](#manual-annotating-for-dependency-injection) и [minification and annotation](#minification-and-annotation).

**[Back to top](#table-of-contents)**

## Manual Annotating for Dependency Injection

### Уязвимости от Минификации
###### [Style [Y090](#style-y090)]

  - Избегайте объявления зависимостей без использования безопасного для минификации подхода. 
  
    *Почему?*: Параметры для компонент (типа контроллер, фабрика и т.п.) будут преобразованы в усеченные переменные. Например, `common` и `dataservice` превратятся `a` или `b` и не будут найдены средой AngularJS.

    ```javascript
    /* избегайте этого - не безопасно для минификации */
    angular
        .module('app')
        .controller('Dashboard', Dashboard);

    function Dashboard(common, dataservice) {
    }
    ```

    Этот после минификации будет производить усеченные переменные и это будет вызывать ошибки выполнения. 

    ```javascript
    /* избегайте этого- не безопасно для минификации */
    angular.module('app').controller('Dashboard', d);function d(a, b) { }
    ```

### Определяйте Зависимости Вручную
###### [Style [Y091](#style-y091)]

  - Используйте `$inject` для ручного определения ваших зависимостей для AngulaJS.
  
    *Почему?*: Этот техника отражает технику использованную в [`ng-annotate`](https://github.com/olov/ng-annotate), которую я рекомендую для автоматического создания зависимостей, безопасных для минификации. Если `ng-annotate` обнаруживает вставку(injection), которая уже была, то она не будет продублирована.

    *Почему?*: Это гарантирует вашим зависимостям защиту от повреждений, которую может нанести минификация, путем урезания и укорачивания переменных. Например, `common` и `dataservice` превратятся `a` и `b`, и не будут найдены средой AngularJS.

    *Почему?*: Избегайте создания однострочных зависимостей в виде длинных списков, которые трудно читаемы в массиве. Еще в вводят в конфуз, то что такие массивы сосотоят из серии строк, в то время как последний элемент - это компонент-функция.

    ```javascript
    /* избегайте этого */
    angular
        .module('app')
        .controller('Dashboard', 
            ['$location', '$routeParams', 'common', 'dataservice', 
                function Dashboard($location, $routeParams, common, dataservice) {}
            ]);
    ```

    ```javascript
    /* избегайте этого */
    angular
      .module('app')
      .controller('Dashboard', 
          ['$location', '$routeParams', 'common', 'dataservice', Dashboard]);
      
    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    ```javascript
    /* рекомендовано */
    angular
        .module('app')
        .controller('Dashboard', Dashboard);

    Dashboard.$inject = ['$location', '$routeParams', 'common', 'dataservice'];

    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    Замечание: Если функция снизу является возращаемым значением, то $inject может быть недостижымым (это может случится в директиве). Это можно решить перемещением $inject выше, чем возращаемое значение, либо использовать альтернативный синтаксис массива вставок.

    Замечание: [`ng-annotate 0.10.0`](https://github.com/olov/ng-annotate) ввело возможность, когда `$inject` переносится туда, где оно доступно.

    ```javascript
    // внутри определения директивы
    function outer() {
        return {
            controller: DashboardPanel,
        };

        DashboardPanel.$inject = ['logger']; // Недоступный код
        function DashboardPanel(logger) {
        }
    }
    ```

    ```javascript
    // внутри определения директивы
    function outer() {
        DashboardPanel.$inject = ['logger']; // Доступный код
        return {
            controller: DashboardPanel,
        };

        function DashboardPanel(logger) {
        }
    }
    ```

### Определяйте Маршрутных Обработчиков Зависимостей Вручную
###### [Style [Y092](#style-y092)]

  - Используйте $inject чтобы вручную определить ваш маршрутный обработчик зависимостей для компонентов AngularJS.

    *Почему?*: Эта техника убирает аннимные функции для маршрутных обработчиков, делая чтение такого код проще.

    *Почему?*: Оператор $inject` может предшествовать обработчику для того, чтобы сдалать любую любую минификацию зависимостей безопасной.

    ```javascript
    /* рекомендовано */
    function config($routeProvider) {
        $routeProvider
            .when('/avengers', {
                templateUrl: 'avengers.html',
                controller: 'Avengers',
                controllerAs: 'vm',
                resolve: {
                    moviesPrepService: moviePrepService
                }
            });
    }

    moviePrepService.$inject = ['movieService'];
    function moviePrepService(movieService) {
        return movieService.getMovies();
    }
    ```

**[Back to top](#table-of-contents)**

## Минификация и аннотация

### ng-annotate
###### [Style [Y100](#style-y100)]

  - Используйте [ng-annotate](//github.com/olov/ng-annotate) для [Gulp](http://gulpjs.com) или [Grunt](http://gruntjs.com) и комментируйте функции, которые нуждаются в автоматической вставке зависимостей, используйте `/** @ngInject */`.
  
    *Why?*: Это гарантирует, что в вашем коде нет зависимостей, которые не используют защиту для повреждений от минификации.

    *Why?*: [`ng-min`](https://github.com/btford/ngmin) не рекомендуется для применения, выводится из употребления 

    >Я предпочитаю Gulp, так как для меня он проще для чтения, написания кода и отладки.

    Следующий код не использует защиту зависимостей от минификации.
    
    ```javascript
    angular
        .module('app')
        .controller('Avengers', Avengers);

    /* @ngInject */
    function Avengers(storageService, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero() {
            var hero = avengerService.find(vm.heroSearch);
            storageService.save(hero.name, hero);
        }
    }
    ```

    Если код выше запустить через ng-annotate, то будет произведен код с аннотацией `$inject`, и код станет устойчив к минификации.  

    ```javascript
    angular
        .module('app')
        .controller('Avengers', Avengers);

    /* @ngInject */
    function Avengers(storageService, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero() {
            var hero = avengerService.find(vm.heroSearch);
            storageService.save(hero.name, hero);
        }
    }

    Avengers.$inject = ['storageService', 'avengerService'];
    ```

    Замечание: Если `ng-annotate` обнаруживает вставки которые уже сделаны (например `@ngInject` был обнаружен), он не будет дублирован в коде `$inject`.

    Замечание: Если используется маршрутный обработчик, то вы можете перед встраиваемой функцией подставить `/* @ngInject */` и это будет производить корректный аннотационный код, делающий каждую вставленную зависимость безопасной для минификации. 

    ```javascript
    // Используем @ngInject аннотацию
    function config($routeProvider) {
        $routeProvider
            .when('/avengers', {
                templateUrl: 'avengers.html',
                controller: 'Avengers',
                controllerAs: 'vm',
                resolve: { /* @ngInject */
                    moviesPrepService: function(movieService) {
                        return movieService.getMovies();
                    }
                }
            });
    }
    ```

    > Замечание: Начиная с AngularJS 1.3 используйте [`ngApp`](https://docs.angularjs.org/api/ng/directive/ngApp) директивный параметр `ngStrictDi`. При наличии инжектора будет создан режим "strict-di", который не даст приложению работать, если обнаружит функции, которые не используют явные аннотации (например, для защиты от минификации). Отладочная информация будет отображаться в консоли, чтобы помочь разработчику выявить код, ломающий приложение.
    `<body ng-app="APP" ng-strict-di>`

### Используйте Gulp или Grunt для ng-annotate
###### [Style [Y101](#style-y101)]

  - Используйте [gulp-ng-annotate](https://www.npmjs.org/package/gulp-ng-annotate) или [grunt-ng-annotate](https://www.npmjs.org/package/grunt-ng-annotate) для автоматических билдов. Вставляйте `/* @ngInject */` перед любой функцией, которая имеет зависимости.
  
    *Почему?*: ng-annotate будет отлавливать большинство зависимостей, но иногда требуются вставлять подсказки в виде синтаксиса `/* @ngInject */`.

    Следующий код является примером gulp задания с использованием ngAnnotate.

    ```javascript
    gulp.task('js', ['jshint'], function() {
        var source = pkg.paths.js;
        return gulp.src(source)
            .pipe(sourcemaps.init())
            .pipe(concat('all.min.js', {newLine: ';'}))
            // Annotate before uglify so the code get's min'd properly.
            .pipe(ngAnnotate({
                // true helps add where @ngInject is not used. It infers.
                // Doesn't work with resolve, so we must be explicit there
                add: true
            }))
            .pipe(bytediff.start())
            .pipe(uglify({mangle: true}))
            .pipe(bytediff.stop())
            .pipe(sourcemaps.write('./'))
            .pipe(gulp.dest(pkg.paths.dev));
    });

    ```

**[Back to top](#table-of-contents)**

## Обработка Исключений

### декораторы
###### [Style [Y110](#style-y110)]

  - Используйте [decorator](https://docs.angularjs.org/api/auto/service/$provide#decorator), во время конфигурации, применяя сервис [`$provide`](https://docs.angularjs.org/api/auto/service/$provide), пользовательские действия будут происходить в сервисе [`$exceptionHandler`](https://docs.angularjs.org/api/ng/service/$exceptionHandler), если произойдут исключения. 
  
    *Почему?*: Это дает постоянный надежный способ обработки необработанных исключений AngularJS во время разработки и во время выполнения.

    Замечание: Другим способом является переопределение сервиса вместо использования декоратора. Это прекрасный способ, но если вы хотите сохранить поведение по умолчанию, и просто дополнить это поведение, то декоратоор крайне рекомендуем.

    ```javascript
    /* рекомендовано */
    angular
        .module('blocks.exception')
        .config(exceptionConfig);

    exceptionConfig.$inject = ['$provide'];

    function exceptionConfig($provide) {
        $provide.decorator('$exceptionHandler', extendExceptionHandler);
    }

    extendExceptionHandler.$inject = ['$delegate', 'toastr'];

    function extendExceptionHandler($delegate, toastr) {
        return function(exception, cause) {
            $delegate(exception, cause);
            var errorData = { 
                exception: exception, 
                cause: cause 
            };
            /**
             * Здесь можно добавить ошибку в сервисную коллекцию,
             * добавить ошибки в $rootScope, логировать ошибки на удаленный сервер
             * или записывать их локально. Или просто бросить ошибку дальше. Это полностью зависит от вас.
             * throw exception;
             */
            toastr.error(exception.msg, errorData);
        };
    }
    ```

### Обработчики Исключений
###### [Style [Y111](#style-y111)]

  - Создайте фабрику, которая предоставит интерфейс для перехвата и изящной обработки исключений. 

    *Почему?*: Это дает постоянный и надежный способ для перехвата исключений, которые могут возникнуть в вашем коде (например, во время вызовов объекта XHR или сбоев в работе объектов promise).

    Замечание: Перехватчик исключений хорош для отлавливания и реагирования специфических исключений для вызовов, про которые вы точно знаете, что они могут бросить только одно исключение. Например, вы делаете вызов XHR для получения данных с удаленного сервера и хотите поймать все исключения этого сервиса и обработать их индивидуально.

    ```javascript
    /* рекомендовано */
    angular
        .module('blocks.exception')
        .factory('exception', exception);

    exception.$inject = ['logger'];

    function exception(logger) {
        var service = {
            catcher: catcher
        };
        return service;

        function catcher(message) {
            return function(reason) {
                logger.error(message, reason);
            };
        }
    }
    ```

### Маршрутные ошибки
###### [Style [Y112](#style-y112)]

  - Обрабатывайте и логгируйте все маршрутные ошибки изпользуя [`$routeChangeError`](https://docs.angularjs.org/api/ngRoute/service/$route#$routeChangeError).

    *Почему?*: Это дает постоянный и устойчивый способ обработки всех маршрутных ошибок.

    *Почему?*: Потенциально мы получим лучший пользовательский опыт обработки маршрутных ошибок и научимся перенаправлять их на более дружественный экран описания ошибки с большими подробностями и указаниями к восстановлению здоровой ситуации.

    ```javascript
    /* рекомендовано */
    function handleRoutingErrors() {
        /**
         * Отмена маршрута:
         * Во время маршрутной ошибки, мы переходим на информационную панель.
         * Не забудьте реализовать выход, если происходит попытка перехода дважды.
         */
        $rootScope.$on('$routeChangeError',
            function(event, current, previous, rejection) {
                var destination = (current && (current.title || current.name || current.loadedTemplateUrl)) ||
                    'unknown target';
                var msg = 'Error routing to ' + destination + '. ' + (rejection.msg || '');
                /**
                 * Опционально мы можем записать логи, изпользуя пользовательский сервис или $log.
                 * (Не забудьте инжектировать пользовательский сервис)
                 */
                logger.warning(msg, [current]);
            }
        );
    }
    ```

**[Back to top](#table-of-contents)**

## Именования

### Рекомендации для именований
###### [Style [Y120](#style-y120)]

  - Используйте согласованные имена для всех компонентов по шаблону, который описывает особенность(feature) компонента, а затем (опционально) его тип. Я рекомендую шаблон - `feature.type.js`. Существует два типа имен для большинства случаев:
    * имя файла (`avengers.controller.js`)
    * имя компонента, которое зарегистрировано Angular (`AvengersController`)
 
    *Почему?*: Соглашения об именованиях дает постояннный надежный способ поиска содержимого быстрым беглым взглядом. Согласованность в проекте жизненно важна. Согласованность в команде очень важна. Согласованность между компаниями дает огромную эффективность. 

    *Почему?*: Соглашения об именованиях просто должны помочь найти вам свой код быстрее, и сделать его проще для понимания. 
### Характерные Имена Файлов
###### [Style [Y121](#style-y121)]

  - Используйте согласованные имена для всех компонентов используя шаблон, который описывает компонентную особенность, затем опционально его тип. Я рекомендую шаблон - `feature.type.js`.  

    *Почему?*: Это надежный способ для быстрой идентификации компонентов.

    *Почему?*: Автоматизирует рабочий процесс.

    ```javascript
    /**
     * общие настройки
     */

    // Контроллеры
    avengers.js
    avengers.controller.js
    avengersController.js

    // Сервисы/Фабрики
    logger.js
    logger.service.js
    loggerService.js
    ```

    ```javascript
    /**
     * рекомендовано
     */

    // контроллеры
    avengers.controller.js
    avengers.controller.spec.js

    // сервисы/фабрики
    logger.service.js
    logger.service.spec.js

    // константы
    constants.js
    
    // определение модуля
    avengers.module.js

    // маршруты
    avengers.routes.js
    avengers.routes.spec.js

    // конфигурация
    avengers.config.js
    
    // директивы
    avenger-profile.directive.js
    avenger-profile.directive.spec.js
    ```

  Замечание: Другим общим соглашением является именование файлов контроллера без слова `controller`, например называем файл `avengers.js` вместо `avengers.controller.js`. Все остальные соглашения все же должны содержать суффикс типа. Просто контроллеры наиболее общий тип компонетов, и таким образом мы экономим на время на печатании имени, но контроллеры все равно прекрасно идентифицируются. Я рекомендую выбрать один тип соглашения и быть на одной волне со своей командой.
  
    ```javascript
    /**
     * рекомендовано
     */
    // Controllers
    avengers.js
    avengers.spec.js
    ```

### Имена Тестовых Файлов
###### [Style [Y122](#style-y122)]

  - Имя тестовой спецификации подобно имени компонента, которая его тестит, только к ней еще добавляется суффикс `spec`. 

    *Почему?*: Это надежный способ для быстрой идентификации компонентов.

    *Why?*: Это шаблон соответствующий [karma](http://karma-runner.github.io/) или другим движкам для запуска тестов.

    ```javascript
    /**
     * рекомендовано
     */
    avengers.controller.spec.js
    logger.service.spec.js
    avengers.routes.spec.js
    avenger-profile.directive.spec.js
    ```

### Имена Контроллеров
###### [Style [Y123](#style-y123)]

  - Используйте согласованные имена для всех контроллеров, именованных по их характерной особенности. Используйте  UpperCamelCase (ВерхнийВерблюжийРегистр) для контроллеров, так как они являются конструкторами. 

    *Почему?*: Это дает надежный и понятный способ для быстрой идентификации и применения контроллеров.

    *Почему?*: UpperCamelCase является традиционным форматом для идентификации объектов, которые могут быть созданы с помощью конструктора.

    ```javascript
    /**
     * рекомендовано
     */

    // avengers.controller.js
    angular
        .module
        .controller('HeroAvengers', HeroAvengers);

    function HeroAvengers() { }
    ```

### Суффикс Имени Контроллера
###### [Style [Y124](#style-y124)]

  - Добавляйте к имени контроллера суффикс `Controller` или не добавляйте. Выберите что-то одно, но не два.

    *Почему?*: Суффикс `Controller` более общеупотребим и наиболее явно описателен.

    *Почему?*: Если не указывать суффикс, то получатся более краткие имена, но контроллеры все равно будут легко идентифицируемы, даже без суффикса. 

    ```javascript
    /**
     * рекомендовано: Вариант 1
     */

    // avengers.controller.js
    angular
        .module
        .controller('Avengers', Avengers);

    function Avengers() { }
    ```

    ```javascript
    /**
     * рекомендовано: Вариант 2
     */

    // avengers.controller.js
    angular
        .module
        .controller('AvengersController', AvengersController);

    function AvengersController() { }
    ```

### Имена Фабрик
###### [Style [Y125](#style-y125)]

  - Используйте согласованные имена для всех фабрик, именуйте их по характерной особенности. Используйте camel-casing для сервисов и фабрик.

    *Почему?*: Это дает надежный и понятный способ для быстрой идентификации и применения фабрик.

    ```javascript
    /**
     * рекомендовано
     */

    // logger.service.js
    angular
        .module
        .factory('logger', logger);

    function logger() { }
    ```

### Имена Директивных Компонент
###### [Style [Y126](#style-y126)]

  - Используйте согласованные имена для всех директив, применяя camel-case. Добавляйте короткий префикс для описания области, которой эти директивы принадлежат (иногда это может быть префикс компании, иногда префикс проекта).

    *Почему?*: Это дает надежный и понятный способ для быстрой идентификации и применения компонент.

    ```javascript
    /**
     * рекомендовано
     */

    // avenger-profile.directive.js
    angular
        .module
        .directive('xxAvengerProfile', xxAvengerProfile);

    // применять так <xx-avenger-profile> </xx-avenger-profile>

    function xxAvengerProfile() { }
    ```

### Модули
###### [Style [Y127](#style-y127)]

  - Если разрабатываются несколько модулей, файл главного модуля будет называться `app.module.js`, а другие модули получат свое название по своему назначению (то что они представляют). Например, модуль администрирования будет назван `admin.module.js`. Соответствующие зарегистрированные имена модулей будут `app` и `admin`. 

    *Почему?*: Это дает согласованность для многих модулей приложения, а также позволяет расширяться в огромные приложения.  
    *Почему?*: Получаем простой способ автоматизировать работу по первоначальной загрузке всех модульных определений, а затем уже остальных файлов angular (для )  Provides easy way to use task automation to load all module definitions first, then all other angular files (для упаковки и комплектации файлов - bundling).

### Конфигурация
###### [Style [Y128](#style-y128)]

  - Отделяйте конфигурационную информацию от модуля в отдельном файле, называйте такой файл по названию модульного файла. Конфигурационный файл для главного `app` модуля называем `app.config.js` (или просто `config.js`). Конфигурацию для модуля `admin.module.js` называем соответственно `admin.config.js`.

    *Почему?*: Конфигурация отделяется от определения модуля, компонентов и активного кода.

    *Why?*: Мы получаем идентифицируемое место для установки конфигурации для модуля.

### Маршруты
###### [Style [Y129](#style-y129)]

  - Выделяйте конфигурацию маршрута в свой собственный файл. Примеры могут быть такими: `app.route.js` для главного модуля и `admin.route.js` для модуля `admin`. Даже в маленьких приложениях я предпочитаю такое разделение от остальной конфигурации. 

**[Back to top](#table-of-contents)**

## Структура Приложения по Приниципу LIFT
### LIFT
###### [Style [Y140](#style-y140)]

  - Структура вашего приложения должна быть построена таким образом, чтобы вы могли: `L`размещать ваш код быстро - (`L`ocate your code quickly), `I`идентифицировать код практически с первого взгляда - (`I`dentify the code at a glance), `F`держать структуру ровной насколько это возможно - (keep the `F`lattest structure you can), и стараться оставаться DRY (Don’t Repeat Yourself) - Не Повторяйте Себя - (`T`ry to stay DRY - Don’t Repeat Yourself). Структура должна придерживаться этим основным 4 правилам.

    *Почему LIFT?*: Получаем согласованную структуру, которая хорошо масштабируется, разбита на модули, и легко позволяет увеличить эффективность разработчика, путем быстрого нахождения кода. Другой способ проверить структуру вашего приложения - это спросить себя: Как быстро я могу открыть все сооответсвующие файлы, чтобы работать над нужной функциональностью? 
    
    Когда я чувствую, что работать с моей структурой некомфортно, я возвращаюсь к принципам LIFT.
  
    1. `L`Размещать наш код легко  (`L`ocating our code is easy)
    2. `I`Идентифицировать код быстро (`I`dentify code at a glance)
    3. `F`Держать структуру ровной как можно дольше (`F`lat structure as long as we can)
    4. `T`Старайтесь оставаться DRY или T-DRY (Don’t Repeat Yourself)  - Не Повторяйте Себя (`T`ry to stay DRY - Don’t Repeat Yourself)

### Размещение
###### [Style [Y141](#style-y141)]

  - Размещайте свой код интуитивно, просто и быстро.

    *Почему?*: Я считаю, это должно быть супер важно для проекта. Если команда не может быстро найти файлы, с которыми нужно работать, то команда не сможет работать настолько эффективно, насколько это возможно. Такая структура должна быть изменена. Вы можете не знать имени файла или где находятся его сопутствующие файлы, тогда поместите их в наиболее интуитивно подходящее место, рядом друг другом, и это сэкономит кучу времени. Описательная структура папок может помочь с этим.

    ```
    /bower_components
    /client
      /app
        /avengers
        /blocks
          /exception
          /logger
        /core
        /dashboard
        /data
        /layout
        /widgets
      /content
      index.html
    .bower.json
    ```

### Идентификация
###### [Style [Y142](#style-y142)]

  - Когда вы смотрите на файл вы должны мгновенно знать и понимать, что он содержит и представляет.

    *Почему?*: Вы потратите меньше времени на охоту и клевание кода, и станете более эффективными. Если для этого потребуется более длинные имена файлов, то пусть будет так. Делайте имена файлов описательными, и держите в файле только один компонент. Избегайте содержать в файле несколько контроллеров, несколько сервисов или вообще смесь всего. Могут быть конечно отклонения от правила 1 компонента в файле, когда я прописываю в одном файле очень маленькие сущности, которые связаны друг с другом, но они все равно очень легко идентифицируемы.

### Плоская структура
###### [Style [Y143](#style-y143)]

  - Держите структуру папок плоской, как можно дольше. Когда у вас больше 7 файлов начните думать о разделении.

    *Почему?*: Никто не хочет искать файл в семи уровнях папок. Вспомните о меню на веб-сайтах … все что глубже второго уровня требует серьезного размышления. В в организации структуры папок нет жестких правил, но если папка содержит 7-10 файлов, нужно делать подпапку. Основывайтесь на уровне вашего комфорта.  Base it on your comfort level. Используйте плоскую структуру пока не станет точно очевидно, что нужно создать новую папку (и чтобы соблюсти принципы LIFT).

### T-DRY (Try to Stick to DRY)
###### [Style [Y144](#style-y144)]

  - Be DRY, but don't go nuts and sacrifice readability.

    *Why?*: Being DRY is important, but not crucial if it sacrifices the others in LIFT, which is why I call it T-DRY. I don’t want to type session-view.html for a view because, well, it’s obviously a view. If it is not obvious or by convention, then I name it. 

**[Back to top](#table-of-contents)**

## Application Structure

### Overall Guidelines
###### [Style [Y150](#style-y150)]

  - Have a near term view of implementation and a long term vision. In other words, start small and but keep in mind on where the app is heading down the road. All of the app's code goes in a root folder named `app`. All content is 1 feature per file. Each controller, service, module, view is in its own file. All 3rd party vendor scripts are stored in another root folder and not in the `app` folder. I didn't write them and I don't want them cluttering my app (`bower_components`, `scripts`, `lib`).

    Note: Find more details and reasoning behind the structure at [this original post on application structure](http://www.johnpapa.net/angular-app-structuring-guidelines/).

### Layout
###### [Style [Y151](#style-y151)]

  - Place components that define the overall layout of the application in a folder named `layout`. These may include a shell view and controller may act as the container for the app, navigation, menus, content areas, and other regions. 

    *Why?*: Organizes all layout in a single place re-used throughout the application.

### Folders-by-Feature Structure
###### [Style [Y152](#style-y152)]

  - Create folders named for the feature they represent. When a folder grows to contain more than 7 files, start to consider creating a folder for them. Your threshold may be different, so adjust as needed. 

    *Why?*: A developer can locate the code, identify what each file represents at a glance, the structure is flat as can be, and there is no repetitive nor redundant names. 

    *Why?*: The LIFT guidelines are all covered.

    *Why?*: Helps reduce the app from becoming cluttered through organizing the content and keeping them aligned with the LIFT guidelines.

    *Why?*: When there are a lot of files (10+) locating them is easier with a consistent folder structures and more difficult in flat structures.

    ```javascript
    /**
     * recommended
     */

    app/
        app.module.js
        app.config.js
        app.routes.js
        components/
            calendar.directive.js
            calendar.directive.html
            user-profile.directive.js
            user-profile.directive.html
        layout/
            shell.html
            shell.controller.js
            topnav.html
            topnav.controller.js
        people/
            attendees.html
            attendees.controller.js
            speakers.html
            speakers.controller.js
            speaker-detail.html
            speaker-detail.controller.js
        services/
            data.service.js
            localstorage.service.js
            logger.service.js
            spinner.service.js
        sessions/
            sessions.html
            sessions.controller.js
            session-detail.html
            session-detail.controller.js
    ```

      ![Sample App Structure](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/modularity-2.png)

      Note: Do not use structuring using folders-by-type. This requires moving to multiple folders when working on a feature and gets unwieldy quickly as the app grows to 5, 10 or 25+ views and controllers (and other features), which makes it more difficult than folder-by-feature to locate files.

    ```javascript
    /* 
    * avoid
    * Alternative folders-by-type.
    * I recommend "folders-by-feature", instead.
    */
    
    app/
        app.module.js
        app.config.js
        app.routes.js
        controllers/
            attendees.js
            session-detail.js
            sessions.js
            shell.js
            speakers.js
            speaker-detail.js
            topnav.js
        directives/
            calendar.directive.js
            calendar.directive.html
            user-profile.directive.js
            user-profile.directive.html
        services/
            dataservice.j
            localstorage.js
            logger.js
            spinner.js
        views/
            attendees.html
            session-detail.html
            sessions.html
            shell.html
            speakers.html
            speaker-detail.html
            topnav.html
    ``` 

**[Back to top](#table-of-contents)**

## Modularity

### Many Small, Self Contained Modules
###### [Style [Y160](#style-y160)]

  - Create small modules that encapsulate one responsibility.

    *Why?*: Modular applications make it easy to plug and go as they allow the development teams to build vertical slices of the applications and roll out incrementally. This means we can plug in new features as we develop them.

### Create an App Module
###### [Style [Y161](#style-y161)]

  - Create an application root module whose role is pull together all of the modules and features of your application. Name this for your application.

    *Why?*: AngularJS encourages modularity and separation patterns. Creating an application root module whose role is to tie your other modules together provides a very straightforward way to add or remove modules from your application.

### Keep the App Module Thin
###### [Style [Y162](#style-y162)]

  - Only put logic for pulling together the app in the application module. Leave features in their own modules.

    *Why?*: Adding additional roles to the application root to get remote data, display views, or other logic not related to pulling the app together muddies the app module and make both sets of features harder to reuse or turn off.

    *Why?*: The app module becomes a manifest that describes which modules help define the application. 

### Feature Areas are Modules
###### [Style [Y163](#style-y163)]

  - Create modules that represent feature areas, such as layout, reusable and shared services, dashboards, and app specific features (e.g. customers, admin, sales).

    *Why?*: Self contained modules can be added to the application with little or no friction.

    *Why?*: Sprints or iterations can focus on feature areas and turn them on at the end of the sprint or iteration.

    *Why?*: Separating feature areas into modules makes it easier to test the modules in isolation and reuse code. 

### Reusable Blocks are Modules
###### [Style [Y164](#style-y164)]

  - Create modules that represent reusable application blocks for common services such as exception handling, logging, diagnostics, security, and local data stashing.

    *Why?*: These types of features are needed in many applications, so by keeping them separated in their own modules they can be application generic and be reused across applications.

### Module Dependencies
###### [Style [Y165](#style-y165)]

  - The application root module depends on the app specific feature modules and any shared or reusable modules.

    ![Modularity and Dependencies](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/modularity-1.png)

    *Why?*: The main app module contains a quickly identifiable manifest of the application's features. 

    *Why?*: Each feature area contains a manifest of what it depends on, so it can be pulled in as a dependency in other applications and still work. 

    *Why?*: Intra-App features such as shared data services become easy to locate and share from within `app.core` (choose your favorite name for this module).

    Note: This is a strategy for consistency. There are many good options here. Choose one that is consistent, follows AngularJS's dependency rules, and is easy to maintain and scale.

    > My structures vary slightly between projects but they all follow these guidelines for structure and modularity. The implementation may vary depending on the features and the team. In other words, don't get hung up on an exact like-for-like structure but do justify your structure using consistency, maintainability, and efficiency in mind. 

    > In a small app, you can also consider putting all the shared dependencies in the app module where the feature modules have no direct dependencies. This makes it easier to maintain the smaller application, but makes it harder to reuse modules outside of this application.

**[Back to top](#table-of-contents)**

## Startup Logic

### Configuration
###### [Style [Y170](#style-y170)]

  - Inject code into [module configuration](https://docs.angularjs.org/guide/module#module-loading-dependencies) that must be configured before running the angular app. Ideal candidates include providers and constants.

    *Why?*: This makes it easier to have a less places for configuration.

  ```javascript
  angular
      .module('app')
      .config(configure);

  configure.$inject = 
      ['routerHelperProvider', 'exceptionHandlerProvider', 'toastr'];

  function configure (routerHelperProvider, exceptionHandlerProvider, toastr) {
      exceptionHandlerProvider.configure(config.appErrorPrefix);
      configureStateHelper();

      toastr.options.timeOut = 4000;
      toastr.options.positionClass = 'toast-bottom-right';

      ////////////////

      function configureStateHelper() {
          routerHelperProvider.configure({
              docTitle: 'NG-Modular: '
          });
      }
  }
  ```

### Run Blocks
###### [Style [Y171](#style-y171)]

  - Any code that needs to run when an application starts should be declared in a factory, exposed via a function, and injected into the [run block](https://docs.angularjs.org/guide/module#module-loading-dependencies).

    *Why?*: Code directly in a run block can be difficult to test. Placing in a factory makes it easier to abstract and mock.

  ```javascript
  angular
      .module('app')
      .run(runBlock);

    runBlock.$inject = ['authenticator', 'translator'];

    function runBlock(authenticator, translator) {
        authenticator.initialize();
        translator.initialize();
    }
  ```

**[Back to top](#table-of-contents)**

## Angular $ Wrapper Services

### $document and $window
###### [Style [Y180](#style-y180)]

  - Use [`$document`](https://docs.angularjs.org/api/ng/service/$document) and [`$window`](https://docs.angularjs.org/api/ng/service/$window) instead of `document` and `window`.

    *Why?*: These services are wrapped by Angular and more easily testable than using document and window in tests. This helps you avoid having to mock document and window yourself.

### $timeout and $interval
###### [Style [Y181](#style-y181)]

  - Use [`$timeout`](https://docs.angularjs.org/api/ng/service/$timeout) and [`$interval`](https://docs.angularjs.org/api/ng/service/$interval) instead of `setTimeout` and `setInterval` .

    *Why?*: These services are wrapped by Angular and more easily testable and handle AngularJS's digest cycle thus keeping data binding in sync.

**[Back to top](#table-of-contents)**

## Testing
Unit testing helps maintain clean code, as such I included some of my recommendations for unit testing foundations with links for more information.

### Write Tests with Stories
###### [Style [Y190](#style-y190)]

  - Write a set of tests for every story. Start with an empty test and fill them in as you write the code for the story.

    *Why?*: Writing the test descriptions helps clearly define what your story will do, will not do, and how you can measure success.

    ```javascript
    it('should have Avengers controller', function() {
        // TODO
    });

    it('should find 1 Avenger when filtered by name', function() {
        // TODO
    });

    it('should have 10 Avengers', function() {
        // TODO (mock data?)
    });

    it('should return Avengers via XHR', function() {
        // TODO ($httpBackend?)
    });

    // and so on
    ```

### Testing Library
###### [Style [Y191](#style-y191)]

  - Use [Jasmine](http://jasmine.github.io/) or [Mocha](http://mochajs.org) for unit testing.

    *Why?*: Both Jasmine and Mocha are widely used in the AngularJS community. Both are stable, well maintained, and provide robust testing features.

    Note: When using Mocha, also consider choosing an assert library such as [Chai](http://chaijs.com).

### Test Runner
###### [Style [Y192](#style-y192)]

  - Use [Karma](http://karma-runner.github.io) as a test runner.

    *Why?*: Karma is easy to configure to run once or automatically when you change your code.

    *Why?*: Karma hooks into your Continuous Integration process easily on its own or through Grunt or Gulp.

    *Why?*: Some IDE's are beginning to integrate with Karma, such as [WebStorm](http://www.jetbrains.com/webstorm/) and [Visual Studio](http://visualstudiogallery.msdn.microsoft.com/02f47876-0e7a-4f6c-93f8-1af5d5189225).

    *Why?*: Karma works well with task automation leaders such as [Grunt](http://www.gruntjs.com) (with [grunt-karma](https://github.com/karma-runner/grunt-karma)) and [Gulp](http://www.gulpjs.com) (with [gulp-karma](https://github.com/lazd/gulp-karma)).

### Stubbing and Spying
###### [Style [Y193](#style-y193)]

  - Use [Sinon](http://sinonjs.org/) for stubbing and spying.

    *Why?*: Sinon works well with both Jasmine and Mocha and extends the stubbing and spying features they offer.

    *Why?*: Sinon makes it easier to toggle between Jasmine and Mocha, if you want to try both.

### Headless Browser
###### [Style [Y194](#style-y194)]

  - Use [PhantomJS](http://phantomjs.org/) to run your tests on a server.

    *Why?*: PhantomJS is a headless browser that helps run your tests without needing a "visual" browser. So you do not have to install Chrome, Safari, IE, or other browsers on your server. 

    Note: You should still test on all browsers in your environment, as appropriate for your target audience.

### Code Analysis
###### [Style [Y195](#style-y195)]

  - Run JSHint on your tests. 

    *Why?*: Tests are code. JSHint can help identify code quality issues that may cause the test to work improperly.

### Alleviate Globals for JSHint Rules on Tests
###### [Style [Y196](#style-y196)]

  - Relax the rules on your test code to allow for common globals such as `describe` and `expect`.

    *Why?*: Your tests are code and require the same attention and code quality rules as all of your production code. However, global variables used by the testing framework, for example, can be relaxed by including this in your test specs.

    ```javascript
    /* global sinon, describe, it, afterEach, beforeEach, expect, inject */
    ```

  ![Testing Tools](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/testing-tools.png)

### Organizing Tests
###### [Style [Y197](#style-y197)]

  - Place unit test files (specs) side-by-side with your client code. Place specs that cover server integration or test multiple components in a separate `tests` folder.

    *Why?*: Unit tests have a direct correlation to a specific component and file in source code. 

    *Why?*: It is easier to keep them up to date since they are always in sight. When coding whether you do TDD or test during development or test after development, the specs are side-by-side and never out of sight nor mind, and thus more likely to be maintained which also helps maintain code coverage.

    *Why?*: When you update source code it is easier to go update the tests at the same time.

    *Why?*: Placing them side-by-side makes it easy to find them and easy to move them with the source code if you move the source.


    *Why?*: Separating specs so they are not in a distributed build is easy with grunt or gulp.

    ```
    /src/client/app/customers/customer-detail.controller.js
                             /customer-detail.controller.spec.js
                             /customers.controller.spec.js
                             /customers.controller-detail.spec.js
                             /customers.module.js
                             /customers.route.js
                             /customers.route.spec.js
    ```

**[Back to top](#table-of-contents)**

## Animations

### Usage
###### [Style [Y210](#style-y210)]

  - Use subtle [animations with AngularJS](https://docs.angularjs.org/guide/animations) to transition between states for views and primary visual elements. Include the [ngAnimate module](https://docs.angularjs.org/api/ngAnimate). The 3 keys are subtle, smooth, seamless.

    *Why?*: Subtle animations can improve User Experience when used appropriately.

    *Why?*: Subtle animations can improve perceived performance as views transition.

### Sub Second
###### [Style [Y211](#style-y211)]

  - Use short durations for animations. I generally start with 300ms and adjust until appropriate. 

    *Why?*: Long animations can have the reverse affect on User Experience and perceived performance by giving the appearance of a slow application.

### animate.css
###### [Style [Y212](#style-y212)]

  - Use [animate.css](http://daneden.github.io/animate.css/) for conventional animations.

    *Why?*: The animations that animate.css provides are fast, smooth, and easy to add to your application.

    *Why?*: Provides consistency in your animations.

    *Why?*: animate.css is widely used and tested.

    Note: See this [great post by Matias Niemelä on AngularJS animations](http://www.yearofmoo.com/2013/08/remastered-animation-in-angularjs-1-2.html)

**[Back to top](#table-of-contents)**

## Comments

### jsDoc
###### [Style [Y220](#style-y220)]

  - If planning to produce documentation, use [`jsDoc`](http://usejsdoc.org/) syntax to document function names, description, params and returns. Use `@namespace` and `@memberOf` to match your app structure.

    *Why?*: You can generate (and regenerate) documentation from your code, instead of writing it from scratch.

    *Why?*: Provides consistency using a common industry tool.

    ```javascript
    /**
     * Logger Factory
     * @namespace Factories
     */
    (function() {
      angular
          .module('app')
          .factory('logger', logger);

      /**
       * @namespace Logger
       * @desc Application wide logger
       * @memberOf Factories
       */
      function logger($log) {
          var service = {
             logError: logError
          };
          return service;

          ////////////

          /**
           * @name logError
           * @desc Logs errors
           * @param {String} msg Message to log 
           * @returns {String}
           * @memberOf Factories.Logger
           */
          function logError(msg) {
              var loggedMsg = 'Error: ' + msg;
              $log.error(loggedMsg);
              return loggedMsg;
          };
      }
    })();
    ```

**[Back to top](#table-of-contents)**

## JS Hint

### Use an Options File
###### [Style [Y230](#style-y230)]

  - Use JS Hint for linting your JavaScript and be sure to customize the JS Hint options file and include in source control. See the [JS Hint docs](http://www.jshint.com/docs/) for details on the options.

    *Why?*: Provides a first alert prior to committing any code to source control.

    *Why?*: Provides consistency across your team.

    ```javascript
    {
        "bitwise": true,
        "camelcase": true,
        "curly": true,
        "eqeqeq": true,
        "es3": false,
        "forin": true,
        "freeze": true,
        "immed": true,
        "indent": 4,
        "latedef": "nofunc",
        "newcap": true,
        "noarg": true,
        "noempty": true,
        "nonbsp": true,
        "nonew": true,
        "plusplus": false,
        "quotmark": "single",
        "undef": true,
        "unused": false,
        "strict": false,
        "maxparams": 10,
        "maxdepth": 5,
        "maxstatements": 40,
        "maxcomplexity": 8,
        "maxlen": 120,

        "asi": false,
        "boss": false,
        "debug": false,
        "eqnull": true,
        "esnext": false,
        "evil": false,
        "expr": false,
        "funcscope": false,
        "globalstrict": false,
        "iterator": false,
        "lastsemic": false,
        "laxbreak": false,
        "laxcomma": false,
        "loopfunc": true,
        "maxerr": false,
        "moz": false,
        "multistr": false,
        "notypeof": false,
        "proto": false,
        "scripturl": false,
        "shadow": false,
        "sub": true,
        "supernew": false,
        "validthis": false,
        "noyield": false,

        "browser": true,
        "node": true,

        "globals": {
            "angular": false,
            "$": false
        }
    }
    ```

**[Back to top](#table-of-contents)**

## Constants

### Vendor Globals
###### [Style [Y240](#style-y240)]

  - Create an AngularJS Constant for vendor libraries' global variables.

    *Why?*: Provides a way to inject vendor libraries that otherwise are globals. This improves code testability by allowing you to more easily know what the dependencies of your components are (avoids leaky abstractions). It also allows you to mock these dependencies, where it makes sense.

    ```javascript
    // constants.js

    /* global toastr:false, moment:false */
    (function() {
        'use strict';

        angular
            .module('app.core')
            .constant('toastr', toastr)
            .constant('moment', moment);
    })();
    ```
    
###### [Style [Y241](#style-y241)]

  - Use constants for values that do not change and do not come from another service. When constants are used only for a module that may be reused in multiple applications, place constants in a file per module named after the module. Until this is required, keep constants in the main module in a `constants.js` file.

    *Why?*: A value that may change, even infrequently, should be retrieved from a service so you do not have to change the source code. For example, a url for a data service could be placed in a constants but a better place would be to load it from a web service.

    *Why?*: Constants can be injected into any angular component, including providers.

    *Why?*: When an application is separated into modules that may be reused in other applications, each stand-alone module should be able to operate on its own including any dependent constants. 

    ```javascript
    // Constants used by the entire app
    angular
        .module('app.core')
        .constant('moment', moment);

    // Constants used only by the sales module
    angular
        .module('app.sales')
        .constant('events', {
            ORDER_CREATED: 'event_order_created',
            INVENTORY_DEPLETED: 'event_inventory_depleted'
        });
    ```

**[Back to top](#table-of-contents)**

## File Templates and Snippets
Use file templates or snippets to help follow consistent styles and patterns. Here are templates and/or snippets for some of the web development editors and IDEs.

### Sublime Text
###### [Style [Y250](#style-y250)]

  - AngularJS snippets that follow these styles and guidelines. 

    - Download the [Sublime Angular snippets](assets/sublime-angular-snippets.zip?raw=true) 
    - Place it in your Packages folder
    - Restart Sublime 
    - In a JavaScript file type these commands followed by a `TAB`
 
    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective // creates an Angular directive
    ngfactory // creates an Angular factory
    ngmodule // creates an Angular module
    ```

### Visual Studio
###### [Style [Y251](#style-y251)]

  - AngularJS file templates that follow these styles and guidelines can be found at [SideWaffle](http://www.sidewaffle.com)

    - Download the [SideWaffle](http://www.sidewaffle.com) Visual Studio extension (vsix file)
    - Run the vsix file
    - Restart Visual Studio

### WebStorm
###### [Style [Y252](#style-y252)]

  - AngularJS snippets and file templates that follow these styles and guidelines. You can import them into your WebStorm settings:

    - Download the [WebStorm AngularJS file templates and snippets](assets/webstorm-angular-file-template.settings.jar?raw=true) 
    - Open WebStorm and go to the `File` menu
    - Choose the `Import Settings` menu option
    - Select the file and click `OK`
    - In a JavaScript file type these commands followed by a `TAB`:

    ```javascript
    ng-c // creates an Angular controller
    ng-f // creates an Angular factory
    ng-m // creates an Angular module
    ```

**[Back to top](#table-of-contents)**

## AngularJS docs
For anything else, API reference, check the [Angular documentation](//docs.angularjs.org/api).

## Contributing

Open an issue first to discuss potential changes/additions. If you have questions with the guide, feel free to leave them as issues in the repository. If you find a typo, create a pull request. The idea is to keep the content up to date and use github’s native feature to help tell the story with issues and PR’s, which are all searchable via google. Why? Because odds are if you have a question, someone else does too! You can learn more here at about how to contribute.

*By contributing to this repository you are agreeing to make your content available subject to the license of this repository.*

### Process
    1. Discuss the changes in an Issue. 
    2. Open a Pull Request, reference the issue, and explain the change and why it adds value.
    3. The Pull Request will be evaluated and either merged or declined.

## License

_tldr; Use this guide. Attributions are appreciated._

### (The MIT License)

Copyright (c) 2014 [John Papa](http://johnpapa.net)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**[Back to top](#table-of-contents)**
