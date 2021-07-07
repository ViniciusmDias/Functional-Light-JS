# Functional-Light JavaScript
# Capítulo 5: Reduzindo Efeitos Colaterais

No [Capítulo 2](ch2.md), discutimos como uma função pode ter saídas além de seu valor de `return`. Agora que você deve estar muito confortável com a definição de uma função na PF, a ideia de tais efeitos colaterais deve cheirar estranho.

Vamos examinar as várias formas diferentes de efeitos colaterais e ver por que eles são prejudiciais à qualidade e legibilidade do nosso código.

Mas não me deixe esconder as coisas aqui. A piada deste capítulo: é impossível escrever um programa sem efeitos colaterais. Bem, não é impossivel, você certamente pode. Mas esse programa não fará nada útil ou observável. Se você escrevesse um programa com zero efeitos colaterais, não seria capaz de dizer a diferença entre ele e um programa vazio.

Os programadores funcionais não eliminam todos os efeitos colaterais. Em vez disso, o objetivo é limitá-los tanto quanto possível. Para fazer isso, primeiro precisamos entendê-los totalmente.

## Efeitos ao lado, Por Favor

Causa e efeito: uma das observações intuitivas mais fundamentais que nós, humanos, podemos fazer sobre o mundo ao nosso redor. Empurre um livro da borda de uma mesa, ele cai no chão. Você não precisa de um diploma de física para saber que a causa foi você empurrar o livro e o efeito foi a gravidade puxando-o para o chão. Existe uma relação clara e direta.

Na programação, também lidamos inteiramente com causa e efeito. Se você chamar uma função (causa), ela exibe uma mensagem na tela (efeito).

Ao ler um programa, é extremamente importante que o leitor seja capaz de identificar claramente cada causa e cada efeito. Em qualquer medida em que uma relação direta entre causa e efeito não possa ser vista prontamente em uma leitura do programa, a legibilidade desse programa é degradada.

Considere:

```js
function foo(x) {
    return x * 2;
}

var y = foo( 3 );
```

Neste programa trivial, é imediatamente claro que chamar foo (a causa) com o valor `3` terá o efeito de retornar o valor `6` que é então atribuído a `y` (o efeito). Não há ambiguidadae aqui.

Mas agora:

```js
function foo(x) {
    y = x * 2;
}

var y;

foo( 3 );
```

Este programa tem exatamente o mesmo resultado. Mas há uma diferença muito grande. A causa e o efeito são separados. O efeito é indireto. A configuração de `y` dessa forma é o que chamamos de efeito colateral.

**Nota:** Quando uma função faz referência a uma variável fora dela, isso é chamado de variável livre. Nem todas as referências de variáveis livres serão ruins, mas devemos ter muito cuidado com elas.

E se eu der a você uma referência para chamar uma função `bar(..)` para a qual você não pode ver o código, mas eu disse que não tem esses efeitos colaterais indiretos, apenas um valor de `return` explícito?

```js
bar( 4 );           // 42
```

Como você sabe que as partes internas de `bar(..)` não criam efeitos colaterais, agora você pode raciocinar sobre qualquer chamada de `bar(..)` como esta de uma maneira muito mais direta. Mas se você não sabia que `bar(..)` não tinha efeitos colaterais, para entender o resultado de chamá-lo, você teria que ler e dissecar toda a sua lógica. Isso é uma carga tributária mental extra para o leitor.

**A legibilidade de uma função de efeito colateral é pior** porque requer mais leitura para entender o programa.

Mas o problema é mais profundo do que isso. Considere:

```js
var x = 1;

foo();

console.log( x );

bar();

console.log( x );

baz();

console.log( x );
```

Você tem certeza de quais valores serão impressos em cada `console.log(x)`?

A resposta correta é: de forma alguma. Se você não tem certeza se `foo()`, `bar()`, e `baz()` são efeitos colaterais ou não, você não pode garantir o que será o `x` em cada etapa, a menos que você inspecione cada implementação, **e** então rastreie o programa da linha 1 em diante, acompanhando todas as mudanças de estado conforme for avançando.

Em outras palavras, o `console.log(x)` final é impossível de analisar ou prever, a menos que você tenha executado mentalmente todo o programa até aquele ponto.

Adivinha quem é bom em rodar o seu programa? O mecanismo JS. Adivinha quem não é tão bom em executar seu programar? O leitor do seu código. E ainda, sua escolha de escrever código (potencialmente) com efeitos colaterais em uma ou mais dessas chamadas de função significa que você sobrecarregou o leitor com a necessidade de executar mentalmente seu programa em sua totalidade até uma determinada linha, para que eles pudessem ler e entender essa linha. 

Se `foo()`, `bar()`, e `baz()` estivessem livres de efeitos colaterais, eles não poderiam afetar `x`, o que significa que não precisamos executá-los para rastrear mentalmente o que acontece com `x`. Isso é menos imposto mental e torna o código mais legível.

### Hidden Causes

### Causas Ocultas

Outputs, changes in state, are the most commonly cited manifestation of side effects. But another readability-harming practice is what some refer to as side causes. Consider:

Saídas, mudanças no estado, são as manifestações de efeitos colaterais mais comumente citadas. Mas outra prática que pejudica a legibilidade é o que alguns chamam de causas colaterais. Considere:

```js
function foo(x) {
    return x + y;
}

var y = 3;

foo( 1 );           // 4
```

`y` is not changed by `foo(..)`, so it's not the same kind of side effect as we saw before. But now, the calling of `foo(..)` actually depends on the presence and current state of a `y`. If later, we do:

`y` não é alterado por `foo(..)`, então não é o mesmo tipo de efeito colateral que vimos antes. Mas agora, a chamada de `foo(..)` realmente depende da presença e do estado atual de um `y`. Se mais tarde, faremos:

```js
y = 5;

// ..

foo( 1 );           // 6
```

Might we be surprised that the call to `foo(1)` returned different results from call to call?

Podemos nos surpreender se a chamada para `foo(1)` retornou diferentes resultados de chamada para chamada?

`foo(..)` has an indirection of cause that is harmful to readability. The reader cannot see, without inspecting `foo(..)`'s implementation carefully, what causes are contributing to the output effect. It *looks* like the argument `1` is the only cause, but it turns out it's not.

`foo(..)` tem uma causa indireta que é prejudicial à legibilidade. O leitor não pode ver, sem inspecionar a implementação de `foo(..)` cuidadosamente, causas que estão contribuindo para o efeito de saída. *Parece* que o argumento `1` é a única causa, mas não é.

To aid readability, all of the causes that will contribute to determining the effect output of `foo(..)` should be made as direct and obvious inputs to `foo(..)`. The reader of the code will clearly see the cause(s) and effect.

Para ajudar na legibilidade, todas as causas que contribuirão para determinar a saída do efeito de `foo(..)` devem ser feitas como entradas diretas e óbvias para `foo(..)`. O leitor do código verá claramente a(s) causa(s) e o efeito.

#### Fixed State

#### Estado Fixo

Does avoiding side causes mean the `foo(..)` function cannot reference any free variables?

Evitar causas secundárias significa que a função `foo(..)` não pode fazer referência a nenhuma variável livre?

Consider this code:

Considere este código:

```js
function foo(x) {
    return x + bar( x );
}

function bar(x) {
    return x * 2;
}

foo( 3 );           // 9
```

It's clear that for both `foo(..)` and `bar(..)`, the only direct cause is the `x` parameter. But what about the `bar(x)` call? `bar` is just an identifier, and in JS it's not even a constant (aka, non-reassignable variable) by default. The `foo(..)` function is relying on the value of `bar` -- a variable that references the second function -- as a free variable.

É claro que para `foo(..)` e `bar(..)`, a única causa direta é o parãmetro `x`. Mas e quanto à chamada `bar(x)`? `bar` é apenas um identificador, e em JS nem mesmo é uma constante (também conhecida como variável não reatribuível) por padrão. A função `foo(..)` depende do valor de `bar` -- uma variável que faz referência à segunda função -- como uma variável livre.

So is this program relying on a side cause?

Então, este programa depende de uma causa secundária?

I say no. Even though it is *possible* to overwrite the `bar` variable's value with some other function, I am not doing so in this code, nor is it a common practice of mine or precedent to do so. For all intents and purposes, my functions are constants (never reassigned).

Eu disse não. Embora seja *possível* substituir o valor da variável `bar` por alguma outra função, não estou fazendo isso neste código, nem é uma prática comum minha ou precedente fazer isso. Para todos os efeitos, minhas funções são constantes (nunca reatribuídas).

Consider:

Considere:

```js
const PI = 3.141592;

function foo(x) {
    return x * PI;
}

foo( 3 );           // 9.424776000000001
```

**Note:** JavaScript has `Math.PI` built-in, so we're only using the `PI` example in this text as a convenient illustration. In practice, always use `Math.PI` instead of defining your own!

**Nota:** JavaScript tem `Math.PI` embutido, então estamos usando apenas o exemplo `PI` neste texto como uma ilustração conveniente. Na prática, sempre use `Math.PI` em vez de definir o seu próprio!

How about the preceding code snippet? Is `PI` a side cause of `foo(..)`?

E quanto ao trecho de código anterior? `PI` é uma causa secundária de `foo(..)`?

Two observations will help us answer that question in a reasonable way:

Duas observações nos ajudarão a responder a essa pergunta de maneira razoável:

1. Think about every call you might ever make to `foo(3)`. Will it always return that `9.424..` value? **Yes.** Every single time. If you give it the same input (`x`), it will always return the same output.

1. Pense em cada chamada que você possa fazer para `foo(3)`. Ele sempre retornará o valor `9.424..`? **Sim.** Sempre. Se você fornecer a mesma entrada (`x`), sempre retornará a mesma saída.

