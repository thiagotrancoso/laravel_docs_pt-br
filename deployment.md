# Deploy

- [Introdução](#introducao)
- [Configuração do servidor](#configuracao-do-servidor)
    - [Nginx](#nginx)
- [Otimização](#otimizacao)
    - [Otimização do autoloader](#otimizacao-do-autoloader)
    - [Otimizando o carregamento da configuração](#otimizando-o-carregamento-da-configuracao)
    - [Otimizando o carregamento da rota](#otimizando-o-carregamento-da-rota)
- [Deploy com Forge](#deploy-com-forge)

<a name="introducao"></a>
## Introdução

Quando você estiver pronto para implantar seu aplicativo Laravel em produção, há algumas coisas importantes que você pode fazer para garantir que seu aplicativo esteja sendo executado da forma mais eficiente possível. Neste documento, abordaremos alguns ótimos pontos de partida para garantir que seu aplicativo Laravel seja implantado corretamente.

<a name="configuracao-do-servidor"></a>
## Configuração do servidor

<a name="nginx"></a>
### Nginx

Se você estiver implantando seu aplicativo em um servidor executando o Nginx, poderá usar o seguinte arquivo de configuração como ponto de partida para configurar seu servidor da web. Provavelmente, esse arquivo precisará ser personalizado, dependendo da configuração do seu servidor. Se você deseja assistência no gerenciamento do servidor, considere usar um serviço como [Laravel Forge](https://forge.laravel.com):

    server {
        listen 80;
        server_name example.com;
        root /example.com/public;

        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Content-Type-Options "nosniff";

        index index.html index.htm index.php;

        charset utf-8;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }

        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }

        error_page 404 /index.php;

        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            include fastcgi_params;
        }

        location ~ /\.(?!well-known).* {
            deny all;
        }
    }

<a name="otimizacao"></a>
## Otimização

<a name="otimizacao-do-autoloader"></a>
### Otimização do autoloader

Ao implantar na produção, certifique-se de otimizar o mapa do autoloader do Composer para que o Composer possa encontrar rapidamente o arquivo adequado a ser carregado para uma determinada classe:

    composer install --optimize-autoloader --no-dev

> {tip} Além de otimizar o **autoloader**, você deve sempre incluir um arquivo `composer.lock` no repositório de controle de origem do seu projeto. As dependências do seu projeto podem ser instaladas muito mais rapidamente quando um arquivo `composer.lock` está presente.

<a name="otimizando-o-carregamento-da-configuracao"></a>
### Otimizando o carregamento da configuração

Ao implantar seu aplicativo em produção, certifique-se de executar o comando `php artisan config:cache` durante o processo de implantação:

    php artisan config:cache

Este comando combinará todos os arquivos de configuração do Laravel em um único arquivo em cache, o que reduz bastante o número de viagens que a estrutura deve fazer no sistema de arquivos ao carregar seus valores de configuração.

> {note} Se você executar o comando `php artisan config:cache` durante o processo de implantação, certifique-se de estar chamando apenas a função `env` nos seus arquivos de configuração. Após a configuração ter sido armazenada em cache, o arquivo `.env` não será carregado e todas as chamadas para a função `env` retornarão `null`.

<a name="otimizando-o-carregamento-da-rota"></a>
### Otimizando o carregamento da rota

Se você estiver construindo um aplicativo grande com muitas rotas, verifique se está executando o comando `php artisan route:cache` durante o processo de implantação:

    php artisan route:cache

Este comando reduz todos os seus registros de rota em uma única chamada de método em um arquivo em cache, melhorando o desempenho do registro de rota ao registrar centenas de rotas.

> {note} Como esse recurso usa serialização PHP, você só pode armazenar em cache as rotas para aplicativos que usam exclusivamente rotas baseadas em controlador. O PHP não pode serializar Closures.

<a name="deploy-com-forge"></a>
## Deploy com Forge

Se você não está pronto para gerenciar sua própria configuração de servidor ou não está confortável em configurar todos os vários serviços necessários para executar um aplicativo Laravel robusto, o [Laravel Forge](https://forge.laravel.com) é uma alternativa maravilhosa.

O Laravel Forge pode criar servidores em vários provedores de infraestrutura, como DigitalOcean, Linode, AWS e mais. Além disso, o Forge instala e gerencia todas as ferramentas necessárias para criar aplicativos robustos do Laravel, como Nginx, MySQL, Redis, Memcached, Beanstalk e muito mais.
