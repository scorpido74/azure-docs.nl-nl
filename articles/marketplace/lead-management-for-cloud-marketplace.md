---
title: Lead beheer voor Cloud Marketplace | Azure Marketplace en AppSource
description: Een overzicht van de verschillende onderwerpen met betrekking tot het publiceren van aanbiedingen en technische artefacten naar Azure Marketplace en AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/05/2018
ms.author: pabutler
ms.openlocfilehash: 047ffd983cecd9f9bd971ee3f97e15734d14ce66
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813281"
---
# <a name="lead-management-for-cloud-marketplace"></a>Lead beheer voor Cloud Marketplace


Klanten zijn het midden van een goed bedrijf. Bij de trans formatie van de huidige product verwervingen, moeten de markt verwerkers zich richten op het rechtstreeks verbinden met klanten en het bouwen van een relatie. Daarom is het genereren van leads van hoge kwaliteit een essentieel hulp middel voor uw verkoop cyclus. Nadat u uw aanbieding in de [Cloud Partner-Portal](https://cloudpartner.azure.com/)hebt opgenomen, zijn er hulpprogram ma's ingeschakeld waarmee u op een programmatische manier contact gegevens van klanten kunt ontvangen nadat een klant rente heeft ingecheckt of uw product in Marketplace heeft geïmplementeerd. 



## <a name="what-are-leads-in-the-marketplace"></a>Wat zijn leads in de Marketplace?

De leads zijn afkomstig van klanten die geïnteresseerd zijn of uw producten implementeren via Marketplace. Of uw product wordt vermeld op Azure Marketplace of AppSource, u kunt leads van klanten ontvangen zodra het correct is ingesteld vanuit uw CRM naar uw aanbieding (en) in Cloud Partner-portal 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Verbinding maken tussen uw CRM-systeem en de Cloud Partner Portal

Als u leads wilt ophalen, is lead management connector op de Cloud Partner-portal zo ontworpen dat deze eenvoudig met uw CRM-gegevens kan worden aangesloten op een lijst met beschik bare CRM-systemen. U kunt nu eenvoudig gebruikmaken van de leads die door Marketplace worden gegenereerd zonder een belang rijke engineering-inspanning om te integreren met een extern systeem.

Hier vindt u stapsgewijze instructies voor het verbinden van elk van de mogelijke lead doelen:

**Dynamics CRM online** - [Klik hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) voor instructies over het configureren van Dynamics CRM Online voor het ophalen van leads.

**Marketo** - [Klik hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) voor instructies voor het instellen van de configuratie van de marketo-lead om leads op te halen.

**Sales force** - [Klik hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) voor instructies voor het instellen van uw Sales Force-exemplaar voor het verkrijgen van leads.

**Azure table** - [Klik hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) voor instructies voor het instellen van uw Azure Storage-account voor het ophalen van leads in een Azure-tabel.

**Https-eind punt** - [Klik hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) voor de instructies voor het instellen van uw https-eind punt om leads op te halen.

Zodra u uw lead doel correct hebt geconfigureerd en op uw aanbieding hebt geklikt op publiceren, zullen we de verbinding valideren en u een test lead sturen. Wanneer u de aanbieding bekijkt voordat u live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding te verkrijgen in de preview-omgeving. Het is belang rijk om ervoor te zorgen dat uw lead instellingen up-to-date blijven, zodat u geen leads kwijtraakt. Zorg er dus voor dat u deze verbindingen bijwerkt wanneer iets aan uw kant is gewijzigd.


### <a name="what-are-the-next-steps"></a>Wat zijn de volgende stappen?

Zodra de technische installatie is ingesteld, moet u deze leads opnemen in uw huidige verkoop & marketing strategie en operationele processen. We zijn geïnteresseerd in een beter inzicht in uw algehele verkoop proces en willen nauw keurig samen werken op het leveren van leads van hoge kwaliteit en voldoende gegevens om u te laten slagen. We stellen uw feedback op de manier waarop we u kunnen helpen bij het optimaliseren en verbeteren van de leads die wij u met aanvullende gegevens sturen om deze klanten succesvol te maken. Laat het ons weten als u geïnteresseerd bent in feedback en suggesties zodat uw verkoop team succes voller kan maken met leads in de Marketplace.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Veelvoorkomende fouten bij de configuratie van de lead tijdens het publiceren op de Cloud Partner-Portal 

**Kan de lead niet opslaan in Dynamics CRM. Controleer de instellingen van het Dynamics CRM-account. LastCRMError: kan niet aanmelden bij Dynamics CRM, LastCRMException:** 

> Als O365-verificatie is geselecteerd, controleert u of het gebruikers account en het wacht woord geldig zijn. Als AAD is geselecteerd, controleert u of de Tenant-ID, toepassings-ID en sleutel van de toepassing geheim overeenkomen met wat er is ingesteld voor AAD. Volg de instructies [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Als de gebruikers naam of het wacht woord van het account geldig is, controleert u of het toegang heeft tot Dynamics 365 en of er een licentie is toegewezen (stap 11-15 als u Azure Active Directory of beveiligings instellingen gebruikt als u een Office-gebruiker gebruikt). 

 
**Kan de lead niet opslaan in Dynamics CRM. De gebruiker heeft geen machtigingen voor het maken van het kenmerk leadsourcecode in de entiteit lead** 

> De toepassing/gebruiker mist een of meer beveiligings rollen om de lead schrijver te Microsoft Marketplace. Volg de stappen 11-15 als u Azure Active Directory of beveiligings instellingen gebruikt als u [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)een Office-gebruiker gebruikt.

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: de Tenant is niet gevonden. Dit exemplaar kan zich voordoen als er geen actieve abonnementen voor de Tenant zijn.**  

> De map-id die is opgenomen in de sectie Lead beheer is geen geldige map. U kunt de Directory-id ophalen op [basis van de](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) instructies bij stap 2 (onder Azure Active Directory 

**Kan de lead niet opslaan in Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser is mislukt-er zijn geen rollen aan de gebruiker toegewezen.**  

> Oplossing: wijs de beveiligingsrol toe aan Microsoft Marketplace lead Writer. Volg de [instructies in](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) de beveiligings instellingen. 

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: de toepassing met de id is niet gevonden in de map** 

> De toepassings-id die is opgenomen in de sectie Lead beheer is geen geldige map. Haal de Directory-id op op basis van de instructies in stap 8 (onder Azure Active Directory van [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: de aangevraagde Tenant-id is ongeldig en heeft geen geldige indeling voor een extern domein** 

> De map-id die is opgenomen in de sectie Lead beheer is geen geldige map. Haal de Directory-id op op basis van de instructies bij stap 2 (onder Azure Active Directory van [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: fout bij het valideren van referenties.: er is een ongeldig client geheim gegeven.** 

> Oplossing: Meld u aan bij Azure Portal, Controleer of de toepassings sleutel overeenkomt met wat er in de Cloud Partner-portal. Genereer een wacht woord op [basis van de](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)instructie bij stap 10 (onder Azure Active Directory). 

**Kan de lead niet opslaan in Dynamics CRM. LastCRMError: er is een time-out opgetreden voor het aanvraag kanaal tijdens het wachten op een antwoord na 00:02:00. Verhoog de time-outwaarde die is door gegeven aan de aanroep naar request of verhoog de SendTimeout-waarde voor de binding. De tijd die aan deze bewerking wordt toegewezen, heeft mogelijk een deel van een langere time-out.**  

> Oplossing: Meld u aan bij Cloud Partner-portal, Controleer de details van de winkel > > doel locatie van de lead > > URL, Controleer of het een geldig dynamisch CRM-exemplaar is

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Wat zijn leads en waarom zijn deze belang rijk voor mij als een uitgever op Marketplace?** 

Leads zijn klanten die uw producten implementeren via Marketplace. Of uw product wordt vermeld op [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) of [AppSource](https://appsource.microsoft.com/), u kunt leads ontvangen van klanten die geïnteresseerd zijn in uw product als u de lead bestemming hebt ingesteld op uw aanbieding.  


**Waar kan ik hulp krijgen bij het instellen van mijn lead bestemming?** 

U kunt hier documentatie vinden: [leads van klanten ophalen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) of een ondersteunings ticket verzenden via aka.MS/marketplacepublishersupport Select offer type en lead management. 



**Moet ik een lead bestemming configureren om een aanbieding op Marketplace te publiceren?**

Ja, als u een contact persoon, SaaS-app of advies service publiceert.  
 


**Hoe kan ik controleren of de lead configuratie juist is?**

Nadat u uw aanbieding hebt ingesteld en het doel van de lead, publiceert u uw aanbieding. In de stap voor het valideren van de lead stuurt Marketplace een test bericht naar de doel locatie van de lead die in uw aanbieding is geconfigureerd. 


**Hoe kan ik de test lead vinden?**


Zoek naar ' MSFT_TEST ' in de doel locatie van de lead. Hier volgt een voor beeld van lead gegevens: 

bedrijf = MSFT_TEST_636573304831318844 

land = US 

Beschrijving = MSFT_TEST_636573304831318844 

e-mail = MSFT_TEST_636573304831318844@test.com

Encoding = UTF-8 

Encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 |\<aanbiedings naam > 

OID = 00Do0000000ZHog 

telefoon = 1234567890 

titel = MSFT_TEST_636573304831318844 

 

**Ik heb een live aanbieding, maar ik zie geen leads?**

Elke lead heeft gegevens die worden door gegeven in velden in uw geselecteerde doel bestemming, de leads krijgen de volgende indeling: **bron actie | Aanbieding** 

  *Beperken*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Regelen*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Over*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Hier worden de voorbeeld gegevens van de klant gegevens weer gegeven*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Meer informatie vindt u onder [lead info](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Ik heb Azure BLOB geconfigureerd als mijn lead doel, waarom zie ik de lead niet?** 

De lead wordt alleen geschreven wanneer u Azure BLOB-opslag als uw lead bestemming selecteert. Overschakelen naar Azure-tabel om de lead real time te ontvangen 


**Ik heb een e-mail ontvangen van Marketplace, waarom kan ik de lead niet vinden in mijn CRM?**  

Het is mogelijk dat het e-mail domein van de eind gebruiker afkomstig is van. edu. Voor privacy-redenen geven we geen PII-gegevens uit het domein. edu door. Een ondersteunings ticket verzenden via aka.ms/marketplacepublishersupport 


 **Ik heb Azure Table/Azure-BLOB geconfigureerd als mijn lead doel, hoe kan ik de leads weer geven?** 

U kunt toegang krijgen tot de BLOB of tabel vanuit Azure Portal, of u kunt [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratis downloaden en installeren om de tabellen/blobs van uw Azure Storage-account weer te geven. 


**Ik heb Azure Table als mijn lead bestemming geconfigureerd, kan ik een melding ontvangen wanneer een nieuwe lead door Marketplace wordt verzonden?** 

Ja, volg de instructies voor het instellen van de Azure Table +-functie in de documentatie [hier](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Ik heb Sales Force geconfigureerd als mijn lead-doel waarom kan ik de leads niet vinden?** 

Controleer of het formulier Web to lead een verplicht veld is op basis van een selectie lijst. Zo ja, schakelt u over naar het veld naar een niet-verplicht tekst veld.  
 

**Er is een probleem met mijn lead doel en er zijn enkele leads gemist. Kan ik ze in een e-mail bericht naar mij verzenden?** 

Als gevolg van PII-beleid (persoonlijke Identificeer bare informatie) kunnen we geen lead gegevens delen via onbeveiligde e-mail berichten. 



**Ik heb Azure Storage (BLOB/Table) geconfigureerd als mijn lead-Destination, wat kost het dan kosten?** 

De gegevens van de lead zijn laag (< 1 GB voor bijna alle uitgevers). De kosten zijn afhankelijk van het aantal ontvangen leads, als 1.000-leads per maand worden ontvangen, kosten ongeveer 50 cent. 
