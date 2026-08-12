# Relatório de Preparação e Análise dos Dados de Acidentes Rodoviários — PRF 2025

> **Projeto:** Preparação de dados — Módulo 4  
> **Fonte analisada:** base de acidentes da PRF/DATATRAN 2025  
> **Notebook de origem:** `unudade_4_aponte_em_python1 (1).ipynb`

---

## 1. Visão geral

Este relatório apresenta os principais resultados obtidos no notebook de preparação e tratamento da base de acidentes rodoviários da PRF referente a 2025.

O trabalho teve como foco:

- organização da estrutura do projeto;
- leitura e validação da base de dados;
- inspeção das variáveis e dos tipos de dados;
- diagnóstico de valores ausentes e duplicidades;
- padronização e transformação das variáveis;
- criação de indicadores temporais e de gravidade;
- definição da variável-alvo `acidente_fatal`;
- verificação de possíveis variáveis com *data leakage*;
- construção das bases analítica e modelável;
- exportação das bases tratadas para utilização posterior.

---

## 2. Estrutura e preparação inicial

O notebook verificou/criou a seguinte estrutura de diretórios:

- `dados_brutos`
- `dados_tratados`
- `notebooks`
- `sql`
- `dashboards`
- `relatorios`
- `apresentacao`
- `logs`

A base foi carregada a partir do arquivo de acidentes da PRF/DATATRAN 2025, utilizando leitura com tratamento de *encoding*.

---

## 3. Caracterização da base original

A base inicial apresentou:

| Indicador | Resultado |
|---|---:|
| Registros | **72.529** |
| Colunas | **30** |
| Memória aproximada | **92,7 MB** |
| Tipos `int64` | 10 |
| Tipos `object` | 20 |
| Duplicidades exatas | **0** |

A estrutura inicial contém informações relacionadas a identificação do acidente, data e horário, localização, rodovia, município, causa, tipo de acidente, condições da via e do ambiente, quantidade de pessoas, mortos, feridos, veículos e coordenadas geográficas.

### Validação das colunas

As colunas esperadas foram verificadas e **nenhuma coluna obrigatória estava ausente**.

---

## 4. Distribuição dos acidentes por Unidade da Federação

A análise inicial mostrou concentração desigual dos registros entre as Unidades da Federação.

Entre os estados com maior quantidade de acidentes destacam-se:

1. **MG**
2. **SC**
3. **PR**
4. **RJ**
5. **RS**
6. **SP**
7. **BA**
8. **GO**
9. **PE**
10. **ES**

![Número de acidentes por UF](imagens/celula_6_resultado_2.png)

> A distribuição acima representa a quantidade de registros de acidentes por UF na base analisada. O resultado não deve ser interpretado isoladamente como uma taxa de risco, pois não considera exposição ao tráfego, extensão da malha ou volume de veículos.

---

## 5. Estatísticas de mortos e feridos

Para a variável `mortos`, foram observados:

| Estatística | Valor |
|---|---:|
| Registros | 72.529 |
| Média | 0,0833 |
| Desvio-padrão | 0,3387 |
| Mínimo | 0 |
| Mediana | 0 |
| 75º percentil | 0 |
| Máximo | 16 |

Para `feridos`:

| Estatística | Valor |
|---|---:|
| Registros | 72.529 |
| Média | 1,1520 |
| Desvio-padrão | 1,1437 |
| Mínimo | 0 |
| Mediana | 1 |
| 75º percentil | 1 |
| Máximo | 49 |

Os resultados indicam forte concentração de acidentes sem mortes: pelo menos 75% dos registros apresentam `mortos = 0`.

---

## 6. Qualidade dos dados

### Valores ausentes

O diagnóstico identificou poucos valores ausentes nas variáveis estruturais:

| Variável | Quantidade | Percentual |
|---|---:|---:|
| `uop` | 38 | 0,052% |
| `delegacia` | 22 | 0,030% |
| `regional` | 2 | 0,003% |
| `classificacao_acidente` | 1 | 0,001% |

Posteriormente, as variáveis categóricas e as variáveis numéricas de contagem relevantes foram tratadas, resultando em ausência de nulos nas variáveis principais utilizadas na preparação final.

### Duplicidades

A verificação encontrou:

**0 duplicidades exatas.**

---

## 7. Transformação temporal

A variável de data foi convertida para formato temporal e foram criadas variáveis derivadas para facilitar análises posteriores:

- `ano`
- `mes`
- `trimestre`
- `dia_semana_num`
- `fim_de_semana`

O horário também foi tratado e utilizado para criação de:

