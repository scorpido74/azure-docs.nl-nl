---
title: bestand opnemen
description: bestand opnemen
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 06/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: cb35021ad7e4d735a7dd521e39e4fe5fd102ae01
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888359"
---
### <a name="general-requirements"></a>Algemene vereisten

* Het VNet moet in hetzelfde abonnement en dezelfde regio voorkomen als het Batch-account dat u gebruikt om de pool te maken.

* De pool die van het VNet gebruikmaakt, kan een maximum van 4096 knooppunten bevatten.

* Het subnet dat is opgegeven voor de pool moet voldoende vrije IP-adressen hebben voor het aantal virtuele machines voor de pool, ofwel de som van de `targetDedicatedNodes`- en `targetLowPriorityNodes`-eigenschappen van de pool. Als het subnet onvoldoende vrije IP-adressen heeft, wijst de pool de rekenknooppunten gedeeltelijk toe en wordt een fout weergegeven voor het aanpassen van de grootte.

* Uw Azure Storage-eindpunt moet worden omgezet door alle aangepaste DNS-servers die u beschikbaar maakt voor uw VNet. Vooral URL's met de indeling `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` en `<account>.blob.core.windows.net` moeten kunnen worden omgezet.

Aanvullende vereisten voor VNet verschillen, afhankelijk van of de Batch-pool zich in de configuratie van de virtuele machine bevindt of in de configuratie van Cloud Services. Voor nieuwe implementaties van pools in een VNet wordt de configuratie van de virtuele machine aanbevolen.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools in de configuratie van virtuele machines

**Ondersteunde VNets**: alleen op Azure Resource Manager gebaseerde VNets

**Subnet-ID**: bij het opgeven van het subnet met behulp van de Batch-API's moet u de *resource-id* van het subnet gebruiken. De subnet-id heeft de vorm van:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Machtigingen**: controleer of uw beveiligingsbeleid of de vergrendelingen die voor het VNet-abonnement of de resourcegroep gelden, een gebruikersmachtiging beperkt om het VNet te beheren.

**Aanvullende netwerkresources**: Batch kent automatisch extra netwerkresources toe aan de resourcegroep met het VNet.

> [!IMPORTANT]
>Voor elke knoop punt met 100 toegewezen of lage prioriteit wordt de batch: één netwerk beveiligings groep (NSG), één openbaar IP-adres en één load balancer. De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Voor grote Pools moet u mogelijk een quotum verhoging aanvragen voor een of meer van deze resources.

#### <a name="network-security-groups-batch-default"></a>Netwerk beveiligings groepen: batch-standaard

Het subnet moet inkomende communicatie van de batch-service toestaan om taken te kunnen plannen op de reken knooppunten en uitgaande communicatie om te communiceren met Azure Storage of andere resources, zoals nodig is voor uw werk belasting. Voor Pools in de virtuele-machine configuratie voegt batch Nsg's toe op het niveau van netwerk interfaces (Nic's) dat is gekoppeld aan reken knooppunten. Deze Nsg's zijn geconfigureerd met de volgende aanvullende regels:

* Binnenkomend TCP-verkeer op de poorten 29876 en 29877 van de batch service-IP-adressen die overeenkomen met het servicetag `BatchNodeManagement` .
* Binnenkomend TCP-verkeer op poort 22 (Linux-knooppunten) of poort 3389 (Windows-knooppunten) om externe toegang te verlenen. Voor bepaalde typen taken met meerdere instanties in Linux (zoals MPI), moet u ook SSH-poort 22 verkeer toestaan voor IP-adressen in het subnet met de batch Compute-knoop punten. Dit kan worden geblokkeerd per NSG-regels op subnetniveau (zie hieronder).
* Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk. Dit kan worden gewijzigd per NSG-regels op subnetniveau (zie hieronder).
* Uitgaand verkeer op een wille keurige poort naar Internet. Dit kan worden gewijzigd per NSG-regels op subnetniveau (zie hieronder).

> [!IMPORTANT]
> Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als communicatie met de rekenknooppunten in het opgegeven subnet wordt geweigerd door een netwerkbeveiligingsgroep, zet de Batch-service de status van de rekenknooppunten op **Onbruikbaar**. Daarnaast moeten er geen resource vergrendelingen worden toegepast op resources die zijn gemaakt door batch, anders kan dit ertoe leiden dat het opschonen van resources als gevolg van door de gebruiker geïnitieerde acties, zoals het verwijderen van een groep, wordt voor komen.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Netwerk beveiligings groepen: regels op subnetniveau opgeven

