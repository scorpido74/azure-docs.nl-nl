---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523051"
---
## <a name="select-an-ingestion-type"></a>Een opnametype selecteren

Selecteer **bij Opnametype**een van de volgende opties:
   * **vanuit opslag** - voeg in het **veld Koppeling naar opslag** de URL van het opslagaccount toe. Gebruik [blob sas-URL](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) voor privéopslagaccounts.
   
      ![Inname met één klik uit de opslag](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **uit bestand** - selecteer **Bladeren** om het bestand te zoeken of sleep het bestand naar het veld.
  
      ![Opname met één klik uit het bestand](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **van container** - voeg in het **veld Koppeling naar opslag** de [SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) van de container toe en voer optioneel de steekproefgrootte in.

      ![Inname met één klik uit de container](media/data-explorer-one-click-ingestion-types/from-container.png)

  Er verschijnt een voorbeeld van de gegevens. Als u wilt, u deze filteren om alleen bestanden weer te geven die eindigen met specifieke tekens. Wanneer u de filters aanpast, wordt het voorbeeld automatisch bijgewerkt.
  
  U bijvoorbeeld filteren op alle bestanden die beginnen met het woord *gegevens* en eindigen met een *.csv.gz-extensie.*

  ![Innamefilter met één klik](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
