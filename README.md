# WELCOME

Benvenuto avventuriero, sei pronto alla sfida? Raggiungi la fine del ponte per riuscire a vincere, fai attenzione alle trappole, ai draghi volanti e agli incantesimi che incontrerai lungo la strada. Raccogli più gemme possibili e torna a casa vittorioso!

In questo progetto sono state impiegate delle curve spline chiuse e fisse per delineare i movimenti compiuti dai draghi e delle curve spline aperte generate casualmente per la traiettoria degli incantesimi quindi attenzione, i pericoli possono comparire in qualsiasi momento!

# TEORIA

# Introduzione
In questo progetto sono state impiegate le curve Spline per realizzare la traiettoria dei movimenti di due dei principali ostacoli del gioco: i draghi e le sfere di magia. Per la diversa natura dei due pericoli sono state usate due tipologie di Spline diverse: la prima, usata per le sfere di magia, viene generata in modo casuale durante la sessione di gioco e presenta un unico incollamento di tipo C1 di curve di Bezièr di grado 3, la seconda invece è stata generata in fase di costruzione della scena, è fissa e presenta tre incollamenti di tipo C1 dello stesso tipo di curve di Bezièr citato prima.

# Curve di Bèzier
Una curva di Bezièr è un particolare tipo di curva differenziale che viene definita mediante un poligono di controllo di k+1 punti in un intervallo [0,1]. Il metodo utilizzato in questo progetto per generare queste curve è un algoritmo ricorsivo chiamato Algoritmo di De Casteljau. Il grado della curva viene è determinato dal numero dei vertici del poligono di controllo – 1, in particolare in questo progetto sono state utilizzate solamente curve di grado 3 e quindi il poligono di controllo è formato da 4 punti.

# Algoritmo di De Casteljau

L’algoritmo è definito in questo modo:

Grado 1: Se la curva è di grado 1 essa è detta lineare, viene generata a partire da due punti P0 e P1 e definita da un parametro t compreso nell’intervallo [0,1]. Seguendo l’algoritmo di De Casteljau un qualsiasi punto P(t) compreso tra i due punti sarà dato da:

Se la curva è di grado 2 avremo invece 3 vertici a formare il poligono di controllo: P0, P1 e P2. Per realizzare in questo caso la curva si parametrizzano i segmenti P0P1 e P1P2, individuando due curve ausiliarie di grado 1. I punti ottenuti al passo precedente consentono di determinare un nuovo segmento e tramite sostituzione otteniamo:

Nel caso infine delle curve di grado 3 il discorso precedente si amplia introducendo un altro punto a formare il poligono di controllo ottenendo così infine una curva definita dalla seguente formula:

Questo meccanismo ricorsivo si ripete per ogni t nell’intervallo [0,1].

# Curve Spline
Le curve Spline nascono con l’intento di semplificare la gestione di curve grandi e complesse, infatti avremmo potuto costruire una curva di Bezièr di grado molto elevato con l’algoritmo sopra citato ma sarebbe stato difficile dargli una forma più articolata e la creazione sarebbe diventata molto dispendiosa dal punto di vista computazionale. Una curva Spline, detta anche curva polinomiale a tratti, viene generata dall’incollamento di curve polinomiali. Esse vengono definite su una collezione di intervalli u0,…,ui , nel nostro caso ogni intervallo avrà lunghezza 1 quindi la Spline generata sarà uniforme, ogni ui è detto nodo della Spline. In ogni punto di saldatura, ovvero il punto in cui vengono incollate due curve, valgono delle condizioni di incollamento che possono essere di diverso tipo in base alla soluzione che si vuole ottenere:


Incollamento C0:


- Richiede che il punto finale di una curva che compone la Spline e il primo della curva successiva coincidano.


Incollamento C1:


-Richiede l'incollamento di tipo C0 ed inoltre che i polinomi delle due curve adiacenti abbiano la stessa derivata prima nel punto di saldatura. Per ottenere ciò in termini pratici si devono mettere il penultimo punto della curva, il punto di giuntura e il successivo nella stessa retta. Nel caso di spline uniforme il punto di giuntura di troverà ad occupare la posizione di punto medio del segmento che si forma congiungendo il punto precedente a quello di giuntura e il successivo.


