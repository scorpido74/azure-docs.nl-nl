---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 9bc5be37f3892186233fac197c08066dbfacb43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77780631"
---
U nu het hulpprogramma Gegevensverkenner in de Azure-portal gebruiken om een database en container te maken. 

1. Selecteer Nieuwe**container** **van Data Explorer** > . 
    
    Het gebied **Container toevoegen** wordt helemaal rechts weergegeven, het kan zijn dat u naar rechts moet scrollen om het te zien.

    ![Azure Portal Data Explorer, deelvenster Container toevoegen](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Voer op de pagina **Container toevoegen** de instellingen voor de nieuwe container in.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Taken|Voer *Taken* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Controleer de optie **Voorhet overslaginrichten** van de database, hiermee u de overslag die is ingericht met de database delen over alle containers in de database. Deze optie helpt ook bij kostenbesparingen. |
    |**Doorvoer**|400|Laat de doorvoer op 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *Items in* als de naam voor uw nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| In het voorbeeld dat in dit artikel wordt beschreven, wordt */categorie* als partitiesleutel gebruikt.|
    
    Naast de voorgaande instellingen u optioneel **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een uniek sleutelbeleid te maken tijdens het maken van een container, zorgt u voor de uniciteit van een of meer waarden per partitiesleutel. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](../articles/cosmos-db/unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.
    
    Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.