## Parte I
### A. Integridade de Dados
> O que significa garantir a integridade dos dados em um banco de dados?

    Um BD guarda uma representação do mundo real, e essa representação só será válida se for coerente com o que representa. Sendo assim, a integridade é o conjunto de regras que vai definir o que é "coerente", ou seja, garante que o BD não se torne um "arquvi qualquer" com dados que ninguém pode confiar.

### B. Integridade de Entidade
- O que ela procura garantir?
- Qual é a relação entre integridade de entidade e chave primária (PK)?
- Uma chave primária pode possuir valores repetidos?
- Uma chave primária pode ser nula?
- O que poderia acontecer se essas regras não fossem respeitadas?
---

    A *Integridade de Entidade* busca garantir que cada linha do BD é identificável de forma única e completa. Dessa forma, não temos espaço para ambiguidade. 

    A chave primária (PK) é o que identifica cada linha em um BD, um valor que se distingue de todos os outros, e a *Integridade de Entidade* é a regra que a protege garantindo que nenhum valor seja **nulo** e que seja **distinto** em cada linha. Caso essas regras não sejam respeitadas, o BD perde a capacidade de identificar os registros. 

    Disso decorrem vários problemas, como ambiguidade na identificação (registros iguais), operações no registro errado, e muitos outros problemas.
> Exemplo: A matricula `ra145107` pertence a dois alunos, então operações de consulta, lançar notas etc não da para saber quem é. Já Fulano, não pode nem ser consultado :/
```text
MATRICULA     NOME
─────────────────────────
ra145107      Ciclano
ra145107      Cintia
ra144325      Giovana
NULL          Fulano
```

### C. Integridade Referencial
- O que ela procura garantir?
- Qual é sua relação com a chave estrangeira (FK)?
- Uma FK pode referenciar um registro inexistente?
- O que significa um registro órfão?
- O que pode acontecer ao excluir um registro que está sendo referenciado por outra tabela?
---

    A Integridade Referencial garante a validade das referências entre as tabelas, e isto é feito por chaves estrangeiras (FK). 

    A regra não permite uma FK referenciar um registro inexistente, até por que não é possível referenciar um valor inexistente. Um **registro órfão** é um dado apontado para algo (tabela/linha) que não exise.

    Caso um registro que esta sendo referenciado seja excluido, o comportamento do BD vai depernder da regra de FK configurada (impedir a eclusão ou executar uma ação automática para proteger a integridade de dados). 
> Exemplo: O empréstimo 12 que referencia o livro `7` é um **regisro órfã**. Além disso, se tentassemos excluir o livro 1, o BD bloquearia a exclusão ou executaria uma ação automática, pois o empréstimo 10 o referencia.
### Tabela `LIVRO`

| idLivro(PK) | titulo         | 
|------------:|----------------|
| 1           | Noites Brancas |
| 2           | Crime e Castigo|

### Tabela `EMPRESTIMO`

| idEmprestimo(PK) | dataEmprestimo | idLivro(FK)|
|-----------------:|----------------|------------|
| 10               | 02/09/2026     |          1 |
| 11               | 03/09/2026     |          2 |
| 12               | 03/09/2026     |          7 |

### D. Integridade de Domínio
- O que significa o domínio de um atributo?
- Como podemos determinar quais valores são válidos?
- O tipo de dado ajuda a controlar o domínio?
- Existem restrições que vão além do tipo de dado?
--- 
    O domínio de um atributo é o seu conjunto de valores válidos, geralmente é especificado por um nome, tipo de dado ou um formato. Podemos determinar os valores válidos a partir do significado do atibuto no mundo real (ou seja, a partir das regras de negócio).

    O tipo de dado ajuda a controlar o domínio, mas muitas vezes é insuficiente sozinho (por ser amplo demais), mas além do tipo, temos restrições como CHECK, NOT NULL E DEFAULT, que garantem a validade dos valores.

Considere a tabela:

```text
PRODUTO
────────────────────
idProduto
nome
preco
estoque
```

Determine quais valores deveriam ou não ser permitidos para os atributos `preco` e `estoque`. Justifique sua resposta.

`preco`: 
- **Permitidos:** valores decimais e não negativos.
- **Não permitidos:** valores negativos, texto ou um valor nulo.
- **Just.:** O preço negativo ou nulo impedem a venda do produto.

`estoque`: 
- **Permitidos:** valores interios e não negativos.
- **Não permitidos:** valores negativos, decimais, texto ou um valor nulo.
- **Just.:** Representa uma contagem física, então precisa ser inteiro, o zero é valido pois representa um produto esgotado.

---

## Parte II

