# Functional-Light JavaScript

# Chapter 2: The Nature Of Functions

# Capítulo 2: A Natureza Das Funções

Functional Programming is **not just programming with the `function` keyword.** Oh, if only it was that easy -- I could end the book right here! Nevertheless, functions really _are_ at the center of FP. And it's how we use functions that makes our code _functional_.

Programação Funcional **não é apenas programar com a palavra `function`** Oh, se fosse assim tão fácil -- Eu poderia terminar o livro aqui mesmo! No entanto, as funções realmente _estão_ no centro da PF. E é como usamos funções que torna nosso código _funcional_.

But how sure are you that you know what _function_ really means?

Mas você tem certeza de que sabe o que realmente significa _função_?

In this chapter, we're going to lay the groundwork for the rest of the book by exploring all the foundational aspects of functions. Actually, this is a review of all the things even a non-FP programmer should know about functions. But certainly if we want to get the most out of FP concepts, it's essential we
_know_ functions inside and out.

Neste capítulo, vamos preparar as bases para o resto do livro, explorando todos os aspectos fundamentais das funções. Na verdade, isto é uma revisão de todas as coisas que até mesmo um programador não funcional deve saber sobre funções. Mas certamentem, se quisermos obter o máximo dos conceitos da PF, é essencial _conhecermos_ funções por dentro e por fora.

Brace yourself, because there's a lot more to the function than you may have realized.

Prepare-se, porque funções são muito mais do que você pode ter imaginado.

## What Is a Function?

## O que São Funções?

The question "What is a function?" superficially seems to have an obvious answer: a function is a collection of code that can be executed one or more times.

A pergunta "O que é uma função?" superficialmente parece ter uma resposta óbvia: uma função é uma porção de código que pode ser executada uma ou mais vezes.

While this definition is reasonable, it's missing some very important essence that is the core of a _function_ as it applies to FP. So let's dig below the surface to understand functions more completely.

Embora esta definição seja razoável, está faltando alguma essência muito importante que é o núcleo de uma _função_ conforme se aplica a PF. Então, vamos ir a fundo abaixo da superfície para entender as funções de forma mais completa.

### Brief Math Review

### Breve Revisão de Matemática

I know I've promised we'd stay away from math as much as possible, but bear with me for a moment as we quickly observe some fundamental things about functions and graphs from algebra before we proceed.

Eu sei que prometi que ficaríamos longe da matemática o máximo possível, mas tenha paciência comigo por um momento enquanto observamos rapidamente algumas coisas fundamentais sobre funções e gráficos de álgebra antes de prosseguirmos.

Do you remember learning anything about `f(x)` back in school? What about the equation `y = f(x)`?

Você se lembra de ter aprendido alguma coisa sobre `f(x)` na escola?
E quanto à equação `y = f(x)`?

Let's say an equation is defined like this: <code>f(x) = 2x<sup>2</sup> + 3</code>. What does that mean? What does it mean to graph that equation? Here's the graph:

Digamos que uma equação seja definida assim: <code>f(x) = 2x<sup>2</sup> + 3</code>. O que isso significa? O que significa representar graficamente essa equação? Aqui está o gráfico:

<p align="center">
    <img src="images/fig1.png" width="40%">
</p>

What you can notice is that for any value of `x`, say `2`, if you plug it into the equation, you get `11`. What is `11`, though? It's the _return value_ of the `f(x)` function, which earlier we said represents a `y` value.

O que você pode notar é que para qualquer valor `x`, digamos `2`, se você inserir na equação, obterá `11`. O que é `11`, entretanto? É o _valor de retorno_ da função `f(x)`, que dissemos anteriormente que representa um valor `y`.

In other words, we can choose to interpret the input and output values as a point at `(2,11)` on that curve in the graph. And for every value of `x` we plug in, we get another `y` value that pairs with it as a coordinate for a point. Another is `(0,3)`, and another is `(-1,5)`. Put all those points together, and you have the graph of that parabolic curve as shown here.

Em outras palavras, podemos escolher interpretar os valores de entrada e saída como um ponto em `(2,11)` naquela curva no gráfico. E para cada valor de `x` que inserimos, obtemos outro valor de `y` que emparelha com ele como uma coordenada para um ponto. Outro é `(0,3)`, e outro é `(-1,5)`. Junte todos esses pontos e você terá o gráfico dessa curva parabólica conforme mostrado aqui.

So what's any of this got to do with FP?

Então o que isso tem a ver com PF?

In math, a function always takes input(s), and always gives an output. A term you'll often hear around FP is "morphism"; this is a fancy way of describing a set of values that maps to another set of values, like the inputs of a function related to the outputs of that function.

Em matemática, uma função sempre recebe entrada(s) e sempre fornece uma saída. Um termo que você ouvirá frequentemente em torno da PF é "morfismo"; essa é uma maneira sofisticada de descrever um conjunto de valores que mapeia para outro conjunto de valores, como as entradas de uma função relacionadas às saídas dessa função.

In algebraic math, those inputs and outputs are often interpreted as components of coordinates to be graphed. In our programs, however, we can define functions with all sorts of input(s) and output(s), even though they'll rarely be interpreted as a visually plotted curve on a graph.

Em matemática algébrica, essas entradas e saidas são frequentemente interpretadas como componentes de coordernadas a serem representadas graficamente. Em nossos programas, no entanto, podemos definir funções com todos os tipos de entrada(s) e saída(s), embora raramente sejam interpretadas como uma curva desenhada visualmente em um gráfico.

### Function vs Procedure

### Função vs Procedimento

So why all the talk of math and graphs? Because essentially Functional Programming is about embracing using functions as _functions_ in this mathematical sense.

Então, por que tanta conversa sobre matemática e gráficos? Porque essencialmente a Programação Funcional envolve o uso de funções como _funções_ neste sentido matemático.

You may be more accustomed to thinking of functions as procedures. What's the difference? A procedure is an arbitrary collection of functionality. It may have inputs, it may not. It may have an output (`return` value), it may not.

Você pode estar mais acostumado a pensar nas funções como procedimentos. Qual é a diferença? Um procedimento é uma coleção arbitrária de funcionalidades. Pode ter entradas, pode não ter. Poder ter uma saída (valor de `retorno`), ou não.

A function takes input(s) and definitely always has a `return` value.

Uma função recebe entrada(s) e definitivamente sempre tem um valor de `retorno`.

If you plan to do Functional Programming, **you should be using functions as much as possible**, and trying to avoid procedures wherever possible. All your `function`s should take input(s) and return output(s).

Se você planeja utilizar Programação Funcional, **deve usar as funções tanto quanto possível** e tentar evitar procedimentos sempre que possível. Todas as suas `funções` devem receber entrada(s) e retornar saída(s).

Why? The answer to that will have many levels of meaning that we'll uncover throughout this book.

Por quê? A resposta para essa pergunta terá muitos níveis de significado que descobriremos ao longo deste livro.

## Function Input

## Entrada da Função

So far, we can conclude that functions must expect input. But let's dig into how function inputs work.

Até agora, podemos concluir que as funções devem esperar uma entrada. Mas vamos examinar como funcionam as entradas da função.

You sometimes hear people refer to these inputs as "arguments" and sometimes as "parameters". So what's that all about?

Às vezes, você ouve as pessoas se referirem a essas entradas como "argumentos" e às vezes como "parâmetros". Então, do que se trata?

_Arguments_ are the values you pass in, and _parameters_ are the named variables inside the function that receive those passed-in values. Example:
_Argumentos_ são os valores que você passa e _parâmetros_ são as variáveis nomeadas dentro da função que recebe esses valores passados. Exemplo:

```js
function foo(x, y) {
    // ..
}

var a = 3;

foo(a, a * 2);
```

`a` and `a * 2` (actually, the result of `a * 2`, which is `6`) are the _arguments_ to the `foo(..)` call. `x` and `y` are the _parameters_ that receive the argument values (`3` and `6`, respectively).

`a` e `a * 2` (na verdade, o resultado de `a * 2`, que é `6`) são os _argumentos_ para a chamada `foo(..)`. `x` e `y` são os _parâmetros_ que recebem os valores do argumento (`3` e `6`, respectivamente).

**Note:** In JavaScript, there's no requirement that the number of _arguments_ matches the number of _parameters_. If you pass more _arguments_ than you have declared _parameters_ to receive them, the values pass in just fine untouched. These values can be accessed in a few different ways, including the old-school `arguments` object you may have heard of before. If you pass fewer _arguments_ than the declared _parameters_, each unmatched parameter is treated as an "undefined" variable, meaning it's present and available in the scope of the function, but just starts out with the empty `undefined` value.

