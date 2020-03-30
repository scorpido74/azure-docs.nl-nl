---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70020177"
---
U nu gegevens toevoegen aan uw nieuwe container met Behulp van Data Explorer.

1. Vouw in de **gegevensverkenner**de database **Taken** uit en vouw de container **Items** uit. Selecteer **Objecten**en selecteer vervolgens **Nieuw object**.

   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Voeg nu een document toe aan de container met de volgende structuur.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Nadat u de json aan het tabblad **Documenten** hebt toegevoegd, selecteert u **Opslaan**.

    ![Kopiëren in json-gegevens en selecteer Opslaan in Gegevensverkenner in de Azure-portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Maak nog één document en sla dit op. In het document voegt u een unieke waarde toe voor de eigenschap `id`. Wijzig de andere eigenschappen naar eigen inzicht. De nieuwe documenten kunnen elke gewenste structuur hebben, omdat in Azure Cosmos DB uw gegevens geen schema krijgen opgelegd.