2. Could you replace every usage of `PI` with its immediate value, and could the program run **exactly** the same as it did before? **Yes.** There's no part of this program that relies on being able to change the value of `PI` -- indeed since it's a `const`, it cannot be reassigned -- so the `PI` variable here is only for readability/maintenance sake. Its value can be inlined without any change in program behavior.

2. Você poderia substituir todo uso de `PI` por seu valor imediato e o programa poderia ser executado **exatamente** da mesma forma que antes? **Sim.** Não há nenhuma parte deste programa que dependa da capacidade de alterar o valor de `PI` - na verdade, uma vez que é um `const`, -- ele não pode ser reatribuído -- então a variável `PI` aqui é apenas para facilitar a leitura/manutenção. Seu valor pode ser embutido sem qualquer alteração no comportamento do programa.

My conclusion: `PI` here is not a violation of the spirit of minimizing/avoiding side effects (or causes). Nor is the `bar(x)` call in the previous snippet.

Minha conclusão: `PI` aqui não é uma violação do espírito de minimizar/evitar efeitos colaterais (ou causas). nem é a chamada `bar(x)` no trecho anterior.

In both cases, `PI` and `bar` are not part of the state of the program. They're fixed, non-reassigned references. If they don't change throughout the program, we don't have to worry about tracking them as changing state. As such, they don't harm our readability. And they cannot be the source of bugs related to variables changing in unexpected ways.

Em ambos os casos, `PI` e `bar` não fazem parte do estado do programa. São referências fixas e não reatribuídas. Se eles não mudarem ao longo do programa, não precisamos nos preocupar em rastreá-los como estado de mudança. Como tal, eles não prejudicam nossa legibilidade. E eles não podem ser a fonte de bugs relacionados a variáveis que mudam de maneiras inesperadas.

**Note:** The use of `const` here does not, in my opinion, make the case that `PI` is absolved as a side cause; `var PI` would lead to the same conclusion. The lack of reassigning `PI` is what matters, not the inability to do so. We'll discuss [`const` in Chapter 6](ch6.md/#reassignment).

**Nota:** O uso de `const` aqui não significa, na minha opinião, que `PI` seja absolvido como uma causa secundária; `var PI` levaria à mesma conclusão. A falta de reatribuição de `PI` é o que importa, não a incapacidade de fazê-lo. Discutiremos [`const` no Capítulo 6](ch6.md/#reassignment).

#### Randomness

#### Aleatoriedade

You may never have considered it before, but randomness is a side cause. A function that uses `Math.random()` cannot have predictable output based on its input. So any code that generates unique random IDs/etc. will by definition be considered reliant on the program's side causes.

Você pode nunca ter considerado isso antes, mas a aleatoriedade é uma causa secundária. Uma função que usa `Math.random()` não pode ter uma saída previsível baseada em sua entrada. Portanto, qualquer código que gere IDs aleatórios exclusivos/etc. Serão, por definição, considerados dependentes das causas secundárias do programa.

In computing, we use what's called pseudo-random algorithms for generation. Turns out true randomness is pretty hard, so we just kinda fake it with complex algorithms that produce values that seem observably random. These algorithms calculate long streams of numbers, but the secret is, the sequence is actually predictable if you know the starting point. This starting point is referred to as a seed.

Na computação, usamos o que é chamado de algoritmos pseudo-aleatórios para geração. Acontece que a verdadeira aleatoriedade é muito difícil, então meio que fingimos com algoritmos complexos que produzem valores que parecem aleatórios. Esses algoritmos calculam longos fluxos de números, mas o segredo é que a sequência é realmente previsível se você souber o ponto de partida. Esse ponto de partida é conhecido como semente.

Some languages let you specify the seed value for the random number generation. If you always specify the same seed, you'll always get the same sequence of outputs from subsequent "pseudo-random number" generations. This is incredibly useful for testing purposes, for example, but incredibly dangerous for real-world application usage.

Alguns idiomas permitem que você especifique o valor da semente para a geração de números aleatórios. Se você sempre especificar a mesma semente, sempre obterá a mesma sequência de resultados das gerações subsequentes de "números pseudo-aleatórios". Isso é incrivelmente útil para fins de teste, por exemplo, mas incrivelmente perigoso para o uso de aplicativos do mundo real.

In JS, the randomness of `Math.random()` calculation is based on an indirect input, because you cannot specify the seed. As such, we have to treat built-in random number generation as a side cause.

Em JS, a aleatoriedade do cálculo `Math.random()` é baseada em uma entrada indireta, porque você não pode especificar a semente. Como tal, temos que tratar a geração de números aleatórios embutida como uma causa secundária.

### I/O Effects

### Efeitos E/S

The most common (and essentially unavoidable) form of side cause/effect is input/output (I/O). A program with no I/O is totally pointless, because its work cannot be observed in any way. Useful programs must at a minimum have output, and many also need input. Input is a side cause and output is a side effect.

A forma mais comum (e essencialmente inevitável) de causa/efeito colateral é a entrada/saída (E/S). Um programa sem E/S é totalmente inútil, pois seu trabalho não pode ser observado de nenhum jeito. Programas úteis devem ter pelo menos saída e muitos também precisam de entrada. A entrada é uma causa secundária e a saída é um efeito colateral.

The typical input for the browser JS programmer is user events (mouse, keyboard), and for output is the DOM. If you work more in Node.js, you may more likely receive input from, and send output to, the file system, network connections, and/or the `stdin`/`stdout` streams.

A entrada típica para o programador JS do navegador são os eventos do usuário (mouse, teclado) e para saída é o DOM. Se você trabalha mais em Node.js, é mais provável que receba entrada e envie saída para o sistema de arquivo, conexões de rede e/ou fluxos `stdin` / `stdout`.

As a matter of fact, these sources can be both input and output, both cause and effect. Take the DOM, for example. We update (side effect) a DOM element to show text or an image to the user, but the current state of the DOM is an implicit input (side cause) to those operations as well.

Na verdade, essas fontes podem ser tanto de entrada quanto de saída, tanto de causa quanto de efeito. Veja o DOM, por exemplo. Atualizamos (efeito colateral) um elemento DOM para mostrar texto ou uma imagem ao usuário, mas o estado atual do DOM é uma entrada implícita (causa secundária) para essas operações também.

### Side Bugs

### Bugs Colaterais

+-The scenarios where side causes and side effects can lead to bugs are as varied as the programs in existence. But let's examine a scenario to illustrate these hazards, in hopes that they help us recognize similar mistakes in our own programs.

+-Os cenários onde as causas e efeitos colaterais podem levar bugs são tão variados quanto os programas existentes. Mas vamos examinar um cenário para ilustrar esses riscos, na esperança de que eles nos ajudem a reconhecer erros semelhantes em nossos próprios programas.

Consider:

Considere:

```js
var users = {};
var userOrders = {};

function fetchUserData(userId) {
    ajax( `http://some.api/user/${userId}`, function onUserData(user){
        users[userId] = user;
    } );
}

function fetchOrders(userId) {
    ajax(
        `http://some.api/orders/${userId}`,
        function onOrders(orders){
            for (let order of orders) {
                // keep a reference to latest order for each user
                users[userId].latestOrder = order;
                userOrders[order.orderId] = order;
            }
        }
    );
}

function deleteOrder(orderId) {
    var user = users[ userOrders[orderId].userId ];
    var isLatestOrder = (userOrders[orderId] == user.latestOrder);

    // deleting the latest order for a user?
    if (isLatestOrder) {
        hideLatestOrderDisplay();
    }

    ajax(
        `http://some.api/delete/order/${orderId}`,
        function onDelete(success){
            if (success) {
                // deleted the latest order for a user?
                if (isLatestOrder) {
                    user.latestOrder = null;
                }

                userOrders[orderId] = null;
            }
            else if (isLatestOrder) {
                showLatestOrderDisplay();
            }
        }
    );
}
```

I bet for some readers one of the potential bugs here is fairly obvious. If the callback `onOrders(..)` runs before the `onUserData(..)` callback, it will attempt to add a `latestOrder` property to a value (the `user` object at `users[userId]`) that's not yet been set.

Aposto que para alguns leitos um dos possíveis bugs aqui é bastante óbvio. Se o callback `onOrders(..)` for executado antes do callback `onUserData(..)`, ele tentará adicionar uma propriedade `latestOrder` a um valor (o objeto `user` em `users[userId]`) que ainda não foi definido.

So one form of "bug" that can occur with logic that relies on side causes/effects is the race condition of two different operations (async or not!) that we expect to run in a certain order but under some cases may run in a different order. There are strategies for ensuring the order of operations, and it's fairly obvious that order is critical in that case.

Portanto, uma forma de "bug" que pode ocorrer com a lógica que depende de causas/efeitos colaterais é a condição de corrida de duas operações diferentes (async ou não!) que esperamos executar em uma determinada ordem, mas em alguns casos podem ser executadas em uma ordem diferente. Existes estratégias para garantir a ordem das operações e é bastante óbvio que a ordem é crítica nesse caso.

Another more subtle bug can bite us here. Did you spot it?

Outro bug mais sutil pode nos atacar aqui. Você percebeu?

Consider this order of calls:

Considere esta ordem de chamadas:

```js
fetchUserData( 123 );
onUserData(..);
fetchOrders( 123 );
onOrders(..);

// later

