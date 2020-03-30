---
title: DevTest Labs concepten | Microsoft Documenten
description: Leer de basisconcepten van DevTest Labs en hoe het eenvoudig kan zijn om virtuele Azure-machines te maken, te beheren en te controleren
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22fd78ccd58be1790fcd167da396600e8b876564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428921"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-concepten
## <a name="overview"></a>Overzicht
De volgende lijst bevat de belangrijkste DevTest Labs concepten en definities:

## <a name="labs"></a>Labs
Een lab is de infrastructuur die een groep resources omvat, zoals Virtuele machines (VM's), waarmee u deze resources beter beheren door limieten en quota op te geven.

## <a name="virtual-machine"></a>Virtuele machine
Een Azure VM is een van de verschillende soorten [on-demand, schaalbare computerbronnen](/azure/architecture/guide/technology-choices/compute-decision-tree) die Azure biedt. Azure VM's bieden u de flexibiliteit van virtualisatie zonder de fysieke hardware die deze uitvoert te hoeven kopen en onderhouden, hoewel u de VM nog steeds moet onderhouden door bepaalde taken uit te voeren, zoals het configureren, patchen en installeren van de software waarop wordt uitgevoerd Het.

[Overzicht van virtuele Windows-machines in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) geeft u informatie over wat u moet overwegen voordat u een VM maakt, hoe u deze maakt en hoe u deze beheert.

## <a name="claimable-vm"></a>Claimable VM
Een Azure Claimable VM is een virtuele machine die beschikbaar is voor gebruik door elke labgebruiker met machtigingen. Een labbeheerder kan VM's voorbereiden met specifieke basisafbeeldingen en artefacten en deze opslaan in een gedeelde groep. Een labgebruiker kan vervolgens een werkende VM uit de groep claimen wanneer deze met die specifieke configuratie nodig heeft.

Een VM die claimbaar is, wordt in eerste instantie niet toegewezen aan een bepaalde gebruiker, maar wordt weergegeven in de lijst van elke gebruiker onder "Claimable virtual machines". Nadat een VM door een gebruiker is geclaimd, wordt deze verplaatst naar het gebied 'Mijn virtuele machines' en is deze niet langer te claimen door een andere gebruiker.

## <a name="environment"></a>Omgeving
In DevTest Labs verwijst een omgeving naar een verzameling Azure-bronnen in een lab. [In dit blogbericht](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) wordt beschreven hoe u multi-VM-omgevingen maakt op basis van uw Azure Resource Manager-sjablonen.

## <a name="base-images"></a>Basisinstallatiekopieën
Basisafbeeldingen zijn VM-afbeeldingen met alle tools en instellingen die vooraf zijn geïnstalleerd en geconfigureerd om snel een VM te maken. U een VM inrichten door een bestaande basis te kiezen en een artefact toe te voegen om uw testagent te installeren. U de ingerichte VM vervolgens opslaan als basis, zodat de basis kan worden gebruikt zonder dat de testagent voor elke inrichting van de VM opnieuw hoeft te worden geïnstalleerd.

## <a name="artifacts"></a>Artefacten
Artefacten worden gebruikt om uw toepassing te implementeren en te configureren nadat een VM is ingericht. Artefacten kunnen zijn:

* Hulpprogramma's die u op de VM wilt installeren, zoals agents, Fiddler en Visual Studio.
* Acties die u op de VM wilt uitvoeren, zoals het klonen van een repo.
* Toepassingen die u wilt testen.

Artefacten zijn Json-bestanden van [Azure Resource Manager](../azure-resource-manager/management/overview.md) die instructies bevatten om implementatie uit te voeren en configuratie toe te passen.

## <a name="artifact-repositories"></a>Artefact-opslagplaatsen
Artefact-repositories zijn git-repositories waar artefacten worden ingecheckt. Artefact-opslagplaatsen kunnen worden toegevoegd aan meerdere laboratoria in uw organisatie, waardoor hergebruik en delen mogelijk zijn.

## <a name="formulas"></a>Formules
Formules bieden, naast basisafbeeldingen, een mechanisme voor snelle VM-provisioning. Een formule in DevTest Labs is een lijst met standaardeigenschapwaarden die worden gebruikt om een lab-vm te maken.
Met formules kunnen VM's met dezelfde set eigenschappen - zoals basisafbeelding, VM-grootte, virtueel netwerk en artefacten - worden gemaakt zonder dat deze eigenschappen telkens hoeven op te geven. Bij het maken van een VM uit een formule kunnen de standaardwaarden worden gebruikt als is of gewijzigd.

## <a name="policies"></a>Beleidsregels
Beleid helpt bij het beheersen van de kosten in uw lab. U bijvoorbeeld een beleid maken om VM's automatisch af te sluiten op basis van een gedefinieerd schema.

## <a name="caps"></a>Petjes
Caps is een mechanisme om afval in uw lab te minimaliseren. U bijvoorbeeld een limiet instellen om het aantal VM's dat per gebruiker of in een lab kan worden gemaakt, te beperken.

## <a name="security-levels"></a>Beveiligingsniveaus
Beveiligingstoegang wordt bepaald door RBAC (Azure Role-Based Access Control). Om te begrijpen hoe toegang werkt, helpt het om de verschillen tussen een machtiging, een rol en een bereik zoals gedefinieerd door RBAC te begrijpen.

* Machtiging - Een machtiging is een gedefinieerde toegang tot een specifieke actie (bijvoorbeeld leestoegang tot alle virtuele machines).
* Rol - Een rol is een set machtigingen die kunnen worden gegroepeerd en toegewezen aan een gebruiker. De rol *van* de eigenaar van het abonnement heeft bijvoorbeeld toegang tot alle bronnen binnen een abonnement.
* Bereik : een bereik is een niveau binnen de hiërarchie van een Azure-bron, zoals een resourcegroep, één lab of het hele abonnement.

Binnen het bereik van DevTest Labs zijn er twee soorten rollen om gebruikersmachtigingen te definiëren: labeigenaar en labgebruiker.

* Lab Owner - Een lab eigenaar heeft toegang tot alle middelen in het lab. Daarom kan een labeigenaar beleid wijzigen, vm's lezen en schrijven, het virtuele netwerk wijzigen, enzovoort.
* Lab-gebruiker - Een labgebruiker kan alle labbronnen bekijken, zoals VM's, beleidsregels en virtuele netwerken, maar kan geen beleid of VM's wijzigen die door andere gebruikers zijn gemaakt.

Als u wilt zien hoe u aangepaste rollen in DevTest Labs maakt, raadpleegt u het [artikel, Verleent u gebruikersmachtigingen voor specifiek labbeleid](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Aangezien scopes hiërarchisch zijn, krijgen deze machtigingen automatisch op elk bereik dat op een lager niveau is, wanneer een gebruiker machtigingen heeft op een bepaald bereik. Als een gebruiker bijvoorbeeld is toegewezen aan de rol van abonnementseigenaar, hebben ze toegang tot alle bronnen in een abonnement, waaronder alle virtuele machines, alle virtuele netwerken en alle labs. Daarom neemt een abonnementeigenaar automatisch de rol van labeigenaar over. Het tegendeel is echter niet waar. Een labeigenaar heeft toegang tot een lab, wat een lager bereik is dan het abonnementsniveau. Daarom kan een labeigenaar geen virtuele machines of virtuele netwerken of bronnen zien die zich buiten het lab bevinden.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen
Alle concepten die in dit artikel worden besproken, kunnen worden geconfigureerd met Azure Resource Manager-sjablonen, waarmee u de infrastructuur/configuratie van uw Azure-oplossing definiëren en herhaaldelijk in een consistente status implementeren.

[Begrijp de structuur en syntaxis van Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) beschrijft de structuur van een Azure Resource Manager-sjabloon en de eigenschappen die beschikbaar zijn in de verschillende secties van een sjabloon.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
[Maak een lab in DevTest Labs](devtest-lab-create-lab.md)
