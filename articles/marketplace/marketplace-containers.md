---
title: Publicatie handleiding voor container aanbiedingen in azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van container aanbiedingen in azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: fae11343332fe741301016d4871d098b0214c7f5
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964444"
---
# <a name="publishing-guide-for-container-offers"></a>Publicatie handleiding voor container aanbiedingen

Met container aanbiedingen kunt u uw container installatie kopie naar Azure Marketplace publiceren. Gebruik deze hand leiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Container aanbiedingen zijn transactie aanbiedingen die worden geïmplementeerd en gefactureerd via Azure Marketplace. De aanroep van een actie die een gebruiker ziet is ' nu ophalen '.

Gebruik het type *container* aanbod wanneer uw oplossing een docker-container installatie kopie is die is ingesteld als een Kubernetes Azure container service-exemplaar. 

> [!NOTE]
> Voor beelden van Azure container service-exemplaren op basis van Kubernetes zijn Azure Kubernetes service of Azure Container Instances, de keuze van Azure-klanten voor een op Kubernetes gebaseerde container-runtime.  

Micro soft biedt momenteel ondersteuning voor de BYOL-licentie modellen (gratis en meebrengen van eigen licentie).

## <a name="container-offer-requirements"></a>Vereisten voor container aanbod

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis of BYOL facturerings model.<br><br> |  
| Installatie kopie die is gebouwd op basis van een Dockerfile | Container installatie kopieën moeten zijn gebaseerd op de specificatie van de docker-installatie kopie en zijn gebouwd op basis van een Dockerfile.<br> <br>Voor meer informatie over het bouwen van docker-installatie kopieën raadpleegt u de sectie ' gebruik ' van [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosting in een Azure Container Registry opslagplaats | Container installatie kopieën moeten worden gehost in een Azure Container Registry opslag plaats.<br> <br>Voor meer informatie over het werken met Azure Container Registry raadpleegt [u Quick Start: een persoonlijk container register maken met behulp van de Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Afbeeldingen taggen | Container installatie kopieën moeten ten minste één tag bevatten (maximum aantal Tags: 16).<br><br>Zie de `docker tag` pagina op de documentatie site van [docker](https://docs.docker.com/engine/reference/commandline/tag) voor meer informatie over het coderen van een afbeelding.<br><br> |  

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, leert u hoe u [uw Cloud activiteiten kunt verg Roten met Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registreren voor en werken in het partner centrum:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- Zie [een Azure-container aanbieding maken](./partner-center-portal/create-azure-container-offer.md) voor meer informatie.
