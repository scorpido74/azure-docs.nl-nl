---
title: Problemen met Azure-Arc voor Server Agent-verbindings problemen oplossen
description: In dit artikel wordt beschreven hoe u problemen oplost en oplost met de verbonden machine agent die zich voordoet met Azure Arc voor servers (preview) wanneer u probeert verbinding te maken met de service.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261798"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Problemen met de verbindings problemen van de verbonden computer agent oplossen

Dit artikel bevat informatie over het oplossen van problemen die zich kunnen voordoen bij het configureren van de Azure-Arc voor servers (preview) verbonden machine agent voor Windows of Linux. Bij het configureren van de verbinding met de service, worden zowel de interactieve installatie als de schaal op de juiste wijze gebruikt. Zie [Arc for servers Overview](./overview.md)(Engelstalig) voor algemene informatie.

## <a name="agent-verbose-log"></a>Uitgebreid logboek van agent

Vóór het volgen van de stappen voor probleem oplossing die verderop in dit artikel worden beschreven, is de minimale informatie die u nodig hebt, het uitgebreide logboek. Het bevat de uitvoer van de **azcmagent** -hulp programma-opdrachten wanneer het argument uitgebreid (-v) wordt gebruikt. De logboek bestanden worden geschreven naar `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` voor Windows en Linux naar `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Hier volgt een voor beeld van de opdracht om uitgebreide logboek registratie in te scha kelen met de verbonden machine agent voor Windows wanneer u een interactieve installatie uitvoert.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Hier volgt een voor beeld van de opdracht om uitgebreide logboek registratie in te scha kelen met de verbonden machine agent voor Windows wanneer u een installatie op schaal uitvoert met behulp van een service-principal.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Hier volgt een voor beeld van de opdracht om uitgebreide logboek registratie in te scha kelen met de verbonden machine agent voor Linux wanneer een interactieve installatie wordt uitgevoerd.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Hier volgt een voor beeld van de opdracht om uitgebreide logboek registratie in te scha kelen met de verbonden machine agent voor Linux wanneer u een installatie op schaal uitvoert met behulp van een service-principal.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemen met de agent verbinding met de service

De volgende tabel bevat enkele van de bekende fouten en suggesties voor het oplossen van problemen en het oplossen ervan.

|Bericht |Fout |Mogelijke oorzaak |Oplossing |
|--------|------|---------------|---------|
|Kan de apparaat stroom voor het verificatie token niet ophalen |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Kan `login.windows.net` eind punt niet bereiken | Controleer de verbinding met het eind punt. |
|Kan de apparaat stroom voor het verificatie token niet ophalen |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |De toegang tot het eind punt wordt geblokkeerd door de proxy of firewall `login.windows.net` . | Controleer de verbinding met het eind punt en wordt deze niet geblokkeerd door een firewall of proxy server. |
|Kan geen autorisatie token verkrijgen van de SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |De toegang tot het eind punt wordt geblokkeerd door de proxy of firewall `login.windows.net` . |Controleer de verbinding met het eind punt en wordt deze niet geblokkeerd door een firewall of proxy server. |
|Kan geen autorisatie token verkrijgen van de SPN |`Invalid client secret is provided` |Onjuist of ongeldig Service-Principal Secret. |Controleer het geheim van de Service-Principal. |
| Kan geen autorisatie token verkrijgen van de SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Onjuiste Service-Principal en/of Tenant-ID. |Controleer de Service-Principal en/of de Tenant-ID.|
|Reactie van ARM-resource ophalen |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Verkeerde referenties en/of machtigingen |Controleer of de service-principal lid is van de voor bereide rol **Azure connectd machine** . |
|Kan de ARM-resource niet AzcmagentConnect |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure-resource providers zijn niet geregistreerd. |Registreer de [resource providers](./agent-overview.md#register-azure-resource-providers). |
|Kan de ARM-resource niet AzcmagentConnect |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |De toegang tot het eind punt wordt geblokkeerd door de proxy server of firewall `management.azure.com` . |Controleer de verbinding met het eind punt en wordt deze niet geblokkeerd door een firewall of proxy server. |

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.

* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.