Het is niet vereist om Nsg's op het subnet van het virtuele netwerk op te geven, omdat met batch een eigen Nsg's wordt geconfigureerd (zie hierboven). Als er een NSG is gekoppeld aan het subnet waarin batch compute nodes zijn geïmplementeerd of als u aangepaste NSG regels wilt Toep assen om de standaard instellingen te overschrijven, moet u deze NSG configureren met ten minste de regels voor binnenkomende en uitgaande verbindingen, zoals wordt weer gegeven in de volgende tabellen.

Configureer inkomend verkeer op poort 3389 (Windows) of 22 (Linux) alleen als u externe toegang tot de reken knooppunten van buiten bronnen wilt toestaan. Mogelijk moet u poort 22-regels inschakelen op Linux als u ondersteuning nodig hebt voor taken met meerdere instanties met bepaalde MPI-Runtimes. Het toestaan van verkeer op deze poorten is niet strikt vereist om de groeps berekenings knooppunten bruikbaar te maken.

**Inkomende beveiligingsregels**

| IP-adressen van bron | Bron servicetag | Bronpoorten | Doel | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- | --- |
| N.v.t. | `BatchNodeManagement`[Servicetag (als](../articles/virtual-network/security-overview.md#service-tags) u een regionale variant gebruikt, in dezelfde regio als uw batch-account) | * | Alle | 29876-29877 | TCP | Toestaan |
| IP-adressen van gebruikers bronnen voor het op afstand verkrijgen van toegang tot de reken knooppunten en/of het subnet van het reken knooppunt voor Linux-taken voor meerdere exemplaren, indien nodig. | N.v.t. | * | Alle | 3389 (Windows), 22 (Linux) | TCP | Toestaan |

> [!WARNING]
> De IP-adressen van de batch-service kunnen na verloop van tijd worden gewijzigd. Daarom is het raadzaam om de servicetag `BatchNodeManagement` (of Regional variant) te gebruiken voor NSG-regels. Het is niet raadzaam om NSG regels rechtstreeks met batch service-IP-adressen in te vullen.

**Uitgaande beveiligingsregels**

| Bron | Bronpoorten | Doel | Doelservicetag | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- | --- |
| Alle | * | [Servicetag](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(als u een regionale variant gebruikt, in dezelfde regio als uw batch-account) | 443 | TCP | Toestaan |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools die zijn gemaakt in de Cloud Services-configuratie

**Ondersteunde VNets**: klassieke VNets

**Subnet-ID**: bij het opgeven van het subnet met behulp van de Batch-API's moet u de *resource-id* van het subnet gebruiken. De subnet-id heeft de vorm van:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Machtigingen**: de `Microsoft Azure Batch`-service-principal moet de toegangsbeheerrol `Classic Virtual Machine Contributor` hebben voor het betreffende VNet.

#### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Het subnet moet toestaan dat met binnenkomende communicatie van de Batch-service taken op de rekenknooppunten kunnen worden gepland, en toestaan dat uitgaande communicatie mogelijk is met Azure Storage of andere resources.

U hoeft geen netwerkbeveiligingsgroep op te geven omdat Batch binnenkomende communicatie alleen configureert van Batch IP-adressen naar de poolknooppunten. Als echter aan het opgegeven subnet netwerkbeveiligingsgroepen en/of een firewall zijn gekoppeld, configureert u de binnenkomende en uitgaande beveiligingsregels zoals wordt weergegeven in de volgende tabellen. Als communicatie met de rekenknooppunten in het opgegeven subnet wordt geweigerd door een netwerkbeveiligingsgroep, zet de Batch-service de status van de rekenknooppunten op **Onbruikbaar**.

Configureer inkomend verkeer op poort 3389 voor Windows als u RDP-toegang tot de groeps knooppunten wilt toestaan. Het is niet vereist dat de poolknooppunten kunnen worden gebruikt.

**Inkomende beveiligingsregels**

| IP-adressen van bron | Bronpoorten | Doel | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- |
Alle <br /><br />Hoewel dit in feite 'alles toestaan' vereist, past de Batch-service een regel voor toegangsbeheerlijsten toe op het niveau van elk knooppunt, die ervoor zorgt dat alle IP-adressen van niet-Batch-services worden uitgefilterd. | * | Alle | 10100, 20100, 30100 | TCP | Toestaan |
| Optioneel, voor het toestaan van RDP-toegang tot reken knooppunten. | * | Alle | 3389 | TCP | Toestaan |

**Uitgaande beveiligingsregels**

| Bron | Bronpoorten | Doel | Doelpoorten | Protocol | Bewerking |
| --- | --- | --- | --- | --- | --- |
| Alle | * | Alle | 443  | Alle | Toestaan |
