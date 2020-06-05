---
title: CLI-voorbeelden van Azure Monitor
description: Hier vindt u voorbeelden van CLI-opdrachten voor Azure Monitor-functies. Azure Monitor is een Microsoft Azure-service waarmee u waarschuwingen kunt verzenden of web-URL's kunt aanroepen op basis van waarden van geconfigureerde telemetriegegevens, en cloudservices, virtuele machines en web-apps automatisch kunt schalen.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/16/2018
ms.openlocfilehash: 3c55becb098c9d93688b59f1db4f702acbff3f72
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837264"
---
# <a name="azure-monitor-cli-samples"></a>CLI-voorbeelden van Azure Monitor
In dit artikel vindt u voorbeelden van CLI-opdrachten (Command-Line Interface), om vanaf de opdrachtregel toegang te krijgen tot Azure Monitor-functies. Met Azure Monitor kunt u cloudservices, virtuele machines en web-apps automatisch schalen en waarschuwingen verzenden of web-URL's aanroepen op basis van waarden van geconfigureerde telemetriegegevens.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI nog niet hebt geïnstalleerd, volgt u de instructies in [Install the Azure CLI](/cli/azure/install-azure-cli) (De Azure CLI installeren). U kunt ook [Azure Cloud Shell](/azure/cloud-shell) gebruiken om de CLI als een interactieve ervaring in uw browser uit te voeren. Bekijk een volledig overzicht van alle beschikbare opdrachten in de [naslaginformatie over de CLI van Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
De eerste stap is om u aan te melden bij uw Azure-account.

```azurecli
az login
```

Nadat u deze opdracht hebt uitgevoerd, moet u zich aanmelden via de instructies op het scherm. Alle opdrachten werken in de context van uw standaardabonnement.

Gebruik de volgende opdracht om de details van uw huidige abonnement weer te geven.

```azurecli
az account show
```

Als u de werkcontext wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht:

```azurecli
az account set -s <Subscription ID or name>
```

Voer de volgende opdracht uit om een lijst met alle ondersteunde Azure Monitor-opdrachten weer te geven.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Activiteitenlogboek weergeven voor een abonnement

Voer de volgende opdracht uit om een lijst met gebeurtenissen uit het activiteitenlogboek weer te geven.

```azurecli
az monitor activity-log list
```

Probeer het volgende om alle beschikbare opties weer te geven.

```azurecli
az monitor activity-log list -h
```

Hier volgt een voorbeeld om logboeken weer te geven voor een bepaalde resourcegroep:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Voorbeeld om logboeken weer te geven op beller:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Voorbeeld om logboeken weer te geven op beller voor een resourcetype, binnen een bepaald datumbereik

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Werken met waarschuwingen 
> [!NOTE]
> Op dit moment worden alleen waarschuwingen (klassiek) ondersteund in de CLI. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Waarschuwingsregels (klassiek) ophalen in een resourcegroep

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Een regel voor metrische waarschuwingen (klassiek) maken

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Een regel voor waarschuwingen (klassiek) verwijderen

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Logboekprofielen

Gebruik de informatie in dit gedeelte om met logboekprofielen te werken.

### <a name="get-a-log-profile"></a>Een logboekprofiel ophalen

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Een logboekprofiel met retentie toevoegen

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Een logboekprofiel met retentie en Event Hub toevoegen

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Een logboekprofiel verwijderen

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostiek

Gebruik de informatie in dit gedeelte om met diagnostische instellingen te werken.

### <a name="get-a-diagnostic-setting"></a>Een diagnostische instelling ophalen

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Een diagnostische instelling verwijderen

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatisch schalen

Gebruik de informatie in dit gedeelte om met instellingen voor automatisch schalen te werken. U moet deze voorbeelden aanpassen.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Instellingen voor automatisch schalen ophalen voor een resourcegroep

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Instellingen voor automatisch schalen op naam ophalen voor een resourcegroep

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Instellingen voor automatisch instellen

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

