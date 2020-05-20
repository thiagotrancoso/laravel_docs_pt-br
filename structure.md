# Estrutura de diretórios

- [Introdução](#introduction)
- [O diretório Raiz](#the-root-directory)
    - [O diretório `app`](#the-root-app-directory)
    - [O diretório `bootstrap`](#the-bootstrap-directory)
    - [O diretório `config`](#the-config-directory)
    - [O diretório `database`](#the-database-directory)
    - [O diretório `public`](#the-public-directory)
    - [O diretório `resources`](#the-resources-directory)
    - [O diretório `routes`](#the-routes-directory)
    - [O diretório `storage`](#the-storage-directory)
    - [O diretório `tests`](#the-tests-directory)
    - [O diretório `vendor`](#the-vendor-directory)
- [O diretório App](#the-app-directory)
    - [O diretório `Broadcasting`](#the-broadcasting-directory)
    - [O diretório `Console`](#the-console-directory)
    - [O diretório `Events`](#the-events-directory)
    - [O diretório `Exceptions`](#the-exceptions-directory)
    - [O diretório `Http`](#the-http-directory)
    - [O diretório `Jobs`](#the-jobs-directory)
    - [O diretório `Listeners`](#the-listeners-directory)
    - [O diretório `Mail`](#the-mail-directory)
    - [O diretório `Notifications`](#the-notifications-directory)
    - [O diretório `Policies`](#the-policies-directory)
    - [O diretório `Providers`](#the-providers-directory)
    - [O diretório `Rules`](#the-rules-directory)

<a name="introduction"></a>
## Introdução

A estrutura padrão de aplicativos do Laravel visa fornecer um excelente ponto de partida para aplicativos grandes e pequenos. Mas você é livre para organizar seu aplicativo da maneira que desejar. O Laravel não impõe quase nenhuma restrição sobre onde uma determinada classe está localizada - desde que o Composer possa carregar automaticamente a classe.

#### Onde está o diretório de Models?

Ao iniciar o Laravel, muitos desenvolvedores ficam confusos com a falta de um diretório `models`. No entanto, a falta desse diretório é intencional. Achamos a palavra "models" ambígua, pois significa muitas coisas diferentes para muitas pessoas diferentes. Alguns desenvolvedores se referem ao "model" de um aplicativo como a totalidade de toda a sua lógica de negócios, enquanto outros se referem a "models" como classes que interagem com um banco de dados relacional.

Por esse motivo, escolhemos colocar os "models" do Eloquent no diretório `app` por padrão, e permitimos que o desenvolvedor os coloque em outro lugar, se assim o desejar.

<a name="the-root-directory"></a>
## O diretório raiz

<a name="the-root-app-directory"></a>
#### O diretório App

O diretório `app` contém o código principal do seu aplicativo. Exploraremos este diretório com mais detalhes em breve, no entanto, quase todas as classes no seu aplicativo estarão neste diretório.

<a name="the-bootstrap-directory"></a>
#### O diretório Bootstrap

O diretório `bootstrap` contém o arquivo `app.php`, que inicializa o framework. Este diretório também abriga um diretório `cache` que contém arquivos gerados pelo framework para otimização do desempenho, como os arquivos de cache de rota e de serviços.

<a name="the-config-directory"></a>
#### O diretório Config

O diretório `config`, como o nome indica, contém todos os arquivos de configuração do seu aplicativo. É uma ótima idéia ler todos esses arquivos e familiarizar-se com todas as opções disponíveis.

<a name="the-database-directory"></a>
#### O Diretório de Database

O diretório `database` contém suas "migrations", "factories" and "seeds". Se desejar, você também pode usar este diretório para armazenar um banco de dados SQLite.

<a name="the-public-directory"></a>
#### O diretório Public

O diretório `public` contém o arquivo `index.php`, que é o ponto de entrada para todas as solicitações que entram no seu aplicativo e configura o carregamento automático. Esse diretório também abriga seus "assets", como imagens, JavaScript e CSS.

<a name="the-resources-directory"></a>
#### O diretório Resources

O diretório `resources` contém suas "views", bem como seus recursos brutos, não compilados, como LESS, SASS ou JavaScript. Este diretório também abriga todos os seus arquivos de idioma.

<a name="the-routes-directory"></a>
#### O diretório Routes

O diretório `routes` contém todas as definições de rota para seu aplicativo. Por padrão, vários arquivos de rota estão incluídos no Laravel: `web.php`, `api.php`, `console.php` e `channels.php`.

O arquivo `web.php` contém rotas que o `RouteServiceProvider` coloca no grupo de middleware `web`, que fornece o estado da sessão, a proteção CSRF e a criptografia de cookies. Se seu aplicativo não oferecer uma API RESTful então, todas as suas rotas provavelmente serão definidas no arquivo `web.php`.

O arquivo `api.php` contém rotas que o` RouteServiceProvider` coloca no grupo de middleware `api`, que fornece limitação de taxa. Essas rotas devem ser sem estado, portanto, as solicitações que entram no aplicativo por essas rotas devem ser autenticadas por tokens e não terão acesso ao estado da sessão.

O arquivo `console.php` é onde você pode definir todos os seus comandos de console baseados em Closure. Cada Closure está vinculado a uma instância de comando, permitindo uma abordagem simples para interagir com os métodos de IO de cada comando. Mesmo que esse arquivo não defina rotas HTTP, ele define pontos de entrada (routes) baseados no console em seu aplicativo.

O arquivo `channels.php` é onde você pode registrar todos os canais de transmissão de eventos que seu aplicativo suporta.

<a name="the-storage-directory"></a>
#### O diretório Storage

O diretório `storage` contém seus templates blade compilados, sessões baseadas em arquivo, caches de arquivo e outros arquivos gerados pela framework. Este diretório é composto dos diretórios `app`,` framework` e `logs`. O diretório `app` pode ser usado para armazenar todos os arquivos gerados pelo seu aplicativo. O diretório `framework` é usado para armazenar arquivos e caches gerados pela framework. Finalmente, o diretório `logs` contém os arquivos de log do seu aplicativo.

O diretório `storage/app/public` pode ser usado para armazenar arquivos gerados pelo usuário, como avatares de perfil, que devem estar acessíveis ao público. Você deve criar um link simbólico em `public/storage` que aponte para este diretório. Você pode criar o link usando o comando `php artisan storage:link`.

<a name="the-tests-directory"></a>
#### O diretório Tests

O diretório `tests` contém seus testes automatizados. Um exemplo de teste com [PHPUnit](https://phpunit.de/) é fornecido imediatamente. Cada classe de teste deve ter o sufixo da palavra `Test`. Você pode executar seus testes usando os comandos `phpunit` ou `php vendor/bin/phpunit`.

<a name="the-vendor-directory"></a>
#### O diretório Vendor

O diretório `vendor` contém suas dependências do [Composer](https://getcomposer.org).

<a name="the-app-directory"></a>
## O diretório app

A maior parte do seu aplicativo está no diretório `app`. Por padrão, esse diretório está no namespace `App` e é carregado automaticamente pelo Composer usando o padrão de carregamento automático da [PSR-4 autoloading standard](https://www.php-fig.org/psr/psr-4/).

O diretório `app` contém uma variedade de diretórios adicionais, como `Console`, `Http` e` Providers`. Pense nos diretórios `Console` e `Http` como fornecendo uma API no núcleo do seu aplicativo. O protocolo HTTP e a CLI são mecanismos para interagir com seu aplicativo, mas na verdade não contêm lógica de aplicativo. Em outras palavras, são duas maneiras de emitir comandos para o seu aplicativo. O diretório `Console` contém todos os seus comandos do Artisan, enquanto o diretório `Http` contém seus "controllers", "middleware", and "requests".

Uma variedade de outros diretórios será gerada dentro do diretório `app` enquanto você usa os comandos `make` do Artisan para gerar classes. Portanto, por exemplo, o diretório `app/Jobs` não existirá até que você execute o comando `php artisan make:job` para gerar uma classe de job.

> {tip} Muitas das classes no diretório `app` podem ser geradas pelo Artisan através de comandos. Para revisar os comandos disponíveis, execute o comando `php artisan list make` no seu terminal.

<a name="the-broadcasting-directory"></a>
#### O diretório Broadcasting

O diretório `Broadcasting` contém todas as classes de broadcasting para seu aplicativo. Essas classes são geradas usando o comando `php artisan make:channel`. Esse diretório não existe por padrão, mas será criado para você quando você criar seu primeiro canal. Para saber mais sobre os canais, consulte a documentação sobre [event broadcasting](/docs/{{version}}/broadcasting).

<a name="the-console-directory"></a>
#### O diretório Console

O diretório `Console` contém todos os comandos personalizados do Artisan para seu aplicativo. Esses comandos podem ser gerados usando o comando `php artisan make:command`. Esse diretório também abriga o kernel do console, onde são registrados os comandos personalizados do Artisan e definidas as [tarefas agendadas](/docs/{{version}}/scheduling).

<a name="the-events-directory"></a>
#### O diretório Events

Este diretório não existe por padrão, mas será criado quando você executar os comandos `php artisan event:generate` ou `php artisan make:event`. O diretório `Events` abriga [classes de eventoss](/docs/{{version}}/events). Os eventos podem ser usados para alertar outras partes de seu aplicativo que uma determinada ação ocorreu, fornecendo uma grande flexibilidade e desacoplamento.

<a name="the-exceptions-directory"></a>
#### O diretório Exceptions

O diretório `Exceptions` contém o manipulador de exceções e também é um bom lugar para colocar as exceções geradas pelo seu aplicativo. Se você deseja personalizar como suas exceções são registradas ou renderizadas, modifique a classe `Handler` neste diretório.

<a name="the-http-directory"></a>
#### O diretório Http

O diretório `Http` contém seus controllers, middleware, and form requests. Quase toda a lógica para lidar com solicitações que entram no seu aplicativo será colocada neste diretório.

<a name="the-jobs-directory"></a>
#### O diretório Jobs

Esse diretório não existe por padrão, mas será criado quando você executar o comando `php artisan make:job`. O diretório `Jobs` abriga as [filas de jobs](/docs/{{version}}/queues) de seu aplicativo. Os jobs podem ser enfileirados pelo seu aplicativo ou executados de forma síncrona no ciclo de vida da solicitação atual. Às vezes, as tarefas executadas de forma síncrona durante a solicitação atual são chamadas de "commands", pois são uma implementação do [padrão de comando](https://en.wikipedia.org/wiki/Command_pattern).

<a name="the-listeners-directory"></a>
#### O diretório Listeners

Esse diretório não existe por padrão, mas será criado quando você executar o comando `php artisan event:generate` ou `php artisan make:listener`. O diretório `Listeners` contém as classes que manipulam seus [eventos](/docs/{{version}}/events). Os event listeners recebem uma instância de evento e executam a lógica em resposta ao evento que está sendo disparado. Por exemplo, um "event" `UserRegistered` pode ser tratado por um "listener" `SendWelcomeEmail`.

<a name="the-mail-directory"></a>
#### O diretório Mail

Esse diretório não existe por padrão, mas será criado quando você executar o comando `php artisan make:mail`. O diretório `Mail` contém todas as suas classes que representam os emails enviados pelo seu aplicativo. Os objetos Mail permitem que você encapsule toda a lógica da criação de um email em uma classe única e simples que possa ser enviada usando o método `Mail::send`.

<a name="the-notifications-directory"></a>
#### O diretório Notifications

Esse diretório não existe por padrão, mas será criado quando você executar o comando `php artisan make:notification`. O diretório `Notifications` contém todas as notificações "transacionais" enviadas pelo seu aplicativo, como notificações simples sobre eventos que ocorrem em seu aplicativo. A notificação do Laravel apresenta resumos enviando notificações através de uma variedade de drivers, como email, Slack, SMS ou armazenados em um banco de dados.

<a name="the-policies-directory"></a>
#### O diretório Policies

Esse diretório não existe por padrão, mas será criado quando você executar o comando `php artisan make:policy`. O diretório `Policies` contém as classes de política de autorização para seu aplicativo. As "policies" são usadas para determinar se um usuário pode executar uma determinada ação contra um recurso. Para mais informações, consulte a [documentação da autorização](/docs/{{version}}/authorization).

<a name="the-providers-directory"></a>
#### O diretório Providers

O diretório `Providers` contém todos os [service providers](/docs/{{version}}/providers) para seu aplicativo. Os "service providers" inicializam seu aplicativo vinculando serviços no contêiner de serviço, registrando eventos ou executando outras tarefas para preparar seu aplicativo para solicitações de entrada.

Em um nvo aplicativo do Laravel, esse diretório já conterá vários "providers". Você pode adicionar seus próprios "providers" a esse diretório, conforme necessário.

<a name="the-rules-directory"></a>
#### O diretório Rules

Esse diretório não existe por padrão, mas será criado quando você executar o comando `php artisan make:rule`. O diretório `Rules` contém os objetos de regra de validação personalizados para seu aplicativo. As "rules" são usadas para encapsular a lógica de validação complicada em um objeto simples. Para mais informações, consulte a [documentação de validação](/docs/{{version}}/validation).