| Problema encontrado | Por que é um problema? | Tipo de integridade | Regra que deveria existir |
|----------------------|------------------------|---------------------|---------------------------|
| Ex.: Cliente 2 aparece duas vezes | Dois registros possuem o mesmo identificador | Entidade | `idCliente` não pode se repetir |
| Cliente 4 está sem nome | O nome é necessário para cadastrar o cliente. | Domínio | `nome` deve ser preenchido e não pode ser vazio. |
| Clientes 1 e 5 possuem o mesmo e-mail | Se o e-mail identifica cada cadastro, ele não deveria pertencer a dois clientes diferentes. | Domínio | `email` não pode se repetir. |
| Produto 11 tem o preço negativo | Não faz sentido um produto ter preço de venda negativo. | Domínio | `preco` deve ser maior ou igual a zero. |
| Produto 12 tem o estoque negativo | Não dá para ter uma quantidade física negativa de produtos. | Domínio | `estoque` deve ser inteiro e maior ou igual a zero. |
| Produto 13 está sem nome | Fica faltando uma informação básica para identificar o produto no cadastro. | Domínio | `nome` deve ser preenchido e não pode ser vazio. |
| Pedido 101 tem valor negativo| Um pedido comum de compra não deveria ter um valor total negativo. | Domínio | `valor` deve ser maior ou igual a zero. |
| Pedido 102 referencia o cliente 99 | O cliente 99 não existe na tabela `CLIENTE`, então o pedido fica sem uma referência válida. | Referencial | O `idCliente` do pedido precisa existir na tabela `CLIENTE`. |
| Pedido 103 está sem data | Não tem como saber quando o pedido foi feito. | Domínio | `data` deve ser preenchida com uma data válida. |

---

## Parte III

### Tabela `CLIENTE`

- **[E] R01** — Cada cliente deve ter um `idCliente` único e não nulo.
- **[D] R02** — O nome do cliente deve ser preenchido. (não pode aceitar valores nulos)
- **[D] R03** — Dois clientes não podem ter o mesmo e-mail.
- **[D] R04** — Todo cliente deve ter um e-mail preenchido e em um formato válido.

### Tabela `PRODUTO`

- **[E] R05** — Cada produto deve ter um `idProduto` único e não nulo.
- **[D] R06** — Todo produto precisa ter um nome preenchido.
- **[D] R07** — O preço precisa ser um número não negativo e não pode ser nulo.
- **[D] R08** — O estoque deve ser um número inteiro, não negativo e não nulo.
- **[D] R09** — Se nenhum estoque for informado ao cadastrar um produto, ele começa com zero.

### Tabela `PEDIDO`

- **[E] R10** — Cada pedido deve ter um `idPedido` único e não nulo.
- **[D] R11** — Todo pedido precisa ter uma data válida e preenchida.
- **[D] R12** — O valor do pedido deve ser numérico, não negativo e não nulo.
- **[R] R13** — Um pedido só pode fazer referência a um cliente que já exista na tabela `CLIENTE`.
- **[D] R14** — O `idCliente` do pedido não pode ser nulo, pois todo pedido precisa pertencer a algum cliente.
- **[R] R15** — Não deve ser possível excluir um cliente e deixar os pedidos dele sem uma referência válida.

---

## Parte IV 

### `PRIMARY KEY`

1. **O que procura garantir?** Que cada registro tenha um identificador único, sem valores repetidos ou nulos.
2. **Exemplo:** usar o `idCliente` como PK para não deixar cadastrar dois clientes com o mesmo identificador.

### `FOREIGN KEY`

1. **O que procura garantir?** Que as referências entre as tabelas sejam válidas.
2. **Exemplo:** impedir que um pedido seja cadastrado com o `idCliente = 99`, caso esse cliente não exista.

### `NOT NULL`

1. **O que procura garantir?** Que um campo não fique com valor nulo.
2. **Exemplo:** exigir que o nome do cliente seja informado no cadastro.

### `UNIQUE`

1. **O que procura garantir?** Que um valor, ou uma combinação de valores, não se repita onde isso não é permitido.
2. **Exemplo:** impedir que dois clientes tenham o mesmo e-mail.

### `CHECK`

1. **O que procura garantir?** Que um valor respeite alguma condição definida para o campo.
2. **Exemplo:** verificar se o preço de um produto é maior ou igual a zero, impedindo preços negativos.

### `DEFAULT`

1. **O que procura garantir?** Que um campo receba um valor padrão quando nenhum for informado.
2. **Exemplo:** fazer com que o estoque de um produto comece em zero, caso ninguém informe outra quantidade.

---

## Faça as conexões

| Necessidade | Mecanismo que você utilizaria |
|---|---|
| Identificar cada cliente de maneira única | `PRIMARY KEY` |
| Impedir um cliente sem nome | `NOT NULL` (e uma verificação para impedir texto vazio) |
| Impedir dois clientes com o mesmo e-mail | `UNIQUE` |
| Impedir preço negativo | `CHECK` |
| Garantir que um pedido pertença a um cliente existente | `FOREIGN KEY` (e `NOT NULL`, se o reg. for obrigatório) |
| Fazer um produto iniciar com estoque zero quando nenhum valor for informado | `DEFAULT` |

> **Existe relação entre os problemas de integridade estudados e os mecanismos encontrados durante sua pesquisa? Explique.**

Sim, os problemas que vimos nas tabelas são justamente situações que podem acontecer quando o BD não possui regras para controlar os dados, enquanto os mecanismos, servem para colocar essas regras em prática. Ou seja, as regras de integridade dizem **o que precisa ser respeitado**, e esses mecanismos ajudam o BD a garantir isso.

---

## REFERÊNCIAS
- Slides da matéria: [text](https://github.com/JoaoChoma/bd_1_2026)
- PostgreSQL Documentation: [text](https://www.postgresql.org/docs/current/)
