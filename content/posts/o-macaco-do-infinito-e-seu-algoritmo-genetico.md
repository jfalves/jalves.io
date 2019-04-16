---
draft: False
title: "O Macaco do Infinito e seu Algoritmo Genético"
date: 2019-04-13T08:34:00-03:00
authors:
  - Jonathan Alves
---
Suponhamos o seguinte absurdo: em um universo paralelo, a terra é populada apenas por um macaco e uma máquina de escrever (Sim, você leu certo!) e toda a interação nesse universo ocorre apenas entre eles. Com suas habilidades primitivas, o macaco aperta aleatoriamente as teclas da máquina, gerando páginas e mais páginas de conteúdos aleatórios. Quais são as chances desses macacos acidentalmente escrever o livro Hamlet, de William Shakespeare, ou pelo menos parte dela?
Fazendo uma conta simples, vamos calcular a chance de aleatoriamente escrever a palavra "Ser", extraída da famosa frase "Ser ou não ser, eis a questão". Considerando um teclado com 26 letras mais a tecla de espaço, ou seja, 27 teclas, a chance de apertamos a tecla "S" é de 1/27. Já a chance de acertar a tecla "E" após acertamos a letra "S" é de 1/27 * 1/27 e por fim, a chance de acertarmos a tecla "R" após termos acertado as duas primeiras é de 1/27 * 1/27 * 1/27, que é igual à 0,005%. Assim, podemos imaginar que para que o macaco acerte aleatoriamente toda a sequência de letras que compõem Hamlet seja uma probabilidade muitíssimo baixa (1/27<sup>quantidade total de caracteres</sup>), mas ainda sim possível, caso as tentativas ocorressem em um período de tempo infinito<sup>1</sup>.
Porém, existe uma técnica muito mais eficiente para navegarmos por todas as soluções aleatórias e obtermos um resultado satisfatório, chamado de Algoritmo Genético.

## O Algoritmo Genético
O algoritmo genético<sup>2</sup> é inspirado no conceito de seleção natural, tirado da teoria da evolução de Charles Darwin, e como o próprio escreveu:

> "Não é o mais forte que sobrevive. Nem o mais inteligente. Mas o que se adapta às mudanças."

Baseado nisso, John Holland<sup>3</sup> introduziu o conceito de algoritmo genético que muito abstratamente funciona assim:

Dado uma população de soluções aleatórias do seu problema.

1. Escolhe-se reprodutores proporcionalmente à sua adaptabilidade, ou seja, quanto mais apto, maior a chance de ser escolhido.
2. Os sobreviventes se reproduzem, garantindo que as características boas se propaguem pras gerações futuras.
3. Os filhos dessa geração estão sujeitos à alguma mutação, que pode ser algo positivo ou não no ponto de vista da evolução.
4. Os reprodutores são substituídos pelo seu filhos e o ciclo se repete.

Esse algoritmo é muito útil quando temos um problema com incontáveis possibilidades e queremos achar a melhor solução, ou seja, uma ferramenta de busca dentro de um espaço amostal grande. Algumas das aplicações<sup>4</sup> incluem otimização de funções matemáticas, problemas de otimização de rota de veículos, otimização de distribuição, etc.
Vamos agora partir para um exemplo prático, implementado em python, vamos ajudar o macaco da introdução a achar a frase "ser ou não ser eis a questão" dentro da aleatoriedade utilizando algoritmo genético.

## População Inicial
Vamos começar definindo uma população inicial aleatoriamente. Os genes, característica que identifica um indivíduo, vão ser representados por uma lista de caracteres, que iremos manipular com o objetivo de aproximar esses indivíduos da frase de referência. Para esse exemplo vamos usar a biblioteca **numpy** para lidar com algumas operações de vetores.

{{< highlight python3 "linenos=table" >}}
import string
import numpy as np
from numpy import random

#Realiza o setup inicial dos dados necessários para começar utilizar o algoritmo
target = 'ser ou nao ser eis a questao'
population_size = 100
mutation_rate = 0.01
genes_sample_space = list(string.whitespace[0] + string.ascii_lowercase)

