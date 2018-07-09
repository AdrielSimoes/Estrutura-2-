# Estrutura-2-#include <stdio.h>
#include <stdlib.h>

struct arv{
    int n, fb;

    struct arv *esq;
    struct arv *dir;
    struct arv *pai;
};

struct arv *raiz = NULL;

void menu();
void insere();
void exibir_pre(struct arv*);
void exibir_niveis(struct arv*);
void exibir_niveis_b(struct arv*, int, int);
void remove_p();
void remover(struct arv*);
struct arv* menor_dir(struct arv*);
struct arv* buscar(int);
void limpar_arv(struct arv*);
int altura(struct arv*);
struct arv* balanceia(struct arv*);
struct arv* rot_dir(struct arv*);
struct arv* rot_esq(struct arv*);

int main(){
    struct arv *b;
    int op, n;

    while(1){
        menu();

        scanf("%d", &op);
        getchar();

        switch(op){
            case 1: insere();
                    break;

            case 2: remove_p();
                    printf("Pronto!\n");
                    getchar();
                    break;

            case 3: printf("Digite o numero a procurar: ");
                    scanf("%d", &n);
                    getchar();

                    b = buscar(n);

                    if(b){
                        printf("Elemento encontrado: %d\n", b->n);
                    }

                    else{
                        printf("Elemento nao encontrado!\n");
                    }

                    b = NULL;
                    getchar();
                    break;

            case 4: exibir_pre(raiz);
                    printf("\nPronto!\n");
                    getchar();
                    break;

            case 5: exibir_niveis(raiz);
                    printf("\nPronto!\n");
                    getchar();
                    break;

            case 6: printf("Altura da arvore: %d\n", altura(raiz));
                    getchar();
                    break;

            case 7: if(raiz){
                        limpar_arv(raiz);
                        printf("arv limpa!\n");
                    }

                    else{
                        printf("A arvore ja estava limpa!\n");
                    }

                    getchar();
                    break;

            case 8: if(raiz){
                        limpar_arv(raiz);
                    }

                    exit(0);

            default: printf("Opcao incorreta!\n");
                     getchar();
                     break;
        }
    }
}

void menu(){
	
	printf("\n");
    printf("1 - Inserir\n");
    printf("2 - Remover\n");
    printf("3 - Buscar\n");
    printf("4 - Exibir - Pre Ordem\n");
    printf("5 - Exibir por niveis\n");
    printf("6 - Altura da arvore\n");
    printf("7 - Limpar arvore\n");
    printf("8 - Sair\n\n");

    printf("Digite uma opcao: ");

}

void insere(){
    struct arv *aux1 = NULL, *aux2 = NULL;
    int n;

    printf("Digite o numero a inserir: ");
    scanf("%d", &n);
    getchar();

    if(!raiz){
        raiz = (struct arv*) malloc(sizeof(struct arv));

        if(!raiz){
            printf("Erro ao alocar =(\n");
            exit(1);
        }

        raiz->n = n;
        raiz->esq = NULL;
        raiz->dir = NULL;
        raiz->pai = NULL;

        raiz->fb = 0;
    }

    else{
        aux1 = raiz;
        aux2 = aux1;

        while(aux2){
            if(n < aux2->n){
                aux2 = aux2->esq;

                if(!aux2){
                    aux1->esq = (struct arv*) malloc(sizeof(struct arv));

                    if(!aux1->esq){
                        printf("Erro ao alocar =(\n");
                        exit(1);
                    }

                    aux2 = aux1->esq;

                    aux2->esq = NULL;
                    aux2->dir = NULL;
                    aux2->pai = aux1;
                    aux2->n = n;
                    break;
                }
                else{
                    aux1 = aux2;
                }
            }

            else{
                aux2 = aux2->dir;

                if(!aux2){
                    aux1->dir = (struct arv*) malloc(sizeof(struct arv));

                    if(!aux1->dir){
                        printf("Erro ao alocar =(\n");
                        exit(1);
                    }

                    aux2 = aux1->dir;

                    aux2->esq = NULL;
                    aux2->dir = NULL;
                    aux2->pai = aux1;
                    aux2->n = n;
                    break;
                }

                else{
                    aux1 = aux2;
                }
            }
        }
    }

    printf("Elemento Inserido!\n");
    getchar();

    if(aux2){
        while(aux2){
            aux2->fb = altura(aux2->dir) - altura(aux2->esq);

            if(aux2->fb > 1 || aux2->fb < -1){
                aux2 = balanceia(aux2);
            }

            aux2 = aux2->pai;
        }
    }
}

void exibir_pre(struct arv *aux){
    if(!raiz){
        printf("arvore vazia!");
        return;
    }

    if(aux){
        printf("%d(%d) ", aux->n, aux->fb);
        exibir_pre(aux->esq);
        exibir_pre(aux->dir);
    }
}


void remove_p(){
    struct arv *aux;
    int n;

    printf("Digite o numero que deseja remover: ");
    scanf("%d", &n);
    getchar();

    aux = buscar(n);

    if(aux){
        remover(aux);
    }

    else{
        printf("O numero nao existe na arvore!\n");
    }
}

