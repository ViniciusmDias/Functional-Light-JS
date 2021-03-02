# Functional-Light JavaScript

# Capítulo 2: A Natureza Das Funções

Programação Funcional **não é apenas programar com a palavra `function`**. Oh, se fosse assim tão fácil -- Eu poderia terminar o livro aqui mesmo! No entanto, as funções realmente _estão_ no centro da PF. E é como usamos funções que torna o nosso código _funcional_.

Mas você tem certeza de que sabe o que realmente significa _função_?


Neste capítulo, vamos preparar as bases para o resto do livro, explorando todos os aspectos fundamentais das funções. Na verdade, isto é uma revisão de todas as coisas que até mesmo um programador não funcional deve saber sobre funções. Mas certamente, se quisermos obter o máximo dos conceitos da PF, é essencial _conhecermos_ funções por dentro e por fora.

Prepare-se, porque funções podem ser muito mais do que você possa ter imaginado.

## O que são Funções?

A pergunta "O que é uma função?" superficialmente parece ter uma resposta óbvia: uma função é uma porção de código que pode ser executada uma ou mais vezes.

Embora esta definição seja razoável, está faltando uma essência muito importante, que é o core de como uma _função_ se aplica a PF. Então, vamos ir mais fundo, abaixo da superfície, para entender as funções de forma mais completa.

### Breve Revisão de Matemática

Eu sei que prometi que ficaríamos longe da matemática o máximo possível, mas tenha paciência comigo por um momento enquanto observamos rapidamente algumas coisas fundamentais sobre funções e gráficos de álgebra antes de prosseguirmos.

Você se lembra de ter aprendido alguma coisa sobre `f(x)` na escola? E quanto à equação `y = f(x)`?


Vamos dizer que uma equação seja definida assim: `f(x) = 2x<sup>2</sup> + 3`. O que isso significa? O que significa representar graficamente essa equação? Aqui está o gráfico:

<p align="center">
    <img src="images/fig1.png" width="40%">
</p>

O que você pode notar é que para qualquer valor `x`, digamos `2`, se você inserir na equação, obterá `11`. O que é `11`, entretanto? É o _valor de retorno_ da função `f(x)`, que dissemos anteriormente que representa um valor `y`.

Em outras palavras, podemos escolher interpretar os valores de entrada e saída como um ponto em `(2,11)` naquela curva no gráfico. E para cada valor de `x` que inserimos, obtemos outro valor de `y` que emparelha com ele como uma coordenada para um ponto. Outro é `(0,3)`, e outro é `(-1,5)`. Junte todos esses pontos e você terá o gráfico dessa curva parabólica conforme mostrado aqui.

Então o que isso tem a ver com PF?

Em matemática, uma função sempre recebe entrada(s) e sempre fornece uma saída. Um termo que você ouvirá frequentemente em torno da PF é "morfismo". Essa é uma maneira sofisticada de descrever um conjunto de valores que mapeia para outro conjunto de valores, como as entradas de uma função são relacionadas às saídas dessa função.


Em matemática algébrica, essas entradas e saidas são frequentemente interpretadas como componentes de coordernadas a serem representadas graficamente. Em nossos programas, no entanto, podemos definir funções com todos os tipos de entrada(s) e saída(s), embora raramente sejam interpretadas como uma curva desenhada visualmente em um gráfico.

### Função vs Procedimento


Então, por que tanta conversa sobre matemática e gráficos? Porque essencialmente a Programação Funcional envolve o uso de _funções_ neste sentido matemático.


Você pode estar mais acostumado a pensar nas funções como procedimentos. Qual é a diferença? Um procedimento é uma coleção arbitrária de funcionalidades. Pode ter entradas, pode não ter. Poder ter uma saída (valor de `retorno`), ou não.

Uma função recebe entrada(s) e definitivamente sempre tem um valor de `retorno`.

Se você planeja utilizar Programação Funcional, **deve usar as funções tanto quanto possível** e tentar evitar procedimentos sempre que possível. Todas as suas `funções` devem receber entrada(s) e retornar saída(s).

Por quê? A resposta para essa pergunta terá muitos níveis de significado que descobriremos ao longo deste livro.

## Entrada da Função

Até agora, podemos concluir que as funções devem esperar uma entrada. Mas vamos examinar como funcionam as entradas da função.

Às vezes, você ouve as pessoas se referirem a essas entradas como "argumentos" e às vezes como "parâmetros". Então, do que se trata?

_Argumentos_ são os valores que você passa e _parâmetros_ são as variáveis nomeadas dentro da função que recebe esses valores passados. Exemplo:

```js
function foo(x, y) {
    // ..
}

var a = 3;

foo(a, a * 2);
```

`a` e `a * 2` (na verdade, o resultado de `a * 2`, que é `6`) são os _argumentos_ para a chamada `foo(..)`. `x` e `y` são os _parâmetros_ que recebem os valores do argumento (`3` e `6`, respectivamente).

**Nota:** Em JavaScript, não há requisito de que o número de _argumentos_ corresponda ao número de _parâmetros_. Se você passar mais _argumentos_ do que declarou de _parâmetros_ para recebê-los, os valores seguem intocados. Esses valores podem ser acessados de algumas maneiras diferentes, incluindo o objeto antigo `arguments`, do qual você já deve ter ouvido falar. Se você passar menos _argumentos_ do que os _parâmetros_ declarados, cada parâmetro não correspondido é tratado como uma variável "indefinida", o que significa que está presente e disponível no escopo da função, mas apenas começão com o valor vazio `undefined`.

### Parâmetros padrão

A partir do ES6, os parâmetros podem declarar _valores padrão_. No caso em que o argumento para aquele parâmetro não é passado, ou é passado o valor `undefined`, a expressão de atribuição padrão é substituída.

Considere:

```js
function foo(x = 3) {
    console.log(x);
}

foo(); // 3
foo(undefined); // 3
foo(null); // null
foo(0); // 0
```

É sempre uma boa prática pensar nos casos padrões que podem ajudar na usabilidade de suas funções. No entanto, parâmetros padrões podem trazer mais complexidade em termos de leitura e compreensão das varias maneiras de como uma função é chamada. Seja criterioso sobre o quanto você confia nesse recurso.

