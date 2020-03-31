---
title: Blob-uitvoer verifiëren met Managed Identity Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Azure Blob-opslaguitvoer.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129969"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Azure Blob Storage-uitvoer

[Managed Identity-verificatie](../active-directory/managed-identities-azure-resources/overview.md) voor uitvoer naar Azure Blob-opslag geeft Stream Analytics-taken directe toegang tot een opslagaccount in plaats van een verbindingstekenreeks te gebruiken. Naast verbeterde beveiliging u met deze functie ook gegevens schrijven naar een opslagaccount in een Virtual Network (VNET) binnen Azure.

In dit artikel ziet u hoe u Managed Identity inschakelt voor de Blob-uitvoer(s) van een Stream Analytics-taak via de Azure-portal en via een Azure Resource Manager-implementatie.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>De functie Stream Analytics maken met de Azure-portal

1. Maak een nieuwe Stream Analytics-taak of open een bestaande taak in de Azure-portal. Selecteer **Beheerde identiteit** onder **Configureren**in de menubalk aan de linkerkant van het scherm. Controleer of 'Beheerde identiteit met systeemtoegewezen identiteit' is geselecteerd en klik op de knop **Opslaan** onder in het scherm.

   ![Beheerde identiteit van Stream Analytics configureren](./media/common/stream-analytics-enable-managed-identity.png)

