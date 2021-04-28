# Functional-Light JavaScript

# Capítulo 3: Gerenciando entradas de função

O [Capítulo 2](ch2.md) explorou a natureza central das `funções` JS e criou as bases sobre o que torna uma `função` em uma *função* da PF. Mas para alavancar todo o poder da PF, também precisamos de padrões e práticas para manipular funções para mudar e ajustar suas interações, para movê-las à nossa vontade.

Especificamente, nossa atenção neste capítulo estará nas entradas de parâmetros das funções. Ao reunir funções de todas as formas diferentes em seus programas, você rapidamente enfrentará incompatibilidades no número/ordem/tipo de entradas, bem como a necessidade de especificar algumas entradas em momentos diferentes de outras.

Na verdade, para fins estilísticos de legibilidade, às vezes você vai querer definir funções de uma forma que esconda totalmente suas entradas!

Esses tipos de técnicas são absolutamente essenciais para tornar as funções realmente *funcionais*.

## Todos por um

Imagine que você está passando uma função para um utilitário, onde o utilitário enviará vários argumentos para essa função. Mas você pode querer que a função receba apenas um único argumento.

Podemos desenvolver um auxiliar simples que envolve uma chamada de função para garantir que apenas um argumento seja passado. Já que isso está efetivamente garantindo que uma função seja tratada como unária, vamos chamá-la assim:

<a name="unary"></a>

```js
function unary(fn) {
    return function onlyOneArg(arg){
        return fn( arg );
    };
}
```