fetchOrders( 123 );
deleteOrder( 456 );
onOrders(..);
onDelete(..);
```

Do you see the interleaving of `fetchOrders(..)` and `onOrders(..)` with the `deleteOrder(..)` and `onDelete(..)` pair? That potential sequencing exposes a weird condition with our side causes/effects of state management.

Você vê a intercalação de `fetchOrder(..)` e `onOrders(..)` com o par `deleteOrder(..)` e `onDelete(..)`? Esse sequenciamento potencial expõe uma condição estranha com nossas causas/efeitos colaterais de gerenciamento de estado.

There's a delay in time (because of the callback) between when we set the `isLatestOrder` flag and when we use it to decide if we should empty the `latestOrder` property of the user data object in `users`. During that delay, if `onOrders(..)` callback fires, it can potentially change which order value that user's `latestOrder` references. When `onDelete(..)` then fires, it will assume it still needs to unset the `latestOrder` reference.

Há um atraso no tempo (por causa do retorno de chamada) entre quando definimos a flag `isLatestOrder` e quando o usamos para decidir se devemos esvaziar a propriedade `latestOrder` do objeto de dados do usuário em `users`. Durante esse atraso, se o retorno da chamada `onOrders(..)` for chamado, ele pode potencialmente alterar o valor do pedido que o `latestOrder` do usuário faz referência. Quando `onDelete(..)` então disparar, ele assumirá que ainda precisa remover a referência `latestOrder`.

The bug: the data (state) *might* now be out of sync. `latestOrder` will be unset, when potentially it should have stayed pointing at a newer order that came in to `onOrders(..)`.

O bug: os dados (estado) *podem* agora estar fora de sincronia. `latestOrder` não será definido, quando potencialmente deveria ter permanecido apontando para um pedido mais recente que veio para `onOrders(..)`.

The worst part of this kind of bug is that you don't get a program-crashing exception like we did with the other bug. We just simply have state that is incorrect; our application's behavior is "silently" broken.

A pior parte desse tipo de bug é que você não obtém uma exceção de travamento como fizemos com o outro bug. Simplesmente temos um estado incorreto. O comportamento da nossa aplicação é interrompido "silenciosamente".

The sequencing dependency between `fetchUserData(..)` and `fetchOrders(..)` is fairly obvious, and straightforwardly addressed. But the potential sequencing dependency between `fetchOrders(..)` and `deleteOrder(..)` is far less obvious. These two seem to be more independent. And ensuring that their order is preserved is more tricky, because you don't know in advance (before the results from `fetchOrders(..)`) whether that sequencing really must be enforced.

A dependência de sequenciamento entre `fetchUserData(..)` e `fetchOrders(..)` é bastante óbvia e abordada de forma direta. Mas a dependência potencial de sequenciamento entre `fetchOrder(..)` e `deleteOrder(..)` é muito menos óbvia. Esses dois parecem ser mais independentes. E garantir que sua ordem seja preservada é mais complicado, poqeu você não sabe com antecedência (antes dos resultados de `fetchOrders(..)`) se essa sequência realmente deve ser aplicada.

Yes, you can recompute the `isLatestOrder` flag once `deleteOrder(..)` fires. But now you have a different problem: your UI state can be out of sync.

Sim, você pode recalcular a flag `isLatestOrder` uma vez que `deleteOrder(..)` for acionado. Mas agora você tem um problema diferente: o estado da interface do usuário pode estar fora de sincronia.

If you had called the `hideLatestOrderDisplay()` previously, you'll now need to call the function `showLatestOrderDisplay()`, but only if a new `latestOrder` has in fact been set. So you'll need to track at least three states: was the deleted order the "latest" originally, and is the "latest" set, and are those two orders different? These are solvable problems, of course. But they're not obvious by any means.

Se você chamou `hideLatestOrderDisplay()` anteriormente, agora você precisará chamar a função `showLatestOrderDisplay()`, mas apenas se uma nova `latestOrder` foi, de fato, definida. Portanto, você precisará acompanhar pelo menos três estados: o pedido excluído era o "mais recente", originalmente, e o "mais recente" foi definido, esses dois pedidos são diferentes? Esses são problemas solucionáveis, é claro. Mas eles não são óbvios de forma alguma.

All of these hassles are because we decided to structure our code with side causes/effects on a shared set of state.

Todas essas dificuldades se devem ao fato de que decidimos estruturar nosso código com causas/efeitos colaterais em um conjunto de estados compartilhado.

Functional programmers detest these sorts of side cause/effect bugs because of how much it hurts our ability to read, reason about, validate, and ultimately **trust** the code. That's why they take the principle to avoid side causes/effects so seriously.

Os programadores funcionais detestam esses tipos de bugs de causa/efeitos colaterais por causa do quanto eles prejudicam nossa capacidade de ler, raciocinar sobre, validar e, finalmente, **confiar** no código. É por isso que eles levam o princípio de evitar causas/efeitos colaterais tão a sério.

There are multiple different strategies for avoiding/fixing side causes/effects. We'll talk about some later in this chapter, and others in later chapters. I'll say one thing for certain: **writing with side causes/effects is often of our normal default** so avoiding them is going to require careful and intentional effort.

Existem varias estratégias diferentes para evitar/corrigir causas/efeitos colaterais. Falaremos sobre alguns posteriormente neste capítulo e outros em capítulos posteriores. Direi uma coisa com certeza: **escrever com causas/efeitos colaterais costuma ser nosso padrão normal**, portanto, evitá-los exigirá um esforço cuidadoso e intencional.

## Once Is Enough, Thanks

## Uma Vez é o Suficiente, Obrigado

If you must make side effect changes to state, one class of operations that's useful for limiting the potential trouble is idempotence. If your update of a value is idempotent, then data will be resilient to the case where you might have multiple such updates from different side effect sources.

Se você precisar fazer alterações de efeito colateral no estado, uma classe de operações que é útil para limitar o problema potencial é a idempotência. Se a atualização de um valor for idempotente, os dados serão resilientes ao caso em que você possa ter várias dessas atualizações de diferentes fontes de efeitos colaterais. 

If you try to research it, the definition of idempotence can be a little confusing; mathematicians use a slightly different meaning than programmers typically do. However, both perspectives are useful for the functional programmer.

Se você tentar pesquisar, a definição de idempotência pode ser um pouco confusa. Os matemáticos usam um significado ligeiramente diferente do que os programadores costumam fazer. No entanto, ambas as perspectivas são úteis para o programador funcional.

First, let's give a counter example that is neither mathematically nor programmingly idempotent:

Primeiro, vamos dar um contra-exemplo que não é nem matematicamente nem idempotente de programação:

```js
function updateCounter(obj) {
    if (obj.count < 10) {
        obj.count++;
        return true;
    }

    return false;
}
```

This function mutates an object via reference by incrementing `obj.count`, so it produces a side effect on that object. If `updateCounter(o)` is called multiple times -- while `o.count` is less than `10`, that is -- the program state changes each time. Also, the output of `updateCounter(..)` is a Boolean, which is not suitable to feed back into a subsequent call of `updateCounter(..)`.

Esta função altera um objeto por meio de referência incrementando `obj.count`, de modo que produz um efeito colateral naquele objeto. Se `updateCounter(o)` é chamado várias vezes -- enquanto `o.count` é menor que `10`, isto é -- o estado do programa muda a cada vez. Além disso, a saída de `updateCounter(..)` é um booleano, que não é adequado para realimentar uma chamada subsequente de `updateCounter(..)`.

### Mathematical Idempotence

### Idempotência Matemática

From the mathematical point of view, idempotence means an operation whose output won't ever change after the first call, if you feed that output back into the operation over and over again. In other words, `foo(x)` would produce the same output as `foo(foo(x))` and `foo(foo(foo(x)))`.

Do ponto de vista matemático, idempotência significa uma operação cuja saída nunca mudará após a primeira chamada, se você alimentar essa saída de volta para a operação repetidamente. Em outras palavras, `foo (x)` produziria a mesma saída que `foo(foo(x))` e `foo(foo(foo(x)))`. 

A typical mathematical example is `Math.abs(..)` (absolute value). `Math.abs(-2)` is `2`, which is the same result as `Math.abs(Math.abs(Math.abs(Math.abs(-2))))`. Other idempotent mathematical utilities include:

Um exemplo matemático típico é `Math.abs(..)`(valor absoluto). `Math.abs(-2)` é `2`, que é o mesmo resultado que `Math.abs(Math.abs(Math.abs(Math.abs(-2))))`. Outros utilitários matemáticos idempotentes incluem:

* `Math.min(..)`
* `Math.max(..)`
* `Math.round(..)`
* `Math.floor(..)`
* `Math.ceil(..)`

Some custom mathematical operations we could define with this same characteristic:

Algumas operações matemáticas personalizadas que podemos definir com esta mesma característica:

```js
function toPower0(x) {
    return Math.pow( x, 0 );
}

function snapUp3(x) {
    return x - (x % 3) + (x % 3 > 0 && 3);
}

toPower0( 3 ) == toPower0( toPower0( 3 ) );         // true

snapUp3( 3.14 ) == snapUp3( snapUp3( 3.14 ) );      // true
```

Mathematical-style idempotence is **not** restricted to mathematical operations. Another place we can illustrate this form of idempotence is with JavaScript primitive type coercions:

A idempotência de estilo matemático **não** se restringe a operações matemáticas. Outro lugar em que podemos ilustrar essa forma de idempotência é com as coerções de tipo primitivo do JavaScript:

```js
var x = 42, y = "hello";

String( x ) === String( String( x ) );              // true

Boolean( y ) === Boolean( Boolean( y ) );           // true
```

Earlier in the text, we explored a common FP tool that fulfills this form of idempotence:

No início do texto, exploramos uma ferramenta comum da PF que preenche essa forma de idempotência:

```js
identity( 3 ) === identity( identity( 3 ) );    // true
```

Certain string operations are also naturally idempotent, such as:

Certas operações de string também são naturalmente idempotentes, como:

```js
function upper(x) {
    return x.toUpperCase();
}

function lower(x) {
    return x.toLowerCase();
}

var str = "Hello World";

upper( str ) == upper( upper( str ) );              // true