Incollamento C2:


- Richiede la condizione C1 ed inoltre necessita che i due polinomi abbiano anche la derivata seconda uguale nel punto di saldatura, avendo così stessa tangente e curvatura in quel punto.
Sviluppo e scelte implementative
B-Spline uniforme chiusa


Questa prima tipologia di Spline viene utilizzata per delineare il movimento dei draghi. Nel gioco sono presenti 5 curve di questa tipologia ma con forme diverse, ognuna di esse è creata dall’incollamento di 4 curve di Bèzier e quindi il poligono di controllo sarà formato da 13 punti (con il primo e ultimo punto che però coincidono visto che la curva è chiusa).


N.B: le spline non sono visibili durante la sessione di gioco ma solo in modalità creazione, altrimenti sarebbe troppo semplice identificare i movimenti degli ostacoli.

Tramite la modalità scena è possibile spostare i punti del poligono di controllo facendo così assumere alla curva la forma desiderata, le curve vengono realizzate mediante l’algoritmo di De Casteljau. E’ stato scelto un numero così elevato di punti per poter creare curve molto ampie e complesse, che riuscissero quindi ad evitare alcuni ostacoli presenti nel gioco mantenendo coerenza nella traiettoria. Gli incollamenti soddisfano la condizione C1, è stata scelta questa condizione per fare in modo che il punto di raccordo fosse dolce per poter così rendere anche il movimento dei draghi che seguono la curva più realistico. Tale condizione viene verificata nella funzione EnforceMode che controlla, ogni qual volta viene spostato un punto in cui è avvenuto un incollamento, che la condizione venga rispettata. Nel caso delle curve B- Spline uniformi la condizione di incollamento C1 vuole non solo che i tre punti (prima del punto di incollamento, punto centrale, dopo il punto di incollamento) siano allineati, ma anche che il punto centrale sia il punto medio del segmento che collega i due punti restanti.


Per creare una maggiore variabilità di gioco verranno attivate casualmente solo alcune delle spline 5, in modo tale da avere una esperienza di gioco più avvincente. Nella modalità Easy sarà attivata solo una delle 5, in modalità medium 3 su 5, in modalità difficile tutte e 5 mentre in modalità random il numero sarà casuale.

B-Spline uniforme aperta
Il secondo tipo di B-Spline uniforme ha delle caratteristiche in comune con il primo ma viene generata in modo diverso. Presenta un solo incollamento di tipo C1, il poligono di controllo è formato quindi da soli 7 punti che però sono casuali. Questo tipo di Spline viene utilizzato per definire la traiettoria delle magie che vengono scagliate da delle streghe nella parte sinistra del ponte. In totale ci sono due Spline di questo tipo nel gioco.

N.B: le spline non sono visibili durante la sessione di gioco ma solo in modalità creazione, altrimenti sarebbe troppo semplice identificare i movimenti degli ostacoli.

Il primo punto della curva è posizionato dove è presente la strega, altri 5 punti sono casuali ma possono capitare solamente nella parte sinistra del ponte senza superarlo e senza uscire dall’area di gioco. L’ultimo punto sarà anch’esso casuale ma obbligatoriamente dovrà finire nel ponte e in un punto tra la posizione attuale del giocatore e la fine del ponte. E’ stato scelto di rendere queste Spline più piccole perché non erano richieste forme complesse e di rendere la generazione casuale per dare più variabilità al gioco. Ogni qual volta una magia percorre tutta la traiettoria formata dalla Spline viene invocata la ShufflePoints che crea una nuova Spline mantenendo i vincoli prima citati e viene generata una nuova magia.

# LINK AL GIOCO

Sei curioso di provare il gioco? Collegati al sito: http://www.mat.unimi.it/users/alzati/Geometria_Computazionale_98-99/apps/medievalrun/index.html
e scricalo dalla sezione download!

