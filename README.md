# VerneMQ

MQTT broker.

## Client management

Check official docs about this topic here: <https://docs.vernemq.com/configuration/db-auth#redis>

To interact with database, you have to run `redis-cli` inside `vmq-redis` running container.
Use `docker exec ...` or start a shell session through Portainer to access `vmq-redis`.

### Create clients

```sh
$ redis-cli

> SET "[\"\",\"client-1\",\"user-1\"]" "{\"passhash\":\"$2a$12$4WvSLjSMai7viDn0Dh3VAeYkQEkSPefybwjUO8FkmLrEr7O2MBrue\",\"subscribe_acl\":[{\"pattern\":\"test-topic\"}]}"
OK

> SET "[\"\",\"client-2\",\"user-2\"]" "{\"passhash\":\"$2a$12$4WvSLjSMai7viDn0Dh3VAeYkQEkSPefybwjUO8FkmLrEr7O2MBrue\",\"publish_acl\":[{\"pattern\":\"test-topic\"}]}"
OK
```

Here, you define a pair of new keys `"[\"\",\"client-1\",\"user-1\"]"` and `"[\"\",\"client-2\",\"user-2\"]"`. It means:

1. empty mountpoint.
1. `client-<n>` client IDs.
1. `user-<n>` usernames.

These keys are set with values:

1. `"{\"passhash\":\"$2a$12$4WvSLjSMai7viDn0Dh3VAeYkQEkSPefybwjUO8FkmLrEr7O2MBrue\",\"subscribe_acl\":[{\"pattern\":\"test-topic\"}]}"`.
1. `"{\"passhash\":\"$2a$12$4WvSLjSMai7viDn0Dh3VAeYkQEkSPefybwjUO8FkmLrEr7O2MBrue\",\"publish_acl\":[{\"pattern\":\"test-topic\"}]}"`.

This means subscribe granted to `test-topic` topic to `user-1` user (from `client-1` client) and publish granted to `test-topic` topic to `user-2` user (from `client-2` client), both using same password hash.

This value contains a bcrypt password hash `passhash`, generated for password value `changeme`.

Create a new password and generate a bcrypt hash (with 12 rounds) for it. You can use any tool, <https://www.browserling.com/tools/bcrypt> for example.

### List clients

```sh
$ redis-cli

> KEYS *
1) "[\"\",\"client-1\",\"user-1\"]"
2) "[\"\",\"client-2\",\"user-2\"]"
```

### Delete client

```sh
$ redis-cli

> DEL "[\"\",\"client-1\",\"user-1\"]" "[\"\",\"client-2\",\"user-2\"]"
(integer) 2
```

## Usage example

You can test broker using any MQTT client, like Mosquitto (install with `sudo apt install mosquitto-clients`).

First, subscribe with:

```sh
mosquitto_sub -h <mqtt-host> -t test-topic -u user-1 -P changeme -i client-1
```

Then, run publish in another session:

```sh
mosquitto_pub -h <mqtt-host> -t test-topic -u user-2 -P changeme -i client-2 -m "hello"
```

You should see now published message at first session.
