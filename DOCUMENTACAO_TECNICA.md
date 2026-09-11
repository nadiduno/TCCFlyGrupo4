# Documentação Técnica — FlyFix

**Grupo Ada Lovelace (G4)** · Turma 23 · Fly Educação e Cultura · setembro 2026

Este documento acompanha o notebook `Versao_17_TCC_EvasaoFly_G4CienciaDeDadosFlyV17.ipynb` e o README. Ele existe para responder, com número e estação, a três perguntas: **de onde vem cada número**, **quais decisões foram tomadas e por quê**, e **como reproduzir tudo**. Nada aqui foi estimado; todo valor sai de uma célula executada do notebook.

---

## 1. Resumo em uma página

| Item | Valor |
|------|-------|
| **Pergunta** | Dá para prever, no momento da inscrição, quais alunas correm mais risco de evadir, usando só o formulário? |
| **Dados** | Base de inscrições da Fly: 2.380 linhas × 163 colunas, turmas 10 a 23 |
| **Fonte do desfecho** | Coluna `Aprovadas` da própria base (turmas 10, 11, 14, 15 e 17) |
| **Universo do modelo** | 166 alunas que entraram no curso (100 formadas, 66 não) |
| **Taxa de evasão registrada** | 39,8% (66 de 166) |
| **Variáveis no modelo** | 10, escolhidas por cobertura no código |
| **Modelo campeão (por código)** | Regressão Logística, C = 5, L2 |
| **AP na validação cruzada** | 0,521 (piso teórico 0,395; acaso medido 0,496, percentil 95 = 0,599) |
| **p-valor do embaralhamento** | 0,33 — o desempenho ainda cabe no acaso |
| **Resultado no teste (42 alunas)** | recall 1,00 · precisão 0,55 · F1 0,71 · AP 0,63 |
| **Uso permitido** | ordenar fila de prioridade para acolhimento |
| **Uso proibido** | informar risco à aluna; decidir desligamento, bolsa ou seleção; concluir sobre grupo com menos de 10 alunas |
| **Conclusão** | O formulário de inscrição não basta para prever evasão. O gargalo é a ausência de dados de engajamento durante o curso. |

---

## 2. Histórico dos números (por que houve três taxas diferentes)

Ao longo do projeto circularam três taxas de evasão. Registrar isso é parte da honestidade do TCC.

| Versão | Universo | Taxa | O que aconteceu | Vale? |
|--------|----------|------|-----------------|-------|
| **V14** | 215 alunas | 33,5% | Somava a coluna `Aprovadas` com os backlogs. O leitor de backlog era permissivo: a T20 entrou com 17 alunas e 0% de evasão a partir de uma coluna vazia, inflando a classe "ficou". | Não |
| **V15 / V16** | 60 alunas | 65,0% | O mapa de conceitos passou a procurar a coluna de status como `status_aprovacao`, `Status de aprovação` ou `Status`. O nome real é `Aprovadas`, então a fonte A sumiu. Sobraram só os backlogs — 33 das 60 alunas de um bloco com 100% de evasão. O modelo "excelente" reconhecia a turma, não a aluna. | Não |
| **V17 (esta)** | 166 alunas | 39,8% | Alvo de uma fonte só, a coluna `Aprovadas`, com regra única e auditoria por turma. Backlog fora, documentado. | **Sim** |

**Fato que sustenta a decisão:** entre as 123 alunas dos backlogs que têm status, a evasão é 30%; entre as 60 que cruzavam com o perfil, 65%. Essa diferença é **viés de seleção da chave**, não taxa do programa. Misturar as fontes contaminava o alvo.

---

## 3. Os dados

### 3.1 O arquivo

`BDFLYG4.xlsx - Sheet1.csv`: 2.380 linhas, 165 colunas antes do *strip* dos nomes, 163 depois (duas perguntas só diferiam por um espaço no fim e foram juntadas por *coalesce*). Cada linha é uma candidata. O `aluna_id` (formato `Aluna 1331`) é o identificador anônimo atribuído pela Fly: 2.218 ids distintos, 162 linhas repetidas (a mesma pessoa inscrita em mais de uma turma).

### 3.2 O vazio estrutural

**81,8% das células estão vazias.** Não é descuido: cada turma respondeu a um formulário diferente, e 91 das 163 colunas foram respondidas por uma turma só. O notebook (Estação 2.4) desenha esse vazio por turma. **Regra:** vazio estrutural não se preenche com moda ou mediana e não se descarta; ele se **rotula**.

### 3.3 Dados pessoais

58 das 163 colunas foram sinalizadas como risco (texto livre, identificador, relato). Nenhuma delas é impressa no notebook e nenhuma entra no modelo (Estação 2.5). A data de nascimento serve só para derivar a idade e é descartada.

