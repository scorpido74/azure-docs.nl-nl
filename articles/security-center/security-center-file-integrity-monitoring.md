---
title: Controle van bestands integriteit in Azure Security Center | Microsoft Docs
description: Meer informatie over het configureren van FIM (File Integrity Monitoring) in Azure Security Center met behulp van deze procedure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: da0eeafc9d92bf08a2475d7676f1da8a4fa30da5
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461389"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Controle van bestands integriteit in Azure Security Center
Meer informatie over het configureren van FIM (File Integrity Monitoring) in Azure Security Center met behulp van deze procedure.


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|[Azure Defender voor servers](defender-for-servers-introduction.md) vereist|
|Vereiste rollen en machtigingen:|**Eigenaar van de werk ruimte** kan FIM inschakelen/uitschakelen (Zie [Azure-rollen voor log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)) voor meer informatie.<br>**Lezer** kan resultaten weer geven.|
|Clouds|![Ja ](./media/icons/yes-icon.png) commerciële Clouds<br>![Ja ](./media/icons/yes-icon.png) US gov<br>![Geen ](./media/icons/no-icon.png) China gov, andere gov<br>Wordt alleen ondersteund in regio's waar de oplossing voor het bijhouden van wijzigingen van Azure Automation beschikbaar is.<br>Zie [ondersteunde regio's voor gekoppelde log Analytics-werk ruimte](../automation/how-to/region-mappings.md).<br>[Meer informatie over het bijhouden van wijzigingen](../automation/change-tracking.md) |
|||





## <a name="what-is-fim-in-security-center"></a>Wat is FIM in Security Center?
Met de File Integrity Monitoring (FIM), ook wel bekend als wijzigings controle, worden bestanden en registers van het besturings systeem, toepassings software en anderen onderzocht op wijzigingen die mogelijk duiden op een aanval. Een vergelijkings methode wordt gebruikt om te bepalen of de huidige status van het bestand afwijkt van de laatste scan van het bestand. U kunt deze vergelijking gebruiken om te bepalen of er geldige of verdachte wijzigingen zijn aangebracht in uw bestanden.

De bestands integriteits controle van Security Center valideert de integriteit van Windows-bestanden, Windows-REGI ster en Linux-bestanden. U selecteert de bestanden die u wilt controleren door FIM in te scha kelen. Security Center bewaakt bestanden met FIM ingeschakeld voor activiteit, zoals:

- Bestanden en REGI ster maken en verwijderen
- Bestands wijzigingen (wijzigingen in de bestands grootte, Toegangs beheer lijsten en de hash van de inhoud)
- Wijzigingen in het REGI ster (wijzigingen in grootte, toegangscontrole lijsten, type en de inhoud)

Security Center raadt entiteiten aan om te bewaken, waarmee u eenvoudig FIM kunt inschakelen. U kunt ook uw eigen FIM-beleid of-entiteiten definiëren om te bewaken. In dit scenario ziet u hoe.

> [!NOTE]
> De FIM-functie (File Integrity Monitoring) werkt voor Windows-en Linux-computers en Vm's en is alleen beschikbaar wanneer **Azure Defender voor servers** is ingeschakeld. Bekijk de [prijzen](security-center-pricing.md) voor meer informatie. FIM uploadt gegevens naar de Log Analytics-werk ruimte. De gegevens kosten zijn van toepassing op basis van de hoeveelheid gegevens die u uploadt. Zie [log Analytics prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) voor meer informatie.

FIM maakt gebruik van de Azure Wijzigingen bijhouden-oplossing om wijzigingen in uw omgeving bij te houden en te identificeren. Als bestands integriteits controle is ingeschakeld, hebt u een **Wijzigingen bijhouden** bron van het type **oplossing**. Zie [Details van gegevensverzameling voor Wijzigingen bijhouden](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) voor informatie over de gegevensverzameling door Wijzigingen bijhouden van Azure.

> [!NOTE]
> Als u de **Wijzigingen bijhouden** resource verwijdert, schakelt u ook de functie bestands integriteit controleren uit in Security Center.

## <a name="which-files-should-i-monitor"></a>Welke bestanden moet ik controleren?
U moet nadenken over de bestanden die essentieel zijn voor uw systeem en toepassingen wanneer u kiest welke bestanden moeten worden bewaakt. Overweeg het kiezen van bestanden die u niet verwacht te wijzigen zonder te hoeven plannen. Het kiezen van bestanden die regel matig worden gewijzigd door toepassingen of besturings systeem (zoals logboek bestanden en tekst bestanden) maken veel ruis waardoor het moeilijk is om een aanval te identificeren.

Security Center biedt de volgende lijst met aanbevolen items die moeten worden bewaakt op basis van bekende aanvals patronen. Dit zijn onder andere bestanden en Windows-register sleutels. Alle sleutels bevinden zich onder HKEY_LOCAL_MACHINE (HKLM in de tabel).

|**Linux-bestanden**|**Windows-bestanden**|**Windows-register sleutels**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \Boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell mappen|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User shell-mappen|
|/ Boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \Boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell mappen|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User shell-mappen|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Controle van bestands integriteit inschakelen 

