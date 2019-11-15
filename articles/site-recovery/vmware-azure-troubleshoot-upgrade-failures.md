---
title: Problemen met de upgrade van de Microsoft Azure Site Recovery provider oplossen
description: Los veelvoorkomende problemen op die optreden bij het upgraden van de Microsoft Azure Site Recovery provider.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 0e6631d36f8b8153c34f787f8941b45b7522aceb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083929"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Problemen met het upgraden van de Microsoft Azure Site Recovery-provider oplossen

Dit artikel helpt u bij het oplossen van problemen die fouten kunnen veroorzaken tijdens een Microsoft Azure Site Recovery provider upgrade.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>De upgrade mislukt met de melding dat de nieuwste Site Recovery-provider al is geïnstalleerd.

Bij de upgrade van Microsoft Azure Site Recovery provider (DRA), mislukt de installatie van de Unified Setup en wordt het volgende fout bericht weer gegeven:

De upgrade wordt niet ondersteund omdat er al een hogere versie van de software is geïnstalleerd.

Voer de volgende stappen uit om een upgrade uit te voeren:

1. Down load de Microsoft Azure Site Recovery Unified Setup:
   1. Selecteer in de sectie Koppelingen naar momenteel ondersteunde update pakketten van de service- [updates in azure site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) -artikel de provider waarmee u een upgrade uitvoert.
   2. Ga op de pagina samen vouwen naar de sectie **informatie over updates** en down load het update pakket voor Microsoft Azure site Recovery Unified Setup.

2. Open een opdracht prompt en navigeer naar de map waarnaar u het Unified Setup-bestand hebt gedownload. Pak de installatie bestanden uit met behulp van de volgende opdracht, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;mappad voor de uitgepakte bestanden&gt;.
    
    Van de voorbeeldopdracht:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. Ga in de opdracht prompt naar de map waarin u de bestanden hebt uitgepakt en voer de volgende installatie opdrachten uit:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Ga terug naar de map waarnaar u de geïntegreerde installatie hebt gedownload en voer MicrosoftAzureSiteRecoveryUnifiedSetup. exe uit om de upgrade te volt ooien. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>De upgrade is mislukt omdat de naam van de externe map wordt gewijzigd

De upgrade kan alleen worden uitgevoerd als de naam van de map van de derde partij niet is gewijzigd.

Om het probleem op te lossen.

1. Start de REGI ster-editor (Regedit. exe) en open de vertakking HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Inspecteer de `Build_Version` sleutel waarde. Als deze is ingesteld op de meest recente versie, vermindert u het versie nummer. Bijvoorbeeld, als de meest recente versie 9,22 is.\* en de `Build_Version` sleutel ingesteld op die waarde, en verminder deze in 9,21.\*.
1. Down load de nieuwste Microsoft Azure Site Recovery Unified Setup:
   1. Selecteer in de sectie Koppelingen naar momenteel ondersteunde update pakketten van de service- [updates in azure site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) -artikel de provider waarmee u een upgrade uitvoert.
   2. Ga op de pagina samen vouwen naar de sectie **informatie over updates** en down load het update pakket voor Microsoft Azure site Recovery Unified Setup.
1. Open een opdracht prompt en navigeer naar de map waarnaar u het Unified Setup-bestand hebt gedownload en de Setup-bestanden uit de down load te halen met behulp van de volgende opdracht, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;mappad voor de uitgepakte bestanden&gt;.

    Van de voorbeeldopdracht:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. Ga in de opdracht prompt naar de map waarin u de bestanden hebt uitgepakt en voer de volgende installatie opdrachten uit:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Gebruik taak beheer om de voortgang van de installatie te controleren. Wanneer het proces voor CX_THIRDPARTY_SETUP. EXE is niet meer zichtbaar in taak beheer. Ga verder met de volgende stap.
1. Controleer of C:\thirdparty bestaat en of de map de RRD-bibliotheken bevat.
1. Ga terug naar de map waarnaar u de geïntegreerde installatie hebt gedownload en voer MicrosoftAzureSiteRecoveryUnifiedSetup. exe uit om de upgrade te volt ooien. 