- `hora`
- `turno`
- `faixa_horaria`

As faixas horárias apresentaram a seguinte distribuição de registros:

| Faixa horária | Registros |
|---|---:|
| 00h–02h | 3.959 |
| 03h–05h | 4.948 |
| 06h–08h | 11.517 |
| 09h–11h | 9.342 |
| 12h–14h | 9.678 |
| 15h–17h | 12.624 |
| 18h–20h | 13.473 |
| 21h–23h | 6.988 |

A maior quantidade de registros ocorreu na faixa **18h–20h**, seguida por **15h–17h**.

---

## 8. Mortes por turno

A análise da quantidade total de mortos por turno indicou maior concentração no período classificado como **NOITE**, seguido por **TARDE**, **MADRUGADA** e **MANHÃ**.

![Número de mortos por turno](imagens/celula_20_resultado_2.png)

Esse resultado descreve a distribuição das mortes registradas segundo a classificação de turno utilizada no notebook.

---

## 9. Principais causas registradas

O ranking das causas de acidentes apresentou como principais categorias:

| Posição | Causa | Registros |
|---:|---|---:|
| 1 | Ausência de reação do condutor | 11.469 |
| 2 | Reação tardia ou ineficiente do condutor | 10.799 |
| 3 | Acessar a via sem observar a presença dos outros veículos | 7.097 |
| 4 | Condutor deixou de manter distância do veículo | 4.413 |
| 5 | Velocidade incompatível | 4.088 |
| 6 | Manobra de mudança de faixa | 4.016 |
| 7 | Ingestão de álcool pelo condutor | 3.685 |
| 8 | Demais falhas mecânicas ou elétricas | 3.385 |
| 9 | Transitar na contramão | 2.475 |
| 10 | Condutor dormindo | 2.116 |

As categorias relacionadas à reação e ao comportamento do condutor aparecem com destaque no ranking de frequência.

---

## 10. Acidentes fatais

Foi criada a variável-alvo:

`acidente_fatal = 1` quando `mortos >= 1`; caso contrário, `0`.

A distribuição obtida foi:

| Classificação | Registros | Percentual |
|---|---:|---:|
| Não fatal (`0`) | 67.319 | 92,82% |
| Fatal (`1`) | 5.210 | 7,18% |
| **Total** | **72.529** | **100%** |

![Distribuição da variável-alvo acidente_fatal](imagens/celula_32_resultado_1.png)

A taxa global de acidentes fatais calculada no notebook foi de aproximadamente **7,18%**.

A regra utilizada para criação do alvo foi validada e apresentou:

**0 violações da regra do alvo.**

---

## 11. Indicadores de gravidade

Foram criados indicadores derivados para apoiar análises futuras:

### `total_vitimas`

Calculado como:

`mortos + feridos_leves + feridos_graves`

### `indice_gravidade`

Calculado como:

`mortos × 3 + feridos_graves × 2 + feridos_leves`

Também foram criadas:

- `acidente_grave`
- `br_formatada`
- `chave_localidade`

Essas variáveis ampliam a capacidade de análise da base sem substituir as variáveis originais.

---

## 12. Tipos de acidentes com maior taxa de fatalidade

Entre as categorias avaliadas com quantidade mínima de registros, os maiores valores de taxa fatal encontrados foram:

| Tipo de acidente | Acidentes | Fatais | Taxa fatal |
|---|---:|---:|---:|
| Atropelamento de Pedestre | 3.057 | 902 | 30% |
| Colisão frontal | 4.739 | 1.396 | 29% |
| Colisão lateral sentido oposto | 2.152 | 212 | 10% |
| Eventos atípicos | 287 | 23 | 8% |
| Atropelamento de Animal | 1.133 | 68 | 6% |
| Saída de leito carroçável | 10.209 | 605 | 6% |
| Colisão com objeto | 5.109 | 297 | 6% |
| Capotamento | 1.373 | 63 | 5% |
| Colisão transversal | 9.306 | 427 | 5% |
| Colisão traseira | 14.360 | 619 | 4% |

**Atropelamento de pedestre** e **colisão frontal** apresentaram as maiores taxas fatais entre as categorias destacadas.

---

## 13. Indicadores consolidados

Após as transformações, a base analítica apresentou os seguintes indicadores:

| Indicador | Resultado |
|---|---:|
| Linhas | **72.529** |
| Colunas | **44** |
| Acidentes fatais | **5.210** |
| Taxa de acidentes fatais | **7,18%** |
| Total de mortos | **6.043** |
| Total de feridos | **83.550** |

---

## 14. Base analítica