1. Selecteer in het gebied **geavanceerde beveiliging** van het **Azure Defender** -dash board de optie **Bestands integriteit controleren**.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="FIM starten" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    De pagina configuratie van **Bestands integriteit controleren** wordt geopend.

    De volgende informatie wordt gegeven voor elke werk ruimte:

    - Het totale aantal wijzigingen dat is opgetreden in de afgelopen week (mogelijk ziet u een streepje '-' als FIM niet is ingeschakeld op de werk ruimte)
    - Totaal aantal computers en Vm's die aan de werk ruimte rapporteren
    - Geografische locatie van de werk ruimte
    - Azure-abonnement waarvan de werk ruimte zich bevindt

1. Gebruik deze pagina voor het volgende:

    - De status en instellingen van elke werk ruimte openen en weer geven

    - ![Pictogram upgrade plan ][4] de werk ruimte voor het gebruik van Azure Defender bijwerken. Dit pictogram geeft aan dat de werk ruimte of het abonnement niet is beveiligd met Azure Defender. Uw abonnement moet worden beveiligd met Azure Defender om de FIM-functies te kunnen gebruiken. [Meer informatie](security-center-pricing.md).

    - ![Pictogram inschakelen][3] Schakel FIM in op alle computers onder de werk ruimte en configureer de FIM-opties. Dit pictogram geeft aan dat FIM niet is ingeschakeld voor de werk ruimte-gebruik deze knop om het volgende te doen:

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="FIM starten":::


    > [!TIP]
    > Als er geen knop inschakelen of bijwerken is en de ruimte leeg is, betekent dit dat FIM al is ingeschakeld voor de werk ruimte.


1. Selecteer **inschakelen**. De details van de werk ruimte, inclusief het aantal Windows-en Linux-computers onder de werk ruimte, worden weer gegeven.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM starten":::

   De aanbevolen instellingen voor Windows en Linux worden ook vermeld.  Vouw **Windows-bestanden**, het **REGI ster**en de **Linux-bestanden** uit om de volledige lijst met aanbevolen items weer te geven.

1. Schakel de selectie vakjes uit voor de aanbevolen entiteiten die u niet wilt bewaakt door FIM.

1. Selecteer **Bestands integriteit controleren Toep assen** om FIM in te scha kelen.

