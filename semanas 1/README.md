# Semana 1 - Prática Linux Gamificada

## 📝 Descrição

Missão prática no Google Cloud Shell para aprender comandos Linux essenciais aplicados ao contexto de Big Data. Esta semana simula ambientes reais de clusters Hadoop, explorando conceitos de HDFS, MapReduce, Docker e monitoramento de recursos.

---

## 🎯 Objetivos

- Configurar e acessar ambiente Linux (Google Cloud Shell)
- Criar estrutura de Data Lake simulando zonas Bronze/Silver/Gold
- Implementar pipelines de processamento com operadores Unix
- Monitorar recursos de CPU e memória como um SRE
- Simular nós de cluster com containers Docker
- Praticar permissões e logs de auditoria
- Automatizar tarefas com scripts bash

---

# 📋 Entregas dos Exercícios

---

# Exercício 1: ⚡ Acessando o Ambiente (Nível Zero)

## Objetivo

Configurar e verificar o ambiente Linux no Google Cloud Shell.

## Comandos Executados

```bash
uname -a

pwd
```

## 📸 Print de Tela

### Verificar o ambiente
![Verificar o ambiente](Verificar o ambiente.png)

---

# Exercício 2: 🗄️ Organização do Data Lake

## Objetivo

Simular a estrutura de diretórios de um Data Lake com zonas de dados.

## Comandos Executados

```bash
mkdir -p bigdata/raw
mkdir -p bigdata/processed
mkdir -p bigdata/logs

ls -R bigdata
```

## 📸 Prints de Tela

### Estrutura de diretórios
![Estrutura](Crie%20a%20estrutura%20de%20diretórios.png)

### Dataset fictício
![Dataset](Crie%20um%20dataset%20fictício.png)

---

# Exercício 3: 🔁 Pipeline de Processamento

## Objetivo

Simular processamento distribuído utilizando pipes do Linux.

## Comandos Executados

```bash
cat bigdata/raw/dados.csv | grep produtoA > bigdata/processed/dados_filtrados.csv

cat bigdata/raw/dados.csv | wc -l

cat bigdata/raw/dados.csv | sort | uniq
```

## 📸 Prints de Tela

### Pipeline
![Pipeline](Simule%20uma%20transformação%20de%20dados%20via%20pipeline.png)

### MapReduce
![MapReduce](Simulação%20de%20MapReduce.png)

---

# Exercício 4: 📊 Monitoramento de Recursos

## Objetivo

Monitorar CPU, memória e processos do sistema.

## Comandos Executados

```bash
top

yes > /dev/null &

top

kill <PID>
```

## 📸 Prints de Tela

### Simulação de carga de CPU
![CPU](Simule%20carga%20de%20CPU.png)

### Uso do sistema
![Sistema](Verificar%20uso%20do%20sistema.png)

---

# Exercício 5: 🐳 Containers — Simulando Nós de Cluster

## Objetivo

Criar containers Docker simulando nós master e worker.

## Comandos Executados

```bash
docker --version

docker network create cluster-net

docker run -dit --name master --network cluster-net ubuntu

docker run -dit --name worker --network cluster-net ubuntu

docker ps
```

## 📸 Prints de Tela

### Docker e rede
![Docker](Verifique%20o%20Docker%20e%20crie%20a%20rede%20do%20cluster.png)

### Containers
![Containers](Crie%20os%20containers%20master%20e%20worker.png)

### Master e ping
![Master](Entre%20no%20master%20e%20instale%20o%20ping.png)

---

# Exercício 6: 🔐 Scripts e Permissões

## 📸 Prints de Tela

### Script de inicialização
![Script](Crie%20o%20script%20de%20inicialização1.png)

### Permissão de execução
![Permissão](Permita%20execução%20e%20execute%20o%20script.png)

---

# 📊 Resultados Esperados

Ao concluir esta semana, foi possível:

- ✅ Aprender comandos Linux essenciais
- ✅ Simular um Data Lake
- ✅ Trabalhar com pipelines de dados
- ✅ Monitorar recursos do sistema
- ✅ Utilizar Docker para simulação de clusters
- ✅ Automatizar tarefas com scripts bash

---

# 📚 Referências

- Google Cloud Shell
- Hadoop HDFS
- Docker
- Linux Commands
- Conceitos de Big Data e Data Lake
