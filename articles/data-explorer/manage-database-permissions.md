---
title: Databasemachtigingen beheren in Azure Data Explorer
description: In dit artikel worden op rollen gebaseerde toegangsbesturingselementen voor databases en tabellen in Azure Data Explorer beschreven.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030101"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Machtigingen voor Azure Data Explorer-database beheren

Met Azure Data Explorer u de toegang tot databases en tabellen beheren met behulp van een *op rollen gebaseerd toegangscontrolemodel.* Onder dit model worden *principals* (gebruikers, groepen en apps) toegewezen aan *rollen.* Opdrachtgevers hebben toegang tot bronnen op basis van de toegewezen rollen.

In dit artikel worden de beschikbare rollen beschreven en hoe u principals aan die rollen toewijzen met behulp van de beheeropdrachten Azure Portal en Azure Data Explorer.

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Azure Data Explorer heeft de volgende rollen:

|Rol                       |Machtigingen                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Databasebeheerder             |Kan alles doen in het bereik van een bepaalde database.|
|Databasegebruiker              |Kan alle gegevens en metadata in de database lezen. Bovendien kunnen ze tabellen maken (de tabelbeheerder voor die tabel worden) en functies in de database.|
|Databaseviewer            |Kan alle gegevens en metadata in de database lezen.|
|Database-inname          |Kan gegevens opnemen naar alle bestaande tabellen in de database, maar de gegevens niet opvragen.|
|Databasemonitor           |Kan uitvoeren '.show ...' opdrachten in de context van de database en de onderliggende entiteiten.|
|Tabelbeheerder                |Kan alles doen in het toepassingsgebied van een bepaalde tabel. |
|Tafelinnameer             |Kan gegevens opnemen in het bereik van een bepaalde tabel, maar de gegevens niet opvragen.|

## <a name="manage-permissions-in-the-azure-portal"></a>Machtigingen beheren in de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Data Explorer-cluster.

1. Selecteer in de sectie **Overzicht** de database waar u machtigingen wilt beheren.

    ![Database selecteren](media/manage-database-permissions/select-database.png)

1. Selecteer **Machtigingen** **en voeg toe**.

    ![Databasemachtigingen](media/manage-database-permissions/database-permissions.png)

1. Selecteer **onder Databasemachtigingen toevoegen**de rol waaraan u de hoofdsom wilt toewijzen en selecteer **principals**.

    ![Databasemachtigingen toevoegen](media/manage-database-permissions/add-permission.png)

1. Zoek de hoofdsom op, selecteer deze en **selecteer .**

    ![Machtigingen beheren in de Azure-portal](media/manage-database-permissions/new-principals.png)

1. Selecteer **Opslaan**.

    ![Machtigingen beheren in de Azure-portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Machtigingen beheren met beheeropdrachten

1. Meld u [https://dataexplorer.azure.com](https://dataexplorer.azure.com)aan bij en voeg uw cluster toe als het nog niet beschikbaar is.

1. Selecteer in het linkerdeelvenster de juiste database.

1. Gebruik `.add` de opdracht om principals `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`toe te wijzen aan rollen: . Als u een gebruiker wilt toevoegen aan de gebruikersrol database, voert u de volgende opdracht uit, waarbij de naam en gebruiker van uw database worden vervangen.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    De uitvoer van de opdracht toont de lijst met bestaande gebruikers en de rollen waaraan ze zijn toegewezen in de database.
    
    Zie [Principles and Identity Providers](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers) voor voorbeelden met betrekking tot Azure Active Directory en het Kusto-autorisatiemodel

## <a name="next-steps"></a>Volgende stappen

[Query's schrijven](write-queries.md)
