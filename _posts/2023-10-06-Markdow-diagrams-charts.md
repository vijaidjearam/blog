---
layout: post
date: 2023-10-06 08:45:39
title: MarkDown Diagrams and Charts
category: MarkDown
tags: markdown
---

Source: (Hedgedoc.org)[https://demo.hedgedoc.org/features?both]
### Diagrams

#### UML Sequence Diagrams

You can render sequence diagrams like this:

```mermaid
    sequenceDiagram;
        Client-->>DHCPsever: Pxe DHCP request;
        DHCPsever-->>Client: DHCP Offer with IP Address;
        DHCPsever-->>Client: DHCP Offer with Next Server Info;
        DHCPsever-->>Client: DHCP Offer with Boot File name;
        Client-->>TFTPserver: Client Contacts the Next server using ip address given by the DHCP;
        Client-->>TFTPserver: Client requests the boot file given by the DHCP ;
        TFTPserver-->>Client: Provides the Boot File required;
```

More information about **sequence diagrams** syntax [here](https://bramp.github.io/js-sequence-diagrams/).
