---
title: Importeren uit Azure SQL Database
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module gegevens importeren in Azure Machine Learning Visual Interface voor het ophalen van gegevens uit een Azure SQL Database.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694608"
---
# <a name="import-from-azure-sql-database"></a>Importeren uit Azure SQL Database

In dit artikel wordt beschreven hoe u de module [gegevens importeren](import-data.md) in azure machine learning Visual Interface kunt gebruiken om gegevens op te halen uit een Azure SQL database.  

Als u gegevens uit een Data Base wilt importeren, moet u zowel de naam van de server als de naam van de data base opgeven, en een SQL-instructie die de tabel, weer gave of query definieert.  

Over het algemeen is het opslaan van gegevens in azure-data bases duurder dan het gebruik van tabellen of blobs in Azure. Er kunnen ook beperkingen gelden voor de hoeveelheid gegevens die u kunt opslaan in een Data Base, afhankelijk van het type abonnement. Er zijn echter geen transactie kosten voor SQL Azure data base, zodat de optie ideaal is voor snelle toegang tot kleinere hoeveel heden veelgebruikte informatie, zoals tabellen voor gegevens opzoeken of gegevens woordenboeken.

Het opslaan van gegevens in een Azure-data base verdient ook de voor keur als u gegevens moet kunnen filteren voordat u deze kunt lezen, of als u voor spellingen of metrieken wilt opslaan in de-Data Base voor rapportage.

## <a name="how-to-import-data-from-azure-sql-database"></a>Gegevens importeren uit Azure SQL Database

1. Voeg de module [gegevens importeren](import-data.md) toe aan de pijp lijn. U kunt deze module vinden in de visuele interface, in de categorie gegevens invoer en uitvoer.

1. Selecteer **Azure SQL database**voor **gegevens bron**.

1. Stel de volgende opties in die specifiek zijn voor Azure SQL Database.

    **Database server naam**: Typ de naam van de server die door Azure wordt gegenereerd. Normaal gesp roken heeft het de vorm `<generated_identifier>.database.windows.net`.

    **Database naam**: Typ de naam van een bestaande Data Base op de server die u hebt opgegeven.

    **Account naam Server gebruiker**: Typ de gebruikers naam van een account met toegangs machtigingen voor de data base.

    **Wacht woord Server gebruikers account**: Geef het wacht woord op voor het opgegeven gebruikers account.

    **Database query**: Typ of plak een SQL-instructie met een beschrijving van de gegevens die u wilt lezen. Valideer de SQL-instructie altijd en controleer de query resultaten vooraf, met behulp van een hulp programma zoals Visual Studio Server Explorer of SQL Server Data Tools.

1. Als de gegevensset die u in Azure Machine Learning leest, niet naar verwachting van de uitvoering van de pijp lijn wordt gewijzigd, selecteert u de optie **in cache opgeslagen resultaten gebruiken** .

    Als deze is geselecteerd en er geen andere wijzigingen zijn aangebracht in module parameters, worden de gegevens door de pijp lijn geladen wanneer de module voor het eerst wordt uitgevoerd en wordt vervolgens een versie van de gegevensset in de cache gebruikt.

    Schakel deze optie uit als u de gegevensset opnieuw wilt laden voor elke herhaling van de pijp lijn. Telkens wanneer alle para meters in [import gegevens](import-data.md)worden gewijzigd, wordt de gegevensset opnieuw geladen vanuit de bron.

1. Voer de pijplijn uit.

    Als [import gegevens](import-data.md) de gegevens in de visuele interface laadt, kan ook een impliciete type conversie worden uitgevoerd, afhankelijk van de gebruikte gegevens typen in de bron database.

## <a name="results"></a>Resultaten

Wanneer het importeren is voltooid, klikt u op de gegevensset uitvoer en selecteert u **visualiseren** om te zien of de gegevens zijn geïmporteerd.

Desgewenst kunt u de gegevensset en de meta gegevens wijzigen met behulp van de hulpprogram ma's in de visuele interface:

- Gebruik [meta gegevens bewerken](edit-metadata.md) om kolom namen te wijzigen, een kolom te converteren naar een ander gegevens type of om aan te geven welke kolommen labels of functies zijn.

- Gebruik [select columns in dataset](select-columns-in-dataset.md) om een subset van kolommen te selecteren.

- Gebruik [partitie en voor beeld](partition-and-sample.md) om de gegevensset te scheiden op basis van criteria, of haal de bovenste n rijen op.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
