# 🪂 Passo Firme — Previsão de Evasão na Fly Educação

> **Uso de dados para identificar o risco de evasão na Fly Educação antes que ele aconteça.**

Modelo preditivo de potencial de empregabilidade e mobilidade financeira para egressas da Fly Educação: um modelo que identifica quem tem mais risco de evadir, **para agir antes**.

**Grupo Ada Lovelace** — Brenda Amaral · Fernanda da Silva · Nadiveth Duno · Profana Buzato · Sheilliane Santos · Vicência Vitória Souza
**Orientadora:** Andressa Freires · Turma Fly · diversiData · agosto/2026

---

## 📌 Contexto

A evasão escolar é um dos principais desafios da educação brasileira e está associada a diversos fatores econômicos, sociais e demográficos — renda familiar, região de residência, gênero, raça/cor, acesso a equipamento e internet, sobrecarga de trabalho e cuidado, entre outros.

O **Passo Firme** parte de uma constatação central: quem desiste de estudar raramente o faz por falta de vontade, mas pelo **acúmulo de obstáculos** — cansaço do trabalho, aperto financeiro, isolamento e questões de saúde mental. A proposta une tecnologia e apoio humano: modelos preditivos em Python identificam estudantes em risco de evasão, e uma camada de IA aciona a rede de acolhimento da ONG (mentoria, escuta ativa, suporte socioemocional) **antes que a desistência aconteça**.

Diferente de outros projetos, aqui o "cliente" é a própria **Fly Educação**. O perfil das inscritas — maioria preta e parda, escolarizada e jovem-adulta (78% pretas + pardas) — já é insumo para relatório institucional e captação de recursos. A entrega de ouro do modelo é uma **lista de fatores de risco acionáveis**: se computador/internet pesarem → empréstimo de equipamento; se horário pesar → turmas alternativas; se confiança inicial pesar → mentoria reforçada nas primeiras semanas.

**Pergunta principal:** Como fatores socioeconômicos e demográficos influenciam a evasão escolar e de que forma uma IA orientadora pode auxiliar estudantes em situação de vulnerabilidade por meio do acesso a informações sobre programas de apoio e permanência?

---

## 🏗️ Visão geral da arquitetura

O pipeline segue o fluxo clássico de um projeto de ciência de dados, do dado bruto ao modelo:

