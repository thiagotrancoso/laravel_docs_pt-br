# Eloquent: Começando

- [Introdução](#introducao)
- [Definindo modelos](#definindo-modelos)
    - [Convenções do modelo eloquent](#convencoes-do-modelo-eloquent)
    - [Valores padrão para atributos](#valores-padrao-para-atributos)
- [Recuperando modelos](#recuperando-modelos)
    - [Coleções](#colecoes)
    - [Resultados com chunking](#resultados-com-chunking)
    - [Subqueries avançadas](#subqueries-avancadas)
- [Recuperando modelos únicos / agregados](#recuperando-modelos-unicos)
    - [Recuperando agregados](#recuperando-agregados)
- [Inserindo e atualizando modelos](#inserindo-e-atualizando-modelos)
    - [Inserindo](#inserindo)
    - [Atualizando](#atualizando)
    - [Atribuição em massa](#atribuicao-em-massa)
    - [Outros métodos de criação](#outros-metodos-de-criacao)
- [Excluindo modelos](#excluindo-modelos)
    - [Soft deleting](#soft-deleting)
    - [Consultando soft delete](#consultando-soft-delete)
- [Replicando modelos](#replicando-modelos)
- [Query scopes](#query-scopes)
    - [Escopos globais](#escopos-globais)
    - [Escopos locais](#escopos-locais)
- [Comparando modelos](#comparando-modelos)
- [Eventos](#eventos)
    - [Observers](#observers)

<a name="introducao"></a>
## Introdução

O ORM Eloquent incluído no Laravel fornece uma implementação bonita e simples do ActiveRecord para trabalhar com seu banco de dados. Cada tabela de banco de dados possui um "Modelo" correspondente, usado para interagir com essa tabela. Os modelos permitem consultar dados em suas tabelas, além de inserir novos registros na tabela.

Antes de começar, certifique-se de configurar uma conexão com o banco de dados no `config/database.php`. Para obter mais informações sobre como configurar seu banco de dados, consulte [a documentação](/docs/{{version}}/database#configuration).

<a name="definindo-modelos"></a>
## Definindo modelos

Para começar, vamos criar um **Model** Eloquent. Os **models** geralmente ficam no diretório `app`, mas você pode colocá-los em qualquer lugar que possa ser carregado automaticamente de acordo com o arquivo `composer.json`. Todos os **models** do Eloquent estendem a classe `Illuminate\Database\Eloquent\Model`.

A maneira mais fácil de criar uma instância do **model** é usando o comadando do artisan `make:model`:

```bash
php artisan make:model Flight
```

Se você deseja gerar uma **migration** de banco de dados automaticamente ao gerar o modelo, pode usar a opção `--migration` ou `-m`:

```bash
php artisan make:model Flight --migration

php artisan make:model Flight -m
```

<a name="convencoes-do-modelo-eloquent"></a>
### Convenções do modelo eloquent

Agora, vejamos um exemplo de **model** `Flight`, que usaremos para recuperar e armazenar informações da nossa tabela de banco de dados `Flights`:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    //
}
```

#### Nomes de tabela

Observe que não informamos ao Eloquent qual tabela usar para o nosso modelo `Flight`. Por convenção, o nome plural da classe será usado como o nome da tabela, a menos que outro nome seja especificado explicitamente. Portanto, neste caso, o Eloquent assumirá que o modelo `Flight` armazena registros na tabela `flights`. Você pode especificar uma tabela personalizada definindo uma propriedade `table` no seu modelo:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Tabela associada ao modelo.
     *
     * @var string
     */
    protected $table = 'my_flights';
}
```

#### Chaves primárias

O Eloquent também assumirá que cada tabela possui uma coluna de chave primária chamada `id`. Você pode definir uma propriedade protegida `$primaryKey` para substituir esta convenção:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Chave primária associada à tabela.
     *
     * @var string
     */
    protected $primaryKey = 'flight_id';
}
```

Além disso, o Eloquent assume que a chave primária é um valor inteiro incremental, o que significa que, por padrão, a chave primária será automaticamente convertida para um `int`. Se você deseja usar uma chave primária não incremental ou não numérica, defina a propriedade pública `$incrementing` no seu modelo como `false`:

```php
<?php

class Flight extends Model
{
    /**
     * Indica se a chave primária deve incrementar automaticamente.
     *
     * @var bool
     */
    public $incrementing = false;
}
```

Se sua chave primária não for um número inteiro, você deve definir a propriedade protegida `$keyType` no seu modelo como` string`:

```php
<?php

class Flight extends Model
{
    /**
     * O "tipo" da chave primária.
     *
     * @var string
     */
    protected $keyType = 'string';
}
```

#### Timestamps

Por padrão, o Eloquent espera que as colunas `created_at` e `updated_at` existam em suas tabelas. Se você não deseja que essas colunas sejam gerenciadas automaticamente pelo Eloquent, configure a propriedade `$timestamps` no seu modelo como `false`:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Indica se o modelo deve ter as colunas "created_at" e updated_at.
     *
     * @var bool
     */
    public $timestamps = false;
}
```

Se você precisar personalizar o formato de seus registros de data e hora, defina a propriedade `$dateFormat` no seu modelo. Essa propriedade determina como os atributos de data são armazenados no banco de dados, bem como seu formato quando o modelo é serializado para uma matriz ou JSON:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Formato de armazenamento das colunas de data do modelo.
     *
     * @var string
     */
    protected $dateFormat = 'U';
}
```

Se você precisar personalizar os nomes das colunas `created_at` e `updated_at`, você pode definir as constantes `CREATED_AT` e `UPDATED_AT` no seu modelo:

```php
<?php

class Flight extends Model
{
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'last_update';
}
```

#### Conexão com Banco de Dados

Por padrão, todos os modelos do Eloquent usarão a conexão de banco de dados padrão configurada para seu aplicativo. Se você deseja especificar uma conexão diferente para o modelo, use a propriedade `$connection`:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Nome da conexão com o banco de dados para o modelo.
     *
     * @var string
     */
    protected $connection = 'connection-name';
}
```

<a name="valores-padrao-para-atributos"></a>
### Valores padrão para atributos

Se você deseja definir os valores padrão para alguns dos atributos do seu modelo, você pode definir uma propriedade `$attributes` no seu modelo:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Valores padrão para atributos do modelo.
     *
     * @var array
     */
    protected $attributes = [
        'delayed' => false,
    ];
}
```

<a name="recuperando-modelos"></a>
## Recuperando modelos

Depois de criar um modelo e sua tabela de banco de dados associada, você estará pronto para começar a recuperar dados do seu banco de dados. Pense em cada modelo Eloquent como um poderoso construtor de consultas, permitindo consultar fluentemente a tabela de banco de dados associada ao modelo. Por exemplo:

```php
<?php

$flights = App\Flight::all();

foreach ($flights as $flight) {
    echo $flight->name;
}
```

#### Adicionando restrições

O método do Eloquent `all` retornará todos os resultados da tabela do modelo. Como cada modelo Eloquent serve como um construtor de consultas, você também pode adicionar restrições às consultas e, em seguida, usar o método `get` para recuperar os resultados:

```php
$flights = App\Flight::where('active', 1)
                ->orderBy('name', 'desc')
                ->take(10)
                ->get();
```

> Como os modelos Eloquent são construtores de consultas, revise todos os métodos disponíveis no construtor de consultas. Você pode usar qualquer um desses métodos em suas consultas do Eloquent.

#### Atualizando modelos

Você pode atualizar modelos usando os métodos `fresh` e `refresh`. O método `fresh` recuperará o modelo do banco de dados. A instância do modelo existente não será afetada:

```php
$flight = App\Flight::where('number', 'FR 900')->first();

$freshFlight = $flight->fresh();
```

O método `refresh` irá reidratar o modelo existente usando novos dados do banco de dados. Além disso, todos os seus relacionamentos carregados também serão atualizados:

```php
$flight = App\Flight::where('number', 'FR 900')->first();

$flight->number = 'FR 456';

$flight->refresh();

$flight->number; // "FR 900"
```

<a name="colecoes"></a>
### Coleções

Para métodos Eloquent como `all` e `get` que recuperam vários resultados, uma instância de `Illuminate\Database\Eloquent\Collection` será retornada. A classe `Collection` fornece uma variedade de métodos úteis para trabalhar com seus resultados do Eloquent:

```php
$flights = $flights->reject(function ($flight) {
    return $flight->cancelled;
});
```

Você também pode fazer um loop sobre a coleção como uma matriz:

```php
foreach ($flights as $flight) {
    echo $flight->name;
}
```

<a name="resultados-com-chunking"></a>
### Resultados com chunking

Se você precisar processar milhares de registros Eloquent, use o comando `chunk`. O método `chunk` recuperará um "chunk" dos modelos Eloquent, alimentando-os com uma determinada `Closure` para processamento. O uso do método `chunk` economizará memória ao trabalhar com grandes conjuntos de resultados:

```php
Flight::chunk(200, function ($flights) {
    foreach ($flights as $flight) {
        //
    }
});
```

O primeiro argumento passado para o método é o número de registros que você deseja receber por "bloco". A "Closure" passada como o segundo argumento será chamada para cada registro que é recuperado do banco de dados. Uma consulta ao banco de dados será executada para recuperar cada parte dos registros transmitidos ao Closure.

#### Usando cursors

O método `cursor` permite que você repita os registros do banco de dados usando um cursor, que executará apenas uma única consulta. Ao processar grandes quantidades de dados, o método `cursor` pode ser usado para reduzir bastante o uso de memória:

```php
foreach (Flight::where('foo', 'bar')->cursor() as $flight) {
    //
}
```

O `cursor` retorna uma instância de `Illuminate\Support\LazyCollection`. As **"lazy collections"** permitem que você use muitos dos métodos de coleção disponíveis nas coleções típicas do Laravel, enquanto carrega apenas um modelo na memória por vez:

```php
$users = App\User::cursor()->filter(function ($user) {
    return $user->id > 500;
});

foreach ($users as $user) {
    echo $user->id;
}
```

<a name="subqueries-avancadas"></a>
### Subqueries avançadas

#### Subconsulta

O Eloquent também oferece suporte avançado à subconsulta, que permite extrair informações de tabelas relacionadas em uma única consulta. Por exemplo, vamos imaginar que temos uma tabela `destinations` de voo e uma tabela de `flights` para destinos. A tabela `flights` contém uma coluna `arrived_at` que indica quando o vôo chegou ao destino.

Usando a funcionalidade de subconsulta disponível para os métodos `select` e `addSelect`, podemos selecionar todos os `destinations` e o nome do voo que chegou mais recentemente a esse destino usando uma única consulta:

```php
use App\Destination;
use App\Flight;

return Destination::addSelect(['last_flight' => Flight::select('name')
    ->whereColumn('destination_id', 'destinations.id')
    ->orderBy('arrived_at', 'desc')
    ->limit(1)
])->get();
```

#### Ordenação de Subconsulta

Além disso, a função `orderBy` do construtor de consultas suporta subconsultas. Podemos usar essa funcionalidade para classificar todos os destinos com base em quando o último voo chegou a esse destino. Novamente, isso pode ser feito durante a execução de uma única consulta no banco de dados:

```php
return Destination::orderByDesc(
    Flight::select('arrived_at')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderBy('arrived_at', 'desc')
        ->limit(1)
)->get();
```

<a name="recuperando-modelos-unicos"></a>
## Recuperando modelos únicos / agregados

Além de recuperar todos os registros de uma determinada tabela, você também pode recuperar registros únicos usando `find`, `first` ou `firstWhere`. Em vez de retornar uma coleção de modelos, esses métodos retornam uma única instância de modelo:

```php
// Recupere um modelo por sua chave primária...
$flight = App\Flight::find(1);

// Recupere o primeiro modelo que corresponde às restrições de consulta...
$flight = App\Flight::where('active', 1)->first();

// Atalho para recuperar o primeiro modelo que corresponde às restrições de consulta...
$flight = App\Flight::firstWhere('active', 1);
```

Você também pode chamar o método `find` com uma matriz de chaves primárias, que retornará uma coleção dos registros correspondentes:

```php
$flights = App\Flight::find([1, 2, 3]);
```

Às vezes, você pode recuperar o primeiro resultado de uma consulta ou executar alguma outra ação se nenhum resultado for encontrado. O método `firstOr` retornará o primeiro resultado encontrado ou, se nenhum resultado for encontrado, executará o retorno de chamada fornecido. O resultado do retorno de chamada será considerado o resultado do método `firstOr`:

```php
$model = App\Flight::where('legs', '>', 100)->firstOr(function () {
        // ...
});
```

O método `firstOr` também aceita uma matriz de colunas para recuperar:

```php
$model = App\Flight::where('legs', '>', 100)
            ->firstOr(['id', 'legs'], function () {
                // ...
            });
```

#### Not Found Exceptions

Às vezes, você pode querer lançar uma exceção se um modelo não for encontrado. Isso é particularmente útil em rotas ou controladores. Os métodos `findOrFail` e `firstOrFail` recuperam o primeiro resultado da consulta, contudo, se nenhum resultado for encontrado, uma `Illuminate\Database\Eloquent\ModelNotFoundException` será lançada:

```php
$model = App\Flight::findOrFail(1);

$model = App\Flight::where('legs', '>', 100)->firstOrFail();
```

Se a exceção não for capturada, uma resposta HTTP `404` é automaticamente enviada de volta ao usuário. Não é necessário escrever verificações explícitas para retornar respostas `404` ao usar estes métodos:

```php
Route::get('/api/flights/{id}', function ($id) {
    return App\Flight::findOrFail($id);
});
```

<a name="recuperando-agregados"></a>
### Recuperando agregados

Você também pode usar os métodos `count`, `sum`, `max` e outros métodos agregados fornecidos pelo construtor de consultas. Esses métodos retornam o valor escalar apropriado em vez de uma instância de modelo completa:

```php
$count = App\Flight::where('active', 1)->count();

$max = App\Flight::where('active', 1)->max('price');
```

<a name="inserindo-e-atualizando-modelos"></a>
## Inserindo e atualizando modelos

<a name="inserindo"></a>
### Inserindo

Para criar um novo registro no banco de dados, crie uma nova instância de modelo, defina atributos no modelo e chame o método `save`:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Flight;
use Illuminate\Http\Request;

class FlightController extends Controller
{
    /**
     * Crie uma nova instância de vôo.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        // Valide a requisição...

        $flight = new Flight;

        $flight->name = $request->name;

        $flight->save();
    }
}
```

Neste exemplo, atribuímos o parâmetro `name` da requisição HTTP no atributo `name` da instância do modelo `App\Flight`. Quando chamamos o método `save`, um registro será inserido no banco de dados. Os timestamps de data e hora `created_at` e `updated_at` serão configurados automaticamente quando o método `save` for chamado, portanto não há necessidade de configurá-los manualmente.

<a name="atualizando"></a>
### Atualizando

O método `save` também pode ser usado para atualizar modelos que já existem no banco de dados. Para atualizar um modelo, você deve recuperá-lo, definir os atributos que deseja atualizar e, em seguida, chamar o método `save`. Novamente, o registro de data e hora `updated_at` será atualizado automaticamente, portanto não há necessidade de definir manualmente seu valor:

```php
$flight = App\Flight::find(1);

$flight->name = 'New Flight Name';

$flight->save();
```

#### Atualizações em massa

As atualizações também podem ser realizadas em qualquer número de modelos que correspondam a uma determinada consulta. Neste exemplo, todos os voos que estão `active` e têm um `destination` de `San Diego` serão marcados como atrasados:

```php
App\Flight::where('active', 1)
            ->where('destination', 'San Diego')
            ->update(['delayed' => 1]);
```

O método `update` espera uma matriz de pares de colunas e valores representando as colunas que devem ser atualizadas.

> Ao emitir uma atualização em massa via Eloquent, os eventos do modelo `saving`, `saved`, `updating` e `updated` não serão acionados para os modelos atualizados. Isso ocorre porque os modelos nunca são realmente recuperados ao emitir uma atualização em massa.

#### Examinando alterações de atributo

O Eloquent fornece os métodos `isDirty`, `isClean` e `wasChanged` para examinar o estado interno do seu modelo e determinar como seus atributos foram alterados quando foram carregados originalmente.

O método `isDirty` determina se algum atributo foi alterado desde que o modelo foi carregado. Você pode passar um nome de atributo específico para determinar se um atributo específico está sujo. O método `isClean` é o oposto de `isDirty` e também aceita um argumento de atributo opcional:

```php
$user = User::create([
    'first_name' => 'Taylor',
    'last_name' => 'Otwell',
    'title' => 'Developer',
]);

$user->title = 'Painter';

$user->isDirty(); // true
$user->isDirty('title'); // true
$user->isDirty('first_name'); // false

$user->isClean(); // false
$user->isClean('title'); // false
$user->isClean('first_name'); // true

$user->save();

$user->isDirty(); // false
$user->isClean(); // true
```

O método `wasChanged` determina se algum atributo foi alterado quando o modelo foi salvo pela última vez dentro do ciclo de solicitação atual. Você também pode passar um nome de atributo para ver se um atributo específico foi alterado:

```php
$user = User::create([
    'first_name' => 'Taylor',
    'last_name' => 'Otwell',
    'title' => 'Developer',
]);

$user->title = 'Painter';
$user->save();

$user->wasChanged(); // true
$user->wasChanged('title'); // true
$user->wasChanged('first_name'); // false
```

<a name="atribuicao-em-massa"></a>
### Atribuição em massa

Você também pode usar o método `create` para salvar um novo modelo em uma única linha. A instância do modelo inserido será retornada a você. No entanto, antes de fazer isso, você precisará especificar um atributo `fillable` ou `guarded` no modelo, pois todos os modelos Eloquent protegem contra atribuição em massa por padrão.

Uma vulnerabilidade de atribuição em massa ocorre quando um usuário passa um parâmetro HTTP inesperado por meio de uma solicitação e esse parâmetro altera uma coluna em seu banco de dados que você não esperava. Por exemplo, um usuário mal-intencionado pode enviar um parâmetro `is_admin` por meio de uma solicitação HTTP, que é passada ao método `create` do seu modelo, permitindo que o usuário se encaminhe para um administrador.

Portanto, para começar, defina quais atributos do modelo você deseja permitir a atribuição em massa. Você pode fazer isso usando a propriedade `$fillable` no modelo. Por exemplo, vamos atribuir o atributo `name` do modelo `Flight`:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Os atributos que são atribuíveis em massa.
     *
     * @var array
     */
    protected $fillable = ['name'];
}
```

Depois de termos tornado os atributos atribuíveis em massa, podemos usar o método `create` para inserir um novo registro no banco de dados. O método `create` retorna a instância do modelo salvo:

```php
$flight = App\Flight::create(['name' => 'Flight 10']);
```

Se você já possui uma instância de modelo, pode usar o método `fill` para preenchê-lo com uma matriz de atributos:

```php
$flight->fill(['name' => 'Flight 22']);
```

#### Atributos de proteção

Enquanto o `$fillable` serve como uma "lista branca" de atributos que devem ser atribuídos em massa, você também pode optar por usar o `$guarded`. A propriedade `$guarded` deve conter uma matriz de atributos que você não deseja atribuir em massa. Todos os outros atributos que não estão na matriz serão atribuíveis em massa. Portanto, `$guarded` funciona como uma "lista negra". Importante, você deve usar `$fillable` ou `$guarded` - não os dois. No exemplo abaixo, todos os atributos **com exceção de `price`**, poderão ser atribuídos em massa:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * Os atributos que não são atribuíveis em massa.
     *
     * @var array
     */
    protected $guarded = ['price'];
}
```

Se você deseja tornar todos os atributos atribuíveis em massa, defina a propriedade `$guarded` como uma matriz vazia:

```php
/**
 * Os atributos que não são atribuíveis em massa.
 *
 * @var array
 */
protected $guarded = [];
```

<a name="outros-metodos-de-criacao"></a>
### Outros métodos de criação

#### `firstOrCreate` / `firstOrNew`

Existem dois outros métodos que você pode usar para criar modelos atribuindo atributos em massa: `firstOrCreate` e `firstOrNew`. O método `firstOrCreate` tentará localizar um registro do banco de dados usando os pares de coluna / valor fornecidos. Se o modelo não puder ser encontrado no banco de dados, um registro será inserido com os atributos do primeiro parâmetro, juntamente com os do segundo parâmetro opcional.

O método `firstOrNew`, como `firstOrCreate`, tentará localizar um registro no banco de dados que corresponda aos atributos fornecidos. Contudo, se um modelo não for encontrado, uma nova instância do modelo será retornada. Observe que o modelo retornado por `firstOrNew` ainda não foi mantido no banco de dados. Você precisará chamar `save` manualmente para persistir:

```php
// Recupere o vôo pelo nome ou crie-o se ele não existir...
$flight = App\Flight::firstOrCreate(['name' => 'Flight 10']);

// Recupere o vôo pelo nome ou crie-o com os atributos name, delayed e arrival_time...
$flight = App\Flight::firstOrCreate(
    ['name' => 'Flight 10'],
    ['delayed' => 1, 'arrival_time' => '11:30']
);

// Recupera o vôo pelo nome ou instancia um novo objeto...
$flight = App\Flight::firstOrNew(['name' => 'Flight 10']);

// Recupera o vôo pelo nome ou instancia com os atributos name, delayed, e arrival_time ...
$flight = App\Flight::firstOrNew(
    ['name' => 'Flight 10'],
    ['delayed' => 1, 'arrival_time' => '11:30']
);
```

#### `updateOrCreate`

Você também pode encontrar situações em que deseja atualizar um modelo existente ou criar um novo modelo, se não houver nenhum. O Laravel fornece o método `updateOrCreate` para fazer isso em uma única etapa. Como o método `firstOrCreate`, `updateOrCreate` persiste no modelo, portanto não há necessidade de chamar o método `save()`:

```php
// Se houver um vôo de Oakland para San Diego, defina o preço para $99.
// Se não existir um modelo correspondente, crie um.
$flight = App\Flight::updateOrCreate(
    ['departure' => 'Oakland', 'destination' => 'San Diego'],
    ['price' => 99, 'discounted' => 1]
);
```

<a name="excluindo-modelos"></a>
## Excluindo Modelos

Para excluir um modelo, chame o método `delete` em uma instância do modelo:

```php
$flight = App\Flight::find(1);

$flight->delete();
```

#### Excluindo um modelo existente pela chave primária

No exemplo acima, estamos recuperando o modelo do banco de dados antes de chamar o método `delete`. No entanto, se você conhece a chave primária do modelo, você pode excluí-lo sem recuperá-lo chamando o método `destroy`.  Além de uma única chave primária como argumento, o método `destroy` aceita várias chaves primárias, uma matriz de chaves primárias ou uma coleção de chaves primárias:

```php
App\Flight::destroy(1);

App\Flight::destroy(1, 2, 3);

App\Flight::destroy([1, 2, 3]);

App\Flight::destroy(collect([1, 2, 3]));
```

#### Excluindo modelos por consulta

Você também pode executar uma instrução de exclusão em um conjunto de modelos. Neste exemplo, excluiremos todos os vôos marcados como inativos. Como as atualizações em massa, as exclusões em massa não acionarão nenhum evento de modelo para os modelos excluídos:

```php
$deletedRows = App\Flight::where('active', 0)->delete();
```

> Ao executar uma instrução de exclusão em massa via Eloquent, os eventos do modelo `deleting` e `deleted` não serão disparados para os modelos excluídos. Isso ocorre porque os modelos nunca são realmente recuperados ao executar a instrução delete.

<a name="soft-deleting"></a>
### Soft Deleting

Além de remover os registros do banco de dados, o Eloquent pode executar o "soft delete" nos modelos. Quando os modelos são excluídos, eles não são realmente removidos do seu banco de dados. Em vez disso, um atributo `deleted_at` é definido no modelo e inserido no banco de dados. Se um modelo tiver um valor não nulo em `deleted_at`, o modelo será excluído temporariamente. Para ativar "soft delete" para um modelo, use o atributo `Illuminate\Database\Eloquent\SoftDeletes` no modelo:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Flight extends Model
{
    use SoftDeletes;
}
```

> A característica `SoftDeletes` converterá automaticamente o atributo` deleted_at` em uma instância de `DateTime` / `Carbon` para você.

Você também deve adicionar a coluna `deleted_at` à sua tabela de banco de dados. O construtor de esquema do Laravel contém um método auxiliar para criar esta coluna:

```php
// Migration
Schema::table('flights', function (Blueprint $table) {
    $table->softDeletes();
});
```

Agora, quando você chama o método `delete` no modelo, a coluna `deleted_at` será definida para a data e hora atuais. E, ao consultar um modelo que usa "soft deletes", os modelos excluídos serão automaticamente excluídos de todos os resultados da consulta.

Para determinar se uma determinada instância de modelo foi excluída com "soft deletes", use o método `trashed`:

```php
if ($flight->trashed()) {
    //
}
```

<a name="consultando-soft-delete"></a>
### Consultando "soft delete"

#### Consulta incluindo modelos excluídos por "soft delete"

Como observado acima, os modelos marcados com "soft delete" serão excluídos automaticamente dos resultados da consulta. Contudo, você pode forçar modelos excluídos a serem exibidos em um conjunto de resultados usando o método `withTrashed` na consulta:

```php
$flights = App\Flight::withTrashed()
                ->where('account_id', 1)
                ->get();
```

O método `withTrashed` também pode ser usado em uma consulta de relacionamento:

```php
$flight->history()->withTrashed()->get();
```

#### Recuperando apenas registros marcado com "soft delete"

O método `onlyTrashed` recuperará **apenas** modelos excluídos por "soft delete":

```php
$flights = App\Flight::onlyTrashed()
                ->where('airline_id', 1)
                ->get();
```

#### Restaurando modelos excluídos por "soft delete"

Às vezes, você pode querer "cancelar a exclusão" de um modelo excluído. Para restaurar um modelo excluído temporariamente para um estado ativo, use o método `restore` em uma instância de modelo:

```php
$flight->restore();
```

Você também pode usar o método `restore` em uma consulta para restaurar rapidamente vários modelos. Novamente, como outras operações de "massa", isso não acionará nenhum evento de modelo para os modelos restaurados:

```php
App\Flight::withTrashed()
        ->where('airline_id', 1)
        ->restore();
```

Como o método `withTrashed`, o método `restore` também pode ser usado em relacionamentos:

```php
$flight->history()->restore();
```

#### Exclusão permanente de modelos

Às vezes, pode ser necessário remover permanentemente um modelo do seu banco de dados. Para remover permanentemente um modelo excluído do banco de dados, use o método `forceDelete`:

```php
// Força a exclusão de uma instância de modelo único...
$flight->forceDelete();

// Força a exclusão de todos os modelos relacionados...
$flight->history()->forceDelete();
```

<a name="replicando-modelos"></a>
## Replicando modelos

Você pode criar uma cópia não salva de uma instância de modelo usando o método `replicate`. Isso é particularmente útil quando você tem instâncias de modelo que compartilham muitos dos mesmos atributos:

```php
$shipping = App\Address::create([
    'type' => 'shipping',
    'line_1' => '123 Example Street',
    'city' => 'Victorville',
    'state' => 'CA',
    'postcode' => '90001',
]);

$billing = $shipping->replicate()->fill([
    'type' => 'billing'
]);

$billing->save();
```

<a name="query-scopes"></a>
## Query Scopes

<a name="escopos-globais"></a>
### Escopos globais

Os escopos globais permitem adicionar restrições a todas as consultas para um determinado modelo. A própria funcionalidade **soft delete** do Laravel utiliza escopos globais para extrair apenas modelos "não excluídos" do banco de dados. Escrever seus próprios escopos globais pode fornecer uma maneira conveniente e fácil de garantir que todas as consultas de um determinado modelo recebam certas restrições.

#### Escrevendo Escopos globais

Escrever um escopo global é simples. Defina uma classe que implemente a interface `Illuminate\Database\Eloquent\Scope`. Esta interface requer que você implemente um método: `apply`. O método `apply` pode adicionar restrições `where` à consulta, conforme necessário:

```php
<?php

namespace App\Scopes;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;

class AgeScope implements Scope
{
    /**
     * Aplique o escopo a um determinado construtor de consultas Eloquent.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $builder
     * @param  \Illuminate\Database\Eloquent\Model  $model
     * @return void
     */
    public function apply(Builder $builder, Model $model)
    {
        $builder->where('age', '>', 200);
    }
}
```

> Se seu escopo global estiver adicionando colunas à cláusula select da consulta, você deve usar o método `addSelect` em vez de `select`. Isso impedirá a substituição não intencional da cláusula de seleção existente da consulta.

#### Aplicando Escopos globais

Para atribuir um escopo global a um modelo, você deve substituir o método `boot` de um determinado modelo e usar o método `addGlobalScope`:

```php
<?php

namespace App;

use App\Scopes\AgeScope;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Método de inicialização do modelo.
     *
     * @return void
     */
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope(new AgeScope);
    }
}
```

Após adicionar o escopo, uma consulta ao `User::all()` produzirá o seguinte SQL:

```sql
select * from `users` where `age` > 200
```

#### Escopos Globais Anônimos

O Eloquent também permite definir escopos globais usando Closures, o que é particularmente útil para escopos simples que não justificam uma classe separada:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Método de inicialização do modelo.
     *
     * @return void
     */
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope('age', function (Builder $builder) {
            $builder->where('age', '>', 200);
        });
    }
}
```

#### Removendo escopos globais

Se você deseja remover um escopo global para uma determinada consulta, você pode usar o método `withoutGlobalScope`. O método aceita o nome da classe do escopo global como seu único argumento:

```php
User::withoutGlobalScope(AgeScope::class)->get();
```

Ou, se você definiu o escopo global usando uma **closure**:

```php
User::withoutGlobalScope('age')->get();
```

Se você deseja remover vários ou mesmo todos os escopos globais, pode usar o método `withoutGlobalScopes`:

```php
// Remove todos os escopos globais...
User::withoutGlobalScopes()->get();

// Remove alguns dos escopos globais...
User::withoutGlobalScopes([
    FirstScope::class, SecondScope::class
])->get();
```

<a name="escopos-locais"></a>
### Escopos Locais

Os escopos locais permitem definir conjuntos comuns de restrições que você pode reutilizar facilmente em todo o aplicativo. Por exemplo, pode ser necessário recuperar com frequência todos os usuários considerados populares. Para definir um escopo, prefixe um método do tipo `scope` no modelo Eloquent.

Os escopos sempre devem retornar uma instância do **query builder**:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Escopo de consulta para incluir apenas usuários populares.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $query
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopePopular($query)
    {
        return $query->where('votes', '>', 100);
    }

    /**
     * Escopo de consulta para incluir apenas usuários ativos.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $query
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopeActive($query)
    {
        return $query->where('active', 1);
    }
}
```

#### Utilizando um escopo local

Depois de definido o escopo, você pode chamar os métodos de escopo ao consultar o modelo. Contudo, você não deve incluir o prefixo `scope` ao chamar o método. Você pode até encadear chamadas para vários escopos, por exemplo:

```php
$users = App\User::popular()->active()->orderBy('created_at')->get();
```

A combinação de vários escopos de modelo Eloquent através de um operador de consulta `or` pode exigir o uso de **Closure callbacks**:

```php
$users = App\User::popular()->orWhere(function (Builder $query) {
    $query->active();
})->get();
```

No entanto, como isso pode ser complicado, o Laravel fornece um método `orWhere` de "ordem superior" que permite encadear esses escopos com fluência sem o uso de Closures:

```php
$users = App\User::popular()->orWhere->active()->get();
```

#### Escopos dinâmicos

Às vezes, você pode definir um escopo que aceita parâmetros. Para começar, basta adicionar seus parâmetros adicionais ao seu escopo. Os parâmetros do escopo devem ser definidos após o parâmetro `$query`:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Escopo de consulta para incluir apenas usuários de um determinado tipo.
     *
     * @param  \Illuminate\Database\Eloquent\Builder  $query
     * @param  mixed  $type
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopeOfType($query, $type)
    {
        return $query->where('type', $type);
    }
}
```

Agora, você pode passar os parâmetros ao chamar o escopo:

```php
$users = App\User::ofType('admin')->get();
```

<a name="comparando-modelos"></a>
## Comparando modelos

Às vezes, você pode precisar determinar se dois modelos são iguais. O método `is` pode ser usado para verificar rapidamente dois modelos com a mesma chave primária, tabela e conexão com o banco de dados:

```php
if ($post->is($anotherPost)) {
    //
}
```

<a name="eventos"></a>
## Eventos

Modelos eloquentes acionam vários eventos, permitindo conectar nos seguintes pontos no ciclo de vida de um modelo: `retrieved`, `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`. Eventos permitem executar código facilmente cada vez que uma classe de modelo específica é salva ou atualizada no banco de dados. Cada evento recebe a instância do modelo por meio de seu construtor.

O evento `retrieved` será acionado quando um modelo existente for recuperado do banco de dados. Quando um novo modelo é salvo pela primeira vez, os eventos `creating` e `created` serão acionados. Se um modelo já existe no banco de dados e o método `save` é chamado, os eventos `updating` / `updated` serão acionados. Contudo, em ambos os casos, os eventos `saving` / `saved` serão acionados.

> Ao emitir uma atualização em massa ou excluir via Eloquent, os eventos do modelo `saved`, `updated`, `deleting` e `deleted` não serão acionados para os modelos afetados. Isso ocorre porque os modelos nunca são realmente recuperados ao emitir uma atualização ou exclusão em massa.

Para começar, defina uma propriedade `$dispatchesEvents` em seu modelo Eloquent que mapeie vários pontos do ciclo de vida do modelo Eloquent para suas próprias classes de eventos:

```php
<?php

namespace App;

use App\Events\UserDeleted;
use App\Events\UserSaved;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use Notifiable;

    /**
     * Mapa de eventos para o modelo.
     *
     * @var array
     */
    protected $dispatchesEvents = [
        'saved' => UserSaved::class,
        'deleted' => UserDeleted::class,
    ];
}
```

Após definir e mapear seus eventos Eloquent, você poderá usar **ouvintes de eventos** para manipular os eventos.

<a name="observers"></a>
### Observers

#### Definindo observers

Se você estiver ouvindo muitos eventos em um determinado modelo, poderá usar observadores para agrupar todos os seus ouvintes em uma única classe. As classes de observadores têm nomes de métodos que refletem os eventos eloquentes que você deseja ouvir. Cada um desses métodos recebe o modelo como seu único argumento. O comando `php artisan make:observer` é a maneira mais fácil de criar uma nova classe de observador:

```bash
php artisan make:observer UserObserver --model=User
```

Este comando colocará o novo observador no diretório `App/Observers`. Se esse diretório não existir, o Artisan o criará para você. Seu novo observador terá a seguinte aparência:

```php
<?php

namespace App\Observers;

use App\User;

class UserObserver
{
    /**
     * Manipule o evento "created" do Usuário.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function created(User $user)
    {
        //
    }

    /**
     * Manipule o evento "updated" do Usuário.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function updated(User $user)
    {
        //
    }

    /**
     * Manipule o evento "deleted" do usuário.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function deleted(User $user)
    {
        //
    }

    /**
     * Manipule o evento "forceDeleted" do usuário.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function forceDeleted(User $user)
    {
        //
    }
}
```

Para registrar um observador, use o método `observe` no modelo que você deseja observar. Você pode registrar observadores no método `boot` de um de seus provedores de serviços. Neste exemplo, registraremos o observador no `AppServiceProvider`:

```php
<?php

namespace App\Providers;

use App\Observers\UserObserver;
use App\User;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Registre qualquer serviço de aplicativo.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Inicialize todos os serviços de aplicativos.
     *
     * @return void
     */
    public function boot()
    {
        User::observe(UserObserver::class);
    }
}
```