**Nota:** Em JavaScript, não há requisito de que o número de _argumentos_ corresponda ao número de _parâmetros_. Se você passar mais _argumentos_ do que declarou de _parâmetros_ para recebê-los, os valores seguem intocados. Esses valores podem ser acessados de algumas maneiras diferentes, incluindo o objeto antigo `arguments`, do qual você já deve ter ouvido falar. Se você passar menos _argumentos_ do que os _parâmetros_ declarados, cada parâmetro não correspondido é tratado como uma variável "indefinida", o que significa que está presente e disponível no escopo da função, mas apenas começão com o valor vazio `undefined`.

### Defaulting Parameters

### Parâmetros padrão

As of ES6, parameters can declare _default values_. In the case where the argument for that parameter is not passed, or it's passed the value `undefined`, the default assignment expression is substituted.

A partir do ES6, os parâmetros podem declarar _valores padrão_. No caso em que o argumento para aquele parâmetro não é passado, ou é passado o valor `undefined`, a expressão de atribuição padrão é substituída.

Consider:
Considerar:

```js
function foo(x = 3) {
    console.log(x);
}

foo(); // 3
foo(undefined); // 3
foo(null); // null
foo(0); // 0
```

It's always a good practice to think about any default cases that can aid the usability of your functions. However, defaulting parameters can lead to more complexity in terms of reading and understanding the variations of how a function is called. Be judicious in how much you rely on this feature.

É sempre uma boa prática pensar nos casos padrão que podem ajudar na usabilidade de suas funções. No entanto, parâmetros padrão podem trazer mais complexidade em termos de leitura e compreensão das varias maneiras de como uma função é chamada. Seja criterioso sobre o quanto você confia nesse recurso.

### Counting Inputs

### Contando Entradas

The number of arguments a function "expects" -- how many arguments you'll likely want to pass to it -- is determined by the number of parameters that are declared:

O numero de argumentos que uma função "espera" -- quantos argumentos você provavelmente deseja passar para ela -- é determinado pelo número de parâmetros que são declarados:

```js
function foo(x, y, z) {
    // ..
}
```

`foo(..)` _expects_ three arguments, because it has three declared parameters. This count has a special term: arity. Arity is the number of parameters in a function declaration. The arity of `foo(..)` is `3`.

`foo(..)` _espera_ três argumentos, porque tem três parâmetros declardos. Essa contagem possui um termo especial: aridade. Aridade é o número de parâmetros em uma declaração de função. A aridade de `foo(..)` é `3`.

Furthermore, a function with arity 1 is also called "unary", a function with arity 2 is also called "binary", and a function with arity 3 or higher is called "n-ary".

Além disso, uma função com aridade 1 também é chamada de "unária", uma função com aridade 2 também é chamada de "binária" e uma função com aridade 3 ou superior é chamada de "n-ária".

You may wish to inspect a function reference during the runtime of a program to determine its arity. This can be done with the `length` property of that function reference:

Você pode querer inspecionar uma referência de função durante o tempo de execução de um programa para determinar sua aridade. Isso pode ser feito com a propriedade `lenght` dessa referência de função:


```js
function foo(x, y, z) {
    // ..
}

foo.length; // 3
```

One reason for determining the arity during execution would be if a piece of code received a function reference from multiple sources, and sent different values depending on the arity of each.

Uma razão para determinar a aridade durante a execução seria se um trecho de código recebesse uma referência de função de várias fontes e enviasse valores diferentes dependendo da aridade de cada uma delas.

For example, imagine a case where an `fn` function reference could expect one, two, or three arguments, but you always want to just pass a variable `x` in the last position:

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

**Tip:** The `length` property of a function is read-only and it's determined at the time you declare the function. It should be thought of as essentially a piece of metadata that describes something about the intended usage of the function.

**Dica:** A propriedade `length` de uma função é somente para leitura e é determinada no momento em que você declara a função. Deve ser considerado essencialmente como um pedaço de metadados que descreve algo sobre o uso pretendido da função.

One gotcha to be aware of is that certain kinds of parameter list variations can make the `length` property of the function report something different than you might expect:

Um problema a ter em conta é que certos tipos de variações da lista de parâmetros podem tornar a propriedade `length` do relatório de função, algo diferente do que você pode esperar.

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

What about counting the number of arguments the current function call received? This was once trivial, but now the situation is slightly more complicated. Each function has an `arguments` object (array-like) available that holds a reference to each of the arguments passed in. You can then inspect the `length` property of `arguments` to figure out how many were actually passed:

Que tal contar o número de argumentos que a chamada de função atual recebeu? Isso já foi trivial, mas agora a situação é um pouco mais complicada. Cada função tem um objeto `arguments` (semelhante a um array) disponível que contém uma referência a cada um dos argumentos passados. Você pode então inspecionar a propriedade `length` dos `arguments` para descobrir quantos foram realmente passados:

```js
function foo(x, y, z) {
    console.log(arguments.length);
}

foo(3, 4); // 2
```

As of ES5 (and strict mode, specifically), `arguments` is considered by some to be sort of deprecated; many avoid using it if possible. In JS, we "never" break backward compatibility no matter how helpful that may be for future progress, so `arguments` will never be removed. But it's now commonly suggested that you avoid using it whenever possible.

A partir do ES5 (e do modo estrito, especificamente), `arguments` são considerados por alguns como obsoletos; muitos evitam usá-lo, se possível. Em JS, nós "nunca" quebramos a compatibilidade com versões anteriores, não importa o quão útil isso possa ser para o progresso futuro, então `arguments` nunca serão removidos. Mas agora é comumente sugerido que você evite usá-lo sempre que possível.

However, I suggest that `arguments.length`, and only that, is OK to keep using for those cases where you need to care about the passed number of arguments. A future version of JS might possibly add a feature that offers the ability to determine the number of arguments passed without consulting `arguments.length`; if that happens, then we can fully drop usage of `arguments`!

No entando, sugiro que `arguments.length`, e apenas isso, pode continuar a ser usado para aqueles casos em que você precisa se preocupar com o número de argumentos transmitidos. Uma versão futura do JS pode possivelmente adicionar um recurso que oferece a capacidade de determinar o número de argumentos passados sem consultar `arguments.length`; se isso acontecer, podemos descartar totalmente o uso de `argumentos`!

Be careful: **never** access arguments positionally, like `arguments[1]`. Stick to `arguments.length` only, and only if you must.

Seja cuidadoso: **nunca** acesse argumentos posicionalmente, como `arguments[1]`. Atenha-se apenas a `arguments.length`, e somente se for necessário.

Except, how will you access an argument that was passed in a position beyond the declared parameters? I'll answer that in a moment; but first, take a step back and ask yourself, "Why would I want to do that?" Seriously. Think about that closely for a minute.

Exceto, como você acessará um argumento que foi passado em um posição além dos parâmetros declarados? Já vou responder; mas primeiro, dê um passo para trás e pergunte-se: "Por que eu faria isso?" Seriamente. Pense nisso por um minuto.

It should be pretty rare that this occurs; it shouldn't be something you regularly expect or rely on when writing your functions. If you find yourself in such a scenario, spend an extra 20 minutes trying to design the interaction with that function in a different way. Name that extra argument even if it's exceptional.

Deve ser muito raro que isso ocorra; não deve ser algo que você regularmente espera ou confia ao escrever suas funções. Se você se encontrar em tal cenário, gaste 20 minutos extras tentando projetar a interação com essa função de uma maneira diferente. Nomeie esse arumento extra, mesmo que seja excepcional.

A function signature that accepts an indeterminate amount of arguments is referred to as a variadic function. Some people prefer this style of function design, but I think you'll find that often the FPer wants to avoid these where possible.

Uma assinatura de função que aceita uma quantidade indeterminada de argumentos é chamada de função variável. Algumas pessoas preferem esse estilo de design de função, mas acho que você descobrirá que muitas vezes o Programador Funcional deseja evitá-lo sempre que possível.

OK, enough harping on that point.

Ok, chega de insistência nesse ponto.

Say you do need to access the arguments in a positional array-like way, possibly because you're accessing an argument that doesn't have a formal parameter at that position. How do we do it?

Digamos que você precise acessar os argumentos de uma forma semelhante a um array posicional, possivelmente porque está acessando um argumento que não possui um parâmetro formal nessa posição. Como fazemos isso?

ES6 to the rescue! Let's declare our function with the `...` operator -- variously referred to as "spread", "rest", or (my preference) "gather":

ES6 ao resgate! Vamos declarar nossa função com o operador `...` - conhecido como "spread", "rest", ou (como eu prefiro) "gather":

```js
function foo(x, y, z, ...args) {
    // ..
}
```

See the `...args` in the parameter list? That's an ES6 declarative form that tells the engine to collect (ahem, "gather") all remaining arguments (if any) not assigned to named parameters, and put them in a real array named `args`. `args` will always be an array, even if it's empty. But it **will not** include values that are assigned to the `x`, `y`, and `z` parameters, only anything else that's passed in beyond those first three values:

