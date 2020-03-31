---
title: Veelgestelde vragen in Azure Data Catalog
description: Veelgestelde vragen over Azure Data Catalog, inclusief mogelijkheden voor het ontdekken, annotatie en beheer van gegevensbronnen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7dbb1b4a8b85350b8bf8a6df0c9035a19055444c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409016"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Veelgestelde vragen in Azure Data Catalog
In dit artikel vindt u antwoorden op veelgestelde vragen met betrekking tot de Azure Data Catalog-service.

## <a name="what-is-azure-data-catalog"></a>Wat is Azure Data Catalog?
Data Catalog is een volledig beheerde service, gehost in Microsoft Azure, die fungeert als een systeem van registratie en detectie voor bedrijfsgegevensbronnen. Met Data Catalog kan elke gebruiker, van analisten tot gegevenswetenschappers en ontwikkelaars, gegevensbronnen registreren, ontdekken, begrijpen en consumeren.

## <a name="what-customer-challenges-does-it-solve"></a>Welke klantuitdagingen lost het op?
Data Catalog richt zich op de uitdagingen van data-source discovery en "dark data", zodat gebruikers bedrijfsgegevensbronnen kunnen ontdekken en begrijpen.

## <a name="what-are-its-target-audiences"></a>Wat zijn de doelgroepen?
Data Catalog is ontworpen voor technische en niet-technische gebruikers, waaronder:

* Dataontwikkelaars en BI- en analyseprofessionals: mensen die verantwoordelijk zijn voor het produceren van gegevens en analyse-inhoud die anderen kunnen consumeren.
* Data stewards: Mensen die de kennis hebben over de gegevens, wat het betekent en hoe het bedoeld is om te worden gebruikt.
* Gegevensconsumenten: Mensen die gemakkelijk de gegevens moeten kunnen ontdekken, begrijpen en verbinden met de gegevens die ze nodig hebben om hun werk te doen, met behulp van de tool van hun keuze.
* Centrale IT: Mensen die honderden gegevensbronnen vindbaar moeten maken voor zakelijke gebruikers en die toezicht moeten houden op de manier waarop gegevens worden gebruikt en door wie.

## <a name="what-is-its-availability-by-region"></a>Wat is de beschikbaarheid per regio?
Data Catalog-services zijn momenteel beschikbaar in de volgende datacenters:

* VS - west
* VS - oost
* Europa -west
* Europa - noord
* Australië - oost
* Azië - zuidoost

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Wat zijn de beperkingen op het aantal gegevensactiva?
De Gratis Editie van Data Catalog is beperkt tot 5.000 geregistreerde gegevensactiva.

De Standard Edition van Data Catalog ondersteunt tot 100.000 geregistreerde gegevensassets.

Elk object dat is geregistreerd in gegevenscatalogus, zoals tabellen, weergaven, bestanden en rapporten, telt als een gegevenselement.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Wat zijn de ondersteunde gegevensbron- en activatypen?
Zie [Gegevenscatalogus DSR](data-catalog-dsr.md)voor een lijst met momenteel ondersteunde gegevensbronnen.

## <a name="how-do-i-request-support-for-another-data-source"></a>Hoe vraag ik ondersteuning voor een andere gegevensbron?
Als u functieverzoeken en andere feedback wilt indienen, gaat u naar de [gegevenscatalogus op de Azure Feedback Forums.](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources)

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Waarom krijg ik al een *foutcatalogus* als ik een nieuwe catalogus probeer te maken?

Wanneer u Office 365 E5 met Power BI Pro-licentie koopt, maakt Microsoft automatisch een standaardcatalogus in de regio van het abonnement. Deze catalogus maakt gebruik van de gratis SKU. De gebruikerslicentie van Office 365 / Power BI wordt beheerd op de pagina Office 365-beheer. 

Dit type gegevenscatalogus heeft echter geen **beheerdersoptie** en is niet zichtbaar in de **Azure-portal.** U dit type gegevenscatalogus niet verwijderen. Op dezelfde manier mag u de naam van de gegevenscatalogus niet wijzigen en u deze niet verplaatsen naar een andere regio. 

Gebruikersaccounts waaraan automatisch een Power BI Pro-licentie is toegewezen, hebben toegang tot de gegevenscatalogus vanwege licentieovereenkomst toen ze zich met de Power BI Pro-licentie hebben aangemeld voor Office 365 E5. Dit type gebruiker heeft volledige toegang tot gegevenscataloguselementen zonder beheerdersrechten. Zo'n gebruiker maakt *geen* deel uit van de functie **Catalogusgebruiker** in Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Hoe ga ik aan de slag met Data Catalog?
De beste manier om aan de slag te gaan is door aan de slag te gaan [met datacatalogus.](data-catalog-get-started.md) Dit artikel is een end-to-end overzicht van de mogelijkheden in de service.

