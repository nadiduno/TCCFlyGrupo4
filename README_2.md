# 🚀 FlyFix: prever a evasão para acolher, nunca para rotular

**Grupo Ada Lovelace (G4) · Turma Fly · diversiData · Setembro de 2026**

> Cada Falso Negativo é uma mulher que a gente poderia ter acolhido antes.
> O modelo existe para chegar antes da evasão, nunca para rotular ninguém. 💛

---

## Sumário

1. [Sobre o projeto](#sobre-o-projeto)
2. [A pergunta e as hipóteses](#a-pergunta-e-as-hipóteses)
3. [Os dados](#os-dados)
4. [Como o notebook é organizado](#como-o-notebook-é-organizado)
5. [Principais achados](#principais-achados)
6. [Os números do projeto](#os-números-do-projeto)
7. [Ficha técnica do modelo](#ficha-técnica-do-modelo-model-card)
8. [FlyFix: a camada de acolhimento](#flyfix-a-camada-de-acolhimento)
9. [Limitações](#limitações-assumidas-com-todas-as-letras)
10. [Estrutura do repositório](#estrutura-do-repositório)
11. [Como rodar](#como-rodar)
12. [Sobre os dados e privacidade](#sobre-os-dados-e-privacidade)
13. [Próximos passos](#próximos-passos)
14. [Time](#time)

---

## Sobre o projeto

Este é o Trabalho de Conclusão de Curso do Grupo Ada Lovelace (G4), feito em parceria com a
**Fly Educação**, organização que forma mulheres em situação de vulnerabilidade para o mercado de
tecnologia.

O ponto de partida não foi "vamos treinar um modelo". Foi uma pergunta concreta, feita por quem
trabalha todo dia tentando manter mulheres dentro de um curso de tecnologia:

> **Dá para prever, no momento da inscrição, quais alunas correm mais risco de evadir do curso da
> Fly, usando só o que elas responderam no formulário?**

Repare na restrição: *só o que elas responderam no formulário*. Essa frase é o coração do trabalho,
e é ela que produz a resposta mais importante do projeto, que está na seção
[Principais achados](#principais-achados).

O projeto tem duas partes que se completam:

| Parte | O que é | Onde está |
|---|---|---|
| **O notebook** | Da base crua ao modelo, com raio-X, harmonização, alvo auditado, EDA com incerteza, comparação de modelos e teste de honestidade | `Versao_17_TCC_EvasaoFly_G4CienciaDeDadosFlyV17.ipynb` |
| **O FlyFix** | A camada de acolhimento: transforma a fila do modelo em conversa da equipe da Fly, com guarda de linguagem e registro de engajamento | `passo_firme.html` (o arquivo mantém o nome antigo) |

### As duas regras inegociáveis

1. **Dado é gente.** A base crua tem texto livre com endereço e relato pessoal. Em nenhum momento
   ela é exibida. Só a versão harmonizada, que não tem nenhum campo identificável.
2. **Nada de número enfeitado.** Se o modelo for fraco, o notebook diz que é fraco, mostra a conta
   e explica o que faltou. Ausência de resultado documentada é resultado.

---

## A pergunta e as hipóteses

As hipóteses foram escritas **antes** de olhar os dados, como afirmação mais mecanismo:

| | Hipótese | O mecanismo por trás | O que esperávamos ver |
|---|---|---|---|
| **H1** | Acesso digital precário aumenta a evasão | quem só tem celular não consegue programar | evasão maior em `tem_computador = Não` |
| **H2** | Sobrecarga de cuidado aumenta a evasão | mãe ou cuidadora tem menos tempo livre real | evasão maior em `mae_cuidadora = Sim` |
| **H3** | Menos disponibilidade declarada, mais evasão | é a promessa de tempo feita na inscrição | quem evadiu declarou menos horas |
| **H4** | Renda por pessoa menor, mais evasão | precariedade material atrapalha a permanência | evasão cai conforme a renda sobe |
| **H5** | Perfil demográfico (raça, região, idade) se associa à evasão | desigualdades estruturais não param na porta do curso | diferenças entre grupos |

Cada hipótese é testada na Estação 7 do notebook **com intervalo de confiança e V de Cramér
corrigido por viés**, e a H2 tem um destino especial que está nos achados.

---

## Os dados

| Arquivo | Unidade de cada linha | Para que serve |
|---|---|---|
| Base de inscrições (2.380 linhas, turmas 10 a 23) | uma candidata | o **perfil** (variáveis explicativas) e o **desfecho** (coluna `Aprovadas`, turmas 10 a 17) |
| Backlogs, 12 abas | uma aluna matriculada | **fora desta versão**, ver abaixo |

**O que a base tem de difícil, e que o notebook resolve e documenta:**

- **163 colunas porque cada turma usou um formulário diferente.** A mesma pergunta aparece escrita
  de até cinco jeitos. Solução: um mapa de conceitos com `coalesce` e filtro de vocabulário
  (Estação 3), que reduz as 163 colunas a **15 conceitos limpos**.
- **A coluna de desfecho tem seis grafias** (`Formada`, `Formadas`, `Aprovada`, `Aprovadas`,
  `Não aprovada`, `Não aprovadas`). Solução: uma regra única, escrita e auditada por turma (Estação 6).
- **`aluna_id` se repete dentro da própria base** (a mesma pessoa inscrita em mais de uma turma).
  Solução: fica a inscrição mais completa de cada aluna, e a regra fica escrita (Estação 3). No
  universo do modelo não há nenhuma aluna repetida.
- **Dados pessoais em texto livre.** Solução: o raio-X localiza as colunas de risco sem imprimir
  nenhum conteúdo, e nenhuma delas entra no modelo (Estação 2.5).

**Por que o backlog ficou fora.** Uma versão anterior tentou usar as 12 abas de acompanhamento como
segunda fonte de desfecho. O que ela mediu: 6 das 12 abas (312 alunas) não têm nenhuma coluna de
status, metade veio com cabeçalho quebrado, e só 90 das 435 chaves `aluna_id` (21%) casam com a
base. Misturar as duas fontes produzia um alvo que o modelo não separava da turma. Nesta versão o
alvo vem de **uma fonte só**, e cada número pode ser reproduzido em três linhas a partir da base.
O backlog virou pedido formal à Fly (Estação 16).

As 15 variáveis harmonizadas, com regra de construção e vocabulário, estão no dicionário da
Estação 4 do notebook.

---

## Como o notebook é organizado

O notebook tem **16 estações**. Todo bloco de código tem duas âncoras: **🔎 O que vamos fazer**
antes do código, e **📖 Como ler** depois do resultado, dizendo o que aquilo significa e o que
**não** significa. Quem quiser só a história pode ler as duas âncoras e pular o código.

| Estação | Pergunta que ela responde |
|---|---|
| 0 | Ambiente, semente aleatória e identidade visual (verde = ficou, coral = evadiu, sempre) |
| 1 | O que é o arquivo que a Fly mandou? |
| 2 | **Raio-X da base crua**: tamanho, chave, tipos de coluna, mapa do vazio por turma, dado pessoal |
| 3 | Costura das 163 colunas em 15 conceitos, padronização, e o relatório do que foi descartado |
| 4 | **Dicionário das variáveis**: o que cada uma mede, como foi construída, quanto está preenchida |
| 5 | **Distribuição de cada variável**: o retrato do público, categorias raras, variáveis quase constantes |
| 6 | Construção e **auditoria do alvo `evadiu`**: a regra, o funil, a taxa por turma, por que o backlog ficou fora |
| 7 | **EDA com hipóteses**: taxa por grupo com intervalo de confiança, V de Cramér, teste de confundimento |
| 8 | **Limitações**, escritas antes de qualquer modelo ser treinado |
| 9 | Preparo sem vazamento: Pipeline, separação estratificada, variáveis escolhidas por código |
| 10 | **A régua**: por que AP é a métrica principal, por que acurácia é proibida sozinha |
| 11 | **Cinco modelos** sob o mesmo protocolo (5 dobras × 10 repetições) e por que cada um está na disputa |
| 12 | Ajuste do campeão e **escolha do limiar pelo custo real**, não pelo 0,5 padrão |
| 13 | Diagnóstico do campeão: quatro destinos, importância por permutação, direção dos efeitos, calibração |
| 14 | **Teste de honestidade**: embaralhar o alvo e refazer a busca inteira para medir o acaso |
| 15 | A pergunta da Fly: "por quem eu começo?" A fila de prioridade, o modelo salvo e a ficha técnica |
| 16 | O que fazer com o resultado: pedido de dados, próximos passos e a base do FlyFix |

> **Regra do notebook:** o modelo campeão é escolhido **por código** (maior AP média em 50 provas),
> e todas as estações seguintes usam esse mesmo modelo. Nenhum gráfico mostra um modelo diferente do
> que venceu.

---

## Principais achados

Em cinco frases:

1. **As 163 colunas eram uma dúzia de perguntas escritas de cinco jeitos.** A costura documentada na
   Estação 3 recuperou variáveis que, sozinhas, seriam inutilizáveis.
2. **Das 2.380 inscrições, 1.170 têm status de aprovação e 166 entraram no curso.** A maior parte da
   perda é seleção (1.004 não aprovadas), não falta de registro. Quem não entrou não pode ter evadido.
3. **Entre as 166 que entraram, 66 não constam como formadas: 39,8%.** Por turma, a taxa vai de 0%
   (T10) a 66% (T14). As turmas com evasão perto de zero provavelmente não registraram quem saiu, e
   isso está escrito como limitação, com número.
4. **Nenhum fator isolado do formulário tem associação forte com a evasão.** Região é a maior
   (V = 0,26, fraca) e está mais associada à turma (V = 0,50) do que à evasão. Computador, raça,
   LGBTQIAPN+ e deficiência ficam em zero. A hipótese mais promissora (H2, mãe ou cuidadora) **não pôde
   ser testada**: a pergunta não existiu para nenhuma aluna do universo. Isso não é lacuna, é o achado
   mais acionável do TCC: a Fly deixou de coletar justamente o dado que mais provavelmente explicaria a
   evasão das alunas dela.
5. **O modelo ordena a fila, mas ainda não supera o acaso com segurança.** A campeã por código
   (Regressão Logística) faz AP 0,52 na validação cruzada contra 0,40 do piso teórico; o teste de
   embaralhamento, que refaz a busca inteira cerca de 280 vezes com o alvo bagunçado, mostra que o acaso tira
   0,50 em média e 0,60 no percentil 95 (p = 0,33). Tradução: com o formulário de inscrição, o
   máximo que dá para fazer é priorizar uma fila. O sinal que falta é o **engajamento durante o curso**
   (presença, entrega, acesso à plataforma), que a base não tem.

O gráfico final (Estação 15) traduz isso em algo acionável: comparando a fila ordenada pelo modelo
com uma busca aleatória, ele mostra quantas alunas em risco a equipe alcança se procurar as primeiras
N da fila. Essa é a métrica que importa para quem faz o acolhimento.

---

## Os números do projeto

Os números abaixo saem do próprio notebook (rodada de 08/09/2026, semente 42), com a estação
indicada. A fonte da verdade é `dados/ficha_do_modelo.json`, que o notebook gera.

| O que | Valor | Estação |
|---|---|---|
| Inscrições na base | 2.380 linhas × 163 colunas | 1 |
| Células vazias na base crua | 81,8% (vazio estrutural, não descuido) | 2.1 |
| Colunas respondidas por uma turma só | 91 de 163 | 2.4 |
| Conceitos após a harmonização | 15 | 3 |
| Inscrições com status de aprovação (turmas 10 a 17) | 1.170 | 6.1 |
| Não aprovadas (fora do universo) | 1.004 | 6.1 |
| **Universo do modelo** (entraram e têm desfecho) | **166 alunas** (7,0% das inscrições), sem repetição de `aluna_id` | 6.1 |
| **Taxa de evasão no universo** | **39,8%** (66 de 166) | 6.1 |
| Taxa por turma | T14 66% (n=50) · T15 40% (30) · T17 40% (50) · T11 6% (17) · T10 0% (19) | 6.2 |
| Variáveis no modelo (escolhidas por código) | 10: idade, escolaridade, renda por pessoa, pessoas na casa, raça, computador, região, LGBTQIAPN+, deficiência, gênero | 9 |
| Treino / teste | 124 / 42 alunas, estratificado (39,5% / 40,5% de evasão) | 9 |
| Modelo campeão e hiperparâmetros | **Regressão Logística** (C = 5, penalidade L2) | 11 e 12 |
| AP na validação cruzada vs. piso do acaso | **0,521** contra piso teórico de 0,395; acaso medido por embaralhamento: média 0,496, percentil 95 em 0,599 | 12 e 14 |
| p-valor do teste de embaralhamento | **0,33** (cerca de 280 rodadas, cada uma refazendo a busca em grade) | 14 |
| Limiar operacional (meta: recall ≥ 80%) | **0,30**; no treino, recall 82% e precisão 43% | 12 |

### A comparação dos modelos (Estação 11, média de 5 dobras × 10 repetições)

| Modelo | AP | desvio | Recall | Precisão | F1 | Acurácia |
|---|---|---|---|---|---|---|
| **Regressão Logística** (campeã por código) | **0,525** | 0,091 | 0,568 | 0,477 | 0,510 | 0,577 |
| Random Forest | 0,523 | 0,097 | 0,524 | 0,462 | 0,483 | 0,562 |
| Gradient Boosting | 0,477 | 0,084 | 0,331 | 0,419 | 0,360 | 0,553 |
| Árvore rasa | 0,455 | 0,047 | 0,642 | 0,444 | 0,513 | 0,542 |
| Baseline (classe mais comum) | 0,395 | 0,010 | 0,000 | 0,000 | 0,000 | 0,605 |

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
> 6 pontos. A decisão foi tomada na validação cruzada; o teste é conferência, não veredito. E o
> embaralhamento mostra que um AP de 0,52 ainda cabe dentro do que o acaso produz com este n.

---

## Ficha técnica do modelo (model card)

O notebook gera automaticamente `dados/ficha_do_modelo.json` junto com o modelo treinado. Ela traz:

- o modelo campeão e seus hiperparâmetros;
- a fonte do alvo (coluna `Aprovadas`, turmas 10 a 17) e o tamanho do universo;
- a métrica principal (average precision / AUC-PR) comparada ao piso do acaso;
- o p-valor do teste de embaralhamento;
- o limiar operacional escolhido e as métricas no teste;
- as limitações medidas;
- e os dois campos que raramente aparecem em modelo em produção no Brasil:

| Campo | Conteúdo |
|---|---|
| ✅ **Uso permitido** | ordenar fila de prioridade para ações de acolhimento |
| ❌ **Uso proibido** | informar risco individual à aluna · decidir desligamento, bolsa ou seleção · tirar conclusão sobre grupo com menos de 10 alunas |

Isso não é formalidade. Um modelo que ordena risco de evasão, se usado para decidir desligamento ou
corte de bolsa, vira máquina de exclusão, e faz isso justamente com as alunas mais vulneráveis, que
são as que ele mais aponta. Escrever a proibição no arquivo é o que impede que a próxima pessoa a
abrir o `.joblib` use sem saber.

---

## FlyFix: a camada de acolhimento

> "Chegar antes da evasão, nunca rotular ninguém."

O modelo, sozinho, entrega uma fila ordenada por risco. Quem faz o acolhimento é gente. O FlyFix é
a ferramenta interna, de uso exclusivo das gestoras da Fly, que transforma essa fila em ação da
equipe. É uma aplicação web autocontida (HTML, CSS e JS, sem backend obrigatório), organizada em seis
páginas:

| Página | O que faz |
|---|---|
| **Painel** | mostra a fila de prioridade e um controle de capacidade da semana. É esse número, definido pela equipe e não pelo algoritmo, que decide o corte da fila |
| **Ficha da aluna** | o que a inscrição respondeu e o que ficou em branco, para embasar a conversa |
| **Mensagens** | gera rascunhos de abordagem por trilha de apoio (abertura, acesso digital, rede de cuidado, logística, retomar contato) e por canal (WhatsApp ou e-mail). A gestora sempre revisa e envia; a ferramenta nunca envia sozinha |
| **Registro da conversa** | transforma anotação em texto livre em campos estruturados. É a coluna de engajamento que a base hoje não tem (limitação L5), construída conversa por conversa |
| **Base e conexão** | carrega a fila real exportada pela Estação 15 (`dados/fila_prioridade.csv`), permite ligar a uma API que serve o `.joblib`, e propõe o mapeamento de cabeçalhos de planilhas novas para os 15 conceitos |
| **Ficha técnica** | o model card completo dentro da ferramenta: métricas, variáveis, uso permitido e proibido, limitações |

### As três regras que a ferramenta aplica na prática

A camada de mensagens roda uma **guarda de linguagem** antes de qualquer texto aparecer na tela:

1. **Nunca aparece probabilidade ou número de risco**, nem na fila, nem em mensagem. A fila mostra
   só posição e faixa de prioridade.
2. **Raça, orientação e deficiência nunca viram motivo de contato**, mesmo sendo variáveis que o
   modelo usa internamente. Elas entram no cálculo, mas nunca atravessam para a tela ou para o texto.
3. **As trilhas de apoio são oferecidas a todas as alunas da turma**, nunca como diagnóstico
   individual. O modelo não sabe qual é a barreira de cada aluna, e supor errado quebra a confiança
   na primeira mensagem.

Há também um **copiloto interno** (chat lateral) que responde perguntas sobre a fila e o modelo
citando a estação do notebook de onde veio a informação, e diz explicitamente quando a resposta não
existe nos dados, em vez de inventar.

🔐 A ferramenta funciona inteira sobre um arquivo carregado manualmente. Nenhuma função depende de
rede. Opcionalmente, conecta-se a uma API que serve o modelo (`api_passo_firme.py`).

---

## Limitações, assumidas com todas as letras

Escritas na Estação 8, **antes** de qualquer modelo ser treinado, de propósito.

| # | Limitação |
|---|---|
| **L1** | **Tamanho.** O universo tem 166 alunas (66 evasões) e o conjunto de teste tem 42, das quais 17 evadiram. Uma aluna a mais ou a menos mexe o recall em 6 pontos. Por isso toda decisão é tomada por validação cruzada repetida, nunca numa prova única. |
| **L2** | **Alvo de uma fonte só.** `evadiu = 1` significa "aprovada e não consta como formada". Isso pode incluir registro de conclusão faltante, e uma turma com evasão perto de zero pode ser turma que não lançou quem saiu. A taxa medida é a taxa registrada, sempre lida com o n e por turma. |
| **L3** | **Confundimento.** 30% do universo vem de uma única turma, e as turmas diferem em período, região e forma de registro. Região × turma (V = 0,50) é mais forte que região × evasão (0,26). Qualquer variável que varie por turma pode parecer preditiva sem ser. |
| **L4** | **Cobertura.** O desfecho existe só nas turmas 10, 11, 14, 15 e 17 (1.170 das 2.380 inscrições têm a coluna `Aprovadas`). As turmas 12 e 16 a 23 não têm desfecho na base, e o backlog ficou fora. Quem está fora pode ser sistematicamente diferente. Isso é viés de seleção, não só perda de n. |
| **L5** | **Variáveis.** Todas foram declaradas na inscrição, antes do curso começar. Não há nenhuma variável de engajamento (presença, entrega, acesso à plataforma), que a literatura aponta como o preditor mais forte. |
| **L6** | **Autodeclaração.** "Tenho 3 horas por dia" é uma intenção, não uma medida do que aconteceu depois. |
| **L7** | **Escala.** `disponibilidade_h` junta um formulário que perguntava horas por dia com outro que perguntava por semana. Ficou fora do modelo por cobertura (22% no universo), mas a ressalva vale para qualquer uso futuro. |

**L5 é a decisiva.** As outras melhoram sozinhas conforme a Fly acumula turmas. A L5 é de outra
natureza: por mais turmas que se acumulem, um formulário de inscrição continua sendo uma foto tirada
antes do curso.

---

## Estrutura do repositório

```
.
├── Versao_17_TCC_EvasaoFly_G4CienciaDeDadosFlyV17.ipynb   # o notebook completo: texto, gráficos e código
├── passo_firme.html               # a camada de acolhimento (autocontida, abre no navegador)
├── api_passo_firme.py             # opcional: API FastAPI que serve o modelo .joblib
├── DOCUMENTACAO.md                # documentação técnica: decisões, regras e como reproduzir cada número
├── requirements.txt
├── .gitignore                     # inclui dados/
├── dados/                         # GERADA ao rodar o notebook (não versionada)
│   ├── universo_com_alvo.parquet  # o universo do modelo, anonimizado
│   ├── modelo_evasao_G4.joblib    # modelo treinado + limiar + lista de variáveis
│   ├── ficha_do_modelo.json       # model card, a fonte da verdade dos números
│   └── fila_prioridade.csv        # fila exportada para o FlyFix (sem probabilidade)
└── README.md
```

> A pasta `dados/` é gerada localmente e pode conter artefatos derivados de dados sensíveis.
> Ela **nunca** deve ser commitada. O `.gitignore` já cuida disso.

---

## Como rodar

### Opção A: Google Colab (recomendada, sem instalar nada)

1. Abra o notebook no Colab.
2. Rode as células em ordem. A Estação 1 lê a base direto da URL do Drive; se o arquivo
   `BDFLYG4.xlsx - Sheet1.csv` estiver na mesma pasta, ele é usado no lugar da URL.
3. A **Estação 14 demora** cerca de 2 minutos: ela refaz a busca em grade a cada embaralhamento do
   alvo e escolhe sozinha quantas rodadas cabem nesse tempo (mínimo de 40).
4. Ao final, baixe a pasta `dados/` para usar o modelo e a ficha no FlyFix.

### Opção B: local

**Pré-requisitos:** Python 3.10 ou superior e acesso ao arquivo original da Fly (ele não está
neste repositório, veja [Sobre os dados e privacidade](#sobre-os-dados-e-privacidade)).

```bash
git clone https://github.com/<organizacao>/<repositorio>.git
cd <repositorio>

python -m venv venv
source venv/bin/activate        # Linux e Mac
venv\Scripts\activate           # Windows

pip install -r requirements.txt
jupyter notebook Versao_17_TCC_EvasaoFly_G4CienciaDeDadosFlyV17.ipynb
```

`requirements.txt`:

```
pandas>=2.0
numpy
matplotlib
scikit-learn>=1.3
scipy
joblib
pyarrow
```

O notebook cria a pasta `dados/` sozinho.

### Reproduzir o número principal em três linhas

```python
import pandas as pd
base = pd.read_csv("BDFLYG4.xlsx - Sheet1.csv", low_memory=False)
st = base["Aprovadas"].str.strip().str.lower()
entraram = st.isin(["formada", "formadas", "aprovada", "aprovadas"])
print(entraram.sum(), st[entraram].isin(["aprovada", "aprovadas"]).mean())   # 166, 0.398
```

### Abrir o FlyFix

O `passo_firme.html` é autocontido. Basta abrir no navegador. Ele abre com uma fila de exemplo.
Para usar a fila real, carregue `dados/fila_prioridade.csv` na aba **Base e conexão**.

---

## Sobre os dados e privacidade

Os dados contêm informação sensível de alunas reais: endereço, relatos pessoais e, em abas
antigas, até CPF. Por isso:

- **A base crua nunca é exibida** em nenhum ponto do notebook. O raio-X (Estação 2.5) localiza as
  colunas de risco pelo nome e pela forma, conta e nomeia, mas não imprime conteúdo.
- **Nenhuma coluna de texto livre entra no modelo.** A data de nascimento é usada só para derivar a
  idade e descartada em seguida.
- **`aluna_id` é um identificador anônimo atribuído pela Fly** (formato `Aluna 1331`). Ele aparece
  na fila de prioridade porque a equipe precisa saber de quem está falando. Antes de compartilhar
  qualquer saída, confira que ele não virou nome nem e-mail.
- **Os arquivos de dados não fazem parte deste repositório** e não devem ser commitados.
- Qualquer reprodução deste projeto deve seguir o mesmo cuidado: tratar dado como gente, não como
  planilha.

---

## Próximos passos

### O pedido de dados para a Fly, em ordem de impacto

| # | O que pedir | Por que muda o jogo | Esforço |
|---|---|---|---|
| 1 | **Presença e entrega de atividade, por semana e por aluna** | é o preditor mais forte na literatura e não existe nesta base | médio |
| 2 | **A mesma chave `aluna_id`** na base de inscrições e nos backlogs | hoje só 21% cruzam, e por isso o backlog ficou fora; corrigir multiplica o universo sem coletar nada novo | baixo |
| 3 | **Status de saída padronizado**, com data e motivo em lista fechada | separa "desistiu", "foi desligada", "ainda cursando" e "concluiu sem registro", que hoje viram o mesmo `evadiu = 1` | baixo |
| 4 | **A pergunta de mãe ou cuidadora em todos os formulários** | é a hipótese mais promissora e não existiu para nenhuma aluna do universo | baixo |
| 5 | **Backlogs com cabeçalho na primeira linha** | cada aba quebrada custou alunas e exigiu um detector inteiro | baixo |

### O que o grupo ainda pode fazer com os dados de hoje

1. **Incorporar os backlogs como segunda fonte** assim que a Fly padronizar chave e status. A
   Estação 6 recebe a fonte B com uma célula a mais, e comparar as duas fontes vira análise de
   sensibilidade do alvo.
2. **Testar um alvo alternativo** em que "aprovada sem registro de formatura" saia do `evadiu = 1` e
   vire categoria própria.
3. **Levar a curva da Estação 15 para a Fly** e perguntar quantas alunas a equipe consegue procurar
   por turma. Esse número define o limiar, e aí ele passa a ser uma decisão da instituição.

### Para o FlyFix

1. Subir a API (`api_passo_firme.py`) para substituir o carregamento manual.
2. Alimentar o modelo com os registros de conversa acumulados, fechando o ciclo que a L5 aponta.
3. Validar o roteiro de mensagens com a equipe de acolhimento da Fly antes do uso em turmas reais.

---

## Time

**Grupo Ada Lovelace (G4)**, Turma Fly de Ciência de Dados e IA, diversiData.

Brenda Amaral · Fernanda da Silva · Nadi Duno · Sheilliane Santos · Vicência Vitória Souza

**Orientadora:** Andressa Freires

Projeto desenvolvido como Trabalho de Conclusão de Curso, em parceria com a Fly Educação.

<p align="center">Feito com rigor técnico e um compromisso: o modelo existe para chegar antes, nunca para rotular. 💛</p>