Veja o `...args` na lista de parâmetrosw Essa é uma forma declarativa do ES6 que diz a máquina para coletar (ahem, "gather") todos os argumentos restantes (se houver) não atribuídos aos parâmetros nomeados e colocá-los em um array chamado `args`. `args` sempre será um array, mesmo se estiver vazio. Mas **não incluirá** valores atribuídos aos parâmetros `x`, `y` e `z`, apenas qualquer outra coisa que seja passada além desses três primeiros valores:

```js
function foo(x, y, z, ...args) {
    console.log(x, y, z, args);
}

foo(); // undefined undefined undefined []
foo(1, 2, 3); // 1 2 3 []
foo(1, 2, 3, 4); // 1 2 3 [ 4 ]
foo(1, 2, 3, 4, 5); // 1 2 3 [ 4, 5 ]
```

So, if you _really_ want to design a function that can account for an arbitrary number of arguments to be passed in, use `...args` (or whatever name you like) on the end. Now, you'll have a real, non-deprecated, non-yucky array to access those argument values from.

Então, se você _realmente_ deseja projetar uma função que possa levar em conta um número arbitrário de argumentos a serem passados, use `...args` (ou qualquer nome que você quiser) no final. Agora, você terá uma matriz real, não obsoleta e não nojenta para acessar esses valores de argumento.

Just pay attention to the fact that the value `4` is at position `0` of that `args`, not position `3`. And its `length` value won't include those three `1`, `2`, and `3` values. `...args` gathers everything else, not including the `x`, `y`, and `z`.

Apenas preste atenção ao fato de que o valor `4` está na posição `0` daquele `args`, não na posição `3`. E seu valor de `length` não incluirá esses três valores `1`, `2` e `3`. `...args` reúne todo o resto, não incluindo `x`, `y` e `z`.

You _can_ use the `...` operator in the parameter list even if there's no other formal parameters declared:

Você _pode_ usar o operador `...` no lista de parâmetros mesmo se não houver outros parâmetros formais declarados:

```js
function foo(...args) {
    // ..
}
```

Now `args` will be the full array of arguments, whatever they are, and you can use `args.length` to know exactly how many arguments have been passed in. And you're safe to use `args[1]` or `args[317]` if you so choose. Please don't pass in 318 arguments, though.

Agora `args` será o array completo de argumentos, quaisquer que sejam, e você pode usar `args.length` para saber exatamente quantos argumentos foram passados. E você está seguro para usar `args[1]` ou `args[317]` se você escolher. Porém, não passe 318 argumentos.

### Arrays of Arguments
### Arrays de Argumentos

What if you wanted to pass along an array of values as the arguments to a function call?

E se você quisesse passar um array de valores como argumentos para uma chamada de função?

```js
function foo(...args) {
    console.log(args[3]);
}

var arr = [1, 2, 3, 4, 5];

foo(...arr); // 4
```

Our new friend `...` is used, but now not just in the parameter list; it's also used in the argument list at the call-site. It has the opposite behavior in this context. In a parameter list, we said it _gathered_ arguments together. In an argument list, it _spreads_ them out. So the contents of `arr` are actually spread out as individual arguments to the `foo(..)` call. Do you see how that's different from just passing in a reference to the whole `arr` array?

Nosso novo amigo `...` é usado, mas agora não somente na lista de parâmetros; também é usado na lista de argumentos no call-site. Ele tem o comportamento oposto neste contexto. Em uma lista de parâmetros, dissemos que ela _agrupa_ os argumentos. Em uma lista de argumentos, ela os _espalha_. Portanto, o conteúdo de `arr` é realmente espalhado como argumentos individuais para a chamada `foo(..)`. Você vê como isso é diferente de apenas passar uma referência para todo o array `arr`?

By the way, multiple values and `...` spreadings can be interleaved, as you see fit:

A propósito, vários valores e distribuições `...` podem ser intercalados, como você achar necessário:

```js
var arr = [2];

foo(1, ...arr, 3, ...[4, 5]); // 4
```

Think of `...` in this symmetric sense: in a value-list position, it _spreads_. In an assignment position -- like a parameter list, because arguments get _assigned to_ parameters -- it _gathers_.

Pense em `...` neste sentido simétrico: em uma posição da lista de valores, ele _espalha_. Em uma posição de atribuição -- como uma lista de parâmetros, porque os argumentos são _reduzidos a_ parâmetros -- ele _agrupa_.

Whichever behavior you invoke, `...` makes working with arrays of arguments much easier. Gone are the days of `slice(..)`, `concat(..)`, and `apply(..)` to wrangle our argument value arrays.

Qualquer que seja o comportamento que você invocar, `...` torna o trabalho com arrays de argumentos muito mais fácil. Já se foram os dias de `slice(..)`, `concat(..)` e `apply(..)` para disputar nossas matrizes de valor de argumento.

**Tip:** Actually, these methods are not entirely useless. There will be a few places we rely on them throughout the code in this book. But certainly in most places, `...` will be much more declaratively readable, and preferable as a result.

**Dica:** Na verdade, esses métodos não são totalmente inúteis. Haverá alguns lugares onde dependemos deles ao longo do código deste livro. Mas certamente na maioria dos lugares, `...` será muito mais legível declarativamente e, como resultado, preferível.

### Parameter Destructuring
### Destructuração de Parâmetros

Consider the variadic `foo(..)` from the previous section:
Considere a variável `foo(..)` da seção anterior:

```js
function foo(...args) {
    // ..
}

foo(...[1, 2, 3]);
```

What if we wanted to change that interaction so the caller of our function passes in an array of values instead of individual argument values? Just drop the two `...` usages:

E se quiséssemos mudar essa interação para que o chamador de nossa função passe um array de valores em vez de valores individuais de argumentos? Basta descartar os dois usos `...`:

```js
function foo(args) {
    // ..
}

foo([1, 2, 3]);
```

Simple enough. But what if now we wanted to give a parameter name to each of the first two values in the passed-in array? We aren't declaring individual parameters anymore, so it seems we lost that ability.

Simples o suficiente. Mas e se agora quisermos dar um nome de parâmetro a cada um dos primeiros dois valores passados no array? Não declaramos mais parâmetros individuais, então parece que perdemos essa capacidade.

Thankfully, ES6 destructuring is the answer. Destructuring is a way to declare a _pattern_ for the kind of structure (object, array, etc.) that you expect to see, and how decomposition (assignment) of its individual parts should be processed.

Felizmente, a desestruturação do ES6 é a resposta. A desestruturação é uma maneira de declarar um _padrão_ para o tipo de estrutura (objeto, array, etc.) que você espera ver e como a decomposição (atribuição) de suas partes individuais deve ser processada.

Consider:
Considere:

<a name="funcparamdestr"></a>

```js
function foo([x, y, ...args] = []) {
    // ..
}

foo([1, 2, 3]);
```

Do you spot the `[ .. ]` brackets around the parameter list now? This is called array parameter destructuring.

Você identificou os colchetes `[ .. ]` em torno da lista de parâmetros agora? Isso é chamado de desestruturação de parâmetros de matriz.

In this example, destructuring tells the engine that an array is expected in this assignment position (aka parameter). The pattern says to take the first value of that array and assign to a local parameter variable called `x`, the second to `y`, and whatever is left is _gathered_ into `args`.

Neste exemplo, a desestruturação informa ao mecanismo que um array é esperado nesta posição de atribuição (também conhecido como parâmetro). O padrão diz para pegar o primeiro valor desse array e atribuir a uma variável de parâmetro local chamada `x`, o segundo para `y` e o que sobrar _agrupar_ em `args`.

### The Importance of Declarative Style
### A Importância do Estilo Declarativo

Considering the destructured `foo(..)` we just looked at, we could instead have processed the parameters manually:

Considerando o `foo(..)` desestruturado que acabamos de ver, poderíamos ter processado os parâmetros manualmente:

```js
function foo(params) {
    var x = params[0];
    var y = params[1];
    var args = params.slice(2);

    // ..
}
```

