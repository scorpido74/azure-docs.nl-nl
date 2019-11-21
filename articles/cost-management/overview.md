---
title: Overzicht van Cloudyn in Azure | Microsoft Docs
description: Cloudyn is een kostenbeheeroplossing voor meerdere clouds die helpt bij het gebruik van Azure- en andere cloudresources.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 70951bfdafb9e54a63c2645d4993233fc4b8446c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230017"
---
# <a name="what-is-the-cloudyn-service"></a>Wat is de Cloudyn-service?

Met Cloudyn, een dochteronderneming van Microsoft, kunt u het gebruik van de cloud en de uitgaven voor uw Azure-resources en andere cloudproviders (waaronder AWS en Google) volgen. Aan de hand van eenvoudige dashboard-rapporten krijgt u inzicht in de toewijzing van kosten en tevens factureringen en teruggaven. Met behulp van Cloudyn kunt u uw cloud optimaliseren door weinig gebruikte resources te identificeren, die u vervolgens kunt aanpassen en beheren.

Zie [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Inleiding tot Azure Cloudyn) voor een inleidende video.

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [Azure Cost Management](overview-cost-mgt.md) voor meer informatie.

Watch the [Azure Cost Management and Cloudyn video](https://www.youtube.com/watch?v=PmwFWwSluh8) to see recommendations when you should use either Azure Cost Management or Cloudyn, based on your business needs.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Cloudyn features moving to Azure Cost Management

Microsoft acquired Cloudyn and is migrating its cost management features from the Cloudyn portal natively into Azure. To use the new features, sign-in to the Azure portal and navigate to [Cost Management and Billing](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) in the list of Azure services. Compared to Cloudyn, the native experience offers improved performance and lower data latency of about eight hours.

Key feature migration for Enterprise Agreement, Pay-As-You-Go, and MSDN offer categories to Azure Cost Management is complete. CSP subscriptions are in the process of being migrated over to Azure Cost Management.

If you have an offer category not yet migrated, you should continue to use the Cloudyn portal. Everyone else can use Azure Cost Management.

| Microsoft Azure offers and features | Recommended cost management service |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Cross-cloud cost analysis support for AWS (in preview) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS recommendations | [Cloudyn](https://azure.cloudyn.com) |

Some of the following features are available in Cloudyn, but all of them are available now in Azure Cost Management.

- API's
- Azure compute recommendations
- Azure Reservation recommendations
- Budgetten
- Kostenanalyse
- Export data to an Azure storage account
- Lagere latentie
- Power BI content pack and connector
- Resource tag support

## <a name="monitor-usage-and-spending"></a>Gebruik en uitgaven bewaken

Het bewaken van uw gebruik en uitgaven is cruciaal voor cloudinfrastructuren, omdat organisaties betalen voor de resources waarvan ze gebruik maken. Als het gebruik de afgesproken drempelwaarden overschrijden, kunnen zich al snel onverwachte kostenoverschrijdingen voordoen. A few important factors can make ad hoc monitoring difficult. Ten eerste: bij het beramen van kosten op basis van gemiddeld gebruik wordt aangenomen dat uw verbruik consistent blijft gedurende een bepaalde factureringsperiode. Ten tweede: als de kosten uw budget (dreigen te) overschrijden, is het belangrijk dat u vooraf meldingen krijgt om uw bestedingen te kunnen aanpassen. Ten slotte: sommige cloudserviceproviders leveren geen kostenramingen versus drempelwaarden of rapporten met vergelijkingen tussen bepaalde perioden.

Rapporten kunnen uw uitgaven helpen bewaken voor het analyseren en volgen van het gebruik van, de kosten voor en de trends in de cloud. Door rapporten met overuren te gebruiken, kunt u afwijkingen in de trends detecteren. Ondoelmatigheden in de cloudimplementatie komen naar voren in optimalisatierapporten. In kostenanalyserapporten ziet u ook ondoelmatigheden.

## <a name="manage-costs"></a>Kosten beheren

Met behulp van historische gegevens kunt u kosten beheren bij het analyseren van uw gebruik en kosten in de loop van de tijd om trends te herkennen. De trends worden vervolgens gebruikt om een schatting te maken van toekomstige uitgaven. Cloudyn omvat tevens nuttige rapporten met geraamde kosten.

Kostentoewijzing gaat over het beheer van kosten door het analyseren van uw kosten op basis van uw taggingbeleid. U kunt tags gebruiken voor uw aangepaste accounts, resources en entiteiten om de kostentoewijzing te verfijnen. Category Manager organiseert uw tags voor extra overzicht. En u gebruikt kostentoewijzing voor factureringen/terugstortingen om de kosten van resourcegebruik en aanverwante kosten te tonen om consumptief gedrag te beïnvloeden of tenantklanten te factureren.

Door middel van toegangscontrole kunt u kosten beheren door ervoor te zorgen dat gebruikers en teams alleen toegang hebben tot de gegevens voor kostenbeheer die ze echt nodig hebben. U maakt gebruik van entiteitsstructuur, gebruikersbeheer en geplande rapporten met ontvangerslijsten om toegang te verlenen.

Waarschuwingen helpen u de kosten beheren doordat u automatisch op de hoogte wordt gebracht als er ongewone uitgaven of budgetoverschrijdingen plaatsvinden. Waarschuwingen kunnen ook andere belanghebbenden automatisch op de hoogte stellen als er sprake is van afwijkende bestedingen of kans op budgetoverschrijding. Diverse rapporten ondersteunen waarschuwingen op basis van budgettaire en kostendrempels. Waarschuwingen worden momenteel echter niet ondersteund voor CSP-partneraccounts of -abonnementen.

## <a name="improve-efficiency"></a>Efficiëntie verbeteren

Met Cloudyn kunt u het optimale VM-gebruik vaststellen en inactieve VM's identificeren of inactieve VM's en niet-gekoppelde schijven verwijderen. Met behulp van gegevens uit rapporten over optimaal en inefficiënt rangschikken op afmetingen kunt u een plan opstellen om inactieve VM's uit bedrijf te halen. Optimaliseringsrapporten worden momenteel echter niet ondersteund voor CSP-partneraccounts of -abonnementen.

Als u gereserveerde AWS-instanties van virtuele machines hebt ingericht, kunt u het gebruik van gereserveerde instanties verbeteren aan de hand van optimaliseringsrapporten. Hierin ziet u aanbevelingen voor aanschafbeleid, kunt u ongebruikte reserveringen wijzigen en inrichtingen plannen.


## <a name="next-steps"></a>Volgende stappen

U bent nu bekend met Cloudyn. In de volgende stap registreert u uw cloudomgeving en onderzoekt u uw gegevens.

- [Een individueel Azure-abonnement registreren](quick-register-azure-sub.md)
