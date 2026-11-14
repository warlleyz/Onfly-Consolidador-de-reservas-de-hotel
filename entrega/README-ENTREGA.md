# Relatório de Entrega — Desafio Técnico Onfly

## 1. Objetivo

O objetivo deste desafio foi identificar e corrigir defeitos em uma aplicação Python responsável por consolidar reservas de hotéis a partir de arquivos CSV e gerar relatórios em formato JSON.

As correções foram realizadas preservando a estrutura do projeto, as regras de negócio e os testes automatizados fornecidos.

## 2. Defeitos identificados e corrigidos

### 2.1. Cálculo de diárias

**Arquivo:** `src/onfly_report/services/stay_calculator.py`

**Problema:** o cálculo apresentava valores incorretos quando uma reserva atravessava meses diferentes.

**Causa raiz:** a implementação subtraía apenas os dias do mês, ignorando mês e ano.

**Correção:** utilização da diferença entre as datas completas, multiplicada pela quantidade de quartos.

### 2.2. Conversão de moedas

**Arquivo:** `src/onfly_report/services/currency_converter.py`

**Problema:** os valores convertidos para reais estavam incorretos.

**Causa raiz:** a implementação dividia o valor pela taxa de câmbio e realizava conversões desnecessárias para `float`.

**Correção:** multiplicação direta entre os valores `Decimal`, preservando a precisão monetária.

### 2.3. Regras de status

**Arquivo:** `src/onfly_report/services/status_policy.py`

**Problema:** reservas com status `NO_SHOW` eram incluídas nos totais financeiros.

**Causa raiz:** a condição excluía somente reservas `CANCELLED`.

**Correção:** a função passou a considerar faturáveis apenas reservas com status `CONFIRMED`.

### 2.4. Ranking de hotéis

**Arquivo:** `src/onfly_report/services/report_builder.py`

**Problema:** o ranking apresentava os hotéis com menor faturamento primeiro.

**Causa raiz:** a ordenação utilizava o faturamento em ordem crescente.

**Correção:** aplicação de ordenação decrescente por faturamento, mantendo o identificador do hotel em ordem crescente como critério de desempate.

## 3. Investigação e abordagem

Inicialmente, configurei um ambiente virtual Python e instalei as dependências do projeto.

Executei os testes automatizados com `pytest` e identifiquei cinco testes falhando.

A investigação foi realizada analisando as mensagens de erro, os resultados esperados e as funções responsáveis por cada comportamento.

As correções foram feitas individualmente, validando cada alteração com seu teste específico antes de executar novamente a suíte completa.

Após corrigir o cálculo de diárias, a conversão de moedas, as regras de status e o ranking de hotéis, os cinco testes passaram. O teste de regressão também foi resolvido pelas correções realizadas.

Durante o desenvolvimento, utilizei programação em conjunto com auxílio de IA para analisar erros, discutir soluções e compreender o comportamento do código. As alterações foram aplicadas e testadas localmente por mim.

## 4. Como executar e validar

### Preparação do ambiente

O projeto exige Python 3.11 ou superior.

```bash
python -m venv .venv
source .venv/Scripts/activate
python -m pip install -r requirements-dev.txt
python -m pip install -e .
```

O comando de ativação apresentado é para Git Bash no Windows.

### Executar os testes automatizados

```bash
python -m pytest -v
```

**Resultado obtido:** 5 testes aprovados, 0 falhas.

### Verificar a qualidade do código

```bash
python -m ruff check .
```

**Resultado obtido:** `All checks passed!`

### Gerar o relatório completo

```bash
python -m onfly_report \
  --input data/reservas_hoteis.csv \
  --rates data/taxas_cambio.csv \
  --output output/report.json \
  --top 5
```

**Resultado obtido:**

- Reservas: 200
- Confirmadas: 153
- Diárias: 909
- Total BRL: R$ 249.543,79

### Validar com a amostra fornecida

```bash
python -m onfly_report \
  --input data/exemplo-validacao.csv \
  --rates data/taxas_cambio.csv \
  --output output/exemplo.json
```

Os resultados obtidos foram:

| Indicador | Resultado |
|---|---:|
| Total de reservas | 12 |
| Confirmadas | 9 |
| Canceladas | 2 |
| No-show | 1 |
| Diárias | 28 |
| Faturamento em BRL | 7566.00 |
| Média por diária em BRL | 270.21 |

Os valores, a distribuição mensal e o ranking de hotéis foram comparados com a referência do desafio e corresponderam aos resultados esperados.

## 5. Considerações finais

Os quatro defeitos identificados foram corrigidos sem modificar os testes fornecidos, os dados originais ou a interface de linha de comando.

A aplicação passou nos cinco testes automatizados e na análise estática com Ruff. A execução com os arquivos CSV fornecidos também foi realizada com sucesso.

O desenvolvimento foi acompanhado de commits específicos para cada correção, facilitando o histórico e a rastreabilidade das alterações.