# **Caml Light** #
Caml light é una leggera e versatile implementazione del linguaggio Caml, sviluppato intorno all'inizio degli anni '90. Il linguaggio é realizato come un compilatore di bytecode e sia l'interprete che il sistema di runtime sono sviluppati in c standard. Il linguaggio é nominato light proprio perché é facilmente spostabile sulla maggior parte di piattaforme a 32 o 64 bit.  
Caml é un meta language impiegato per descrivere la semantica di programmi scritti in altri linguaggi, perche molto semplice da usare.
Fa parte del gruppo dei **linguaggi funzionali**, cioé incentrati sul concetto di funzione nel senso matematico del termine. Poiché le funzioni possono richiamare se stesse e non esistono cambiamenti di stati, si tratta di un linguaggio basato sul **paradigma ricorsivo**.

## **Caml light:** Installazione ##

* **Linux:**
  * `$ apt install camllight`
  * download from https://caml.inria.fr/caml-light/release.en.html


* **Windows o Mac:**
  * download from https://caml.inria.fr/caml-light/release.en.html


## **Caml light:** Sintassi ##

(Gli esempi in questa guida saranno presi da un terminale linux)

### Espressioni ###
Le espressioni in caml devono tutte terminare con ';;', altrimenti caml non interpreta l'espressione e si aspetta altro:

ESEMPIO:
```
#3;;
- : int = 3
```

La seconda riga corrisponde a ciò che caml ritorna in base all'interpretazione dell'espressione data.<br>
Nella risposta il `-` sta ad indicare il nome che é stato dato al valore, in questo caso c´è un meno perche appunto non é stato dato alcun nome al valore. Per dare un nome ad un valore si usa l'operazione **let**. <br>

ESEMPIO:
```
#let n = 3;;
n : int = 3
```
Dopo i due punti caml stampa il tipo del risultato, che lo capisce senza che venga esplicitamente specificato attraverso un **inferenza di tipo**, e in fine stampa il valore dell'espressione dopo l'uguale.

### Operazioni ###
Le operazioni in caml sono le stesse di tutti gli altri linguaggi, l'unica differenza é che fa inferenza di tipo attraverso queste, quindi esistono 2 diversi modi di scrivere una stessa operazione in base a che tipo di valori la stiamo applicando:

  * Somma tra interi: +

  * Somma tra float: +.

  * Differenza tra interi: -

  * Differenza tra float: -.

  * Rapporto tra interi: /

  * Rapporto tra float: /.

  * Moltiplicazione tra interi: *

  * Moltiplicazione tra float: \*.

  NB: Le operzioni tra tipi diversi in caml non sono ammesse

### Dichiarazione di Funzioni ###
Con **let** si possono dichiarare anche funzioni.

ESEMPIO:
```
#let f n = n+2;;
f : int -> int = <fun>
```
La prima riga corrisponde a quello che bisogna scrivere per dichiarare una funzione. Dopo let servono dei nomi, il primo (`f`) é il nome della funzione, il secondo (`n`) é il parametro della funzione, ovviamente i nomi delle funzioni e dei parametri di queste sono arbitrari.<br>
La seconda riga, come sempre, corrisponde a quello che ritorna caml in base alla sua interpretazione, infatti stampa che `f` é la funzione che va da interi ad interi (`int -> int`), cioé che ha come parametri valori interi e restituisce valori interi, e che é uguale ad un valore funzionale (`<fun>`). Ogni dichiarazione di funzione ha come risultato `<fun>` perché, secondo la teoria della calcolabilità, in molti casi non é decidibile quale funzione stiamo definendo.

Ora se usiamo questa funzione `f` con il parametro 2:
```
#f 2;;
- : int = 4
```
Il risultato ovviamente sarà un intero uguale a 4.


## **Caml light:** Funzioni ##
Il caml é sensibile a come i parametri vengo definiti alla dichiarazione di una funzione, infatti questo:
```
#let f (n, m) = n + m + 3;;
f : int * int -> int = <fun>
```
é molto diverso da questo:
```
#let f n m = n + m + 3;;
f : int -> int -> int = <fun>
```
come si puo notare dalla risposta che da caml alle 2 espressioni.<br>
La prima infatti corrisponde ad una funzione `f` che ha come parametri 2 valori n e m tutti e due interi (`int * int -> ...`) e che ritorna un valore sempre intero (`... -> int`).

