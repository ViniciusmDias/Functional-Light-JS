# Functional-Light JavaScript
# Prefácio

> *A monad is just a monoid in the category of endofunctors.*
> *Uma mônada é apenas um monóide na categoria de endofunctors.*

Did I just lose you? Don't worry, I'd be lost, too! All those terms that only mean something to the already-initiated in Functional Programming&trade; (FP) are just jumbled nonsense to many of the rest of us.

Eu acabei de perder você? Não se preocupe, eu também estaria perdido! Todos esses termos que só significam algo para os já iniciados no trabalho em Programação Funcional (PF), são apenas um disparate confuso para muitos de nós. 

This book is not going to teach you what those words mean. If that's what you're looking for, keep looking. In fact, there are already plenty of great books that teach FP the *right way*, from the top-down. Those words have important meanings and if you formally study FP in-depth, you'll absolutely want to get familiar with them.

Este livro não vai te ensinar o que essas palavras significam. Se é isso uqe está procurando, continue procurando. Na verdade, já existem muitos livros excelentes que ensinam a PF da *maneira certa*, de cima para baixo. Essas palavras têm significados importantes e se você estudar formalmente a PF em profundidade, com certeza vai querer se familiarizar com elas.

But this book is going to approach the topic quite differently. I'm going to present fundamental FP concepts from the ground-up, with fewer special or non-intuitive terms than most approaches to FP. We'll try to take a practical approach to each principle rather than a purely academic angle. **There will be terms**, no doubt. But we'll be careful and deliberate about introducing them and explaining why they're important.

Mas este livro abordará o assunto de maneira bem diferente. Vou apresentar conceitos fundamentais da PF desde o início, com menos termos especiais ou não intuitivos do que a maioria das abordagens da PF. Tentaremos adotar uma abordagem prática para cada princípio, em vez de um ângulo puramente acadêmico. **Haverá prazos**, sem dúvida. Mas seremos cuidadosos e ponderados ao apresentá-los e explicar por que são importantes.

Sadly, I am not a card-carrying member of the FP Cool Kids Club. I've never been formally taught anything about FP. And though I have a CS academic background and I am decent at math, mathematical notation is not how my brain understands programming. I have never written a line of Scheme, Clojure, or Haskell. I'm not an old-school Lisp'r.

Infelizmente, não sou membro do clube de crianças legais da PF. Nunca fui formalmente ensinado nada sobre PF. E embora eu tenha formação acadêmica em ciência da computação e seja decente em matemática, a notação matemática não é a forma como meu cérebro entende programação. Nunca escrevi uma linha de Scheme, Clojure ou Haskell. Não sou um Lisp'r da velha escola.

I *have* attended countless conference talks about FP, each one with the desperate clinging hope that finally, *this time* would be the time I understood what this whole functional programming mysticism is all about. And each time, I came away frustrated and reminded that those terms got all mixed up in my head and I had no idea if or what I learned. Maybe I learned things. But I couldn't figure out what those things were for the longest time.

Eu *já* participei de inúmeras palestras de conferências sobre PF, cada uma com a esperança desesperada de que finalmente, *desta vez* seria o momento em que eu entendesse do que se trata todo esse misticismo de programação funcional. E a cada vez, ficava frustrado e lembrava que esses termos se misturavam na minha cabeça e não tinha ideia se ou o que havia aprendido. Talvez eu tenha aprendido coisas. Mas eu não consegui descobrir o que eram essas coisas por muito tempo.

Little by little, across those various exposures, I teased out bits and pieces of important concepts that seem to just come all too naturally to the formal FPer. I learned them slowly and I learned them pragmatically and experientially, not academically with appropriate terminology. Have you ever known a thing for a long time, and only later found out it had a specific name you never knew!?

Pouco a pouco, através dessas várias exposições, descobri pedaços de conceitos importantes que parecem vir muito naturalmente para o programador funcional mais formal. Eu os aprendi lentamente e os aprendi pragmaticamente e experiencialmente, não academicamente com a terminologia apropriada. Você já conhece uma coisa há muito tempo e só depois descobriu que tinha um nome específico que você nunca conheceu!?

Maybe you're like me; I heard terms such as "map-reduce" around industry segments like "big data" for years with no real idea what they were. Eventually I learned what the `map(..)` function did -- all long before I had any idea that list operations were a cornerstone of the FPer path and what makes them so important. I knew what *map* was long before I ever knew it was called `map(..)`.

Talvez você seja como eu. Eu ouvi os termos como "map-reduce" em segmentos da indústria como "big data" por anos, sem nenhuma ideia real do que eles eram. Eventualmente eu aprendi o que a função `map(..)` fazia, muito antes de eu ter qualquer ideia de que as operações de lista eram a base do caminho do programador funcional e o que as torna tão importante. Eu sabia o que o *map* era muito antes de saber que se chamava `map(..)`.

