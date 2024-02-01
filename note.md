# Progetto Bizmate
Realizzazione di una serie di task necessari per l'apprendimento e potenziamento delle competenze necessarie alla realizzazione di un progetto finale.
Creazione di container tramite Docker contenenti immagini necessarie all'avvio di database (Redis, Mongo e Clickhouse), framework (Benthos) e server (Mqtt).

## Prima Task: 
### Realizzazione di tre container contenenti Benthos e MongoDB e avvio di un client che comunica con un database.

In questa parte ho realizzato due compose, uno dei quali monta un'immagine del framework Benthos per due servizi diversi, mentre l'altro monta un'immagine del database Mongo. I file realizzati, quindi, sono due: uno per [client e server](docker-compose-benthos.yml) Benthos e uno per il [database](docker-compose-mongodb.yml). Ho realizzato successivamente due file config che saranno eseguiti non appena avviati i container. Nel primo [file](config/config_benthos/client_config.yaml) sto configurando un client con un input che viene eseguito ogni due secondi. Questo input viene poi inviato ad un indirizzo, quello del server appena sopra, tramite una richiesta http di tipo POST. Il secondo [file](config/config_benthos/server_config.yaml) è configurato in modo da ascoltare tutte le richieste in arrivo, accettare solo quelle POST e salvarle nel database Mongo. Il database deve essere precedentemente avviato tramite [questo](docker-compose-mongodb.yml) file. Dopodichè si possono avviare gli altri container tramite [questo](docker-compose-benthos.yml) file per iniziare l'invio e il salvataggio dentro MongoDB.

## Seconda Task: 
### Realizzazione di un container contenente un database Redis e avvio della sua interfaccia Insight.

In questa parte ho realizzato un singolo compose, dove ho inserito due servizi che avvieranno immagini differenti per permettermi di avviare un database Redis e la sua interfaccia. Basta quindi avviare [questo](docker-compose-redisdb.yml) singolo file per poter avere un database Redis in locale interagibile tramite [browser](http://localhost:8001). 

## Terza Task: 
### Realizzazione di un container contenente un broker MQTT e salvarne i dati su mongo tramite Benthos.

L'obiettivo del progetto è quello di salvare su un database Mongo i messaggi MQTT che partivano da un client, filtrare i messaggi inviati usando Benthos, dopodichè salvare i messaggi filtrati su Mongo. Per fare questo ho riutilizzato il [compose](docker-compose-mongodb.yml) di Mongo creato in precedenza, ho creato un [compose](docker-compose-mosquitto.yml) per avere un broker MQTT di Mosquitto e tramite un programma chiamato MQTT Explorer ho iniziato ad inviare messaggi MQTT. Ho usato [questo](config/config_benthos/mqtt_mongo_config.yaml) file di configurazione per Benthos per assicurarmi la riuscita del progetto. In questo caso, Benthos rimane in ascolto nella porta assegnata all'invio del messaggio MQTT e, tramite l'aiuto di un processore, mappa il messaggio, lo filtra analizzando la presenza di un elemento (in questo caso filtra il valore "Giuseppe" nel campo "name" e ne cancella la chiave), aggiunge il valore della data di aggiunta ed infine invia il risultato al database Mongo. Ho preferito inoltre visualizzare il risultato finale con una comoda stampa nel terminale.

## Quarta task 
### ..

## Comandi
### Ecco una lista di comandi usati per avviare il compose e per provare i vari file config di Benthos:
#### - per avviare il compose:
*docker-compose -f [nome_file_docker_compose.yml] up*
#### - per interrompere il compose:
*ctrl+c* <br>
*docker stop [id container]*
#### - per visualizzare i container attualmente attivi:
*docker ps*
#### - per avviare un file di configurazione Benthos senza compose:
*benthos -c [nome_file_config.yaml]*