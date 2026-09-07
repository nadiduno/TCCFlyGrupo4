# 🚀 FixFly — prever para acolher, não para rotular

**Grupo Ada Lovelace (G4) · Turma Fly · diversiData**

> Cada Falso Negativo é uma mulher que a gente poderia ter acolhido **antes**. O modelo existe para chegar antes da evasão, nunca para rotular ninguém. 💛

🔗 **Repositório:** https://github.com/nadiduno/TCCFlyGrupo4

---

## 📖 Sobre este projeto

Este é o Trabalho de Conclusão de Curso do **Grupo Ada Lovelace (G4)**, feito para a **Fly** dentro do programa de formar mulheres para o mercado de tecnologia.

O ponto de partida não foi "vamos treinar um modelo". Foi uma pergunta concreta, feita por quem trabalha todo dia tentando manter mulheres dentro de um curso de tecnologia:

> **Dá para prever, no momento da inscrição, quais alunas correm mais risco de evadir do curso da Fly, usando só o que elas responderam no formulário?**

Repare na restrição: *só o que elas responderam no formulário*. Essa frase é o coração do trabalho — e é ela que produz a resposta mais importante do projeto, que você vai ler no fim deste README.

O notebook pega os arquivos que a Fly disponibilizou e vai até o fim: entende a base, reconstrói as variáveis, testa hipóteses, treina modelos, mede com honestidade — e diz exatamente o que dá e o que não dá para afirmar hoje. A ferramenta **FixFly** transforma o resultado em ação de acolhimento na mão das gestoras.

### As duas regras inegociáveis do projeto

1. **Dado é gente.** A base bruta tem texto livre com endereço, CPF e relato pessoal. Em nenhum momento exibimos a base crua — criamos um **script de anonimização em Python** e só trabalhamos com a versão harmonizada, 100% não identificável (LGPD por design).
2. **Nada de número enfeitado.** Se o modelo for fraco, o notebook diz que é fraco, mostra a conta e explica o que faltou. **Ausência de resultado documentada é resultado.**

---

## 🗂️ Sumário

