Algoritmo de Floyd-Warshall
======

Objetivos
---------

Ao final desse handout, você deve ser capaz de:

- Entender o algoritmo de Floyd-Warshall
- Implementar esse mesmo algoritmo em C

Grafos e o problema do caminho mais curto
----------

Imagine que o departamento de TI do Insper está com dificuldades de garantir a melhor conectividade WI-FI em todas as áreas do Prédio 2. Você, como aluno exemplar da disciplina de Desafios de Programação, foi convidado para ajudar a equipe a identificar possíveis pontos problemáticos na rede.

Para otimizar o desempenho da rede, é crucial entender qual seria o tempo mínimo de latência entre diferentes pontos de distribuição de sinal. Um dos jeitos de desenhar esse problema e delinear as informações é através de um **grafo**.

![Grafo de latências](grafo_inicial.drawio.png)

À primeira vista, um diagrama como esse pode parecer confuso, mas ele é uma maneira eficiente de representar relações entre objetos. Vamos tentar entender alguns de seus elementos.

Quando analisamos grafos, denominamos as "bolinhas" de **vértices** e as linhas de **arestas**. Cada aresta tem um **peso**, que, nesse caso, representa a latência (em milissegundos) entre os vértices.

??? Aquecimento

Além de "latência", você consegue pensar em outras unidades com as quais as arestas podem ser medidas nesse grafo? E em outros sistemas?

::: Gabarito
O peso atribuído às arestas pode ser medido em distância, custo, ou qualquer outra unidade que faça sentido para o problema. Por exemplo, se o grafo representasse um sistema de transporte público, o peso poderia ser o custo da passagem. Se o grafo representasse um sistema de abastecimento de água, o peso poderia ser a quantidade de água que passa por aquela aresta.
:::

???

Para garantir que todas as áreas do Prédio 2 estejam cobertas com a melhor conectividade possível, precisamos saber a latência mínima entre cada par de pontos de distribuição de sinal. Isso nos ajuda a identificar pontos problemáticos e como podemos redirecionar o sinal para melhorar a conexão entre eles.

Esse problema e suas variações são conhecidos como o **problema do caminho mais curto**. O algoritmo que vamos falar visa simplificar a resolução desse problema. Diferentemente de outros métodos semelhantes, o algoritmo de Floyd-Warshall é capaz de calcular o caminho mais curto entre todos os pares de vértices de uma vez só. Muito conveniente para nós, não é mesmo?

Bom, vamos lá.

O algoritmo
----------

A ideia do algoritmo de Floyd-Warshall é calcular a menor distância entre *todos* os pares de vértices em um grafo de uma vez só. Isso nos permite fazer melhores e mais rápidas comparações do que se analisássemos cada par de vértices separadamente.

Vamos manter a ideia da rede WI-FI mas rearranjar um pouco a disposição dos vértices para que possamos trabalhar melhor alguns conceitos.

![Grafo exemplo](grafo_exemplo.drawio.png)

Perceba que esse é um grafo **não direcionado**. Isso significa que a relação entre os vértices é simétrica, ou seja, se a distância entre **A** e **B** é 80, a distância entre **B** e **A** também é 80.

Podemos inicializar as distâncias do grafo em uma matriz. Algumas observações para isso:
+ Se essa distância existe como um peso, vamos colocá-la.
+ Se não, vamos adotar como convenção que a distância é infinita (INF).

??? Checkpoint

Escreva a matriz de distâncias inicial para o grafo acima. Use esse template:

![template-tabela](template_tabela.drawio.png)

Lembre-se que, nesse exemplo, o grafo é não direcionado

::: Gabarito

![gabarito-chekpoint](matriz_inicial_checkpoint.drawio.png)

:::

???

Um método que funcionaria é testar todos os caminhos possíveis, mas isso demoraria muito e seria pouco eficiente. Deve existir um método melhor.

??? Exercício

No grafo abaixo, qual seria a distância ente os vértices **A** e **B** quando inicializados na matriz de entrada? Essa é a menor distância entre esses dois vértices?

![Grafo de exemplo com vértice intermediário](grafo_intermediario.png)

::: Gabarito

A distância entre **A** e **B** é inicializada na matriz como 50. No entanto, podemos obter um valor menor se passarmos pelo vértice **C**. Nesse caso, a distância entre **A** e **B** seria 30.

:::

???

Vamos aplicar essa ideia então para melhorar nossa aproximação inicial. Nossa estimativa inicial só considerava um passo (só caminhos diretos), que não é o que queremos.

Vamos considerar um segundo passo então. E se, além caminhos diretos, a gente também considerasse caminhos que passam por A?

??? Checkpoint

Olhe o gráfico abaixo (o mesmo do começo dessa seção). Qual é a distância inicial de **C** para **D**? E considerando a ideia acima, qual a nova distância entre **C** e **D**?

![Grafo exemplo](grafo_exemplo.drawio.png)

::: Gabarito

Inicialmente, a distância entre **C** e **D** é 30. Considerando **A** como um ponto intermediário, existe outro caminho com uma distância de 180.

???

Nesse caso, não estamos ganhando nada, porque passar por **A** custa mais. Mas não é difícil de imaginar que se o novo total fosse menor, poderíamos melhorar nossa estimativa. Vamos ver se melhora para os outros pontos então.

