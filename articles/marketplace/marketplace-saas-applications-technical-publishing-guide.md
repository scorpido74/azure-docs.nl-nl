---
title: Publicatiegids voor Azure Marketplace SaaS-toepassingen
description: Stapsgewijze handleiding en publicatiechecklists voor het publiceren van SaaS-toepassingen naar Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 1bc2feb4d8f3f103802bd823c66cd3f54ac5d74a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687545"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS-toepassingen bieden publicatiegids

SaaS-toepassingen kunnen worden gepubliceerd in de markt met drie verschillende oproepen tot actie: "Neem contact met mij op", "Probeer het nu", en "Nu krijgen." Deze gids legt deze drie opties uit, inclusief vereisten voor elk. 

## <a name="offer-overview"></a>Aanbiedingsoverzicht  

SaaS-toepassingen zijn beschikbaar in beide Azure Storefronts In de volgende tabel worden de huidige beschikbare opties beschreven:

| Storefront, optie | Aanbieding | Proef/Transact |  
| --- | --- | --- |  
| AppSource | Ja | Ja |
| Azure-marktplaats | Nee | Ja |   

**Lijst:**  De optie Voor het publiceren van aanbiedingen bestaat uit een aanbiedingstype Contact mij en wordt gebruikt wanneer deelname op proef- of transactieniveau niet haalbaar is. Het voordeel van deze aanpak is dat het uitgevers met een oplossing in de markt in staat stelt om onmiddellijk leads te ontvangen die kunnen worden omgezet in deals om uw bedrijf te vergroten.  
**Proef/transactie:**  De klant heeft de mogelijkheid om direct een proefversie voor uw oplossing te kopen of aan te vragen. Het bieden van een proefervaring verhoogt het betrokkenheidsniveau dat aan klanten wordt aangeboden en stelt klanten in staat om uw oplossing te verkennen voordat ze kopen. Met een Trial-ervaring hebt u betere kansen op promotie in de etalages en u meer en rijkere leads verwachten van klantinteracties. Proeven moeten gratis ondersteuning bevatten, ten minste voor de duur van de proefperiode.  

| SaaS Apps Aanbieding | Zakelijke vereisten | Technische voorschriften |  
| --- | --- | --- |  
| **Neem contact met ons op** | Ja | Nee |  
| **PowerBI / Dynamiek** | Ja | Ja (Azure AD-integratie) |  
| **SaaS-apps**| Ja | Ja (Azure AD-integratie) |     

## <a name="saas-list"></a>SaaS-lijst

De call-to-action voor een SaaS-vermelding zonder proefversie en zonder factureringsfunctionaliteit is 'Neem contact met mij op'. 

U hoeft Azure Active Directory niet te configureren om een SaaS-toepassing weer te geven. 

|Vereisten  |Details  |
|---------|---------|
|Uw app is een SaaS-aanbod  |   Uw oplossing is een SaaS-aanbod en u biedt een multitenant SaaS-product aan.      |


## <a name="saas-trial"></a>SaaS-proces

U biedt een oplossing of app met behulp van een free-to-try, software-as-a-service (SaaS)-gebaseerde trial. Gratis proefaanbiedingen kunnen worden gepresenteerd als een proefaccount met beperkte of beperkte duur. 


|Vereisten  |Details  |
|---------|---------|
|Uw app is een SaaS-aanbod  |   Uw oplossing is een SaaS-aanbod en u biedt een multitenant SaaS-product aan.      |
|Uw app is AAD ingeschakeld     |   De klant wordt doorverwezen naar uw domein en u handelt rechtstreeks met de klant       |


## <a name="saas-trial-technical-requirements"></a>Technische vereisten voor SaaS-proef

De technische vereisten voor SaaS-toepassingen zijn eenvoudig. Uitgevers hoeven alleen te worden geïntegreerd met Azure Active Directory (Azure AD) om te worden gepubliceerd. Azure AD-integratie met toepassingen is goed gedocumenteerd en Microsoft biedt meerdere SDK's en resources om dit te bereiken.  

Om te beginnen raden we u aan een abonnement te hebben dat is toegewezen aan uw Azure Marketplace-publicatie, zodat u het werk isoleren van andere initiatieven. Zodra dit is gebeurd, u beginnen met het implementeren van uw SaaS-toepassing in dit abonnement om de ontwikkelingswerkzaamheden te starten.  

De beste Azure Active Directory-documentatie, -voorbeelden en -richtlijnen bevinden zich op de volgende sites: 

