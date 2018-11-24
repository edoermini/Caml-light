# **Caml** #
Caml é un meta language, perché veniva usato per descrivere la semantica di programmi scritti in altri linguaggi, in quanto molto semplice da usare.
Fa parte del gruppo dei **linguaggi funzionali**, cioé linguaggi incentrati sul concetto di funzione nel senso matematico del termine, e in piu é caratterizzato dalla **ricorsivitá**, infatti le funzioni possono richiamare se stesse. In particolare, in questo testo presenteremo i metodi di programmazione funzionale con il Caml Light.

## **Caml light:** Installazione ##

* **Linux:**
  * `$ apt install camllight`
  * download from https://caml.inria.fr/caml-light/release.en.html


* **Windows o Mac:**
  * download from https://caml.inria.fr/caml-light/release.en.html


## **Caml light:** Sintassi ##

(Gli esempi in questa guida saranno presi da un terminale linux)

* **Espressioni:**<br>
  Le espressioni in caml devono tutte terminare con ';;', altrimenti caml non intende che l'espressione é terminata e si aspetta altro:

  ESEMPIO:
  ```
  #3;;
  - : int = 3
  ```

  La seconda riga corrisponde alla risposta che il caml da in base all'interpretazione dell'espressione data.<br>
  Nella risposta il `-` sta ad indicare il nome che ho dato al valore, in questo caso é un meno perche non ho dato nessun nome al valore. Per dare un nome ad un valore si usa l'operazione **let**. <br>

  ESEMPIO:
  ```
  #let n = 3;;
  n : int = 3
  ```
  Dopo i due punti caml ci dice il tipo del risultato, che lo capisce senza che noi lo specifichiamo attraverso un **inferenza di tipo**, e poi ci da il valore dell'espressione dopo l'uguale.

  * **Operazioni:**<br>
  Le operazioni in caml sono uguali come in tutti gli altri linguaggi, l'unica differenza é che fa inferenza di tipo attraverso queste quindi esistono 2 diversi modi di scrivere una stessa operazione in base a che tipi la stiamo applicando:

    * Somma tra interi: +

    * Somma tra float: +.

    * Differenza tra interi: -

    * Differenza tra float: -.

    * Rapporto tra interi: /

    * Rapporto tra float: /.

    * Moltiplicazione tra interi: *

    * Moltiplicazione tra float: *.

    NB: Le operzioni tra tipi diversi in caml non sono ammesse