lower( str ) == lower( lower( str ) );              // true
```

We can even design more sophisticated string formatting operations in an idempotent way, such as:

Podemos até projetar operações de formatação de strings mais sofisticadas de maneira idempotente, como:

```js
function currency(val) {
    var num = parseFloat(
        String( val ).replace( /[^\d.-]+/g, "" )
    );
    var sign = (num < 0) ? "-" : "";
    return `${sign}$${Math.abs( num ).toFixed( 2 )}`;
}

currency( -3.1 );                                   // "-$3.10"

currency( -3.1 ) == currency( currency( -3.1 ) );   // true
```

`currency(..)` illustrates an important technique: in some cases the developer can take extra steps to normalize an input/output operation to ensure the operation is idempotent where it normally wouldn't be.

`currency(..)` ilustra uma técnica importante: em alguns casos, o desenvolvedor pode tomar medidas extras para normalizar uma operação de entrada/saída para garantir que a operação seja idempotente onde normalmente não seria.

Wherever possible, restricting side effects to idempotent operations is much better than unrestricted updates.

Sempre que possível, restringir os efeitos colaterais a operações idempotentes é muito melhor do que atualizações irrestritas.

### Programming Idempotence
### Idempotência de Programação

The programming-oriented definition for idempotence is similar, but less formal. Instead of requiring `f(x) === f(f(x))`, this view of idempotence is just that `f(x);` results in the same program behavior as `f(x); f(x);`. In other words, the result of calling `f(x)` subsequent times after the first call doesn't change anything.

A definição orientada à programação para idempotência é semelhante, mas menos formal. Em vez de exigir `f(x) === f(f(x))`, esta visão de idempotência é apenas que `f(x);` resulta no mesmo comportamento de programa que `f(x);f(x)`. Em outras palavras, o resultado de chamar `f(x)` vezes subsequentes após a primeira chamada não muda nada.

That perspective fits more with our observations about side effects, because it's more likely that such an `f(..)` operation creates an idempotent side effect rather than necessarily returning an idempotent output value.

Essa perspectiva se encaixa mais com nossas observações sobre os efeitos colaterais, porque é mais provável que tal operação `f(..)` crie um efeito colateral idempotente ao invés de necessariamente retornar um valor de saída idempotente.

This idempotence-style is often cited for HTTP operations (verbs) such as GET or PUT. If an HTTP REST API is properly following the specification guidance for idempotence, PUT is defined as an update operation that fully replaces a resource. As such, a client could either send a PUT request once or multiple times (with the same data), and the server would have the same resultant state regardless.

Este estilo de idempotência é frequentemente citado para operações HTTP(verbos) como GET ou PUT. Se uma API HTTP REST estiver seguindo adequadamente a orientação de especificação para idempotência, PUT é definido como uma operação de atualização que substitui totalmente um recurso. Assim, um cliente poderia enviar uma solicitação PUT uma ou várias vezes (com os mesmos dados) e o servidor teria o mesmo estado resultante independentemente.

Thinking about this in more concrete terms with programming, let's examine some side effect operations for their idempotence (or lack thereof):

Pensando nisso em termos mais concretos com programação, vamos examinar algumas operações de efeito colateral para sua idempotência (ou falta dela):

```js
// idempotent:
obj.count = 2;
a[a.length - 1] = 42;
person.name = upper( person.name );

// non-idempotent:
obj.count++;
a[a.length] = 42;
person.lastUpdated = Date.now();
```

Remember: the notion of idempotence here is that each idempotent operation (like `obj.count = 2`) could be repeated multiple times and not change the program state beyond the first update. The non-idempotent operations change the state each time.

Lembre-se: a noção de idempotência aqui é que cada operação idempotente (como `obj.count = 2`) pode ser repetida várias vezes e não alterar o estado do programa após a primeira atualização. As operações não idempotentes mudam o estado a cada vez.

What about DOM updates?
E quanto às atualizações do DOM?

```js
var hist = document.getElementById( "orderHistory" );

// idempotent:
hist.innerHTML = order.historyText;

// non-idempotent:
var update = document.createTextNode( order.latestUpdate );
hist.appendChild( update );
```

The key difference illustrated here is that the idempotent update replaces the DOM element's content. The current state of the DOM element is irrelevant, because it's unconditionally overwritten. The non-idempotent operation adds content to the element; implicitly, the current state of the DOM element is part of computing the next state.

A principal diferença ilustrada aqui é que a atualização idempotente substitui o conteúdo do elemento DOM. O estado atual do elemento DOM é irrelevante, porque é substituído incondicionalmente. A operação não idempotente adiciona conteúdo ao elemento. implicitamente, o estado atual do elemento DOM faz parte do cálculo do próximmo estado.

It won't always be possible to define your operations on data in an idempotent way, but if you can, it will definitely help reduce the chances that your side effects will crop up to break your expectations when you least expect it.

Nem sempre será possível definir suas operações em dados de uma forma idempotente, mas se você puder, isso certamente ajudará a reduzir as chances de que seus efeitos colaterais surjam para quebrar suas expectativas quando você menos espera.

## Pure Bliss
## Puro Êxtase

A function with no side causes/effects is called a pure function. A pure function is idempotent in the programming sense, because it cannot have any side effects. Consider:

Uma função sem causas/efeitos colaterais é chamada de função pura. Uma função pura é idempotente no sentido de programação, porque não pode ter efeitos colaterais.
Considerar:

```js
function add(x,y) {
    return x + y;
}
```

All the inputs (`x` and `y`) and outputs (`return ..`) are direct; there are no free variable references. Calling `add(3,4)` multiple times would be indistinguishable from only calling it once. `add(..)` is pure and programming-style idempotent.

Todas as entradas (`x` e `y`) e saídas (`return ..`) são diretas; não há referências de variáveis livres. Chamar `add(3,4)` várias vezes seria indistinguível de chamá-lo apenas uma vez. `add(3,4)` é puro e idempotente de estilo de programação.

However, not all pure functions are idempotent in the mathematical sense, because they don't have to return a value that would be suitable for feeding back in as their own input. Consider:

No entanto, nem todas as funções puras são idempotentes no sentido matemático, porque elas não precisam retornar um valor que seria adequado para realimentação como sua própria entrada. Considerar: 

```js
function calculateAverage(nums) {
    var sum = 0;
    for (let num of nums) {
        sum += num;
    }
    return sum / nums.length;
}

calculateAverage( [1,2,4,7,11,16,22] );         // 9
```

The output `9` is not an array, so you cannot pass it back in: `calculateAverage(calculateAverage( .. ))`.

A saída `9` não é uma matriz, então você não pode passá-la de volta em: `calculateAverage(calculateAverage(..))`.

As we discussed earlier, a pure function *can* reference free variables, as long as those free variables aren't side causes.

Como discutimos anteriormente, uma função pura *pode* fazer referência a variáveis livres, desde que essas variáveis livres não sejam causas secundárias.

Some examples:

Alguns exemplos:

```js
const PI = 3.141592;

function circleArea(radius) {
    return PI * radius * radius;
}

function cylinderVolume(radius,height) {
    return height * circleArea( radius );
}
```

`circleArea(..)` references the free variable `PI`, but it's a constant so it's not a side cause. `cylinderVolume(..)` references the free variable `circleArea`, which is also not a side cause because this program treats it as, in effect, a constant reference to its function value. Both these functions are pure.

`circleArea(..)` referencia a variável livre `PI`, mas é uma constante, então não é uma causa secundária. `cylinderVolume(..)` faz referência à variável livre `circleArea`, o que também não é uma causa secundária porque este programa a trata, na verdade, como uma referência constante ao valor de sua função.

Ambas as funções são puras.

Another example where a function can still be pure but reference free variables is with closure:

Outro exemplo em que uma função ainda pode ser pura, mas com variáveis livres de referência é com closure:

```js
function unary(fn) {
    return function onlyOneArg(arg){
        return fn( arg );
    };
}
```

`unary(..)` itself is clearly pure -- its only input is `fn` and its only output is the `return`ed function -- but what about the inner function `onlyOneArg(..)`, which closes over the free variable `fn`?

`unário(..)` em si é claramente puro -- sua única entrada é `fn` e sua única saída é a função `return` -- mas e a função interna `onlyOneArg(..)`, que fecha a variável livre `fn`?

It's still pure because `fn` never changes. In fact, we have full confidence in that fact because lexically speaking, those few lines are the only ones that could possibly reassign `fn`.

Ainda é puro porque `fn` nunca muda. Na verdade, temos total confiança nesse fato porque, lexicamente, essas poucas linhas são as únicas que poderiam possivelmente reatribuir `fn`.

**Note:** `fn` is a reference to a function object, which is by default a mutable value. Somewhere else in the program *could*, for example, add a property to this function object, which technically "changes" the value (mutation, not reassignment). However, because we're not relying on anything about `fn` other than our ability to call it, and it's not possible to affect the callability of a function value, `fn` is still effectively unchanging for our reasoning purposes; it cannot be a side cause.

**Nota:** `fn` é uma referência a um objeto de função, que por padrão é um valor mutável. Em algum outro lugar no programa *poderia*, por exemplo, adicionar uma propriedade a esse objeto de função, que tecnicamente "altera" o valor (mutação, não reatribuição). No entanto, como não estamos contando com nada sobre `fn` além de nossa capacidade de chamá-lo, e não é possível afetar a capacidade de chamada de um valor de função, `fn` ainda é efetivamente imutável para nossos propósitos de raciocínio; não pode ser uma causa secundária.

Another common way to articulate a function's purity is: **given the same input(s), it always produces the same output.** If you pass `3` to `circleArea(..)`, it will always output the same result (`28.274328`).

Outra forma comum de articular a pureza de uma função é: **dada a(s) mesma(s) entrada(s), ela sempre produz a mesma saída. **Se você passar `3` para `circleArea(..)`, ela sempre terá o mesmo resultado como saída (`28.274328`).

If a function *can* produce a different output each time it's given the same inputs, it is impure. Even if such a function always `return`s the same value, if it produces an indirect output side effect, the program state is changed each time it's called; this is impure.

Se uma função *pode* produzir uma saída diferente cada vez que recebe as mesmas entradas, ela é impura. Mesmo que tal função sempre `retorne` o mesmo valor, se ela produz um efeito colateral de saída indireto, o estado do programa é alterado cada vez que é chamado; isso é impuro.

Impure functions are undesirable because they make all of their calls harder to reason about. A pure function's call is perfectly predictable. When someone reading the code sees multiple `circleArea(3)` calls, they won't have to spend any extra effort to figure out what its output will be *each time*.

Funções impuras são indesejáveis porque tornam todas as chamadas mais difíceis de raciocinar. A chamada de uma função pura é perfeitamente previsível. Quando alguém lendo o código vê várias chamadas `circleArea(3)`, eles não terão que gastar nenhum esforço extra para descobrir qual será sua saída *a cada vez*.

**Note:** An interesting thing to ponder: is the heat produced by the CPU while performing any given operation an unavoidable side effect of even the most pure functions/programs? What about just the CPU time delay as it spends time on a pure operation before it can do another one?

**Nota:** Uma coisa interessante a considerar: o calor produzido pela CPU durante a execução de qualquer operação é um efeito colateral inevitável até mesmo das funções/programas mais puros? Que tal apenas o atraso de tempo da CPU, uma vez que ela gasta tempo em uma operação pura antes de poder fazer outra?

### Purely Relative

### Puramente Relativo

We have to be very careful when talking about a function being pure. JavaScript's dynamic value nature makes it all too easy to have non-obvious side causes/effects.

Devemos ter muito cuidado ao falar sobre uma função ser pura. A natureza do valor dinâmico do JavaScript torna muito fácil de ter causas/efeitos colaterais não óbvios.

Consider:

Considere:

```js
function rememberNumbers(nums) {
    return function caller(fn){
        return fn( nums );
    };
}

