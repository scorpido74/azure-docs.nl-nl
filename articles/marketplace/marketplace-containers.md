---
title: Publicatiegids voor containers voor Azure Marketplace
description: In dit artikel worden de vereisten beschreven om containers op de Marketplace te publiceren
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277303"
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

- [Registreer](https://azuremarketplace.microsoft.com/sell) op de marktplaats.

Als u bent geregistreerd en een nieuwe aanbieding maakt of aan een bestaand aanbod werkt,

- [Meld u aan bij Cloud Partner Portal](https://cloudpartner.azure.com) om uw aanbieding te maken of te voltooien.
- Zie [Containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) voor meer informatie.