## <a name="how-do-i-register-my-data"></a>Hoe registreer ik mijn gegevens?
Ga als u uw gegevens registreren in gegevenscatalogus:
1. Start in de portal Azure Data Catalog in het gebied **Publiceren** het hulpprogramma voor de registratie van Azure Data Catalog. 
2. Meld u in het hulpprogramma voor gegevenscatalogusgegevensbron aan met dezelfde referenties die u gebruikt om toegang te krijgen tot de portal Gegevenscatalogus.
3. Selecteer de gegevensbron en de specifieke elementen die u wilt registreren.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Welke eigenschappen wordt het geëxtraheerd voor gegevensassets die zijn geregistreerd?
De specifieke eigenschappen verschillen van gegevensbron tot gegevensbron, maar in het algemeen haalt de publicatieservice gegevenscatalogus de volgende informatie uit:

* Naam van activa
* Assettype
* Beschrijving van activa
* Kenmerk/kolomnamen
* Gegevenstypen kenmerken/kolomgegevens
* Kenmerk/kolombeschrijving

> [!IMPORTANT]
> Het registreren van gegevenselementen met Data Catalog verplaatst of kopieert uw gegevens niet naar de cloud. Het registreren van assets uit een gegevensbron kopieert de metagegevens van de assets naar Azure, maar de gegevens blijven op de bestaande locatie van de gegevensbron. De uitzondering op deze regel is als u ervoor kiest om voorbeeldrecords of een gegevensprofiel te uploaden wanneer u de activa registreert. Wanneer u een voorbeeld opneemt, worden maximaal 20 records van elk item gekopieerd en opgeslagen als momentopname in gegevenscatalogus. Wanneer u een gegevensprofiel opneemt, worden geaggregeerde gegevens berekend en opgenomen in de metagegevens die in de catalogus zijn opgeslagen. Geaggregeerde informatie kan de grootte van tabellen, het percentage null-waarden per kolom of de minimum-, maximum- en gemiddeldewaarden voor kolommen omvatten. 
>
>

> [!NOTE]
> Voor gegevensbronnen zoals SQL Server Analysis Services met een **eigenschap** beschrijving van eerste klas, wordt met het hulpprogramma gegevenscatalogusgegevensbron die eigenschapswaarde geëxtraheerd. Voor *on-premises* SQL Server relationele databases die geen eersteklas **beschrijving** hebben, haalt het hulpprogramma gegevenscatalogusgegevensbron de waarde uit de **ms_description** uitgebreide eigenschap voor objecten en kolommen. Deze eigenschap wordt niet ondersteund voor SQL Azure. Zie [Uitgebreide eigenschappen gebruiken voor databaseobjecten](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)voor meer informatie .
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Hoe lang moet het duren voordat nieuw geregistreerde assets in de catalogus worden weergegeven?
Nadat u activa hebt geregistreerd bij Gegevenscatalogus, kan het zijn dat er een periode van 5 tot 10 seconden is voordat ze worden weergegeven in de portal gegevenscatalogus.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hoe annotaer en verrijk ik de metagegevens voor mijn geregistreerde gegevensassets?
De eenvoudigste manier om metagegevens voor geregistreerde assets te verstrekken, is door het element in de portal Gegevenscatalogus te selecteren en vervolgens de waarden in het eigenschappenvenster of het schemavenster voor het geselecteerde object in te voeren.

U tijdens het registratieproces ook enkele metagegevens, zoals experts en tags, verstrekken. De waarden die u opgeeft in de publicatieservice gegevenscatalogus zijn van toepassing op alle activa die op dat moment zijn geregistreerd. Als u de onlangs geregistreerde objecten in de portal wilt weergeven voor extra annotatie, selecteert u de knop **Portal bekijken** op het laatste scherm van het hulpprogramma voor gegevensbronregistratie gegevenscatalogus.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hoe verwijder ik mijn geregistreerde gegevensobjecten?
U een object uit gegevenscatalogus verwijderen door het object in de portal te selecteren en vervolgens op de knop **Verwijderen** te klikken. Als u het object verwijdert, worden de metagegevens uit gegevenscatalogus verwijderd, maar heeft dit geen invloed op de onderliggende gegevensbron.

