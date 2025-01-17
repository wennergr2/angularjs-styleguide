# Guia de Estilo AngularJS

*Guia de Estilo opinativo de AngularJS para times. Por [@john_papa](//twitter.com/john_papa)*

*Traduzido por [Eric Douglas](https://github.com/ericdouglas), [Ciro Nunes](https://github.com/cironunes), [Jean Lucas de Carvalho](https://github.com/jlcarvalho) e [Vinicius Sabadim Fernandes](https://github.com/vinicius-sabadim)*

>The [original English version](http://jpapa.me/ngstyles) is the source of truth, as it is maintained and updated first.
>A [versão original em inglês](http://jpapa.me/ngstyles) é a fonte da verdade, já que é corrigida e atualizada primeiro.

Se você procura por um guia de estilo opinativo para sintaxe, convenções e estruturação de aplicações AngularJS, então siga em frente! Estes estilos são baseados em minha experiência com desenvolvimento com [AngularJS](//angularjs.org), apresentações, [cursos de treinamento na Pluralsight](http://pluralsight.com/training/Authors/Details/john-papa) e trabalhando em equipes.

> Se você gostar deste estilo, confira meu curso [AngularJS Patterns: Clean Code](http://jpapa.me/ngclean) na Plurasight.

A proposta deste guia de estilo é fornecer uma direção na construção de aplicações AngularJS mostrando convenções que eu uso, e o mais importante, porque eu as escolhi.

## A Importância da Comunidade e Créditos

Nunca trabalhe sozinho. Acho que a comunidade AngularJS é um grupo incrível que é apaixonado em compartilhar experiências. Dessa forma, Todd Motto, um amigo e expert em AngularJS e eu temos colaborado com vários estilos e convenções. Nós concordamos na maioria deles, e discordamos em alguns. Eu encorajo você a conferir o [guia do Todd](https://github.com/toddmotto/angularjs-styleguide) para ter uma noção sobre sua abordagem e como ela se compara a esta.

Vários de meus estilos vieram de várias sessões de pair-programming que [Ward Bell](http://twitter.com/wardbell) e eu tivemos. Embora não corcordemos sempre, meu amigo Ward certamente me ajudou influenciando na última evolução deste guia.

## Veja os estilos em um aplicativo de exemplo

Embora este guia explique o **o quê**, **porque** e **como**, acho útil ver tudo isso em prática. Este guia é acompanhado de uma aplicação de exemplo que segue estes estilos e padrões. Você pode encontrar a [aplicação de exemplo (chamada "modular") aqui](https://github.com/johnpapa/ng-demos) na pasta `modular`. Sinta-se livre para pegá-la, cloná-la e *forká-la*. [Instruções de como rodar o aplicativo estão em seu README](https://github.com/johnpapa/ng-demos/tree/master/modular).

> **Nota de tradução**: Os títulos originais de cada seção será mantido, pois caso você queira buscar mais sobre estes assuntos futuramente, fazendo tal busca em inglês será obtido um resultado **imensamente** melhor. 
>
> Após o título, estará a tradução auxiliar, quando necessária, visto que alguns termos são mais facilmente entendidos quando não traduzidos, por fazerem parte do núcleo do estudo em questão.
>
> Para eventuais erros de digitação e/ou tradução, favor enviar um pull-request! 

## Tabela de Conteúdo

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
ou *Responsabilidade Única*

### Regra nº 1

  - Defina um componente por arquivo.

  O exemplo seguinte define um módulo `app` e suas dependências, define um controller e define um factory, todos no mesmo arquivo.

  ```javascript
  /* evite */
  angular
    	.module('app', ['ngRoute'])
    	.controller('SomeController' , SomeController)
    	.factory('someFactory' , someFactory);
  	
  function SomeController() { }

  function someFactory() { }
  ```

  Os mesmos componentes agora estão separados em seus próprios arquivos.

  ```javascript
  /* recomendado */
  
  // app.module.js
  angular
    	.module('app', ['ngRoute']);
  ```

  ```javascript
  /* recomendado */
  
  // someController.js
  angular
    	.module('app')
    	.controller('SomeController' , SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* recomendado */
  
  // someFactory.js
  angular
    	.module('app')
    	.factory('someFactory' , someFactory);
  	
  function someFactory() { }
  ```

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## IIFE
### JavaScript Closures

  - Envolva os componentes AngularJS em uma *Immediately Invoked Function Expression (IIFE - Expressão de função imediatamente invocada)*.

  **Por que?** Uma IIFE remove as variáveis do escopo global. Isso ajuda a prevenir declarações de variáveis e funções de viverem por mais tempo que o esperado no escopo global, que também auxilia evitar colisões de variáveis.

  **Por que?** Quando seu código é minificado e empacotado dentro de um único arquivo para *deployment* no servidor de produção, você pode ter conflitos de variáveis e muitas variáveis globais. Uma IIFE o protege em todos estes aspectos provendo escopo de variável para cada arquivo.

  ```javascript
  /* evite */
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  // função logger é adicionada como uma variável global
  function logger() { }

  // storage.js
  angular
      .module('app')
      .factory('storage', storage);

  // função storage é adicionada como uma variável global
  function storage() { }
  ```

  
  ```javascript
  /**
   * recomendado 
   *
   * nenhuma global é deixada para trás 
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

  - **Nota**: Apenas para agilizar, o resto dos exemplos neste guia omitirão a sintaxe IIFE. 

  - **Nota**: IIFE impede que códigos de teste alcancem membros privados como expressões regulares ou funções auxiliares que são frequentemente boas para testes unitários. Entretanto, você pode testá-las através de membros acessíveis ou expondo-os pelo próprio componente. Por exemplo, colocando funções auxiliares, expressões regulares ou constantes em sua própria *factory* ou constante. 

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Modules
ou *Módulos*

### Avoid Naming Collisions
ou *Evitando Colisão de Nomes*

  - Use uma única convenção de nomes com separadores para sub-módulos.

  **Por que?** Nomes únicos ajudam a evitar colisão de nomes no módulo. Separadores ajudam a definir a hierarquia de módulos e submódulos. Por exemplo, `app` pode ser seu módulo raiz, enquanto `app.dashboard` e `app.users` podem ser módulos que são usados como dependências de `app`. 

### Definições (*aka Setters*)

> ps: **aka** é o acrônimo de **A**lso **K**nown** **A**s, de forma traduzida, **também conhecido como**.

  - Declare os módulos sem uma variável usando a sintaxe *setter*.

  **Por que?** Com 1 componente por arquivo, raramente será necessário criar uma variável para o módulo.
	
  ```javascript
  /* evite */
  var app = angular.module('app', [
      'ngAnimate',
      'ngRoute',
      'app.shared',
      'app.dashboard'
  ]);
  ```

  Ao invés, use a simples sintaxe *setter*.

  ```javascript
  /* recomendado */
  angular
    	.module('app', [
          'ngAnimate',
          'ngRoute',
          'app.shared',
          'app.dashboard'
      ]);
  ```

### *Getters*

  - Quando usando um módulo, evite usar as variáveis e então use o encadeamento com a sintaxe *getter*.

  **Por que?** Isso produz um código mais legível e evita colisão de variáveis ou vazamentos.

  ```javascript
  /* evite */
  var app = angular.module('app');
  app.controller('SomeController' , SomeController);
  
  function SomeController() { }
  ```

  ```javascript
  /* recomendado */
  angular
      .module('app')
      .controller('SomeController' , SomeController);
  
  function SomeController() { }
  ```

### *Setting* vs *Getting* 
ou *Definindo* vs *Obtendo*

  - Apenas *set* (configure) uma vez e *get* (receba) em todas as outras instâncias.
	
  **Por que?** Um módulo deve ser criado somente uma vez, então recupere-o deste ponto em diante. 
  	  
	  - Use `angular.module('app', []);` para definir (*set*) um módulo.
	  - Use  `angular.module('app');` para pegar (*get*) este módulo. 

### Named vs Anonymous Functions
ou *unções Nomeadas vs Funções Anônimas*

  - Use funções nomeadas ao invés de passar uma função anônima como um callback. 

  **Por que?** Isso produz um código mais legível, é muito fácil de *debugar*, e reduz a quantidade de callbacks aninhados no código.

  ```javascript
  /* evite */
  angular
      .module('app')
      .controller('Dashboard', function() { });
      .factory('logger', function() { });
  ```

  ```javascript
  /* recomendado */

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Controllers
ou *Controladores*

### controllerAs View Syntax

  - Utilize a sintaxe [`controllerAs`](http://www.johnpapa.net/do-you-like-your-angular-controllers-with-or-without-sugar/) ao invés da sintaxe `clássica controller com $scope`. 

	**Por que?**: Controllers são construídos, "iniciados", e fornecem um nova instância única, e a sintaxe `controlerAs` é mais próxima de um construtor JavaScriptar do que a `sintaxe clássica do $scope`.

	**Por que?**: Isso promove o uso do binding de um objeto "pontuado", ou seja, com propriedades na View (ex. `customer.name` ao invés de `name`), que é mais contextual, legível, e evita qualquer problema com referências que podem ocorrer sem a "pontuação"

	**Por que?**: Ajuda a evitar o uso de chamadas ao `$parent` nas Views com controllers aninhados.

  ```html
  <!-- evite -->
  <div ng-controller="Customer">
      {{ name }}
  </div>
  ```

  ```html
  <!-- recomendado -->
  <div ng-controller="Customer as customer">
     {{ customer.name }}
  </div>
  ```

### controllerAs Controller Syntax

  - Utilize a sintaxe `controllerAs` ao invés invés da sintaxe `clássica controller com $scope`. 

  - A sintaxe `controllerAs` usa o `this` dentro dos controllers que fica ligado ao `$scope`.

  **Por que?**: O `controllerAs` é uma forma mais simples de lidar com o `$scope`. Você ainda poderá fazer o bind para a View e ainda poderá acessar os métodos do `$scope`.  

  **Por que?**: Ajuda a evitar a tentação de usar o métodos do `$scope` dentro de um controller quando seria melhor evitá-los ou movê-los para um factory. Considere utilizar o  `$scope` em um factory, ou em um controller apenas quando necessário. Por exemplo, quando publicar e subscrever eventos usando [`$emit`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$emit), [`$broadcast`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$broadcast), ou [`$on`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$on) considere mover estes casos para um factory e invocá-los a partir do controller.

  ```javascript
  /* evite */
  function Customer($scope) {
      $scope.name = {};
      $scope.sendMessage = function() { };
  }
  ```

  ```javascript
  /* recomendado - mas veja a próxima sessão */
  function Customer() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

### controllerAs with vm

  - Utilize uma variável de captura para o `this` quando usar a sintaxe `controllerAs`. Escolha um nome de variável consistente como `vm`, que representa o ViewModel.
  
  **Por  que?**: A palavra-chave `this` é contextual e quando usada em uma função dentro de um controller pode mudar seu contexto. Capturando o contexto do `this` evita a ocorrência deste problema.

  ```javascript
  /* evite */
  function Customer() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

  ```javascript
  /* recomendado */
  function Customer() {
      var vm = this;
      vm.name = {};
      vm.sendMessage = function() { };
  }
  ```

  Nota: Você pode evitar qualquer [jshint](http://www.jshint.com/) warnings colocando o comentário abaixo acima da linha de código. 
    
  ```javascript
  /* jshint validthis: true */
  var vm = this;
  ```
   
 Nota: Quando watches são criados no controller utilizando o `controller as`, você pode observar o objeto `vm.*` utilizando a seguinte sintaxe. (Crie watches com cuidado pois eles deixam o ciclo de digest mais "carregado".)

  ```javascript
  $scope.$watch('vm.title', function(current, original) {
      $log.info('vm.title was %s', original);
      $log.info('vm.title is now %s', current);
  });
  ```

### Bindable Members Up Top

  - Coloque os objetos que precisam de bind no início do controller, em ordem alfabética, e não espalhados através do código do controller.
  
    **Por que?**: Colocar os objetos que precisam de bind no início torna mais fácil de ler e te ajuda a instantaneamente identificar quais objetos do controller podem ser utilizados na View.

    **Por que?**: Setar funções anônimas pode ser fácil, mas quando essas funções possuem mais de 1 linha do código elas podem dificultar a legibilidade. Definir as funções abaixo dos objetos que necessitam de bind (as funções serão elevadas pelo JavaScript Hoisting) move os detalhes de implementação para o final do controller, mantém os objetos que necessitam de bind no topo, e deixa o código mais fácil de se ler.

  ```javascript
  /* evite */
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
  /* recomendado */
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

   Nota: Se a função possuir apenas 1 linha considere matê-la no topo, desde que a legibilidade não seja afetada.

  ```javascript
  /* evite */
  function Sessions(data) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = function() {
          /** 
           * linhas 
           * de
           * código
           * afetam
           * a
           * legibilidade
           */
      };
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

  ```javascript
  /* recomendado */
  function Sessions(dataservice) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = dataservice.refresh; // 1 linha está OK
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  ```

### Function Declarations to Hide Implementation Details

  - Utilize declarações de funções para esconder detalhes de implementação. Mantenha seus objetos que necessitam de bind no topo. Quando você precisar fazer o bind de uma função no controller, aponte ela para a declaração de função que aparece no final do arquivo. Ela está ligada diretamente aos objetos que precisam de bind no início do arquivo. Para mais detalhes veja [este post](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).
    
    **Por que?**: Colocar os objetos que precisam de bind no início torna mais fácil de ler e te ajuda a instantaneamente identificar quais objetos do controller podem ser utilizados na View. (Mesmo do item anterior.)

    **Por que?**: Colocar os detalhes de implementação de uma função no final do arquivo coloca a complexidade fora do foco, logo, você pode focar nas coisas importantes no topo.

    **Por que?**: Declarações de funções são içadas, logo, não existe problema de se utilizar uma função antes dela ser definida (como haveria com expressões de função).

    **Por que?**: Você nunca precisará se preocupar com declarações de funções quebrarem seu código por colocar `var a` antes de `var b` por que `a` depende de `b`.     

    **Por que?**: A ordenação é crítica em expressões de função.

  ```javascript
  /** 
   * evite 
   * Usar expressões de funções.
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

  Note-se que as coisas importantes estão espalhadas no exemplo anterior. No exemplo abaixo, nota-se que as coisas importantes do javascript estão logo no topo. Por exemplo, os objetos que precisam de bind no controller como `vm.avengers` e `vm.title`. Os detalhes de implementação estão abaixo. Isto é mais fácil de ler.

  ```javascript
  /*
   * recomendado
   * Usar declarações de funções
   * e objetos que precisam de bind no topo.
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

### Defer Controller Logic

  - Remova a lógica do controller delegando ela a services e factories.

    **Por que?**: A lógica pode ser reutilizada em múltiplos controllers quando colocada em um service e exposta através de uma função.

    **Por que?**: A lógica em um serviço pode ser mais facilmente isolada em um teste unitário, enquanto a lógica feita no controlador pode ser facilmente [mockada](http://www.thoughtworks.com/pt/insights/blog/mockists-are-dead-long-live-classicists).

    **Por que?**: Remove as dependências e esconde os detalhes de implementação do controlador.

  ```javascript
  /* evite */
  function Order($http, $q) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.total = 0;

      function checkCredit() { 
          var orderTotal = vm.total;
          return $http.get('api/creditcheck').then(function(data) {
              var remaining = data.remaining;
              return $q.when(!!(remaining > orderTotal));
          });
      };
  }
  ```

  ```javascript
  /* recomendado */
  function Order(creditService) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.total = 0;

      function checkCredit() { 
         return creditService.check();
      };
  }
  ```

### Keep Controllers Focused

  - Defina um controller para a view, e tente não reutilizar o controller para outras views. Ao invés disso, coloque as lógicas reaproveitáveis em factories e mantenha o controller simples e focado em sua view.
  
    **Por que?**: Reutilizar controllers em várias views é arriscado e um boa cobertura de testes end to end (e2e) é obrigatório para se garantir estabilidade em grandes aplicações.

### Assigning Controllers

  - Quando um controller deve ser pareado com sua view e algum componente pode ser reutilizado por outros controllers ou views, defina controllers juntamente de suas rotas. 
    
    Nota: Se uma View é carregada de outra forma que não seja através de uma rota, então utilize a sintaxe `ng-controller="Avengers as vm"`. 

    **Por que?**: Parear os controllers nas rotas permite diferentes rotas invocarem diferentes pares de controllers e views. Quando um controller é utilizado na view usando a sintaxe [`ng-controller`](https://docs.angularjs.org/api/ng/directive/ngController), esta view sempre será associada ao mesmo controller.

 ```javascript
  /* evite - quando utilizar com uma rota e emparelhamento dinâmico é desejado */

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
  /* recomendado */

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Services
ou *Serviços*

### Singletons

  - Services são instanciados com a palavra-chave `new`, use `this` para métodos públicos e variáveis. Services são bastante similares a factories, use um factory para consistência. 
  
    Nota: [Todos services em AngularJS são singletons](https://docs.angularjs.org/guide/services). Isso significa que há apenas uma instância do serviço para cada injetor.

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Factories
ou *Fábricas*

### Single Responsibility
ou *Responsabilidade Única*

  - Factories devem ter [responsabilidade única](http://en.wikipedia.org/wiki/Single_responsibility_principle), que é encapsulado pelo seu contexto. Assim que uma factory começa a exceder a proposta de singularidade, uma nova factory deve ser criada.

### Singletons

  - Factories são singletons e retornam um objeto que contém os membros do serviço.
  
    Nota: [Todos services em AngularJS são singletons](https://docs.angularjs.org/guide/services).

### Accessible Members Up Top
ou *Membros acessíveis no topo*

  - Exponha os membros que podem ser invocados no serviço (a interface) no topo, utilizando uma técnica derivada do [Revealing Module Pattern](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript). 

    **Por que?**: Colocando no topo os membros que podem ser invocados da factory, a leitura torna-se mais fácil e ajuda a identificar imediatamente quais membros da factory podem ser invocados e testados através de teste unitário (e/ou mock). 

    **Por que?**: É especialmente útil quando o arquivo torna-se muito longo e ajuda a evitar a necessidade de rolagem para ver o que é exposto.

    **Por que?**: Definir as funções conforme você escreve o código pode ser fácil, mas quando essas funções tem mais que 1 linha de código, elas podem reduzir a leitura e causar rolagem. Definir a interface no topo do que pode ser invocado da factory, torna a leitura mais fácil e mantém os detalhes de implementação mais abaixo.

  ```javascript
  /* evite */
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
  /* recomendado */
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

  Dessa forma, os bindings são espelhados através do objeto da interface da factory e os valores primitivos não podem ser atualizados sozinhos utilizando o revealing module pattern

    ![Factories Using "Above the Fold"](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/above-the-fold-2.png)

### Function Declarations to Hide Implementation Details
ou *Declarações de função para esconder detalhes de implementação*

  - Use function declarations (declarações de função) para esconder detalhes de implementação. Mantenha os membros acessíveis da factory no topo. Aponte as function declarations que aparecem posteriormente no arquivo. Para mais detalhes leia [esse post](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).

    **Por que?**: Colocando os membros acessíveis no topo, a leitura torna-se mais fácil e ajuda a identificar imediatamente quais membros da factory podem ser acessados externamente.

    **Por que?**: Colocando os detalhes de implementação da função posteriormente no arquivo move a complexidade para fora da visão, permitindo que você veja as coisas mais importantes no topo.

    **Por que?**: Function declarations (declarações de função) são içadas (hoisted) para que não hajam preocupações em utilizar uma função antes que ela seja definida (como haveria com function expressions (expressões de função)).

    **Por que?**: Você nunca deve se preocupar com function declaration (declarações de função) onde `var a` está antes de `var b` vai ou não quebrar o seu código porque `a` depende de `b`.     

    **Por que?**: A ordem é crítica com function expressions (expressões de função) 

  ```javascript
  /**
   * evite
   * Utilizando function expressions (expressões de função)
   */
   function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var getAvengers = function() {
         // detalhes de implementação
      };

      var getAvengerCount = function() {
          // detalhes de implementação
      };

      var getAvengersCast = function() {
         // detalhes de implementação
      };

      var prime = function() {
         // detalhes de implementação
      };

      var ready = function(nextPromises) {
          // detalhes de implementação
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
   * recomendado
   * Utilizando function declarations (declarações de função)
   * e membros acessíveis no topo.
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
         // detalhes de implementação
      }

      function getAvengerCount() {
          // detalhes de implementação
      }

      function getAvengersCast() {
         // detalhes de implementação
      }

      function prime() {
          // detalhes de implementação
      }

      function ready(nextPromises) {
          // detalhes de implementação
      }
  }
  ```

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Data Services
ou *Serviços de dados*

### Separate Data Calls
ou *Chamadas de dados separadas*

  - A lógica de refatoração (refactor) para operações com dados e interação com dados na factory. Faça serviços de dados responsáveis por chamadas XHR, armazenamento local (local storage), armazenamento em memória (stashing) ou outras operações com dados.

    **Por que?**: A responsabilidade dos controladores (controllers) é para a apresentação e coleta de informações da view. Eles não devem se importar como os dados são adquiridos, somente como "perguntar" por eles. Separar os serviços de dados (data services), move a lógica de como adquiri-los para o serviço e deixa o controlador (controller) mais simples e focado na view.

    **Por que?**: Isso torna mais fácil testar (mock ou real) as chamadas de dados quando estiver testando um controlador (controller) que utiliza um serviço de dados (data service).

    **Por que?**: A implementação de um serviço de dados (data service) pode ter um código bem específico para lidar com o repositório de dados. Isso pode incluir cabeçalhos (headers), como comunicar com os dados ou outros serviços, como $http. Separando a lógica de dados em um serviço, coloca toda a lógica somente em um local e esconde a implementação de consumidores de fora (talvez um controlador (controller)), tornado mais fácil mudar a implementação.

  ```javascript
  /* recomendado */

  // factory de serviço de dados (data service factory)
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
    
    Nota: O serviço de dados (data service) é chamado pelos consumidores, como um controlador (controller), escondendo a implementação dos consumidores, como mostrado abaixo.

  ```javascript
  /* recomendado */

  // controlador chamando uma factory de serviço de dados
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

### Return a Promise from Data Calls
ou *Retorne uma promessa de chamadas de dados*

  - Quando chamar um serviço de dados (data service) que retorna uma promessa (promise), como o $http, retorne uma promessa (promise) na função que está chamando também.

    **Por que?**: Você pode encandear as promessas (promises) juntas e definir ações após a promessa (promise) da chamada do dado ser completada, resolvendo ou rejeitando a promessa (promise).

  ```javascript
  /* recomendado */

  activate();

  function activate() {
      /**
       * Passo 1
       * Chame a função getAvengers para os dados
       * dos vingadores (avengers) e espere pela promessa (promise)
       */
      return getAvengers().then(function() {
          /**
           * Passo 4
           * Faça uma ação resolvendo a promessa (promise) finalizada
           */
          logger.info('Activated Avengers View');
      });
  }

  function getAvengers() {
        /**
         * Passo 2
         * Chame o serviço de dados (data service) e espere
         * pela promessa (promise)
         */
        return dataservice.getAvengers()
            .then(function(data) {
                /**
                 * Passo 3
                 * Atribua os dados e resolva a promessa (promise)
                 */
                vm.avengers = data;
                return vm.avengers;
        });
  }
  ```

    **[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Directives
ou *Diretivas*

### Limit 1 Per File
ou *Limite 1 por arquivo*

  - Crie uma diretiva (directive) por arquivo. Nomeie o arquivo pela diretiva.

    **Por que?**: É fácil misturar todas as diretivas em um arquivo, mas é difícil depois separá-las, já que algumas são compartilhadas entre aplicativos, outras pelos módulos (modules) e algumas somente para um módulo. 

    **Por que?**: Uma diretiva (directive) por arquivo é mais fácil de dar manutenção.

  ```javascript
  /* evite */
  /* directives.js */

  angular
      .module('app.widgets')

      /* diretiva de pedido (order) que é específica para o módulo de pedido */
      .directive('orderCalendarRange', orderCalendarRange)

      /* diretiva de vendas (sales) que pode ser usada em qualquer lugar do aplicativo de vendas */
      .directive('salesCustomerInfo', salesCustomerInfo)

      /* diretiva de spinner que pode ser usada em qualquer lugar dos aplicativos */
      .directive('sharedSpinner', sharedSpinner);


  function orderCalendarRange() {
      /* detalhes de implementação */
  }

  function salesCustomerInfo() {
      /* detalhes de implementação */
  }

  function sharedSpinner() {
      /* detalhes de implementação */
  }
  ```

  ```javascript
  /* recomendado */
  /* calendarRange.directive.js */

  /**
   * @desc diretiva de pedido (order) que é específica para o módulo de pedido em uma companhia chamada Acme
   * @example <div acme-order-calendar-range></div>
   */
  angular
      .module('sales.order')
      .directive('acmeOrderCalendarRange', orderCalendarRange);

  function orderCalendarRange() {
      /* detalhes de implementação */
  }
  ```

  ```javascript
  /* recomendado */
  /* customerInfo.directive.js */

  /**
   * @desc diretiva de spinner que pode ser usada em qualquer lugar de um aplicativo de vendas em uma companhia chamada Acme
   * @example <div acme-sales-customer-info></div>
   */    
  angular
      .module('sales.widgets')
      .directive('acmeSalesCustomerInfo', salesCustomerInfo);

  function salesCustomerInfo() {
      /* detalhes de implementação */
  }
  ```

  ```javascript
  /* recomendado */
  /* spinner.directive.js */

  /**
   * @desc diretiva de spinner que pode ser usada em qualquer lugar de aplicativo em uma companhia chamada Acme
   * @example <div acme-shared-spinner></div>
   */
  angular
      .module('shared.widgets')
      .directive('acmeSharedSpinner', sharedSpinner);

  function sharedSpinner() {
      /* detalhes de implementação */
  }
  ```

    Nota: Há diferentes opções de nomear diretivas (directives), especialmente quando elas podem ser usadas em escopes (scopes) variados. Escolha uma que faça a diretiva e o nome do arquivo distinto e simples. Alguns exemplos são mostrados abaixo, mas veja a seção de nomeação para mais recomendações.

### Limit DOM Manipulation
ou *Limite a manipulação do DOM*

  - Quando estiver manipulando o DOM diretamente, utilize uma diretiva (directive). Se formas alternativas podem ser utilizadas, como utilizar CSS para setar estilos ou [serviços de animação (animation services)](https://docs.angularjs.org/api/ngAnimate), Angular templating, [`ngShow`](https://docs.angularjs.org/api/ng/directive/ngShow) ou [`ngHide`](https://docs.angularjs.org/api/ng/directive/ngHide), então prefira utilizá-los. Por exemplo, se uma diretiva simplesmente esconde ou mostra um elemento, use ngHide/ngShow. 

    **Por que?**: A manipulação do DOM pode ser difícil de testar, debugar, e há melhores maneiras (ex: CSS, animações (animations), templates).

### Provide a Unique Directive Prefix
ou *Forneça um prefixo único para as diretivas*

  - Forneça um curto, único e descritivo prefixo para a diretiva, como `acmeSalesCustomerInfo`, que é declarado no HTML como `acme-sales-customer-info`.

    **Por que?**: Um prefixo curto e único identifica o contexto e a origem da diretiva. Por exemplo, o prefixo `cc-` pode indicar que a diretiva é parte de um aplicativo da CodeCamper, enquanto a diretiva `acme-` pode indicar uma diretiva para a companhia Acme. 

    Nota: Evite `ng-`, pois são reservadas para as diretivas do AngularJS. Pesquisa largamente as diretivas utilizadas para evitar conflitos de nomes, como `ion-` que são utilizadas para o [Ionic Framework](http://ionicframework.com/). 

### Restrict to Elements and Attributes
ou *Restringir para elementos e atributos*

  - Quando criar uma diretiva que faça sentido por si só como um elemento, utilize restrição `E` (elemento personalizado) e opcionalmente `A` (atributo personalizado). Geralmente, se ela pode ter o seu próprio controlador (controller), `E` é o apropriado. Em linhas gerais, `EA` é permitido, mas atente para a implementação como elemento quando faz sentido por si só e como atributo quando estende algo existente no DOM.

    **Por que?**: Faz sentido.

    **Por que?**: Nós podemos utilizar uma diretiva como uma classe (class), mas se a diretiva está realmente agindo como um elemento, faz mais sentido utilizar como um elemento, ou pelo menos como um atributo.

    Nota: EA é o padrão para o AngularJS 1.3 +

  ```html
  <!-- evite -->
  <div class="my-calendar-range"></div>
  ```

  ```javascript
  /* evite */
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
  <!-- recomendado -->
  <my-calendar-range></my-calendar-range>
  <div my-calendar-range></div>
  ```
  
  ```javascript
  /* recomendado */
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

### Directives and ControllerAs
ou *Diretivas e "ControladorComo"*

  - Utilize a sintaxe `controller as` com uma diretiva para consistência com o uso de `controller as` com os pares view e controlador (controller).

    **Por que?**: Faz sentido e não é difícil.

    Nota: A diretiva (directive) abaixo demonstra algumas maneiras que você pode utilizar escopos (scopes) dentro de link e controller de uma diretiva, utilizando controllerAs. Eu coloquei o template somente para manter tudo em um mesmo local. 

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
          controller : ExampleController,
          controllerAs: 'vm'
      };
      return directive;

      ExampleController.$inject = ['$scope'];
      function ExampleController($scope) {
          // Injetando $scope somente para comparação
          /* jshint validthis:true */
          var vm = this;

          vm.min = 3; 
          vm.max = $scope.max; 
          console.log('CTRL: $scope.max = %i', $scope.max);
          console.log('CTRL: vm.min = %i', vm.min);
          console.log('CTRL: vm.max = %i', vm.max);
      }

      function linkFunc(scope, el, attr, ctrl) {
          console.log('LINK: scope.max = %i', scope.max);
          console.log('LINK: scope.vm.min = %i', scope.vm.min);
          console.log('LINK: scope.vm.max = %i', scope.vm.max);
      }
  }
  ```

  ```html
  /* example.directive.html */
  <div>hello world</div>
  <div>max={{vm.max}}<input ng-model="vm.max"/></div>
  <div>min={{vm.min}}<input ng-model="vm.min"/></div>
  ```

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Resolving Promises for a Controller
ou *Resolvendo promessas para um controlador*

### Controller Activation Promises
ou *Ativação de promessas no controlador*

  - Resolva a lógica de inicialização no controlador (controller) em uma função `iniciar`.
     
    **Por que?**: Colocando a lógica de inicialização em um lugar consistente no controlador (controller), torna mais fácil de localizar, mais consistente para testar e ajuda a evitar o espalhamento da lógica de inicialização pelo controlador (controller).

    Nota: Se vocẽ precisa cancelar a rota condicionalmente antes de utilizar o controlador (controller), utilize uma resolução de rota (route resolve).
    
  ```javascript
  /* evite */
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
  /* recomendado */
  function Avengers(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      iniciar();

      ////////////

      function iniciar() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Route Resolve Promises
ou *Resolução de promessas na rota*

  - Quando o controlador (controller) depende de uma promessa ser resolvida, resolva as dependências no `$routeProvider` antes da lógica do controlador (controller) ser executada. Se vocẽ precisa cancelar a rota condicionalmente antes do controlador (controller) ser ativado, utilize uma resolução de rota (route resolve).

    **Por que?**: Um controlador (controller) pode precisar de dados antes de ser carregado. Esses dados podem vir de uma promessa (promise) através de uma factory personalizada ou [$http](https://docs.angularjs.org/api/ng/service/$http). Utilizando [resolução de rota (route resolve)](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider) permite as promessas (promises) serem resolvidas antes da lógica do controlador (controller) ser executada, então ele pode executar ações através dos dados dessa promessa (promise).

  ```javascript
  /* evite */
  angular
      .module('app')
      .controller('Avengers', Avengers);

  function Avengers(movieService) {
      var vm = this;
      // não resolvida
      vm.movies;
      // resolvida assíncrona
      movieService.getMovies().then(function(response) {
          vm.movies = response.movies;
      });
  }
  ```

  ```javascript
  /* melhor */

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
        /* jshint validthis:true */
        var vm = this;
        vm.movies = moviesPrepService.movies;
  }
  ```

    Nota: As dependências no código de exemplos do `movieService` não estão seguras da minificação. Para mais detalhes de como fazer o código minificado seguro, veja as seções [injeção de dependência (dependency injection)](#manual-annotating-for-dependency-injection) e [minificação e anotação (minification and annotation)](#minification-and-annotation).

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Manual Annotating for Dependency Injection

### UnSafe from Minification

  - Avoid using the shortcut syntax of declaring dependencies without using a minification-safe approach.
  
    *Why?*: The parameters to the component (e.g. controller, factory, etc) will be converted to mangled variables. For example, `common` and `dataservice` may become `a` or `b` and not be found by AngularJS.

    ```javascript
    /* avoid - not minification-safe*/
    angular
        .module('app')
        .controller('Dashboard', Dashboard);

    function Dashboard(common, dataservice) {
    }
    ```

    This code may produce mangled variables when minified and thus cause runtime errors.

    ```javascript
    /* avoid - not minification-safe*/
    angular.module('app').controller('Dashboard', d);function d(a, b) { }
    ```

### Manually Identify Dependencies

  - Use `$inject` to manually identify your dependencies for AngularJS components.
  
    *Why?*: This technique mirrors the technique used by [`ng-annotate`](https://github.com/olov/ng-annotate), which I recommend for automating the creation of minification safe dependencies. If `ng-annotate` detects injection has already been made, it will not duplicate it.

    *Why?*: This safeguards your dependencies from being vulnerable to minification issues when parameters may be mangled. For example, `common` and `dataservice` may become `a` or `b` and not be found by AngularJS.

    *Why?*: Avoid creating in-line dependencies as long lists can be difficult to read in the array. Also it can be confusing that the array is a series of strings while the last item is the component's function. 

    ```javascript
    /* avoid */
    angular
        .module('app')
        .controller('Dashboard', 
            ['$location', '$routeParams', 'common', 'dataservice', 
                function Dashboard($location, $routeParams, common, dataservice) {}
            ]);      
    ```

    ```javascript
    /* avoid */
    angular
      .module('app')
      .controller('Dashboard', 
         ['$location', '$routeParams', 'common', 'dataservice', Dashboard]);
      
    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    ```javascript
    /* recommended */
    angular
        .module('app')
        .controller('Dashboard', Dashboard);

    Dashboard.$inject = ['$location', '$routeParams', 'common', 'dataservice'];
      
    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    Note: When your function is below a return statement the $inject may be unreachable (this may happen in a directive). You can solve this by either moving the $inject above the return statement or by using the alternate array injection syntax. 

    Note: [`ng-annotate 0.10.0`](https://github.com/olov/ng-annotate) introduced a feature where it moves the `$inject` to where it is reachable.

    ```javascript
    // inside a directive definition
    function outer() {
        return {
            controller: DashboardPanel,
        };

        DashboardPanel.$inject = ['logger']; // Unreachable
        function DashboardPanel(logger) {
        }
    }
    ```

    ```javascript
    // inside a directive definition
    function outer() {
        DashboardPanel.$inject = ['logger']; // reachable
        return {
            controller: DashboardPanel,
        };

        function DashboardPanel(logger) {
        }
    }
    ```

### Manually Identify Route Resolver Dependencies

  - Use $inject to manually identify your route resolver dependencies for AngularJS components.
  
    *Why?*: This technique breaks out the anonymous function for the route resolver, making it easier to read.

    *Why?*: An `$inject` statement can easily precede the resolver to handle making any dependencies minification safe.

    ```javascript
    /* recommended */
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

    moviePrepService.$inject =  ['movieService'];
    function moviePrepService(movieService) {
        return movieService.getMovies();
    }
    ```

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Minification and Annotation

### ng-annotate

  - Use [ng-annotate](//github.com/olov/ng-annotate) for [Gulp](http://gulpjs.com) or [Grunt](http://gruntjs.com) and comment functions that need automated dependency injection using `/** @ngInject */`
  
    *Why?*: This safeguards your code from any dependencies that may not be using minification-safe practices.

    *Why?*: [`ng-min`](https://github.com/btford/ngmin) is deprecated 

    >I prefer Gulp as I feel it is easier to write, to read, and to debug.

    The following code is not using minification safe dependencies.

    ```javascript
    angular
        .module('app')
        .controller('Avengers', Avengers);

    /* @ngInject */
    function Avengers(storageService, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero(){
            var hero = avengerService.find(vm.heroSearch);
            storageService.save(hero.name, hero);
        }
    }
    ```

    When the above code is run through ng-annotate it will produce the following output with the `$inject` annotation and become minification-safe.

    ```javascript
    angular
        .module('app')
        .controller('Avengers', Avengers);

    /* @ngInject */
    function Avengers(storageService, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero(){
            var hero = avengerService.find(vm.heroSearch);
            storageService.save(hero.name, hero);
        }
    }

    Avengers.$inject = ['storageService', 'avengerService'];
    ```

    Note: If `ng-annotate` detects injection has already been made (e.g. `@ngInject` was detected), it will not duplicate the `$inject` code.

    Note: When using a route resolver you can prefix the resolver's function with `/* @ngInject */` and it will produce properly annotated code, keeping any injected dependencies minification safe.

    ```javascript
    // Using @ngInject annotations
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

    > Note: Starting from AngularJS 1.3 use the [`ngApp`](https://docs.angularjs.org/api/ng/directive/ngApp) directive's `ngStrictDi` parameter. When present the injector will be created in "strict-di" mode causing the application to fail to invoke functions which do not use explicit function annotation (these may not be minification safe). Debugging info will be logged to the console to help track down the offending code.
    `<body ng-app="APP" ng-strict-di>`

### Use Gulp or Grunt for ng-annotate

  - Use [gulp-ng-annotate](https://www.npmjs.org/package/gulp-ng-annotate) or [grunt-ng-annotate](https://www.npmjs.org/package/grunt-ng-annotate) in an automated build task. Inject `/* @ngInject */` prior to any function that has dependencies.
  
    *Why?*: ng-annotate will catch most dependencies, but it sometimes requires hints using the `/* @ngInject */` syntax.

    The following code is an example of a gulp task using ngAnnotate

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Exception Handling

### decorators

  - Use a [decorator](https://docs.angularjs.org/api/auto/service/$provide#decorator), at config time using the [`$provide`](https://docs.angularjs.org/api/auto/service/$provide) service, on the [`$exceptionHandler`](https://docs.angularjs.org/api/ng/service/$exceptionHandler) service to perform custom actions when exceptions occur.
  
    *Why?*: Provides a consistent way to handle uncaught AngularJS exceptions for development-time or run-time.

    Note: Another option is to override the service instead of using a decorator. This is a fine option, but if you want to keep the default behavior and extend it a decorator is recommended.

  	```javascript
    /* recommended */
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
             * Could add the error to a service's collection,
             * add errors to $rootScope, log errors to remote web server,
             * or log locally. Or throw hard. It is entirely up to you.
             * throw exception;
             */
            toastr.error(exception.msg, errorData);
        };
    }
  	```

### Exception Catchers

  - Create a factory that exposes an interface to catch and gracefully handle exceptions.

    *Why?*: Provides a consistent way to catch exceptions that may be thrown in your code (e.g. during XHR calls or promise failures).

    Note: The exception catcher is good for catching and reacting to specific exceptions from calls that you know may throw one. For example, when making an XHR call to retrieve data from a remote web service and you want to catch any exceptions from that service and react uniquely.

    ```javascript
    /* recommended */
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

### Route Errors

  - Handle and log all routing errors using [`$routeChangeError`](https://docs.angularjs.org/api/ngRoute/service/$route#$routeChangeError).

    *Why?*: Provides a consistent way handle all routing errors.

    *Why?*: Potentially provides a better user experience if a routing error occurs and you route them to a friendly screen with more details or  recovery options.

    ```javascript
    /* recommended */
    function handleRoutingErrors() {
        /**
         * Route cancellation:
         * On routing error, go to the dashboard.
         * Provide an exit clause if it tries to do it twice.
         */
        $rootScope.$on('$routeChangeError',
            function(event, current, previous, rejection) {
                var destination = (current && (current.title || current.name || current.loadedTemplateUrl)) ||
                    'unknown target';
                var msg = 'Error routing to ' + destination + '. ' + (rejection.msg || '');
                /**
                 * Optionally log using a custom service or $log.
                 * (Don't forget to inject custom service)
                 */
                logger.warning(msg, [current]);
            }
        );
    }
    ```

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Naming

### Naming Guidelines

  - Use consistent names for all components following a pattern that describes the component's feature then (optionally) its type. My recommended pattern is `feature.type.js`. There are 2 names for most assets:
    *   the file name (`avengers.controller.js`)
    *   the registered component name with Angular (`AvengersController`)
 
    *Why?*: Naming conventions help provide a consistent way to find content at a glance. Consistency within the project is vital. Consistency with a team is important. Consistency across a company provides tremendous efficiency.

    *Why?*: The naming conventions should simply help you find your code faster and make it easier to understand. 

### Feature File Names

  - Use consistent names for all components following a pattern that describes the component's feature then (optionally) its type. My recommended pattern is `feature.type.js`.

    *Why?*: Provides a consistent way to quickly identify components.

    *Why?*: Provides pattern matching for any automated tasks.

    ```javascript
    /**
     * common options 
     */

    // Controllers
    avengers.js
    avengers.controller.js
    avengersController.js

    // Services/Factories
    logger.js
    logger.service.js
    loggerService.js
    ```

    ```javascript
    /**
     * recommended
     */

    // controllers
    avengers.controller.js
    avengers.controller.spec.js

    // services/factories
    logger.service.js
    logger.service.spec.js

    // constants
    constants.js
    
    // module definition
    avengers.module.js

    // routes
    avengers.routes.js
    avengers.routes.spec.js

    // configuration
    avengers.config.js
    
    // directives
    avenger-profile.directive.js
    avenger-profile.directive.spec.js
    ```

  Note: Another common convention is naming controller files without the word `controller` in the file name such as `avengers.js` instead of `avengers.controller.js`. All other conventions still hold using a suffix of the type. Controllers are the most common type of component so this just saves typing and is still easily identifiable. I recommend you choose 1 convention and be consistent for your team.

    ```javascript
    /**
     * recommended
     */
    // Controllers
    avengers.js
    avengers.spec.js
    ```

### Test File Names

  - Name test specifications similar to the component they test with a suffix of `spec`.  

    *Why?*: Provides a consistent way to quickly identify components.

    *Why?*: Provides pattern matching for [karma](http://karma-runner.github.io/) or other test runners.

    ```javascript
    /**
     * recommended
     */
    avengers.controller.spec.js
    logger.service.spec.js
    avengers.routes.spec.js
    avenger-profile.directive.spec.js
    ```

### Controller Names

  - Use consistent names for all controllers named after their feature. Use UpperCamelCase for controllers, as they are constructors.

    *Why?*: Provides a consistent way to quickly identify and reference controllers.

    *Why?*: UpperCamelCase is conventional for identifying object that can be instantiated using a constructor.

    ```javascript
    /**
     * recommended
     */

    // avengers.controller.js
    angular
        .module
        .controller('HeroAvengers', HeroAvengers);

    function HeroAvengers(){ }
    ```
    
### Controller Name Suffix

  - Append the controller name with the suffix `Controller` or with no suffix. Choose 1, not both.

    *Why?*: The `Controller` suffix is more commonly used and is more explicitly descriptive.

    *Why?*: Omitting the suffix is more succinct and the controller is often easily identifiable even without the suffix.

    ```javascript
    /**
     * recommended: Option 1
     */

    // avengers.controller.js
    angular
        .module
        .controller('Avengers', Avengers);

    function Avengers(){ }
    ```

    ```javascript
    /**
     * recommended: Option 2
     */

    // avengers.controller.js
    angular
        .module
        .controller('AvengersController', AvengersController);

    function AvengersController(){ }
    ```

### Factory Names

  - Use consistent names for all factories named after their feature. Use camel-casing for services and factories.

    *Why?*: Provides a consistent way to quickly identify and reference factories.

    ```javascript
    /**
     * recommended
     */

    // logger.service.js
    angular
        .module
        .factory('logger', logger);

    function logger(){ }
    ```

### Directive Component Names

  - Use consistent names for all directives using camel-case. Use a short prefix to describe the area that the directives belong (some example are company prefix or project prefix).

    *Why?*: Provides a consistent way to quickly identify and reference components.

    ```javascript
    /**
     * recommended
     */

    // avenger.profile.directive.js    
    angular
        .module
        .directive('xxAvengerProfile', xxAvengerProfile);

    // usage is <xx-avenger-profile> </xx-avenger-profile>

    function xxAvengerProfile(){ }
    ```

### Modules

  -  When there are multiple modules, the main module file is named `app.module.js` while other dependent modules are named after what they represent. For example, an admin module is named `admin.module.js`. The respective registered module names would be `app` and `admin`. A single module app might be named `app.js`, omitting the module moniker.

    *Why?*: An app with 1 module is named `app.js`. It is the app, so why not be super simple.
 
    *Why?*: Provides consistency for multiple module apps, and for expanding to large applications.

    *Why?*: Provides easy way to use task automation to load all module definitions first, then all other angular files (for bundling).

### Configuration

  - Separate configuration for a module into its own file named after the module. A configuration file for the main `app` module is named `app.config.js` (or simply `config.js`). A configuration for a module named `admin.module.js` is named `admin.config.js`.

    *Why?*: Separates configuration from module definition, components, and active code.

    *Why?*: Provides a identifiable place to set configuration for a module.

### Routes

  - Separate route configuration into its own file. Examples might be `app.route.js` for the main module and `admin.route.js` for the `admin` module. Even in smaller apps I prefer this separation from the rest of the configuration. An alternative is a longer name such as `admin.config.route.js`.

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Application Structure LIFT Principle
### LIFT

  - Structure your app such that you can `L`ocate your code quickly, `I`dentify the code at a glance, keep the `F`lattest structure you can, and `T`ry to stay DRY. The structure should follow these 4 basic guidelines. 

    *Why LIFT?*: Provides a consistent structure that scales well, is modular, and makes it easier to increase developer efficiency by finding code quickly. Another way to check your app structure is to ask yourself: How quickly can you open and work in all of the related files for a feature?

    When I find my structure is not feeling comfortable, I go back and revisit these LIFT guidelines
  
    1. `L`ocating our code is easy
    2. `I`dentify code at a glance
    3. `F`lat structure as long as we can
    4. `T`ry to stay DRY (Don’t Repeat Yourself) or T-DRY

### Locate

  - Make locating your code intuitive, simple and fast.

    *Why?*: I find this to be super important for a project. If the team cannot find the files they need to work on quickly,  they will not be able to work as efficiently as possible, and the structure needs to change. You may not know the file name or where its related files are, so putting them in the most intuitive locations and near each other saves a ton of time. A descriptive folder structure can help with this.

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

### Identify

  - When you look at a file you should instantly know what it contains and represents.

    *Why?*: You spend less time hunting and pecking for code, and become more efficient. If this means you want longer file names, then so be it. Be descriptive with file names and keeping the contents of the file to exactly 1 component. Avoid files with multiple controllers, multiple services, or a mixture. There are deviations of the 1 per file rule when I have a set of very small features that are all related to each other, they are still easily identifiable.

### Flat

  - Keep a flat folder structure as long as possible. When you get to 7+ files, begin considering separation.

    *Why?*: Nobody wants to search 7 levels of folders to find a file. Think about menus on web sites … anything deeper than 2 should take serious consideration. In a folder structure there is no hard and fast number rule, but when a folder has 7-10 files, that may be time to create subfolders. Base it on your comfort level. Use a flatter structure until there is an obvious value (to help the rest of LIFT) in creating a new folder.

### T-DRY (Try to Stick to DRY)

  - Be DRY, but don't go nuts and sacrifice readability.

    *Why?*: Being DRY is important, but not crucial if it sacrifices the others in LIFT, which is why I call it T-DRY. I don’t want to type session-view.html for a view because, well, it’s obviously a view. If it is not obvious or by convention, then I name it. 

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Application Structure

### Overall Guidelines

  -  Have a near term view of implementation and a long term vision. In other words, start small and but keep in mind on where the app is heading down the road. All of the app's code goes in a root folder named `app`. All content is 1 feature per file. Each controller, service, module, view is in its own file. All 3rd party vendor scripts are stored in another root folder and not in the `app` folder. I didn't write them and I don't want them cluttering my app (`bower_components`, `scripts`, `lib`).

    Note: Find more details and reasoning behind the structure at [this original post on application structure](http://www.johnpapa.net/angular-app-structuring-guidelines/).

### Layout

  - Place components that define the overall layout of the application in a folder named `layout`. These may include a shell view and controller may act as the container for the app, navigation, menus, content areas, and other regions. 

    *Why?*: Organizes all layout in a single place re-used throughout the application.

### Folders-by-Feature Structure

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
            dataservice.js  
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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Modularity
  
### Many Small, Self Contained Modules

  - Create small modules that encapsulate one responsibility.

    *Why?*: Modular applications make it easy to plug and go as they allow the development teams to build vertical slices of the applications and roll out incrementally.  This means we can plug in new features as we develop them.

### Create an App Module

  - Create an application root module whose role is pull together all of the modules and features of your application. Name this for your application.

    *Why?*: AngularJS encourages modularity and separation patterns. Creating an application root module whose role is to tie your other modules together provides a very straightforward way to add or remove modules from your application.

### Keep the App Module Thin

  - Only put logic for pulling together the app in the application module. Leave features in their own modules.

    *Why?*: Adding additional roles to the application root to get remote data, display views, or other logic not related to pulling the app together muddies the app module and make both sets of features harder to reuse or turn off.

### Feature Areas are Modules

  - Create modules that represent feature areas, such as layout, reusable and shared services, dashboards, and app specific features (e.g. customers, admin, sales).

    *Why?*: Self contained modules can be added to the application will little or no friction.

    *Why?*: Sprints or iterations can focus on feature areas and turn them on at the end of the sprint or iteration.

    *Why?*: Separating feature areas into modules makes it easier to test the modules in isolation and reuse code. 

### Reusable Blocks are Modules

  - Create modules that represent reusable application blocks for common services such as exception handling, logging, diagnostics, security, and local data stashing.

    *Why?*: These types of features are needed in many applications, so by keeping them separated in their own modules they can be application generic and be reused across applications.

### Module Dependencies

  - The application root module depends on the app specific feature modules, the feature modules have no direct dependencies, the cross-application modules depend on all generic modules.

    ![Modularity and Dependencies](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/modularity-1.png)

    *Why?*: The main app module contains a quickly identifiable manifest of the application's features. 

    *Why?*: Cross application features become easier to share. The features generally all rely on the same cross application modules, which are consolidated in a single module (`app.core` in the image).

    *Why?*: Intra-App features such as shared data services become easy to locate and share from within `app.core` (choose your favorite name for this module).

    Note: This is a strategy for consistency. There are many good options here. Choose one that is consistent, follows AngularJS's dependency rules, and is easy to maintain and scale.

    > My structures vary slightly between projects but they all follow these guidelines for structure and modularity. The implementation may vary depending on the features and the team. In other words, don't get hung up on an exact like-for-like structure but do justify your structure using consistency, maintainability, and efficiency in mind. 

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Angular $ Wrapper Services

### $document and $window

  - Use [`$document`](https://docs.angularjs.org/api/ng/service/$document) and [`$window`](https://docs.angularjs.org/api/ng/service/$window) instead of `document` and `window`.

    *Why?*: These services are wrapped by Angular and more easily testable than using document and window in tests. This helps you avoid having to mock document and window yourself.

### $timeout and $interval

  - Use [`$timeout`](https://docs.angularjs.org/api/ng/service/$timeout) and [`$interval`](https://docs.angularjs.org/api/ng/service/$interval) instead of `setTimeout` and `setInterval` .

    *Why?*: These services are wrapped by Angular and more easily testable and handle AngularJS's digest cycle thus keeping data binding in sync.

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Testing
Unit testing helps maintain clean code, as such I included some of my recommendations for unit testing foundations with links for more information.

### Write Tests with Stories

  - Write a set of tests for every story. Start with an empty test and fill them in as you write the code for the story.

    *Why?*: Writing the test descriptions helps clearly define what your story will do, will not do, and how you can measure success.

    ```javascript
    it('should have Avengers controller', function() {
        //TODO
    });

    it('should find 1 Avenger when filtered by name', function() {
        //TODO
    });

    it('should have 10 Avengers', function() {}
        //TODO (mock data?)
    });

    it('should return Avengers via XHR', function() {}
        //TODO ($httpBackend?)
    });

    // and so on
    ```

### Testing Library

  - Use [Jasmine](http://jasmine.github.io/) or [Mocha](http://visionmedia.github.io/mocha/) for unit testing.

    *Why?*: Both Jasmine and Mocha are widely used in the AngularJS community. Both are stable, well maintained, and provide robust testing features.

    Note: When using Mocha, also consider choosing an assert library such as [Chai](http://chaijs.com).

### Test Runner

  - Use [Karma](http://karma-runner.github.io) as a test runner.

    *Why?*: Karma is easy to configure to run once or automatically when you change your code.

    *Why?*: Karma hooks into your Continuous Integration process easily on its own or through Grunt or Gulp.

    *Why?*: Some IDE's are beginning to integrate with Karma, such as [WebStorm](http://www.jetbrains.com/webstorm/) and [Visual Studio](http://visualstudiogallery.msdn.microsoft.com/02f47876-0e7a-4f6c-93f8-1af5d5189225).

    *Why?*: Karma works well with task automation leaders such as [Grunt](http://www.gruntjs.com) (with [grunt-karma](https://github.com/karma-runner/grunt-karma)) and [Gulp](http://www.gulpjs.com) (with [gulp-karma](https://github.com/lazd/gulp-karma)).

### Stubbing and Spying

  - Use Sinon for stubbing and spying.

    *Why?*: Sinon works well with both Jasmine and Mocha and extends the stubbing and spying features they offer.

    *Why?*: Sinon makes it easier to toggle between Jasmine and Mocha, if you want to try both.

### Headless Browser

  - Use [PhantomJS](http://phantomjs.org/) to run your tests on a server.

    *Why?*: PhantomJS is a headless browser that helps run your tests without needing a "visual" browser. So you do not have to install Chrome, Safari, IE, or other browsers on your server. 

    Note: You should still test on all browsers in your environment, as appropriate for your target audience.

### Code Analysis

  - Run JSHint on your tests. 

    *Why?*: Tests are code. JSHint can help identify code quality issues that may cause the test to work improperly.

### Alleviate Globals for JSHint Rules on Tests

  - Relax the rules on your test code to allow for common globals such as `describe` and `expect`.

    *Why?*: Your tests are code and require the same attention and code quality rules as all of your production code. However, global variables used by the testing framework, for example, can be relaxed by including this in your test specs.

    ```javascript
    /* global sinon, describe, it, afterEach, beforeEach, expect, inject */
    ```

  ![Testing Tools](https://raw.githubusercontent.com/johnpapa/angularjs-styleguide/master/assets/testing-tools.png)

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Animations

### Usage

  - Use subtle [animations with AngularJS](https://docs.angularjs.org/guide/animations) to transition between states for views and primary visual elements. Include the [ngAnimate module](https://docs.angularjs.org/api/ngAnimate). The 3 keys are subtle, smooth, seamless.

    *Why?*: Subtle animations can improve User Experience when used appropriately.

    *Why?*: Subtle animations can improve perceived performance as views transition.

### Sub Second

  - Use short durations for animations. I generally start with 300ms and adjust until appropriate.  

    *Why?*: Long animations can have the reverse affect on User Experience and perceived performance by giving the appearance of a slow application.

### animate.css

  - Use [animate.css](http://daneden.github.io/animate.css/) for conventional animations.

    *Why?*: The animations that animate.css provides are fast, smooth, and easy to add to your application.

    *Why?*: Provides consistency in your animations.

    *Why?*: animate.css is widely used and tested.

    Note: See this [great post by Matias Niemelä on AngularJS animations](http://www.yearofmoo.com/2013/08/remastered-animation-in-angularjs-1-2.html)

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Comments

### jsDoc

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## JS Hint

### Use an Options File

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## Constants

*ou Constantes*

### Globais de terceiros (*vendors*)

  - Cria uma *Constant* no AngularJS para variáveis globais de bibliotecas de terceiros.

    *Por que?*: Fornece uma forma de injetar bibliotecas de terceiros que de outra forma seriam globais. Isso melhora a testabilidade do código permitindo a você conhecer mais facilmente quais dependências os seus componentes têm (evita vazamento de abstrações). Também permite que você simule estas dependências, o que faz sentido. 

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## File Templates and Snippets
Use file templates or snippets to help follow consistent styles and patterns. Here are templates and/or snippets for some of the web development editors and IDEs.

### Sublime Text

  - AngularJS snippets that follow these styles and guidelines. 

    - Download the [Sublime Angular snippets](assets/sublime-angular-snippets.zip) 
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

  - AngularJS file templates that follow these styles and guidelines can be found at [SideWaffle](http://www.sidewaffle.com)

    - Download the [SideWaffle](http://www.sidewaffle.com) Visual Studio extension (vsix file)
    - Run the vsix file
    - Restart Visual Studio

### WebStorm

  - AngularJS snippets and file templates that follow these styles and guidelines. You can import them into your WebStorm settings:

    - Download the [WebStorm AngularJS file templates and snippets](assets/webstorm-angular-file-template.settings.jar) 
    - Open WebStorm and go to the `File` menu
    - Choose the `Import Settings` menu option
    - Select the file and click `OK`
    - In a JavaScript file type these commands followed by a `TAB`:

    ```javascript
    ng-c // creates an Angular controller
    ng-f // creates an Angular factory
    ng-m // creates an Angular module
    ```

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**

## AngularJS docs
For anything else, API reference, check the [Angular documentation](//docs.angularjs.org/api).

## Contributing

Open an issue first to discuss potential changes/additions. If you have questions with the guide, feel free to leave them as issues in the repository. If you find a typo, create a pull request. The idea is to keep the content up to date and use github’s native feature to help tell the story with issues and PR’s, which are all searchable via google. Why? Because odds are if you have a question, someone else does too! You can learn more here at about how to contribute.

*By contributing to this repository you are agreeing to make your content available subject to the license of this repository.*

### Process
    1. Discuss the changes in an Issue. 
    1. Open a Pull Request, reference the issue, and explain the change and why it adds value.
    1. The Pull Request will be evaluated and either merged or declined.

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

**[⬆ De volta ao topo ⬆](#tabela-de-conte%C3%BAdo)**
