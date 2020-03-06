---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305015"
---
## <a name="configure-the-data-source"></a>De gegevens bron configureren

U voert de volgende stappen uit om Azure Data Explorer te configureren als een gegevens bron voor uw dash board-hulp programma. Deze stappen worden in deze sectie nader besproken:

1. Maak een service-principal voor Azure Active Directory (Azure AD). De service-principal wordt gebruikt door uw dash board om toegang te krijgen tot de Azure Data Explorer-service.

1. Voeg de Azure AD-Service-Principal toe aan de rol *viewers* in de Azure Data Explorer-data base.

1. Geef de verbindings eigenschappen van uw dash board-hulp programma op op basis van informatie van de Azure AD-Service-Principal en test vervolgens de verbinding.

### <a name="create-a-service-principal"></a>Een service-principal maken

U kunt de service-principal maken in het [Azure Portal](#azure-portal) of met behulp van de [Azure cli](#azure-cli) -opdracht regel ervaring. Ongeacht de methode die u gebruikt, krijgt u na het maken van de waarden voor vier verbindings eigenschappen die u in latere stappen gaat gebruiken.

#### <a name="azure-portal"></a>Azure-portal

1. Volg de instructies in de [documentatie van Azure Portal](/azure/active-directory/develop/howto-create-service-principal-portal)om de service-principal te maken.

    1. Wijs in de sectie [toepassing aan een rol toewijzen](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) een roltype van het type **lezer** toe aan uw Azure Data Explorer-cluster.

    1. In de sectie [waarden ophalen voor aanmelden](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) kopieert u de drie eigenschaps waarden die zijn opgenomen in de stappen: **Directory-ID** (Tenant-ID), **toepassings-id**en **wacht woord**.

1. In de Azure Portal selecteert u **abonnementen** en kopieert u vervolgens de id voor het abonnement waarin u de Service-Principal hebt gemaakt.

    ![Abonnements-ID-Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Een service-principal maken. Stel een geschikt bereik en een roltype van `reader`in.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Zie [een Azure-service-principal maken met Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

1. De opdracht retourneert een resultaatset als de volgende. Kopieer de drie eigenschaps waarden: **appID**, **Password**en **Tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Een lijst met uw abonnementen ophalen.

    ```azurecli
    az account list --output table
    ```

    Kopieer de juiste abonnements-ID.

    ![Abonnements-ID-CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>De Service-Principal toevoegen aan de rol viewers

Nu u een Service-Principal hebt, voegt u deze toe aan de rol *kijkers* in de Azure Data Explorer-data base. U kunt deze taak uitvoeren onder **machtigingen** in de Azure Portal, of onder **query** met behulp van een beheer opdracht.

#### <a name="azure-portal---permissions"></a>Azure Portal-machtigingen

1. Ga in het Azure Portal naar uw Azure Data Explorer-cluster.

1. Selecteer in de sectie **overzicht** de data base met de StormEvents-voorbeeld gegevens.

    ![Data base selecteren](media/data-explorer-configure-data-source/select-database.png)

1. Selecteer **machtigingen** en vervolgens **toevoegen**.

    ![Database machtigingen](media/data-explorer-configure-data-source/database-permissions.png)

1. Selecteer onder **database machtigingen toevoegen**de rol van de **kijker** en **Selecteer vervolgens principals**.

    ![Database machtigingen toevoegen](media/data-explorer-configure-data-source/add-permission.png)

1. Zoek naar de service-principal die u hebt gemaakt. Selecteer de principal en **Selecteer**.

    ![Machtigingen beheren in de Azure Portal](media/data-explorer-configure-data-source/new-principals.png)

1. Selecteer **Opslaan**.

    ![Machtigingen beheren in de Azure Portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Beheer opdracht-query

1. Ga in het Azure Portal naar uw Azure Data Explorer-cluster en selecteer **query**.

    ![Query's uitvoeren](media/data-explorer-configure-data-source/query.png)

1. Voer de volgende opdracht uit in het query-venster. Gebruik de toepassings-ID en Tenant-ID van de Azure Portal of CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    De opdracht retourneert een resultaatset als de volgende. In dit voor beeld is de eerste rij voor een bestaande gebruiker in de data base en de tweede rij voor de service-principal die zojuist is toegevoegd.

    ![Resultatenset](media/data-explorer-configure-data-source/result-set.png)
