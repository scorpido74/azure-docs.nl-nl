---
title: Voor beelden van Azure Monitor CLI Quick Start
description: Voor beeld van CLI-opdrachten voor Azure Monitor-functies. Azure Monitor is een Microsoft Azure-service waarmee u waarschuwings meldingen kunt verzenden, webadressen moet aanroepen op basis van de waarden van geconfigureerde telemetriegegevens en automatisch schalen Cloud Services, Virtual Machines en Web Apps.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248929"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Voor beelden van Azure Monitor CLI Quick Start
In dit artikel vindt u een voor beeld van opdracht regel interface-opdrachten (CLI) om u te helpen bij het openen van Azure Monitor-functies. Met Azure Monitor kunt u Cloud Services, Virtual Machines en Web Apps automatisch schalen en waarschuwings meldingen verzenden of de Url's van websites aanroepen op basis van de waarden van geconfigureerde telemetriegegevens.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI nog niet hebt geïnstalleerd, volgt u de instructies voor [het installeren van de Azure cli](/cli/azure/install-azure-cli). U kunt [Azure Cloud shell](/azure/cloud-shell) ook gebruiken om de CLI uit te voeren als een interactieve ervaring in uw browser. Bekijk een volledige verwijzing naar alle beschik bare opdrachten in de [Naslag informatie over Azure monitor cli](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
De eerste stap is om u aan te melden bij uw Azure-account.

```azurecli
az login
```

Nadat u deze opdracht hebt uitgevoerd, moet u zich aanmelden via de instructies op het scherm. Alle opdrachten werken in de context van uw standaard abonnement.

Gebruik de volgende opdracht om de details van uw huidige abonnement weer te geven.

```azurecli
az account show
```

Als u de werk context wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht.

```azurecli
az account set -s <Subscription ID or name>
```

Als u een lijst wilt weer geven met alle ondersteunde Azure Monitor opdrachten, voert u de volgende handelingen uit.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Activiteiten logboek weer geven voor een abonnement

Voer de volgende handelingen uit om een lijst met gebeurtenissen in het activiteiten logboek weer te geven.

```azurecli
az monitor activity-log list
```

Voer de volgende handelingen uit om alle beschik bare opties weer te geven.

```azurecli
az monitor activity-log list -h
```

Hier volgt een voor beeld van het weer geven van logboeken met een resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Voor beeld voor het weer geven van logboeken per oproeper

```azurecli
az monitor activity-log list --caller myname@company.com
```

Voor beeld voor het weer geven van Logboeken op basis van een aanroeper voor een resource type binnen een datum bereik

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Werken met waarschuwingen 
> [!NOTE]
> Er worden op dit moment alleen waarschuwingen (klassiek) ondersteund in CLI. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Waarschuwings regels (klassiek) in een resource groep ophalen

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Een regel voor metrische waarschuwing maken (klassiek)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Een waarschuwing (klassieke) regel verwijderen

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Logboek profielen

Gebruik de informatie in deze sectie om met logboek profielen te werken.

### <a name="get-a-log-profile"></a>Een logboek profiel ophalen

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Een logboek profiel met retentie toevoegen

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Een logboek profiel met retentie en EventHub toevoegen

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Een logboek profiel verwijderen

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostiek

Gebruik de informatie in deze sectie om met Diagnostische instellingen te werken.

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

Gebruik de informatie in deze sectie om te werken met instellingen voor automatisch schalen. U moet deze voor beelden wijzigen.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Instellingen voor automatisch schalen voor een resource groep ophalen

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Instellingen voor automatisch schalen ophalen op naam in een resource groep

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Instellingen voor automatisch schalen instellen

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

