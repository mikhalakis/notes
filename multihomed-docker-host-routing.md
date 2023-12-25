# Multihomed Docker Host Routing

```mermaid
---
title: Case Diagram
---
graph LR
    ISPA(("<b>InternetProvider A</b><br>GW: A.A.A.Ga")) <--A.A.A.A/A--> eth0
    subgraph host ["<b>DOCKER HOST.</b> Default GW: B.B.B.Gb"]
        eth0([eth0]) <--> NgA
        subgraph dnet ["Docker bridge network D.D.D.D/D"]
            NgA("<b>NgA</b> - nginx<br>exposed on eth0")
            DC("some other<br>containers...")
            NgB("<b>NgB</b> - nginx<br>exposed on eth1")
            DC <--> NgA
            DC <--> NgB
        end
        eth1([eth1]) <--> NgB
        
        S("Host's own servises<br>like <b>sshd</b> and etc...")
      end
    ISPB(("<b>Internet Provider B</b><br>GW: B.B.B.Gb")) <--B.B.B.B/B--> eth1
    style dnet fill:#ddd,stroke:#333,font-weigth:bold;
    classDef Isp fill:#66F,color:white;
    classDef Srv fill:#99F;
    classDef net fill:#F77;
    class ISPA,ISPB,eth Isp;
    class NgA,NgB,DC Srv;
    class eth0,eth1 net;
    
```
The Diagram on the top demonstrates some case then the Docker Host should be connected to different ISPs. 

Without some tunning all outgoing traffic will be routed through the default gateway **B.B.B.Gb**. Even if the request comes through **eth0** and is served by **NgA** (or Host's own services), the response will leave the **Host** through **eth1**. The only exception is that when the response destination IP is in the same subnetwork in which the **eth0** is connected (i.e. belongs *A.A.A.A/A*). It's because the **Host's** routing table looks like this:
```
default via B.B.B.Gb/B dev eth1 proto static
A.A.A.A/A dev eth0 proto kernel scope link src A.A.A.Ha
B.B.B.B/B dev eth1 proto kernel scope link src B.B.B.Hb
```
