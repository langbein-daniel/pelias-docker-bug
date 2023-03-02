# LICENSE

* GTFS data: https://www.vgn.de/web-entwickler/open-data/

# Data import and first start

```bash
# set -e
sudo docker compose up -d elasticsearch

while ! curl --fail 'http://localhost:9200/_cluster/health?wait_for_status=green&timeout=1s' &>/dev/null; do
  echo "Waiting for Elasticsearch ..."
  sleep 2s
done

sudo docker compose run --rm schema ./bin/create_index
# this imports data into two new layers, stop and station
sudo docker compose run --rm gtfs   ./start.sh
sleep 10s
sudo docker compose up
```

Wait some seconds. Then open http://localhost:4000/v1/search?text=n%C3%BCrnberg&lang=en&layers=stop,station

Result 1:

```json
{
  "geocoding": {
    "version": "0.2",
    "attribution": "http://localhost:4000/attribution",
    "query": {
      "text": "nürnberg",
      "size": 10,
      "layers": [
        "stop",
        "station"
      ],
      "private": false,
      "lang": {
        "name": "English",
        "iso6391": "en",
        "iso6393": "eng",
        "via": "querystring",
        "defaulted": false
      },
      "querySize": 20,
      "parser": "pelias",
      "parsed_text": {
        "subject": "nürnberg",
        "street": "nürnberg"
      }
    },
    "errors": [
      "getaddrinfo ENOTFOUND placeholder"
    ],
    "engine": {
      "name": "Pelias",
      "author": "Mapzen",
      "version": "1.0"
    },
    "timestamp": 1677767598005
  },
  "type": "FeatureCollection",
  "features": []
}
```

Stop all services (`STRG` + `c`). Then start them again, all at once:

```bash
sudo docker compose up
```

Wait some seconds. Then open http://localhost:4000/v1/search?text=n%C3%BCrnberg&lang=en&layers=stop,station

Result 2:

```json
{
  "geocoding": {
    "version": "0.2",
    "attribution": "http://localhost:4000/attribution",
    "query": {
      "text": "nürnberg",
      "size": 10,
      "private": false,
      "lang": {
        "name": "English",
        "iso6391": "en",
        "iso6393": "eng",
        "via": "querystring",
        "defaulted": false
      },
      "querySize": 20,
      "parser": "pelias",
      "parsed_text": {
        "subject": "nürnberg",
        "street": "nürnberg"
      }
    },
    "errors": [
      "'stop' is an invalid layers parameter. Valid options: coarse,address,venue,street,country,macroregion,region,county,localadmin,locality,borough,neighbourhood,continent,empire,dependency,macrocounty,macrohood,microhood,disputed,postalcode,ocean,marinearea",
      "'station' is an invalid layers parameter. Valid options: coarse,address,venue,street,country,macroregion,region,county,localadmin,locality,borough,neighbourhood,continent,empire,dependency,macrocounty,macrohood,microhood,disputed,postalcode,ocean,marinearea"
    ],
    "engine": {
      "name": "Pelias",
      "author": "Mapzen",
      "version": "1.0"
    },
    "timestamp": 1677767731849
  },
  "type": "FeatureCollection",
  "features": []
}
```

Finally, stop all services (`STRG` + `c`) and remove them:

```bash
sudo docker compose down
```
