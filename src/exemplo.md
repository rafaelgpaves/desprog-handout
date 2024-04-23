Título
======

Subtítulo
---------

Para criar um parágrafo, basta escrever um texto contínuo, sem pular linhas.

Você também pode criar

1. listas;

2. ordenadas,

assim como

* listas;

* não-ordenadas

e imagens. Lembre que todas as imagens devem estar em uma subpasta *img*.

![](logo.png)

Para tabelas, usa-se a [notação do
MultiMarkdown](https://fletcher.github.io/MultiMarkdown-6/syntax/tables.html),
que é muito flexível. Vale a pena abrir esse link para saber todas as
possibilidades.

| coluna a | coluna b |
|----------|----------|
| 1        | 2        |

Ao longo de um texto, você pode usar *itálico*, **negrito**, {red}(vermelho) e
[[tecla]]. Também pode usar uma equação LaTeX: $f(n) \leq g(n)$. Se for muito
grande, você pode isolá-la em um parágrafo.

$$\lim_{n \rightarrow \infty} \frac{f(n)}{g(n)} \leq 1$$

Para inserir uma animação, use `md :` seguido do nome de uma pasta onde as
imagens estão. Essa pasta também deve estar em *img*.

:bubble

Você também pode inserir código, inclusive especificando a linguagem.

``` py
def f():
    print('hello world')
```

``` c
void f() {
    printf("hello world\n");
}
```

Se não especificar nenhuma, o código fica com colorização de terminal.

```
hello world
```


!!! Aviso
Este é um exemplo de aviso, entre `md !!!`.
!!!


??? Exercício

Este é um exemplo de exercício, entre `md ???`.

::: Gabarito
Este é um exemplo de gabarito, entre `md :::`.
:::

???


![](graph.png)

Podemos inicializar as distâncias em uma matriz
``` c
int dist[][] = {
    [0, 5, 9, 11, INF, INF],
    [5, 0, INF, INF, 3, INF],
    [9, INF, 0, 4, 3, INF],
    [11, INF, 4, 0, INF, 14], 
    [INF, 3, 3, INF, 0, 7], 
    [INF, INF, INF, 14, 7, 0]
}
```

A distância entre um ponto e ele mesmo é zero. A distância entre dois pontos que não estão conectados diretamente é infinita.


Intuitivamente, podemos pensar em testar todos os caminhos possíveis, mas isso demoraria muito. E se escolhermos um ponto intermediário?


Pense na ideia do algoritmo. Como ficaria seu pseudocódigo?

``` pseudocodigo
para cada vértice entre A, B, ..., F:
    para cada ponto de partida entre A, B, ..., F:
        para cada ponto de chegada entre A, B, ..., F:
            dist = dist entre partida e vértice mais dist entre saída e vértice
            se a nova distancia for menor que a distancia registrada na matriz:
                substituir a distancia na matriz
```

isso pode ser traduzido bem diretamente para C:
``` c
for (int k = 0; k < V; k++) {
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            if (dist[i][k] + dist[k][j] > dist[i][j]) {
                dist[i][j] = dist[i][k] + dist[k][j];
            }
        }
    }
}
```

Como podemos ver na implementação, existem 3 loops aninhados. Pela receita para estimar complexidade estudada na Aula 6, sabemos que a complexidade desse algoritmo é O(n³)

??? Exercício

Pare um pouco para pensar na complexidade desse algoritmo. E o número de arestas, não influencia?

::: Gabarito
Independente do número de arestas, o algoritmo fará comparações considerando todos os caminhos possíveis. O número de arestas no começo não influencia a complexidade desse algoritmo.
:::

???
