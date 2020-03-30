---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123184"
---
De Azure File Sync-agent wordt regelmatig bijgewerkt om nieuwe functionaliteit toe te voegen en problemen op te lossen. We raden u aan Microsoft Update te configureren om updates te ontvangen voor de Azure File Sync-agent zodra deze beschikbaar zijn.

#### <a name="major-vs-minor-agent-versions"></a>Belangrijke versus kleine agentversies
* Belangrijke agentversies bevatten vaak nieuwe functies en hebben een toenemend aantal als het eerste deel van het versienummer. Bijvoorbeeld: \*2.\*.\*\*
* Minor agent versies worden ook wel "patches" en worden vaker uitgebracht dan grote versies. Ze bevatten vaak bug fixes en kleinere verbeteringen, maar geen nieuwe functies. Bijvoorbeeld: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Upgradepaden
Er zijn vier goedgekeurde en geteste manieren om de Azure File Sync-agent-updates te installeren. 
1. **(Voorkeur) Microsoft Update configureren om updates van agenten automatisch te downloaden en te installeren.**  
    We raden u altijd aan om elke Azure File Sync-update te gebruiken om ervoor te zorgen dat u toegang hebt tot de nieuwste oplossingen voor de serveragent. Microsoft Update maakt dit proces naadloos, door automatisch updates voor u te downloaden en te installeren.
2. **Gebruik AfsUpdater.exe om agentupdates te downloaden en te installeren.**  
    De AfsUpdater.exe bevindt zich in de agentinstallatiemap. Dubbelklik op de uitvoerbare om agentupdates te downloaden en te installeren. 
3. **Patch een bestaande Azure File Sync-agent met een Microsoft Update-patchbestand of een .msp-uitvoerbaar bestand. Het nieuwste Azure File Sync update-pakket kan worden gedownload uit de [Microsoft Update Catalog.](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)**  
    Als u een .msp-uitvoerbare uitvoert, wordt uw Azure File Sync-installatie geüupgradet met dezelfde methode die automatisch wordt gebruikt door Microsoft Update in het vorige upgradepad. Als u een Microsoft Update-patch toepast, wordt een in-place upgrade van een Azure File Sync-installatie uitgevoerd.
4. **Download het nieuwste Azure File Sync-agent-installatieprogramma van het [Microsoft Downloadcentrum.](https://go.microsoft.com/fwlink/?linkid=858257)**  
    Als u een bestaande installatie van azure bestandssynchronisatieagent wilt upgraden, verwijdert u de oudere versie en installeert u de nieuwste versie van het gedownloade installatieprogramma. De serverregistratie, synchronisatiegroepen en andere instellingen worden onderhouden door het installatieprogramma Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Beheer van de levenscyclus van automatische agent
Met agent versie 6 heeft het team voor bestandssynchronisatie een functie voor automatische upgrade van een agent geïntroduceerd. U een van de twee modi selecteren en een onderhoudsvenster opgeven waarin de upgrade op de server wordt uitgevoerd. Deze functie is ontworpen om u te helpen met het beheer van de levenscyclus van de agent door een vangrail te bieden die voorkomt dat uw agent verloopt of door een probleemloze, huidige instelling te laten verlopen.
1. Met **de standaardinstelling** wordt geprobeerd te voorkomen dat de agent verloopt. Binnen 21 dagen na de geboekte vervaldatum van een agent, zal de agent proberen om zichzelf te upgraden. Het zal een poging starten om één keer per week te upgraden binnen 21 dagen voor het verstrijken en in het geselecteerde onderhoudsvenster. **Deze optie elimineert niet de noodzaak voor het nemen van reguliere Microsoft Update-patches.**
1. Optioneel u selecteren dat de agent zichzelf automatisch upgradet zodra er een nieuwe agentversie beschikbaar is (momenteel niet van toepassing op geclusterde servers). Deze update vindt plaats tijdens het geselecteerde onderhoudsvenster en stelt uw server in staat om te profiteren van nieuwe functies en verbeteringen zodra deze algemeen beschikbaar zijn. Dit is de aanbevolen, zorgeloze instelling die belangrijke agentversies en regelmatige updatepatches naar uw server zal bieden. Elke agent vrijgelaten is op GA kwaliteit. Als u deze optie selecteert, vliegt Microsoft de nieuwste agentversie naar u. Geclusterde servers zijn uitgesloten. Zodra het vliegen is voltooid, wordt de agent ook beschikbaar op [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>De instelling voor automatische upgrade wijzigen

In de volgende instructies wordt beschreven hoe u de instellingen wijzigen nadat u het installatieprogramma hebt voltooid, als u wijzigingen moet aanbrengen.

Open een PowerShell-console en navigeer naar de map waar u de synchronisatieagent hebt geïnstalleerd en importeer vervolgens de servercmdlets. Standaard zou dit er ongeveer als volgt uitzien:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

U kunt `Get-StorageSyncAgentAutoUpdatePolicy` uitvoeren om de huidige beleidsinstelling te controleren en te bepalen of u deze wilt wijzigen.

Als u de huidige beleidsinstelling wilt wijzigen in de vertraagde updatetrack, u het als volgt gebruiken:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Als u de huidige beleidsinstelling wilt wijzigen in de directe updatetrack, u het als volgt gebruiken:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanties voor de levenscyclus en wijzigingsbeheer van agenten
Azure File Sync is een cloudservice, die voortdurend nieuwe functies en verbeteringen introduceert. Dit betekent dat een specifieke Azure File Sync-agentversie slechts voor een beperkte tijd kan worden ondersteund. Om uw implementatie te vergemakkelijken, garanderen de volgende regels dat u voldoende tijd en meldingen hebt om agentupdates/upgrades in uw wijzigingsbeheerproces aan te passen:

- Belangrijke agent versies worden ondersteund voor ten minste zes maanden vanaf de datum van de eerste release.
- Wij garanderen dat er een overlapping van ten minste drie maanden tussen de ondersteuning van de grote agent versies. 
- Waarschuwingen worden uitgegeven voor geregistreerde servers met behulp van een binnenkort verlopen agent ten minste drie maanden voorafgaand aan het verstrijken. U controleren of een geregistreerde server een oudere versie van de agent gebruikt onder het gedeelte geregistreerde servers van een Storage Sync-service.
- De levensduur van een secundaire agentversie is gebonden aan de bijbehorende hoofdversie. Bijvoorbeeld, wanneer agent versie 3.0 wordt uitgebracht, agent versies 2. \* zullen allemaal samen verlopen.

> [!Note]
> Als u een agentversie met een vervaldatumwaarschuwing installeert, wordt een waarschuwing weergegeven, maar slaagt dit. Een poging om een verlopen agentversie te installeren of verbinding te maken, wordt niet ondersteund en wordt geblokkeerd.
