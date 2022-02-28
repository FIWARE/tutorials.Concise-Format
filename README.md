# Concise NGSI-LD[<img src="https://img.shields.io/badge/NGSI-LD-d6604d.svg" width="90"  align="left" />](https://www.etsi.org/deliver/etsi_gs/CIM/001_099/009/01.04.01_60/gs_cim009v010401p.pdf)[<img src="https://fiware.github.io/tutorials.Concise-Format/img/fiware.png" align="left" width="162">](https://www.fiware.org/)<br/>

[![FIWARE Core Context Management](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/core.svg)](https://github.com/FIWARE/catalogue/blob/master/core/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.Concise-Format.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
<br/> [![JSON LD](https://img.shields.io/badge/JSON--LD-1.1-f06f38.svg)](https://w3c.github.io/json-ld-syntax/)
[![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

This tutorial introduces the concise NGSI-LD format and demonstrates its use and explains
the differences between concise and normalized NGSI-LD payloads.

The tutorial uses [cUrl](https://ec.haxx.se/) commands throughout, but is also available as
[Postman documentation](https://fiware.github.io/tutorials.Concise-Format/ngsi-ld.html)

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/d24facc3c430bb5d5aaf)


## Contents

<details>
<summary><strong>Details</strong></summary>

</details>

# Concise NGSI-LD Payloads

> "To speak much is one thing; to speak to the point another!"
>
> — Sophocles, Oedipus at Colonus




#### Device Monitor

For the purpose of this tutorial, a series of dummy agricultural IoT devices have been created, which will be attached
to the context broker. Details of the architecture and protocol used can be found in the
[IoT Sensors tutorial](https://github.com/FIWARE/tutorials.IoT-Sensors/tree/NGSI-LD) The state of each device can be
seen on the UltraLight device monitor web page found at: `http://localhost:3000/device/monitor`

![FIWARE Monitor](https://fiware.github.io/tutorials.Concise-Format/img/farm-devices.png)



![](https://fiware.github.io/tutorials.Concise-Format/img/history-graphs.png)

# Architecture

This application builds on the components and dummy IoT devices created in
[previous tutorials](https://github.com/FIWARE/tutorials.IoT-Agent/). It will use three FIWARE components: the
[Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/), the
[IoT Agent for Ultralight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/), and
[QuantumLeap](https://smartsdk.github.io/ngsi-timeseries-api/) .

Therefore the overall architecture will consist of the following elements:

-   The **FIWARE Generic Enablers**:

    -   The [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/) which will receive requests using
        [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/rep/NGSI-LD/NGSI-LD/raw/master/spec/updated/generated/full_api.json)
    -   The FIWARE [IoT Agent for UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/) which will
        receive southbound requests using
        [NGSI-LD](https://forge.etsi.org/swagger/ui/?url=https://forge.etsi.org/rep/NGSI-LD/NGSI-LD/raw/master/spec/updated/generated/full_api.json)
        and convert them to
        [UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/usermanual/index.html#user-programmers-manual)
        commands for the devices
    -   FIWARE [QuantumLeap](https://smartsdk.github.io/ngsi-timeseries-api/) subscribed to context changes and
        persisting them into a **CrateDB** database

-   A [MongoDB](https://www.mongodb.com/) database:

    -   Used by the **Orion Context Broker** to hold context data information such as data entities, subscriptions and
        registrations
    -   Used by the **IoT Agent** to hold device information such as device URLs and Keys

-   A [CrateDB](https://crate.io/) database:

    -   Used as a data sink to hold time-based historical context data
    -   offers an HTTP endpoint to interpret time-based data queries

-   An HTTP **Web-Server** which offers static `@context` files defining the context entities within the system.
-   The **Tutorial Application** does the following:
    -   Acts as set of dummy [agricultural IoT devices](https://github.com/FIWARE/tutorials.IoT-Sensors/tree/NGSI-LD)
        using the
        [UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/usermanual/index.html#user-programmers-manual)
        protocol running over HTTP.

Since all interactions between the elements are initiated by HTTP requests, the entities can be containerized and run
from exposed ports.

The overall architecture can be seen below:

![](https://fiware.github.io/tutorials.Concise-Format/img/architecture.png)

The necessary configuration information can be seen in the services section of the associated `docker-compose.yml` file.
It has been described in a previous tutorial.

# Prerequisites

## Docker and Docker Compose

To keep things simple all components will be run using [Docker](https://www.docker.com). **Docker** is a container
technology which allows to different components isolated into their respective environments.

-   To install Docker on Windows follow the instructions [here](https://docs.docker.com/docker-for-windows/)
-   To install Docker on Mac follow the instructions [here](https://docs.docker.com/docker-for-mac/)
-   To install Docker on Linux follow the instructions [here](https://docs.docker.com/install/)

**Docker Compose** is a tool for defining and running multi-container Docker applications. A series of
[YAML files](https://raw.githubusercontent.com/FIWARE/tutorials.Concise-Format/NGSI-LD/docker-compose.yml) are used to
configure the required services for the application. This means all container services can be brought up in a single
command. Docker Compose is installed by default as part of Docker for Windows and Docker for Mac, however Linux users
will need to follow the instructions found [here](https://docs.docker.com/compose/install/)

You can check your current **Docker** and **Docker Compose** versions using the following commands:

```console
docker-compose -v
docker version
```

Please ensure that you are using Docker version 20.10 or higher and Docker Compose 1.29  or higher and upgrade if
necessary.

## Cygwin for Windows

We will start up our services using a simple Bash script. Windows users should download [cygwin](http://www.cygwin.com/)
to provide a command-line functionality similar to a Linux distribution on Windows.

# Start Up

Before you start, you should ensure that you have obtained or built the necessary Docker images locally. Please clone
the repository and create the necessary images by running the commands as shown:

```console
git clone https://github.com/FIWARE/tutorials.Concise-Format.git
cd tutorials.Concise-Format
git checkout NGSI-LD

./services create
```

Thereafter, all services can be initialized from the command-line by running the
[services](https://github.com/FIWARE/tutorials.Concise-Format/blob/NGSI-LD/services) Bash script provided within the
repository:

```console
./services start
```

> :information_source: **Note:** If you want to clean up and start over again you can do so with the following command:
>
> ```console
> ./services stop
> ```


---

## Create Operations

Create Operations map to HTTP POST.

-   The `/ngsi-ld/v1/entities` endpoint is used for creating new entities
-   The `/ngsi-ld/v1/entities/<entity-id>/attrs` endpoint is used for adding new attributes

Any newly created entity must have `id` and `type` attributes and a valid `@context` definition. All other attributes
are optional and will depend on the system being modelled. If additional attributes are present though, each should
specify both a `type` and a `value`.

The response will be **201 - Created** if the operation is successful or **409 - Conflict** if the operation fails.

### Create a New Data Entity

This example adds a new **TemperatureSensor** entity to the context.

#### :one: Request:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entities/' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
      "id": "urn:ngsi-ld:TemperatureSensor:001",
      "type": "TemperatureSensor",
      "category": "sensor",
      "temperature": {
            "value": 25,
            "unitCode": "CEL"
      }
}'
```

New entities can be added by making a POST request to the `/ngsi-ld/v1/entities` endpoint.

The request will fail if the entity already exists in the context.

#### :two: Request:

You can check to see if the new **TemperatureSensor** can be found in the context by making a GET request

```console
curl -L -X GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

### Create New Attributes

This example adds a new `batteryLevel` Property and a `controlledAsset` Relationship to the existing
**TemperatureSensor** entity with `id=urn:ngsi-ld:TemperatureSensor:001`.

#### :three: Request:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
       "batteryLevel": {
            "value": 0.9,
            "unitCode": "C62"
      },
      "controlledAsset": {
            "object": "urn:ngsi-ld:Building:barn002"
      }
}'
```

New attributes can be added by making a POST request to the `/ngsi-ld/v1/entities/<entity>/attrs` endpoint.

The payload should consist of a JSON object holding the attribute names and values as shown.

All `type=Property` attributes must have a `value` associated with them. All `type=Relationship` attributes must have an
`object` associated with them which holds the URN of another entity. Well-defined common metadata elements such as
`unitCode` can be provided as strings, all other metadata should be passed as a JSON object with its own `type` and
`value` attributes

Subsequent requests using the same `id` will update the value of the attribute in the context.

#### :four: Request:

You can check to see if the new **TemperatureSensor** can be found in the context by making a GET request

```console
curl -L -X GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

As you can see there are now two additional attributes (`batteryLevel` and `controlledAsset`) added to the entity. These
attributes have been defined in the `@context` as part of the **Device** model and therefore can be read using their
short names.

### Batch Create New Data Entities or Attributes

This example uses the convenience batch processing endpoint to add three new **TemperatureSensor** entities to the
context. Batch create uses the `/ngsi-ld/v1/entityOperations/create` endpoint.

#### :five: Request:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/create' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/ld+json' \
--data-raw '[
    {
      "id": "urn:ngsi-ld:TemperatureSensor:002",
      "type": "TemperatureSensor",
      "category": "sensor",
      "temperature": {
            "value": 20,
            "unitCode": "CEL"
      }
    },
    {
      "id": "urn:ngsi-ld:TemperatureSensor:003",
      "type": "TemperatureSensor",
      "category":  "sensor",
      "temperature": {
            "value": 2,
            "unitCode": "CEL"
      }
    },
     {
      "id": "urn:ngsi-ld:TemperatureSensor:004",
      "type": "TemperatureSensor",
      "category":  "sensor",
      "temperature": {
            "type": "Property",
            "value": 100,
            "unitCode": "CEL"
      }
    }
]'
```

The request will fail if any of the attributes already exist in the context. The response highlights which actions have
been successful and the reason for failure (if any has occurred).

```jsonld
{
    "@context": "http://context/ngsi-context.jsonld",
    "success": [
        "urn:ngsi-ld:TemperatureSensor:002",
        "urn:ngsi-ld:TemperatureSensor:003",
        "urn:ngsi-ld:TemperatureSensor:004"
    ],
    "errors": []
}
```

### Batch Create/Overwrite New Data Entities

This example uses the convenience batch processing endpoint to add or amend two **TemperatureSensor** entities in the
context.

-   if an entity already exists, the request will update that entity's attributes.
-   if an entity does not exist, a new entity will be created.

#### :six: Request:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/upsert' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/ld+json' \
--data-raw '[
    {
      "id": "urn:ngsi-ld:TemperatureSensor:002",
      "type": "TemperatureSensor",
      "category": "sensor",
      "temperature": {
            "value": 21,
            "unitCode": "CEL"
      }
    },
    {
      "id": "urn:ngsi-ld:TemperatureSensor:003",
      "type": "TemperatureSensor",
      "category": "sensor",
      "temperature": {
            "value": 27,
            "unitCode": "CEL"
      }
    }
]'
```

Batch processing for create/overwrite uses the `/ngsi-ld/v1/entityOperations/upsert` endpoint.

A subsequent request containing the same data (i.e. same entities and `actionType=append`) will also succeed won't
change the context state. The `modifiedAt` metadata will be amended however.

## Read Operations

-   The `/ngsi-ld/v1/entities` endpoint is used for listing entities
-   The `/ngsi-ld/v1/entities/<entity>` endpoint is used for retrieving the details of a single entity.

For read operations the `@context` must be supplied in a `Link` header.

### Filtering

-   The `options` parameter (combined with the `attrs` parameter) can be used to filter the returned fields
-   The `q` parameter can be used to filter the returned entities

### Read a Data Entity (verbose)

This example reads the full context from an existing **TemperatureSensor** entity with a known `id`.

#### :seven: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-d 'options=sysAttrs'
```

#### Response:

TemperatureSensor `urn:ngsi-ld:TemperatureSensor:001` is returned as _normalized_ NGSI-LD. Additional metadata is
returned because `options=sysAttrs`. By default the `@context` is returned in the payload body (although this could be
moved due to content negotiation if the `Accept:application/json` had been set. The full response is shown below:

```jsonld
{
    "@context": "http://context/ngsi-context.jsonld",
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "createdAt": "2020-08-27T14:33:06Z",
    "modifiedAt": "2020-08-27T14:33:10Z",
    "category": {
        "type": "Property",
        "createdAt": "2020-08-27T14:33:06Z",
        "modifiedAt": "2020-08-27T14:33:06Z",
        "value": "sensor"
    },
    "temperature": {
        "type": "Property",
        "createdAt": "2020-08-27T14:33:06Z",
        "modifiedAt": "2020-08-27T14:33:06Z",
        "value": 25,
        "unitCode": "CEL"
    },
    "batteryLevel": {
        "value": 0.8,
        "type": "Property",
        "createdAt": "2020-08-27T14:33:10Z",
        "modifiedAt": "2020-08-27T14:33:10Z",
        "unitCode": "C62"
    },
    "controlledAsset": {
        "object": "urn:ngsi-ld:Building:barn002",
        "type": "Relationship",
        "createdAt": "2020-08-27T14:33:10Z",
        "modifiedAt": "2020-08-27T14:33:10Z"
    }
}
```

Individual context data entities can be retrieved by making a GET request to the `/ngsi-ld/v1/entities/<entity>`
endpoint.

### Read an Attribute from a Data Entity

This example reads the value of a single attribute (`temperature`) from an existing **TemperatureSensor** entity with a
known `id`.

#### :eight: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-d 'attrs=temperature'
```

#### Response:

The sensor `urn:ngsi-ld:TemperatureSensor:001` is reading at 25°C. The response is shown below:

```jsonld
{
    "@context": "http://context/ngsi-context.jsonld",
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "temperature": {
        "type": "Property",
        "value": 25,
        "unitCode": "CEL"
    }
}
```

Because `options=keyValues` was not used this is the normalized response including the metadata such as `unitCode`.
Context data can be retrieved by making a GET request to the `/ngsi-ld/v1/entities/<entity-id>` endpoint and selecting
the `attrs` using a comma separated list.

### Read a Data Entity (key-value pairs)

This example reads the key-value pairs from the context of an existing **TemperatureSensor** entities with a known `id`.

#### :nine: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'options=keyValues'
```

#### Response:

The sensor `urn:ngsi-ld:TemperatureSensor:001` is reading at 25°C. The response is shown below:

```json
{
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "category": "sensor",
    "temperature": 25,
    "batteryLevel": 0.8,
    "controlledAsset": "urn:ngsi-ld:Building:barn002"
}
```

The response contains an unfiltered list of context data from an entity containing all of the attributes of the
`urn:ngsi-ld:TemperatureSensor:001`. The payload body does not contain an `@context` attribute since the
`Accept: application/json` was set.

Combine the `options=keyValues` parameter with the `attrs` parameter to retrieve a limited set of key-value pairs.

### Read Multiple attributes values from a Data Entity

This example reads the value of two attributes (`category` and `temperature`) from the context of an existing
**TemperatureSensor** entity with a known `id`.

#### :one::zero: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'options=keyValues' \
-d 'attrs=category,temperature'
```

#### Response:

The sensor `urn:ngsi-ld:TemperatureSensor:001` is reading at 25°C. The response is shown below:

```json
{
    "id": "urn:ngsi-ld:TemperatureSensor:001",
    "type": "TemperatureSensor",
    "category": "sensor",
    "temperature": 25
}
```

Combine the `options=keyValues` parameter and the `attrs` parameter to return a list of values.

### List all Data Entities (verbose)

This example lists the full context of all **TemperatureSensor** entities.

#### :one::one: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-d 'type=TemperatureSensor'
```

#### Response:

On start-up the context was empty, four **TemperatureSensor** entities have been added by create operations so the full
context will now contain four sensors.

```jsonld
[
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:004",
        "type": "TemperatureSensor",
        "category": {
            "type": "Property",
            "value": "sensor"
        },
        "temperature": {
            "type": "Property",
            "value": 100,
            "unitCode": "CEL"
        }
    },
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:002",
        "type": "TemperatureSensor",
        "category": {
            "type": "Property",
            "value": "sensor"
        },
        "temperature": {
            "type": "Property",
            "value": 21,
            "unitCode": "CEL"
        }
    },
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:003",
        "type": "TemperatureSensor",
        "category": {
            "type": "Property",
            "value": "sensor"
        },
        "temperature": {
            "type": "Property",
            "value": 27,
            "unitCode": "CEL"
        }
    },
    {
        "@context": "http://context/ngsi-context.jsonld",
        "id": "urn:ngsi-ld:TemperatureSensor:001",
        "type": "TemperatureSensor",
        "batteryLevel": {
            "type": "Property",
            "value": 0.8,
            "unitCode": "C62"
        },
        "category": {
            "type": "Property",
            "value": "sensor"
        },
        "controlledAsset": {
            "type": "Relationship",
            "object": "urn:ngsi-ld:Building:barn002"
        },
        "temperature": {
            "type": "Property",
            "value": 25,
            "unitCode": "CEL"
        }
    }
]
```

### List all Data Entities (key-value pairs)

This example lists the `temperature` attribute of all **TemperatureSensor** entities.

#### :one::two: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'type=TemperatureSensor' \
-d 'options=keyValues' \
-d 'attrs=temperature'
```

#### Response:

The full context contains four sensors, they are returned in a random order:

```json
[
    {
        "id": "urn:ngsi-ld:TemperatureSensor:004",
        "type": "TemperatureSensor",
        "temperature": 100
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:002",
        "type": "TemperatureSensor",
        "temperature": 21
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:003",
        "type": "TemperatureSensor",
        "temperature": 27
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:001",
        "type": "TemperatureSensor",
        "temperature": 25
    }
]
```

Full context data for a specified entity type can be retrieved by making a GET request to the `/ngsi-ld/v1/entities/`
endpoint and supplying the `type` parameter, combine this with the `options=keyValues` parameter and the `attrs`
parameter to retrieve key-values.

### Filter Data Entities by ID

This example lists selected data from two **TemperatureSensor** entities chosen by `id`. Note that every `id` must be
unique, so `type` is not required for this request. To filter by `id` add the entries in a comma delimited list.

#### :one::three: Request:

```console
curl -G -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/'' \
-H 'Link: <http://context/json-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json' \
-d 'id=urn:ngsi-ld:TemperatureSensor:001,urn:ngsi-ld:TemperatureSensor:002' \
-d 'options=keyValues' \
-d 'attrs=temperature'
```

#### Response:

The response details the selected attributes from the selected entities.

```json
[
    {
        "id": "urn:ngsi-ld:TemperatureSensor:002",
        "type": "TemperatureSensor",
        "temperature": 21
    },
    {
        "id": "urn:ngsi-ld:TemperatureSensor:001",
        "type": "TemperatureSensor",
        "temperature": 25
    }
]
```

## Update Operations

Overwrite operations are mapped to HTTP PATCH:

-   The `/ngsi-ld/v1/entities/<entity-id>/attrs/<attribute>` endpoint is used to update an attribute
-   The `/ngsi-ld/v1/entities/<entity-id>/attrs` endpoint is used to update multiple attributes

### Overwrite the value of an Attribute value

This example updates the value of the `category` attribute of the Entity with `id=urn:ngsi-ld:TemperatureSensor:001`

#### :one::four: Request:

```console
curl -iX PATCH 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs/category' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
    "value": ["sensor", "actuator"],
    "type": "Property"
}'
```

Existing attribute values can be altered by making a PATCH request to the
`/ngsi-ld/v1/entities/<entity-id>/attrs/<attribute>` endpoint. The appropriate `@context` should be supplied as a `Link`
header.

### Overwrite Multiple Attributes of a Data Entity

This example simultaneously updates the values of both the `category` and `controlledAsset` attributes of the Entity
with `id=urn:ngsi-ld:TemperatureSensor:001`.

#### :one::five: Request:

```console
curl -iX PATCH 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
      "category": {
            "value": [
                  "sensor",
                  "actuator"
            ],
            "type": "Property"
      },
      "controlledAsset": {
            "type": "Relationship",
            "object": "urn:ngsi-ld:Building:barn001"
      }
}'
```

### Batch Update Attributes of Multiple Data Entities

This example uses the convenience batch processing endpoint to update existing sensors.

#### :one::six: Request:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/upsert?options=update' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '[
  {
    "id": "urn:ngsi-ld:TemperatureSensor:003",
    "type": "TemperatureSensor",
    "category": {
      "type": "Property",
      "value": [
        "actuator",
        "sensor"
      ]
    }
  },
  {
    "id": "urn:ngsi-ld:TemperatureSensor:004",
    "type": "TemperatureSensor",
    "category": {
      "type": "Property",
      "value": [
        "actuator",
        "sensor"
      ]
    }
  }
]'
```

Batch processing uses the `/ngsi-ld/v1/entityOperations/upsert` endpoint. The payload body holds an array of the
entities and attributes we wish to update. The `options=update` parameter indicates we will not remove existing
attributes if they already exist and have not been included in the payload.

An alternative would be to use the `/ngsi-ld/v1/entityOperations/update` endpoint. Unlike `upsert`, the `update`
operation will not silently create any new entities - it fails if the entities do not already exist.

### Batch Replace Entity Data

This example uses the convenience batch processing endpoint to replace entity data of existing sensors.

#### :one::seven: Request:

```console
curl -iX POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/update?options=replace' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '[
  {
    "id": "urn:ngsi-ld:TemperatureSensor:003",
    "type": "TemperatureSensor",
    "category": {
      "type": "Property",
      "value": [
        "actuator",
        "sensor"
      ]
    }
  },
  {
    "id": "urn:ngsi-ld:TemperatureSensor:004",
    "type": "TemperatureSensor",
    "temperature": {
      "type": "Property",
      "value": [
        "actuator",
        "sensor"
      ]
    }
  }
]'
```

Batch processing uses the `/ngsi-ld/v1/entityOperations/update` endpoint with a payload with the - `options=replace`
parameter, this means we will overwrite existing entities. `/ngsi-ld/v1/entityOperations/upsert` could also be used if
new entities are also to be created.

## Delete Operations

Delete Operations map to HTTP DELETE.

-   The `/ngsi-ld/v1/entities/<entity-id>` endpoint can be used to delete an entity
-   The `/ngsi-ld/v1/entities/<entity-id>/attrs/<attribute>` endpoint can be used to delete an attribute

The response will be **204 - No Content** if the operation is successful or **404 - Not Found** if the operation fails.

### Data Relationships

If there are entities within the context which relate to one another, you must be careful when deleting an entity. You
will need to check that no references are left dangling once the entity has been deleted.

Organizing a cascade of deletions is beyond the scope of this tutorial, but it would be possible using a batch delete
request.

### Delete an Entity

This example deletes the entity with `id=urn:ngsi-ld:TemperatureSensor:004` from the context.

#### :one::eight: Request:

```console
curl -iX DELETE 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:004'
```

Entities can be deleted by making a DELETE request to the `/ngsi-ld/v1/entities/<entity>` endpoint.

Subsequent requests using the same `id` will result in an error response since the entity no longer exists in the
context.

### Delete an Attribute from an Entity

This example removes the `batteryLevel` attribute from the entity with `id=urn:ngsi-ld:TemperatureSensor:001`.

#### :one::nine: Request:

```console
curl -L -X DELETE 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs/batteryLevel' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"'
```

Attributes can be deleted by making a DELETE request to the `/ngsi-ld/v1/entities/<entity>/attrs/<attribute>` endpoint.
It is important to supply the appropriate `@context` in the request in the form of a `Link` header to ensure that the
attribute name can be recognised.

If the entity does not exist within the context or the attribute cannot be found on the entity, the result will be an
error response.

### Batch Delete Multiple Entities

This example uses the convenience batch processing endpoint to delete some **TemperatureSensor** entities.

#### :two::zero: Request:

```console
curl -L -X POST 'http://localhost:1026/ngsi-ld/v1/entityOperations/delete' \
-H 'Content-Type: application/json' \
--data-raw '[
  "urn:ngsi-ld:TemperatureSensor:002",
  "urn:ngsi-ld:TemperatureSensor:003"
]'
```

Batch processing uses the `/ngsi-ld/v1/entityOperations/delete` endpoint with a payload consisting of an array of
elements to delete.

If an entity does not exist in the context, the result will be an error response.

### Batch Delete Multiple Attributes from an Entity

This example uses the PATCH `/ngsi-ld/v1/entities/<entity-id>/attrs` endpoint to delete some attributes from a
**TemperatureSensor** entity.

#### :two::one: Request:

```console
curl -L -X PATCH 'http://localhost:1026/ngsi-ld/v1/entities/urn:ngsi-ld:TemperatureSensor:001/attrs' \
-H 'Content-Type: application/json' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
--data-raw '{
      "category": {
            "value": null,
            "type": "Property"
      },
      "controlledAsset": {
            "type": "Relationship",
            "object": null
      }
}'
```

If a value is set to `null` the attribute is deleted.

### Find existing data relationships

This example returns a header indicating whether any linked data relationships remain against the entity
`urn:ngsi-ld:Building:barn002`

#### :two::two: Request:

```console
curl -iX GET 'http://localhost:1026/ngsi-ld/v1/entities/?type=TemperatureSensor&limit=0&count=true&q=controlledAsset==%22urn:ngsi-ld:Building:barn002%22' \
-H 'Link: <http://context/ngsi-context.jsonld>; rel="http://www.w3.org/ns/json-ld#context"; type="application/ld+json"' \
-H 'Accept: application/json'
```

#### Response:

```json
[]
```

Because the `limit=0` parameter has been used **no entities** are listed in the payload body, however the `count=true`
means that the count is passed as a header instead:

```text
NGSILD-Results-Count: 1
```

If `limit` was not present the payload would hold the details of every matching entity instead.

# Next Steps

Want to learn how to add more complexity to your application by adding advanced features? You can find out by reading
the other [tutorials in this series](https://ngsi-ld-tutorials.rtfd.io)

---

## License

[MIT](LICENSE) © 2022 FIWARE Foundation e.V.