* **Dichiarazione di Funzioni:**<br>
  Con **let** si possono dichiarare anche funzioni.

  ESEMPIO:
  ```
  #let f n = n+2;;
  f : int -> int = <fun>
  ```
  La prima riga corrisponde a quello che bisogna scrivere per dichiarare una funzione. Dopo let servono dei nomi, il primo (`f`) é il nome della funzione, il secondo (`n`) é il parametro della funzione, ovviamente i nomi delle funzioni e dei parametri di queste sono arbitrari.<br>
  La seconda riga, come sempre, corrisponde a quello che ci risponde caml, infatti ci dice che ora `f` é la funzione che va da interi ad interi (`int -> int`), cioé che ha come parametri valori interi e restituisce valori interi, e che é uguale ad un valore funzionale (`<fun>`). Ogni dichiarazione di funzione ha come risultato `<fun>` perché, secondo la teoria della calcolabilità, in molti casi non é decidibile quale funzione stiamo definendo.

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
La seconda é una funzione detta curryed (dal matematico americano Curry del '900), cioé una funzione con argomenti in sequenza. Significa che, prendendo come esempio il secondo caso sopra, la funzione `f` prende un primo argomento `n` intero (`int -> ...`) e restituisce una funzione che prende un argomento `m` intero (`... -> int -> ...`) e mi da il risultato che é sempre un intero (`... -> int`).

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

* **Funzioni polimorfe:**<br>
  Le funzioni polimorfe sono particolari funzioni che possono essere applicate a valori di diverso tipo.

  ESEMPIO:
  ```
  #let f n = n;;
  f : 'a -> 'a = <fun>
  ```
  La risposta di caml a questa espressione, che sarebbe la funzione identità, é che la funzione `f` é una funzione che va da un qualsiasi tipo `'a` allo stesso tipo `'a`. Da questo si capisce bene che il caml interpreta i tipi che non capisce da l'inferenza di tipi mettendo un apice e vicino una lettera qualsiasi.

  ESEMPIO:
  ```
  #let f (n,m) = (n, m+1);;
  f : 'a * int -> 'a * int = <fun>

  #f (3.5, 2);;
  - : float * int = (3.5, 3)
  ```

* **Funzioni Ricorsive:**<br>
  L'espressione di funzioni ricorsive in caml spesso é necessario farla inserendo un espressione condizionale: `if e then e1 else e2`, con `e` rappresentante un valore booleano *true* o *false*, ed `e1`, `e2` aventi lo stesso tipo.<br>
  Nella dichiarazione di funzioni ricorsive in caml non basta mettere `let` ma é necessario far capire a caml che la funzione che stiamo definendo é una funzione ricorsiva (la funzione appare sia a destra che a sinistra dell'uguale), questo lo si fa con `let rec`.

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
Le liste in caml sono strutture omogenee, cioé strutture con valori dello stesso tipo e con un ordine.<br>
Questo tipo di dato viene indicato con delle parentesi quadre, in cui, al loro interno vengono messi i valori, rigorosamente tutti dello stesso tipo.

ESEMPIO:
```
#[10; 3; 4];;
- : int list = [10; 3; 4]
```
Caml risponde dicendoci che l'espressione corrisponde ad una lista e dandoci il tipo dei valori della lista `int list ...` e in fine ci da il valore della lista `... = [10; 3; 4]`

* **Lista vuota:**<br>
  In caml esiste una particolare lista chiamata lista vuota e indicata con `[]`, la quale può essere usata per costruire appunto liste.<br>

  ESEMPIO:
  ```
  #[];;
  - : 'a list = []
  ```
  Il fatto che la lista vuota sia una particolare lista lo si nota dalla risposta di caml infatti il tipo di valori che può avere quella lista é indefinito, perciò la lista vuota é una lista non ancora inizializzata del tutto (alla quale manca un tipo di valori definito). Il tipo dei valori della lista però viene definito non appena si inserisce il primo valore con il *cons*, e quindi piu in generale caml capisce il tipo di valori di una lista dall'elemento in testa a quest'ultima.

* **Costruttore di valori (cons):**<br>
  Il costruttori di valori o *cons* é un operatore infisso (si indica in mezzo ai valori) e si indica con `::`. Questo operatore si usa per aggiungere valori int testa ad una lista, infatti, il primo argomento (a sinistra dell'operatore) é un valore di lista e il secondo (a destra dell'operatore) é una lista con valori dello stesso tipo del primo argomento.

  ESEMPIO:
  ```
  #3 :: [-2; 4];;
  - : int list = [3; -2; 4]
  ```
  L'utilita, quindi, della lista vuota quindi entra in gioco ora, infatti, come detto in precedenza, con la lista vuota e usando il costruttore di valori si possono creare liste.

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
  La risposta di caml cambia leggermente perche il valore dei tipi della lista é un valore di funzione da interi ad interi (`int -> int`) e la lista é formata da valori funzionali `<fun>` tanti quante sono le funzioni allínterno della lista.

* **Funzioni sulle liste:**<br>
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
Un pattern é un'espressione che contiene **variabili**, cioé nomi che possono essere instanziati (ai quali posso associare valori), per esempio `x :: []` é un pattern.<br>
I pattern possono essere uguagliati a valori instanziando le variabili a valori, ovviamente corretti dal punto di vista del tipo.
