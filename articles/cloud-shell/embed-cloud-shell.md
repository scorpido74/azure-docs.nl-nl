---
title: Azure Cloud Shell insluiten | Microsoft Documenten
description: Meer informatie over het insluiten van Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60614313"
---
# <a name="embed-azure-cloud-shell"></a>Azure Cloud Shell insluiten

Door Cloud Shell in te bedden, kunnen ontwikkelaars en contentschrijvers Cloud Shell rechtstreeks openen vanuit een speciale URL, [shell.azure.com.](https://shell.azure.com) Dit brengt onmiddellijk de volledige kracht van cloud shell's authenticatie, tooling en up-to-date Azure CLI / Azure PowerShell tools aan uw gebruikers.

Knop Normale grootte

[![Regelmatige lancering](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Knop groot formaat

[![Grote lancering](https://shell.azure.com/images/launchcloudshell@2x.png "Azure Cloud Shell starten")](https://shell.azure.com)

## <a name="how-to"></a>Uitleg

Integreer de startknop van Cloud Shell in afwaarderingsbestanden door het volgende te kopiëren:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

De HTML om een pop-up Cloud Shell in te sluiten staat hieronder:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Ervaring aanpassen

Stel een specifieke shell-ervaring in door uw URL uit te breiden.

|Ervaring   |URL   |
|---|---|
|Meest recent gebruikte shell   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell snelaan de start](quickstart.md)<br>
[PowerShell in Cloud Shell snel van start](quickstart-powershell.md)
