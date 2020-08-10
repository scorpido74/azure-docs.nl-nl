---
title: Azure Security Center en Azure Container Registry
description: Meer informatie over het scannen van uw container registers met Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: 767ad928779743ec28dbe3da8be6154a1fe0b0ab
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042307"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Azure Container Registry afbeeldingen scannen door Security Center

Azure Container Registry (ACR) is een beheerde, persoonlijke docker-register service waarmee uw container installatie kopieën voor Azure-implementaties in een centraal REGI ster worden opgeslagen en beheerd. Het is gebaseerd op de open-source docker Registry 2,0.

Als u zich in de Standard-laag van Azure Security Center bevindt, kunt u de bundel met container registers toevoegen. Deze optionele functie biedt meer inzicht in de beveiligings problemen van de installatie kopieën in uw op Azure Resource Manager gebaseerde registers. Hiermee schakelt u de bundel op het abonnements niveau in of uit om alle registers in een abonnement te behandelen. Deze functie wordt per afbeelding in rekening gebracht, zoals wordt weer gegeven op de [pagina met prijzen](security-center-pricing.md). Als u de container register bundel inschakelt, zorgt u ervoor dat Security Center gereed is voor het scannen van installatie kopieën die naar het REGI ster worden gepusht. 

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemene beschikbaarheid|
|Koers|Standard-laag|
|Ondersteunde registers en installatie kopieën:|![Ja ](./media/icons/yes-icon.png) door Linux gehoste ACR-registers die toegankelijk zijn via het open bare Internet en shell toegang bieden.<br>![Geen door ](./media/icons/yes-icon.png) Windows gehoste ACR-registers.<br>![Geen ](./media/icons/yes-icon.png) persoonlijke registers-Security Center vereist dat uw registers toegankelijk zijn via het open bare Internet. Security Center kan momenteel geen verbinding maken met, of scannen, registers met toegang beperkt met een firewall, een service-eind punt of persoonlijke eind punten zoals een persoonlijke Azure-koppeling.<br>![Geen ](./media/icons/yes-icon.png) minimale afbeeldings afbeeldingen zoals [docker](https://hub.docker.com/_/scratch/) -werk afbeeldingen, of "Distroless"-installatie kopieën die alleen een toepassing en de runtime-afhankelijkheden bevatten zonder pakket beheer, shell of besturings systeem.|
|Vereiste rollen en machtigingen:|Rol van **beveiligings lezer** en [Azure container Registry lezer](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Clouds|![Ja](./media/icons/yes-icon.png) Commerciële Clouds<br>![Nee](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||




## <a name="when-are-images-scanned"></a>Wanneer worden afbeeldingen gescand?

Wanneer een installatie kopie naar het REGI ster wordt gepusht, wordt de installatie kopie automatisch gescand door Security Center. U kunt de scan van een afbeelding activeren door deze naar uw opslag plaats te pushen.

Wanneer de scan is voltooid (doorgaans na ongeveer 2 minuten, maar Maxi maal 15 minuten kan zijn), zijn er conclusies beschikbaar als Security Center aanbevelingen als volgt:

[![Voor beeld Azure Security Center aanbeveling over zwakke plekken die zijn gedetecteerd in een gehoste installatie kopie van Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Voor delen van integratie

Security Center identificeert Azure Resource Manager gebaseerde ACR-registers in uw abonnement en biedt probleemloos het volgende:

* **Azure-systeem eigen beveiligings problemen scannen** voor alle pushed Linux-installatie kopieën. Security Center scant de installatie kopie met behulp van een scanner van de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys. Deze systeem eigen oplossing is standaard naadloos geïntegreerd.

* **Beveiligings aanbevelingen** voor Linux-installatie kopieën met bekende beveiligings problemen. Security Center bevat details van elk gemeld beveiligings probleem en een Ernst classificatie. Daarnaast biedt het hulp bij het herstellen van de specifieke beveiligings problemen die zijn gevonden op elke installatie kopie die naar het REGI ster wordt gepusht.

![Overzicht op hoog niveau Azure Security Center en Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Veelgestelde vragen over het scannen van Azure Container Registry afbeeldingen

### <a name="how-does-security-center-scan-an-image"></a>Hoe scant Security Center een afbeelding?
De installatie kopie wordt opgehaald uit het REGI ster. Het wordt vervolgens uitgevoerd in een geïsoleerde sandbox met de Qualys-scanner die een lijst met bekende beveiligings problemen ophaalt.

Security Center filters en classificeert de resultaten van de scanner. Wanneer een afbeelding in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen beveiligings aanbevelingen voor installatie kopieën waarvoor problemen moeten worden opgelost. Als er alleen een melding wordt weer geven als er problemen zijn, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.

### <a name="how-often-does-security-center-scan-my-images"></a>Hoe vaak Security Center mijn afbeeldingen scannen?
Scans van afbeeldingen worden geactiveerd tijdens elke push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan ik de scan resultaten verkrijgen via REST API?
Ja. De resultaten bevinden zich onder [Subevaluaties van de rest-API](/rest/api/securitycenter/subassessments/list/). U kunt ook Azure resource Graph (ARG), de Kusto-achtige API voor al uw resources, gebruiken: een query kan een specifieke scan ophalen.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Welke register typen worden er gescand? Welke typen worden er gefactureerd?
In het [gedeelte Beschik baarheid](#availability) worden de typen container registers vermeld die worden ondersteund door de container registers bundel. 

Als registers die niet worden ondersteund, zijn verbonden met uw Azure-abonnement, worden ze niet gescand en wordt u niet gefactureerd.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Beveiliging van virtuele machines](security-center-virtual-machine-protection.md) -Hiermee worden de aanbevelingen van Security Center beschreven
