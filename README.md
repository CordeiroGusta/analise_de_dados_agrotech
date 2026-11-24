# AgroTech Solutions — Auditoria de Dados e KPIs Gerenciais

Projeto acadêmico desenvolvido para a disciplina de **Banco de Dados II**, com o objetivo de transformar uma base de dados consolidada de filiais em informações confiáveis, por meio de processos de limpeza, padronização e análise de dados, apoiando decisões estratégicas da gestão.

---

## 1. Visão geral

O projeto consiste na análise e tratamento de uma base de dados fictícia da empresa AgroTech Solutions, do segmento agropecuário.

O trabalho foi dividido em três partes:

### Parte 1 — Limpeza e tratamento de dados

Identificação e correção de inconsistências na base original, incluindo registros duplicados, erros de digitação, fornecedores com nomes divergentes, valores unitários incorretos, quantidades inválidas, estados e categorias fora do padrão e datas incompatíveis. Também foram aplicados bloqueios comerciais em determinados produtos.

### Parte 2 — Relatório gerencial

Elaboração de consultas SQL para analisar a disponibilidade dos produtos, participação dos fornecedores, valores unitários, distribuição por categorias e estados, volume armazenado e valor potencial do estoque.

### Parte 3 — Indicadores-chave de desempenho (KPIs)

Desenvolvimento de 10 indicadores gerenciais utilizando funções de agregação, agrupamentos, subconsultas, expressões condicionais e tratamento de divisão por zero. Os indicadores permitem avaliar a disponibilidade, o volume físico, o valor potencial do estoque e a concentração por fornecedores, categorias e estados.

---

## 2. Como executar

### Requisitos

- MySQL Server 8.0 ou superior.
- MySQL Workbench ou outro cliente compatível com MySQL.
- Permissão para criação de bancos de dados e execução de comandos SQL.

### Executando o projeto

1. Clone ou baixe este repositório.
2. Abra o MySQL Workbench e conecte-se ao servidor MySQL.
3. Abra o arquivo `AgroTech.sql`.
4. Execute o script completo, desde a criação do banco até as consultas dos KPIs.
5. Confira os resultados apresentados pelas consultas de cada etapa.

### Arquivo SQL

O arquivo `AgroTech.sql` concentra toda a implementação do projeto, incluindo:

- Criação do banco de dados `agropecuaria_db`.
- Criação da tabela `tb_vendas_agro`.
- Inserção dos 75 registros iniciais.
- Identificação e tratamento das inconsistências.
- Consultas do relatório gerencial.
- Implementação e execução dos 10 KPIs.

**Importante:** o script cria o banco de dados utilizando `CREATE DATABASE`, sem utilizar `IF NOT EXISTS` ou excluir bancos preexistentes. Portanto, deve ser executado em um ambiente no qual o banco `agropecuaria_db` ainda não exista.

---

## 3. Dicionário de dados

### Tabela: `tb_vendas_agro`

A tabela `tb_vendas_agro` armazena os registros de produtos da AgroTech Solutions, reunindo informações sobre identificação, classificação, fornecedores, localização, quantidade, preço, data e disponibilidade em estoque.

Cada linha representa um registro de produto associado a um fornecedor e a um estado, contendo sua quantidade e valor unitário cadastrados.

A tabela possui nove colunas:

| Coluna           | Tipo de dado  | Descrição                                                                                      | Exemplo da base tratada |
| ---------------- | ------------- | ---------------------------------------------------------------------------------------------- | ----------------------- |
| `id`             | INT           | Identificador único do registro. Chave primária da tabela.                                     | `1`                     |
| `produto`        | VARCHAR(100)  | Nome comercial do produto agropecuário.                                                        | `Milho Safrinha`        |
| `categoria`      | VARCHAR(50)   | Classificação do produto, como Grãos, Rações, Defensivos, Minerais, Sementes ou Fertilizantes. | `Grãos`                 |
| `fornecedor`     | VARCHAR(100)  | Empresa responsável pelo fornecimento do produto.                                              | `AgroSul`               |
| `estado`         | VARCHAR(50)   | Unidade federativa associada ao registro, padronizada pela sigla oficial.                      | `PR`                    |
| `quantidade`     | INT           | Quantidade de unidades físicas armazenadas ou registradas para o produto.                      | `120`                   |
| `valor_unitario` | DECIMAL(10,2) | Valor monetário cadastrado por unidade do produto, com duas casas decimais.                    | `89.90`                 |
| `data_venda`     | DATE          | Data associada ao registro do produto, utilizada para análises temporais.                      | `2025-01-10`            |
| `estoque`        | BOOLEAN       | Indica se o produto está disponível para comercialização (`TRUE`) ou indisponível (`FALSE`).   | `TRUE`                  |