But here we highlight a principle we only briefly introduced in [Chapter 1](ch1.md/#readability): declarative code communicates more effectively than imperative code.

Mas aqui destacamos um princípio que apresentamos apenas brevemente no [Capítulo 1](ch1.md/#readability): o código declarativo se comunica com mais eficácia do que o código imperativo.

Declarative code (for example, the destructuring in the former `foo(..)` snippet, or the `...` operator usages) focuses on what the outcome of a piece of code should be.

O código declarativo (por exemplo, a desestruturação no fragmento `foo(..)` anterior ou nos usos do operador `...`) foca em qual deve ser o resultado de um trecho de código.

Imperative code (such as the manual assignments in the latter snippet) focuses more on how to get the outcome. If you later read such imperative code, you have to mentally execute all of it to understand the desired outcome. The outcome is _coded_ there, but it's not as clear because it's clouded by the details of _how_ we get there.

O código imperativo (como as atribuições manuais no último fragmento) se concentra mais em como obter o resultado. Se, posteriormente, você ler esse código imperativo, terá de executá-lo mentalmente para entender o resultado desejado. O resultado está _codificado_ lá, mas não é tão claro porque é obscurecido pelos detalhes de _como_ chegamos lá.

The earlier `foo(..)` is regarded as more readable, because the destructuring hides the unnecessary details of _how_ to manage the parameter inputs; the reader is free to focus only on _what_ we will do with those parameters. That's clearly the most important concern, so it's what the reader should be focused on to understand the code most completely.

O `foo(..)` anterior é considerado mais legível, porque a desestruturação oculta os detalhes desnecessários de _como_ gerenciar as entradas dos parâmetros; o leitor está livre para se concentrar apenas em _o_ que faremos com esses parâmetros. Essa é claramente a preocupação mais importante, portanto, é nisso que o leitor deve se concentrar para entender o código de forma mais completa.

Wherever possible, and to whatever degrees our language and our libraries/frameworks will let us, **we should be striving for declarative, self-explanatory code.**

Sempre que possível e em todos os graus que nossa linguagem e nossas bibliotecas/frameworks permitem, **devemos nos esforçar para obter um código declarativo e autoexplicativo**.

## Named Arguments

## Argumentos Nomeados

Just as we can destructure array parameters, we can destructure object parameters:

Assim como podemos desestruturar os parâmetros do array, podemos desestruturar os parâmetros do objeto:

```js
function foo({ x, y } = {}) {
    console.log(x, y);
}

foo({
    y: 3,
}); // undefined 3
```

We pass in an object as the single argument, and it's destructured into two separate parameter variables `x` and `y`, which are assigned the values of those corresponding property names from the object passed in. It didn't matter that the `x` property wasn't on the object; it just ended up as a variable with `undefined` like you'd expect.

Passamos um objeto como o único argumento, e ele é desestruturado em duas variáveis de parâmetro separadas `x` e `y`, que são atribuídos aos valores dos nomes de propriedade correspondentes do objeto passado. Não importava se a propriedade `x` não estava no objeto; acabou sendo uma variável `undefined` como você esperava. 

But the part of parameter object destructuring I want you to pay attention to is the object being passed into `foo(..)`.

Mas a parte da desestruturação do parâmetro do objeto que eu quero que você preste atenção é o objeto que está sendo passado para `foo(..)`.

With a normal call-site like `foo(undefined,3)`, position is used to map from argument to parameter; we put the `3` in the second position to get it assigned to a `y` parameter. But at this new kind of call-site where parameter destructuring is involved, a simple object-property indicates which parameter (`y`) the argument value `3` should be assigned to.

Com uma call-site normal como `foo(undefined,3)`, a posição é usada para mapear de argumento para parâmetro; colocamos `3` na segunda posição para atribuí-lo a um parâmetro `y`. Mas neste novo tipo de call-site onde a desestruturação de parâmetro está envolvida, uma propriedade de objeto simples indica a qual parâmetro (`y`) o valor do argumento `3` deve ser atribuído.

We didn't have to account for `x` in _that_ call-site because in effect we didn't care about `x`. We just omitted it, instead of having to do something distracting like passing `undefined` as a positional placeholder.

Não tivemos que contabilizar `x` _naquela_ call-site porque, na verdade, não nos importamos com `x`. Nós apenas o omitimos, em vez de ter que fazer algo pertubador, como passar `undefined` para marcar a posição.

Some languages have an explicit feature for this: named arguments. In other words, at the call-site, labeling an input value to indicate which parameter it maps to. JavaScript doesn't have named arguments, but parameter object destructuring is the next best thing.

Algumas linguagens têm um recurso explícito para isso: argumentos nomeados. Em outras palavras, no call-site, rotulando um valor de entrada para indicar para qual parâmetro ele mapeia. JavaScript não tem argumentos nomeados, mas a desestruturação do objeto de parâmetro é a coisa mais próxima disso.

Another FP-related benefit of using an object destructuring to pass in potentially multiple arguments is that a function that only takes one parameter (the object) is much easier to compose with another function's single output. Much more on that in [Chapter 4](ch4.md).

Outro benefício relacionado a PF onde usamos uma desestruturação de objeto para passar argumentos potencialmente múltiplos é que uma função que leva apenas um parâmetro (o objeto) é muito mais fácil de compor com a única saída de outra função. Muito mais sobre isso no [Capítulo 4](ch4.md).

### Unordered Parameters
### Parâmetros Não Odernados

Another key benefit is that named arguments, by virtue of being specified as object properties, are not fundamentally ordered. That means we can specify inputs in whatever order we want:

Outra benefício importante é que os argumentos nomeados, em virtude de serem especificados como propriedade do objetos, não são fundamentalmente ordenados. Isto quer dizer que podemos especificar as entradas na ordem que quisermos:

```js
function foo({ x, y } = {}) {
    console.log(x, y);
}

foo({
    y: 3,
}); // undefined 3
```

We're skipping the `x` parameter by simply omitting it. Or we could specify an `x` argument if we cared to, even if we listed it after `y` in the object literal. The call-site is no longer cluttered by ordered-placeholders like `undefined` to skip a parameter.

Estamos pulando o parâmetro `x` simplesmente omitindo-o. Ou poderíamos especificar um argumento `x` se quiséssemos, mesmo se o listássemos após `y` no literal do objeto. O call-site não é mais desordenado por marcadores de posição como `undefined` para pular um parâmetro.

Named arguments are much more flexible, and attractive from a readability perspective, especially when the function in question can take three, four, or more inputs.

Argumentos nomeados são muito mais flexíveis e atraentes do ponto de vista da legibilidade, especialmente quando a função em questão pode receber três, quatro ou mais entradas.

**Tip:** If this style of function arguments seems useful or interesting to you, check out coverage of my [FPO library in Appendix C](apC.md/#bonus-fpo).

**Dica:** Se este estilo de argumentos de função parece útil ou interessante para você, verifique a cobertura da minha [biblioteca FPO no Apêndice C](apC.md/#bonus-fpo).

## Function Output
## Saída de função

Let's shift our attention from a function's inputs to its output.
Vamos mudar nossa atenção das entradas de uma função para sua saída.

In JavaScript, functions always return a value. These three functions all have identical `return` behavior:

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

The `undefined` value is implicitly `return`ed if you have no `return` or if you just have an empty `return;`.

O valor `undefined` é implicitamente `retornado`se você não tiver um `return` ou se vocẽ tiver apenas uma `return` vazio. 

But keeping as much with the spirit of FP function definition as possible -- using functions and not procedures -- our functions should always have outputs, which means they should explicitly `return` a value, and usually not `undefined`.

Mas mantendo o máximo possível com o espírito de definição de PF -- usando funções e não procedimentos -- nossas funções devem sempre ter saídas, o que significa que elas devem `return` explicitamente um valor, e geralmente não `undefined`.

A `return` statement can only return a single value. So if your function needs to return multiple values, your only viable option is to collect them into a compound value like an array or an object:

Uma instrução `return` só pode retornar um único valor. Portanto, se sua função precisa retornar vários valores, sua única opção viável é coletá-los em um valor composto, como uma matrízou um objeto:

```js
function foo() {
    var retValue1 = 11;
    var retValue2 = 31;
    return [retValue1, retValue2];
}
```

Then, we'll assign `x` and `y` from two respective items in the array that comes back from `foo()`:

Em seguida, atribuiremos `x` e `y` de dois respectivos itens na matriz que vem de `foo()`

```js
var [x, y] = foo();
console.log(x + y); // 42
```

Collecting multiple values into an array (or object) to return, and subsequently destructuring those values back into distinct assignments, is a way to transparently express multiple outputs for a function.

Coletar vários valores em uma matriz (ou objeto) para retornar e, subsequentemente, desestruturar esses valores de volta em atribuições distintas, é uma maneira de expressar de forma transparente várias saídas para uma função.

**Tip:** I'd be remiss if I didn't suggest you take a moment to consider if a function needing multiple outputs could be refactored to avoid that, perhaps separated into two or more smaller single-purpose functions? Sometimes that will be possible, sometimes not; but you should at least consider it.

**Dica:** Eu seria negligente se não sugerisse que você parasse um momento para considerar se uma função que precisa de várias saídas poderia ser refatorada para evitar isso, talvez separada em duas ou mais funções menores de propósito único. Às vezes isso será possível, às vezes não; mas você deve pelo menos considerar isso.

### Early Returns
### Retornos Antecipados

The `return` statement doesn't just return a value from a function. It's also a flow control structure; it ends the execution of the function at that point. A function with multiple `return` statements thus has multiple possible exit points, meaning that it may be harder to read a function to understand its output behavior if there are many paths that could produce that output.

A instrução `return` não retorna apenas um valor de uma função. É também uma estrutura de controle de fluxo; ele termina a execução da função nesse ponto. Uma função com várias instruções `return`, portanto, tem vários pontos de sáida possíveis, o que significa que pode ser mais difícil ler uma função para entender seu comportamento de saída se houver muitos caminhos que poderiam produzir essa saída.

Consider:

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

Pop quiz: without cheating and running this code in your browser, what does `foo(2)` return? What about `foo(4)`? And `foo(8)`? And `foo(12)`?

Questionário rápido: sem trapacear e rodar este código em seu navegador, o que `foo(2)` retorna? E sobre `foo(4)`? E `foo(8)`? E `foo(12)`?

How confident are you in your answers? How much mental tax did you pay to get those answers? I got it wrong the first two times I tried to think it through, and I wrote it!

Você está confiante em suas respostas? Quanto da sua mente você gastou para obter essas respostas? Eu entendi errado nas duas primeiras vezes que tentei pensar sobre e escrevi! 

I think part of the readability problem here is that we're using `return` not just to return different values, but also as a flow control construct to quit a function's execution early in certain cases. There are obviously better ways to write that flow control (the `if` logic, etc.), but I also think there are ways to make the output paths more obvious.

Eu acho que parte do problema de legibilidade aqui é que estamos usando `return` não apenas para retornar valores diferentes, mas também como uma construção de controle de fluxo para encerrar a execução de uma função mais cedo em certos casos. Obviamente, existem maneiras melhores de escrever esse controle de fluxo (a lógica `if`, etc.), mas também, acho que existem maneiras de tornar os caminhos de saída mais óbvios.

**Note:** The answers to the pop quiz are `2`, `2`, `8`, and `13`.

**Observação:** As respostas do questionário rápido são `2`, `2`, `8` e `13`.

Consider this version of the code:

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

This version is unquestionably more verbose. But I would argue it's slightly simpler logic to follow, because every branch where `retValue` can get set is _guarded_ by the condition that checks if it's already been set.

Esta versão é inquestionávelmente mais verbosa. Mas eu diria que é uma lógica mais simples de seguir, porque cada branch onde `retValue` pode ser definido é _guardado_ pela condição que verifica se ele já foi definido antes.

Rather than `return`ing from the function early, we used normal flow control (`if` logic) to determine the `retValue`'s assignment. At the end, we simply `return retValue`.

Em vez de usar `return` antecipadamente na função, usamos o controle de fluxo normal (`if` lógico) para determinar a atribuição de `retValue`'s. No final, simplesmente `retornamos retValue`.

I'm not unconditionally saying that you should always have a single `return`, or that you should never do early `return`s, but I do think you should be careful about the flow control part of `return` creating more implicitness in your function definitions. Try to figure out the most explicit way to express the logic; that will often be the best way.

Não estou dizendo incondicionalmente que você deve sempre ter um único `return`, ou que você nunca deve usar `return` antecipado, mas eu acho que você deve ter cuidado com a parte de controle de fluxo do `return` criando mais implicitude em suas definições de função. Tente descobrir a maneira mais explícita de expressar a lógica; essa geralmente será a melhor maneira.

### Un`return`ed Outputs
### Saídas não retornadas

One technique that you've probably used in most code you've written, and maybe didn't even think about it much, is to have a function output some or all of its values by simply changing variables outside itself.

Uma técnica que você provavelmente usou na maior parte do código que você tem escrevido, e talvez não tenha pensado muito nisso, é fazer com que uma função produza alguns ou todos os seus valores simplesmente alterando as variáveis de fora dela.

Remember our <code>f(x) = 2x<sup>2</sup> + 3</code> function from earlier in the chapter? We could have defined it like this in JS:

Lembra-se de nossa função <code>f(x) = 2x<sup>2</sup> + 3</code> do início do capítulo? Poderíamos ter definido assim em JS:

```js
var y;

function f(x) {
    y = 2 * Math.pow(x, 2) + 3;
}

f(2);

y; // 11
```

I know this is a silly example; we could just as easily have `return`d the value instead of setting it into `y` from within the function:

Eu sei que este é um exemplo bobo; Poderíamos facilmente ter um `return` do valor em vez de defini-lo como `y` de dentro da função:

```js
function f(x) {
    return 2 * Math.pow(x, 2) + 3;
}

var y = f(2);

y; // 11
```

Both functions accomplish the same task, so is there any reason we should pick one version over the other? **Yes, absolutely.**

Ambas as funções realizam a mesma tarefa, então há algum motivo para escolhermos uma versão em vez da outra? **Sim, absolutamente.**

One way to explain the difference is that the `return` in the latter version signals an explicit output, whereas the `y` assignment in the former is an implicit output. You may already have some intuition that guides you in such cases; typically, developers prefer explicit patterns over implicit ones.

Uma maneira de explicar a diferença é que o `return` na última versão sinaliza uma saída explícita, enquanto a atribuição `y` na primeira versão é uma saída implícita. Você pode já ter alguma intuição que o orienta nesses casos; Normalmente, os desenvolvedores preferem padrões explícitos aos implícitos.

But changing a variable in an outer scope, as we did with the `y` assignment inside of `foo(..)`, is just one way of achieving an implicit output. A more subtle example is making changes to non-local values via reference.

Mas alterar uma variável em um escopo externo, como fizemos com a atribuição `y` dentro de `foo(..)`, é apenas uma maneira de obter uma saída implícita. Um exemplo mais sutil é fazer alterações em valores não locais por meio de referência.

Consider:
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

The most obvious output from this function is the sum `124`, which we explicitly `return`ed. But do you spot the other output? Try that code and then inspect the `nums` array. Now do you spot the difference?

A saída mais óbvia dessa função é a soma `124`, que explicitamente `retornamos`. Mas você identifica a outra saída? Coloque esse código em prática e inspecione o `nums` do array. Agora você percebe a diferença? 

Instead of an `undefined` empty slot value in position `4`, now there's a `0`. The harmless looking `list[i] = 0` operation ended up affecting the array value on the outside, even though we operated on a local `list` parameter variable.

Em vez de um valor de slot vazio `undefined` na posição `4`, agora há um `0`. A operação `list[i] = 0` de aparência inofensiva acabou afetando o valor do array do lado de fora, embora operássemos em uma variável de parâmetro `list` local.

Why? Because `list` holds a reference-copy of the `nums` reference, not a value-copy of the `[1,3,9,..]` array value. JavaScript uses references and reference-copies for arrays, objects, and functions, so we may create an accidental output from our function all too easily.

Por quê? Porque `list` contém uma cópia-referência da referência `nums`, não uma cópia-valor do valor do array `[1,3,9,..]`. JavaScript usa referências e cópias de referência para arrays, objetos e funções, portanto, podemos criar uma saída acidental de nossa função com muita facilidade.

This implicit function output has a special name in the FP world: side effects. And a function that has _no side effects_ also has a special name: pure function. We'll talk a lot more about these in [Chapter 5](ch5.md), but the punchline is that we'll want to prefer pure functions and avoid side effects wherever possible.

Essa saída de função implícita tem um nome especial no mundo da PF: efeitos colaterais. E uma função que _não tem efeitos colaterais_ também tem um nome especial: função pura. Falaremos muito mais sobre isso no [Capítulo 5](ch5.md), mas o ponto principal é que queremos preferir funções puras e evitar efeitos colaterais sempre que possível.

## Functions of Functions
## Funções de Funções

Functions can receive and return values of any type. A function that receives or returns one or more other function values has the special name: higher-order function.

Funções podem receber e retornar valores de qualquer tipo. Uma função que recebe ou retorna uma ou mais outros valores de função tem um especial nome: funções de ordem superior.

Consider:
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

`forEach(..)` is a higher-order function because it receives a function as an argument.

`forEach(..)` é uma função de ordem superior porque recebe uma função como um argumento.

A higher-order function can also output another function, like:

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

`return` is not the only way to "output" an inner function:

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

Functions that treat other functions as values are higher-order functions by definition. FPers write these all the time!

As funções que tratam outras funções como valores são funções de ordem superior por definição. Os programadores funcionais escrevem isso o tempo todo!

### Keeping Scope
### Mantendo o Escopo

One of the most powerful things in all of programming, and especially in FP, is how a function behaves when it's inside another function's scope. When the inner function makes reference to a variable from the outer function, this is called closure.

Uma das coisas mais poderosas em toda a programação, e especialmente na PF, é como uma função se comporta quando está dentro do escopo de outra função. Quando a função interna faz referência a uma variável da função externa, isso é chamado de closure.

Defined pragmatically:
Definido pragmaticamente:

> Closure is when a function remembers and accesses variables from outside of its own scope, even when that function is executed in a different scope.

> Closure é quando uma função lembra e acessa variáveis de fora de seu próprio escopo, mesmo quando essa função é executada em um escopo diferente.

Consider:
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

The `msg` parameter variable in the scope of `foo(..)` is referenced inside the inner function. When `foo(..)` is executed and the inner function is created, it captures the access to the `msg` variable, and retains that access even after being `return`ed.

A variável de parâmetro `msg` no escopo de `foo(..)` é referenciada dentro da função interna. Quando `foo(..)` é executado e a função interna é criada, ele captura o acesso à variável `msg` e retém esse acesso mesmo depois de ser `retornado`.

Once we have `helloFn`, a reference to the inner function, `foo(..)` has finished and it would seem as if its scope should have gone away, meaning the `msg` variable would no longer exist. But that doesn't happen, because the inner function has a closure over `msg` that keeps it alive. The closed over `msg` variable survives for as long as the inner function (now referenced by `helloFn` in a different scope) stays around.

Uma vez que temos `helloFn`, uma referência à função interna, `foo(..)` terminou e parece que seu escopo deveria ter desaparecido, significando que a variável `msg` não existiria mais. Mas isso não acontecen, porque a função interna tem um fechamento sobre `msg` que a mantém viva. A variável fechada sobre `msg` sobrevive enquanto a função interna (agora referenciada por `helloFn` em um escopo diferente) permanece. 

Let's look at a few more examples of closure in action:
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

The inner function `identify()` has closure over the parameter `name`.

A função interna `identify()` tem closure sobre o parâmetro `nome`.

The access that closure enables is not restricted to merely reading the variable's original value -- it's not just a snapshot but rather a live link. You can update the value, and that new current state remains remembered until the next access:

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

**Warning:** For reasons that we'll explore in more depth later in the book, this example of using closure to remember a state that changes (`val`) is probably something you'll want to avoid where possible.

**Aviso:** Por razões que exploraremos com mais profundidade posteriormente neste livro, este exemplo de uso de closure para lembrar um estado que muda (`val`) é provavelmente algo que você deve evitar sempre que possível.

If you have an operation that needs two inputs, one of which you know now but the other will be specified later, you can use closure to remember the first input:

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

Normally, a `sum(..)` function would take both an `x` and `y` input to add them together. But in this example we receive and remember (via closure) the `x` value(s) first, while the `y` value(s) are separately specified later.

Normalmente, uma função `sum(..)` tomaria ambas as entradas `x` e `y` para adicioná-los. Mas, neste exemplo, recebemos e lembramos (por meio do fechamento) o(s) valor(es) de `x` primeiro, quanto o(s) valor(es) `y` são especificados separadamente mais tarde.

**Note:** This technique of specifying inputs in successive function calls is very common in FP, and comes in two forms: partial application and currying. We'll dive into them more thoroughly in [Chapter 3](ch3.md/#some-now-some-later).

**Nota:** Esta técnica de especificar entradas em chamadas de função sucessiva é muito comum em PF e vem em duas formas: aplicação parcial e currying. Vamos mergulhar neles mais profudamente no [Capítulo 3](ch3.md/#some-now-some-later). 

Of course, since functions are just values in JS, we can remember function values via closure:

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

Instead of distributing/repeating the `toUpperCase()` and `toLowerCase()` logic all over our code, FP encourages us to create simple functions that encapsulate -- a fancy way of saying wrapping up -- that behavior.

Em vez de distribuir/repetir as lógicas `toUpperCase()` e `toLowerCase()` em todo o nosso código, a PF nos incentiva a criar funções simples que encapsulam -- uma maneira elegante de dizer encerrando -- esse comportamento.

Specifically, we create two simple unary functions `lower(..)` and `upperFirst(..)`, because those functions will be much easier to wire up to work with other functions in the rest of our program.

Especificamente, criamos duas funções unárias simples `lower(..)` e `upperFirst(..)`, porque essas funções serão muito mais fáceis de conectar para trabalhar com outras funções no resto do nosso programa.

**Tip:** Did you spot how `upperFirst(..)` could have used `lower(..)`?

**Dica:** Você percebeu como `upperFirst(..)` poderia ter usado `lower(..)`?

We'll use closure heavily throughout the rest of the text. It may just be the most important foundational practice in all of FP, if not programming as a whole. Make sure you're really comfortable with it!

Usaremos muito as closures no restante do texto. Pode ser apenas a prática fundamental mais importante em todo a FP, se não a programação como um todo. Certifique-se de que você está realmente confortável com isso!

## Syntax

## Sintaxe

Before we move on from this primer on functions, let's take a moment to discuss their syntax.

Antes de prosseguirmos com esta introdução sobre funções, vamos discutir um pouco sobre sua sintaxe.

More than many other parts of this text, the discussions in this section are mostly opinion and preference, whether you agree with the views presented here or take opposite ones. These ideas are highly subjective, though many people seem to feel rather absolutely about them.

Mais do que muitas outras partes deste texto, as discussões nesta seção são principalmente opiniões e preferências, quer você concorde com as visões apresentadas aqui ou opte por outras. Essas idéias são altamente subjetivas, embora muitas pessoas acreditam nessas idéias.

Ultimately, you get to decide.

Em última análise, você decide.

### What's in a Name?

### O que há em um nome?

Syntactically speaking, function declarations require the inclusion of a name:

Sintaticamente falando, as declarações de função precisão incluir um nome:

```js
function helloMyNameIs() {
    // ..
}
```

But function expressions can come in both named and anonymous forms:

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

What exactly do we mean by anonymous, by the way? Specifically, functions have a `name` property that holds the string value of the name the function was given syntactically, such as `"helloMyNameIs"` or `"namedFunctionExpr"`. This `name` property is most notably used by the console/developer tools of your JS environment to list the function when it participates in a stack trace (usually from an exception).

O que exatamente queremos dizer com anônimo, a propósito? Especificamente, as funções tem uma propriedade `nome` que contém o valor da string do nome que a função foi dada sintaticamente, como `"helloMyNameIs"` ou `"namedFunctionExpr"`. Esta propriedade `nome` é mais notavelmente usada pelas ferramentas de console/desenvolvedor de seu ambiente JS para listar a função quando ela participa de um rastreamento de pilha (geralmente de uma exceção).

Anonymous functions are generally displayed as `(anonymous function)`.

As funções anônimas são geralmente exibidas como `(função anônima)`.

If you've ever had to debug a JS program from nothing but a stack trace of an exception, you probably have felt the pain of seeing `(anonymous function)` appear line after line. This listing doesn't give a developer any clue whatsoever as to the path the exception came from. It's not doing the developer any favors.

Se você ja teve que debugar um programa JS de nada além de um rastreamento de pilha de uma exceção, provavelmente sentiu a dor de ver `(função anônima)` aparecer linha após linha. Esta lista não da ao desenvolvedor nenhuma pista quanto ao caminho onde veio a exceção. Não está fazendo nenhum favor ao desenvolvedor.

If you name your function expressions, the name is always used. So if you use a good name like `handleProfileClicks` instead of `foo`, you'll get much more helpful stack traces.

Se você nomear suas expressões de função, o nome será sempre usado. Portanto, se você usar um bom nome como `handleProfileClicks` em veze de `foo`, você obterá rastreamentos de pilha muito mais úteis. 

As of ES6, anonymous function expressions are in certain cases aided by _name inferencing_. Consider:

A partir do ES6, as expressões de função anônima são, em certos casos, auxiliadas por _inferência de nome_ Considere:

```js
var x = function () {};

x.name; // x
```

If the engine is able to guess what name you _probably_ want the function to take, it will go ahead and do so.

Se a máquina for capaz de adivinhar que nome você _provavelmente_ deseja que a função assuma, ela o fará.

But beware, not all syntactic forms benefit from name inferencing. Probably the most common place a function expression shows up is as an argument to a function call:

Mas cuidado, nem todas as formas sintáticas se beneficiam da inferência de nomes. Provavelmente o lugar mais comum em que uma expressão de função aparece é como argumento para uma chamada de função:

```js
function foo(fn) {
    console.log(fn.name);
}

var x = function () {};

foo(x); // x
foo(function () {}); //
```

When the name can't be inferred from the immediate surrounding syntax, it remains an empty string. Such a function will be reported as `(anonymous function)` in a stack trace should one occur.

Quando o nome não pode ser inferido dentro dos parentêses, ele permanece uma string vazia. Tal função será relatada como `(função anônima)` em um rastreamento de pilha, caso ocorra.

There are other benefits to a function being named besides the debugging question. First, the syntactic name (aka lexical name) is useful for internal self-reference. Self-reference is necessary for recursion (both sync and async) and also helpful with event handlers.

Existem outros benefícios em um função sendo nomeada, além da questão de depuração. Primeiro, o nome sintático (também conhecido como nome léxico) é útil para autorreferência interna. A auto referência é necessária para recursão (em sincronismo e assincronismo) e também é útil com manipuladores de eventos.

Consider these different scenarios:

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

In all these cases, the named function's lexical name was a useful and reliable self-reference from inside itself.

Em todos esses casos, o nome léxico da função nomeada era uma auto-referência útil e confiável de dentro dela mesma. 

Moreover, even in simple cases with one-liner functions, naming them tends to make code more self-explanatory and thus easier to read for those who haven't read it before:

Além disso, mesmo em casos simples com funções de uma linha, nomeá-los tende a tornar o código mais auto explicativo e, portanto, mais fácil de ler para quem nunca o leu antes:

```js
people.map(function getPreferredName(person) {
    return person.nicknames[0] || person.firstName;
});
// ..
```

The function name `getPreferredName(..)` tells the reader something about what the mapping operation is intending to do that is not entirely obvious from just its code. This name label helps the code be more readable.

O nome da função `getPreferredName(..)` diz ao leitor algo sobre o que a operação de mapeamento pretende fazer que não é totalmente óbvio apenas pelo seu código. Este rótulo de nome ajuda o código a ser mais legível.

Another place where anonymous function expressions are common is with immediately invoked function expressions (IIFEs):

Outro lugar onde as expressões de função anônimas são comuns é com expressões de função imediatamente invocadas (IIFEs):

```js
(function () {
    // look, I'm an IIFE!
})();
```

You virtually never see IIFEs using names for their function expressions, but they should. Why? For all the same reasons we just went over: stack trace debugging, reliable self-reference, and readability. If you can't come up with any other name for your IIFE, at least use the word IIFE:

Você virtualmente nunca vê IIFEs usando nomes para suas expressões de função, mas deveriam. Porque? Pelas mesmas razões que acabamos de examinar: depuração de rastreamento de pilha, auto-referência confiável e legibilidade. Se você não conseguir encontrar outro nome para seu IIFE, pelo menos use a palavra IIFE:

```js
(function IIFE() {
    // You already knew I was an IIFE!
})();
```

What I'm getting at is there are multiple reasons why **named functions are always more preferable to anonymous functions.** As a matter of fact, I'd go so far as to say that there's basically never a case where an anonymous function is more preferable. They just don't really have any advantage over their named counterparts.

O que estou querendo dizer é que existem várias razões pelas quais **funções nomeadas são sempre mais preferíveis a funções anônimas.** Na verdade, eu diria que basicamente nunca há um caso em que uma função anônima é mais preferível. Elas simplesmente não têm nenhuma vantagem sobre suas contrapartes nomeadas.

It's incredibly easy to write anonymous functions, because it's one less name we have to devote our mental attention to figuring out.

É incrivelmente fácil escrever funções anônimas, porque é um nome a menos que temos que dedicar nossa atenção mental para descobrir.

I'll be honest; I'm as guilty of this as anyone. I don't like to struggle with naming. The first few names I come up with for a function are usually bad. I have to revisit the naming over and over. I'd much rather just punt with a good ol' anonymous function expression.

Eu serei honesto; Sou tão culpado disso quanto qualquer um. Eu não gosto de lutar com nomes. Os primeiros nomes que penso para uma função geralmente são ruins. Tenho que revisitar a nomeação repetidamente. Eu prefiro apenas usar uma boa expressão de função anônima.

But we're trading ease-of-writing for pain-of-reading. This is not a good trade-off. Being lazy or uncreative enough to not want to figure out names for your functions is an all too common, but poor, excuse for using anonymous functions.

Mas estamos trocando facilidade de escrita por dor de leitura. Esta não é uma boa troca. Ser preguiçoso ou pouco criativo para não querer descobrir nomes para suas funções é uma desculpa muito comum, mas pobre, para usar funções anônimas.

**Name every single function.** And if you sit there stumped, unable to come up with a good name for some function you've written, I'd strongly suggest you don't fully understand that function's purpose yet -- or it's just too broad or abstract. You need to go back and re-design the function until this is more clear. And by that point, a name will become more apparent.

**Nomeia toda função.** E se você ficar perplexo, incapaz de encontrar um bom nome para alguma função que escreveu, sugiro que você ainda entenda totalmente o propósito dessa função -- ou ela é muito ampla ou abstrata. Você precisa voltar e redesenhar a função até que isso fique mais claro. E nesse ponto, um nome ficará mais aparente.

In my practice, if I don't have a good name to use for a function, I name it `TODO` initially. I'm certain that I'll at least catch that later when I search for "TODO" comments before committing code.

Na minha prática, se não tenho um bom nome para usar em uma função, eu a chamo de `TODO` inicialmente. Tenho certeza de que pelo menos entenderei isso mais tarde, quando pesquisar os comentários `"TODO"` antes de enviar o código.

I can testify from my own experience that in the struggle to name something well, I usually have come to understand it better, later, and often even refactor its design for improved readability and maintainability.

Posso testemunhar por experiência própria que, na luta para nomear bem algo, geralmente passei a entendê-lo melhor, mais tarde, e muitas vezes até refatorar seu formato para facilitar a leitura e a manutenção.

This time investment is well worth it.

Esse investimento de tempo vale a pena.

### Functions Without `function`

### Funções sem `função`

So far we've been using the full canonical syntax for functions. But you've no doubt also heard all the buzz around the ES6 `=>` arrow function syntax.

Até agora, usamos a sintaxe canônica completa para funções. Mas você, sem dúvida, também ouvir todo barulho em torno da sintaxe de arrow functions `=>`.

Compare:

Compare:

```js
people.map(function getPreferredName(person) {
    return person.nicknames[0] || person.firstName;
});

// vs.

people.map((person) => person.nicknames[0] || person.firstName);
```

Whoa.

Whoa.

The keyword `function` is gone, so is `return`, the parentheses (`( )`), the curly braces (`{ }`), and the innermost semicolon (`;`). In place of all that, we used a so-called fat arrow symbol (`=>`).

A palavra-chave `função` se foi, assim como `return`, os parênteses (`( )`), as chaves (`{}`) e o ponto e vírgula mais interno (`;`). No lugar de tudo isso, usamos o chamado símbola de seta grande (`=>`).

But there's another thing we omitted. Did you spot it? The `getPreferredName` function name.

Mas há outra coisa que omitimos. Você percebeu? O nome da função `getPreferredName`.

That's right; `=>` arrow functions are lexically anonymous; there's no way to syntactically provide it a name. Their names can be inferred like regular functions, but again, the most common case of function expression values passed as arguments won't get any assistance in that way. Bummer.

Isso mesmo; `=>` as funções de seta são lexicamente anônimas; não há como fornecer um nome sintaticamente. Seus nomes podem ser inferidos como funçẽos regulares, mas, novamente, o caso mais comum de valores de expressão passados como argumentos de funções não teria nenhuma ajuda dessa forma. 

If `person.nicknames` isn't defined for some reason, an exception will be thrown, meaning this `(anonymous function)` will be at the top of the stack trace. Ugh.

Se `person.nicknames` não for definido por algum motivo, uma exceção será lançada, o que significa que esta `(função anônima)` estará no topo do stack trace. ECA.

Honestly, the anonymity of `=>` arrow functions is a `=>` dagger to the heart, for me. I cannot abide by the loss of naming. It's harder to read, harder to debug, and impossible to self-reference.

Honestamente, o anonimato das funções de seta `=>` é um `=>` punhal no coração, para mim. Não posso tolerar a perda de um nome. É mais difícil de ler, mais difícil de debugar e impossível de auto referênciar.

But if that wasn't bad enough, the other slap in the face is that there's a whole bunch of subtle syntactic variations that you must wade through if you have different scenarios for your function definition. I'm not going to cover all of them in detail here, but briefly:

Mas se isso não fosse ruim o suficiente, o outro tapa na cara é que há um monte de variações sintáticas sutis que você que você deve percorrer se tiver diferentes cenários para a definição de sua função. Não vou cobrir todos eles em detalhes aqui, mas resumidamente:

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

The case for excitement over `=>` in the FP world is primarily that it follows almost exactly from the mathematical notation for functions, especially in FP languages like Haskell. The shape of `=>` arrow function syntax communicates mathematically.

O caso de empolgação com `=>` no mundo da PF é principalmente que ela segue quase que exatamente a notação matemática para funções, especialmente em linguagens de PF como Haskell. A forma da sintaxe da função de seta `=>` se comunica matematicamente.

Digging even further, I'd suggest that the argument in favor of `=>` is that by using much lighter-weight syntax, we reduce the visual boundaries between functions which lets us use simple function expressions much like we'd use lazy expressions -- another favorite of the FPer.

Indo ainda mais longe, sugiro que um argumento a favor de `=>` é que usando uma sintaxe muito mais leve, reduzimos os limites visuais entre as funções, o que nos permite usar expressões de função simples da mesma forma que usaríamos expressões preguiçõsas -- outro favorito dos programadores funcionais.

I think most FPers are going to wave off the concerns I'm sharing. They love anonymous functions and they love saving on syntax. But like I said before: you decide.

Acho que a maioria dos programadores funcionais vai ignorar as preocupações que estou compartilhando. Eles adoram funções anônimas e adoram economizar na sintaxe. Mas como eu disse antes: você decide.

**Note:** Though I do not prefer to use `=>` in practice in my production code, they are useful in quick code explorations. Moreover, we will use arrow functions in many places throughout the rest of this book -- especially when we present typical FP utilities -- where conciseness is preferred to optimize for the limited physical space in code snippets. Make your own determinations whether this approach will make your own production-ready code more or less readable.

**Nota:** Embora eu não prefira usar `=>` na prática em meu código de produção, eles são úteis em explorações de código rápidas. Além disso, usaremos as funções de seta em muitos lugares no restante deste livro -- especialmente quando apresentamos utilitários da PF típicos -- onde a concisão é preferida para otimizar o espaço físico limitado em trechos de código. Faça suas próprias escolhas se essa abordagem tornará seu próprio código pronto para produção mais ou menos legível. 

## What's This?

## O que é isso?

If you're not familiar with the `this` binding rules in JavaScript, I recommend checking out my book _You Don't Know JS: this & Object Prototypes_. For the purposes of this section, I'll assume you know how `this` gets determined for a function call (one of the four rules). But even if you're still fuzzy on _this_, the good news is we're going to conclude that you shouldn't be using `this` if you're trying to do FP.

Se você não está familiarizado com as regras de vinculação `this` em JavaScript, recomendo dar uma olhada no meu livro _You Don't Know JS: this & Object Prototypes_. Para os fins desta seção, assumirei que você sabe como o `this` é determinado para uma chamada de função (uma das quatro regras). Mas mesmo que você ainda esteja confuso com _this_, a boa notícia é que vamos concluir que você não deve usar `this` se estiver tentando fazer PF.

**Note:** We're tackling a topic that we'll ultimately conclude we shouldn't use. Why!? Because the topic of `this` has implications for other topics covered later in this book. For example, our notions of function purity are impacted by `this` being essentially an implicit input to a function (see [Chapter 5](ch5.md)). Additionally, our perspective on `this` affects whether we choose array methods (`arr.map(..)`) versus standalone utilities (`map(..,arr)`) (see [Chapter 9](ch9.md)). Understanding `this` is essential to understanding why `this` really should _not_ be part of your FP!

**Observação:** Estamos abordando um tópico que, em última análise, concluiremos que não deve ser usado. Por que!? Porque o tópico `this` tem implicações para outros tópicos cobertos posteriormente neste livro. Por exemplo, nossas noções de pureza de função são impactadas por `this` ser essencialmente uma entrada implícita para uma função (consulte [Capítulo 5](ch5.md)). Além disso, nossa perspectiva sobre `this` afeta se escolhemos métodos de array (`arr.map(..)`) contra utilitários autônomos (`map(..,arr)`) (veja [Capítulo 9](ch9.md)). Entender `this` é essencial para entender porque `this` realmente _não_ deve fazer parte da sua PF!

JavaScript `function`s have a `this` keyword that's automatically bound per function call. The `this` keyword can be described in many different ways, but I prefer to say it provides an object context for the function to run against.

As `funções` JavaScript têm uma palavra-chave `this` que é automaticamente vinculada por chamada de função. A palavra-chave `this` pode ser descritas de muitas maneiras diferentes, mas prefiro dizer que ela fornece um contexto de objeto para a execução da função.

`this` is an implicit parameter input for your function.

`this` é uma entrada de parâmetro implícita para sua função.

Consider:

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

Of course, if `this` can be input into a function implicitly, the same object context could be sent in as an explicit argument:

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

Simpler. And this kind of code will be a lot easier to deal with in FP. It's much easier to wire multiple functions together, or use any of the other input wrangling techniques we will get into in the next chapter, when inputs are always explicit. Doing them with implicit inputs like `this` ranges from awkward to nearly impossible depending on the scenario.

Mais simples. E esse tipo de código será muito mais fácil de lidar na PF. É muito mais fácil conectar várias funções ou usar qualquer uma das outras técnicas de transformação de entrada que veremos no próximo capítulo, qunado as entradas são sempre explícitas. Fazê-los com entradas implícitas como `this` varia de estranho a quase impossível dependendo do cenário.

There are other tricks we can leverage in a `this`-based system, including prototype-delegation (also covered in detail in _You Don't Know JS: this & Object Prototypes_):

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

**Note:** `Object.assign(..)` is an ES6+ utility for doing a shallow assignment copy of properties from one or more source objects to a single target object: `Object.assign( target, source1, ... )`.

**Nota:** `Object.assign(..)` é um utilitário ES6+ para fazer uma cópia de atribuição superficial de propriedades de um ou mais objetos de origem para um único objeto de destino: `Object.assign (target, source1, ...)`.

In case you're having trouble parsing what this code does: we have two separate objects `Login` and `Auth`, where `Login` performs prototype-delegation to `Auth`. Through delegation and the implicit `this` context sharing, these two objects virtually compose during the `this.authorize()` function call, so that properties/methods on `this` are dynamically shared with the `Auth.authorize(..)` function.

Caso você esteja tendo problemas para analisar o que este código faz: nós temos dois objetos separados `Login` e `Auth`, onde `Login` executa a delegação de protótipo para `Auth`. Atravéz da delegação e do compartilhamento de contexto implícito `this`, esses dois objetos virtualmente compõem durante a chamada da função `this.authorize()`, de modo que as propriedades/métodos em `this` são dinamicamente compartilhados com a função `Auth.authorize(..)`. 

_This_ code doesn't fit with various principles of FP for a variety of reasons, but one of the obvious hitches is the implicit `this` sharing. We could be more explicit about it and keep code closer to FP-friendly style:

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

From my perspective, the problem is not with using objects to organize behavior. It's that we're trying to use implicit input instead of being explicit about it. When I'm wearing my FP hat, I want to leave `this` stuff on the shelf.

Do meu ponto de vista, o problema não é usar objetos para organizar o comportamento. É que estamos tentando usar entradas implícitas em vez de ser explícitos sobre isso. Quando estou usando meu boné da PF, quero deixar "essas coisas" na prateleira.

## Summary

## Resumo

Functions are powerful.

Funções são poderosas.

But let's be clear what a function is. It's not just a collection of statements/operations. Specifically, a function needs one or more inputs (ideally, just one!) and an output.

Mas vamos deixar claro o que é uma função. Não é apenas uma coleções de instruções/operações. Especificamente, uma função precisa de uma ou mais entradas (de preferência, apenas uma!) e uma saída.

Functions inside of functions can have closure over outer variables and remember them for later. This is one of the most important concepts in all of programming, and a fundamental foundation of FP.

Funções dentro de funções podem ter fechamento sobre variáveis externas e lembrá-las para depois. Este é um dos conceitos mais importantes em toda a programação e uma base fundamental da PF.

Be careful of anonymous functions, especially `=>` arrow functions. They're convenient to write, but they shift the cost from author to reader. The whole reason we're studying FP here is to write more readable code, so don't be so quick to jump on that bandwagon.

Tenha cuidado com as funções anônimas, especialmente as funções de seta `=>`. Elas são convenientes de escrever, mas transferem o custo do autor para o leitor. O motivo pelo qual estamos estudando PF aqui é para escrever um código mais legível, então não se precipite nesse momento.

Don't use `this`-aware functions. Just don't.

Não use `this` conscientemente em funções. Não faça isso.

You should now be developing a clear and colorful perspective in your mind of what _function_ means in Functional Programming. It's time to start wrangling functions to get them to interoperate, and the next chapter teaches you a variety of critical techniques you'll need along the way.

Agora você deve estar desenvolvendo uma perspectiva clara e colorida em sua mente do que _função_ significa em Programação Funcional. É hora de começar a organizar funções para fazẽ-las interoperar, e o próximo capítulo ensina uma variedade de técnicas críticas de que você precisará ao longo do caminho.
