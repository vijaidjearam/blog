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

[![](https://mermaid.ink/img/pako:eNqVks9uwjAMxl_FyhleIAekrQiNw7ZKcOwlNE6J1CbMcTsQ4t2X4G1M22XklFi_7_Of-KzaaFFplfBtxNDi0puOzNAEyKfqPQaezxeL5VNVJ5yQNNRHhPIEKpLEgn4DhRadFuzVOSR497yHdQ0P1hKmdIfoBY8MG6SMwTq4eIf0MUaGle8Rghnwd0_b1TZb0LUpiUIVA5uWE_AeJbEAMCYfOvAHMFI_dH7CALvTlSx5_-H-OTBx35XaXKntjxWI183hZ481xclbFJNbg8XbE1qRqpkakAbjbf7ac4k1KvN5Bkrnq0Vnxp4b1YRLRs3IcXMKrdJMI87UeLCGvzZBaWf6lKNoPUd6lnW5bs3lA0URvtc?type=png)](https://mermaid.live/edit#pako:eNqVks9uwjAMxl_FyhleIAekrQiNw7ZKcOwlNE6J1CbMcTsQ4t2X4G1M22XklFi_7_Of-KzaaFFplfBtxNDi0puOzNAEyKfqPQaezxeL5VNVJ5yQNNRHhPIEKpLEgn4DhRadFuzVOSR497yHdQ0P1hKmdIfoBY8MG6SMwTq4eIf0MUaGle8Rghnwd0_b1TZb0LUpiUIVA5uWE_AeJbEAMCYfOvAHMFI_dH7CALvTlSx5_-H-OTBx35XaXKntjxWI183hZ481xclbFJNbg8XbE1qRqpkakAbjbf7ac4k1KvN5Bkrnq0Vnxp4b1YRLRs3IcXMKrdJMI87UeLCGvzZBaWf6lKNoPUd6lnW5bs3lA0URvtc)
