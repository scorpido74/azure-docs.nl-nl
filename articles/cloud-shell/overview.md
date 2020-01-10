---
title: Overzicht van Azure Cloud Shell | Microsoft Docs
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
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832477"
---
# <a name="overview-of-azure-cloud-shell"></a>Overzicht van Azure Cloud Shell
Azure Cloud Shell is een interactieve, geverifieerde shell die toegankelijk is voor browsers voor het beheer van Azure-resources.
Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken: Bash of PowerShell.

Probeer vanuit shell.azure.com door hieronder te klikken.

[![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

Probeer uit Azure Portal met behulp van het Cloud Shell pictogram.

![Portal starten](media/overview/portal-launch-icon.png)

## <a name="features"></a>Functies

### <a name="browser-based-shell-experience"></a>Shell-ervaring op basis van een browser
Cloud Shell biedt toegang tot een op een browser gebaseerde opdracht regel ervaring die is gebouwd met Azure-beheer taken.
Maak gebruik van Cloud Shell om niet-vastmaken aan de hand van een lokale machine te werken op een manier die alleen in de cloud kan worden verstrekt.

### <a name="choice-of-preferred-shell-experience"></a>Keuze van de voorkeurs shell-ervaring
Gebruikers kunnen kiezen tussen bash of Power shell.
1. Selecteer **Cloud shell**.

    ![Cloud Shell pictogram](media/overview/overview-cloudshell-icon.png)

2. Selecteer **bash** of **Power shell**.

    ![Kies bash of Power shell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Geverifieerd en geconfigureerd Azure-werk station
Cloud Shell wordt beheerd door micro soft, zodat het beschikt over populaire opdracht regel Programma's en taal ondersteuning. Cloud Shell wordt ook automatisch geauthenticeerd voor directe toegang tot uw resources via de Azure CLI-of Azure PowerShell-cmdlets.

Bekijk de volledige [lijst met hulpprogram ma's die zijn geïnstalleerd in Cloud shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Geïntegreerde Cloud Shell editor
Cloud Shell biedt een geïntegreerde grafische tekst editor op basis van de open-source-Monaco-editor. U kunt eenvoudig configuratie bestanden maken en bewerken door `code .` uit te voeren voor een naadloze implementatie via Azure CLI of Azure PowerShell.

Meer [informatie over de Cloud shell editor](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Geïntegreerd met docs.microsoft.com

U kunt Cloud Shell rechtstreeks vanuit documentatie die op [docs.Microsoft.com](https://docs.microsoft.com)wordt gehost gebruiken. Het is geïntegreerd in [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure POWERSHELL](https://docs.microsoft.com/powershell/azure/overview) en [Azure cli-documentatie](https://docs.microsoft.com/cli/azure) : Klik op de knop ' proberen ' in een code fragment om de insluitende shell-ervaring te openen. 

### <a name="multiple-access-points"></a>Meerdere toegangs punten
Cloud Shell is een flexibel hulp programma dat kan worden gebruikt vanuit:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-documentatie](https://docs.microsoft.com/cli/azure)
* [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio code Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>De opslag van uw Microsoft Azure-bestanden verbinden
Cloud Shell machines zijn tijdelijk, maar uw bestanden worden op twee manieren bewaard: via een schijf installatie kopie en via een gekoppelde bestands share met de naam `clouddrive`.  De eerste keer dat u Cloud Shell opstart, verschijnt de vraag of er namens u een resourcegroep, opslagaccount en Azure Files-share kan worden gemaakt. Dit is een eenmalige stap die automatisch wordt gekoppeld voor alle sessies. Eén bestands share kan worden toegewezen en wordt gebruikt door zowel bash als Power shell in Cloud Shell.

Lees meer voor meer informatie over het koppelen van een [Nieuw of bestaand opslag account](persisting-shell-storage.md) of voor meer informatie over de [persistentie mechanismen die worden gebruikt in Cloud shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Azure Storage-firewall wordt niet ondersteund voor Cloud shell-opslag accounts.

## <a name="concepts"></a>Concepten
* Cloud Shell wordt uitgevoerd op een tijdelijke host die wordt verstrekt per sessie per gebruiker
* Cloud Shell na 20 minuten een time-out zonder interactieve activiteit
* Cloud Shell vereist dat een Azure-bestands share wordt gekoppeld
* Cloud Shell gebruikt dezelfde Azure-bestands share voor zowel bash als Power shell
* Aan Cloud Shell is één computer per gebruikers account toegewezen
* Cloud Shell blijft $HOME met behulp van een installatie kopie van 5 GB die is opgeslagen in uw bestands share
* Machtigingen worden ingesteld als een reguliere Linux-gebruiker in bash

Meer informatie over functies in [bash in Cloud shell](features.md) en [power shell in Cloud shell](features-powershell.md).

## <a name="pricing"></a>Prijzen
De machine die Cloud Shell host, is gratis, met een vereiste van een gekoppelde Azure Files share. Normale opslag kosten zijn van toepassing.

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell Quick Start](quickstart.md) <br>
[Power shell in Cloud Shell Quick Start](quickstart-powershell.md)