### Restrições e regras aplicáveis

- **`id`:** chave primária (`PRIMARY KEY`), impedindo identificadores duplicados.
- **`produto`:** nomes padronizados para evitar variações de escrita e duplicidade de nomenclatura.
- **`categoria`:** classificação padronizada para permitir agrupamentos consistentes.
- **`fornecedor`:** nomes corrigidos para evitar que o mesmo fornecedor seja contabilizado separadamente por erros de digitação.
- **`estado`:** utilização de siglas estaduais, como PR, SP, MT e SC.
- **`quantidade`:** valores negativos e iguais a zero foram tratados conforme as inconsistências identificadas.
- **`valor_unitario`:** valores negativos, zerados ou considerados suspeitos foram corrigidos.
- **`data_venda`:** registros posteriores a 31/12/2025 foram excluídos.
- **`estoque`:** valores booleanos utilizados para representar a disponibilidade comercial. Produtos com valor unitário superior a R$ 800,00 na categoria Defensivos foram bloqueados.

**Observação:** os tipos e a chave primária representam as restrições declaradas na criação da tabela. As demais regras de qualidade e validação foram implementadas por meio dos comandos de tratamento SQL, não por restrições adicionais `CHECK` ou `NOT NULL`.

### Interpretação das principais colunas

#### Quantidade (`quantidade`)

Representa o volume físico de unidades associado a cada registro. É utilizada para calcular o volume total armazenado, identificar registros de grande quantidade e apoiar análises logísticas.

#### Estoque (`estoque`)

Representa a disponibilidade comercial do produto. Um registro com `TRUE` está disponível para comercialização, enquanto `FALSE` indica indisponibilidade ou bloqueio comercial.

Um produto indisponível continua fazendo parte da base e suas unidades continuam sendo contabilizadas nos indicadores que consideram todos os registros.

#### Data de venda (`data_venda`)

Representa a data associada ao registro e permite análises por período. No projeto, foi utilizada para delimitar o período válido da base, mantendo apenas registros até 31/12/2025.

A existência dessa data não significa, isoladamente, que ocorreu uma venda efetiva.

---

## 4. Resultados do tratamento e do relatório gerencial

### 4.1. Resultados da limpeza e padronização

A base original possuía 75 registros, contendo inconsistências intencionais para simular problemas encontrados em ambientes reais de dados.

Após a execução dos procedimentos de tratamento, foram obtidos os seguintes resultados:

| Indicador                                   | Resultado |
| ------------------------------------------- | --------: |
| Registros na carga inicial                  |        75 |
| Registros duplicados removidos              |         3 |
| Registros com datas incompatíveis removidos |         4 |
| Total de registros excluídos                |         7 |
| Registros corrigidos                        |        18 |
| Produtos bloqueados comercialmente          |         6 |
| Registros na base tratada                   |        68 |
| Unidades totais armazenadas                 |     7.387 |

Os principais procedimentos realizados foram:

1. Remoção de três registros duplicados de produtos.
2. Correção de cinco nomes de produtos.
3. Padronização de três fornecedores.
4. Correção de quatro valores unitários suspeitos ou inválidos.
5. Correção de duas quantidades incorretas.
6. Padronização de dois estados.
7. Correção de duas categorias.
8. Exclusão de quatro registros com datas posteriores ao limite estabelecido.
9. Bloqueio comercial de seis registros de defensivos com valor unitário superior a R$ 800,00.