Eventually I began to gather all these tidbits of understanding into what I now call "Functional-Light Programming" (FLP).

Por fim, comecei a reunir todos esses pequenos fragmentos de compreensão no que agora chamo de "Functional-Light Programming" (FLP).

## Mission

## Missão

But why is it so important for you to learn functional programming, even the light form?

Mas por que é tão importante que você aprenda programação funcional, até mesmo a forma leve?

I've come to believe something very deeply in recent years, so much so you could *almost* call it a religious belief. I believe that programming is fundamentally about humans, not about code. I believe that code is first and foremost a means of human communication, and only as a *side effect* (hear my self-referential chuckle) does it instruct the computer.

Passei a acreditar em algo muito profundamente nos últimos anos, tanto que você poderia *quase* chamar de crença religiosa. Eu acredito que a programação é fundamentalmente sobre humanos, não sobre código. Eu acredito que o código é antes de mais nada um meio de comunicação humana, e apenas como um *efeito colateral* (ouça minha risada maligna) ele instrui o computador.

The way I see it, functional programming is at its heart about using patterns in your code that are well-known, understandable, *and* proven to keep away the mistakes that make code harder to understand. In that view, FP -- or, ahem, FLP! -- might be one of the most important collections of tools any developer could acquire.

Ao meu ver, a programação funcional é fundamental para o uso de padrões em seu código que são bem conhecidos, compreensíveis *e* comprovadamente afastados dos erros que tornam o código mais difícil de entender. Nesse ponto de vista, PF, ou, ahem, FLP! Pode ser uma das mais importantes coleções de ferramentas que qualquer desenvolvedor pode adquirir.

> The curse of the monad is that... once you understand... you lose the ability to explain it to anyone else.
>
> Douglas Crockford 2012 "Monads and Gonads"
>
> https://www.youtube.com/watch?v=dkZFtimgAcM

> A maldição da mônada é que... uma vez que você entende... você perde a habilidade de explicá-la para qualquer outra pessoa.
>
> Douglas Crockford 2012 "Mônadas e gônadas"
>
> https://www.youtube.com/watch?v=dkZFtimgAcM

I hope this book "Maybe" breaks the spirit of that curse, even though we won't talk about "monads" until the very end in the appendices.

Espero que este livro "Talvez" quebre o espírito dessa maldição, embora não falemos sobre "mônadas" até o final dos apêndices.

The formal FPer will often assert that the *real value* of FP is in using it essentially 100%: it's an all-or-nothing proposition. The belief is that if you use FP in one part of your program but not in another, the whole program is polluted by the non-FP stuff and therefore suffers enough that the FP was probably not worth it.

O programador funcional formal afirma que o *valor real* da PF está em usá-lo essencialmente em 100%, é uma proposição de tudo ou nada. 

I'll say unequivocally: **I think that absolutism is bogus**. That's as silly to me as suggesting that this book is only good if I use perfect grammar and active voice throughout; if I make any mistakes, it degrades the entire book's quality. Nonsense.

Direi de forma inequívoca: **Acho que o absolutismo é falso**. Isso é tão bobo para mim, quanto sugerir que este livro só é bom se eu usar a gramática perfeita e a voz ativa o tempo todo, que se eu cometer algum erro, isso prejudicará a qualidade do livro inteiro. Absurdo.

The better I am at writing in a clear, consistent voice, the better your reading experience will be. But I'm not a 100% perfect author. Some parts will be better written than others. The parts where I can still improve are not going to invalidate the other parts of this book which are useful.

Quanto melhor eu escrever com uma voz clara e consistente, melhor será sua experiência de leitura. Mas não sou um autor 100% perfeito. Algumas partes serão melhor escritas do que outras. As partes que ainda posso melhorar não invalidarão as outras partes deste livro que são úteis.

And so it goes with our code. The more you can apply these principles to more parts of your code, the better your code will be. Use them well 25% of the time, and you'll get some good benefit. Use them 80% of the time, and you'll see even more benefit.

E assim acontece com nosso código. Quanto mais você aplicar esses princípios a mais partes do seu código, melhor será o seu código. Use-os bem 25% do tempo e você obterá bons proveitos. Use-os 80% do tempo e você terá ainda mais proveitos.

With perhaps a few exceptions, I don't think you'll find many absolutes in this text. We'll instead talk about aspirations, goals, principles to strive for. We'll talk about balance and pragmatism and trade-offs.

Tirando algumas exceções, não acho que você encontrará muitas certezas neste texto. Em vez disso, falaremos sobre aspirações, objetivos e princípios pelos quais lutar. Falaremos sobre equilíbrio, pragmatismo e compensações.

Welcome to this journey into the most useful and practical foundations of FP. We both have plenty to learn!

Bem-vindo a esta jornada para os fundamentos mais úteis e práticos da PF. Ambos temos muito o que aprender!