var list = [1,2,3,4,5];

var simpleList = rememberNumbers( list );
```

`simpleList(..)` looks like a pure function, as it's a reference to the inner function `caller(..)`, which just closes over the free variable `nums`. However, there's multiple ways that `simpleList(..)` can actually turn out to be impure.

`simpleList(..)` parece uma função pura, pois é uma referência à função interna `caller(..)`, que apenas fecha sobre a variável livre `nums`. No entanto, existem várias maneiras pelas quais `simpleList(..)` pode realmente se tornar impuro.

First, our assertion of purity is based on the array value (referenced both by `list` and `nums`) never changing:

Primeiro, nossa afirmação de pureza é baseada no valor do array (referenciado por `list` e `nums`) nunca mudando:

```js
function median(nums) {
    return (nums[0] + nums[nums.length - 1]) / 2;
}

simpleList( median );       // 3

// ..

list.push( 6 );

// ..

simpleList( median );       // 3.5
```

When we mutate the array, the `simpleList(..)` call changes its output. So, is `simpleList(..)` pure or impure? Depends on your perspective. It's pure for a given set of assumptions. It could be pure in any program that didn't have the `list.push(6)` mutation.

Quando alteramos o array, a chamada `simpleList(..)` altera sua saída. Então, `simpleList(..)` é puro ou impuro? Depende da sua perspectiva. É puro para um determinado conjunto de suposições. Ele poderia ser puro em qualquer programa que não tivesse a mutação `list.push(6)`.

We could guard against this kind of impurity by altering the definition of `rememberNumbers(..)`. One approach is to duplicate the `nums` array:

Poderíamos nos proteger contra esse tipo de impureza alterando a definição de `lembrarNúmeros(..)`. Uma abordagem é duplicar a matriz `nums`:

```js
function rememberNumbers(nums) {
    // make a copy of the array
    nums = [...nums];

    return function caller(fn){
        return fn( nums );
    };
}
```

But an even trickier hidden side effect could be lurking:

Mas um efeito colateral oculto ainda mais complicado pode estar à espreita:

```js
var list = [1,2,3,4,5];

// make `list[0]` be a getter with a side effect
Object.defineProperty(
    list,
    0,
    {
        get: function(){
            console.log( "[0] was accessed!" );
            return 1;
        }
    }
);

var simpleList = rememberNumbers( list );
// [0] was accessed!
```

A perhaps more robust option is to change the signature of `rememberNumbers(..)` to not receive an array in the first place, but rather the numbers as individual arguments:

Uma opção talvez mais robusta é alterar a assinatura de `rememberNumbers(..)` para não receber uma matriz em primeiro lugar, mas sim os números como argumentos individuais:

```js
function rememberNumbers(...nums) {
    return function caller(fn){
        return fn( nums );
    };
}

var simpleList = rememberNumbers( ...list );
// [0] was accessed!
```

The two `...`s have the effect of copying `list` into `nums` instead of passing it by reference.

Os dois `...`s têm o efeito de copiar `list` em `nums` em vez de passá-lo por referência.

**Note:** The console message side effect here comes not from `rememberNumbers(..)` but from the `...list` spreading. So in this case, both `rememberNumbers(..)` and `simpleList(..)` are pure.

**Nota:** O efeito colateral da mensagem do console aqui não vem de `rememberNumber(..)` mas da propagação de `...list`. Portanto, neste caso, `rememberNumbers(..)` e `simpleList(..)` são puros.

But what if the mutation is even harder to spot? Composition of a pure function with an impure function **always** produces an impure function. If we pass an impure function into the otherwise pure `simpleList(..)`, it's now impure:

Mas e se a mutação for ainda mais difícil de detectar? A composição de uma função pura com uma função impura **sempre** produz uma função impura. Se passarmos uma função impura para `simpleList(..)` pura, agora ela é impura:

```js
// yes, a silly contrived example :)
function firstValue(nums) {
    return nums[0];
}

function lastValue(nums) {
    return firstValue( nums.reverse() );
}

simpleList( lastValue );    // 5

list;                       // [1,2,3,4,5] -- OK!

simpleList( lastValue );    // 1
```

**Note:** Despite `reverse()` looking safe (like other array methods in JS) in that it returns a reversed array, it actually mutates the array rather than creating a new one.

**Nota:** Apesar de `reverse()` parecer seguro (como outros métodos de array em JS), ele retorna um array invertido, na verdade ele modifica o array ao invés de criar um novo.

We need a more robust definition of `rememberNumbers(..)` to guard against the `fn(..)` mutating its closed over `nums` via reference:

Precisamos de uma definição mais robusta de `rememberNumbers(..)` para nos proteger contra a mutação de `fn(..)` em `nums` via referência:

```js
function rememberNumbers(...nums) {
    return function caller(fn){
        // send in a copy!
        return fn( [...nums] );
    };
}
```

So is `simpleList(..)` reliably pure yet!? **Nope.** :(

Então `simpleList(..)` é confiavelmente puro ainda!? **Não.** :(

We're only guarding against side effects we can control (mutating by reference). Any function we pass that has other side effects will have polluted the purity of `simpleList(..)`:

Estamos apenas nos protegendo contra os efeitos colaterais que podemos controlar (mutação por referência). Qualquer função que passarmos que tenha outros efeitos colaterais terá poluído a pureza de `simpleList(..)`:

```js
simpleList( function impureIO(nums){
    console.log( nums.length );
} );
```

In fact, there's no way to define `rememberNumbers(..)` to make a perfectly pure `simpleList(..)` function.

Na verdade, não há como definir `rememberNumbers(..)` para fazer uma função `simpleList(..)` perfeitamente pura.

Purity is about confidence. But we have to admit that in many cases, **any confidence we feel is actually relative to the context** of our program and what we know about it. In practice (in JavaScript) the question of function purity is not about being absolutely pure or not, but about a range of confidence in its purity.

Pureza é sobre confiança. Mas temos que admitir que, em muitos casos, **qualquer confiança que sentirmos é, na verdade, relativa ao contexto** do nosso programa e ao que sabemos sobre ele. Na prática (em JavaScript), a questão da pureza da função não é sobre ser absolutamente puro ou não, mas sobre uma gama de confiança em sua pureza.

The more pure, the better. The more effort you put into making a function pure(r), the higher your confidence will be when you read code that uses it, and that will make that part of the code more readable.

Quanto mais puro, melhor. Quanto mais esforço você colocar em fazer uma função pura(r), maior será sua confiança ao ler o código que a usa, e isso tornará essa parte do código mais legível.

## There or Not
## Existe ou Não

So far, we've defined function purity both as a function without side causes/effects and as a function that, given the same input(s), always produces the same output. These are just two different ways of looking at the same characteristics.

Até agora, definimos a pureza da função como sem causas/efeitos colaterais e como uma função que, dada a(s) mesma(s) entrada(s), sempre produz a mesma saída. Essas são apenas duas maneiras diferentes de encarar as mesmas características.

But a third way of looking at function purity, and perhaps the most widely accepted definition, is that a pure function has referential transparency.

Mas uma terceira maneira de olhar para a pureza da função, e talvez a definição mais amplamente aceita, é que uma função pura tem transparência referencial.

Referential transparency is the assertion that a function call could be replaced by its output value, and the overall program behavior wouldn't change. In other words, it would be impossible to tell from the program's execution whether the function call was made or its return value was inlined in place of the function call.

Transparência referencial é a afirmação de que uma chamada de função poderia ser substituída por seu valor de saída e o comportamento geral do programa não mudaria. Em outras palavras, seria impossível dizer, a partir da execução do programa, se a chamada de função foi feita ou se seu valor de retorno foi embutido no lugar da chamada de função.

From the perspective of referential transparency, both of these programs have identical behavior as they are built with pure functions:

Do ponto de vista da transparência referencial, ambos os programas têm comportamento idêntico, pois são construídos com funções puras:

```js
function calculateAverage(nums) {
    var sum = 0;
    for (let num of nums) {
        sum += num;
    }
    return sum / nums.length;
}