ESEMPIO:
```
#f(3,5);;
- : int = 11
```
La seconda é una funzione detta curryed (dal matematico americano Curry del '900), cioé una funzione con argomenti in sequenza. Significa che, prendendo come esempio il secondo caso sopra, la funzione `f` prende un primo argomento `n` intero (`int -> ...`) e restituisce una funzione che prende un argomento `m` intero (`... -> int -> ...`) e da il risultato che é a sua volta un intero (`... -> int`).

ESEMPIO:
```
# f 3 4;;
- : int = 10
```
oppure
```
#f 3;;
- : int -> int = <fun>

#g = f 3;;            (come dire #g m = 3 + m + 3;;)
g : int -> int = <fun>

#g 4;;
- : int = 10
```

ESEMPIO: Trasformare una funzione nella sua versione curryed<br>
  ```
  #let t (n,m) = 2*n + m;;
  t : int * int -> int = <fun>

  #let s n m = t (n,m);;        (s é la versione curryed di t)
  s : int -> int -> int + <fun>
  ```

Le funzioni in caml quindi posso dare come risultato altre funzioni.

### Funzioni polimorfe ###
Le funzioni polimorfe sono particolari funzioni che possono essere applicate a valori di diverso tipo.

ESEMPIO:
```
#let f n = n;;
f : 'a -> 'a = <fun>
```
La risposta di caml a questa espressione, che sarebbe la funzione identità, é che la funzione `f` é una funzione che va da un qualsiasi tipo `'a` allo stesso tipo `'a`. Da questo si capisce bene che il caml interpreta i tipi che non definisce da l'inferenza di tipi mettendo un apice e vicino una lettera qualsiasi.

ESEMPIO:
```
#let f (n,m) = (n, m+1);;
f : 'a * int -> 'a * int = <fun>

#f (3.5, 2);;
- : float * int = (3.5, 3)
```

### Funzioni Ricorsive ###
L'espressione di funzioni ricorsive in caml light spesso é necessario farla inserendo un espressione condizionale: `if e then e1 else e2`, con `e` rappresentante un valore booleano *true* o *false*, ed `e1`, `e2` aventi lo stesso tipo.<br>
Nella dichiarazione di funzioni ricorsive non basta mettere `let` ma é necessario far capire a caml che la funzione che stiamo definendo é una funzione ricorsiva (la funzione appare sia a destra che a sinistra dell'uguale), questo lo si fa con `let rec`.

ESEMPIO:
```
#let rec f n = if n = 0 then 0 else 2+f(n-1);;
f : int -> int = <fun>
```
In questo esempio la funzione ricorsiva rappresenta la funzione *f(x) = 2x* infatti:
```
#f 5;;
- : int = 10
```

## **Caml light:** Liste ##
Le liste in caml light sono strutture omogenee, cioé strutture con valori dello stesso tipo e con un ordine.<br>
Questo tipo di dato viene indicato con delle parentesi quadre, in cui, al loro interno vengono messi i valori, rigorosamente tutti dello stesso tipo.

ESEMPIO:
```
#[10; 3; 4];;
- : int list = [10; 3; 4]
```
Caml come sempre ritorna la sua interpretazione stampando, in questo caso, il tipo dei valori della lista `int list ...` e il valore della lista `... = [10; 3; 4]`

### Lista vuota ###
In caml esiste una particolare lista chiamata lista vuota che viene indicata con `[]`, la quale può essere usata per costruire appunto liste.<br>

ESEMPIO:
```
#[];;
- : 'a list = []
```
Il fatto che la lista vuota sia una particolare lista lo si nota dalla risposta di caml infatti il tipo di valori che può avere quella lista é indefinito, perciò la lista vuota é una lista non ancora inizializzata del tutto (alla quale manca un tipo di valori definito). Il tipo dei valori della lista però viene definito non appena si inserisce il primo valore con il *cons*, e quindi piu in generale caml capisce il tipo di valori di una lista dall'elemento in testa a quest'ultima.

### Costruttore di valori (cons) ###
Il costruttore di valori o *cons* é un operatore infisso (si indica in mezzo ai valori) e si indica con `::`. Questo operatore si usa per aggiungere valori in testa ad una lista, infatti, il primo argomento (a sinistra dell'operatore) é un valore di lista e il secondo (a destra dell'operatore) é una lista con valori dello stesso tipo del primo argomento.

ESEMPIO:
```
#3 :: [-2; 4];;
- : int list = [3; -2; 4]
```
Qui si nota l'utilità della lista vuota, infatti, come detto in precedenza, con questo tipo di lista e usando il costruttore di valori si possono creare altre liste.

ESEMPIO:
```
#3 :: [];;
- : int list = [3]
```
```
#3.5 :: [];;
- : float list = [3.5]
```
```
#3 :: -2 :: 4 :: [];;
- : int list = [3; -2; 4]
```

In caml é anche possibile creare liste di liste.

ESEMPIO:
```
#[3; 4] :: [];;
- : int list list = [[3; 4]]
```
```
#[3; 4] :: [5] :: [];;
- : int list list = [[3; 4]; [5]]
```

Oppure si possono creare liste di funzioni.

ESEMPIO:
```
#let g n = n+1;;
g : int -> int = <fun>

#g :: [];;
- : (int -> int) list = [<fun>]

#[g; g];;
- : (int -> int) list = [<fun>, <fun>]
```
La risposta di caml cambia leggermente perche il valore dei tipi della lista é un valore di funzione da interi ad interi (`int -> int`) e la lista é formata da valori funzionali `<fun>` tanti quante sono le funzioni all'interno della lista.

### Funzioni sulle liste ###
Sulle liste sono predefinite 2 funzioni:
* **hd (head):** restituisce il primo elemento di una lista
* **tl (tail):** cnacella il primo elemento di una lista

Queste 2 funzioni posso essere usate a loro volta per creare altre funzioni che operano sulle liste, come ad esempio la funzione opposta a *hd* che chiameremo 'lt' cioé 'last':
```
#let rec lt l = if tl l = [] then hd l else lt(tl l);;
lt : 'a list -> 'a = <fun>

#lt [3; 4];;
- : int = 4
```
Oppure la funzione opposta a *tl* che chiameremo 'rmlt' cioé 'removelast':
```
#let rec rmlt l = if tl l = [] then [] else hd l :: rmlt(tl l);;
rmlt : 'a list -> 'a list = <fun>

#rmlt [3; 4; 5];;
- : int list = [3; 4]
```
O ancora la funzione che conta la lunghezza di una lista:
```
#let rec len l = if tl l = [] then 1 else 1 + len(tl l);;
len : 'a list -> int = <fun>

#len [3; 4];;
- : int = 2
```

## **Caml light:** Pattern ##
Un pattern é un'espressione contenente **variabili**, cioé nomi che possono essere istanziati (ai quali posso associare valori), per esempio `x :: []` é un pattern. I pattern possono essere uguagliati a valori istanziando le variabili a valori, ovviamente corretti dal punto di vista del tipo.
Per esempio `x :: []` può essre uguagliata al valore `[5]` perché `[5] = 5 :: []` e istanziando x al valore 5 il pattern `x :: []` = `5 :: []`.<br>
`x :: []` può essere uguagliato a qualsiasi lista composta **esclusivamente da un solo valore**, infatti `x :: []` non si può uguagliare a `[5; 7]` = `5 :: 7 :: []`.<br>
Un generico pattern in caml si esprime con `x :: xs` dove `x` rappresenta il valore in testa alla lista generica `xs` (i nomi dell'espressione `x` e `xs` sono totalmente arbitrari).
Questo pattern più generico, perciò, é uguagliabile a liste composte da **almeno un elemento**, infatti prendendo l'esempio precendente con la lista `[5; 7]` si può istanziare `x` al valore `5` e `xs` alla lista `[7]` e lo stesso vale per la lista `[5]` infatti si avrà `x = 5` e `xs = []`.<br>
Esistono infiniti pattern come `x :: y :: ys` che si uguaglia a liste con almeno 2 valori, oppure `x :: y :: z :: zs` che si uguaglia a liste con almeno 3 valori e cosi via.

### Pattern matching ###
Con il *pattern matching* si cerca di uguagliare il valore di un espressione `e` con i `pattern` in sequenza:

NOTAZIONE:

```
match e with
    pattern(1) -> e(1)
  | pattern(2) -> e(2)
  | pattern(3) -> e(3)
  ....
```
Se il valore di `e` si uguaglia al `pattern(i)` il risultato é dato dall'espressione `e(i)`.

ESEMPIO:
```
#match [3; 4] with
    [] -> 0
  | x :: xs -> x;;
- : int = 3
```

Il *pattern matching* si può usare per definire funzioni ricorsive.<br>
Per esempio si può usare per ridefinire la funzione che calcola la lunghezza di una lista ('len'):
```
#let rec len l = match l with
                        [] -> 0
                      | x :: xs -> 1 + len xs;;
len : 'a list -> int = <fun>
```
Oppure per ridefinire la funzione che prende l'ultimo elemento di una lista:
```
#let rec last l = match l with
                        x :: [] -> x
                      | x :: xs -> last xs;;
last : 'a list -> 'a = <fun>
```
Solitamente nel *pattern matching* é buona norma usare pattern **mutuamente esclusivi**, il che significa che il pattern che viene uguagliato esclude tutti gli altri perché il valore dell'espressione non può e non deve essere uguagliarne altri.<br>
Nell'esempio precendente i due pattern non sono mutuamente esclusivi, infatti prendendo la lista `[3]`, sia il primo che il secondo pattern la ugualgiano. La definizione della funzione 'last', perciò, deve essere modificata in maniera tale da rendere tutti i pattern <u>mutuamente esclusivi</u>:
```
#let rec last l = match l with
                        x :: [] -> x
                      | x :: y :: ys -> last (y :: ys);;
last : 'a list -> 'a = <fun>
```

## **Caml light:** Concatenazione tra liste ##
In caml é predefinito un operatore infisso di concatenazione tra liste, indicato con `@`.

ESEMPIO:
```
#[3; 4] @ [5; 6];;
- : int list = [3; 4; 5; 6]
```
Caml non sa definire un tipo per questo operatore infatti se gli passiamo solamente l'operatore all'interprete caml risponde con un errore di tipo.<br>
Per definire un tipo per questo operatore é necessario ricorrere ad un ulteriore operatore `prefix`, sempre una funzione predefinita che rende un operatore infisso prefisso.
```
#prefix @;;
- : 'a list -> 'a list -> 'a list = <fun>
```

ESEMPIO di applicazione:
```
#prefix @ [3; 4] [5; 6];;
- : int list = [3; 4; 5; 6]
```

Propietà della concatenazione tra liste:
  1. `[] @ l` = `l @ []` = `l`
  2. `x :: (l1 @ l2)` = `(x :: l1) @ l2`

L'operatore di concatenazione tra liste é possibile definirla ricorsivamente:
```
#let rec append l1 l2 = match l1 with
                              [] -> l2
                            | x :: xs -> x :: append xs l2;;
append : 'a list -> 'a list -> 'a list = <fun>
```

La ricorsione é realizzata (implementata) mediante uno stack:<br>
`append [3; 4] [5;6]` => `3 :: append [4] [5; 6]` => `3 :: 4 :: append [] [5; 6]` => `3 :: 4 :: [5;  6]`<br>
Quando una funzione ricorsiva non termina mai si crea uno stack overflow, ossia la memoria a disposizione viene totalmente occupata e si causa un errore del tipo stack overflow.

## **Caml light:** Clausola when ##
La clausola **when** serve in caml per definire una condizione in base alla quale il valore di un'espressione deve essere uguagliato ad un pattern o meno.<br>
Prendiamo come esempio la funzione `take n l` che restituisce la lista composta dai primi n elementi di l (se si sono, altrimenti me ne da quanti possibili):
```
#let rec take n l = match l with
                      [] -> []
                    | x :: xs -> if n = 0 then [] else x :: take (n-1) xs;;
```
La funzione può essere riscritta in questo modo:
```
#let rec take n l = match (l, n) with
                        ([], n) -> []
                      | (l, 0) -> []
                      | (x :: xs, n) -> x :: take (n-1) xs;;
```
In questa ultima versione però non c'é mutua esclusione tra i pattern, precisamente tra gli ultimi due. In questi casi si deve usare la clausola **when** infatti:
```
#let rec take n l = match (l, n) with
                        ([], n) -> []
                      | (l, 0) -> []
                      | (x :: xs, n) when n > 0 -> x :: take (n-1) xs;;
```
in questa maniera tutti i pattern sono mutuamente esclusivi.
