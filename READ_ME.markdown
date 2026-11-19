# Sequencia de Fibonacci

Projeto final do Curso Introdutorio de Programacao de Baixo Nivel
Liga ProtoFPGA - UFSC Ararangua

**Dupla:** Bernardo de Souza Teixeira e Bernardo Cattoi Dahmer

## O que o programa faz

Le um numero N e imprime os N primeiros termos da sequencia de Fibonacci, em que cada termo e a soma dos dois anteriores (0, 1, 1, 2, 3, 5, 8...). Depois confere o ultimo termo com uma segunda versao, recursiva, e pergunta se a pessoa quer calcular de novo.

## Como executar

1. Abra `projeto_fibonacci.asm` no MARS.
2. Clique em Assemble e depois em Run.
3. No painel Run I/O, digite N (de 1 a 47) e aperte Enter. No fim, digite `1` para calcular de novo ou `0` para sair.

## Exemplo

Entrada: `10`

Saida:

```
Primeiros 10 termos: 0 1 1 2 3 5 8 13 21 34
Conferindo com a versao recursiva: fib(9) = 34
```

Com um valor fora do intervalo (por exemplo `0` ou `48`), o programa avisa e pede N de novo:

```
Valor invalido! N precisa estar entre 1 e 47.
```

## Como o codigo esta organizado

| Funcao | Recebe | Devolve | O que faz |
| -------- | ------------- | ------------- | --------- |
| `fibonacci` | `$a0` = n (quantidade de termos) | nada | Imprime os n primeiros termos com um laco (versao iterativa) |
| `imprime_termo` | `$a0` = valor | nada | Imprime o valor seguido de um espaco |
| `fib` | `$a0` = n | `$v0` = F(n) | Calcula o n-esimo termo de forma recursiva: fib(n) = fib(n-1) + fib(n-2) |

O `main` le N, valida a entrada, chama `fibonacci` e depois `fib(N-1)` para conferir o ultimo termo impresso.

Sobre a convencao de chamada:

- `fibonacci` chama `imprime_termo`, por isso guarda `$ra` na pilha. O contador e os dois termos (`a` e `b`) ficam em `$s0`, `$s1` e `$s2`, que sobrevivem ao `jal` e tambem sao salvos na pilha, porque o `main` guarda N em `$s0`.
- `fib` chama a si mesma duas vezes. Cada chamada guarda na pilha o `$ra`, o seu `n` (`$s0`) e o resultado de `fib(n-1)` (`$s1`), que precisa sobreviver a segunda chamada.
- `imprime_termo` nao chama ninguem, entao nao usa a pilha.

## Iterativa x recursiva

As duas versoes chegam ao mesmo resultado, mas o custo e muito diferente. A iterativa faz uma soma por termo: para N termos, N somas. A recursiva recalcula os mesmos termos varias vezes (fib(n-2) e calculado dentro de fib(n-1) e de novo fora dele), e o numero de chamadas cresce quase como o proprio Fibonacci: fib(24) ja faz cerca de 150 mil chamadas. Por isso a conferencia recursiva so roda para N ate 25; acima disso o MARS demoraria demais.

## Limitacoes

- N precisa estar entre 1 e 47. O termo F(47) = 2971215073 nao cabe num inteiro de 32 bits com sinal (o maior e 2147483647), entao o ultimo termo possivel e F(46) = 1836311903.
- O laco calcula sempre um termo a frente do que imprime. Por isso a soma usa `addu`: com `add`, o MARS geraria uma excecao de overflow nesse termo extra quando N = 47, mesmo sem imprimi-lo.
- A conferencia recursiva so e feita para N ate 25, por ser lenta.
- A entrada precisa ser um numero inteiro; se a pessoa digitar letras, o proprio MARS gera um erro.