```
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
┌─────────────────────┐
│  Limpeza em lote    │  colunas mortas · cabeçalho vazado · normalização
│  (dataFly)          │  categórica · dicionário de mapeamentos · UFs · idades
└──────────┬──────────┘
           │  3. Criar TARGET (evadiu) a partir de status_aprovacao
           ▼
┌─────────────────────┐
│  Análise exploratória│  perfil das inscritas + taxas de evasão + qui-quadrado
└──────────┬──────────┘
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

## 📏 Regras

Decisões de projeto que valem como convenção para todo o notebook:

1. **`dataFlyRaw` é intocável.** Toda limpeza é feita em uma cópia (`dataFly`). O bruto fica congelado em `.parquet` como backup.
2. **Vazio estrutural ≠ erro da aluna.** Como cada turma respondeu um formulário diferente, muitos nulos ocorrem porque a pergunta *não existia* naquela época. Esses casos são rotulados como `"Pergunta ausente na turma"`; ausências reais viram `"Não respondeu"`.
3. **Nada de moda/mediana antes do split.** Nenhuma imputação estatística no pré-processamento manual — só rótulos-constante. Estatística aprendida só dentro do Pipeline.
4. **Recuperar antes de descartar.** Idades impossíveis são investigadas (data colada, `data_nascimento`) antes de virarem nulo. Nenhuma linha é descartada por erro pontual.
5. **`"Não aprovada"` não é evasão.** Quem não entrou no programa não pode ter evadido — fica fora do universo do modelo (`<NA>`).
6. **Texto livre não se "limpa".** Relatos e descrições das alunas são preservados; a normalização só atinge colunas categóricas (≤ 60 valores únicos).
7. **Atualização = re-executar, não re-digitar.** Tudo centralizado em funções (`criar_target`) e dicionários (`MAPEAMENTOS`). Quando novos status chegam, basta re-rodar.
8. **A métrica que importa é o RECALL da classe "Evadiu".** Encontrar quem precisa de apoio vale mais do que acurácia geral.
9. **Correlação não é causa.** Os achados apontam onde apoiar, nunca "culpam" perfis.

---

## 📁 Estrutura das pastas

Arquivos atuais do repositório:

```
passo-firme/
├── README.md                                          # este arquivo
├── Copy_of_TCC_-_EvasaoFly_-_G4CienciaDeDadosFlyV7.ipynb  # notebook principal (versão atual)
├── renameCols_maps.py                                 # dicionário de renomeação de colunas por turma
├── beneficio_pedemeia.csv                             # dados públicos — programa Pé-de-Meia (ainda não integrado)
└── tx_rend_brasil_regioes_ufs_2023.xlsx               # taxas de rendimento/evasão por região/UF, 2023 (ainda não integrado)
```

**O que é cada arquivo:**

| Arquivo | Papel no projeto | Status |
|---------|------------------|--------|
| `...FlyV7.ipynb` | Notebook principal e mais atual — todo o fluxo (carga → limpeza → target → EDA → modelagem) | ✅ ativo |
| `renameCols_maps.py` | Script com o `rename_maps` — mapeia os nomes originais das colunas de cada turma para nomes padronizados | ✅ em uso |
| `beneficio_pedemeia.csv` | Base pública para cruzar elegibilidade a programas de permanência (Pé-de-Meia) | ⏳ baixada, **ainda não usada** no notebook |
| `tx_rend_brasil_regioes_ufs_2023.xlsx` | Base pública de rendimento/evasão por região e UF (2023) para contextualizar os achados | ⏳ baixada, **ainda não usada** no notebook |

> **Nota de transparência:** na versão atual (V7), o pipeline usa **apenas os dados internos da Fly** (formulários das turmas). As duas bases externas acima já estão no repositório, mas ainda **não foram integradas** ao fluxo — elas são insumo para os próximos passos (cruzamento com programas de apoio e benchmark regional de evasão). Ver *Possíveis melhorias do modelo*.

> **Observação sobre organização:** conforme o projeto cresce, vale separar em subpastas — `notebooks/`, `scripts/` e `data/` (com `raw/` e `processed/`). Como os dados das alunas são sensíveis, mantenha as bases internas fora do versionamento (`.gitignore`) e trabalhe com dados anonimizados/agregados. As bases externas são públicas e podem ser versionadas normalmente.

---

## ⚙️ Setup

O projeto foi desenvolvido no **Google Colab** (Python 3.10+). Para reproduzir:

### 1. Instalar dependências

```bash
pip install missingno gdown pyarrow openpyxl scikit-learn scipy \
            pandas numpy matplotlib seaborn -q
```

Ou, com `requirements.txt`:

```bash
pip install -r requirements.txt
```

**Versões de referência usadas no desenvolvimento:**

| Pacote  | Versão |
|---------|--------|
| pandas  | 2.2.2  |
| numpy   | 2.0.2  |
| seaborn | 0.13.2 |

### 2. Baixar o script de renomeação

O notebook baixa automaticamente o `renameCols_maps.py` via `gdown` (Google Drive). Alternativamente, mantenha-o em `scripts/` e importe localmente:

```python
from renameCols_maps import rename_maps
```

### 3. Executar

Rode o notebook **do início ao fim**. O loader baixa as abas do Google Sheets, a limpeza em massa roda sozinha e `criar_target` reconstrói a coluna `evadiu`.

---

## 🔧 Configuração

Parâmetros ajustáveis no notebook:

| Parâmetro | Onde | Valor padrão | Função |
|-----------|------|--------------|--------|
| `sheet_id` | Seção 1.1 | `1y0cfHPKjqp75...` | ID da planilha do Google Sheets |
| `abas` | Seção 1.1 | dict de 8 turmas | Mapeamento aba → nome da planilha |
| `PRESENTE_MIN` | Seção 2.2 | `5` (%) | Preenchimento mínimo para considerar que a pergunta existia na turma |
| `ANO_REF` | Seção 3.6 | `2026` | Ano de referência para cálculo de idade |
| `FEATURES_CAT` | Seção 6 | `['raca_etnia', 'escolaridade', 'faixa_etaria', 'renda_familiar_pessoa', 'uf']` | Variáveis de entrada do modelo |
| `MINIMO_ALUNAS` | Seção 6 | `300` | **Trava de segurança**: só treina com volume razoável de target |

**Paleta de cores:** roxo `#7b5ea7`, cinza `#A9A9A9`, teal `#0f6e52`.

