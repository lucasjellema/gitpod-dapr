# gitpod-dapr
Gitpod workspace definition for a Dapr.io environment

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/lucasjellema/gitpod-dapr)


This environment has Dapr installed - both the Dapr CLI (based on [these instructions](https://docs.dapr.io/getting-started/install-dapr-cli/))
and also the Dapr Sidecar binaries (as locally running Docker Containers for Dapr, Redis and Zipkin) (according to [the instructions provided here](https://docs.dapr.io/getting-started/install-dapr-selfhost/))

With

``` 
docker ps
```

you can check on the containers that are running.

With 

```
dapr -version
```

you can check the currently installed versions of the CLI and the Dapr Runtime.

The default components configuration is installed in directory `$HOME/.dapr` and the subdirectory `components` . This is where Redis is configured as both the default state store and the default pubsub component.

Check these out with 
```
ls $HOME/.dapr
cat  $HOME/.dapr/config.yaml
cat  $HOME/.dapr/components/statestore.yaml
cat  $HOME/.dapr/components/pubsub.yaml 
```

## Getting Started with the Dapr API

Follow the instructions in this document [Getting Started with the Dapr API](https://docs.dapr.io/getting-started/get-started-api/)

Subsequently - or even before that - feel free to get going with [the Quickstarts](https://docs.dapr.io/getting-started/quickstarts/)


A very quick demo consists of these steps:

The dapr run command launches an application, together with a sidecar. Launch a Dapr sidecar that will listen on port 3500 for a blank application named myapp:

```
dapr run --app-id myapp --dapr-http-port 3500
```

Open a new bash terminal. Update the state with an object - a JSON map with *key* set to `name` and the *value* set to `Bruce Wayne`:

```
curl -X POST -H "Content-Type: application/json" -d '[{ "key": "name", "value": "Bruce Wayne"}]' http://localhost:3500/v1.0/state/statestore
```

Retrieve the object you just stored in the state by using the state management API with the key name. In the same terminal window, run the following command:

```
curl http://localhost:3500/v1.0/state/statestore/name 
```

Look in the Redis container and verify Dapr is using it as a state store. Use the Redis CLI with the following command:
```
docker exec -it dapr_redis redis-cli
```

List the Redis keys to see how Dapr created a key value pair with the app-id you provided to dapr run as the key’s prefix:
```
keys *
```

View the state values by running:
```
hgetall "myapp||name"
```

Exit the Redis CLI with:
```
exit
```
and return to the Bash prompt.

In the same terminal window, delete thename state object from the state store.
```
curl -v -X DELETE -H "Content-Type: application/json" http://localhost:3500/v1.0/state/statestore/name
```

