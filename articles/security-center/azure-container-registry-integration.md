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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 0ca7bfb276f49da720264305a92d31e81857cfd5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229323"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integratie met Security Center (preview-versie)

Azure Container Registry (ACR) is een beheerde, persoonlijke docker-register service waarmee uw container installatie kopieën voor Azure-implementaties in een centraal REGI ster worden opgeslagen en beheerd. Het is gebaseerd op de open-source docker Registry 2,0.

Gebruikers Azure Security Center van de Standard-laag van het REGI ster en de installatie kopieën kunnen de optionele versie van de container registers inschakelen voor een diepere zicht baarheid in uw register-en afbeeldings problemen. Ga voor meer informatie naar het overzicht van [prijzen](security-center-pricing.md). Als de bundel is ingeschakeld, worden in Security Center automatisch afbeeldingen in uw REGI ster gescand wanneer een installatie kopie naar het REGI ster wordt gepusht.

> [!NOTE]
> De eerste scan van een REGI ster wordt alleen uitgevoerd nadat de bundel van de container registers is ingeschakeld en een installatie kopie naar het REGI ster wordt gepusht. Security Center

Wanneer de scan is voltooid (doorgaans na ongeveer 10 minuten), zijn er conclusies beschikbaar in Security Center aanbevelingen als volgt:

[![voor beeld Azure Security Center aanbeveling over beveiligings problemen gedetecteerd in een gehoste installatie kopie van een Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Voor delen van integratie

Security Center identificeert ACR-registers in uw abonnement en biedt probleemloos het volgende:

* **Azure-systeem eigen beveiligings problemen scannen** voor alle pushed Linux-installatie kopieën. Security Center scant de installatie kopie met behulp van een scanner van de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys. Deze systeem eigen oplossing is standaard naadloos geïntegreerd.

* **Beveiligings aanbevelingen** voor Linux-installatie kopieën met bekende beveiligings problemen. Security Center bevat details van elk gemeld beveiligings probleem en een Ernst classificatie. Daarnaast biedt het hulp bij het herstellen van de specifieke beveiligings problemen die zijn gevonden op elke installatie kopie die naar het REGI ster wordt gepusht.

![Overzicht op hoog niveau Azure Security Center en Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met de Azure Kubernetes-service](azure-kubernetes-service-integration.md)

* [Beveiliging van virtuele machines](security-center-virtual-machine-protection.md) -Hiermee worden de aanbevelingen van Security Center beschreven