# Multihomed Docker Host Routing

```mermaid
---
title: Case Diagram
---
graph LR
    ISPA(("<b>Internet Provider A</b><br>GW: <i>A.A.A.Ga</i>")) <--"<i>A.A.A.A/A</i>"--> eth0
    subgraph host ["<b>DOCKER HOST.</b> Default GW: <i>B.B.B.Gb</i>"]
        eth0([eth0<br><i>A.A.A.Ha</i>]) <--> NgA
        subgraph dnet ["Docker bridge network <i>D.D.D.D/D</i>"]
            NgA("<b>NgA</b> - nginx<br>exposed on eth0")
            DC("some other<br>containers...")
            NgB("<b>NgB</b> - nginx<br>exposed on eth1")
            DC <--> NgA
            DC <--> NgB
        end
        eth1([eth1<br><i>B.B.B.Hb</i>]) <--> NgB
        
        S("Host's own servises<br>like <b>sshd</b> and etc...")
      end
    ISPB(("<b>Internet Provider B</b><br>GW: <i>B.B.B.Gb</i>")) <--"<i>B.B.B.B/B</i>"--> eth1
    style dnet fill:#ddd,stroke:#333,font-weigth:bold;
    classDef Isp fill:#66F,color:white;
    classDef Srv fill:#99F;
    classDef net fill:#F77;
    class ISPA,ISPB,eth Isp;
    class NgA,NgB,DC Srv;
    class eth0,eth1 net;
    
```
The Diagram on the top demonstrates some case then the Docker Host should be connected to different ISPs. 

Without some tunning all outgoing traffic will be routed through the default gateway **B.B.B.Gb**. Even if the request comes through **eth0** and is served by **NgA** (or Host's own services), the response will leave the **Host** through **eth1**. The only exception is when the response destination IP belongs the same subnetwork which the **eth0** is connected (i.e. belongs *A.A.A.A/A*). It's because the **Host's** routing table looks like this:
```
default via B.B.B.Gb/B dev eth1 proto static
A.A.A.A/A dev eth0 proto kernel scope link src A.A.A.Ha
B.B.B.B/B dev eth1 proto kernel scope link src B.B.B.Hb
```
