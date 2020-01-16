---
title: Database machtigingen beheren in azure Data Explorer
description: In dit artikel worden toegangs beheer op basis van rollen voor data bases en tabellen in azure Data Explorer beschreven.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030101"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Machtigingen voor Azure Data Explorer-data base beheren

Met Azure Data Explorer kunt u de toegang tot data bases en tabellen beheren met behulp *van een op rollen gebaseerd toegangs beheer* model. Onder dit model worden *principals* (gebruikers, groepen en apps) toegewezen aan *rollen*. Principals hebben toegang tot bronnen op basis van de rollen die ze hebben toegewezen.

In dit artikel worden de beschik bare rollen beschreven en wordt uitgelegd hoe u principals aan deze rollen kunt toewijzen met behulp van de Azure Portal-en Azure Data Explorer management-opdrachten.

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Azure Data Explorer heeft de volgende rollen:

|Rol                       |Machtigingen                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Database beheerder             |Kan iets doen binnen het bereik van een bepaalde data base.|
|Database gebruiker              |Kan alle gegevens en meta gegevens in de-data base lezen. Daarnaast kunnen ze tabellen maken (die de tabel beheerder voor die tabel worden) en functies in de-data base.|
|Database Viewer            |Kan alle gegevens en meta gegevens in de-data base lezen.|
|Database opname          |Kan gegevens opnemen naar alle bestaande tabellen in de data base, maar geen query uitvoeren op de gegevens.|
|Database monitor           |Kan '. show... ' uitvoeren opdrachten in de context van de data base en de onderliggende entiteiten.|
|Tabel beheerder                |Kan alles in het bereik van een bepaalde tabel doen. |
|Tabel opname             |Kan gegevens opnemen in het bereik van een bepaalde tabel, maar geen query uitvoeren op de gegevens.|

## <a name="manage-permissions-in-the-azure-portal"></a>Machtigingen beheren in de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Data Explorer-cluster.

1. Selecteer in de sectie **overzicht** de data base waarvoor u de machtigingen wilt beheren.

    ![Database selecteren](media/manage-database-permissions/select-database.png)

1. Selecteer **machtigingen** en vervolgens **toevoegen**.

    ![Database machtigingen](media/manage-database-permissions/database-permissions.png)

1. Selecteer onder **database machtigingen toevoegen**de rol waaraan u de principal wilt toewijzen en **Selecteer vervolgens principals**.

    ![Database machtigingen toevoegen](media/manage-database-permissions/add-permission.png)

1. Zoek de principal op, Selecteer deze en **Selecteer**.

    ![Machtigingen beheren in de Azure Portal](media/manage-database-permissions/new-principals.png)

1. Selecteer **Opslaan**.

    ![Machtigingen beheren in de Azure Portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Machtigingen beheren met beheer opdrachten

1. Meld u aan bij [https://dataexplorer.azure.com](https://dataexplorer.azure.com)en voeg uw cluster toe als dit nog niet beschikbaar is.

1. Selecteer de juiste data base in het linkerdeel venster.

1. Gebruik de `.add` opdracht om principals toe te wijzen aan rollen: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Als u een gebruiker wilt toevoegen aan de gebruikersrol data base, voert u de volgende opdracht uit en vervangt u de naam van de data base en de gebruiker.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    De uitvoer van de opdracht toont de lijst met bestaande gebruikers en de rollen waaraan ze zijn toegewezen in de-data base.
    
    Voor voor beelden met betrekking tot Azure Active Directory en het Kusto-autorisatie model raadpleegt u [principes en id-providers](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Volgende stappen

[Query's schrijven](write-queries.md)