### 3.4 Harmonização: de 163 colunas a 15 conceitos

Um mapa de conceitos lista, para cada conceito, todas as colunas em que ele pode estar. O *coalesce* pega o primeiro valor não vazio, e um filtro de vocabulário descarta resposta fora da pergunta (formulário desalinhado). O notebook imprime o que foi descartado por conceito (Estação 3.2), para que um rótulo novo nunca suma em silêncio.

| Variável | Tipo | O que mede | Como foi construída | % preenchido na base |
|----------|------|------------|---------------------|:---:|
| `renda_ord` | ordinal 0 a 3 | renda familiar por pessoa, em salários mínimos | 0: menos de 1 SM; 1: de 1 a 2; 2: de 2 a 3; 3: mais de 3 | 99 |
| `raca` | categórica | autodeclaração de raça/cor | Preta, Parda, Branca, Indígena, Amarela | 98 |
| `tem_computador` | categórica | tem computador ou notebook | Sim; Não (celular ou tablet como único aparelho) | 96 |
| `tem_internet` | categórica | tem acesso à internet | Sim, Não | 94 |
| `deficiencia` | categórica | é pessoa com deficiência | Sim, Não | 94 |
| `genero` | categórica | autodeclaração de gênero | Mulher Cis, Mulher Transgênero, Não-binárie, Outro | 93 |
| `lgbt` | categórica | pertence à comunidade LGBTQIAPN+ | Sim, Não, Prefiro não responder | 93 |
| `regiao` | categórica | região do país | N, NE, CO, SE, S — derivada de 95 grafias de estado | 90 |
| `idade` | numérica | anos completos em 2026 | número direto, resgatado de data colada, ou calculado da data de nascimento | 88 |
| `escolaridade` | ordinal 1 a 6 | maior nível de estudo | 1: fundamental incompleto até 6: pós-graduação | 87 |
| `disponibilidade_h` | numérica | horas por dia declaradas | ponto médio da faixa; formulários por semana divididos por 7 | 70 |
| `pessoas_casa` | numérica | pessoas na casa | número declarado, válido entre 1 e 15 | 69 |
| `mae_cuidadora` | categórica | tem filhos ou é cuidadora | Sim, Não; pergunta que só existiu em parte dos formulários | 12 |
| `turma_aba` / `turma` | contexto | turma da inscrição | não entra no modelo | 100 |
| `status_aprovacao` | alvo | resultado da seleção e do curso | coluna `Aprovadas`, seis grafias | 49 |

---

## 4. O alvo `evadiu`

### 4.1 A regra

| Valor em `Aprovadas` | Situação | `evadiu` |
|----------------------|----------|----------|
| Não aprovada, Não aprovadas (1.004) | não entrou no curso | fora do universo |
| Formada, Formadas (100) | entrou e concluiu | 0 |
| Aprovada, Aprovadas (66) | entrou e não consta como formada | 1 |

**Princípio:** evadir não é o mesmo que não ser aprovada. **Seleção não é evasão.** Uma `assert` no notebook garante que nenhuma grafia nova passe sem ser mapeada, e outra garante que nenhuma aluna se repita no universo.

### 4.2 O funil

| Degrau | Alunas |
|--------|:---:|
| Inscrições na base | 2.380 |
| Com a coluna `Aprovadas` preenchida (turmas 10 a 17) | 1.170 |
| Não aprovadas (fora) | 1.004 |
| **Entraram: universo do modelo** | **166** |
| Não constam como formadas | 66 (39,8%) |

### 4.3 A auditoria por turma

| Turma | Alunas | Evadiram | Taxa |
|-------|:---:|:---:|:---:|
| T14 | 50 | 33 | 66,0% |
| T17 | 50 | 20 | 40,0% |
| T15 | 30 | 12 | 40,0% |
| T10 | 19 | 0 | 0,0% |
| T11 | 17 | 1 | 5,9% |

**Leitura obrigatória:** T10 e T11 com evasão perto de zero provavelmente não registraram quem saiu. Se a banca perguntar, a taxa das turmas 14, 15 e 17 é 65 de 130 (**50%**). O notebook reporta a taxa geral sempre com o `n` e com a abertura por turma.

### 4.4 Por que o backlog ficou fora

Medido na versão anterior: 6 das 12 abas (312 alunas) sem coluna de status; metade das abas com cabeçalho quebrado; 90 das 435 chaves (21%) casando com a base; definição de "saiu" diferente (Desligada, Evasão, assinatura de desligamento). A Estação 6.3 documenta isso e a Estação 16 transforma em pedido à Fly. Quando a chave e o status forem padronizados, a fonte B entra com uma célula a mais.

---

