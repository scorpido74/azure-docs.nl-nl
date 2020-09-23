---
title: Resource-inventaris, gebruiks gegevens, metrische gegevens en logboeken uploaden naar Azure Monitor
description: Resource-inventaris, gebruiks gegevens, metrische gegevens en logboeken uploaden naar Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936038"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Resource-inventaris, gebruiks gegevens, metrische gegevens en logboeken uploaden naar Azure Monitor

Met Azure Arc Data Services kunt u *eventueel* uw metrische gegevens en logboeken uploaden naar Azure monitor zodat u metrische gegevens, logboeken, waarschuwingen kunt verzamelen en analyseren, meldingen verzendt of geautomatiseerde acties triggert. Wanneer u uw gegevens naar Azure Monitor verzendt, kunt u de gegevens van de bewaking en logboeken van de site opslaan en op grote schaal lange termijn opslag van de gegevens voor geavanceerde analyses.  Als u meerdere sites hebt die Azure Arc Data Services hebben, kunt u Azure Monitor gebruiken als centrale locatie voor het verzamelen van alle logboeken en metrische gegevens over uw sites.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Voordat u begint

Er zijn enkele eenmalige installatie stappen vereist voor het inschakelen van de scenario's voor het uploaden van Logboeken en metrische gegevens:

1) Maak een Service-Principal/Azure Active Directory toepassing, inclusief het maken van een geheim voor client toegang en wijs de Service-Principal toe aan de rol ' bewaking metrische gegevens Uitgever ' op de abonnementen waar de resources van het data base-exemplaar zich bevinden.
2) Maak een log Analytics-werk ruimte en haal de sleutels op en stel de gegevens in omgevings variabelen in.

Het eerste item is vereist voor het uploaden van metrische gegevens en de tweede is vereist voor het uploaden van Logboeken.

Volg deze opdrachten om de service-principal voor metrische gegevens te maken en toe te wijzen aan de rollen ' bewaking metrische gegevens Uitgever ' en ' Inzender ', zodat de Service-Principal metrische gegevens kan uploaden en Create-en upload bewerkingen kan uitvoeren.

## <a name="create-service-principal-and-assign-roles"></a>Een service-principal maken en rollen toewijzen

Volg deze opdrachten voor het maken van de service-principal voor metrische gegevens en wijs deze toe aan de rol ' metrische gegevens van de uitgever controleren ':

Als u een Service-Principal wilt maken, voert u deze opdracht uit:

