# Laravel Valet

- [Introdução](#Introdução)
    - [Valet ou Homestead](#Valet-ou-Homestead)
- [Instalação](#Instalação)
    - [Atualizando](#Atualizando)
- [Sites de exibição](#Sites-de-exibição)
    - [O comando `park`](#O-comando-`park`)
    - [O comando `link`](#O-comando-`link`)
    - [Protegendo sites com TLS](#Protegendo-sites-com-TLS)
- [Compartilhando sites](#Compartilhando-sites)
- [Variáveis ​​de ambiente específicas do site](#Variáveis-​​de-ambiente-específicas-do-site)
- [Servindo um site padrão](#Servindo-um-site-padrão)
- [Drivers personalizados do Valet](#Drivers-personalizados-do-Valet)
    - [Drivers locais](#Drivers-locais)
- [Configuração do PHP](#Configuração-do-PHP)
- [Outros comandos do Valet](#Outros-comandos-do-Valet)
- [Diretórios e arquivos do Valet](#Diretórios-e-arquivos-do-Valet)

<a name="introduction"></a>
## Introdução

O Valet é um ambiente de desenvolvimento do Laravel para minimalistas do Mac. No Vagrant, no arquivo `/etc/hosts`. Você pode até compartilhar seus sites usando túneis locais. *Sim, nós também gostamos*.

O Laravel Valet configura o seu Mac para sempre executar o [Nginx](https://www.nginx.com/) em segundo plano quando a máquina é iniciada. Em seguida, usando o [DnsMasq](https://en.wikipedia.org/wiki/Dnsmasq), o Valet proxies todas as solicitações no domínio `*.test` para apontar para sites instalados na sua máquina local.

Em outras palavras, um ambiente de desenvolvimento extremamente rápido do Laravel que usa aproximadamente 7 MB de RAM. O Valet não é um substituto completo para o Vagrant ou o Homestead, mas fornece uma ótima alternativa se você deseja conceitos básicos flexíveis, prefere velocidade extrema ou está trabalhando em uma máquina com uma quantidade limitada de RAM.

Pronto para uso, o suporte com Valet inclui, mas não se limita a:

<style>
    #valet-support > ul {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        line-height: 1.9;
    }
</style>

<div id="valet-support" markdown="1">
- [Laravel](https://laravel.com)
- [Lumen](https://lumen.laravel.com)
- [Bedrock](https://roots.io/bedrock/)
- [CakePHP 3](https://cakephp.org)
- [Concrete5](https://www.concrete5.org/)
- [Contao](https://contao.org/en/)
- [Craft CMS](https://craftcms.com)
- [Drupal](https://www.drupal.org/)
- [Jigsaw](https://jigsaw.tighten.co)
- [Joomla](https://www.joomla.org/)
- [Katana](https://github.com/themsaid/katana)
- [Kirby](https://getkirby.com/)
- [Magento](https://magento.com/)
- [OctoberCMS](https://octobercms.com/)
- [Sculpin](https://sculpin.io/)
- [Slim](https://www.slimframework.com)
- [Statamic](https://statamic.com)
- Static HTML
- [Symfony](https://symfony.com)
- [WordPress](https://wordpress.org)
- [Zend](https://framework.zend.com)
</div>

No entanto, você pode estender o Valet com seus próprios [drivers personalizados](#custom-valet-drivers).

<a name="valet-or-homestead"></a>
### Valet ou Homestead

Como você deve saber, o Laravel oferece o [Homestead](/docs/{{version}}/homestead), outro ambiente de desenvolvimento local do Laravel. O Homestead e Valet diferem em relação ao público-alvo e sua abordagem ao desenvolvimento local. O Homestead oferece uma máquina virtual completa do Ubuntu com configuração automatizada de Nginx. O Homestead é uma escolha maravilhosa se você deseja um ambiente de desenvolvimento Linux totalmente virtualizado ou está no Windows/Linux.

O Valet tem suporte apenas para Mac e exige que você instale o PHP e um servidor de banco de dados diretamente na sua máquina local. Isso é facilmente alcançado usando o [Homebrew](https://brew.sh/) com comandos como `brew install php` e `brew install mysql`. O Valet fornece um ambiente de desenvolvimento local extremamente rápido com consumo mínimo de recursos, por isso é ótimo para desenvolvedores que exigem apenas PHP/MySQL e não precisam de um ambiente de desenvolvimento totalmente virtualizado.

Tanto o Valet quanto o Homestead são ótimas opções para configurar o ambiente de desenvolvimento do Laravel. Qual você escolher dependerá do seu gosto pessoal e das necessidades de sua equipe.

<a name="installation"></a>
## Instalação

**O Valet requer macOS e [Homebrew](https://brew.sh/). Antes da instalação, certifique-se de que nenhum outro programa, como o Apache ou o Nginx, esteja vinculado à porta 80 da sua máquina local.**

<div class="content-list" markdown="1">
- Instale ou atualize o [Homebrew](https://brew.sh/) para a versão mais recente usando o comando `brew update`.
- Instale o PHP 7.4 usando o Homebrew com o comando `brew install php`.
- Instale o [Composer](https://getcomposer.org).
- Instale o Valet com o Composer com o comando `composer global require laravel/valet`. Verifique se o diretório `~/.composer/vendor/bin` está no "PATH" do seu sistema.
- Execute o comando `valet install`. Isso irá configurar e instalar o Valet e o DnsMasq, e registrará o daemon do Valet para iniciar quando o sistema iniciar.
</div>

Depois que o Valet estiver instalado, tente executar o ping em qualquer domínio `*.test` no seu terminal usando um comando como `ping foobar.test`. Se o Valet estiver instalado corretamente, você verá esse domínio respondendo no `127.0.0.1`.

O Valet inicia automaticamente seu daemon sempre que sua máquina é inicializada. Não há necessidade de executar `valet start` ou `valet install` novamente quando a instalação inicial do Valet estiver concluída.

#### Usando outro domínio

Por padrão, o Valet atende seus projetos usando o TLD `.test`. Se você deseja usar outro domínio, pode fazê-lo usando o comando `valet tld tld-name`.

Por exemplo, se você quiser usar `.app` em vez de `.test`, execute `valet tld app` e o Valet começará a servir seus projetos em `*.app` automaticamente.

#### Banco de dados

Se você precisa de um banco de dados, tente o MySQL executando `brew install mysql@5.7` na sua linha de comando. Uma vez instalado o MySQL, você pode iniciá-lo usando o comando `brew services start mysql@5.7`. Você pode se conectar ao banco de dados em `127.0.0.1` usando o nome de usuário `root` e uma string vazia para a senha.

#### Versões do PHP

O Valet permite que você alterne as versões do PHP usando o comando `valet use php@version`. O Valet instalará a versão especificada do PHP via Brew se ainda não estiver instalada:

    valet use php@7.2

    valet use php

> {note} O Valet serve apenas uma versão do PHP por vez, mesmo se você tiver várias versões do PHP instaladas.

#### Redefinindo sua instalação

Se você está tendo problemas para executar corretamente a instalação do Valet, executar o comando `composer global update` seguido de `valet install` redefinirá sua instalação e poderá resolver uma variedade de problemas. Em casos raros, pode ser necessário "reinstalar" o Valet executando `valet uninstall --force` seguido de `valet install`.

<a name="upgrading"></a>
### Atualizando

Você pode atualizar sua instalação do Valet usando o comando `composer global update` no seu terminal. Após a atualização, é uma boa prática executar o comando `valet install` para que o Valet possa fazer atualizações adicionais nos seus arquivos de configuração, se necessário.

<a name="serving-sites"></a>
## Sites de exibição

Depois que o Valet estiver instalado, você estará pronto para começar a servir sites. O Valet fornece dois comandos para ajudá-lo a servir os sites do Laravel: `park` e `link`.

<a name="the-park-command"></a>
#### O comando `park`

<div class="content-list" markdown="1">
- Crie um novo diretório no seu Mac executando algo como `mkdir ~/Sites`. Em seguida, `cd ~/Sites` e execute `valet park`. Este comando registrará seu diretório de trabalho atual como um caminho que o Valet deve procurar por sites.
- Em seguida, crie um novo site do Laravel dentro deste diretório: `laravel new blog`.
- Abra `http://blog.test` no seu navegador.
</div>

**É tudo o que há para isso.** Agora, qualquer projeto do Laravel que você criar dentro do diretório "parked" será servido automaticamente usando a convenção `http://folder-name.test`.

<a name="the-link-command"></a>
#### O comando `link`

O comando `link` também pode ser usado para servir os sites do Laravel. Este comando é útil se você deseja servir um único site em um diretório e não o diretório inteiro.

<div class="content-list" markdown="1">
- Para usar o comando, navegue até um de seus projetos e execute `valet link app-name` no seu terminal. O Valet criará um link simbólico em `~/.config/valet/Sites` que aponta para o seu diretório de trabalho atual.
- Após executar o comando `link`, você pode acessar o site no seu navegador em `http://app-name.test`.
</div>

Para ver uma lista de todos os seus diretórios vinculados, execute o comando `valet links`. Você pode usar `valet unlink app-name` para destruir o link simbólico.

> {tip} Você pode usar o `link valet` para servir o mesmo projeto de vários (sub)domínios. Para adicionar um subdomínio ou outro domínio ao seu projeto, execute `valet link subdomain.app-name` na pasta do projeto.

<a name="securing-sites"></a>
#### Protegendo sites com TLS

Por padrão, o Valet exibe sites em HTTP simples. No entanto, se você deseja servir um site através de TLS criptografado usando HTTP/2, use o comando `secure`. Por exemplo, se seu site estiver sendo veiculado por Valet no domínio `laravel.test`, você deve executar o seguinte comando para protegê-lo:

    valet secure laravel

Para "desproteger" um site e voltar a servir seu tráfego através de HTTP simples, use o comando `unsecure`. Assim como o comando `secure`, este comando aceita o nome do host que você deseja desproteger:

    valet unsecure laravel

<a name="sharing-sites"></a>
## Compartilhando sites

O Valet ainda inclui um comando para compartilhar seus sites locais com o mundo, fornecendo uma maneira fácil de testar seu site em dispositivos móveis ou compartilhá-lo com membros da equipe e clientes. Nenhuma instalação de software adicional é necessária após a instalação do Valet.

### Compartilhando sites via Ngrok

Para compartilhar um site, navegue até o diretório do site no seu terminal e execute o comando `valet share`. Um URL acessível ao público será inserido na sua área de transferência e está pronto para colar diretamente no seu navegador ou compartilhar com sua equipe.

Para parar de compartilhar seu site, pressione `Control + C` para cancelar o processo.

> {tip} Você pode passar parâmetros adicionais para o comando **share**, como `valet share --region=eu`. Para mais informações, consulte a [documentação do ngrok](https://ngrok.com/docs).

### Compartilhando sites na sua rede local

O Valet restringe o tráfego de entrada para a interface interna `127.0.0.1` por padrão. Dessa forma, sua máquina de desenvolvimento não é exposta a riscos de segurança da Internet.

Se você deseja permitir que outros dispositivos na sua rede local acessem os sites Valet na sua máquina através do endereço IP da sua máquina (por exemplo: `192.168.1.10/app-name.test`), será necessário editar manualmente a configuração apropriada do Nginx para esse site para remover a restrição na diretiva `listen` removendo o prefixo `127.0.0.1:` na diretiva para as portas 80 e 443.

Se você não executou o `valet secure` no projeto, poderá abrir o acesso à rede para todos os sites não-HTTPS editando o arquivo `/usr/local/etc/nginx/valet/valet.conf`. No entanto, se você estiver servindo o site do projeto por HTTPS (você executou `valet secure` para o site), edite o arquivo `~/.config/valet/Nginx/app-name.test`.

Depois de atualizar sua configuração do Nginx, execute o comando `valet restart` para aplicar as alterações na configuração.

<a name="site-specific-environment-variables"></a>
## Variáveis ​​de ambiente específicas do site

Alguns aplicativos que usam outras estruturas podem depender das variáveis ​​de ambiente do servidor, mas não fornecem uma maneira de configurar essas variáveis ​​no seu projeto. O valet permite que você configure variáveis ​​de ambiente específicas do site adicionando um arquivo `.valet-env.php` na raiz do seu projeto. Essas variáveis ​​serão adicionadas à matriz global `$ _SERVER`:

    <?php

    // Set $_SERVER['key'] to "value" for the foo.test site...
    return [
        'foo' => [
            'key' => 'value',
        ],
    ];

    // Set $_SERVER['key'] to "value" for all sites...
    return [
        '*' => [
            'key' => 'value',
        ],
    ];

<a name="serving-a-default-site"></a>
## Servindo um site padrão

Às vezes, você pode configurar o Valet para servir um site "padrão" em vez de um `404` ao visitar um domínio desconhecido de `test`. Para fazer isso, você pode adicionar uma opção `default` ao seu arquivo de configuração `~/.config/valet/config.json` que contém o caminho para o site que deve funcionar como seu site padrão:

    "default": "/Users/Sally/Sites/foo",

<a name="custom-valet-drivers"></a>
## Drivers personalizados do Valet

Você pode escrever seu próprio "driver" do Valet para atender a aplicativos PHP em execução em outra estrutura ou CMS que não seja suportado nativamente pelo Valet. Quando você instala o Valet, é criado o diretório `~/.config/valet/Drivers` que contém o arquivo `SampleValetDriver.php`. Este arquivo contém uma implementação de driver de amostra para demonstrar como gravar um driver personalizado. Escrever um driver requer apenas a implementação de três métodos: `serves`, `isStaticFile` e `frontControllerPath`.

Todos os três métodos recebem os valores `$sitePath`, `$siteName` e `$uri` como argumentos. O `$sitePath` é o caminho completo para o site que está sendo servido na sua máquina, como `/Users/Lisa/Sites/my-project`. O `$siteName` é o **"nome do site"**, do domínio (`my-project`). O `$uri` é o URI da solicitação de entrada (`/foo/bar`).

Depois de concluir o driver Valet personalizado, coloque-o no diretório `~/.config/valet/Drivers` usando a convenção de nomenclatura `FrameworkValetDriver.php`. Por exemplo, se você estiver escrevendo um driver personalizado com valet para WordPress, seu nome de arquivo deve ser `WordPressValetDriver.php`.

Vamos dar uma olhada em uma implementação de exemplo de cada método que o driver Valet personalizado deve implementar.

#### O método `serves`

O método `serves` deve retornar `true` se o seu driver atender à solicitação recebida. Caso contrário, o método deve retornar `false`. Portanto, dentro desse método, você deve tentar determinar se o `$sitePath` fornecido contém um projeto do tipo que você está tentando servir.

Por exemplo, vamos fingir que estamos escrevendo um `WordPressValetDriver`. Nosso método `serves` pode ser algo como isto:

    /**
     * Determina se o driver atende à solicitação.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return bool
     */
    public function serves($sitePath, $siteName, $uri)
    {
        return is_dir($sitePath.'/wp-admin');
    }

#### O método `isStaticFile`

O `isStaticFile` deve determinar se a solicitação de entrada é para um arquivo "estático", como uma imagem ou uma folha de estilo. Se o arquivo for estático, o método deverá retornar o caminho completo para o arquivo estático no disco. Se a solicitação recebida não for para um arquivo estático, o método retornará `false`:

    /**
     * Determine se a solicitação de entrada é para um arquivo estático.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string|false
     */
    public function isStaticFile($sitePath, $siteName, $uri)
    {
        if (file_exists($staticFilePath = $sitePath.'/public/'.$uri)) {
            return $staticFilePath;
        }

        return false;
    }

> {note} O método `isStaticFile` será chamado apenas se o método `serves` retornar `true` para a solicitação recebida e o URI da solicitação não for `/`.

#### O método `frontControllerPath`

O método `frontControllerPath` deve retornar o caminho completo ao "front controller" do aplicativo, que normalmente é o arquivo "index.php" ou equivalente:

    /**
     * Obtém o caminho totalmente resolvido para o controlador frontal do aplicativo.
     *
     * @param  string  $sitePath
     * @param  string  $siteName
     * @param  string  $uri
     * @return string
     */
    public function frontControllerPath($sitePath, $siteName, $uri)
    {
        return $sitePath.'/public/index.php';
    }

<a name="local-drivers"></a>
### Drivers locais

Se você deseja definir um driver Valet personalizado para um único aplicativo, crie um `LocalValetDriver.php` no diretório raiz do aplicativo. Seu driver personalizado pode estender a classe base `ValetDriver` ou estender um driver específico de aplicativo existente, como o `LaravelValetDriver`:

    class LocalValetDriver extends LaravelValetDriver
    {
        /**
         * Determina se o driver atende à solicitação.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return bool
         */
        public function serves($sitePath, $siteName, $uri)
        {
            return true;
        }

        /**
         * Obtém o caminho totalmente resolvido para o controlador frontal do aplicativo.
         *
         * @param  string  $sitePath
         * @param  string  $siteName
         * @param  string  $uri
         * @return string
         */
        public function frontControllerPath($sitePath, $siteName, $uri)
        {
            return $sitePath.'/public_html/index.php';
        }
    }

<a name="php-configuration"></a>
## Configuração do PHP

Você pode adicionar outros arquivos `.ini` de configuração do PHP no diretório `/usr/local/etc/php/7.X/conf.d/` para personalizar sua instalação do PHP. Depois de adicionar ou atualizar essas configurações, você deve executar o `valet restart php`.

### Limites de memória PHP

Por padrão, o Valet especifica o limite de memória da instalação do PHP e o tamanho máximo de upload do arquivo no arquivo de configuração `/usr/local/etc/php/7.X/conf.d/php-memory-limits.ini`. Isso afeta os processos PHP da CLI e do FPM.

### Processos de pool PHP-FPM

A configuração do PHP-FPM do Valet está contida no arquivo de configuração `/usr/local/etc/php/7.X/php-fpm.d/valet-fpm.conf`. Nesse arquivo, você pode aumentar o número de servidores FPM e processos filhos utilizados pelo seu aplicativo PHP.

<a name="other-valet-commands"></a>
## Outros comandos do Valet

Comando  | Descrição
------------- | -------------
`valet forget` | Execute este comando em um diretório **"estacionado"** para removê-lo da lista de diretórios estacionados.
`valet log` | Veja uma lista de logs dos serviços do Valet.
`valet paths` | Veja todos os seus diretório **"estacionados"**.
`valet restart` | Reinicie o daemon Valet.
`valet start` | Inicie o daemon Valet.
`valet stop` | Pare o daemon Valet.
`valet trust` | Adicione arquivos sudoers para Brew e Valet para permitir que comandos Valet sejam executados sem solicitar senhas.
`valet uninstall` | Desinstalar o Valet: Mostra instruções para desinstalação manual ou passe o parâmetro `--force` para excluir agressivamente todo o Valet.

<a name="valet-directories-and-files"></a>
## Diretórios e arquivos do Valet

Você pode encontrar as seguintes informações de diretório e arquivo úteis ao solucionar problemas com o ambiente Valet:

Arquivo / Diretório | Descrição
--------- | -----------
`~/.config/valet/` | Contém toda a configuração do Valet. Você pode querer manter um backup desta pasta.
`~/.config/valet/dnsmasq.d/` | Contém a configuração do DNSMasq.
`~/.config/valet/Drivers/` | Contém drivers personalizados do Valet.
`~/.config/valet/Extensions/` | Contém **extensões/comandos** personalizados do Valet.
`~/.config/valet/Nginx/` | Contém todas as configurações de site Nginx geradas com Valet. Esses arquivos são reconstruídos ao executar os comandos `install`,` secure` e `tld`.
`~/.config/valet/Sites/` | Contém todos os links simbólicos para projetos.
`~/.config/valet/config.json` | Arquivo de configuração principal do Valet.
`~/.config/valet/valet.sock` | O soquete PHP-FPM usado pela configuração Nginx do Valet. Isso só existirá se o PHP estiver sendo executado corretamente.
`~/.config/valet/Log/fpm-php.www.log` | Log do usuário para erros do PHP.
`~/.config/valet/Log/nginx-error.log` | Log do usuário para erros do Nginx.
`/usr/local/var/log/php-fpm.log` | Log do sistema para erros de PHP-FPM.
`/usr/local/var/log/nginx` | Contém acesso ao Nginx e logs de erro.
`/usr/local/etc/php/X.X/conf.d` | Contém arquivos `*.ini` para várias definições de configuração do PHP.
`/usr/local/etc/php/X.X/php-fpm.d/valet-fpm.conf` | Arquivo de configuração do pool PHP-FPM.
`~/.composer/vendor/laravel/valet/cli/stubs/secure.valet.conf` | A configuração padrão do Nginx usada para criar certificados de site.
