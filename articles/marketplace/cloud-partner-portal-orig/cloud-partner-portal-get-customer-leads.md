---
title: Leads van klanten configureren | Azure Marketplace
description: Klanten leads configureren in de Cloud Partner-portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 312e172321c25248f1b0801bdbccf71762319989
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818812"
---
<a name="get-customer-leads"></a>Leads klant ophalen
==================

In dit artikel wordt uitgelegd hoe u leads voor klanten maakt met behulp van de Cloud Partner-portal. U kunt deze leads verbinden met uw CRM-systeem en ze integreren in uw verkoop pijplijn.

## <a name="leads"></a>Leiders

Leads zijn klanten die geïnteresseerd zijn in of implementeren uw producten vanuit de [Azure Marketplace](https://azuremarketplace.microsoft.com/) of via [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  De klant neemt een ' test station ' van uw aanbieding. Test stations zijn een versnelde kans om uw bedrijf direct te delen met potentiële klanten, zonder dat u daarvoor belemmeringen hoeft te doen. Alle test stations genereren een lead voor een klant die geïnteresseerd is in het proberen van uw product om meer te weten te komen. Meer informatie over test stations op [Azure Marketplace-test station](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Voor beelden van Marketplace-test drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. De klant heeft toestemming om hun gegevens te delen na het selecteren van ' nu ophalen '. Deze lead is een **eerste geïnteresseerde** lead, waar we informatie delen over de klant die geïnteresseerd is in het ophalen van uw product. De lead is de bovenkant van de verwervings trechter.

   ![Optie nu downloaden](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Klant selecteert kopen in [Azure Portal](https://portal.azure.com/) om uw product te verkrijgen. Deze lead is een **actieve** lead, waar we informatie delen over een klant die is begonnen met het implementeren van uw product.

   ![Aankoop optie](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  De klant nam een ' test station ' voor uw aanbieding. Test stations zijn een versnelde kans om uw bedrijf direct te delen met potentiële klanten, zonder dat u daarvoor belemmeringen hoeft te doen. Alle test stations genereren een lead van een klant die geïnteresseerd is in het proberen van uw product om meer te weten te komen. Meer informatie over test stations op [AppSource-test station](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Voor beeld van test station](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  De klant heeft toestemming om hun gegevens te delen na het selecteren van ' nu ophalen '. Deze lead is een **eerste geïnteresseerde** lead, waar we informatie delen over de klant die geïnteresseerd is in het ophalen van uw product. De lead is de bovenkant van de verwervings trechter.

      ![Optie nu downloaden](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Klant selecteert contact met mij op uw aanbieding. Deze lead is een **actieve** lead, waar we informatie delen over een klant die vraagt om te worden opgevolgd over uw product.

    ![Optie contact opnemen](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Lead gegevens
---------

Elke lead die u ontvangt tijdens het klant verwervings proces heeft gegevens in specifieke velden. Omdat u leads van meerdere stappen krijgt, is het de beste manier om de leads af te handelen en de opvolging te personaliseren. Op deze manier krijgt elke klant een passend bericht en maakt u een unieke relatie.

### <a name="lead-source"></a>Bron van lead

De indeling voor een bron van een lead is **bron**-**actie** |  **aanbieding**

**Bronnen**: "AzureMarketplace", "AzurePortal", "TestDrive" en "APPSOURCE (SPZA)"

**Acties**:
- ' INS '--installatie. Deze actie bevindt zich op Azure Marketplace of AppSource wanneer een klant uw product koopt.
- "PLT": staat voor een partner-LED-proef versie. Deze actie is op AppSource wanneer een klant de optie contact opnemen gebruikt.
- "DNC"--geen contact opnemen. Deze actie bevindt zich op AppSource wanneer een partner die op de pagina van uw app staat, wordt gevraagd om contact met u op te nemen. De koppen worden gedeeld die deze klant heeft door gegeven aan uw app, maar er hoeven geen contact mee te worden gemaakt.
- ' Maken ': deze actie bevindt zich alleen in de Azure Portal en wordt gegenereerd wanneer een klant uw aanbieding aan hun account aanschaft.
- "StartTestDrive": deze actie geldt alleen voor test stations en wordt gegenereerd wanneer een klant hun test drive start.

**Aanbiedingen**

In de volgende voor beelden worden unieke id's weer gegeven die zijn toegewezen aan een uitgever en een specifieke aanbieding: controle punt. Check-Point-r77-10sg-byol, BitNami. openedxcypress en docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Klant gegevens

In de velden in het volgende voor beeld worden de klant gegevens weer gegeven die zijn opgenomen in een lead.
- Voor naam: John
- LastName: Smit
- E-mail: jsmith\@microsoft.com
- Telefoon: 1234567890
- Land: VS
- Bedrijf: micro soft
- Titel: CTO

>[!Note]
>Niet alle gegevens in het vorige voor beeld zijn altijd beschikbaar voor elke lead.

We werken actief aan het verbeteren van leads, dus als er een gegevens veld is dat u hier niet ziet, maar graag [uw feedback wilt ontvangen](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Verbinding maken met uw CRM-systeem met de Cloud Partner-portal
------------------------------------------------------------

Om leads te kunnen ophalen, hebben we onze lead management-connector op de Cloud Partner-portal gebouwd, zodat u eenvoudig uw CRM-gegevens kunt koppelen en de verbinding voor u wordt gemaakt. U kunt nu eenvoudig gebruikmaken van de leads die door Marketplace worden gegenereerd zonder een belang rijke engineering-inspanning om te integreren met een extern systeem.

![Connector voor lead beheer](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

We kunnen leads naar een groot aantal CRM-systemen schrijven of rechtstreeks naar een Azure Storage tabel waar u de leads kunt beheren. Elk van de volgende koppelingen bevat instructies voor het maken van verbinding met mogelijke lead doelen:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) voor instructies over het configureren van Dynamics CRM Online voor het ophalen van leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) om de instructies voor het instellen van de marketo-lead configuratie op te halen om leads te verkrijgen.
-    [Sales Force](./cloud-partner-portal-lead-management-instructions-salesforce.md) om instructies te krijgen voor het instellen van uw Sales Force-exemplaar om leads te verkrijgen.
-    [Azure-tabel](./cloud-partner-portal-lead-management-instructions-azure-table.md) voor de instructies voor het instellen van uw Azure Storage-account om leads in een Azure-tabel op te halen.
-   [Https-eind punt](./cloud-partner-portal-lead-management-instructions-https.md) voor de instructies voor het instellen van uw https-eind punt om leads op te halen.

Nadat u uw lead bestemming hebt geconfigureerd en uw aanbieding hebt gepubliceerd, wordt de verbinding gevalideerd en ontvangt u een test lead. Wanneer u de aanbieding bekijkt voordat u live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding te verkrijgen in de preview-omgeving. Het is belang rijk om ervoor te zorgen dat uw lead instellingen up-to-date blijven, zodat u geen leads kwijtraakt. Zorg er dus voor dat u deze verbindingen bijwerkt wanneer iets aan uw kant is gewijzigd.

<a name="what-next"></a>Wat nu?
----------

Zodra de technische installatie is ingesteld, moet u deze leads opnemen in uw huidige verkoop & marketing strategie en operationele processen. We zijn zeer geïnteresseerd in een beter inzicht in uw algehele verkoop proces en willen nauw keurig samen werken met leads van hoge kwaliteit en voldoende gegevens om u te laten slagen. We stellen uw feedback op de manier waarop we u kunnen helpen bij het optimaliseren en verbeteren van de leads die wij u met aanvullende gegevens sturen om deze klanten succesvol te maken. Laat het ons weten als u geïnteresseerd bent in [feedback](mailto:AzureMarketOnboard@microsoft.com) en suggesties zodat uw verkoop team succes voller kan maken met leads in de Marketplace.
