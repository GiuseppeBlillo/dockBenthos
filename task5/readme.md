# Application
## Descrizione
Questo applicativo arricchisce dei dati presi da un database ClickHouse e li inserisce, sempre in ClickHouse, tramite Benthos, usando Docker. L'obiettivo è ottenere una copia dei dati da un database e far sì che i dati inseriti in un altro database siano arricchiti da altri valori.
L'input di Benthos per la SELECT da ClickHouse è il seguente:

```
input:
  sql_select:
    driver: "clickhouse" 
    dsn: clickhouse://default@localhost:9000/testdb? 
    table: users
    columns: [NAME, AGE, GENDER]
```
Qui si considerano solo sesso, nome ed età presenti nella TABLE 'users'.
Un esempio della TABLE può essere questo:

```
ID|NAME |AGE|GENDER|ADDED     |
--+-----+---+------+----------+
 0|john | 32|M     |2024-01-24|
 1|mary | 29|F     |2024-02-29|
 2|mark | 30|M     |2024-02-29|
 3|anna | 35|F     |2024-02-29|
 4|peter| 33|M     |2024-01-24|
```

Sempre tramite Benthos l'applicativo mappa il JSON in entrata e ne copia i valori nella radice del documento senza il campo "NAME". Successivamente vengono utilizzati dei metodi per
mettere in maiuscolo la prima lettera del nome, per poi assegnarlo a "root.FIRSTNAME". Inoltre viene assegnato ad un "root.LASTNAME" un cognome fittizio grazie alla funzione ``fake()``. 

```
pipeline:
  processors:
    - mapping: |
        root = this.without("NAME")
        root.FIRSTNAME = this.NAME.capitalize()
        root.LASTNAME = fake("last_name")
```
        
Una volta utilizzato questo processore, il JSON viene inviato in un database per l'archiviazione (nel nostro caso sempre ClickHouse) tramite un processore di output "sql_raw".

```
output:
  sql_raw:
    driver: "clickhouse" 
    dsn: clickhouse://default@localhost:9000/testdb?
    query: INSERT INTO hydrated_users (ID, AGE, GENDER, LASTNAME, FIRSTNAME, ADDED) VALUES (generateUUIDv4(), ?, ?, ?, ?, now());
    args_mapping: root = [ this.AGE, this.GENDER, this.LASTNAME, this.FIRSTNAME]
```
I dati inseriti nel database si presenteranno in questo modo.
```
ID                                  |FIRSTNAME|AGE|LASTNAME|GENDER|ADDED              |
------------------------------------+---------+---+--------+------+-------------------+
0332b16a-01be-44fb-840e-17ccce4b3657|Mark     | 30|Hessel  |M     |2024-03-04 09:22:03|
2ad1ce35-6de4-4bf2-ba41-e927f41684b4|Mary     | 29|Olson   |F     |2024-03-04 09:22:03|
315ef872-ed45-4fa2-b933-3dbd2dfb9d39|Anna     | 35|Steuber |F     |2024-03-04 09:22:03|
890c5938-a48b-4697-b113-350ce772f3e0|Peter    | 33|Ullrich |M     |2024-03-04 09:22:03|
c93eb359-a398-4cba-bae0-0831f75d5672|John     | 32|Feeney  |M     |2024-03-04 09:22:03|
```
## Link Utili
### Documentazioni
- [Benthos](https://www.benthos.dev/docs/about)
- [ClickHouse](https://clickhouse.com/docs)