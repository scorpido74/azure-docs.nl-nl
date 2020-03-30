---
title: Problemen oplossen azure VM-toewijzingsfouten | Microsoft Documenten
description: Toewijzingsfouten oplossen wanneer u een vm in Azure maakt, opnieuw start of het formaat ervan wijzigt
services: virtual-machines
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: cjiang
ms.openlocfilehash: b4750ad9fdfa214aa4d7b6a0355c319e7eb1d9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484397"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Toewijzingsproblemen bij het maken, opnieuw starten of het wijzigen van het formaat van VM’s in Azure oplossen

Wanneer u een virtuele machine (VM) maakt, gestopte VM's (deallocated) opnieuw start of het formaat van een VM wijzigt, wijst Microsoft Azure rekenbronnen toe aan uw abonnement. We investeren voortdurend in extra infrastructuur en functies om ervoor te zorgen dat we altijd alle VM-typen beschikbaar hebben om de vraag van klanten te ondersteunen. U echter af en toe problemen ondervinden bij het toewijzen van resources vanwege een ongekende groei van de vraag naar Azure-services in specifieke regio's. Dit probleem kan optreden wanneer u VM's in een regio probeert te maken of starten terwijl de VM's de volgende foutcode en -bericht weergeven:

**Foutcode**: Toewijzing mislukt of ZonalAlAlalalalalismislukt

**Foutbericht:**"Toewijzing is mislukt. We hebben onvoldoende capaciteit voor de gevraagde VM-grootte in deze regio. Lees meer over het verbeteren van\/de kans op toewijzingsucces op https: /aka.ms/allocation-guidance"

In dit artikel worden de oorzaken van enkele van de gemeenschappelijke toewijzingsfouten uitgelegd en worden mogelijke oplossingen voorgesteld.

