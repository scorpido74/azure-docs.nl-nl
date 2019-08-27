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
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020177"
---
U kunt nu gegevens aan uw nieuwe container toevoegen met behulp van Data Explorer.

1. Vouw vanuit het **Data Explorer**de Data Base **taken** uit, vouw de container **items** uit. Selecteer **items**en selecteer vervolgens **Nieuw item**.

   ![Nieuwe documenten maken in Data Explorer in de Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
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

3. Wanneer u de JSON hebt toegevoegd aan het tabblad **documenten** , selecteert u **Opslaan**.

    ![Kopieer in JSON-gegevens en selecteer Opslaan in Data Explorer in het Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Maak nog één document en sla dit op. In het document voegt u een unieke waarde toe voor de eigenschap `id`. Wijzig de andere eigenschappen naar eigen inzicht. De nieuwe documenten kunnen elke gewenste structuur hebben, omdat in Azure Cosmos DB uw gegevens geen schema krijgen opgelegd.