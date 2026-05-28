# Semana 3 - Entregas da Disciplina

## 📝 Descrição

Registro das entregas práticas da Semana 3 da disciplina de **Infraestrutura de Big Data** (IAL008 · Fatec Ourinhos · Prof. Isaque Katahira). Implementação do paradigma MapReduce utilizando Python + mrjob no **Google Cloud Shell**, com desenvolvimento do WordCount e do LogAnalyzer.

## 🎯 Objetivos

- Compreender o modelo de programação distribuída MapReduce (Map → Shuffle & Sort → Reduce)
- Configurar o ambiente de desenvolvimento no Google Cloud Shell
- Implementar o WordCount em Python com a biblioteca mrjob
- Executar o WordCount sobre um arquivo de texto real
- Criar um analisador de logs (LogAnalyzer) que conta ocorrências por nível (ERROR/WARN/INFO)

---

## 📋 Entregas dos Exercícios

### Exercício 1: ☁️ Configurar o Ambiente — Verificar Python e Instalar mrjob

**Objetivo:** Acessar o Google Cloud Shell e preparar o ambiente instalando a biblioteca `mrjob`, que permite escrever jobs MapReduce em Python sem precisar instalar o Hadoop localmente.

#### Comandos Executados:

```bash
# Verificar a versão do Python (deve mostrar Python 3.x)
python3 --version

# Instalar a biblioteca mrjob
pip3 install mrjob

# Confirmar que a instalação foi bem-sucedida
python3 -c "import mrjob; print('mrjob OK')"
```

#### 📸 Print de Tela:

![Verificar o Python e instalar o mrjob](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%203/prints3/Verifique-o-Python-e-instale-o-mrjob.png)

#### 🧠 Questão Respondida:

**Q (Quiz 1): Qual fase do MapReduce é responsável por agrupar todos os valores com a mesma chave antes de enviá-los ao Reducer?**  
**R:** B) **Shuffle & Sort** — fase intermediária automática do framework. O Hadoop executa o Shuffle sem que o programador precise implementá-lo. Ele garante que todos os pares `("hadoop", 1)` emitidos por diferentes Mappers em diferentes nós chegam ao mesmo Reducer, permitindo a agregação correta dos resultados.

---

### Exercício 2: 📁 Criar a Pasta de Trabalho e o Arquivo de Texto

**Objetivo:** Organizar o ambiente de trabalho criando a estrutura de diretórios e o arquivo `texto.txt` com 5 linhas de conteúdo que será usado como entrada para o WordCount.

#### Comandos Executados:

```bash
# Criar a pasta de trabalho e entrar nela
mkdir -p ~/bigdata/semana03 && cd ~/bigdata/semana03

# Criar o arquivo de texto com 5 linhas
cat > texto.txt << 'EOF'
big data e o futuro da tecnologia
hadoop processa big data em clusters
spark e mais rapido que hadoop na memoria
big data com spark hadoop e python
machine learning usa big data para aprender
EOF

# Confirmar o conteúdo do arquivo (deve mostrar as 5 linhas)
cat texto.txt
```

#### 📸 Print de Tela:

![Criar a pasta de trabalho e o arquivo de texto](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%203/prints3/Crie-a-pasta-de-trabalho-e-o-arquivo-de-texto.png)

#### 🧠 Questão Respondida:

**Q (Quiz 3): No arquivo `texto.txt` criado com 5 linhas, qual a contagem da palavra "big" após executar o WordCount?**  
**R:** D) **4** — "big" aparece nas linhas 1 (`"big data e o futuro..."`), 2 (`"hadoop processa big data..."`), 4 (`"big data com spark..."`), e 5 (`"usa big data para..."`). O Mapper emite `("big", 1)` para cada ocorrência sem agrupar; o Shuffle & Sort agrupa todas as 4 ocorrências e o Reducer soma: `sum([1,1,1,1]) = 4`.

---

### Exercício 3: 🐍 Criar o arquivo `wordcount.py` com o editor nano

**Objetivo:** Implementar o algoritmo WordCount em Python usando a biblioteca `mrjob`, com a função `mapper` emitindo pares `(palavra, 1)` para cada token e a função `reducer` somando as contagens agrupadas pelo Shuffle.

#### Comandos Executados:

```bash
# Abrir o editor nano para criar o arquivo
nano wordcount.py

# Conteúdo do arquivo wordcount.py:
# from mrjob.job import MRJob
# import re
#
# class WordCount(MRJob):
#
#     def mapper(self, _, line):
#         palavras = re.findall(r'\w+', line.lower())
#         for palavra in palavras:
#             yield palavra, 1
#
#     def reducer(self, palavra, contagens):
#         yield palavra, sum(contagens)
#
# if __name__ == '__main__':
#     WordCount.run()

# Salvar: Ctrl+O → Enter → Ctrl+X para sair

# Confirmar que o arquivo foi criado
ls -la wordcount.py
```

#### 📸 Print de Tela:

![Criar o arquivo wordcount.py com o editor nano](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%203/prints3/Crie-o-arquivo-wordcount-py-com-o-editor-nano.png)

#### 🧠 Questão Respondida:

**Q (Quiz 2): O Mapper recebe a linha `"hadoop spark hadoop"`. Quantas vezes ele emite a chave `"hadoop"`?**  
**R:** B) **2 vezes** — o Mapper emite `(palavra, 1)` para cada ocorrência encontrada, sem agrupar. A linha `"hadoop spark hadoop"` gera 3 pares: `("hadoop",1)`, `("spark",1)`, `("hadoop",1)`. O agrupamento só acontece na fase de **Shuffle & Sort**, que é executada automaticamente pelo framework entre o Map e o Reduce. Essa separação de responsabilidades é o que permite o paralelismo real do MapReduce.

