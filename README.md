# Office

**Um escritório em pixel art dentro do Discord.** Você anda por um mapa 7×7 de emojis, contrata funcionários que geram dinheiro por segundo, bate o ponto pra receber e, pela porta, entra na sua casa pra decorar com móveis comprados na loja.

[Outros projetos do Seaka](https://github.com/seaka-bot)

> [!WARNING]
> **Código de acervo.** Foi escrito pra uma versão antiga do BDFD e não é mantido. Depende de um `saldo` que vinha do sistema de economia do bot (veja [o que não está aqui](#o-que-não-está-aqui)).

## Arquivos

| Arquivo | Gatilho no BDFD | O que faz |
|---|---|---|
| `prefix/office.bdscript2` | `$alwaysExecute` | Comando `office` (também `escritório` e `escritorio`). Confere o prefixo sozinho e desenha o escritório. |
| `prefix/loja.bdscript2` | `$alwaysExecute` | Comando `loja` (também `lojinha`). Mostra o catálogo de móveis. |
| `oninteraction/office.bdscript2` | `$onInteraction` | Andar pelo escritório. Mostra o botão **interação** quando você para em algo. |
| `oninteraction/Interactions.bdscript2` | `$onInteraction` | O que cada interação faz: contratar, falar com funcionário, bater o ponto e a porta da casa. |
| `oninteraction/house.bdscript2` | `$onInteraction` | Andar pela casa. |
| `oninteraction/editHouse.bdscript2` | `$onInteraction` | Modo de edição da casa e o inventário de móveis. |
| `oninteraction/editPlaceMapHouse.bdscript2` | `$onInteraction` | Mover, colocar e guardar um móvel. |
| `oninteraction/loja.bdscript2` | `$onInteraction` | Navegar pela loja e comprar. |

Todos são **BDScript 2**.

## Variáveis

| Variável | Padrão | Usada como | Pra que serve |
|---|---|---|---|
| `Prefixo` | `!` *(ou o seu)* | servidor | Prefixo dos comandos |
| `MapaAuto` | *(veja abaixo)* | usuário | O escritório |
| `MapaCasa` | *(veja abaixo)* | usuário | A casa |
| `FuncAuto` | *(veja abaixo)* | usuário **e** global | Os 8 funcionários |
| `ItensCasa` | *(veja abaixo)* | global | O catálogo de móveis da loja |
| `InvCasa` | *(vazio)* | usuário | O inventário de móveis do jogador |
| `VzAuto` | `0` | usuário | Quanto o jogador ganha por segundo, somando todos os funcionários |
| `TimeAuto` | `0` | usuário | Quando o ponto foi batido. `0` = ponto fechado. |
| `saldo` | `[PREENCHER]` | usuário | O dinheiro do jogador. O primeiro funcionário custa 10.000; pra testar, use um padrão maior que isso. |

### `MapaAuto`: o escritório

```
8-4-4-5-4-4-4-0-1-1-0-6-6-0-0-1-1-0-6-6-0-0-0-0-0-0-0-0-0-7-7-0-1-1-0-0-0-0-0-1-1-0-3-0-0-0-0-0-0
```

São 49 números (7×7) separados por `-`. Montado em grade:

```
8 4 4 5 4 4 4
0 1 1 0 6 6 0
0 1 1 0 6 6 0
0 0 0 0 0 0 0
0 7 7 0 1 1 0
0 0 0 0 1 1 0
3 0 0 0 0 0 0
```

| Número | O que é |
|---|---|
| `0` | chão livre |
| `1` | mesa vazia → **interação:** contratar |
| `2` | mesa com funcionário → **interação:** falar e atualizar (aparece depois de contratar) |
| `3` | ponto → **interação:** bater o ponto e receber |
| `4` a `7` | bloqueiam a passagem (paredes e móveis) |
| `8` | porta → **interação:** entrar na casa |

O jogador começa na posição 8, logo abaixo da porta. As mesas nas posições 9, 10, 16, 17, 33, 34, 40 e 41 são dos funcionários 1 a 8, nessa ordem.

### `MapaCasa`: a casa

```
0-4-4-4-4-4-4-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0
```

```
0 4 4 4 4 4 4
0 0 0 0 0 0 0
0 0 0 0 0 0 0
0 0 0 0 0 0 0
0 0 0 0 0 0 0
0 0 0 0 0 0 0
0 0 0 0 0 0 0
```

`0` é chão livre e `4` é parede. Quando você coloca um móvel, o `0` daquele quadrado vira o ID do móvel (`11` a `17`).

### `FuncAuto`: os funcionários

```
0-1-10000-Robso/0-2-30000-Mayara/0-3-90000-Joao/0-10-100000000-Hudson/0-20-200000000-Fredricksen/0-200-15000000000-Anna/0-400-150000000000-Paola/0-1000-500000000000000-Giovanna
```

Cada funcionário é `nível-rendimento-preço-nome`, separados por `/`:

| Mesa | Funcionário | Rende (p/s) | Preço |
|---|---|---|---|
| 1 | Robso | 1 | 10.000 |
| 2 | Mayara | 2 | 30.000 |
| 3 | Joao | 3 | 90.000 |
| 4 | Hudson | 10 | 100.000.000 |
| 5 | Fredricksen | 20 | 200.000.000 |
| 6 | Anna | 200 | 15.000.000.000 |
| 7 | Paola | 400 | 150.000.000.000 |
| 8 | Giovanna | 1000 | 500.000.000.000.000 |

A mesma variável é usada de dois jeitos:
- **Do jogador:** guarda o nível, o rendimento e o preço atuais de cada funcionário dele.
- **Global (o valor padrão):** é a tabela base. A cada atualização, o funcionário ganha **mais um rendimento base**, e o preço da próxima é multiplicado por **1,7**.

### `ItensCasa`: o catálogo da loja

```
11-cadeira-100-🪑/12-poltrona-200-💺/13-mesa-250-🍽️/14-guarda_roupa-200-🚪/15-árvore-500-🌳/16-palmeira-1000-🌴/17-lorax-9999999999999-🌲
```

Cada móvel é `id-nome-preço-emoji`, separados por `/`. O nome não pode ter `-`, por isso `guarda_roupa`. Os emojis aqui são padrão do Discord; troque pelos seus, se quiser.

O inventário (`InvCasa`) começa vazio e guarda `quantidade-id-nome-emoji/...`.

## Emojis do mapa

Os quadrados do mapa são emojis customizados do meu servidor, com o ID fixo no código. **Não vão aparecer no seu.** Suba os seus e troque o `<:nome:id>` nestas linhas, que se repetem no começo de cada arquivo:

```bdfd
$var[0;<:0_:...>]   ← chão
$var[1;<:1_:...>]   ← mesa vazia
$var[2;<:2_:...>]   ← mesa com funcionário
$var[3;<:3_:...>]   ← ponto
$var[4;<:4_:...>]   ← parede
...
$var[8;<:8_:...>]   ← porta
$var[u;<:u_:...>]   ← o jogador
```

Na casa, o chão é `<:10:...>` e a parede é `<:9_:...>`. No modo de edição, o jogador aparece como ❎.

## Permissões

O bot precisa de **Inserir links** (Embed Links) no canal.

## Como jogar

1. `office` abre o escritório. Ande com ⬅️ ⬆️ ⬇️ ➡️.
2. Parado numa mesa vazia, aperte **interação** → **comprar** pra contratar.
3. Parado numa mesa ocupada: veja o nível e **atualize** o funcionário.
4. No ponto: a primeira vez **começa a contar**; a segunda **paga** `segundos × rendimento` no seu saldo.
5. `loja` abre o catálogo. Escolha com ⬆️ ⬇️ e aperte **comprar**. O móvel vai pro seu inventário.
6. Na porta: **Entrar** leva pra casa. Lá, **editar casa** abre o inventário: escolha um móvel, posicione e aperte **colocar**. **Guardar** devolve o móvel pro inventário.

## Limitações conhecidas

- A loja confere se você tem saldo pro móvel, mas **não desconta o preço** depois da compra.

## O que não está aqui

- **O `saldo`**: vinha do sistema de economia do bot, junto com outros comandos.

## Técnicas usadas

- Laço gerado com `$eval`: [explicação geral](https://github.com/seaka-bot/.github/blob/main/TECNICAS.md#laço-com-eval)
- Mapa, funcionários e inventário em strings: [explicação geral](https://github.com/seaka-bot/.github/blob/main/TECNICAS.md#string-como-banco-de-dados)
- Estado no ID do botão (aqui separado por `_`): [explicação geral](https://github.com/seaka-bot/.github/blob/main/TECNICAS.md#estado-no-id-do-botão)

## Créditos

Por **hirozn** · [mnsa.com.br](https://mnsa.com.br)