Para **atualizar com novos dados**, há dois caminhos (ambos em lote):

- **Caminho A** — chegou uma planilha nova com `nome_completo` + `status_aprovacao`: fazer o `merge`, atualizar o status e chamar `criar_target` (o target se refaz sozinho).
- **Caminho B** — o status veio direto no Sheets: basta **re-executar o notebook** do início.

---

## 🎯 Planejamento de aplicabilidade

O modelo não é um fim em si — ele alimenta um **ecossistema de acolhimento** dividido em duas frentes:

### Soluções internas (relacionamento Fly ↔ alunas)
- E-mail automatizado disparado quando os indicadores se aproximam do risco de evasão.
- Acompanhamento pedagógico personalizado e frequente com quem não está acompanhando as aulas.
- Monitoramento de presença ativa (câmera aberta, participação real) para o professor identificar focos de evasão durante as aulas.
- Plantão de dúvidas e canais de acolhimento ao longo da semana.

### Busca por parcerias (externo)
- **Falta de equipamento** → ponte de doação de computadores/wi-fi.
- **Saúde mental** → rede de profissionais com atendimento popular ou gratuito.
- **Falta de renda** → rede de empresas parceiras para oportunidades de trabalho.

### Camada de IA orientadora
Uma IA generativa que traduz o resultado do modelo em **recomendações práticas e personalizadas**. Uma evolução possível: gerar um **índice de risco de evasão** (baixo/moderado/alto) com explicação dos fatores, programas compatíveis com o perfil da estudante e orientações sobre onde buscar apoio — conectando também a programas públicos (Pé-de-Meia, Bolsa Família, PNAE, PNATE, e iniciativas estaduais/municipais).

**Propósito → Processo → Resultado:** evitar que as alunas desistam → construir uma rede forte (interna + parcerias) → transformação real na vida das alunas, com a Fly comprovando o uso de IA para prever evasão e conquistar novas parcerias.

---

## 🧪 Contexto dos dados de treino e teste

**Fonte:** formulários de inscrição/seleção da Fly Educação (turmas 10–23), consolidados em **2.348 linhas × 91 colunas**.

### O desafio central: "vazio estrutural"
Como a Fly evoluiu seus processos, **cada turma respondeu a um formulário diferente**. Boa parte dos nulos não é erro — é a pergunta que simplesmente não existia naquela época. Por isso a distinção rigorosa entre `"Não respondeu"` e `"Pergunta ausente na turma"`.

### A variável-alvo (`evadiu`)
Derivada de `status_aprovacao`:

| Status | Situação | `evadiu` |
|--------|----------|----------|
| Formada | Concluiu o programa | `0` |
| Aprovada | Entrou mas não formou | `1` |
| Não aprovada | Nunca entrou (seleção, não evasão) | `<NA>` (fora do universo) |
| Sem status | Aguardando dados de outras turmas | `<NA>` |

### Estado atual do target (parcial)

| Situação | Contagem |
|----------|----------|
| Aguardando dados da turma | 1.177 |
| Não aprovada (não entrou) | 1.004 |
| Formada (concluiu) | 100 |
| Evadiu (entrou e não concluiu) | 66 |