Muitos programadores funcionais tendem a preferir a sintaxe de função de seta mais curta `=>` para esse código (consulte no [Capítulo 2, "Funções sem `função`"](ch2.md/#functions-without-function)), como: 

```js
var unary =
    fn =>
        arg =>
            fn( arg );
```

**Nota:** Com certeza isso é mais conciso e espaçado. Mas eu pessoalmente sinto que tudo o que pode ganhar em simetria com a notação matemática, perde mais em legibilidade geral com as funções sendo todas anônimas, obscurecendo os limites do escopo e tornando o closure de decifração um pouco mais enigmático. 

Um exemplo comumente citado para usar `unário(..)` é com o utilitário `map(..)` (consulte no [Capítulo 9, "Map"](ch9.md/#map)) e `parseInt(..)`. `map(..)` chama uma função que mapeia para cada item em uma lista e cada vez que ele invoca a função map, ele passa três argumentos: `valor`, `idx` e `arr`.

Isso geralmente não é um bom negócio, a menos que você esteja tentando usar algo como uma função de mapeamento que se comportará incorretamente se receber muitos argumentos. Considere:

```js
["1","2","3"].map( parseInt );
// [1,NaN,NaN]
```

Para a assinatura `parseIn(str, radix)`, é claro que quando `map(..)` passa `index` na posição do segundo argumento, ele é interpretado por `parseInt(..)` como o `radix`, que nós não queremos.

`unary(..)` cria uma função que irá ignorar todos, exceto o primeiro argumento passado a ele, significando que o `índice` passado nunca é recebido por `parseInt(..)` e confundido com o `radix`:

<a name="mapunary"></a>

```js
["1","2","3"].map( unary( parseInt ) );
// [1,2,3]
```

### Um a um

Falando em funções com apenas um argumento, outro utilitário básico comum no conjunto de ferramentas da PF é uma função que recebe um argumento e não faz nada além de retornar o valor não alterado:

```js
function identity(v) {
    return v;
}

// ou no ES6 => forma de seta
var identity =
    v =>
        v;
```

Este utilitário parece tão simples que dificilmente será útil. Mas mesmo funções simples podem ser úteis no mundo da PF. Como se costuma dizer na atuação: não há pequenos papéis, apenas pequenos atores.

Por exemplo, imagine que você gostaria de dividir uma string usando uma expressão regular, mas a matriz resultante pode conter alguns valores vazios. Para descartá-los, podemos usar a operação de array `filter(..)` do JS (Veja no [Capítulo 9, "Filter"](ch9.md/#filter)) com `identity(..)``como predicado:

```js
var words = "Now is the time for all...".split( /\s|\b/ );
words;
// ["","Now","is","the","time","for","all","...",""]

words.filter(identity);
// ["Now","is","the","time","for","all","..."]
```

Como a `identity(..)` simplesmente retorna o valor passado a ela, o JS força cada valor em `verdadeiro` ou `falso` e isso determina se deve manter ou excluir cada valor na matriz final.

**Dica:** Outra função unária que pode ser usada como o predicado no exemplo anterior é a função `Boolean(..)`, integrada do JS, que coage explicitamente um valor para `verdadeiro` ou `falso`.

Outro exemplo de uso de `identity(..)` é com uma função padrão no lugar de uma transformação:

```js
function output(msg,formatFn = identity) {
    msg = formatFn( msg );
    console.log( msg );
}

function upper(txt) {
    return txt.toUpperCase();
}

output( "Hello World", upper );     // HELLO WORLD
output( "Hello World" );            // Hello World
```

Você também pode ver `identity(..)` usado como uma função de transformação padrão para as chamadas `map(..)` ou como valor inicial em um `reduce(..)` de uma lista de funções, ambos os utilitários serão abordados no [Capítulo 9](ch9.md).

### Inalterável

Certas APIs não permitem que você passe um valor diretamente para um método, mas exigem que você passe uma função, mesmo que essa função literalmente apenas retorne o valor. Uma dessas APIs é o método `then(..)` nas Promises do JS.

```js
// doesn't work:
p1.then( foo ).then( p2 ).then( bar );

// instead:
p1.then( foo ).then( function(){ return p2; } ).then( bar );
```

Muitos afirmam que as funções de seta ES6 `=>` são a melhor "solução":

```js
p1.then( foo ).then( () => p2 ).then( bar );
```

Mas existe um utilitário da PF que é mais adequado para a tarefa:

```js
function constant(v) {
    return function value(){
        return v;
    };
}

// or the ES6 => form
var constant =
    v =>
        () =>
            v;
```

Com este pequeno utilitário organizado da PF, podemos resolver nosso incômodo `then(..)` corretamente:

```js
p1.then( foo ).then( constant( p2 ) ).then( bar );
```

**Aviso:** Embora a versão da função de seta `() => p2` seja menor do que a `constant(p2)`, eu encorajo você a resistir à tentação de usá-la. A função de seta está retornando um valor externo a si mesma, o que é um pouco pior do ponto de vista da PF. Abordaremos as armadilhas de tais ações posteriormente no livro (consulte no [Capítulo 5](ch5.md)).

## Adaptando Argumentos a Parâmetros

Há uma variedade de padrões e truques que podemos usar para adaptar a assinatura de uma função para corresponder aos tipos de argumentos que desejamos fornecer a ela.

Lembre-se [desta assinatura de função do Capítulo 2](ch2.md/#user-content-funcparamdestr) que destaca o uso da desestruturação do parâmetro do array:

```js
function foo( [x,y,...args] = [] ) {
```

Esse padrão é útil quando passado dentro de um array, mas você precisa tratar seu conteúdo como parâmetros individuais. `foo(..)` é, portanto, tecnicamente unário, quando é executado, apenas um argumento (um array) será passado para ele. Mas dentro da função, você pode endereçar diferentes entradas (`x`,`y`, etc) individualmente.

No entanto, às vezes você não terá a capacidade de alterar a declaração da função para usar a desestruturação de parâmetros de um array. Por exemplo, imagine estas funções: 

```js
function foo(x,y) {
    console.log( x + y );
}

function bar(fn) {
    fn( [ 3, 9 ] );
}

bar( foo );         // fails
```

Você percebe por que `bar(foo)` falha?

O array `[3,9]` é enviado como um único valor para `fn(..)`, mas `foo(..)` expera `x` e `y` separadamente. Se púdessemos mudar a declaração de `foo(..)` para ser `function foo([x,y]) { ..` estaríamos bem. Ou, se pudéssemos mudar o comportamento de `bar(..)` para fazer a chamada como `fn(...[3,9])`, os valores `3` e `9` seriam passados individualmente.

Haverá ocasiões em que você terá duas funções incompatíveis dessa forma e não poderá alterar suas declarações/definições. Então como você pode usá-las juntas?

Podemos definir um auxiliar para adaptar uma função de modo que ela espalhe um único array recebido como seus argumentos individuais:

<a name="spreadargs"></a>

```js
function spreadArgs(fn) {
    return function spreadFn(argsArr){
        return fn( ...argsArr );
    };
}

// or the ES6 => arrow form
var spreadArgs =
    fn =>
        argsArr =>
            fn( ...argsArr );
```

**Nota:** Chamei este auxiliar de `spreadArgs(..)`, mas em bibliotecas como Ramda é comumente chamado de `apply(..)`.

Agora podemos usar `spreadArgs(..)` para adaptar `foo(..)` para funcionar como a entrada adequada para `bar(..)`:

```js
bar( spreadArgs( foo ) );           // 12
```

Não parecera claro ainda por que essas ocasiões surgem, mas você as verá com frequência. Essencialmente, `spreadArgs(..)` nos permite definir funções que `retornam` múltiplos valores por meio de um array, mas ainda têm esses múltiplos valores tratados independentemente como entradas para outra função.

Enquanto estamos falando sobre um utilitário `spreadArgs(..)`, vamos também definir um utilitário para lidar com a ação oposta:

```js
function gatherArgs(fn) {
    return function gatheredFn(...argsArr){
        return fn( argsArr );
    };
}

// or the ES6 => arrow form
var gatherArgs =
    fn =>
        (...argsArr) =>
            fn( argsArr );
```

**Nota:** No Ramda, este utilitário é referido como `unapply(..)`, sendo o oposto de `apply(..)`. Acho que a terminologia "spread"/"gather" é um pouco mais descritiva para o que está acontecendo.

Podemos usar esse utilitário para reunir argumentos individuais em um único array, talvez porque queremos adaptar uma função com desestruturação de parâmetros de array para outro utilitário que passa argumentos separadamente. Iremos [cobrir `reduce(..)` mais completamente no Capítulo 9](ch9.md/#reduce). Em suma, ele chama repetidamente sua função redutora com dois parâmetros individuais, que agora podemos *gather* juntos:

```js
function combineFirstTwo([ v1, v2 ]) {
    return v1 + v2;
}

[1,2,3,4,5].reduce( gatherArgs( combineFirstTwo ) );
// 15
```

## Um Pouco Agora, Um Pouco Depois

Se uma função receber vários argumentos, você pode querer especificar alguns deles antecipadamente e deixar o resto para ser especificado mais tarde.

Considere esta função:

```js
function ajax(url,data,callback) {
    // ..
}
```

Vamos imaginar que você gostaria de configurar várias chamadas de API em que os URLS são conhecidos antecipadamente, mas os dados e o retorno da chamada para lidar com a resposta só serão conhecidos mais tarde.

Claro, você pode simplesmente adiar a realização da chamada `àjax(..)` até que todos os bits sejam conhecidos e referir-se a alguma constante global para a URL naquele momento. Mas outra maneira é criar uma referência de função que já tenha o argumento `url` predefinido.

O que vamos fazer é criar uma nova função que ainda chama `ajax(..)` nos bastidores e definir manualmente o primeiro argumento para a URL da API de seu interesse, enquanto esperamos para aceitar os outros dois argumentos mais tarde:

```js
function getPerson(data,cb) {
    ajax( "http://some.api/person", data, cb );
}

function getOrder(data,cb) {
    ajax( "http://some.api/order", data, cb );
}
```

Especificar manualmente esses wrappers de chamada de função realmente é possível, mas pode ser um pouco tedioso, especialmente se também houver variações com diferentes argumentos predefinidos, como:

```js
function getCurrentUser(cb) {
    getPerson( { user: CURRENT_USER_ID }, cb );
}
```

Uma prática com a qual um programador funcional se acostuma é procurar padrões em que fazemos o mesmo tipo de coisas repetidamente e tentar transformar essas ações em utilitários reutilizáveis genéricos. Na verdade, tenho certeza de que esse já é o instinto para muitos de vocês, leitores, então isso não é apenas uma coisa da PF. Mas é inquestionavelmente importante para a Programação Funcional.

Para conceber tal utilidade para a predefinição de um argumento, vamos examinar conceitualmente o que está acontecendo, não apenas observando as implementações manuais mostradas aqui.

Uma maneira de ver com clareza o que está acontecendo é que a função `getOrder(data, cb)` é uma *aplicação parcial* da função `ajax(url, data, cb)`. Essa terminologia vem da noção de que os argumentos são *aplicados* a parâmetros no local de chamada de função. E como você pode ver, estamos apenas aplicando alguns dos argumentos iniciais, especificamente, o argumento para o parâmetro `url`, enquanto deixamos o resto para ser aplicado mais tarde.

Para ser um pouco mais formal sobre esse padrão, a aplicação parcial é estritamente uma redução na aridade de uma função. Lembre-se, esse é o número de entradas de parâmetros esperadas. Reduzimos a aridade da função `ajax(..)` original de 3 para 2 na função `getOrder(..)`.

Vamos definir um utilitário `partial(..)`:

```js
function partial(fn,...presetArgs) {
    return function partiallyApplied(...laterArgs){
        return fn( ...presetArgs, ...laterArgs );
    };
}

// or the ES6 => arrow form
var partial =
    (fn,...presetArgs) =>
        (...laterArgs) =>
            fn( ...presetArgs, ...laterArgs );
```

**Dica:** Não aceite este snippet pelo valor de cara. Pare por alguns momentos para digerir o que está acontecendo com este utilitário. Certifique-se de que você realmente *entendeu*.

A função `partial(..)` recebe um `fn` para a qual estamos aplicando parcialmente. Então, quaisquer argumentos passados subsequentes são reunidos no array `presetArgs` e salvos para mais tarde.

Uma nova função interna (chamada `partialllyApplied(..)` apenas para maior clareza) é criada e `retornada`. Os próprios argumentos da função interna são reunidos em um array chamado de `laterArgs`.

Percebe as referências à `fn` e `presetArgs` dentro desta função interna? Como isso funciona? Depois que `partial(..)` termina a execução, como a função interna continua sendo capaz de acessar `fn` e `presetArgs`? Se você responseu **closure**, está no caminho certo! A função interna `partiallyApplied(..)` fecha sobre as variáveis `fn` e `presetArgs` para que possa continuar acessando-as mais tarde, não importa onde a função seja executada. É por isso que entender closure é fundamental!

Quando a função `partiallyApplied(..)` é posteriormente executada em algum outro lugar em seu programa, ela usa o `fn` fechado para executar a função original, primeiro fornecendo qualquer um dos argumentos parciais da aplicação `presetArgs` (fechado), depois quaisquer outros argumentos em `laterArgs`.

Se alguma coisa estiver confusa, pare e releia. Confie em mim, você ficará feliz por ter feito isso à medida que avançamos no texto.

Vamos agora usar o utilitário `partial(..)` para fazer aquelas funções que foram parcialmente aplicadas anteriormente:

```js
var getPerson = partial( ajax, "http://some.api/person" );

var getOrder = partial( ajax, "http://some.api/order" );
```

Tire um momento para considerar a forma/partes internas de `getPerson(..)`. Será mais ou menos assim:

```js
var getPerson = function partiallyApplied(...laterArgs) {
    return ajax( "http://some.api/person", ...laterArgs );
};
```

O mesmo será verdadeiro para `getOrder(..)`. Mas e quanto a `getCurrentUser(..)`?

```js
// version 1
var getCurrentUser = partial(
    ajax,
    "http://some.api/person",
    { user: CURRENT_USER_ID }
);

// version 2
var getCurrentUser = partial( getPerson, { user: CURRENT_USER_ID } );
```

Podemos definir `getCurrentUser(..)` com os argumentos `url` e `data` especificados diretamente (versão 1), ou definir `getCurrentUser(..)` como uma aplicação parcial do `getPerson(..)` aplicado parcialmente, especificando apenas o argumento adicional `data` (versão 2).

A versão 2 é um pouco mais limpa de se expressar porque reutiliza algo já definido. Como tal, acho que se encaixa um pouco mais no espírito da PF.

Só para ter certeza de que entendemos como essas duas versões funcionarão nos bastidores, elas se parecem com:

```js
// version 1
var getCurrentUser = function partiallyApplied(...laterArgs) {
    return ajax(
        "http://some.api/person",
        { user: CURRENT_USER_ID },
        ...laterArgs
    );
};

// version 2
var getCurrentUser = function outerPartiallyApplied(...outerLaterArgs){
    var getPerson = function innerPartiallyApplied(...innerLaterArgs){
        return ajax( "http://some.api/person", ...innerLaterArgs );
    };

    return getPerson( { user: CURRENT_USER_ID }, ...outerLaterArgs );
}
```

Novamente, pare e releia esses trechos de código para ter certeza de entender o que está acontecendo lá.

**Nota:** A versão 2 tem uma camada extra de quebra de função envolvida. Isso pode cheirar estranho e desnecessário, mas esta é apenas uma daquelas coisas na PF com que você vai querer se sentir realmente confortável. Estaremos agrupando muitas camadas de funções umas nas outras conforme progredimos no texto. Lembre-se, isso é programação *funcional*!

Vamos dar uma olhada em outro exemplo da utilidade da aplicação parcial. Considere uma função `add(..)` que pega dois argumentos e os adiciona:

```js
function add(x,y) {
    return x + y;
}
```

Agora imagine que gostaríamos de pegar uma lista de números e adicionar um certo número a cada um deles. Usaremos o utilitário `map(..)` (veja no [Capítulo 9, "Map"](ch9.md/#map)) integrado aos arrays JS:

```js
[1,2,3,4,5].map( function adder(val){
    return add( 3, val );
} );
// [4,5,6,7,8]
```

A razão pela qual não podemos passar `add(..)` diretamente para `map(..)` é porque a assinatura de `add(..)` não corresponde à função de mapeamento que `map(..)` espera. É aí que a aplicação parcial pode nos ajudar: podemos adaptar a assinatura de `add(..)` para algo que corresponda a:

```js
[1,2,3,4,5].map( partial( add, 3 ) );
// [4,5,6,7,8]
```

A chamada `partial(add, 3)` produz uma nova função unária que espera apenas mais um argumento.

O utilitário `map(..)` fará um loop através do array (`[1,2,3,4,5]`) e repetidamente chamará esta função unária, uma vez para cada um desses valores, respectivamente. Assim, as chamadas feitas serão efetivamente `add(3,1)`, `add(3,2)`, `add(3,3)`, `add(3,4)` e `add(3,5)`. A matriz desses resultados é `[4,5,6,7,8]`.

### `bind(..)`

Todas as funções JavaScript têm um utilitário embutido chamado `bind(..)`. Ele tem dois recursos: predefinir o contexto `this` e aplicar argumentos parcialmente.

Acho que é incrivelmente equivocado combinar esses dois recursos em um utilitário. Às vezes, você desejará vincular fisicamente o contexto `this` e não aplicar argumentos parcialmente. Outras vezes, você desejará aplicar argumentos parcialmente, mas não se preocupará com a vinculação `this`. Nunca precisei dos dois ao mesmo tempo.

Em último cenário (aplicação parcial sem definir o contexto `this`) é estranho porque você tem que passar um marcador ignorável para o argumento de ligação `this` (o primeiro), normalmente `nulo`.

Considere:

```js
var getPerson = ajax.bind( null, "http://some.api/person" );
```

Esse `nulo` me incomoda sem fim. Apesar desse *this* aborrecimento, é razoavelmente conveniente que o JS tenha um utilitário embutido para aplicação parcial. No entanto, a maioria dos programadores da PF prefere usar o utilitário `partial(..)` dedicado em sua biblioteca de PF escolhida.

### Invertendo Argumentos

Lembre-se de que a assinatura para nossa função Ajax é :`ajax(url, data, cb)`. E se quiséssemos aplicar parcialmente o `cb`, mas esperar para especificar `data` e `url` mais tarde? Poderíamos criar um utilitário que envolva uma função para inverter a ordem de seus argumentos.

```js
function reverseArgs(fn) {
    return function argsReversed(...args){
        return fn( ...args.reverse() );
    };
}

// or the ES6 => arrow form
var reverseArgs =
    fn =>
        (...args) =>
            fn( ...args.reverse() );
```

Agora podemos inverter a ordem dos argumentos `ajax(..)`, para que possamos então aplicar parcialmente da direita em vez da esquerda. Para restaurar a ordem esperada, reverteremos a função aplicada parcialmente depois:

```js
var cache = {};

var cacheResult = reverseArgs(
    partial( reverseArgs( ajax ), function onResult(obj){
        cache[obj.id] = obj;
    } )
);

// later:
cacheResult( "http://some.api/person", { user: CURRENT_USER_ID } );
```

Em vez de usar manualmente `reverseArgs(..)` (duas vezes!) para este propósito, podemos definir um `partialRight(..)` que aplica parcialmente os argumentos mais à direita. Por baixo dos panos, ele pode usar o mesmo truque duplo reverso.

<a name="partialright"></a>

```js
function partialRight(fn,...presetArgs) {
    return reverseArgs(
        partial( reverseArgs( fn ), ...presetArgs.reverse() )
    );
}

var cacheResult = partialRight( ajax, function onResult(obj){
    cache[obj.id] = obj;
});

// later:
cacheResult( "http://some.api/person", { user: CURRENT_USER_ID } );
```

Outra implementação mais direta (e, certamente, com mais desempenho) de `partialRight(..)` que não usa o truque duplo-reverso é:

```js
function partialRight(fn,...presetArgs) {
    return function partiallyApplied(...laterArgs){
        return fn( ...laterArgs, ...presetArgs );
    };
}

// or the ES6 => arrow form
var partialRight =
    (fn,...presetArgs) =>
        (...laterArgs) =>
            fn( ...laterArgs, ...presetArgs );
```

Nenhuma dessas implementações de `partialRight(..)` garantem que um parâmetro específico receberá um valor específico parcialmente aplicado, ela apenas garante que os valores parcialmente aplicados apareçam como o(s) argumento(s) mais à direita (aka, último) passado para a função original.

Por exemplo:

```js
function foo(x,y,z,...rest) {
    console.log( x, y, z, rest );
}

var f = partialRight( foo, "z:last" );

f( 1, 2 );          // 1 2 "z:last" []

f( 1 );             // 1 "z:last" undefined []

f( 1, 2, 3 );       // 1 2 3 ["z:last"]

f( 1, 2, 3, 4 );    // 1 2 3 [4,"z:last"]
```

O valor `"z:last"` só é aplicado ao parâmetro `z` no caso em que `f(..)` é chamado com exatamente dois argumentos (correspondendo aos parâmetros `x` e `y`). Em todos os outros casos, o `"z:last"` será apenas o argumento mais à direita, não importa quantos argumentos o precedam.

## Um por vez

Vamos examinar uma técnica semelhante à aplicação parcial, em que uma função que espera vários argumentos é dividida em funções encadeadas sucessivas em que cada uma recebe um único argumento (aridade: 1) e retorna outra função para aceitar o próximo argumento.

Essa técnica é chamada de currying.

Para ilustrar primeiro, vamos imaginar que já tínhamos uma versão curry de `ajax(..)` criada. é assim que o usaríamos:

```js
curriedAjax( "http://some.api/person" )
    ( { user: CURRENT_USER_ID } )
        ( function foundUser(user){ /* .. */ } );
```

Os três conjuntos `(..)`s denotam três chamadas de função em cadeia. Mas talvez dividir cada uma das três chamadas ajude a ver o que está acontecendo melhor:

```js
var personFetcher = curriedAjax( "http://some.api/person" );

var getCurrentUser = personFetcher( { user: CURRENT_USER_ID } );

getCurrentUser( function foundUser(user){ /* .. */ } );
```

Em vez de pegar todos os argumentos de uma vez (como `ajax(..)`), ou alguns dos argumentos logo no inicio e o resto depois (via `parcial(..)`), esta função `curriedAjax(..)` recebe um argumento de cada vez, cada um em uma chamada de função separada.

Currying é semelhante à aplicação parcial em que cada chamada curried sucessiva aplica parcialmente outro argumento à função original, até que todos os argumentos tenham sido passados.

A principal diferença é que `curriedAjax(..)` retornará uma função (nós a chamamos de `personFetcher(..)`) que espera **somente o próximo argumento** com os dados, não um que (como o anterior `getPerson(..)`) pode receber todos os outros argumentos.

Se uma função original esperava cinco argumentos, a forma curry dessa função pegaria apenas o primeiro argumento e retornaria uma função para aceitar o segundo. Esse pegaria apenas o segundo argumento e retornaria uma função para aceitar o terceiro. E assim por diante.

Portanto, currying desdobra uma única função de aridade superior em uma série de funções unárias encadeadas.

Como podemos definir um utilitário para fazer este currying? Considere:

<a name="curry"></a>

```js
function curry(fn,arity = fn.length) {
    return (function nextCurried(prevArgs){
        return function curried(nextArg){
            var args = [ ...prevArgs, nextArg ];

            if (args.length >= arity) {
                return fn( ...args );
            }
            else {
                return nextCurried( args );
            }
        };
    })( [] );
}

// or the ES6 => arrow form
var curry =
    (fn,arity = fn.length,nextCurried) =>
        (nextCurried = prevArgs =>
            nextArg => {
                var args = [ ...prevArgs, nextArg ];

                if (args.length >= arity) {
                    return fn( ...args );
                }
                else {
                    return nextCurried( args );
                }
            }
        )( [] );
```

A abordagem aqui é iniciar uma coleção de argumentos em `prevArgs` como um array `[]` vazio, e adicionar cada `nextArg` recebido a ele, chamando a concatenação `args`. Enquanto `args.length` é menor que `arity` (o número de parâmetros declarados/esperados da função `fn(..)` original), faça e retorne outra função `curried(..)` para coletar o próximo argumento `nextArg`, passando a coleção `args` em execução junto com seu `prevArgs`. Assim que tivermos `args` suficientes, execute a função `fn(..)` original com eles.

Por padrão, esta implementação depende da capacidade de inspecionar a propriedade `length` da função a ser curried para saber quantas iterações da currying precisaremos antes de coletar todos os seus argumentos esperados.

**Nota:** Se você usar esta implementação de `curry(..)` com uma função que não tem uma propriedade `length` precisa, você precisará passar o `arity` (o segundo parâmetro de `curry(..)`) para garantir que `curry(..)` funcione corretamente. `length` será impreciso se a assinatura do parâmetro da função incluir valores de parâmetro padrão, desestruturação de parâmetro ou sua variável com `...args` (Veja no [Capítulo 2](ch2.md)).

Aqui está como usaríamos `curry(..)` para nosso exemplo anterior de `ajax(..)`:

```js
var curriedAjax = curry( ajax );

var personFetcher = curriedAjax( "http://some.api/person" );

var getCurrentUser = personFetcher( { user: CURRENT_USER_ID } );

getCurrentUser( function foundUser(user){ /* .. */ } );
```

Cada chamada aplica parcialmente mais um argumento à chamada original `ajax(..)`, até que todos os três tenham sido fornecidos e `ajax(..)` seja realmente invocado.

Lembra-se do nosso exemplo da discussão de aplicação parcial sobre adicionar `3` a cada valor em uma lista de números? Como currying é semelhante à aplicação parcial, poderíamos fazer essa tarefa com currying quase da mesma maneira:

```js
[1,2,3,4,5].map( curry( add )( 3 ) );
// [4,5,6,7,8]
```

A diferença entre os dois? `partial(add,3)` vs `curry(add)(3)`.

Por que você deve escolher `curry(..)` em vez de `partial(..)`? Pode ser útil no caso em que você sabe com antecedência que `add(..)` é a função a ser adaptada, mas o valor `3` ainda não é conhecido:

```js
var adder = curry( add );

// later
[1,2,3,4,5].map( adder( 3 ) );
// [4,5,6,7,8]
```

Vejamos outro exemplo de números, desta vez adicionando uma lista deles:

```js
function sum(...nums) {
    var total = 0;
    for (let num of nums) {
        total += num;
    }
    return total;
}

sum( 1, 2, 3, 4, 5 );                       // 15

// now with currying:
// (5 to indicate how many we should wait for)
var curriedSum = curry( sum, 5 );

curriedSum( 1 )( 2 )( 3 )( 4 )( 5 );        // 15
```

A vantagem de currying aqui é que cada chamada para passar um argumento produz outra função que é mais especializada e podemos capturar e usar *aquela* nova função posteriormente no programa. A aplicação parcial específica todos os argumentos parcialmente aplicados antecipadamente, produzindo uma função que está esperando por todos os outros argumentos **na próxima chamada**.

Se você quiser usar a aplicação parcial para especificar um parâmetro (ou vários!) por vez, terá que continuar chamando `partial(..)` novamente em cada função parcialmente aplicada sucessivamente. Em contraste, as funções curried fazem isso automaticamente, tornando o trabalho com argumentos individuais cada vez mais ergonômicos. 

Currying e a aplicação parcial usam closure para lembrar os argumentos ao longo do tempo até que todos tenham sido recebidos e, em seguida, a função original pode ser chamada.

### Visualizando Funções Curried

Vamos examinar mais de perto o `curriedSum(..)` da seção anterior. Lembre-se de seu uso: `curriedSum(1)(2)(3)(4)(5)`, cinco chamadas de função subsequentes (encadeadas).

E se definirmos manualmente um `curriedSum(..)` em vez de usar `curry(..)`? Como ficaria isso?

```js
function curriedSum(v1) {
    return function(v2){
        return function(v3){
            return function(v4){
                return function(v5){
                    return sum( v1, v2, v3, v4, v5 );
                };
            };
        };
    };
}
```

Definitivamente mais feio, sem dúvidas. Mas essa é uma maneira importante de visualizar o que está acontecendo com uma função de curry. Cada chamada de função aninhada está retornando outra função que aceitará o próximo argumento e isso continuará até que tenhamos específicado todos os argumentos esperados.

Ao tentar decifrar as funções curried, descobri que me ajuda tremendamente se eu puder desembrulhá-las mentalmente como uma série de funções aninhadas.

Na verdade, para reforçar esse ponto, vamos considerar o mesmo código, mas escrito com as funções de seta do ES6:

```js
curriedSum =
    v1 =>
        v2 =>
            v3 =>
                v4 =>
                    v5 =>
                        sum( v1, v2, v3, v4, v5 );
```

E agora, tudo em uma linha:

```js
curriedSum = v1 => v2 => v3 => v4 => v5 => sum( v1, v2, v3, v4, v5 );
```

Dependendo da sua perspectiva, essa forma de visualizar a função curried pode ser mais ou menos útil para você. Para mim, é um pouco mais obscuro.

Mas a razão pela qual mostro dessa forma é que ela parece quase idêntica à notação matemática (e à sintaxe de Haskell) para uma função cirred! Essa é uma razão pela qual aqueles que gostam de notação matemática (e/ou Haskell) gostam da forma de função de seta do ES6.

### Por que Currying e Aplicação Parcial?

Com qualquer estilo, currying (como `sum(1)(2)(3)`) ou aplicação parcial (como `partial(sum,1,2)(3)`), a chamada de função parece inquestionavelmente mais estranho do que um mais comum como `sum(1,2,3)`. Então, **por que iríamos nessa direção** ao adotar a PF? Existem várias camadas para responder a essa pergunta.

O primeiro e mais óbvio motivo é que o currying e aplicação parcial permitem que você separe no tempo/espaço (em toda a sua base de código) quando e onde argumentos separados são específicados, enquanto as chamadas de função tradicionais exigem que todos os argumentos estejam presentes ao mesmo tempo. Se você tem um lugar em seu código onde você conhece alguns dos argumentos e outro lugar onde os outros argumentos são determinados, currying ou aplicação parcial são muito úteis.

Outra camada para essa resposta, específicamente para currying, é que a composição de funções é muito mais fácil quando há apenas um argumento. Portanto, uma função que precisa de três argumentos em última instância, se for curried, torna-se uma função que precisa de apenas um, três vezes. Esse tipo de função unária será muito mais fácil de trabalhar quando começarmos a compô-los. Abordaremos esse tópico posteriormente no [Capítulo 4](ch4.md).

Mas a camada mais importante é a especialização de funções generalizadas e como essa abstração melhora a legibilidade do código.

Considere nosso exemplo de execução `ajax(..)`:

```js
ajax(
    "http://some.api/person",
    { user: CURRENT_USER_ID },
    function foundUser(user){ /* .. */ }
);
```

O call-site inclui todas as informações necessárias para passar para a versão mais generalizada do utilitário (`ajax(..)`). A desvantagem potencial da legibilidade é que pode ser o caso de que a URL e os dados não sejam informações relevantes neste ponto do programa, mas mesmo assim essas informações estão confundindo a chamada de função.

Agora considere:

```js
var getCurrentUser = partial(
    ajax,
    "http://some.api/person",
    { user: CURRENT_USER_ID }
);

// later

getCurrentUser( function foundUser(user){ /* .. */ } );
```

Nesta versão, definimos uma função `getCurrentUser(..)` antecipadamente que já possui informações conhecidas como a URL e a predefinição dos dados. A chamada de função para `getCurrentUser(..)` não está confusa com informações que **naquele ponto do código** não são relevantes.

Além disso, o nome semântico para a função `getCurrentUser(..)` representa com mais precisão o que está acontecendo do que apenas `ajax(..)` com uma URL e os dados.

É disso que trata a abstração: separar dois conjuntos de detalhes, neste caso, o *como* obter um usuário atual e *o que* fazemos com esse usuário e inserir um limite semântico entre eles, o que facilita o raciocínio de cada parte de forma independente.

Quer você use currying ou aplicação parcial, criando funções especializadas a partir de funções generalizadas é uma técnica poderosa para abstração semântica e de legibilidade aprimorada.

### Currying Com Mais de Um Argumento?

A definição e implementação que dei sobre currying até agora são, acredito, tão fiéis ao espírito quanto podemos obter em JavaScript.

Especificamente, se olharmos brevemente como currying funciona em Haskell, podemos observar que vários argumentos sempre vão para uma função, um por vez, um por chamada curried, exceto tuplas (análogas a matrizes para nossos propósitos) que transportam múltiplos valores em um único argumento.

Por exemplo, em Haskell:

```haskell
foo 1 2 3
```

Isso chama a função `foo` e tem o resultado de passar três valores `1`, `2` e `3`. Mas as função são automaticamente curried em Haskell, o que significa que cada valor entra como uma chamada curry separada. O equivalente em JS seria semelhante a `foo(1)(2)(3)`, que é o mesmo estilo de `curry(..)` que apresentei anteriormente.

**Nota:** Em Haskell, `foo(1,2,3)` não está passando esses três valores de uma vez como três argumentos separados, mas uma tupla (como uma matriz JS) com um único argumento. Para isso funcionar, `foo` precisaria ser alterado para lidar com uma tupla nessa posição de argumento. Pelo que eu posso dizer, não há como em Haskell passar todos os trẽs argumentos separadamente com apenas uma chamada de função, cada argumento recebe sua própria chamada de curried. Claro, a presença de várias chamadas é transparente para o desenvolvedor Haskell, mas é muito mais sintaticamente óbvio para o desenvolvedor JS.

Por essas razões, acho que o `curry(..)`, que demonstrei anteriormente, é uma adaptação fiel, ou o que eu poderia chamar de "currying estrito". No entanto, é importante observar que há uma definição mais ampla usada nas bibliotecas JavaScript de PF.

Especificamente, os utilitários currying JS geralmente permitem que você especifique vários argumentos para cada chamada curried. Revisitando nosso exemplo `sum(..)` anterior, ficaria assim:

```js
var curriedSum = looseCurry( sum, 5 );

curriedSum( 1 )( 2, 3 )( 4, 5 );            // 15
```

Vemos uma ligeira economia de sintaxe de menos `( )` e um benefício de desempenho implícito de agora ter três chamadas de função em vez de cinco. Mas, fora isso, usar `looseCurry(..)` é idêntico em resultado final à definição mais restrita de `curry(..)` anterior. Eu acho que o fator conveniência/desempenho é provavelmente o motivo pelo qual os frameworks permitem vários argumentos. Isso parece mais uma questão de gosto.

Podemos adaptar nossa implementação de currying anterior a esta definição mais comum:

<a name="loosecurry"></a>

```js
function looseCurry(fn,arity = fn.length) {
    return (function nextCurried(prevArgs){
        return function curried(...nextArgs){
            var args = [ ...prevArgs, ...nextArgs ];

            if (args.length >= arity) {
                return fn( ...args );
            }
            else {
                return nextCurried( args );
            }
        };
    })( [] );
}
```

Agora, cada chamada curried aceita um ou mais argumentos (como `nextArgs`). Vamos deixar como um exercício para o leitor interessado escrever a versão em `=>` ES6 de `looseCurry(..)` semelhante a como fizemos em `curry(..)` anteriormente.

### Sem Curry para Mim, Por Favor

Também poder ser o caso de você ter uma função curry que gostaria essencialmente de desfazer o curry, basicamente, transformar uma função como `f(1)(2)(3)` de volta em uma função como `g(1,2,3)`.

O padrão utilizado para isso é (des)chocantemente chamado de `uncurry(..)`. Aqui está uma implementação simples e ingênua:

```js
function uncurry(fn) {
    return function uncurried(...args){
        var ret = fn;

        for (let arg of args) {
            ret = ret( arg );
        }

        return ret;
    };
}

// or the ES6 => arrow form
var uncurry =
    fn =>
        (...args) => {
            var ret = fn;

            for (let arg of args) {
                ret = ret( arg );
            }

            return ret;
        };
```

**Aviso:** Não presuma apenas que `uncurry(curry(f))` tem o mesmo comportamento que `f`. Em algumas bibliotecas, a falta de pressa resultaria em uma função como a original, mas não em todas, certamente não no nosso exemplo aqui. A função não recorrente atua (principalmente) da mesma forma que a função original se você passar para ela tantos argumentos quanto a função original esperava. No entanto, se você passar menos argumentos, ainda receberá de volta uma função parcialmente curried esperando por mais argumentos. Essa peculiaridade é ilustrada no seguinte trecho:

```js
function sum(...nums) {
    var sum = 0;
    for (let num of nums) {
        sum += num;
    }
    return sum;
}

var curriedSum = curry( sum, 5 );
var uncurriedSum = uncurry( curriedSum );

curriedSum( 1 )( 2 )( 3 )( 4 )( 5 );        // 15

uncurriedSum( 1, 2, 3, 4, 5 );              // 15
uncurriedSum( 1, 2, 3 )( 4 )( 5 );          // 15
```

Provavelmente, o caso mais comum de usar `uncurry(..)` não é com uma função curry manualmente como mostrado, mas com uma função que sai curried como resultado de algum outro conjunto de operações. Ilustraremos esse cenário posteriormente neste capítulo na [discussão "Sem pontos"](#no-points).

## A Ordem Importa

No Capítulo 2, exploramos o [padrão de argumentos nomeados](ch2.md/#named-arguments). Uma vantagem principal dos argumentos nomeados é não precisar conciliar a ordem dos argumentos, melhorando assim a legibilidade.

Vimos as vantagens de usar currying/aplicação parcial para fornecer argumentos individuais para uma função separadamente. Mas a desvantagem é que essas técnicas são tradicionalmente baseadas em argumentos posicionais, a ordenação de argumentos é, portanto, uma dor de cabeça inevitável.

Utilitários como `reverseArgs(..)` (e outros) são necessários para manipular argumentos para colocá-los na ordem correta. Às vezes, temos sorte e definimos uma função com parâmetros na ordem em que mais tarde queremos fazer um curry neles, mas outras vezes essa ordem é incompatível e temos que pular para reordenar.

A frustração não é apenas que precisamos usar algum utilitário para manipular as propriedades, mas o fato de que o uso do utilitário confunde nosso código com um pouco de ruído extra. Esses tipos de coisas são como pequenos cortes de papel, um aqui ou parece que não cortou, mas a dor pode certamente aumentar.

Podemos melhorar o currying/aplicação parcial para liberá-lo dessas preocupações de ordem? Vamos aplicar os truques do estilo de argumentos nomeados e inventar alguns utilitários auxiliares para esta adaptação:

```js
function partialProps(fn,presetArgsObj) {
    return function partiallyApplied(laterArgsObj){
        return fn( Object.assign( {}, presetArgsObj, laterArgsObj ) );
    };
}

function curryProps(fn,arity = 1) {
    return (function nextCurried(prevArgsObj){
        return function curried(nextArgObj = {}){
            var [key] = Object.keys( nextArgObj );
            var allArgsObj = Object.assign(
                {}, prevArgsObj, { [key]: nextArgObj[key] }
            );

            if (Object.keys( allArgsObj ).length >= arity) {
                return fn( allArgsObj );
            }
            else {
                return nextCurried( allArgsObj );
            }
        };
    })( {} );
}
```

**Dica:** Nós nem mesmo precisamos de `partialPropsRight(..)` porque não precisamos nos preocupar com qual ordem as propriedades estão sendo mapeadas, os mapeamentos de nomes tornam essa questão de ordem discutível!

Veja como usar esses utilitários:

```js
function foo({ x, y, z } = {}) {
    console.log( `x:${x} y:${y} z:${z}` );
}

var f1 = curryProps( foo, 3 );
var f2 = partialProps( foo, { y: 2 } );

f1( {y: 2} )( {x: 1} )( {z: 3} );
// x:1 y:2 z:3

f2( { z: 3, x: 1 } );
// x:1 y:2 z:3
```

Mesmo com currying ou aplicação parcial, a ordem não importa mais! Agora podemos especificar quais argumentos queremos em qualquer sequência que faça sentido. Chega de `reverseArgs(..)` ou outros incômodos. Legal!

**Dica:** Se este estilo de argumentos de função parece útil ou interessante para você, verifique a cobertura da minha [biblioteca FPO no Apêndice C](apC.md/#bonus-fpo).

### Propriedades de Espalhamento (Spreading)

Infelizmente, só podemos tirar vantagem de usar argumentos nomeados se tivermos controle sobre a assinatura de `foo(..)` e defini-la para desestruturar seu primeiro parâmetro. E se quiséssemos usar essa técnica com uma função que tivesse seus parâmetros individualmente (sem desestruturação de parâmetro!), e não pudéssemos alterar essa assinatura de função? Por exemplo:

```js
function bar(x,y,z) {
    console.log( `x:${x} y:${y} z:${z}` );
}
```

Assim como o utilitário `spreadArgs(..)` anterior, podemos definir um auxiliar `spreadArgProps(..)` que pega os pares `chave:valor` de um argumento de objeto e "spreads" os valores como argumentos individuais.

No entanto, existem algumas peculiaridades a serem observadas. Com `spreadArgs(..)`, estávamos lidando com arrays, onde a ordem é bem definida e óbvia. Entretanto, com objetos, a ordem das propriedades é menos claro e não necessariamente confiável. Dependendo de como um objeto é criado e das propriedades definidas, não podemos ter certeza absoluta de quais propriedades de ordem de enumeração sairiam.

Esse utilitário precisa de uma maneira de permitir que você defina em que ordem a função em questão espera seus argumentos (por exemplo, ordem de enumeração de propriedade). Podemos passar um array como `["x","y","z"]` para dizer ao utilitário para extrair as propriedades do argumento do objeto exatamente nessa ordem.

Isso é decente, mas também é uma pena que então *nos obrigue* a adicionar esse array de nome de propriedade mesmo para as funções mais simples. Existe algum tipo de truque que possamos usar para detectar a ordem em que os parâmetros são listados para uma função, pelo menos nos casos mais comuns? Felizmente, sim!

As funções JavaScript têm um método `.toString()` que fornece uma representação em string do código da função, incluindo a assinatura da declaração da função. Tirando o pó de nossas habilidades de análise de expressão regular, podemos analisar a representação de string da função e extrair os parâmetros nomeados individualmente. O código parece um pouco complicado, mas é bom o suficiente para fazer o trabalho:

```js
function spreadArgProps(
    fn,
    propOrder =
        fn.toString()
        .replace( /^(?:(?:function.*\(([^]*?)\))|(?:([^\(\)]+?)
            \s*=>)|(?:\(([^]*?)\)\s*=>))[^]+$/, "$1$2$3" )
        .split( /\s*,\s*/ )
        .map( v => v.replace( /[=\s].*$/, "" ) )
) {
    return function spreadFn(argsObj){
        return fn( ...propOrder.map( k => argsObj[k] ) );
    };
}
```

**Nota:** A lógica de análise dos parâmetros deste utilitário está longe de ser à prova de balas. Estamos usando expressões regulares para analisar o código, o que já é uma premissa falha! Mas nosso único objetivo aqui é lidar com os casos comuns, o que isso faz razoavelmente bem. Precisamos apenas de uma detecção padrão sensata da ordem dos parâmetros para funções com parâmetros simples (incluindo aqueles com valores de parâmetro padrão). Não precisamos, por exemplo, ser capazes de analisar um parâmetro desestruturado complexo, porque provavelmente não usaríamos este utilitário com tal função, de qualquer maneira. Portanto, essa lógica faz o trabalho 80% do tempo. Ele nos permite substituir o array `propOrder` para qualquer outra assinatura de função mais complexa que de outra forma não seria analisada corretamente. Esse é o tipo de equilíbrio pragmático que este livro busca encontrar sempre que possível.

Vamos ilustrar usando nosso utilitário `spreadArgProps(..)`:

```js
function bar(x,y,z) {
    console.log( `x:${x} y:${y} z:${z}` );
}

var f3 = curryProps( spreadArgProps( bar ), 3 );
var f4 = partialProps( spreadArgProps( bar ), { y: 2 } );

f3( {y: 2} )( {x: 1} )( {z: 3} );
// x:1 y:2 z:3

f4( { z: 3, x: 1 } );
// x:1 y:2 z:3
```

Embora a ordem não seja mais uma preocupação, o uso de funções definidas neste estilo requer que você saiba qual é o nome exato de cada argumento. Você não pode simplesmente lembrar, "oh, a função entra como o primeiro argumento". Em vez disso, você deve se lembrar, "o parâmetro da função é chamado 'fn'." As convenções podem criar consistência de nomenclatura que diminui esse fardo, mas ainda é algo a ter em conta.

Pese essas compensações com cuidado.

## Sem Pontos

Um estilo popular de código no mundo da PF visa reduzir parte da desordem visual, removendo o mapeamento de parâmetro argumento desnecessário. Esse estilo é formalmente chamado de programação tácita ou, mais comumente: estilo sem pontos. O termo "ponto" aqui se refere à entrada de parâmetro de uma função.

**Aviso:** Pare por um momento. Vamos nos certificar de que tomamos o cuidado de não tomar essa discussão como uma sugestão ilimitada de que você exagere ao tentar ser livre de pontos em seu código de PF a todo custo. Esta deve ser uma técnica para melhorar a legibilidade, quando usada com moderação. Mas, como acontece com a maioria das coisas no desenvolvimento de software, você pode definitivamente abusar dela. Se o seu código ficar mais difícil de entender por causa dos obstáculos que você precisa percorrer para ficar sem pontos, pare. Você não ganhará uma fita azul só porque encontrou uma maneira inteligente, mas esotérica, de remover outro "ponto" de seu código.

Vamos começar com um simples exemplo:


```js
function double(x) {
    return x * 2;
}

[1,2,3,4,5].map( function mapper(v){
    return double( v );
} );
// [2,4,6,8,10]
```

Você pode ver que `mapper(..)` e `double(..)` têm as mesmas (ou compatíveis, de qualquer maneira) assinaturas? O parâmetro ("ponto") `v` pode mapear diretamente para o argumento correspondente na chamada `double(..)`. Como tal, o wrapper da função `mapper(..)` é desnecessário. Vamos simplificar com o estilo sem pontos:

```js
function double(x) {
    return x * 2;
}

[1,2,3,4,5].map( double );
// [2,4,6,8,10]
```

Vamos revisitar um exemplo anterior:

```js
["1","2","3"].map( function mapper(v){
    return parseInt( v );
} );
// [1,2,3]
```

Neste exemplo, `mapper(..)` está realmente servindo a um propósito importante, que é descartar o argumento `index` que `map(..)` passaria, porque `parseInt(..)` interpretaria incorretamente esse valor como uma `raiz` para a análise.

Se você se lembra do início deste capítulo, este foi um exemplo em que `unário(..)` nos ajuda:

```js
["1","2","3"].map( unary( parseInt ) );
// [1,2,3]
```

Sem pontos!

A principal coisa a procurar é se você tem uma função com parâmetro(s) que é/são passados diretamente para uma chamada de função interna. Em ambos os exemplos anteriores, `mapper(..)` tinha o parâmetro `v` que foi passado para outra chamada de função. Conseguimos substituir essa camada de abstração por uma expressão sem pontos usando `unário(..)`.

**Aviso:** Você pode ter ficado tentado, como eu, a tentar `map(partialRight(parseInt,10))` para aplicar parcialmente à direita o valor `10` como o `radix`. No entanto, como vimos anteriormente, `partialRight(..)` apenas garante que `10` será o ultimo argumento passado, não que será especificamente o segundo argumento. Como o próprio `map(..)` passa três argumentos (`valor`, `índice`, `arr`) para sua função de mapeamento, o valor `10` seria apenas o quarto argumento para `parseInt(..)`, ele só presta atenção aos dois primeiros.

<a name="shortlongenough"></a>

Aqui está outro exemplo:

```js
// convenience to avoid any potential binding issue
// with trying to use `console.log` as a function
function output(txt) {
    console.log( txt );
}

function printIf( predicate, msg ) {
    if (predicate( msg )) {
        output( msg );
    }
}

function isShortEnough(str) {
    return str.length <= 5;
}

var msg1 = "Hello";
var msg2 = msg1 + " World";

printIf( isShortEnough, msg1 );         // Hello
printIf( isShortEnough, msg2 );
```

Agora, digamos que você deseja imprimir uma mensagem apenas se for longa o suficiente, em outras palavras, se for `!isShortEnough(..)`. Seu primeiro pensamento é provavelmente este:

```js
function isLongEnough(str) {
    return !isShortEnough( str );
}

printIf( isLongEnough, msg1 );
printIf( isLongEnough, msg2 );          // Hello World
```

Bastante fácil... mas "aponta" agora! Vê como `str` é passado? Sem reimplementar a verificação `str.lenght`, podemos refatorar este código para o estilo sem pontos?

Vamos definir um auxiliar de negação `not(..)` (muitas vezes referido como `complemento(..)` em bibliotecas de PF):

```js
function not(predicate) {
    return function negated(...args){
        return !predicate( ...args );
    };
}

// or the ES6 => arrow form
var not =
    predicate =>
        (...args) =>
            !predicate( ...args );
```

A seguir, vamos usar `not(..)` para definir alternativamente `isLongEnough(..)` sem "pontos":

```js
var isLongEnough = not( isShortEnough );

printIf( isLongEnough, msg2 );          // Hello World
```

Isso é muito bom, não é? Mas nós *poderíamos* continuar. `printIf(..)` poderia ser refatorado para ser ele próprio livre de pontos.

Podemos expressar a parte condicional do `if` com um utilitário `when(..)`:

```js
function when(predicate,fn) {
    return function conditional(...args){
        if (predicate( ...args )) {
            return fn( ...args );
        }
    };
}

// or the ES6 => form
var when =
    (predicate,fn) =>
        (...args) =>
            predicate( ...args ) ? fn( ...args ) : undefined;
```

Vamos misturar `when(..)` com alguns outros utilitários auxiliares que vimos anteriormente neste capítulo, tornar o `printIf(..)` livre de pontos:

```js
var printIf = uncurry( partialRight( when, output ) );
```

Veja como fizemos: Aplicamos parcialmente à direita o método `output` como segundo argumento (`fn`) para `when(..)`, o que nos deixa com uma função ainda esperando o primeiro argumento (`predicate`). *Essa* função, quando chamada produz outra função que espera a string da mensagem, ficaria assim: `fn(predicate)(str)`.

Uma cadeia de múltiplas (duas) chamadas de função como essa se parece muito com uma função curried, então nós `uncurry(..)` este resultado para produzir uma única função que espera os dois argumentos `str` e `predicate` juntos, que corresponde à assinatura original `printIf(predicate, str)`.

Aqui está o exemplo completo (assumindo que vários utilitários já detalhados neste capítulo estejam presentes):

<a name="finalshortlong"></a>

```js
function output(msg) {
    console.log( msg );
}

function isShortEnough(str) {
    return str.length <= 5;
}

var isLongEnough = not( isShortEnough );

var printIf = uncurry( partialRight( when, output ) );

var msg1 = "Hello";
var msg2 = msg1 + " World";

printIf( isShortEnough, msg1 );         // Hello
printIf( isShortEnough, msg2 );

printIf( isLongEnough, msg1 );
printIf( isLongEnough, msg2 );          // Hello World
```

Esperançosamente, a prática da PF de codificação de estilo sem pontos está começando a fazer um pouco mais de sentido. Ainda será necessária muita prática para treinar a si mesmo para pensar dessa forma naturalmente. **E você ainda terá que fazer julgamentos** para saber se a codificação sem pontos vale a pena, bem como até que ponto irá beneficiar a legibilidade do seu código.

O que você acha? Pontos ou nenhum ponto para você?

**Observação:** Quer mais prática com codificação de estilo sem pontos? Revisitaremos essa técnica no [Capítulo 4, "Revisitando pontos"](ch4.md/#revisiting-points), com base no conhecimento recente da composição de funções.

## Resumo

A aplicação parcial é uma técnica para reduzir a aridade (ou seja, o número esperado de argumentos para uma função) criando uma nova função em que alguns dos argumentos são predefinidos.

Currying é uma forma especial de aplicação parcial em que a aridade é reduzida a 1, com uma cadeia de chamadas de função em cadeia sucessivas, cada uma com um argumento. Depois que todos os argumentos foram especificados por essas chamadas de função, a função original é executada com todos os argumentos coletados. Você também pode desfazer um currying.

Outros utilitários importantes como `unary(..)`, `identity(..)` e `constant(..)` fazem parte da caixa de ferramentas base para a PF.

Livre de pontos é um estilo de escrever código que elimina o detalhamento desnecessário de parâmetros de mapeamento ("pontos") para argumentos, com o objetivo de tornar o código mais fácil de ler/entender.

Todas essas técnicas distorcem as funções para que possam funcionar juntas de maneira mais natural. Com suas funções configuradas de forma compatível agora, o próximo capítulo ensinará como combiná-las para modelar os fluxos de dados por meio de seu programa.