var numbers = [1,2,4,7,11,16,22];

var avg = calculateAverage( numbers );

console.log( "The average is:", avg );      // The average is: 9
```

```js
function calculateAverage(nums) {
    var sum = 0;
    for (let num of nums) {
        sum += num;
    }
    return sum / nums.length;
}

var numbers = [1,2,4,7,11,16,22];

var avg = 9;

console.log( "The average is:", avg );      // The average is: 9
```

The only difference between these two snippets is that in the latter one, we skipped the `calculateAverage(nums)` call and just inlined its output (`9`). Since the rest of the program behaves identically, `calculateAverage(..)` has referential transparency, and is thus a pure function.

A única diferença entre esses dois trechos é que, no último, pulamos a chamada `calculateAverage(nums)` e apenas alinhamos sua saída (`9`). Uma vez que o resto do programa se comporta de forma idêntica, `calculateAverage(..)` tem transparência referencial e, portanto, é uma função pura.

### Mentally Transparent
### Mentalmente Transparente

The notion that a referentially transparent pure function *can be* replaced with its output does not mean that it *should literally be* replaced. Far from it.

A noção de que uma função pura referencialmente transparente *pode ser* substituída por sua saída não significa que ela *deva ser* substituída literalmente. Longe disso.

The reasons we build functions into our programs instead of using pre-computed magic constants are not just about responding to changing data, but also about readability with proper abstractions. The function call to calculate the average of that list of numbers makes that part of the program more readable than the line that just assigns the value explicitly. It tells the story to the reader of where `avg` comes from, what it means, and so on.

As razões pelas quais criamos funções em nossos programas em vez de usar constantes mágicas pré-calculadas não são apenas para responder a dados variáveis, mas também sobre a legibilidade com abstrações adequadas. A chamada de função para calcular a média dessa lista de números torna aquela parte do programa mais legível do que a linha que apenas atribui o valor explicitamente. Conta ao leitor uma história de onde vem o `avg`, o que ele significa e assim por diante.

What we're really suggesting with referential transparency is that as you're reading a program, once you've mentally computed what a pure function call's output is, you no longer need to think about what that exact function call is doing when you see it in code, especially if it appears multiple times.

O que estamos realmente sugerindo com transparência referencial é que enquanto você está lendo um programa, uma vez que você calculou mentalmente o que é a saída de uma chamada de função pura, você não precisa mais pensar sobre o que aquela chamada de função exata está fazendo quando você vê em código, especialmente se aparecer várias vezes.

That result becomes kinda like a mental `const` declaration, which as you're reading you can transparently swap in and not spend any more mental energy working out.

Esse resultado se torna como uma declaração mental `const`, que enquanto você lê, vocÊ pode trocar de forma transparente e não gastar mais energia mental trabalhando.

Hopefully the importance of this characteristic of a pure function is obvious. We're trying to make our programs more readable. One way we can do that is to give the reader less work, by providing assistance to skip over the unnecessary stuff so they can focus on the important stuff.

Esperançosamente, a importância dessa característica de uma função pura é óbvia. Estamos tentando tornar nossos programas mais legíveis. Uma maneira de fazer isso é dar menos trabalho ao leitor, fornecendo assistência para pular coisas desnecessárias para que eles possam se concentrar nas coisas importantes.

The reader shouldn't need to keep re-computing some outcome that isn't going to change (and doesn't need to). If you define a pure function with referential transparency, the reader won't have to.

O leitor não precisar ficar recalculando algum resultado que não vai mudar (e não precisa). Se você definir uma função pura com transparência referencial, o leitor não precisará fazer isso.

### Not So Transparent?
### Não é Tão Transparente?

What about a function that has a side effect, but this side effect isn't ever observed or relied upon anywhere else in the program? Does that function still have referential transparency?

Que tal uma função que tem um efeito colateral, mas esse efeito colateral nunca é observado ou invocado em nenhuma outra parte do programa? Essa função ainda tem transparência referencial?

Here's one:

```js
function calculateAverage(nums) {
    sum = 0;
    for (let num of nums) {
        sum += num;
    }
    return sum / nums.length;
}

var sum;
var numbers = [1,2,4,7,11,16,22];

var avg = calculateAverage( numbers );
```

Did you spot it?

Você percebeu?

`sum` is an outer free variable that `calculateAverage(..)` uses to do its work. But, every time we call `calculateAverage(..)` with the same list, we're going to get `9` as the output. And this program couldn't be distinguished in terms of behavior from a program that replaced the `calculateAverage(nums)` call with the value `9`. No other part of the program cares about the `sum` variable, so it's an unobserved side effect.

`soma` é uma variável externa livre que `calculateAverage(..)` usa para fazer seu trabalho. Mas, toda vez que chamarmos `calculateAverage(..)` com a mesma lista, obteremos `9` como saída. E este programa não poderia ser diferenciado em termos de comportamento de um programa que substituiu a chamada `calculateAverage(nums)` pelo valor `9`. Nenhuma outra parte do programa se preocupa com a variável `sum`, então é um efeito colateral não observado.

Is a side cause/effect that's unobserved like this tree:

É uma causa/efeito colateral não observado como está árvore:

> If a tree falls in the forest, but no one is around to hear it, does it still make a sound?

> Se uma árvore cair na floresta, mas não houver ninguém por perto para ouvi-la, ela ainda faz algum barulho?

By the narrowest definition of referential transparency, I think you'd have to say `calculateAverage(..)` is still a pure function. However, because we're trying to avoid a strictly academic approach in favor of balancing it with pragmatism, I also think this conclusion needs more perspective. Let's explore.

Pela definição mais restrita de transparência referencial, acho que você teria que dizer que `calculateAverage(..)` ainda é uma função pura. No entanto, como estamos tentando evitar uma abordagem estritamente acadêmica em favor de equilibrá-la com o pragmatismo, também acho que essa conclusão precisa de mais perspectiva. Vamos explorar.

#### Performance Effects
#### Efeitos de Desempenho

You'll generally find these kind of side-effects-that-go-unobserved being used to optimize the performance of an operation. For example:

Geralmente, você encontrará esse tipo de efeitos colaterais que não são observados sendo usados para otimizar o desempenho de uma operação. Por exemplo:

```js
var cache = [];

function specialNumber(n) {
    // if we've already calculated this special number,
    // skip the work and just return it from the cache
    if (cache[n] !== undefined) {
        return cache[n];
    }

    var x = 1, y = 1;

    for (let i = 1; i <= n; i++) {
        x += i % 2;
        y += i % 3;
    }

    cache[n] = (x * y) / (n + 1);

    return cache[n];
}

