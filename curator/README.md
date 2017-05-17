# Managing indices through Curator


Curator can help you manage your indices in elasticsearch or to clean up data. In the config file `config.yml` remember to se the hostname and port of your elasticsearch server, like in the snippet from the config below:

```bash
client:
  hosts:
    - elasticsearch
  port: 9200
```

In the action file `action_file.yml` add your actions. E.g. if you wish to delete all indices with the prefix `metricbeat-` that are older than `10 days`:

```bash
---
actions:
  1:
    action: delete_indices
    description: >-
      Alias indices older than 10 days, with a prefix of metric-beat-.
    options:
      continue_if_exception: False
      disable_action: False
    filters:
    - filtertype: pattern
      kind: prefix
      value: metric-beat-
    - filtertype: age
      source: creation_date
      direction: older
      unit: days
      unit_count: 10
```

Find more information about `Actions`, `Options` and `Filters` in the [Curator documentation](https://www.elastic.co/guide/en/elasticsearch/client/curator/5.0/about.html).

## Starting the Docker container

The configuration and the frequency of how often curator is run, is handled through the `COMMAND` and `SCHEDULE` environment variables. The `COMMAND` runs curator specifying the configs. `SCHEDULE` is a cron expression of how often curator is run. Below is an example of how to start the container.

```bash
docker run -d -e SCHEDULE='30 4 * * *' -e COMMAND='curator --config /etc/curator/config.yml  /etc/curator/action_file.yml' --network=<name_of_network> --link <name_of_elasticsearch_container>:elasticsearch robinaa/elk:curator-latest
```
