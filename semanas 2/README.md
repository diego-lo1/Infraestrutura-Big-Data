# Semana 2 - Entregas da Disciplina

## 📝 Descrição

Registro das entregas práticas da Semana 2 da disciplina de **Infraestrutura de Big Data** (IAL008 · Fatec Ourinhos · Prof. Isaque Katahira). Instalação e configuração de um cluster Hadoop HDFS pseudo-distribuído no Ubuntu via WSL2, cobrindo Java, SSH, variáveis de ambiente, arquivos XML e operações básicas no HDFS.

## 🎯 Objetivos

- Instalar o Java JDK 11 e criar o usuário dedicado `hadoop`
- Configurar autenticação SSH sem senha para o cluster
- Baixar e instalar o Hadoop 3.3.6
- Configurar variáveis de ambiente no `.bashrc`
- Configurar os arquivos XML do Hadoop (`core-site`, `hdfs-site`, `mapred-site`, `yarn-site`)
- Formatar e iniciar os serviços HDFS e YARN
- Executar operações básicas no sistema de arquivos distribuído (HDFS)

---

## 📋 Entregas dos Exercícios

### Missão 1: 🔧 Preparar o Sistema — Java e Dependências

**Objetivo:** Instalar o Java JDK 11 (requisito do Hadoop 3.x) e criar um usuário dedicado chamado `hadoop` para rodar o cluster de forma segura e isolada.

#### Comandos Executados:

```bash
# Atualizar o sistema
sudo apt update

# Instalar o Java JDK 11
sudo apt install openjdk-11-jdk -y

# Verificar a instalação do Java
java -version

# Criar o usuário hadoop
sudo adduser hadoop

# Adicionar ao grupo sudo
sudo usermod -aG sudo hadoop

# Mudar para o usuário hadoop
su - hadoop
```

#### 📸 Print de Tela:

![Verificar versão do Java](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/java-version.png)

![Usuário hadoop criado e logado](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/usuario-hadoop.png)

#### 🧠 Questão Respondida:

**Q: Por que é recomendado criar um usuário separado para rodar o Hadoop?**  
**R:** Isolar o Hadoop em um usuário dedicado é uma boa prática de segurança. Caso o cluster seja comprometido, o invasor terá acesso apenas ao que o usuário `hadoop` pode acessar, sem expor o sistema inteiro. Também facilita o gerenciamento de permissões e a identificação dos processos do cluster.

---

### Missão 2: 🔑 Configurar SSH sem Senha

**Objetivo:** Configurar a autenticação SSH por chaves para que o Hadoop consiga se comunicar entre os nós (e consigo mesmo em modo pseudo-distribuído) sem necessidade de senha.

#### Comandos Executados:

```bash
# Instalar o servidor SSH
sudo apt install openssh-server openssh-client -y

# Gerar o par de chaves SSH (RSA, sem senha)
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

# Adicionar a chave pública às chaves autorizadas
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

# Ajustar permissões
chmod 600 ~/.ssh/authorized_keys

# Testar a conexão SSH sem senha
ssh localhost

# Sair da sessão SSH
exit
```

#### 📸 Print de Tela:

![Testando conexão SSH sem senha](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/testando-conexao-ssh-sem-senha.png)

#### 🧠 Questão Respondida:

**Q: Por que o Hadoop precisa de SSH sem senha configurado, mesmo em modo pseudo-distribuído?**  
**R:** O Hadoop usa SSH para iniciar e parar os daemons (NameNode, DataNode, ResourceManager, NodeManager) nos nós do cluster. Mesmo rodando em um único computador, os scripts `start-dfs.sh` e `start-yarn.sh` se conectam via SSH ao `localhost` para lançar os serviços automaticamente. Sem autenticação por chave, o processo ficaria parado aguardando senha e não subiria o cluster.

---

### Missão 3: 📦 Baixar e Instalar o Hadoop 3.3

