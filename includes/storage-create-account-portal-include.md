---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76759842"
---
Als u een v2-opslagaccount voor algemeen gebruik wilt maken in de Azure Portal, volgt u deze stappen:

1. Selecteer **Alle services** in het menu van Azure Portal. Typ in de lijst met resources **Opslagaccounts**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.
2. Kies in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.
3. Selecteer het abonnement waarin u het opslagaccount wilt maken.
4. Selecteer **Nieuwe maken** onder het veld **Resourcegroep**. Voer een naam in voor de nieuwe resourcegroep, zoals in de volgende afbeelding wordt weergegeven.

    ![Schermafbeelding van het maken van een resourcegroep in de portal](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
6. Selecteer een locatie voor uw opslagaccount of gebruik de standaardlocatie.
7. Laat deze velden ingesteld staan op de standaardwaarden:

   |Veld  |Waarde  |
   |---------|---------|
   |Implementatiemodel     |Resource Manager         |
   |Prestaties     |Standard         |
   |Soort account     |StorageV2 (general-purpose v2)         |
   |Replicatie     |Geografisch redundante opslag met leestoegang (RA-GRS)         |
   |Toegangslaag     |Warm         |

8. Als u [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)wilt gebruiken, kiest u het tabblad **Geavanceerd** en stelt u **hiërarchische naamruimte** in **op Ingeschakeld**.
9. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
10. Selecteer **Maken**.

Zie [Azure-opslagaccountoverzicht](https://docs.microsoft.com/azure/storage/common/storage-account-overview) voor meer informatie over typen opslagaccounts en andere opslagaccountinstellingen. Zie overzicht van Azure [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)voor meer informatie over resourcegroepen. 
