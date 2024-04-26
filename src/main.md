Algoritmo de Floyd-Warshall
======

Objetivos
---------

Ao final desse handout, você deve ser capaz de:

- Entender o algoritmo de Floyd-Warshall
- Implementar esse mesmo algoritmo em C

Grafos e o problema do caminho mais curto
----------

Imagine que você é um aluno que mora um pouco afastado do Insper. Em um dia anormal, o caminho que você costuma fazer está extremamente congestionado. Existem algumas rotas alternativas que você pode usar, mas elas costumam ser mais longas. Como você poderia fazer para descobrir qual é a **rota mais rápida?**

Um dos jeitos de desenhar esse problema é através de um **grafo**.

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


A ideia envolve passar por cada ponto do grafo e tratá-lo como se fosse um intermedíario entre outros dois pontos. Escolhendo um ponto qualquer de partida, calcular a distância entre ele e o intermediário mais o intermediário e todos os outros pontos. Se essa nova distância for menor que a original, substituímos na matriz de distância. E repetimos isso, considerando todos os pontos como ponto de partida.

Após consideerar todos os pontos como intermediários, sabemos que a matriz possui as menores distâncias entre quaisquer dois pontos. Isso ocorre porque quando escolhemos um ponto `md k`, temos certeza de que para todos os pontos até `md k - 1`, já temos as menores distâncias sem passar pelos pontos `md k` a `mk V`. Ou seja, após considerar todos os pontos, temos todas as menores distâncias.


??? Checkpoint

Agora pense na ideia do algoritmo. Como ficaria seu pseudocódigo?

::: Gabarito

``` pseudocodigo
para cada vértice entre A, B, ..., F:
    para cada ponto de partida entre A, B, ..., F:
        para cada ponto de chegada entre A, B, ..., F:
            dist = dist entre partida e vértice mais dist entre saída e vértice
            se a nova distancia for menor que a distancia registrada na matriz:
                substituir a distancia na matriz
```

:::

???

isso pode ser traduzido bem diretamente para C:
``` c
for (int k = 0; k < V; k++) {
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            if (dist[i][k] + dist[k][j] < dist[i][j]) {
                dist[i][j] = dist[i][k] + dist[k][j];
            }
        }
    }
}
```

Como podemos ver na implementação, existem 3 loops aninhados. Pela receita para estimar complexidade estudada na [Aula 6](https://ensino.hashi.pro.br/desprog/aula/6/), sabemos que a complexidade desse algoritmo é O(n³)

Ufa, chega de teoria. Agora um pouco de prática!

??? Exercício

Considere o seguinte grafo:

![](graph2.png)

Escreva como está a matriz de distâncias após cada iteração do loop mais externo.

**Dica**: pense no código em alto nível. Provavelmente será mais difícil tentando simular o algoritmo na sua mente.

::: Outra Dica

Comece construindo a matriz de distâncias:

![](ex2_0.drawio.png)

:::

::: Gabarito

:ex2

:::

???
