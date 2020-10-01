# Functional-Light JavaScript
# Capítulo 1: Porquê Programação Funcional?
 
> Programador funcional: (substantivo) Aquele que nomeia as variáveis "x", nomeaia as funções "f" e nomeia os padrões de código como "prépromorfismo zigohistomórfico"
>
> James Iry @jamesiry 5/13/15
>
> https://twitter.com/jamesiry/status/598547781515485184
 
Programação Funcional (PF) não é, de forma alguma, um novo conceito. Está presente em quase toda a história da programação. No entanto, não estou certo se é justo dizer, mas... com certeza não parecia o conceito dominante no mundo dos desenvolvedores até, talvez, os últimos anos. Acho que a PF tem sido mais o reino dos acadêmicos.
 
No entanto, tudo isso está mudando. Uma onda de interesse está crescendo em torno da PF, não apenas no nível de linguagens, mas também em bibliotecas e frameworks. Provavelmente você pode estar lendo este texto porque finalmente percebeu que a PF é algo que você não pode mais ignorar. Ou talvez, você seja como eu e já tentou aprender PF muitas vezes no passado, mas teve dificuldade em passar por todos os termos ou notações matemáticas.
 
O objetivo deste primeiro capítulo é responder a perguntas como "Por que devo usar o estilo de PF no meu código?" e "Como o Functional-Light Javascript se compara ao que outros dizem sobre a PF?" Depois de preenchermos esse fundamento, vamos descobrir ao longo do restante do livro, peça por peça, as técnicas e padrões para escrever JS no estilo Functional-Light.
 
## De Relance
  
Vamos brevemente ilustrar a noção de "Functional-Light JavaScript" com um antes e depois de um trecho código. Considere:
 
```js
var numbers = [4,10,0,27,42,17,15,-6,58];
var faves = [];
var magicNumber = 0;
 
pickFavoriteNumbers();
calculateMagicNumber();
outputMsg();                // The magic number is: 42
 
// ***************
 
function calculateMagicNumber() {
   for (let fave of faves) {
       magicNumber = magicNumber + fave;
   }
}
 
function pickFavoriteNumbers() {
   for (let num of numbers) {
       if (num >= 10 && num <= 20) {
           faves.push( num );
       }
   }
}
 
function outputMsg() {
   var msg = `The magic number is: ${magicNumber}`;
   console.log( msg );
}
```
 
Agora considere um estilo muito diferente que atinge exatamente o mesmo resultado:
 
```js
var sumOnlyFavorites = FP.compose( [
   FP.filterReducer( FP.gte( 10 ) ),
   FP.filterReducer( FP.lte( 20 ) )
] )( sum );
 
var printMagicNumber = FP.pipe( [
   FP.reduce( sumOnlyFavorites, 0 ),
   constructMsg,
   console.log
] );
 
var numbers = [4,10,0,27,42,17,15,-6,58];
 
printMagicNumber( numbers );        // The magic number is: 42
 
// ***************
 
function sum(x,y) { return x + y; }
function constructMsg(v) { return `The magic number is: ${v}`; }
```
 
Uma vez que você entendeu a PF e Functional-Light, provavelmente é assim que você *leria* e processaria mentalmente esse segundo trecho:
 
> Primeiro estamos criando uma função chamada `sumOnlyFavorites(..)`, que é uma combinação de três outras funções. Combinamos dois filtros, um verificando se um valor é maior ou igual a 10 e outro se é menor ou igual a 20. Em seguida, incluímos o redutor de `soma(..)` na composição do transdutor. A função resultante `sumOnlyFavorites(..)` é um redutor que verifica se um valor passa em ambos os filtros e, em caso afirmativo, adiciona o valor a um valor acumulador.
>
> Então fazemos outra função chamada `printMagicNumber(..)`, que primeiro reduz uma lista de números usando o redutor `sumOnlyFavorites(..)` que acabamos de definir, resultando em uma soma de apenas números que passaram nas verificações de *favoritos*. Então `printMagicNumber(..)` adiciona essa soma final em `constructMsg(..)`, que cria um valor de string que finalmente vai para `console.log(..)`.
 