## 5. EDA com hipóteses (Estação 7)

Toda taxa por grupo vem com **intervalo de confiança de Wilson (95%)**, e a força da associação é o **V de Cramér corrigido por viés (Bergsma)**. Com 166 alunas, o qui-quadrado não vale em várias tabelas (casela esperada menor que 5) e o p-valor fica indisponível, o que é **reportado, não escondido**.

| Variável | n usado | V de Cramér | Força | Conclusão |
|----------|:---:|:---:|-------|-----------|
| região | 164 | 0,261 | fraca | não dá para afirmar com este n |
| deficiência | 166 | 0,066 | quase nula | idem |
| raça | 165 | 0,000 | quase nula | idem |
| computador | 166 | 0,000 (p = 0,51) | quase nula | idem |
| LGBTQIAPN+ | 165 | 0,000 (p = 0,68) | quase nula | idem |
| gênero | 165 | 0,000 | quase nula | idem |
| mãe/cuidadora | 0 | não testável | — | pergunta não existiu para o universo |
| internet | 0 | não testável | — | sem variação no universo |

**Teste de confundimento (7.4):** turma × região tem V = 0,501, **maior** que região × evasão (0,261). Veredito do notebook: região está mais associada à turma do que à evasão; interpretar como causa seria erro.

**Destino das hipóteses:**

- **H1 (computador):** sem associação.
- **H2 (cuidado):** não testável — e isso é o achado mais acionável do TCC.
- **H3 (disponibilidade):** fora do modelo por cobertura (22% no universo).
- **H4 (renda):** sem associação forte.
- **H5 (perfil):** só região aparece, colada na turma.

---

## 6. Preparo e modelagem (Estações 9 a 12)

### 6.1 Regras contra vazamento

- Separação treino/teste **75/25 estratificada** (124/42 alunas; 39,5% e 40,5% de evasão).
- Imputação (mediana ou categoria mais frequente) e *one-hot* **dentro do Pipeline**, aprendidos só no treino, inclusive em cada dobra da validação cruzada.
- Variáveis escolhidas **por código**: entram as que têm ao menos 60% de resposta dentro do universo e alguma variação. Ficaram fora `disponibilidade_h` (22%), `mae_cuidadora` (0%), `tem_internet` (sem variação) e `turma_aba` (confundida com período e região).
- 10 variáveis viram 24 colunas numéricas: **5,2 alunas por coluna** no treino. Abaixo de 10, modelo complexo decora. Isso foi anotado **antes** de comparar.

### 6.2 A régua

Métrica principal: **AP (average precision, AUC-PR)**, porque a pergunta operacional é "o modelo ordena bem quem tem mais risco?". Piso do acaso: a taxa de evasão do treino (0,395). **Acurácia é proibida sozinha:** um modelo que diz "ninguém evade" acerta 60% e não acha ninguém. Meta operacional: **recall ≥ 80%**, porque deixar passar custa mais que uma conversa a mais.

### 6.3 Comparação (5 dobras × 10 repetições = 50 provas)

| Modelo | AP | desvio | Recall | Precisão | F1 | Acurácia |
|--------|:---:|:---:|:---:|:---:|:---:|:---:|
| **Regressão Logística** | **0,525** | 0,091 | 0,568 | 0,477 | 0,510 | 0,577 |
| Random Forest | 0,523 | 0,097 | 0,524 | 0,462 | 0,483 | 0,562 |
| Gradient Boosting | 0,477 | 0,084 | 0,331 | 0,419 | 0,360 | 0,553 |
| Árvore rasa | 0,455 | 0,047 | 0,642 | 0,444 | 0,513 | 0,542 |
| Baseline | 0,395 | 0,010 | 0,000 | 0,000 | 0,000 | 0,605 |

A campeã é escolhida **por código** (maior AP média) e todas as estações seguintes usam só ela. A Regressão Logística e a Random Forest estão empatadas dentro do desvio; a logística vence por 0,002 e tem a vantagem de **coeficientes legíveis**. A campeã variou de AP 0,40 a 0,86 entre as 50 provas: essa largura é parte do resultado.

### 6.4 Ajuste e limiar

Busca em grade com validação cruzada (mesmas 50 provas): **C = 5, penalidade L2, solver lbfgs**. AP ajustada: 0,521. O limiar **não é 0,5**: é o maior limiar que ainda entrega recall ≥ 80% no treino, medido com probabilidades fora da amostra (`cross_val_predict`). Resultado: **0,296**, com recall 82% e precisão 43% no treino. Tradução: a cada 10 alunas procuradas, cerca de 4 teriam evadido mesmo.

---

## 7. Diagnóstico e honestidade (Estações 13 e 14)

### 7.1 Teste (42 alunas, 17 evadiram, limiar 0,30)

