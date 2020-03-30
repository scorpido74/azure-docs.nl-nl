---
title: Gegevenselementen beheren in Azure-gegevenscatalogus
description: In het artikel wordt uitgelegd hoe u de zichtbaarheid en het eigendom van gegevenselementen beheert die zijn geregistreerd in Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736333"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gegevenselementen beheren in Azure-gegevenscatalogus
## <a name="introduction"></a>Inleiding
Azure Data Catalog is ontworpen voor het ontdekken van gegevensbronnen, zodat u eenvoudig de gegevensbronnen ontdekken en begrijpen die u nodig hebt om analyses uit te voeren en beslissingen te nemen. Deze detectiemogelijkheden hebben de grootste impact wanneer u en andere gebruikers het breedste scala aan beschikbare gegevensbronnen kunnen vinden en begrijpen. Met deze elementen in het achterhoofd, het standaardgedrag van Data Catalog is voor alle geregistreerde gegevensbronnen zichtbaar te zijn voor en vindbaar voor alle catalogusgebruikers.

Data Catalog geeft u geen toegang tot de gegevens zelf. Toegang tot gegevens wordt beheerd door de eigenaar van de gegevensbron. Met Gegevenscatalogus u gegevensbronnen ontdekken en de metagegevens bekijken die gerelateerd zijn aan de bronnen die in de catalogus zijn geregistreerd.

Er kunnen zich echter situaties voordoen waarin gegevensbronnen alleen zichtbaar moeten zijn voor specifieke gebruikers of voor leden van specifieke groepen. In dergelijke scenario's kunnen gebruikers eigenaar worden van geregistreerde gegevenselementen in de catalogus en vervolgens de zichtbaarheid van de activa die ze bezitten beheren.

> [!NOTE]
> De functionaliteit die in dit artikel wordt beschreven, is alleen beschikbaar in de standaardeditie van Azure-gegevenscatalogus. De Free Edition biedt geen mogelijkheden voor eigendom en beperking van de zichtbaarheid van gegevensactiva.
>
>

## <a name="manage-ownership-of-data-assets"></a>Beheer het eigendom van gegevensactiva
Gegevenselementen die zijn geregistreerd in gegevenscatalogus, zijn standaard niet eigendom. Elke gebruiker met toestemming om toegang te krijgen tot de catalogus kan deze elementen ontdekken en annoteren. Gebruikers kunnen eigenaar worden van niet-eigendom gegevensactiva en vervolgens de zichtbaarheid beperken van de activa die ze bezitten.

Wanneer een gegevenselement in gegevenscatalogus eigendom is, kunnen alleen gebruikers die zijn geautoriseerd door de eigenaren het item ontdekken en de metagegevens bekijken, en alleen de eigenaren kunnen het item uit de catalogus verwijderen.

> [!NOTE]
> Eigendom in gegevenscatalogus is alleen van invloed op de metagegevens die in de catalogus zijn opgeslagen. Eigendom verleent geen machtigingen aan de onderliggende gegevensbron.
>
>

### <a name="take-ownership"></a>Eigenaar worden
Gebruikers kunnen eigenaar worden van gegevenselementen door de optie **Eigendom overnemen** te selecteren in de portal Gegevenscatalogus. Er zijn geen speciale machtigingen vereist om eigenaar te worden van een niet-eigendom gegevensactief. Elke gebruiker kan eigenaar worden van een niet-eigendom gegevensactief.

### <a name="add-owners-and-co-owners"></a>Eigenaren en mede-eigenaren toevoegen
Als een gegevenselement al eigendom is, kunnen andere gebruikers niet zomaar eigenaar schap nemen. Ze moeten worden toegevoegd als mede-eigenaren door een bestaande eigenaar. Elke eigenaar kan extra gebruikers of beveiligingsgroepen toevoegen als mede-eigenaren.

> [!NOTE]
> Het is een beste gewoonte om ten minste twee personen als eigenaren voor een eigen gegevens actief.
>
>

### <a name="remove-owners"></a>Eigenaren verwijderen
Net zoals elke eigenaar van een actief mede-eigenaar kan toevoegen, kan elke eigenaar van een actief elke mede-eigenaar verwijderen.

Een eigenaar van een actief die zichzelf verwijdert als eigenaar, kan het actief niet meer beheren. Als de eigenaar van het actief zichzelf verwijdert als eigenaar en er geen andere mede-eigenaren zijn, keert het actief terug naar een niet-eigendom.

## <a name="control-visibility"></a>Zichtbaarheid beheren
Eigenaren van gegevensactiva kunnen de zichtbaarheid van de gegevensactiva die zij bezitten, beheren. Als u de zichtbaarheid als standaard wilt beperken, kunnen alle gebruikers van de gegevenscatalogus het gegevensitem ontdekken en bekijken, en kan de eigenaar van de asset de zichtbaarheidsinstelling van **Iedereen** inschakelen naar **Eigenaren & Deze gebruikers** in de eigenschappen voor het item. Eigenaren kunnen vervolgens specifieke gebruikers en beveiligingsgroepen toevoegen.

> [!NOTE]
> Waar mogelijk moeten machtigingen voor eigendom en zichtbaarheid van activa worden toegewezen aan beveiligingsgroepen en niet aan individuele gebruikers.
>
>

## <a name="catalog-administrators"></a>Catalogusbeheerders
Beheerders van gegevenscatalogus zijn impliciet mede-eigenaar van alle elementen in de catalogus. Eigenaren van activa kunnen de zichtbaarheid van beheerders niet verwijderen en beheerders kunnen het eigendom en de zichtbaarheid van alle gegevenselementen in de catalogus beheren.

## <a name="summary"></a>Samenvatting
Met het crowdsourcingmodel van de gegevenscatalogus met metadata en detectie van gegevensactiva kunnen alle catalogusgebruikers bijdragen en ontdekken. De Standard Edition van Data Catalog is ontworpen voor eigendom en beheer om de zichtbaarheid en het gebruik van specifieke gegevensassets te beperken.