Todas essas peças móveis *falam* com um desenvolvedor de PF em maneiras que, agora, provavelmente não lhe parecem familiares. Este livro irá ajudá-lo a *falar* com esse mesmo tipo de raciocínio, para que isso seja tão legível para você quanto qualquer outro código, se não até mais!
 
Algumas outras observações rápidas sobre essa comparação de código:
 
* É provável que, para muitos leitores, o primeiro trecho pareça mais confortável/legível/sustentável do que o último. Está tudo bem se for esse o seu caso. Você está exatamente no lugar certo. Tenho certeza de que, se você seguir todo o livro e praticar tudo o que falamos, esse segundo trecho de código acabará se tornando muito mais natural, talvez até melhor!
 
* Você pode ter feito a tarefa de maneira significativa ou totalmente diferente de qualquer um dos trechos de código apresentados. Tudo bem também. Este livro não será prescritivo ao ditar que você deve fazer algo de uma maneira específica. O objetivo é ilustrar os prós e contras de vários padrões e permitir que você tome essas decisões. No final deste livro, como você abordaria a tarefa pode ficar um pouco mais perto do segundo trecho de código do que agora.
 
* Também é possível que você já seja um desenvolvedor de PF experiente, que está examinando o início deste livro para ver se há algo útil para ler. Certamente, esse segundo trecho de código tem algumas partes bem familiares. Mas também aposto que você pensou, "Hmmm, eu não teria feito *dessa* maneira..." em alguns momentos. Está tudo bem e completamente aceitável.
 
   Este não é um livro tradicional e canônico da PF. Às vezes, parecemos bastante heréticos em nossas abordagens. Estamos buscando um equilíbrio pragmático entre os benefícios claros e inegáveis da PF e a necessidade de entregar JS viável e sustentável sem ter que enfrentar uma montanha assustadora de matemática/notação/terminologia. Isto não é a *sua* PF, é "Functional-Light Javascript".
 
Quaisquer que sejam seus motivos para ler este livro, seja bem-vindo!
 
## Confiança
 
Tenho uma premissa muito simples que está por trás de tudo que eu faço como professor de desenvolvimento de software (em JavaScript): código em que você não pode confiar é código que você não entende. O inverso também é verdade como: um código que você não entende, é um código que você não pode confiar. Além disso, se você não puder confiar ou compreender seu código, não poderá ter nenhuma confiança de que o código que você escreve é apropriado para a tarefa. Você executa o programa e basicamente cruza os dedos.
 
O que eu quero dizer com confiança? Quero dizer que você pode verificar, lendo e raciocinando, não apenas executando, que entende o que um pedaço de código *fará*; você não está apenas contando com o que ele *deve* fazer. Mais frequentemente do que parece prudente, tendemos a confiar na execução do conjunto de testes para verificar a exatidão de nossos programas. Não quero sugerir que os testes sejam ruins. Mas acho que devemos desejar ser capazes de entender nosso código bem o suficiente para sabermos que o conjunto de testes será aprovado antes de ser executado.
 
As técnicas que formam a fundação da PF são projetadas com a mentalidade para termos muito mais confiança em nossos programas apenas por lê-los. Alguém que entenda de PF e que seja disciplinado o suficiente para usá-lo diligentemente em seus programas, irá escrever um código que ele, **e outros**, possam ler e verificar se o programa fará o que eles desejam.
 
A confiança também aumenta quando usamos técnicas que evitam ou minimizam as prováveis fontes de bugs. Esse é talvez um dos maiores argumentos de venda da PF: os programas da PF geralmente têm menos bugs e os bugs que existem geralmente estão nos lugares mais óbvios, então são mais fáceis de encontrar e corrigir. O código da PF tende a ser mais resistente a bugs -- certamente não à prova de bugs.
 
Ao longo deste livro, você começará a desenvolver com mais confiança no código que escreve, porque usará padrões e práticas já comprovadas; e você evitará as mais comuns causas de bugs nos programas!
 
## Comunicação
 
Por que a Programação Funcional é importante? Para responder a isso, precisamos dar um grande passo para trás e falar sobre por que a programação em si é importante.
 
Você pode ficar surpreso ao ouvir isso, mas não acredito que o código seja basicamente um conjunto de instruções para o computador. Na verdade, acho que o fato de o código instruir o computador é quase que um acidente feliz.
 
Acredito profundamente que o papel mais importante do código é ser um meio de comunicação com outros seres humanos.
 
