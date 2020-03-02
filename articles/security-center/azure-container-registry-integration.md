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
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205990"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integratie met Security Center Azure Container Registry

Azure Container Registry (ACR) is een beheerde, persoonlijke docker-register service waarmee uw container installatie kopieën voor Azure-implementaties in een centraal REGI ster worden opgeslagen en beheerd. Het is gebaseerd op de open-source docker Registry 2,0.

Als u zich in de Standard-laag van Azure Security Center bevindt, kunt u de bundel met container registers toevoegen. Deze optionele functie biedt meer inzicht in de beveiligings problemen van de installatie kopieën in uw op ARM gebaseerde registers. Hiermee schakelt u de bundel op het abonnements niveau in of uit om alle registers in een abonnement te behandelen. Deze functie wordt per afbeelding in rekening gebracht, zoals wordt weer gegeven op de [pagina met prijzen](security-center-pricing.md). Als u de container register bundel inschakelt, zorgt u ervoor dat Security Center gereed is voor het scannen van installatie kopieën die naar het REGI ster worden gepusht. 

Wanneer een installatie kopie naar het REGI ster wordt gepusht, wordt de installatie kopie automatisch gescand door Security Center. U kunt de scan van een afbeelding activeren door deze naar uw opslag plaats te pushen.

Wanneer de scan is voltooid (doorgaans na ongeveer 10 minuten), zijn er conclusies beschikbaar in Security Center aanbevelingen als volgt:

[![voor beeld Azure Security Center aanbeveling over beveiligings problemen gedetecteerd in een gehoste installatie kopie van een Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Voor delen van integratie

Security Center identificeert op ARM gebaseerde ACR-registers in uw abonnement en biedt probleemloos het volgende:

* **Azure-systeem eigen beveiligings problemen scannen** voor alle pushed Linux-installatie kopieën. Security Center scant de installatie kopie met behulp van een scanner van de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys. Deze systeem eigen oplossing is standaard naadloos geïntegreerd.

* **Beveiligings aanbevelingen** voor Linux-installatie kopieën met bekende beveiligings problemen. Security Center bevat details van elk gemeld beveiligings probleem en een Ernst classificatie. Daarnaast biedt het hulp bij het herstellen van de specifieke beveiligings problemen die zijn gevonden op elke installatie kopie die naar het REGI ster wordt gepusht.

![Overzicht op hoog niveau Azure Security Center en Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de beveiligings functies van de container van Security Center:

* [Azure Security Center en container beveiliging](container-security.md)

* [Integratie met de Azure Kubernetes-service](azure-kubernetes-service-integration.md)

* [Beveiliging van virtuele machines](security-center-virtual-machine-protection.md) -Hiermee worden de aanbevelingen van Security Center beschreven
