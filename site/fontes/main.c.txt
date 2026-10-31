/* VoeHeap - Embarque Aereo com Prioridade */
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include "heap.h"

#define LIMITE 30

long comparacoes = 0;
long trocas = 0;

Heap aviao;
int iniciado = 0;
int embarcados = 0;

int lerInt(char *msg, int min, int max)
{
    int x, r;
    while (1) {
        printf("%s", msg);
        r = scanf("%d", &x);
        if (r == EOF)
            exit(0);
        if (r != 1) {
            scanf("%*s");
            printf("Valor invalido!\n");
            continue;
        }
        if (x >= min && x <= max)
            return x;
        printf("Digite um valor entre %d e %d.\n", min, max);
    }
}

void geraVetor(Passageiro v[], int n)
{
    int i;
    for (i = 1; i <= n; i++) {
        sprintf(v[i].nome, "Pass%d", i);
        v[i].prioridade = 1 + rand() % n;
    }
}

void imprimeVetor(Passageiro v[], int n)
{
    int i;
    for (i = 1; i <= n; i++)
        printf("%s(%d) ", v[i].nome, v[i].prioridade);
    printf("\n");
}

int ordenado(Passageiro v[], int n)
{
    int i;
    for (i = 2; i <= n; i++)
        if (v[i - 1].prioridade > v[i].prioridade)
            return 0;
    return 1;
}

void troca(Passageiro *a, Passageiro *b)
{
    Passageiro t = *a;
    *a = *b;
    *b = t;
    trocas++;
}

void InsertSort(Passageiro v[], int n)
{
    int i, j;
    for (i = 2; i <= n; i++) {
        j = i;
        while (j > 1) {
            comparacoes++;
            if (v[j - 1].prioridade > v[j].prioridade) {
                troca(&v[j - 1], &v[j]);
                j--;
            } else
                break;
        }
    }
}

/* devolve o INDICE da mediana entre v[e], v[meio] e v[d] */
int mediana(Passageiro v[], int e, int d)
{
    int m = (e + d) / 2;

    comparacoes++;
    if (v[e].prioridade > v[m].prioridade) {
        comparacoes++;
        if (v[m].prioridade > v[d].prioridade) return m;
        comparacoes++;
        if (v[e].prioridade > v[d].prioridade) return d;
        return e;
    }
    comparacoes++;
    if (v[e].prioridade > v[d].prioridade) return e;
    comparacoes++;
    if (v[m].prioridade > v[d].prioridade) return d;
    return m;
}

void QuickSort(Passageiro v[], int e, int d)
{
    int i, j;
    Passageiro pivo;

    if (e >= d)
        return;

    pivo = v[mediana(v, e, d)];
    i = e;
    j = d;
    do {
        while (comparacoes++, pivo.prioridade > v[i].prioridade)
            i++;
        while (comparacoes++, v[j].prioridade > pivo.prioridade)
            j--;
        if (i <= j) {
            if (i < j)
                troca(&v[i], &v[j]);
            i++;
            j--;
        }
    } while (i <= j);

    QuickSort(v, e, j);
    QuickSort(v, i, d);
}

int temAviao(void)
{
    if (!iniciado)
        printf("ERRO: nenhum aviao em embarque. Use a opcao 1 ou 6 primeiro.\n");
    return iniciado;
}

void novoEmbarque(void)
{
    int n, i;
    Passageiro p;

    n = lerInt("Capacidade do aviao (1 a 1024): ", 1, TAM_MAX);
    InicHeap(&aviao, n);
    iniciado = 1;
    embarcados = 0;

    for (i = 1; i <= n; i++) {
        sprintf(p.nome, "Pass%d", i);
        p.prioridade = 1 + rand() % n;
        if (!InsereHeap(&aviao, p)) {
            printf("ERRO: nao consegui inserir %s!\n", p.nome);
            break;
        }
        if (n <= LIMITE)
            printf("Inseri %s (prioridade %d)\n", p.nome, p.prioridade);
    }
    printf("%d passageiros no Heap.\n", aviao.final);
    if (n <= LIMITE)
        ImprimeHeap(&aviao);
}

void embarcaProximo(void)
{
    Passageiro p;

    if (!temAviao())
        return;
    if (!RemoveHeap(&aviao, &p)) {
        printf("ERRO: Heap vazio, ninguem para embarcar!\n");
        return;
    }
    embarcados++;
    printf("Embarcou #%d: %s (prioridade %d)\n", embarcados, p.nome, p.prioridade);
}

void embarcaTodos(void)
{
    Passageiro p;

    if (!temAviao())
        return;
    if (aviao.final == 0) {
        printf("ERRO: Heap vazio, ninguem para embarcar!\n");
        return;
    }
    while (RemoveHeap(&aviao, &p)) {
        embarcados++;
        printf("Embarcou #%d: %s (prioridade %d)\n", embarcados, p.nome, p.prioridade);
    }
}

void imprimePendentes(void)
{
    if (!temAviao())
        return;
    ImprimeHeap(&aviao);
}