> [!NOTE]
> U kunt de instellingen op elk gewenst moment wijzigen. Zie de onderstaande [bewaakte entiteiten bewerken](#edit-monitored-entities) voor meer informatie.



## <a name="audit-monitored-workspaces"></a>Gecontroleerde werk ruimten controleren 

Het dash board **Bestands integriteits controle** wordt weer gegeven voor werk ruimten waarin FIM is ingeschakeld. Het FIM-dash board wordt geopend nadat u FIM hebt ingeschakeld in een werk ruimte of wanneer u een werk ruimte selecteert in het venster **Bestands integriteit controle** waarvoor FIM is ingeschakeld.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM starten":::

In het FIM-dash board voor een werk ruimte worden de volgende details weer gegeven:

- Totaal aantal machines dat is verbonden met de werk ruimte
- Totaal aantal wijzigingen dat is opgetreden tijdens de geselecteerde tijds periode
- Een uitsplitsing van het wijzigings type (bestanden, REGI ster)
- Een uitsplitsing van een wijzigings categorie (gewijzigd, toegevoegd, verwijderd)

Selecteer **filter** aan de bovenkant van het dash board om de tijds periode te wijzigen waarvoor wijzigingen worden weer gegeven.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="FIM starten":::

Op het tabblad **servers** worden de computers weer gegeven die aan deze werk ruimte rapporteren. Voor elke machine worden de volgende dashboard lijsten weer gegeven:

- Totaal aantal wijzigingen dat is opgetreden tijdens de geselecteerde periode
- Een uitsplitsing van de totale wijzigingen als bestands wijzigingen of register wijzigingen

**Zoeken in Logboeken** wordt geopend wanneer u een computer naam invoert in het zoek veld of een computer selecteert die wordt vermeld op het tabblad computers. in de zoek opdracht in Logboeken worden alle wijzigingen weer gegeven die zijn aangebracht tijdens de geselecteerde tijds periode voor de machine. U kunt een wijziging uitbreiden voor meer informatie.

![Zoeken in logboeken][8]

Op het tabblad **wijzigingen** (zie hieronder) worden alle wijzigingen voor de werk ruimte in de geselecteerde tijds periode weer gegeven. Voor elke entiteit die is gewijzigd, wordt in het dash board het volgende weer gegeven:

- Computer waarop de wijziging plaatsvond
- Type wijziging (REGI ster of bestand)
- Wijzigings categorie (gewijzigd, toegevoegd, verwijderd)
- Datum en tijd van wijziging

![Wijzigingen voor de werk ruimte][9]

**Wijzigings Details** worden geopend wanneer u een wijziging in het zoek veld invoert of een entiteit selecteert die wordt vermeld op het tabblad **wijzigingen** .

![Details wijzigen][10]

## <a name="edit-monitored-entities"></a>Bewaakte entiteiten bewerken

1. Ga terug naar het **dash board bestands integriteit controleren** en selecteer **instellingen**.

   ![Instellingen][11]

   **Werkruimte configuratie** wordt geopend met de weer gave van drie tabbladen: **Windows-REGI ster**, **Windows-bestanden**en Linux- **bestanden**. Elk tabblad bevat de entiteiten die u in die categorie kunt bewerken. Voor elke entiteit die wordt vermeld, geeft Security Center aan of FIM is ingeschakeld (true) of niet is ingeschakeld (false).  Als u de entiteit bewerkt, kunt u FIM in-of uitschakelen.

   ![Werkruimte configuratie][12]

2. Selecteer een identiteits beveiliging. In dit voor beeld hebben we een item geselecteerd onder het Windows-REGI ster. **Bewerken voor wijzigingen bijhouden** wordt geopend.

   ![Bewerken of wijzigingen bijhouden][13]

Onder **bewerken voor wijzigingen bijhouden** kunt u het volgende doen:

- Controle van bestands integriteit inschakelen (true) of uitschakelen (ONWAAR)
- De naam van de entiteit opgeven of wijzigen
- De waarde of het pad opgeven of wijzigen
- De entiteit verwijderen, de wijziging negeren of de wijziging opslaan

## <a name="add-a-new-entity-to-monitor"></a>Een nieuwe entiteit toevoegen om te bewaken
1. Ga terug naar het **dash board bestands integriteit controleren** en selecteer de **instellingen** bovenaan. **Werkruimte configuratie** wordt geopend.
2. Selecteer onder **werkruimte configuratie**het tabblad voor het type entiteit dat u wilt toevoegen: Windows-REGI ster, Windows-bestanden of Linux-bestanden. In dit voor beeld hebben we **Linux-bestanden**geselecteerd.

   ![Een nieuw item toevoegen om te bewaken][14]

3. Selecteer **Toevoegen**. **Toevoegen voor wijzigingen bijhouden** wordt geopend.

   ![Aangevraagde informatie invoeren][15]

4. Typ op de pagina **toevoegen** de gevraagde gegevens en selecteer **Opslaan**.

## <a name="disable-monitored-entities"></a>Bewaakte entiteiten uitschakelen
1. Ga terug naar het dash board **File Integrity Monitoring** .
2. Selecteer een werk ruimte waar FIM momenteel is ingeschakeld. Een werk ruimte is ingeschakeld voor FIM als de knop inschakelen of upgrade plan ontbreekt.

   ![Een werk ruimte selecteren waar FIM is ingeschakeld][16]

3. Selecteer **instellingen**onder Bestands integriteit controleren.

   ![Instellingen selecteren][17]

4. Selecteer onder **werkruimte configuratie**een groep waarvoor **ingeschakeld** is ingesteld op waar.

   ![Werkruimte configuratie][18]

5. Selecteer onder **bewerken voor wijzigingen bijhouden** venster **ingesteld op** onwaar.

   ![Ingeschakeld instellen op ONWAAR][19]

6. Selecteer **Opslaan**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Controle van mappen en paden met behulp van joker tekens

Joker tekens gebruiken om het bijhouden van meerdere mappen te vereenvoudigen. De volgende regels zijn van toepassing wanneer u de bewaking van mappen configureert met behulp van joker tekens:
-   Joker tekens zijn vereist voor het bijhouden van meerdere bestanden.
-   Joker tekens kunnen alleen worden gebruikt in het laatste segment van een pad, zoals C:\folder\file of/etc/*. conf
-   Als een omgevings variabele een pad bevat dat niet geldig is, wordt de validatie uitgevoerd, maar het pad mislukt wanneer de inventarisatie wordt uitgevoerd.
-   Bij het instellen van het pad vermijdt u algemene paden zoals c: \* . *, waardoor er te veel mappen worden gepasseerd.

## <a name="disable-fim"></a>FIM uitschakelen
U kunt FIM uitschakelen. FIM maakt gebruik van de Azure Wijzigingen bijhouden-oplossing om wijzigingen in uw omgeving bij te houden en te identificeren. Door FIM uit te scha kelen, verwijdert u de Wijzigingen bijhouden-oplossing uit de geselecteerde werk ruimte.

1. Als u FIM wilt uitschakelen, keert u terug naar het dash board **File Integrity Monitoring** .
2. Selecteer een werkruimte.
3. Selecteer onder **Bestands integriteit controleren**de optie **uitschakelen**.

   ![FIM uitschakelen][20]

4. Selecteer **verwijderen** om uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u de File Integrity Monitoring (FIM) in Security Center kunt gebruiken. Voor meer informatie over Security Center raadpleegt u de volgende pagina's:

* [Beveiligings beleid instellen](tutorial-security-policy.md) : informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen.
* [Aanbevelingen voor beveiliging beheren](security-center-recommendations.md) : meer informatie over hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
* [Azure Security Blog](https://docs.microsoft.com/archive/blogs/azuresecurity/) (Azure-beveiligingsblog): hier vindt u het laatste nieuws over Azure-beveiliging en andere informatie.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png