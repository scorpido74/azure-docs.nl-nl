---
title: Klantleads configureren | Azure Marketplace
description: Klantleads configureren in de Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280317"
---
<a name="get-customer-leads"></a>Leads klant ophalen
==================

In dit artikel wordt uitgelegd hoe u klantleads maakt met behulp van de Cloud Partner Portal. U deze leads aansluiten op uw CRM-systeem en deze integreren in uw verkooppijplijn.

## <a name="leads"></a>Potentiële klanten

Leads zijn klanten die geïnteresseerd zijn in of uw producten implementeren vanuit de [Azure Marketplace](https://azuremarketplace.microsoft.com/) of van [AppSource.](https://appsource.microsoft.com)

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Klant neemt een "Proefrit" van uw aanbod. Testdrives zijn een versnelde kans voor u om uw bedrijf direct te delen met potentiële klanten zonder enige toetredingsdrempels. Alle teststations genereren een lead voor een klant die geïnteresseerd is in het uitproberen van uw product om meer te weten te komen. Meer informatie over teststations op [Azure Marketplace-teststation](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Voorbeelden van Marktplaats-teststations](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Klant stemt in met het delen van hun informatie na het selecteren van "Nu krijgen". Deze lead is een **eerste interesselead,** waarbij we informatie delen over klanten die interesse hebben getoond in het verkrijgen van uw product. De lead is de bovenkant van de acquisitietrechter.

   ![Download het nu optie](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. De klant selecteert 'Kopen' in de [Azure Portal](https://portal.azure.com/) om uw product op te halen. Deze lead is een **actieve** lead, waarbij we informatie delen over een klant die is begonnen met het implementeren van uw product.

   ![Aankoopoptie](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  De klant heeft een "Test Drive" aangenomen voor uw aanbieding. Testdrives zijn een versnelde kans voor u om uw bedrijf direct te delen met potentiële klanten zonder enige toetredingsdrempels. Alle teststations genereren een lead van een klant die geïnteresseerd is in het uitproberen van uw product om meer te weten te komen. Meer informatie over testritten op [AppSource-teststation](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Voorbeeld van testrit](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Klant stemt in met het delen van hun informatie na het selecteren van "Nu krijgen". Deze lead is een **eerste interesselead,** waarbij we informatie delen over klanten die interesse hebben in het verkrijgen van uw product. De lead is de bovenkant van de acquisitietrechter.

      ![Download het nu optie](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Klant selecteert "Neem contact met mij op" op uw aanbod. Deze lead is een **actieve** lead, waarbij we informatie delen over een klant die vraagt om opgevolgd te worden over uw product.

    ![Neem contact met mij op, optie](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Leadgegevens
---------

Elke lead die u ontvangt tijdens het klantacquisitieproces heeft gegevens op specifieke gebieden. Omdat u leads uit meerdere stappen krijgt, u de leads het beste verwerken door de follow-ups te dedupliceren en te personaliseren. Op deze manier krijgt elke klant een passend bericht en creëer je een unieke relatie.

### <a name="lead-source"></a>Leadbron

De indeling voor een leadbron is-**Bronactieaanbieding** |  **Offer** **Source**

**Bronnen**: "AzureMarketplace", "AzurePortal", "TestDrive" en "AppSource (SPZA)"

**Acties**:
- "INS" -- Installatie. Deze actie is op Azure Marketplace of AppSource wanneer een klant uw product koopt.
- "PLT" - staat voor Partner Led Trial. Deze actie is op AppSource wanneer een klant de optie Contact met mij gebruikt.
- "DNC" -- Neem geen contact op. Deze actie staat op AppSource wanneer een partner die is crossop uw app-pagina wordt vermeld, wordt verzocht gecontacteerd te worden. We delen de heads-up dat deze klant is vermeld in uw app, maar ze hoeven niet te worden gecontacteerd.
- 'Maken' - Deze actie bevindt zich alleen in de Azure Portal en wordt gegenereerd wanneer een klant uw aanbieding naar zijn account koopt.
- "StartTestDrive" - Deze actie is alleen voor teststations en wordt gegenereerd wanneer een klant zijn testrit start.

**Aanbiedingen**

In de volgende voorbeelden worden unieke id's weergegeven die zijn toegewezen aan een uitgever en een specifieke aanbieding: checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress en docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Klantgegevens

In de velden in het volgende voorbeeld worden de klantgegevens weergegeven die in een lead zijn opgenomen.
- Voornaam: John
- Achternaam: Smith
- E-mail:\@jsmith microsoft.com
- Telefoon: 1234567890
- Land: VS
- Bedrijf: Microsoft
- Benaming: CTO

>[!Note]
>Niet alle gegevens in het vorige voorbeeld zijn altijd beschikbaar voor elke lead.

We zijn actief bezig met het verbeteren van leads, dus als er een gegevensveld is dat u hier niet ziet, maar graag zou willen hebben, stuur ons dan [uw feedback.](mailto:AzureMarketOnboard@microsoft.com)

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Uw CRM-systeem verbinden met de Cloud Partner Portal
------------------------------------------------------------

Om leads te krijgen, hebben we onze Lead Management-connector op de Cloud Partner Portal gebouwd, zodat u eenvoudig uw CRM-gegevens aansluiten en we de verbinding voor u maken. Nu u eenvoudig gebruik maken van de leads gegenereerd door de markt zonder een aanzienlijke technische inspanning om te integreren met een extern systeem.

![Leadbeheerconnector](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

We kunnen leads in verschillende CRM-systemen schrijven of rechtstreeks naar een Azure Storage Table, waar u de leads beheren zoals u dat wilt. Elk van de volgende links bevat instructies voor het verbinden met mogelijke leadbestemmingen:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) voor de instructies over het configureren van Dynamics CRM Online voor het verkrijgen van leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) om de instructies voor het opzetten van Marketo Lead Configuration te krijgen om leads te krijgen.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) krijgt instructies voor het instellen van je Salesforce-exemplaar om leads te genereren.
-    [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) krijgt de instructies voor het instellen van uw Azure-opslagaccount voor het ophalen van leads in een Azure-tabel.
-   [Https-eindpunt](./cloud-partner-portal-lead-management-instructions-https.md) om de instructies te krijgen voor het instellen van uw Https-eindpunt om leads te genereren.

Nadat u uw hoofdbestemming hebt geconfigureerd en uw aanbieding hebt gepubliceerd, valideren we de verbinding en sturen we u een testlead. Wanneer u het aanbod bekijkt voordat u live gaat, u ook uw leadverbinding testen door te proberen het aanbod zelf te verkrijgen in de preview-omgeving. Het is belangrijk om ervoor te zorgen dat uw leadinstellingen up-to-date blijven, zodat u geen leads verliest, dus zorg ervoor dat u deze verbindingen bijwerkt wanneer er iets is veranderd aan uw kant.

<a name="what-next"></a>Wat nu?
----------

Zodra de technische set-up is op zijn plaats, moet u deze leads op te nemen in uw huidige verkoop & marketing strategie en operationele processen. We zijn zeer geïnteresseerd in een beter begrip van uw totale verkoopproces en willen nauw met u samenwerken bij het leveren van hoogwaardige leads en voldoende gegevens om u succesvol te maken. We zijn blij met uw feedback over hoe we de leads die we u sturen kunnen optimaliseren en verbeteren met aanvullende gegevens om deze klanten succesvol te maken. Laat het ons weten als je geïnteresseerd bent in [het geven van feedback](mailto:AzureMarketOnboard@microsoft.com) en suggesties om je verkoopteam succesvoller te laten zijn met Marketplace Leads.
