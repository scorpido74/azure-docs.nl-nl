---
title: Loodbeheer voor commerciële marktplaatsen | Azure Marketplace en AppSource
description: Een overzicht van verschillende onderwerpen met betrekking tot het publiceren van aanbiedingen en technische artefacten naar de Azure Marketplace en AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383783"
---
# <a name="lead-management-for-commercial-marketplace"></a>Lead management voor commerciële marktplaats


Klanten zijn het centrum van een goede business. Bij de transformatie van de huidige productacquisities moeten marketeers zich richten op het rechtstreeks verbinden met klanten en het opbouwen van een relatie. Daarom is het genereren van leads van hoge kwaliteit een essentieel hulpmiddel voor uw verkoopcyclus. Na het aanbieden van uw aanbieding in [partnercentrum](https://partner.microsoft.com/), zijn er tools ingeschakeld voor u om programmatisch klantcontactgegevens te ontvangen onmiddellijk nadat een klant interesse heeft getoond of uw product op de markt implementeert. 



## <a name="what-are-leads-in-the-marketplace"></a>Wat zijn leads in de markt?

De leads zijn van klanten die geïnteresseerd zijn of uw producten implementeren via de Marketplace. Of uw product nu wordt vermeld op Azure Marketplace of AppSource, u leads van klanten ontvangen zodra het goed is ingesteld van uw CRM naar uw vermelding(en) in partnercentrum. 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Uw CRM-systeem verbinden met Partner Center

Om leads te krijgen, is leadmanagementconnector in partnercentrum zo ontworpen dat deze eenvoudig kan worden aangesloten met uw CRM-gegevens op een lijst met beschikbare CRM-systemen. Nu u eenvoudig gebruik maken van de leads gegenereerd door de markt zonder een aanzienlijke technische inspanning om te integreren met een extern systeem.

Hier volgen stapsgewijze instructies voor het verbinden van elk van de mogelijke leadbestemmingen:

**Dynamics CRM Online** - Zie [leadbeheer configureren voor Dynamics 365 voor klantbetrokkenheid](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) voor instructies over het configureren van Dynamics CRM Online voor het verkrijgen van leads.

**Marketo** - Raadpleeg [leadbeheer configureren in Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) voor instructies voor het instellen van Marketo Lead Configuration om leads te genereren.

**Salesforce** - Raadpleeg [het configureren van leadbeheer voor Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) voor instructies voor het instellen van uw Salesforce-exemplaar om leads te genereren.

**Azure Table** - Bekijk [leadbeheer configureren met behulp van een Azure-tabel](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) voor instructies voor het instellen van uw Azure-opslagaccount voor het ophalen van leads in een Azure-tabel.

**Https-eindpunt** - Bekijk [leadbeheer configureren met behulp van een HTTPS-eindpunt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) voor instructies voor het instellen van uw Https-eindpunt om leads te genereren.

Zodra u uw leadbestemming goed hebt geconfigureerd en op Publicatie op uw aanbieding hebt geraken, valideren we de verbinding en sturen we u een testlead. Wanneer u het aanbod bekijkt voordat u live gaat, u ook uw leadverbinding testen door te proberen het aanbod zelf te verkrijgen in de preview-omgeving. Het is belangrijk om ervoor te zorgen dat uw leadinstellingen up-to-date blijven, zodat u geen leads verliest, dus zorg ervoor dat u deze verbindingen bijwerkt wanneer er iets is veranderd aan uw kant.

### <a name="what-are-the-next-steps"></a>Wat zijn de volgende stappen?

Zodra de technische installatie is ingevoerd, moet u deze leads opnemen in uw huidige verkoop & marketingstrategie en operationele processen. We zijn geïnteresseerd in een beter begrip van uw totale verkoopproces en willen nauw met u samenwerken bij het leveren van hoogwaardige leads en voldoende gegevens om u succesvol te maken. We zijn blij met uw feedback over hoe we de leads die we u sturen kunnen optimaliseren en verbeteren met aanvullende gegevens om deze klanten succesvol te maken. Laat het ons weten als je geïnteresseerd bent in het geven van feedback en suggesties om je verkoopteam succesvoller te laten zijn met Marketplace Leads.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>Veelvoorkomende fouten in de leadconfiguratie tijdens het publiceren in Partnercenter

**Kon de lead niet opslaan in Dynamics CRM. Controleer de instellingen van het Dynamics CRM-account. LastCRMError: Kan zich niet aanmelden bij Dynamics CRM, LastCRMException:** 

> Als O365-verificatie is geselecteerd, controleert u of het gebruikersaccount en het wachtwoord geldig zijn. Als AAD is geselecteerd, controleert u of de tenant-id, toepassings-id en toepassingsgeheime sleutel overeenkomen met wat er op AAD is ingesteld. Volg [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)de instructies . Als de gebruikersnaam/het wachtwoord van het account geldig is, moet u ervoor zorgen dat het toegang heeft tot Dynamics 365 en een licentie heeft toegewezen (stappen 11-15 als u Azure Active Directory of beveiligingsinstellingen gebruikt als u een Office-gebruiker gebruikt). 

**Kon de lead niet opslaan in Dynamics CRM. Gebruiker heeft geen machtigingen voor het kenmerk leadsourcecode in de leadentiteit** 

> De toepassing/gebruiker mist beveiligingsrollen(en) naar Microsoft Marketplace-leadwriter. Volg stappen 11-15 als u Azure Active Directory of beveiligingsinstellingen gebruikt als u hier een Office-gebruiker [gebruikt.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Kon de lead niet opslaan voor Dynamics CRM met behulp van AAD. Uitzondering:: Huurder niet gevonden. Dit exemplaar kan gebeuren als er geen actieve abonnementen voor de tenant zijn.**  

> De directory-id die in de sectie leadbeheer wordt opgegeven, is geen geldige map. Ontvang de Directory Id op basis van de instructies in stap 2 (onder Azure Active Directory, vanaf [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Kon de lead niet opslaan in Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser is mislukt - er zijn geen rollen toegewezen aan de gebruiker.**  

> Oplossing: Beveiligingsrol toewijzen aan Microsoft Marketplace-hoofdschrijver. Volg [de](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) instructies hier onder Beveiligingsinstellingen.

**Kon de lead niet opslaan voor Dynamics CRM met behulp van AAD. Uitzondering:: Toepassing met id is niet gevonden in de map** 

> De toepassings-id die in de sectie leadbeheer wordt opgegeven, is geen geldige map. Ontvang de Directory Id op basis van de instructies in stap 8 (onder Azure Active Directory, vanaf [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Kon de lead niet opslaan voor Dynamics CRM met behulp van AAD. Uitzondering:: Aangevraagde tenant-id is niet geldig en niet geldig externe domeinindeling** 

> De directory-id die in de sectie leadbeheer wordt opgegeven, is geen geldige map. Ontvang de Directory Id op basis van de instructies in stap 2 (onder Azure Active Directory, vanaf [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Kon de lead niet opslaan voor Dynamics CRM met behulp van AAD. Uitzondering:: Fout bij het valideren van referenties.: Ongeldig clientgeheim wordt verstrekt.** 

> Oplossing: Meld u aan bij de Azure-portal en controleer of de toepassingssleutel overeenkomt met wat zich in het Partnercentrum bevindt. Genereer wachtwoord op basis van de instructie bij Stap 10 (onder Azure Active Directory), vanaf [hier).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Kon de lead niet opslaan in Dynamics CRM. LastCRMError: Het aanvraagkanaal heeft een time-out opgetreden tijdens het wachten op een antwoord na 00:02:00. Verhoog de time-outwaarde die wordt doorgegeven aan de aanroep naar Aanvraag of verhoog de verzendtijdwaarde op de binding. De tijd die aan deze bewerking is toegewezen, kan een deel van een langere time-out zijn geweest.**  

> Oplossing: Meld u aan bij Partnercentrum, controleer storefront-gegevens >> bestemming voor het leiden >> URL, controleer of het een geldig Dynamic CRM-exemplaar is.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Wat zijn leads en waarom zijn ze belangrijk voor mij als uitgever op Marketplace?** 

Leads zijn klanten die uw producten implementeren via de Marketplace. Of uw product nu wordt vermeld op [Azure Marketplace](https://azuremarketplace.microsoft.com) of [AppSource,](https://appsource.microsoft.com/)u leads ontvangen van klanten die geïnteresseerd zijn in uw product als u de hoofdbestemming in uw aanbieding hebt ingesteld.  

**Waar kan ik hulp krijgen bij het instellen van mijn hoofdbestemming?** 

U vindt hier documentatie: [Ontvang klantleads](./partner-center-portal/commercial-marketplace-get-customer-leads.md) of verzend een ondersteuningsticket via aka.ms/marketplacepublishersupport selecteer aanbiedingstype en leadbeheer. 

**Moet ik een leadbestemming configureren om een aanbieding op Marketplace te publiceren?**

Ja, als u een Contact Me SaaS-app of Consulting Services publiceert.  
 
**Hoe kan ik bevestigen dat de leadconfiguratie correct is?**

Nadat u uw aanbieding hebt ingesteld en de bestemming van het hoofd hebt geleid, publiceert u uw aanbieding. Bij de stap leadvalidatie stuurt Marketplace een testlead naar de hoofdbestemming die in uw aanbieding is geconfigureerd. 

**Hoe kan ik de testlead vinden?**

Zoek naar 'MSFT_TEST' in uw hoofdbestemming, hier is een voorbeeld test lead gegevens: 

onderneming = MSFT_TEST_636573304831318844 

land = VS 

description = MSFT_TEST_636573304831318844 

e-mail =MSFT_TEST_636573304831318844@test.com

codering = UTF-8 

codering = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844| \<Aanbiedingsnaam> 

oid = 00Do0000000ZHog 

telefoon = 1234567890 

titel = MSFT_TEST_636573304831318844 

**Ik heb een live aanbod, maar ik zie geen aanwijzingen?**

Elke lead heeft gegevens doorgegeven in velden in uw geselecteerde leadbestemming, de leads komen in deze indeling: **Bron-Actie| Aanbieding** 

  *Bronnen:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Acties:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Biedt:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Hier vindt u voorbeeldgegevens van de klantgegevens*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Lees meer onder [Lead Info](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Ik heb Azure BLOB geconfigureerd als mijn hoofdbestemming, waarom zie ik de lead niet?** 

De lead wordt alleen geschreven wanneer u Azure BLOB-opslag als hoofdbestemming selecteert. Schakel over naar azure-tabel om de lead realtime te ontvangen.

**Ik heb een e-mail ontvangen van Marketplace, waarom kan ik de lead niet vinden in mijn CRM?**  

Het is mogelijk dat het e-maildomein van de eindgebruiker van .edu is. Om privacyredenen geven we geen persoonlijke identificeerbare gegevens door van .edu-domein. Stuur een [ondersteuningsticket](https://aka.ms/marketplacepublishersupport)in.

**Ik heb Azure Table/Azure BLOB geconfigureerd als mijn hoofdbestemming, hoe kan ik de leads bekijken?** 

U hebt toegang tot de blob of tabel vanuit azure portal of u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratis downloaden en installeren om de tabellen/blobs van uw Azure-opslagaccount te bekijken. 

**Ik heb Azure Table geconfigureerd als mijn hoofdbestemming, kan ik een melding krijgen wanneer een nieuwe lead wordt verzonden door Marketplace?** 

Ja, volg [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)de instructies voor het instellen van Azure Table + Function on the documentation. 

**Ik heb Salesforce geconfigureerd als mijn hoofdbestemming, waarom kan ik de leads niet vinden?** 

Controleer of het web-naar-leadformulier een verplicht veld is op basis van een picklist. Zo ja, schakel het veld over naar een niet-verplicht tekstveld.  
 
**Er was een probleem met mijn hoofdbestemming, en ik miste enkele aanwijzingen. Kan ik ze in een e-mail naar mij laten sturen?** 

Vanwege het privacybeleid kunnen we geen leadinformatie delen via onbeveiligde e-mail. 

**Ik heb Azure Storage (BLOB/Table) geconfigureerd als mijn hoofdbestemming, hoeveel kost dit?** 

Lead gen-gegevens zijn laag (<1 GB voor bijna alle uitgevers). De kosten zijn afhankelijk van het aantal ontvangen leads, als 1.000 leads worden ontvangen in een maand, kost het ongeveer 50 cent. 
