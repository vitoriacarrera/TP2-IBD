# DCC011 — Introdução a Banco de Dados (2026/2)
## Trabalho Prático 2: Modelagem e Análise de Dados Abertos
### Tema: Fiscalização Ambiental e Autos de Infração do IBAMA em 2026

Repositório colaborativo do Grupo para desenvolvimento, versionamento e entrega do Trabalho Prático 2 da disciplina **DCC011: Introdução a Banco de Dados** (UFMG - 2026/2).

---

## 👥 Membros da Equipe
* **Evandro Araújo Furlaneto Delgado** — Matrícula: `2025018830`
* **Letícia Schmidt Tolentino Santos** — Matrícula: `2025014168`
* **Victor Daniel Barbosa Thomé** — Matrícula: `2026303953`
* **Vitória Medeiros Carrera de Oliveira** — Matrícula: `2019027245`
* **Yuri Florentino Kosfeld** — Matrícula: `2022038503`

---

## 📂 Arquitetura do Repositório

```text
.
├── tp2.ipynb              # Notebook principal de entrega (executado e consolidado)
├── tp2_template.ipynb     # Template oficial fornecido pelo professor (gabarito de formatação)
├── TP2_IBD_PROPOSTA.md    # Proposta oficial submetida e aprovada pela disciplina
├── tp2.pdf                # Edital oficial com as instruções, rubricas e datas
├── graficos/              # Imagens e figuras em alta resolução prontas para os slides
│   ├── diagrama_er.png
│   ├── consulta_9_macro_infracoes.png
│   ├── consulta_10_multas_por_uf.png
│   └── analise_dispersao_geoespacial.png
├── .gitignore             # Bloqueia o versionamento de CSVs, ZIPs e bases SQLite pesadas
└── README.md              # Este guia de contexto, modelagem e fluxo de trabalho
```

---

## 🏛️ Modelo Relacional do Projeto (SQLite)

O banco de dados relacional foi construído via processo *bottom-up* em **3ª Forma Normal (3FN)** a partir do conjunto de dados aberto do IBAMA (`auto_infracao_2026.csv`), totalizando **36.218 instâncias** distribuídas em:

1. **`Municipio` (1.674 instâncias):** `cod_municipio` (PK IBGE), `nome_municipio`, `uf`.
2. **`Infrator` (9.858 instâncias):** `id_infrator` (PK surrogate), `nome_infrator`, `tipo_pessoa` (PF/PJ), `cpf_cnpj`.
3. **`Ordem_Fiscalizacao` (872 instâncias):** `codigo_ordem` (PK), `unidade_ordenadora`, `nome_operacao`.
4. **`Tipo_Infracao` (23 instâncias):** `id_tipo` (PK surrogate), `cod_infracao`, `descricao_infracao`, `tipo_infracao`.
5. **`Autuado_Em` (10.172 instâncias - Relacionamento M:N):** Chave composta (`id_infrator`, `codigo_ordem`). Modela as ordens fiscais e operações especiais em que cada infrator foi autuado.
6. **`Auto_Infracao` (13.619 instâncias):** `num_auto_infracao` (PK), `data_hora`, `valor_multa`, `tipo_auto`, `descricao_fato`, `forma_entrega`, `local`, `bioma`, `unidade_controle`, `latitude`, `longitude` e as Chaves Estrangeiras (`id_infrator`, `cod_municipio`, `id_tipo`, `codigo_ordem`).

---

## 🌿 Diretrizes do Fluxo de Trabalho Git (Para Membros e IAs)

Para garantir produtividade, organização e **zero conflitos destrutivos** de mesclagem no arquivo `.ipynb`:

### 1. A branch `main` é protegida
* **Nunca comite diretamente na `main`.** Todo trabalho deve vir por meio de um **Pull Request (PR)** aprovado por pelo menos 1 colega.

### 2. Fluxo de Branches por Tarefa
Antes de iniciar qualquer edição, crie e troque para uma branch específica da sua tarefa:
```bash
git checkout -b feature/nome-da-sua-tarefa
# Exemplos:
# git checkout -b docs/autoavaliacao-evandro
# git checkout -b refactor/consulta-8-grafico
```