2. Selecteer in het venster uitvoereigenschappen van de uitvoersink azure blob-opslag de vervolgkeuzelijst Verificatiemodus en kies **Beheerde identiteit**. Zie [Uitvoer van Azure Stream Analytics begrijpen](./stream-analytics-define-outputs.md)voor informatie over de andere uitvoereigenschappen. Klik op **Opslaan**als u klaar bent.

   ![Azure Blob-opslaguitvoer configureren](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Nu de taak is gemaakt, raadpleegt u de taak Geef de Stream Analytics toegang tot het gedeelte [van uw opslagaccount](#give-the-stream-analytics-job-access-to-your-storage-account) in dit artikel.

## <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Met Azure Resource Manager u de implementatie van uw Stream Analytics-taak volledig automatiseren. U Resource Manager-sjablonen implementeren met Azure PowerShell of Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). In de onderstaande voorbeelden wordt gebruik gemaakt van de Azure CLI.


1. U een **Microsoft.StreamAnalytics/streamingjobs-bron** met een beheerde identiteit maken door de volgende eigenschap op te nemen in het resourcegedeelte van uw resourcemanagersjabloon:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Met deze eigenschap moet Azure Resource Manager de identiteit voor uw Stream Analytics-taak maken en beheren. Hieronder vindt u een voorbeeld sjabloon Resource Manager waarmee een streamanalytics-taak met Beheerde identiteit is ingeschakeld en een klodderuitvoerput die beheerde identiteit gebruikt:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    De bovenstaande taak kan worden geïmplementeerd in de **voorbeeldgroep Voorbeeldgroep** resourcegroep met de opdracht Onder Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Nadat de taak is gemaakt, u Azure Resource Manager gebruiken om de volledige definitie van de taak op te halen.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    De bovenstaande opdracht retourneert een antwoord zoals het onderstaande:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Neem nota van de **principalId** van de definitie van de taak, die de beheerde identiteit van uw taak identificeert in Azure Active Directory en wordt gebruikt in de volgende stap om de Stream Analytics-taak toegang te verlenen tot het opslagaccount.

3. Nu de taak is gemaakt, raadpleegt u de taak Geef de Stream Analytics toegang tot het gedeelte [van uw opslagaccount](#give-the-stream-analytics-job-access-to-your-storage-account) in dit artikel.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Geef de Stream Analytics-taak toegang tot uw opslagaccount

Er zijn twee toegangsniveaus die je kiezen om je Stream Analytics-taak te geven:

1. **Toegang op containerniveau:** deze optie geeft de taak toegang tot een specifieke bestaande container.
2. **Toegang op accountniveau:** deze optie geeft de taak algemene toegang tot het opslagaccount, inclusief de mogelijkheid om nieuwe containers te maken.

Tenzij u de taak nodig hebt om containers namens u te maken, moet u **toegang op containerniveau** kiezen, omdat deze optie de taak het vereiste minimumniveau van toegang verleent. Beide opties worden hieronder uitgelegd voor de Azure-portal en de opdrachtregel.

### <a name="grant-access-via-the-azure-portal"></a>Toegang verlenen via de Azure-portal

#### <a name="container-level-access"></a>Toegang tot containerniveau

1. Navigeer naar het configuratiedeelvenster van de container in uw opslagaccount.

2. Selecteer **Toegangsbeheer (IAM)** aan de linkerkant.

3. Klik onder de sectie Een roltoewijzing toevoegen op **Toevoegen**.

4. In het deelvenster Roltoewijzing:

    1. De **rol instellen op** 'Opslagblob-gegevensbijdrager'
    2. Controleer of de **vervolgkeuzelijst Toegang tot** toewijzing is ingesteld op 'Azure AD-gebruiker, -groep of serviceprincipal'.
    3. Typ de naam van uw Stream Analytics-taak in het zoekveld.
    4. Selecteer uw taak Stream Analytics en klik op **Opslaan**.

   ![Containertoegang verlenen](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Toegang tot accountniveau

1. Ga naar uw opslagaccount.

2. Selecteer **Toegangsbeheer (IAM)** aan de linkerkant.

3. Klik onder de sectie Een roltoewijzing toevoegen op **Toevoegen**.

4. In het deelvenster Roltoewijzing:

    1. De **rol instellen op** 'Opslagblob-gegevensbijdrager'
    2. Controleer of de **vervolgkeuzelijst Toegang tot** toewijzing is ingesteld op 'Azure AD-gebruiker, -groep of serviceprincipal'.
    3. Typ de naam van uw Stream Analytics-taak in het zoekveld.
    4. Selecteer uw taak Stream Analytics en klik op **Opslaan**.

   ![Toegang tot een account verlenen](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Toegang verlenen via de opdrachtregel

#### <a name="container-level-access"></a>Toegang tot containerniveau

Voer de volgende opdracht uit met Azure CLI als u toegang wilt geven tot een specifieke container:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Toegang tot accountniveau

Voer de volgende opdracht uit met Azure CLI om toegang te geven tot het hele account:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET-toegang inschakelen

Wanneer u de **firewalls en virtuele netwerken**van uw opslagaccount configureert, u optioneel netwerkverkeer toestaan vanuit andere vertrouwde Microsoft-services. Wanneer Stream Analytics verifieert met managed identity, levert het het bewijs dat de aanvraag afkomstig is van een vertrouwde service. Hieronder vindt u instructies om deze VNET-toegangsuitzondering in te schakelen.

1.  Navigeer naar het deelvenster 'Firewalls en virtuele netwerken' in het configuratiedeelvenster van het opslagaccount.
2.  Controleer of de optie 'Vertrouwde Microsoft-services toegang geven tot dit opslagaccount' is ingeschakeld.
3.  Als u deze hebt ingeschakeld, klikt u op **Opslaan**.

   ![VNET-toegang inschakelen](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Beperkingen
Hieronder staan de huidige beperkingen van deze functie:

1. Klassieke Azure-opslagaccounts.

2. Azure-accounts zonder Azure Active Directory.

3. Toegang met meerdere tenant's wordt niet ondersteund. De serviceprincipal die is gemaakt voor een bepaalde Functie Van Stream Analytics moet zich bevinden in dezelfde Azure Active Directory-tenant waarin de taak is gemaakt en kan niet worden gebruikt met een bron die zich in een andere Azure Active Directory-tenant bevindt.

4. [Gebruikerstoegewezen identiteit](../active-directory/managed-identities-azure-resources/overview.md) wordt niet ondersteund. Dit betekent dat de gebruiker niet in staat is om zijn eigen serviceprincipal in te voeren die wordt gebruikt door zijn Stream Analytics-taak. De serviceprincipal moet worden gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Inzicht krijgen in uitvoer vanuit Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Aangepaste blob-uitvoerpartities van Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
