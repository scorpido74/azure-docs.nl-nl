---
title: Azure Monitor CLI snelstartvoorbeelden
description: Voorbeeld van CLI-opdrachten voor Azure Monitor-functies. Azure Monitor is een Microsoft Azure-service waarmee u waarschuwingsmeldingen verzenden, web-URL's oproepen op basis van waarden van geconfigureerde telemetriegegevens en autoScale Cloud Services, virtuele machines en webapps.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248929"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Azure Monitor CLI snelstartvoorbeelden
In dit artikel ziet u de cli-opdrachten (command line interface) om u te helpen toegang te krijgen tot Azure Monitor-functies. Met Azure Monitor u AutoScale Cloud Services, Virtual Machines en Web Apps en waarschuwingsmeldingen of oproepweb-URL's verzenden op basis van waarden van geconfigureerde telemetriegegevens.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI nog niet hebt geïnstalleerd, volgt u de instructies voor [het installeren van de Azure CLI](/cli/azure/install-azure-cli). U [Azure Cloud Shell](/azure/cloud-shell) ook gebruiken om de CLI uit te voeren als een interactieve ervaring in uw browser. Bekijk een volledige referentie van alle beschikbare opdrachten in de [Azure Monitor CLI-verwijzing.](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) 

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
De eerste stap is om in te loggen op uw Azure-account.

```azurecli
az login
```

Na het uitvoeren van deze opdracht moet u inloggen via de instructies op het scherm. Alle opdrachten werken in de context van uw standaardabonnement.

Als u de details van uw huidige abonnement wilt weergeven, gebruikt u de volgende opdracht.

```azurecli
az account show
```

Als u de werkcontext wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht.

```azurecli
az account set -s <Subscription ID or name>
```

Voer het volgende uit om een lijst met alle ondersteunde Azure Monitor-opdrachten weer te geven.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Activiteitenlogboek voor een abonnement weergeven

Voer het volgende uit om een lijst met gebeurtenissen in het activiteitenlogboek weer te geven.

```azurecli
az monitor activity-log list
```

Probeer het volgende om alle beschikbare opties weer te geven.

```azurecli
az monitor activity-log list -h
```

Hier is een voorbeeld om logboeken op te nemen door een resourceGroep

```azurecli
az monitor activity-log list --resource-group <group name>
```

Voorbeeld om logboeken op beller te vermelden

```azurecli
az monitor activity-log list --caller myname@company.com
```

Voorbeeld om logboeken op beller op een resourcetype te vermelden, binnen een datumbereik

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Werken met waarschuwingen 
> [!NOTE]
> Alleen waarschuwingen (klassiek) worden op dit moment ondersteund in CLI. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Waarschuwingsregels (klassieke) regels in een resourcegroep ontvangen

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Een regel met metrische waarschuwing (klassieke) regel maken

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Een waarschuwingsregel (klassieke) regel verwijderen

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Logboekprofielen

Gebruik de informatie in deze sectie om te werken met logboekprofielen.

### <a name="get-a-log-profile"></a>Een logboekprofiel opmaken

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Een logboekprofiel toevoegen met retentie

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Een logboekprofiel toevoegen met retentie en EventHub

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

Gebruik de informatie in deze sectie om te werken met diagnostische instellingen.

### <a name="get-a-diagnostic-setting"></a>Een diagnostische instelling opdoen

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

Gebruik de informatie in deze sectie om te werken met instellingen voor automatisch schalen. U moet deze voorbeelden wijzigen.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Instellingen voor automatisch schalen voor een resourcegroep opvragen

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Instellingen voor automatisch schalen op naam in een resourcegroep

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Instellingen voor automatisch schalen instellen

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

