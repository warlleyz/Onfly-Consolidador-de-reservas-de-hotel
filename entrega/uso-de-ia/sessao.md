# Registro da sessão de programação conjunta — Desafio Onfly

**Data:** 14/11/2026  
**Ferramenta:** ChatGPT — OpenAI  
**Modelo:** GPT-6  
**Modalidade:** Pair Programming

**Nota:** Este documento é um registro consolidado das principais interações da sessão. Não substitui a exportação integral do histórico da conversa.

---

## 1. Definição da abordagem

**Desenvolvedor:**
> Tenho que fazer um teste para uma empresa.

**IA:**
> Posso ajudar a realizar o teste, analisando as questões e explicando as respostas.

**Desenvolvedor:**
> Vamos seguir o modelo de programacao conjunta. Você nao vai fazer tudo sozinho.

**IA:**
> Combinado. Vamos trabalhar no modelo de programação em conjunto (Pair Programming). Você conduz a implementação, eu analiso, oriento e explico as decisões. Vamos trabalhar uma etapa por vez.

Foi definido que o desenvolvedor conduziria a implementação e executaria os comandos, enquanto a IA auxiliaria na investigação, explicação e validação.

## 2. Preparação do ambiente

**Desenvolvedor:**
> Antes uma coisa, acho que preciso do python instalado, nao tenho.

**IA:**
> Vamos verificar a instalação com `python --version` e `python -m pip --version`.

**Desenvolvedor:**
> Instalei.

O desenvolvedor confirmou:

```text
Python 3.15.2
pip 26.2.2
```

O projeto foi organizado em um repositório GitHub:

`https://github.com/warlleyz/Onfly-Consolidador-de-reservas-de-hotel`

A IA orientou a verificação do `.gitignore`, a criação do commit inicial e o envio ao GitHub.

O desenvolvedor configurou um ambiente virtual, instalou as dependências e executou os testes automatizados.

## 3. Diagnóstico inicial

**IA:**
> Execute `python -m pytest -v` para identificar os testes que estão falhando.

**Desenvolvedor:**

```text
tests/test_currency.py FAILED
tests/test_nights.py FAILED
tests/test_regression_report.py FAILED
tests/test_status_rules.py FAILED
tests/test_top_hotels.py FAILED

5 failed
```

A IA sugeriu investigar os problemas individualmente, começando pelo cálculo de diárias.

## 4. Correção do cálculo de diárias

**Desenvolvedor:**

Apresentou a implementação original de `stay_calculator.py`:

```python
def calculate_room_nights(checkin: date, checkout: date, rooms: int) -> int:
    return (checkout.day - checkin.day) * rooms
```

**IA:**
> O atributo `.day` considera somente o dia do mês. Para calcular corretamente a diferença, precisamos subtrair as datas completas e utilizar `.days`.

**Desenvolvedor:**
> `return (checkout - checkin).days * rooms`

**IA:**
> Correto! Agora o cálculo considera a data completa, incluindo mudanças de mês e ano.

**Desenvolvedor:**
> Passou.

O teste `test_nights_across_month_boundary` foi aprovado.

## 5. Correção da conversão de moedas

**Desenvolvedor:**

Apresentou a implementação de `currency_converter.py`:

```python
def convert_to_brl(amount: Decimal, currency: str, rates: dict[str, Decimal]) -> Decimal:
    rate = rates[currency]
    return Decimal(str(float(amount) / float(rate)))
```

**IA:**
> Encontramos dois problemas: a divisão pela taxa de câmbio e o uso desnecessário de `float`, que pode introduzir imprecisões em cálculos financeiros.

**Desenvolvedor:**
> return amount * rate

**IA:**
> Correto!

**Desenvolvedor:**
> Passou.

O teste de conversão de moedas foi aprovado.

Após executar novamente a suíte completa, o desenvolvedor confirmou que o teste de regressão também passou.

Resultado intermediário:

```text
3 passed, 2 failed
```

## 6. Correção da política de status

**Desenvolvedor:**

Apresentou o código original de `status_policy.py`:

```python
def is_billable(status: str) -> bool:
    return status != "CANCELLED"
```

**IA:**
> A condição atual permite incluir reservas `NO_SHOW`. A regra do desafio determina que somente `CONFIRMED` deve entrar nos totais financeiros e de noites.

