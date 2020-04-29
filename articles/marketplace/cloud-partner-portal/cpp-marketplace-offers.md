---
title: Azure en AppSource Marketplace biedt
description: Aanbiedingen van de Azure-en AppSource-markt plaatsen maken en beheren
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278481"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure en AppSource Marketplace biedt

In dit eerste deel van deze sectie worden de algemene bewerkingen geïntroduceerd voor het maken en beheren van aanbiedingen voor de Azure-en AppSource-markt plaatsen.  Dit gedeelte bevat de achtergrond die u moet begrijpen om specifieke aanbiedings typen te beheren, evenals technische informatie die gebruikelijk is voor alle typen aanbiedingen.  De meeste van deze sectie bevat gedetailleerde instructies voor het maken en beheren van specifieke aanbiedings typen.  

De volgende video bevat een inleiding tot de verschillende mogelijkheden en verschillende aanbiedings typen die beschikbaar zijn in azure Marketplace of AppSource.  Het omvat ook belang rijke technische en zakelijke aspecten van het publiceren van een toepassing of service in deze markt plaatsen.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Apps en services bouwen voor Azure Marketplace en AppSource-build 2018**

Zie de [publicatie handleiding voor Azure Marketplace en AppSource](../marketplace-publishers-guide.md)voor meer informatie over deze markt plaatsen.


## <a name="common-offer-operations"></a>Algemene aanbiedings bewerkingen

Het proces voor het maken van een nieuwe aanbieding verschilt veel van de aanbiedings typen, bijvoorbeeld tussen een [Azure-toepassings aanbieding](./azure-applications/cpp-azure-app-offer.md) en een [advies service-aanbieding](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Daarentegen worden veel van de andere bewerkingen die u uitvoert op een aanbieding in het [Cloud Partner-Portal](https://cloudpartner.azure.com) , standaard op de verschillende soorten aanbiedingen.  Deze algemene bewerkingen, waaronder publiceren, weer geven status, bijwerken en verwijderen, worden behandeld in de sectie [aanbiedingen beheren](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Test Drive

*Test drive* is een Marketplace-functie waarmee klanten een demonstratie optie voor ' try voor u kopen ' bieden voor elke aanbieding die is ingeschakeld.  De capaciteit van het test station is beperkt tot de volgende subset van aanbiedings typen: [Azure-toepassingen](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 voor klant betrokkenheid](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 voor Financiën en bewerkingen](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS-toepassingen](./saas-app/cpp-saas-offer.md)en [virtuele machines](./virtual-machine/cpp-virtual-machine-offer.md).  Voor deze functie moet de uitgever een sjabloon voor het testen van een station maken, aangepast voor de aanbieding.  Zie de sectie [test station](./test-drive/what-is-test-drive.md)voor meer informatie.

U kunt door de bestaande Marketplace-aanbiedingen bladeren die een test drive demonstratie hebben door het [test drive-filter](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)toe te passen. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace-en AppSource-aanbiedings typen

De volgende tabel geeft een lijst van de huidige aanbiedings typen die door de [Cloud Partner-Portal](https://cloudpartner.azure.com)worden ondersteund.  Voor elk aanbiedings type wordt een lijst weer gegeven met de Marketplace ('s) waar de aanbieding kan worden vermeld, evenals een algemene beschrijving van de technologie van de aanbod oplossing.

|                Aanbiedings type                |  Marketplace  |   Beschrijving                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-toepassing](./azure-applications/cpp-azure-app-offer.md) | Azure | De oplossing bestaat uit een of meer virtuele machines (Vm's), optionele aangepaste Azure-code, geïmplementeerd via een Azure Resource Manager-sjabloon.  De implementatie kan door de klant worden door middel van een oplossings sjabloon of worden beheerd door de uitgever. Dit type wordt gebruikt om meer flexibiliteit te bieden dan het geleverde type voor de virtuele machine.  |
| [Consulting Service](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | beide | Micro soft-bevoegde consultants kunnen hun domeingebonden Services vermelden op Azure Marketplace of AppSource.  Hun expertise helpt klanten bij het beoordelen van hun problemen en het maken en implementeren van de juiste oplossingen om te voldoen aan de doel stellingen van hun bedrijf.  |
| [Verpakking](./containers/cpp-containers-offer.md)  | Azure | De oplossing is een docker-container installatie kopie die is ingericht als een Kubernetes service of Azure container instances. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Een pakket dat deze Enter prise resource planning (ERP) en het beheer systeem voor bedrijven uitbreidt. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Een pakket dat dit CRM-systeem (Customer Resource Management) uitbreidt via de modules Sales, service, project service en Field Service  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Een pakket dat deze ERP-service (Enter prise resource planning) uitbreidt die ondersteuning biedt voor geavanceerde Financiën, bedrijfs activiteiten, productie en supply chain management |
| [Module IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Een docker-compatibele container die wordt uitgevoerd op een IoT Edge apparaat.  Het bestaat uit kleine reken modules die gebruikmaken van een combi natie van aangepaste code, andere Azure-Services en services van derden. |
| [Power BI-app](./power-bi/cpp-power-bi-offer.md) | AppSource | Een Power BI-app die aanpas bare Power BI inhoud verpakt, inclusief gegevens sets, rapporten en dash boards |
| [SaaS-app](./saas-app/cpp-saas-offer.md) | Azure | De oplossing is een software-as-a-service-abonnement dat wordt beheerd door de uitgever, waarmee gebruikers zich aanmelden via een aangepaste interface die gebruikmaakt van Azure Active Directory. |
| [Virtuele machine](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | De oplossing bevindt zich in één virtuele machine die is geïmplementeerd op het abonnement van de klant.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Zie voor meer informatie [publicatie handleiding per aanbiedings type](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Volgende stappen

U vindt meer informatie over de algemene bewerkingen die u kunt uitvoeren op Marketplace-aanbiedingen en de algemene technische kenmerken en activa in het artikel [Managed aanbiedingen](./manage-offers/cpp-manage-offers.md).