Os dois registros completamente inconsistentes também foram eliminados pelo critério de data, pois apresentavam datas posteriores a 31/12/2025.

**Resultado:** a base passou de 75 para 68 registros, mantendo os dados corrigidos e padronizados para as análises seguintes.

### 4.2. Resultados do relatório gerencial

Após o tratamento, foram executadas consultas para compreender a composição da base e identificar concentrações relevantes.

#### Disponibilidade dos produtos

- 48 registros disponíveis.
- 20 registros indisponíveis.

Isso representa aproximadamente 70,59% de disponibilidade na base tratada.

#### Participação dos fornecedores

| Fornecedor  | Registros |
| ----------- | --------: |
| Campo Verde |        16 |
| NutriCampo  |        14 |
| AgroSul     |        11 |
| Solo Forte  |        10 |
| BioPlant    |         9 |
| AgroMax     |         8 |

Campo Verde apresenta a maior participação, com 16 dos 68 registros.

#### Distribuição por estado

| Estado | Unidades armazenadas |
| ------ | -------------------: |
| PR     |                2.107 |
| MT     |                1.561 |
| MS     |                1.227 |
| GO     |                  899 |
| SC     |                  614 |
| RS     |                  415 |
| MG     |                  320 |
| SP     |                  244 |

Paraná e Mato Grosso concentram aproximadamente 49,65% das unidades armazenadas.

#### Valor potencial do estoque disponível

O valor potencial do estoque disponível foi calculado utilizando a multiplicação da quantidade pelo valor unitário de cada registro disponível.

O resultado foi de **R$ 769.784,00**.

Esse valor representa uma estimativa potencial baseada nos dados cadastrados, não correspondendo a faturamento ou lucro efetivamente realizado.

---

## 5. Catálogo e resultados dos 10 KPIs

Os indicadores foram desenvolvidos utilizando a tabela `tb_vendas_agro`, com diferentes combinações de agregações, filtros e agrupamentos.

### Convenções de cálculo

Para os indicadores financeiros que utilizam a multiplicação de quantidade pelo valor unitário, foi adotada a expressão **valor potencial do estoque**.

Essa expressão representa uma estimativa baseada no volume registrado e no preço unitário cadastrado, não devendo ser interpretada como faturamento, receita ou lucro.

Nos indicadores percentuais, a divisão por zero é tratada com a função `NULLIF(denominador, 0)`. Dessa forma, quando o denominador é zero, a consulta retorna `NULL` em vez de gerar um erro de divisão por zero.

---

### KPI 01 — Taxa de disponibilidade de registros

**Objetivo:** medir quanto do catálogo está apto à comercialização.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `estoque`
- `id`

**Regra de cálculo:**

Registros com `estoque = TRUE` divididos pelo total de registros, multiplicados por 100.

**Numerador:** 48 registros disponíveis.

**Denominador:** 68 registros totais.

**Filtro:** nenhum. São considerados todos os registros da base tratada.

**Unidade de medida:** percentual (%).

**Resultado: 70,59%**

48 ÷ 68 × 100 = 70,59%.

Dos 68 registros, 48 estão disponíveis e 20 estão indisponíveis, correspondendo a 29,41% da base.

**Interpretação gerencial:** a empresa pode utilizar esse indicador para monitorar a disponibilidade geral do catálogo e estabelecer metas de liberação ou reposição de produtos.

Meta sugerida pelo grupo: 80% ou mais.

---

### KPI 02 — Unidades em registros indisponíveis

**Objetivo:** mostrar quanto do volume físico armazenado está parado em itens indisponíveis.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `estoque`
- `quantidade`

**Regra de cálculo:**

Soma das quantidades dos registros com `estoque = FALSE`, dividida pela soma das quantidades de todos os registros, multiplicada por 100.

**Numerador:** 1.706 unidades indisponíveis.

**Denominador:** 7.387 unidades totais.

**Filtro:** nenhum no denominador; o numerador considera apenas `estoque = FALSE`.

