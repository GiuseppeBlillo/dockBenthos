# Application
## Descrizione
L'applicativo prende in input una richiesta MQTT rimanendo in ascolto secondo un topic pre-impostato "school/class/students" e ricevendo un JSON. 
Benthos mappa le chiavi e i propri valori del JSON e viene cancellata la chiave "firstname" se essa, una volta settata in maiuscolo, ha un valore definito (in questo caso "GIUSEPPE"). 
Il JSON risultante viene poi inviato in un database MONGO.

## Link Utili
### Documentazioni
- [Benthos](https://www.benthos.dev/docs/about)
- [Mongo](https://www.mongodb.com/docs/)