specialNumber( 6 );             // 4
specialNumber( 42 );            // 22
specialNumber( 1E6 );           // 500001
specialNumber( 987654321 );     // 493827162
```

This silly `specialNumber(..)` algorithm is deterministic and thus pure from the definition that it always gives the same output for the same input. It's also pure from the referential transparency perspective -- replace any call to `specialNumber(42)` with `22` and the end result of the program is the same.

Este algoritmo tolo `specialNumber(..)` é determinístico e, portanto, puro da definição de que sempre dá a mesma saída para a mesma entrada. Também é puro de perspectiva de transparência referencial -- substitua qualquer chamada a `specialNumber(42)` com `22` e o resultado final do programa é o mesmo.

However, the function has to do quite a bit of work to calculate some of the bigger numbers, especially the `987654321` input. If we needed to get that particular special number multiple times throughout our program, the `cache`ing of the result means that subsequent calls are far more efficient.

No entanto, a função tem que fazer um pouco de trabalho para calcular alguns dos números maiores, especialmente a entrada `987654321`. Se precisarmos obter aquele número especial em particular várias vezes em nosso programa, o `cache`amento do resultado significa que as chamadas subsequentes são muito mais eficientes.

Don't be so quick to assume that you could just run the calculation `specialNumber(987654321)` once and manually stick that result in some variable/constant. Programs are often highly modularized and globally accessible scopes are not usually the way you want to share state between those independent pieces. Having `specialNumber(..)` do its own caching (even though it happens to be using a global variable to do so!) is a more preferable abstraction of that state sharing.

Não se precipite ao assumir que você poderia simplesmente executar o cálculo `specialNumber(987654321)` uma vez e colar manualmente esse resultado em alguma variável/constante. Os programas costumam ser altamente modularizados e os escopos globalmente acessíveis não costumam ser a maneira como você deseja compartilhar o estado entre essas partes independentes. Ter `specialNumber(..)` fazendo seu próprio cache (mesmo que esteja usando uma variável global para fazer isso!) É uma abstração mais preferível do que esse compartilhamento de estado.

The point is that if `specialNumber(..)` is the only part of the program that accesses and updates the `cache` side cause/effect, the referential transparency perspective observably holds true, and this might be seen as an acceptable pragmatic "cheat" of the pure function ideal.

O ponto é que se `specialNumber(..)` é a única parte do programa que acessa e atualiza a causa/efeito colateral da `cache`, a perspectiva de transparência referencial é observável e isso pode ser visto como uma pragmática aceitável "trapaça" do ideal de função pura.

But should it?

Mas deveria?

Typically, this sort of performance optimization side effecting is done by hiding the caching of results so they *cannot* be observed by any other part of the program. This process is referred to as memoization. I always think of that word as "memorization"; I have no idea if that's even remotely where it comes from, but it certainly helps me understand the concept better.

Normalmente, esse tipo de efeito colateral de otimização de desempenho é feito ocultando o cache de resultados para que *não* possam ser observados por qualquer outra parte do programa. Este processo é conhecido como memoização. Sempre penso nessa palavra como "memorização"; Não tenho ideia se é remotamente de onde vem, mas certamente me ajuda a entender melhor o conceito.

Consider:

Considere:

```js
var specialNumber = (function memoization(){
    var cache = [];

    return function specialNumber(n){
        // if we've already calculated this special number,
        // skip the work and just return it from the cache
        if (cache[n] !== undefined) {
            return cache[n];
        }

        var x = 1, y = 1;

        for (let i = 1; i <= n; i++) {
            x += i % 2;
            y += i % 3;
        }

        cache[n] = (x * y) / (n + 1);

        return cache[n];
    };
})();
```

We've contained the `cache` side causes/effects of `specialNumber(..)` inside the scope of the `memoization()` IIFE, so now we're sure that no other parts of the program *can* observe them, not just that they *don't* observe them.

Contemos as causas/efeitos colaterais do `cache` de `specialNumber(..)` dentro do escopo do IIFE `memoization()`, então agora temos certeza de que nenhuma outra parte do programa *pode* observá-los, eles simplesmente *não* observam eles.

That last sentence may seem like a subtle point, but actually I think it might be **the most important point of the entire chapter**. Read it again.

Essa última frase pode parecer um ponto sutil, mas na verdade acho que pode ser **o ponto mais importante de todo o capítulo**. Leia isso novamente.

Recall this philosophical musing:

Lembre-se desta reflexão filosófica:

> If a tree falls in the forest, but no one is around to hear it, does it still make a sound?

> Se uma árvore cair na floresta, mas não houver ningém por perto para ouvi-la, ela ainda faz algum barulho?

Going with the metaphor, what I'm getting at is: whether the sound is made or not, it would be better if we never create a scenario where the tree can fall without us being around; we'll always hear the sound when a tree falls.

Seguindo com a metáfora, o que quero chegar é: quer o som seja feito ou não, seria melhor se nunca criarmos um cenário em que a árvore possa cair sem estarmos por perto, sempre ouviremos o som quando uma árvore cair.

The purpose of reducing side causes/effects is not per se to have a program where they aren't observed, but to design a program where fewer of them are possible, because this makes the code easier to reason about. A program with side causes/effects that *just happen* to not be observed is not nearly as effective in this goal as a program that *cannot* observe them.

O propósito de reduzir as causas/efeitos colaterais não é para se ter um programa onde eles não sejam observados, mas projetar um programa onde menos deles sejam possíveis, porque isso torna o código mais fácil de raciocinar. Um programa com causas/efeitos colaterais que *simplesmente acontecem* que não são observados não são tão eficaz neste objetivo quanto um programa que *não pode* observá-los.

If side causes/effects can happen, the writer and reader must mentally juggle them. Make it so they can't happen, and both writer and reader will find more confidence over what can and cannot happen in any part.

Se causas/efeitos colaterais podem acontecer, o escritor e o leitor devem manipulá-los mentalmente. Faça com que isso não aconteça, e tanto o escritor quanto o leitor terão mais confiança sobre o que pode ou não aocntecer em qualquer parte.

## Purifying
## Purificando

The first best option in writing functions is that you design them from the beginning to be pure. But you'll spend plenty of time maintaining existing code, where those kinds of decisions were already made; you'll run across a lot of impure functions.

A primeira e melhor opção ao escrever funções é projetá-las desde o início para que sejam puras. Mas você gastará muito tempo mantendo o código existente, onde esse tipo de decisão já foi feito, você encontrará muitas funções impuras.

If possible, refactor the impure function to be pure. Sometimes you can just shift the side effects out of a function to the part of the program where the call of that function happens. The side effect wasn't eliminated, but it was made more obvious by showing up at the call-site.

Se possível, refatore a função impura para ser pura. Às vezes, você pode simplesmente deslocar os efeitos colaterais de uma função para a parte do programa onde ocorre a chamada dessa função. O efeito colateral não foi eliminado, mas se tornou mais óbvio ao aparecer na chamada da função.

Consider this trivial example:

Considere este exemplo trivial:

```js
function addMaxNum(arr) {
    var maxNum = Math.max( ...arr );
    arr.push( maxNum + 1 );
}

var nums = [4,2,7,3];

addMaxNum( nums );

nums;       // [4,2,7,3,8]
```

The `nums` array needs to be modified, but we don't have to obscure that side effect by containing it in `addMaxNum(..)`. Let's move the `push(..)` mutation out, so that `addMaxNum(..)` becomes a pure function, and the side effect is now more obvious:

O array `nums` precisa ser modificado, mas não temos que obscurecer esse efeito colateral, contendo-o em `addMaxNum(..)`. Vamos mover a mutação `push(..)` para fora, de modo que `addMaxNum(..)` se torne uma função pura e o efeito colateral seja agora mais óbvio:

```js
function addMaxNum(arr) {
    var maxNum = Math.max( ...arr );
    return maxNum + 1;
}

var nums = [4,2,7,3];

nums.push(
    addMaxNum( nums )
);

nums;       // [4,2,7,3,8]
```

**Note:** Another technique for this kind of task could be to use an immutable data structure, which we cover in the next chapter.

**Nota:** Outra técnica para esse tipo de tarefa poderia ser o uso de uma estrutura de dados imutável, que abordaremos no próximo capítulo.

But what can you do if you have an impure function where the refactoring is not as easy?

Mas o que você pode fazer se tiver uma função impura em que a refatoração não seja tão fácil?

You need to figure what kind of side causes/effects the function has. It may be that the side causes/effects come variously from lexical free variables, mutations-by-reference, or even `this` binding. We'll look at approaches that address each of these scenarios.

Você precisa descobrir que tipo de causas/efeitos colaterais a função tem. Pode ser que as causas/efeitos colaterais venham de variáveis léxicas livres, mutações por referência ou mesmo ligações `this`. Veremos as abordagens que tratam de cada um desses cenários.

### Containing Effects
### Contendo Efeitos

If the nature of the concerned side causes/effects is with lexical free variables, and you have the option to modify the surrounding code, you can encapsulate them using scope.

Se a natureza das causas/efeitos colaterais em questão for com variáveis lexicais livres e você tiver a opção de modificar o código ao redor, poderá encapsulá-los usando o escopo.

Recall:

Lembrar:

```js
var users = {};

function fetchUserData(userId) {
    ajax( `http://some.api/user/${userId}`, function onUserData(user){
        users[userId] = user;
    } );
}
```

One option for purifying this code is to create a wrapper around both the variable and the impure function. Essentially, the wrapper has to receive as input "the entire universe" of state it can operate on.

Uma opção para purificar esse código é criar um invólucro em torno da variável e da função impura. Essencialmente, o invólucro deve receber como entrada "todo o universo" de estado no qual pode operar.

```js
function safer_fetchUserData(userId,users) {
    // simple, naive ES6+ shallow object copy, could also
    // be done w/ various libs or frameworks
    users = Object.assign( {}, users );

    fetchUserData( userId );

    // return the copied state
    return users;


    // ***********************

    // original untouched impure function:
    function fetchUserData(userId) {
        ajax(
            `http://some.api/user/${userId}`,
            function onUserData(user){
                users[userId] = user;
            }
        );
    }
}
```

**Warning:** `safer_fetchUserData(..)` is *more* pure, but is not strictly pure in that it still relies on the I/O of making an Ajax call. There's no getting around the fact that an Ajax call is an impure side effect, so we'll just leave that detail unaddressed.

**Aviso:** `safer_fetchUserData(..)` é *mais* puro, mas não é estritamente puro porque ainda depende da E/S para fazer uma chamada Ajax. Não há como contornar o fato de que uma chamada Ajax é um efeito colateral impuro, portanto, deixaremos esse detalhe sem solução.

Both `userId` and `users` are input for the original `fetchUserData`, and `users` is also output. The `safer_fetchUserData(..)` takes both of these inputs, and returns `users`. To make sure we're not creating a side effect on the outside when `users` is mutated, we make a local copy of `users`.

Ambos `userId` e `users` são dados de entrada para o `fetchUserData` original e `users` também é gerado. O `safer_fetchUserData(..)` pega essas duas entradas e retorna `users`. Para ter certeza de que não estamos criando um efeito colateral externo quando `users` é mutado, fazemos uma cópia local de `users`.

This technique has limited usefulness mostly because if you cannot modify a function itself to be pure, you're not that likely to be able to modify its surrounding code either. However, it's helpful to explore it if possible, as it's the simplest of our fixes.

Essa técnica tem utilidade limitada principalmente porque, se você não pode modificar uma função para ser pura, provavelmente também não será capaz de modificar o código circundante. No entanto, é útil explorá-lo se possível, pois é a mais simples de nossas correções.

Regardless of whether this will be a practical technique for refactoring to pure functions, the more important take-away is that function purity only need be skin deep. That is, the **purity of a function is judged from the outside**, regardless of what goes on inside. As long as a function's usage behaves pure, it is pure. Inside a pure function, impure techniques can be used -- in moderation! -- for a variety of reasons, including most commonly, for performance. It's not necessarily, as they say, "turtles all the way down".

Independentemente de se essa será uma técnica prática para refatorar para funções puras, o mais importante é que a pureza da função precisa ser superficial. Ou seja, a **pureza de uma função é julgada de fora**, independentemente do que acontece dentro. Contando que o uso de uma função seja pura, ela é pura. Dentro de uma função pura, técnicas impuras podem ser usadas -- com moderação! -- por uma variedade de razões, incluindo mais comumente, para desempenho. Não é necessariamente, como se costuma dizer, "tartarugas até o fim".

Be very careful, though. Any part of the program that's impure, even if it's wrapped with and only ever used via a pure function, is a potential source of bugs and confusion for readers of the code. The overall goal is to reduce side effects wherever possible, not just hide them.

Porém, tenha muito cuidado. Qualquer parte do programa que seja impura, mesmo que esteja envolvida e apenas seja usada por meio de uma função pura, é uma fonte potencial de bugs e confusão para os leitores do código. O objetivo geral é reduzir os efeitos colaterais sempre que possível, não apenas ocultá-los.

### Covering Up Effects
### Efeitos de Encobrimento

Many times you will be unable to modify the code to encapsulate the lexical free variables inside the scope of a wrapper function. For example, the impure function may be in a third-party library file that you do not control, containing something like:

Muitas vezes você não conseguirá modificar o código para encapsular as variáveis lexicas livres dentro do escopo de uma função wrapper. Por exemplo, a função impura pode estar em um arquivo de bibliote de terceiros que você não controla, contendo algo como: 

```js
var nums = [];
var smallCount = 0;
var largeCount = 0;

