---
title: Azure Security Center en Azure Container Registry
description: Meer informatie over de integratie van Azure Security Center met Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970464"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integratie met Security Center Azure Container Registry

Azure Container Registry (ACR) is een beheerde, persoonlijke docker-register service waarmee uw container installatie kopieën voor Azure-implementaties in een centraal REGI ster worden opgeslagen en beheerd. Het is gebaseerd op de open-source docker Registry 2,0.

Als u zich in de Standard-laag van Azure Security Center bevindt, kunt u de bundel met container registers toevoegen. Deze optionele functie biedt meer inzicht in de beveiligings problemen van de installatie kopieën in uw op ARM gebaseerde registers. Hiermee schakelt u de bundel op het abonnements niveau in of uit om alle registers in een abonnement te behandelen. Deze functie wordt per afbeelding in rekening gebracht, zoals wordt weer gegeven op de [pagina met prijzen](security-center-pricing.md). Als u de container register bundel inschakelt, zorgt u ervoor dat Security Center gereed is voor het scannen van installatie kopieën die naar het REGI ster worden gepusht. 


## <a name="availability"></a>Beschikbaarheid

- Release status: **algemene Beschik baarheid**
- Vereiste rollen: **beveiligings lezer** en [rol van Azure container Registry lezer](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Clouds 
    - ✔ Commerciële Clouds
    - ✘-Cloud voor de Amerikaanse overheid
    - ✘ China Government Cloud, overige gov-Clouds


## <a name="when-are-images-scanned"></a>Wanneer worden afbeeldingen gescand?

Wanneer een installatie kopie naar het REGI ster wordt gepusht, wordt de installatie kopie automatisch gescand door Security Center. U kunt de scan van een afbeelding activeren door deze naar uw opslag plaats te pushen.

Wanneer de scan is voltooid (doorgaans na ongeveer 10 minuten, maar Maxi maal 40 minuten kan zijn), zijn er conclusies beschikbaar als Security Center aanbevelingen als volgt:

[![Voor beeld Azure Security Center aanbeveling over zwakke plekken die zijn gedetecteerd in een gehoste installatie kopie van Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Voor delen van integratie

Security Center identificeert op ARM gebaseerde ACR-registers in uw abonnement en biedt probleemloos het volgende:

* **Azure-systeem eigen beveiligings problemen scannen** voor alle pushed Linux-installatie kopieën. Security Center scant de installatie kopie met behulp van een scanner van de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys. Deze systeem eigen oplossing is standaard naadloos geïntegreerd.

* **Beveiligings aanbevelingen** voor Linux-installatie kopieën met bekende beveiligings problemen. Security Center bevat details van elk gemeld beveiligings probleem en een Ernst classificatie. Daarnaast biedt het hulp bij het herstellen van de specifieke beveiligings problemen die zijn gevonden op elke installatie kopie die naar het REGI ster wordt gepusht.

![Overzicht op hoog niveau Azure Security Center en Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>ACR met veelgestelde vragen over Security Center

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Welke typen installatie kopieën kunnen Azure Security Center scannen?
Security Center scant installatie kopieën op basis van Linux-besturings systemen die shell toegang bieden. 

De Qualys-scanner ondersteunt geen super minimale afbeeldingen zoals [docker](https://hub.docker.com/_/scratch/) -werk afbeeldingen, of Distroless-installatie kopieën die alleen uw toepassing en de runtime-afhankelijkheden bevatten zonder pakket beheer, shell of besturings systeem.

### <a name="how-does-azure-security-center-scan-an-image"></a>Hoe scant Azure Security Center een afbeelding?
De installatie kopie wordt opgehaald uit het REGI ster. Het wordt vervolgens uitgevoerd in een geïsoleerde sandbox met de Qualys-scanner die een lijst met bekende beveiligings problemen ophaalt.

Security Center filters en classificeert de resultaten van de scanner. Wanneer een afbeelding in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen beveiligings aanbevelingen voor installatie kopieën waarvoor problemen moeten worden opgelost. Als er alleen een melding wordt weer geven als er problemen zijn, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Hoe vaak Azure Security Center mijn afbeeldingen scannen?
Scans van afbeeldingen worden geactiveerd tijdens elke push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan ik de scan resultaten verkrijgen via REST API?
Ja. De resultaten bevinden zich onder [Subevaluaties van de rest-API](/rest/api/securitycenter/subassessments/list/). U kunt ook Azure resource Graph (ARG), de Kusto-achtige API voor al uw resources, gebruiken: een query kan een specifieke scan ophalen.
 



## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Beveiliging van virtuele machines](security-center-virtual-machine-protection.md) -Hiermee worden de aanbevelingen van Security Center beschreven