- [O que este projeto entrega](#-o-que-este-projeto-entrega)
- [Como o notebook é organizado](#-como-o-notebook-é-organizado-as-estações)
- [Principais achados](#-principais-achados)
- [Estrutura do repositório](#-estrutura-do-repositório)
- [Como rodar](#️-como-rodar)
- [Sobre os dados](#-sobre-os-dados)
- [O modelo — a régua e a campeã](#-o-modelo--a-régua-e-a-campeã)
- [Ficha técnica do modelo (model card)](#-ficha-técnica-do-modelo-model-card)
- [FixFly — a camada de acolhimento](#-fixfly--a-camada-de-acolhimento)
- [Limitações](#️-limitações-assumidas-com-todas-as-letras)
- [Próximos passos](#️-próximos-passos)
- [Time](#-time)

---

## 🎯 O que este projeto entrega

| # | Entrega | Onde no notebook |
|---|---------|------------------|
| 1 | Raio-X e reconstrução da base: **163 colunas bagunçadas viram 15 variáveis limpas** e documentadas | Estações 1 a 5 |
| 2 | Um alvo `evadiu` **auditado**, com as fontes, as regras e os riscos escritos | Estação 6 |
| 3 | EDA com hipóteses, incerteza medida e o **confundimento exposto** | Estações 7 e 8 |
| 4 | Modelo honesto: comparação com validação cruzada, **baseline**, limiar escolhido pelo custo real, teste de embaralhamento e ficha técnica | Estações 9 a 15 |
| 5 | **FixFly**: a fila vira ação de acolhimento, com guarda de linguagem e trava ética | Estação 16 + `Prototivo-IAGenerativa-Fly.html` |

### Como ler o notebook

Todo bloco de código tem duas âncoras:

- **🔎 O que vamos fazer** — antes do código, diz o objetivo em uma frase.
- **📖 Como ler** — depois do resultado, diz o que aquilo significa e o que **não** significa.

Se você só quiser a história do projeto, leia as duas âncoras e pule o código.

---

## 🧭 Como o notebook é organizado (as estações)

| Estação | Pergunta que responde |
|---------|----------------------|
| 0 | Ambiente, semente aleatória e identidade visual (verde = ficou, coral = evadiu, sempre) |
| 1 | O que é cada arquivo que a Fly mandou? |
| 2 | Raio-X da base crua: tamanho, tipos de coluna, vazio estrutural × vazio real, dado pessoal |
| 3 | Costura das **163 colunas em 15 conceitos**, e padronização de cada variável |
| 4–5 | Cortes de uso e teste das hipóteses do grupo |
| 6 | Construção auditada do alvo `evadiu`, cruzando inscrições com backlogs |
| 7 | Análise exploratória, força de associação de cada variável (**V de Cramér**) |
| 8 | Limitações escritas **antes** de qualquer modelo ser treinado |
| 9–11 | Preparo dos dados e disputa entre 5 modelos sob o mesmo protocolo |
| 12 | Ajuste de hiperparâmetros e escolha do limiar de decisão pelo **custo real** (não o padrão 0,5) |
| 13–14 | Avaliação no teste e teste de embaralhamento (*permutation test*) |
| 15 | A pergunta que a Fly realmente faz: *"por quem eu começo a ligar?"* |
| 16 | O que fazer com o resultado: pedido de dados, próximos passos e a base para o **FixFly** |

---

## 🔍 Principais achados

Em cinco frases:

1. As 163 colunas eram uma dúzia de perguntas escritas de **cinco jeitos diferentes**; a costura documentada recuperou variáveis que, sozinhas, seriam inutilizáveis.
2. Só uma fração das inscrições chega ao fim do funil com perfil **e** desfecho — e a maior perda vem de **padronização de chave** (só 21% casam), não de dado inexistente.
3. O alvo `evadiu` é uma mistura de duas definições, e a fonte majoritária confunde evasão com registro faltante. **A taxa medida é um teto, não um valor exato.**
4. Nenhuma variável do formulário de inscrição tem associação mais que fraca com a evasão — e o **teste de embaralhamento** mede, com método, o quanto disso é sinal e o quanto é acaso (p empírico = **0,0244**: existe sinal real, ainda que fraco).
5. O gargalo é a **ausência de dados de engajamento durante o curso**. O notebook não descobriu quem evade: descobriu **qual dado a Fly precisa passar a coletar** para que a pergunta tenha resposta.

O gráfico final (Estação 15) traduz isso em algo acionável: comparando a fila ordenada pelo modelo com uma busca aleatória, mostra **quantas alunas em risco a equipe alcança se procurar as primeiras N da fila** — a métrica que importa para quem faz o acolhimento.

---

## 📁 Estrutura do repositório

```
TCCFlyGrupo4/
├── README.md                                  # este arquivo
├── Evasao FLy.pdf                             # relatório/guia do projeto
├── Prototivo-IAGenerativa-Fly.html            # ferramenta FixFly: fila, mensagens, copiloto e ficha técnica
├── renameCols_maps.py                         # dicionário de renomeação de colunas por turma
├── tcc_evasaofly_g4cienciadedadosfly.py       # notebook exportado (.py) — versões do fluxo/limpeza
│   └── (…v2, …v3, …V15)                        # histórico da evolução da limpeza e da modelagem
├── beneficio_pedemeia.csv                     # base pública — programa Pé-de-Meia (não integrada)
├── tx_rend_brasil_regioes_ufs_2023.xlsx       # base pública — rendimento/evasão por UF, 2023 (não integrada)
├── requirements.txt                           # dependências
├── .gitignore                                 # ignora dados sensíveis e artefatos gerados
└── dados/                                      # gerado ao rodar o notebook (NÃO versionado)
    ├── modelo_evasao_G4.joblib                # modelo treinado + limiar + lista de variáveis
    ├── ficha_do_modelo.json                   # model card gerado automaticamente
    └── fila_fixfly.json                       # fila de prioridade exportada para o FixFly
```

> 💡 A pasta `dados/` é gerada localmente ao rodar o notebook e pode conter artefatos derivados de dados sensíveis. Ela já está no `.gitignore`.

---

## ⚙️ Como rodar

### Pré-requisitos
- Python 3.10 ou superior
- Acesso aos arquivos originais da Fly (base de inscrições + backlogs) — os dados **não** estão neste repositório (ver *Sobre os dados*).

### 1. Clone o repositório
```bash
git clone https://github.com/nadiduno/TCCFlyGrupo4.git
cd TCCFlyGrupo4
```

### 2. Crie um ambiente virtual (recomendado)
```bash
python -m venv venv
source venv/bin/activate      # Linux/Mac
venv\Scripts\activate         # Windows
```

### 3. Instale as dependências
```bash
pip install -r requirements.txt
```

### 4. Rode o notebook
Abra o notebook no **Google Colab** (recomendado) ou no Jupyter e execute **Runtime → Run all**. Ele cria automaticamente a pasta `dados/` com o modelo treinado (`.joblib`) e a ficha técnica (`.json`).

> ⚠️ O notebook lê os dados a partir de links do Google Drive/Sheets. Para rodar com seus próprios dados, substitua as URLs `url_base` e `url_backlog` no início do notebook pelos caminhos dos seus arquivos.

### 5. Abra o FixFly
O `Prototivo-IAGenerativa-Fly.html` é **autocontido** — não precisa de servidor nem instalação. Basta abrir no navegador:
```bash
xdg-open "Prototivo-IAGenerativa-Fly.html"    # Linux
open "Prototivo-IAGenerativa-Fly.html"        # Mac
start "Prototivo-IAGenerativa-Fly.html"       # Windows
```
Ele abre com uma fila de exemplo carregada. Para usar a fila real gerada pelo notebook, veja a aba **Base e conexão** dentro da própria ferramenta.

---

## 🔒 Sobre os dados

Os dados contêm informações sensíveis de alunas reais (nome, endereço, relatos pessoais, CPF em abas antigas). Por isso:

- A **base crua nunca é exibida** em nenhum ponto do notebook — apenas a versão harmonizada e anonimizada, gerada por um **script de anonimização em Python** que o próprio grupo criou.
- Os arquivos de dados **não** fazem parte deste repositório e não devem ser commitados (o `.gitignore` já bloqueia).
- Qualquer reprodução deve seguir o mesmo cuidado: **tratar dado como gente, não como planilha.**

---

## 🤖 O modelo — a régua e a campeã

Comparação de candidatos, **sempre com o baseline dentro** (para expor a armadilha da acurácia):

| Modelo | Acurácia | Recall (evadiu) | F1 (evadiu) |
|--------|:--------:|:---------------:|:-----------:|
| Baseline (chute na classe mais comum) | 0,67 | 0,00 | 0,00 |
| Gradient Boosting | 0,65 | 0,61 | 0,54 |
| Random Forest | 0,59 | 0,78 | 0,56 |
| **★ Regressão Logística (campeã)** | 0,63 | **0,89** | **0,62** |

O baseline tem a maior acurácia (67%) e **recall 0** — acerta muito e não encontra ninguém que evade. Por isso a régua é o **recall**, não a acurácia.

**Modelo campeão: Regressão Logística** — melhor recall (0,89) e F1 (0,62). Em português: **de cada 9 alunas que vão evadir, o modelo encontra 8 a tempo de acolher.** Salvo em `modelo_evasao_G4.joblib` para alimentar o FixFly.

> A decisão não vem de uma rodada só: a comparação usa **validação cruzada** e um **teste de embaralhamento** (Estação 14) para separar sinal de acaso. Com este `n`, o ganho é real mas modesto — e o notebook diz isso com todas as letras.

---

## 🧾 Ficha técnica do modelo (model card)

O notebook gera automaticamente uma ficha técnica (`ficha_do_modelo.json`) junto com o modelo. Ela documenta: o modelo campeão e seus hiperparâmetros; a métrica principal (*average precision* / AUC-PR) comparada ao piso do acaso; o p-valor do teste de embaralhamento; o limiar operacional escolhido; e — o mais importante — dois campos que raramente aparecem em modelos em produção no Brasil:

| Campo | Conteúdo |
|-------|----------|
| ✅ **Uso permitido** | ordenar a fila de prioridade para ações de acolhimento |
| ❌ **Uso proibido** | informar risco individual à aluna · decidir desligamento, bolsa ou seleção · tirar conclusões sobre grupos com menos de 10 alunas |

Essa não é uma formalidade. Um modelo que ordena risco de evasão, se usado para decidir desligamento ou corte de bolsa, transforma uma ferramenta de acolhimento em **máquina de exclusão** — e faz isso justamente com as alunas mais vulneráveis, que são as que ele mais aponta.

---

## 🤝 FixFly — a camada de acolhimento

> *"Chegar antes da evasão, nunca rotular ninguém."*

O modelo, sozinho, entrega uma fila ordenada por risco — mas quem faz o acolhimento é gente, não algoritmo. O **FixFly** (`Prototivo-IAGenerativa-Fly.html`) é a ferramenta interna que transforma essa fila em ação da equipe da Fly, e é a resposta completa ao que a Estação 16 deixava como próximo passo.

É uma aplicação web autocontida (HTML/CSS/JS, sem backend obrigatório) organizada em **seis páginas**:

| Página | O que faz |
|--------|-----------|
| **Painel** | mostra a fila de prioridade e um controle de **capacidade da semana** — é esse número, definido pela equipe, e não o algoritmo, que decide o corte da fila |
| **Ficha da aluna** | o que a inscrição respondeu e o que ficou em branco, para embasar a conversa |
| **Mensagens** | gera rascunhos por trilha de apoio (abertura, acesso digital, rede de cuidado, logística, retomar contato) e por canal (WhatsApp/e-mail) — a gestora sempre revisa e envia, **a ferramenta nunca envia sozinha** |
| **Registro da conversa** | transforma anotação em texto livre em campos estruturados — é a **coluna de engajamento** que a base hoje não tem (limitação L5), sendo construída conversa por conversa |
| **Base e conexão** | carrega a fila real exportada da Estação 15, liga a uma API que serve o `.joblib`, e inclui um leitor que propõe o mapeamento de cabeçalhos de planilhas novas para os 15 conceitos |
| **Ficha técnica** | o model card completo dentro da ferramenta: métricas, variáveis, uso permitido/proibido e as limitações medidas |

### As três regras que a ferramenta aplica na prática

A camada de mensagens roda uma **guarda de linguagem** antes de qualquer texto aparecer na tela, garantindo que:

1. **Nunca** aparece uma probabilidade ou número de risco para a aluna — nem na fila, nem em nenhuma mensagem;
2. **Nunca** raça, orientação (LGBT+) ou deficiência viram motivo de contato, mesmo sendo variáveis usadas internamente pelo modelo — elas entram no cálculo, mas nunca atravessam para a tela;
3. As trilhas de apoio são sempre oferecidas a **todas as alunas da turma**, nunca como diagnóstico individual — o modelo não sabe qual é a barreira de cada aluna, e supor errado quebra a confiança logo na primeira mensagem.

Há também um **copiloto interno** (chat lateral) que responde perguntas sobre a fila e o modelo sempre citando a estação do notebook de onde veio a informação — e diz explicitamente quando a resposta **não existe** nos dados, em vez de inventar.

> 🔐 **Uso restrito à equipe da Fly.** A ferramenta funciona inteira sobre um JSON colado manualmente — nenhuma função depende de rede — e pode opcionalmente se conectar a uma API que sirva o modelo `.joblib`.

---

## ⚠️ Limitações, assumidas com todas as letras

| # | Limitação |
|---|-----------|
| **L1** | **Universo pequeno:** o conjunto de teste tem poucas dezenas de alunas, então o resultado depende muito de qual divisão treino/teste caiu — por isso o notebook usa validação cruzada repetida, nunca uma prova única. |
| **L2** | **Alvo misto:** a maior parte do universo vem de uma fonte cuja definição de evasão inclui possível registro faltante. A taxa medida é um teto. |
| **L3** | **Confundimento:** turma, período e fonte do desfecho estão colados. Efeito de contexto e efeito de perfil não se separam com estes dados. |
| **L4** | **Cobertura:** só 21% das chaves dos backlogs cruzam com a base de inscrições — e quem não cruza pode ser sistematicamente diferente. |
| **L5** | **Sem engajamento:** nenhuma variável de engajamento durante o curso está disponível — só variáveis declaradas na inscrição, antes do curso começar. É a limitação decisiva, e é a que a aba *Registro da conversa* do FixFly começa a resolver. |

---

## 🛣️ Próximos passos

**O que mudaria o resultado** (pedido de dados para a Fly, em ordem de impacto):
1. Presença e entrega de atividade, por semana e por aluna
2. A mesma chave `aluna_id` em todas as bases (inscrições e backlogs)
3. Status de saída padronizado, com data e motivo em lista fechada
4. A pergunta sobre mãe/cuidadora em todos os formulários
5. Backlogs com cabeçalho padronizado na primeira linha

**O que o grupo ainda pode explorar com os dados atuais:**
- Modelar isoladamente com a fonte mais confiável, como análise de sensibilidade
- Testar um alvo alternativo, separando "sem registro de formatura" de evasão de fato
- Levar a curva de priorização (Estação 15) para a Fly definir o limiar operacional com a equipe
- Integrar as bases públicas já no repositório (`beneficio_pedemeia.csv`, `tx_rend_brasil_regioes_ufs_2023.xlsx`) como enriquecimento

**Para o FixFly:**
- Subir uma API em produção para substituir a colagem manual de JSON
- Alimentar o modelo com os registros de conversa acumulados, fechando o ciclo que a limitação L5 aponta
- Validar o roteiro de mensagens com a equipe de acolhimento da Fly antes do uso em turmas reais

---

## 👩‍💻 Time

**Grupo Ada Lovelace (G4)** — Turma da Fly *"Mulheres In Tech: Data Science e IA · LGBTQIA+"*
Brenda Amaral · Fernanda da Silva · Nadi Duno · Profana Buzato · Sheilliane Santos · Vicência Vitória Souza
**Orientadora:** Andressa Freires · diversiData

Projeto desenvolvido como Trabalho de Conclusão de Curso, em parceria com a Fly.

<p align="center">Feito com rigor técnico e um compromisso: o modelo existe para chegar antes, nunca para rotular. 💛</p>