#Gera uma população inicial aleatória.
target_size = len(target)
population_list = list()

for n in range(population_size):
  individual = random.choice(genes_sample_space, target_size)
  population_list.append(individual)

#Apresenta a população, esse item é opcional
[print(''.join(element)) for element in population_list]
{{< /highlight >}}

Das linhas 7-10, definimos a variável **target**, que representa a referência pela qual vamos aproximar nossa população. Também, iniciamos as variáveis **population_size** e **mutation_rate** que são responsáveis por determinar o tamanho da população e a taxa de mutação dos genes. Não se preocupe se ainda não entender para que usaremos a taxa de mutação, isso será explicado em breve. Por fim, definimos um espaço amostral dos genes, a variável **genes_sample_space**, que é uma lista responsável por manter todas as possibilidades de caracteres que um gene pode ter.

Da linha 17-19, populamos uma lista com os genes de todos os indivíduos da população, escolhidos aleatoriamente através da função **random.choice(array, n)**, que retorna uma lista do tamanho da frase de referência com base no conteúdo do espaço amostral.

## Classificando os indivíduos
Agora que temos a população inicial gerada, vamos classificar os indivíduos para sabermos o quão aptos eles são. No nosso exemplo, criaremos uma função _fitness_ que diga o quão próximo nossos indivíduos estão da frase de referência.

{{< highlight python3 "linenos=table" >}}
#Calcula o score de cada indivíduo
def fitness(individual, target, size):

    score = 0

    for idx in range(size):
        if individual[idx] == target[idx]:
            score += 1

    return 2**score

#Calcula a adaptação dos indivíduos.
population_fitness = list()
for idx in range(population_size):
  population_fitness.append(fitness(population_list[idx], target, target_size))
{{< /highlight >}}

Na linha 3-10, temos a definição da função que recebe como parâmetro o indivíduo, a frase referência e o tamanho do genes. Também, comparamos gene à gene do indivíduo com o da frase referência, a cada acerto nós acrescentamos 1 à sua pontuação.

Na linha 11, retornamos a sua pontuação através do cálculo **2**<sup>**score**</sup>. Mas porque não retornarmos somente a soma da pontuação?

Bom sem entrar muito em detalhes o que estamos fazendo é bonificar os indivíduos com mais acertos, utilizando uma função exponencial. Poderíamos utilizar somente a pontuação como métrica, mas teríamos um algoritmo mais lento por estarmos utilizando uma função linear de classificação.

Finalmente, nas linhas 15-17, realizamos os cálculos de classificação para todos os membros da população e armazenamos em uma lista chamada **population_fitness**.

## Selecionando os indivíduos
Com o cálculo da adaptabilidade de cada indivíduo da população, iremos criar um conjunto de possíveis reprodutores. Nessa fase algo bem intuitivo nos passa pela cabeça, que é a ideia de selecionarmos somente os melhores indivíduos. Porém essa não é uma boa ideia, pois ao fazermos isso, estamos negando a variabilidade genética, que é responsável pela diversidade da nossa população e essa decisão pode fazer com que nossa população fique estagnada em um máximo local. A abordagem que tomei foi de escolher proporcionalmente os indivíduos reprodutores, ou seja, quanto mais apto mais provável de ser escolhido e vice versa.

{{< highlight python3 "linenos=table" >}}
#calcula o melhor score de toda população
best_score = np.amax(population_fitness)

#Seleciona os índividuos que irão se reproduzir
def mate_selection(population_list, population_fitness, population_size, best_score):

    mates = list()

    for idx in range(population_size):
        individual = population_list[idx]

        #Normaliza a pontuação dos indivíduos para uma escala de 0 à 1.
        normalized_score = np.interp(population_fitness[idx], (0,best_score), (0,1))
        #Probabilidade do indivíduo ser escolhido.
        probability = int(normalized_score * 100)

        for n in range(probability):
            mates.append(individual)

    return mates

#1. Seleciona os indivíduos que irão se reproduzir.
mating_pool = mate_selection(population_list, population_fitness, population_size, best_score)
mating_pool_size = len(mating_pool)
{{< /highlight >}}

