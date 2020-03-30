---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78305015"
---
## <a name="configure-the-data-source"></a>De gegevensbron configureren

U voert de volgende stappen uit om Azure Data Explorer te configureren als gegevensbron voor uw dashboardhulpprogramma. We behandelen deze stappen in deze sectie in meer detail:

1. Maak een Azure Active Directory (Azure AD) serviceprincipal. De serviceprincipal wordt door uw dashboardhulpprogramma gebruikt om toegang te krijgen tot de Azure Data Explorer-service.

1. Voeg de azure AD-serviceprincipal toe aan de *kijkersrol* in de Azure Data Explorer-database.

1. Geef de verbindingseigenschappen van uw dashboardhulpprogramma op op basis van informatie van de Azure AD-serviceprincipal en test de verbinding.

### <a name="create-a-service-principal"></a>Een service-principal maken

U de serviceprincipal maken in de [Azure-portal](#azure-portal) of de Azure CLI-opdrachtregelervaring gebruiken. [Azure CLI](#azure-cli) Ongeacht welke methode u gebruikt, na het maken krijgt u waarden voor vier verbindingseigenschappen die u in latere stappen zult gebruiken.

#### <a name="azure-portal"></a>Azure Portal

1. Als u de serviceprincipal wilt maken, volgt u de instructies in de [azure-portaldocumentatie.](/azure/active-directory/develop/howto-create-service-principal-portal)

    1. Wijs in de sectie [De toepassing toewijzen aan een rol](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) een roltype **Reader** toe aan uw Azure Data Explorer-cluster.

    1. Kopieer in de sectie [Waarden voor aanmelden](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) de drie eigenschapswaarden die in de stappen worden behandeld: **Directory-id** (tenant-id), **toepassings-id**en **wachtwoord**.

1. Selecteer in de Azure-portal **Abonnementen** en kopieer vervolgens de id voor het abonnement waarin u de serviceprincipal hebt gemaakt.

    ![Abonnement-ID - portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure-CLI

1. Een service-principal maken. Stel een passend bereik en `reader`een roltype .

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Zie [Een Azure-serviceprincipal maken met Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

1. De opdracht retourneert een resultaatset als volgt. Kopieer de drie eigenschapswaarden: **appID,** **wachtwoord**en **tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Ontvang een lijst met uw abonnementen.

    ```azurecli
    az account list --output table
    ```

    Kopieer de juiste abonnements-ID.

    ![Abonnement-ID - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>De serviceprincipal toevoegen aan de kijkersrol

Nu u een serviceprincipal hebt, voegt u deze toe aan de *kijkersrol* in de Azure Data Explorer-database. U deze taak uitvoeren onder **Machtigingen** in de Azure-portal of onder **Query** met behulp van een beheeropdracht.

#### <a name="azure-portal---permissions"></a>Azure-portal - Machtigingen

1. Ga in de Azure-portal naar uw Azure Data Explorer-cluster.

1. Selecteer in de sectie **Overzicht** de database met de voorbeeldgegevens van StormEvents.

    ![Database selecteren](media/data-explorer-configure-data-source/select-database.png)

1. Selecteer **Machtigingen** **en voeg toe**.

    ![Databasemachtigingen](media/data-explorer-configure-data-source/database-permissions.png)

1. Selecteer **onder Databasemachtigingen toevoegen**de rol **Viewer** en **selecteer principals**.

    ![Databasemachtigingen toevoegen](media/data-explorer-configure-data-source/add-permission.png)

1. Zoek naar de serviceprincipal die u hebt gemaakt. Selecteer de hoofdsom **en selecteer .**

    ![Machtigingen beheren in de Azure-portal](media/data-explorer-configure-data-source/new-principals.png)

1. Selecteer **Opslaan**.

    ![Machtigingen beheren in de Azure-portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Opdracht Beheer - Query

1. Ga in de Azure-portal naar uw Azure Data Explorer-cluster en selecteer **Query**.

    ![Query’s uitvoeren](media/data-explorer-configure-data-source/query.png)

1. Voer de volgende opdracht uit in het queryvenster. Gebruik de toepassings-id en tenant-id van de Azure-portal of CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    De opdracht retourneert een resultaatset als volgt. In dit voorbeeld is de eerste rij voor een bestaande gebruiker in de database en de tweede rij is voor de serviceprincipal die net is toegevoegd.

    ![Resultaatset](media/data-explorer-configure-data-source/result-set.png)