**Unidade de medida:** percentual (%) e unidades físicas.

**Resultado: 23,09%**

1.706 ÷ 7.387 × 100 = 23,09%.

Quase um quarto das unidades armazenadas está associado a registros indisponíveis.

**Interpretação gerencial:** esse indicador permite identificar o volume físico que não está apto à comercialização, apoiando decisões sobre liberação, realocação ou escoamento de produtos.

---

### KPI 03 — Valor potencial do estoque disponível

**Objetivo:** dimensionar o valor potencial dos produtos que estão aptos à comercialização.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `quantidade`
- `valor_unitario`
- `estoque`

**Regra de cálculo:**

Soma de `quantidade × valor_unitario` dos registros com `estoque = TRUE`.

Também é calculada a participação desse valor em relação ao valor potencial total da base.

**Numerador:** R$ 769.784,00, correspondente ao valor potencial dos registros disponíveis.

**Denominador:** R$ 1.096.474,50, correspondente ao valor potencial de todos os registros.

**Filtro:** `estoque = TRUE` para o valor disponível. O total utilizado como referência considera todos os registros.

**Unidade de medida:** reais (R$) e percentual (%).

**Resultado: R$ 769.784,00**

Esse valor representa 70,21% do valor potencial total da base.

R$ 769.784,00 ÷ R$ 1.096.474,50 × 100 = 70,21%.

**Interpretação gerencial:** os 48 registros disponíveis representam R$ 769.784,00 em valor potencial, considerando os preços cadastrados.

O indicador pode apoiar a definição de prioridades comerciais e o planejamento da comercialização dos itens de maior valor.

---

### KPI 04 — Valor médio ponderado por unidade

**Objetivo:** identificar quanto vale, em média, cada unidade armazenada, considerando o peso das quantidades de cada produto.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `quantidade`
- `valor_unitario`

**Regra de cálculo:**

Soma do valor potencial de todos os registros dividida pela soma de todas as quantidades.

**Numerador:** R$ 1.096.474,50, correspondente ao valor potencial total.

**Denominador:** 7.387 unidades totais.

**Filtro:** nenhum. São considerados todos os registros.

**Unidade de medida:** reais por unidade (R$/unidade).

**Resultado: R$ 148,43 por unidade**

R$ 1.096.474,50 ÷ 7.387 = R$ 148,43.

**Interpretação gerencial:** o indicador demonstra o valor médio ponderado das unidades armazenadas.

Minerais apresentam volumes elevados e valores unitários relativamente baixos, enquanto defensivos possuem valores unitários maiores e quantidades menores.

A análise permite separar decisões logísticas, relacionadas ao volume físico, de decisões comerciais, relacionadas ao valor dos produtos.

---

### KPI 05 — Concentração por fornecedor

**Objetivo:** medir a participação de cada fornecedor no total de registros e identificar possíveis concentrações de fornecimento.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `fornecedor`
- `id`

**Regra de cálculo:**

Quantidade de registros de cada fornecedor dividida pelo total de registros da base, multiplicada por 100.

**Numerador:** 16 registros do fornecedor líder, Campo Verde.

**Denominador:** 68 registros totais.

**Filtro:** nenhum. Todos os fornecedores são considerados.

**Unidade de medida:** percentual (%) e quantidade de registros.

**Resultado: 23,53% — Campo Verde**

16 ÷ 68 × 100 = 23,53%.

Distribuição dos fornecedores:

| Fornecedor  | Registros | Participação |
| ----------- | --------: | -----------: |
| Campo Verde |        16 |       23,53% |
| NutriCampo  |        14 |       20,59% |
| AgroSul     |        11 |       16,18% |
| Solo Forte  |        10 |       14,71% |
| BioPlant    |         9 |       13,24% |
| AgroMax     |         8 |       11,76% |

**Interpretação gerencial:** Campo Verde apresenta a maior participação, com pouco menos de um quarto dos registros.

Apesar da liderança, a distribuição demonstra uma carteira relativamente equilibrada entre os fornecedores.