**Desenvolvedor:**
> `return status == "CONFIRMED"`

**IA:**
> Correto! Essa condição permite contabilizar somente as reservas confirmadas.

O desenvolvedor aplicou a alteração e executou novamente os testes.

Resultado:

```text
4 passed, 1 failed
```

## 7. Correção do ranking de hotéis

**Desenvolvedor:**

Compartilhou o código do arquivo `report_builder.py`.

A IA identificou a ordenação:

```python
ranked_hotels = sorted(
    hotels.items(),
    key=lambda item: (item[1]["amount"], item[0])
)
```

**IA:**
> A função `sorted()` utiliza ordem crescente por padrão. Precisamos ordenar o faturamento do maior para o menor, mantendo o identificador do hotel como critério de desempate.

**Desenvolvedor:**
> ranked_hotels = sorted(hotels.items(), key=lambda item: (-item[1]["amount"], item[0]))

**IA:**
> Correto

O último teste foi aprovado.

## 8. Validação dos testes

O desenvolvedor executou:

```bash
python -m pytest -v
```

Resultado informado:

```text
5 passed
```

Depois, executou:

```bash
python -m ruff check .
```

**Desenvolvedor:**

```text
All checks passed!
```

## 9. Validação com os dados reais

A IA orientou a execução:

```bash
python -m onfly_report \
  --input data/reservas_hoteis.csv \
  --rates data/taxas_cambio.csv \
  --output output/report.json \
  --top 5
```

**Desenvolvedor:**

```text
Reservas: 200 | Confirmadas: 153 | Noites: 909 | Total BRL: 249543.79
```

A execução terminou sem erros aparentes.

## 10. Validação da amostra oficial

A IA solicitou a execução:

```bash
python -m onfly_report \
  --input data/exemplo-validacao.csv \
  --rates data/taxas_cambio.csv \
  --output output/exemplo.json
```

**Desenvolvedor:**

```text
Reservas: 12 | Confirmadas: 9 | Noites: 28 | Total BRL: 7566.00
```

O desenvolvedor também apresentou o conteúdo de `output/exemplo.json`.

Os valores foram comparados com os resultados esperados no README do desafio:

| Indicador | Resultado |
|---|---:|
| Reservas | 12 |
| Confirmadas | 9 |
| Canceladas | 2 |
| No-show | 1 |
| Diárias | 28 |
| Total BRL | 7566.00 |
| Média por diária | 270.21 |

A distribuição mensal e o ranking de hotéis também corresponderam à referência fornecida.

## 11. Documentação de entrega

A IA auxiliou na estruturação dos arquivos exigidos pela empresa:

- `entrega/README-ENTREGA.md`
- `entrega/uso-de-ia/declaracao.md`
- `entrega/uso-de-ia/sessao.md`

O desenvolvedor revisou a declaração de transparência e propôs a seguinte redação:

**Desenvolvedor:**
> O desenvolvimento ocorreu de forma colaborativa. A IA forneceu explicações e sugestões, enquanto eu conduzi as correções, executei os comandos e validei os resultados localmente.

A redação foi aceita para a declaração.

## 12. Compatibilidade com Python 3.11

A IA orientou a instalação e configuração do Python 3.11, conforme os requisitos do desafio.

O desenvolvedor criou e ativou um ambiente virtual separado:

```bash
py -3.11 -m venv .venv311
source .venv311/Scripts/activate
```

**Desenvolvedor:**

```text
Python 3.11.9
(.venv311)
```

Após instalar as dependências e executar os testes:

**Desenvolvedor:**
> Tudo passou.

O desenvolvedor também atualizou o `.gitignore` para ignorar `.venv311/`.

## 13. Resultado da colaboração

Ao final da implementação e das validações relatadas:

- Quatro defeitos foram corrigidos.
- Os cinco testes automatizados passaram.
- O Ruff não identificou problemas.
- O CLI executou com os dados fornecidos.
- A amostra oficial apresentou os valores esperados.
- Os testes também passaram com Python 3.11.9.
- A documentação de entrega foi preparada com auxílio da IA.

A IA atuou na investigação dos erros, explicação dos conceitos, sugestão das correções e orientação da validação. O desenvolvedor aplicou as alterações, executou os comandos, conferiu os resultados e organizou o repositório.

---

**Fim do registro consolidado.**