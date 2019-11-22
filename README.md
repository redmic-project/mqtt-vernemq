# VerneMQ

MQTT broker.


## Client management

Check official docs about this topic here: https://docs.vernemq.com/configuration/db-auth#redis

To interact with database, you have to run `redis-cli` inside `vmq-redis` running container.
Use `docker exec ...` or start a shell session through Portainer to access `vmq-redis`.


### List clients

```
$ redis-cli

> KEYS *

```


### Create client

```
$ redis-cli

> SET "[\"\",\"test-client\",\"test-user\"]" "{\"passhash\":\"$2a$12$WDzmynWSMRVzfszQkB2MsOWYQK9qGtfjVpO8iBdimTOjCK/u6CzJK\",\"subscribe_acl\":[{\"pattern\":\"a/+/c\"}]}"

```

Here, you define a new key `"[\"\",\"test-client\",\"test-user\"]"` (empty mountpoint, `test-client` client ID, `test-user` username) with value `"{\"passhash\":\"$2a$12$WDzmynWSMRVzfszQkB2MsOWYQK9qGtfjVpO8iBdimTOjCK/u6CzJK\",\"subscribe_acl\":[{\"pattern\":\"a/+/c\"}]}"`.

This value contains a bcrypt password hash `passhash` and a list of topic patterns `subscribe_acl` with granted access for this client.

Decide a new password and generate a bcrypt hash (with 12 rounds) for it.
You can use any tool, https://www.browserling.com/tools/bcrypt for example.


### Delete client

```
$ redis-cli

> DEL "[\"\",\"test-client\",\"test-user\"]"

```
