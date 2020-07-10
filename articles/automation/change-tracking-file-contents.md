---
title: Wijzigingen bijhouden en inventaris in Azure Automation beheren
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt gebruiken om wijzigingen in de software en micro soft-Services in uw omgeving bij te houden.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185599"
---
# <a name="manage-change-tracking-and-inventory"></a>Wijzigingen bijhouden en inventaris beheren

Wanneer u een nieuw bestand of register sleutel toevoegt om bij te houden, Azure Automation dit voor [Wijzigingen bijhouden en inventaris](change-tracking.md)inschakelen. In dit artikel wordt beschreven hoe u tracering configureert, traceer resultaten controleert en hoe u waarschuwingen afhandelt wanneer er wijzigingen worden gedetecteerd.

Voordat u de procedures in dit artikel gebruikt, moet u ervoor zorgen dat u Wijzigingen bijhouden en inventaris op uw Vm's hebt ingeschakeld met een van de volgende technieken:

* [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](automation-enable-changes-from-auto-acct.md)
* [Wijzigingen bijhouden en inventaris inschakelen door te bladeren door de Azure Portal](automation-enable-changes-from-browse.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook](automation-enable-changes-from-runbook.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanaf een virtuele Azure-machine](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Het bereik voor de implementatie beperken

Wijzigingen bijhouden en inventarisatie gebruikt een scope configuratie in de werk ruimte om de computers te richten op het ontvangen van wijzigingen. Zie [beperken wijzigingen bijhouden en inventarisatie bereik](automation-scope-configurations-change-tracking.md)voor meer informatie.

## <a name="track-files"></a>Bestanden bijhouden

U kunt Wijzigingen bijhouden en inventaris gebruiken om wijzigingen in bestanden en mappen/mappen bij te houden. In deze sectie wordt beschreven hoe u bestands tracering configureert in Windows en op Linux.

### <a name="configure-file-tracking-on-windows"></a>Bestands tracering configureren in Windows

Gebruik de volgende stappen om bestands tracering op Windows-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. 
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer op de pagina werkruimte configuratie de optie **Windows-bestanden**en klik vervolgens op **toevoegen** om een nieuw bestand toe te voegen om bij te houden.
4. Voer in het deel venster Windows-bestand voor Wijzigingen bijhouden toevoegen de informatie in voor het bestand of de map die u wilt bijhouden en klik op **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
    |Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
    |Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld: **c:\Temp \\ \* . txt**. U kunt ook omgevings variabelen gebruiken, zoals `%winDir%\System32\\\*.*` .       |
    |Padtype     | Het type pad. Mogelijke waarden zijn bestand en map.        |    
    |Recursie     | Waar als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden, en ONWAAR anders onwaar.        |    
    |Bestands inhoud uploaden | True voor het uploaden van bestands inhoud op bijgehouden wijzigingen en ONWAAR, anders onwaar.|

5. Zorg ervoor dat u waar opgeeft voor het **uploaden van bestands inhoud**. Met deze instelling schakelt u het bijhouden van bestands inhoud in voor het opgegeven bestandspad.

### <a name="configure-file-tracking-on-linux"></a>Bestands tracering configureren in Linux

Gebruik de volgende stappen om bestands tracering op Linux-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. 
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer op de pagina werkruimte configuratie de optie **Linux-bestanden**en klik vervolgens op **toevoegen** om een nieuw bestand toe te voegen om bij te houden.
4. Voer in het deel venster Linux-bestand voor Wijzigingen bijhouden toevoegen de informatie in voor het bestand of de map die u wilt bijhouden en klik op **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
    |Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
    |Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld **/etc/*. conf**.       |
    |Padtype     | Het type pad. Mogelijke waarden zijn bestand en Directory.        |
    |Recursie     | Waar als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden, en ONWAAR anders onwaar.        |
    |Sudo gebruiken     | True om sudo te gebruiken bij het controleren op het item en ONWAAR, anders false.         |
    |Koppelingen     | Instelling die bepaalt hoe symbolische koppelingen moeten worden verwerkt bij het door lopen van directory's. Mogelijke waarden zijn:<br> Negeren: Hiermee worden symbolische koppelingen genegeerd en worden de bestanden/mappen waarnaar wordt verwezen, niet meegenomen.<br>Follow-volgt de symbolische koppelingen tijdens recursie en bevat ook de bestanden/mappen waarnaar wordt verwezen.<br>Beheren: volgt de symbolische koppelingen en staat de wijziging van geretourneerde inhoud toe.<br>**Opmerking:** De optie beheren wordt niet aanbevolen, omdat deze geen ondersteuning biedt voor het ophalen van bestands inhoud.    |
    |Bestands inhoud uploaden | True voor het uploaden van bestands inhoud op bijgehouden wijzigingen en ONWAAR, anders onwaar. |

5. Zorg ervoor dat u waar opgeeft voor het **uploaden van bestands inhoud**. Met deze instelling schakelt u het bijhouden van bestands inhoud in voor het opgegeven bestandspad.

   ![Linux-bestand toevoegen](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Bestands inhoud bijhouden

Met het bijhouden van bestands inhoud kunt u de inhoud van een bestand voor en na een bijgehouden wijziging bekijken. De functie slaat de inhoud van het bestand op in een [opslag account](../storage/common/storage-account-overview.md) nadat elke wijziging is doorgevoerd. Hier volgen enkele regels om de inhoud van het bestand bij te houden:

* Een standaard opslag account met het Resource Manager-implementatie model is vereist voor het opslaan van bestands inhoud. 
* Gebruik geen Premium-en klassieke implementatie model opslag accounts. Zie [over Azure Storage-accounts](../storage/common/storage-account-create.md).
* U kunt het opslag account koppelen aan slechts één Automation-account.
* [Wijzigingen bijhouden en inventaris](change-tracking.md) moeten zijn ingeschakeld in uw Automation-account.

### <a name="enable-tracking-for-file-content-changes"></a>Bijhouden van wijzigingen in bestands inhoud inschakelen

Voer de volgende stappen uit om het bijhouden van wijzigingen in de bestands inhoud in te scha kelen:

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Wijzigingen bijhouden** onder **configuratie beheer**.
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer **Bestands inhoud** en klik op **koppelen**. Met deze selectie wordt het deel venster locatie van inhoud toevoegen voor Wijzigingen bijhouden geopend.

   ![Locatie van inhoud toevoegen](./media/change-tracking-file-contents/enable.png)

4. Selecteer het abonnement en het opslag account dat moet worden gebruikt voor het opslaan van de bestands inhoud. 

5. Als u het bijhouden van bestands inhoud voor alle bestaande bijgehouden bestanden wilt inschakelen, selecteert u **aan** voor **uploaden van bestands inhoud voor alle instellingen**. U kunt deze instelling voor elk bestandspad later wijzigen.

   ![Opslag account instellen](./media/change-tracking-file-contents/storage-account.png)

6. In Wijzigingen bijhouden en inventaris worden Uri's van het opslag account en de Shared Access Signature (SAS) weer gegeven wanneer het bijhouden van bestands inhoud wordt ingeschakeld. De hand tekeningen verlopen na 365 dagen en u kunt ze opnieuw maken door te klikken op **opnieuw genereren**.

   ![Lijst met account sleutels](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>De inhoud van een bijgehouden bestand weer geven

Zodra Wijzigingen bijhouden en de inventaris een wijziging voor een bijgehouden bestand detecteert, kunt u de inhoud van het bestand bekijken in het deel venster Details wijzigen.  

![Wijzigingen weer geven](./media/change-tracking-file-contents/change-list.png)

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Kies een bestand in de lijst met wijzigingen en selecteer **wijzigingen in bestands inhoud weer geven** om de inhoud van het bestand te bekijken. In het deel venster Details wijzigen ziet u de standaard voor en na de bestands informatie voor elke eigenschap.

   ![Details wijzigen](./media/change-tracking-file-contents/change-details.png)

3. U bekijkt de bestands inhoud in een weer gave naast elkaar. U kunt **inline** selecteren om een inline weer gave van de wijzigingen weer te geven.

## <a name="track-registry-keys"></a>Register sleutels bijhouden

Gebruik de volgende stappen om het bijhouden van register sleutels op Windows-computers te configureren:

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Wijzigingen bijhouden** onder **configuratie beheer**. 
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer op de pagina werkruimte configuratie de optie **Windows-REGI ster**.
4. Klik op **+ toevoegen** om een nieuwe register sleutel aan de tracering toe te voegen.
5. Voer in het deel venster Windows-REGI ster toevoegen voor Wijzigingen bijhouden de gegevens in voor de sleutel die u wilt bijhouden en klik vervolgens op **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als een instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van de register sleutel die moet worden gevolgd.        |
    |Groep     | Groeps naam voor het logisch groeperen van register sleutels.        |
    |Windows-registersleutel   | Sleutel naam met pad, bijvoorbeeld `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Zoek logboeken voor wijzigings records

U kunt verschillende zoek acties uitvoeren op de Azure Monitor logboeken voor wijzigings records. Open de pagina wijzigingen bijhouden en klik op **log Analytics** om de pagina logboeken te openen. De volgende tabel bevat voor beelden van zoek opdrachten in Logboeken voor wijzigings records.

|Query  |Beschrijving  |
|---------|---------|
|`ConfigurationData`<br>&#124;`where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124;`where SvcState == "Stopped"`<br>&#124;`summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Toont de meest recente inventaris records voor micro soft-services die zijn ingesteld op auto, maar die zijn gerapporteerd als gestopt. De resultaten zijn beperkt tot de meest recente record voor de opgegeven software naam en computer.    |
|`ConfigurationChange`<br>&#124;`where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124;`order by TimeGenerated desc`|Geeft wijzigings records weer voor verwijderde software.|

## <a name="create-alerts-on-changes"></a>Waarschuwingen maken voor wijzigingen

In het volgende voor beeld ziet u dat het bestand **c:\Windows\System32\drivers\etc\hosts** op een machine is gewijzigd. Dit bestand is belang rijk omdat Windows het gebruikt om hostnamen om te zetten in IP-adressen. Deze bewerking heeft voor rang op DNS en kan leiden tot verbindings problemen. Het kan ook leiden tot omleiding van verkeer naar kwaad aardige of anderszins gevaarlijke websites.

![Grafiek met de wijziging van het bestand hosts](./media/change-tracking-file-contents/changes.png)

We gebruiken dit voor beeld om de stappen voor het maken van waarschuwingen voor een wijziging te bespreken.

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** onder **configuratie beheer**en selecteer vervolgens **log Analytics**. 
2. Zoek in de logboeken zoeken naar inhouds wijzigingen in het bestand **hosts** met de query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Met deze query wordt gezocht naar wijzigingen in de inhoud voor bestanden met volledig gekwalificeerde padnamen die het woord bevatten `hosts` . U kunt ook een specifiek bestand vragen door het pad naar de volledig gekwalificeerde vorm te wijzigen, bijvoorbeeld met behulp van `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Nadat de query de resultaten heeft geretourneerd, klikt u op **nieuwe waarschuwings regel** in de zoek opdracht voor Logboeken om de pagina voor het maken van een waarschuwing te openen. U kunt ook naar deze pagina navigeren via **Azure monitor** in de Azure Portal. 

4. Controleer de query opnieuw en wijzig de logica van de waarschuwing. In dit geval moet de waarschuwing worden geactiveerd als er zelfs één wijziging wordt gedetecteerd op alle computers in de omgeving.

    ![Wijziging in query voor het bijhouden van wijzigingen in het bestand hosts](./media/change-tracking-file-contents/change-query.png)

5. Nadat de waarschuwings logica is ingesteld, wijst u actie groepen toe om acties uit te voeren als reactie op het activeren van de waarschuwing. In dit geval worden e-mail berichten ingesteld om te worden verzonden en wordt een ITSM-ticket (IT Service Management) gemaakt. 

    ![Actie groep configureren voor waarschuwing bij wijziging](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [limiet voor wijzigingen bijhouden en inventarisatie bereik](automation-scope-configurations-change-tracking.md)voor meer informatie over Scope configuraties.
* Als u wilt zoeken naar logboeken die zijn opgeslagen in uw Log Analytics-werk ruimte, raadpleegt u [Zoek opdrachten in Logboeken in azure monitor logboeken](../azure-monitor/log-query/log-query-overview.md).
* Als u klaar bent met implementaties, raadpleegt u de [werk ruimte ontkoppelen van het Automation-account voor wijzigingen bijhouden en inventarisatie](automation-unlink-workspace-change-tracking.md).
* Zie [Vm's verwijderen uit wijzigingen bijhouden en inventaris](automation-remove-vms-from-change-tracking.md)om uw vm's uit wijzigingen bijhouden en inventaris te verwijderen.
* Zie [problemen met wijzigingen bijhouden-en inventaris problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van functie fouten.
