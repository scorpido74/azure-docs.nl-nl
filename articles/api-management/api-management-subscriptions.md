---
title: Abonnementen in Azure API Management | Microsoft Documenten
description: Meer informatie over het concept van abonnementen in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073410"
---
# <a name="subscriptions-in-azure-api-management"></a>Abonnementen maken in Azure API Management

Abonnementen zijn een belangrijk concept in Azure API Management. Ze zijn de meest voorkomende manier voor API-consumenten om toegang te krijgen tot API's gepubliceerd via een API Management-exemplaar. Dit artikel geeft een overzicht van het concept.

## <a name="what-are-subscriptions"></a>Wat zijn abonnementen?

Wanneer u API's publiceert via API-beheer, is het eenvoudig en gebruikelijk om de toegang tot deze API's te beveiligen met abonnementssleutels. Ontwikkelaars die de gepubliceerde API's moeten gebruiken, moeten een geldige abonnementssleutel in HTTP-verzoeken opnemen wanneer ze naar deze API's bellen. Anders worden de aanroepen onmiddellijk geweigerd door de API Management-gateway. Ze worden niet doorgestuurd naar de back-end diensten.

Om een abonnementssleutel te krijgen voor toegang tot API's, is een abonnement vereist. Een abonnement is in wezen een benoemde container voor een paar abonnementssleutels. Ontwikkelaars die de gepubliceerde API's moeten gebruiken, kunnen abonnementen krijgen. En ze hebben geen goedkeuring nodig van API-uitgevers. API-uitgevers kunnen ook abonnementen rechtstreeks maken voor API-consumenten.

> [!TIP]
> API Management ondersteunt ook andere mechanismen voor het beveiligen van toegang tot API's, waaronder de volgende voorbeelden:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Clientcertificaten](api-management-howto-mutual-certificates-for-clients.md)
> - [IP whitelisting](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Bereik van abonnementen

Abonnementen kunnen worden gekoppeld aan verschillende scopes: product, alle API's of een individuele API.

### <a name="subscriptions-for-a-product"></a>Abonnementen voor een product

Traditioneel werden abonnementen in API-beheer altijd gekoppeld aan één [API-productbereik.](api-management-terminology.md) Ontwikkelaars vonden de lijst met producten op de Developer Portal. Vervolgens zouden ze abonnementsaanvragen indienen voor de producten die ze wilden gebruiken. Nadat een abonnementsaanvraag is goedgekeurd, automatisch of door API-uitgevers, kan de ontwikkelaar de sleutels gebruiken om toegang te krijgen tot alle API's in het product. Op dit moment toont de ontwikkelaarsportal alleen de productscopeabonnementen onder de sectie gebruikersprofiel. 

![Productabonnementen](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> In bepaalde scenario's willen API-uitgevers mogelijk een API-product publiceren naar het publiek zonder de vereiste abonnementen. Ze kunnen de optie **Abonnement vereisen** deselecteren op de pagina **Instellingen** van het product in de Azure-portal. Hierdoor zijn alle API's onder het product toegankelijk zonder API-sleutel.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Abonnementen voor alle API's of een individuele API

Toen we de [verbruikslaag](https://aka.ms/apimconsumptionblog) van API-beheer introduceerden, hebben we een paar wijzigingen aangebracht om het sleutelbeheer te stroomlijnen:
- Ten eerste hebben we nog twee abonnementsscopes toegevoegd: alle API's en één API. Het bereik van abonnementen is niet langer beperkt tot een API-product. Het is nu mogelijk om sleutels te maken die toegang verlenen tot een API of alle API's binnen een API-beheerexemplaar, zonder dat u een product hoeft te maken en de API's er eerst aan hoeft toe te voegen. Ook wordt elke API Management-instantie nu geleverd met een onveranderlijk, all-API-abonnement. Dit abonnement maakt het eenvoudiger en eenvoudiger om API's in de testconsole te testen en debuggen.

- Ten tweede staat API Management nu **zelfstandige** abonnementen toe. Abonnementen hoeven niet langer te worden gekoppeld aan een ontwikkelaarsaccount. Deze functie is handig in scenario's zoals wanneer meerdere ontwikkelaars of teams een abonnement delen.

- Ten slotte kunnen API-uitgevers nu abonnementen rechtstreeks [maken](api-management-howto-create-subscriptions.md) in de Azure-portal:

    ![Flexibele abonnementen](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over API-beheer:

+ Leer andere [concepten](api-management-terminology.md) in API-beheer.
+ Volg onze [tutorials](import-and-publish.md) voor meer informatie over API Management.
+ Kijk op onze [FAQ-pagina](api-management-faq.md) voor veelgestelde vragen.
