---
title: Tabblad Marktplaats voor virtuele machines in de Cloud Partner Portal voor Azure Marketplace
description: Beschrijft het tabblad Marketplace dat wordt gebruikt bij het maken van een Azure Marketplace VM-aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 156a405d93aeac27f27fe617e1a5485044309846
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273814"
---
# <a name="virtual-machine-marketplace-tab"></a>Tabblad Marktplaats voor virtuele machines

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een Azure Virtual Machine-aanbieding maken](https://aka.ms/CreateAzureVMoffer) om uw gemigreerde aanbiedingen te beheren.

Op het tabblad **Marketplace** van de pagina **Nieuwe aanbieding** u uw potentiële klanten marketing- en verkoop- en juridische informatie en overeenkomsten bieden en leads beheren die vanuit de markt worden gegenereerd. Deze lange vorm is verdeeld in vier secties: **Overzicht**, **Marketing Artefacten,** **Lead Management**, en **Juridische**.


## <a name="overview-section"></a>Overzichtssectie
Voer in deze sectie de algemene informatie over uw Azure Marketplace-aanbieding in.  Een toegevoegd sterretje (*) op de veldnaam geeft aan dat dit vereist is.

![Overzichtssectie van het tabblad Marketplace voor virtuele machines](./media/publishvm_008.png)

In de volgende tabel worden het doel en de inhoud van deze velden beschreven. Vereiste velden worden aangeklaagd door een sterretje (*).

|  **Veld**                |     **Beschrijving**                                                          |
|  ---------                |     ---------------                                                          |
| **Titel\***                 | Titel van het aanbod, vaak de lange, formele naam. Deze titel zal prominent worden weergegeven in de markt.  Maximale lengte van 50 tekens. |
| **Samenvatting\***               | Kort doel of functie van oplossing.  Maximale lengte van 100 tekens. |
| **Lange samenvatting\***          | Doel of functie van oplossing.  Maximale lengte van 256 tekens. |
| **Beschrijving\***           | Beschrijving van de oplossing.  Maximale lengte van 3000 tekens, ondersteunt eenvoudige HTML-opmaak. |
| **Microsoft CSP Reseller-kanaal\*** | Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen. |
| **Marketing-id\***  | Een unieke URL om aan deze aanbieding te koppelen, bevat meestal uw organisatie en oplossingsnaam, maximale lengte 50 tekens.  Bijvoorbeeld: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Voorbeeld van abonnements-iD's bekijken\*** | Voeg één tot 100 abonnements-id's van previewers toe. Deze abonnementen op de witte lijst hebben toegang tot de aanbieding zodra deze is gepubliceerd, voordat het live gaat. |
| **Nuttige links**          | Url's toevoegen aan de documentatie, releasenotes, veelgestelde vragen, enzovoort. |
| **Voorgestelde rubrieken\*** | Selecteer maximaal twee (2) categorieën, waaronder een primaire en een secundaire categorie (optioneel). Selecteer maximaal twee (2) subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie is geselecteerd, is het aanbod nog steeds alleen te ontdekken in de geselecteerde categorie. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sectie Marketingartefacten

Dit tweede deel is verdeeld in drie subsecties: **Logos**, **Screenshot**, en **Video's**. Logo's zijn de enige vereiste marketing artefacten, maar alle zijn sterk aanbevolen voor de beste oproep van de klant. 

![Sectie Marketingartefacten van het tabblad Marketplace op het formulier Nieuwe aanbieding voor virtuele machines](./media/publishvm_009.png)

In de volgende tabel worden het doel en de inhoud van deze velden beschreven. Vereiste velden worden aangeklaagd door een sterretje (*).

|  **Veld**                |     **Beschrijving**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos*  |  |
| **Klein\***                 | 40x40 pixel .ico bitmap                                                      |
| **Medium\***                | 90x90 pixel .ico bitmap                                                      |
| **Groot\***                 | 115x115 pixel .ico bitmap                                                   |
| **Breed\***                  | 255x115 pixel .ico bitmap                                                    |
| **Held**                  | 815x290 bitmap.  Optioneel, maar eenmaal geüpload kan het heldenpictogram niet worden verwijderd. |
| *Schermopnamen*  | Optioneel, maar maximaal vijf screenshots per SKU. |
| **Naam**                  | Naam of titel <!-- TODO - max char length? none specified in UI -->                               |
| **Installatiekopie**                 | Beeldopname van het scherm, 533x324 pixel                                         |
| *Video's*  |  |
| **Naam**                  | Naam of titel  <!-- TODO - max char length? -->                              |
| **Koppeling**                  | Video-URL, gehost op YouTube of Vimeo                                        |
| **Miniatuur**             | 533x324 bitmap                                                               |
|   |   |

### <a name="logo-guidelines"></a>Logorichtlijnen

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alle logo's die naar de Cloud Partner Portal zijn geüpload, moeten de richtlijnen volgen:

*  Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Houd het aantal primaire en secundaire kleuren op uw logo laag.
*  De themakleuren van de Azure-portal zijn wit en zwart. Vermijd daarom het gebruik van deze kleuren als de achtergrondkleur van uw logo's. Gebruik een kleur die uw logo's prominent zou maken in de Azure-portal. We adviseren eenvoudige primaire kleuren. Als u een transparante achtergrond gebruikt, zorg er dan voor dat de logo's/tekst niet wit, zwart of blauw zijn.
*  Gebruik geen verloopachtergrond op uw logo.
*  Plaats geen tekst, zelfs uw bedrijf of merknaam, op het logo. De look en feel van uw logo moet "plat" zijn en hellingen vermijden.
*  Rek het logo niet uit.

#### <a name="hero-logo"></a>Hero-logo

Het Hero-logo is optioneel; maar eenmaal geüpload, kan het heldenpictogram niet worden verwijderd.  Het pictogram van het Hero-logo moet de richtlijnen volgen:

*  Zwarte, witte en transparante achtergronden zijn niet toegestaan voor heldenpictogrammen.
*  Vermijd het gebruik van een lichte kleur als achtergrond van de held pictogram.  De weergavenaam publisher, de titel van het abonnement en het lange overzicht van de aanbieding worden weergegeven in witte lettertypekleur en moeten opvallen tegen de achtergrond.
*  Vermijd het gebruik van de meeste tekst terwijl u het hero-logo ontwerpt.  De naam van de uitgever, de titel van het abonnement, het lang overzicht van de aanbieding en een knop maken zijn programmatisch ingebed in het heldenpictogram wanneer de aanbieding wordt weergegeven. 
* Voeg een ongebruikte rechthoek toe aan de rechterkant van het heldenpictogram, van 415x100 pixels en een verschuiving van 370 px vanaf links.  

Als voorbeeld is het volgende heldenpictogram voor de Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Voorbeeldpictogram voor Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Voorbeeld van marketinginformatie 

In de volgende afbeelding ziet u hoe marketinginformatie wordt weergegeven op de hoofdproductpagina van Microsoft Windows Server.

![Voorbeeld productpagina voor Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Sectie Leadbeheer

In het derde gedeelte u klantenleads verzamelen die zijn gegenereerd uit uw Azure Marketplace-aanbiedingen. Het biedt de volgende opslagopties (uit een vervolgkeuzelijst) voor deze leadinformatie.

* **Geen** - de standaard, leadinformatie wordt niet verzameld.
* Azure Table - geschreven naar de Azure-tabel die is opgegeven door een verbindingstekenreeks.
* Dynamics CRM Online - geschreven naar de instantie [Microsoft Dynamics 365 Online,](https://dynamics.microsoft.com/) opgegeven op basis van een URL en verificatiereferenties.
* HTTPS-eindpunt - geschreven naar het opgegeven HTTPS-eindpunt als json-payload.
* Marketo - geschreven naar de opgegeven [Instantie Marketo,](https://www.marketo.com/) opgegeven door server-ID, munchkin-id en formulier-ID.
* Salesforce - geschreven naar een [Salesforce-database,](https://www.salesforce.com/) opgegeven door een object-id.

Nadat u uw aanbieding hebt gepubliceerd, wordt de leadverbinding gevalideerd en wordt een testlead automatisch naar de geconfigureerde bestemming verzonden. Leadinformatie moet continu worden beheerd en deze instellingen moeten onmiddellijk worden bijgewerkt wanneer er wijzigingen worden aangebracht in uw architectuur voor klantbeheer.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Juridische sectie

In dit laatste gedeelte u de vereiste juridische documenten verstrekken die nodig zijn voor elke aanbieding.  

|  **Veld**                    |     **Beschrijving**                                        |
|  ---------                    |     ---------------                                        |
| **URL van het privacybeleid\***      | URL naar uw geplaatste privacybeleid                          |
| **Standaardcontract gebruiken?\***  |   |
| **Gebruiksvoorwaarden\***            | beleid als platte tekst of eenvoudige HTML.                       |
|  |  |


## <a name="next-steps"></a>Volgende stappen

Op het volgende [tabblad Ondersteuning](./cpp-support-tab.md) biedt u technische en gebruikersondersteuningsbronnen voor uw aanbieding.
