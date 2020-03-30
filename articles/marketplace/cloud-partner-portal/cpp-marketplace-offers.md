---
title: Azure- en AppSource Marketplace-aanbiedingen
description: Aanbiedingen van de Azure- en AppSource-marktplaatsen maken en beheren
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278481"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure- en AppSource Marketplace-aanbiedingen

In dit eerste deel van deze sectie worden de algemene bewerkingen geïntroduceerd die worden gebruikt voor het maken en beheren van aanbiedingen voor de Azure- en AppSource-marketplaces.  Dit deel biedt de achtergrond die u moet begrijpen om specifieke aanbiedingstypen te beheren, evenals technische informatie die gebruikelijk is voor alle aanbiedingstypen.  De meerderheid van deze sectie bevat gedetailleerde instructies over het maken en beheren van specifieke aanbiedingstypen.  

De volgende video introduceert de verschillende mogelijkheden en verschillende aanbiedingstypen die beschikbaar zijn in Azure Marketplace of AppSource.  Het omvat ook belangrijke technische en zakelijke aspecten van het publiceren van een applicatie of dienst in deze marktplaatsen.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Apps en services bouwen voor Azure Marketplace en AppSource - Build 2018**

Zie [Azure Marketplace- en AppSource-publicatiehandleiding voor](../marketplace-publishers-guide.md)meer informatie over deze marktplaatsen.


## <a name="common-offer-operations"></a>Transacties met een gemeenschappelijk aanbod

Het proces van het maken van een nieuwe aanbieding verschilt sterk tussen aanbiedingstypen, bijvoorbeeld tussen een [Azure-toepassingsaanbieding](./azure-applications/cpp-azure-app-offer.md) en een [consultingserviceaanbieding.](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)  Veel van de andere bewerkingen die u uitvoert op een aanbieding in de [Cloud Partner Portal](https://cloudpartner.azure.com) zijn daarentegen vrij gestandaardiseerd voor alle aanbiedingstypen.  Deze veelvoorkomende bewerkingen, waaronder publiceren, bekijken status, bijwerken en verwijderen, worden behandeld in de sectie [Aanbiedingen beheren](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Test Drive

*Test Drive* is een marktplaatsfunctie die klanten een demonstratieoptie voor elke aanbieding biedt die voor elke aanbieding is ingeschakeld.  De testdrive-mogelijkheid is beperkt tot de volgende subset van aanbiedingstypen: [Azure-toepassingen](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), Dynamics [365 voor customer engagement,](./dyn365ce/cpp-customer-engagement-offer.md) [Dynamics 365 voor financiën en bewerkingen,](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) [SaaS-toepassingen](./saas-app/cpp-saas-offer.md)en [virtuele machines](./virtual-machine/cpp-virtual-machine-offer.md).  Voor deze mogelijkheid moet de uitgever een teststationsjabloon maken, aangepast voor zijn aanbieding.  Zie de sectie [Proefrit voor](./test-drive/what-is-test-drive.md)meer informatie .

U bladeren door de bestaande marktplaatsaanbiedingen met Test Drive-demonstraties door het [filter voor teststations](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)toe te passen. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace- en AppSource-aanbiedingstypen

In de volgende tabel worden de huidige aanbiedingstypen weergegeven die worden ondersteund door de [Cloud Partner Portal.](https://cloudpartner.azure.com)  Voor elk aanbiedingstype worden de marktplaats(en) vermeld waar het aanbod kan worden vermeld, evenals een algemene beschrijving van de technologie voor de aanbiedingsoplossing.

|                Aanbiedingstype                |  Marketplace  |   Beschrijving                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-toepassing](./azure-applications/cpp-azure-app-offer.md) | Azure | De oplossing bestaat uit een of meer virtuele machines (VM's), optionele aangepaste Azure-code, geïmplementeerd via een Azure Resource Manger-sjabloon.  Implementatie kan door de klant via een oplossingssjabloon worden beheerd of door de uitgever worden beheerd. Dit type wordt gebruikt om meer flexibiliteit te bieden dan voorzien virtuele machine aanbod type.  |
| [Adviesservice](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | beide | Door Microsoft gekwalificeerde consultants kunnen hun domeinspecifieke services aanbieden op Azure Marketplace of AppSource.  Hun expertise helpt klanten bij het beoordelen van hun problemen en het creëren en implementeren van de juiste oplossingen om hun bedrijfsdoelstellingen te bereiken.  |
| [Container](./containers/cpp-containers-offer.md)  | Azure | Oplossing is een Docker-containerafbeelding die is ingericht als een op Kubernetes gebaseerde service of Azure Container-exemplaren. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Een pakket dat dit ERP-systeem (Enterprise Resource Planning) en business management uitbreidt. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Een pakket dat dit CRM-systeem (Customer Resource Management) uitbreidt via de modules verkoop, service, projectservice en fieldservice  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Een pakket dat deze ERP-service (Enterprise Resource Planning) uitbreidt die geavanceerde financiering, bedrijfsvoering, productie en supply chain-beheer ondersteunt |
| [IoT Edge-module](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Een Docker-compatibele container die wordt uitgevoerd op een IoT Edge-apparaat.  Het bestaat uit kleine computermodules die een combinatie van aangepaste code, andere Azure-services en services van derden gebruiken. |
| [Power BI-app](./power-bi/cpp-power-bi-offer.md) | AppSource | Een Power BI-app die aanpasbare Power BI-inhoud verpakt, inclusief gegevenssets, rapporten en dashboards |
| [SaaS-app](./saas-app/cpp-saas-offer.md) | Azure | Oplossing is een software-as-a-service-abonnement, beheerd door de uitgever, dat gebruikers aanmelden via een aangepaste interface die Azure Active Directory gebruikt. |
| [Virtuele machine](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Oplossing is opgenomen in een virtuele machine geïmplementeerd op het abonnement van de klant.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Zie [Publicatiegids op aanbiedingstype voor](../publisher-guide-by-offer-type.md)meer informatie .


## <a name="next-steps"></a>Volgende stappen

U leert over de algemene bewerkingen die u uitvoeren op marktplaatsaanbiedingen en hun algemene technische kenmerken en activa in het artikel [Aanbiedingen beheren.](./manage-offers/cpp-manage-offers.md)
