Algoritmo de Floyd-Warshall
======

Objetivos
---------

Ao final desse handout, você deve ser capaz de:

- Entender o algoritmo de Floyd-Warshall
- Implementar esse mesmo algoritmo em C

Grafos e o problema do caminho mais curto
----------

Imagine que você faz parte de uma entidade no Insper com estudantes espalhados por toda a cidade de São Paulo. Ao debater sobre o melhor horário para reuniões, você percebe que o trânsito é um grande problema para uma boa parte desses estudantes. Além disso, cada um tem um ponto de partida diferente.

Você assumiu a missão de analisar esse problema e sugerir o melhor horário médio para as reuniões considerando o tempo de locomoção de cada estudante. Uma estratégia interessante seria obter o tempo médio de locomoção entre o Insper e cada um dos estudantes e realizar uma análise a partir disso.

Um dos jeitos de desenhar esse problema e delinear as informações é através de um **grafo**.

![Grafo de rotas](grafo_inicial.png)

Em grafos como esse, denominamos as "bolinhas" de **vértices** e as linhas de **arestas**. Cada aresta tem um **peso**, que, nesse caso, representa o tempo de locomoção (em minutos) entre os vértices.

??? Aquecimento

Além de "tempo de locomoção", você consegue pensar em outras unidades com as quais as arestas podem ser medidas nesse grafo? E em outros sistemas?

::: Gabarito
O peso atribuído às arestas pode ser medido em distância, custo, ou qualquer outra unidade que faça sentido para o problema. Por exemplo, se o grafo representasse um sistema de transporte público, o peso poderia ser o custo da passagem. Se o grafo representasse um sistema de comunicação, o peso poderia ser a latência da conexão.
:::

???

Em nosso sistema, vamos considerar o vértice **I** como o Insper e todos os outros vértices como pontos de partida dos estudantes. É provável que você consiga inferir qual o ponto mais distante e qual o mais próximo a partir da imagem. No entanto, não é uma tarefa rápida calcular o tempo de locomoção exato entre todos os vértices, ainda mais considerando que existem variados caminhos.

Esse problema e suas variações são conhecidos como o **problema do caminho mais curto**. O algoritmo que vamos falar hoje é um dos que visa simplificar a resolução desse problema. Diferentemente de outros métodos semelhantes, o algoritmo de Floyd-Warshall é capaz de calcular o caminho mais curto entre todos os pares de vértices de uma vez só. Interessante, não?

Bom, vamos lá.

O algoritmo
----------

A ideia do algoritmo de Floyd-Warshall é calcular a menor distância entre *todos* os pares de vértices em um grafo de uma vez só. Isso nos permite fazer melhores e mais rápidas comparações do que se analisássemos cada par de vértices separadamente.

Vamos entender como ele funciona através de um exemplo.

![Grafo exemplo](graph.png)

Podemos inicializar as distâncias do grafo em uma matriz:

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

??? Exercício

Ao observar a matriz de entrada, o que significam os valores `INF`? E o que significa o valor 0?

::: Gabarito
O valor `INF` representa a inexistência de uma aresta entre dois vértices. O valor 0 representa a distância entre um vértice e ele mesmo.
:::

???

Intuitivamente, podemos pensar em testar todos os caminhos possíveis, mas isso demoraria muito e seria pouco eficiente. E se escolhessemos um ponto intermediário?

??? Exercício

No grafo abaixo, qual seria a distância ente os vértices **A** e **B** quando inicializados na matriz de entrada? Essa é a menor distância entre esses dois vértices?

![Grafo de exemplo com vértice intermediário](grafo_intermediario.png)

::: Gabarito

A distância entre **A** e **B** é inicializada na matriz como 50. No entanto, podemos obter um valor menor se passarmos pelo vértice **C**. Nesse caso, a distância entre **A** e **B** seria 30.

:::

???

Apesar de ter sido um exemplo simples, podemos pensar em uma maneira de generalizar essa lógica para todos os vértices do grafo, que podem possuir mais de um caminho intermediário.

A ideia envolve passar por **cada ponto** do grafo e tratá-lo como se fosse um intermediário entre outros dois pontos. Após isso, escolhendo um ponto qualquer de partida, calculamos a distância entre ele, o intermediário e todos os outros possíveis pontos de chegada.

??? Exercício

Em termos de pseudocódigo, quantos loops você precisaria para implementar essa ideia?

::: Gabarito

``` pseudocodigo
para cada vértice entre A, B, ..., F:
    para cada ponto de partida entre A, B, ..., F:
        para cada ponto de chegada entre A, B, ..., F:
```

:::

???

Se a distância calculada através dos intermediários for menor que a original, substituímos na matriz de distância.

Após considerar todos os pontos como intermediários, sabemos que a matriz possui as menores distâncias entre quaisquer dois pontos. Isso ocorre porque quando escolhemos um ponto `md k`, temos certeza de que para todos os pontos até `md k - 1`, já temos as menores distâncias sem passar pelos pontos `md k` a `mk V`. Ou seja, após considerar todos os pontos, temos todas as menores distâncias.

??? Checkpoint

Agora pense no algoritmo como um todo, considerando o cálculo de distâncias. Como ficaria seu pseudocódigo?

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

Isso pode ser traduzido bem diretamente para C:

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
