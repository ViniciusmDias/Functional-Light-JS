# Functional-Light JavaScript
# Prefácio

> *Uma mônada é apenas um monóide na categoria de endofunctors.*

Eu acabei de perder você? Não se preocupe, eu também estaria perdido! Todos esses termos que só significam algo para quem já iniciou no trabalho em Programação Funcional (PF), são apenas um disparate confuso para muitos de nós. 

Este livro não vai te ensinar o que essas palavras significam. Se é isso que está procurando, continue procurando. Na verdade, já existem muitos livros excelentes que ensinam a PF da *maneira certa*, de cima para baixo. Essas palavras têm significados importantes e se você estudar formalmente a PF em profundidade, com certeza vai querer se familiarizar com elas.

Mas este livro abordará o assunto de maneira bem diferente. Vou apresentar conceitos fundamentais da PF desde o início, com menos termos especiais ou não intuitivos do que a maioria das abordagens da PF. Tentaremos adotar uma abordagem prática para cada princípio, em vez de um ângulo puramente acadêmico. **Haverá prazos**, sem dúvida. Mas seremos cuidadosos e ponderados ao apresentá-los e explicar por que são importantes.

Infelizmente, não sou membro do clube de pessoas legais da PF. Nunca fui formalmente ensinado nada sobre PF. E embora eu tenha formação acadêmica em ciência da computação e seja decente em matemática, a notação matemática não é a forma como meu cérebro entende programação. Nunca escrevi uma linha de Scheme, Clojure ou Haskell. Não sou um Lisp'r da velha escola.

Eu *já* participei de inúmeras palestras de conferências sobre PF, cada uma com a esperança desesperada de que finalmente, *desta vez* seria o momento em que eu entendesse do que se trata todo esse misticismo de programação funcional. E a cada vez, ficava mais frustrado, enquanto lembrava que esses termos se misturavam na minha cabeça e não tinha ideia se eu havia aprendido. Talvez eu tenha aprendido algumas coisas, mas eu não consegui descobrir o que eram essas coisas por muito tempo.

Pouco a pouco, através dessas várias exposições, descobri pedaços de conceitos importantes que parecem vir muito naturalmente para o programador funcional mais formal. Eu os aprendi lentamente, os aprendi pragmaticamente e experiencialmente, não academicamente com a terminologia apropriada. Você já conheceu uma coisa há muito tempo e só depois descobriu que tinha um nome específico que você nunca conheceu!?

Talvez você seja como eu. Eu ouvi os termos como "map-reduce" em segmentos da indústria como "big data" por anos, sem nenhuma ideia real do que eles eram. Eventualmente eu aprendi o que a função `map(..)` fazia, muito antes de eu ter qualquer ideia de que as operações de lista eram a base do caminho do programador funcional e o que as torna tão importante. Eu sabia o que o *map* era muito antes de saber que se chamava `map(..)`.

Por fim, comecei a reunir todos esses pequenos fragmentos de compreensão no que agora chamo de "Functional-Light Programming" (FLP).

## Missão

Mas por que é tão importante que você aprenda programação funcional, até mesmo a forma "light"?

Passei a acreditar em algo muito profundamente nos últimos anos, tanto que você poderia *quase* chamar de crença religiosa. Eu acredito que a programação é fundamentalmente sobre humanos, não sobre código. Eu acredito que o código é antes de mais nada um meio de comunicação humana, e apenas como um *efeito colateral* (ouça minha risada maligna) ele instrui o computador.

Ao meu ver, a programação funcional é fundamental para o uso de padrões em seu código que são bem conhecidos, compreensíveis *e* comprovadamente afastados dos erros que tornam o código mais difícil de entender. Nesse ponto de vista, PF, ou, ahem, FLP! Pode ser uma das mais importantes coleções de ferramentas que qualquer desenvolvedor pode adquirir.

> A maldição da mônada é que... uma vez que você entende... você perde a habilidade de explicá-la para qualquer outra pessoa.
>
> Douglas Crockford 2012 "Mônadas e gônadas"
>
> https://www.youtube.com/watch?v=dkZFtimgAcM

Espero que este livro "Talvez" quebre o espírito dessa maldição, embora não falaremos sobre "mônadas" até o final dos apêndices.

O programador funcional formal afirma que o *valor real* da PF está em usá-lo essencialmente em 100%, é uma proposição de tudo ou nada. 

Direi de forma inequívoca: **Acho que o absolutismo é falso**. Isso é tão bobo para mim, quanto sugerir que este livro só é bom se eu usar a gramática perfeita e a voz ativa o tempo todo, que se eu cometer algum erro, isso prejudicará a qualidade do livro inteiro. Um absurdo.

Quanto melhor eu escrever com uma voz clara e consistente, melhor será sua experiência de leitura. Mas não sou um autor 100% perfeito. Algumas partes serão melhor escritas do que outras. As partes que ainda posso melhorar, não invalidarão as outras partes deste livro que são úteis.

E assim acontece com nosso código. Quanto mais você aplicar esses princípios a mais partes do seu código, melhor será o seu código. Use-os bem 25% do tempo e você obterá bons proveitos. Use-os 80% do tempo e você terá proveitos ainda melhores.

Tirando algumas exceções, não acho que você encontrará muitas certezas neste texto. Em vez disso, falaremos sobre aspirações, objetivos e princípios pelos quais lutar. Falaremos sobre equilíbrio, pragmatismo e compensações.

Bem-vindo a esta jornada para os fundamentos mais úteis e práticos da PF. Ambos temos muito o que aprender!
