## Introduction

BUCHI’s OpenInterface provides full and well documented access to all data an instrument produces and consumes. It provides approximately the same amount of functionality as can be accessed via the device’s on-screen menu. 

The OpenInterface is a RESTful HTTP Service that runs directly on the device. The API documentation is provided as an OpenAPI file in our [GitHub repository](https://github.com/buchi-labortechnik-ag/openinterface_rotavapor/blob/master/rotavapor_openinterface.yaml). For easier readability we also provide this as [HTML doc](https://developer.buchi.digital/rotavapor/openinterface/doc/index.html).

Please follow the "Getting Started" chapter further down for an easier start. 

## Common use cases

### Process control/monitoring (High Level)

-  Start / stop distillations

-  Adjust process parameters (e.g. set vacuum during distillation)

-  Monitor running distillations

-  Centrally monitoring instrument usage/availability

-  Anomaly detection

### Subsystem control (Low Level)

- Using a pump, a heating bath, etc. in a system other than a rotavapor.

## Hardware support
### BUCHI R-300
BUCHI's Rotavapor R-300 line is a modular system. All current generation subdevices are supported but a [Interface I-300pro](https://www.buchi.com/en/products/laboratory-evaporation/interface-i-300-pro) with VacuBox is always required.

### BUCHI R-220
The R-220pro is currently not supported. If a R-220pro is detected, the OpenInterface is disabled on the I-300pro. 

## Getting started

### Wiring and IP configuration

Ensure that the I-300pro is connected to your network. Either configure a fixed IP address or set up an address reservation on your DHCP server in order to ensure that your Rotavapor system is always reachable under the same IP address.

### Enable OpenInterface

OpenInterface is disabled by default. For enabling it, use the I-300pro on-screen menu. Take note of the passwords and restart the device.
On your computer, open a web browser and enter the IP address of your system. A webpage that shows the current status of the OpenInterface should be displayed.

### API documentation
The API documentation is provided as a OpenAPI document on [GitHub](https://github.com/buchi-labortechnik-ag/openinterface_rotavapor/blob/master/rotavapor_openinterface.yaml). That document is mostly usable together with (OpenAPI tooling)[https://openapi.tools/]. For human consumtion we also provide this doc as a [HTML version](https://developer.buchi.digital/rotavapor/openinterface/doc/index.html).

This API is rather simple and it might be more enjoyable to simply try it out first.

### Explore the API

Use [Postman](https://www.getpostman.com/) or it's open-source alternative [Insomnia](https://insomnia.rest/) for exploring the OpenInterface. Download and import the OpenInterface OpenApi file from [GitHub](https://github.com/buchi-labortechnik-ag/openinterface_rotavapor/blob/master/rotavapor_openinterface.yaml). Once imported, a few more things need to be configured:

- _Disable Certificate Validation:_ In Postman this can be done under File > Settings > General > SSL Certificate Validation

- _Enter Rotavapor IP Address:_ Change base URL to contain your Rotavapor's IP address under Edit Collection > Variables

- _Add credentials:_ Under Edit Collection > Authentication choose Basic Auth, enter rw as user and the password that was shown on screen of the I-300pro at the time you've enabled the OpenInterface.

- _Change Authorization:_ For each request inside the collection, in the Authorization tab, change Type to 'Inherit auth from parent'

Afterwards you should be able to use Postman for exploring the API.


## Authentication

Basic Authentication is used. This is one of the simplest forms of
authentication and involves a username and password being sent along by the
client in the HTTP header of each request.


There are two predefined users, `rw` and `ro`. It is not
possible to define additional users. A new random password can be set for
each user via the on-screen menu of the I-300.


## Authorization

The authorization is hard-wired to the two default users. User `ro` only provides read access while user `rw` additionally allows write/control access.


## Security

The OpenInterface is disabled by default and explicitly needs to be
activated via the I-300 on-screen menu. The OpenInterface enforces encrypted
communication using TLS. It uses a self-signed certificate that is
generated for the device’s IP once the API is activated. All such certificates share a [common root certificate](https://raw.githubusercontent.com/buchi-labortechnik-ag/openinterface_rotavapor/master/root_cert.crt) that can be used for validation.


## Discovery

No discovery features are offered. The instrument’s IP address can be
determined by using its on-screen menu.


## Versioning

The version of the API is part of the URL.


## Webhooks

Webhooks are the web's callbacks. It's a lightweight low-latency alternative for status and error monitoring where polling would be disproportionate.
This also allows passing events to services like [IFTTT](https://ifttt.com/), [Microsoft Flow](https://flow.microsoft.com/), or [Zapier](https://zapier.com/).

The OpenInterface is used for managing the webhook registrations. A webhook registration at the bare minumum is an URL and a list of Rotavapor Events. Whenever such an event occurs, the rotavapor will call that URL. 


### Templates

When registering for a webhook a template containing placeholders can be
provided. The placeholders are then populated with corresponding data at the
time the event occurs.

Currently only text templates are supported (e.g. JSON, XML, CSV, etc.)


### Certificate verification

Both http and https is supported. If https is used, server certificates will be validated.

If the instrument is in an isolated network without internet access, the “disableCertCheck” flag should be set when registering webhooks in order to ensure that the webhook is called even when the instrument is unable to properly verify the server’s certificate.

### Authentication

When registering a Webhook it is possible to specify additional headers.
Therefore HTTP basic authentication or a custom header with an access key
can be used. There is no support for other types of authentication.