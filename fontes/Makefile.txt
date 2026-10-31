voeheap: main.c heap.c heap.h
	gcc -Wall -o voeheap main.c heap.c

# gera o log de testes (semente fixa)
log: voeheap
	./voeheap 2026 < entrada.txt > log.txt

clean:
	rm -f voeheap *.o
