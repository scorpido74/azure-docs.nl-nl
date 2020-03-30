---
title: De bedrijfswoordenlijst instellen in Azure-gegevenscatalogus
description: How-to-artikel waarin de bedrijfswoordenlijst in Azure Data Catalog wordt gemarkeerd voor het definiëren en gebruiken van een gemeenschappelijke bedrijfswoordenschat om geregistreerde gegevenselementen te taggen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976797"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>De bedrijfswoordenlijst instellen voor beheerde tagging

## <a name="introduction"></a>Inleiding

Azure Data Catalog maakt detectie van gegevensbronnen mogelijk, zodat u eenvoudig de gegevensbronnen ontdekken en begrijpen die u nodig hebt om analyses uit te voeren en beslissingen te nemen. Deze mogelijkheden maken de grootste impact wanneer u het breedste scala aan beschikbare gegevensbronnen vinden en begrijpen.

Eén functie gegevenscatalogus die een beter begrip van assets bevordert, waardoor gegevens worden getagd. Door tagging te gebruiken, u zoekwoorden koppelen aan een asset of een kolom, wat het op zijn beurt gemakkelijker maakt om het item te ontdekken via zoeken of browsen. Tagging helpt u ook gemakkelijker inzicht te krijgen in de context en intentie van het item.

Echter, tagging kan soms problemen veroorzaken van zijn eigen. Enkele voorbeelden van problemen die tagging kan introduceren zijn:

* Het gebruik van afkortingen op sommige activa en uitgebreide tekst op anderen. Deze inconsistentie belemmert de ontdekking van activa, ook al was het de bedoeling om de assets met dezelfde tag te taggen.
* Mogelijke variaties in betekenis, afhankelijk van de context. Een tag met *de* naam Inkomsten op een klantgegevensset kan bijvoorbeeld inkomsten per klant betekenen, maar dezelfde tag op een kwartaalgegevensset voor verkopen kan kwartaalomzet voor het bedrijf betekenen.  

Om deze en andere soortgelijke uitdagingen aan te pakken, bevat Data Catalog een bedrijfswoordenlijst.

Met de bedrijfswoordenlijst van de gegevenscatalogus kan een organisatie belangrijke bedrijfstermen en hun definities documenteren om een gemeenschappelijke zakelijke woordenschat te creëren. Deze governance maakt consistentie in het gegevensgebruik in de hele organisatie mogelijk. Nadat een term is gedefinieerd in de bedrijfswoordenlijst, kan deze worden toegewezen aan een gegevenselement in de catalogus. Deze aanpak, *geregeld tagging*, is dezelfde aanpak als tagging.

## <a name="glossary-availability-and-privileges"></a>Beschikbaarheid en bevoegdheden voor woordenlijst

De bedrijfswoordenlijst is alleen beschikbaar in de Standaardeditie van Azure Data Catalog. De gratis editie van gegevenscatalogus bevat geen woordenlijst en biedt geen mogelijkheden voor beheerde tags.

U hebt toegang tot de bedrijfswoordenlijst via de **optie Woordenlijst** in het navigatiemenu van de datacatalogusportal.  

![Gegevenscatalogus - Toegang tot de bedrijfswoordenlijst](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Beheerders van gegevenscatalogus en leden van de rol woordenlijstbeheerders kunnen woordenlijsten in de bedrijfswoordenlijst maken, bewerken en verwijderen. Alle gebruikers van de gegevenscatalogus kunnen de term definities bekijken en assets taggen met woordenlijsttermen.

![Gegevenscatalogus - Een nieuwe woordenlijstterm toevoegen](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Woordenlijsttermen maken

Beheerders van gegevenscatalogus en woordenlijstbeheerders kunnen woordenlijsttermen maken door op de knop **Nieuwe term te** klikken. Elke woordenlijstterm bevat de volgende velden:

* Een bedrijfsdefinitie voor de term
* Een beschrijving die de beoogde gebruiks- of bedrijfsregels voor het actief of de kolom vastlegt
* Een lijst van belanghebbenden die het meest weten over de term
* De bovenliggende term, die de hiërarchie definieert waarin de term is georganiseerd

## <a name="glossary-term-hierarchies"></a>Hiërarchieën voor woordenlijsten

Door de bedrijfswoordenlijst van de gegevenscatalogus te gebruiken, kan een organisatie haar zakelijke woordenschat beschrijven als een hiërarchie van termen en kan het een classificatie maken van termen die beter haar bedrijfstaxonomie vertegenwoordigen.

Een term moet uniek zijn op een bepaald niveau van hiërarchie. Dubbele namen zijn niet toegestaan. Er is geen limiet aan het aantal niveaus in een hiërarchie, maar een hiërarchie is vaak gemakkelijker te begrijpen wanneer er drie niveaus of minder zijn.

Het gebruik van hiërarchieën in de bedrijfswoordenlijst is optioneel. Als u het bovenliggende termveld leeg laat voor woordenlijsttermen, wordt een platte (niet-hiërarchische) lijst met termen in de woordenlijst gemaakt.  

## <a name="tagging-assets-with-glossary-terms"></a>Activa taggen met woordenlijsttermen

Nadat woordenlijsttermen zijn gedefinieerd in de catalogus, is de ervaring van het taggen van assets geoptimaliseerd om de woordenlijst te doorzoeken terwijl een gebruiker een tag typt. De portal Gegevenscatalogus geeft een lijst weer met overeenkomende woordenlijsttermen om uit te kiezen. Als de gebruiker een woordenlijstterm uit de lijst selecteert, wordt de term als tag aan het item toegevoegd (ook wel een woordenlijst genoemd). De gebruiker kan er ook voor kiezen om een nieuwe tag te maken door een term te typen die niet in de woordenlijst staat (ook wel een gebruikerstag genoemd).

![Gegevensasset die is getagd met één gebruikerstag en twee woordenlijsttags](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Gebruikerstags zijn het enige type tag dat wordt ondersteund in de gratis editie van gegevenscatalogus.

### <a name="hover-behavior-on-tags"></a>Gedrag van zweven op tags

In de portal Gegevenscatalogus zijn de twee typen tags visueel verschillend en vertonen ze verschillende zwevende gedragingen. Wanneer u de plaats over een gebruikerstag houdt, ziet u de tagtekst en de gebruiker of gebruikers die de tag hebben toegevoegd. Wanneer u over een woordenlijsttag zweeft, ziet u ook de definitie van de woordenlijstterm en een koppeling om de bedrijfswoordenlijst te openen om de volledige definitie van de term te bekijken.

### <a name="search-filters-for-tags"></a>Zoekfilters naar tags

Woordenlijsttags en gebruikerstags zijn beide doorzoekbaar en u ze toepassen als filters in een zoekopdracht.

## <a name="summary"></a>Samenvatting

Door de bedrijfswoordenlijst in Azure Data Catalog te gebruiken en de beheerde tags die dit mogelijk maken, u gegevenselementen op een consistente manier identificeren, beheren en ontdekken. De bedrijfswoordenlijst kan het leren van de bedrijfswoordenschat door organisatieleden bevorderen. De woordenlijst ondersteunt ook het vastleggen van zinvolle metagegevens, wat het ontdekken en begrijpen van activa vereenvoudigt.

## <a name="next-steps"></a>Volgende stappen

* [REST API-documentatie voor bedrijfswoordenlijsten](/rest/api/datacatalog/data-catalog-glossary)
