# OCTAVE

## Indice

- [Il programma](#Il%20programma)
- [Arrays](#Arrays)
    - [Vettori](#Vettori)
    - [Matrici](#Matrici)
        - [Operazioni aritmetiche](#Operazioni%20aritmetiche)
        - [Moltiplicazione non puntuale](#Moltiplicazione%20non%20puntuale)
- [Syntax](#Syntax)
    - [Funzioni](#Funzioni)
    - [Istruzioni](#Istruzioni)
        - [If](#If)
        - [For](#For)
        - [Switch](#Switch)
        - [While](#While)
        - [Do - Until](#Do%20-%20Until)
- [Sistema di equazioni](#Sistema%20di%20equazioni)
    - [Metodo con la matrice inversa](#Metodo%20con%20la%20matrice%20inversa)
    - [Metodo di Gauss](#Metodo%20di%20Gauss)
    - [Metodo di Jacobi](#Metodo%20di%20Jacobi)
- [Integrali](#Integrali)
    - [Metodo dei rettangoli e dei trapezi](#Metodo%20dei%20rettangoli%20e%20dei%20trapezi)
    - [Tecnica di Monte Carlo](#Tecnica%20di%20Monte%20Carlo)
- [Calcolo di una radice tramite bisezione](#Calcolo%20di%20una%20radice%20tramite%20bisezione)
- [Tecnica di Monte Carlo (cerchio)](#Tecnica%20di%20Monte%20Carlo%20%28cerchio%29)
- [Autori](#Autori)

## Il programma
È un software open source. Usa file `.m`, in larga parte compatibili con quelli di MATLAB, che è un software proprietario.

## Arrays

Gli elementi sono indicizzati a partire da 1.

### Vettori
Generalmente indicati con una lettera minuscola.

```octave
% vettore riga
a = [val1 val2 val3]

% vettore colonna
b = [val1; val2; val3]
```

### Matrici
Generalmente indicate con una lettera maiuscola.

```octave
C = [val1 val2 val3; val4 val5 val6; val7 val8 val9]
```

#### Operazioni aritmetiche

Date due matrici `A` e `B` delle stesse dimensioni, gli elementi della matrice `C` si calcolano così:

| Operatore | Risultato |
| --- | --- |
| `A + B` | `C(i, j) = A(i, j) + B(i, j)` |
| `A - B` | `C(i, j) = A(i, j) - B(i, j)` |
| `A .* B` | `C(i, j) = A(i, j) * B(i, j)` |
| `A ./ B` | `C(i, j) = A(i, j) / B(i, j)` |

#### Moltiplicazione non puntuale

Il prodotto `A * B` si calcola con la seguente formula:


$$
c_{ij} = \sum_{k=1}^{\text{num. colonne di }A} a_{ik} b_{kj}
$$


È possibile solo se il numero di colonne di `A` è uguale al numero di righe di `B`. La matrice ottenuta avrà lo stesso numero di righe di `A` e lo stesso numero di colonne di `B`.


## Syntax

### Funzioni

```octave
inv(x)
% calcola 1/x se x è un numero non nullo; se x è una matrice quadrata invertibile, ne calcola l'inversa
```

```octave
diag(X)
% estrae i valori sulla diagonale della matrice X
```

```octave
rand()
% numero pseudocasuale tra 0 e 1
```

```octave
inline(X)
% crea una funzione a partire dall'espressione contenuta nella stringa X
```

```octave
[v, ier, nfun, err] = quad("funzdaintegrare", a, b, [tolleranza,0])

%{
Parametri:
"funzdaintegrare": la funzione da integrare
a: estremo inferiore
b: estremo superiore
[tolleranza,0]: tolleranza assoluta e relativa richieste sul calcolo
%}

%{
Valori restituiti:
v: Il valore numerico dell'integrale (il risultato principale).
ier: Il codice che segnala eventuali problemi di integrazione. Se è 0, il calcolo è terminato con successo.
nfun: Il numero di volte che la funzione è stata valutata durante il calcolo.
err: La stima dell'errore commesso.
%}
```

```octave
feval(X, x)

% valuta nel punto x la funzione indicata da X
```

### Istruzioni

#### If

```octave
if (condition)
 ...
end
```

#### For

```octave
for i = inizio:passo:fine
 ...
end
```

#### Switch

```octave
switch (var)
case valore
 ...
otherwise
 ...
end
```

#### While

```octave
while (condizione)
 ...
end
```

#### Do - Until

```octave
do
 ...
until (condition)
```

---

## Sistema di equazioni

### Metodo con la matrice inversa

#### **Teoria:**

Poiché `A*x = b`, con `A` matrice dei coefficienti e `b` vettore colonna dei termini noti, se `A` è invertibile si può moltiplicare ambo i membri per l'inverso della matrice `A` e si ottiene `x = inv(A) * b`.

#### **Programma:**

```octave
x = inv(A) * b
```

### Metodo di Gauss

#### **Teoria:**

Si elimina via via un'incognita alla volta fino a ottenere un'ultima equazione con una sola incognita. Da lì si risale per trovare i valori delle altre incognite.

Un sistema rimane equivalente se:
- Si scambiano due equazioni fra loro.
- Si moltiplica un'equazione per una costante diversa da zero.
- Si somma a un'equazione un multiplo di un'altra equazione.

Il sistema si risolve dunque col seguente procedimento:
1. Si sceglie un'equazione di riferimento, detta di pivot, procedendo dalla prima alla penultima.
2. Si moltiplica l'equazione di pivot per `k` e la si somma a ciascuna equazione sottostante, in modo da annullare l'incognita corrispondente.
    > `k = -A(rx, r) / A(r, r)`
3. Arrivati all'ultima riga, si ricava il valore dell'ultima incognita.
4. Da qui si risale, sostituendo nelle equazioni precedenti i valori già trovati e ricavando tutte le altre incognite.

#### **Programma:**

```octave
% definizione dei coefficienti
A = [2 3 4 5 6; 3 7 8 9 2; 4 8 0 6 1;  5 9 6 4 3; 5 2 -4 4 3]

% definizione dei valori noti
b = [2; 3; 6; 0; 3 ]

% numero di righe e di colonne della matrice
nr = 5;
nc = 5;

% soluzione calcolata con l'inversa, solo per confronto
% sol1 = inv(A) * b

% ci si ferma alla penultima riga perché sotto l'ultima non ci sono altre righe da modificare

% riga di riferimento, di pivot
for r = 1 : nr - 1

 % righe da cui eliminare l'incognita corrispondente, ossia quelle successive alla riga di pivot
 for rx = r + 1 : nr

  % calcolo del coefficiente per annullare l'incognita corrispondente
  k = - A(rx,r) / A(r,r);

  % ricalcolo dei coefficienti (A) e dei termini noti (b)
  % (rx,:) esegue l'operazione per ogni colonna della riga rx
  A(rx,:) = A(rx,:) + k*A(r,:);
  b(rx,1) = b(rx,1) + k*b(r,1);

 end

end

% ricavo l'ultima incognita
x(nr,1) = b(nr,1) / A(nr,nr);

% ricavo le rimanenti incognite iterativamente
for r = nr - 1: -1: 1
 somma = 0;
 for k = r + 1: nc
  somma += x(k,1) * A(r,k);
 end
  x(r,1) = (b(r,1) - somma) / A(r,r);
end

x
```

### Metodo di Jacobi

#### **Teoria:**

1. Si isola un'incognita per equazione: `x1` nella prima riga, `x2` nella seconda e così via.
2. Si sceglie un valore iniziale per ogni incognita e si calcolano i nuovi valori usando quelli dell'iterazione precedente.
3. Se il metodo converge, continuando a sostituire i valori le approssimazioni si stabilizzano.

Per usare questo metodo, la matrice deve essere quadrata e avere elementi diagonali diversi da zero. Si portano a destra i termini con le incognite non associate agli elementi della diagonale principale, nelle posizioni `(1,1), ..., (n,n)`. La matrice `A` dei coefficienti si esprime tramite `D` (che contiene solo la diagonale di `A`) e `N` (che contiene gli altri coefficienti con il segno opposto), secondo la formula:
```text
A = D - N
```

Da qui si può ricavare facilmente l'espressione chiave del programma ricordando la formula:
```text
Ax = b
```

Sostituendo e sviluppando si ottiene:
```text
(D - N)x = b
Dx - Nx = b
Dx = Nx + b
x = inv(D)Nx + inv(D)b
```

Per controllare quanto cambiano due approssimazioni successive si usa la norma euclidea della loro differenza. La formula è la seguente:

$$
\lVert \mathbf{x} - \mathbf{y} \rVert_2 = \sqrt{\sum_{i=1}^{n}(x_i-y_i)^2}
$$


#### **Programma:**

```octave

% dichiarazione delle matrici
A = [5 -1 2; 3 8 -2; 1 1 4]
b = [12; 25; 6]

% isolamento della diagonale
% il primo diag estrae i valori della diagonale di A in un vettore
% il secondo diag rende il vettore estratto una matrice quadrata
D = diag(diag(A));

N = -(A - D);

% vettore contenente i valori iniziali delle incognite, di nostra scelta
x = [1; 1; 1]

% si ricalcolano le incognite fino alla convergenza
do
 y = inv(D) * N * x + inv(D) * b;
 norma = norm(y - x);
 x = y;
until (norma < 0.001)

x
```

---

## Integrali

### Metodo dei rettangoli e dei trapezi

#### **Teoria:**

Per approssimare l'integrale, che coincide con l'area se la funzione è non negativa, si possono usare due formule:
- Metodo dei rettangoli: sommatoria, per `i` da `0` a `n - 1`, di `Dx * f(a + i * Dx)`.
- Metodo dei trapezi: sommatoria, per `i` da `0` a `n - 1`, di `Dx/2 * (f(a + i * Dx) + f(a + (i + 1) * Dx))`.

#### **Programma:**

1. Si crea un file `.m` con lo stesso nome della funzione.
```octave
function [y] = funzdaintegrare(x)
y = 5 .* sin(x) + 3;
```

2. File principale
```octave
% dichiarazione degli estremi dell'intervallo di integrazione
a = 0;
b = 2;

% soglia sulla differenza tra due approssimazioni successive
tolleranza = 0.001;

area = 0;   

% numero di rettangoli/trapezi
n = 1;

do
 areaold = area;
 n *= 2;
 delta = (b - a) / n;
 area = 0;
  for i = 0: n - 1
    % questa formula è per il metodo dei trapezi
    area += delta * (funzdaintegrare(a + i * delta) + funzdaintegrare(a + (i + 1) * delta)) / 2;
  end
until (tolleranza > abs(area - areaold))

printf("l'area vale %f \n", area);
printf("n= %i \n",n);

% verifica tramite la funzione di integrazione di Octave
[v, ier, nfun, err] = quad("funzdaintegrare", a, b, [tolleranza,0])
```

### Tecnica di Monte Carlo

#### **Teoria:**

Il metodo Monte Carlo permette di approssimare un'area con un procedimento probabilistico. In una regione che contiene la figura si scelgono punti casuali e si verifica se sono dentro o fuori dalla figura. Con un numero sufficiente di punti si può ottenere una buona approssimazione dell'area.

#### **Programma:**

```octave
% Definisce la funzione
f = inline('3*x^2+2*x+1')

% Definizione degli estremi
a = 1;
b = 5;

% Calcolo del massimo della parabola: in questo intervallo la funzione è crescente, quindi il massimo è f(b).
max = feval(f,5)

% rettangolo contenente la funzione
arearett = (b - a) * max;

% Inizializzazione del contatore dei tiri finiti a segno
conta = 0;

for i = 1: 100000
 x = 4 * rand() + 1;
 y = max * rand();

 % controllo per capire se il lancio è interno all'area da misurare
 if (y < feval(f,x))
  conta += 1;
 end
end

area = arearett * conta / 100000
```

---

## Calcolo di una radice tramite bisezione

#### **Teoria**

Per approssimare una radice di una funzione si cerca un valore `x` per cui `f(x)` sia uguale o sufficientemente vicino a zero.
Data una funzione continua nell'intervallo `[a, b]`, con `a < b` e valori `f(a)` e `f(b)` di segno opposto:
1. Si calcola il punto medio `x`.
2. Si confronta il segno di `f(x)` con quello di `f(a)` o di `f(b)` e si sceglie la metà in cui i valori agli estremi hanno segno opposto.
3. Si continua a dimezzare l'intervallo finché non si ottiene un valore di `f(x)` abbastanza vicino a zero o una larghezza dell'intervallo inferiore alla soglia scelta.

#### **Programma:**

```octave
% estremi inferiore e superiore dell'intervallo iniziale
a = 1;
b = 2;

% Tolleranza sulla larghezza dell'intervallo usata per arrestare il calcolo
intervalloMin = 0.001;

% Soglia di tolleranza alternativa. Se il valore della funzione nel punto medio è abbastanza vicino a zero si interrompe in anticipo.
zeroMax = 0.01;

% Definisce la funzione in questione
f = inline('x^3-x-2');

do
 x = (b + a) / 2;

 % se il valore della funzione è abbastanza vicino a zero, si accetta l'approssimazione
 if (abs(feval(f,x)) < zeroMax)
  break;
 end

 % decide quale metà conservare
 if (sign(feval(f,x)) == sign(feval(f,a)))
  a = x;
 else
  b = x;
  end

until ((b-a) < intervalloMin)

x
```

---

## Tecnica di Monte Carlo (cerchio)

#### **Teoria**

Per determinare l'area di una figura in modo approssimativo si può usare il metodo probabilistico Monte Carlo, che consiste nello scegliere molti punti casuali in una regione che contiene la figura e verificare se cadono al suo interno o all'esterno. Di seguito il programma per approssimare l'area di un cerchio.

#### **Programma:**

```octave
% Definizione del numero di lanci: aumentandolo tende a migliorare la precisione dell'approssimazione
nlanci = 100000;

% lato del quadrato circoscritto
l = 1;

% Contatore dei punti interni alla figura
conta = 0;

for i = 1: nlanci
 x = rand() - 0.5;
 y = rand() - 0.5;

 % controllo per capire se il lancio è interno all'area da misurare
 if(x^2+y^2 <= 0.25)
  conta += 1;
 end
end

areacerchio = conta / nlanci
```

## Autori

Emanuele Cananzza - [GitHub Profile](https://github.com/emanuele-c147)
