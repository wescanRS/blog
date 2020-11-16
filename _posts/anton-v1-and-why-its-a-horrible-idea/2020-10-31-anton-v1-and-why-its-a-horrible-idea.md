---
title: Anton V1 and why it's a horrible idea
date: 2020-10-31 11:11:11 +07:00
tags: [anton, anton-v1]
description: Read the specification of the Anton V1 protocol and why it's such a horrible idea
image: "/you_named_it_anton.gif"
---


So for [wescan](https://wescan.retarders.top/), we have multiple servers that do their part on scanning the internet, obviously though, we wanted them to be synchronized so we have no duplicate scans

We decided to design Anton


![you_named_it_anton.gif](you_named_it_anton.gif)

Anton would be a protocol (that also stood for **AN**other **T**errible pr**O**tocol definitio**N**, other than the silicon valley reference) that would tell each scanning server what to scan and the scanning server would return back the results and the anton server would save them in the database.

This was a bad idea and we knew it, but it would probably be temporary after we got funding and then we could start using some pub/sub service from our preferred cloud provider

### Why is it a bad idea?
- Imagine ~8 different servers from around the world sending a couple megabytes of data to a single server at the same time, that would be hell, that's a DDoS attack!

- Authentication would be a struggle, we didn't know how to implement it, we wanted to keep the whole protocol as minimalistic as possible. Our idea was to have a different random key that would change for every scan and with that key, scanning servers would be able to log into the anton server

- I honestly know nothing about protocols and i just realized it, a protocol definition is not that simple to implement using code, i took a look at the [minecraft protocol](https://wiki.vg/Protocol) and i designed a few packets

```yaml
id: 0x01
state: connecting
sender: client
receiver: server

fields:
  region:
    type: string
    description: 'the continent where the client is located'
    possible:
      - 'America'
      - 'Europe'
      - 'Asia'
      - 'Africa'
```

```yaml
id: 0x02
state: stable
sender: server
receiver: client

fields:
  targets:
    type: list-string
    description: 'x targets to scan where x is set on the anton server configuration'
```

```yaml
id: 0x03
state: stable
sender: client
receiver: server

fields:
  results:
    type: list-dict
    description: 'a list of dictionaries containing each target scanned and the results'
```

it looks easy to write how the packets work, but it would be hard to implement them in a non object-oriented approach

### How did we solve it? (hint: we didn't)
👀