??? Checkpoint

Escreva para esse grafo a matriz de distâncias, agora aplicando essa nova ideia.

![Grafo exemplo](grafo_exemplo.drawio.png)

::: Gabarito

![gabarito-chekpoint](matriz_checkpoint_a_intermediario.drawio.png)

:::

???

Boa! Algumas distâncias estão menores agora!

Então vamos continuar estendendo essa ideia. Vamos considerar agora que podemos usar B também como um ponto intermediário. 

??? Checkpoint

Na matriz anterior, calculamos o caminho de **D** a **B** que possa passar por **A** e de **B** a **C** que possa passar por **A**. Algum outro caminho que possa passar por **A** e/ou **B** poderia ser melhor que esse?

::: Gabarito

Não, pois aí iríamos repetir vértices, então o caminho não seria o menor possível

:::

???

Quando concatenamos, por exemplo, o caminho de **D** a **B** que possa passar por **A** e de **B** a **C** que possa passar por **A**, já temos o melhor caminho possível que conseguimos até agora usando tanto **A** quanto **B** como intermediários. Com isso, existem duas possibilidades:

1. O novo caminho *é* melhor do que o que a gente tinha.

2. O novo caminho *não é* melhor do que o que a gente tinha.

Talvez você já tenha entendido a ideia então!

??? Checkpoint

Primeiro escreva como a matriz está após considerar B também como intermediário. Agora estendendo essa ideia, como ficaria a matriz na próxima iteração? E na outra? Faça de todas as iterações, incluindo a última

::: Incluindo B
![gabarito-chekpoint](matriz_b_intermediario.drawio.png)
:::

::: Incluindo C
![gabarito-chekpoint](matriz_c_intermediario.drawio.png)
:::

::: Incluindo D
![gabarito-chekpoint](matriz_d_intermediario.drawio.png)
:::

::: Incluindo E
![gabarito-chekpoint](matriz_e_intermediario.drawio.png)
:::

::: Incluindo F
![gabarito-chekpoint](matriz_f_intermediario.drawio.png)
:::

???


Em resumo, vamos começar apenas com os pesos descritos na imagem. Depois, considerar que um ponto `md A` pode ser usado como ponto intermediário, e ver se o caminho entre todos os pares de pontos, passando por ele, é menor do que o registrado anteriormente. Depois, considerar que um ponto `md B` pode também (além de `md A`) ser considerado como intermediário, e ver novamente se o caminho entre todos os pares de pontos, passando por quaisquer intermediários, é menor do que o registrado anteriormente. E repetir isso até que todos os pontos tenham sido considerados intermediários.

Essa é a ideia por trás do algoritmo de Floyd-Warshall. Como podemos perceber, é um algoritmo de **programação dinâmica**. Assim como na [APS3](https://ensino.hashi.pro.br/desprog/aps/3/), sempre que precisamos de um valor, podemos pegá-lo na matriz (não precisamos calcular mais de uma vez)

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

Mas por que isso funciona, e a matriz sai no final com os valores de caminhos mais curtos entre quaisquer par de pontos?

??? Checkpoint

Vamos considerar o grafo do começo dessa seção novamente. Começamos montando a matriz de distâncias. O que significa incluir um novo ponto no conjunto de pontos intermediários?

::: Gabarito

Quando incluimos um ponto `md B` na lista de pontos considerados como intermediários, já temos na matriz de distâncias o caminho ótimo (caminho mais curto) entre, digamos, dois pontos `md A` e `md E` (desconsiderando `md B`). A partir disso, podemos possivelmente melhorar o caminho considerando também `md B`, então no final da iteração do loop a matriz terá o caminho ótimo considerando-o também. Ou seja, depois de considerarmos que todos os pontos podem ser intermediários, a matriz terá a menor distância entre quaisquer dois pontos.

:::

???

Continuando com o algoritmo, se a distância calculada através dos intermediários for menor que a original, substituímos na matriz de distância.

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

![Grafo do exercício](grafo_exercicio.png)

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

Desafio (Opcional)
--------

Se você não conseguiu chegar até aqui, tudo bem. Mas se quiser fazer um último desafio, vamos seguir em frente!

Falamos como Floyd-Warshall é um algoritmo de programação dinâmica. Mas ele também pode ser escrito usando recursão.

??? Desafio

Como ficaria Floyd-Warshall recursivo?

Para te ajudar nisso, lembre-se de algumas coisas que vimos nesse handout:
+ Iniciamos uma matriz com os pesos.
+ Sempre que adicionamos um ponto no conjunto de pontos que estamos considerando intermediários, todas os valores que precisamos já estão na matriz.

**Dica**: provavelmente será mais fácil pensar em fazer duas funções, uma recursiva (que chame ela mesma) e uma principal que chame essa recursiva.

::: Gabarito

```c

int recursao(int i, int j, int k, int dist[V][V]) {
    if (k < 0) {
        return dist[i][j];
    }
    return min(recursao(i, j, k - 1, dist), recursao(i, k, k - 1, dist) + recursao(k, j, k - 1, dist));
}

void fw_recursivo(int dist[V][V]) {
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            dist[i][j] = recursao(i, j, V - 1, dist);
        }
    }
}

```

:::

???