## <a name="what-is-an-expert"></a>Wat is een expert?
Een expert is een persoon die een geïnformeerd perspectief heeft over een gegevensobject. Een object kan meerdere experts hebben. Een expert hoeft niet de "eigenaar" te zijn voor een object, maar is gewoon iemand die weet hoe de gegevens kunnen en moeten worden gebruikt.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hoe deel ik informatie met het team Gegevenscatalogus als ik problemen ondervind?
Als u problemen wilt melden, informatie wilt delen en vragen wilt stellen, gaat u naar het [Azure Data Catalog-forum](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Werkt de catalogus met een andere gegevensbron waarin ik geïnteresseerd ben?
We werken actief aan het toevoegen van meer gegevensbronnen aan Data Catalog. Als u een specifieke gegevensbron wilt ondersteunen, stelt u deze voor (of stem uw steun uit als deze al is voorgesteld) door naar de [gegevenscatalogus op de Azure Feedback Forums](https://feedback.azure.com/forums/906052-data-catalog)te gaan.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Welke machtigingen heb ik nodig om assets te registreren bij Data Catalog?
Als u het registratiehulpprogramma voor gegevenscatalogus wilt uitvoeren, hebt u machtigingen nodig voor de gegevensbron waarmee u de metagegevens van de bron lezen. Als u ook een voorbeeld wilt opnemen, moet u machtigingen hebben waarmee u de gegevens van de objecten die worden geregistreerd, lezen.

Met de catalogus met gegevens kunnen catalogusbeheerders ook beperken welke gebruikers en groepen metagegevens aan de catalogus kunnen toevoegen. Zie [De toegang tot gegevenscatalogus en gegevenselementen beveiligen](data-catalog-how-to-secure-catalog.md)voor meer informatie.

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Wordt Data Catalog ook beschikbaar gesteld voor on-premises implementatie?
Data Catalog is een cloudservice die kan werken met zowel cloud- als on-premises gegevensbronnen om een hybride oplossing voor het ontdekken van gegevensbronnen te leveren. Er zijn momenteel geen plannen voor een versie van de datacatalogusservice die on-premises wordt uitgevoerd.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kan ik meer of rijkere metadata extraheren uit de gegevensbronnen die ik registreer?
We zijn actief bezig om de mogelijkheden van Data Catalog uit te breiden. Als u tijdens de registratie extra metagegevens uit de gegevensbron wilt laten halen, stelt u deze voor (of stem ervoor, als deze al is voorgesteld) in de [gegevenscatalogus op de Azure Feedback Forums.](https://feedback.azure.com/forums/906052-data-catalog) 

Als u kolom-/schemametagegevens, voorvertoningen of gegevensprofielen wilt opnemen voor gegevensbronnen waarbij deze metagegevens niet worden geëxtraheerd door het hulpprogramma voor gegevensbronregistratie, u de API voor gegevenscatalogus gebruiken om deze metagegevens toe te voegen. Zie [API voor de restvan azure gegevenscatalogus voor](https://docs.microsoft.com/rest/api/datacatalog/)meer informatie .

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hoe beperk ik de zichtbaarheid van geregistreerde gegevensassets, zodat alleen bepaalde mensen ze kunnen ontdekken?
Selecteer de gegevenselementen in de gegevenscatalogus en klik op de knop **Eigendom overnemen.** Eigenaren van gegevenselementen in gegevenscatalogus kunnen de zichtbaarheidsinstellingen wijzigen zodat alle gebruikers de eigen activa kunnen ontdekken of de zichtbaarheid kunnen beperken tot specifieke gebruikers. Zie [Gegevenselementen beheren in Azure-gegevenscatalogus](data-catalog-how-to-manage.md)voor meer informatie.

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hoe werk ik de registratie voor een gegevenselement bij, zodat wijzigingen in de gegevensbron worden weergegeven in de catalogus?
Als u de metagegevens wilt bijwerken voor gegevenselementen die al in de catalogus zijn geregistreerd, registreert u de gegevensbron die de elementen bevat opnieuw. Alle wijzigingen in de gegevensbron, zoals kolommen die worden toegevoegd of verwijderd uit tabellen of weergaven, worden bijgewerkt in de catalogus, maar eventuele annotaties die door gebruikers worden verstrekt, blijven behouden.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Mijn vraag wordt hier niet beantwoord. Waar kan ik terecht voor antwoorden?
Ga naar het [Azure Data Catalog-forum](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Vragen die daar gesteld worden, vinden hier hun weg.
