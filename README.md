# 🚀 FixFly — Previsão de Evasão na Fly Educação > ** Uso de dados para identificar o risco de evasão na Fly Educação antes que ele aconteça.**
Modelo preditivo de potencial de empregabilidade e mobilidade financeira para egressas da Fly Educação: um modelo que identifica quem tem mais risco de evadir, **para agir antes**. 
**Grupo Ada Lovelace** — Brenda Amaral · Fernanda da Silva · Nadi Duno ·  Sheilliane Santos · Vitória Souza**
Orientadora:** Andressa Freires
Mulheres in Tech (MIT 23) - versão Data Science & IA LGBTQIA+ · Grupo 4 · Setembro 2026

--- 
## 📌 Contexto 

A evasão escolar é um dos principais desafios da educação brasileira e está associada a diversos fatores econômicos, sociais e demográficos, como renda familiar, região de residência, gênero, raça/cor, acesso a equipamento e internet, sobrecarga de trabalho e cuidado, entre outros. 

O projeto **FixFly** parte de uma constatação central: quem desiste de estudar raramente o faz por falta de vontade, mas pelo **acúmulo de obstáculos** — cansaço do trabalho, aperto financeiro, isolamento e questões de saúde mental. A proposta une tecnologia e apoio humano por meio de modelos preditivos em Python que identificam estudantes em risco de evasão, e uma camada de IA que aciona a rede de acolhimento da ONG Fly Educação ( com possibilidades de mentoria, escuta ativa, suporte socioemocional) **antes que a desistência aconteça**. 

O perfil das inscritas — maioria preta e parda, escolarizada e jovem-adulta (78% pretas + pardas). A entrega de ouro do modelo é uma **lista de fatores de risco acionáveis**: se computador/internet pesarem → empréstimo de equipamento; se horário pesar → turmas alternativas; se confiança inicial ou quesões de saúde mental pesarem → mentoria reforçada e acolhimento qualificado logo nas primeiras semanas. 

**Pergunta principal:** Como fatores socioeconômicos e demográficos influenciam a evasão escolar e de que forma uma IA orientadora pode auxiliar estudantes em situação de vulnerabilidade por meio do acesso a informações sobre programas de apoio e permanência?

---


## Visão geral da arquitetura

O pipeline segue o fluxo clássico de um projeto de ciência de dados, do dado bruto ao modelo:

```text
┌─────────────────────┐
│  Google Sheets      │  8 abas (turmas 10–23), formulários que
│  (fonte dos dados)  │  mudaram ao longo do tempo
└──────────┬──────────┘
           │  1. Carregar → renomear colunas (script) → empilhar
           ▼
┌─────────────────────┐
│  dataFlyRaw         │  2.348 linhas × 91 colunas (congelado em .parquet)
│  (backup intocado)  │
└──────────┬──────────┘
           │  2. Conhecer (raio-x): shape, info, nulos, mapa de preenchimento
           ▼
┌──────────────────────┐
│  Limpeza em lote     │  colunas mortas · cabeçalho vazado · normalização
│  (dataFly)           │  categórica · dicionário de mapeamentos · UFs · idades
└──────────┬───────────┘
           │  3. Criar TARGET (evadiu) a partir de status_aprovacao
           ▼
┌──────────────────────┐
│  Análise exploratória │  perfil das inscritas + taxas de evasão + qui-quadrado
└──────────┬───────────┘
           │  4. Modelagem (trava de segurança por volume mínimo)
           ▼
┌─────────────────────┐
│  Pipeline sklearn   │  split → ColumnTransformer → OneHot → LogisticRegression
│  (foco no RECALL)   │  class_weight='balanced'
└──────────┬──────────┘
           │  5. Camada de IA (orientação + acionamento da rede de acolhimento)
           ▼
        Ação da Fly
```

**Princípio metodológico central:** constantes com significado (`"Não respondeu"`) podem vir antes do split; qualquer estatística aprendida da base (imputação, encoding) só entra **dentro do Pipeline, no treino** — evitando vazamento de dados (*data leakage*).

---
---

## 📖 Sobre este projeto

Este é o Trabalho de Conclusão de Curso do **Grupo Ada Lovelace (G4)**, feito para a **Fly Educação**, como parte do programa que se propoe a formar mulheres para o mercado de tecnologia.