void checaAviao(void)
{
    if (!temAviao())
        return;
    if (ChecaHeap(&aviao))
        printf("ChecaHeap: o vetor E um heap valido.\n");
    else
        printf("ChecaHeap: o vetor NAO e um heap valido.\n");
}

void constroi(void)
{
    int n;

    n = lerInt("Numero de passageiros (1 a 1024): ", 1, TAM_MAX);
    InicHeap(&aviao, n);
    geraVetor(aviao.v, n);
    aviao.final = n;
    iniciado = 1;
    embarcados = 0;

    printf("Vetor gerado (sem ordem de heap):\n");
    if (n <= LIMITE)
        imprimeVetor(aviao.v, n);
    printf("ChecaHeap antes: %s\n", ChecaHeap(&aviao) ? "heap valido" : "NAO e heap");

    comparacoes = 0;
    trocas = 0;
    ConstroiHeap(&aviao);
    printf("Heapfy (construcao): %ld comparacoes, %ld trocas\n", comparacoes, trocas);

    if (n <= LIMITE)
        ImprimeHeap(&aviao);
    printf("ChecaHeap depois: %s\n", ChecaHeap(&aviao) ? "heap valido" : "NAO e heap");
}

/* ordena uma COPIA, para nao perder o embarque em andamento */
void executaHeapSort(void)
{
    static Heap copia;
    int n;

    if (!temAviao())
        return;
    n = aviao.final;
    if (n == 0) {
        printf("ERRO: nao ha passageiros no Heap!\n");
        return;
    }
    copia = aviao;
    comparacoes = 0;
    trocas = 0;
    HeapSort(&copia);

    printf("Vetor ordenado (crescente):\n");
    if (n <= LIMITE)
        imprimeVetor(copia.v, n);
    printf("HeapSort: %ld comparacoes, %ld trocas\n", comparacoes, trocas);
    printf("Conferencia: vetor %s.\n", ordenado(copia.v, n) ? "ordenado" : "NAO ordenado");
}

void linha(char *nome, long c, long t, int ok)
{
    printf("%-11s %12ld %10ld %12ld   %s\n", nome, c, t, c + t, ok ? "sim" : "NAO");
}

void comparaAlgoritmos(void)
{
    static Passageiro orig[TAM_MAX + 1], q[TAM_MAX + 1], ins[TAM_MAX + 1];
    static Heap h;
    long cH, tH, cQ, tQ, cI, tI;
    int i;

    geraVetor(orig, TAM_MAX);
    InicHeap(&h, TAM_MAX);
    for (i = 1; i <= TAM_MAX; i++) {
        h.v[i] = orig[i];
        q[i] = orig[i];
        ins[i] = orig[i];
    }
    h.final = TAM_MAX;

    comparacoes = 0; trocas = 0;
    HeapSort(&h);
    cH = comparacoes; tH = trocas;

    comparacoes = 0; trocas = 0;
    QuickSort(q, 1, TAM_MAX);
    cQ = comparacoes; tQ = trocas;

    comparacoes = 0; trocas = 0;
    InsertSort(ins, TAM_MAX);
    cI = comparacoes; tI = trocas;

    printf("Vetor aleatorio com %d elementos\n", TAM_MAX);
    printf("%-11s %12s %10s %12s   %s\n", "Algoritmo", "Comparacoes", "Trocas", "Total", "Ordenou?");
    linha("HeapSort", cH, tH, ordenado(h.v, TAM_MAX));
    linha("QuickSort", cQ, tQ, ordenado(q, TAM_MAX));
    linha("InsertSort", cI, tI, ordenado(ins, TAM_MAX));
}

int main(int argc, char *argv[])
{
    int op;

    /* ./voeheap 2026  -> semente fixa (log reproduzivel) */
    if (argc > 1)
        srand(atoi(argv[1]));
    else
        srand(time(NULL));

    do {
        printf("\nVoeHeap - Embarque com Prioridade\n");
        printf(" 1 - Novo embarque (passageiros aleatorios)\n");
        printf(" 2 - Embarcar proximo passageiro (RemoveHeap)\n");
        printf(" 3 - Embarcar todos os passageiros restantes\n");
        printf(" 4 - Imprimir quem ainda falta embarcar (ImprimeHeap)\n");
        printf(" 5 - Checar se e um heap valido (ChecaHeap)\n");
        printf(" 6 - Vetor desordenado + construir heap (Heapfy)\n");
        printf(" 7 - HeapSort e imprimir vetor ordenado\n");
        printf(" 8 - Comparar HeapSort x QuickSort x InsertSort (1024)\n");
        printf(" 0 - Sair\n");
        op = lerInt("Opcao: ", 0, 8);
        printf("\n");

        switch (op) {
            case 1: novoEmbarque(); break;
            case 2: embarcaProximo(); break;
            case 3: embarcaTodos(); break;
            case 4: imprimePendentes(); break;
            case 5: checaAviao(); break;
            case 6: constroi(); break;
            case 7: executaHeapSort(); break;
            case 8: comparaAlgoritmos(); break;
            case 0: printf("Encerrando o VoeHeap.\n"); break;
        }
    } while (op != 0);

    return 0;
}
