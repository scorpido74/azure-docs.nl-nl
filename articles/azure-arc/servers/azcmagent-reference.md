---
title: CLI-interface van met Azure verbonden machineagent
description: Referentiedocumentatie voor de Azure Connected Machine-agent CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513196"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>CLI-interface van met Azure verbonden machineagent

Het `Azcmagent` hulpprogramma (Azure Connected Machine Agent) wordt gebruikt om een niet-azure-apparaatverbinding met Azure te configureren en op te lossen.

De agent zelf is `himdsd` een daemon proces opgeroepen `himds` op Linux, en een Windows-service opgeroepen op Windows.

Bij normaal `azcmagent connect` gebruik wordt het gebruikt om een verbinding `azcmagent disconnect` tot stand te brengen tussen deze machine en Azure en als u besluit dat u die verbinding niet meer wilt. De andere opdrachten zijn voor het oplossen van problemen of andere speciale gevallen.

## <a name="options"></a>Opties

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>ZIE OOK

* [azcmagent connect](#azcmagent-connect) - Verbindt deze machine met Azure
* [azcmagent loskoppelen](#azcmagent-disconnect) - Hiermee verbreekt deze machine van Azure
* [azcmagent reconnect](#azcmagent-reconnect) - Verbindt deze machine opnieuw met Azure
* [azcmagent show](#azcmagent-show) - Krijgt machine metadata en Agent status. Dit is vooral handig voor het oplossen van problemen.
* [azcmagent versie](#azcmagent-version) - Display de Hybrid Management Agent versie

## <a name="azcmagent-connect"></a>azcmagent verbinden

Verbindt deze machine met Azure

### <a name="synopsis"></a>Synopsis

Hiermee maakt u een resource in Azure die deze machine vertegenwoordigt.

Hiermee worden de verificatieopties gebruikt om een bron te maken in Azure Resource Manager die deze machine vertegenwoordigt. De bron bevindt zich in de aangevraagde abonnements- en resourcegroep en gegevens over de machine worden opgeslagen in het Azure-gebied dat is opgegeven door de locatieparameter.
De standaardresourcenaam is de hostnaam van deze machine als deze niet wordt overschreven.

Een certificaat dat overeenkomt met de systeemtoegewezen identiteit van deze machine wordt vervolgens lokaal gedownload en opgeslagen. Zodra deze stap is voltooid, beginnen de **Azure Connected Machine Metadata** Service en Guest Configuration Agent te synchroniseren met Azure cloud.

Verificatieopties:

* Toegang tot token`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Service Principal ID en geheim`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Apparaataanmelding (interactief)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent los te koppelen

Hiermee wordt deze machine losgekoppeld van Azure

### <a name="synopsis"></a>Synopsis

Hiermee verwijdert u de bron in Azure die deze server vertegenwoordigt.

Met deze opdracht worden de verificatieopties gebruikt om de Azure Resource Manager-bron die deze machine vertegenwoordigt, te verwijderen. Na dit punt worden de **Azure Connected Machine Metadata Service** en Guest Configuration Agent losgekoppeld. Met deze opdracht worden de services niet gestopt of verwijderd: verwijder het pakket om dat te doen.

Deze opdracht vereist hogere bevoegdheden dan de rol 'Azure Connected Machine Onboarding'.

Zodra de verbinding met `azcmagent connect`een `azcmagent reconnect` machine is verbroken, gebruikt u niet of u er een nieuwe bron voor wilt maken in Azure.

Verificatieopties:

* Toegang tot token`azcmagent disconnect --access-token <>`
* Service Principal ID en geheim`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Aanmelden voor interactief apparaat`azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>azcmagent opnieuw verbinden

Verbindt deze machine opnieuw met Azure

### <a name="synopsis"></a>Synopsis

Maak de machine opnieuw verbinden met ongeldige referenties met Azure.

Als een machine al een resource in Azure heeft, maar deze niet kan verifiëren, kan deze opnieuw worden verbonden met deze opdracht. Dit is mogelijk als een machine lang genoeg is uitgeschakeld om het certificaat te laten verlopen (ten minste 45 dagen).

Als een machine `azcmagent disconnect`is `azcmagent connect` losgekoppeld met, gebruik in plaats daarvan.

Met deze opdracht worden de verificatieopties gebruikt om nieuwe referenties op te halen die overeenkomen met de Azure Resource Manager-bron die deze machine vertegenwoordigt.

Deze opdracht vereist hogere bevoegdheden dan de Azure **Connected Machine Onboarding-rol.**

Verificatieopties

* Toegang tot token`azcmagent reconnect --access-token <>`
* Service Principal ID en geheim`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Aanmelden voor interactief apparaat`azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent show

Krijgt machine metadata en Agent status. Dit is vooral handig voor het oplossen van problemen.

### <a name="synopsis"></a>Synopsis

Krijgt machine metadata en Agent status. Dit is vooral handig voor het oplossen van problemen.


### <a name="syntax"></a>Syntaxis

```
azcmagent show [flags]
```

### <a name="options"></a>Opties

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent versie

De versie van de hybride beheeragent weergeven

### <a name="synopsis"></a>Synopsis

De versie van de hybride beheeragent weergeven

### <a name="syntax"></a>Syntaxis

```none
azcmagent version [flags]
```

### <a name="options"></a>Opties

```none
  -h, --help   help for version
```
