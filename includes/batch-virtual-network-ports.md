---
title: bestand opnemen
description: bestand opnemen
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086280"
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
>Voor elke 50 dedicated nodes (of elke 20 knooppunten met lage prioriteit) wijst Batch toe: één netwerkbeveiligingsgroep (NSG), één openbaar IP-adres en één load balancer. De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Voor grote groepen moet u mogelijk een quotumverhoging aanvragen voor een of meer van deze resources.

#### <a name="network-security-groups-batch-default"></a>Netwerkbeveiligingsgroepen: batchstandaard

Het subnet moet inkomende communicatie vanuit de Batch-service toestaan om taken op de compute-knooppunten te plannen en uitgaande communicatie om te communiceren met Azure Storage of andere resources als dat nodig is door uw werkbelasting. Voor pools in de configuratie van virtuele machines voegt Batch NSG's toe op het netwerkinterfaces (NIC's) niveau dat is gekoppeld aan compute nodes. Deze NSG's zijn geconfigureerd met de volgende aanvullende regels:

* Binnenkomend TCP-verkeer op poorten 29876 en 29877 van `BatchNodeManagement` IP-adressen van batchservice die overeenkomen met de servicetag.
* Binnenkomend TCP-verkeer op poort 22 (Linux-knooppunten) of poort 3389 (Windows-knooppunten) om externe toegang te verlenen. Voor bepaalde typen multi-instance taken op Linux (zoals MPI), moet u ook SSH-poort 22-verkeer voor IP's toestaan in het subnet met de Batch compute nodes. Dit kan worden geblokkeerd per NSG-regels op subnetniveau (zie hieronder).
* Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk. Dit kan worden gewijzigd per NSG-regels op subnetniveau (zie hieronder).
* Uitgaand verkeer op elke poort naar het internet. Dit kan worden gewijzigd per NSG-regels op subnetniveau (zie hieronder).

> [!IMPORTANT]
> Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als communicatie met de rekenknooppunten in het opgegeven subnet wordt geweigerd door een netwerkbeveiligingsgroep, zet de Batch-service de status van de rekenknooppunten op **Onbruikbaar**. Bovendien mogen er geen resourcevergrendelingen worden toegepast op resources die door Batch zijn gemaakt, anders kan dit leiden tot het voorkomen van opschoning van resources als gevolg van door de gebruiker geïnitieerde acties zoals het verwijderen van een groep.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Netwerkbeveiligingsgroepen: regels op subnetniveau opgeven

Het is niet vereist om NSGs op het subnetniveau van het virtuele netwerk op te geven omdat Batch zijn eigen NSG's configureert (zie hierboven). Als u een NSG hebt die is gekoppeld aan het subnet waarbij batchcomputenodes worden geïmplementeerd of aangepaste NSG-regels wilt toepassen om de toegepaste standaardwaarden te overschrijven, moet u deze NSG configureren met ten minste de inkomende en uitgaande beveiligingsregels zoals weergegeven in de volgende Tabellen.

Configureer binnenkomend verkeer op poort 3389 (Windows) of 22 (Linux) alleen als u externe toegang tot de compute nodes van externe bronnen moet toestaan. Mogelijk moet u poort 22-regels voor Linux inschakelen als u ondersteuning nodig hebt voor taken met meerdere taken met bepaalde MPI-runtimes. Het toestaan van verkeer op deze poorten is niet strikt vereist om de rekenknooppunten van de groep bruikbaar te maken.

**Inkomende beveiligingsregels**

| IP-adressen van bron | Bronservicetag | Bronpoorten | Doel | Doelpoorten | Protocol | Actie |
| --- | --- | --- | --- | --- | --- | --- |
| N.v.t. | `BatchNodeManagement`[Servicetag](../articles/virtual-network/security-overview.md#service-tags) (als u een regionale variant gebruikt in dezelfde regio als uw Batch-account) | * | Alle | 29876-29877 | TCP | Toestaan |
| Ip-functie van gebruikersbron voor het op afstand openen van compute nodes en/of compute node subnet voor Linux multi-instance taken, indien nodig. | N.v.t. | * | Alle | 3389 (Windows), 22 (Linux) | TCP | Toestaan |

**Uitgaande beveiligingsregels**

| Bron | Bronpoorten | Doel | Doelservicetag | Doelpoorten | Protocol | Actie |
| --- | --- | --- | --- | --- | --- | --- |
| Alle | * | [Servicetag](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(als u een regionale variant gebruikt, in dezelfde regio als uw Batch-account) | 443 | TCP | Toestaan |

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

Configureer binnenkomend verkeer op poort 3389 voor Windows als u RDP-toegang tot de poolknooppunten moet toestaan. Het is niet vereist dat de poolknooppunten kunnen worden gebruikt.

**Inkomende beveiligingsregels**

| IP-adressen van bron | Bronpoorten | Doel | Doelpoorten | Protocol | Actie |
| --- | --- | --- | --- | --- | --- |
Alle <br /><br />Hoewel dit in feite 'alles toestaan' vereist, past de Batch-service een regel voor toegangsbeheerlijsten toe op het niveau van elk knooppunt, die ervoor zorgt dat alle IP-adressen van niet-Batch-services worden uitgefilterd. | * | Alle | 10100, 20100, 30100 | TCP | Toestaan |
| Optioneel om RDP-toegang tot compute nodes toe te staan. | * | Alle | 3389 | TCP | Toestaan |

**Uitgaande beveiligingsregels**

| Bron | Bronpoorten | Doel | Doelpoorten | Protocol | Actie |
| --- | --- | --- | --- | --- | --- |
| Alle | * | Alle | 443  | Alle | Toestaan |
