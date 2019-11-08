---
title: Containers bieden een publicatie handleiding voor Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van containers in Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822996"
---
# <a name="containers-offer-publishing-guide"></a>De containers bieden een publicatie gids

Met container aanbiedingen kunt u uw container installatie kopie naar Azure Marketplace publiceren. Gebruik deze hand leiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Dit zijn transactie aanbiedingen die worden geïmplementeerd en gefactureerd via Marketplace. De aanroep van een actie die een gebruiker ziet is ' nu ophalen '.

Gebruik het type container aanbod wanneer uw oplossing een docker-container installatie kopie is ingericht als een op Kubernetes gebaseerde Azure-container service.

>[!NOTE]
>Bijvoorbeeld een Kubernetes Azure container service zoals Azure Kubernetes service of Azure Container Instances, de keuze van Azure-klanten voor een op Kubernetes gebaseerde container-runtime.  

Micro soft biedt momenteel ondersteuning voor de BYOL-licentie modellen (gratis en meebrengen van eigen licentie).

## <a name="containers-offer"></a>Aanbod van containers

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis of BYOL facturerings model. |  
| Installatie kopie die is gebouwd op basis van Dockerfile | Container installatie kopieën moeten zijn gebaseerd op de specificatie van de docker-installatie kopie en moeten worden gebouwd op basis van een Dockerfile.<ul> <li>Ga voor meer informatie over het bouwen van docker-installatie kopieën naar het gedeelte gebruik dat zich bevindt op [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting in ACR | Container installatie kopieën moeten worden gehost in een ACR-opslag plaats (Azure Container Registry).<ul> <li>Ga voor meer informatie over het werken met ACR naar de Snelstartgids: een container register maken met behulp van de pagina Azure Portal op [docs.Microsoft.com/azure/container-Registry/container-Registry-Get-Started-Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Afbeeldings codes | Container installatie kopieën moeten ten minste één tag (maximum aantal Tags: 16) bevatten.<ul> <li>Ga voor meer informatie over het coderen van een afbeelding naar de pagina docker tag op [docs.docker.com/engine/Reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, 

- [Registreer](https://azuremarketplace.microsoft.com/sell) u in de Marketplace.

Als u bent geregistreerd en een nieuwe aanbieding maakt of aan een bestaand item werkt,

- [Meld u aan bij Cloud Partner-Portal](https://cloudpartner.azure.com) om uw aanbieding te maken of te volt ooien.
- Zie [containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) voor meer informatie.