**Objetivo:** Baixar o Hadoop 3.3.6 diretamente do servidor Apache, extrair e instalar no diretório `/usr/local/hadoop` com as permissões corretas para o usuário hadoop.

#### Comandos Executados:

```bash
# Baixar o Hadoop 3.3.6
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz

# Extrair no diretório /usr/local/
sudo tar xzf hadoop-3.3.6.tar.gz -C /usr/local/

# Renomear para um nome mais curto
sudo mv /usr/local/hadoop-3.3.6 /usr/local/hadoop

# Mudar o dono da pasta para o usuário hadoop
sudo chown -R hadoop:hadoop /usr/local/hadoop

# Verificar a instalação
ls /usr/local/hadoop

# Confirmar a versão instalada
hadoop version
```

#### 📸 Print de Tela:

![Verificar instalação do Hadoop](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/verificar-instalaçao-hadoop.png)

![Versão do Hadoop 3.3.6](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/hadoop-versão.png)

#### 🧠 Questão Respondida:

**Q: O que significa o comando `sudo chown -R hadoop:hadoop /usr/local/hadoop` e por que ele é necessário?**  
**R:** O `chown` (change owner) altera o dono e grupo de arquivos e diretórios. O `-R` aplica recursivamente em todos os arquivos internos. O formato `hadoop:hadoop` define o usuário *e* o grupo proprietário simultaneamente. Sem essa etapa, o usuário `hadoop` não teria permissão de escrita no diretório de instalação, impedindo o Hadoop de criar arquivos de log, iniciar daemons e gravar metadados.

---

### Missão 4: ⚙️ Configurar Variáveis de Ambiente

**Objetivo:** Configurar as variáveis de ambiente no arquivo `~/.bashrc` para que o sistema saiba onde o Hadoop e o Java estão instalados, tornando os comandos `hadoop`, `hdfs` e `yarn` disponíveis no terminal.

#### Comandos Executados:

```bash
# Descobrir o caminho correto do Java
readlink -f $(which java) | sed "s|/bin/java||"

# Editar o arquivo .bashrc
nano ~/.bashrc

# Adicionar no final do arquivo:
# ===== HADOOP ENVIRONMENT =====
# export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
# export HADOOP_HOME=/usr/local/hadoop
# export HADOOP_INSTALL=$HADOOP_HOME
# export HADOOP_MAPRED_HOME=$HADOOP_HOME
# export HADOOP_COMMON_HOME=$HADOOP_HOME
# export HADOOP_HDFS_HOME=$HADOOP_HOME
# export YARN_HOME=$HADOOP_HOME
# export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
# export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
# export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"

# Recarregar as configurações
source ~/.bashrc

# Verificar as variáveis
echo $HADOOP_HOME
echo $JAVA_HOME
hadoop version
```

#### 📸 Print de Tela:

![Editar .bashrc e adicionar variáveis](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/editar-bashrc-e-adicionar-variaveis.png)

![Verificar as variáveis de ambiente](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/verificar-as-variaveis.png)

#### 🧠 Questão Respondida:

**Q: Qual a diferença entre usar `export` e simplesmente definir uma variável no `.bashrc`?**  
**R:** O `export` torna a variável disponível para todos os processos *filhos* do shell atual. Sem ele, a variável existe apenas no shell corrente e não é visível para programas executados a partir dele — como os scripts do Hadoop (`start-dfs.sh`, `hdfs`, etc.). O `source ~/.bashrc` é necessário para recarregar o arquivo no shell atual sem precisar fechar e reabrir o terminal.

---

### Missão 5: 📝 Configurar os Arquivos XML do Hadoop

**Objetivo:** Configurar os quatro arquivos XML que definem o comportamento do cluster: `hadoop-env.sh` (caminho do Java), `core-site.xml` (endereço do NameNode), `hdfs-site.xml` (replicação e caminhos dos nós), `mapred-site.xml` (framework MapReduce) e `yarn-site.xml` (gerenciador de recursos).

