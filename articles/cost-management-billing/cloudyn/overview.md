---
title: Overzicht van Cloudyn in Azure | Microsoft Docs
description: Cloudyn is een kostenbeheeroplossing voor meerdere clouds die helpt bij het gebruik van Azure- en andere cloudresources.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: bfd00613a3949b29e2defcb6f97398a39091d0e6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76774060"
---
# <a name="what-is-the-cloudyn-service"></a>Wat is de Cloudyn-service?

Met Cloudyn, een dochteronderneming van Microsoft, kunt u het gebruik van de cloud en de uitgaven voor uw Azure-resources en andere cloudproviders (waaronder AWS en Google) volgen. Aan de hand van eenvoudige dashboard-rapporten krijgt u inzicht in de toewijzing van kosten en tevens factureringen en teruggaven. Met behulp van Cloudyn kunt u uw cloud optimaliseren door weinig gebruikte resources te identificeren, die u vervolgens kunt aanpassen en beheren.

Zie [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Inleiding tot Azure Cloudyn) voor een inleidende video.

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [Azure Cost Management](../cost-management-billing-overview.md) voor meer informatie.

Bekijk de [video over Azure Cost Management en Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) om aanbevelingen te zien voor het gebruik van Azure Cost Management of Cloudyn, op basis van uw bedrijfsbehoeften.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Cloudyn-functies worden overgebracht naar Azure Cost Management

Microsoft heeft Cloudyn overgenomen en de functies voor kostenbeheer worden nu overgebracht van de Cloudyn-portal naar Azure. Als u de nieuwe functies wilt gebruiken, meldt u zich aan bij de Azure-portal en selecteert u [Kostenbeheer en facturering](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) in de lijst met Azure-services. Vergeleken met Cloudyn biedt de systeemeigen ervaring betere prestaties en een lagere gegevenslatentie van ongeveer acht uur.

De migratie van belangrijke functies voor de aanbiedingscategorieën Enterprise Agreement, Betalen per gebruik en MSDN naar Azure Cost Management is voltooid. CSP-abonnementen worden inmiddels gemigreerd naar Azure Cost Management.

Als u een aanbiedingscategorie hebt die nog niet is gemigreerd, moet u de Cloudyn-portal blijven gebruiken. In alle andere gevallen kunt u Azure Cost Management gebruiken.

| Aanbiedingen en functies van Microsoft Azure | Aanbevolen service voor kostenbeheer |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Ondersteuning voor kostenanalyse van clouds voor AWS (in preview) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS-aanbevelingen | [Cloudyn](https://azure.cloudyn.com) |

Enkele van de volgende functies zijn beschikbaar in Cloudyn, maar alle functies zijn nu beschikbaar in Azure Cost Management.

- API's
- Aanbevelingen voor Azure Compute
- Aanbevelingen voor Azure-reserveringen
- Budgetten
- Kostenanalyse
- Gegevens exporteren naar een Azure-opslagaccount
- Lagere latentie
- Power BI-inhoudspakket en -connector
- Ondersteuning voor resourcetags

## <a name="monitor-usage-and-spending"></a>Gebruik en uitgaven bewaken

Het bewaken van uw gebruik en uitgaven is cruciaal voor cloudinfrastructuren, omdat organisaties betalen voor de resources waarvan ze gebruik maken. Als het gebruik de afgesproken drempelwaarden overschrijden, kunnen zich al snel onverwachte kostenoverschrijdingen voordoen. Enkele belangrijke factoren kunnen ad-hocbewaking lastig maken. Ten eerste: bij het beramen van kosten op basis van gemiddeld gebruik wordt aangenomen dat uw verbruik consistent blijft gedurende een bepaalde factureringsperiode. Ten tweede: als de kosten uw budget (dreigen te) overschrijden, is het belangrijk dat u vooraf meldingen krijgt om uw bestedingen te kunnen aanpassen. Ten slotte: sommige cloudserviceproviders leveren geen kostenramingen versus drempelwaarden of rapporten met vergelijkingen tussen bepaalde perioden.

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
