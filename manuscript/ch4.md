# Functional-Light JavaScript
# Chapter 4: Composing Functions
# Capítulo 4: Funções de composição

By now, I hope you're feeling much more comfortable with what it means to use functions for functional programming.

Por agora, espero que você esteja se sentindo muito mais confortável com o que significa usar funções para programação funcional.

A functional programmer sees every function in their program like a simple little Lego piece. They recognize the blue 2x2 brick at a glance, and know exactly how it works and what they can do with it. When they begin building a bigger, more complex Lego model, as they need each next piece, they already have an instinct for which of their many spare pieces to grab.

Um programador funcional vê cada função em seu programa como uma peça de Lego. Eles reconhecem o tijolo 2x2 azul de relance e sabem exatamente como funciona e o que podem fazer com ele. Quando eles começam a construir um modelo de Lego maior e mais complexo, elas já têm um instinto de qual de de suas muitas peças sobressalentes utilizar.

But sometimes you take the blue 2x2 brick and the gray 4x1 brick and put them together in a certain way, and you realize, "that's a useful piece that I need often".

Mas às vezes você pega o tijolo 2x2 azul e o tijolo 4x1 cinza e os coloca juntos de uma certa maneira, e você percebe, "essa é uma peça útil que eu preciso frequentemente".

So now you've come up with a new "piece", a combination of two other pieces, and you can reach for that kind of piece now anytime you need it. It's more effective to recognize and use this compound blue-gray L-brick thing where it's needed than to separately think about assembling the two individual bricks each time.

Portanto, agora você criou uma nova "peça", uma combinação de duas outras peças, e pode pegar esse tipo de peça sempre que precisar. É mais eficaz reconhecer e usar essa coisa composta de tijolo L cinza-azulado onde for necessário do que pensar separadamente em montar os dois tijolos individuais a cada vez.

Functions come in a variety of shapes and sizes. And we can define a certain combination of them to make a new compound function that will be handy in various parts of the program. This process of using functions together is called composition.

As funções vêm em uma variedade de formas e tamanhos. E podemos definir uma certa combinação deles para fazer uma nova função composta que será útil em varias partes do programa. Esse processo de usar funções juntas é chamado de composição.

Composition is how an FPer models the flow of data through the program. In some senses, it's the most foundational concept in all of FP, because without it, you can't declaratively model data and state changes. In other words, everything else in FP would collapse without composition.

Composição é como um programador funcional modela o fluxo de dados através do programa. Em alguns sentidos, é o conceito mais fundamental em toda a PF, porque sem ele, você não pode modelar dados e mudanças de estado declarativamente. Em outras palavras, tudo o mais na PF entraria em colapso sem composição.

## Output to Input

## Saída para entrada

We've already seen a few examples of composition. For example, our discussion of [`unary(..)` in Chapter 3](ch3.md/#user-content-unary) included this expression: [`[..].map(unary(parseInt))`](ch3.md/#user-content-mapunary). Think about what's happening there.

