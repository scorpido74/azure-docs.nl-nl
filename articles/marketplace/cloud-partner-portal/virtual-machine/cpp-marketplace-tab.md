---
title: Het tabblad Marketplace voor virtuele machines in de Cloud Partner-portal voor Azure Marketplace
description: Hierin wordt het tabblad Marketplace beschreven dat wordt gebruikt voor het maken van een Azure Marketplace-VM-aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 2c3d316d0d2810cb2a25ffd3bc4e34cf3454c10d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146862"
---
# <a name="virtual-machine-marketplace-tab"></a>Het tabblad Marketplace voor virtuele machines

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een Azure virtual machine-aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

Op het tabblad **Marketplace** van de pagina **nieuwe aanbieding** kunt u uw potentiële klanten voorzien van marketing-, verkoop-en juridische informatie en-overeenkomsten en de leads beheren die zijn gegenereerd op basis van de Marketplace. Deze lange vorm is onderverdeeld in vier secties: **overzicht**, **marketing artefacten**, **beheer van leads**en **juridisch**.


## <a name="overview-section"></a>Overzichtssectie
In deze sectie voert u de algemene informatie in over uw Azure Marketplace-aanbieding.  Een toegevoegd sterretje (*) op de veld naam geeft aan dat deze vereist is.

![Overzichts sectie van het tabblad Marketplace voor virtuele machines](./media/publishvm_008.png)

In de volgende tabel worden het doel en de inhoud van deze velden beschreven. Vereiste velden worden gedicteerd door een asterisk (*).