Você provavelmente sabe por experiência que uma grande parte do seu tempo gasto "codificando" é, na realidade, gasto lendo o código existente. Pouquíssimos de nós temos o privilégio de gastar todo, ou a maior parte do nosso tempo, simplesmente elaborando todos os novos códigos e nunca lidando com códigos que outros (ou o nosso próprio eu no passado) escreveram.
 
É amplamente difundido que os desenvolvedores gastam 70% do tempo de manutenção do código lendo para entendê-lo. Isso é revelador. 70%. Não é de se admirar que a média global de linhas de código que um programador escreve por dia seja cerca de 10. Passamos até 7 horas do nosso dia apenas lendo o código para descobrir onde essas 10 linhas devem ir!
 
Precisamos nos concentrar muito mais na legibilidade do nosso código. E, à propósito, legibilidade não se trata apenas de menos caracteres. A legibilidade é, na verdade, mais afetada pela intimidade.<a href="#user-content-footnote-1"><sup>1</sup></a>
 
Se vamos gastar nosso tempo preocupados em fazer um código que seja mais legível e compreensível, a PF é fundamental nisso. Os princípios da PF são bem estabelecidos, profundamente estudados, controlados e, provavelmente, podem ser verificados. Dedicar algum tempo para aprender e empregar esses princípios de PF, acabará por levar a um código mais fácil e reconhecível para você e outras pessoas. O aumento da familiaridade do código e a conveniência desse reconhecimento irão melhorar a legibilidade do código.
 
Por exemplo, depois de aprender o que `map(..)` faz, você será capaz de identificá-lo e entendê-lo quase que instantaneamente quando o vir em qualquer programa. Mas toda vez que você vir um loop `for`, terá que ler todo o loop para entendê-lo. A sintaxe do loop `for` pode ser familiar, mas a essência do que ele está fazendo não é; isso tem que ser *lido*, todas as vezes.
 
Por ter mais código reconhecível de relance e, portanto, gastar menos tempo tentando descobrir o que o código está fazendo, nosso foco fica livre para pensar sobre os níveis mais elevados da lógica do programa; de qualquer maneira, essas são as coisas mais importantes e que mais precisam da nossa atenção.
 
PF (pelo menos, sem toda aquela terminologia botando ela para baixo) é uma das ferramentas mais eficazes para a elaboração de código legível. *É* por isso que é tão importante.
 
## Legibilidade
 
Legibilidade não é uma característica binária. É um fator humano altamente subjetivo que descreve nossa relação com o código. E vai variar naturalmente com o tempo, à medida que nossas habilidades e compreensão evoluem. Eu experimentei efeitos semelhantes aos da figura a seguir e, enfaticamente, outras pessoas com quem conversei também.
 
<p align="center">
   <img src="images/fig17.png" width="50%">
</p>
 
Você pode sentir esses mesmos efeitos conforme você avança no livro. Mas tenha coragem; se você persistir, a curva volta a subir!
 
*Imperativo* descreve o código que a maioria de nós provavelmente já escreve naturalmente; é focado em instruir precisamente o computador *como* fazer algo. Código declarativo -- o tipo que aprenderemos a escrever, que segue os princípios da PF - é o código mais focado em descrever *qual* o resultado.
 
Vamos revisitar os dois trechos de código apresentados anteriormente neste capítulo.
 
O primeiro trecho é imperativo, focado quase inteiramente em *como* fazer as tarefas; está repleto de declarações `if`, loops `for`, variáveis temporárias, reatribuições, mutações de valor, chamadas de função com efeitos colaterais e fluxo de dados implícito entre funções. Você certamente *pode* rastrear sua lógica para ver como os números fluem e mudam para o estado final, mas não é nem um pouco claro ou direto.
 
O segundo trecho é mais declarativo; ele acaba com a maioria das técnicas imperativas mencionadas acima. Observe que não há condicionais, loops, efeitos colaterais, reatribuições, ou mutações; em vez disso, ele emprega padrões bem conhecidos (para o mundo da PF, pelo menos!) e padrões confiáveis, como filtragem, redução, transdução e composição. O foco muda do nível inferior *como* para o nível superior *qual* de resultados.
 
