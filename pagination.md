# Banco de dados: Paginação

- [Introdução](#introducao)
- [Uso básico](#uso-basico)
    - [Paginando resultados do query builder](#paginando-resultados-do-query-builder)
    - [Paginação de resultados do eloquent](#paginacao-de-resultados-do-eloquent)
    - [Criando manualmente um paginator](#criando-manualmente-um-paginator)
- [Exibindo resultados da paginação](#exibindo-resultados-da-paginacao)
    - [Convertendo resultados em JSON](#convertendo-resultados-em-json)
- [Personalizando a exibição de paginação](#personalizando-a-exibicao-de-paginacao)
- [Métodos da instância do paginator](#metodos-da-instancia-do-paginator)

<a name="introducao"></a>
## Introdução

Em outros frameworks, a paginação pode ser muito dolorosa. O *paginator* do Laravel é integrado ao construtor de consultas e ao Eloquent ORM e fornece paginação conveniente e fácil de usar dos resultados do banco de dados imediatamente. O HTML gerado pelo *paginator* é compatível com a estrutura CSS do Bootstrap.

<a name="uso-basico"></a>
## Uso básico

<a name="paginando-resultados-do-query-builder"></a>
### Paginando resultados do Query Builder

Existem várias maneiras de paginar itens. O mais simples é usar o método `paginate` no construtor de consultas ou em uma consulta Eloquent. O método `paginate` cuida automaticamente da definição do limite e do deslocamento adequados com base na página atual que está sendo visualizada pelo usuário. Por padrão, a página atual é detectada pelo valor do argumento da string de consulta `page` na solicitação HTTP. Esse valor é detectado automaticamente pelo Laravel e também é automaticamente inserido nos links gerados pelo *paginator*.

Neste exemplo, o único argumento passado para o método `paginate` é o número de itens que você deseja exibir **por página**. Nesse caso, vamos especificar que gostaríamos de exibir `15` itens por página:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Support\Facades\DB;

class UserController extends Controller
{
    /**
     * Mostrar todos os usuários para o aplicativo.
     *
     * @return Response
     */
    public function index()
    {
        $users = DB::table('users')->paginate(15);

        return view('user.index', ['users' => $users]);
    }
}
```

> Atualmente, operações de paginação que usam uma instrução `groupBy` não podem ser executadas eficientemente pelo Laravel. Se você precisar usar um `groupBy` com um conjunto de resultados paginado, é recomendável consultar o banco de dados e criar um *paginator* manualmente.

#### "Paginação simples"

Se você só precisa exibir links simples **"Próximo"** e **"Anterior"** em sua exibição de paginação, você pode usar o método `simplePaginate` para executar uma consulta mais eficiente. Isso é muito útil para conjuntos de dados grandes quando você não precisa exibir um link para cada número de página ao renderizar sua exibição:

```php
$users = DB::table('users')->simplePaginate(15);
```

<a name="paginacao-de-resultados-do-eloquent"></a>
### Paginação de resultados do Eloquent

Você também pode paginar consultas do Eloquent. Neste exemplo, paginaremos o modelo `User` com `15` itens por página. Como você pode ver, a sintaxe é quase idêntica à paginação dos resultados do construtor de consultas:

```php
$users = App\User::paginate(15);
```

Você pode chamar `paginate` após definir outras restrições na consulta, como cláusulas `where`:

```php
$users = User::where('votes', '>', 100)->paginate(15);
```

Você também pode usar o método `simplePaginate` ao paginar os modelos Eloquent:

```php
$users = User::where('votes', '>', 100)->simplePaginate(15);
```

<a name="criando-manualmente-um-paginator"></a>
### Criando manualmente um *paginator*

Às vezes, você pode querer criar uma instância de paginação manualmente, passando uma variedade de itens. Você pode fazer isso criando uma instância de `Illuminate\Pagination\Paginator` ou `Illuminate\Pagination\LengthAwarePaginator`, dependendo de suas necessidades.

A classe `Paginator` não precisa saber o número total de itens no conjunto de resultados, no entanto, por isso, a classe não possui métodos para recuperar o índice da última página. O `LengthAwarePaginator` aceita quase os mesmos argumentos que o `Paginator`, no entanto, exige uma contagem do número total de itens no conjunto de resultados.

Em outras palavras, o `Paginator` corresponde ao método `simplePaginate` no construtor de consultas e no Eloquent, enquanto o `LengthAwarePaginator` corresponde ao método `paginate`.

> Ao criar manualmente uma instância do *paginator*, você deve "fatiar" manualmente a matriz de resultados que passar para o *paginator*. Se não tiver certeza de como fazer isso, consulte a função PHP [array_slice](https://secure.php.net/manual/en/function.array-slice.php).

<a name="exibindo-resultados-da-paginacao"></a>
## Exibindo resultados da paginação

Ao chamar o método `paginate`, você receberá uma instância de `Illuminate\Pagination\LengthAwarePaginator`. Ao chamar o método `simplePaginate`, você receberá uma instância de `Illuminate\Pagination\Paginator`. Esses objetos fornecem vários métodos que descrevem o conjunto de resultados. Além desses métodos auxiliares, as instâncias do *paginator* são iteradores e podem ser repetidas como uma matriz. Portanto, depois de recuperar os resultados, você pode exibir os resultados e renderizar os links da página usando *Blade*:

```html
<div class="container">
    @foreach ($users as $user)
        {{ $user->name }}
    @endforeach
</div>

{{ $users->links() }}
```

O método `links` renderiza os links para o restante das páginas no conjunto de resultados. Cada um desses links já conterá a variável de string de consulta `page` adequada. Lembre-se, o HTML gerado pelo método `links` é compatível com o [framework CSS Bootstrap](https://getbootstrap.com).

#### Personalizando o URI do *paginator*

O método `withPath` permite personalizar o URI usado pelo *paginator* ao gerar links. Por exemplo, se você quiser que o *paginator* gere links como `http://example.com/custom/url?page=N`, você deve passar `custom/url` para o método `withPath`:

```php
Route::get('users', function () {
    $users = App\User::paginate(15);

    $users->withPath('custom/url');

    //
});
```

#### Anexando links de paginação

Você pode anexar à string de consulta os links de paginação usando o método `appends`. Por exemplo, para adicionar `sort=votes` a cada link de paginação, você deve fazer a seguinte chamada para `appends`:

```html
{{ $users->appends(['sort' => 'votes'])->links() }}
```

Se você deseja anexar um **"fragmento hash"** aos URLs do *paginator*, você pode usar o método `fragment`. Por exemplo, para anexar `#foo` ao final de cada link de paginação, faça a seguinte chamada ao método `fragment`:

```html
{{ $users->fragment('foo')->links() }}
```

#### Ajustando os links de paginação

Você pode controlar quantos links adicionais serão exibidos em cada lado do *paginator*. Por padrão, três links são exibidos em cada lado dos links do *paginator* primário. No entanto, você pode controlar esse número usando o método `onEachSide`:

```html
{{ $users->onEachSide(5)->links() }}
```

<a name="convertendo-resultados-em-json"></a>
### Convertendo resultados em JSON

As classes de resultados do *paginator* do Laravel implementam o contrato de interface `Illuminate\Contracts\Support\Jsonable` e expõem o método `toJson`, portanto é muito fácil converter os resultados da paginação em JSON. Você também pode converter uma instância do *paginator* em JSON retornando-a de uma rota ou ação do controlador:

```php
Route::get('users', function () {
    return App\User::paginate();
});
```

O JSON do *paginator* incluirá metainformações como `total`, `current_page`, `last_page` e mais. Os objetos de resultado reais estarão disponíveis através da chave `data` na matriz JSON. Aqui está um exemplo do JSON criado retornando uma instância do *paginator* de uma rota:

```json
{
    "total": 50,
    "per_page": 15,
    "current_page": 1,
    "last_page": 4,
    "first_page_url": "http://laravel.app?page=1",
    "last_page_url": "http://laravel.app?page=4",
    "next_page_url": "http://laravel.app?page=2",
    "prev_page_url": null,
    "path": "http://laravel.app",
    "from": 1,
    "to": 15,
    "data":[
        {
            // Result Object
        },
        {
            // Result Object
        }
    ]
}
```

<a name="personalizando-a-exibicao-de-paginacao"></a>
## Personalizando a exibição de paginação

Por padrão, as visualizações renderizadas para exibir os links de paginação são compatíveis com o framework CSS Bootstrap. No entanto, se você não estiver usando o Bootstrap, poderá definir suas próprias visualizações para renderizar esses links. Ao chamar o método `links` em uma instância do *paginator*, passe o nome da visualização como o primeiro argumento para o método:

```php
{{ $paginator->links('view.name') }}

// Passando dados para a visualização
{{ $paginator->links('view.name', ['foo' => 'bar']) }}
```

No entanto, a maneira mais fácil de personalizar as visualizações de paginação é exportando-as para o diretório `resources/views/vendor` usando o comando `vendor:publish`:

```bash
php artisan vendor:publish --tag=laravel-pagination
```

Este comando colocará as *views* no diretório `resources/views/vendor/pagination`. O arquivo `bootstrap-4.blade.php` dentro deste diretório corresponde à visualização de paginação padrão. Você pode editar este arquivo para modificar o HTML da paginação.

Se você deseja designar um arquivo diferente como a visualização de paginação padrão, pode usar os métodos `defaultView` e `defaultSimpleView` do *paginator* em seu `AppServiceProvider`:

```php
use Illuminate\Pagination\Paginator;

public function boot()
{
    Paginator::defaultView('view-name');

    Paginator::defaultSimpleView('view-name');
}
```

<a name="metodos-da-instancia-do-paginator"></a>
## Métodos da instância do *paginator*

Cada instância do *paginator* fornece informações adicionais de paginação por meio dos seguintes métodos:

Método  |  Descrição
-------  |  -----------
`$results->count()`  |  Retorna a quantidade de itens para a página atual.
`$results->currentPage()`  |  Retorna o número da página atual.
`$results->firstItem()`  |  Retorna o número do primeiro item nos resultados.
`$results->getOptions()`  |  Retorna as opções do *paginator*.
`$results->getUrlRange($start, $end)`  |  Cria um intervalo de URLs de paginação.
`$results->hasPages()`  |  Determina se há itens suficientes para dividir em várias páginas.
`$results->hasMorePages()`  |  Determina se há mais itens no banco de dados.
`$results->items()`  |  Retorna os itens para a página atual.
`$results->lastItem()`  |  Retorna o número do último item nos resultados.
`$results->lastPage()`  |  Retorna o número da última página disponível. (Não disponível ao usar o `simplePaginate`).
`$results->nextPageUrl()`  |  Retorna o URL para a próxima página.
`$results->onFirstPage()`  |  Determina se o *paginator* está na primeira página.
`$results->perPage()`  |  Número de itens a serem mostrados por página.
`$results->previousPageUrl()`  |  Retorna o URL da página anterior.
`$results->total()`  |  Determina o número total de itens correspondentes no banco de dados. (Não disponível ao usar o `simplePaginate`).
`$results->url($page)`  |  Retorna o URL para um determinado número de página.
`$results->getPageName()`  |  Retorna a variável de consulta usada para armazenar a página.
`$results->setPageName($name)`  |  Define a variável de consulta usada para armazenar a página.
