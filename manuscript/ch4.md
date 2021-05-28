# Functional-Light JavaScript
# Capítulo 4: Funções de composição

Por agora, espero que você esteja se sentindo muito mais confortável com o que significa usar funções para programação funcional.

Um programador funcional vê cada função em seu programa como uma simples peça de Lego. Eles reconhecem o tijolo 2x2 azul de relance e sabem exatamente como funciona e o que podem fazer com ele. Quando eles começam a construir um modelo de Lego maior e mais complexo, conforme eles precisam da próxima peça, eles já têm um instinto de qual de suas muitas peças sobressalentes utilizar.

Mas às vezes você pega o tijolo 2x2 azul e o tijolo 4x1 cinza e os coloca juntos de uma certa maneira, e você percebe, "essa é uma peça útil que eu preciso frequentemente".

Portanto, agora você criou uma nova "peça", uma combinação de duas outras peças, e pode pegar esse tipo de peça sempre que precisar. É mais eficaz reconhecer e usar essa coisa composta de tijolo L cinza-azulado onde for necessário, do que pensar separadamente em montar os dois tijolos individuais a cada vez.

As funções vêm em uma variedade de formas e tamanhos. E podemos definir uma certa combinação deles para fazer uma nova função composta que será útil em varias partes do programa. Esse processo de usar funções juntas é chamado de composição.

Composição é como um programador funcional modela o fluxo de dados através do programa. Em alguns sentidos, é o conceito mais fundamental em toda a PF, porque sem ele, você não pode modelar dados e mudanças de estado declarativamente. Em outras palavras, tudo o mais na PF entraria em colapso sem composição.

## Saída para Entrada

