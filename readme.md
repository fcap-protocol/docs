# FCAP Whitepaper
> This project is WIP, these docs are not complete

## Problem Statement
All existing iot protocols ...

## Goal
A layer-2 independent, Flexible and Constrained Application Protocol.

### Protocol Requirements

1. Optimized for micro-controller 
    - In a constrained environments
    - Memory constrained

2. Application independent
    - Packet structure must be able to be used for any context
    - e.g. MySensors can ONLY send IOT data
    - Users should be able to define their own API / message structure, but the library should provide a consistent encoding (see below)
        - Library provides a set of data fields, and the user and use them for whatever they want
        - Look at how HTTP use defined
    - Library should provide the request life-cycle (request-response-drop)
    - basically a small version of http
    - support for raw binary data types (normal)
    - support for ascii formats 
        - ability to send human readable ascii version of a given message
        - optional feature
        - inspired by G-code

### Implementation Requirements

1. Modular implementation
    - ability to support middleware

2. Transmission methods are typically not abstracted
    - current solutions are strict / limited on a single transport layer (e.g. HTTP always on tcp/ip even tho theoretically possible on anything)
    - this helps to make the library more usable in constrained environments (cost or hardware or other reasons legal)
    - ability to forward packets between physical layers
        - can daisy chain different physical layers without re-work

## Existing Solutions
The following outlines some of the areas of inspiration for this protocol, separated loosely by application and transport layer. 
Both areas are considered as this protocol aims ...

### Application

- HTTP 
    - the success of http as an application layer protocol fit for any purpose, has inspired many of the features of this protocol

- [COAP](https://en.wikipedia.org/wiki/Constrained_Application_Protocol)
    - Very versatile
    - on top of UDP (need to check), therefore not very constricted super small devices

- [MySensors](https://www.mysensors.org/)
    - [GitHub C Library](https://github.com/mysensors/MySensors)
    - No very application independent
    - But is very constrained
    - Different formats based on where used
        - Packet structure is different between serial and RF

- [helium](https://www.helium.com/)
    - WAN protocol for sharing and collecting data on a global scale
    - specific to LoRaWan

- [lwIoT](https://github.com/lwIoT/lwiot-core)
    - not complete
    - lots of dependencies
        - freertos
        - dns client

- [gRPC]()
    - only works for point to point communication 
    - ...


### Transport

There exists many protocols which simply provide transmission layer infrastructure (layers 2-4) such as the following:

* [Radio Head](https://github.com/jecrespo/RadioHead)
    - only has a specific list of harware drivers
    - Provides
        - Packet Structures
            - only length, to, from and a blank payload section
        - Routing
        - Reliability
        - Mesh networking
    - *All kind of stuff that should be able to sit in a middleware layer of this protocol*

* [Lora WAN](https://lora-alliance.org/about-lorawan/)
    - Very complex
    - More here

- [lwIP](https://savannah.nongnu.org/projects/lwip/)
    - Transport protocol
    - could be used as a middleware layer in lwiot

* more research needed...

In summary, there are library which abstract the radio from the user just sending arbitary byte arrays, however there isn't anything which deals with it after that. I.e. I might want to send a 'dictionary' of key-value pairs in a request-response pattern. The library should do that.

### Our Solution

* Primarily addressing the application layer for embedded devices. **Layer 7**

* However the spec/protocol (middleware) should provide a mechanism to implement some transport layer functionality (such as encryption & reliability) **Layer 3 & 4**

    - This can be done using packet encapsulation
        - Where a middle ware layer may take a packet, do something with it, and produce a a superset packet (encapsulating it). Both the underlying and new packet are in the form this library specifies, allowing us to reuse the same library (good for embedded systems)


> Important to note that in embedded systems, is it common for not the entire OSI stack to exist. I.e. an embedded system often has only a Layer 1 Phy, Layer 2 data link and layer 7 application level. This protocol aims to make the application easier to use *AND* optionally add other layers

### Sample Use Cases

- No-Proprietary RF Network
    - All existing RF networks in production, typically use custom application specific protocols
    - E.g. ZigBee (proprietary license), RF Blinds, Weather 


## Features
- TLV format
- middleware functionality
- No dependencies
- simple integration
    - only 2 functions to implement, send & receive bytes

### MVP
- c-library
- Basic request/response of raw bytes
- interpret basic tlv packets


### Stretch
- 

## TODO

- Get / Response
- example on udp
- example on serial
- relay middleware


- Example on IR 
    - Reliability middle ware
        - everything is TCP
