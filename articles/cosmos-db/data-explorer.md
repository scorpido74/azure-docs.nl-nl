---
title: Azure Cosmos DB explorer gebruiken om uw gegevens te beheren
description: Azure Cosmos DB explorer is een zelfstandige webconsole interface waarmee u de gegevens die zijn opgeslagen in Azure Cosmos DB weergeven en beheren.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096816"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Werken met gegevens in Azure Cosmos Explorer 

Azure Cosmos DB explorer is een zelfstandige webconsole interface waarmee u de gegevens die zijn opgeslagen in Azure Cosmos DB weergeven en beheren. Azure Cosmos DB Explorer is gelijk aan het bestaande **Data Explorer** tabblad dat beschikbaar is in azure Portal wanneer u een Azure Cosmos DB-account maakt. De belangrijke voordelen van Azure Cosmos DB explorer ten opzichte van de bestaande Data explorer zijn:

* U hebt een volledig scherm-onroerend goed om uw gegevens, uitvoeren van query's, opgeslagen procedures, triggers weer te geven en de resultaten ervan bekijken.  

* U kunt tijdelijk of permanent lees- of lees-/ schrijftoegang opgeven aan uw databaseaccount en de verzamelingen aan andere gebruikers die geen toegang tot Azure portal of een abonnement hebben.  

* U kunt de resultaten van de query delen met andere gebruikers die geen toegang tot Azure portal of een abonnement hebben.  

## <a name="access-azure-cosmos-db-explorer"></a>Toegang tot Azure Cosmos DB explorer

1. Meld u aan bij [Azure Portal](https://portal.azure.com/). 

2. Ga vanuit **alle resources**naar uw Azure Cosmos DB-account, selecteer sleutels en kopieer de **primaire verbindings reeks**.  

3. Ga naar https://cosmos.azure.com/, plak de connection string en selecteer **verbinding maken**. Met behulp van de verbindingsreeks, kunt u toegang tot de Azure Cosmos DB explorer zonder een tijdslimiet.  

   Als u wilt dat voor andere gebruikers tijdelijk toegang tot uw Azure Cosmos DB-account, kunt u dit doen met behulp van de lezen / schrijven en lezen toegangs-URL's. 

4. Open de Blade **Data Explorer** en selecteer **Open volledige scherm**. In het pop-upvenster kunt u twee toegangs-Url's bekijken: **lezen/schrijven** en **lezen**. Deze URL's kunnen u uw Azure Cosmos DB-account tijdelijk delen met andere gebruikers. Toegang tot het account verloopt binnen 24 uur, waarmee u verbinding maken kunt met behulp van een nieuw toegangs-URL of de verbindingsreeks. 

   **Lezen-schrijven** : wanneer u de lees-schrijf-URL met andere gebruikers deelt, kunnen ze de data bases, verzamelingen, query's en andere resources die zijn gekoppeld aan dit specifieke account weer geven en wijzigen.

   **Lezen** : wanneer u de alleen-lezen URL deelt met andere gebruikers, kunnen ze de data bases, verzamelingen, query's en andere resources weer geven die zijn gekoppeld aan dit specifieke account. Bijvoorbeeld, als u delen van resultaten van een query met uw collega's die geen toegang hebt tot Azure portal of uw Azure Cosmos DB-account wilt, kunt u opgeven ze met deze URL.

   Kies het type toegang waarmee u het account wilt openen en klik op **openen**. Nadat u de Verkenner openen, is de ervaring is hetzelfde als u had met het tabblad Data Explorer in Azure portal.   

   ![Open Azure Cosmos DB explorer](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Bekende problemen

Momenteel is de ervaring voor het **openen van volledig scherm** , waarmee u tijdelijke lees-/schrijftoegang of lees toegang kunt delen, maar nog niet wordt ondersteund voor Azure Cosmos DB Gremlin-en Table-API-accounts. U kunt nog steeds uw Gremlin en Table-API-accounts weergeven door de verbindingsreeks wordt doorgegeven aan Azure Cosmos DB Explorer. 

Het weer geven van documenten die een UUID bevatten, wordt momenteel niet ondersteund in Data Explorer. Dit heeft geen invloed op het laden van verzamelingen, waarbij alleen afzonderlijke documenten of query's worden weer gegeven die deze documenten bevatten. Om deze documenten weer te geven en te beheren, moeten gebruikers het hulp programma blijven gebruiken dat oorspronkelijk werd gebruikt om deze documenten te maken.

## <a name="next-steps"></a>Volgende stappen
Nu dat u hebt geleerd hoe u aan de slag met Azure Cosmos DB explorer voor het beheren van uw gegevens, u kunt volgende:

* Begin met het definiëren van [query's](sql-api-query-reference.md) met behulp van de SQL-syntaxis en het [Program meren](stored-procedures-triggers-udfs.md) van de server aan de hand van opgeslagen procedures, udf's en triggers. 
