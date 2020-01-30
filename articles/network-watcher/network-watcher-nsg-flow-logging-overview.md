---
title: Inleiding tot stroom logboek registratie voor Nsg's
titleSuffix: Azure Network Watcher
description: In dit artikel wordt uitgelegd hoe u de functie NSG flow logs van Azure Network Watcher gebruikt.
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
ms.openlocfilehash: f231a5339b9c696b2a427b0713118d4b46e30277
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840992"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Inleiding tot stroom logboek registratie voor netwerk beveiligings groepen

Stroom logboeken voor netwerk beveiligings groepen (NSG) zijn een functie van Network Watcher waarmee u informatie kunt bekijken over binnenkomend en IP-verkeer via een NSG. Stroom logboeken worden geschreven in JSON-indeling en weer gegeven uitgaande en inkomende stromen per regel. de netwerk interface (NIC) van de stroom is van toepassing op 5-tuple-informatie over de stroom (bron/doel-IP, bron/doel poort en Protocol), als het verkeer is toegestaan of geweigerd, en in versie 2, gegevens over door Voer (bytes en pakketten).


![overzicht van stroom logboeken](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Terwijl flow logboeken doel-Nsg's, worden ze niet weer gegeven als de andere logboeken. Stroom logboeken worden alleen in een opslag account opgeslagen en volgen het pad naar Logboeken dat in het volgende voor beeld wordt weer gegeven:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
U kunt stroom logboeken analyseren en inzicht krijgen in uw netwerk verkeer met behulp van [Traffic Analytics](traffic-analytics.md).

Dezelfde Bewaar beleidsregels voor andere logboeken zijn van toepassing op stroom Logboeken. U kunt het Bewaar beleid voor logboek registratie instellen van 1 dag tot 365 dagen. Als geen bewaarbeleid is ingesteld, worden de logboeken voor altijd bewaard.

> [!NOTE] 
> Het gebruik van de functie voor het retentie beleid met NSG-stroom registratie kan leiden tot een groot aantal opslag bewerkingen en de bijbehorende kosten. Als u de functie voor het Bewaar beleid niet nodig hebt, raden we u aan deze waarde in te stellen op 0.


## <a name="log-file"></a>Logboekbestand

Stroom logboeken bevatten de volgende eigenschappen:

* de **tijd** waarop de gebeurtenis is geregistreerd
* **systemId** -resource-id van de netwerk beveiligings groep.
* **categorie** : de categorie van de gebeurtenis. De categorie is altijd **NetworkSecurityGroupFlowEvent**
* **ResourceID** -de resource-id van de NSG
* **operationName** - Always NetworkSecurityGroupFlowEvents
* **Eigenschappen** : een verzameling eigenschappen van de stroom
    * **Versie** -versie nummer van het gebeurtenis schema voor het stroom logboek
    * **stromen** : een verzameling stromen. Deze eigenschap heeft meerdere vermeldingen voor verschillende regels
        * **regel** -regel waarvoor de stromen worden weer gegeven
            * **stromen** -een verzameling stromen
                * **Mac** : het MAC-adres van de NIC voor de virtuele machine waarop de stroom is verzameld
                * **flowTuples** : een teken reeks die meerdere eigenschappen voor de stroom-tuple in een door komma's gescheiden indeling bevat
                    * **Tijds tempel** : deze waarde is het tijds tempel van wanneer de stroom is opgetreden in de UNIX-epoche-indeling
                    * **Bron-IP** -het bron-IP-adres
                    * **Doel-IP** -het doel-IP-adres
                    * **Bron poort** : de bron poort
                    * **Doel poort** -de doel poort
                    * **Protocol** -het Protocol van de stroom. Geldige waarden zijn **T** voor TCP en **U** voor UDP
                    * **Verkeers stroom** : de richting van de verkeers stroom. Geldige waarden zijn **I** voor inkomend en **O** voor uitgaand verkeer.
                    * **Beslissing van verkeer** : of verkeer is toegestaan of geweigerd. Geldige **waarden zijn voor** toegestaan en **D** voor geweigerd.
                    * **Stroom status-alleen versie 2** : Hiermee wordt de status van de stroom vastgelegd. Mogelijke statussen zijn **B**: Begin, wanneer een stroom wordt gemaakt. Er worden geen statistische gegevens geleverd. **C**: Continu, voor een actieve stroom. Statistische gegevens worden geleverd met intervallen van 5 minuten. **E**: Eind, wanneer een stroom is beëindigd. Er worden statistische gegevens geleverd.
                    * **Pakketten-bron naar doel-versie 2 alleen** Het totale aantal TCP-of UDP-pakketten dat sinds de laatste update van de bron naar het doel is verzonden.
                    * **Verzonden bytes-bron naar doel-versie 2** Het totale aantal TCP-of UDP-pakket bytes dat sinds de laatste update van de bron naar het doel is verzonden. Pakketbytes omvatten de pakket-header en -nettolading.
                    * **Pakketten-doel naar bron-versie 2 alleen** Het totale aantal TCP-of UDP-pakketten dat sinds de laatste update van de bestemming naar de bron is verzonden.
                    * **Verzonden bytes-bestemming naar bron-versie 2** Het totale aantal bytes van de TCP-en UDP-pakketten dat sinds de laatste update van de bestemming naar de bron is verzonden. Pakketbytes omvatten een pakket-header en -nettolading.

## <a name="nsg-flow-logs-version-2"></a>NSG flow-logboeken versie 2

Versie 2 van de logboeken bevat een stroom status. U kunt configureren welke versie van de stroom logboeken u ontvangt. Zie [NSG-stroom logboeken](network-watcher-nsg-flow-logging-portal.md)inschakelen voor meer informatie over het inschakelen van stroom Logboeken.

Stroom status *B* wordt vastgelegd wanneer een stroom wordt gestart. De stroom status *C* en de stroom status *E* zijn staten die de voortzetting van een stroom en stroom beëindiging markeren. Zowel de *C* -als de *E* -status bevat informatie over de band breedte van het verkeer.

**Voor beeld**: flow-Tuples van een TCP-conversatie tussen 185.170.185.105:35370 en 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

Voor voortzetting *C* en de laatste *E* stroom status zijn de aantallen voor bytes en pakketten cumulatief van de tijd van de vorige record in de stroom tupel. Verwijzen naar het vorige voor beeld van de conversatie is het totale aantal overgedragen pakketten 1021 + 52 + 8005 + 47 = 9125. Het totale aantal overgebrachte bytes is 588096 + 29952 + 4610880 + 27072 = 5256000.

De volgende tekst is een voor beeld van een stroom logboek. Zoals u ziet, zijn er meerdere records die voldoen aan de eigenschappen lijst die in de voor gaande sectie wordt beschreven.

## <a name="nsg-flow-logging-considerations"></a>Aandachtspunten voor NSG flow-logboek registratie

**Overwegingen voor het opslag account**: 

- Locatie: het gebruikte opslag account moet zich in dezelfde regio bevinden als de NSG.
- Zelfs wisseling van sleutels: als u de toegangs sleutels wijzigt/roteert naar uw opslag account, werken NSG-stroom logboeken niet meer. U kunt dit probleem oplossen door de NSG-stroom Logboeken uit te scha kelen en opnieuw in te scha kelen.

**Schakel logboek registratie van de NSG-stroom in op alle nsg's die zijn gekoppeld aan een resource**: stroom logboek registratie in Azure is geconfigureerd op de NSG-resource. Een stroom wordt alleen gekoppeld aan één NSG-regel. In scenario's waarin meerdere Nsg's worden gebruikt, raden wij aan dat logboek registratie voor NSG-flow is ingeschakeld op alle Nsg's het subnet of de netwerk interface van een resource heeft toegepast om ervoor te zorgen dat alle verkeer wordt geregistreerd. Zie [hoe verkeer wordt geëvalueerd](../virtual-network/security-overview.md#how-traffic-is-evaluated) in netwerk beveiligings groepen voor meer informatie.

**Kosten**voor het vastleggen van de stroom: de logboek registratie voor NSG wordt gefactureerd op het volume van de logboeken die zijn gegenereerd. High Traffic volume kan leiden tot een groot stroom logboek volume en de bijbehorende kosten. De prijzen voor het NSG-stroom logboek bevatten geen onderliggende kosten voor opslag. Het gebruik van de functie voor het retentie beleid met NSG-stroom registratie kan leiden tot een groot aantal opslag bewerkingen en de bijbehorende kosten. Als u de functie voor het Bewaar beleid niet nodig hebt, raden we u aan deze waarde in te stellen op 0. Zie voor meer informatie [Network Watcher prijzen](https://azure.microsoft.com/pricing/details/network-watcher/) en [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie.

**Binnenkomende stromen die zijn geregistreerd van Internet ip's naar vm's zonder open bare ip's**: vm's waaraan geen openbaar IP-adres is toegewezen via een openbaar IP-adres dat is gekoppeld aan de NIC als instantie niveau openbaar IP of die deel uitmaken van een basis Load Balancer back-end-groep, gebruiken [standaard SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) en hebben een IP-adres dat is toegewezen door Azure om uitgaande connectiviteit te vergemakkelijken. Als gevolg hiervan ziet u mogelijk stroom logboek vermeldingen voor stromen van IP-adressen van Internet, als de stroom bestemd is voor een poort in het bereik van poorten die zijn toegewezen voor SNAT. Hoewel Azure deze stromen naar de virtuele machine niet toestaat, wordt de poging geregistreerd en wordt deze weer gegeven in het NSG-stroom logboek van Network Watcher. U wordt aangeraden ongewenste binnenkomend Internet verkeer expliciet met NSG te blok keren.

**Onjuist aantal bytes en pakket voor stateless stromen**: [netwerk beveiligings groepen (nsg's)](https://docs.microsoft.com/azure/virtual-network/security-overview) worden geïmplementeerd als een [stateful firewall](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Veel standaard-en interne regels die de stroom van verkeer regelen, worden echter op een staatloze manier geïmplementeerd. Als gevolg van de beperkingen van het platform worden het aantal bytes en pakketten niet vastgelegd voor stateless stromen (dat wil zeggen, verkeer stromen via stateless regels), worden ze alleen opgenomen voor stateful stromen. Het aantal bytes en pakketten dat in de NSG-stroom Logboeken (en Traffic Analytics) wordt gerapporteerd, kan daarom afwijken van de werkelijke stromen. Deze beperking is gepland om te worden vastgesteld op 2020 juni.

## <a name="sample-log-records"></a>Voorbeeld logboek records

De volgende tekst is een voor beeld van een stroom logboek. Zoals u ziet, zijn er meerdere records die voldoen aan de eigenschappen lijst die in de voor gaande sectie wordt beschreven.


> [!NOTE]
> De waarden in de eigenschap **flowTuples* zijn een door komma's gescheiden lijst.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Voor beeld van versie 1 NSG-stroom logboek indeling
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Voor beeld van versie 2 NSG-stroom logboek indeling
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

- Zie [NSG-stroom logboeken](network-watcher-nsg-flow-logging-portal.md)inschakelen voor meer informatie over het inschakelen van stroom Logboeken.
- Zie [NSG-stroom logboeken lezen](network-watcher-read-nsg-flow-logs.md)voor meer informatie over het lezen van de stroom Logboeken.
- Zie [Azure monitor-logboeken voor netwerk beveiligings groepen (nsg's) voor](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)meer informatie over NSG-logboek registratie.
- Zie [problemen met een VM-netwerk verkeer diagnosticeren](diagnose-vm-network-traffic-filtering-problem.md) om te bepalen of verkeer wordt toegestaan of geweigerd op een VM