O indicador pode apoiar o monitoramento da dependência comercial e a diversificação de fornecedores.

Limite de alerta sugerido pelo grupo: fornecedores acima de 30% dos registros.

---

### KPI 06 — Valor potencial por categoria

**Objetivo:** identificar em quais categorias está concentrado o valor potencial do estoque disponível.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `categoria`
- `quantidade`
- `valor_unitario`
- `estoque`

**Regra de cálculo:**

Valor potencial de cada categoria dividido pelo valor potencial total dos registros disponíveis, multiplicado por 100.

**Numerador:** R$ 338.018,00, correspondente à categoria Grãos.

**Denominador:** R$ 769.784,00, correspondente ao valor potencial total disponível.

**Filtro:** `estoque = TRUE`.

**Unidade de medida:** reais (R$) e percentual (%).

**Resultado: 43,91% — Grãos**

R$ 338.018,00 ÷ R$ 769.784,00 × 100 = 43,91%.

Distribuição do valor potencial disponível:

| Categoria     | Valor potencial | Participação |
| ------------- | --------------: | -----------: |
| Grãos         |   R$ 338.018,00 |       43,91% |
| Fertilizantes |   R$ 120.755,00 |       15,69% |
| Defensivos    |   R$ 101.155,00 |       13,14% |
| Rações        |    R$ 92.089,20 |       11,96% |
| Minerais      |    R$ 77.968,00 |       10,13% |
| Sementes      |    R$ 39.798,80 |        5,17% |

**Interpretação gerencial:** Grãos concentra mais de 40% do valor potencial disponível, demonstrando sua relevância para a operação.

O resultado pode orientar prioridades de gestão de estoque e estratégias comerciais, considerando principalmente as categorias que concentram maior valor.

---

### KPI 07 — Distribuição de unidades por estado

**Objetivo:** comparar os estados pelo volume físico armazenado, apoiando decisões de logística e distribuição.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `estado`
- `quantidade`

**Regra de cálculo:**

Soma das quantidades de cada estado dividida pela soma das quantidades de todos os estados, multiplicada por 100.

**Numerador:** 2.107 unidades no Paraná (PR).

**Denominador:** 7.387 unidades totais.

**Filtro:** nenhum. Todos os registros são considerados.

**Unidade de medida:** unidades físicas e percentual (%).

**Resultado: 28,52% — Paraná (PR)**

2.107 ÷ 7.387 × 100 = 28,52%.

Distribuição das unidades:

| Estado | Unidades | Participação |
| ------ | -------: | -----------: |
| PR     |    2.107 |       28,52% |
| MT     |    1.561 |       21,13% |
| MS     |    1.227 |       16,61% |
| GO     |      899 |       12,17% |
| SC     |      614 |        8,31% |
| RS     |      415 |        5,62% |
| MG     |      320 |        4,33% |
| SP     |      244 |        3,30% |

**Interpretação gerencial:** Paraná e Mato Grosso concentram aproximadamente 49,65% das unidades armazenadas.

Essa concentração pode orientar o planejamento de capacidade de armazenamento, transporte e distribuição, considerando especialmente os estados com maior volume físico.

---

### KPI 08 — Taxa de indisponibilidade por categoria

**Objetivo:** identificar as categorias com maior proporção de registros indisponíveis.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `categoria`
- `estoque`
- `id`

**Regra de cálculo:**

Quantidade de registros com `estoque = FALSE` em cada categoria dividida pelo total de registros daquela categoria, multiplicada por 100.

**Numerador:** 7 registros indisponíveis na categoria Defensivos.

**Denominador:** 14 registros totais na categoria Defensivos.

**Filtro:** nenhum. São considerados todos os registros de cada categoria.

**Unidade de medida:** percentual (%) e quantidade de registros.

**Resultado: 50,00% — Defensivos**

7 ÷ 14 × 100 = 50,00%.

Distribuição das taxas de indisponibilidade:

