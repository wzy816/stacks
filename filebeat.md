# filebeat

## docker
```
docker pull docker.elastic.co/beats/filebeat:5.6.12
```

## filebeat.yml
```yml
filebeat.prospectors:
- input_type: log
  paths:
    - /var/log/myapp/log.*
  tags: ["myapp"]
  json.keys_under_root: true

output.elasticsearch:
  hosts: ['my_elasticsearch_ip:9200']
```

## rum 

```bash
docker run -d \
  --name=filebeat \
  --user=root  \
  --volume="/path/to/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="/path/to/log/dir/:/var/log/myapp/:ro" \
  docker.elastic.co/beats/filebeat:5.6.12 filebeat -e -strict.perms=false
```