### Contando Entradas

O número de argumentos que uma função "espera" -- quantos argumentos você provavelmente deseja passar para ela -- é determinado pelo número de parâmetros que são declarados:

```js
function foo(x, y, z) {
    // ..
}
```

`foo(..)` _espera_ três argumentos, porque tem três parâmetros declarados. Essa contagem possui um termo especial: aridade. Aridade é o número de parâmetros em uma declaração de função. A aridade de `foo(..)` é `3`.

Além disso, uma função com aridade 1 também é chamada de "unária", uma função com aridade 2 também é chamada de "binária" e uma função com aridade 3 ou superior é chamada de "n-ária".

Você pode querer inspecionar uma referência de função durante o tempo de execução de um programa para determinar sua aridade. Isso pode ser feito com a propriedade `lenght` dessa referência de função:


```js
function foo(x, y, z) {
    // ..
}

foo.length; // 3
```

Uma razão para determinar a aridade durante a execução seria se um trecho de código recebesse uma referência de função de várias fontes e enviasse valores diferentes dependendo da aridade de cada uma delas.

Por exemplo, imagine um caso onde uma referência de função `fn` poderia esperar um, dois ou três argumentos, mas você sempre quer apenas passar uma variável `x` na última posição:

```js
// `fn` está definido para alguma referência de função
// `x` existe com algum valor

if (fn.length == 1) {
    fn(x);
} else if (fn.length == 2) {
    fn(undefined, x);
} else if (fn.length == 3) {
    fn(undefined, undefined, x);
}
```


**Dica:** A propriedade `length` de uma função é somente para leitura e é determinada no momento em que você declara a função. Deve ser considerado essencialmente como um pedaço de metadados que descreve algo sobre a intenção de uso da função.

Um problema a ter em conta é que certos tipos de variações da lista de parâmetros podem modificar a propriedade `length` do relatório de função, para algo diferente do que você está esperando.

```js
function foo(x, y = 2) {
    // ..
}

function bar(x, ...args) {
    // ..
}

function baz({ a, b }) {
    // ..
}

foo.length; // 1
bar.length; // 1
baz.length; // 1
```

Que tal contar o número de argumentos que a chamada de função atual recebeu? Isso já foi trivial, mas agora a situação é um pouco mais complicada. Cada função tem um objeto com `argumentos` (semelhante a um array) disponível que contém uma referência a cada um dos argumentos passados. Você pode então inspecionar a propriedade `length` dos `argumentos` para descobrir quantos realmente foram passados:

```js
function foo(x, y, z) {
    console.log(arguments.length);
}

foo(3, 4); // 2
```

A partir do ES5 (e do modo estrito, especificamente), `argumentos` são considerados por alguns como obsoletos. Muitos evitam usá-lo, se possível. Em JS, nós "nunca" quebramos a compatibilidade com versões anteriores, não importa o quão útil isso possa ser para o progresso futuro, então `argumentos` nunca serão removidos. Mas agora é comumente sugerido que você evite usá-lo sempre que possível.

No entando, sugiro que `arguments.length`, e apenas isso, pode continuar a ser usado para aqueles casos em que você precisa se preocupar com o número de argumentos transmitidos. Uma versão futura do JS pode possivelmente adicionar um recurso que oferece a capacidade de determinar o número de argumentos passados sem consultar `arguments.length`, se isso acontecer, podemos descartar totalmente o uso de `argumentos`!

Seja cuidadoso: **nunca** acesse argumentos posicionalmente, como `arguments[1]`. Atenha-se apenas a `arguments.length`, e somente se for necessário.

Como você acessará um argumento que foi passado em um posição além dos parâmetros declarados? Já vou responder, mas primeiro, dê um passo para trás e pergunte-se: "Por que eu faria isso?" Seriamente. Pense nisso por um minuto.

Deve ser muito raro que isso ocorra, não deve ser algo que você regularmente espera ou confia ao escrever suas funções. Se você se encontrar em tal cenário, gaste 20 minutos extras tentando projetar a interação com essa função de uma maneira diferente. Nomeie esse argumento extra, mesmo que seja excepcional.

Uma assinatura de função que aceita uma quantidade indeterminada de argumentos é chamada de função variável. Algumas pessoas preferem esse estilo de design de função, mas acho que você descobrirá que muitas vezes o Programador Funcional tenta evitá-lo sempre que possível.

Ok, chega de insistência nesse ponto.

Digamos que você precise acessar os argumentos de uma forma semelhante a um array posicional, possivelmente porque está acessando um argumento que não possui um parâmetro formal nessa posição. Como fazemos isso?

ES6 ao resgate! Vamos declarar nossa função com o operador `...` - conhecido como "spread", "rest", ou (como eu prefiro) "gather":

```js
function foo(x, y, z, ...args) {
    // ..
}
```

Vê o `...args` na lista de parâmetros? Essa é uma forma declarativa do ES6 que diz ao computador para coletar (com, "gather") todos os argumentos restantes (se houver) não atribuídos aos parâmetros nomeados e colocá-los em um array chamado `args`. `args` sempre será um array, mesmo se estiver vazio. Mas **não incluirá** valores atribuídos aos parâmetros `x`, `y` e `z`, apenas qualquer outra coisa que seja passada além desses três primeiros valores:

```js
function foo(x, y, z, ...args) {
    console.log(x, y, z, args);
}

foo(); // undefined undefined undefined []
foo(1, 2, 3); // 1 2 3 []
foo(1, 2, 3, 4); // 1 2 3 [ 4 ]
foo(1, 2, 3, 4, 5); // 1 2 3 [ 4, 5 ]
```

Então, se você _realmente_ deseja projetar uma função que possa levar em conta um número arbitrário de argumentos a serem passados, use `...args` (ou qualquer nome que você quiser) no final. Agora, você terá uma matriz real, não obsoleta e não nojenta para acessar esses valores de argumento.

Apenas preste atenção ao fato de que o valor `4` está na posição `0` daquele `args`, não na posição `3`. E seu valor de `length` não incluirá esses três valores `1`, `2` e `3`. `...args` reúne todo o resto, não incluindo `x`, `y` e `z`.

