---
title: Problemen met de upgrade van de Microsoft Azure Site Recovery Provider oplossen
description: Veelvoorkomende problemen oplossen bij het upgraden van de Microsoft Azure Site Recovery-provider.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893900"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Problemen met het upgraden van de Microsoft Azure Site Recovery-provider oplossen

Met dit artikel u problemen oplossen die storingen kunnen veroorzaken tijdens een upgrade van de Microsoft Azure Site Recovery Provider.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>De upgrade mislukt als u meldt dat de nieuwste siteherstelprovider al is geïnstalleerd

Bij het upgraden van Microsoft Azure Site Recovery Provider (DRA) mislukt de upgrade Unified Setup en geeft het foutbericht het foutbericht af:

Upgrade wordt niet ondersteund omdat een hogere versie van de software al is geïnstalleerd.

Ga als volgt te werk om te upgraden:

1. Download de Microsoft Azure Site Recovery Unified Setup:
   1. Selecteer in het gedeelte 'Koppelingen naar momenteel ondersteunde update-rollups' van het artikel [Service-updates in Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) de provider waarnaar u een upgrade uitvoert.
   2. Zoek op de rolluppagina de sectie **Informatie bijwerken** en download de rollup bijwerken voor Microsoft Azure Site Recovery Unified Setup.

2. Open een opdrachtprompt en navigeer naar de map waarnaar u het bestand Unified Setup hebt gedownload. Haal de installatiebestanden uit de download met de volgende opdracht MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;mappad voor de geëxtraheerde bestanden&gt;.
    
    Voorbeeldopdracht:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Geëxtraheerd

3. Navigeer in de opdrachtprompt naar de map waarnaar u de bestanden hebt geëxtraheerd en voer de volgende installatieopdrachten uit:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Ga terug naar de map waarnaar u de Unified Setup hebt gedownload en voer MicrosoftAzureSiteRecoveryUnifiedSetup.exe uit om de upgrade te voltooien. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Upgradefout als gevolg van de naam van de map van derden

Om de upgrade te laten slagen, mag de map van de derde partij niet worden hernoemd.

Om het probleem op te lossen.

1. Start de registereditor (regedit.exe) en open de HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 branch.
1. Controleer `Build_Version` de sleutelwaarde. Als deze is ingesteld op de nieuwste versie, vermindert u het versienummer. Bijvoorbeeld, als de nieuwste versie is 9.22. \* en `Build_Version` de sleutel ingesteld op die waarde, dan te verlagen tot 9,21. \*.
1. Download de nieuwste Microsoft Azure Site Recovery Unified Setup:
   1. Selecteer in het gedeelte 'Koppelingen naar momenteel ondersteunde update-rollups' van het artikel [Service-updates in Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) de provider waarnaar u een upgrade uitvoert.
   2. Zoek op de rolluppagina de sectie **Informatie bijwerken** en download de rollup bijwerken voor Microsoft Azure Site Recovery Unified Setup.
1. Open een opdrachtprompt en navigeer naar de map waarnaar u het Unified Setup-bestand hebt gedownload en de installatiebestanden uit de&lt;download halen met&gt;de volgende opdracht, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x: mappad voor de geëxtraheerde bestanden .

    Voorbeeldopdracht:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Geëxtraheerd

1. Navigeer in de opdrachtprompt naar de map waarnaar u de bestanden hebt geëxtraheerd en voer de volgende installatieopdrachten uit:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Gebruik taakbeheer om de voortgang van de installatie te controleren. Wanneer het proces voor CX_THIRDPARTY_SETUP. EXE is niet langer zichtbaar in taakbeheer, ga naar de volgende stap.
1. Controleer of C:\thirdparty bestaat en of de map de RRD-bibliotheken bevat.
1. Ga terug naar de map waarnaar u de Unified Setup hebt gedownload en voer MicrosoftAzureSiteRecoveryUnifiedSetup.exe uit om de upgrade te voltooien. 