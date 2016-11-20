# Redes Neurais

Refatorando um código de JS, cheio de `this`, que veio do C para módulos em Node.js, por exemplo essa função:

```js
this.Mutate = function(chromo)
{
    //traverse the chromosome and mutate each weight dependent
    //on the mutation rate
    for (i=0; i < chromo.length; ++i)
    {
        //do we perturb this weight?
        if (RandFloat() < this.m_dMutationRate)
        {
            //add or subtract a small value to the weight
            chromo[i] += (RandomClamped() * MAX_PERTUBATION);
        }
    }
    return chromo;
};
```

Na minha mão já virou:

```js
const RandFloat = () => Math.random() 
const RandomClamped = () => RandFloat() - RandFloat()
const Mutate = (chromo) => chromo.map((e, i) => (RandFloat() < 0.3)
                                    ? e += (RandomClamped() * 0.3)
                                    : e)

module.exports = Mutate
```

## Metodologia de Estudo

Como vocês já devem saber que sou meio louco por isso gosto de testar formas diferentes de aprender, eu já conhecia todo o conceito sobre [Redes Neurais]() mas nunca tinha feito nada com ela muito menos programado uma. Entretanto hoje, no meu aniversário 20/11, um broder meu e que provavelmente fará um curso sobre [Machine Learning]() na [Webschool.io]() próximo ano, pediu-me que o ajudasse a refatorar um pouco seu código pois ele está mais acostumado com C/C++ e claro eu fui com a maior sede ao pote.

Porém ainda estava meio nebuloso algumas coisas pois a nomenclatura que ele usou para seus parâmetros não fazia muito sentido, para mim. Então pensei:

> **Bora modularizar/atomizar essa porra toda aí depois eu estudo e só monto o programa usando essas funções!**

![](http://i.giphy.com/JwwYTeuOHhEB2.gif) 

## Atomic Design

> Você deve se questionar se eu sou retardado mental para querer aplicar Atomic Design em Redes Neurais neh?





Pois então pense aqui comigo...

O elemento básico de uma rede neural são os neurônios, pois são eles que fazem a conexão entre os dados, assim como no nosso cérebro, correto?

Sabendo disso podemos deduzir que eles são nossos [átomos](), beleza?!

Porém dentro das Redes Neurais cada neurônio possui pesos que serão usados para calcular sua saída, não vamos entrar nos detalhes técnicos agora, vamos nos ater ao conceito do Atomic Design.

Se o neurônio é nosso átomo e possui 1 ou mais pesos "dentro dele", esses pesos são o quê?


![quarks]()


Caso você não conheça a image acima ela representa os [Quarks]() conhecidos pela Física Quântica atualmente...

> O quark, na física de partículas, é uma partícula elementar e um dos dois elementos básicos que constituem a matéria (o outro é o lépton). Quarks se combinam para formar partículas compostas chamadas hádrons; os mais estáveis desse tipo são os prótons e os nêutrons, que são os principais componentes dos núcleos atômicos[1]. Devido a um fenômeno conhecido como confinamento, quarks nunca são diretamente observados ou encontrados isoladamente; 

*fonte: [https://pt.wikipedia.org/wiki/Quark](https://pt.wikipedia.org/wiki/Quark)*

Ligando uma coisa na outra podemos afirmar que esses pesos são os quarks, pois seu agrupamento que "forma" o átomo/neurônio.

> Até aí nada de novo, né? 
> 
> Mas então porque comecei falando disso?


Comecei por elas pois são os menores elementos que temos na Rede Neural e foi a primeira função que vi [aqui](https://github.com/suissa/estudos-sobre-inteligencia-artificial/blob/master/NN/CNeuralNet.js):

```js
function SNeuron(NumInputs){
    
    this.m_NumInputs = NumInputs + 1;
    this.m_vecWeight = [];
    
    //we need an additional weight for the bias hence the +1
    for (let i = 0; i < NumInputs + 1; ++i) {
        //set up the weights with an initial random value
        this.m_vecWeight[i] = 0.0;
    }
}
```

Como você deve ter visto no código original ele é cheiooooooo de `this` e como você sabe o `this` no JavaScript tem o valor de onde é chamado e não criado, mas beleza!

Então fui analisar ela antes de refatorar:

- entra NumInputs, número de inputs? 
- crio m_vecWeight como array vazio
- popula o array, do tamanho de NumInputs com 0.0

Porém antes de refatorar eu fui tirar algumas dúvidas com o nosso professor de IA da Webschool e perguntei se NumInputs era o número de entradas da Rede Neural e ele me disse que não, que na verdade **é a quantidade de pesos do neurônio!** Fora isso ainda me disse que os pesos **não podem ser 0!**

Pensei:

> POORRRA! Pq não faz o esquema do jeito então?

A primeira coisa que fiz foi modularizar essa função mudando `NumInputs` para `amountWeights` e colocar um valor padrão como randômico:

```js
module.exports = (amountWeights, defaultValue=Math.random()) => 
  Array.from({ length: amountWeights }, () => defaultValue)
```