O ponto de partida foi direcionado de uma pergunta concreta, feita por quem trabalha todo dia tentando manter mulheres dentro de um curso de tecnologia:

> **Dá para prever, no momento da inscrição, quais alunas correm mais risco de evadir do curso da Fly, usando só o que elas responderam no formulário?**

A partir disso, o notebook pega os arquivos que a Fly disponibilizou e vai até o fim: entende a base, reconstrói as variáveis, testa hipóteses, treina modelos, mede com honestidade — e diz exatamente o que dá e o que não dá para afirmar hoje. A ferramenta **FixFly** transforma o resultado em ação de acolhimento na mão das gestoras.

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
3. O alvo `evadiu` é uma mistura de duas definições, e a fonte majoritária confunde evasão com registro faltante. **A taxa medida (65% num universo de 60 alunas) é um teto, não a evasão real da Fly.**
4. Das **cinco hipóteses** levantadas antes de olhar os dados (acesso digital, cuidado, disponibilidade, renda e perfil), **nenhuma se confirmou** com força estatística — e a associação de região aparece **confundida com a turma** (V=0,552, quase igual). Nenhuma variável do formulário tem associação mais que fraca com a evasão — e o **teste de embaralhamento** mede, com método, o quanto disso é sinal e o quanto é acaso (p empírico = **0,0244**: existe sinal real, ainda que fraco).
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

A régua foi escolhida **antes** de olhar os resultados: a métrica principal é a **AP (average precision / AUC-PR)**, que mede se o modelo **ordena bem a fila de risco** — o uso real. Acurácia é métrica proibida sozinha. O protocolo: **50 provas por modelo** (validação cruzada estratificada, 5 dobras × 10 repetições), sempre com o **baseline dentro**.

| Modelo | AP (AUC-PR) | Recall | F1 | Acurácia |
|--------|:-----------:|:------:|:--:|:--------:|
| **★ Random Forest (campeã)** | **0,969** | 0,81 | 0,85 | 0,82 |
| Regressão Logística | 0,950 | 0,77 | 0,82 | 0,79 |
| Gradient Boosting | 0,921 | 0,83 | 0,82 | 0,77 |
| Árvore rasa | 0,874 | 0,72 | 0,76 | 0,73 |
| Baseline (chuta a classe mais comum) | 0,644 | 1,00 | 0,78 | 0,64 |

Repare no baseline: ele tem **recall 1,00** — porque chuta que *todas* evadem — e mesmo assim fica no **piso do acaso** (AP 0,644). É a prova de que recall ou acurácia **sozinhos** enganam; a régua justa é a AP.

**Modelo campeão: Random Forest** — maior AP na média das 50 provas (**0,969**), afinada por GridSearch para **AP 0,975**. No teste final: **recall 0,90** (de 10 evasões, encontra 9), precisão 0,82, F1 0,857. Salvo em `modelo_evasao_G4.joblib` para alimentar o FixFly.

> **Honestidade sobre o `n`:** o AP variou de **0,89 a 1,00** entre as 50 provas — com 60 alunas, o desempenho depende de quais caem no treino, e reportar só a média esconderia metade da verdade. O **limiar operacional** foi fixado em 0,491 (o maior que ainda entrega recall ≥ 80%), e a decisão vem da validação cruzada, não de uma prova única.

## 🧾 Ficha técnica do modelo (model card)

O notebook gera automaticamente uma ficha técnica (`ficha_do_modelo.json`) junto com o modelo. Ela documenta: o modelo campeão (**Random Forest**) e seus hiperparâmetros; a métrica principal (*average precision* / AUC-PR = 0,975 na validação, contra piso do acaso 0,644); o **p-valor empírico do teste de embaralhamento = 0,0244** (o acaso tira em média 0,75, não zero — e o modelo real, 0,975); o limiar operacional escolhido (0,491); e — o mais importante — dois campos que raramente aparecem em modelos em produção no Brasil:

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
Brenda Amaral · Fernanda da Silva · Nadi Duno · Sheilliane Santos · Vicência Vitória Souza
**Orientadora:** Andressa Freires · diversiData

Projeto desenvolvido como Trabalho de Conclusão de Curso, em parceria com a Fly.

<p align="center">Feito com rigor técnico e um compromisso: o modelo existe para chegar antes, nunca para rotular. 💛</p>