void remover(struct arv *aux){
    struct arv *aux2, *salva = aux->pai;

    if(aux != raiz){
        if(!aux->esq && !aux->dir){
            if(aux->n < aux->pai->n){
                aux->pai->esq = NULL;
            }
            else{
                aux->pai->dir = NULL;
            }

            free(aux);
        }

        else if(!aux->dir){
            if(aux->n < aux->pai->n){
                aux->pai->esq = aux->esq;
            }
            else{
                aux->pai->dir = aux->esq;
            }
            aux->esq->pai = aux->pai;
            free(aux);
        }

        else if(!aux->esq){
            if(aux->n < aux->pai->n){
                aux->pai->esq = aux->dir;
            }
            else{
                aux->pai->dir = aux->dir;
            }
            aux->dir->pai = aux->pai;
            free(aux);
        }

        else{
            aux2 = menor_dir(aux->dir);
            aux->n = aux2->n;

            remover(aux2);
        }
    }

    else{
        if(!aux->esq && !aux->dir){
            free(aux);
            raiz = NULL;
        }

        else if(!aux->dir){
            aux->esq->pai = NULL;
            raiz = aux->esq;
            free(aux);
        }

        else if(!aux->esq){
            aux->dir->pai = NULL;
            raiz = aux->dir;
            free(aux);
        }

        else{
            aux2 = menor_dir(aux->dir);
            aux->n = aux2->n;

            remover(aux2);
        }
    }

    if(salva){
        while(salva){
            salva->fb = altura(salva->dir) - altura(salva->esq);

            if(salva->fb > 1 || salva->fb < -1){
                salva = balanceia(salva);
            }

            salva = salva->pai;
        }
    }
}

struct arv* menor_dir(struct arv *aux){
    while(aux->esq){
        aux = aux->esq;
    }

    return aux;
}

struct arv* buscar(int n){
    struct arv *aux;

    aux = raiz;

    while(aux && aux->n != n){
        if(n < aux->n){
            aux = aux->esq;
        }

        else{
            aux = aux->dir;
        }
    }

    return aux;
}

void limpar_arv(struct arv *aux){
    if(aux->esq){
        limpar_arv(aux->esq);
    }

    if(aux->dir){
        limpar_arv(aux->dir);
    }

    free(aux);
    raiz = NULL;
}

int altura(struct arv *aux){
    if(!aux){
        return 0;
    }

    int ae = altura(aux->esq);
    int ad = altura(aux->dir);

    return (ae > ad) ? ae + 1: ad + 1;
}

struct arv* rot_dir(struct arv *aux){
    struct arv *esq = aux->esq;

    aux->esq = esq->dir;
    esq->dir = aux;

    if(aux->esq){
        aux->esq->pai = aux;
    }

    esq->pai = aux->pai;
    aux->pai = esq;

    if(aux == raiz){
        raiz = esq;
    }

    if(esq->pai){
        if(esq->n < esq->pai->n){
            esq->pai->esq = esq;
        }
        else{
            esq->pai->dir = esq;
        }
    }

    esq->fb = altura(esq->dir) - altura(esq->esq);
    aux->fb = altura(aux->dir) - altura(aux->esq);

    return esq;
}

struct arv* rot_esq(struct arv *aux){
    struct arv *dir = aux->dir;

    aux->dir = dir->esq;
    dir->esq = aux;

    if(aux->dir){
        aux->dir->pai = aux;
    }

    dir->pai = aux->pai;
    aux->pai = dir;

    if(aux == raiz){
        raiz = dir;
    }

    if(dir->pai){
        if(dir->n < dir->pai->n){
            dir->pai->esq = dir;
        }
        else{
            dir->pai->dir = dir;
        }
    }

    dir->fb = altura(dir->dir) - altura(dir->esq);
    aux->fb = altura(aux->dir) - altura(aux->esq);
    return dir;
}

struct arv* balanceia(struct arv *aux){
    if(aux->fb < -1){
        if(aux->esq->fb > 0){
            aux->esq = rot_esq(aux->esq);
        }

        aux = rot_dir(aux);
    }

    else if(aux->fb > 1){
        if(aux->dir->fb < 0){
            aux->dir = rot_dir(aux->dir);
        }

        aux = rot_esq(aux);
    }

    return aux;
}

void exibir_niveis(struct arv *aux){
    int i;

    for(i = 0; i <= altura(raiz); i++){
        printf("Nivel %d\n", i);
        exibir_niveis_b(raiz, i, 0);
        printf("\n\n");
    }
}

void exibir_niveis_b(struct arv *aux, int atual, int cont){
    if(!raiz){
        printf("A arvore esta vazia!");
        getchar();
        return;
    }

    if(aux){
        exibir_niveis_b(aux->esq, atual, cont+1);

        if(atual == cont){
            printf("%d ", aux->n);
        }

        exibir_niveis_b(aux->dir, atual, cont+1);
    }
}