function generateMoreRandoms(count) {
    for (let i = 0; i < count; i++) {
        let num = Math.random();

        if (num >= 0.5) {
            largeCount++;
        }
        else {
            smallCount++;
        }

        nums.push( num );
    }
}
```

The brute-force strategy to *quarantine* the side causes/effects when using this utility in the rest of our program is to create an interface function that performs the following steps:

A estratégia de força bruta para *colocar em quarentena* as causas/efeitos colaterais ao usar este utilitário no resto do nosso programa é criar uma função de interface que execute as seguintes etapas:

1. Capture the to-be-affected current states
2. Set initial input states
3. Run the impure function
4. Capture the side effect states
5. Restore the original states
6. Return the captured side effect states

1. Capture os estados atuais a serem afetados
2. Defina os estados de entrada iniciais
3. Execute a função impuras
4. Capture os estados de efeito colateral
5. Restaure os estados originais
6. Retorna os estados de efeito colateral capturados

```js
function safer_generateMoreRandoms(count,initial) {
    // (1) Save original state
    var orig = {
        nums,
        smallCount,
        largeCount
    };

    // (2) Set up initial pre-side effects state
    nums = [...initial.nums];
    smallCount = initial.smallCount;
    largeCount = initial.largeCount;

    // (3) Beware impurity!
    generateMoreRandoms( count );

    // (4) Capture side effect state
    var sides = {
        nums,
        smallCount,
        largeCount
    };

    // (5) Restore original state
    nums = orig.nums;
    smallCount = orig.smallCount;
    largeCount = orig.largeCount;

    // (6) Expose side effect state directly as output
    return sides;
}
```

And to use `safer_generateMoreRandoms(..)`:

```js
var initialStates = {
    nums: [0.3, 0.4, 0.5],
    smallCount: 2,
    largeCount: 1
};

safer_generateMoreRandoms( 5, initialStates );
// { nums: [0.3,0.4,0.5,0.8510024448959794,0.04206799238...

nums;           // []
smallCount;     // 0
largeCount;     // 0
```

That's a lot of manual work to avoid a few side causes/effects; it'd be a lot easier if we just didn't have them in the first place. But if we have no choice, this extra effort is well worth it to avoid surprises in our programs.

É muito trabalho manual para evitar algumas causas/efeitos colaterais, seria muito mais fácil se não os tivéssemos em primeiro lugar. Mas se não tivermos escolha, esse esforço extra vale a pena para evitar surpresas em nossos programas.

**Note:** This technique really only works when you're dealing with synchronous code. Asynchronous code can't reliably be managed with this approach because it can't prevent surprises if other parts of the program access/modify the state variables in the interim.

**Nota:** Essa técnica realmente só funciona quando você está lidando com código síncrono. O código assíncrono não pode ser gerenciado de forma confiável com essa abordagem porque não pode evitar surpresas se outras partes do programa acessarem/modificarem as variáveis de estado nesse ínterim.

### Evading Effects
### Efeitos de Evasão

When the nature of the side effect to be dealt with is a mutation of a direct input value (object, array, etc.) via reference, we can again create an interface function to interact with instead of the original impure function.

Quando a natureza do efeito colateral a ser tratado é uma mutação de um valor de entrada direto (objeto, array, etc.) via referência, podemos novamente criar uma função de interface para interagir em vez da função impura original.

Consider:

Considerar:

```js
function handleInactiveUsers(userList,dateCutoff) {
    for (let i = 0; i < userList.length; i++) {
        if (userList[i].lastLogin == null) {
            // remove the user from the list
            userList.splice( i, 1 );
            i--;
        }
        else if (userList[i].lastLogin < dateCutoff) {
            userList[i].inactive = true;
        }
    }
}
```

Both the `userList` array itself, plus the objects in it, are mutated. One strategy to protect against these side effects is to do a deep (well, just not shallow) copy first:

Tanto o array `userList` em si, mais os objetos nele, são mutados. Uma estratégia para se proteger contra esses efeitos colaterais é fazer uma cópia profunda (bem, não superficial) primeiro: 

```js
function safer_handleInactiveUsers(userList,dateCutoff) {
    // make a copy of both the list and its user objects
    let copiedUserList = userList.map( function mapper(user){
        // copy a `user` object
        return Object.assign( {}, user );
    } );

    // call the original function with the copy
    handleInactiveUsers( copiedUserList, dateCutoff );

    // expose the mutated list as a direct output
    return copiedUserList;
}
```

The success of this technique will be dependent on the thoroughness of the *copy* you make of the value. Using `[...userList]` would not work here, since that only creates a shallow copy of the `userList` array itself. Each element of the array is an object that needs to be copied, so we need to take extra care. Of course, if those objects have objects inside them (they might!), the copying needs to be even more robust.

O sucesso desta técnica dependerá do rigor da *cópia* que você fizer do valor. Usar `[...userList]` não funcionaria aqui, já que isso apenas cria uma cópia superficial do próprio array `userList`. Cada elemento da matriz é um ojeto que precisa ser copiado, portanto, precisamos tomar cuidado extra. Claro, se esses objetos tiverem objetos dentro deles (eles podem!), a cópia precisa ser ainda mais robusta.

### `this` Revisited
### `this` Revisitado

Another variation of the via-reference side cause/effect is with `this`-aware functions having `this` as an implicit input. See [Chapter 2, "What's This"](ch2.md/#whats-this) for more info on why the `this` keyword is problematic for FPers.

Outra variação da causa/efeito colateral via referência é com funções que reconhecem `this` tendo `this` como uma entrada implícita. Veja [Capítulo 2, "O que é isto"](ch2.md/#whats-this) para mais informações sobre porque a palavra-chave `this` é problemática para FPers.

Consider:

Considerar:

```js
var ids = {
    prefix: "_",
    generate() {
        return this.prefix + Math.random();
    }
};
```

Our strategy is similar to the previous section's discussion: create an interface function that forces the `generate()` function to use a predictable `this` context:

Nossa estratégia é semelhante à discussão da seção anterior: criar uma função de interface que force a função `generate()` a usar um contexto `this` previsível: 

```js
function safer_generate(context) {
    return ids.generate.call( context );
}

// *********************

safer_generate( { prefix: "foo" } );
// "foo0.8988802158307285"
```

These strategies are in no way fool-proof; the safest protection against side causes/effects is to not do them. But if you're trying to improve the readability and confidence level of your program, reducing the side causes/effects wherever possible is a huge step forward.

Essas estratégias não são à prova de erros, a proteção mais segura contra causas/efeitos colaterais é não praticá-los. Mas se você está tentando melhorar a legibilidade e o nível de confiança de seu programa, reduzir as causas/efeitos colaterais sempre que possível é um grande passo em frente.

Essentially, we're not really eliminating side causes/effects, but rather containing and limiting them, so that more of our code is verifiable and reliable. If we later run into program bugs, we know that the parts of our code still using side causes/effects are the most likely culprits.

Essencialmente, não estamos eliminando realmente as causas/efeito colaterais, mas sim os contendo e limitando, de forma que uma parte maior do nosso código seja verificável e confiável. Se mais tarde encontrarmos bugs de programa, saberemos que as partes do nosso código que ainda usam causas/efeitos colaterais são os prováveis culpados.

## Summary
## Resumo

Side effects are harmful to code readability and quality because they make your code much harder to understand. Side effects are also one of the most common *causes* of bugs in programs, because juggling them is hard. Idempotence is a strategy for restricting side effects by essentially creating one-time-only operations.

Os efeitos colaterais são prejudiciais à legibilidade e à qualidade do código, pois tornam o código muito mais difícil de entender. Os efeitos colaterais também são uma das *causas* mais comuns de bugs em programas, porque lidar com eles é difícil. Idempotência é uma estratégia para restringir os efeitos colaterais, essencialmente criando operações únicas.

Pure functions are how we best avoid side effects. A pure function is one that always returns the same output given the same input, and has no side causes or side effects. Referential transparency further states that -- more as a mental exercise than a literal action -- a pure function's call could be replaced with its output and the program would not have altered behavior.

Funções puras são a melhor maneira de evitar os efeitos colaterais. Uma função pura é aquela que sempre retorna a mesma saída com a mesma entrada e não tem causas ou efeitos colaterais. A transparência referencial afirma ainda que -- mais como um exercício mental do que uma ação literal -- a chamada de uma função pura poderia ser substituída por sua saída e o programa não teria o comportamento alterado. 

Refactoring an impure function to be pure is the preferred option. But if that's not possible, try encapsulating the side causes/effects, or creating a pure interface against them.

Refatorar uma função impura para ser pura é a melhor opção. Mas se isso não for possível, tente encapsular as causas/efeitos colaterais ou criar uma interface pura com eles.

No program can be entirely free of side effects. But prefer pure functions in as many places as that's practical. Collect impure functions side effects together as much as possible, so that it's easier to identify and audit these most likely culprits of bugs when they arise.

Nenhum programa pode ser totalmente isento de efeitos colaterais. Mas prefira funções puras sempre que possível. Reúna os efeitos colaterais das funções impuras, para que seja mais fácil identificar e auditar esses prováveis culpados dos bugs quando eles surgirem.
