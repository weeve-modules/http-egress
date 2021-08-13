# HTTP Egress

|                |                                   |
| -------------- | --------------------------------- |
| Name           | HTTP Egress                       |
| Version        | v0.0.1                            |
| Dockerhub Link | weevenetwork/http-egress       |
| Authors        | Jakub Grzelak                     |



- [HTTP Egress](#http-egress)
  - [Description](#description)
  - [Features](#features)
  - [Environment Variables](#environment-variables)
    - [Module Specific](#module-specific)
    - [Set by the weeve Agent on the edge-node](#set-by-the-weeve-agent-on-the-edge-node)
  - [Dependencies](#dependencies)
  - [Input](#input)
  - [Output](#output)
  - [Docker Compose Example](#docker-compose-example)

## Description

HTTP Egress is a module responsible for passing all input data to a specified URL or a Webhook address by HTTP ReST API.
Two ReST API methods are supported: POST an GET. If POST is chosen, then data are passed to a specified URL. If GET is chosen,
then data are passed as URL's query string parameters.

This module is containerized using Docker.

## Features

- Egress data from data service
- Send data to a specified URL and Webhook
- Uses HTTP ReST API

## Environment Variables

### Module Specific

The following module configurations can be provided in a data service designer section on weeve platform:

| Name               | Environment Variables | Type   | Description                                                                                           |
| ------------------ | --------------------- | ------ | ----------------------------------------------------------------------------------------------------- |
| Egress Webhook URL | EGRESS_WEBHOOK_URL    | string | Webhook URL or other HTTP ReST endpoint address where data is sent.                                   |
| Method             | METHOD                | string | ReST API request method: POST or GET.                                                                 |
| Input Labels       | LABELS                | string | List of comma (,) separated labels to read from a previous module. Leave empty ("") to keep all data. |
| Timestamp          | TIMESTAMP             | string | Label for timestamp field for incoming data. If left empty, webhook module will add the timestamp.    |

Moreover, other features required for establishing the inter-container communication between modules in a data service are set by weeve agent.

### Set by the weeve Agent on the edge-node

| Environment Variables | type   | Description        |
| --------------------- | ------ | ------------------ |
| MODULE_NAME           | string | Name of the module |

## Dependencies

```txt
Flask==1.1.1
requests
python-decouple==3.4
```

## Input

Input to this module is JSON body single object or array of objects:

Example:

```node
{
  temperature: 15,
}
```

```node
[
  {
    temperature: 15,
  },
  {
    temperature: 21,
  },
  {
    temperature: 25,
  },
];
```

## Output

Output to this module is identical JSON body as input that is sent to a chosen endpoint.

Example:

```node
{
  temperature: 15,
}
```

```node
[
  {
    temperature: 15,
  },
  {
    temperature: 21,
  },
  {
    temperature: 25,
  },
];
```

## Docker Compose Example

```yml
version: "3"
services:
  webhook:
    image: weevenetwork/http-egress
    environment:
      MODULE_NAME: http-egress
      EGRESS_WEBHOOK_URL: https://hookb.in/r1YwjDyn7BHzWWJVK8Gq
      HANDLER_PORT: 80
      METHOD: POST
      LABELS: ""
      TIMESTAMP: ""
    ports:
      - 5000:80
```