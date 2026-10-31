#include <stdio.h>
#include "heap.h"

static void troca(Passageiro *a, Passageiro *b)
{
    Passageiro t = *a;
    *a = *b;
    *b = t;
    trocas++;
}

void InicHeap(Heap *h, int capacidade)
{
    h->final = 0;
    h->capacidade = capacidade;
}

int InsereHeap(Heap *h, Passageiro p)
{
    int i;

    if (h->final == h->capacidade)
        return 0;

    h->final++;
    h->v[h->final] = p;

    i = h->final;
    while (i > 1) {
        comparacoes++;
        if (h->v[i].prioridade > h->v[i / 2].prioridade) {
            troca(&h->v[i], &h->v[i / 2]);
            i = i / 2;
        } else
            break;
    }
    return 1;
}

/* o ultimo elemento vai para a raiz e desce com Heapfy */
int RemoveHeap(Heap *h, Passageiro *p)
{
    if (h->final == 0)
        return 0;

    *p = h->v[1];
    h->v[1] = h->v[h->final];
    h->final--;
    Heapfy(h, 1);
    return 1;
}

void Heapfy(Heap *h, int i)
{
    int e, d, maior;

    while (1) {
        e = 2 * i;
        d = 2 * i + 1;
        maior = i;

        if (e <= h->final) {
            comparacoes++;
            if (h->v[e].prioridade > h->v[maior].prioridade)
                maior = e;
        }
        if (d <= h->final) {
            comparacoes++;
            if (h->v[d].prioridade > h->v[maior].prioridade)
                maior = d;
        }
        if (maior == i)
            break;

        troca(&h->v[i], &h->v[maior]);
        i = maior;
    }
}

void ConstroiHeap(Heap *h)
{
    int i;

    for (i = h->final / 2; i >= 1; i--)
        Heapfy(h, i);
}

int ChecaHeap(Heap *h)
{
    int i;

    for (i = 2; i <= h->final; i++)
        if (h->v[i].prioridade > h->v[i / 2].prioridade)
            return 0;
    return 1;
}

void ImprimeHeap(Heap *h)
{
    int i;

    printf("Heap com %d passageiro(s):\n", h->final);
    for (i = 1; i <= h->final; i++)
        printf("%s(%d) ", h->v[i].nome, h->v[i].prioridade);
    printf("\n");
}

void HeapSort(Heap *h)
{
    int n = h->final;

    ConstroiHeap(h);
    while (h->final > 1) {
        troca(&h->v[1], &h->v[h->final]);
        h->final--;
        Heapfy(h, 1);
    }
    h->final = n;
}
