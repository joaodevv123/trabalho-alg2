/* Heap de maximo em vetor: usa v[1..final]; pai = i/2, filhos = 2i e 2i+1 */
#ifndef HEAP_H
#define HEAP_H

#define TAM_MAX 1024

typedef struct {
    char nome[30];
    int prioridade;
} Passageiro;

typedef struct {
    Passageiro v[TAM_MAX + 1];
    int final;
    int capacidade;
} Heap;

/* contadores de comparacoes e trocas (definidos em main.c) */
extern long comparacoes;
extern long trocas;

void InicHeap(Heap *h, int capacidade);
int InsereHeap(Heap *h, Passageiro p);
int RemoveHeap(Heap *h, Passageiro *p);
void Heapfy(Heap *h, int i);
void ConstroiHeap(Heap *h);
int ChecaHeap(Heap *h);
void ImprimeHeap(Heap *h);
void HeapSort(Heap *h);

#endif
