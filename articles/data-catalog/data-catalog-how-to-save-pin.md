---
title: Zoekopdrachten opslaan en gegevenselementen vastmaken in Azure-gegevenscatalogus
description: How-to-artikelmarkeringsmogelijkheden in Azure Data Catalog voor het opslaan van gegevensbronnen en gegevenselementen voor later gebruik.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976806"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Zoekopdrachten opslaan en gegevenselementen vastmaken in Azure-gegevenscatalogus
## <a name="introduction"></a>Inleiding
Azure Data Catalog biedt mogelijkheden voor het ontdekken van gegevensbronnen. U de catalogus snel zoeken en filteren om gegevensbronnen te vinden en het beoogde doel te begrijpen, waardoor het gemakkelijker wordt om de juiste gegevens voor de taak te vinden.

Maar wat als u regelmatig met dezelfde gegevens moet werken? En wat als u en andere gebruikers regelmatig uw kennis bijdragen aan dezelfde gegevensbronnen in de catalogus? In deze situaties kan het inefficiënt zijn om herhaaldelijk dezelfde zoekopdrachten uit te geven. Dit is waar opgeslagen zoek- en vastgemaakte gegevenselementen kunnen helpen.

## <a name="saved-searches"></a>Opgeslagen zoekopdrachten
Een opgeslagen zoekopdracht in gegevenscatalogus is een herbruikbare zoekdefinitie per gebruiker. U een zoekopdracht definiëren, inclusief zoektermen, tags en andere filters, en deze vervolgens opslaan. U de opgeslagen zoekdefinitie later opnieuw uitvoeren om gegevenselementen die overeenkomen met de zoekcriteria, terug te sturen.

### <a name="create-a-saved-search"></a>Een opgeslagen zoekopdracht maken
Ga als volgt te werk om een opgeslagen zoekopdracht te maken:
1. Klik in de azure-gegevenscatalogusportal in het huidige **zoekvenster** op **Opslaan**. 

    ![Koppeling Huidige zoekinstellingen opslaan](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Voer de zoekcriteria in die u opnieuw wilt gebruiken en klik op **Opslaan**.

    ![Opgeslagen zoekinstellingen huidige zoeknaam](./media/data-catalog-how-to-save-pin/02-name.png)

3. Wanneer u wordt gevraagd, voert u een naam in voor de opgeslagen zoekopdracht. Kies een naam die zinvol is en die de gegevenselementen beschrijft die door de zoekopdracht worden geretourneerd.

### <a name="manage-saved-searches"></a>Opgeslagen zoekopdrachten beheren
Nadat u een of meer zoekopdrachten hebt opgeslagen, wordt een optie **Opgeslagen zoekopdrachten** weergegeven onder het vak **Huidig zoeken.** Wanneer de lijst wordt uitgevouwen, worden alle opgeslagen zoekopdrachten weergegeven.

 ![Gegevenscatalogus - Lijst met opgeslagen zoekopdrachten](./media/data-catalog-how-to-save-pin/03-list.png)

Voer een van de volgende bewerkingen uit:

* Als u een zoekopdracht wilt uitvoeren, selecteert u een opgeslagen zoekopdracht in de lijst.

* Als u een lijst met beheeropties voor een opgeslagen zoekopdracht wilt weergeven, klikt u op de pijl-omlaag naast de zoeknaam.

    ![Opties voor het beheren van opgeslagen zoekopdrachten](./media/data-catalog-how-to-save-pin/04-managing.png)

* Als u een nieuwe naam voor de opgeslagen zoekopdracht wilt invoeren, selecteert u **Naam wijzigen**. De zoekdefinitie wordt niet gewijzigd.

* Als u de opgeslagen zoekopdracht uit uw lijst wilt verwijderen, selecteert u **Verwijderen**en bevestigt u de verwijdering.

* Als u de opgeslagen zoekopdracht wilt markeren als uw standaardzoekopdracht, selecteert u **Opslaan als standaard**. Als u een 'lege' zoekopdracht uitvoert vanaf de startpagina van Azure Data Catalog, wordt uw standaardzoekopdracht uitgevoerd. Bovendien wordt de zoekopdracht die is gemarkeerd als de standaardzoekopdracht boven aan de lijst **Opgeslagen zoekopdrachten** weergegeven.

### <a name="organizational-saved-searches"></a>Door de organisatie opgeslagen zoekopdrachten
Alle gebruikers in uw organisatie kunnen zoekopdrachten opslaan voor eigen gebruik. Beheerders van gegevenscatalogi kunnen ook zoekopdrachten opslaan voor alle gebruikers binnen de organisatie. Wanneer beheerders een zoekopdracht opslaan, krijgen ze een **aandeel binnen de bedrijfsoptie** te zien. Als u deze optie selecteert, wordt de opgeslagen zoekopdracht voor alle gebruikers in de organisatie gedeeld.

 ![Gegevenscatalogus - Door de organisatie opgeslagen zoekopdrachten](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Vastgemaakte gegevenselementen
Met opgeslagen zoekopdrachten u zoekdefinities opslaan en opnieuw gebruiken. De gegevenselementen die door de zoekopdrachten worden geretourneerd, kunnen in de loop van de tijd veranderen naarmate de inhoud van de catalogus verandert. Wanneer u gegevenselementen vastmaakt, u expliciet specifieke gegevenselementen identificeren om ze gemakkelijker toegankelijk te maken zonder dat u een zoekopdracht hoeft te gebruiken.

Het vastmaken van een gegevenselement is eenvoudig. Als u het gegevenselement wilt toevoegen aan uw vastgemaakte lijst, klikt u gewoon op het pictogram van de **pin.** Het pictogram wordt weergegeven in de hoek van de tegel in de tegelweergave en in de meest linkse kolom in de lijstweergave in de Azure Data Catalog-portal.

![Gegevenscatalogus - Het pictogram gegevens-asset-pin](./media/data-catalog-how-to-save-pin/05-pinning.png)

Het losmaken van een gegevensasset is even eenvoudig. Klik op het pictogram **losmaken** om de instelling voor het geselecteerde item in te schakelen.

![Gegevenscatalogus - Het pictogram gegevensasset ontkoppelen](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>De sectie Mijn activa
De startpagina van de datacatalogusportal bevat een sectie **Mijn activa** waarin activa worden weergegeven die van belang zijn voor de huidige gebruiker. Deze sectie bevat zowel vastgemaakte activa als opgeslagen zoekopdrachten.

![De sectie Mijn activa op de startpagina](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Samenvatting
Azure Data Catalog biedt mogelijkheden waarmee u gemakkelijker de gegevensbronnen ontdekken die u nodig hebt, zodat u en andere leden van de organisatie minder tijd kunnen besteden aan het zoeken naar gegevens en meer tijd aan het werken ermee. Opgeslagen zoekopdrachten en vastgemaakte gegevensassets bouwen voort op deze kernmogelijkheden, zodat gebruikers eenvoudig gegevensbronnen kunnen identificeren waarmee ze herhaaldelijk werken.
