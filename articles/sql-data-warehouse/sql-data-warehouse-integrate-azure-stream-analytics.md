---
title: Azure Stream Analytics gebruiken
description: Tips voor het gebruik van Azure Stream Analytics met Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685732"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Azure Stream Analytics gebruiken met SQL Data Warehouse
Azure Stream Analytics is een volledig beheerde service met lage latentie en een Maxi maal beschik bare, schaal bare complexe gebeurtenis verwerking via streaming-gegevens in de Cloud. Lees de [Inleiding tot Azure stream Analytics voor][Introduction to Azure Stream Analytics]meer informatie over de basis principes. U kunt vervolgens leren hoe u een end-to-end-oplossing maakt met Stream Analytics door de zelf studie [aan de slag met Azure stream Analytics][Get started using Azure Stream Analytics] te volgen.

In dit artikel leert u hoe u uw Azure SQL Data Warehouse-Data Base kunt gebruiken als een uitvoer Sink voor uw Stream Analytics taken.

## <a name="prerequisites"></a>Vereisten
Voer eerst de volgende stappen uit in de zelf studie [aan de slag met Azure stream Analytics][Get started using Azure Stream Analytics] .  

1. Een event hub-invoer maken
2. Toepassing voor gebeurtenis Generator configureren en starten
3. Een Stream Analytics-taak inrichten
4. Taak invoer en-query opgeven

Maak vervolgens een Azure SQL Data Warehouse data base

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Taak uitvoer opgeven: Azure SQL Data Warehouse data base
### <a name="step-1"></a>Stap 1
Klik in uw Stream Analytics-taak op **uitvoer** boven aan de pagina en klik vervolgens op **toevoegen**.

### <a name="step-2"></a>Stap 2
Selecteer SQL Database.

### <a name="step-3"></a>Stap 3
Voer de volgende waarden in op de volgende pagina:

* *Uitvoer alias*: Voer een beschrijvende naam in voor deze taak uitvoer.
* *Abonnement*:
  * Als uw SQL Data Warehouse-data base zich in hetzelfde abonnement bevindt als de Stream Analytics taak, selecteert u SQL Database gebruiken uit het huidige abonnement.
  * Als uw data base zich in een ander abonnement bevindt, selecteert u SQL Database van een ander abonnement gebruiken.
* *Data Base*: Geef de naam op van een doel database.
* *Server naam*: Geef de server naam op voor de data base die u zojuist hebt opgegeven. U kunt de Azure Portal gebruiken om dit te vinden.

![][server-name]

* *Gebruikers naam*: Geef de gebruikers naam op van een account met schrijf machtigingen voor de data base.
* *Wacht woord*: Geef het wacht woord op voor het opgegeven gebruikers account.
* *Tabel*: Geef de naam op van de doel tabel in de data base.

![][add-database]

### <a name="step-4"></a>Stap 4
Klik op de knop controleren om deze taak uitvoer toe te voegen en te controleren of Stream Analytics verbinding kan maken met de data base.

Wanneer de verbinding met de data base is geslaagd, ziet u een melding in de portal. U kunt klikken op testen om de verbinding met de data base te testen.

## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van integratie van SQL Data Warehouse][SQL Data Warehouse integration overview]voor een overzicht van de integratie.

Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars][SQL Data Warehouse development overview] voor meer tips voor ontwikkelaars.

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
