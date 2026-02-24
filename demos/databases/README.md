## Databases

### Aurora fast clone

* Compute on the same disk pages
* New cluster in five minutes
* Pay for the changed pages
* Chain supported
* Ideal for testing and analytics

### Aurora local forward

### PartiQL and DynamoDB

Foto de mono loco

```bash
ST="
  SELECT uuid, pokemon.name, latLon.lat, latLon.lon 
  FROM pokemondb
"
aws dynamodb execute-statement --statement "$ST" --limit 3 | fracjson
```

```bash
ST="
  SELECT * FROM pokemondb 
  WHERE pokemon.name = 'Sawsbuck'
"
aws dynamodb execute-statement --statement "$ST" --limit 3 | fracjson
```

```bash
ST="
  SELECT uuid, pokemon.name, latLon.lat, latLon.lon 
  FROM pokemondb 
  WHERE latLon.lat > 42.0 
    AND latLon.lat < 43.0
"
aws dynamodb execute-statement --statement "$ST" --limit 3 | fracjson
```

```bash
ST="INSERT INTO pokemondb VALUE {
  'uuid': '1111-2222-3333-4444-5555',
  'timestamp': 1700000000000,
  'pokemon': {'id': '4', 'name': 'Charmander'},
  'latLon': {'lat': 43.5, 'lon': 2.1}
}"
aws dynamodb execute-statement --statement "$ST" --limit 3 | fracjson
```

