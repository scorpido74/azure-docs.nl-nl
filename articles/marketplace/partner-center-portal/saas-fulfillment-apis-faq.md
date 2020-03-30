---
title: SaaS fulfillment API's - FAQ | Azure Marketplace
description: Detectie- en aankoopervaringen door de klanten van een SaaS-aanbieding in de Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275693"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS-uitvoerings-API's - Veelgestelde vragen

De integratievereisten met Azure Marketplace om Azure-klanten in staat te stellen zich te abonneren op de SaaS-aanbieding worden vermeld.

## <a name="discovery-experience"></a>Ontdekkingservaring

Zodra de aanbieding is gepubliceerd, kunnen Azure-gebruikers de SaaS-aanbieding in Azure Marketplace ontdekken. Uw klanten kunnen aanbiedingen filteren op basis van producttype (SaaS) en de SaaS-diensten ontdekken waarin ze geïnteresseerd zijn.

## <a name="purchase-experience"></a>Aankoopervaring

Zodra een gebruiker geïnteresseerd is in een specifieke SaaS-service, kan de gebruiker zich abonneren via Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Wat betekent het dat een Azure-gebruiker zich abonneert op een SaaS-aanbieding in Azure Marketplace?

Dit betekent dat een gebruiker de gebruiksvoorwaarden en privacyverklaring kan bekijken die zijn gekoppeld aan de SaaS-service, en ermee kan instemmen om ervoor te betalen volgens de factureringsvoorwaarden die door u, de uitgever van de SaaS-aanbieding, op de factuur van Microsoft zijn ingesteld. Gebruikers kunnen hun bestaande betalingsprofiel in Azure gebruiken om te betalen voor het SaaS-serviceverbruik.

Dit is gunstig voor een heleboel redenen. Klanten kunnen nu op één plek ontdekken en zich abonneren met Microsoft Cloud Platform als vertrouwde bron, zonder dat ze elke ISV-software die het wil gebruiken, hoeven door te lichten. Ook kunnen klanten hun bestaande betalingsprofiel gebruiken zonder dat ze elke ISV-software expliciet onafhankelijk hoeven te betalen.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Wordt de gebruiker automatisch in rekening gebracht wanneer de aanbieding is geabonneerd?

Tijdens een abonnement op het SaaS-aanbod heeft de gebruiker ermee ingestemd om te betalen voor het verbruik van de SaaS-service via het Microsoft-platform. De kosten worden echter pas in rekening gebracht wanneer de aanbieding wordt verbruikt. De gebruiker moet naar uw SaaS-aanbieding gaan en het maken van een account bevestigen om de aanbieding te kunnen verwerken. U stelt Microsoft vervolgens op de hoogte om te beginnen met factureren voor dit SaaS-abonnement van de klant.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hoe wordt u op de hoogte gesteld wanneer een gebruiker zich abonneert op uw SaaS-aanbieding?

Nadat u zich heeft geabonneerd op een aanbieding, kan de Azure-gebruiker al zijn aanbiedingen in Azure ontdekken en beheren. Standaard is de status van een nieuw geabonneerd SaaS-aanbod **'Provisioning, fulfillment pending'.** In deze status wordt de Azure-gebruiker gevraagd met een actie om **'Account configureren'** te maken om te bladeren naar hun SaaS-abonnementsbeheerervaring in de Azure-portal.

Wanneer de gebruiker op **'Account configureren'** klikt, worden ze doorgestuurd naar de SaaS-servicewebsite. De URL waarnaar ze worden genavigeerd, wordt door de uitgever verstrekt op het moment van publicatie van de aanbieding. Deze pagina wordt de bestemmingspagina van de uitgever genoemd. Azure-gebruikers moeten kunnen inloggen op de SaaS-bestemmingspagina op basis van hun bestaande AAD-referenties in Azure.

Wanneer de Azure-gebruiker wordt doorgestuurd naar de bestemmingspagina, wordt een token toegevoegd aan de query-URL. Dit token is van korte duur en geldig voor een tijdsduur van 24 uur. U vervolgens de aanwezigheid van dit token detecteren en de API van Microsoft aanroepen om meer context aan het token te krijgen.

![Abonnementsstroom klant](media/saas-metering-service-integration-flow-a.png)

Zie het [SaaS fulfillment API-document](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) voor meer informatie over het API-contract voor het afhandelen van transacties in de levenscyclus van een SaaS-aanbieding.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Hoe kent u het SaaS-aanbod waarop de gebruiker zich abonneert in Azure?

Het antwoord `Resolve` op de API bevat de aanbiedings- en planinformatie die is gekoppeld aan het SaaS-abonnement.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hoe kan de Azure-gebruiker het abonnement wijzigen dat is gekoppeld aan dit Azure-abonnement?

* De Azure-gebruiker kan het abonnement dat is gekoppeld aan het SaaS-abonnement rechtstreeks wijzigen in de SaaS-ervaring of via het Microsoft-platform.

* Conversies kunnen op elk moment in de factureringscyclus worden uitgevoerd. U moet elke conversie erkennen, die van kracht zal worden zodra erkend.

* Pre-paid plan **(maandelijks of** **jaarlijks**) tarieven zijn pro-rated. Eventuele overschrijdingen die tot het tijdstip van conversie worden uitgestoten, worden in de volgende factuur in rekening gebracht. Op basis van het nieuwe plan zullen nieuwe overschrijdingen worden uitgestoten.

>[!Note]
>U downgrades blokkeren als u geen specifieke conversiepaden wilt ondersteunen.

De onderstaande reeks legt de stroom vast wanneer een Azure-klant van plan verandert in de SaaS-ervaring:

![De veranderingsstroom van het klantplan](media/saas-metering-service-integration-flow-b.png)

De onderstaande reeks legt de stroom vast wanneer een Azure-klant van plan verandert in de storefront van Microsoft

![De veranderingsstroom van de winkelwinkel van de klant](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hoe kan de Azure-gebruiker zich afmelden voor het abonnement dat is gekoppeld aan een Azure-abonnement?

Een Azure-gebruiker kan zich afmelden voor een gekochte SaaS-aanbieding, rechtstreeks in de SaaS-ervaring of via het Microsoft-platform. Zodra de gebruiker zich afmeldt, worden deze niet meer in rekening gebracht vanaf de volgende factureringscyclus.

De onderstaande reeks legt de stroom vast wanneer een Azure-klant zich afmeldt voor de SaaS-aanbieding in de SaaS-ervaring:

![Klant meldt zich af in de SaaS-ervaring](media/saas-metering-service-integration-flow-d.png)

De onderstaande reeks legt de stroom vast wanneer Azure-gebruiker zich afmeldt in de storefront van Microsoft:

![Klant meldt zich af in de etalage van Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [API's voor marketplace-meetservice voor](./marketplace-metering-service-apis.md) meer informatie.