|  | previu ficar | previu evadir |
|--|:---:|:---:|
| **ficou (25)** | VN 11 | FP 14 |
| **evadiu (17)** | FN 0 | VP 17 |

AP 0,634 (acaso 0,405) · recall 1,000 · precisão 0,548 · F1 0,708 · acurácia 0,667 · Brier 0,197 (chutar a taxa da base: 0,241). Importância por permutação acima da própria incerteza: raça e região. Os coeficientes indicam direção, não magnitude confiável.

### 7.2 Embaralhamento

Cerca de 280 rodadas em que o alvo é embaralhado e a busca em grade inteira é refeita. AP do modelo real: **0,521**. AP do acaso: média 0,496, percentil 95 = 0,599. **p = 0,33.** Veredito: o desempenho está dentro do que o acaso produz com este universo. **Não é fracasso do grupo:** é a medida honesta do que a base permite, e é o argumento central para o pedido de dados.

### 7.3 O que isso significa para o uso

- **Pode:** ordenar uma fila de prioridade para o acolhimento (a curva da Estação 15 mostra o ganho sobre procurar ao acaso, em uma divisão de teste).
- **Não pode:** afirmar que uma aluna vai evadir, informar risco a ela, decidir desligamento, bolsa ou seleção, ou tirar conclusão sobre grupos com menos de 10 alunas.

---

## 8. Limitações (Estação 8, escritas antes do modelo)

| # | Limitação | Número |
|---|-----------|--------|
| **L1** | Tamanho | 166 alunas, 66 evasões; teste com 42, uma aluna move o recall em 6 pontos |
| **L2** | Alvo de uma fonte só | `evadiu = 1` é "aprovada sem registro de formatura"; taxa registrada, não vivida |
| **L3** | Confundimento | 30% do universo vem de uma turma; turma × região V = 0,50 |
| **L4** | Cobertura | desfecho só em 1.170 das 2.380 inscrições (turmas 10 a 17) |
| **L5** | Variáveis | nenhuma de engajamento durante o curso. **A decisiva.** |
| **L6** | Autodeclaração | promessas feitas na inscrição |
| **L7** | Escala | `disponibilidade_h` mistura formulários por dia e por semana |

---

## 9. Artefatos gerados pelo notebook

| Arquivo | Conteúdo | Uso |
|---------|----------|-----|
| `dados/universo_com_alvo.parquet` | as 166 alunas, 17 colunas, sem dado identificável além do `aluna_id` anônimo | auditoria, FlyFix |
| `dados/modelo_evasao_G4.joblib` | `{'modelo': Pipeline, 'limiar': 0.296, 'variaveis': [...]}` | API `api_passo_firme.py` |
| `dados/ficha_do_modelo.json` | model card: universo, taxa, variáveis, campeão, hiperparâmetros, AP, p-valor, limiar, métricas no teste, usos permitidos e proibidos, fonte do alvo, limitações | fonte da verdade dos números |
| `dados/fila_prioridade.csv` | fila do teste ordenada por risco, sem probabilidade | FlyFix, aba Base e conexão |

A pasta `dados/` **nunca** é commitada.

---

## 10. Como reproduzir

1. Colab ou local (ver README). Semente fixa em **42**; os números batem entre execuções.
2. Rodar as células em ordem. Tempo total em máquina comum: 5 a 8 minutos (a Estação 14 usa cerca de 2 minutos e escolhe sozinha o número de rodadas, mínimo 40).
3. O número principal em três linhas:

```python
import pandas as pd
base = pd.read_csv("BDFLYG4.xlsx - Sheet1.csv", low_memory=False)
st = base["Aprovadas"].str.strip().str.lower()
entraram = st.isin(["formada", "formadas", "aprovada", "aprovadas"])
print(entraram.sum(), round(st[entraram].isin(["aprovada", "aprovadas"]).mean(), 3))   # 166 0.398
```

---

## 11. FlyFix: regras da camada de acolhimento

| Regra | Como é aplicada |
|-------|-----------------|
| Nunca mostrar probabilidade ou score | a fila exibe posição e faixa (atenção maior, média, menor) |
| Raça, orientação e deficiência nunca viram motivo de contato | guarda de linguagem antes de qualquer texto na tela |
| A IA sugere, a gestora envia | nenhum envio automático; nenhuma mensagem para a aluna sem revisão humana |
| Trilhas de apoio para toda a turma | acesso digital, rede de cuidado, logística; nunca como diagnóstico individual |
| Registro de conversa | cria a coluna de engajamento que a base não tem (L5) |
| Aviso de protótipo | toda tela que faz previsão avisa que é protótipo acadêmico e não serve para decisão sobre pessoas reais |

---
