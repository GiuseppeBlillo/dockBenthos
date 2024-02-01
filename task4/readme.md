# Application
## Descrizione
L'applicativo prende in input una richiesta MQTT in ascolto sotto un topic:

```
/merakimv/devices
```

Un esempio di input da inviare tramite MQTT può essere questo:

```
{
    "ts" : 1706715085000,
    "tenant" : "bizmate",
    "networkId" : "l_123456",
    "serial" : "Q2FV-Z2TK-TK54",
    "name" : "corridoio",
    "firmware" : "v1.0"
} 
```

Tramite Benthos l'applicativo mappa il JSON in entrata e verifica se la versione del firmware è inferiore ad una versione data (nel nostro caso 5.5). Nel JSON verrà aggiunta una chiave "notes" con valore "upgrade_available" se la versione è inferiore, altrimenti il valore sarà "current_version".

L'output finale dovrà essere salvato su database Mongo o eventualmente aggiornato
 

```
{
    "ts" : 1706715085000,
    "tenant" : "bizmate",
    "networkId" : "l_123456",
    "serial" : "Q2FV-Z2TK-TK54",
    "name" : "corridoio",
    "firmware" : "v1.0",    
    "notes": "upgrade available"
} 
```
## Link Utili
### Documentazioni
- [Benthos](https://www.benthos.dev/docs/about)
- [Mongo](https://www.mongodb.com/docs/)
- [Meraki](https://developer.cisco.com/meraki/mv-sense/overview/#introduction)