| Categoria     | Indisponíveis | Total |   Taxa |
| ------------- | ------------: | ----: | -----: |
| Defensivos    |             7 |    14 | 50,00% |
| Sementes      |             4 |     9 | 44,44% |
| Fertilizantes |             3 |     9 | 33,33% |
| Grãos         |             3 |    16 | 18,75% |
| Rações        |             2 |    13 | 15,38% |
| Minerais      |             1 |     7 | 14,29% |

**Interpretação gerencial:** metade dos registros de Defensivos está indisponível, em parte devido ao bloqueio comercial aplicado aos produtos com valor unitário superior a R$ 800,00.

Sementes também apresenta uma taxa elevada, de 44,44%.

O indicador permite priorizar a investigação dos motivos de bloqueio e avaliar estratégias de reposição e disponibilidade comercial.

---

### KPI 09 — Volume em grandes registros

**Objetivo:** avaliar se o volume físico está concentrado em poucos registros com grandes quantidades.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `quantidade`
- `id`

**Regra de cálculo:**

São considerados grandes registros aqueles cuja quantidade é superior a 200 unidades.

O indicador calcula duas participações:

1. Quantidade de registros com mais de 200 unidades dividida pelo total de registros, multiplicada por 100.
2. Soma das unidades desses registros dividida pelo total de unidades, multiplicada por 100.

**Numeradores:**

- 11 registros grandes.
- 3.030 unidades nesses registros.

**Denominadores:**

- 68 registros totais.
- 7.387 unidades totais.

**Filtro:** `quantidade > 200`.

**Unidade de medida:** percentual (%), registros e unidades físicas.

**Resultados:**

- 16,18% dos registros possuem mais de 200 unidades.
- 41,02% das unidades estão concentradas nesses registros.

Cálculos:

11 ÷ 68 × 100 = 16,18%.

3.030 ÷ 7.387 × 100 = 41,02%.

**Interpretação gerencial:** apenas 16,18% dos registros concentram 41,02% de todo o volume físico armazenado.

Os 11 registros identificados estão associados principalmente a calcários, soja e milho.

O resultado demonstra a importância de planejar a armazenagem e o transporte considerando os produtos de maior volume.

O limite de 200 unidades foi definido como critério de análise pelo grupo, não como uma regra operacional oficial da empresa.

---

### KPI 10 — Indisponibilidade por fornecedor

**Objetivo:** identificar os fornecedores cujos produtos apresentam as maiores taxas de indisponibilidade.

**Tabela e colunas utilizadas:**

- `tb_vendas_agro`
- `fornecedor`
- `estoque`
- `id`

**Regra de cálculo:**

Quantidade de registros de cada fornecedor com `estoque = FALSE` dividida pelo total de registros daquele fornecedor, multiplicada por 100.

**Numerador:** 5 registros indisponíveis do fornecedor AgroMax.

**Denominador:** 8 registros totais do fornecedor AgroMax.

**Filtro:** nenhum. Todos os fornecedores são considerados.

**Unidade de medida:** percentual (%) e quantidade de registros.

**Resultado: 62,50% — AgroMax**

5 ÷ 8 × 100 = 62,50%.

Distribuição das taxas de indisponibilidade:

| Fornecedor  | Indisponíveis | Total |   Taxa |
| ----------- | ------------: | ----: | -----: |
| AgroMax     |             5 |     8 | 62,50% |
| BioPlant    |             4 |     9 | 44,44% |
| AgroSul     |             3 |    11 | 27,27% |
| Campo Verde |             4 |    16 | 25,00% |
| Solo Forte  |             2 |    10 | 20,00% |
| NutriCampo  |             2 |    14 | 14,29% |

**Interpretação gerencial:** AgroMax apresenta a maior taxa de indisponibilidade, com 62,50% dos seus registros bloqueados.

BioPlant aparece em seguida, com 44,44%.

O indicador permite identificar fornecedores que demandam maior atenção em relação à disponibilidade dos produtos e às condições comerciais.

A empresa pode utilizar essas informações para revisar o abastecimento, investigar os motivos dos bloqueios e avaliar novos pedidos.

---

## 6. Conclusão gerencial