Na linha 3, retornamos a melhor pontuação para utilizarmos posteriormente.

Na linha 7, definimos a função que irá retornar o conjunto de reprodutores recebendo como parâmetro a população inicial e sua respectivas pontuações, o tamanho da população e a melhor pontuação.

Nas linhas 11-22, para cada individuo iremos calcular a probabilidade dele ser escolhido numa escala de 0% à 100% e iremos criar uma lista de indivíduos repetidos pela quantidade correspondente à sua probabilidade. A variável **normalized_score** converte o intervalo das pontuações, do 0 até a melhor pontuação, em um intervalo de 0 à 1. Esse processo é muito comum na estatística e é conhecido como normalização. Já a variável **probability** escala a normalização para um número inteiro de 0 à 100, isso só para criar o vetor de _n_ posições desse indivíduo.

##  A Reprodução e a mutação
Agora que temos uma lista de candidatos proporcional à sua adaptabilidade, vamos gerar filhos. Essa técnica é comumente chamada de _crossover_ e consiste em misturar os genes de dois indivíduos selecionados ao acaso da nossa lista de reprodutores.

{{< highlight python3 "linenos=table" >}}
#Realiza o cruzamento dos genes
def crossover(parent_a, parent_b, target_size):

    child = list()
    midpoint = random.randint(target_size)

    for idx in range(target_size):
        if idx > midpoint:
            child.append(parent_a[idx])
        else:
            child.append(parent_b[idx])

    return child

#Realiza a mutação dos genes
def mutation(child, target_size, mutation_rate):

    for idx in range(target_size):
        if mutation_rate >= random.random():
            child[idx] = random.choice(genes_sample_space, 1)[0]

    return child

#2. Fase de Reprodução.
for idx in range(population_size):
    parent_a = mating_pool[random.randint(mating_pool_size)]
    parent_b = mating_pool[random.randint(mating_pool_size)]

    #2.1 Reproduz-se
    child = crossover(parent_a, parent_b, target_size)
    #3. Ocorre uma mutação.
    child = mutation(child, target_size, mutation_rate)

    #4. Substituímos a população pelo filho gerado.
    population_list[idx] = child
{{< /highlight >}}

A linha 3 corresponde a função **crossover** onde é responsável pelo cruzamento de genes de dois indivíduos, no caso, o indivíduo **parent_a** e **parent_b**. Na linha 6, define-se o ponto de corte do genes aleatoriamente. E por fim, das linhas 8-14 retorna-se um novo indivíduo, mescla de **parent_a** e **parent_b**.

Da linha 28-41, para cada indivíduo seleciona-se dois indivíduos aleatórios do conjunto de reprodutores e aplica-se a função **crossover**.

A partir disso, na linha 37, aplica-se a função **mutation**, responsável por ocasionar mutações genéticas de acordo com uma taxa de mutabilidade. Essa função é interessante pois apesar de querermos caminhar em direção a uma solução ótima, também gostaríamos de conservar uma variedade genética.

Após essas fases, na linha 41, substituímos o indivíduo pelo o que acabamos de gerar. Com isso, o ciclo dessa geração encerra-se e passamos pelas fases descritas, novamente.  

Abaixo segue todo o código em um único lugar.

{{< highlight python3 "linenos=table" >}}
import string
import numpy as np
from numpy import random


#Calcula o score de cada indivíduo
def fitness(individual, target, size):

    score = 0

    for idx in range(size):
        if individual[idx] == target[idx]:
            score += 1

    return 2**score

#Seleciona os índividuos que irão se reproduzir
def mate_selection(population_list, population_fitness, population_size, best_score):

    mates = list()

    for idx in range(population_size):
        individual = population_list[idx]

        #Normaliza a pontuação dos indivíduos para uma escala de 0 à 1.
        normalized_score = np.interp(population_fitness[idx], (0,best_score), (0,1))
        #Probabilidade do indivíduo ser escolhido.
        probability = int(normalized_score * 100)

        for n in range(probability):
            mates.append(individual)

    return mates