---

### Exercício 4: ⚙️ Missão 1 — Executar o WordCount

**Objetivo:** Executar o job MapReduce `wordcount.py` sobre o arquivo `texto.txt` e verificar os resultados, confirmando as contagens de palavras como `"big": 4`, `"hadoop": 3` e `"spark": 2`.

#### Comandos Executados:

```bash
# Executar o WordCount (2>/dev/null oculta logs internos do mrjob)
python3 wordcount.py texto.txt 2>/dev/null

# Salvar o resultado para entrega
python3 wordcount.py texto.txt 2>/dev/null > resultado.txt
cat resultado.txt

# BÔNUS: ver as 10 palavras mais frequentes em ordem decrescente
python3 wordcount.py texto.txt 2>/dev/null | sort -t$'\t' -k2 -rn | head -10
```

#### 📸 Print de Tela:

![Executar o WordCount](https://raw.githubusercontent.com/diego-lo1/Infraestrutura-Big-Data/main/semanas%203/prints3/Execute-o-WordCount.png)

#### 🧠 Questão Respondida:

**Q: O que o parâmetro `2>/dev/null` faz ao executar o WordCount e por que ele é usado?**  
**R:** O `2>` redireciona o **stderr** (saída de erros padrão, descritor de arquivo 2) para `/dev/null`, que é um dispositivo especial do Linux que descarta tudo que recebe. O mrjob imprime mensagens internas de progresso e configuração no stderr. Redirecionando para `/dev/null`, apenas a saída real do MapReduce (os pares `palavra → contagem`) aparece no terminal, tornando o resultado mais limpo e legível.

---

### Exercício 5: 📊 Missão 2 — LogAnalyzer: Contar Erros por Tipo

**Objetivo:** Criar um novo job MapReduce chamado `loganalyzer.py` que lê um arquivo de logs e conta quantas linhas são de cada nível (`ERROR`, `WARN`, `INFO`), extraindo a 3ª palavra de cada linha como chave.

#### Comandos Executados:

```bash
# Criar o arquivo de logs para testar
cat > app.log << 'EOF'
2024-03-15 10:23:45 INFO Servidor iniciado na porta 8080
2024-03-15 10:23:46 INFO Banco de dados conectado com sucesso
2024-03-15 10:24:01 WARN Memoria acima de 80 por cento
2024-03-15 10:24:15 ERROR NullPointerException em UserService
2024-03-15 10:24:30 INFO Requisicao GET processada em 120ms
2024-03-15 10:24:45 ERROR Timeout ao conectar com servico externo
2024-03-15 10:25:00 WARN Resposta lenta detectada 3200ms
2024-03-15 10:25:15 ERROR NullPointerException em OrderService
2024-03-15 10:25:30 INFO Cache limpo com sucesso
2024-03-15 10:25:45 WARN Conexao instavel detectada
2024-03-15 10:26:00 ERROR Falha ao salvar no banco de dados
2024-03-15 10:26:15 INFO Backup automatico concluido
EOF

# Criar o loganalyzer.py
nano loganalyzer.py

# Conteúdo do loganalyzer.py:
# from mrjob.job import MRJob
#
# class LogAnalyzer(MRJob):
#
#     def mapper(self, _, line):
#         partes = line.split()
#         if len(partes) >= 3:
#             nivel = partes[2]   # 3ª palavra = ERROR, WARN ou INFO
#             yield nivel, 1
#
#     def reducer(self, nivel, contagens):
#         yield nivel, sum(contagens)
#
# if __name__ == '__main__':
#     LogAnalyzer.run()

# Executar o LogAnalyzer
python3 loganalyzer.py app.log 2>/dev/null
```

#### 📸 Print de Tela:

*[Print da execução do loganalyzer.py — adicionar após executar a missão]*

#### 🧠 Questão Respondida:

**Q: No LogAnalyzer, por que usamos `partes[2]` (índice 2) para extrair o nível de log, e não `partes[0]` ou `partes[1]`?**  
**R:** O formato de cada linha de log é `DATA HORA NIVEL MENSAGEM` — por exemplo: `"2024-03-15 10:23:45 ERROR NullPointerException"`. Após o `line.split()`, o índice 0 contém a data, o índice 1 contém a hora e o índice 2 contém o nível (`ERROR`, `WARN` ou `INFO`). Usar `partes[2]` extrai exatamente o campo que queremos usar como chave no MapReduce. A verificação `len(partes) >= 3` garante que linhas malformadas ou vazias não causem erros de índice.

---

## 📊 Resultados Esperados

Ao concluir esta semana, você terá:
- ✅ Ambiente Google Cloud Shell configurado com Python 3 e mrjob
- ✅ Compreensão do pipeline MapReduce: Map → Shuffle & Sort → Reduce
- ✅ WordCount implementado em Python e executado com sucesso
- ✅ Resultados confirmados: `"big": 4`, `"hadoop": 3`, `"spark": 2`
- ✅ LogAnalyzer implementado, contando `ERROR: 4`, `INFO: 5`, `WARN: 3`

## 📚 Referências

- [Semana 03 — MapReduce (ikatahira.github.io)](https://ikatahira.github.io/infraestrutura_big_data/semanas/semana03.html)
- [Google Cloud Shell](https://shell.cloud.google.com/)
- [mrjob — Documentação Oficial](https://mrjob.readthedocs.io/)
- MapReduce: Simplified Data Processing on Large Clusters — Dean & Ghemawat, Google (2004)