Em vez de mexer com uma instrução `if` para testar um número, delegamos isso a um utilitário PF bem conhecido como `gte(..)` (maior-que-ou-igual-a), e então nos concentramos na tarefa mais importante de combinar esse filtro com outro filtro e uma função de soma.
 
Além disso, o fluxo de dados por meio do segundo programa é explícito:
 
1. Uma lista de números vai para `printMagicNumber(..)`
2. Um de cada vez, esses números são processados por `sumOnlyFavorites(..)`, resultando em um único número total de apenas nossos tipos de números favoritos.
3. Esse total é convertido em uma string de mensagem com `construcMsg(..)`.
4. A string de mensagem é impressa no console com `console.log(..)`.
 
Você ainda pode sentir que essa abordagem é complicada e que o trecho imperativo era mais fácil de entender. Você está muito mais acostumado a ele; a familiaridade tem uma influência profunda em nossos julgamentos de legibilidade. No final deste livro, porém, você terá internalizado os benefícios da abordagem declarativa do segundo trecho de código e essa familiaridade dará vida a sua legibilidade.
 
Eu sei que pedir para você acreditar neste ponto é como pedir para você ter fé.
 
É preciso muito mais esforço, e às vezes mais código, para melhorar sua legibilidade e minimizar ou eliminar muitos dos erros que levam a bugs, da forma que eu estou sugerindo. Sinceramente, quando comecei a escrever este livro, nunca poderia ter escrito (ou compreendido totalmente!) aquele segundo trecho de código. À medida que vou avançando em minha jornada de aprendizado, ele vai se tornando mais natural e confortável.
 
Se você espera que a refatoração da PF, servirá como uma bala mágica de prata, que transformará rapidamente seu código para ser mais gracioso, elegante, inteligente, resiliente e conciso -- que isso virá fácil em um curto prazo -- infelizmente, essa não é uma expectativa realista.
 
PF é uma maneira muito diferente de pensar sobre como o código deve ser estruturado, para tornar o fluxo de dados muito mais óbvio e ajudar o leitor a seguir seu pensamento. Isso levará algum tempo. Esse esforço vale a pena, mas pode ser uma jornada árdua.
 
Muitas vezes, ainda preciso de várias tentativas de refatorar um trecho de código imperativo em um de PF mais declarativo, antes de finalizar com algo que seja claro o suficiente para eu entender depois. Descobri que a conversão para a PF é um processo lento e interativo, em vez de uma rápida mudança binária de um paradigma para outro.
 
Também aplico o teste "ensinar depois" a cada parte do código que escrevo. Depois de escrever um trecho de código, deixo-o sozinho por algumas horas ou dias, depois volto e tento lê-lo com novos olhos e finjo que preciso ensiná-lo ou explicá-lo a outra pessoa. Normalmente, fica um pouco claro e confuso nas primeiras vezes, então eu ajusto e repito!
 
Não estou tentando diminuir seu ânimo. Eu realmente quero que você corte essas ervas daninhas. Estou feliz por ter feito isso. Posso finalmente começar a ver a curva subindo em direção a uma legibilidade aprimorada. O esforço valeu a pena. Valerá a pena para você também.
 
## Perspectiva
 
Na maioria dos outros textos, a PF parece ter uma abordagem de cima para baixo, mas vamos na direção oposta: trabalhando do zero, vamos descobrir o princípios básicos fundamentais que acredito que os Programadores Funcionais formais admitiriam ser o andaime para tudo que eles fazem. Mas, na maior parte do tempo, ficaremos longe da intimidante terminologia ou notação matemática que pode frustrar os aprendizes com tanta facilidade.
 
Eu acredito que é menos importante como você chama algo e mais importante que você entenda o que é e como funciona. Isso não quer dizer que a terminologia compartilhada não tenha importância -- sem dúvidas ela facilita a comunicação entre profissionais experientes. Mas para o aprendiz, descobri que pode ser uma distração.
 
Portanto, este livro tentará enfocar mais os conceitos básicos e menos nas extravagâncias. Isso não quer dizer que não haverá terminologia; definitivamente haverá. Mas não se deixe envolver por palavras sofisticadas. Sempre que necessário, olhe além delas para as ideias.
 
