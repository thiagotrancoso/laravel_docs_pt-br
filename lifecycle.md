# Ciclo de vida da solicitação

- [Introdução](#introducao)
- [Visão geral do ciclo de vida](#visao-geral-do-ciclo-de-vida)
- [Focar em Service Providers](#focar-em-service-providers)

<a name="introducao"></a>
## Introdução

Ao usar qualquer ferramenta no "mundo real", você se sente mais confiante se entender como essa ferramenta funciona. O desenvolvimento de aplicativos não é diferente. Ao entender como suas ferramentas de desenvolvimento funcionam, você se sente mais confortável e confiante ao usá-las.

O objetivo deste documento é fornecer uma boa visão geral de alto nível de como a estrutura do Laravel funciona. Ao conhecer melhor a estrutura geral, tudo parece menos "mágico" e você ficará mais confiante ao criar seus aplicativos. Se você não entender todos os termos imediatamente, não desanime! Apenas tente obter uma compreensão básica do que está acontecendo, e seu conhecimento aumentará à medida que você explora outras seções da documentação.

<a name="visao-geral-do-ciclo-de-vida"></a>
## Visão geral do ciclo de vida

### Primeiras coisas

O ponto de entrada para todas as solicitações de um aplicativo Laravel é o arquivo `public/index.php`. Todas as solicitações são direcionadas para esse arquivo pela configuração do servidor (Apache / Nginx). O arquivo `index.php` não contém muito código. Pelo contrário, é um ponto de partida para carregar o restante do framework.

O arquivo `index.php` carrega a definição de carregador automático gerado pelo Composer e recupera uma instância do aplicativo Laravel do script `bootstrap/app.php`. A primeira ação realizada pelo próprio Laravel é criar uma instância do aplicativo / [service container](/docs/{{version}}/container).

### HTTP / Console Kernels

Em seguida, a solicitação recebida é enviada ao kernel HTTP ou ao console, dependendo do tipo de solicitação que está entrando no aplicativo. Esses dois kernels servem como o local central pelo qual todas as solicitações fluem. Por enquanto, vamos nos concentrar no kernel HTTP, localizado em `app/Http/Kernel.php`.

O kernel HTTP estende a classe `Illuminate\Foundation\Http\Kernel`, que define uma matriz de `bootstrappers` que serão executados antes da execução da solicitação. Esses bootstrappers configuram o tratamento de erros, configuram o log, [detecta o ambiente do aplicativo](/docs/{{version}}/configuration#environment-configuration), e execute outras tarefas que precisam ser realizadas antes que a solicitação seja realmente tratada.

O kernel HTTP também define uma lista de [middleware](/docs/{{version}}/middleware) HTTP que todas as solicitações devem passar antes de serem tratadas pelo aplicativo. Esses middlewares lidam com a leitura e gravação da [sessão HTTP](/docs/{{version}}/session), determina se o aplicativo está no modo de manutenção, [verifica o CSRF token](/docs/{{version}}/csrf), e mais.

A assinatura do método `handle` do kernel HTTP é bastante simples: receba uma `Request` e retorne uma `Response`. Pense no Kernel como uma grande caixa preta que representa todo o seu aplicativo. Alimente solicitações HTTP e ele retornará respostas HTTP.

#### Service Providers

Uma das ações mais importantes de inicialização do Kernel é carregar o [service providers](/docs/{{version}}/providers) para sua aplicação. Todos os **service providers** para o aplicativo estão configurados no array `provider` do arquivo de configuração `config/app.php`. Primeiro, o método `register` será chamado em todos os **providers**, então, uma vez que todos os **providers** tenham sido registrados, o método `boot` será chamado.

Os **service providers** são responsáveis ​​por inicializar todos os vários componentes do framework, como os componentes de banco de dados, fila, validação e roteamento. Como eles inicializam e configuram todos os recursos oferecidos pelo framework, os **service providers** são o aspecto mais importante de todo o processo de inicialização do Laravel.

#### Dispatch Request

Depois que o aplicativo for inicializado e todos os **service providers** tiverem sido registrados, o `Request` será entregue ao **router** para envio. O **router** enviará a solicitação para uma rota ou **controller**, além de executar qualquer **middleware** específico da rota.

<a name="focar-em-service-providers"></a>
## Focar em Service Providers

Os **service providers** são realmente a chave para inicializar um aplicativo Laravel. A instância do aplicativo é criada, os **service providers** são registrados e a **request** é entregue ao aplicativo de inicialização. É realmente assim tão simples!

Ter uma compreensão firme de como um aplicativo Laravel é criado e inicializado por meio de **service providers** é muito valioso. Os **service providers** padrão do seu aplicativo são armazenados no diretório `app/Providers`.

Por padrão, o `AppServiceProvider` está bastante vazio. Esse **provider** é um ótimo local para adicionar bindings de **service container** e autoinicialização do seu aplicativo. Para aplicativos grandes, convém criar vários **service providers**, cada um com um tipo mais específico de inicialização.
