---
title: Toepassingsconsistente back-ups van Linux VM's
description: Maak toepassingsconsistente back-ups van uw Virtuele Linux-machines naar Azure. In dit artikel wordt uitgelegd in het configureren van het scriptframework om een back-up te maken van linux-VM's die door Azure zijn geïmplementeerd. Dit artikel bevat ook informatie over het oplossen van problemen.
ms.reviewer: anuragm
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 36eeb9f63c67a01bf37412101e23be035596de94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173009"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Toepassingsconsistente back-up van Azure Linux VM's

Wanneer u back-upmomentopnamen van uw VM's maakt, betekent de consistentie van de toepassing dat uw toepassingen worden gestart wanneer de VM's worden opgestart nadat ze zijn hersteld. Zoals u zich voorstellen, is consistentie van de toepassing uiterst belangrijk. Om ervoor te zorgen dat uw Linux VM's consistent zijn, u het Linux-prescript- en post-script-framework gebruiken om toepassingsconsistente back-ups te maken. Het pre-script- en postscriptframework ondersteunt linux-virtuele machines die door Azure Resource Manager zijn geïmplementeerd. Scripts voor toepassingsconsistentie bieden geen ondersteuning voor door Service Manager geïmplementeerde virtuele machines of virtuele Windows-machines.

## <a name="how-the-framework-works"></a>Hoe het framework werkt

Het framework biedt een optie om aangepaste prescripts en postscripts uit te voeren terwijl u VM-snapshots maakt. Prescripts worden uitgevoerd vlak voordat u de VM-momentopname maakt en postscripts worden onmiddellijk uitgevoerd nadat u de VM-momentopname hebt gemaakt. Prescripts en postscripts bieden de flexibiliteit om uw toepassing en omgeving te beheren, terwijl u VM-snapshots maakt.

Pre-scripts roepen native application API's aan, die de IOs opschrijven en inhoud in het geheugen naar de schijf spoelen. Deze acties zorgen ervoor dat de momentopname consistent is. Post-scripts gebruiken native application API's om de IOs te ontdooien, waardoor de toepassing de normale bewerkingen na de VM-momentopname kan hervatten.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Stappen voor het configureren van pre-script en post-script

1. Meld u aan als de hoofdgebruiker bij de Linux-VM waar u een back-up van wilt maken.

2. Download **VMSnapshotScriptPluginConfig.json** vanaf [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)en kopieer deze naar de **/etc/azure-map** voor alle VM's waarvan u een back-up wilt maken. Als de **map /etc/azure** niet bestaat, maakt u deze.

3. Kopieer het voorscript en het postscript voor uw toepassing op alle VM's die u van plan bent een back-up te maken. U de scripts naar elke locatie op de VM kopiëren. Zorg ervoor dat u het volledige pad van de scriptbestanden in het **VMSnapshotScriptPluginConfig.json-bestand** bijwerkt.

4. Controleer de volgende machtigingen voor deze bestanden:

   - **VMSnapshotScriptPluginConfig.json**: Toestemming "600." Alleen 'root'-gebruiker moet bijvoorbeeld machtigingen voor 'lezen' en 'schrijven' voor dit bestand hebben en geen enkele gebruiker mag machtigingen hebben voor 'uitvoeren'.

   - **Pre-script bestand**: Toestemming "700."  Alleen 'root'-gebruiker moet bijvoorbeeld machtigingen voor dit bestand hebben 'lezen', 'schrijven' en 'uitvoeren'.

   - **Post-script** Toestemming '700'. Alleen 'root'-gebruiker moet bijvoorbeeld machtigingen voor dit bestand hebben 'lezen', 'schrijven' en 'uitvoeren'.

   > [!IMPORTANT]
   > Het framework geeft gebruikers veel macht. Beveilig het framework en zorg ervoor dat alleen de "root" gebruiker toegang heeft tot kritieke JSON- en scriptbestanden.
   > Als niet aan de vereisten wordt voldaan, wordt het script niet uitgevoerd, wat resulteert in een crash van het bestandssysteem en een inconsistente back-up.
   >

5. Configureer **VMSnapshotScriptPluginConfig.json** zoals hier beschreven:
    - **pluginName:** Laat dit veld zoals het is, of uw scripts werken mogelijk niet zoals verwacht.

    - **preScriptLocatie:** geef het volledige pad op van het voorscript op de VM waarvan een back-up wordt gemaakt.

    - **postScriptLocatie:** geef het volledige pad op van het postscript op de VM waarvan een back-up wordt gemaakt.

    - **preScriptParams:** geef de optionele parameters op die moeten worden doorgegeven aan het prescript. Alle parameters moeten tussen aanhalingstekens staan. Als u meerdere parameters gebruikt, scheidt u de parameters met een komma.

    - **postScriptParams:** Geef de optionele parameters op die moeten worden doorgegeven aan het postscript. Alle parameters moeten tussen aanhalingstekens staan. Als u meerdere parameters gebruikt, scheidt u de parameters met een komma.

    - **preScriptNoOfRetries:** Stel het aantal keren in dat het voorscript opnieuw moet worden geprobeerd als er een fout optreedt voordat het wordt geünd. Nul betekent slechts één poging en geen opnieuw proberen als er een mislukking is.

    - **postScriptNoOfRetries**: Stel het aantal keren in dat het postscript opnieuw moet worden geprobeerd als er een fout optreedt voordat het wordt geünd. Nul betekent slechts één poging en geen opnieuw proberen als er een mislukking is.

    - **time-outInSeconds**: Geef individuele time-outs op voor het voorscript en het postscript (maximale waarde kan 1800 zijn).

    - **continueBackupOnFailure**: Stel deze waarde in op **true** als u wilt dat Azure Backup terugvalt naar een consistent/crash consistent back-up van een bestandssysteem als pre-script of post-script mislukt. Als u dit instelt op **false** mislukt, wordt de back-up mislukt in het geval van scriptfouten (behalve wanneer u een VM met één schijf hebt die terugvalt op crashconsistente back-up, ongeacht deze instelling).

    - **fsFreezeEnabled:** Geef op of Linux fsfreeze moet worden aangeroepen terwijl u de VM-momentopname maakt om de consistentie van het bestandssysteem te garanderen. We raden u aan deze instelling op **true** te houden, tenzij uw toepassing afhankelijk is van het uitschakelen van fsfreeze.

    - **ScriptsExecutionPollTimeSeconds:** Stel de tijd in die de extensie moet uitslapen tussen elke poll en de uitvoering van het script. Als de waarde bijvoorbeeld 2 is, controleert de extensie of de uitvoering van het voor-/postscript elke 2 seconden is voltooid. De minimale en maximale waarde die het kan nemen is respectievelijk 1 en 5. De waarde moet strikt een geheel getal zijn.

