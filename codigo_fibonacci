###############################################################################
# Projeto final - Curso Introdutorio de Programacao de Baixo Nivel
# Liga ProtoFPGA - UFSC Ararangua
#
# Dupla: Bernardo de Souza Teixeira e Bernardo Cattoi Dahmer
# Tema:  2 - Sequencia de Fibonacci
#
# O que o programa faz:
#   Le um inteiro N (de 1 a 47) e imprime os N primeiros termos da sequencia
#   de Fibonacci (0 1 1 2 3 5 8 ...), em que cada termo e a soma dos dois
#   anteriores. Depois confere o ultimo termo com uma versao recursiva,
#   fib(n), e pergunta se a pessoa quer calcular de novo.
#
# Teste obrigatorio:
#   Entrada: 10
#   Saida:   Primeiros 10 termos: 0 1 1 2 3 5 8 13 21 34
#
# Funcoes:
#   fibonacci(n)       - imprime os n primeiros termos (versao iterativa)
#   imprime_termo(x)   - imprime um termo seguido de um espaco
#   fib(n)             - devolve o n-esimo termo (versao recursiva)
###############################################################################

        .data
msg_titulo:     .asciiz "=== Sequencia de Fibonacci ===\n"
msg_pede_n:     .asciiz "\nDigite N (de 1 a 47): "
msg_invalido:   .asciiz "Valor invalido! N precisa estar entre 1 e 47.\n"
msg_primeiros:  .asciiz "Primeiros "
msg_termos:     .asciiz " termos: "
msg_recursiva:  .asciiz "\nConferindo com a versao recursiva: fib("
msg_igual:      .asciiz ") = "
msg_lenta:      .asciiz "\n(Conferencia recursiva pulada: para N > 25 ela fica muito lenta.)"
msg_de_novo:    .asciiz "\nCalcular de novo? (1 = sim, 0 = nao): "
msg_fim:        .asciiz "Fim do programa.\n"
espaco:         .asciiz " "

        .text
        .globl main

###############################################################################
# main: le N, valida, chama fibonacci(n), confere com fib(n-1) e repete se
# a pessoa quiser.
# Registradores $s usados no main (precisam sobreviver aos jal):
#   $s0 = N
###############################################################################
main:
        # Imprime o titulo
        li   $v0, 4
        la   $a0, msg_titulo
        syscall

ler_n:
        # Pede N
        li   $v0, 4
        la   $a0, msg_pede_n
        syscall

        li   $v0, 5                 # servico 5: le inteiro
        syscall
        move $s0, $v0               # $s0 = N (sobrevive aos jal)

        # Valida: 1 <= N <= 47 (F(47) ja nao cabe em 32 bits com sinal)
        blt  $s0, 1, n_invalido
        bgt  $s0, 47, n_invalido
        j    n_valido

n_invalido:
        li   $v0, 4
        la   $a0, msg_invalido
        syscall
        j    ler_n                  # pede N de novo

n_valido:
        # Imprime "Primeiros N termos: "
        li   $v0, 4
        la   $a0, msg_primeiros
        syscall
        li   $v0, 1
        move $a0, $s0
        syscall
        li   $v0, 4
        la   $a0, msg_termos
        syscall

        # Chama fibonacci(N), que imprime os termos
        move $a0, $s0
        jal  fibonacci

        # Conferencia recursiva do ultimo termo, F(N-1), so se N <= 25
        bgt  $s0, 25, pula_recursiva

        li   $v0, 4
        la   $a0, msg_recursiva
        syscall
        li   $v0, 1
        addi $a0, $s0, -1           # imprime N-1
        syscall
        li   $v0, 4
        la   $a0, msg_igual
        syscall

        addi $a0, $s0, -1           # $a0 = N-1
        jal  fib                    # $v0 = fib(N-1)
        move $a0, $v0
        li   $v0, 1
        syscall
        j    perguntar

pula_recursiva:
        li   $v0, 4
        la   $a0, msg_lenta
        syscall