### 3. Regra de Ouro contra Conflitos em Notebooks Jupyter (`.ipynb`)
Arquivos `.ipynb` são estruturas em JSON que salvam contadores de execução (`execution_count`), metadados e imagens binárias toda vez que uma célula é executada. Para evitar conflitos de merge:
1. **Durante o desenvolvimento:** No Jupyter/Colab, antes de dar `git add`, vá em **Kernel $\rightarrow$ Restart & Clear All Outputs** e salve o notebook limpo de saídas de execução.
2. **Dica recomendada (`nbstripout`):** Você pode instalar este filtro automático do Git na sua máquina para limpar metadados e saídas automaticamente a cada commit:
   ```bash
   pip install nbstripout
   nbstripout --install
   ```
3. **Na versão final consolidada:** A consolidação com os DataFrames e gráficos já renderizados é executada e commitada centralmente na `main`.

### 4. Abrindo o Pull Request
Ao terminar sua parte e enviar para o GitHub:
```bash
git push -u origin feature/nome-da-sua-tarefa
```
1. Abra o PR apontando para a `main`.
2. Descreva de forma objetiva o que foi alterado.
3. Peça a revisão de pelo menos 1 colega. Após a aprovação (*Approve*), faça o **Squash and merge**.

---

## 🤖 Contexto e Diretrizes para Assistentes de IA (Copilot / ChatGPT / Claude / Gemini)

Caso algum membro do grupo utilize ferramentas de IA generativa para auxiliar na escrita ou refinamento do código, a IA **DEVE** seguir as seguintes restrições do projeto:

> ### ⚠️ Regras Inegociáveis para Agentes de IA:
> 1. **Preservar a Estrutura do Template:** Não altere a hierarquia ou a redação dos 21 títulos e subtítulos definidos em `tp2_template.ipynb` (Seções 1 a 7 e subseções 6.1 a 6.4.2). O professor usa script automático de verificação.
> 2. **Fidelidade ao Esquema Relacional:** Não crie entidades ou tabelas inventadas. O modelo é estritamente o aprovado em `TP2_IBD_PROPOSTA.md` (5 entidades + relação M:N `Autuado_Em`).
> 3. **Cotas Obrigatórias de Consultas (Seção 6):** As 10 consultas devem obedecer estritamente a proporção:
>    * 2 de seleção e projeção (6.1.1 e 6.1.2)
>    * 3 de junção de 2 relações (6.2.1 a 6.2.3)
>    * 3 de junção de 3 ou mais relações (6.3.1 a 6.3.3)
>    * 2 de agregação sobre junção (6.4.1 e 6.4.2)
> 4. **Tratamento de Dados:** Valores monetários (`valor_multa`) e coordenadas (`latitude`/`longitude`) devem ser mantidos como tipos numéricos `REAL` para viabilizar cálculos analíticos.
> 5. **Não comitar arquivos binários ou datasets pesados:** O arquivo `auto_infracao_csv.zip` e bases `.csv` ou `.db` devem permanecer ignorados no `.gitignore`.

---

## 🏃 Como Executar Localmente

1. Clone o repositório:
   ```bash
   git clone https://github.com/Ykosfeld/TP2-IBD.git
   cd TP2-IBD
   ```
2. Certifique-se de que o arquivo compactado `auto_infracao_csv.zip` ou o arquivo `auto_infracao_2026.csv` esteja presente na raiz ou dentro da pasta `auto_infracao_csv/` (disponibilizado internamente pelo grupo).
3. Abra o Jupyter Notebook ou Google Colab:
   ```bash
   jupyter lab tp2.ipynb
   # ou
   jupyter notebook tp2.ipynb
   ```
4. Execute as células sequencialmente. O script detecta e descompacta a base de dados automaticamente caso necessário, cria o banco `auto_infracao.db` e executa todas as consultas analíticas.
