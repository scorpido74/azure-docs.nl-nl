---
title: Publicatiegids voor containers voor Azure Marketplace
description: In dit artikel worden de vereisten beschreven om containers op de Marketplace te publiceren
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 80bcf0d5d37a44cee2aab38161abac619542204a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684329"
---
# <a name="containers-offer-publishing-guide"></a>Publicatiegids voor containers

Containeraanbiedingen helpen u uw containerafbeelding te publiceren naar de Azure Marketplace. Gebruik deze handleiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Dit zijn transactieaanbiedingen die worden geïmplementeerd en gefactureerd via de Marketplace. De call-to-action die een gebruiker ziet is 'Nu halen'.

Gebruik het type containeraanbieding wanneer uw oplossing een Docker-containerafbeelding is die is ingericht als een Op Kubernetes gebaseerde Azure-containerservice.

>[!NOTE]
>Bijvoorbeeld een Op Kubernetes gebaseerde Azure-containerservice zoals Azure Kubernetes Service of Azure Container Instances, de keuze van Azure-klanten voor een op Kubernetes gebaseerde containerruntime.  

Microsoft ondersteunt momenteel gratis byol-licentiemodellen (bring-your-own-license).

## <a name="containers-offer"></a>Containers Aangeboden

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis of BYOL-factureringsmodel. |  
| Afbeelding die is gebouwd met Dockerfile | Containerafbeeldingen moeten gebaseerd zijn op de docker-afbeeldingsspecificatie en moeten zijn opgebouwd uit een Dockerfile.<ul> <li>Ga voor meer informatie over het maken van dockerafbeeldingen naar de sectie Gebruik op [docs.docker.com/engine/reference/builder/#usage.](https://docs.docker.com/engine/reference/builder/#usage)</li> </ul> |  
| Hosting in ACR | Containerafbeeldingen moeten worden gehost in een ACR-opslagplaats (Azure Container Registry).<ul> <li>Ga voor meer informatie over het werken met ACR naar de Quickstart: Een containerregister maken met de Azure-portalpagina op [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Afbeeldingen taggen | Containerafbeeldingen moeten ten minste 1 tag bevatten (maximale tags: 16).<ul> <li>Ga voor meer informatie over het taggen van een afbeelding naar de tagpagina van de docker op [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Volgende stappen

Als je dat nog niet gedaan hebt, 

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over de marktplaats.

Als u zich wilt registreren in partnercentrum, begint u met het maken van een nieuwe aanbieding of werkt u aan een bestaand aanbieding:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
- Zie [een Azure-containeraanbieding maken](./partner-center-portal/create-azure-container-offer.md) voor meer informatie.