Você _pode_ usar o operador `...` na lista de parâmetros mesmo se não houver outros parâmetros formais declarados:

```js
function foo(...args) {
    // ..
}
```

Agora `args` será o array completo de argumentos, quaisquer que sejam, e você pode usar `args.length` para saber exatamente quantos argumentos foram passados. E você está seguro para usar `args[1]` ou `args[317]` se você escolher. Porém, não passe 318 argumentos.

### Arrays de Argumentos

E se você quisesse passar um array de valores como argumentos para uma chamada de função?

```js
function foo(...args) {
    console.log(args[3]);
}

var arr = [1, 2, 3, 4, 5];

foo(...arr); // 4
```

Nosso novo amigo `...` é usado, mas agora não somente na lista de parâmetros, também é usado na lista de argumentos no call-site. Ele tem o comportamento oposto neste contexto. Em uma lista de parâmetros, dissemos que ela _agrupa_ os argumentos. Em uma lista de argumentos, ela os _espalha_. Portanto, o conteúdo de `arr` é realmente espalhado como argumentos individuais para a chamada `foo(..)`. Você vê como isso é diferente de apenas passar uma referência para todo o array `arr`?

A propósito, vários valores e distribuições `...` podem ser intercalados, como você achar necessário:

```js
var arr = [2];

foo(1, ...arr, 3, ...[4, 5]); // 4
```

Pense em `...` neste sentido simétrico: em uma posição da lista de valores, ele _espalha_. Em uma posição de atribuição, como uma lista de parâmetros, porque os argumentos são _reduzidos a_ parâmetros, ele _agrupa_.

Qualquer que seja o comportamento que você invocar, `...` torna o trabalho com arrays de argumentos muito mais fácil. Já se foram os dias de `slice(..)`, `concat(..)` e `apply(..)` para brigar por nossos valores de argumentos nos arrays.

**Dica:** Na verdade, esses métodos não são totalmente inúteis. Haverá alguns lugares onde dependemos deles ao longo do código deste livro. Mas certamente na maioria dos lugares, `...` será muito mais legível declarativamente e, como resultado, preferível.

### Destructuração de Parâmetros

Considere a variável `foo(..)` da seção anterior:

```js
function foo(...args) {
    // ..
}

foo(...[1, 2, 3]);
```

E se quiséssemos mudar essa interação para que o chamador de nossa função passe um array de valores em vez de valores individuais de argumentos? Basta descartar os dois usos `...`:

```js
function foo(args) {
    // ..
}

foo([1, 2, 3]);
```

Simples o suficiente. Mas e se agora quiséssemos dar um nome de parâmetro a cada um dos primeiros dois valores passados no array? Não declaramos mais parâmetros individuais, então parece que perdemos essa capacidade.

Felizmente, a desestruturação do ES6 é a resposta. A desestruturação é uma maneira de declarar um _padrão_ para o tipo de estrutura (objeto, array, etc.) que você espera ver e como a decomposição (atribuição) de suas partes individuais deve ser processada.

Considere:

<a name="funcparamdestr"></a>

```js
function foo([x, y, ...args] = []) {
    // ..
}

foo([1, 2, 3]);
```

Você identificou os colchetes `[ .. ]` em torno da lista de parâmetros agora? Isso é chamado de desestruturação de parâmetros de matriz.

Neste exemplo, a desestruturação informa ao mecanismo que um array é esperado nesta posição de atribuição (também conhecido como parâmetro). O padrão diz para pegar o primeiro valor desse array e atribuir a uma variável de parâmetro local chamada `x`, o segundo para `y` e o que sobrar _agrupar_ em `args`.

### A Importância do Estilo Declarativo

Considerando o `foo(..)` desestruturado que acabamos de ver, poderíamos ter processado os parâmetros manualmente:

```js
function foo(params) {
    var x = params[0];
    var y = params[1];
    var args = params.slice(2);

    // ..
}
```

