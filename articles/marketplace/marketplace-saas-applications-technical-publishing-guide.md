---
title: Publicatie handleiding voor SaaS-toepassingen-micro soft Commercial Marketplace
description: Vereisten en bronnen voor het publiceren van SaaS-toepassingen die worden aangeboden aan Microsoft AppSource en Azure Marketplace.
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: c19799265679eeead96bf95943f274aa32c75ff2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121568"
---
# <a name="saas-applications-offer-publishing-guide"></a>Publicatiehandleiding voor SaaS-toepassingen

U kunt SaaS-toepassingen in de commerciële Marketplace publiceren met drie verschillende aanroepen naar actie: ' contact opnemen ', ' nu proberen ' en ' nu downloaden '. In dit artikel worden deze drie opties beschreven, met inbegrip van de vereisten voor elk. 

## <a name="offer-overview"></a>Overzicht van aanbieding  

SaaS-toepassingen zijn beschikbaar in Microsoft AppSource en Azure Marketplace.  Zowel de lijst met ondersteunings aanbiedingen van de winkel, de proef versie en het trans acties.

**Lijst:**  De optie voor het publiceren van aanbiedingen bestaat uit een aanbiedings type voor contact personen en wordt gebruikt wanneer een proef abonnement of een deelname op transactie niveau niet haalbaar is. Het voor deel van deze benadering is dat uitgevers met een oplossing in de markt direct leads ontvangen die kunnen worden omgezet in deals om uw bedrijf te verg Roten.  
**Proef versie/trans actie:**  De klant heeft de mogelijkheid om rechtstreeks een proef versie voor uw oplossing te kopen of aan te vragen. Het bieden van een proef ervaring verhoogt het engagement niveau voor klanten en stelt klanten in staat om uw oplossing te verkennen voordat u deze koopt. Als u een proef ervaring hebt, hebt u een betere kans op promotie in de-winkel en verwacht u meer en uitgebreidere leads van klant afspraken. Tests moeten ten minste voor de duur van de proef periode gratis ondersteuning bevatten.  

| SaaS-apps bieden | Zakelijke vereisten | Technische vereisten |  
| --- | --- | --- |  
| **Contact opnemen** | Ja | Nee |  
| **Power BI/Dynamics** | Yes | Ja (Azure AD-integratie) |  
| **SaaS-apps**| Yes | Ja (Azure AD-integratie) |     

## <a name="saas-list"></a>SaaS-lijst

De aanroep van actie voor een SaaS-vermelding zonder proef versie en geen facturerings functionaliteit is ' Neem contact met mij op '. 

U hoeft Azure Active Directory niet te configureren om een SaaS-toepassing weer te geven. 

|Vereisten  |Details  |
|---------|---------|
|Uw app is een SaaS-aanbieding  |   Uw oplossing is een SaaS-aanbieding en u kunt een multi tenant-SaaS-product aanbieden.      |


## <a name="saas-trial"></a>SaaS-proef versie

U geeft een oplossing of app op met behulp van een gratis, op SaaS (Software as a Service) gebaseerde proef versie. Aanbiedingen voor een gratis proef versie kunnen worden weer gegeven als een account met een beperkt gebruik of een proef abonnement met een beperkte geldigheids duur. 


|Vereisten  |Details  |
|---------|---------|
|Uw app is een SaaS-aanbieding  |   Uw oplossing is een SaaS-aanbieding en u kunt een multi tenant-SaaS-product aanbieden.      |
|Voor uw app is AAD ingeschakeld     |   De klant wordt opnieuw omgeleid naar uw domein en u gaat rechtstreeks met de klant handelen       |


## <a name="saas-trial-technical-requirements"></a>Technische vereisten voor SaaS-proef versie

De technische vereisten voor SaaS-toepassingen zijn eenvoudig. Uitgevers moeten alleen worden geïntegreerd met Azure Active Directory (Azure AD) om te worden gepubliceerd. Azure AD-integratie met toepassingen is goed gedocumenteerd en micro soft biedt meerdere Sdk's en bronnen om dit te bereiken.  

We raden u aan om te beginnen een abonnement dat speciaal is afgestemd op uw Azure Marketplace-publicatie, zodat u het werk kunt isoleren van andere initiatieven. Zodra u dit hebt gedaan, kunt u beginnen met het implementeren van uw SaaS-toepassing in dit abonnement om het ontwikkelende werk te starten.  

De beste Azure Active Directory documentatie, voor beelden en richt lijnen bevinden zich op de volgende sites: 

* [Ontwikkelaars handleiding Azure Active Directory](../active-directory/develop/index.yml)

* [Integreren met Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md)

