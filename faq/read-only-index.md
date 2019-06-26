---
title: Индекс в состоянии read-only
---

Иногда ElasticSearch отказывается принимать новые записи со следующей 
ошибкой:

> FORBIDDEN/12/index read-only / allow delete (api)]

В большинстве случаев это связано с тем, что был достигнут [flood disk 
watermark](https://www.elastic.co/guide/en/elasticsearch/reference/current/disk-allocator.html),
по дефолту составляющий 95% занятости диска на любом из узлов - в этом 
случае ElasticSearch блокирует индекс на всех узлах сразу. В случае,
если подобное использование диска не происходит в настоящем, это не 
значит, что оно не произошло вообще:

- ElasticSearch использует дополнительное место во время мерджа 
сегментов, создавая новый файл со всеми данными
- Место могло быть занято и освобождено другими потребителями, например,
логами, которые были отротированы
- Место могло быть занято на других узлах, нежели индекс расположен 
сейчас - при достижении high disk watermark ElasticSearch начинает 
эвакуировать шарды с проблемного узла.

Чтобы разблокировать индекс после устранения проблем на узле, необходимо
выполнить следующую операцию: 

```
PUT {index}/_settings
{
    "index": {
        "blocks": {
            "read_only_allow_delete": "false"
        }
    }
}
```

Также можно разблокировать все существующие индексы целиком:

```
PUT */_settings
{
    "index": {
        "blocks": {
            "read_only_allow_delete": "false"
        }
    }
}
```