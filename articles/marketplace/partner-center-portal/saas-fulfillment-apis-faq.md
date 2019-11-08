---
title: Api's voor SaaS-uitvoering-Veelgestelde vragen | Azure Marketplace
description: Ervaringen voor detectie en aankopen door de klanten van een SaaS-aanbieding op de Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 2f8aa41604ac176181252fb7b30cdeb2f039d10f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822877"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS-uitvoerings-API's - Veelgestelde vragen

De integratie vereisten met Azure Marketplace, waarmee Azure-klanten zich kunnen abonneren op de SaaS-aanbieding, worden weer gegeven.

## <a name="discovery-experience"></a>Detectie-ervaring

Zodra de aanbieding is gepubliceerd, kunnen Azure-gebruikers de SaaS-aanbieding in azure Marketplace ontdekken. Uw klanten kunnen aanbiedingen filteren op basis van product type (SaaS) en de SaaS-services ontdekken waar ze mee geïnteresseerd zijn.

## <a name="purchase-experience"></a>Ervaring kopen

Zodra een gebruiker is geïnteresseerd in een specifieke SaaS-service, kan de gebruiker zich abonneren op Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Wat betekent het dat een Azure-gebruiker zich abonneert op een SaaS-aanbieding in azure Marketplace?

Dit betekent dat een gebruiker de gebruiksrecht overeenkomst en privacyverklaring kan bekijken die is gekoppeld aan de SaaS-service en ermee akkoord gaat om deze te betalen volgens de door u ingestelde facturerings voorwaarden, de uitgever van de SaaS-aanbieding op de factuur van micro soft. Gebruikers kunnen hun bestaande betalings profiel in azure gebruiken om te betalen voor het SaaS-service verbruik.

Dit kan een groot aantal redenen zijn. Klanten kunnen nu op één plek ontdekken en zich abonneren met Microsoft Cloud platform als een vertrouwde bron, zonder dat ze elke ISV-Software moeten gebruiken die ze nodig hebben. Klanten kunnen ook hun bestaande betalings profiel gebruiken zonder dat ze expliciet afzonderlijke ISV-software hoeven te betalen.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Worden de gebruikers automatisch in rekening gebracht wanneer de aanbieding is geabonneerd?

Tijdens het abonneren op de SaaS-aanbieding heeft de gebruiker besloten om te betalen voor het gebruik van de SaaS-service via het micro soft-platform. Kosten worden echter alleen gestart wanneer de aanbieding wordt verbruikt. De gebruiker moet naar uw SaaS-aanbieding gaan en het aanmaken van accounts bevestigen om te beginnen met het gebruik van de aanbieding. Vervolgens meldt u micro soft om te beginnen met de facturering voor dit klant SaaS-abonnement.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hoe wordt u gewaarschuwd wanneer een gebruiker zich abonneert op uw SaaS-aanbieding?

Nadat u zich hebt geabonneerd op een aanbieding, kan de Azure-gebruiker al hun aanbiedingen in azure detecteren en beheren. Standaard is de status van een nieuw geabonneerde SaaS **-aanbieding ' inrichting, in behandeling '** . In deze status wordt de Azure-gebruiker gevraagd om een actie te **' account configureren '** om te bladeren naar de beheer ervaring van het SaaS-abonnement in de Azure Portal.

Wanneer de gebruiker op **account configureren**klikt, wordt deze omgeleid naar de website van de SaaS-service. De URL waarnaar ze worden genavigeerd, wordt geboden door de uitgever op het moment dat de aanbieding wordt gepubliceerd. Deze pagina wordt de landings pagina van de uitgever genoemd. Azure-gebruikers moeten zich kunnen aanmelden bij de SaaS-landings pagina op basis van hun bestaande AAD-referenties in Azure.

Wanneer de Azure-gebruiker wordt omgeleid naar de landings pagina, wordt een token toegevoegd aan de query-URL. Dit token is korte tijd en geldig voor een tijds duur van 24 uur. U kunt vervolgens de aanwezigheid van dit token detecteren en de API van micro soft aanroepen om meer context te verkrijgen die aan het token is gekoppeld.

![Abonnements stroom van de klant](media/saas-metering-service-integration-flow-a.png)

Zie het document [SaaS fulfillment API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) voor meer informatie over het API-contract voor het verwerken van Transact-scenario's in de levens cyclus van een SaaS-aanbieding.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Hoe weet u wat de SaaS-aanbieding is waarmee de gebruiker zich abonneert in azure?

De reactie op de `Resolve`-API bevat de aanbieding en plan informatie die is gekoppeld aan het SaaS-abonnement.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hoe kan de Azure-gebruiker het abonnement wijzigen dat is gekoppeld aan dit Azure-abonnement?

* De Azure-gebruiker kan het abonnement dat is gekoppeld aan het SaaS-abonnement rechtstreeks wijzigen in de SaaS-ervaring of via het micro soft-platform.

* Conversies kunnen op elk gewenst moment worden uitgevoerd in de facturerings cyclus. U moet een conversie bevestigen, die wordt uitgevoerd wanneer deze eenmaal is bevestigd.

* Tarieven voor het vooraf betaalde abonnement (**maandelijks** of **jaarlijks**) zijn Pro-beoordeeld. Alle overschrijding die tot de tijd van de conversie worden verzonden, worden in de volgende factuur in rekening gebracht. Nieuwe overschotten worden verzonden op basis van het nieuwe abonnement.

>[!Note]
>U kunt downgrades blok keren als u geen specifieke conversie paden wilt ondersteunen.

In de onderstaande volg orde wordt de stroom vastgelegd wanneer een Azure-klant het abonnement wijzigt in de SaaS-ervaring:

![Wijzigings stroom van klant abonnement](media/saas-metering-service-integration-flow-b.png)

In de onderstaande volg orde wordt de stroom vastgelegd wanneer een Azure-klant verandert in het abonnement van micro soft

![Wijzigings stroom klant-winkel abonnement](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hoe kan de Azure-gebruiker zich afmelden voor het plan dat is gekoppeld aan het Azure-abonnement?

Een Azure-gebruiker kan zich afmelden bij een gekochte SaaS-aanbieding, hetzij rechtstreeks in de SaaS-ervaring, hetzij via het micro soft-platform. Zodra de gebruiker zich afmeldt, worden er geen kosten meer in rekening gebracht voor de volgende facturerings cyclus.

In de onderstaande volg orde wordt de stroom vastgelegd wanneer een Azure-klant zich afmeldt bij de SaaS-aanbieding in de SaaS-ervaring:

![Klant heeft zich afgemeld bij de SaaS-ervaring](media/saas-metering-service-integration-flow-d.png)

In de onderstaande volg orde wordt de stroom vastgelegd wanneer de Azure-gebruiker zich afmeldt op de winkel van micro soft:

![Klant heeft zich afgemeld op de winkel van micro soft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [api's voor Marketplace metering service](./marketplace-metering-service-apis.md) voor meer informatie.
