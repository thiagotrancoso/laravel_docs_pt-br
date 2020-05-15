# Instalação

- [Instalação](#installation)
    - [Requisitos do servidor](#server-requirements)
    - [Instalando o Laravel](#installing-laravel)
    - [Configuração](#configuration)
- [Configuração do servidor](#web-server-configuration)
    - [Configuração de Diretório](#directory-configuration)
    - [URLs amigáveis](#pretty-urls)

<a name="installation"></a>
## Instalação

<a name="server-requirements"></a>
### Requisitos do servidor

A estrutura do Laravel possui alguns requisitos de sistema. Todos esses requisitos são atendidos pela máquina virtual [Laravel Homestead](/docs/{{version}}/homestead), portanto, é altamente recomendável que você use o Homestead como seu ambiente de desenvolvimento local do Laravel.

No entanto, se você não estiver usando o Homestead, precisará garantir que o servidor atenda aos seguintes requisitos:

<div class="content-list" markdown="1">
- `PHP >= 7.2.0`
- `BCMath PHP Extension`
- `Ctype PHP Extension`
- `Fileinfo PHP extension`
- `JSON PHP Extension`
- `Mbstring PHP Extension`
- `OpenSSL PHP Extension`
- `PDO PHP Extension`
- `Tokenizer PHP Extension`
- `XML PHP Extension`
</div>

<a name="installing-laravel"></a>
### Instalando o Laravel

O Laravel utiliza o [Composer](https://getcomposer.org) para gerenciar suas dependências. Portanto, antes de usar o Laravel, verifique se o Composer está instalado em sua máquina.

#### Instalando Via Laravel Installer

Primeiro, faça o download do instalador do Laravel usando o Composer:

    composer global require laravel/installer

Coloque o diretório bin do Composer na variável de ambiente `$PATH`, para que o executável do laravel possa ser localizado pelo seu sistema. O diretório bin existe em diferentes locais, com base no seu sistema operacional, no entanto, alguns locais comuns incluem:

<div class="content-list" markdown="1">
- macOS: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
- GNU / Linux Distributions: `$HOME/.config/composer/vendor/bin` or `$HOME/.composer/vendor/bin`
</div>

Você também pode encontrar o caminho de instalação global do Composer executando o comando `composer global about` e pesquisando a partir da primeira linha.

Uma vez instalado, o comando `laravel new` criará uma nova instalação do Laravel no diretório que você especificar. Por exemplo, o comando `laravel new blog` criará um diretório chamado `blog` contendo uma nova instalação do Laravel com todas as dependências do Laravel já instaladas:

    laravel new blog

#### Instalando Via Composer Create-Project

Como alternativa, você também pode instalar o Laravel executando o comando `composer create-project` no seu terminal:

    composer create-project --prefer-dist laravel/laravel blog "6.*"

#### Servidor de Desenvolvimento Local

Se você possui o PHP instalado localmente e gostaria de usar o servidor de desenvolvimento interno do PHP para atender ao seu aplicativo, você pode usar o comando `artisan serve`. Este comando iniciará um servidor de desenvolvimento em `http://localhost:8000`:

    php artisan serve

Opções de desenvolvimento local mais robustas estão disponíveis via [Homestead](/docs/{{version}}/homestead) e [Valet](/docs/{{version}}/valet).

<a name="configuration"></a>
### Configuração

#### Diretório público

Depois de instalar o Laravel, você deve configurar a raiz do servidor da web `/` para ser o diretório `public`. O `index.php` do diretório public serve como controlador frontal para todas as solicitações HTTP que entram no seu aplicativo.

#### Arquivos de configuração

Todos os arquivos de configuração da estrutura do Laravel são armazenados no diretório `config`. Cada opção está documentada, portanto, fique à vontade para procurar nos arquivos e se familiarizar com as opções disponíveis.

#### Permissões de diretório

Após a instalação do Laravel, você pode precisar configurar algumas permissões. Os diretórios dentro de `storage` e `bootstrap/cache` devem ser graváveis pelo servidor da Web ou o Laravel não será executado. Se você estiver usando a máquina virtual [Homestead](/docs/{{version}}/homestead), essas permissões já estarão definidas.

#### Chave do aplicativo

A próxima coisa que você deve fazer após instalar o Laravel é definir a chave do aplicativo como uma sequência aleatória. Se você instalou o Laravel via Composer ou o instalador do Laravel, essa chave já foi definida pelo comando `php artisan key:generate`.

Normalmente, essa sequência deve ter 32 caracteres. A chave pode ser definida no arquivo de ambiente `.env`. Se você não copiou o arquivo `.env.example` para um novo arquivo chamado `.env`, faça isso agora. **Se a chave do aplicativo não estiver definida, suas sessões do usuário e outros dados criptografados não estarão seguros!**

#### Configuração Adicional

O Laravel quase não precisa de outra configuração para uso. Você está livre para começar a desenvolver! No entanto, você pode revisar o arquivo `config/app.php` e sua documentação. Ele contém várias opções, como `timezone` e `locale`, que você pode alterar de acordo com seu aplicativo.

Você também pode configurar alguns componentes adicionais do Laravel, como:

<div class="content-list" markdown="1">
- [Cache](/docs/{{version}}/cache#configuration)
- [Database](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)
</div>

<a name="web-server-configuration"></a>
## Configuração do servidor

<a name="directory-configuration"></a>
### Configuração de Diretório

O Laravel sempre deve ser disponibilizado fora da raiz do "diretório da web" configurado no seu servidor. Você não deve tentar disponiblizar um aplicativo Laravel a partir de um subdiretório do "diretório da web". Fazer isso pode expor arquivos confidenciais presentes no seu aplicativo.

<a name="pretty-urls"></a>
### URLs amigáveis

#### Apache

O Laravel inclui um arquivo `public/.htaccess` que é usado para fornecer URLs amigáveis sem o `index.php` na url. Antes de disponibilizar o Laravel com o Apache, ative o módulo `mod_rewrite` para que o arquivo `.htaccess` interprete as URLs amigáveis.

Se o arquivo `.htaccess` fornecido com o Laravel não funcionar com a instalação do Apache, tente esta alternativa:

    Options +FollowSymLinks -Indexes
    RewriteEngine On

    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

#### Nginx

Se você estiver usando o Nginx, a seguinte diretiva na configuração do seu site direcionará todas as solicitações para `index.php`:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Ao usar o [Homestead](/docs/{{version}}/homestead) ou [Valet](/docs/{{version}}/valet), as URLs amigáveis serão configuradas automaticamente.