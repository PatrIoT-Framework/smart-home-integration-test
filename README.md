# Smart House Tests

This repository contains tests for virtual smart home, written with [PatrIoT Framework](htpps://patriot-framework.io).

__Prerequisites__

This is the list of installed software you'll need in order to run 
this example of integration tests with PatrIoT Framework.
 
* Java in version 8 or higher
* Maven
* Docker
* Docker compose

## Environment setup
In order to run these tests you need to have the tested software prepared and ready.
Please follow the `Build Docker` at repositories:

* [Virtual Home](https://github.com/PatrIoT-Framework/virtual-smart-home#building-docker-image) with `IMAGE_TAG=patriotframework/virtual-home`
* [Smart Gateway](https://github.com/PatrIoT-Framework/smart-home-gateway#build-docker-image) with `IMAGE_TAG=patriotframework/smart-gateway`

Pull the router image by executing comand:

```bash
$ docker pull patriotframework/patriot-router:latest
```

Then you can start up the monitoring environment:

```bash
$ docker-compose up -d
```

The command will start the monitoring environment with output similar to this:

```
Creating network "smarthousetests_default" with the default driver
Creating smarthousetests_mongodb_1       ... done
Creating smarthousetests_elasticsearch_1 ... done
Creating smarthousetests_kibana_1        ... done
Creating smarthousetests_graylog_1       ... done
```

Please not the network name and execute following command:

```bash
$ docker newtwork inspect ${NETWORK_NAME}
```

Where the network name is the name printed by `docker-compose` on 
the first line, in this case the name would be `smarthousetests_default`,
and note the ip address for `graylog` container, in our case named
`smarthousetests_graylog_1`.
The output for graylog will be similar to 

```
            "eb410e97891a323aca5120bb46e319cad66b63e181ad60f2fdb6dbbe478dab01": {
                "Name": "smarthousetests_graylog_1",
                "EndpointID": "9204f94f1c3c7699c209a81e6b7053f55c70bf278be0c6d25e6139510feca712",
                "MacAddress": "02:42:ac:12:00:05",
                "IPv4Address": "172.18.0.5/16",
                "IPv6Address": ""
            }
```

When you'll have the ip address of graylog, set it to the file
`src/test/resources/patriot.properties` to key `io.patriot_framework.monitoring.addr`, 
in this case

```properties
io.patriot_framework.monitoring.addr=172.18.0.5
```

By this step all the preparation is done.

## Executing the tests

When all of the preparation is done you can execute all of the tests
by executing

```bash
$ mvn clean test
```

The command will:

* Setup the testing environment and deploy testes applications
* Execute the tests
* Clean up all resources

### Clean up after failed setup or tear down

If for some reason the setup phase ended in the middle of setup 
(e.g. user interruption) or before tear down, you can delete all 
of the docker resources related to test run by following command

```bash
docker rm -f smarthome Internal1 External1 gateway
docker network rm SmartHome GatewayNetwork
```

### Stopping the monitoring environment

If you'll need to stop the monitoring environment, without deletion
(e.g. stop the containers but with data intact) you can perform it
by following command

```bash
$ docker-compose stop
```

Then the environment can be restarted by

```bash
$ docker-compose start
```

If you'd need to remove all of the resources (network, containers),
then you'll need to execute

```bash
$ docker-compose down
```

__WARNING__ 
The removal of the containers does mean, that you'll need to start 
the deployment from the top, because the network for the monitoring 
environment will be deleted and new one might have different properties
(e.g. network address).