Als uw Azure-probleem niet wordt verholpen in dit artikel, gaat u naar de [Azure-forums op MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U uw probleem op @AzureSupport deze forums of op Twitter. U ook een Azure-ondersteuningsaanvraag indienen door Ondersteuning op de [Azure-ondersteuningssite te](https://azure.microsoft.com/support/options/) selecteren.

Totdat uw gewenste VM-type beschikbaar is in uw voorkeursregio, raden we klanten die problemen ondervinden met implementatieproblemen aan om de richtlijnen in de volgende tabel te beschouwen als een tijdelijke tijdelijke oplossing. 

Identificeer het scenario dat het beste bij uw aanvraag past en probeer de toewijzingsaanvraag opnieuw met behulp van de bijbehorende voorgestelde tijdelijke oplossing om de kans op succes van toewijzing te vergroten. U het ook altijd later opnieuw proberen. Dit komt omdat er mogelijk voldoende resources zijn vrijgemaakt in het cluster, de regio of de zone om aan uw aanvraag te voldoen. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>De grootte van een virtuele machine wijzigen of VM's toevoegen aan een bestaande beschikbaarheidsset

### <a name="cause"></a>Oorzaak

Een aanvraag om het formaat van een vm te wijzigen of een VM toe te voegen aan een bestaande beschikbaarheidsset, moet worden geprobeerd op het oorspronkelijke cluster met de bestaande beschikbaarheidsset. De gevraagde VM-grootte wordt ondersteund door het cluster, maar het cluster heeft momenteel mogelijk niet voldoende capaciteit. 

### <a name="workaround"></a>Tijdelijke oplossing

Als de vm deel kan uitmaken van een andere beschikbaarheidsset, maakt u een VM in een andere beschikbaarheidsset (in dezelfde regio). Deze nieuwe VM kan vervolgens worden toegevoegd aan hetzelfde virtuele netwerk.

Stop (deallocate) alle VM's in dezelfde beschikbaarheidset en start vervolgens elk apparaat opnieuw.
Stoppen: klik op Resourcegroepen > [uw resourcegroep] > Resources > [uw beschikbaarheidsset] > Virtuele Machines > [uw virtuele machine] > Stop.
Nadat alle VM's zijn gestopt, selecteert u de eerste vm en klikt u op Start.
Deze stap zorgt ervoor dat een nieuwe toewijzingspoging wordt uitgevoerd en dat een nieuw cluster kan worden geselecteerd met voldoende capaciteit.

## <a name="restart-partially-stopped-deallocated-vms"></a>Gedeeltelijk gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten

### <a name="cause"></a>Oorzaak

Gedeeltelijke toewijzing betekent dat u een of meer, maar niet alle VM's in een beschikbaarheidsset hebt gestopt (deallocated). Wanneer u een vm uitwijst, worden de bijbehorende resources vrijgegeven. Het opnieuw starten van VM's in een gedeeltelijk toegewezen beschikbaarheidsset is hetzelfde als het toevoegen van VM's aan een bestaande beschikbaarheidsset. Daarom moet de toewijzingsaanvraag worden geprobeerd op het oorspronkelijke cluster met de bestaande beschikbaarheidsset die mogelijk niet over voldoende capaciteit beschikt.

### <a name="workaround"></a>Tijdelijke oplossing

Stop (deallocate) alle VM's in dezelfde beschikbaarheidset en start vervolgens elk apparaat opnieuw.
Stoppen: klik op Resourcegroepen > [uw resourcegroep] > Resources > [uw beschikbaarheidsset] > Virtuele Machines > [uw virtuele machine] > Stop.
Nadat alle VM's zijn gestopt, selecteert u de eerste vm en klikt u op Start.
Dit zorgt ervoor dat een nieuwe toewijzingspoging wordt uitgevoerd en dat een nieuw cluster kan worden geselecteerd met voldoende capaciteit.

## <a name="restart-fully-stopped-deallocated-vms"></a>Volledig gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten

### <a name="cause"></a>Oorzaak

Volledige toewijzing betekent dat u alle VM's in een beschikbaarheidsset hebt gestopt (deallocated). De toewijzingsaanvraag voor het opnieuw opstarten van deze VM's is gericht op alle clusters die de gewenste grootte binnen de regio of zone ondersteunen. Wijzig uw toewijzingsaanvraag per de suggesties in dit artikel en probeer het verzoek opnieuw om de kans op toewijzingssucces te vergroten. 

### <a name="workaround"></a>Tijdelijke oplossing

Als u oudere VM-series of -formaten gebruikt, zoals Dv1, DSv1, Av1, D15v2 of DS15v2, u overwegen over te stappen op nieuwere versies. Zie deze aanbevelingen voor specifieke VM-formaten.
Als u niet de optie hebt om een andere VM-grootte te gebruiken, probeert u implementeren naar een andere regio binnen dezelfde geo. Voor meer informatie over de beschikbare VM-formaten in elke regio ophttps://aka.ms/azure-regions

Als u beschikbaarheidszones gebruikt, probeert u een andere zone binnen de regio die mogelijk beschikbaar is voor de gevraagde VM-grootte.

Als uw toewijzingsaanvraag groot is (meer dan 500 cores), raadpleegt u de richtlijnen in de volgende secties om de aanvraag op te splitsen in kleinere implementaties.

Probeer [de VM opnieuw te implementeren.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows) Als u de VM opnieuw implementeert, wordt de VM toegewezen aan een nieuw cluster binnen de regio.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Toewijzingsfouten voor oudere VM-formaten (Av1, Dv1, DSv1, D15v2, DS15v2 enz.)

Terwijl we de Azure-infrastructuur uitbreiden, implementeren we hardware van nieuwere generatie die is ontworpen om de nieuwste virtuele machinetypen te ondersteunen. Sommige van de oudere serie VM's draaien niet op onze nieuwste generatie infrastructuur. Om deze reden kunnen klanten af en toe toewijzingsfouten ervaren voor deze verouderde SKU's. Om dit probleem te voorkomen, moedigen we klanten die virtuele machines uit oudere series gebruiken aan om te overwegen over te stappen op de gelijkwaardige nieuwere VM's per de volgende aanbevelingen: deze VM's zijn geoptimaliseerd voor de nieuwste hardware en laten u profiteren van betere prijzen en prestaties. 

|Verouderde VM-serie/-grootte|Aanbevolen nieuwere VM-serie/-grootte|Meer informatie|
|----------------------|----------------------------|--------------------|
|Av1-serie|[Av2-serie](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1- of DSv1-serie (D1 t/m D5)|[Dv3- of DSv3-serie](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1- of DSv1-serie (D11 t/m D14)|[Ev3- of ESv3-serie](../ev3-esv3-series.md)|
|D15v2 of DS15v2|Als u het implementatiemodel van Resource Manager gebruikt om te profiteren van de grotere VM-formaten, u overwegen over te stappen naar D16v3/DS16v3 of D32v3/DS32v3. Deze zijn ontworpen om te draaien op de nieuwste generatie hardware. Als u het implementatiemodel resourcebeheer gebruikt om ervoor te zorgen dat uw VM-exemplaar is geïsoleerd voor hardware die is toegewezen aan één klant, u overwegen over te stappen op de nieuwe geïsoleerde VM-formaten, E64i_v3 of E64is_v3, die zijn ontworpen om te worden uitgevoerd op de nieuwste hardware. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Toewijzingsfouten voor grote implementaties (meer dan 500 kernen)

Verminder het aantal exemplaren van de gevraagde VM-grootte en probeer de implementatiebewerking opnieuw. Bovendien u voor grotere implementaties [azure-schaalsets voor virtuele machines](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)evalueren. Het aantal VM-exemplaren kan automatisch toenemen of afnemen in reactie op de vraag of een gedefinieerd schema, en u hebt een grotere kans op toewijzingsucces omdat de implementaties over meerdere clusters kunnen worden verspreid. 

## <a name="background-information"></a>Achtergrondinformatie
### <a name="how-allocation-works"></a>Hoe toewijzing werkt
De servers in Azure-datacenters worden in clusters gepartitioneerd. Normaal gesproken wordt er een toewijzingsaanvraag in meerdere clusters uitgevoerd, maar het is mogelijk dat bepaalde beperkingen van de toewijzingsaanvraag het Azure-platform ertoe dwingen de aanvraag in slechts één cluster uit te voeren. In dit artikel verwijzen we naar dit als 'vastgemaakt aan een cluster'. Diagram 1 hieronder illustreert het geval van een normale toewijzing die wordt geprobeerd in meerdere clusters. Diagram 2 illustreert het geval van een toewijzing die is vastgemaakt aan cluster 2, omdat daar de bestaande cloudservice-CS_1 of beschikbaarheidsset wordt gehost.
![Toewijzingsdiagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Waarom toewijzingsfouten optreden
Wanneer een toewijzingsaanvraag is vastgemaakt aan een cluster, is de kans groter dat er geen vrije resources worden gevonden omdat de beschikbare resourcegroep kleiner is. Als uw toewijzingsaanvraag is vastgemaakt aan een cluster, maar het type resource dat u hebt aangevraagd, wordt deze niet ondersteund door dat cluster, mislukt uw aanvraag zelfs als het cluster gratis resources heeft. In het volgende diagram 3 wordt het geval geïllustreerd waarin een vastgemaakte toewijzing mislukt omdat het enige kandidaatcluster geen vrije resources heeft. Diagram 4 illustreert het geval waarin een vastgemaakte toewijzing mislukt omdat het enige kandidaatcluster de gevraagde VM-grootte niet ondersteunt, ook al heeft het cluster gratis resources.

![Vastgemaakte toewijzingsfout](./media/virtual-machines-common-allocation-failure/Allocation2.png)


