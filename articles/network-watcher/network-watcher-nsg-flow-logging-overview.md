---
title: Inleiding tot stroomregistratie voor NSGs
titleSuffix: Azure Network Watcher
description: In dit artikel wordt uitgelegd hoe u de functie NSG-stroomlogboeken van Azure Network Watcher gebruiken.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228255"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Inleiding tot stroomlogboekregistratie voor netwerkbeveiligingsgroepen

Stroomlogboeken voor netwerkbeveiligingsgroepen zijn een functie van Network Watcher waarmee u informatie kunt bekijken over inkomend en uitgaand IP-verkeer via een netwerkbeveiligingsgroep (NSG). Stroomlogboeken hebben de JSON-indeling en bevatten uitgaand en binnenkomend verkeer per regel, de netwerkinterface (NIC) waarop de stroom betrekking heeft, een tuple met vijf gegevens over de stroom (bron-/doel-IP, bron-/doelpoort en protocol), of het verkeer is toegestaan of geweigerd en, in versie 2, informatie over doorvoer (bytes en pakketten).


![overzicht van stroomlogboeken](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Terwijl stroomlogboeken op NSG's zijn gericht, worden ze niet hetzelfde weergegeven als de andere logboeken. Stroomlogboeken worden alleen opgeslagen in een opslagaccount en volgen het logboekpad dat in het volgende voorbeeld wordt weergegeven:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
U stroomlogboeken analyseren en inzicht krijgen in uw netwerkverkeer met behulp van [verkeersanalyses.](traffic-analytics.md)

Hetzelfde bewaarbeleid voor andere logboeken is van toepassing op stroomlogboeken. U het beleid voor logboekbehoud instellen van 1 dag tot 365 dagen. Als een bewaarbeleid niet is ingesteld, worden de logboeken voor altijd onderhouden.

## <a name="log-file"></a>Logboekbestand

Stroomlogboeken bevatten de volgende eigenschappen:

* **tijd** - Tijd waarop de gebeurtenis is geregistreerd
* **systemId** - Network Security Group resource Id.
* **categorie** - De categorie van het evenement. De categorie is altijd **NetworkSecurityGroupFlowEvent**
* **resourceid** - De resource Id van de NSG
* **operationName** - Altijd NetworkSecurityGroupFlowEvents
* **eigenschappen** - Een verzameling eigenschappen van de stroom
    * **Versie** - Versienummer van het gebeurtenisschema van het stroomlogboek
    * **stromen** - Een verzameling stromen. Deze eigenschap heeft meerdere vermeldingen voor verschillende regels
        * **regel** - Regel waarvoor de stromen worden vermeld
            * **stromen** - een verzameling stromen
                * **mac** - Het MAC-adres van de NIC voor de VM waar de stroom is verzameld
                * **flowTuples** - Een tekenreeks die meerdere eigenschappen bevat voor de stroomtuple in door komma's gescheiden indeling
                    * **Tijdstempel** - Deze waarde is de tijdstempel van wanneer de stroom plaatsvond in unix-tijdvakformaat
                    * **Bron-IP** - De bron-IP
                    * **Bestemming IP** - Het doel-IP
                    * **Bronpoort** - De bronpoort
                    * **Bestemmingshaven** - De bestemmingshaven
                    * **Protocol** - Het protocol van de stroom. Geldige waarden zijn **T** voor TCP en **U** voor UDP
                    * **Traffic Flow** - De richting van de verkeersstroom. Geldige waarden zijn **i** voor inkomende en **O** voor uitgaande.
                    * **Verkeersbesluit** - Of het verkeer is toegestaan of geweigerd. Geldige waarden zijn **A** voor toegestaan en **D** voor geweigerd.
                    * **Flow State - Alleen versie 2** - Legt de status van de stroom vast. Mogelijke statussen zijn **B**: Begin, wanneer een stroom wordt gemaakt. Er worden geen statistische gegevens geleverd. **C**: Continu, voor een actieve stroom. Statistische gegevens worden geleverd met intervallen van 5 minuten. **E**: Eind, wanneer een stroom is beëindigd. Er worden statistische gegevens geleverd.
                    * **Pakketten - Bron naar bestemming - Alleen versie 2** Het totale aantal TCP- of UDP-pakketten dat sinds de laatste update van bron naar bestemming is verzonden.
                    * **Verzonden bytes - Bron naar bestemming - alleen versie 2** Het totale aantal TCP- of UDP-pakketbytes dat sinds de laatste update van bron naar bestemming is verzonden. Pakketbytes omvatten de pakket-header en -nettolading.
                    * **Pakketten - Bestemming naar bron - alleen versie 2** Het totale aantal TCP- of UDP-pakketten dat sinds de laatste update van bestemming naar bron is verzonden.
                    * **Verzonden bytes - Bestemming naar bron - alleen versie 2** Het totale aantal TCP- en UDP-pakketbytes dat sinds de laatste update van bestemming naar bron is verzonden. Pakketbytes omvatten een pakket-header en -nettolading.

## <a name="nsg-flow-logs-version-2"></a>NSG-stroomlogboeken versie 2

Versie 2 van de logboeken introduceert stroomstatus. U configureren welke versie van stroomlogboeken u ontvangt. Zie [NSG-stroomlogboekregistratie inschakelen](network-watcher-nsg-flow-logging-portal.md)voor meer informatie over het inschakelen van stroomlogboeken.

Stroomstatus *B* wordt geregistreerd wanneer een stroom wordt gestart. Stroomstatus *C* en stroomstatus *E* zijn staten die de voortzetting markeren van respectievelijk een stroom- en stroombeëindiging. Zowel *C-* als *E-statussen* bevatten informatie over verkeersbandbreedte.

**Voorbeeld:** Stroomt tuples uit een TCP-gesprek tussen 185.170.185.105:35370 en 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,I,I,105,105,10.2.0.4,35370,35370,2.0.4,35370,23,A,C,1021,58096,8005,4610880" "1493696138,185.185.105,10.2.0,4,35370,23,T,I,A,E,52.29952,47072"

Voor *vervolg-C-* en end *E-stroomstatussen* zijn byte- en pakkettellingen geaggregeerde tellingen vanaf het moment van de vorige stroomtuplerecord. Verwijzend naar het vorige voorbeeldgesprek is het totale aantal overgekomen pakketten 1021+52+8005+47 = 9125. Het totale aantal overgedragen bytes is 588096+29952+4610880+27072 = 5256000.

De tekst die volgt is een voorbeeld van een stroomlogboek. Zoals u zien, zijn er meerdere records die de eigenschappenlijst volgen die in de vorige sectie is beschreven.

## <a name="nsg-flow-logging-considerations"></a>Overwegingen voor nsg-stroomregistratie

**Overwegingen van de opslagrekening**: 

- Locatie: Het gebruikte opslagaccount moet zich in dezelfde regio bevinden als de NSG.
- Sleutelrotatie zelf beheren: als u de toegangssleutels van uw opslagaccount wijzigt/roteert, werkt NSG Flow Logs niet meer. Als u dit probleem wilt oplossen, moet u NSG-stroomlogboeken uitschakelen en opnieuw inschakelen.

**NSG-stroomlogboekregistratie inschakelen voor alle NSG's die aan een resource zijn gekoppeld:** Stroomregistratie in Azure is geconfigureerd op de NSG-bron. Een stroom wordt alleen gekoppeld aan één NSG-regel. In scenario's waarin meerdere NSG's worden gebruikt, raden we aan dat NSG-stroomregistratie is ingeschakeld op alle NSG's die het subnet of de netwerkinterface van een bron toepassen om ervoor te zorgen dat al het verkeer wordt geregistreerd. Zie voor meer informatie [hoe het verkeer wordt geëvalueerd](../virtual-network/security-overview.md#how-traffic-is-evaluated) in netwerkbeveiligingsgroepen.

**Stroomregistratiekosten**: NSG-stroomregistratie wordt gefactureerd op het volume van de geproduceerde logboeken. Een hoog verkeersvolume kan leiden tot een groot stroomlogboekvolume en de bijbehorende kosten. Nsg Flow log prijzen omvat niet de onderliggende kosten van opslag. Het gebruik van de bewaarbeleidfunctie met NSG Flow Logging betekent dat u voor langere tijd afzonderlijke opslagkosten hoeft te maken. Als u de functie bewaarbeleid niet nodig hebt, raden we u aan deze waarde in te stellen op 0. Zie [Network Watcher Pricing](https://azure.microsoft.com/pricing/details/network-watcher/) en Azure Storage [Pricing](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie voor meer informatie.

**Binnenkomende stromen die zijn geregistreerd van internet-IP's naar IP's zonder openbare IP-adressen:** VM's die geen openbaar IP-adres hebben toegewezen via een openbaar IP-adres dat is gekoppeld aan de NIC als openbaar IP-adres op instantieniveau, of die deel uitmaken van een back-endpool voor basislasten, standaard [SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) gebruiken en een IP-adres hebben toegewezen door Azure om uitgaande connectiviteit te vergemakkelijken. Als gevolg hiervan ziet u mogelijk stroomlogboekvermeldingen voor stromen van internet-IP-adressen, als de stroom is bestemd voor een poort in het bereik van poorten die zijn toegewezen voor SNAT. Hoewel Azure deze stromen niet toestaat voor de VM, wordt de poging geregistreerd en wordt deze weergegeven in het NSG-stroomlogboek van Network Watcher. Wij raden u aan ongewenst inkomend internetverkeer expliciet te blokkeren met NSG.

**Onjuiste byte- en pakkettellingen voor stateloze stromen**: [Network Security Groups (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview) worden geïmplementeerd als een [stateful firewall](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Veel standaard/interne regels die de verkeersstroom regelen, worden echter stateloos geïmplementeerd. Vanwege platformbeperkingen worden de bytes en pakkettentellingen niet geregistreerd voor stateloze stromen (dat wil zeggen verkeersstromen die door staatloze regels gaan), ze worden alleen geregistreerd voor stateful flows. Het aantal bytes en pakketten dat wordt gerapporteerd in NSG Flow Logs (en Traffic Analytics) kan dus afwijken van de werkelijke stromen. Deze beperking moet in juni 2020 worden vastgesteld.

## <a name="sample-log-records"></a>Voorbeeldlogboekrecords

De tekst die volgt is een voorbeeld van een stroomlogboek. Zoals u zien, zijn er meerdere records die de eigenschappenlijst volgen die in de vorige sectie is beschreven.


> [!NOTE]
> Waarden in de eigenschap **flowTuples* zijn een door komma's gescheiden lijst.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Voorbeeld van het voorbeeld van de NSG-stroomindeling van versie 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>Voorbeeld van het voorbeeld van de nsg-stroomindeling van versie 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Volgende stappen

- Zie [NSG-stroomlogboekregistratie inschakelen](network-watcher-nsg-flow-logging-portal.md)voor meer informatie over het inschakelen van stroomlogboeken.
- Zie [NSG-stroomlogboeken lezen](network-watcher-read-nsg-flow-logs.md)voor meer informatie over het lezen van de stroomlogboeken.
- Zie [Azure Monitor-logboeken voor netwerkbeveiligingsgroepen (NSG's) voor](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)meer informatie over NSG-logboekregistratie.
- Zie [Een VM-netwerkfilterprobleem diagnosticeren](diagnose-vm-network-traffic-filtering-problem.md) als u wilt bepalen of verkeer is toegestaan of geweigerd aan of van een vm
