Vagrant é um software para a construção completa de ambientes de desenvolvimento virtuais desenvolvido por Mitchell Hashimoto e John Bender. Com um foco em automação, essa ferramenta diminui e facilita o tempo de configuração desses ambientes virtuais. A idéia é que um usuário poderá criar uma determinado conjunto de instruções, usando a DSL (Domain-Specific Language) do Vagrant, que irá montar uma, ou mais de uma, máquina virtual e  configurá-la. Sempre que o usuário rodar esse conjunto de instruções o resultado deverá ser o mesmo.

Para nós, desenvolvedores, o Vagrant irá isolar dependências e as configurações destas em um único ambiente virtual, consistente e descartável, sem precisar sacrificar nenhuma das ferramentas que você está acostumado a trabalhar. Depois de criado um único arquivo,o Vagrantfile, só precisamos dar um simples comando, no qual abordaremos mais adiante, que todo o seu ambiente irá ser montado, instalado e configurado para ser utilizado. É possível também replicar esse ambiente de maneira fácil e rápida. Se você faz parte de um time de desenvolvedores em um projeto, por exemplo, outros membros desse time podem criar os próprios ambientes de desenvolvimento do mesmo arquivo de configuração inicial, independente do sistema operacionai utilizado na máquina hospedeira, todos estarão utilizando o mesmo ambiente com a mesma configuração inicial. Como dito no próprio site do Vagrant: “Dê adeus à desculpa ‘na minha máquina roda’”!

Outro exemplo seria uma situação onde um desenvolvedor esteja constantemente alterando um software e fazendo deploys nele. Ora, quanto maior o número de deploys efetuados, maior a chance de que um desses deploys contenha falhas, não concordam? O desenvolvedor é humano, está sujeito a falhas, estresses e influências externas. Então, a ideia de se poder realizar uma alteração e testá-la em um ambiente controlado, e depois ser capaz de desfazer, editar ou acrescentar a essa alteração, é excelente! Isso diminui o risco durante o desenvolvimento ao mesmo tempo que permite a realização de um grande número de mudanças.

    Instalar o Vagrant no Ubuntu é extremamente fácil. Primeiro, precisaremos instalar o programa VirtualBox, que é um dos gigantes em que o Vagrant se apoia, utilizando-o para criar dinamicamente máquinas virtuais configuráveis, leves e portáteis. Para isso, execute o seguinte comando no terminal:

    sudo apt-get install virtualbox

Após o VirtualBox ter sido instalado corretamente, instalaremos agora o Vagrant. No
terminal, execute:

sudo apt-get install vagrant



Pronto! Já podemos começar a usar o Vagrant. Escolha e acesse, via terminal, um diretório qualquer da sua máquina. Iremos criar o arquivo que conterá toda a configuração inicial do nosso ambiente virtual. Esse arquivo é o Vagrantfile, mencionado anteriormente.
Ele será criado automaticamente quando executarmos o seguinte comando:

vagrant init

Após o comando ser completado, observamos que foi criado o Vagrantfile no diretório atual. Se você abrí-lo poderá ver o comando para levantar uma máquina virtual simples, sem configurações específicas (Vagrant::Config.run do |config|), e comentários padrão (todas as linhas iniciadas com ’#’). Nos comentários há um tutorial básico para a utilização desse arquivo.

No nosso caso, queremos que, quando mandarmos o Vagrant levantar nosso ambiente, ele próprio já instale e configure o JDK7 e o Tomcat7, além de inicializar o Tomcat. Para isso, crie um arquivo de texto, no mesmo diretório do seu Vagrantfile, chamado “inicializacao.sh”. Nele, digite: 

sudo apt-get -y update
sudo apt-get -y install openjdk-7-jdk
sudo apt-get -y install tomcat7
sudo service tomcat7 start

Salve o arquivo e feche-o. O Vagrant irá executar esse shell script quando levantarmos a máquina e, exatamente como colocamos, rodar esses comandos um após o outro.

Agora, configuraremos como será o nosso ambiente virtual propriamente dito. Abra o Vagrantfile e apague tudo que estiver nele. Feito isso, digite:

    
Vagrant.configure("2") do |config|

    config.vm.box = "hashicorp/precise32"

    config.vm.provision :shell, path: "inicializacao.sh"

    config.vm.define :maquina do |maquina_config|
        maquina_config.vm.network "private_network", ip: "172.16.81.100"
    end

end

Na primeira linha estamos dizendo para o Vagrant, usando a versão dois da ferramenta, configurar uma máquina virtual. 


Na segunda, está especificado que imagem, chamada de box, será usada para a criação de todas as máquinas que usarem esse arquivo de configuração. Existem diversas imagens que podemos baixar na internet (pelo site: http://www.vagrantbox.es/) e utilizarmos, ou mesmo criar uma a partir do nosso sistema operacional. No nosso caso, o Vagrant irá usar o Ubuntu 32bits, que se chama hashicorp/precise32. Não é preciso baixar a imagem que usaremos imediatamente, pois ela será baixada automaticamente quando executarmos o comando para criação da máquina.


Na terceira linha, dizemos ao Vagrant para utilizar o arquivo shell script “inicializacao.sh” e executar os comandos nele contidos.


Na quarta linha, estamos dizendo ao Vagrant que a nossa máquina virtual a ser criada se chamará “maquina”. Além disso, estou abrindo uma configuração nessa máquina, onde especificamos qual será essa configuração na quinta linha. Nela, estamos configurando o ip da conexão da máquina para fixo e que ele será “172.16.81.100”.

    Terminamos o conjunto de configurações da nossa máquina virtual. Para mais informações sobre os comandos de configuração, acesse: https://docs.vagrantup.com/v2/

Agora, iremos executá-lo. Via terminal, acesse o diretório que você criou e que contém o arquivo Vagrantfile. Feito isso, digite: 

vagrant up

Executando esse comando, o Vagrant irá criar uma máquina virtual, instalar o Ubuntu 32bits nessa máquina usando a imagem determinada, que será baixada automaticamente caso não exista, e iniciará o Ubuntu. Se o comando foi executado com sucesso, já podemos utilizar nossa máquina virtual.

Pronto! Já criamos, configuramos e acessamos nosso ambiente de desenvolvimento virtual. E todo esse processo é automatizado pelo Vagrant. Podemos excluir a máquina virtual criada com o comando vagrant destroy e criarmos outra com o vagrant up, será uma máquina nova idêntica a anterior. Percebemos que não importa quantas máquinas criemos, elas sempre seguirão as configurações especificadas no Vagrantfile. 


