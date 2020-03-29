---
title: Azure Cloud Shell-beperkingen | Microsoft Documenten
description: Overzicht van beperkingen van Azure Cloud Shell
services: azure
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
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951476"
---
# <a name="limitations-of-azure-cloud-shell"></a>Beperkingen van Azure Cloud Shell

Azure Cloud Shell heeft de volgende bekende beperkingen:

## <a name="general-limitations"></a>Algemene beperkingen

### <a name="system-state-and-persistence"></a>Systeemstatus en persistentie

De machine die uw Cloud Shell-sessie biedt, is tijdelijk en wordt gerecycled nadat uw sessie gedurende 20 minuten inactief is. Cloud Shell vereist dat een Azure-bestandsshare wordt gemonteerd. Als gevolg hiervan moet uw abonnement opslagbronnen kunnen instellen om toegang te krijgen tot Cloud Shell. Andere overwegingen zijn:

* Met gemonteerde opslag blijven alleen `$Home` wijzigingen in de map bestaan.
* Azure-bestandsshares kunnen alleen worden gestart vanuit uw [toegewezen regio.](persisting-shell-storage.md#mount-a-new-clouddrive)
  * Voer in `env` Bash uit om `ACC_LOCATION`je regio te vinden die is ingesteld als .

### <a name="browser-support"></a>Browserondersteuning

Cloud Shell ondersteunt de nieuwste versies van Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox en Apple Safari. Safari in privémodus wordt niet ondersteund.

### <a name="copy-and-paste"></a>Kopiëren en plakken

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Voor een bepaalde gebruiker kan slechts één shell actief zijn

Gebruikers kunnen slechts één type shell tegelijk starten, **bash** of **PowerShell.** U echter meerdere exemplaren van Bash of PowerShell tegelijk laten uitvoeren. Als u wisselt tussen Bash of PowerShell met behulp van het menu, wordt Cloud Shell opnieuw opgestart, waardoor bestaande sessies worden beëindigd. U ook bash in PowerShell `bash`uitvoeren door te typen en `pwsh`u PowerShell in bash uitvoeren door te typen.

### <a name="usage-limits"></a>Gebruikslimieten

Cloud Shell is bedoeld voor interactieve use cases. Als gevolg hiervan worden langlopende niet-interactieve sessies zonder waarschuwing beëindigd.

## <a name="bash-limitations"></a>Bash beperkingen

### <a name="user-permissions"></a>Gebruikersmachtigingen

Machtigingen worden ingesteld als gewone gebruikers zonder sudo-toegang. Elke installatie `$Home` buiten uw directory wordt niet gehandhaafd.

### <a name="editing-bashrc-or-profile"></a>Bewerken .bashrc of $PROFILE

Wees voorzichtig bij het bewerken van .bashrc of het $PROFILE-bestand van PowerShell, waardoor dit onverwachte fouten in Cloud Shell kan veroorzaken.

## <a name="powershell-limitations"></a>PowerShell-beperkingen

### <a name="azuread-module-name"></a>`AzureAD`modulenaam

De `AzureAD` modulenaam `AzureAD.Standard.Preview`is momenteel, de module biedt dezelfde functionaliteit.

### <a name="sqlserver-module-functionality"></a>`SqlServer`modulefunctionaliteit

De `SqlServer` module in Cloud Shell heeft alleen prerelease-ondersteuning voor PowerShell Core. In het `Invoke-SqlCmd` bijzonder, is nog niet beschikbaar.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standaardbestandslocatie wanneer deze is gemaakt vanuit Azure-station:

Met PowerShell-cmdlets kunnen gebruikers geen bestanden maken onder het Azure:drive. Wanneer gebruikers nieuwe bestanden maken met andere tools, zoals vim `$HOME` of nano, worden de bestanden standaard opgeslagen in de standaardbestanden. 

### <a name="gui-applications-are-not-supported"></a>GUI-toepassingen worden niet ondersteund

Als de gebruiker een opdracht uitvoert waarmee een Dialoogvenster van Windows `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`wordt gemaakt, ziet u een foutbericht zoals: .

### <a name="large-gap-after-displaying-progress-bar"></a>Grote kloof na het weergeven van de voortgangsbalk

Als de gebruiker een actie uitvoert die een voortgangsbalk weergeeft, zoals een tabblad dat in het `Azure:` station wordt voltooid, is het mogelijk dat de cursor niet goed is ingesteld en er een gat wordt weergegeven waar de voortgangsbalk voorheen was.

## <a name="next-steps"></a>Volgende stappen

[Problemen met Cloud Shell oplossen](troubleshooting.md) <br>
[Snelstartgids voor Bash](quickstart.md) <br>
[Snelstartgids voor PowerShell](quickstart-powershell.md)