Mas aqui destacamos um princípio que apresentamos apenas brevemente no [Capítulo 1](ch1.md/#readability): o código declarativo se comunica com mais eficácia do que o código imperativo.

O código declarativo (por exemplo, a desestruturação no fragmento `foo(..)` anterior ou nos usos do operador `...`) foca em qual deve ser o resultado de um trecho de código.

O código imperativo (como as atribuições manuais no último fragmento) se concentra mais em como obter o resultado. Se, posteriormente, você ler esse código imperativo, terá de executá-lo mentalmente para entender o resultado desejado. O resultado está _codificado_ lá, mas não é tão claro porque é obscurecido pelos detalhes de _como_ chegamos lá.

O `foo(..)` anterior é considerado mais legível, porque a desestruturação oculta os detalhes desnecessários de _como_ gerenciar as entradas dos parâmetros; o leitor está livre para se concentrar apenas em _o_ que faremos com esses parâmetros. Essa é claramente a preocupação mais importante, portanto, é nisso que o leitor deve se concentrar para entender o código de forma mais completa.

Sempre que possível e em todos os graus que nossa linguagem e nossas bibliotecas/frameworks permitem, **devemos nos esforçar para obter um código declarativo e autoexplicativo**.

## Argumentos Nomeados

Assim como podemos desestruturar os parâmetros do array, podemos desestruturar os parâmetros do objeto:

```js
function foo({ x, y } = {}) {
    console.log(x, y);
}

foo({
    y: 3,
}); // undefined 3
```

Passamos um objeto como o único argumento, e ele é desestruturado em duas variáveis de parâmetro separadas `x` e `y`, que são atribuídos aos valores dos nomes de propriedade correspondentes do objeto passado. Não importava se a propriedade `x` não estava no objeto; acabou sendo uma variável `undefined` como você esperava. 

Mas a parte da desestruturação do parâmetro do objeto que eu quero que você preste atenção é o objeto que está sendo passado para `foo(..)`.

Com uma call-site normal como `foo(undefined,3)`, a posição é usada para mapear de argumento para parâmetro, colocamos `3` na segunda posição para atribuí-lo a um parâmetro `y`. Mas neste novo tipo de call-site onde a desestruturação de parâmetro está envolvida, uma propriedade de objeto simples indica a qual parâmetro (`y`) o valor do argumento `3` deve ser atribuído.

Não tivemos que contabilizar `x` _naquela_ call-site porque, na verdade, não nos importamos com `x`. Nós apenas o omitimos, em vez de ter que fazer algo pertubador, como passar `undefined` para marcar a posição.

Algumas linguagens têm um recurso explícito para isso: argumentos nomeados. Em outras palavras, no call-site, rotulando um valor de entrada para indicar para qual parâmetro ele mapeia. JavaScript não tem argumentos nomeados, mas a desestruturação do objeto de parâmetro é a coisa mais próxima disso.

Outro benefício relacionado a PF onde usamos uma desestruturação de objeto para passar argumentos potencialmente múltiplos é que uma função que leva apenas um parâmetro (o objeto) é muito mais fácil de compor com a única saída de outra função. Mais sobre isso no [Capítulo 4](ch4.md).

### Parâmetros Não Odernados

Outra benefício importante é que os argumentos nomeados, em virtude de serem especificados como propriedade do objetos, não são fundamentalmente ordenados. Isto quer dizer que podemos especificar as entradas na ordem que quisermos:

```js
function foo({ x, y } = {}) {
    console.log(x, y);
}

foo({
    y: 3,
}); // undefined 3
```

Estamos pulando o parâmetro `x` simplesmente omitindo-o. Ou poderíamos especificar um argumento `x` se quiséssemos, mesmo se o listássemos após `y` no literal do objeto. O call-site não é mais desordenado por marcadores de posição como `undefined` para pular um parâmetro.

Argumentos nomeados são muito mais flexíveis e atraentes do ponto de vista da legibilidade, especialmente quando a função em questão pode receber três, quatro ou mais entradas.

**Dica:** Se este estilo de argumentos de função parece útil ou interessante para você, verifique a cobertura da minha [biblioteca FPO no Apêndice C](apC.md/#bonus-fpo).

## Saída de função

Vamos mudar nossa atenção das entradas de uma função para a sua saída.

Em JavaScript, as funções sempre retornam um valor. Todas essas três funções têm o mesmo comportamento no `return`:

```js
function foo() {}

function bar() {
    return;
}

function baz() {
    return undefined;
}
```

O valor `undefined` é implicitamente `retornado`se você não tiver um `return` ou se vocẽ tiver apenas uma `return` vazio. 

Mas mantendo o máximo possível com o espírito de definição de PF -- usando funções e não procedimentos -- nossas funções devem sempre ter saídas, o que significa que elas devem `retornar` explicitamente um valor, e não `undefined`.

Uma instrução `return` só pode retornar um único valor. Portanto, se sua função precisa retornar vários valores, sua única opção viável é coletá-los em um valor composto, como um array ou um objeto:

```js
function foo() {
    var retValue1 = 11;
    var retValue2 = 31;
    return [retValue1, retValue2];
}
```

Em seguida, atribuiremos `x` e `y` de dois respectivos itens no array que vem de `foo()`

```js
var [x, y] = foo();
console.log(x + y); // 42
```

Coletar vários valores em uma matriz (ou objeto) para retornar e, subsequentemente, desestruturar esses valores de volta em atribuições distintas, é uma maneira de expressar de forma transparente várias saídas para uma função.

**Dica:** Eu seria negligente se não sugerisse que você parasse um momento para considerar se uma função que precisa de várias saídas poderia ser refatorada para evitar isso, talvez separada em duas ou mais funções menores de propósito único. Às vezes isso será possível, às vezes não, mas você deve pelo menos considerar isso.

### Retornos Antecipados

A instrução `return` não retorna apenas um valor de uma função. É também uma estrutura de controle de fluxo; ele termina a execução da função nesse ponto. Uma função com várias instruções `return`, portanto, tem vários pontos de sáida possíveis, o que significa que pode ser mais difícil ler uma função para entender seu comportamento de saída se houver muitos caminhos que poderiam produzir essa saída.

Considere:

```js
function foo(x) {
    if (x > 10) return x + 1;

    var y = x / 2;

    if (y > 3) {
        if (x % 2 == 0) return x;
    }

    if (y > 1) return y;

    return x;
}
```

Questionário rápido: sem trapacear e rodar este código em seu navegador, o que `foo(2)` retorna? E sobre `foo(4)`? E `foo(8)`? E `foo(12)`?

Você está confiante em suas respostas? Quanto da sua mente você gastou para obter essas respostas? Eu entendi errado nas duas primeiras vezes que tentei pensar sobre e escrevi!

Eu acho que parte do problema de legibilidade aqui é que estamos usando `return` não apenas para retornar valores diferentes, mas também como uma construção de controle de fluxo para encerrar a execução de uma função mais cedo em certos casos. Obviamente, existem maneiras melhores de escrever esse controle de fluxo (a lógica `if`, etc.), mas também, acho que existem maneiras de tornar os caminhos de saída mais óbvios.

**Observação:** As respostas do questionário rápido são `2`, `2`, `8` e `13`.

Considere esta versão do código:

```js
function foo(x) {
    var retValue;

    if (retValue == undefined && x > 10) {
        retValue = x + 1;
    }

    var y = x / 2;

    if (y > 3) {
        if (retValue == undefined && x % 2 == 0) {
            retValue = x;
        }
    }

    if (retValue == undefined && y > 1) {
        retValue = y;
    }

    if (retValue == undefined) {
        retValue = x;
    }

    return retValue;
}
```

Esta versão é inquestionávelmente mais verbosa. Mas eu diria que é uma lógica mais simples de seguir, porque cada branch onde `retValue` pode ser definido é _guardado_ pela condição que verifica se ele já foi definido antes.

Em vez de usar `return` antecipadamente na função, usamos o controle de fluxo normal (`if` lógico) para determinar a atribuição de `retValue`'s. No final, simplesmente `retornamos retValue`.

Não estou dizendo incondicionalmente que você deve sempre ter um único `return`, ou que você nunca deve usar `return` antecipado, mas eu acho que você deve ter cuidado com a parte de controle de fluxo do `return` criando mais implicitude em suas definições de função. Tente descobrir a maneira mais explícita de expressar a lógica, essa geralmente será a melhor maneira.

### Saídas não retornadas

Uma técnica que você provavelmente usou na maior parte do código que você tem escrito e talvez, não tenha pensado muito nisso, é fazer com que uma função produza alguns ou todos os seus valores simplesmente alterando as variáveis de fora dela.

Lembra-se de nossa função <code>f(x) = 2x<sup>2</sup> + 3</code> do início do capítulo? Poderíamos ter definido assim em JS:

```js
var y;

function f(x) {
    y = 2 * Math.pow(x, 2) + 3;
}

f(2);

y; // 11
```

Eu sei que este é um exemplo bobo; Poderíamos facilmente ter um `return` do valor em vez de defini-lo como `y` de dentro da função:

```js
function f(x) {
    return 2 * Math.pow(x, 2) + 3;
}

var y = f(2);

y; // 11
```

Ambas as funções realizam a mesma tarefa, então há algum motivo para escolhermos uma versão em vez da outra? **Sim, absolutamente.**

Uma maneira de explicar a diferença é que o `return` na última versão sinaliza uma saída explícita, enquanto a atribuição `y` na primeira versão é uma saída implícita. Você pode já ter alguma intuição que o orienta nesses casos; Normalmente, os desenvolvedores preferem padrões explícitos aos implícitos.

Mas alterar uma variável em um escopo externo, como fizemos com a atribuição `y` dentro de `foo(..)`, é apenas uma maneira de obter uma saída implícita. Um exemplo mais sutil é fazer alterações em valores não locais por meio de referência.

Considere:

```js
function sum(list) {
    var total = 0;
    for (let i = 0; i < list.length; i++) {
        if (!list[i]) list[i] = 0;

        total = total + list[i];
    }

    return total;
}

var nums = [1, 3, 9, 27, , 84];

sum(nums); // 124
```

A saída mais óbvia dessa função é a soma `124`, que explicitamente `retornamos`. Mas você identifica a outra saída? Coloque esse código em prática e inspecione o `nums` do array. Agora você percebe a diferença? 

Em vez de um valor de slot vazio `undefined` na posição `4`, agora há um `0`. A operação `list[i] = 0` de aparência inofensiva acabou afetando o valor do array do lado de fora, embora operássemos em uma variável de parâmetro `lista` local.

Por quê? Porque a `lista` contém uma cópia-referência da referência `nums`, não uma cópia-valor do valor do array `[1,3,9,..]`. JavaScript usa referências e cópias de referência para arrays, objetos e funções, portanto, podemos criar uma saída acidental de nossa função com muita facilidade.

Essa saída de função implícita tem um nome especial no mundo da PF: efeitos colaterais. E uma função que _não tem efeitos colaterais_ também tem um nome especial: função pura. Falaremos muito mais sobre isso no [Capítulo 5](ch5.md), mas o ponto principal é que queremos preferir funções puras e evitar efeitos colaterais sempre que possível.

## Funções de Funções

Funções podem receber e retornar valores de qualquer tipo. Uma função que recebe ou retorna uma ou mais outros valores de função tem um especial nome: funções de ordem superior.

Considere:

```js
function forEach(list, fn) {
    for (let v of list) {
        fn(v);
    }
}

forEach([1, 2, 3, 4, 5], function each(val) {
    console.log(val);
});
// 1 2 3 4 5
```

`forEach(..)` é uma função de ordem superior porque recebe uma função como um argumento.

Uma função de ordem superior também pode gerar outra função, como:

```js
function foo() {
    return function inner(msg) {
        return msg.toUpperCase();
    };
}

var f = foo();

f("Hello!"); // HELLO!
```

`return` não é a única maneira de "produzir" uma função interna:

```js
function foo() {
    return bar(function inner(msg) {
        return msg.toUpperCase();
    });
}

function bar(func) {
    return func("Hello!");
}

foo(); // HELLO!
```

As funções que tratam outras funções como valores são funções de ordem superior por definição. Os programadores funcionais escrevem isso o tempo todo!

### Mantendo o Escopo

Uma das coisas mais poderosas em toda a programação, e especialmente na PF, é como uma função se comporta quando está dentro do escopo de outra função. Quando a função interna faz referência a uma variável da função externa, isso é chamado de closure.

Definido pragmaticamente:

> Closure é quando uma função lembra e acessa variáveis de fora de seu próprio escopo, mesmo quando essa função é executada em um escopo diferente.

Considere:

```js
function foo(msg) {
    var fn = function inner() {
        return msg.toUpperCase();
    };

    return fn;
}

var helloFn = foo("Hello!");

helloFn(); // HELLO!
```

A variável de parâmetro `msg` no escopo de `foo(..)` é referenciada dentro da função interna. Quando `foo(..)` é executado e a função interna é criada, ele captura o acesso à variável `msg` e retém esse acesso mesmo depois de ser `retornado`.

Uma vez que temos `helloFn`, uma referência à função interna, `foo(..)` terminou e parece que seu escopo deveria ter desaparecido, significando que a variável `msg` não existiria mais. Mas isso não acontecen, porque a função interna tem uma closure sobre `msg` que a mantém viva. A variável closure sobre `msg` sobrevive enquanto a função interna (agora referenciada por `helloFn` em um escopo diferente) permanece. 

Vejamos mais alguns exemplos das closures em ação: 

```js
function person(name) {
    return function identify() {
        console.log(`I am ${name}`);
    };
}

var fred = person("Fred");
var susan = person("Susan");

fred(); // I am Fred
susan(); // I am Susan
```

A função interna `identify()` tem closure sobre o parâmetro `nome`.

O acesso que a closure permite não se restringe apenas à leitura do valor original da variável -- não é apenas um instantâneo, mas um link ativo. Você pode atualizar o valor e esse novo estado atual permanece lembrado até o próximo acesso:

```js
function runningCounter(start) {
    var val = start;

    return function current(increment = 1) {
        val = val + increment;
        return val;
    };
}

var score = runningCounter(0);

score(); // 1
score(); // 2
score(13); // 15
```

**Aviso:** Por razões que exploraremos com mais profundidade posteriormente neste livro, este exemplo de uso de closure para lembrar um estado que muda (`val`) é provavelmente algo que você deve evitar sempre que possível.

Se você tiver uma operação que precisa de duas entradas, uma das quais você conhece agora, mas a outra será especificada mais tarde, você pode usar closure para lembrar a primeira entrada:

```js
function makeAdder(x) {
    return function sum(y) {
        return x + y;
    };
}

// we already know `10` and `37` as first inputs, respectively
var addTo10 = makeAdder(10);
var addTo37 = makeAdder(37);

// later, we specify the second inputs
addTo10(3); // 13
addTo10(90); // 100

addTo37(13); // 50
```

Normalmente, uma função `sum(..)` tomaria ambas as entradas `x` e `y` para adicioná-los. Mas, neste exemplo, recebemos e lembramos (por meio de closure) o(s) valor(es) de `x` primeiro, quanto o(s) valor(es) `y` são especificados separadamente mais tarde.

**Nota:** Esta técnica de especificar entradas em chamadas de função sucessiva é muito comum em PF e vem em duas formas: aplicação parcial e currying. Vamos mergulhar neles mais profudamente no [Capítulo 3](ch3.md/#some-now-some-later). 

Obviamente, como as funções são apenas valores em JS, podemos lembrar os valores das funções por meio de closure:

```js
function formatter(formatFn) {
    return function inner(str) {
        return formatFn(str);
    };
}

var lower = formatter(function formatting(v) {
    return v.toLowerCase();
});

var upperFirst = formatter(function formatting(v) {
    return v[0].toUpperCase() + v.substr(1).toLowerCase();
});

lower("WOW"); // wow
upperFirst("hello"); // Hello
```

Em vez de distribuir/repetir as lógicas `toUpperCase()` e `toLowerCase()` em todo o nosso código, a PF nos incentiva a criar funções simples que encapsulam -- uma maneira elegante de dizer empacotando -- esse comportamento.

Especificamente, criamos duas funções unárias simples `lower(..)` e `upperFirst(..)`, porque essas funções serão muito mais fáceis de conectar para trabalhar com outras funções no resto do nosso programa.

**Dica:** Você percebeu como `upperFirst(..)` poderia ter usado `lower(..)`?

Usaremos muito as closures no restante do texto. Pode ser apenas a prática fundamental mais importante em todo a FP, se não a programação como um todo. Certifique-se de que você está realmente confortável com isso!

## Sintaxe

Antes de prosseguirmos com esta introdução sobre funções, vamos discutir um pouco sobre sua sintaxe.

Mais do que muitas outras partes deste texto, as discussões nesta seção são principalmente opiniões e preferências, quer você concorde com as visões apresentadas aqui ou opte por outras. Essas idéias são altamente subjetivas, embora muitas pessoas acreditam nessas idéias.

Em última análise, você decide.

### O que há em um nome?

Sintaticamente falando, as declarações de função precisão incluir um nome:

```js
function helloMyNameIs() {
    // ..
}
```

Mas as expressões de função podem vir nas formas nomeadas e anônimas:


```js
foo(function namedFunctionExpr() {
    // ..
});

bar(function () {
    // <-- look, no name!
    // ..
});
```

O que exatamente queremos dizer com anônimo, a propósito? Especificamente, as funções tem uma propriedade `nome` que contém o valor da string do nome que a função foi dada sintaticamente, como `"helloMyNameIs"` ou `"namedFunctionExpr"`. Esta propriedade `nome` é mais notavelmente usada pelas ferramentas de console/desenvolvedor de seu ambiente JS para listar a função quando ela participa de um rastreamento de pilha (geralmente de uma exceção).

As funções anônimas são geralmente exibidas como `(função anônima)`.

Se você ja teve que debugar um programa JS com nada além de um rastreamento de pilha de uma exceção, provavelmente sentiu a dor de ver `(função anônima)` aparecer linha após linha. Esta lista não da ao desenvolvedor nenhuma pista quanto ao caminho de onde veio a exceção. Ela não está fazendo nenhum favor ao desenvolvedor.

Se você nomear suas expressões de função, o nome será sempre usado. Portanto, se você usar um bom nome como `handleProfileClicks` em veze de `foo`, você obterá rastreamentos de pilha muito mais úteis. 

A partir do ES6, as expressões de função anônima são, em certos casos, auxiliadas por _inferência de nome_.

Considere:

```js
var x = function () {};

x.name; // x
```

Se a máquina for capaz de adivinhar que nome você _provavelmente_ deseja que a função assuma, ela o fará.

Mas cuidado, nem todas as formas sintáticas se beneficiam da inferência de nomes. Provavelmente o lugar mais comum em que uma expressão de função aparece é como argumento para uma chamada de função:

```js
function foo(fn) {
    console.log(fn.name);
}

var x = function () {};

foo(x); // x
foo(function () {}); //
```

Quando o nome não pode ser inferido dentro dos parentêses, ele permanece uma string vazia. Tal função será relatada como `(função anônima)` em um rastreamento de pilha, caso ocorra.

Existem outros benefícios em um função sendo nomeada, além da questão de depuração. Primeiro, o nome sintático (também conhecido como nome léxico) é útil para auto referência interna. A auto referência é necessária para recursão (em sincronismo e assincronismo) e também é útil com manipuladores de eventos.

Considere estes diferentes cenários:

```js
// sync recursion:
function findPropIn(propName, obj) {
    if (obj == undefined || typeof obj != "object") return;

    if (propName in obj) {
        return obj[propName];
    } else {
        for (let prop of Object.keys(obj)) {
            let ret = findPropIn(propName, obj[prop]);
            if (ret !== undefined) {
                return ret;
            }
        }
    }
}
```

```js
// async recursion:
setTimeout(function waitForIt() {
    // does `it` exist yet?
    if (!o.it) {
        // try again later
        setTimeout(waitForIt, 100);
    }
}, 100);
```

```js
// event handler unbinding
document.getElementById("onceBtn").addEventListener(
    "click",
    function handleClick(evt) {
        // unbind event
        evt.target.removeEventListener("click", handleClick, false);

        // ..
    },
    false
);
```

Em todos esses casos, o nome léxico da função nomeada era uma auto-referência útil e confiável de dentro dela mesma. 

Além disso, mesmo em casos simples com funções de uma linha, nomeá-los tende a tornar o código mais auto explicativo e, portanto, mais fácil de ler para quem nunca o leu antes:

```js
people.map(function getPreferredName(person) {
    return person.nicknames[0] || person.firstName;
});
// ..
```

O nome da função `getPreferredName(..)` diz ao leitor algo sobre o que a operação de mapeamento pretende fazer que não é totalmente óbvio apenas pelo seu código. Este rótulo de nome ajuda o código a ser mais legível.

Outro lugar onde as expressões de função anônimas são comuns é com expressões de função imediatamente invocadas (IIFEs):

```js
(function () {
    // look, I'm an IIFE!
})();
```

Você virtualmente nunca vê IIFEs usando nomes para suas expressões de função, mas deveriam. Porque? Pelas mesmas razões que acabamos de examinar: depuração de rastreamento de pilha, auto-referência confiável e legibilidade. Se você não conseguir encontrar outro nome para seu IIFE, pelo menos use a palavra IIFE:

```js
(function IIFE() {
    // You already knew I was an IIFE!
})();
```

O que estou querendo dizer é que existem várias razões pelas quais **funções nomeadas são sempre mais preferíveis a funções anônimas.** Na verdade, eu diria que basicamente nunca há um caso em que uma função anônima é mais preferível. Elas simplesmente não têm nenhuma vantagem sobre suas contrapartes nomeadas.

É incrivelmente fácil escrever funções anônimas, porque é um nome a menos que temos que dedicar nossa atenção mental para descobrir.

Eu serei honesto, sou tão culpado disso quanto qualquer um. Eu não gosto de lutar com nomes. Os primeiros nomes que penso para uma função geralmente são ruins. Tenho que revisitar a nomeação repetidamente. Eu prefiro apenas usar uma boa expressão de função anônima.

Mas estamos trocando facilidade de escrita por dor de leitura. Esta não é uma boa troca. Ser preguiçoso ou pouco criativo para não querer descobrir nomes para suas funções é uma desculpa muito comum, mas pobre, para usar funções anônimas.

**Nomeia toda função.** E se você ficar perplexo, incapaz de encontrar um bom nome para alguma função que escreveu, sugiro que você ainda entenda totalmente o propósito dessa função, ou ela é muito ampla ou muito abstrata. Você precisa voltar e redesenhar a função até que isso fique mais claro. E nesse ponto, um nome ficará mais aparente.

Na minha prática, se não tenho um bom nome para usar em uma função, eu a chamo de `TODO` inicialmente. Tenho certeza de que pelo menos entenderei isso mais tarde, quando pesquisar nos comentários por `TODO` antes de enviar o código.

Posso testemunhar por experiência própria que, na luta para nomear bem algo, geralmente passei a entendê-lo melhor, mais tarde, e muitas vezes, até refatorar seu formato para facilitar a leitura e a manutenção.

Esse investimento de tempo vale a pena.

### Funções sem `função`

Até agora, usamos a sintaxe canônica completa para funções. Mas você, sem dúvida, também ouviu todo barulho em torno da sintaxe da funções de seta `=>`.

Compare:

```js
people.map(function getPreferredName(person) {
    return person.nicknames[0] || person.firstName;
});

// vs.

people.map((person) => person.nicknames[0] || person.firstName);
```

Uowl.

A palavra-chave `função` se foi, assim como `return`, os parênteses (`( )`), as chaves (`{ }`) e o ponto e vírgula mais interno (`;`). No lugar de tudo isso, usamos o chamado símbolo de seta grande (`=>`).

Mas há outra coisa que omitimos. Você percebeu? O nome da função `getPreferredName`.

Isso mesmo, as funções de seta `=>` são lexicamente anônimas, não há como fornecer um nome sintaticamente. Seus nomes podem ser inferidos como funções regulares, mas, novamente, o caso mais comum de valores de expressão passados como argumentos de funções não teria nenhuma ajuda dessa forma. 

Se `person.nicknames` não for definido por algum motivo, uma exceção será lançada, o que significa que esta `(função anônima)` estará no topo da pilha de rastreamento. ECA.

Honestamente, o anonimato das funções de seta `=>` é um `=>` punhal no coração, para mim. Não posso tolerar a perda de um nome. É mais difícil de ler, mais difícil de debugar e impossível de auto referênciar.

Mas se isso não fosse ruim o suficiente, o outro tapa na cara é que existem muitas variações sintáticas sutis, que você deve percorrer se tiver diferentes cenários para a definição de sua função. Não vou cobrir todos eles em detalhes aqui, mas resumidamente:

```js
people.map((person) => person.nicknames[0] || person.firstName);

// multiple parameters? need ( )
people.map((person, idx) => person.nicknames[0] || person.firstName);

// parameter destructuring? need ( )
people.map(({ person }) => person.nicknames[0] || person.firstName);

// parameter default? need ( )
people.map((person = {}) => person.nicknames[0] || person.firstName);

// returning an object? need ( )
people.map((person) => ({
    preferredName: person.nicknames[0] || person.firstName,
}));
```

O caso de empolgação com `=>` no mundo da PF é principalmente que ela segue quase que exatamente a notação matemática para funções, especialmente em linguagens de PF como Haskell. A forma sintática da função de seta `=>` se comunica matematicamente.

Indo ainda mais longe, sugiro que um argumento a favor de `=>` é que usando uma sintaxe muito mais leve, reduzimos os limites visuais entre as funções, o que nos permite usar expressões de função mais simples, da mesma forma que usaríamos expressões preguiçõsas, outro favorito dos programadores funcionais.

Acho que a maioria dos programadores funcionais vai ignorar as preocupações que estou compartilhando. Eles adoram funções anônimas e adoram economizar na sintaxe. Mas como eu disse antes: você decide.

**Nota:** Embora eu não prefira usar `=>` na prática em meu código de produção, eles são úteis em explorações de código rápidas. Além disso, usaremos as funções de seta em muitos lugares no restante deste livro, especialmente quando apresentamos utilitários da PF típicos, onde a concisão é preferida para otimizar o espaço físico limitado em trechos de código. Faça suas próprias escolhas se essa abordagem tornará seu próprio código pronto para produção mais ou menos legível. 

## O que é isso?

Se você não está familiarizado com as regras de vinculação `this` em JavaScript, recomendo dar uma olhada no meu livro _You Don't Know JS: this & Object Prototypes_. Para os fins desta seção, assumirei que você sabe como o `this` é determinado para uma chamada de função (uma das quatro regras). Mas mesmo que você ainda esteja confuso com _this_, a boa notícia é que vamos concluir que você não deve usar `this` se estiver tentando fazer PF.

**Observação:** Estamos abordando um tópico que, em última análise, concluiremos que não deve ser usado. Por que!? Porque o tópico `this` tem implicações para outros tópicos cobertos posteriormente neste livro. Por exemplo, nossas noções de pureza de função são impactadas por `this` ser essencialmente uma entrada implícita para uma função (consulte no [Capítulo 5](ch5.md)). Além disso, nossa perspectiva sobre `this` afeta se escolhemos métodos de array (`arr.map(..)`) contra utilitários autônomos (`map(..,arr)`) (veja no [Capítulo 9](ch9.md)). Entender `this` é essencial para entender porque `this` realmente _não_ deve fazer parte da sua PF!

As `funções` JavaScript têm uma palavra-chave `this` que é automaticamente vinculada por chamada de função. A palavra-chave `this` pode ser descritas de muitas maneiras diferentes, mas prefiro dizer que ela fornece um contexto de objeto para a execução da função.

`this` é uma entrada de parâmetro implícita para sua função.

Considere:

```js
function sum() {
    return this.x + this.y;
}

var context = {
    x: 1,
    y: 2,
};

sum.call(context); // 3

context.sum = sum;
context.sum(); // 3

var s = sum.bind(context);
s(); // 3
```

Claro, se `this` pode ser inserido em uma função implicitamente, o mesmo contexto de objeto pode ser enviado como um argumento explícito:

```js
function sum(ctx) {
    return ctx.x + ctx.y;
}

var context = {
    x: 1,
    y: 2,
};

sum(context);
```

Mais simples. E esse tipo de código será muito mais fácil de lidar na PF. É muito mais fácil conectar várias funções ou usar qualquer uma das outras técnicas de transformação de entrada que veremos no próximo capítulo, quando as entradas são sempre explícitas. Fazê-los com entradas implícitas como `this`, varia de estranho a quase impossível dependendo do cenário.

Existem outros truques que podemos aproveitar em um sistema baseado em `this`, incluindo a delegação de protótipo (também abordada em detalhes em _You Don't Know JS: this & Object Prototypes_):

```js
var Auth = {
    authorize() {
        var credentials = `${this.username}:${this.password}`;
        this.send(credentials, (resp) => {
            if (resp.error) this.displayError(resp.error);
            else this.displaySuccess();
        });
    },
    send(/* .. */) {
        // ..
    },
};

var Login = Object.assign(Object.create(Auth), {
    doLogin(user, pw) {
        this.username = user;
        this.password = pw;
        this.authorize();
    },
    displayError(err) {
        // ..
    },
    displaySuccess() {
        // ..
    },
});

Login.doLogin("fred", "123456");
```

**Nota:** `Object.assign(..)` é um utilitário ES6+ para fazer uma cópia de atribuição superficial de propriedades de um ou mais objetos de origem para um único objeto de destino: `Object.assign (target, source1, ...)`.

Caso você esteja tendo problemas para analisar o que este código faz: nós temos dois objetos separados `Login` e `Auth`, onde `Login` executa a delegação de protótipo para `Auth`. Atravéz da delegação e do compartilhamento de contexto implícito `this`, esses dois objetos virtualmente compõem durante a chamada da função `this.authorize()`, de modo que as propriedades/métodos em `this` são dinamicamente compartilhados com a função `Auth.authorize(..)`. 

O código _this_ não se encaixa em vários princípios da PF por uma variedade de razões, mas um dos obstáculos óbvios é o compartilhamento implícito de `this`. Poderíamos ser mais explícitos sobre isso e manter o código mais próximo do estilo amigável da PF.

```js
// ..

authorize(ctx) {
    var credentials = `${ctx.username}:${ctx.password}`;
    Auth.send( credentials, function onResp(resp){
        if (resp.error) ctx.displayError( resp.error );
        else ctx.displaySuccess();
    } );
}

// ..

doLogin(user,pw) {
    Auth.authorize( {
        username: user,
        password: pw
    } );
}

// ..
```

Do meu ponto de vista, o problema não é usar objetos para organizar o comportamento. É que estamos tentando usar entradas implícitas em vez de ser explícitos sobre isso. Quando estou usando meu boné da PF, quero deixar "essas coisas" na prateleira.

## Resumo

Funções são poderosas.

Mas vamos deixar claro o que é uma função. Não é apenas uma coleções de instruções/operações. Especificamente, uma função precisa de uma ou mais entradas (de preferência, apenas uma!) e uma saída.

Funções dentro de funções podem ter fechamento sobre variáveis externas e lembrá-las para depois. Este é um dos conceitos mais importantes em toda a programação e uma base fundamental da Progamação Funcional.

Tenha cuidado com as funções anônimas, especialmente as funções de seta `=>`. Elas são convenientes de escrever, mas transferem o custo do autor para o leitor. O motivo pelo qual estamos estudando PF aqui é para escrever um código mais legível, então não se precipite nesse momento.

Não use `this` conscientemente em funções. Apenas não faça isso.

Agora você deve estar desenvolvendo uma perspectiva clara e colorida em sua mente do que _função_ significa em Programação Funcional. É hora de começar a organizar funções para fazẽ-las interoperar, e o próximo capítulo ensina uma variedade de técnicas críticas de que você precisará ao longo do caminho.
