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
ms.openlocfilehash: 1c1b48d3715d838827f88f99fc0849d25677fdcc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585744"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry-integratie met Security Center

Azure Container Registry (ACR) is een beheerde, privé Docker-registerservice die uw containerafbeeldingen voor Azure-implementaties opslaat en beheert in een centraal register. Het is gebaseerd op de open-source Docker Registry 2.0.

Als u de standaardlaag van Azure Security Center bevindt, u de containerregistersbundel toevoegen. Deze optionele functie brengt een dieper inzicht in de kwetsbaarheden van de afbeeldingen in uw ARM-gebaseerde registers. Schakel de bundel op abonnementsniveau in of uit om alle registers in een abonnement te dekken. Deze functie wordt per afbeelding in rekening gebracht, zoals op de prijspagina wordt [weergegeven.](security-center-pricing.md) Als u de containerregistersbundel inschakelt, wordt ervoor dat Security Center klaar is om afbeeldingen te scannen die naar het register worden geschoven. 

Wanneer een afbeelding naar uw register wordt gepusht, scant Security Center die afbeelding automatisch. Als u de scan van een afbeelding wilt activeren, duwt u deze naar uw opslagplaats.

Wanneer de scan is voltooid (meestal na ongeveer 10 minuten), zijn bevindingen beschikbaar in aanbevelingen van het Beveiligingscentrum als volgt:

[![Voorbeeld van azure security center-aanbeveling over kwetsbaarheden die zijn ontdekt in een acr-afbeelding (Azure Container Registry)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Voordelen van integratie

Security Center identificeert ARM-gebaseerde ACR-registers in uw abonnement en biedt naadloos:

* **Azure-native kwetsbaarheid scannen** voor alle gepushte Linux-afbeeldingen. Security Center scant de afbeelding met behulp van een scanner van de toonaangevende leverancier van kwetsbaarheidscannen, Qualys. Deze native oplossing is standaard naadloos geïntegreerd.

* **Beveiligingsaanbevelingen** voor Linux-afbeeldingen met bekende kwetsbaarheden. Security Center bevat details over elk gemeld beveiligingslek en een ernstclassificatie. Bovendien, het geeft richtlijnen voor hoe de specifieke kwetsbaarheden gevonden op elke afbeelding geduwd om het register te verhelpen.

![Overzicht van Azure Security Center en Azure Container Registry (ACR) op hoog niveau](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de containerbeveiligingsfuncties van Security Center:

* [Azure Security Center en containerbeveiliging](container-security.md)

* [Integratie met Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Virtual Machine protection](security-center-virtual-machine-protection.md) - Beschrijft de aanbevelingen van security center