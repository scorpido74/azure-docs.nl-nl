---
title: Publicatie handleiding voor aanbiedingen van virtuele machines op Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een virtuele machine en een gratis proef versie van een software die wordt geïmplementeerd vanuit Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: dac67c6dae801d11866f7c4978a050a0cb2fa1dd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065654"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>Publicatie handleiding voor aanbiedingen van virtuele machines

Het publiceren van installatie kopieën van virtuele machines (VM) is een van de belangrijkste manieren om een oplossing naar Azure Marketplace te publiceren. Gebruik deze hand leiding om inzicht te krijgen in de vereisten voor dit type aanbieding. 

Aanbiedingen voor virtuele machines zijn transactie aanbiedingen die via Azure Marketplace worden geïmplementeerd en gefactureerd. Het aanroepen van een actie die een gebruiker ziet, is *nu downloaden*.

## <a name="free-trial"></a>Gratis proefversie 

Als u gebruikers de mogelijkheid wilt bieden om uw aanbieding te testen, moet u toegang krijgen tot software licenties met beperkte termen wanneer u het BYOL-facturerings model (meebrengen van eigen licentie) gebruikt. 

## <a name="test-drive"></a>Station testen

U kunt een of meer virtuele machines implementeren met behulp van IaaS (Infrastructure as a Service) of SaaS-apps (Software as a Service). Een voor deel van de *test drive* publicatie optie is de geautomatiseerde installatie van een virtuele machine of een volledige oplossing, onder geleid door een door een partner gehoste rond leiding. Met een test drive kunnen uw klanten Vm's evalueren zonder extra kosten. Een klant hoeft geen bestaande Azure-klant te zijn om te kunnen samen werken met de proef ervaring. 

Als u aan de slag wilt gaan, kunt u contact met ons opnemen via e-mail op [amp-Testdrive](mailto:amp-testdrive@microsoft.com). 

|Vereisten  |Details |
|---------|---------|
| U hebt een Azure Marketplace-app   |  Een of meer virtuele machines via IaaS of SaaS.      |

## <a name="interactive-demo"></a>Interactieve demo

Met deze aanbieding geeft u uw klanten een begeleide ervaring van uw oplossing met behulp van een interactieve demonstratie. Het voor deel van een interactieve demo-publicatie optie is dat u een proef ervaring kunt bieden zonder dat u een ingewikkelde configuratie van uw complexe oplossing hoeft te bieden. 

## <a name="virtual-machine-offer"></a>Virtuele-machineaanbieding

Gebruik het type *virtuele machine* -aanbieding wanneer u een virtueel apparaat implementeert op het abonnement dat is gekoppeld aan uw klant. Vm's zijn volledig geschikt voor Commerce, met behulp van betalen per gebruik of uw eigen licentie modellen (BYOL). Micro soft host de commerce trans actie en factureert uw klant namens u. U krijgt het voor deel van het gebruik van de voorkeurs betalings relatie tussen uw klant en micro soft, inclusief alle Enter prise-overeenkomsten.

> [!NOTE]
> Op dit moment kunnen de monetaire toezeg gingen die aan een Enterprise Agreement zijn gekoppeld, worden gebruikt voor het Azure-gebruik van uw virtuele machine, maar niet op basis van uw software licentie kosten.  
> 
> [!NOTE]
> U kunt de detectie en implementatie van uw virtuele machine beperken tot een specifieke set klanten door de installatie kopie en prijzen als een persoonlijke aanbieding te publiceren. Persoonlijke aanbiedingen bieden u de mogelijkheid om exclusieve aanbiedingen voor uw dichtstbijzijnde klanten te maken en om aangepaste software en voor waarden te leveren. Met de aangepaste voor waarden kunt u een groot aantal scenario's markeren, met inbegrip van veld LED-deals met gespecialiseerde prijzen en voor waarden, en vroegtijdige toegang tot beperkte release software. Met persoonlijke aanbiedingen kunt u specifieke prijzen of producten aan een beperkt aantal klanten geven door een nieuw abonnement te maken met deze gegevens.  
>
> Zie voor meer informatie [persoonlijke aanbiedingen op Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Vereiste | Details |  
|:--- |:--- | 
| Facturering en meting | Uw virtuele machine moet de facturering per maand BYOL of betalen naar gebruik ondersteunen. |  
| Azure-compatibele virtuele harde schijf (VHD) | Vm's moeten zijn gebouwd op Windows of Linux. Zie voor meer informatie over het maken van een VHD: <ul> <li>[Linux-distributies die zijn goedgekeurd op Azure](../virtual-machines/linux/endorsed-distros.md) (voor Linux-vhd's).</li> <li>[Een met Azure compatibele VHD maken](./partner-center-portal/azure-vm-create-offer.md) (voor Windows-vhd's).</li> </ul> |  

>[!Note]
>De opt-in voor de Cloud Solution Provider (CSP)-partner kanaal is nu beschikbaar. Zie [Cloud Solution Providers](./cloud-solution-providers.md)(Engelstalig) voor meer informatie over het marketing uw aanbod via micro soft CSP-partner kanalen.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, leert u hoe u [uw Cloud activiteiten kunt verg Roten met Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registreren voor en werken in het partner centrum:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- Zie [een aanbieding voor een virtuele machine maken](./partner-center-portal/azure-vm-create-offer.md) voor meer informatie.
