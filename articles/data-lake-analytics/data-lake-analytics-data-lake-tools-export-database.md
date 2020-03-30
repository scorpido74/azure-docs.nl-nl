---
title: "U-SQL-database exporteren: Azure Data Lake-hulpprogramma's voor Visual Studio"
description: Meer informatie over het gebruik van Azure Data Lake Tools voor Visual Studio om een U-SQL-database te exporteren en deze automatisch te importeren naar een lokaal account.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315798"
---
# <a name="export-a-u-sql-database"></a>Een U-SQL-database exporteren

In dit artikel vindt u meer informatie over het gebruik van [Azure Data Lake Tools voor Visual Studio](https://aka.ms/adltoolsvs) om een U-SQL-database te exporteren als één U-SQL-script en gedownloade resources. U de geëxporteerde database importeren naar een lokaal account in hetzelfde proces.

Klanten onderhouden meestal meerdere omgevingen voor ontwikkeling, test en productie. Deze omgevingen worden gehost op zowel een lokaal account, op de lokale computer van een ontwikkelaar als in een Azure Data Lake Analytics-account in Azure. 

Wanneer u U-SQL-query's ontwikkelt en afstemt in ontwikkel- en testomgevingen, moeten ontwikkelaars hun werk vaak opnieuw maken in een productiedatabase. De wizard Databaseexporteren helpt dit proces te versnellen. Met behulp van de wizard kunnen ontwikkelaars de bestaande databaseomgeving en voorbeeldgegevens klonen naar andere Data Lake Analytics-accounts.

## <a name="export-steps"></a>Exportstappen

### <a name="step-1-export-the-database-in-server-explorer"></a>Stap 1: De database exporteren in Server Explorer

Alle Data Lake Analytics-accounts waarvoor u machtigingen hebt, worden weergegeven in Server Explorer. Ga als u de database exporteert:

1. Vouw in Server Explorer het account uit dat de database bevat die u wilt exporteren.
2. Klik met de rechtermuisknop op de database en selecteer **Exporteren**. 
   
    ![Server Explorer - Een database exporteren](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Als de menuoptie **Exporteren** niet beschikbaar is, moet u [het hulpprogramma bijwerken naar de laatste release.](https://aka.ms/adltoolsvs)

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Stap 2: De objecten configureren die u wilt exporteren

Als u slechts een klein deel van een grote database nodig hebt, u een subset configureren van objecten die u wilt exporteren in de wizard Exporteren. 

De exportactie wordt voltooid door een U-SQL-taak uit te voeren. Daarom brengt exporteren vanuit een Azure-account enige kosten met zich mee.

![Wizard Databaseexporteren - Exportobjecten selecteren](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Stap 3: De lijst met objecten en andere configuraties controleren

In deze stap u de geselecteerde objecten verifiëren in het **vak Objectlijst exporteren.** Als er fouten zijn, selecteert u **Vorige** om terug te gaan en de objecten die u wilt exporteren correct te configureren.

U ook andere instellingen voor het exportdoel configureren. Configuratiebeschrijvingen worden weergegeven in de volgende tabel:

|Configuratie|Beschrijving|
|-------------|-----------|
|Bestemmingsnaam|Deze naam geeft aan waar u de geëxporteerde databasebronnen wilt opslaan. Voorbeelden zijn samenstellingen, aanvullende bestanden en voorbeeldgegevens. Er wordt een map met deze naam gemaakt onder uw lokale hoofdmap voor gegevens.|
|Projectmap|Dit pad definieert waar u het geëxporteerde U-SQL-script wilt opslaan. Alle definities van databaseobjecten worden op deze locatie opgeslagen.|
|Alleen schema|Als u deze optie selecteert, worden alleen databasedefinities en resources (zoals verzamelingen en aanvullende bestanden) geëxporteerd.|
|Schema en gegevens|Als u deze optie selecteert, worden databasedefinities, resources en gegevens geëxporteerd. De bovenste N-rijen tabellen worden geëxporteerd.|
|Automatisch importeren in lokale database|Als u deze optie selecteert, wordt de geëxporteerde database automatisch geïmporteerd naar uw lokale database wanneer het exporteren is voltooid.|

![Wizard Databaseexporteren - Lijst met objecten exporteren en andere configuraties](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Stap 4: Controleer de exportresultaten

Wanneer het exporteren is voltooid, u de geëxporteerde resultaten bekijken in het logboekvenster in de wizard. In het volgende voorbeeld ziet u hoe u geëxporteerde U-SQL-script- en databasebronnen vinden, waaronder verzamelingen, aanvullende bestanden en voorbeeldgegevens:

![Wizard Databaseexporteren - Resultaten exporteren](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>De geëxporteerde database importeren naar een lokaal account

De handigste manier om de geëxporteerde database te importeren is door het selectievakje **Automatisch importeren in lokale database** in te schakelen tijdens het exporteren in stap 3. Als u dit selectievakje niet hebt geselectievakje, zoekt u eerst het geëxporteerde U-SQL-script in het exportlogboek. Voer vervolgens het U-SQL-script lokaal uit om de database te importeren naar uw lokale account.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>De geëxporteerde database importeren in een Data Lake Analytics-account

Ga als u de database wilt importeren naar een ander Data Lake Analytics-account:

1. Upload de geëxporteerde resources, inclusief assemblages, extra bestanden en voorbeeldgegevens, naar het standaard Azure Data Lake Store-account van het Data Lake Analytics-account waarnaar u wilt importeren. U de geëxporteerde bronmap vinden onder de hoofdmap lokale gegevens. Upload de hele map naar de hoofdmap van het standaard Data Lake Store-account.
2. Wanneer het uploaden is voltooid, dient u het geëxporteerde U-SQL-script in bij het Data Lake Analytics-account waarnaar u de database wilt importeren.

## <a name="known-limitations"></a>Bekende beperkingen

Als u momenteel de optie **Schema en gegevens** selecteert in stap 3, voert het hulpprogramma een U-SQL-taak uit om de gegevens te exporteren die zijn opgeslagen in tabellen. Hierdoor kan het proces voor het exporteren van gegevens traag zijn en u kosten maken. 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over U-SQL-databases](/u-sql/data-definition-language-ddl-statements) 
* [U-SQL-taken testen en controleren op fouten met behulp van lokale uitvoering en de Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)