> [!NOTE]
> Voor het maken van een Service-Principal zijn [bepaalde machtigingen vereist in azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Voorbeelduitvoer:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Sla de appId-en Tenant waarden op in een omgevings variabele voor later gebruik:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Voer deze opdracht uit om de Service-Principal toe te wijzen aan de rol ' bewaking metrische gegevens Uitgever ' op het abonnement waarin de resources van het data base-exemplaar zich bevinden:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Voorbeelduitvoer:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Een log Analytics-werk ruimte maken

Voer vervolgens deze opdrachten uit om een Log Analytics-werk ruimte te maken en de toegangs gegevens in omgevings variabelen in te stellen.

> [!NOTE]
> Sla deze stap over als u al een werk ruimte hebt.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Voorbeelduitvoer:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>ID en gedeelde sleutel toewijzen aan omgevings variabelen

Sla de customerId (werk ruimte-ID) op als een omgevings variabele die u later wilt gebruiken:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Met deze opdracht worden de toegangs sleutels die nodig zijn om verbinding te maken met uw log Analytics-werk ruimte afgedrukt:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Voorbeelduitvoer:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Sla de primaire sleutel op in een omgevings variabele die u later wilt gebruiken:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Definitieve omgevings variabelen instellen en bevestigen

Stel de URL van de SPN-instantie in een omgevings variabele in:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Controleer of alle vereiste omgevings variabelen zijn ingesteld als u wilt:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Gegevens uploaden naar Azure Monitor

Als u metrische gegevens wilt uploaden voor uw Azure SQL Managed instances en Azure Database for PostgreSQL grootschalige-Server groepen, voert u de volgende CLI-opdrachten uit:

Hiermee worden alle metrische gegevens naar het opgegeven bestand geëxporteerd:

```console
azdata arc dc export -t metrics --path metrics.json
```

Hiermee worden metrische gegevens naar Azure Monitor geüpload:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>De metrische gegevens weergeven in de Azure Portal

Zodra uw metrische gegevens zijn geüpload, moet u ze kunnen visualiseren vanuit de Azure Portal.

Als u uw metrische gegevens in de portal wilt weer geven, gebruikt u deze speciale koppeling om de portal te openen: <https://portal.azure.com> Zoek vervolgens naar uw data base-exemplaar op naam in de zoek balk:

U kunt het CPU-gebruik op de overzichts pagina weer geven of u kunt klikken op metrische gegevens in het navigatie paneel aan de linkerkant.

Kies SQL Server als metrische naam ruimte:

Selecteer de metrische gegevens die u wilt visualiseren (u kunt ook meerdere selecteren):

Wijzig de frequentie in de laatste 30 minuten:

> [!NOTE]
> U kunt alleen metrische gegevens uploaden voor de afgelopen 30 minuten. Azure Monitor negeert metrische gegevens die ouder zijn dan 30 minuten.

## <a name="upload-logs-to-azure-monitor"></a>Logboeken uploaden naar logboeken van Azure Monitor

 Voor het uploaden van Logboeken voor uw Azure SQL Managed instances en Azure Database for PostgreSQL grootschalige-Server groepen voert u de volgende CLI-opdrachten uit:

Hiermee worden alle logboeken naar het opgegeven bestand geëxporteerd:

```console
azdata arc dc export -t logs --path logs.json
```

Hiermee worden logboeken geüpload naar een Azure monitor log Analytics-werk ruimte:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Uw logboeken in Azure Portal weer geven

Zodra de logboeken zijn geüpload, zou u er als volgt query's op moeten kunnen uitvoeren met de logboekqueryverkenner:

1. Open de Azure Portal en zoek naar uw werk ruimte op naam in de zoek balk bovenin en selecteer deze.
2. Klik in het linkerdeelvenster op Logboeken
3. Klik op aan de slag (of klik op de koppelingen op de pagina aan de slag voor meer informatie over Log Analytics als u er nog geen ervaring voor hebt)
4. Volg de zelf studie om meer te weten te komen over Log Analytics als dit de eerste keer is
5. Vouw Aangepaste logboeken aan de onderkant van de lijst met tabellen uit. Er wordt dan een tabel met de naam sql_instance_logs_CL weergegeven.
6. Klik op het oogpictogram naast de tabelnaam
7. Klik op de knop Weergeven in query-editor
8. U hebt nu een query in de query-editor waarin de meest recente 10 gebeurtenissen in het logboek worden weergegeven
9. Nu kunt u experimenteren met het uitvoeren van query's in de logboeken met behulp van de query-editor, het instellen van waarschuwingen, enzovoort.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Het automatisch laden van metrische gegevens en Logboeken (optioneel)

Als u voortdurend metrische gegevens en logboeken wilt uploaden, kunt u een script maken en dit elke paar minuten uitvoeren op een timer.  Hieronder ziet u een voor beeld van het automatiseren van de uploads met behulp van een Linux-shell script.

Voeg in uw favoriete tekst/code-editor het volgende toe aan de script inhoud aan het bestand en sla het op als een uitvoerbaar script bestand zoals. v (Linux/Mac) of. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Het uitvoer bare script bestand maken

```console
chmod +x myuploadscript.sh
```

Voer het script uit om de 2 minuten:

```console
watch -n 120 ./myuploadscript.sh
```

U kunt ook een job scheduler gebruiken zoals cron of Windows taak planner of een Orchestrator zoals Ansible, puppet of chef.
