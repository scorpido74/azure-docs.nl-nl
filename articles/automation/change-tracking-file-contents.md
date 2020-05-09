---
title: Wijzigingen bijhouden en inventaris in Azure Automation beheren
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt gebruiken om wijzigingen in de software en micro soft-Services bij te houden die in uw omgeving optreden.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779294"
---
# <a name="manage-change-tracking-and-inventory"></a>Wijzigingen bijhouden en inventaris beheren

Wanneer u een nieuw bestand of een nieuwe register sleutel toevoegt om bij te houden, Azure Automation deze optie inschakelen voor de functie [Wijzigingen bijhouden en inventaris](change-tracking.md) . Dit artikel bevat procedures voor het werken met deze functie.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>De functie volledige Wijzigingen bijhouden en inventaris inschakelen

Als u [FIM (File Integrity Monitoring) hebt Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)ingeschakeld, kunt u de functie volledige wijzigingen bijhouden en inventaris gebruiken, zoals hieronder wordt beschreven. Uw instellingen worden niet verwijderd door dit proces.

> [!NOTE]
> Het inschakelen van de functie volledige Wijzigingen bijhouden en inventarisatie kan extra kosten veroorzaken. Zie [prijzen voor Automation](https://azure.microsoft.com/pricing/details/automation/).

1. Verwijder de bewakings oplossing door te navigeren naar de werk ruimte en te zoeken in de [lijst met geïnstalleerde bewakings oplossingen](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Klik op de naam van de oplossing om de overzichts pagina te openen en klik vervolgens op **verwijderen**, zoals beschreven in [een bewakings oplossing verwijderen](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Als u Wijzigingen bijhouden en inventaris opnieuw wilt inschakelen, gaat u naar het Automation-account en selecteert u **Wijzigingen bijhouden** onder **configuratie beheer**.
4. Kies de Log Analytics werk ruimte en het Automation-account, bevestig de instellingen van uw werk ruimte en klik op **inschakelen**.

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>Computers onboarden naar Wijzigingen bijhouden en inventaris

Als u wijzigingen wilt bijhouden, moet u Wijzigingen bijhouden en inventaris inschakelen in Azure Automation. Hier volgen de aanbevolen en ondersteunde manieren om uw machines uit te geven voor deze functie: 

* [Onboarding vanaf een virtuele machine](automation-onboard-solutions-from-vm.md)
* [Onboarding van surfen op meerdere computers](automation-onboard-solutions-from-browse.md)
* [Onboarding uitvoeren vanuit uw Automation-account](automation-onboard-solutions-from-automation-account.md)
* [Onboarding in een Azure Automation runbook](automation-onboard-solutions.md)

## <a name="track-files"></a>Bestanden bijhouden

### <a name="configure-file-tracking-on-windows"></a>Bestands tracering configureren in Windows

Gebruik de volgende stappen om bestands tracering op Windows-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. 
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer op de pagina werkruimte configuratie de optie **Windows-bestanden**en klik vervolgens op **toevoegen** om een nieuw bestand toe te voegen om bij te houden.
4. Voer in het deel venster Windows-bestand voor Wijzigingen bijhouden toevoegen de informatie in voor het bestand dat u wilt bijhouden en klik op **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als de instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
    |Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
    |Pad invoeren     | Het pad om het bestand te controleren, bijvoorbeeld: **\\\*c:\Temp. txt**. U kunt ook omgevings variabelen gebruiken, zoals `%winDir%\System32\\\*.*`.       |
    |Padtype     | Het type pad. Mogelijke waarden zijn bestand en Directory.        |    
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
    |Koppelingen     | Instelling die bepaalt hoe symbolische koppelingen moeten worden verwerkt bij het door lopen van directory's. Mogelijke waarden zijn:<br> Negeren: Hiermee worden symbolische koppelingen genegeerd en worden de bestanden/mappen waarnaar wordt verwezen, niet meegenomen.<br>Follow-volgt de symbolische koppelingen tijdens recursie en bevat ook de bestanden/mappen waarnaar wordt verwezen.<br>Beheren: volgt de symbolische koppelingen en staat het wijzigen van geretourneerde inhoud toe. **Opmerking** : deze optie wordt niet aanbevolen omdat het ophalen van bestands inhoud niet wordt ondersteund.    |
    |Bestands inhoud uploaden | True voor het uploaden van bestands inhoud op bijgehouden wijzigingen en ONWAAR, anders onwaar. |

5. Zorg ervoor dat u waar opgeeft voor het **uploaden van bestands inhoud**. Met deze instelling schakelt u het bijhouden van bestands inhoud in voor het opgegeven bestandspad.

   ![Linux-bestand toevoegen](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Bestands inhoud bijhouden

Met het bijhouden van bestands inhoud kunt u de inhoud van een bestand voor en na een bijgehouden wijziging bekijken. De functie slaat de inhoud van het bestand op in een opslag account nadat elke wijziging is doorgevoerd. Hier volgen enkele regels om de inhoud van het bestand bij te houden:

* Een standaard opslag account met het Resource Manager-implementatie model is vereist voor het opslaan van bestands inhoud. 

* Gebruik geen Premium-en klassieke implementatie model opslag accounts. Zie [over Azure Storage-accounts](../storage/common/storage-create-storage-account.md).

* Het opslag account dat u gebruikt, kan worden verbonden met slechts één Automation-account.

* [Wijzigingen bijhouden en inventarisatie](change-tracking.md) is ingeschakeld in uw Automation-account.

### <a name="enable-tracking-for-file-content-changes"></a>Bijhouden van wijzigingen in bestands inhoud inschakelen

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Wijzigingen bijhouden** onder **configuratie beheer**.
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer **Bestands inhoud** en klik op **koppelen**. Met deze selectie wordt het deel venster locatie van inhoud toevoegen voor Wijzigingen bijhouden geopend.

   ![Locatie van inhoud inschakelen](./media/change-tracking-file-contents/enable.png)

4. Selecteer het abonnement en het opslag account dat moet worden gebruikt voor het opslaan van de bestands inhoud. 

5. Als u het bijhouden van bestands inhoud voor alle bestaande bijgehouden bestanden wilt inschakelen, selecteert u **aan** voor **uploaden van bestands inhoud voor alle instellingen**. U kunt deze instelling voor elk bestandspad later wijzigen.

   ![Opslag account instellen](./media/change-tracking-file-contents/storage-account.png)

6. In Wijzigingen bijhouden en inventaris worden Uri's van het opslag account en de Shared Access Signature (SAS) weer gegeven wanneer het bijhouden van bestands inhoud wordt ingeschakeld. De hand tekeningen verlopen na 365 dagen en u kunt ze opnieuw maken door te klikken op **opnieuw genereren**.

   ![Lijst met account sleutels](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>De inhoud van een bijgehouden bestand weer geven

Zodra Wijzigingen bijhouden en de inventaris een wijziging voor een bijgehouden bestand detecteert, kunt u de inhoud van het bestand bekijken in het deel venster Details wijzigen.  

![Wijzigingen weer geven](./media/change-tracking-file-contents/change-list.png)

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Kies een bestand in de lijst met wijzigingen en selecteer **wijzigingen in bestands inhoud weer geven** om de inhoud van het bestand te bekijken. In het deel venster Details wijzigen ziet u de standaard voor-en na-bestands gegevens.

   ![Details wijzigen](./media/change-tracking-file-contents/change-details.png)

3. U bekijkt de bestands inhoud in een weer gave naast elkaar. U kunt **inline** selecteren om een inline weer gave van de wijzigingen weer te geven.

## <a name="track-registry-keys"></a>Register sleutels bijhouden

Gebruik de volgende stappen om het bijhouden van register sleutels op Windows-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. 
2. Klik op **Instellingen bewerken** (het tandwiel symbool).
3. Selecteer op de pagina werkruimte configuratie de optie **Windows-REGI ster**.
4. Klik op **+ toevoegen** om een nieuwe register sleutel aan de tracering toe te voegen.
5. Voer in het deel venster Windows-REGI ster toevoegen voor Wijzigingen bijhouden de gegevens in voor de sleutel die u wilt bijhouden en klik vervolgens op **Opslaan**. In de volgende tabel worden de eigenschappen gedefinieerd die u kunt gebruiken voor de informatie.

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Ingeschakeld     | Waar als een instelling wordt toegepast en anders false.        |
    |Itemnaam     | Beschrijvende naam van de register sleutel die moet worden gevolgd.        |
    |Groep     | Groeps naam voor het logisch groeperen van register sleutels.        |
    |Windows-registersleutel   | Sleutel naam met pad, bijvoorbeeld **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user shell Folders\Common gestart**.      |

## <a name="search-logs-for-change-records"></a>Zoek logboeken voor wijzigings records

U kunt verschillende zoek acties uitvoeren op de Azure Monitor logboeken voor wijzigings records. Open de pagina wijzigingen bijhouden en klik op **log Analytics** om de pagina logboeken te openen. De volgende tabel bevat voor beelden van zoek opdrachten in Logboeken voor wijzigings records.

|Query’s uitvoeren  |Beschrijving  |
|---------|---------|
|ConfigurationData<br>&#124; waarbij ConfigDataType = = "micro soft-Services" en SvcStartupType = = "auto"<br>&#124; waarbij SvcState = = "gestopt"<br>&#124; arg_max (TimeGenerated, *) samenvatten op software naam, computer         | Toont de meest recente inventaris records voor micro soft-services die zijn ingesteld op auto, maar die zijn gerapporteerd als gestopt. De resultaten zijn beperkt tot de meest recente record voor de opgegeven software naam en computer.    |
|ConfigurationChange<br>&#124; waarbij ConfigChangeType = = "software" en ChangeCategory = = "verwijderd"<br>&#124; order by TimeGenerated desc|Geeft wijzigings records weer voor verwijderde software.|

## <a name="create-alerts-on-changes"></a>Waarschuwingen maken voor wijzigingen

In het volgende voor beeld ziet u dat het bestand **C:\Windows\System32\drivers\etc\hosts** op een machine is gewijzigd. Dit bestand is belang rijk omdat Windows het gebruikt om hostnamen om te zetten in IP-adressen. Deze bewerking heeft voor rang op DNS en kan leiden tot verbindings problemen. Het kan ook leiden tot omleiding van verkeer naar kwaad aardige of anderszins gevaarlijke websites.

![Een grafiek met de wijziging van het bestand hosts](./media/change-tracking-file-contents/changes.png)

We gebruiken dit voor beeld om de stappen voor het maken van waarschuwingen voor een wijziging te bespreken.

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** onder **configuratie beheer**en selecteer vervolgens **log Analytics**. 
2. Zoek in de logboeken zoeken naar inhouds wijzigingen in het bestand **hosts** met de query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Met deze query wordt gezocht naar een wijziging in de inhoud voor bestanden met een volledig gekwalificeerd pad met het woord ' hosts '. U kunt ook een specifiek bestand vragen door het pad naar de volledig gekwalificeerde vorm te wijzigen, bijvoorbeeld met behulp `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`van.

3. Nadat de query de gewenste resultaten heeft geretourneerd, klikt u op **nieuwe waarschuwings regel** in de zoek opdracht voor Logboeken om de pagina voor het maken van een waarschuwing te openen. U kunt ook naar deze pagina navigeren via **Azure monitor** in de Azure Portal. 

4. Controleer de query opnieuw en wijzig de logica van de waarschuwing. In dit geval moet de waarschuwing worden geactiveerd als er zelfs één wijziging wordt gedetecteerd op alle computers in de omgeving.

    ![Wijziging in query voor het bijhouden van wijzigingen in het bestand hosts](./media/change-tracking-file-contents/change-query.png)

5. Nadat de waarschuwings logica is ingesteld, wijst u actie groepen toe om acties uit te voeren als reactie op de waarschuwing die wordt geactiveerd. In dit geval worden e-mail berichten ingesteld om te worden verzonden en wordt een ITSM-ticket (IT Service Management) gemaakt. 

    ![Actie groep configureren voor waarschuwing bij wijziging](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van wijzigingen bijhouden en inventaris](change-tracking.md)voor basis principes van wijzigingen bijhouden en inventarisatie.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van wijzigingen voor een virtuele machine in Azure.
* [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) gebruiken om gedetailleerde gegevens voor het bijhouden van wijzigingen weer te geven.