Eu chamo a prática menos formal aqui de "Programação Functional-Light" porque acho que o formalismo que a verdadeira PF sofre pode ser bastante pesado se você não estiver acostumado com o pensamento formal. Não estou apenas adivinhando; está é minha própria história pessoal. Mesmo depois de ensinar PF e escrever este livro, ainda posso dizer que o formalismo de termos e notações em PF é muito, muito difícil de processar para mim. Eu tentei e tentei, mas não consigo absorver muito isso.
 
Conheço muitos Programadores Funcionais que acreditam que o próprio formalismo ajuda a aprender. Mas eu acho que há claramente um penhasco onde isso só se torna verdade quando você atinge um certo conforto com o formalismo. Se por acaso você já tem formação matemática ou até mesmo um pouco de experiência em Ciência da Computação, isso pode ser mais natural para você. Mas alguns de nós não temos, e não importa o quanto tentemos, o formalismo continua atrapalhando.
 
Portanto, este livro apresenta os conceitos nos quais acredito que a PF é construído, mas chega lá dando-lhe um impulso de baixo para escalar a parede do penhasco, em vez de gritar condescendentemente com você do topo, estimulando-o a descobrir como subir conforme você avança.
 
## Como encontrar o equilíbrio
 
Se você já está programando há muito tempo, é provável que já tenha ouvido a frase "YAGNI" antes: "You Ain't Gonna Need It" (Você não vai precisar disso). Esse princípio vem principalmente da programação extrema e enfatiza o alto risco e o custo de criar um recurso antes que ele seja necessário.
 
Às vezes, achamos que precisamos de um recurso no futuro, construímos ele agora acreditando que será mais fácil de fazer conforme construímos outras coisas, então percebemos que erramos e o recurso não era necessário, ou precisava ser bem diferente. Outras vezes, acertamos, mas criamos um recurso muito cedo e utilizamos o tempo dos recursos que são genuinamente necessários agora; nos sujeitamos a perder uma oportunidade ao diluir a nossa energia.
 
YAGNI nos desafia a lembrar: mesmo que seja contra-intuitivo em uma situação, devemos frequentemente adiar a construção de algo até que ele seja realmente necessário. Temos a tendência de exagerar nossas estimativas mentais sobre o custo futuro da refatoração de adicioná-lo mais tarde, quando sentirmos a necessidade. Provavelmente, não será tão difícil fazer mais tarde como podemos supor.
 
No que se refere à programação funcional, eu ofereceria este conselho: haverá muitos padrões interessantes e atraentes discutidos neste texto, mas só porque você acha algum padrão interessante para aplicar, pode ser que não seja necessariamente apropriado fazê-lo em uma determinada parte do seu código.
 
É aqui que vou diferir de muitos Programadores Funcionais formais: só porque você *pode* aplicar a PF a algo, não significa que você *deve* aplicar a PF nele. Além disso, há muitas maneiras de dividir um problema, embora você possa ter aprendido uma abordagem mais sofisticada que seja mais "preparada para manutenção e extensibilidade no futuro", pode ser que um padrão da PF mais simples seja mais do que o suficiente nesse local.
 
Geralmente, eu recomendo buscar o equilíbrio no que você codifica e ser conservador na aplicação dos conceitos da PF conforme você vai pegando o jeito das coisas. O princípio do YAGNI é o padrão ao decidir se um determinado padrão ou abstração ajudará aquela parte do código a ser mais legível ou se está apenas inserindo sofisticação inteligente que (ainda) não está validada.
 
> Lembrete, qualquer ponto de extensibilidade que nunca será usado não é somente um esforço desperdiçado, ele provavelmente também atrapalhará o seu caminho
>
> Jeremy D. Miller @jeremydmiller 2/20/15
>
> https://twitter.com/jeremydmiller/status/568797862441586688
 
Lembre-se de que cada linha de código que você escreve tem um custo de leitura associado a ela. Esse leitor pode ser outro membro da equipe ou até mesmo o seu futuro eu. Nenhum desses leitores ficará impressionado com sofisticação excessivamente inteligente e desnecessária apenas para exibir a sua maestria na PF.
 
O melhor código é aquele que é mais legível no futuro, porque atinge exatamente o equilíbrio certo entre o que pode/deve ser (idealismo) e o que é necessário ser (pragmatismo).
 
## Recursos
 