A base analítica final possui:

**72.529 linhas × 44 colunas**

Além das variáveis originais, foram incorporadas variáveis derivadas para análise temporal, classificação de fatalidade, gravidade e localização.

Entre as variáveis adicionais estão:

- `ano`
- `mes`
- `trimestre`
- `dia_semana_num`
- `fim_de_semana`
- `hora`
- `turno`
- `faixa_horaria`
- `acidente_fatal`
- `total_vitimas`
- `acidente_grave`
- `indice_gravidade`
- `br_formatada`
- `chave_localidade`

---

## 15. Base modelável e prevenção de data leakage

Foi construída uma segunda base destinada à modelagem, com:

**72.529 linhas × 11 variáveis.**

O notebook realizou uma verificação específica de variáveis que poderiam causar *data leakage*.

A validação retornou:

> **OK — nenhuma variável proibida encontrada.**

Variáveis diretamente relacionadas ao desfecho, como `mortos`, `feridos_leves` e `feridos_graves`, foram tratadas como proibidas para entrada no modelo.

O processo também classificou as variáveis entre permitidas, duvidosas, proibidas e aquelas que necessitam de análise antes da modelagem.

---

## 16. Exportação dos dados tratados

As bases finais foram exportadas para:

```text
dados_tratados/base_analitica_prf_2025.csv
dados_tratados/base_modelavel_prf_2025.csv
```

Após a exportação, os arquivos foram reabertos para validação.

Resultados da validação:

| Base | Dimensão |
|---|---:|
| Analítica | 72.529 × 44 |
| Modelável | 72.529 × 11 |

---

## 17. Dicionário das principais variáveis criadas

| Variável | Descrição | Uso |
|---|---|---|
| `acidente_fatal` | 1 se `mortos >= 1`; 0 se `mortos = 0` | Alvo |
| `total_vitimas` | Mortos + feridos leves + feridos graves | Análise / dashboard |
| `indice_gravidade` | Mortos × 3 + feridos graves × 2 + feridos leves | Análise / dashboard |
| `br_formatada` | BR padronizada no formato BR-000 | Análise / modelagem |
| `chave_localidade` | UF + município + BR formatada | Análise / dashboard |

---

## 18. Conclusões

A preparação da base PRF 2025 resultou em uma estrutura consistente para análises exploratórias, construção de indicadores e preparação para modelagem.

Os principais resultados do processamento foram:

1. A base inicial possui **72.529 registros e 30 variáveis**.
2. Não foram encontradas **duplicidades exatas**.
3. Os valores ausentes identificados nas variáveis estruturais foram tratados durante o processo de preparação.
4. Foram criadas variáveis temporais para ampliar a capacidade de análise.
5. Foram criados indicadores específicos de fatalidade e gravidade.
6. Foram identificados **5.210 acidentes fatais**, correspondentes a **7,18%** da base.
7. A base registra **6.043 mortos** e **83.550 feridos**.
8. **Atropelamento de pedestre** e **colisão frontal** apresentaram as maiores taxas fatais entre os tipos de acidente destacados.
9. As causas mais frequentes estão fortemente representadas por eventos relacionados à reação e comportamento do condutor.
10. Foi construída uma base analítica com **44 colunas** e uma base modelável com **11 variáveis**.
11. A verificação de *data leakage* não encontrou variáveis proibidas na base modelável final.
12. As bases tratadas foram exportadas e reabertas para validação.

---

## 19. Arquivos gerados pelo projeto

```text
dados_tratados/
├── base_analitica_prf_2025.csv
└── base_modelavel_prf_2025.csv
```

O notebook também registrou arquivos de documentação e apoio, incluindo decisões de tratamento e `README` do projeto.

---

## 20. Observação metodológica

Este relatório foi elaborado exclusivamente a partir dos procedimentos e resultados registrados no notebook analisado. As conclusões representam os resultados observados na base processada e não devem ser interpretadas como medidas de risco populacional ou causalidade sem análises adicionais.

---

## 21. Status do processamento

**Status: concluído**

- [x] Estrutura do projeto
- [x] Leitura da base
- [x] Validação das colunas
- [x] Diagnóstico de qualidade
- [x] Tratamento dos dados
- [x] Criação das variáveis derivadas
- [x] Definição do alvo
- [x] Validação do alvo
- [x] Verificação de *data leakage*
- [x] Construção da base analítica
- [x] Construção da base modelável
- [x] Exportação das bases
- [x] Validação dos arquivos exportados

---

**Relatório gerado a partir do notebook de preparação dos dados PRF 2025.**