#### Comandos Executados:

```bash
# 1. Configurar hadoop-env.sh — Caminho do Java
nano /usr/local/hadoop/etc/hadoop/hadoop-env.sh
# Adicionar: export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

# 2. Configurar core-site.xml — Endereço do NameNode
nano /usr/local/hadoop/etc/hadoop/core-site.xml

# 3. Configurar hdfs-site.xml — Replicação e Caminhos dos Nós
nano /usr/local/hadoop/etc/hadoop/hdfs-site.xml

# 4. Configurar mapred-site.xml — Framework do MapReduce
nano /usr/local/hadoop/etc/hadoop/mapred-site.xml

# 5. Configurar yarn-site.xml — Gerenciador de Recursos
nano /usr/local/hadoop/etc/hadoop/yarn-site.xml
```

#### 📸 Print de Tela:

![Configurar Caminho do Java para o Hadoop (hadoop-env.sh)](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Configurar-Caminho-do-Java-para-o-Hadoop.png)

![Configurar core-site.xml — Endereço do NameNode](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Configurar-core-site-xml-Endereço-do-NameNode.png)

![Configurar hdfs-site.xml — Replicação e Caminhos dos Nós](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Configurar-hdfs-site-xml-Replicaç-o-e-Caminhos-dos-Nós.png)

![Configurar mapred-site.xml — Framework do MapReduce](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Configurar-mapred-site-xml-Framework-do-MapReduce.png)

![Configurar yarn-site.xml — Gerenciador de Recursos](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Configurar-yarn-site-xml-Gerenciador-de-Recursos.png)

#### 🧠 Questão Respondida:

**Q: Por que o `dfs.replication` é configurado como `1` no `hdfs-site.xml` em modo pseudo-distribuído?**  
**R:** Em produção o padrão de replicação é 3 (três cópias de cada bloco em DataNodes diferentes, garantindo tolerância a falhas). Em modo pseudo-distribuído há apenas um DataNode, então se configurarmos replicação maior que 1 o HDFS ficaria permanentemente em estado *Under replicated*, reportando inconsistências. Com `replication=1` o sistema funciona de forma saudável com um único nó.

---

### Missão 6: 🚀 Formatar e Iniciar o Cluster HDFS

**Objetivo:** Formatar o NameNode (inicialização única do sistema de arquivos HDFS), iniciar os serviços HDFS e YARN, verificar os processos com `jps` e acessar as interfaces web de monitoramento.

#### Comandos Executados:

```bash
# Criar os diretórios necessários
mkdir -p ~/tmpdata
mkdir -p ~/dfsdata/namenode
mkdir -p ~/dfsdata/datanode

# Formatar o NameNode (apenas uma vez!)
hdfs namenode -format

# Iniciar os serviços HDFS
start-dfs.sh

# Iniciar os serviços YARN
start-yarn.sh

# Verificar os processos em execução (deve mostrar 5 processos)
jps

# Interfaces web disponíveis:
# localhost:9870 → NameNode Web UI (monitoramento HDFS)
# localhost:8088 → Resource Manager (monitoramento YARN)
# localhost:9864 → DataNode Web UI
```

#### 📸 Print de Tela:

![Iniciar HDFS, YARN e verificar processos com jps](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Iniciar-os-serviços-HDFS-YARN-e-Verificar-os-processos-em-execução-com-jps.png)

![Interface Web do Hadoop — NameNode (localhost:9870)](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/interface-web-do-hadoop-1.png)

![Interface Web do Hadoop — YARN Resource Manager (localhost:8088)](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/interface-web-do-hadoop-2.png)

![Interface Web do Hadoop — DataNode (localhost:9864)](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/interface-web-do-hadoop-3.png)

#### 🧠 Questão Respondida:

