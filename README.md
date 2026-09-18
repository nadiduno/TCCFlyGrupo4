# 🚀 FlyFix: Um modelo preditivo que prevê a evasão das alunas da Fly antes que ela aconteça.

**Um modelo preditivo e uma camada de acolhimento para prever a evasão de alunas da Fly antes que ela aconteça.**
**Grupo Ada Lovelace (G4) · Turma Fly · diversiData · Setembro de 2026**
## 🤝 Colaboradores

[![Author](https://img.shields.io/badge/Brenda%20Amaral-blueviolet%20)](https://www.linkedin.com/in/brendaamarals/)
[![Author](https://img.shields.io/badge/Fernanda%20da%20Silva-blueviolet%20)](https://www.linkedin.com/in/fernanda-leticia-silva/)
[![Author](https://img.shields.io/badge/DS/IA-Nadi%20Duno-blueviolet%20)](https://www.linkedin.com/in/nadiduno/)
[![Author](https://img.shields.io/badge/Sheilliane%20Santos-blueviolet%20)](https://www.linkedin.com/in/sheillianesantos/)
[![Author](https://img.shields.io/badge/Vicencia%20Vitória%20Souza-blueviolet%20)](www.linkedin.com/in/vicencia-vitoria)
<br />
## Orientadora
[![Coach](https://img.shields.io/badge/Staff AI Native Builder-Andressa%20Freires-violet%20)]([www.linkedin.com/in/vicencia-vitoria](https://www.linkedin.com/in/andressafreires/))
<br />
<p align="center">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white">
  <img alt="Scikit-learn" src="https://img.shields.io/badge/Scikit--learn-1.3%2B-orange?logo=scikit-learn&logoColor=white">
  <img alt="Pandas" src="https://img.shields.io/badge/Pandas-2.0%2B-purple?logo=pandas&logoColor=white">
  <img alt="Status" src="https://img.shields.io/badge/status-conclu%C3%ADdo-brightgreen">
</p>

## 🌐 Versão Web

<div>
  <img 
    alt="Imagem que apresenta o protótipo do site, o qual é um mini portfólio do github nas cores laranja e roxo e fundo branco"
    src="https://raw.githubusercontent.com/nadiduno/TCCFlyGrupo4/refs/heads/main/Assets/PrototipoWebEscuro.png" 
    width="50%"
  >
  <br />
</div>
> Cada falso negativo é uma mulher que poderíamos ter acolhido antes.
> O modelo existe para chegar antes da evasão, nunca para rotular ninguém. 💛

---

## 📑 Sumário

1. [Sobre o projeto](#-sobre-o-projeto)
2. [A pergunta e as hipóteses](#-a-pergunta-e-as-hipóteses)
3. [Os dados](#-os-dados)
4. [Principais achados](#-principais-achados)
5. [📊 Os gráficos que contam a história](#-os-gráficos-que-contam-a-história)
6. [Os números do projeto](#-os-números-do-projeto)
7. [Ficha técnica do modelo](#-ficha-técnica-do-modelo-model-card)
8. [FlyFix: a camada de acolhimento](#-flyfix-a-camada-de-acolhimento)
9. [🖼️ Galeria do Protótipo](#️-galeria-do-protótipo)
10. [Limitações](#️-limitações-assumidas-com-todas-as-letras)
11. [Estrutura do repositório](#-estrutura-do-repositório)
12. [Como rodar](#️-como-rodar)
13. [Como Contribuir](#-como-contribuir)
14. [Sobre os dados e privacidade](#-sobre-os-dados-e-privacidade)
15. [Próximos passos](#-próximos-passos)
16. [Time](#-time)

---

## 🎯 Sobre o projeto

Trabalho de Conclusão de Curso do Grupo Ada Lovelace (G4), em parceria com a **Fly Educação**,
organização que forma mulheres em situação de vulnerabilidade para o mercado de tecnologia.

O ponto de partida não foi "vamos treinar um modelo", foi uma pergunta concreta:

> **Dá para prever, no momento da inscrição, quais alunas correm mais risco de evadir do curso da
> Fly, usando só o que elas responderam no formulário?**

Essa restrição (*só o formulário*) é o coração do trabalho e produz a resposta mais importante do
projeto, na seção de [Principais achados](#-principais-achados).

O projeto tem duas partes:

| Parte | O que é | Onde está |
|---|---|---|
| **O Notebook** | Da base crua ao modelo, com raio-X, harmonização, alvo auditado, EDA com incerteza e teste de honestidade | `Versao_17_TCC_EvasaoFly_G4CienciaDeDadosFlyV17.ipynb` |
| **O FlyFix** | A camada de acolhimento: transforma a fila do modelo em conversa da equipe da Fly | `passo_firme.html` |

### As duas regras inegociáveis

1. **Dado é gente.** A base crua tem texto livre com endereço e relato pessoal, e nunca é exibida. Só a versão harmonizada, sem campo identificável.
2. **Nada de número enfeitado.** Se o modelo for fraco, o notebook diz que é fraco, mostra a conta e explica o que faltou.

---

## ❓ A pergunta e as hipóteses

As hipóteses foram escritas **antes** de olhar os dados:

| | Hipótese | O mecanismo por trás | O que esperávamos ver |
|---|---|---|---|
| **H1** | Acesso digital precário aumenta a evasão | quem só tem celular não consegue programar | evasão maior em `tem_computador = Não` |
| **H2** | Sobrecarga de cuidado aumenta a evasão | mãe ou cuidadora tem menos tempo livre real | evasão maior em `mae_cuidadora = Sim` |
| **H3** | Menos disponibilidade declarada, mais evasão | é a promessa de tempo feita na inscrição | quem evadiu declarou menos horas |
| **H4** | Renda por pessoa menor, mais evasão | precariedade material atrapalha a permanência | evasão cai conforme a renda sobe |
| **H5** | Perfil demográfico (raça, região, idade) se associa à evasão | desigualdades estruturais não param na porta do curso | diferenças entre grupos |

Cada hipótese é testada na Estação 7 do notebook com intervalo de confiança e V de Cramér
corrigido por viés.

---

## 📊 Os dados

| Arquivo | Unidade de cada linha | Para que serve |
|---|---|---|
| Base de inscrições (2.380 linhas, turmas 10 a 23) | uma candidata | o **perfil** e o **desfecho** (coluna `Aprovadas`, turmas 10 a 17) |
| Backlogs, 12 abas | uma aluna matriculada | **fora desta versão**, ver abaixo |

**Os quatro problemas da base, e como o notebook resolve:**

- **163 colunas porque cada turma usou um formulário diferente.** A mesma pergunta escrita de até cinco jeitos. → Mapa de conceitos com `coalesce` e filtro de vocabulário (Estação 3), reduzindo a **15 conceitos limpos**.
- **A coluna de desfecho tem seis grafias** (`Formada`, `Formadas`, `Aprovada`, `Aprovadas`, `Não aprovada`, `Não aprovadas`). → Regra única, escrita e auditada por turma (Estação 6).
- **`aluna_id` se repete** (mesma pessoa em mais de uma turma). → Fica a inscrição mais completa de cada aluna, com a regra escrita (Estação 3).
- **Dados pessoais em texto livre.** → O raio-X localiza as colunas de risco sem imprimir conteúdo, e nenhuma delas entra no modelo (Estação 2.5).

**Por que o backlog ficou fora.** 6 das 12 abas (312 alunas) não têm coluna de status, metade veio
com cabeçalho quebrado, e só 90 das 435 chaves `aluna_id` (21%) casam com a base. Misturar as duas
fontes produzia um alvo que o modelo não separava da turma. O backlog virou pedido formal à Fly
(Estação 16).

---

## 🔍 Principais achados

1. **As 163 colunas eram uma dúzia de perguntas escritas de cinco jeitos.** A costura documentada na Estação 3 recuperou variáveis que, sozinhas, seriam inutilizáveis.
2. **Das 2.380 inscrições, 1.170 têm status e 166 entraram no curso.** A maior parte da perda é seleção (1.004 não aprovadas). Quem não entrou não pode ter evadido.
3. **Entre as 166 que entraram, 66 não constam como formadas: 39,8%.** Por turma, de 0% (T10) a 66% (T14). Turmas com evasão perto de zero provavelmente não registraram quem saiu.
4. **Nenhum fator isolado do formulário tem associação forte com a evasão.** Região é a maior (V = 0,26, fraca) e está mais associada à turma (V = 0,50) do que à evasão. A hipótese mais promissora (H2) **não pôde ser testada**: a pergunta não existiu para nenhuma aluna do universo. É o achado mais acionável: a Fly deixou de coletar justamente o dado que mais provavelmente explicaria a evasão das alunas dela.
5. **O modelo ordena a fila, mas ainda não supera o acaso com segurança.** AP 0,52 contra 0,40 do piso, com p = 0,33 no teste de embaralhamento. O sinal que falta é o **engajamento durante o curso** (presença, entrega, acesso), que a base não tem.

---

## 📊 Os gráficos que contam a história

Cinco gráficos do notebook, cada um respondendo a uma pergunta. As imagens estão em
`imagens/graficos/` (adicione-as ao repositório seguindo a estrutura indicada no final desta seção).

### 1. O mapa do vazio (Estação 2.4)

O gráfico que prova que as 163 colunas não são 163 informações, e sim uma dúzia de perguntas
escritas de cinco jeitos.

![O mapa do vazio: cada turma respondeu a um formulário diferente](imagens/graficos/01-mapa-do-vazio.png)

> **Leitura:** faixa escura = a turma respondeu a pergunta; faixa clara = a pergunta nem existia no
> formulário dela. As faixas quase não se sobrepõem. A base está **fragmentada**, não suja. Esse
> diagnóstico justificou toda a Estação 3.

### 2. O que a costura recuperou (Estação 3.1)

Cada linha é um conceito; cinza é a melhor coluna isolada, verde é o conceito costurado.

![Ganho do coalesce por conceito](imagens/graficos/02-ganho-coalesce.png)

> **Leitura:** conceitos como `disponibilidade` e `computador` só existem porque juntamos variantes.
> `mae_cuidadora` **não** melhorou: continua sendo a variável menos preenchida da base. É o primeiro
> prego no caixão da H2.

### 3. O funil dos dados (Estação 6.1)

De 2.380 inscrições ao universo do modelo, com cada degrau documentado.

![O funil: de 2.380 inscrições ao universo de 166 alunas](imagens/graficos/03-funil.png)

> **Leitura:** a maior perda é **seleção** (1.004 não aprovadas), não falta de registro. Quem não
> entrou no curso não pode ter evadido dele. O universo são as 166 que entraram e têm desfecho.

### 4. As hipóteses com incerteza (Estação 7.1)

O gráfico mais importante para a banca. Mostra as hipóteses testadas **com o intervalo de confiança
de 95%** desenhado, não apenas a altura das barras.

![As hipóteses com a incerteza desenhada](imagens/graficos/04-hipoteses-incerteza.png)

> **Leitura:** não compare altura de barras, olhe o tamanho das barras verticais pretas (intervalos
> de confiança). Elas se sobrepõem em praticamente todos os pares, e são largas. Largura de
> intervalo é **falta de dado, não falta de efeito**. Compare com o V14, que mostrava as mesmas
> barras sem incerteza e fazia uma diferença de 15 pontos parecer um achado.

### 5. Os quatro destinos no teste (Estação 13)

A matriz de confusão final, em formato legível: cada quadrante tem um nome e uma consequência.

![Os quatro destinos do modelo no teste](imagens/graficos/05-quatro-destinos.png)

> **Leitura:** das 17 alunas que evadiram no teste, o modelo apontou as 17 (recall 1,00). Para isso
> a equipe procuraria 31 alunas, das quais 14 não iam sair. O custo de um falso positivo é **uma
> conversa de acolhimento a mais**; o de um falso negativo é uma mulher que não foi acolhida. Essa
> assimetria justifica a meta de recall ≥ 80% na Estação 12.

> **Nota sobre as imagens:** os cinco gráficos são gerados pelo notebook. Rode-o, exporte as figuras
> como PNG (ou use `plt.savefig`), e coloque-as em `imagens/graficos/`. Se preferir não versionar
> imagens, substitua cada bloco `![...](...)` por um link direto para a célula do notebook no GitHub.

---

## 🔢 Os números do projeto

Números da rodada de 08/09/2026, semente 42. Fonte da verdade: `dados/ficha_do_modelo.json`, gerado
pelo notebook.

| O que | Valor | Estação |
|---|---|---|
| Inscrições na base | 2.380 linhas × 163 colunas | 1 |
| Células vazias na base crua | 81,8% (vazio estrutural, não descuido) | 2.1 |
| Colunas respondidas por uma turma só | 91 de 163 | 2.4 |
| Conceitos após a harmonização | 15 | 3 |
| Inscrições com status (turmas 10 a 17) | 1.170 | 6.1 |
| Não aprovadas (fora do universo) | 1.004 | 6.1 |
| **Universo do modelo** | **166 alunas** (7,0% das inscrições), sem `aluna_id` repetido | 6.1 |
| **Taxa de evasão no universo** | **39,8%** (66 de 166) | 6.1 |
| Taxa por turma | T14 66% (n=50) · T15 40% (30) · T17 40% (50) · T11 6% (17) · T10 0% (19) | 6.2 |
| Variáveis no modelo | 10: idade, escolaridade, renda por pessoa, pessoas na casa, raça, computador, região, LGBTQIAPN+, deficiência, gênero | 9 |
| Treino / teste | 124 / 42 alunas, estratificado | 9 |
| Modelo campeão | **Regressão Logística** (C = 5, penalidade L2) | 11 e 12 |
| AP na validação cruzada vs. piso do acaso | **0,521** contra 0,395; acaso por embaralhamento: média 0,496, percentil 95 em 0,599 | 12 e 14 |
| p-valor do embaralhamento | **0,33** (~280 rodadas, cada uma refazendo a busca em grade) | 14 |
| Limiar operacional (meta: recall ≥ 80%) | **0,30**; no treino, recall 82% e precisão 43% | 12 |

### A comparação dos modelos (Estação 11, 5 dobras × 10 repetições)

| Modelo | AP | desvio | Recall | Precisão | F1 | Acurácia |
|---|---|---|---|---|---|---|
| **Regressão Logística** (campeã) | **0,525** | 0,091 | 0,568 | 0,477 | 0,510 | 0,577 |
| Random Forest | 0,523 | 0,097 | 0,524 | 0,462 | 0,483 | 0,562 |
| Gradient Boosting | 0,477 | 0,084 | 0,331 | 0,419 | 0,360 | 0,553 |
| Árvore rasa | 0,455 | 0,047 | 0,642 | 0,444 | 0,513 | 0,542 |
| Baseline (classe majoritária) | 0,395 | 0,010 | 0,000 | 0,000 | 0,000 | 0,605 |

A campeã variou de AP 0,40 a 0,86 entre as 50 provas. Essa largura é parte do resultado: com este
universo, o desempenho depende de quais alunas caem no treino.

### O resultado no conjunto de teste (Estação 13, 42 alunas, 17 evadiram, limiar 0,30)

| Métrica | Valor |
|---|---|
| AP | 0,634 (acaso: 0,405) |
| Recall | 1,000 |
| Precisão | 0,548 |
| F1 | 0,708 |
| Acurácia | 0,667 |
| Brier | 0,197 (chutar a taxa da base: 0,241) |

Em português: das 17 alunas que evadiram no teste, o modelo apontou as 17. Para isso a equipe
procuraria 31 alunas, das quais 14 não iam sair. Variáveis cuja contribuição supera a própria
incerteza (importância por permutação): raça e região, as duas coladas na turma (Estação 7.4).

> **Leia com a Estação 14 ao lado.** O teste tem 42 alunas: uma a mais ou a menos muda o recall em
> 6 pontos. A decisão foi tomada na validação cruzada; o teste é conferência, não veredito. O
> embaralhamento mostra que um AP de 0,52 ainda cabe dentro do que o acaso produz com este n.

---

## 📋 Ficha técnica do modelo (Model Card)

O notebook gera automaticamente `dados/ficha_do_modelo.json`. Ela traz:

- o modelo campeão e seus hiperparâmetros;
- a fonte do alvo (`Aprovadas`, turmas 10 a 17) e o tamanho do universo;
- a métrica principal (AP / AUC-PR) comparada ao piso do acaso;
- o p-valor do teste de embaralhamento;
- o limiar operacional e as métricas no teste;
- as limitações medidas;
- e os dois campos que raramente aparecem em modelo em produção no Brasil:

| Campo | Conteúdo |
|---|---|
| ✅ **Uso permitido** | ordenar fila de prioridade para ações de acolhimento |
| ❌ **Uso proibido** | informar risco individual à aluna · decidir desligamento, bolsa ou seleção · tirar conclusão sobre grupo com menos de 10 alunas |

Um modelo que ordena risco de evasão, se usado para decidir desligamento ou corte de bolsa, vira
máquina de exclusão, e faz isso justamente com as alunas mais vulneráveis, que são as que ele mais
aponta. Escrever a proibição no arquivo é o que impede que a próxima pessoa a abrir o `.joblib` use
sem saber.

---

## 🤝 FlyFix: a camada de acolhimento

> "Chegar antes da evasão, nunca rotular ninguém."

O modelo entrega uma fila ordenada por risco. Quem faz o acolhimento é gente. O FlyFix é a
ferramenta interna das gestoras da Fly, uma aplicação web autocontida (HTML, CSS e JS, sem backend
obrigatório), organizada em seis páginas:

| Página | O que faz |
|---|---|
| **Painel** | mostra a fila de prioridade e um controle de capacidade da semana. É esse número, definido pela equipe e não pelo algoritmo, que decide o corte da fila |
| **Ficha da aluna** | o que a inscrição respondeu e o que ficou em branco, para embasar a conversa |
| **Mensagens** | gera rascunhos de abordagem por trilha de apoio (abertura, acesso digital, rede de cuidado, logística, retomar contato) e por canal (WhatsApp ou e-mail). A gestora revisa e envia; a ferramenta nunca envia sozinha |
| **Registro da conversa** | transforma anotação em texto livre em campos estruturados. É a coluna de engajamento que a base hoje não tem (limitação L5), construída conversa por conversa |
| **Base e conexão** | carrega a fila real exportada pela Estação 15 (`dados/fila_prioridade.csv`), permite ligar a uma API que serve o `.joblib`, e propõe o mapeamento de cabeçalhos de planilhas novas para os 15 conceitos |
| **Ficha técnica** | o model card completo dentro da ferramenta: métricas, variáveis, uso permitido e proibido, limitações |

### As três regras que a ferramenta aplica na prática

A camada de mensagens roda uma **guarda de linguagem** antes de qualquer texto aparecer na tela:

1. **Nunca aparece probabilidade ou número de risco**, nem na fila, nem em mensagem. A fila mostra só posição e faixa de prioridade.
2. **Raça, orientação e deficiência nunca viram motivo de contato**, mesmo sendo variáveis que o modelo usa internamente. Elas entram no cálculo, mas nunca atravessam para a tela ou para o texto.
3. **As trilhas de apoio são oferecidas a todas as alunas da turma**, nunca como diagnóstico individual. O modelo não sabe qual é a barreira de cada aluna, e supor errado quebra a confiança na primeira mensagem.

Há também um **copiloto interno** (chat lateral) que responde perguntas sobre a fila e o modelo
citando a estação do notebook de onde veio a informação, e diz explicitamente quando a resposta não
existe nos dados, em vez de inventar.

🔐 A ferramenta funciona inteira sobre um arquivo carregado manualmente. Nenhuma função depende de
rede. Opcionalmente, conecta-se a uma API que serve o modelo (`api_passo_firme.py`).

---

## 🖼️ Galeria do Protótipo

> **Como usar esta seção:** substitua os `src` das imagens abaixo pelas capturas de tela do seu
> protótipo. Exporte em PNG com boa resolução (mínimo 1280 px para web, 390 px para mobile) e
> salve em `imagens/prototipo/`.

### Versão Web — Tema Claro

| Painel de Prioridades | Fila de Alunas |
|---|---|
| ![Painel no tema claro](imagens/prototipo/web-light-painel.png) | ![Fila no tema claro](imagens/prototipo/web-light-fila.png) |
| Visão geral do painel com a fila ordenada por risco. | Detalhe da fila, com o controle de capacidade semanal em destaque. |

| Ficha da Aluna | Camada de Mensagens |
|---|---|
| ![Ficha no tema claro](imagens/prototipo/web-light-ficha.png) | ![Mensagens no tema claro](imagens/prototipo/web-light-mensagens.png) |
| O que a inscrição respondeu, e o que ficou em branco. | Rascunho por trilha e canal, sempre revisado pela gestora. |

### Versão Web — Tema Escuro

| Painel de Prioridades | Camada de Mensagens |
|---|---|
| ![Painel no tema escuro](imagens/prototipo/web-dark-painel.png) | ![Mensagens no tema escuro](imagens/prototipo/web-dark-mensagens.png) |
| A mesma fila, para ambientes com pouca luz. | A guarda de linguagem em ação: nenhum número de risco aparece. |

### Versão Mobile

<p align="center">
  <img src="imagens/prototipo/mobile-fila.png" width="220" alt="Fila no mobile">
  &nbsp;&nbsp;
  <img src="imagens/prototipo/mobile-ficha.png" width="220" alt="Ficha no mobile">
  &nbsp;&nbsp;
  <img src="imagens/prototipo/mobile-mensagem.png" width="220" alt="Mensagens no mobile">
</p>

<p align="center">
  <em>Da esquerda para a direita: fila de prioridades, ficha da aluna e camada de mensagens, adaptadas para uso em campo pela equipe de acolhimento.</em>
</p>

---

## ⚠️ Limitações, assumidas com todas as letras

Escritas na Estação 8, **antes** de qualquer modelo ser treinado.

| # | Limitação |
|---|---|
| **L1** | **Tamanho.** 166 alunas (66 evasões) no universo, 42 no teste (17 evasões). Uma aluna a mais ou a menos mexe o recall em 6 pontos. Toda decisão é tomada por validação cruzada repetida. |
| **L2** | **Alvo de uma fonte só.** `evadiu = 1` significa "aprovada e não consta como formada". Pode incluir registro de conclusão faltante. A taxa medida é a taxa **registrada**. |
| **L3** | **Confundimento.** 30% do universo vem de uma única turma. Região × turma (V = 0,50) é mais forte que região × evasão (0,26). Variável que varia por turma pode parecer preditiva sem ser. |
| **L4** | **Cobertura.** Desfecho existe só nas turmas 10, 11, 14, 15 e 17. Quem está fora pode ser sistematicamente diferente. Isso é viés de seleção, não só perda de n. |
| **L5** | **Variáveis.** Todas declaradas na inscrição. Não há nenhuma variável de engajamento (presença, entrega, acesso), que a literatura aponta como o preditor mais forte. |
| **L6** | **Autodeclaração.** "Tenho 3 horas por dia" é uma intenção, não uma medida do que aconteceu depois. |
| **L7** | **Escala.** `disponibilidade_h` junta formulário por dia com formulário por semana. Ficou fora por cobertura (22% no universo), mas a ressalva vale para uso futuro. |

**L5 é a decisiva.** As outras melhoram sozinhas conforme a Fly acumula turmas. A L5 é de outra
natureza: por mais turmas que se acumulem, um formulário de inscrição continua sendo uma foto tirada
antes do curso.

---

## 📁 Estrutura do repositório

```bash
.
├── Versao_17_TCC_EvasaoFly_G4CienciaDeDadosFlyV17.ipynb   # notebook completo
├── flixfli.html                           # camada de acolhimento (autocontida)
├── api_passo_firme.py                     # opcional: API FastAPI
├── DOCUMENTACAO.md                        # documentação técnica
├── requirements.txt
├── .gitignore                             # inclui dados/
├── Assets/
│   ├── graficos/                          # as 5 figuras citadas neste README
│   └── prototipo/                         # capturas de tela do FlyFix
├── dados/                                 # GERADA ao rodar o notebook (não versionada)
│   ├── universo_com_alvo.parquet
│   ├── modelo_evasao_G4.joblib
│   ├── ficha_do_modelo.json
│   └── fila_prioridade.csv
└── README.md