Usei muitos recursos diferentes para poder compor este texto. Eu acredito que você também pode tirar algum benefício deles, então eu queria tirar um tempo para elencá-los.
 
### Livros
 
Alguns livros de PF/JavaScript que você definitivamente deveria ler:
 
* [O Guia mais adequado para programação funcional do Professor Frisby](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch1.html) por [Brian Lonsdorf](https://twitter.com/drboolean)
* [JavaScript estendido](https://leanpub.com/javascriptallongesix) por [Reg Braithwaite](https://twitter.com/raganwald)
* [JavaScript Funcional](http://shop.oreilly.com/product/0636920028857.do) por [Michael Fogus](https://twitter.com/fogus)
 
### Blogs/sites
 
Alguns outros autores e conteúdo que você deve verificar:
 
* [Vídeos de funções divertidas e divertidas](https://www.youtube.com/watch?v=BMUiFMZr7vk) por [Mattias P Johansson](https://twitter.com/mpjme)
* [Awesome FP JS](https://github.com/stoeffel/awesome-fp-js)
* [Kris Jenkins](http://blog.jenkster.com/2015/12/what-is-functional-programming.html)
* [Eric Elliott](https://medium.com/@_ericelliott)
* [James A Forbes](https://james-forbes.com/)
* [James Longster](https://github.com/jlongster)
* [André Staltz](http://staltz.com/)
* [Functional Programming Jargon](https://github.com/hemanth/functional-programming-jargon#functional-programming-jargon)
* [Exercícios de Programação Funcional](https://github.com/InceptionCode/Functional-Programming-Exercises)
 
### Bibliotecas
 
Os trechos de código neste livro, em grande parte, não dependem de bibliotecas. Cada operação que descobrirmos, derivaremos como implementá-la em JavaScript simples e autônomo. No entanto, à medida que você começa a construir mais de seu código real com PF, futuramente você desejará uma biblioteca para fornecer versões otimizadas e altamente confiáveis desses utilitários comumente aceitos.
 
A propósito, você precisa verificar a documentação das funções da biblioteca que usa para garantir que sabe como elas funcionam. Haverá muitas semelhanças em muitas delas com o código que construímos neste texto, mas sem dúvida terá algumas diferenças, mesmo entre bibliotecas populares.
 
Aqui estão algumas bibliotecas de PF para JavaScript que são um ótimo lugar para começar sua exploração:
 
* [Ramda](http://ramdajs.com)
* [lodash/fp](https://github.com/lodash/lodash/wiki/FP-Guide)
* [functional.js](http://functionaljs.com/)
* [Immutable.js](https://github.com/facebook/immutable-js)
 
[O Apêndice C dá uma olhada mais detalhada nessas bibliotecas](apC.md/#stuff-to-investigate) e em outras.
 
## Resumo
 
Você pode ter vários motivos para começar a ler este livro e diferentes expectativas sobre o que obterá com ele. Este capítulo explica por que quero que você leia o livro e o que quero que você tire dessa jornada. Ele também ajuda você a convencer outras pessoas (como seus colegas desenvolvedores) do por que eles devem viajar nessa jornada com você!
 
A programação funcional consiste em escrever código baseado em princípios comprovados, para que possamos ganhar um nível de confiança e segurança sobre o código que escrevemos e lemos. Não deveríamos nos contentar em escrever um código que ansiosamente *esperamos* que funcione e então respirar abruptamente de alívio quando o conjunto de testes for aprovado. Devemos *saber* o que ele fará antes de executá-lo e devemos estar absolutamente confiantes de que comunicamos todas essas ideias em nosso código para o benefício de outros leitores (incluindo nosso futuro eu).
 
Este é o coração do Functional-Light JavaScript. O objetivo é aprender a se comunicar efetivamente com o nosso código, mas não ter que se sufocar sob montanhas de notação e terminologia para chegar lá.
 
A jornada para aprender programação funcional começa com um entendimento profundo da natureza do que é uma função. É isso que abordaremos no próximo capítulo.
 
----
 
<a name="footnote-1"><sup>1</sup></a>Buse, Raymond P. L., and Westley R. Weimer. “Aprendendo uma métrica para legibilidade do código.” IEEE Transactions on Software Engineering, IEEE Press, Julho de 2010, dl.acm.org/citation.cfm?id=1850615.
 
 