* [Azure-route kaart: beveiliging en identiteit](https://azure.microsoft.com/roadmap/?category=security-identity)

Raadpleeg het volgende voor video zelf studies:

* [Verificatie Azure Active Directory met Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Technische informatie over Azure Active Directory Identity-deel 1 van 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Technische informatie over Azure Active Directory Identity-deel 2 van 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Apps bouwen met Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure Video's gericht op Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Gratis Azure Active Directory training is beschikbaar op  
* [Microsoft Azure voor IT-professionals: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Daarnaast biedt Azure Active Directory een site om te controleren op service-updates   
* [Azure AD-service-updates](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory gebruiken om experimenten in te scha kelen  

Micro soft verifieert alle Marketplace-gebruikers met Azure AD, dus wanneer een geverifieerde gebruiker door de proef versie in Marketplace klikt en wordt omgeleid naar uw proef omgeving, kunt u de gebruiker rechtstreeks in een proef versie inrichten zonder dat hiervoor een extra aanmeldings stap nodig is. Het token dat uw app ontvangt van Azure AD tijdens de verificatie, bevat waardevolle gebruikers gegevens die u kunt gebruiken om een gebruikers account in uw app te maken, waardoor u de inrichtings ervaring automatiseert en de kans op conversie verhoogt. Zie [voorbeeld tokens](../active-directory/develop/active-directory-token-and-claims.md) voor meer informatie over het token.

Als u Azure AD gebruikt voor het inschakelen van 1-klikken verificatie voor uw app of proef versie, doet u het volgende:  
* Stroomlijnt de klant ervaring van Marketplace tot proef versie.  
* Onderhoudt het gevoel van een ' in-product ', zelfs wanneer de gebruiker wordt omgeleid van Marketplace naar uw domein of test omgeving.  
* Hiermee verkleint u de kans op het omleiden van de omleiding omdat er geen extra aanmeldings stap is.  
* Vermindert implementatie barrières voor de grote populatie van Azure AD-gebruikers.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Uw Azure AD-integratie voor Marketplace certificeren  

U kunt uw Azure AD-integratie op verschillende manieren certificeren, afhankelijk van of uw toepassing een single Tenant of meerdere tenants is, en of u geen ervaring hebt met Azure AD federatieve eenmalige aanmelding (SSO) of al hebt ondersteund.  

**Voor multi tenant-toepassingen:**  

Als u al Azure AD ondersteunt, doet u het volgende:
1.    Registreer uw toepassing in de Azure Portal
2.    Schakel de ondersteunings functie voor multitenancy in azure AD in om een proef ervaring met één klik te krijgen. Meer informatie vindt u [hier](../active-directory/develop/active-directory-integrating-applications.md).  

Als u geen ervaring hebt met Azure AD Federated SSO, doet u het volgende: 
1.  Registreer uw toepassing in de Azure Portal
2.  Ontwikkel SSO met Azure AD met behulp van [OpenID Connect Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) of [OAuth 2,0](../active-directory/develop/active-directory-protocols-oauth-code.md).
3.  Schakel de ondersteunings functie voor meerdere multitenancy in AAD in om meer specifieke informatie te verkrijgen met een proef versie met één [klik.](../active-directory/develop/active-directory-devhowto-appsource-certified.md)  

**Gebruik voor toepassing met één Tenant een van de volgende opties:**  
* Gebruikers toevoegen aan uw directory als gast gebruikers met behulp van [Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)
* Hand matig proef versies inrichten voor klanten met behulp van de contact persoon
* Een test station per klant ontwikkelen
* Een voor beeld-demo-app met meerdere tenants bouwen met SSO

## <a name="saas-subscriptions"></a>SaaS-abonnementen

Gebruik het type SaaS-app-aanbieding om uw klant in staat te stellen uw SaaS-gebaseerde technische oplossing als een abonnement te kopen. Voor uw SaaS-app moet aan de volgende vereisten worden voldaan:
- Prijs en factureer de service op een vlak (maandelijks of jaarlijks) of op tarieven per gebruiker.
- Bieden een methode om de service op elk gewenst moment te upgraden of te annuleren.
Micro soft fungeert als host voor de commerce-trans actie. Micro soft factureert namens u uw klant. Als u een SaaS-app als een abonnement wilt aanbieden, moet u integreren met de SaaS-fulfillment-Api's.  Uw service moet ondersteuning bieden voor het inrichten, upgraden en annuleren.

| Vereiste | Details |  
|:--- |:--- |  
|Facturering en meting | Uw aanbieding is geprijsd op basis van het prijs model dat u hebt geselecteerd voor publicatie (vast tarief of per gebruiker).  Als u gebruikmaakt van het model voor vaste kosten, kunt u eventueel extra dimensies opnemen die worden gebruikt om klanten in rekening te brengen voor gebruik dat niet in het vast tarief is opgenomen. |  
|Opzegging | Uw aanbieding wordt op elk gewenst moment geannuleerd door de klant. |  
|Pagina transactie overloop | U host een Azure-landings pagina voor co-branding, waar gebruikers hun SaaS-service account kunnen maken en beheren. |   
| API voor abonnementen | U maakt een service beschikbaar die kan communiceren met het SaaS-abonnement om een gebruikers account en een service plan te maken, bij te werken en te verwijderen. Essentiële wijzigingen in de API moeten binnen 24 uur worden ondersteund. Wijzigingen van niet-kritieke API'S worden periodiek vrijgegeven. |  

>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](./cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen.

## <a name="next-steps"></a>Volgende stappen
Als u dit nog niet hebt gedaan,

* [Meer informatie](https://azuremarketplace.microsoft.com/sell) over Marketplace.

Als u zich wilt registreren in het partner centrum, begint u met het maken van een nieuwe aanbieding of het werken met een bestaand abonnement:

* [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
* Zie [een SaaS-toepassings aanbieding maken](./partner-center-portal/create-new-saas-offer.md) voor meer informatie.
