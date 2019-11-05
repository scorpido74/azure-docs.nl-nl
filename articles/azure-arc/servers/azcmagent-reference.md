---
title: CLI-interface van met Azure verbonden machine agent
description: Referentie documentatie voor de Azure Connected machine Agent CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513196"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>CLI-interface van met Azure verbonden machine agent

Het hulp programma `Azcmagent` (Azure Connected machine agent) wordt gebruikt voor het configureren en oplossen van problemen met een niet-Azure-computer verbinding met Azure.

De agent zelf is een daemon-proces met de naam `himdsd` op Linux en een Windows-service met de naam `himds` in Windows.

Bij normaal gebruik `azcmagent connect` wordt gebruikt om een verbinding tot stand te brengen tussen deze computer en Azure, en `azcmagent disconnect` als u besluit dat deze verbinding niet meer nodig is. De andere opdrachten zijn voor het oplossen van problemen of andere speciale gevallen.

## <a name="options"></a>Opties

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>ZIE OOK

* [azcmagent Connect](#azcmagent-connect) : Hiermee wordt deze computer verbonden met Azure
* [azcmagent verbinding verbreken](#azcmagent-disconnect) : Hiermee wordt de verbinding tussen deze computer en Azure verbroken
* [azcmagent reconnect](#azcmagent-reconnect) : Hiermee wordt deze computer opnieuw verbonden met Azure
* [azcmagent show](#azcmagent-show) -Hiermee worden de meta gegevens van de machine en de agent status opgehaald. Dit is vooral nuttig voor het oplossen van problemen.
* [azcmagent-versie](#azcmagent-version) : de versie van de hybride beheer agent weer geven

## <a name="azcmagent-connect"></a>azcmagent verbinding maken

Deze machine verbinden met Azure

### <a name="synopsis"></a>Samen vatting

Hiermee maakt u een resource in azure die deze machine vertegenwoordigt.

Dit maakt gebruik van de verificatie opties voor het maken van een resource in Azure Resource Manager die deze machine vertegenwoordigen. De resource bevindt zich in het aangevraagde abonnement en de resource groep en de gegevens over de machine worden opgeslagen in de Azure-regio die is opgegeven door de locatie parameter.
De standaard resource naam is de hostnaam van deze computer als deze niet wordt overschreven.

Een certificaat dat overeenkomt met de door het systeem toegewezen identiteit van deze computer, wordt vervolgens lokaal gedownload en opgeslagen. Zodra deze stap is voltooid, worden de **meta gegevens service van Azure Connected machine** en de gast configuratie agent gesynchroniseerd met de Azure-Cloud.

Verificatie opties:

* Toegangs token `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Service-Principal-ID en geheime `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Aanmelden bij apparaat (interactief) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Syntaxis

```none
azcmagent connect [flags]
```

### <a name="options"></a>Opties

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent-verbinding verbreken

Hiermee wordt de verbinding tussen deze computer en Azure verbroken

### <a name="synopsis"></a>Samen vatting

Hiermee verwijdert u de resource in azure die deze server vertegenwoordigt.

Met deze opdracht worden de verificatie opties gebruikt voor het verwijderen van de Azure Resource Manager resource die deze computer vertegenwoordigt. Hierna wordt de verbinding met de **Azure Connected Machine metadata service** en de gast configuratie agent verbroken. Met deze opdracht worden de services niet gestopt of verwijderd: Verwijder het pakket om dit uit te voeren.

Voor deze opdracht zijn hogere bevoegdheden vereist dan de rol ' Azure Connectd machine onboarding '.

Als de verbinding met een computer is verbroken, gebruikt u `azcmagent connect`en niet `azcmagent reconnect` als u een nieuwe resource wilt maken in Azure.

Verificatie opties:

* Toegangs token `azcmagent disconnect --access-token <>`
* Service-Principal-ID en geheime `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Aanmelden bij een interactief apparaat `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Syntaxis

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Opties

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent opnieuw verbinding maken

Hiermee wordt de computer opnieuw verbonden met Azure

### <a name="synopsis"></a>Samen vatting

Verbind de computer opnieuw met ongeldige referenties voor Azure.

Als een machine al een resource in azure heeft maar niet kan worden geverifieerd, kan deze opnieuw worden verbonden met behulp van deze opdracht. Dit kan gebeuren als een computer lang genoeg is uitgeschakeld voor het verlopen van het certificaat (ten minste 45 dagen).

Als een computer niet is verbonden met `azcmagent disconnect`, gebruikt u `azcmagent connect` in plaats daarvan.

Met deze opdracht worden de verificatie opties gebruikt die zijn verschaft om nieuwe referenties op te halen die overeenkomen met de Azure Resource Manager bron van deze computer.

Voor deze opdracht zijn hogere bevoegdheden vereist dan de **met Azure verbonden computer** voorbereidings functie.

Verificatie opties

* Toegangs token `azcmagent reconnect --access-token <>`
* Service-Principal-ID en geheime `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Aanmelden bij een interactief apparaat `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Syntaxis

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Opties

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent weer geven

Hiermee worden de meta gegevens van de computer en de agent status opgehaald. Dit is vooral nuttig voor het oplossen van problemen.

### <a name="synopsis"></a>Samen vatting

Hiermee worden de meta gegevens van de computer en de agent status opgehaald. Dit is vooral nuttig voor het oplossen van problemen.


### <a name="syntax"></a>Syntaxis

```
azcmagent show [flags]
```

### <a name="options"></a>Opties

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent-versie

De versie van de hybride beheer agent weer geven

### <a name="synopsis"></a>Samen vatting

De versie van de hybride beheer agent weer geven

### <a name="syntax"></a>Syntaxis

```none
azcmagent version [flags]
```

### <a name="options"></a>Opties

```none
  -h, --help   help for version
```