**Q: O que acontece se o `hdfs namenode -format` for executado mais de uma vez? Qual o problema gerado?**  
**R:** Cada formatação gera um novo `clusterID` para o NameNode. O DataNode armazena o `clusterID` do cluster ao qual pertence no primeiro início. Se o NameNode for reformatado, ele passa a ter um ID diferente do que o DataNode conhece — gerando o erro *"Incompatible clusterIDs"*. O DataNode se recusa a se conectar e não aparece no `jps`. A solução é limpar os dados de ambos (`~/dfsdata/namenode/*` e `~/dfsdata/datanode/*`) e reformatar uma única vez.

---

### Missão 7: 📂 Operações Básicas no HDFS

**Objetivo:** Executar os comandos essenciais do HDFS para criar diretórios, enviar arquivos, verificar o conteúdo e analisar a integridade e distribuição dos blocos com o comando `fsck`.

#### Comandos Executados:

```bash
# Listar a raiz do HDFS
hdfs dfs -ls /

# Criar estrutura de diretórios no HDFS
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/hadoop
hdfs dfs -mkdir /user/hadoop/dados

# Criar arquivo de teste local
echo "Olá, Hadoop HDFS! Este é meu primeiro arquivo no cluster Big Data." > ~/teste.txt
cat ~/teste.txt

# Enviar o arquivo ao HDFS
hdfs dfs -put ~/teste.txt /user/hadoop/dados/

# Verificar o arquivo no HDFS
hdfs dfs -ls /user/hadoop/dados/

# Ler o conteúdo do arquivo diretamente do HDFS
hdfs dfs -cat /user/hadoop/dados/teste.txt

# Ver informações detalhadas de blocos e localização
hdfs fsck /user/hadoop/dados/teste.txt -files -blocks -locations

# Verificar espaço em disco do HDFS
hdfs dfs -df -h /
```

#### 📸 Print de Tela:

![Verificar o arquivo no HDFS](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Verificar-o-arquivo-no-HDFS.png)

![Ver informações detalhadas com fsck](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%202/prints2/Ver-informações-detalhadas-fsck.png)

#### 🧠 Questão Respondida:

**Q: O que o comando `hdfs fsck` revela sobre o arquivo enviado ao HDFS e qual a importância dessa verificação?**  
**R:** O `fsck` (File System Check) exibe detalhes técnicos do arquivo no HDFS: tamanho, número de blocos, fator de replicação real, ID de cada bloco e em quais DataNodes cada bloco está armazenado. No resultado obtido, o arquivo `teste.txt` de 69 bytes ocupa 1 bloco com status `OK` e o sistema reporta `Status: HEALTHY`. Essa verificação é fundamental em produção para identificar blocos *under-replicated* (com menos cópias do que o esperado), blocos corrompidos ou nós com problema de armazenamento.

---

## 📊 Resultados Esperados

Ao concluir esta semana, você terá:
- ✅ Java 11 instalado e usuário `hadoop` configurado
- ✅ SSH sem senha funcionando para comunicação do cluster
- ✅ Hadoop 3.3.6 instalado em `/usr/local/hadoop`
- ✅ Variáveis de ambiente configuradas no `.bashrc`
- ✅ Arquivos XML do Hadoop configurados para modo pseudo-distribuído
- ✅ Cluster HDFS + YARN em execução com 5 processos confirmados via `jps`
- ✅ Operações básicas no HDFS executadas e documentadas
- ✅ Interfaces web acessíveis em `localhost:9870`, `localhost:8088` e `localhost:9864`

## 📚 Referências

- [Semana 02 — Missão Hadoop HDFS (ikatahira.github.io)](https://ikatahira.github.io/infraestrutura_big_data/semanas/semana02.html)
- [Apache Hadoop 3.3.6 — Documentação Oficial](https://hadoop.apache.org/docs/r3.3.6/)
- [Hadoop: The Definitive Guide — Tom White](https://www.oreilly.com/library/view/hadoop-the-definitive/9781491901687/)
- Google Cloud Shell / WSL2 Ubuntu 22.04
