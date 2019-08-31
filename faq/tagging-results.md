---
title: Маркировка результатов согласно удовлетворяющим подзапросам
---

В некоторых случаях требуется узнать, какому подзапросу удовлетворил 
документ, чаще всего это происходит при необходимости объединить запросы 
разного характера в одном ИЛИ, и приложению требуются дополнительные 
данные, чтобы по-разному отобразить те или иные совпадения. Например, 
данный запрос ищет фильмы, в  которых или участвовал Райан Рейнолдьс, 
или которые относятся к жанру комедии: 

```json
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "stars": {
              "query": "Ryan Reynolds"
            }
          }
        },
        {
          "term": {
            "genre": {
              "value": "comedy"
            }
          }
        }
      ]
    }
  }
}
```

Подобную макрировку можно осуществить с помощью 
[named queries](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-named-queries-and-filters.html)
(появился новый атрибут `_name`):


```json
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "stars": {
              "query": "Ryan Reynolds",
              "_name": "stars"
            }
          }
        },
        {
          "term": {
            "genre": {
              "value": "comedy",
              "_name": "genre"
            }
          }
        }
      ]
    }
  }
}
```

В результате документы будут возвращены с названиями подзапросов, 
которым они удовлетворяют:

```json
[
    {
        "_index": "movies",
        "_type": "_doc",
        "_id": "mRDzcmsBidhkTp3CVss9",
        "_score": 0.753101,
        "_source": {
            "title": "Pokemon Detective Pikachu",
            "genre": [
                "comedy",
                "adventure"
            ],
            "stars": [
                "Ryan Reynolds"
            ],
            "released": "2019-05-10"
        },
        "matched_queries": [
            "genre",
            "stars"
        ]
    },
    {
        "_index": "movies",
        "_type": "_doc",
        "_id": "mBDtcmsBidhkTp3C6Mv6",
        "_score": 0.38845783,
        "_source": {
            "title": "Detention",
            "genre": "comedy",
            "released": "2012-04-13"
        },
        "matched_queries": [
            "genre"
        ]
    },
    {
        "_index": "movies",
        "_type": "_doc",
        "_id": "mhACc2sBidhkTp3CZctM",
        "_score": 0.36464313,
        "_source": {
            "title": "Life",
            "genre": "thriller",
            "stars": [
                "Ryan Reynolds"
            ],
            "released": "2017-03-24"
        },
        "matched_queries": [
            "stars"
        ]
    }
]
```

Как можно видеть, вместе с документами возвращается поле 
`matched_queries`, которое информирует о том, какие подзапросы совпали.

### Тестовые данные

Индекс

```json
{
  "settings": {
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "title": {
      	"type": "text",
        "index": false
      },
      "genre": {
        "type": "keyword"
      },
      "released": {
        "type": "date"
      }
    }
  }
}
```

Документы

```json
{
  "title": "Pokemon Detective Pikachu",
  "genre": ["comedy", "adventure"],
  "stars": ["Ryan Reynolds"],
  "released": "2019-05-10"
}
```

```json
{
  "title": "Detention",
  "genre": "comedy",
  "stars": [],
  "released": "2012-04-13"
}
```

```json
{
  "title": "Life",
  "genre": "thriller",
  "stars": ["Ryan Reynolds"],
  "released": "2017-03-24"
}
```