Já vimos alguns exemplos de composição. Por exemplo, nossa discussão sobre [`unary(..)` no Capítulo3](ch3.md/#user-content-unary) incluiu esta expressão: [`[..].map(unary(parseInt))`](ch3.md/user-content-mapunary). Pense no que está acontecendo lá.

To compose two functions together, pass the output of the first function call as the input of the second function call. In `map(unary(parseInt))`, the `unary(parseInt)` call returns a value (a function); that value is directly passed as an argument to `map(..)`, which returns an array.

Para compor duas funções juntas, passe a saída da primeira chamada de função como a entrada da segunda chamada de função. Em `map(unary(parseInt))`, a chamada `unary(parseInt)` return um valor (uma função), esse valor é diretamente passado como um argumento para `map(..)`, que retorna um array.

To take a step back and visualize the conceptual flow of data, consider:

Para dar um passo para trás e visualizar o fluxo conceitual de dados, considere:

```txt
arrayValue <-- map <-- unary <-- parseInt
```

`parseInt` is the input to `unary(..)`. The output of `unary(..)` is the input to `map(..)`. The output of `map(..)` is `arrayValue`. This is the composition of `map(..)` and `unary(..)`.

`parseInt` é a entrada para `unary(..)`. A saída de `unary(..)` é a entrada para `map(..)`. A saída de `map(..)` é `arrayValue`. Esta é a composição de `map(..)` e `unary(..)`.

**Note:** The right-to-left orientation here is on purpose, though it may seem strange at this point in your learning. We'll come back to explain that more fully later.

**Nota:** A orientação da direita para a esquerda aqui é proposital, embora possa parecer estranho neste ponto do seu aprendizado. Voltaremos para explicar isso detalhadamente mais tarde.

Think of this flow of data like a conveyor belt in a candy factory, where each operation is a step in the process of cooling, cutting, and wrapping a piece of candy. We'll use the candy factory metaphor throughout this chapter to explain what composition is.

Pense neste fluxo de dados como uma correia transportadora em uma fábrica de doces, onde cada operação é uma etapa no processo de resfriamento, corte e embalagem de um pedaço de doce. Usaremos a metáfora da fábrica de doces ao longo deste capítulo para explicar o que é composição.

<p align="center">
    <img src="images/fig2.png">
</p>

Let's examine composition in action one step at a time. Consider these two utilities you might have in your program:

Vamos examinar a composição em ação com um passo de cada vez. Considere estes dois utilitários que você talvez tenha em seu programa:

```js
function words(str) {
    return String( str )
        .toLowerCase()
        .split( /\s|\b/ )
        .filter( function alpha(v){
            return /^[\w]+$/.test( v );
        } );
}

function unique(list) {
    var uniqList = [];

    for (let v of list) {
        // value not yet in the new list?
        if (uniqList.indexOf( v ) === -1 ) {
            uniqList.push( v );
        }
    }

    return uniqList;
}
```

`words(..)` splits a string into an array of words. `unique(..)` takes a list of words and filters it to not have any repeat words in it.

`words(..)` divide uma string em um array de palavras. `unique(..)` pega uma lista de palavras e filtra para não ter palavras repetidas.

To use these two utilities to analyze a string of text:

Como usar esses dois utilitários para analisar uma string de texto:

```js
var text = "To compose two functions together, pass the \
output of the first function call as the input of the \
second function call.";

var wordsFound = words( text );
var wordsUsed = unique( wordsFound );

wordsUsed;
// ["to","compose","two","functions","together","pass",
// "the","output","of","first","function","call","as",
// "input","second"]
```

We name the array output of `words(..)` as `wordsFound`. The input of `unique(..)` is also an array, so we can pass the `wordsFound` into it.

Nomeamos a saída do array de `words(..)` como `wordsFound`. A entrada de `unique(..)` é também um array, então podemos passar o `wordsFound` para ele.

Back to the candy factory assembly line: the first machine takes as "input" the melted chocolate, and its "output" is a chunk of formed and cooled chocolate. The next machine a little down the assembly line takes as its "input" the chunk of chocolate, and its "output" is a cut-up piece of chocolate candy. Next, a machine on the line takes small pieces of chocolate candy from the conveyor belt and outputs wrapped candies ready to bag and ship.

De volta para à linha de montagem da fábrica de doces: a primeira linha da máquina leva como "entrada" o chocolate derretido, e sua "saida" é um pedaço de chocolate formado e resfriado. A proxima máquina um pouco abaixo na linha de montagem toma como "entrada" o pedaço de chocolate, e sua "saída" é um pedaço cortado de bombom de chocolate. Em seguida, uma máquina na linha pega pequenos pedaços de chocolate da esteira transportadora e produz os doces embalados prontos para embalar e enviar.

<img src="images/fig3.png" align="right" width="9%" hspace="20">

The candy factory is fairly successful with this process, but as with all businesses, management keeps searching for ways to grow.

A fábrica de doces é muito bem sucedida nesse processo, mas, como acontece com todos os negócios, a administração continua buscando maneiras de crescer.

To keep up with demand for more candy production, they decide to take out the conveyor belt contraption and just stack all three machines on top of one another, so that the output valve of one is connected directly to the input valve of the one below it. There's no longer sprawling wasted space where a chunk of chocolate slowly and noisily rumbles down a conveyor belt from the first machine to the second.

Para acompanhar a demanda por mais produção de doces, eles decidem retirar a engenhoca da correia transportadora e apenas empilhar as três máquinas uma em cima da outra, de modo que a válvula de saída de uma seja conectada diretamente à válvula de entrada da que está abaixo dela.

This innovation saves a lot of room on the factory floor, so management is happy they'll get to make more candy each day!

Esta inovação economiza muito espaço no chão de fábrica, então a gerência fica feliz em fazer mais doces a cada dia!

The code equivalent of this improved candy factory configuration is to skip the intermediate step (the `wordsFound` variable in the earlier snippet), and just use the two function calls together:

O código equivalente a essa configuração de fábrica de doces aprimorada é pular a etapa intermediária (a variável `wordsFound` no snippet anterior) e apenas usar as duas chamadas de função juntas:

```js
var wordsUsed = unique( words( text ) );
```

**Note:** Though we typically read the function calls left-to-right -- `unique(..)` and then `words(..)` -- the order of operations will actually be more right-to-left, or inner-to-outer. `words(..)` will run first and then `unique(..)`. Later we'll talk about a pattern that matches the order of execution to our natural left-to-right reading, called `pipe(..)`.

**Nota:** Embora normalmente leiamos as chamadas de função da esquerda para a direita -- `unique(..)` e, em seguida, `words(..)` -- a ordem das operações será, na verdade, mais da direita para esquerda, ou interno para externo. `words(..)` será executado primeiro e, em seguida, `exclusivo(..)`. Mais tarde, falaremos sobre um padrão que corresponde à ordem de execução de nossa leitura natural da esquerda para a direita, chamado `pipe(..)`.

The stacked machines are working fine, but it's kind of clunky to have the wires hanging out all over the place. The more of these machine-stacks they create, the more cluttered the factory floor gets. And the effort to assemble and maintain all these machine stacks is awfully time intensive.

As máquinas empilhadas estão funcionando bem, mas é meio desajeitado ter os fios espalhados por todo o lugar. Quaanto mais pilhas de máquinas eles criam, mais desordenado fica o chão de fábrica. E o esforço para montar e manter todas essas pilhas de máquinas consome muito tempo.

<img src="images/fig4.png" align="left" width="15%" hspace="20">

One morning, an engineer at the candy factory has a great idea. She figures that it'd be much more efficient if she made an outer box to hide all the wires; on the inside, all three of the machines are hooked up together, and on the outside everything is now neat and tidy. On the top of this fancy new machine is a valve to pour in melted chocolate and on the bottom is a valve that spits out wrapped chocolate candies. Brilliant!

Certa manhã, um engenheiro da fábrica de doces tem uma ótima ideia. Ela acha que seria muito mais eficiente se ela fizesse uma caixa externa para esconder todos os fios, por dentro, todas as três máquinas estão conectadas e, por fora, tudo ficaria limpo e organizado. No topo dessa nova máquina sofisticada está uma válvula para despejar chocolate derretido e, na parte inferior, uma válvula que cospe bombons de chocolate embrulhados. Brilhante!

This single compound machine is much easier to move around and install wherever the factory needs it. The workers on the factory floor are even happier because they don't need to fidget with buttons and dials on three individual machines anymore; they quickly prefer using the single fancy machine.

Esta máquina de composto único é muito mais fácil de mover e instalar onde quer que a fábrica precise. Os trabalhadores do chão de fábrica estão ainda mais felizes porque não precisam mais se preocupar com botões e mostradores em três máquinas individuais, eles rapidamente preferem usar a máquina sofisticada.

Relating back to the code: we now realize that the pairing of `words(..)` and `unique(..)` in that specific order of execution (think: compound Lego) is something we could use in several other parts of our application. So, let's define a compound function that combines them:

Em relação ao código: agora percebemos que o emparelhamento de `palavras(..)` e `único(..)` naquela ordem específica de execução (pense: Lego composto) é algo que poderiamos usar em várias outras partes da nossa aplicação. Então, vamos definir uma função composta que os combina:

```js
function uniqueWords(str) {
    return unique( words( str ) );
}
```

`uniqueWords(..)` takes a string and returns an array. It's a composition of the two functions: `unique(..)` and `words(..)`; it creates this flow of data:

```txt
wordsUsed <-- unique <-- words <-- text
```

You probably recognize it by now: the unfolding revolution in candy factory design is function composition.

Você provavelmente já deve ter percebido: a revolução que se desenrola no design da fábrica de doces é a composição de funções.

### Machine Making

### Fabricação de Máquinas

The candy factory is humming along nicely, and thanks to all the saved space, they now have plenty of room to try out making new kinds of candies. Building on the earlier success, management is keen to keep inventing new fancy compound machines for their growing candy assortment.

A fábrica de doces está funcionando muito bem, e, graças a todo o espaço economizado, eles agora têm espaço de sobre para experimentar fazer novos tipos de doces. Com base no sucesso anterior, a administração está empenhada em continuar inventando novas máquinas sofisticadas de compostos para sua crescente variedade de doces.

But the factory engineers struggle to keep up, because each time a new kind of fancy compound machine needs to be made, they spend quite a bit of time making the new outer box and fitting the individual machines into it.

Mas os engenheiros da fábrica lutam para acompanhar, porque cada vez que um novo tipo de máquina composta precisa ser feita, eles passam um bom tempo fazendo a nova caixa externa e encaixando as máquinas individuais nela.

So the factory engineers contact an industrial machine vendor for help. They're amazed to find out that this vendor offers a **machine-making** machine! As incredible as it sounds, they purchase a machine that can take a couple of the factory's smaller machines -- the chocolate cooling one and the cutting one, for example -- and wire them together automatically, even wrapping a nice clean bigger box around them. This is surely going to make the candy factory really take off!

Assim, os engenheiros da fábrica entram em contato com um fornecedor de máquina industrial para obter ajuda. Eles ficam surpresos ao descobrir que este fornecedor oferece uma máquina de **fabricação de máquinas**! Por incrível que pareça, eles compram uma máquina que pode pegar algumas máquinas menores da fábrica -- a de resfriamento de chocolate e a de corte, por exemplo -- e conectá-las automaticamente, até mesmo envolvendo uma caixa maior e mais limpa em torno delas. Isso certamente fará a fábrica de doces realmente decolar!

<p align="center">
    <img src="images/fig5.png" width="50%">
</p>

Back to code land, let's consider a utility called `compose2(..)` that creates a composition of two functions automatically, exactly the same way we did manually:

De volta ao mundo do código, vamos considerar um utilitário chamado `compose2(..)` que cria uma composição de duas funções automaticamente, exatamente da mesma forma que fizemos manualmente:

```js
function compose2(fn2,fn1) {
    return function composed(origValue){
        return fn2( fn1( origValue ) );
    };
}

// or the ES6 => form
var compose2 =
    (fn2,fn1) =>
        origValue =>
            fn2( fn1( origValue ) );
```

Did you notice that we defined the parameter order as `fn2,fn1`, and furthermore that it's the second function listed (aka `fn1` parameter name) that runs first, then the first function listed (`fn2`)? In other words, the functions compose from right-to-left.

Você notou que definimos a ordem dos parâmetros como `fn2,fn1`, e, além disso, que é a segunda função listada (também conhecida como nome do parâmetro `fn1`) que executa primeiro, depois a primeira função listada (`fn2`)? Em outras palavras, as funções são compostas da direita para esquerda.

That may seem like a strange choice, but there are some reasons for it. Most typical FP libraries define their `compose(..)` to work right-to-left in terms of ordering, so we're sticking with that convention.

Pode parecer uma escolha estranha, mas existem algumas razões para isso. A maioria das bibliotecas típicas da PF definem seu `compose(..)` para trabalhar da direita para a esquerda em termos de ordenação, então estamos mantendo essa convenção.

But why? I think the easiest explanation (but perhaps not the most historically accurate) is that we're listing them to match the order they are written in code manually, or rather the order we encounter them when reading from left-to-right.

Mas por que? Acho que a explicação mais fácil (mas talvez não a mais historicamente precisa) é que os listamos para corresponder à ordem em que são escritos manualmente no código, ou melhor, a ordem em que os encontramos ao ler da esquerda para a direita.

`unique(words(str))` lists the functions in the left-to-right order `unique, words`, so we make our `compose2(..)` utility accept them in that order, too. The execution order is right-to-left, but the code order is left-to-right. Pay close attention to keep those distinct in your mind.

`unique(words(str))` lista as funções na ordem da esquerda para a direita `unique, words`, então fazemos nosso utilitário `compose2(..)` aceitá-las nessa ordem também. A ordem de execução é da direita para a esquerda, mas a ordem do código é da esquerda para a direita. Preste muita atenção para mantê-los distintos em sua mente.

Now, the more efficient definition of the candy making machine is:

Agora, a definição mais eficiente da máquina de fazer doces é:

```js
var uniqueWords = compose2( unique, words );
```

### Composition Variation

### Variação de Composição

It may seem like the `<-- unique <-- words` combination is the only order these two functions can be composed. But we could actually compose them in the opposite order to create a utility with a bit of a different purpose:

Pode parecer que a combinação `<-- unique <-- words` é a única ordem em que essas duas funções podem ser compostas. Mas poderíamos realmente compồ-los na ordem oposta para criar um utilitário com um propósito um pouco diferente:

```js
var letters = compose2( words, unique );

var chars = letters( "How are you Henry?" );
chars;
// ["h","o","w","a","r","e","y","u","n"]
```

This works because the `words(..)` utility, for value-type safety sake, first coerces its input to a string using `String(..)`. So the array that `unique(..)` returns -- now the input to `words(..)` -- becomes the string `"H,o,w, ,a,r,e,y,u,n,?"`, and then the rest of the behavior in `words(..)` processes that string into the `chars` array.

Isso funciona porque o utilitário `words(..)`, para fins de segurança do tipo de valor, primeiro coage sua entrada para uma string usando `String(..)`. Assim, a matriz que retorna `unique(..)` -- agora a entrada para `words(..)` -- torna-se a string `"H,o,w, ,a,r,e,y,u,n,?"` e então o resto do comportamento em `palavras(..)` processa essa string no array `chars`.

Admittedly, this is a contrived example. But the point is that function compositions are not always unidirectional. Sometimes we put the gray brick on top of the blue brick, and sometimes we put the blue brick on top.

Reconhecidamente, este é um exemplo artificial. Mas a questão é que as composições de funções nem sempre são unidirecionais. Às vezes, colocamos o tijolo cinza em cima do tijolo azul, e às vezes colocamos o tijolo azul em cima.

The candy factory better be careful if they try to feed the wrapped candies into the machine that mixes and cools the chocolate!

A fábrica de doces deve ter cudiado se tentar colocar os doces embrulhados na máquina que mistura e esfria o chocolate!

## General Composition

## Composição Geral

If we can define the composition of two functions, we can just keep going to support composing any number of functions. The general data visualization flow for any number of functions being composed looks like this:

Se pudermos definir a composição de duas funções, podemos simplesmente continuar a suportar a composição de qualquer número de funções. O fluxo geral de visualização de dados para qualquer número de funções sendo compostas é semelhante a este:

```txt
finalValue <-- func1 <-- func2 <-- ... <-- funcN <-- origValue
```

<p align="center">
    <img src="images/fig6.png" width="50%">
</p>

Now the candy factory owns the best machine of all: a machine that can take any number of separate smaller machines and spit out a big fancy machine that does every step in order. That's one heck of a candy operation! It's Willy Wonka's dream!

Agora, a fábrica de doces possui a melhor máquina de todas: uma máquina que pode pegar qualquer número de máquinas menores separadas e cuspir uma grande máquina extravagante que executa cada passo na ordem. Essa é uma operação incrível de doces! É o sonho de Willy Wonka!

We can implement a general `compose(..)` utility like this:

Podemos implementar um utilitário `compose(..)` geral como este:

<a name="generalcompose"></a>

```js
function compose(...fns) {
    return function composed(result){
        // copy the array of functions
        var list = [...fns];

        while (list.length > 0) {
            // take the last function off the end of the list
            // and execute it
            result = list.pop()( result );
        }

        return result;
    };
}

// or the ES6 => form
var compose =
    (...fns) =>
        result => {
            var list = [...fns];

            while (list.length > 0) {
                // take the last function off the end of the list
                // and execute it
                result = list.pop()( result );
            }

            return result;
        };
```

**Warning:** `fns` is a collected array of arguments, not a passed-in array, and as such, it's local to `compose(..)`. It may be tempting to think the `[...fns]` would thus be unnecessary. However, in this particular implementation, `.pop()` inside the inner `composed(..)` function is mutating the list, so if we didn't make a copy each time, the returned composed function could only be used reliably once. We'll revisit this hazard in [Chapter 6](ch6.md/#user-content-hiddenmutation).

**Aviso:** `fns` é um array coletado de argumentos, não um array passado e, como tal, é local para `compose(..)`. Pode ser tentador pensar que `[...fns]` seria desnecessário. No entanto, nesta implementação particular, `.pop()` dentro da função interna `composed(..)` está alterando a lista, portanto, se não fizéssemos uma cópia a cada vez, a função composta retornada só poderia ser usada de forma confiável uma vez. Revisitaremos esse perigo no [Capítulo 6](ch6.md/#user-content-hiddenmutation).

Now let's look at an example of composing more than two functions. Recalling our `uniqueWords(..)` composition example, let's add a `skipShortWords(..)` to the mix:

Agora vamos ver um exemplo de composição de mais de duas funções. Recordando nosso exemplo de composição `uniqueWords(..)`, vamos adicionar um `skipShortWords(..)` à mistura:

```js
function skipShortWords(words) {
    var filteredWords = [];

    for (let word of words) {
        if (word.length > 4) {
            filteredWords.push( word );
        }
    }

    return filteredWords;
}
```

Let's define `biggerWords(..)` that includes `skipShortWords(..)`. The manual composition equivalent is `skipShortWords( unique( words( text ) ) )`, so let's do that with `compose(..)`:

Vamos definir `largeWords(..)` que inclui `skipShortWords(..)`. O equivalente de composição manual é `skipShortWords( unique ( words ( text ) ) )`, então vamos fazer isso com `compose(..)`:

```js
var text = "To compose two functions together, pass the \
output of the first function call as the input of the \
second function call.";

var biggerWords = compose( skipShortWords, unique, words );

var wordsUsed = biggerWords( text );

wordsUsed;
// ["compose","functions","together","output","first",
// "function","input","second"]
```

To do something more interesting with composition, let's use [`partialRight(..)`, which we first looked at in Chapter 3](ch3.md/#user-content-partialright). We can build a right-partial application of `compose(..)` itself, pre-specifying the second and third arguments (`unique(..)` and `words(..)`, respectively); we'll call it `filterWords(..)`.

Para fazer algo mais interessante com composição, vamos usar [`partialRight(..)`, que vimos primeiro no Capítulo 3](ch3.md/#user-content-partialright). Podemos construir uma aplicação parcial à direita do próprio `compose(..)`, pré-especificando o segundo e o terceiro argumento (`unique(..)` e `words(..)`), respectivamente), vamos chamá-lo de `filterWords(..)`.

Then, we can complete the composition multiple times by calling `filterWords(..)`, but with different first-arguments respectively:

Então, podemos completar a composição várias vezes chamando `filterWords(..)`, mas com diferentes primeiros argumentos, respectivamente:

```js
// Note: uses a `<= 4` check instead of the `> 4` check
// that `skipShortWords(..)` uses
function skipLongWords(list) { /* .. */ }

var filterWords = partialRight( compose, unique, words );

var biggerWords = filterWords( skipShortWords );
var shorterWords = filterWords( skipLongWords );

biggerWords( text );
// ["compose","functions","together","output","first",
// "function","input","second"]

shorterWords( text );
// ["to","two","pass","the","of","call","as"]
```

Take a moment to consider what the right-partial application on `compose(..)` gives us. It allows us to specify ahead of time the first step(s) of a composition, and then create specialized variations of that composition with different subsequent steps (`biggerWords(..)` and `shorterWords(..)`). This is one of the most powerful tricks of FP!

Reserve um momento para considerar o que a aplicação parcial à direita em `compose(..)` nos oferece. Ele nos permite especificar com antecedência a(s) primeira(s) etapa(s) de uma composição e, em seguida, criar variações especializadas dessa composição com diferentes etapas subsequentes (`biggerWords(..)` and `shorterWords(..)`). Esté é um dos truques mais poderosos da PF!

You can also `curry(..)` a composition instead of partial application, though because of right-to-left ordering, you might more often want to `curry( reverseArgs(compose), ..)` rather than just `curry( compose, ..)` itself.

Você também pode `curry(..)` uma composição em vez de uma aplicação parcial, embora por causa da ordem da direita para a esquerda, você pode querer mais frequentemente `curry( reverseArgs(compose), ..)` em vez de apenas `curry( compose, ..)` em si.

**Note:** Because `curry(..)` (at least [the way we implemented it in Chapter 3](ch3.md/#user-content-curry)) relies on either detecting the arity (`length`) or having it manually specified, and `compose(..)` is a variadic function, you'll need to manually specify the intended arity like `curry(.. , 3)`.

**Nota:** Porque `curry(..)` (pelo menos [á forma como implementamos no Capítulo 3](ch3.md/#user-content-curry)) depende da detecção de aridade (`comprimento`) ou tê-lo especificado manualmente e `compose(..)` é uma função variável, você precisará especificar manualmente a aridade pretendida como `curry(.., 3)`.

### Alternative Implementations

### Implementações Alternativas

While you may very well never implement your own `compose(..)` to use in production, and rather just use a library's implementation as provided, I've found that understanding how it works under the covers actually helps solidify general FP concepts very well.

Embora você possa muito bem nunca implementar seu próprio `compose(..)` para usar na produção, e sim apenas usar a implementação de uma biblioteca conforme fornecida, descobri que entender como funciona nos bastidores realmente ajuda a solidificar os conceitos gerais de PF muito bem.

So let's examine some different implementation options for `compose(..)`. We'll also see there are some pros/cons to each implementation, especially performance.

Então, vamos examinar algumas opções de implementação diferentes para `compose(..)`. Também veremos que há alguns prós/contras em cada implementação, especialmente o desempenho.

We'll be looking at the [`reduce(..)` utility in detail in Chapter 9](ch9.md/#reduce), but for now, just know that it reduces a list (array) to a single finite value. It's like a fancy loop.

Veremos o utilitário [`reduce(..)` em detalhes no Capítulo 9](ch9.md/#reduce), mas por agora, apenas saiba que ele reduz uma lista (array) a um único valor finito. É como um loop extravagante.

For example, if you did an addition-reduction across a list of numbers (such as `[1,2,3,4,5,6]`), you'd loop over them adding them together as you go. The reduction would add `1` to `2`, and add that result to `3`, and then add that result to `4`, and so on, resulting in the final summation: `21`.

Por exemplo, se você fez uma adição-redução em uma lista de números (como `[1,2,3,4,5,6]`), você faria um loop sobre eles adicionando-os conforme avança. A redução adicionaria `1` a `2` e adicionaria esse resultado a `3` e, em seguida, adicionaria esse resultado a `4` e assim por diante, resultando no somatório final: `21`.

The original version of `compose(..)` uses a loop and eagerly (aka, immediately) calculates the result of one call to pass into the next call. This is a reduction of a list of functions, so we can do that same thing with `reduce(..)`:

A versão original de `compose(..)` usa um loop e avidamente (aka, imediatamente) calcula o resultado de uma chamada para passar para a próxima chamada. Esta é uma redução de uma lista de funções, então podemos fazer a mesma coisa com `reduce(..)`.

<a name="composereduce"></a>

```js
function compose(...fns) {
    return function composed(result){
        return [...fns].reverse().reduce( function reducer(result,fn){
            return fn( result );
        }, result );
    };
}

// or the ES6 => form
var compose = (...fns) =>
    result =>
        [...fns].reverse().reduce(
            (result,fn) =>
                fn( result )
            , result
        );
```

**Note:** This implementation of `compose(..)` uses `[...fns].reverse().reduce(..)` to reduce from right-to-left. We'll [revisit `compose(..)` in Chapter 9](ch9.md/#user-content-composereduceright), instead using `reduceRight(..)` for that purpose.

**Nota:** Esta implementação de `compose(..)` usa `[...fns].reverse().reduce(..)` para reduzir da direita para a esquerda. Iremos [revisitar `compose(..)` no Capítulo 9](ch9.md/#user-content-composereduceright), em vez de usar `reduceRight(..)` para esse propósito.

Notice that the `reduce(..)` looping happens each time the final `composed(..)` function is run, and that each intermediate `result(..)` is passed along to the next iteration as the input to the next call.

Observe que o loop `reduce(..)` acontece cada vez que a função `composed(..)` final é executada, e que cada `result(..)` intermediário é passado para a próxima iteração como a entrada para a próxima chamada.

The advantage of this implementation is that the code is more concise and also that it uses a well-known FP construct: `reduce(..)`. And the performance of this implementation is also similar to the original `for`-loop version.

A vantagem desta implementação é que o código é mais conciso e também usa uma construção da PF bem conhecida: `reduce(..)`. E o desempenho desta implementação também é semelhante ao da versão original do loop `for`.

However, this implementation is limited in that the outer composed function (aka, the first function in the composition) can only receive a single argument. Most other implementations pass along all arguments to that first call. If every function in the composition is unary, this is no big deal. But if you need to pass multiple arguments to that first call, you'd want a different implementation.

No entanto, essa implementação é limitada porque a função composta externa (também conhecida como a primeira função na composição) pode receber apenas um único argumento. A maioria das outras implementações passa todos os argumentos para essa primeira chamada. Se todas as funções de composição fossem unárias, isso não seria grande coisa. Mas se você precisar passar vários argumentos para essa primeira chamada, vocẽ desejará uma implementação diferente.

To fix that first call single-argument limitation, we can still use `reduce(..)` but produce a lazy-evaluation function wrapping:

Para corrigir a limitação de argumento único da primeira chamada, ainda podemos usar `reduce(..)`, mas produzir um empacotamento de função de avaliação preguiçosa:

```js
function compose(...fns) {
    return fns.reverse().reduce( function reducer(fn1,fn2){
        return function composed(...args){
            return fn2( fn1( ...args ) );
        };
    } );
}

// or the ES6 => form
var compose =
    (...fns) =>
        fns.reverse().reduce( (fn1,fn2) =>
            (...args) =>
                fn2( fn1( ...args ) )
        );
```

Notice that we return the result of the `reduce(..)` call directly, which is itself a function, not a computed result. *That* function lets us pass in as many arguments as we want, passing them all down the line to the first function call in the composition, then bubbling up each result through each subsequent call.

Observe que retornamos o resultado da chamada `reduce(..)` diretamente, que é em si uma função, não um resultado calculado. *Essa* função nos permite passar quantos argumentos quisermos, passando-os todos ao longo da linha até a primeira chamada de função na composição e, em seguida, aumentando cada resultado em cada chamada subsequente.

Instead of calculating the running result and passing it along as the `reduce(..)` looping proceeds, this implementation runs the `reduce(..)` looping **once** up front at composition time, and defers all the function call calculations -- referred to as lazy calculation. Each partial result of the reduction is a successively more wrapped function.

Em vez de calcular o resultado da execução e passá-lo à medida que o loop `reduce(..)` prossegue, esta implementação executa o loop `reduce(..)` **uma vez** antes do tempo de composição e adia todas as funções cálculos de chamadas -- conhecidos como cálculos preguiçosos. Cada resultado parcial da redução é uma função sucessivamente mais encapsulada.

When you call the final composed function and provide one or more arguments, all the levels of the big nested function, from the inner most call to the outer, are executed in reverse succession (not via a loop).

Quando você chama a função composta final e fornece um ou mais argumentos, todos os níveis da grande função aninhada, da chamada mais interna à externa, são executados em sucessão reversa (não por meio de um loop).

The performance characteristics will potentially be different than in the previous `reduce(..)`-based implementation. Here, `reduce(..)` only runs once to produce a big composed function, and then this composed function call simply executes all its nested functions each call. In the former version, `reduce(..)` would be run for every call.

As características de desempenho serão potencialmente diferentes das da implementação anterior baseada em `reduce(..)`. Aqui, `reduce(..)` executa apenas uma vez para produzir uma grande função composta, e então, esta chamada de função composta simplesmente executa doas as suas funções aninhadas a cada chamada. Na versão anterior, `reduce(..)` seria executado para todas as chamadas.

Your mileage may vary on which implementation is better, but keep in mind that this latter implementation isn't limited in argument count the way the former one is.

Sua milhagem pode variar em qual implementação é melhor, mas lembre-se de que esta última implementação não é limitada na contagem de argumentos como a anterior.

We could also define `compose(..)` using recursion. The recursive definition for `compose(fn1,fn2, .. fnN)` would look like:

Também poderíamos definir `compose(..)` usando recursão. A definição recursiva para `compose(fn1, fn2, .. fnN)` seria semelhante a:

```txt
compose( compose(fn1,fn2, .. fnN-1), fnN );
```

**Note:** We will cover recursion more fully in [Chapter 8](ch8.md), so if this approach seems confusing, don't worry for now. Or, go read that chapter then come back and re-read this note. :)

**Nota:** Cobriremos a recursão mais completamente no [Capítulo 8](ch8.md), portanto, se essa abordagem parecer confusa, não se preocupe por enquanto. Ou vá ler esse capítulo e depois volte e releia esta nota. :)

Here's how we implement `compose(..)` with recursion:

Veja como implementamos `compose(..)` com recursão:

```js
function compose(...fns) {
    // pull off the last two arguments
    var [ fn1, fn2, ...rest ] = fns.reverse();

    var composedFn = function composed(...args){
        return fn2( fn1( ...args ) );
    };

    if (rest.length == 0) return composedFn;

    return compose( ...rest.reverse(), composedFn );
}

// or the ES6 => form
var compose =
    (...fns) => {
        // pull off the last two arguments
        var [ fn1, fn2, ...rest ] = fns.reverse();

        var composedFn =
            (...args) =>
                fn2( fn1( ...args ) );

        if (rest.length == 0) return composedFn;

        return compose( ...rest.reverse(), composedFn );
    };
```

I think the benefit of a recursive implementation is mostly conceptual. I personally find it much easier to think about a repetitive action in recursive terms instead of in a loop where I have to track the running result, so I prefer the code to express it that way.

Acho que o benefício de uma implementação recursiva é principalmente conceitual. Pessoalmente, acho muito mais fácil pensar em uma ação repetitiva em termos recursivos em vez de em um loop em que tenho que rastrear o resultado da execução, então prefiro que o código o expresse dessa forma.

Others will find the recursive approach quite a bit more daunting to mentally juggle. I invite you to make your own evaluations.

Outros acharão a abordagem recursiva um pouco mais difícil de manipular mentalmente. Convido você a fazer suas próprias avaliações.

## Reordered Composition

## Composição Reordenada

We talked earlier about the right-to-left ordering of standard `compose(..)` implementations. The advantage is in listing the arguments (functions) in the same order they'd appear if doing the composition manually.

Falamos anteriormente sobre a ordem da direita para a esquerda das implementações `compose(..)` padrão. A vantagem é listar os argumentos (funções) na mesma ordem em que apareceriam se a composição fosse feita manualmente.

The disadvantage is they're listed in the reverse order that they execute, which could be confusing. It was also more awkward to have to use `partialRight(compose, ..)` to pre-specify the *first* function(s) to execute in the composition.

A desvantagem é que eles estão listados na ordem inversa em que são executados, o que pode ser confuso. Também era mais difícil ter que usar `partialRight(compose, ..)` para pré-especificar as *primeiras* funções a serem executadas na composição.

The reverse ordering, composing from left-to-right, has a common name: `pipe(..)`. This name is said to come from Unix/Linux land, where multiple programs are strung together by "pipe"ing (`|` operator) the output of the first one in as the input of the second, and so on (i.e., `ls -la | grep "foo" | less`).

A ordem inversa, composta da esquerda para a direita, tem um nome comum: `pipe(..)`. Diz-se que esse nome vem da terra do Unix/Linux, onde vários programas são agrupados por "pipe"ing (operador `|`) a saída do primeiro como entrada do segundo, e assim por diante (ou seja, `ls -la | grep "foo" | less`).

`pipe(..)` is identical to `compose(..)` except it processes through the list of functions in left-to-right order:

`pipe(..)` é idêntico a `compose(..)` excete que processa através da lista de funções na ordem da esquerda para a direita:

```js
function pipe(...fns) {
    return function piped(result){
        var list = [...fns];

        while (list.length > 0) {
            // take the first function from the list
            // and execute it
            result = list.shift()( result );
        }

        return result;
    };
}
```

In fact, we could just define `pipe(..)` as the arguments-reversal of `compose(..)`:

Na verdade, poderíamos apenas definir `pipe(..)` como a inversão de argumentos de `compose(..)`:

```js
var pipe = reverseArgs( compose );
```

That was easy!

Essa foi fácil!

Recall this example from general composition earlier:

Lembre-se deste exemplo da composição geral anterior:

```js
var biggerWords = compose( skipShortWords, unique, words );
```

To express that with `pipe(..)`, we just reverse the order we list them in:

Para expressar isso com `pipe(..)` apenas invertemos a ordem em que os listamos:

```js
var biggerWords = pipe( words, unique, skipShortWords );
```

The advantage of `pipe(..)` is that it lists the functions in order of execution, which can sometimes reduce reader confusion. It may be simpler to read the code: `pipe( words, unique, skipShortWords )`, and recognize that it's executing `words(..)` first, then `unique(..)`, and finally `skipShortWords(..)`.

A vantagem do `pipe(..)` é que ele lista as funções em ordem de execução, o que às vezes pode reduzir a confusão do leitor. Pode ser mais simples ler o código: `pipe( words, unique, skipShortWords )`, e reconhecer que está executando `words(..)` primeiro, depois `unique(..)`, e finalmente `skipShortWords(..)`.

`pipe(..)` is also handy if you're in a situation where you want to partially apply the *first* function(s) that execute. Earlier we did that with right-partial application of `compose(..)`.

`pipe(..)` também é útil se você estiver em uma situação em que deseja aplicar parcialmente as *primeiras* funções executadas. Anteriormente, fizemos isso com a aplicação parcial direita de `compose(..)`.

Compare:

Comparar:

```js
var filterWords = partialRight( compose, unique, words );

// vs

var filterWords = partial( pipe, words, unique );
```

As you may recall from our first implementation of [`partialRight(..)` in Chapter 3](ch3.md/#user-content-partialright), it uses `reverseArgs(..)` under the covers, just as our `pipe(..)` now does. So we get the same result either way.

Como você deve se lembrar da nossa primeira implementação de [`partialRight(..)` no Capítulo 3](ch3.md/#user-content-partialright), ele usa `reverseArgs(..)` nos bastidores, assim como nosso `pipe(..)` agora faz. Portanto, obtemos o mesmo resultado de qualquer maneira.

*In this specific case*, the slight performance advantage to using `pipe(..)` is, because we're not trying to preserve the right-to-left argument order of `compose(..)`, we don't need to reverse the argument order back, like we do inside `partialRight(..)`. So `partial(pipe, ..)` is a little more efficient here than `partialRight(compose, ..)`.

*Neste caso específico*, a pequena vantagem em desempenho de usar `pipe(..)` é, porque não estamos tentando preservar a ordem dos argumentos da direita para a esquerda de `compose(..)`, nós não precisamos reverter a ordem do argumento de volta, como fazemos dentro de `partialRight(..)`. Portanto, `partial(pipe, ..)` é um pouco mais eficiente aqui do que `partialRight(compose, ..)`.

## Abstraction

## Abstração

Abstraction plays heavily into our reasoning about composition, so let's examine it in more detail.

A abstração influencia muito nosso raciocínio sobre composição, portanto, vamos examiná-la com mais detalhes. 

Similar to how partial application and currying (see [Chapter 3](ch3.md/#some-now-some-later)) allow a progression from generalized to specialized functions, we can abstract by pulling out the generality between two or more tasks. The general part is defined once, so as to avoid repetition. To perform each task's specialization, the general part is parameterized.

Semelhante a como a aplicação parcial e currying (ver [Capítulo 3](ch3.md/#some-now-some-later) permitem uma progressão de funções generalizadas para especializadas, podemos abstrair extraindo a generalidade entre duas ou mais tarefas. A parte geral é definida uma vez, para evitar repetições. Para realizar a especialização de cada tarefa, a parte geral é parametrizada.

For example, consider this (obviously contrived) code:

Por exemplo, considere este código (obviamente inventado):

```js
function saveComment(txt) {
    if (txt != "") {
        comments[comments.length] = txt;
    }
}

function trackEvent(evt) {
    if (evt.name !== undefined) {
        events[evt.name] = evt;
    }
}
```

Both of these utilities are storing a value in a data source. That's the generality. The specialty is that one of them sticks the value at the end of an array, while the other sets the value at a property name of an object.

Ambos os utilitários estão armazenando um valor em uma fonte de dados. Essa é a generalidade. A especialidade é que um deles coloca o valor no final de uma matriz, enquanto o outro define o valor em um nome de propriedade de um objeto.

So let's abstract:

Então, vamos abstrair:

```js
function storeData(store,location,value) {
    store[location] = value;
}

function saveComment(txt) {
    if (txt != "") {
        storeData( comments, comments.length, txt );
    }
}

function trackEvent(evt) {
    if (evt.name !== undefined) {
        storeData( events, evt.name, evt );
    }
}
```

The general task of referencing a property on an object (or array, thanks to JS's convenient operator overloading of `[ ]`) and setting its value is abstracted into its own function `storeData(..)`. While this utility only has a single line of code right now, one could envision other general behavior that was common across both tasks, such as generating a unique numeric ID or storing a timestamp with the value.

A tarefa geral de referenciar uma propriedade em um objeto (ou array, graças à sobrecarga de operador conveniente do JS de `[ ]`) e definir se seu valor é abstraído em sua própria função `storeData(..)`. Embora este utilitário tenha apenas uma única linha no momento, pode-se imaginar outro comportamento geral que era comum em ambas as tarefas, como gerar um ID numérico exclusivo ou armazenar um carimbo de data/hora com o valor.

If we repeat the common general behavior in multiple places, we run the maintenance risk of changing some instances but forgetting to change others. There's a principle at play in this kind of abstraction, often referred to as "don't repeat yourself" (DRY).

Se repetirmos o comportamento geral comum em vários lugares, corremos o risco de manutenção de alterar algumas instâncias, mas nos esquecendo de alterar outras. Há um princípio em jogo neste tipo de abstração, frequentemente referido como "não se repita" (DRY).

DRY strives to have only one definition in a program for any given task. An alternative aphorism to motivate DRY coding is that programmers are just generally lazy and don't want to do unnecessary work.

O DRY se esforça para ter apenas uma definição em um programa para qualquer tarefa. Um aforismo alternativo para motivar a codificação DRY é que os programadores geralmente são preguiçosos e não querem fazer trabalho desnecessário.

Abstraction can be taken too far. Consider:

A abstração pode ser levada longe demais. Considerar:

```js
function conditionallyStoreData(store,location,value,checkFn) {
    if (checkFn( value, store, location )) {
        store[location] = value;
    }
}

function notEmpty(val) { return val != ""; }

function isUndefined(val) { return val === undefined; }

function isPropUndefined(val,obj,prop) {
    return isUndefined( obj[prop] );
}

function saveComment(txt) {
    conditionallyStoreData( comments, comments.length, txt, notEmpty );
}

function trackEvent(evt) {
    conditionallyStoreData( events, evt.name, evt, isPropUndefined );
}
```

In an effort to be DRY and avoid repeating an `if` statement, we moved the conditional into the general abstraction. We also assumed that we *may* have checks for non-empty strings or non-`undefined` values elsewhere in the program in the future, so we might as well DRY those out, too!

Em um esforço para ser DRY e evitar repetir uma instrução `if`, movemos o condicional para a abstração geral. Também assumimos que *podemos* ter verificações de strings não vazias ou valores `não definidos` em outro lugar no programa no futuro, então também podemos DRY-los também.

This code *is* more DRY, but to an overkill extent. Programmers must be careful to apply the appropriate levels of abstraction to each part of their program, no more, no less.

Este código *é* mais DRY, mas em um extensão exagerada. Os programadores devem ter o cuidado de aplicar os níveis apropriados de abstração a cada parte de seu programa, nem mais, nem menos.

Regarding our greater discussion of function composition in this chapter, it might seem like its benefit is this kind of DRY abstraction. But let's not jump to that conclusion, because I think composition actually serves a more important purpose in our code.

Em relação à nossa maior discussão sobre composição de funções neste capítulo, pode parecer que seu benefício é esse tipo de abstração DRY. Mas não vamos pular para essa conclusão, porque acho que a composição realmente serve a um propósito mais importante em nosso código.

Moreover, **composition is helpful even if there's only one occurrence of something** (no repetition to DRY out).

Além disso, **composição é útil mesmo se houver apenas uma ocorrência de algo** (sem repetição para aplicar o DRY).

### Separation Enables Focus

### Separação Ativa O Foco

Aside from generalization vs. specialization, I think there's another more useful definition for abstraction, as revealed by this quote:

Além de generalização vs. especialização, acho que há outra definição mais útil para abstração, conforme revelado por esta citação:

> ... abstraction is a process by which the programmer associates a name with a potentially complicated program fragment, which can then be thought of in terms of its purpose of function, rather than in terms of how that function is achieved. By hiding irrelevant details, abstraction reduces conceptual complexity, making it possible for the programmer to focus on a manageable subset of the program text at any particular time.
>
> Michael L. Scott, Programming Language Pragmatics<a href="#user-content-footnote-1"><sup>1</sup></a>

> ... abstração é um processo pelo qual o programador associa um nome a um fragmento de programa potencialmente complicado, que pode então ser pensado em termos de seu propósito de função, ao invés de em termos de como essa função é alcançada. Ao ocultar os detalhes irrelevantes, a abstração reduz a complexidade conceitual, tornando possível para o programador se concentrar em um subconjunto gerenciável do texto do programa a qualquer momento específico.
>
> Michael L. Scott, Programming Language Pragmatics<a href="#user-content-footnote-1"><sup>1</sup></a>

The point this quote makes is that abstraction -- generally, pulling out some piece of code into its own function -- serves the primary purpose of separating apart two pieces of functionality so that it's possible to focus on each piece independently of the other.

O ponto que esta citação mostra é que a abstração -- geralmente, está puxando algum pedaço de código em sua própria função -- serve ao propósito principal de separar duas partes de funcionalidade para que seja possível focar em cada parte independentemente da outra.

Note that abstraction in this sense is not really intended to *hide* details, as if to treat things as black boxes we *never* examine.

Observe que a abstração, neste sentido não tem a intenção de *ocultar* detalhes, como se tratasse as coisas como caixas pretas que *nunca* examinamos.

In this quote, "irrelevant", in terms of what is hidden, shouldn't be thought of as an absolute qualitative judgement, but rather relative to what you want to focus on at any given moment. In other words, when we separate X from Y, if I want to focus on X, Y is irrelevant at that moment. At another time, if I want to focus on Y, X is irrelevant at that moment.

Nesta citação, "irrelevante", em temos do que está oculto, não deve ser pensado como um julgamento qualitativo absoluto, mas sim relativo ao que você deseja focar em um determinado momento. Em outras palavras, quando separamos X de Y, se eu quiser me concentrar em X, Y é irrelevante naquele momento. Em outro momento, se eu quiser me concentrar em Y, X é irrelevante naquele momento.

**We're not abstracting to hide details; we're separating details to improve focus.**

**Não estamos abstraindo para esconder detalhes, estamos separando os detalhes para melhorar o foco.**

Recall that at the outset of this book I stated that FP's goal is to create code that is more readable and understandable. One effective way of doing that is untangling complected (read: tightly braided, as in strands of rope) code into separate, simpler (read: loosely bound) pieces of code. In that way, the reader isn't distracted by the details of one part while looking for the details of the other part.

Lembre-se de que, no início deste livro, afirmei que o objetivo da PF é criar um código mais legível e compreensível. Uma maneira eficaz de fazer isso é desmaranhar código complexo (leia-se: firmemente trançado, como em fios de corda) em pedaços de código separados e mais simples (leia-se: fracamente ligados). Dessa forma, o leitor não se distrai com os detalhes de uma parte enquanto procura os detalhes da outra.

Our higher goal is not to implement something only once, as it is with the DRY mindset. As a matter of fact, sometimes we'll actually repeat ourselves in code.

Nosso objetivo maior não é implementar algo, apenas uma vez, como acontece com a mentalidade DRY. Na verdade, às vezes nos repetimos no código.

As we [asserted in Chapter 3](ch3.md/#why-currying-and-partial-application), the main goal with abstraction is to implement separate things, separately. We're trying to improve focus, because that improves readability.

Como [afirmamos no Capítulo 3](ch3.md/#why-currying-and-partial-application), o objetivo principal da abstração é implementar coisas separadas, separadamente. Estamos tentando melhorar o foco, porque isso melhora a legibilidade.

By separating two ideas, we insert a semantic boundary between them, which affords us the ability to focus on each side independent of the other. In many cases, that semantic boundary is something like the name of a function. The function's implementation is focused on *how* to compute something, and the call-site using that function by name is focused on *what* to do with its output. We abstract the *how* from the *what* so they are separate and separately reason'able.

Ao separar duas ideias, inserimos uma fronteira semântica entre elas, o que nos dá a capacidade de focar em cada lado independente do outro. Em muitos casos, esse limite semântico é algo como o nome de uma função. A implementação da função está focada em *como* computar algo, e a chamada usando essa função por nome está focado em *o que* fazer com sua saída. Nós abstraímos o *how* do *o quê* para que sejam separados e separadamente razoáveis.

Another way of describing this goal is with imperative vs. declarative programming style. Imperative code is primarily concerned with explicitly stating *how* to accomplish a task. Declarative code states *what* the outcome should be, and leaves the implementation to some other responsibility.

Outra maneira de descrever esse objetivo é com o estilo de programação imperativo vs. declarativo. O código imperativo se preocupa principalmente em declarar explicitamente *como* realizar uma tarefa. O código declarativo declara *qual* o resultado deve ser e deixa a implementação para alguma outra responsabilidade.

Declarative code abstracts the *what* from the *how*. Typically declarative coding is favored in readability over imperative, though no program (except of course machine code 1s and 0s) is ever entirely one or the other. The programmer must seek balance between them.

O código declarativo abstrai *o quê* de *como*. Normalmente, a codificação declarativa é favorecida na legibilidade sobre a imperativa, embora nenhum programa (exceto, é claro, os códigos de máquina 1s e 0s) seja inteiramente um ou outro. O programador deve buscar o equilíbrio entre eles.

ES6 added many syntactic affordances that transform old imperative operations into newer declarative forms. Perhaps one of the clearest is destructuring. Destructuring is a pattern for assignment that describes how a compound value (object, array) is taken apart into its constituent values.

ES6 adicionou muitas possibilidades sintáticas que trasnformam antigas operações imperativas em novas formas declarativas. Talvez uma das mais claras seja a desestruturação. A desestruturação é um padrão de atribuição que descreve como um valor composto (objeto, array) é desmontado em seus valores constituíntes. 

Here's an example of array destructuring:

Aqui está um exemplo de desestruturação de array:

```js
function getData() {
    return [1,2,3,4,5];
}

// imperative
var tmp = getData();
var a = tmp[0];
var b = tmp[3];

// declarative
var [ a ,,, b ] = getData();
```

The *what* is assigning the first value of the array to `a` and the fourth value to `b`. The *how* is getting a reference to the array (`tmp`) and manually referencing indexes `0` and `3` in assignments to `a` and `b`, respectively.

O *what* está atribuindo o primeiro valor do array a `a` e o quarto valor a `b`. O *como* está obtendo uma referência ao array (`tmp`) e referenciando manualmente os índices `0` e `3` em atribuições a `a` e `b`, respectivamente.

Does the array destructuring *hide* the assignment? Depends on your perspective. I'm asserting that it simply separates the *what* from the *how*. The JS engine still does the assignments, but it prevents you from having to be distracted by *how* it's done.

A desestruturação do array *esconde* a atribuição? Depende da sua perspectiva. Estou afirmando que simplesmente separa o *o quê* do *como*. O mecanismo JS ainda faz as atribuições, mas evita que você se distraia com *como* isso é feito.

Instead, you read `[ a ,,, b ] = ..` and can see the assignment pattern merely telling you *what* will happen. Array destructuring is an example of declarative abstraction.

Em vez disso, você lê `[a ,,, b] = ..` e pode ver o padrão de atribuição meramente dizendo a você *o que* vai acontecer. A desestruturação de array é um exemplo de abstração declarativa.

### Composition as Abstraction

### Composição como Abstração

What's all this have to do with function composition? Function composition is also declarative abstraction.

O que tudo isso tem a ver com composição de funções? A composição da função também é uma abstração declarativa. 

Recall the `shorterWords(..)` example from earlier. Let's compare an imperative and declarative definition for it:

Lembre-se do exemplo `shorterWords(..)` anterior. Vamos comparar uma definição imperativa e declarativa para ele:

```js
// imperative
function shorterWords(text) {
    return skipLongWords( unique( words( text ) ) );
}

// declarative
var shorterWords = compose( skipLongWords, unique, words );
```

The declarative form focuses on the *what* -- these three functions pipe data from a string to a list of shorter words -- and leaves the *how* to the internals of `compose(..)`.

A forma declarativa se concentra em *o quê* -- essas três funções canalizam dados de uma string para uma lista de palavras mais curtas -- e deixa o *como* para os detalhes internos de `compose(..)`.

In a bigger sense, the `shorterWords = compose(..)` line explains the *how* for defining a `shorterWords(..)` utility, leaving this declarative line somewhere else in the code to focus only on the *what*:

Em um sentido mais amplo, a linha `shorterWords = compose(..)` explica *como* para definir um utilitário `shorterWords(..)`, deixando esta linha declarativa em algum outro lugar do código para focar apenas no *o quê*:

```js
shorterWords( text );
```

Composition abstracts getting a list of shorter words from the steps it takes to do that.

Resumos de composição obtendo uma lsita de palavras mais curtas das etapas necessárias para fazer isso.

By contrast, what if we hadn't used composition abstraction?

Em contraste, e se  não tivéssemos usado a abstração da composição?

```js
var wordsFound = words( text );
var uniqueWordsFound = unique( wordsFound );
skipLongWords( uniqueWordsFound );
```

Or even:

```js
skipLongWords( unique( words( text ) ) );
```

Either of these two versions demonstrates a more imperative style as opposed to the prior declarative style. The reader's focus in those two snippets is inextricably tied to the *how* and less on the *what*.

Qualquer uma dessas duas versões demonstra um estilo mais imperativo em oposição ao estilo declarativo anterior. O foco do leitor nesses dois fragmentos está inextricavelmente ligado ao *como* e menos ao *o quê*.

Function composition isn't just about saving code with DRY. Even if the usage of `shorterWords(..)` only occurs in one place -- so there's no repetition to avoid! -- separating the *how* from the *what* still improves our code.

A composição de funções não é apenas salvar o código com o DRY. Mesmo que o uso de `shorterWords(..)` só ocorra em um lugar -- portanto, não há repetição a ser evitada! -- separando o *como* do *o quê* ainda melhora nosso código.

Composition is a powerful tool for abstraction that transforms imperative code into more readable declarative code.

A composição é uma ferramenta poderosa para abstração que transforma o código imperativo em um código declarativo mais legível.

## Revisiting Points

## Revisitando Pontos

Now that we've thoroughly covered composition (a trick that will be immensely helpful in many areas of FP), let's watch it in action by revisiting point-free style from [Chapter 3, "No Points"](ch3.md/#no-points) with a scenario that's a fair bit more complex to refactor:

Agora que cobrimos completamente a composição (um truque que será imensamente útil em muitas áreas da PF), vamos vê-lo em ação revisitando o estilo sem pontos do [Capítulo 3, "Sem Pontos"](ch3.md/#no-points) com um cenário que é um pouco mais complexo de refatorar:

```js
// given: ajax( url, data, cb )

var getPerson = partial( ajax, "http://some.api/person" );
var getLastOrder = partial( ajax, "http://some.api/order", { id: -1 } );

getLastOrder( function orderFound(order){
    getPerson( { id: order.personId }, function personFound(person){
        output( person.name );
    } );
} );
```

The "points" we'd like to remove are the `order` and `person` parameter references.

Os "pontos" que gostaríamos de remover são as referências dos parâmetros `ordem` e `pessoa`.

Let's start by trying to get the `person` "point" out of the `personFound(..)` function. To do so, let's first define:

Vamos começar tentando obter o "ponto" `person` da função `personFound(..)`. Para fazer isso, vamos primeiro definir:

```js
function extractName(person) {
    return person.name;
}
```

Consider that this operation could instead be expressed in generic terms: extracting any property by name off of any object. Let's call such a utility `prop(..)`:

Considere que esta operação poderia ser expressa em termos genéricos: extração de qualquer propriedade pelo nome de qualquer objeto. Vamos chamar esse utilitário de `prop(..)`:

```js
function prop(name,obj) {
    return obj[name];
}

// or the ES6 => form
var prop =
    (name,obj) =>
        obj[name];
```

While we're dealing with object properties, let's also define the opposite utility: `setProp(..)` for setting a property value onto an object.

Enquanto estamos lidando com propriedades de objeto, vamos também definir o utilitário oposto: `setProp(..)` para definir um valor de propriedade em um objeto.

However, we want to be careful not to just mutate an existing object but rather create a clone of the object to make the change to, and then return it. The reasons for such care will be discussed at length in [Chapter 5](ch5.md).

No entanto, queremos ter cuidado para não apenas transformar um objeto existente, mas sim criar um clone do objeto para fazer a alteração e, em seguida, retorná-lo. As razões para tal cuidado serão discutidas detalhadamente no [Capítulo 5](ch5.md).

<a name="setprop"></a>

```js
function setProp(name,obj,val) {
    var o = Object.assign( {}, obj );
    o[name] = val;
    return o;
}
```

Now, to define an `extractName(..)` that pulls a `"name"` property off an object, we'll partially apply `prop(..)`:

Agora, para definir um `extractName(..)` que extrai uma propriedade `"name"` de um objeto, aplicaremos parcialmente `prop(..)`:

```js
var extractName = partial( prop, "name" );
```

**Note:** Don't miss that `extractName(..)` here hasn't actually extracted anything yet. We partially applied `prop(..)` to make a function that's waiting to extract the `"name"` property from whatever object we pass into it. We could also have done it with `curry(prop)("name")`.

**Nota:** Não perca que `extractName(..)` aqui ainda não extraiu nada. Aplicamos parcialmente `prop(..)` para fazer uma função que está esperando para extrair a propriedade `"name"` de qualquer objeto que passamos para ela. Também poderíamos ter feito isso com `curry(prop("name"))`.

Next, let's narrow the focus on our example's nested lookup calls to this:

A seguir, vamos restringir o foco nas chamadas de pesquisa aninhadas de nosso exemplo para o seguinte:

```js
getLastOrder( function orderFound(order){
    getPerson( { id: order.personId }, outputPersonName );
} );
```

How can we define `outputPersonName(..)`? To visualize what we need, think about the desired flow of data:

Como podemos definir `outputPersonName(..)`? Para visualizar o que precisamos, pense no fluxo de dados desejado:

```txt
output <-- extractName <-- person
```

`outputPersonName(..)` needs to be a function that takes an (object) value, passes it into `extractName(..)`, then passes that value to `output(..)`.

`outputPersonName(..)` precisa ser uma função que recebe um valor (objeto), passa para `extractName(..)` e, em seguida, passa esse valor para `output(..)`.

Hopefully you recognized that as a `compose(..)` operation. So we can define `outputPersonName(..)` as:

Esperançosamente, você reconheceu isso como uma operação `compose(..)`. Portanto, podemos definir `outputPersonName(..)` como:

```js
var outputPersonName = compose( output, extractName );
```

The `outputPersonName(..)` function we just created is the callback provided to `getPerson(..)`. So we can define a function called `processPerson(..)` that presets the callback argument, using `partialRight(..)`:

A função `outputPersonName(..)` que acabamos de criar é o retorno de chamada fornecido para `getPerson(..)`. Portanto, podemos definir uma função chamada `processPerson(..)` que pré-define o argumento de retorno de chamada, usando `partialRight(..)`:

```js
var processPerson = partialRight( getPerson, outputPersonName );
```

Let's reconstruct the nested lookups example again with our new function:

Vamos reconstruir o exemplo de pesquisas aninhadas novamente com nossa nova função:

```js
getLastOrder( function orderFound(order){
    processPerson( { id: order.personId } );
} );
```

Phew, we're making good progress!

Ufa, estamos fazendo um bom progresso!

But we need to keep going and remove the `order` "point". The next step is to observe that `personId` can be extracted from an object (like `order`) via `prop(..)`, just like we did with `name` on the `person` object:

Mas prcisamos continuar e remover o "ponto" de `order`. O próximo passo é observar que `personId` pode ser extraído de um objeto (como `order`) via `prop(..)`, assim como fizemos com `name` no objeto `person`:

```js
var extractPersonId = partial( prop, "personId" );
```

To construct the object (of the form `{ id: .. }`) that needs to be passed to `processPerson(..)`, let's make another utility for wrapping a value in an object at a specified property name, called `makeObjProp(..)`:

Para construir o objeto (da forma `{ id: .. }`) que precisa ser passado para `processPerson(..)`, vamos fazer outro utilitário para envolver um valor em um objeto em um nome de propriedade especificado, chamado `makeObjProp(..)`:

```js
function makeObjProp(name,value) {
    return setProp( name, {}, value );
}

// or the ES6 => form
var makeObjProp =
    (name,value) =>
        setProp( name, {}, value );
```

**Tip:** This utility is known as `objOf(..)` in the Ramda library.

**Dica:** Este utilitário é conhecido como `objOf(..)` na biblioteca Ramda.

Just as we did with `prop(..)` to make `extractName(..)`, we'll partially apply `makeObjProp(..)` to build a function `personData(..)` that makes our data object:

ASsim como fizemos com `prop(..)` para fazer `extractName(..)`, aplicaremos parcialmente `makeObjProp(..)` para construir uma função `personData(..)` que torna nosso objeto de dados:

```js
var personData = partial( makeObjProp, "id" );
```

To use `processPerson(..)` to perform the lookup of a person attached to an `order` value, the conceptual flow of data through operations we need is:

Para usar `processPerson(..)` para realizar a pesquisa de uma pessoa anexada a um valor de `order`, o fluxo conceitual de dados por meio de operações que precisamos é:

```txt
processPerson <-- personData <-- extractPersonId <-- order
```

So we'll just use `compose(..)` again to define a `lookupPerson(..)` utility:

Portanto, usaremos apenas `compose(..)` novamente para definir um utilitário `lookupPerson(..)`:

```js
var lookupPerson =
    compose( processPerson, personData, extractPersonId );
```

And... that's it! Putting the whole example back together without any "points":

E... é isso! COlocando o exemplo inteiro de volta junto sem quaisquer "pontos";

```js
var getPerson = partial( ajax, "http://some.api/person" );
var getLastOrder =
    partial( ajax, "http://some.api/order", { id: -1 } );

var extractName = partial( prop, "name" );
var outputPersonName = compose( output, extractName );
var processPerson = partialRight( getPerson, outputPersonName );
var personData = partial( makeObjProp, "id" );
var extractPersonId = partial( prop, "personId" );
var lookupPerson =
    compose( processPerson, personData, extractPersonId );

getLastOrder( lookupPerson );
```

Wow. Point-free. And `compose(..)` turned out to be really helpful in two places!

Uauu. Livre de pontos. E `compose(..)` acabou sendo muito útil em dois lugares!

I think in this case, even though the steps to derive our final answer were a bit drawn out, the end result is much more readable code, because we've ended up explicitly calling out each step.

Acho que, neste caso, embora as etapas para derivar nossa resposta final tenham sido um pouco demoradas, o resultado final é um código muito mais legível, porque acabamos chamando explicitamente cada etapa.

And even if you didn't like seeing/naming all those intermediate steps, you can preserve point-free but wire the expressions together without individual variables:

E mesmo se você não gostou de ver/nomear todas as etapas intermediárias, você pode preservar sem pontos, mas conectar as expressões sem variáveis individuais:

```js
partial( ajax, "http://some.api/order", { id: -1 } )
(
    compose(
        partialRight(
            partial( ajax, "http://some.api/person" ),
            compose( output, partial( prop, "name" ) )
        ),
        partial( makeObjProp, "id" ),
        partial( prop, "personId" )
    )
);
```

This snippet is less verbose for sure, but I think it's less readable than the previous snippet where each operation is its own variable. Either way, composition helped us with our point-free style.

Este trecho é menos prolixo com certeza, mas acho que é menos legível do que o trecho anterior, onde cada operação é sua própria variável. De qualquer forma, a composição nos ajudou com nosso estilo livre de pontos.

## Summary

## Resumo

Function composition is a pattern for defining a function that routes the output of one function call into another function call, and its output to another, and so on.

Composição de função é um padrão para definir uma função que roteia a saída de uma chamada de função para outra chamada de função e sua saída para outra e assim por diante.

Because JS functions can only return single values, the pattern essentially dictates that all functions in the composition (except perhaps the first called) need to be unary, taking only a single input from the output of the previous function.

COmo as funções JS só podem retornar valores únicos, o padrão essencialmente determina que todas as funções na composição (exceto talvez a primeira chamada) precisam ser unárias, recebendo apenas uma única entrada da saída da função anterior.

Instead of listing out each step as a discrete call in our code, function composition using a utility like `compose(..)` or `pipe(..)` abstracts that implementation detail so the code is more readable, allowing us to focus on *what* the composition will be used to accomplish, not *how* it will be performed.

Em vez de listar cada etapa como uma chamada discreta em nosso código, a composição da função usando um utilitário como `compose(..)` ou `pipe(..)` abstrai esse detalhe de implementação para que o código seja mais legível, permitindo-nos focar sobre *o que* a composição será usada para realizar e não *como* ela será executada.

Composition is declarative data flow, meaning our code describes the flow of data in an explicit, obvious, and readable way.

Composição é um fluxo de dados declarativo, o que significa que nosso código descreve o fluxo de dados de maneira explícita, óbvia e legível.

In many ways, composition is the most important foundational pattern, in large part because it's the only way to route data through our programs aside from using side effects; the next chapter explores why such should be avoided wherever possible.

De muitas maneiras, a composição é o padrão de base mais importante, em grande parte porque é a única maneira de encaminhar os dados por meio de nossos programas, além de usar efeitos colaterais. O próximo capítulo explora por que isso deve ser evitado sempre que possível.

----

<a name="footnote-1"><sup>1</sup></a>Scott, Michael L. “Chapter 3: Names, Scopes, and Bindings.” Programming Language Pragmatics, 4th ed., Morgan Kaufmann, 2015, pp. 115.

<a name="footnote-1"><sup>1</sup></a>Scott, Michael L. "Capítulo 3: Nomes, Escopos, e Vinculações.” Programming Language Pragmatics, 4th ed., Morgan Kaufmann, 2015, pp. 115.