perguntar:
        # Pergunta se quer repetir
        li   $v0, 4
        la   $a0, msg_de_novo
        syscall
        li   $v0, 5
        syscall
        beq  $v0, 1, ler_n          # 1 = sim, volta a pedir N

        # Encerra
        li   $v0, 4
        la   $a0, msg_fim
        syscall
        li   $v0, 10                # servico 10: termina o programa
        syscall

###############################################################################
# fibonacci(n)
#   Recebe: $a0 = n (quantidade de termos, n >= 1)
#   Devolve: nada (so imprime)
#   Imprime os n primeiros termos, um apos o outro, separados por espaco.
#
#   Ideia (versao iterativa):
#       a = 0, b = 1
#       repete n vezes:
#           imprime a
#           t = a + b ; a = b ; b = t
#
#   Como chama imprime_termo, precisa guardar $ra na pilha. Os valores que
#   precisam sobreviver ao jal ficam em $s0-$s2, que tambem sao guardados
#   na pilha (o main usa $s0 e espera que ele continue igual).
#     $s0 = contador (quantos termos faltam)
#     $s1 = a (termo atual)
#     $s2 = b (proximo termo)
###############################################################################
fibonacci:
        # Prologo: abre espaco na pilha e salva $ra, $s0, $s1, $s2
        addi $sp, $sp, -16
        sw   $ra, 12($sp)
        sw   $s0, 8($sp)
        sw   $s1, 4($sp)
        sw   $s2, 0($sp)

        move $s0, $a0               # contador = n
        li   $s1, 0                 # a = F(0) = 0
        li   $s2, 1                 # b = F(1) = 1

fib_laco:
        beq  $s0, $zero, fib_fim    # acabaram os termos? sai do laco

        move $a0, $s1               # imprime_termo(a)
        jal  imprime_termo

        addu $t0, $s1, $s2          # t = a + b (addu: nao gera excecao se
                                    # o termo "a frente" passar de 32 bits)
        move $s1, $s2               # a = b
        move $s2, $t0               # b = t

        addi $s0, $s0, -1           # contador--
        j    fib_laco

fib_fim:
        # Epilogo: restaura os registradores e volta
        lw   $s2, 0($sp)
        lw   $s1, 4($sp)
        lw   $s0, 8($sp)
        lw   $ra, 12($sp)
        addi $sp, $sp, 16
        jr   $ra

###############################################################################
# imprime_termo(x)
#   Recebe: $a0 = valor a imprimir
#   Devolve: nada
#   Imprime o valor e um espaco. Nao chama outra funcao, entao nao precisa
#   salvar $ra; usa so $a0 e $v0.
###############################################################################
imprime_termo:
        li   $v0, 1                 # servico 1: imprime inteiro ($a0)
        syscall

        li   $v0, 4                 # servico 4: imprime texto
        la   $a0, espaco
        syscall
        jr   $ra

###############################################################################
# fib(n) - versao recursiva
#   Recebe: $a0 = n (n >= 0)
#   Devolve: $v0 = F(n)
#     fib(0) = 0
#     fib(1) = 1
#     fib(n) = fib(n-1) + fib(n-2)
#
#   Cada chamada guarda na pilha:
#     $ra - para saber para onde voltar
#     $s0 - o n desta chamada (precisa dele depois da 1a chamada)
#     $s1 - o resultado de fib(n-1) (precisa sobreviver a 2a chamada)
###############################################################################
fib:
        # Caso base: se n < 2, devolve o proprio n
        slti $t0, $a0, 2
        beq  $t0, $zero, fib_recursivo
        move $v0, $a0
        jr   $ra

fib_recursivo:
        # Prologo
        addi $sp, $sp, -12
        sw   $ra, 8($sp)
        sw   $s0, 4($sp)
        sw   $s1, 0($sp)

        move $s0, $a0               # $s0 = n

        addi $a0, $s0, -1           # fib(n-1)
        jal  fib
        move $s1, $v0               # $s1 = fib(n-1)

        addi $a0, $s0, -2           # fib(n-2)
        jal  fib

        addu $v0, $s1, $v0          # $v0 = fib(n-1) + fib(n-2)

        # Epilogo
        lw   $s1, 0($sp)
        lw   $s0, 4($sp)
        lw   $ra, 8($sp)
        addi $sp, $sp, 12
        jr   $ra