#Realiza o cruzamento dos genes
def crossover(parent_a, parent_b, target_size):

    child = list()
    midpoint = random.randint(target_size)

    for idx in range(target_size):
        if idx > midpoint:
            child.append(parent_a[idx])
        else:
            child.append(parent_b[idx])

    return child

#Realiza a mutação dos genes
def mutation(child, target_size, mutation_rate):

    for idx in range(target_size):
        if mutation_rate >= random.random():
            child[idx] = random.choice(genes_sample_space, 1)[0]

    return child


#Realiza o setup inicial dos dados necessários para começar utilizar o algoritmo
target = 'ser ou nao ser eis a questao'
population_size = 100
mutation_rate = 0.01
genes_sample_space = list(string.whitespace[0] + string.ascii_lowercase)

#Gera uma população inicial aleatória.
target_size = len(target)
population_list = list()

for n in range(population_size):
  individual = random.choice(genes_sample_space, target_size)
  population_list.append(individual)


while True:

    #Calcula a adaptação dos indivíduos.
    population_fitness = list()
    for idx in range(population_size):
        population_fitness.append(fitness(population_list[idx], target, target_size))

    #calcula o melhor score de toda população
    best_score = np.amax(population_fitness)

    #1. Seleciona os indivíduos que irão se reproduzir.
    mating_pool = mate_selection(population_list, population_fitness, population_size, best_score)
    mating_pool_size = len(mating_pool)

    #2. Fase de Reprodução.
    for idx in range(population_size):
        parent_a = mating_pool[random.randint(mating_pool_size)]
        parent_b = mating_pool[random.randint(mating_pool_size)]

        #2.1 Reproduz-se
        child = crossover(parent_a, parent_b, target_size)
        #3. Ocorre uma mutação.
        child = mutation(child, target_size, mutation_rate)

        #4. Substituimos a população pelo filho gerado.
        population_list[idx] = child

        print(''.join(population_list[idx]))
{{< /highlight >}}

E o output é:

{{< highlight python3 "linenos=table" >}}
r sdjxdfzeajkujjhnyzpknstbho
wayfyzvqgytoijwllrfwwe rjuyb
 rzedxdgcofyxs gil wjwmwtyfq
pmdbabjlkgqwib efxbqibzypart
eqvbeoufavpmnkqhdmuaxqubskpe
qjjgwgtioenua isvk zvqf fkko
r sdjxdyrsauakintwhowbxbj wd
webdeoufavpaokfcyimyakrtwmzd
nmymmwmhfjbrnjfqgqkyzoweakpe
sszezpybzvdpkmstkgpzxoyeptyj
.
.
.
.
ser ou njopser eis a questao
ser ousnvosser eis a questao
seryok n ofser eis a cuestao
ser ou n ofser eis a questao
ser ku njopser eis a questao
ser ou nhosser eis a questao
ser ou naosser eis a qugstao
ser ou naosser eis a questao
ser ou naosser eis a questao
ser ou nao ser eis a questao
{{< /highlight >}}


## Conclusão
Vimos que o algoritmo genético é uma forma de encontrarmos uma solução ideal dentre infinitas outras. Para uma versão implementada orientada a objetos considere olhar o link 5 na leitura adicional.

## Leitura Adicional
- [[1]](https://natureofcode.com/book/chapter-9-the-evolution-of-code/) Nature of Code: Chapter 9. The Evolution of Code.
- [[2]](https://en.wikipedia.org/wiki/Genetic_algorithm) Wikipedia: Genetic Algorithm
- [[3]](https://en.wikipedia.org/wiki/John_Henry_Holland) Wikipedia: John Henry Holland
- [[4]](http://www2.ica.ele.puc-rio.br/Downloads%5C38/CE-Apostila-Comp-Evol.pdf) Algoritmos Genéticos: Princípios e Aplicações por Marco Aurélio Cavalcanti Pacheco.
- [[5]](https://github.com/jfalves/Genetic-Algorithm-from-Scratch) JFAlves - Github - Genetic Algorithm from Scratch.