Já vimos alguns exemplos de composição. Por exemplo, nossa discussão sobre [`unary(..)` no Capítulo3](ch3.md/#user-content-unary) incluiu esta expressão: [`[..].map(unary(parseInt))`](ch3.md/user-content-mapunary). Pense no que está acontecendo lá.

Para compor duas funções juntas, passe a saída da primeira chamada de função como a entrada da segunda chamada de função. Em `map(unary(parseInt))`, a chamada `unary(parseInt)` retorna um valor (uma função), esse valor é diretamente passado como um argumento para `map(..)`, que retorna um array.

Para dar um passo a trás e visualizar o fluxo conceitual de dados, considere:

```txt
arrayValue <-- map <-- unary <-- parseInt
```

`parseInt` é a entrada para `unary(..)`. A saída de `unary(..)` é a entrada para `map(..)`. A saída de `map(..)` é `arrayValue`. Esta é a composição de `map(..)` e `unary(..)`.

**Nota:** A orientação da direita para a esquerda aqui é proposital, embora possa parecer estranho neste ponto do seu aprendizado. Voltaremos para explicar isso detalhadamente mais tarde.

Pense neste fluxo de dados como uma correia transportadora em uma fábrica de doces, onde cada operação é uma etapa no processo de resfriamento, corte e embalagem de um pedaço de doce. Usaremos a metáfora da fábrica de doces ao longo deste capítulo para explicar o que é composição.

<p align="center">
    <img src="images/fig2.png">
</p>

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

`words(..)` divide uma string em um array de palavras. `unique(..)` pega uma lista de palavras e filtra para não ter palavras repetidas.

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

Nomeamos a saída do array de `words(..)` como `wordsFound`. A entrada de `unique(..)` é também um array, então podemos passar o `wordsFound` para ele.

De volta para à linha de montagem da fábrica de doces: a primeira linha da máquina leva como "entrada" o chocolate derretido, e sua "saída" é um pedaço de chocolate formado e resfriado. A próxima máquina um pouco abaixo na linha de montagem toma como "entrada" o pedaço de chocolate, e sua "saída" é um pedaço cortado de bombom de chocolate. Em seguida, uma máquina na linha pega pequenos pedaços de chocolate da esteira transportadora e produz os doces embalados prontos para embalar e enviar.

<img src="images/fig3.png" align="right" width="9%" hspace="20">

A fábrica de doces é muito bem sucedida nesse processo, mas como acontece com todos os negócios, a administração continua buscando maneiras de crescer.

Para acompanhar a demanda por mais produção de doces, eles decidem retirar a engenhoca da correia transportadora e apenas empilhar as três máquinas uma em cima da outra, de modo que a válvula de saída de uma seja conectada diretamente à válvula de entrada da que está abaixo dela. Não há mais espaço desperdiçado onde um pedaço de chocolate rola lenta e ruidosamente por uma esteira rolante da primeira máquina para a segunda.

Esta inovação economiza muito espaço no chão de fábrica, então a gerência fica feliz em fazer mais doces a cada dia!

O código equivalente a essa configuração de fábrica de doces aprimorada é pular a etapa intermediária (a variável `wordsFound` no snippet anterior) e apenas usar as duas chamadas de função juntas:

```js
var wordsUsed = unique( words( text ) );
```

**Nota:** Embora normalmente leiamos as chamadas de função da esquerda para a direita -- `unique(..)` e, em seguida, `words(..)` -- a ordem das operações será, na verdade, mais da direita para esquerda, ou interno para externo. `words(..)` será executado primeiro e, em seguida, `unique(..)`. Mais tarde, falaremos sobre um padrão que corresponde à ordem de execução de nossa leitura natural da esquerda para a direita, chamado `pipe(..)`.

As máquinas empilhadas estão funcionando bem, mas é meio desajeitado ter os fios espalhados por todo o lugar. Quanto mais pilhas de máquinas eles criam, mais desordenado fica o chão de fábrica. E o esforço para montar e manter todas essas pilhas de máquinas consome muito tempo.

<img src="images/fig4.png" align="left" width="15%" hspace="20">

Certa manhã, uma engenheira da fábrica de doces tem uma ótima ideia. Ela acha que seria muito mais eficiente se ela fizesse uma caixa externa para esconder todos os fios, por dentro, todas as três máquinas estão conectadas e, por fora, tudo ficaria limpo e organizado. No topo dessa nova máquina sofisticada está uma válvula para despejar chocolate derretido e, na parte inferior, uma válvula que cospe bombons de chocolate embrulhados. Brilhante!

Esta máquina de composto único é muito mais fácil de mover e instalar onde quer que a fábrica precise. Os trabalhadores do chão de fábrica estão ainda mais felizes porque não precisam mais se preocupar com botões e mostradores em três máquinas individuais, eles rapidamente preferem usar a máquina sofisticada.

Em relação ao código: agora percebemos que o emparelhamento de `words(..)` e `unique(..)` naquela ordem específica de execução (pense: Lego composto) é algo que poderíamos usar em várias outras partes da nossa aplicação. Então, vamos definir uma função composta que os combina:

```js
function uniqueWords(str) {
    return unique( words( str ) );
}
```

`uniqueWords(..)` pega uma string e retorna um array. É uma composição de duas funções: `unique(..)` e `words(..)`, ele cria este fluxo de dados:

```txt
wordsUsed <-- unique <-- words <-- text
```

Você provavelmente já deve ter percebido: a revolução que se desenrola no design da fábrica de doces é a composição de funções.

### Fabricação de Máquinas

A fábrica de doces está funcionando muito bem, e, graças a todo o espaço economizado, eles agora têm espaço de sobra para experimentar fazer novos tipos de doces. Com base no sucesso anterior, a administração está empenhada em continuar inventando novas máquinas sofisticadas de compostos para sua crescente variedade de doces.

Mas os engenheiros da fábrica lutam para acompanhar, porque cada vez que um novo tipo de máquina composta precisa ser feita, eles passam um bom tempo fazendo a nova caixa externa e encaixando as máquinas individuais nela.

Assim, os engenheiros da fábrica entram em contato com um fornecedor de máquina industrial para obter ajuda. Eles ficam surpresos ao descobrir que este fornecedor oferece uma máquina de **fabricação de máquinas**! Por incrível que pareça, eles compram uma máquina que pode pegar algumas máquinas menores da fábrica -- a de resfriamento de chocolate e a de corte, por exemplo -- e conectá-las automaticamente, até mesmo envolvendo uma caixa maior e mais limpa em torno delas. Isso certamente fará a fábrica de doces realmente decolar!

<p align="center">
    <img src="images/fig5.png" width="50%">
</p>

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

Você notou que definimos a ordem dos parâmetros como `fn2,fn1`, e, além disso, que é a segunda função listada (também conhecida pelo nome do parâmetro `fn1`) que executa primeiro, depois a primeira função listada (`fn2`)? Em outras palavras, as funções são compostas da direita para esquerda.

Pode parecer uma escolha estranha, mas existem algumas razões para isso. A maioria das bibliotecas típicas da PF definem seu `compose(..)` para trabalhar da direita para a esquerda em termos de ordenação, então estamos mantendo essa convenção.

Mas por que? Acho que a explicação mais fácil (mas talvez não a mais precisa historicamente) é que estamos listando para corresponder à ordem em que são escritos manualmente no código, ou melhor, a ordem em que os encontramos ao ler da esquerda para a direita.

`unique(words(str))` lista as funções na ordem da esquerda para a direita `unique, words`, então fazemos nosso utilitário `compose2(..)` aceitá-las nessa ordem também. A ordem de execução é da direita para a esquerda, mas a ordem do código é da esquerda para a direita. Preste muita atenção para mantê-los distintos em sua mente.

Agora, a definição mais eficiente da máquina de fazer doces é:

```js
var uniqueWords = compose2( unique, words );
```

### Variação de Composição

Pode parecer que a combinação `<-- unique <-- words` é a única ordem em que essas duas funções podem ser compostas. Mas poderíamos realmente compồ-los na ordem oposta para criar um utilitário com um propósito um pouco diferente:

```js
var letters = compose2( words, unique );

var chars = letters( "How are you Henry?" );
chars;
// ["h","o","w","a","r","e","y","u","n"]
```

Isso funciona porque o utilitário `words(..)`, para fins de segurança do tipo de valor, primeiro coage sua entrada para uma string usando `String(..)`. Assim, a matriz que retorna `unique(..)` -- agora a entrada para `words(..)` -- torna-se a string `"H,o,w, ,a,r,e,y,u,n,?"` e então o resto do comportamento em `words(..)` processa essa string no array `chars`.

Reconhecidamente, este é um exemplo artificial. Mas a questão é que as composições de funções nem sempre são unidirecionais. Às vezes, colocamos o tijolo cinza em cima do tijolo azul, e outras vezes, colocamos o tijolo azul por cima.

A fábrica de doces deve ter cuidado se tentar colocar os doces embrulhados na máquina que mistura e esfria o chocolate!

## Composição Geral

Se pudermos definir a composição de duas funções, podemos simplesmente continuar a suportar a composição de qualquer número de funções. O fluxo geral de visualização de dados para qualquer número de funções sendo compostas, é semelhante a este:

```txt
finalValue <-- func1 <-- func2 <-- ... <-- funcN <-- origValue
```

<p align="center">
    <img src="images/fig6.png" width="50%">
</p>

Agora, a fábrica de doces possui a melhor máquina de todas: uma máquina que pode pegar qualquer número de máquinas menores separadas e cuspir uma grande máquina extravagante que executa cada passo na ordem. Essa é uma incrível operação de doces! É o sonho de Willy Wonka!

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

**Aviso:** `fns` é um array coletado de argumentos, não um array passado e, como tal, é local para `compose(..)`. Pode ser tentador pensar que `[...fns]` seria desnecessário. No entanto, nesta implementação particular, `.pop()` dentro da função interna `composed(..)` está alterando a lista, portanto, se não fizéssemos uma cópia a cada vez, a função composta retornada só poderia ser usada de forma confiável uma vez. Revisitaremos esse perigo no [Capítulo 6](ch6.md/#user-content-hiddenmutation).

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

Vamos definir `biggerWords(..)` que inclui `skipShortWords(..)`. O equivalente de composição manual é `skipShortWords( unique( words( text ) ) )`, então vamos fazer isso com `compose(..)`:

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

Para fazer algo mais interessante com composição, vamos usar [`partialRight(..)`, que vimos primeiro no Capítulo 3](ch3.md/#user-content-partialright). Podemos construir uma aplicação parcial à direita do próprio `compose(..)`, pré-especificando o segundo e o terceiro argumento (`unique(..)` e `words(..)`), respectivamente), vamos chamá-lo de `filterWords(..)`.

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

Reserve um momento para considerar o que a aplicação parcial à direita em `compose(..)` nos oferece. Ele nos permite especificar com antecedência a(s) primeira(s) etapa(s) de uma composição e, em seguida, criar variações especializadas dessa composição com diferentes etapas subsequentes (`biggerWords(..)` e `shorterWords(..)`). Este é um dos truques mais poderosos da PF!

Você também pode `curry(..)` uma composição em vez de uma aplicação parcial, embora por causa da ordem da direita para a esquerda, você pode querer mais frequentemente `curry( reverseArgs(compose), ..)` em vez de apenas `curry( compose, ..)` em si.

**Nota:** Porque `curry(..)` (pelo menos [á forma como implementamos no Capítulo 3](ch3.md/#user-content-curry)) depende da detecção de aridade (`comprimento`) ou tê-lo especificado manualmente e `compose(..)` é uma função variável, você precisará especificar manualmente a aridade pretendida como `curry(.., 3)`.

### Implementações Alternativas

Embora você possa muito bem nunca implementar seu próprio `compose(..)` para usar na produção, e sim apenas usar a implementação de uma biblioteca conforme fornecida, descobri que entender como funciona nos bastidores realmente ajuda a solidificar os conceitos gerais de PF muito bem.

Então, vamos examinar algumas opções de implementação diferentes para `compose(..)`. Também veremos que há alguns prós/contras em cada implementação, especialmente o desempenho.

Veremos o utilitário [`reduce(..)` em detalhes no Capítulo 9](ch9.md/#reduce), mas por agora, apenas saiba que ele reduz uma lista (array) a um único valor finito. É como um loop extravagante.

Por exemplo, se você fez uma adição-redução em uma lista de números (como `[1,2,3,4,5,6]`), você faria um loop sobre eles adicionando-os conforme avança. A redução adicionaria `1` a `2` e adicionaria esse resultado a `3` e, em seguida, adicionaria esse resultado a `4` e assim por diante, resultando no somatório final: `21`.

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

**Nota:** Esta implementação de `compose(..)` usa `[...fns].reverse().reduce(..)` para reduzir da direita para a esquerda. Iremos [revisitar `compose(..)` no Capítulo 9](ch9.md/#user-content-composereduceright), em vez de usar `reduceRight(..)` para esse propósito.

Observe que o loop `reduce(..)` acontece cada vez que a função `composed(..)` final é executada, e que cada `result(..)` intermediário é passado para a próxima iteração como a entrada para a próxima chamada.

A vantagem desta implementação é que o código é mais conciso e também usa uma construção da PF bem conhecida: `reduce(..)`. E o desempenho desta implementação também é semelhante ao da versão original do loop `for`.

No entanto, essa implementação é limitada porque a função composta externa (também conhecida como a primeira função na composição) pode receber apenas um único argumento. A maioria das outras implementações passa todos os argumentos para essa primeira chamada. Se todas as funções de composição fossem unárias, isso não seria grande coisa. Mas se você precisar passar vários argumentos para essa primeira chamada, vocẽ precisará de uma implementação diferente.

To fix that first call single-argument limitation, we can still use `reduce(..)` but produce a lazy-evaluation function wrapping:

Para corrigir a limitação de argumento único da primeira chamada, ainda podemos usar `reduce(..)`, mas produzirá um empacotamento lento da função:

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

Observe que retornamos o resultado da chamada `reduce(..)` diretamente, que é em si uma função, não um resultado calculado. *Essa* função nos permite passar quantos argumentos quisermos, passando-os todos ao longo da linha até a primeira chamada de função na composição e, em seguida, aumentando cada resultado em cada chamada subsequente.

Em vez de calcular o resultado da execução e passá-lo à medida que o loop `reduce(..)` prossegue, esta implementação executa o loop `reduce(..)` **uma vez** antes do tempo de composição e adia todas as funções cálculos de chamadas -- conhecidos como cálculos preguiçosos. Cada resultado parcial da redução é uma função sucessivamente mais encapsulada.

Quando você chama a função composta final e fornece um ou mais argumentos, todos os níveis da grande função aninhada, da chamada mais interna à externa, são executados em sucessão reversa (não por meio de um loop).

As características de desempenho serão potencialmente diferentes das da implementação anterior baseada em `reduce(..)`. Aqui, `reduce(..)` executa apenas uma vez para produzir uma grande função composta, e então, esta chamada de função composta simplesmente executa doas as suas funções aninhadas a cada chamada. Na versão anterior, `reduce(..)` seria executado para todas as chamadas.

Sua milhagem pode variar em qual implementação é melhor, mas lembre-se de que esta última implementação não é limitada na contagem de argumentos como a anterior.

Também poderíamos definir `compose(..)` usando recursão. A definição recursiva para `compose(fn1, fn2, .. fnN)` seria semelhante a:

```txt
compose( compose(fn1,fn2, .. fnN-1), fnN );
```

**Nota:** Cobriremos a recursão mais completamente no [Capítulo 8](ch8.md), portanto, se essa abordagem parecer confusa, não se preocupe por enquanto. Ou vá ler esse capítulo e depois volte e releia esta nota. :)

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

Acho que o benefício de uma implementação recursiva é principalmente conceitual. Pessoalmente, acho muito mais fácil pensar em uma ação repetitiva em termos recursivos em vez de em um loop em que tenho que rastrear o resultado da execução, então prefiro que o código o expresse dessa forma.

Outros acharão a abordagem recursiva um pouco mais difícil de manipular mentalmente. Convido você a fazer suas próprias avaliações.

## Composição Reordenada

We talked earlier about the right-to-left ordering of standard `compose(..)` implementations. The advantage is in listing the arguments (functions) in the same order they'd appear if doing the composition manually.

Falamos anteriormente sobre a ordem da direita para a esquerda das implementações padrão de `compose(..)`. A vantagem é listar os argumentos (funções) na mesma ordem em que apareceriam se a composição fosse feita manualmente.

A desvantagem é que eles estão listados na ordem inversa em que são executados, o que pode ser confuso. Também era mais difícil ter que usar `partialRight(compose, ..)` para pré-especificar as *primeiras* funções a serem executadas na composição.

A ordem inversa, composta da esquerda para a direita, tem um nome comum: `pipe(..)`. Diz-se que esse nome vem da terra do Unix/Linux, onde vários programas são agrupados por "pipe"ing (operador `|`) a saída do primeiro como entrada do segundo, e assim por diante (ou seja, `ls -la | grep "foo" | less`).

`pipe(..)` é idêntico a `compose(..)` exceto que processa através da lista de funções na ordem da esquerda para a direita:

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

Na verdade, poderíamos apenas definir `pipe(..)` como a inversão de argumentos de `compose(..)`:

```js
var pipe = reverseArgs( compose );
```

Essa foi fácil!

Lembre-se deste exemplo da composição geral anterior:

```js
var biggerWords = compose( skipShortWords, unique, words );
```

Para expressar isso com `pipe(..)` apenas invertemos a ordem em que os listamos:

```js
var biggerWords = pipe( words, unique, skipShortWords );
```

A vantagem do `pipe(..)` é que ele lista as funções em ordem de execução, o que às vezes pode diminuir a confusão do leitor. Pode ser mais simples ler o código: `pipe( words, unique, skipShortWords )`, e reconhecer que está executando `words(..)` primeiro, depois `unique(..)`, e finalmente `skipShortWords(..)`.

`pipe(..)` também é útil se você estiver em uma situação em que deseja aplicar parcialmente as *primeiras* funções executadas. Anteriormente, fizemos isso com a aplicação parcial direita de `compose(..)`.

Comparar:

```js
var filterWords = partialRight( compose, unique, words );

// vs

var filterWords = partial( pipe, words, unique );
```

Como você deve se lembrar da nossa primeira implementação de [`partialRight(..)` no Capítulo 3](ch3.md/#user-content-partialright), ele usa `reverseArgs(..)` nos bastidores, assim como nosso `pipe(..)` agora faz. Portanto, obtemos o mesmo resultado de qualquer maneira.

*Neste caso específico*, a pequena vantagem em desempenho de usar `pipe(..)` é, porque não estamos tentando preservar a ordem dos argumentos da direita para a esquerda de `compose(..)`, nós não precisamos reverter a ordem do argumento de volta, como fazemos dentro de `partialRight(..)`. Portanto, `partial(pipe, ..)` é um pouco mais eficiente aqui do que `partialRight(compose, ..)`.

## Abstração

A abstração influência muito nosso raciocínio sobre composição, portanto, vamos examiná-la com mais detalhes. 

Semelhante a como a aplicação parcial e currying (ver [Capítulo 3](ch3.md/#some-now-some-later) permitem uma progressão de funções generalizadas para especializadas, podemos abstrair extraindo a generalidade entre duas ou mais tarefas. A parte geral é definida uma vez, para evitar repetições. Para realizar a especialização de cada tarefa, a parte geral é parametrizada.

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

Ambos os utilitários estão armazenando um valor em uma fonte de dados. Essa é a generalidade. A especialidade é que um deles coloca o valor no final de uma matriz, enquanto o outro define o valor em um nome de propriedade de um objeto.

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

A tarefa geral de referenciar uma propriedade em um objeto (ou array, graças à sobrecarga de operador conveniente do JS de `[ ]`) e definir se seu valor é abstraído em sua própria função `storeData(..)`. Embora este utilitário tenha apenas uma única linha no momento, pode-se imaginar outro comportamento geral que era comum em ambas as tarefas, como gerar um ID numérico exclusivo ou armazenar um carimbo de data/hora com o valor.

Se repetirmos o comportamento comum geral em vários lugares, corremos o risco de ter que dar manutenção e alterar algumas instâncias, porém nos esquecendo de alterar outras. Há um princípio em jogo neste tipo de abstração, frequentemente referido como "não se repita" (DRY).

O DRY se esforça para ter apenas uma definição em um programa para qualquer tarefa. Um aforismo alternativo para motivar a codificação DRY é que os programadores geralmente são preguiçosos e não querem fazer trabalho desnecessário.

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

Em um esforço para ser DRY e evitar repetir uma instrução `if`, movemos o condicional para a abstração geral. Também assumimos que *podemos* ter verificações de strings não vazias ou valores `não definidos` em outro lugar no programa no futuro, então também podemos usar DRY neles também.

Este código *é* mais DRY, mas em um extensão exagerada. Os programadores devem ter o cuidado de aplicar os níveis apropriados de abstração a cada parte de seu programa, nem mais, nem menos.

Em relação à nossa maior discussão sobre composição de funções neste capítulo, pode parecer que seu benefício é esse tipo de abstração DRY. Mas não vamos pular para essa conclusão, porque acho que a composição realmente serve a um propósito mais importante em nosso código.

Além disso, **composição é útil mesmo se houver apenas uma ocorrência de algo** (sem repetição para aplicar o DRY).

### Separação Ativa O Foco

Além de generalização vs. especialização, acho que há outra definição mais útil para abstração, conforme revelado por esta citação:

> ... abstração é um processo pelo qual o programador associa um nome a um fragmento de programa potencialmente complicado, que pode então ser pensado em termos de seu propósito de função, ao invés de em termos de como essa função é alcançada. Ao ocultar os detalhes irrelevantes, a abstração reduz a complexidade conceitual, tornando possível para o programador se concentrar em um subconjunto gerenciável do texto do programa a qualquer momento específico.
>
> Michael L. Scott, Programming Language Pragmatics<a href="#user-content-footnote-1"><sup>1</sup></a>

O ponto que esta citação mostra é que a abstração -- geralmente, está puxando algum pedaço de código em sua própria função -- serve ao propósito principal de separar duas partes de funcionalidade para que seja possível focar em cada parte independentemente da outra.

Observe que a abstração, neste sentido não tem a intenção de *ocultar* detalhes, como se tratasse as coisas como caixas pretas que *nunca* examinamos.

Nesta citação, "irrelevante", em termos do que está oculto, não deve ser pensado como um julgamento qualitativo absoluto, mas sim relativo ao que você deseja focar em um determinado momento. Em outras palavras, quando separamos X de Y, se eu quiser me concentrar em X, Y é irrelevante naquele momento. Em outro momento, se eu quiser me concentrar em Y, X é irrelevante naquele momento.

**Não estamos abstraindo para esconder detalhes, estamos separando os detalhes para melhorar o foco.**

Lembre-se de que, no início deste livro, afirmei que o objetivo da PF é criar um código mais legível e compreensível. Uma maneira eficaz de fazer isso é desmaranhar código complexo (leia-se: firmemente trançado, como em fios de corda) em pedaços de código separados e mais simples (leia-se: fracamente ligados). Dessa forma, o leitor não se distrai com os detalhes de uma parte enquanto procura os detalhes da outra.

Nosso objetivo maior não é implementar algo, apenas uma vez, como acontece com a mentalidade DRY. Na verdade, às vezes nos repetimos no código.

Como [afirmamos no Capítulo 3](ch3.md/#why-currying-and-partial-application), o objetivo principal da abstração é implementar coisas separadas, separadamente. Estamos tentando melhorar o foco, porque isso melhora a legibilidade.

Ao separar duas ideias, inserimos uma fronteira semântica entre elas, o que nos dá a capacidade de focar em cada lado independente do outro. Em muitos casos, esse limite semântico é algo como o nome de uma função. A implementação da função está focada em *como* computar algo, e a chamada usando essa função por nome está focado em *o que* fazer com sua saída. Nós abstraímos o *como* do *o quê* para que sejam separados e separadamente razoáveis.

Outra maneira de descrever esse objetivo é com o estilo de programação imperativo vs. declarativo. O código imperativo se preocupa principalmente em declarar explicitamente *como* realizar uma tarefa. O código declarativo declara *o que* o resultado deve ser e deixa a implementação para alguma outra responsabilidade.

O código declarativo abstrai *o quê* de *como*. Normalmente, a codificação declarativa é favorecida na legibilidade sobre a imperativa, embora nenhum programa (exceto, é claro, os códigos de máquina 1s e 0s) seja inteiramente um ou outro. O programador deve buscar o equilíbrio entre eles.

ES6 adicionou muitas possibilidades sintáticas que transformam antigas operações imperativas em novas formas declarativas. Talvez uma das mais claras seja a desestruturação. A desestruturação é um padrão de atribuição que descreve como um valor composto (objeto, array) é desmontado em seus valores constituíntes. 

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

O *o que* está atribuindo o primeiro valor do array a `a` e o quarto valor a `b`. O *como* está obtendo uma referência ao array (`tmp`) e referenciando manualmente os índices `0` e `3` em atribuições a `a` e `b`, respectivamente.

A desestruturação do array *esconde* a atribuição? Depende da sua perspectiva. Estou afirmando que simplesmente separa o *o quê* do *como*. O mecanismo JS ainda faz as atribuições, mas evita que você se distraia com *como* isso é feito.

Em vez disso, você lê `[a ,,, b] = ..` e pode ver o padrão de atribuição meramente dizendo a você *o que* vai acontecer. A desestruturação de array é um exemplo de abstração declarativa.

### Composição como Abstração

O que tudo isso tem a ver com composição de funções? A composição da função também é uma abstração declarativa. 

Lembre-se do exemplo `shorterWords(..)` anterior. Vamos comparar uma definição imperativa e declarativa para ele:

```js
// imperative
function shorterWords(text) {
    return skipLongWords( unique( words( text ) ) );
}

// declarative
var shorterWords = compose( skipLongWords, unique, words );
```

A forma declarativa se concentra em *o quê* -- essas três funções canalizam dados de uma string para uma lista de palavras mais curtas -- e deixa o *como* para os detalhes internos de `compose(..)`.

Em um sentido mais amplo, a linha `shorterWords = compose(..)` explica *como* definir um utilitário `shorterWords(..)`, deixando esta linha declarativa em algum outro lugar do código para focar apenas no *o quê*:

```js
shorterWords( text );
```

A composição abstrai obtendo uma lista de palavras mais curtas das etapas necessárias para fazer isso.

Em contraste, e se  não tivéssemos usado a abstração da composição?

```js
var wordsFound = words( text );
var uniqueWordsFound = unique( wordsFound );
skipLongWords( uniqueWordsFound );
```

Ou então:

```js
skipLongWords( unique( words( text ) ) );
```

Qualquer uma dessas duas versões demonstra um estilo mais imperativo em oposição ao estilo declarativo anterior. O foco do leitor nesses dois fragmentos está inextricavelmente ligado ao *como* e menos ao *o quê*.

A composição de funções não é apenas salvar o código com o DRY. Mesmo que o uso de `shorterWords(..)` só ocorra em um lugar -- portanto, não há repetição a ser evitada! -- separar o *como* do *o quê* ainda melhora nosso código.

A composição é uma ferramenta poderosa para abstração que transforma o código imperativo em um código declarativo mais legível.

## Revisitando Pontos

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

Os "pontos" que gostaríamos de remover são as referências dos parâmetros `ordem` e `pessoa`.

Vamos começar tentando obter o "ponto" `person` da função `personFound(..)`. Para fazer isso, vamos primeiro definir:

```js
function extractName(person) {
    return person.name;
}
```

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

Enquanto estamos lidando com propriedades de objeto, vamos também definir o utilitário oposto: `setProp(..)` para definir um valor de propriedade em um objeto.

No entanto, queremos ter cuidado para não apenas transformar um objeto existente, mas sim criar um clone do objeto para fazer a alteração e, em seguida, retorná-lo. As razões para tal cuidado serão discutidas detalhadamente no [Capítulo 5](ch5.md).

<a name="setprop"></a>

```js
function setProp(name,obj,val) {
    var o = Object.assign( {}, obj );
    o[name] = val;
    return o;
}
```

Agora, para definir um `extractName(..)` que extrai uma propriedade `"name"` de um objeto, aplicaremos parcialmente `prop(..)`:

```js
var extractName = partial( prop, "name" );
```

**Nota:** Não perca que `extractName(..)` aqui ainda não extraiu nada. Aplicamos parcialmente `prop(..)` para fazer uma função que está esperando para extrair a propriedade `"name"` de qualquer objeto que passamos para ela. Também poderíamos ter feito isso com `curry(prop("name"))`.

A seguir, vamos restringir o foco nas chamadas de pesquisa aninhadas de nosso exemplo para o seguinte:

```js
getLastOrder( function orderFound(order){
    getPerson( { id: order.personId }, outputPersonName );
} );
```

Como podemos definir `outputPersonName(..)`? Para visualizar o que precisamos, pense no fluxo de dados desejado:

```txt
output <-- extractName <-- person
```

`outputPersonName(..)` precisa ser uma função que recebe um valor (objeto), passa para `extractName(..)` e, em seguida, passa esse valor para `output(..)`.

Esperançosamente, você reconheceu isso como uma operação `compose(..)`. Portanto, podemos definir `outputPersonName(..)` como:

```js
var outputPersonName = compose( output, extractName );
```

A função `outputPersonName(..)` que acabamos de criar é o retorno de chamada fornecido para `getPerson(..)`. Portanto, podemos definir uma função chamada `processPerson(..)` que pré-define o argumento de retorno de chamada, usando `partialRight(..)`:

```js
var processPerson = partialRight( getPerson, outputPersonName );
```

Vamos reconstruir o exemplo de pesquisas aninhadas novamente com nossa nova função:

```js
getLastOrder( function orderFound(order){
    processPerson( { id: order.personId } );
} );
```

Ufa, estamos fazendo um bom progresso!

Mas prcisamos continuar e remover o "ponto" de `order`. O próximo passo é observar que `personId` pode ser extraído de um objeto (como `order`) via `prop(..)`, assim como fizemos com `name` no objeto `person`:

```js
var extractPersonId = partial( prop, "personId" );
```

Para construir o objeto (da forma `{ id: .. }`) que precisa ser passado para `processPerson(..)`, vamos fazer outro utilitário para envolver um valor de um objeto em um nome de propriedade especificado, chamado `makeObjProp(..)`:

```js
function makeObjProp(name,value) {
    return setProp( name, {}, value );
}

// or the ES6 => form
var makeObjProp =
    (name,value) =>
        setProp( name, {}, value );
```

**Dica:** Este utilitário é conhecido como `objOf(..)` na biblioteca Ramda.

Assim como fizemos com `prop(..)` para fazer `extractName(..)`, aplicaremos parcialmente `makeObjProp(..)` para construir uma função `personData(..)` que cria nosso objeto de dados:

```js
var personData = partial( makeObjProp, "id" );
```

Para usar `processPerson(..)` para realizar a pesquisa de uma pessoa anexada a um valor de `order`, o fluxo conceitual de dados por meio de operações que precisamos é:

```txt
processPerson <-- personData <-- extractPersonId <-- order
```

Portanto, usaremos apenas `compose(..)` novamente para definir um utilitário `lookupPerson(..)`:

```js
var lookupPerson =
    compose( processPerson, personData, extractPersonId );
```

E... é isso! Colocando o exemplo inteiro de volta junto sem quaisquer "pontos";

```js
var getPerson = partial( ajax, "http://some.api/person" );
var getLastOrder =
    partial( ajax, "http://some.api/order", { id: -1 } );

var extractName = partial( prop, "name" );
var outputPersonName = compose( output, extractName );
var processPerson = partialRight( getPerson, outputPersonName );
var personData = partial( makeObjProp, "id" );
var extractPersonId = partial( prop, "personId" );
var lookupPerson = compose( processPerson, personData, extractPersonId );

getLastOrder( lookupPerson );
```

Uauu. Livre de pontos. E `compose(..)` acabou sendo muito útil em dois lugares!

Acho que, neste caso, embora as etapas para derivar nossa resposta final tenham sido um pouco demoradas, o resultado final é um código muito mais legível, porque acabamos chamando explicitamente cada etapa.

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

Este trecho com certeza é menos verboso, mas acho que é menos legível do que o trecho anterior, onde cada operação é sua própria variável. De qualquer forma, a composição nos ajudou com nosso estilo sem pontos.

## Resumo

Composição de função é um padrão para definir uma função que aponta a saída de uma chamada de função para outra chamada de função e sua saída para outra e assim por diante.

Como as funções JS só podem retornar valores únicos, o padrão essencialmente determina que todas as funções na composição (exceto talvez a primeira chamada) precisam ser unárias, recebendo apenas uma única entrada da saída da função anterior.

Em vez de listar cada etapa como uma chamada discreta em nosso código, a composição da função usando um utilitário como `compose(..)` ou `pipe(..)` abstrai esse detalhe de implementação para que o código seja mais legível, permitindo-nos focar sobre *o que* a composição será usada para realizar e não *como* ela será executada.

Composição é um fluxo de dados declarativo, o que significa que nosso código descreve o fluxo de dados de maneira explícita, óbvia e legível.

De muitas maneiras, o padrão de composição é a base mais importante, em grande parte porque é a única maneira de encaminhar os dados por meio de nossos programas, sem usar efeitos colaterais. O próximo capítulo explora por que isso deve ser evitado sempre que possível.

----

<a name="footnote-1"><sup>1</sup></a>Scott, Michael L. "Capítulo 3: Nomes, Escopos, e Vinculações.” Programming Language Pragmatics, 4th ed., Morgan Kaufmann, 2015, pp. 115.