|  **Veld**                |     **Beschrijving**                                                          |
|  ---------                |     ---------------                                                          |
| **Titel\***                 | Titel van de aanbieding, vaak de lange, formele naam. Deze titel wordt prominent weer gegeven in de Marketplace.  Maximale lengte van 50 tekens. |
| **Samenvatting\***               | Kort doel of functie van de oplossing.  Maximale lengte van 100 tekens. |
| **Lange samen vatting\***          | Doel of functie van de oplossing.  Maximale lengte van 256 tekens. |
| **Beschrijving\***           | Beschrijving van de oplossing.  De maximale lengte van 3000 tekens biedt ondersteuning voor eenvoudige HTML-opmaak. |
| **Micro soft CSP-reseller Channel\*** | Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](../../cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen. |
| **Marketing-id\***  | Een unieke URL om aan deze aanbieding te koppelen, bevat doorgaans de naam van uw organisatie en de oplossing, maximale lengte van 50 tekens.  Bijvoorbeeld: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Preview-abonnement-Id's\*** | Voeg een id toe aan 100-abonnement-id's van Previewers. Deze op wit vermelde abonnementen hebben toegang tot de aanbieding nadat deze is gepubliceerd, voordat ze live gaan. |
| **Nuttige koppelingen**          | Voeg Url's toe aan de documenties, opmerkingen bij de release, veelgestelde vragen, enzovoort. |
| **Voorgestelde categorieën\*** | Selecteer Maxi maal twee (2) categorieën, inclusief een primaire en secundaire categorie (optioneel). Selecteer Maxi maal twee (2) subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie is geselecteerd, kunt u nog steeds alleen de geselecteerde categorie detecteerbaar maken. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sectie Marketing artefacten

Deze tweede sectie is onderverdeeld in drie subsecties: **logo's**, **scherm afbeelding**en **Video's**. Logo's zijn de enige vereiste marketing artefacten, maar alles wordt sterk aanbevolen voor het beste bezwaar van de klant. 

![Het gedeelte Marketing artefacten op het tabblad Marketplace van het formulier nieuwe aanbieding voor virtuele machines](./media/publishvm_009.png)

In de volgende tabel worden het doel en de inhoud van deze velden beschreven. Vereiste velden worden gedicteerd door een asterisk (*).

|  **Veld**                |     **Beschrijving**                                                          |
|  ---------                |     ---------------                                                          |
| *Logo's*  |  |
| **Klein\***                 | 40x40 pixel. ico-bitmap                                                      |
| **Medium\***                | 90x90 pixel. ico-bitmap                                                      |
| **Groot\***                 | 115x115 pixel. ico-bitmap                                                   |
| **Organisatiebreed\***                  | 255x115 pixel. ico-bitmap                                                    |
| **Hero**                  | 815x290-bitmap.  Optioneel, maar wanneer het held pictogram is geüpload, kan het niet worden verwijderd. |
| *Schermopnamen*  | Optioneel, maar Maxi maal vijf scherm opnamen per SKU. |
| **Naam**                  | Naam of titel <!-- TODO - max char length? none specified in UI -->                               |
| **Installatiekopie**                 | Afbeelding van scherm opname, 533x324 pixel                                         |
| *Video's*  |  |
| **Naam**                  | Naam of titel  <!-- TODO - max char length? -->                              |
| **Koppeling**                  | Video-URL, gehost op YouTube of Vimeo                                        |
| **Voorbeeld**             | 533x324-bitmap                                                               |
|   |   |

### <a name="logo-guidelines"></a>Logorichtlijnen

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alle logo's die naar het Cloud Partner-portal worden geüpload, moeten de volgende richt lijnen volgen:

*  Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Laat het aantal primaire en secundaire kleuren op uw logo laag.
*  De thema kleuren van de Azure Portal zijn wit en zwart. Vermijd het gebruik van deze kleuren als de achtergrond kleur van uw logo's. Gebruik een kleur waarmee uw logo's prominent worden gemaakt in de Azure Portal. We adviseren eenvoudige primaire kleuren. Als u transparante achtergrond gebruikt, moet u ervoor zorgen dat de logo's/tekst niet wit of zwart of blauw zijn.
*  Gebruik geen achtergrond met kleur overgang in uw logo.
*  Vermijd het plaatsen van tekst, zelfs uw naam van uw bedrijf of merk, op het logo. Het uiterlijk van uw logo moet ' plat ' zijn en moet geen kleur overgangen hebben.
*  Rekt het logo niet uit.

#### <a name="hero-logo"></a>Hero-logo

Het held-logo is optioneel; Als het pictogram van de held eenmaal is geüpload, kan het niet meer worden verwijderd.  Het pictogram van het held logo moet voldoen aan de richt lijnen:

*  Zwart, wit en transparante achtergronden zijn niet toegestaan voor held-pictogrammen.
*  Vermijd het gebruik van lichte kleuren als achtergrond van het held-pictogram.  De weergave naam van de uitgever, de titel van de planning en de lange samen vatting van de aanbieding worden weer gegeven in witte tekst kleur en moeten worden opgezocht op de achtergrond.
*  Vermijd het gebruik van de meeste tekst tijdens het ontwerpen van het held-logo.  De naam van de uitgever, de titel van het abonnement, de lange samen vatting van de aanbieding en een knop maken zijn Inge sloten in een programma binnen het held-pictogram wanneer de aanbiedings lijsten. 
* Voeg een ongebruikte rechthoek aan de rechter kant van het pictogram van uw held toe, met een grootte van 415x100 pixels en offset 370 PX vanaf links.  

Als voor beeld is het volgende held pictogram voor de Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Voor beeld van een held pictogram voor Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Marketing informatie-voor beeld 

De volgende afbeelding laat zien hoe marketing informatie wordt weer gegeven op de pagina hoofd product van micro soft Windows Server.

![Voorbeeld product pagina voor micro soft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Sectie Lead beheer

Met het derde gedeelte kunt u leads van klanten verzamelen die zijn gegenereerd op basis van uw aanbiedingen in azure Marketplace. Het biedt de volgende opslag opties (uit een vervolg keuzelijst) voor deze lead gegevens.

* **Geen** : de standaard informatie over de lead wordt niet verzameld.
* Azure-tabel-geschreven naar de Azure-tabel die is opgegeven door een connection string.
* Dynamics CRM Online: geschreven naar het [micro soft Dynamics 365 online](https://dynamics.microsoft.com/) -exemplaar, opgegeven door een URL en verificatie referenties.
* HTTPS-eind punt dat is geschreven naar het opgegeven HTTPS-eind punt als JSON-nettolading.
* Market-geschreven naar het opgegeven [marketo](https://www.marketo.com/) -exemplaar, opgegeven door de server-id, Munchkin-id en formulier-id.
* Sales Force-geschreven naar een [Sales Force](https://www.salesforce.com/) -data base, opgegeven door een object-id.

Nadat u uw aanbieding hebt gepubliceerd, wordt de verbinding van de lead gevalideerd en wordt er automatisch een test lead verzonden naar de geconfigureerde bestemming. Lead gegevens moeten voortdurend worden beheerd en deze instellingen moeten onmiddellijk worden bijgewerkt wanneer er wijzigingen worden aangebracht in de beheer architectuur van uw klanten.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Juridisch gedeelte

In deze laatste sectie kunt u de vereiste juridische documenten voor elke aanbieding opgeven.  

|  **Veld**                    |     **Beschrijving**                                        |
|  ---------                    |     ---------------                                        |
| **URL van privacybeleid\***      | URL naar het geposte privacybeleid                          |
| **Standaard contract gebruiken?\***  |   |
| **Gebruiksvoorwaarden\***            | beleid als tekst zonder opmaak of eenvoudige HTML.                       |
|  |  |


## <a name="next-steps"></a>Volgende stappen

Op het tabblad volgende [ondersteuning](./cpp-support-tab.md) geeft u technische en gebruikers ondersteuning voor uw aanbieding.