> **Universo de evasão atual:** 166 alunas · **Taxa parcial:** ~39,8% *(n pequeno — tratar como hipótese até os demais status chegarem).*

### Divisão treino/teste
- **`train_test_split`** com `test_size=0.2` e **estratificação** (`stratify=y`) para manter a proporção de evasão nos dois lados, `random_state=42`.
- Pré-processamento (imputação constante + One-Hot Encoding) encapsulado no `Pipeline`, aprendendo **só no treino**.
- **Modelo:** `LogisticRegression(max_iter=1000, class_weight='balanced')`.
- **Trava:** o treino só dispara com ≥ 300 alunas com target — hoje o notebook avisa e espera.

Nos testes exploratórios (qui-quadrado), nenhum fator isolado apresentou associação significativa ainda (`p > 0,05` em faixa etária, escolaridade, raça/etnia e renda), com alerta de células esperadas < 5 — coerente com o `n` pequeno.

---

## 🚀 Possíveis melhorias do modelo

- **Aumentar o volume de target:** integrar os status das demais turmas para ultrapassar a trava de 300 e ganhar poder estatístico.
- **Incorporar variáveis comportamentais:** frequência, notas e engajamento por módulo (mencionados na proposta, ainda não presentes no dataset atual) — provavelmente os preditores mais fortes.
- **Testar outros algoritmos:** Random Forest, Gradient Boosting (XGBoost/LightGBM) e comparar com a regressão logística, sempre priorizando **recall da classe "Evadiu"**.
- **Ajuste de threshold:** calibrar o limiar de decisão para o custo real (é melhor um falso positivo — oferecer apoio a quem não precisava — do que perder uma aluna em risco).
- **Explicabilidade:** usar SHAP / coeficientes para transformar o modelo em fatores de risco acionáveis e transparentes para a equipe da Fly.
- **Validação cruzada estratificada** em vez de um único split, dado o tamanho da amostra.
- **Enriquecimento externo (bases já disponíveis no repositório):** integrar o `beneficio_pedemeia.csv` para estimar elegibilidade a programas de permanência e o `tx_rend_brasil_regioes_ufs_2023.xlsx` como benchmark regional de evasão — hoje ambas estão baixadas mas ainda fora do pipeline. Cruzar também com dados de mercado (RAIS/CAGED por CBO) para o "Score de Futuro" (inserção e faixa salarial esperada).
- **Métricas justas:** monitorar desempenho por subgrupo (raça, renda, região) para evitar que o modelo desfavoreça exatamente quem deveria proteger.

---

## ✅ Conclusão

**O que já é resultado:**
- Base unificada, congelada e limpa em massa — cada tipo de sujeira com seu padrão de correção em lote.
- Target `evadiu` criado e documentado, com o universo correto (evadir ≠ não ser aprovada) e atualização automática via `criar_target`.
- Perfil das inscritas mapeado: maioria preta e parda, escolarizada, jovem-adulta — a Fly alcança seu público-alvo.
- Taxa de evasão parcial calculada, com honestidade sobre o `n`.

**Próximos passos (em ordem):**
1. Receber os status das demais turmas → re-executar o notebook (tudo se atualiza sozinho).
2. Rodar a modelagem (a trava libera automaticamente) e olhar o **recall da classe "Evadiu"**.
3. Responder à pergunta de ouro do TCC — *quais perfis precisam de mais apoio para concluir?* — e traduzir isso em recomendação concreta para a Fly.

**Limitações honestas:** os formulários mudaram entre turmas (nem toda pergunta existe para todo mundo); o target ainda é parcial; os dados são autodeclarados; e correlação não é causa. Os achados apontam **onde apoiar**, não culpam perfis.

---

> *"A tecnologia possui um peso humano essencial e deve atuar como motor de transformação real e justiça social."* — a ciência de dados e a IA aqui estão plenamente direcionadas para fortalecer o ser humano e promover a inclusão educacional.
