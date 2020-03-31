---
title: Overzicht van Azure Cloud Shell | Microsoft Documenten
description: Overzicht van de Azure Cloud Shell.
services: ''
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
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832477"
---
# <a name="overview-of-azure-cloud-shell"></a>Overzicht van Azure Cloud Shell
Azure Cloud Shell is een interactieve, geverifieerde, via de browser toegankelijke shell voor het beheren van Azure-resources.
Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken: Bash of PowerShell.

Probeer vanaf shell.azure.com door hieronder te klikken.

[![Start insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Probeer vanuit Azure-portal het cloudshell-pictogram.

![Portal lancering](media/overview/portal-launch-icon.png)

## <a name="features"></a>Functies

### <a name="browser-based-shell-experience"></a>Browsergebaseerde shell-ervaring
Cloud Shell biedt toegang tot een op de browser gebaseerde opdrachtlijnervaring die is gebouwd met Azure-beheertaken in het achterhoofd.
Maak gebruik van Cloud Shell om ongebonden te werken vanaf een lokale machine op een manier die alleen de cloud kan bieden.

### <a name="choice-of-preferred-shell-experience"></a>Keuze van voorkeursshell-ervaring
Gebruikers kunnen kiezen tussen Bash of PowerShell.
1. Selecteer **Cloud Shell**.

    ![Pictogram Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Selecteer **Bash** of **PowerShell**.

    ![Kies Bash of PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Geverifieerd en geconfigureerd Azure-werkstation
Cloud Shell wordt beheerd door Microsoft, dus het wordt geleverd met populaire command-line tools en taalondersteuning. Cloud Shell verifieert ook automatisch automatisch voor directe toegang tot uw resources via de Azure CLI- of Azure PowerShell-cmdlets.

Bekijk de volledige [lijst met hulpprogramma's die zijn geïnstalleerd in Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Geïntegreerde Cloud Shell-editor
Cloud Shell biedt een geïntegreerde grafische teksteditor op basis van de open-source Monaco Editor. Maak en bewerk configuratiebestanden `code .` door te draaien voor naadloze implementatie via Azure CLI of Azure PowerShell.

[Meer informatie over de Cloud Shell-editor](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Geïntegreerd met docs.microsoft.com

U Cloud Shell rechtstreeks gebruiken vanuit documentatie die wordt gehost op [docs.microsoft.com.](https://docs.microsoft.com) Het is geïntegreerd in [Microsoft Learn,](https://docs.microsoft.com/learn/) [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) en [Azure CLI-documentatie](https://docs.microsoft.com/cli/azure) - klik op de knop 'Probeer het' in een codefragment om de meeslepende shell-ervaring te openen. 

### <a name="multiple-access-points"></a>Meerdere toegangspunten
Cloud Shell is een flexibel hulpmiddel dat kan worden gebruikt vanuit:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-documentatie](https://docs.microsoft.com/cli/azure)
* [Documentatie voor Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Azure-accountextensie Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Uw Microsoft Azure Files-opslag verbinden
Cloud Shell-machines zijn tijdelijk, maar uw bestanden blijven op twee manieren bestaan: via `clouddrive`een schijfafbeelding en via een gemonteerde bestandsshare met de naam .  De eerste keer dat u Cloud Shell opstart, verschijnt de vraag of er namens u een resourcegroep, opslagaccount en Azure Files-share kan worden gemaakt. Dit is een eenmalige stap en wordt automatisch gekoppeld voor alle sessies. Een enkele bestandsshare kan in kaart worden gebracht en wordt gebruikt door zowel Bash als PowerShell in Cloud Shell.

Lees meer voor meer informatie over het monteren van een [nieuw of bestaand opslagaccount](persisting-shell-storage.md) of voor meer informatie over de [persistentiemechanismen die worden gebruikt in Cloud Shell.](persisting-shell-storage.md#how-cloud-shell-storage-works)

> [!NOTE]
> Azure storage firewall wordt niet ondersteund voor cloud shell storage accounts.

## <a name="concepts"></a>Concepten
* Cloud Shell draait op een tijdelijke host die per sessie per gebruiker wordt geleverd
* Cloud Shell vergaat na 20 minuten zonder interactieve activiteit
* Cloud Shell vereist dat een Azure-bestandsshare wordt gemonteerd
* Cloud Shell gebruikt hetzelfde Azure-bestandsaandeel voor zowel Bash als PowerShell
* Cloud Shell krijgt één machine per gebruikersaccount toegewezen
* Cloud Shell blijft $HOME met behulp van een afbeelding van 5 GB die in uw bestandsshare wordt bewaard
* Machtigingen worden ingesteld als een gewone Linux-gebruiker in Bash

Meer informatie over functies in [Bash in Cloud Shell](features.md) en [PowerShell in Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Prijzen
De machine die Cloud Shell host, is gratis, met een vereiste van een gemonteerd Azure-bestandenaandeel. Er zijn normale opslagkosten van toepassing.

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell snelaan de start](quickstart.md) <br>
[PowerShell in Cloud Shell snel van start](quickstart-powershell.md)
