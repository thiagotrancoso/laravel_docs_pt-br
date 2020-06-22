# Laravel Homestead

- [Introdução](#introducao)
- [Instalação e configuração](#instalacao-e-configuracao)
  - [Primeiros passos](#primeiros-passos)
  - [Configurando Homestead](#configurando-homestead)
  - [Iniciando a box do Vagrant](#iniciando-a-box-do-vagrant)
  - [Instalação por projeto](#instalacao-por-projeto)
  - [Instalando recursos opcionais](#instalando-recursos-opcionais)
  - [Aliases](#aliases)
- [Uso diário](#uso-diario)
  - [Acessando o Homestead globalmente](#acessando-o-homestead-globalmente)
  - [Conectando via SSH](#conectando-via-ssh)
  - [Conectando no banco de dados](#conectando-no-banco-de-dados)
  - [Backups de banco de dados](#backups-de-banco-de-dados)
  - [Snapshots de banco de dados](#snapshots-de-banco-de-dados)
  - [Adicionando sites](#adicionando-sites)
  - [Variáveis de ambiente](#variaveis-de-ambiente)
  - [Configurando tarefas cron](#configurando-tarefas-cron)
  - [Configurando Mailhog](#configurando-mailhog)
  - [Configurando Minio](#configurando-minio)
  - [Portas](#portas)
  - [Compartilhando seu ambiente](#compartilhando-seu-ambiente)
  - [Múltiplas versões do PHP](#multiplas-versoes-do-php)
  - [Servidores web](#servidores-web)
  - [E-mail](#e-mail)
- [Depuração e criação de perfil](#depuracao-e-criacao-de-perfil)
  - [Depurando solicitações com o Xdebug](#depurando-solicitacoes-com-o-xdebug)
  - [Depurando aplicativos CLI](#depurando-aplicativos-cli)
  - [Criando perfis de aplicativos com o Blackfire](#criando-perfis-de-aplicativos-com-o-blackfire)
- [Interfaces de rede](#interfaces-de-rede)
- [Estendendo o Homestead](#estendendo-o-homestead)
- [Atualizando o Homestead](#atualizando-o-homestead)
- [Configurações específicas do provider](#configuracoes-especificas-do-provider)
  - [VirtualBox](#virtualbox)

<a name="introducao"></a>
## Introdução

O Laravel se esforça para tornar agradável toda a experiência de desenvolvimento do PHP, incluindo o seu ambiente de desenvolvimento local. O [Vagrant](https://www.vagrantup.com) fornece uma maneira simples e elegante de gerenciar e prover máquinas virtuais.

O Laravel Homestead é uma `box` oficial e pré-empacotada do `Vagrant` que fornece um maravilhoso ambiente de desenvolvimento sem a necessidade de instalar PHP, um servidor web e qualquer outro software de servidor em sua máquina local. Não precisa mais se preocupar em estragar seu sistema operacional! As `box` do `Vagrant` são completamente descartáveis. Se algo der errado, você pode destruir e recriar a `box` em minutos!

O Homestead roda em qualquer sistema Windows, Mac ou Linux e inclui Nginx, PHP, MySQL, PostgreSQL, Redis, Memcached, Node e todos os outros itens que você precisa para desenvolver aplicativos Laravel incríveis.

<span class="obs">**[OBS]** Se você estiver usando o Windows, pode ser necessário ativar a virtualização de hardware (VT-x). Geralmente, ele pode ser ativado via BIOS. Se você estiver usando o Hyper-V em um sistema UEFI, poderá ser necessário desativar o Hyper-V para acessar o VT-x.</span>

<a name="included-software"></a>
### Software incluído

<style>
    #software-list > ul {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">
- Ubuntu 18.04
- Git
- PHP 7.4
- PHP 7.3
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- MySQL
- lmm for MySQL or MariaDB database snapshots
- Sqlite3
- PostgreSQL
- Composer
- Node (Com Yarn, Bower, Grunt e Gulp)
- Redis
- Memcached
- Beanstalkd
- Mailhog
- avahi
- ngrok
- Xdebug
- XHProf / Tideways / XHGui
- wp-cli
</div>

<a name="software-opcional"></a>
### Software opcional

<style>
    #software-list > ul {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">
- Apache
- Blackfire
- Cassandra
- Chronograf
- CouchDB
- Crystal & Lucky Framework
- Docker
- Elasticsearch
- Gearman
- Go
- Grafana
- InfluxDB
- MariaDB
- MinIO
- MongoDB
- MySQL 8
- Neo4j
- Oh My Zsh
- Open Resty
- PM2
- Python
- RabbitMQ
- Solr
- Webdriver & Laravel Dusk Utilities
</div>

<a name="instalacao-e-configuracao"></a>
## Instalação e configuração

<a name="primeiros-passos"></a>
### Primeiros passos

Antes de iniciar seu ambiente Homestead, você deve instalar o [VirtualBox 6.x](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com), [Parallels](https://www.parallels.com/products/desktop/) ou [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) assim como [Vagrant](https://www.vagrantup.com/downloads.html). Todos esses pacotes de software fornecem instaladores visuais fáceis de usar para os sistemas operacionais populares.

Para usar o **provider** VMware, você precisará adquirir o "VMware Fusion ou Workstation" e o [VMware Vagrant plug-in](https://www.vagrantup.com/vmware). Embora não seja gratuito, o VMware pode fornecer um desempenho mais rápido das pastas compartilhadas.

Para usar o **provider** Parallels, você precisará instalar [Parallels Vagrant plug-in](https://github.com/Parallels/vagrant-parallels). É grátis.

Por causa das [limitações do Vagrant](https://www.vagrantup.com/docs/hyperv/limitations.html), o **provider** Hyper-V ignora todas as configurações de rede.

#### Instalação da box do Homestead no Vagrant

Após a instalação do *VirtualBox* ou *VMware* e *Vagrant*, você deve adicionar a *box* `laravel/homestead` à sua instalação do Vagrant usando o seguinte comando no seu terminal. A transferência da *box* levará alguns minutos, dependendo da velocidade da sua conexão com a Internet:

```bash
vagrant box add laravel/homestead
```

Se esse comando falhar, verifique se a instalação do Vagrant está atualizada.

<span class="obs">**[OBS]** O Homestead emite periodicamente ***boxes*** "alfa" / "beta" para teste, o que pode interferir no comando `vagrant box add`. Se você está tendo problemas ao executar o comando `vagrant box add`, você pode executar o comando `vagrant up` e a ***box*** correta será baixada quando o Vagrant tentar iniciar a máquina virtual.</span>

#### Instalando Homestead

Você pode instalar o Homestead clonando o repositório na sua máquina host. Considere a clonagem do repositório dentro da pasta `Homestead`, dentro do diretório "home", pois a *box* Homestead servirá como host para todos os seus projetos do Laravel:

```bash
git clone https://github.com/laravel/homestead.git ~/Homestead
```

Você deve verificar a versão do Homestead, pois a branch `master` nem sempre pode ser estável. Você pode encontrar a versão estável mais recente no [GitHub Release Page](https://github.com/laravel/homestead/releases). Como alternativa, você pode fazer o checkout da branch `release`, que sempre contém a última versão estável:

```bash
cd ~/Homestead

git checkout release
```

Depois de clonar o repositório Homestead, execute o comando `bash init.sh` no diretório `Homestead` para criar o arquivo de configuração `Homestead.yaml`. O arquivo `Homestead.yaml` será colocado no diretório `Homestead`:

```bash
// Mac / Linux...
bash init.sh

// Windows...
init.bat
```

<a name="configurando-homestead"></a>
### Configurando Homestead

#### Configurando seu provider

A chave `provider` no seu arquivo `Homestead.yaml` indica qual **provider** deve ser usado pelo Vagrant: `virtualbox`, `vmware_fusion`, `vmware_workstation`, `parallels` ou `hyperv`. Você pode definir o **provider** de sua preferência:

```yaml
provider: virtualbox
```

#### Configurando pastas compartilhadas

A propriedade `folders` no arquivo `Homestead.yaml` lista todas as pastas que você deseja compartilhar com o seu ambiente Homestead. À medida que os arquivos dessas pastas são alterados, eles serão mantidos em sincronia entre a máquina local e o ambiente Homestead. Você pode configurar quantas pastas compartilhadas forem necessárias:

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
```

<span class="obs">**[OBS]** Os usuários do Windows não devem usar a sintaxe do caminho `~/` e, em vez disso, devem usar o caminho completo para o projeto, como `C:\Users\user\Code\project1`.</span>

Você sempre deve mapear projetos individuais no mapeamento de pastas, em vez de mapear toda a pasta `~/code`. Quando você mapeia uma pasta, a máquina virtual acompanha todos os **Input/Output** de disco para todos os arquivos na pasta. Isso leva a problemas de desempenho se você tiver um número muito grande de arquivos em uma pasta.

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1

    - map: ~/code/project2
      to: /home/vagrant/project2
```

<span class="obs">**[OBS]** Você nunca deve montar o diretório atual (`.`) ao usar o Homestead. Isso faz com que o Vagrant não mapeie a pasta atual para `/vagrant` e interromperá os recursos opcionais e causará resultados inesperados durante o provisionamento.</span>

Para habilitar o [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), você só precisa adicionar um sinalizador simples à sua configuração de pasta sincronizada:

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "nfs"
```

<span class="obs">**[OBS]** Ao usar o NFS no Windows, considere instalar o plug-in [vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd). Este plug-in manterá as permissões corretas de "**usuário / grupo**" para arquivos e diretórios na **box** Homestead.</span>

Você também pode passar as opções de [pastas sincronizadas](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) para o Vagrant listando-os na chave `options`:

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "rsync"
      options:
          rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
          rsync__exclude: ["node_modules"]
```

#### Configurando sites Nginx

Não está familiarizado com o Nginx? Sem problemas. A propriedade `sites` permite mapear facilmente um "domínio" para uma pasta no seu ambiente Homestead. Uma configuração de amostra está incluída no arquivo `Homestead.yaml`. Novamente, você pode adicionar quantos sites forem necessários ao seu ambiente Homestead. O Homestead pode servir como um ambiente virtualizado e conveniente para cada projeto do Laravel em que você está trabalhando:

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
```

Se você alterar a propriedade `sites` após provisionar a **box** Homestead, execute o comando `vagrant reload --provision` para atualizar a configuração do Nginx na máquina virtual.

<span class="obs">**[OBS]** Os scripts do homestead são criados para serem o mais idempotentes possível. No entanto, se você estiver enfrentando problemas durante o provisionamento, deverá destruir e reconstruir a máquina virtual através do comando `vagrant destroy && vagrant up`.</span>

<a name="resolucao-de-nome-de-host"></a>
#### Resolução de nome de host

O Homestead publica nomes de host em `mDNS` para resolução automática do host. Se você definir `hostname: homestead` no seu arquivo `Homestead.yaml`, o host estará disponível no "domínio" `homestead.local`. Os sistemas MacOS, iOS e Linux incluem suporte a `mDNS` por padrão. Windows requer a instalação do [Bonjour Print Services for Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US).

O uso de nomes de host automáticos funciona melhor para instalações do Homestead "por projeto". Se você hospedar vários sites em uma única instância do Homestead, poderá adicionar os "domínios" dos seus sites ao arquivo `hosts` da sua máquina. O arquivo `hosts` redirecionará as solicitações dos sites do Homestead para a máquina do Homestead. No Mac e Linux, este arquivo está localizado em `/etc/hosts`. No Windows, ele está localizado em `C:\Windows\System32\drivers\etc\hosts`. As linhas adicionadas neste arquivo serão semelhantes a linha abaixo:

```text
192.168.10.10  homestead.test
```

Verifique se o endereço IP listado é o definido no seu arquivo `Homestead.yaml`. Depois de adicionar o domínio ao seu arquivo `hosts` e iniciar a **box** do Vagrant, você poderá acessar o site através do seu navegador da web:

```text
http://homestead.test
```

<a name="iniciando-a-box-do-vagrant"></a>
### Iniciando a box do Vagrant

Depois de editar o arquivo `Homestead.yaml` a sua necessidade, execute o comando `vagrant up` no diretório Homestead. O Vagrant inicializará a máquina virtual e configurará automaticamente suas pastas compartilhadas e sites Nginx.

Para destruir a máquina virtual, você pode usar o comando `vagrant destroy --force`.

<a name="instalacao-por-projeto"></a>
### Instalação por projeto

Em vez de instalar o Homestead globalmente e compartilhar a mesma **box** do Homestead em todos os seus projetos, você pode configurar uma instância do Homestead para cada projeto que gerencia. Instalar o Homestead por projeto pode ser benéfico se você desejar enviar um arquivo `Vagrantfile` com o seu projeto, permitindo que outras pessoas que trabalham no projeto executem o comando `vagrant up`.

Para instalar o Homestead diretamente no seu projeto, use o Composer:

```bash
composer require laravel/homestead --dev
```

Após a instalação do Homestead, use o comando `make` para gerar os arquivos `Vagrantfile` e `Homestead.yaml` na raiz do projeto. O comando `make` irá configurar automaticamente as diretivas `sites` e `folders` no arquivo `Homestead.yaml`.

Mac / Linux:

```bash
php vendor/bin/homestead make
```

Windows:

```bash
vendor\\bin\\homestead make
```

Em seguida, execute o comando `vagrant up` no seu terminal e acesse o seu projeto em `http://homestead.test` no seu navegador. Lembre-se, você ainda precisará adicionar o `homestead.test` no arquivo `/etc/hosts` ou o domínio de sua escolha se você não estiver usando o sistema automático de resolução de nome de host.

<a name="instalando-recursos-opcionais"></a>
### Instalando recursos opcionais

O software opcional é instalado usando a configuração "features" no seu arquivo de configuração do Homestead. A maioria das "features" pode ser ativada ou desativada com um valor booleano, enquanto alguns permitem várias opções de configuração:

```yaml
features:
    - blackfire:
          server_id: "server_id"
          server_token: "server_value"
          client_id: "client_id"
          client_token: "client_value"
    - cassandra: true
    - chronograf: true
    - couchdb: true
    - crystal: true
    - docker: true
    - elasticsearch:
          version: 7
    - gearman: true
    - golang: true
    - grafana: true
    - influxdb: true
    - mariadb: true
    - minio: true
    - mongodb: true
    - mysql8: true
    - neo4j: true
    - ohmyzsh: true
    - openresty: true
    - pm2: true
    - python: true
    - rabbitmq: true
    - solr: true
    - webdriver: true
```

#### MariaDB

A ativação do MariaDB removerá o MySQL e instalará o MariaDB. O MariaDB serve como um substituto para o MySQL, portanto você ainda deve usar o driver de banco de dados `mysql` na configuração do banco de dados do seu aplicativo.

#### MongoDB

A instalação padrão do MongoDB definirá o nome de usuário do banco de dados como `homestead` e a senha como `secret`.

#### Elasticsearch

Você pode especificar uma versão suportada do Elasticsearch, que pode ser uma versão principal ou um número exato da versão (major.minor.patch). A instalação padrão criará um cluster chamado 'homestead'. Você nunca deve fornecer ao Elasticsearch mais da metade da memória do sistema operacional, portanto, verifique se a máquina do Homestead possui pelo menos duas vezes a alocação do Elasticsearch.

<span class="obs">**[OBS]** Confira a [documentação do Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current) para aprender como personalizar sua configuração.</span>

#### Neo4j

A instalação padrão do Neo4j definirá o nome de usuário do banco de dados como `homestead` e a senha como `secret`. Para acessar o navegador Neo4j, visite `http://homestead.test:7474` através do seu navegador da web. As portas `7687` (Bolt), `7474` (HTTP), and `7473` (HTTPS) estão prontos para atender solicitações do cliente Neo4j.

<a name="aliases"></a>
### Aliases

Você pode adicionar aliases do Bash à sua máquina Homestead modificando o arquivo `aliases` dentro do diretório Homestead:

```text
alias c='clear'
alias ..='cd ..'
```

Depois de atualizar o arquivo `aliases`, você deve provisionar novamente a máquina Homestead usando o comando `vagrant reload --provision`. Isso garantirá que seus novos aliases estejam disponíveis na máquina.

<a name="uso-diario"></a>
## Uso diário

<a name="acessando-o-homestead-globalmente"></a>
### Acessando o Homestead globalmente

Às vezes, você pode querer executar o comando `vagrant up` em sua máquina Homestead de qualquer lugar no seu sistema de arquivos. Você pode fazer isso em sistemas Mac e Linux adicionando uma função Bash ao seu perfil Bash. No Windows, você pode fazer isso adicionando um arquivo "batch" ao seu `PATH`. Esses scripts permitem executar qualquer comando do Vagrant de qualquer lugar do sistema e apontam esse comando automaticamente para a instalação do Homestead:

#### Mac/Linux

```bash
function homestead() {
    ( cd ~/Homestead && vagrant $* )
}
```

Certifique-se de ajustar o caminho `~/Homestead` na função para o local da sua instalação real do Homestead. Após a instalação da função, você pode executar comandos como `homestead up` ou `homestead ssh` de qualquer lugar do sistema.

#### Windows

Crie um arquivo batch `homestead.bat` em qualquer lugar da sua máquina com o seguinte conteúdo:

```bash
@echo off

set cwd=%cd%
set homesteadVagrant=C:\Homestead

cd /d %homesteadVagrant% && vagrant %*
cd /d %cwd%

set cwd=
set homesteadVagrant=
```

Certifique-se de ajustar o caminho do exemplo `C:\Homestead` no script para o local real da sua instalação do Homestead. Após criar o arquivo, adicione o local do arquivo ao seu `PATH`. Agora você poderá executar comandos como `homestead up` ou `homestead ssh` de qualquer lugar do sistema.

<a name="conectando-via-ssh"></a>
### Conectando via SSH

Você pode se conectar via SSH na sua máquina virtual executando o comando `vagrant ssh` no terminal, dentro do diretório Homestead.

Porém, como você provavelmente precisará fazer o SSH na máquina Homestead com frequência, considere adicionar a função descrita acima na máquina host para fazer o SSH rapidamente na máquina Homestead.

<a name="conectando-no-banco-de-dados"></a>
### Conectando no banco de dados

Um banco de dados MySQL e PostgreSQL chamado `homestead` será configurado automaticamente. Para conectar-se ao banco de dados MySQL ou PostgreSQL a partir da máquina host, você deve usar o ip `127.0.0.1` e a porta `33060` (MySQL) ou `54320` (PostgreSQL). O nome de usuário e a senha dos dois bancos de dados são `homestead` / `secret`.

<span class="obs">**[OBS]** Você só deve usar essas portas não padrão ao conectar-se aos bancos de dados da máquina virtual usando a máquina host. Você usará as portas 3306 e 5432 padrão no arquivo de configuração do banco de dados do Laravel, pois o Laravel está executando dentro da máquina virtual.</span>

<a name="backups-de-banco-de-dados"></a>
### Backups de banco de dados

O Homestead pode fazer backup automaticamente do seu banco de dados quando sua **box** do Vagrant for destruída. Para utilizar esse recurso, você deve usar o Vagrant 2.1.0 ou superior. Ou, se você estiver usando uma versão mais antiga do Vagrant, deverá instalar o plug-in `vagrant-triggers`. Para habilitar backups automáticos do banco de dados, adicione a seguinte linha ao seu arquivo `Homestead.yaml`:

```yaml
backup: true
```

Uma vez configurado, o Homestead exportará seus bancos de dados para os diretórios `mysql_backup` e `postgres_backup` quando o comando `vagrant destroy` for executado. Esses diretórios podem ser encontrados na pasta em que você clonou o Homestead ou na raiz do seu projeto, se você estiver usando o método instalação por projeto.

<a name="snapshots-de-banco-de-dados"></a>
### Snapshots de banco de dados

O Homestead suporta o congelamento do estado dos bancos de dados MySQL e MariaDB e a ramificação entre eles usando o [Gerenciador Lógico do MySQL](https://github.com/Lullabot/lmm). Por exemplo, imagine trabalhar em um site com um banco de dados de vários gigabytes. Você pode importar um **snapshot** do banco de dados. Depois de fazer algum trabalho e criar algum conteúdo de teste localmente, você poderá restaurar rapidamente o estado original.

Sob o capô, o LMM usa a funcionalidade de **snapshot** fina do LVM com suporte à cópia na gravação. Na prática, isso significa que alterar uma única linha de uma tabela fará com que as alterações feitas sejam gravadas no disco, economizando tempo e espaço em disco significativos durante as restaurações.

Como o `lmm` interage com o **LVM**, ele deve ser executado como `root`. Para ver todos os comandos disponíveis, execute o `sudo lmm` dentro da sua **box** do Vagrant. Um fluxo de trabalho comum se parece com o seguinte:

1. Importe um banco de dados para a **branch** padrão `master` lmm.
1. Salve um **snapshot** do banco de dados inalterado usando `sudo lmm branch prod-YYYY-MM-DD`.
1. Modifique o banco de dados.
1. Execute `sudo lmm merge prod-AAAA-MM-DD` para desfazer todas as alterações.
1. Execute `sudo lmm delete <branch>` para excluir **branches** desnecessárias.

<a name="adicionando-sites"></a>
### Adicionando sites

Depois que seu ambiente Homestead estiver provisionado e em execução, você poderá adicionar sites Nginx adicionais para seus aplicativos Laravel. Você pode executar quantas instalações do Laravel desejar em um único ambiente Homestead. Para adicionar um site, altere a chave `sites` ao seu arquivo `Homestead.yaml`:

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
    - map: another.test
      to: /home/vagrant/project2/public
```

Se o Vagrant não estiver gerenciando automaticamente o seu arquivo **hosts**, você também precisará adicionar o novo site a esse arquivo:

```text
192.168.10.10  homestead.test
192.168.10.10  another.test
```

Depois que o site for adicionado, execute o comando `vagrant reload --provision` no diretório Homestead.

<a name="tipos-de-site"></a>
#### Tipos de site

O Homestead suporta vários tipos de sites que permitem executar facilmente projetos que não são baseados no Laravel. Por exemplo, podemos adicionar facilmente um aplicativo Symfony ao Homestead usando o tipo de site `symfony2`:

```yaml
sites:
    - map: symfony2.test
      to: /home/vagrant/my-symfony-project/web
      type: "symfony2"
```

Os tipos de sites disponíveis são: `apache`, `apigility`, `expressive`, `laravel` (o padrão), `proxy`, `silverstripe`, `statamic`, `symfony2`, `symfony4`, e `zf`.

<a name="parametros-do-site"></a>
#### Parâmetros do site

Você pode adicionar valores adicionais do Nginx `fastcgi_param` ao seu site através da diretiva de site `params`. Por exemplo, adicionaremos um parâmetro `FOO` com um valor de `BAR`:

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      params:
          - key: FOO
            value: BAR
```

<a name="variaveis-de-ambiente"></a>
### Variáveis de ambiente

Você pode definir variáveis ​​de ambiente globais adicionando-as ao seu arquivo `Homestead.yaml`:

```yaml
variables:
    - key: APP_ENV
      value: local
    - key: FOO
      value: bar
```

Após atualizar o `Homestead.yaml`, certifique-se de reaprovisionar a máquina virtual executando `vagrant reload --provision`. Isso atualizará a configuração do PHP-FPM para todas as versões instaladas do PHP e também atualizará o ambiente para o usuário `vagrant`.

<a name="configurando-tarefas-cron"></a>
### Configurando tarefas cron

O Laravel fornece uma maneira conveniente de agendar tarefas do Cron com um único comando `php artisan schedule:run` para ser executado a cada minuto. O comando `schedule:run` examinará o agendamento de tarefas definido em sua classe `App\Console\Kernel` para determinar quais tarefas devem ser executadas.

Se você deseja que o comando `schedule:run` seja executado em um site Homestead, você pode definir a opção `schedule` como `true` ao definir o site:

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      schedule: true
```

A tarefa Cron para o site será definido na pasta `/etc/cron.d` da máquina virtual.

<a name="configurando-mailhog"></a>
### Configurando Mailhog

O Mailhog permite capturar facilmente seu email de saída e examiná-lo sem realmente enviar o email para seus destinatários. Para começar, atualize o arquivo `.env` para usar as seguintes configurações de email:

```env
MAIL_DRIVER=smtp
MAIL_HOST=localhost
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

Depois que o Mailhog estiver configurado, você poderá acessar o painel do Mailhog em `http://localhost:8025`.

<a name="configurando-minio"></a>
### Configurando Minio

O Minio é um servidor de armazenamento de objetos de código aberto com uma API compatível com Amazon S3. Para instalar o Minio, atualize seu arquivo `Homestead.yaml` com a seguinte opção de configuração na seção recursos:

```yaml
minio: true
```

Por padrão, o Minio está disponível na porta 9600. Você pode acessar o painel de controle do Minio visitando `http://localhost:9600/`. A chave de acesso padrão é `homestead`, enquanto a chave secreta padrão é `secretkey`. Ao acessar o Minio, você deve sempre usar a região `us-east-1`.

Para usar o Minio, você precisará ajustar a configuração do disco S3 no seu arquivo de configuração `config/filesystems.php`. Você precisará adicionar a opção `use_path_style_endpoint` à configuração do disco, além de alterar a chave `url` para `endpoint`:

```text
's3' => [
    'driver' => 's3',
    'key' => env('AWS_ACCESS_KEY_ID'),
    'secret' => env('AWS_SECRET_ACCESS_KEY'),
    'region' => env('AWS_DEFAULT_REGION'),
    'bucket' => env('AWS_BUCKET'),
    'endpoint' => env('AWS_URL'),
    'use_path_style_endpoint' => true
]
```

Por fim, verifique se o arquivo `.env` possui as seguintes opções:

```env
AWS_ACCESS_KEY_ID=homestead
AWS_SECRET_ACCESS_KEY=secretkey
AWS_DEFAULT_REGION=us-east-1
AWS_URL=http://localhost:9600
```

Para provisionar buckets, adicione uma diretiva `buckets` ao seu arquivo de configuração do Homestead:

```yaml
buckets:
    - name: your-bucket
      policy: public
    - name: your-private-bucket
      policy: none
```

Os valores de `policy` suportados incluem: `none`, `download`, `upload` e `public`.

<a name="portas"></a>
### Portas

Por padrão, as seguintes portas são encaminhadas para o seu ambiente Homestead:

<div class="content-list" markdown="1">
- **SSH:** 2222 &rarr; Encaminha para 22
- **ngrok UI:** 4040 &rarr; Encaminha para 4040
- **HTTP:** 8000 &rarr; Encaminha para 80
- **HTTPS:** 44300 &rarr; Encaminha para 443
- **MySQL:** 33060 &rarr; Encaminha para 3306
- **PostgreSQL:** 54320 &rarr; Encaminha para 5432
- **MongoDB:** 27017 &rarr; Encaminha para 27017
- **Mailhog:** 8025 &rarr; Encaminha para 8025
- **Minio:** 9600 &rarr; Encaminha para 9600
</div>

#### Encaminhando portas adicionais

Se desejar, você pode encaminhar portas adicionais para a **box** do Vagrant, além de especificar o protocolo deles:

```yaml
ports:
    - send: 50000
      to: 5000
    - send: 7777
      to: 777
      protocol: udp
```

<a name="compartilhando-seu-ambiente"></a>
### Compartilhando seu ambiente

Você pode compartilhar o que está trabalhando atualmente com colegas de trabalho ou com um cliente. O Vagrant possui uma maneira integrada de suportar isso por meio de `vagrant share` no entanto, isso não funcionará se você tiver vários sites configurados no seu arquivo `Homestead.yaml`.

Para resolver esse problema, o Homestead inclui seu próprio comando `share`. Para começar, faça o SSH na sua máquina Homestead através de `vagrant ssh` e execute `share homestead.test`. Isso compartilhará o site `homestead.test` do seu arquivo de configuração `Homestead.yaml`. Você pode substituir qualquer um dos outros sites configurados por `homestead.test`:

```bash
share homestead.test
```

Após executar o comando, você verá uma tela do Ngrok que contém o log de atividades e as URLs acessíveis ao público para o site compartilhado. Se você deseja especificar uma região, subdomínio ou outra opção de tempo de execução Ngrok personalizada, adicione-os ao seu comando `share`:

```bash
share homestead.test -region=eu -subdomain=laravel
```

<span class="obs">**[OBS]** Lembre-se, o Vagrant é inerentemente inseguro e você está expondo sua máquina virtual à Internet ao executar o comando `share`.</span>

<a name="multiplas-versoes-do-php"></a>
### Múltiplas versões do PHP

O Homestead 6 introduziu o suporte para várias versões do PHP na mesma máquina virtual. Você pode especificar qual versão do PHP usar para um determinado site no seu arquivo `Homestead.yaml`. As versões disponíveis do PHP são: "5.6", "7.0", "7.1", "7.2", "7.3" e "7.4" (o padrão):

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      php: "7.1"
```

Além disso, você pode usar qualquer uma das versões suportadas do PHP via CLI:

```bash
php5.6 artisan list
php7.0 artisan list
php7.1 artisan list
php7.2 artisan list
php7.3 artisan list
php7.4 artisan list
```

Você também pode atualizar a versão padrão da CLI emitindo os seguintes comandos de dentro da sua máquina virtual Homestead:

```bash
php56
php70
php71
php72
php73
php74
```

<a name="servidores-web"></a>
### Servidores web

O Homestead usa o servidor Nginx por padrão. No entanto, ele pode instalar o Apache se o `apache` for especificado como um tipo de site. Embora os dois servidores da Web possam ser instalados ao mesmo tempo, eles não podem estar *em execução* ao mesmo tempo. O comando shell `flip` está disponível para facilitar o processo de alternância entre servidores web. O comando `flip` determina automaticamente qual servidor web está sendo executado, o desliga e depois inicia o outro servidor. Para usar este comando, faça o SSH na sua máquina Homestead e execute o comando no seu terminal:

```bash
flip
```

<a name="e-mail"></a>
### E-mail

O Homestead inclui o agente de transferência de e-mail Postfix, que está escutando na porta `1025` por padrão. Portanto, você pode instruir seu aplicativo a usar o driver de e-mail `smtp` em `localhost` na porta `1025`. Então, todo e-mail enviado será tratado pelo Postfix e capturado pelo Mailhog. Para visualizar seus e-mails enviados, abra [http://localhost:8025](http://localhost:8025) no seu navegador.

<a name="depuracao-e-criacao-de-perfil"></a>
## Depuração e criação de perfil

<a name="depurando-solicitacoes-com-o-xdebug"></a>
### Depurando solicitações com o Xdebug

O Homestead inclui suporte para depuração de etapas usando [Xdebug](https://xdebug.org). Por exemplo, você pode carregar uma página da Web a partir de um navegador e o PHP se conectará ao seu IDE para permitir a inspeção e modificação do código em execução.

Por padrão, o Xdebug já está em execução e pronto para aceitar conexões. Se você precisar habilitar o Xdebug na CLI, execute o comando `sudo phpenmod xdebug` dentro da sua **box** do Vagrant. Em seguida, siga as instruções do seu IDE para habilitar a depuração. Por fim, configure seu navegador para acionar o Xdebug com uma extensão ou [bookmarklet](https://www.jetbrains.com/phpstorm/marklets/).

<span class="obs">**[OBS]** O Xdebug faz com que o PHP execute significativamente mais devagar. Para desativar o Xdebug, execute o `sudo phpdismod xdebug` dentro da sua máquina virtual e reinicie o serviço FPM.</span>

<a name="depurando-aplicativos-cli"></a>
### Depurando aplicativos CLI

Para depurar um aplicativo PHP CLI, use o alias do shell `xphp` dentro da sua máquina virtual:

```bash
xphp path/to/script
```

#### Iniciando automaticamente o Xdebug

Ao depurar testes funcionais que fazem solicitações ao servidor, é mais fácil iniciar automaticamente a depuração, em vez de modificar os testes para passar por um cabeçalho ou cookie personalizado para acionar a depuração. Para forçar o Xdebug a iniciar automaticamente, modifique o arquivo `/etc/php/7.x/fpm/conf.d/20-xdebug.ini` dentro da sua máquina virtual e adicione a seguinte configuração:

```text
; If Homestead.yaml contains a different subnet for the IP address, this address may be different...
xdebug.remote_host = 192.168.10.1
xdebug.remote_autostart = 1
```

<a name="criando-perfis-de-aplicativos-com-o-blackfire"></a>
### Criando perfis de aplicativos com o Blackfire

[Blackfire](https://blackfire.io/docs/introduction) é um serviço SaaS para criação de perfil de solicitações da Web e aplicativos CLI e criação de asserções de desempenho. Oferece uma interface de usuário interativa que exibe dados de perfil em gráficos de chamadas e linhas de tempo. Ele foi desenvolvido para uso em desenvolvimento, preparo e produção, sem custos adicionais para os usuários finais. Ele fornece verificações de desempenho, qualidade e segurança nas configurações de código e `php.ini`.

O [Blackfire Player](https://blackfire.io/docs/player/index) é um aplicativo de rastreamento da Web de código aberto que pode trabalhar em conjunto com o Blackfire para criar cenários de criação de perfil de script.

Para ativar o Blackfire, use a configuração **features** no seu arquivo de configuração do Homestead:

```yaml
features:
    - blackfire:
          server_id: "server_id"
          server_token: "server_value"
          client_id: "client_id"
          client_token: "client_value"
```

Credenciais do servidor Blackfire e credenciais do cliente [requer uma conta de usuário](https://blackfire.io/signup). O Blackfire oferece várias opções para o perfil de um aplicativo, incluindo uma ferramenta CLI e uma extensão do navegador. Por favor [revise a documentação do Blackfire para obter mais detalhes](https://blackfire.io/docs/cookbooks/index).

### Criação de perfil de desempenho do PHP usando o XHGui

[XHGui](https://www.github.com/perftools/xhgui) é uma interface de usuário para explorar o desempenho de seus aplicativos PHP. Para habilitar o XHGui, adicione `xhgui: 'true'` à configuração do seu site:

```yaml
sites:
    - map: your-site.test
      to: /home/vagrant/your-site/public
      type: "apache"
      xhgui: 'true'
```

Se o site já existir, certifique-se de executar o `vagrant provision` depois de atualizar sua configuração.

Para criar um perfil para uma solicitação da web, adicione `xhgui=on` como um parâmetro de consulta a uma solicitação. O XHGui anexará automaticamente um cookie à resposta para que solicitações subsequentes não precisem do valor da sequência de consultas. Você pode visualizar os resultados do perfil do aplicativo navegando para `http://your-site.test/xhgui`.

Para criar um perfil de uma solicitação de CLI usando o XHGui, prefixe o comando com `XHGUI=on`:

```bash
XHGUI=on path/to/script
```

Os resultados do perfil da CLI podem ser visualizados da mesma maneira que os resultados do perfil da web.

Observe que o ato de criar um perfil diminui a execução do script e os tempos absolutos podem ser até o dobro das solicitações do mundo real. Portanto, sempre compare melhorias percentuais e não números absolutos. Além disso, esteja ciente de que o tempo de execução inclui qualquer tempo gasto em pausa em um depurador.

Como os perfis de desempenho ocupam espaço em disco significativo, eles são excluídos automaticamente após alguns dias.

<a name="interfaces-de-rede"></a>
## Interfaces de rede

A propriedade `networks` do arquivo `Homestead.yaml` configura as interfaces de rede para o seu ambiente Homestead. Você pode configurar quantas interfaces forem necessárias:

```yaml
networks:
    - type: "private_network"
      ip: "192.168.10.20"
```

Para habilitar uma interface [bridged](https://www.vagrantup.com/docs/networking/public_network.html), defina uma configuração `bridge` e altere o tipo de rede para `public_network`:

```yaml
networks:
    - type: "public_network"
      ip: "192.168.10.20"
      bridge: "en1: Wi-Fi (AirPort)"
```

Para habilitar [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), basta remover a opção `ip` da sua configuração:

```yaml
networks:
    - type: "public_network"
      bridge: "en1: Wi-Fi (AirPort)"
```

<a name="estendendo-o-homestead"></a>
## Estendendo o Homestead

Você pode estender o Homestead usando o script `after.sh` na raiz do diretório do Homestead. Nesse arquivo, você pode adicionar quaisquer comandos de shell necessários para configurar e personalizar adequadamente sua máquina virtual.

Ao personalizar o Homestead, o Ubuntu pode perguntar se você deseja manter a configuração original de um pacote ou substituí-la por um novo arquivo de configuração. Para evitar isso, você deve usar o seguinte comando ao instalar pacotes para evitar a substituição de qualquer configuração previamente escrita pelo Homestead:

```bash
sudo apt-get -y \
    -o Dpkg::Options::="--force-confdef" \
    -o Dpkg::Options::="--force-confold" \
    install your-package
```

### Personalizações de usuário

Ao usar o Homestead em um ambiente de equipe, você pode ajustar o Homestead para melhor se adequar ao seu estilo de desenvolvimento pessoal. Você pode criar um arquivo `user-customizations.sh` na raiz do seu diretório Homestead (o mesmo diretório que contém seu arquivo `Homestead.yaml`). Nesse arquivo, você pode fazer qualquer personalização que desejar, no entanto, o `user-customizations.sh` não deve ser controlado por versão.

<a name="atualizando-o-homestead"></a>
## Atualizando o Homestead

Antes de começar a atualizar o Homestead, verifique se você removeu sua máquina virtual atual executando o seguinte comando no diretório do Homestead:

```bash
vagrant destroy
```

Em seguida, você precisa atualizar o código fonte do Homestead. Se você clonou o repositório, pode executar os seguintes comandos no local em que você originalmente clonou o repositório:

```bash
git fetch

git pull origin release
```

Esses comandos obtêm o código mais recente do Homestead do repositório GitHub, buscam as tags mais recentes e, em seguida, fazem check-out da versão mais recente marcada. Você pode encontrar a versão estável mais recente no site [GitHub releases page](https://github.com/laravel/homestead/releases).

Se você instalou o Homestead através do arquivo `composer.json` do seu projeto, verifique se o arquivo `composer.json` contém `"laravel/homestead": "^10"` e atualize suas dependências:

```bash
composer update
```

Então, você deve atualizar a **box** do Vagrant usando o comando `vagrant box update`:

```bash
vagrant box update
```

Por fim, você precisará regenerar sua caixa do Homestead para utilizar a instalação mais recente do Vagrant:

```bash
vagrant up
```

<a name="configuracoes-especificas-do-provider"></a>
## Configurações específicas do provider

<a name="virtualbox"></a>
### VirtualBox

#### `natdnshostresolver`

Por padrão, o Homestead configura a configuração `natdnshostresolver` para `on`. Isso permite que o Homestead use as configurações de DNS do sistema operacional host. Se você deseja substituir esse comportamento, adicione as seguintes linhas ao seu arquivo `Homestead.yaml`:

```yaml
provider: virtualbox
natdnshostresolver: 'off'
```

#### Links simbólicos no Windows

Se os links simbólicos não estiverem funcionando corretamente na sua máquina Windows, talvez seja necessário adicionar o seguinte bloco ao seu `Vagrantfile`:

```text
config.vm.provider "virtualbox" do |v|
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
end
```