* [Handleiding voor Azure Active Directory-ontwikkelaars](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Toepassingen integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure Roadmap - Beveiliging en identiteit](https://azure.microsoft.com/roadmap/?category=security-identity)

Bekijk het volgende voor video-zelfstudies:

* [Azure Active Directory-verificatie met Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Technische briefing van Azure Active Directory - Deel 1 van 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Technische briefing van Azure Active Directory - Deel 2 van 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Apps bouwen met Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure-video's gericht op Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Gratis Azure Active Directory-training is beschikbaar op  
* [Microsoft Azure voor IT-pro's Content Series: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Bovendien biedt Azure Active Directory een site om te controleren op service-updates   
* [Azure AD-service-updates](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory gebruiken om proefversies in te schakelen  

Microsoft verifieert alle Marketplace-gebruikers met Azure AD, dus wanneer een geverifieerde gebruiker door uw proefvermelding in Marketplace klikt en wordt doorgestuurd naar uw proefomgeving, u de gebruiker rechtstreeks in een proefversie opnemen zonder dat u een extra aanmeldingsstap hoeft te nemen. Het token dat uw app ontvangt van Azure AD tijdens verificatie bevat waardevolle gebruikersgegevens die u gebruiken om een gebruikersaccount in uw app te maken, zodat u de inrichtingservaring automatiseren en de kans op conversie vergroten. Zie [Voorbeeldtokens voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) meer informatie over het token.

Als u Azure AD gebruikt om verificatie met één klik in te schakelen voor uw app of proefversie, gaat u als volgt te werk:  
* Stroomlijnt de klantervaring van Marketplace naar Trial.  
* Behoudt het gevoel van een 'in-product-ervaring', zelfs wanneer de gebruiker wordt doorgestuurd van Marketplace naar uw domein of trial-omgeving.  
* Hiermee neemt u de kans op stopzetting bij omleiding af omdat er geen extra aanmeldingsstap is.  
* Vermindert implementatiebarrières voor de grote populatie Azure AD-gebruikers.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Uw Azure AD-integratie certificeren voor Marketplace  

Certificeert uw Azure AD-integratie op een aantal verschillende manieren, afhankelijk van of uw toepassing één-tenant of multi-tenant is en of u nieuw bent bij Azure AD federated single sign-on (SSO), of deze al ondersteunt.  

**Voor toepassingen met meerdere tenant's:**  

Als u Azure AD al ondersteunt, gaat u als volgt te werk:
1.    Uw toepassing registreren in de Azure-portal
2.    Schakel de multi-huurondersteuningsfunctie in Azure AD in om een proefervaring met één klik te krijgen. Meer specifieke informatie is [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)te vinden.  

Als u nieuw bent bij Azure AD Federated SSO, gaat u als volgt te werk: 
1.  Uw toepassing registreren in de Azure-portal
2.  SSO ontwikkelen met Azure AD met [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) of [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Multi-tenancy support-functie in AAD inschakelen om 'one-click' trial experience te krijgen Meer specifieke informatie vindt u [hier.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)  

**Voor toepassing met één tenant gebruikt u een van de volgende opties:**  
* Gebruikers toevoegen aan uw directory als gastgebruikers met [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Handmatig proeven voor klanten inrichten met 'Contact mij'
* Ontwikkel een 'Test Drive' per klant
* Een demo-app met meerdere tenant's bouwen met SSO

## <a name="saas-subscriptions"></a>SaaS-abonnementen

Gebruik het aanbiedingstype van de SaaS-app om uw klant in staat te stellen uw saas-gebaseerde, technische oplossing als abonnement te kopen. Aan de volgende vereisten voor uw SaaS-app moet worden voldaan:
- Prijs en facturer en de service op een vast (maandelijks of jaarlijks), of tegen een tarief per gebruiker.
- Geef een methode op om de service op elk gewenst moment te upgraden of te annuleren.
Microsoft host de handelstransactie. Microsoft factureert uw klant namens u. Als u een SaaS-app als abonnement wilt aanbieden, moet u integreren met de SaaS-fulfillment-API's.  Uw service moet voorzieningen, upgraden en annuleren ondersteunen.

| Vereiste | Details |  
|:--- |:--- |  
|Facturering en meting | Uw aanbieding wordt geprijsd op basis van het prijsmodel dat u selecteert voordat u publiceert (vast tarief of per gebruiker).  Als u het forfaitaire model gebruikt, u optioneel extra afmetingen toevoegen die worden gebruikt om klanten in rekening te brengen voor het gebruik dat niet in het vaste tarief is inbegrepen. |  
|Opzegging | Uw aanbieding kan te koop worden op elk gewenst moment door de klant geannuleerd. |  
|Bestemmingspagina voor transacties | U host een bestemmingspagina voor transacties van Azure voor co-branded transacties waar gebruikers hun SaaS-serviceaccount kunnen maken en beheren. |   
| Abonnement-API | U stelt een service bloot die kan communiceren met het SaaS-abonnement om een gebruikersaccount en serviceplan te maken, bij te werken en te verwijderen. Kritieke API-wijzigingen moeten binnen 24 uur worden ondersteund. Niet-kritieke API-wijzigingen worden periodiek vrijgegeven. |  

>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](./cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

## <a name="next-steps"></a>Volgende stappen
Als je dat nog niet gedaan hebt,

* [Meer informatie](https://azuremarketplace.microsoft.com/sell) over de marktplaats.

Als u zich wilt registreren in partnercentrum, begint u met het maken van een nieuwe aanbieding of werkt u aan een bestaand aanbieding:

* [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
* Zie [een SaaS-applicatieaanbieding maken](./partner-center-portal/create-new-saas-offer.md) voor meer informatie.