6. Het scriptframework is nu geconfigureerd. Als de VM-back-up al is geconfigureerd, wordt in de volgende back-up de scripts aangesproken en wordt een consistente back-up van toepassingen geactiveerd. Als de VM-back-up niet is geconfigureerd, configureert u deze met [back-ups van virtuele Azure-machines naar vaults van Recovery Services.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Problemen oplossen

Zorg ervoor dat u de juiste logboekregistratie toevoegt tijdens het schrijven van uw pre-script en post-script en controleer uw scriptlogboeken om scriptproblemen op te lossen. Als u nog steeds problemen hebt met het uitvoeren van scripts, raadpleegt u de volgende tabel voor meer informatie.

| Fout | Foutbericht | Aanbevolen actie |
| ------------------------ | -------------- | ------------------ |
| Pre-scriptexecutionmislukt |Het prescript heeft een fout geretourneerd, zodat de back-up mogelijk niet consistent is.| Kijk naar de foutlogboeken voor uw script om het probleem op te lossen.|  
|Post-scriptexecutionmislukt |Het postscript heeft een fout geretourneerd die van invloed kan zijn op de toepassingsstatus. |Kijk naar de foutlogboeken voor uw script om het probleem op te lossen en controleer de toepassingsstatus. |
| Pre-ScriptNotFound |Het voorscript is niet gevonden op de locatie die is opgegeven in het **VMSnapshotScriptPluginConfig.json** config-bestand. |Zorg ervoor dat het voorscript aanwezig is op het pad dat is opgegeven in het config-bestand om een consistente back-up voor toepassingen te garanderen.|
| Post-ScriptNotFound |Het postscript is niet gevonden op de locatie die is opgegeven in het **VMSnapshotScriptPluginConfig.json** config-bestand. |Zorg ervoor dat het script aanwezig is op het pad dat is opgegeven in het config-bestand om een consistente back-up te garanderen.|
| Onjuist PluginhostFile |Het **Pluginhost-bestand,** dat wordt geleverd met de VmSnapshotLinux-extensie, is beschadigd, dus pre-script en post-script kunnen niet worden uitgevoerd en de back-up is niet toepassingsconsistent.| Verwijder de **VmSnapshotLinux-extensie** en deze wordt automatisch opnieuw geïnstalleerd met de volgende back-up om het probleem op te lossen. |
| OnjuistJSONConfigFile | Het **VMSnapshotScriptPluginConfig.json-bestand** is onjuist, dus pre-script en post-script kunnen niet worden uitgevoerd en de back-up is niet toepassingsconsistent. | Download de kopie van [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) en configureer deze opnieuw. |
| OnvoldoendePermissionforPre-Script | Voor het uitvoeren van scripts moet de "root"-gebruiker de eigenaar van het bestand zijn en moet het bestand machtigingen voor "700" hebben (dat wil zeggen dat alleen "eigenaar" machtigingen moet hebben "lezen", "schrijven" en "uitvoeren"). | Zorg ervoor dat "root" gebruiker is de "eigenaar" van het script bestand en dat alleen "eigenaar" heeft "lezen", "schrijven" en "uitvoeren" machtigingen. |
| OnvoldoendeMachtigingenvoorPost-Script | Voor het uitvoeren van scripts moet de hoofdgebruiker de eigenaar van het bestand zijn en moet het bestand machtigingen voor 700 bevatten (dat wil zeggen dat alleen "eigenaar" machtigingen moet hebben "lezen", "schrijven" en "uitvoeren". | Zorg ervoor dat "root" gebruiker is de "eigenaar" van het script bestand en dat alleen "eigenaar" heeft "lezen", "schrijven" en "uitvoeren" machtigingen. |
| Pre-ScriptTimeout | De uitvoering van de toepassingsconsistente back-up pre-script time-out. | Controleer het script en verhoog de time-out in het **VMSnapshotScriptPluginConfig.json-bestand** dat zich op **/etc/azure bevindt.** |
| Post-ScriptTimeout | Er is een time-out ophet moment dat de toepassing-consistent back-up na het script is uitgevoerd. | Controleer het script en verhoog de time-out in het **VMSnapshotScriptPluginConfig.json-bestand** dat zich op **/etc/azure bevindt.** |

## <a name="next-steps"></a>Volgende stappen

[VM-back-up configureren naar een vault van Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