A análise da base tratada permitiu identificar pontos importantes sobre a disponibilidade comercial, a concentração do valor potencial do estoque e a distribuição dos produtos.

Entre os dez indicadores desenvolvidos, três se destacam pela relevância gerencial:

### 1. KPI 01 — Taxa de disponibilidade de registros

**Resultado: 70,59%**

Esse indicador representa a situação geral do catálogo. Dos 68 registros, 20 estão indisponíveis.

Sua importância está na possibilidade de acompanhar a capacidade comercial da empresa e identificar a necessidade de ações para aumentar a disponibilidade dos produtos.

**Decisão apoiada:** priorizar a liberação, reposição ou revisão dos produtos indisponíveis, buscando melhorar a disponibilidade geral do catálogo.

### 2. KPI 08 — Taxa de indisponibilidade por categoria

**Resultado: 50,00% em Defensivos.**

O indicador demonstra que metade dos registros da categoria Defensivos está indisponível. Sementes também apresenta uma situação relevante, com 44,44% de indisponibilidade.

Esse resultado permite identificar quais categorias necessitam de maior atenção, evitando que bloqueios comprometam a disponibilidade comercial.

**Decisão apoiada:** revisar os critérios de bloqueio comercial e priorizar ações de disponibilidade nas categorias de Defensivos e Sementes.

### 3. KPI 06 — Valor potencial por categoria

**Resultado: 43,91% em Grãos.**

O indicador demonstra que Grãos concentra a maior parcela do valor potencial do estoque disponível, representando R$ 338.018,00.

Essa concentração torna a categoria especialmente relevante para o planejamento comercial e para o acompanhamento do estoque.

**Decisão apoiada:** priorizar o acompanhamento comercial e a gestão de estoque da categoria Grãos, considerando sua participação no valor potencial disponível.

### Recomendações finais

Com base nos resultados, são propostas quatro ações gerenciais:

1. **Reabastecer e revisar Defensivos e Sementes:** investigar as causas das maiores taxas de indisponibilidade e reavaliar os critérios de bloqueio comercial.
2. **Revisar o abastecimento com AgroMax e BioPlant:** investigar os motivos pelos quais esses fornecedores apresentam as maiores taxas de indisponibilidade.
3. **Focar a gestão comercial e logística em Grãos, PR e MT:** considerar a concentração de valor potencial em Grãos e a concentração de unidades nos estados do Paraná e Mato Grosso.
4. **Manter a governança de dados:** implementar validações na entrada de dados para evitar novos erros de nomenclatura, datas, quantidades e valores.

**Conclusão:** o projeto demonstra como a utilização de SQL para limpeza, padronização e análise de dados pode transformar registros inconsistentes em informações úteis para a tomada de decisão. Os dez KPIs desenvolvidos oferecem uma visão estruturada da operação da AgroTech Solutions, permitindo identificar problemas de disponibilidade, concentrações comerciais e oportunidades de melhoria na gestão de estoque e fornecedores.

---

## 7. Tecnologias utilizadas

- **MySQL:** criação, armazenamento, tratamento e consulta dos dados.
- **MySQL Workbench:** ambiente de desenvolvimento e execução dos comandos SQL.
- **SQL:** manipulação, padronização, agregação e análise dos dados.
- **PowerPoint:** elaboração da apresentação dos resultados e indicadores gerenciais.

---

## 8. Estrutura do repositório

```text
analise_de_dados_agrotech/
│
├── README.md
│
├── AgroTech.sql
│
└── Banco de Dados II - KPI's.pptx
```

---

## 9. Apresentação

A apresentação reúne os principais resultados do tratamento dos dados, os dez KPIs desenvolvidos e as recomendações gerenciais elaboradas a partir das consultas SQL.

O arquivo está disponível no repositório:

**[Banco de Dados II - KPI's.pptx](./Banco%20de%20Dados%20II%20-%20KPI's.pptx)**

---

**AgroTech Solutions — Auditoria de Dados e Indicadores Gerenciais**

Projeto acadêmico desenvolvido para a disciplina de Banco de Dados II.
