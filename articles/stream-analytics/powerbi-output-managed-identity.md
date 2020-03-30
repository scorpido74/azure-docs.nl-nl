---
title: Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren naar Power BI-uitvoer
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Power BI-uitvoer.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 8a7dfd7c690d79d8430f7c33a25b38949dbd06c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086323"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren naar Power BI

[Managed Identity-verificatie](../active-directory/managed-identities-azure-resources/overview.md) voor uitvoer naar Power BI geeft Stream Analytics-taken directe toegang tot een werkruimte binnen uw Power BI-account. Met deze functie kunnen implementaties van Stream Analytics-taken volledig worden geautomatiseerd, omdat een gebruiker zich niet langer interactief hoeft aan te melden bij Power BI via de Azure-portal. Bovendien worden langlopende taken die naar Power BI schrijven nu beter ondersteund, omdat u de taak niet periodiek opnieuw hoeft te autoriseren.

In dit artikel ziet u hoe u Managed Identity inschakelt voor de Power BI-uitvoer(s) van een Stream Analytics-taak via de Azure-portal en via een Azure Resource Manager-implementatie.

## <a name="prerequisites"></a>Vereisten

Voor het gebruik van deze functie geldt het volgende:

- Een Power BI-account met een [Pro-licentie](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).

- Een verbeterde werkruimte binnen uw Power BI-account. Zie de aankondiging van Deze functie door [Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) voor meer details.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Een Stream Analytics-taak maken met de Azure-portal

1. Maak een nieuwe Stream Analytics-taak of open een bestaande taak in de Azure-portal. Selecteer **Beheerde identiteit** onder **Configureren**in de menubalk aan de linkerkant van het scherm. Controleer of 'Beheerde identiteit met systeemtoegewezen identiteit' is geselecteerd en selecteer de knop **Opslaan** onder in het scherm.

   ![Beheerde identiteit van Stream Analytics configureren](./media/common/stream-analytics-enable-managed-identity.png)

2. Voordat u de uitvoer configureert, geeft u de taak Stream Analytics toegang tot uw Power BI-werkruimte door de aanwijzingen in de [taak Stream Analytics toegang te geven tot uw Power BI-werkruimtegedeelte](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) van dit artikel.

3. Navigeer naar het gedeelte **Uitvoer** van de taak van uw streamanalyse, selecteer **+ Toevoegen**en kies **Power BI**. Selecteer vervolgens de knop **Autoriseren** en meld u aan met uw Power BI-account.

   ![Autoriseren met Power BI-account](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Zodra u dit heeft geautoriseerd, wordt een vervolgkeuzelijst gevuld met alle werkruimten waartoe u toegang hebt. Selecteer de werkruimte die u in de vorige stap hebt geautoriseerd. Selecteer **vervolgens Beheerde identiteit** als de 'verificatiemodus'. Selecteer ten slotte de knop **Opslaan.**

   ![Power BI-uitvoer configureren met Beheerde identiteit](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Met Azure Resource Manager u de implementatie van uw Stream Analytics-taak volledig automatiseren. U Resource Manager-sjablonen implementeren met Azure PowerShell of Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). In de onderstaande voorbeelden wordt gebruik gemaakt van de Azure CLI.


1. U een **Microsoft.StreamAnalytics/streamingjobs-bron** met een beheerde identiteit maken door de volgende eigenschap op te nemen in het resourcegedeelte van uw resourcemanagersjabloon:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Met deze eigenschap moet Azure Resource Manager de identiteit voor uw Stream Analytics-taak maken en beheren. Hieronder vindt u een voorbeeldsjabloon Resource Manager waarmee een streamanalytics-taak met Managed Identity is ingeschakeld en een Power BI-uitvoersink die beheerde identiteit gebruikt:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    Implementeer de taak hierboven in de **voorbeeldgroep Voorbeeldgroep** resource met de onderaanstaande opdracht Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Nadat de taak is gemaakt, gebruikt u Azure Resource Manager om de volledige definitie van de taak op te halen.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    De bovenstaande opdracht retourneert een antwoord zoals het onderstaande:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Als u van plan bent de REST API van Power BI te gebruiken om de taak Stream Analytics toe te voegen aan uw Power BI-werkruimte, noteert u de geretourneerde 'principalId'.

3. Nu de taak is gemaakt, gaat u door met de taak Stream Analytics toegang geven tot het gedeelte [Power BI-werkruimte](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) in dit artikel.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Geef de Stream Analytics-taak toegang tot uw Power BI-werkruimte

Nu de functie Stream Analytics is gemaakt, kan deze toegang krijgen tot een Power BI-werkruimte.

### <a name="use-the-power-bi-ui"></a>De Power BI-gebruikersinterface gebruiken

   > [!Note]
   > Als u de taak Stream Analytics wilt toevoegen aan uw Power BI-werkruimte met behulp van de gebruikersinterface, moet u ook serviceprincipal-toegang inschakelen in de **ontwikkelaarsinstellingen** in de Power BI-beheerportal. Zie [Aan de slag met een serviceprincipal](https://docs.microsoft.com/power-bi/developer/embed-service-principal#get-started-with-a-service-principal) voor meer informatie.

1. Navigeer naar de toegangsinstellingen van de werkruimte. Zie dit artikel voor meer informatie: [Geef toegang tot uw werkruimte.](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. Typ de naam van uw streamanalytics-taak in het tekstvak en selecteer **Inzender** als toegangsniveau.

3. Selecteer Het deelvenster **toevoegen** en sluiten.

   ![Stream Analytics-taak toevoegen aan Power BI-werkruimte](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>De Power BI PowerShell-cmdlets gebruiken

1. Installeer de `MicrosoftPowerBIMgmt` Power BI PowerShell-cmdlets.

   > [!Important]
   > Zorg ervoor dat u versie 1.0.821 of hoger van de cmdlets gebruikt.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Log in op Power BI.

```powershell
Login-PowerBI
```

3. Voeg uw Stream Analytics-taak toe als bijdrager aan de werkruimte.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>De Power BI REST API gebruiken

De functie Stream Analytics kan ook worden toegevoegd als bijdrager aan de werkruimte door de REST-API 'Groepsgebruiker toevoegen' rechtstreeks te gebruiken. Volledige documentatie voor deze API is hier te vinden: [Groepen - Groepsgebruiker toevoegen](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Voorbeeldaanvraag**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Aanvraagtekst
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Beperkingen
Hieronder staan de beperkingen van deze functie:

- Klassieke Power BI-werkruimten worden niet ondersteund.

- Azure-accounts zonder Azure Active Directory.

- Toegang met meerdere tenant's wordt niet ondersteund. De serviceprincipal die is gemaakt voor een bepaalde Functie Van Stream Analytics moet zich bevinden in dezelfde Azure Active Directory-tenant waarin de taak is gemaakt en kan niet worden gebruikt met een bron die zich in een andere Azure Active Directory-tenant bevindt.

- [Gebruikerstoegewezen identiteit](../active-directory/managed-identities-azure-resources/overview.md) wordt niet ondersteund. Dit betekent dat u uw eigen serviceprincipal niet invoeren om te worden gebruikt door hun Stream Analytics-taak. De serviceprincipal moet worden gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Integratie van Power BI-dashboards met Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Inzicht krijgen in uitvoer vanuit Azure Stream Analytics](./stream-analytics-define-outputs.md)
