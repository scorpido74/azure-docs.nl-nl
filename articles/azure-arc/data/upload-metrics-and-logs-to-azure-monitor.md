---
title: De gebruiks gegevens, de metrieken en de logboeken uploaden naar Azure Monitor
description: Upload resource-inventaris, gebruiks gegevens, metrieken en logboeken naar Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3e3b804e2a3c43eb9579d1c6a1195511df528de2
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629995"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>De gebruiks gegevens, de metrieken en de logboeken uploaden naar Azure Monitor

U kunt regel matig gebruiks gegevens voor facturerings doeleinden exporteren, metrische gegevens en logboeken bewaken en deze vervolgens uploaden naar Azure.  Als u een van deze drie typen gegevens exporteert en uploadt, worden ook de gegevens controller, SQL Managed instance en PostgreSQL grootschalige server-groeps resources in azure gemaakt en bijgewerkt.

> [!NOTE] 
Tijdens de preview-periode zijn er geen kosten verbonden aan het gebruik van Azure Arc enabled Data Services.

## <a name="prerequisites"></a>Vereisten

U hebt de Azure CLI (AZ) en de Azure data CLI (azdata) nodig.  [Hulpprogram Ma's installeren](./install-client-tools.md).

Voordat u gegevens naar Azure uploadt, moet u ervoor zorgen dat de resource provider micro soft. Azureworden is geregistreerd voor uw Azure-abonnement.

U kunt dit controleren door de volgende opdracht uit te voeren:

```console
az provider show -n Microsoft.AzureData -o table
```

Als de resource provider momenteel niet is geregistreerd in uw abonnement, kunt u deze registreren door de volgende opdracht uit te voeren.  Het duurt een paar minuten voordat deze opdracht is voltooid.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Gebruiks gegevens uploaden

Informatie over het gebruik van de inventaris en het resource gebruik kan in de volgende twee stappen worden geüpload naar Azure:

1. Exporteer de gebruiks gegevens met behulp van de ```azdata export``` opdracht als volgt:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Met deze opdracht maakt u een `usage.json` bestand met alle Azure-Arc-gegevens bronnen, zoals SQL Managed instances en postgresql grootschalige, etc. die zijn gemaakt op de gegevens controller.

2. De gebruiks gegevens uploaden met behulp van de ```azdata upload``` opdracht

   > [!NOTE]
   > Wacht ten minste 24 uur nadat u de Azure-gegevens controller hebt gemaakt voordat u de upload uitvoert.

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Metrische gegevens en logboeken uploaden

Met Azure Arc Data Services kunt u eventueel uw metrische gegevens en logboeken uploaden naar Azure Monitor zodat u metrische gegevens, Logboeken kunt samen voegen en analyseren, en hoe u meldingen verzendt, berichten kunt verzenden of geautomatiseerde acties triggert. 

Door uw gegevens naar Azure Monitor te verzenden, kunt u de gegevens van de bewaking en logboeken op een site opslaan en op grote schaal lange termijn opslag van de gegevens voor geavanceerde analyses.

Als u meerdere sites hebt met Azure Arc Data Services, kunt u Azure Monitor gebruiken als centrale locatie voor het verzamelen van alle logboeken en metrische gegevens over uw sites.

### <a name="before-you-begin"></a>Voordat u begint

Er zijn enkele eenmalige installatie stappen vereist voor het inschakelen van de scenario's voor het uploaden van Logboeken en metrische gegevens:

1. Maak een Service-Principal/Azure Active Directory toepassing, inclusief het maken van een geheim voor client toegang en wijs de Service-Principal toe aan de rol ' bewaking metrische gegevens Uitgever ' op de abonnementen waar de resources van het data base-exemplaar zich bevinden.
2. Maak een log Analytics-werk ruimte en haal de sleutels op en stel de gegevens in omgevings variabelen in.

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

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Sla de appId-en Tenant waarden op in een omgevings variabele voor later gebruik. 

Volg dit voor beeld om de appId-en Tenant waarden met Power shell op te slaan:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

U kunt ook op Linux of macOS de appId-en Tenant waarden opslaan met dit voor beeld:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Voer deze opdracht uit om de Service-Principal toe te wijzen aan de rol ' bewaking metrische gegevens Uitgever ' op het abonnement waarin de resources van het data base-exemplaar zich bevinden:


> [!NOTE]
> U moet dubbele aanhalings tekens voor rolnaams gebruiken bij het uitvoeren vanuit een Windows-omgeving.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Voorbeelduitvoer:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Een log Analytics-werk ruimte maken

Voer vervolgens deze opdrachten uit om een Log Analytics-werk ruimte te maken en de toegangs gegevens in omgevings variabelen in te stellen.

> [!NOTE]
> Sla deze stap over als u al een werk ruimte hebt.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Voorbeelduitvoer:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Voor het uploaden van metrische gegevens voor uw Azure Arc enabled SQL Managed instances en PostgreSQL grootschalige-Server groepen met Azure Arc enabled, voert u de volgende CLI-opdrachten uit:

1. Alle metrische gegevens exporteren naar het opgegeven bestand:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Metrische gegevens uploaden naar Azure monitor:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Wacht mini maal 30 minuten nadat de Azure Arc-gegevens exemplaren zijn gemaakt voor de eerste upload
   >
   >Zorg ervoor dat `upload` de gegevens direct worden verwijderd nadat `export` Azure monitor alleen metrische gegevens voor de laatste 30 minuten accepteert. [Meer informatie](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Als er fouten worden weer gegeven met de melding ' fout bij het ophalen van metrische gegevens ' tijdens het exporteren, controleert u of de gegevensverzameling is ingesteld op ```true``` door de volgende opdracht uit te voeren:

```console
azdata arc dc config show
```

en zoek onder "beveiliging"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Controleer of de `allowNodeMetricsCollection` `allowPodMetricsCollection` Eigenschappen en zijn ingesteld op `true` .

## <a name="view-the-metrics-in-the-portal"></a>De metrische gegevens weergeven in de Azure Portal

Zodra uw metrische gegevens zijn geüpload, kunt u ze weer geven vanuit de Azure Portal.
> [!NOTE]
> Het kan enkele minuten duren voordat de geüploade gegevens worden verwerkt voordat u de metrieken in de portal kunt weer geven.


Als u uw metrische gegevens in de portal wilt weer geven, gebruikt u deze koppeling om de portal te openen: <https://portal.azure.com> Zoek vervolgens naar uw data base-exemplaar op naam in de zoek balk:

U kunt het CPU-gebruik op de overzichts pagina weer geven of u kunt klikken op metrische gegevens in het navigatie paneel aan de linkerkant.

Kies SQL Server als metrische naam ruimte:

Selecteer de metrische gegevens die u wilt visualiseren (u kunt ook meerdere selecteren):

Wijzig de frequentie in de laatste 30 minuten:

> [!NOTE]
> U kunt alleen metrische gegevens uploaden voor de afgelopen 30 minuten. Azure Monitor negeert metrische gegevens die ouder zijn dan 30 minuten.

## <a name="upload-logs-to-azure-monitor"></a>Logboeken uploaden naar logboeken van Azure Monitor

 Als u logboeken wilt uploaden voor uw Azure Arc enabled SQL Managed instances en AzureArc ingeschakeld PostgreSQL grootschalige-Server groepen voert u de volgende CLI-opdrachten uit:

1. Alle logboeken exporteren naar het opgegeven bestand:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Logboeken uploaden naar een Azure monitor log Analytics-werk ruimte:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Uw logboeken in Azure Portal weer geven

Zodra de logboeken zijn geüpload, zou u er als volgt query's op moeten kunnen uitvoeren met de logboekqueryverkenner:

1. Open de Azure Portal en zoek naar uw werk ruimte op naam in de zoek balk bovenin en selecteer deze.
2. Klik in het linkerdeelvenster op Logboeken
3. Klik op aan de slag (of klik op de koppelingen op de pagina aan de slag voor meer informatie over Log Analytics als u er nog geen ervaring voor hebt)
4. Volg de zelf studie voor meer informatie over Log Analytics als dit de eerste keer is dat u Log Analytics
5. Vouw Aangepaste logboeken aan de onderkant van de lijst met tabellen uit. Er wordt dan een tabel met de naam sql_instance_logs_CL weergegeven.
6. Klik op het oogpictogram naast de tabelnaam
7. Klik op de knop Weergeven in query-editor
8. U hebt nu een query in de query-editor waarin de meest recente 10 gebeurtenissen in het logboek worden weer gegeven
9. Nu kunt u experimenteren met het uitvoeren van query's in de logboeken met behulp van de query-editor, het instellen van waarschuwingen, enzovoort.

## <a name="automating-uploads-optional"></a>Uploads automatiseren (optioneel)

Als u metrische gegevens en logboeken op een geplande basis wilt uploaden, kunt u een script maken en dit elke paar minuten uitvoeren op een timer. Hieronder ziet u een voor beeld van het automatiseren van de uploads met behulp van een Linux-shell script.

Voeg in uw favoriete tekst/code-editor het volgende script toe aan het bestand en sla het op als een uitvoerbaar script bestand zoals. v (Linux/Mac) of. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Het uitvoer bare script bestand maken

```console
chmod +x myuploadscript.sh
```

Voer het script uit om de 20 minuten:

```console
watch -n 1200 ./myuploadscript.sh
```

U kunt ook een job scheduler gebruiken zoals cron of Windows taak planner of een Orchestrator zoals Ansible, puppet of chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Algemene richt lijnen voor het exporteren en uploaden van gebruik, metrische gegevens

Maken, lezen, bijwerken en verwijderen (ruwe) bewerkingen op Azure Arc-gegevens services worden geregistreerd voor facturerings-en bewakings doeleinden. Er zijn achtergrond services die controleren op deze ruwe bewerkingen en het verbruik op de juiste manier berekenen. De daad werkelijke berekening van het gebruik of verbruik gebeurt volgens een planning en wordt op de achtergrond uitgevoerd. 

Tijdens de preview-periode vindt dit proces een nacht. De algemene richt lijnen zijn het gebruik slechts één keer per dag te uploaden. Wanneer gebruiks gegevens binnen dezelfde periode van 24 uur meerdere keren worden geëxporteerd en geüpload, wordt alleen de resource-inventaris bijgewerkt in Azure Portal, maar niet het resource gebruik.

Voor het uploaden van metrische gegevens accepteert Azure monitor alleen de laatste 30 minuten aan data ([meer informatie](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). De richt lijnen voor het uploaden van metrische gegevens is het uploaden van de metrieken onmiddellijk na het maken van het export bestand, zodat u de volledige gegevensset in Azure Portal kunt weer geven. Als u bijvoorbeeld de metrische gegevens van 2:00 uur hebt geëxporteerd en de upload opdracht hebt uitgevoerd om 2:50 uur. Omdat Azure Monitor alleen gegevens voor de afgelopen 30 minuten accepteert, worden er mogelijk geen gegevens in de portal weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

[Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal](view-billing-data-in-azure.md)

[Resource voor Azure-Arc-gegevens controller in Azure Portal weer geven](view-data-controller-in-azure-portal.md)
