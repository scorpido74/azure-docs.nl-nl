---
title: 'Azure Defender voor container registers: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor container registers.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6254b78ad19e7034f78f7891d57a3474fee0c602
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301920"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Inleiding tot Azure Defender voor container registers

Azure Container Registry (ACR) is een beheerde, persoonlijke docker-register service waarmee uw container installatie kopieën voor Azure-implementaties in een centraal REGI ster worden opgeslagen en beheerd. Het is gebaseerd op de open-source docker Registry 2,0.

Als u alle op Azure Resource Manager gebaseerde registers in uw abonnement wilt beveiligen, schakelt u **Azure Defender in voor container registers** op abonnements niveau. Security Center worden vervolgens de installatie kopieën gescand die naar het REGI ster zijn gepusht, geïmporteerd in het REGI ster of de afbeeldingen die in de afgelopen 30 dagen zijn getrokken. Deze functie wordt per afbeelding in rekening gebracht.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Wat zijn de voor delen van Azure Defender voor container registers?

Security Center identificeert Azure Resource Manager gebaseerde ACR-registers in uw abonnement en biedt probleemloze evaluatie van het beveiligings risico van Azure en het beheer van de installatie kopieën van uw REGI ster.

**Azure Defender voor container registers** bevat een scanner voor beveiligings problemen voor het scannen van de installatie kopieën in uw op Azure Resource Manager gebaseerde Azure container Registry-registers en biedt diepere zicht baarheid in de beveiligings problemen van uw installatie kopieën. De geïntegreerde scanner wordt aangestuurd door Qualys, het toonaangevende beveiligings probleem bij het scannen van de leverancier.

Als er problemen worden gevonden, wordt er een melding weer gegeven in het Security Center-dash board van Qualys of Security Center. Security Center voorziet in elk beveiligings probleem met actie-aanbevelingen, samen met een Ernst classificatie en richt lijnen voor het oplossen van het probleem. Zie de [naslag lijst met aanbevelingen](recommendations-reference.md#recs-containers)voor meer informatie over de aanbevelingen van Security Center voor containers.

Security Center filters en classificeert de resultaten van de scanner. Wanneer een afbeelding in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen beveiligings aanbevelingen voor installatie kopieën waarvoor problemen moeten worden opgelost. Security Center bevat details van elk gemeld beveiligings probleem en een Ernst classificatie. Daarnaast biedt het hulp bij het herstellen van de specifieke beveiligings problemen die op elke installatie kopie worden gevonden.

Als er alleen een melding wordt weer geven als er problemen zijn, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.


## <a name="when-are-images-scanned"></a>Wanneer worden afbeeldingen gescand?

Er zijn drie triggers voor het scannen van afbeeldingen:

- Wanneer een installatie kopie naar het REGI ster wordt gepusht, wordt de installatie kopie **automatisch door Security Center** gescand. U kunt de scan van een afbeelding activeren door deze naar uw opslag plaats te pushen.

- **Recent getrokken** : aangezien er elke dag nieuwe beveiligings problemen worden ontdekt, scant **Azure Defender voor container registers** ook alle afbeeldingen die in de afgelopen 30 dagen zijn opgehaald. Er zijn geen extra kosten verbonden aan het opnieuw scannen. zoals hierboven vermeld, wordt u één keer per afbeelding gefactureerd.

- **Bij import** -Azure container Registry beschikt u over hulpprogram ma's voor het importeren van installatie kopieën in het REGI ster vanuit docker hub, micro soft container Registry of een ander Azure container Registry. **Azure Defender voor container registers** scant alle ondersteunde installatie kopieën die u importeert. Meer informatie over [het importeren van container installatie kopieën naar een container register](../container-registry/container-registry-import-images.md).
 
De scan wordt doorgaans binnen twee minuten voltooid, maar het kan tot vijf tien minuten duren. Bevindingen worden beschikbaar gesteld als Security Center aanbevelingen zoals deze:

[![Voor beeld Azure Security Center aanbeveling over zwakke plekken die zijn gedetecteerd in een gehoste installatie kopie van Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Hoe werkt Security Center met Azure Container Registry

Hieronder vindt u een diagram op hoog niveau van de onderdelen en voor delen van het beveiligen van uw registers met Security Center.

![Overzicht op hoog niveau Azure Security Center en Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Veelgestelde vragen over het scannen van Azure Container Registry afbeeldingen

### <a name="how-does-security-center-scan-an-image"></a>Hoe scant Security Center een afbeelding?
De installatie kopie wordt opgehaald uit het REGI ster. Het wordt vervolgens uitgevoerd in een geïsoleerde sandbox met de Qualys-scanner die een lijst met bekende beveiligings problemen ophaalt.

Security Center filters en classificeert de resultaten van de scanner. Wanneer een afbeelding in orde is, wordt deze als zodanig gemarkeerd door Security Center. Security Center genereert alleen beveiligings aanbevelingen voor installatie kopieën waarvoor problemen moeten worden opgelost. Als er alleen een melding wordt weer geven als er problemen zijn, vermindert Security Center het potentieel voor ongewenste informatieve waarschuwingen.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan ik de scan resultaten verkrijgen via REST API?
Ja. De resultaten bevinden zich onder [Subevaluaties van de rest-API](/rest/api/securitycenter/subassessments/list/). U kunt ook Azure resource Graph (ARG), de Kusto-achtige API voor al uw resources, gebruiken: een query kan een specifieke scan ophalen.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Welke register typen worden er gescand? Welke typen worden er gefactureerd?
Zie [Beschik baarheid](defender-for-container-registries-usage.md#availability)voor een lijst met de typen container registers die worden ondersteund door Azure Defender voor container registers.

Als u niet-ondersteunde registers verbindt met uw Azure-abonnement, worden ze niet gescand en worden er geen kosten in rekening gebracht.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

- [Azure Security Center en container beveiliging](container-security.md)

- [Inleiding tot Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)


