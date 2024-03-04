# BenthosTrial
L'applicativo prende in input dei messaggi MQTT in formato RAW da un canale definito 'saluti' tramite questo componente
```
input:
  redis_pubsub:
    url: redis://localhost:6379
    channels: [saluti]
```
un esempio che può essere inviato tramite terminale può essere il seguente:
```
"ciao"
```
tramite un processo di mapping si assegna al documento "root" il JSON modificandone il contenuto. Se il saluto è "ciao", allora questo verrà cambiato in "buongiorno"
```
pipeline:
  processors:
    - mapping: |
        root = if content().string() == "ciao"{ "buongiorno" }
```
alla fine, il JSON mappato verrà inviato in un server MQTT allo stesso topic e aggiunto ad una lista su Redis chiamata "saluti_list" tramite un componente broker in output.
```    
output:
  broker:
    pattern: fan_out_sequential
    outputs: 
      - mqtt:
          urls: 
            - mqtt://localhost:1883 
          topic: "saluti" 
      - redis_list:
          url: redis://localhost:6379 
          key: saluti_list 
``` 
## Link Utili
### Documentazioni
- [Benthos](https://www.benthos.dev/docs/about)
- [Redis](https://redis.io/docs/reference/)
- [MQTT Explorer](https://mqtt-explorer.com/)
