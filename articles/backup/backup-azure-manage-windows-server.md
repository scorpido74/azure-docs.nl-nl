---
title: Vaults en servers van Azure Recovery Services beheren
description: In dit artikel vindt u informatie over het dashboard van het kluisoverzicht van Herstelservices om uw vaults van Recovery Services te bewaken en te beheren.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 1a4d23c157700f42422cfe7ca8fa1c49e2cf128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131980"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Recovery Services-kluizen beheren en controleren

In dit artikel wordt uitgelegd hoe u het dashboard van het vault **overview-overzicht** van Herstelservices gebruiken om uw vaults van Recovery Services te bewaken en te beheren. Wanneer u een vault van Recovery Services opent vanuit de lijst, wordt het **dashboard Overzicht** voor de geselecteerde kluis geopend. Het dashboard bevat verschillende details over de kluis. Er zijn *tegels* die worden weergegeven: de status van kritieke en waarschuwingswaarschuwingen, voortgangs- en mislukte back-uptaken en de hoeveelheid lokaal redundante opslag (LRS) en geo redundante opslag (GRS) die worden gebruikt. Als u een back-up maakt van Azure VM's naar de kluis, worden in de tegel [ **Status back-up** vooraf controleren kritieke of waarschuwingsitems weergegeven.](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status) De volgende afbeelding is het **overzichtsdashboard** voor **Contoso-vault**. De tegel **Back-upitems** geeft aan dat er negen items zijn geregistreerd in de kluis.

![dashboard van herstelservices](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

De voorwaarden voor dit artikel zijn: een Azure-abonnement, een Vault voor Herstelservices en dat er ten minste één back-upitem is geconfigureerd voor de kluis.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Een kluis van Recovery Services openen

Als u waarschuwingen wilt controleren of beheergegevens over een kluis van Recovery Services wilt bekijken, opent u de kluis.

1. Gebruik uw Azure-abonnement om u aan te melden bij [Azure Portal](https://portal.azure.com/).

2. Klik in de portal op **Alle services**.

   ![Lijst met vaults van Recovery Services openen, stap 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Typ **Herstelservices**in het dialoogvenster **Alle services** . Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer de optie **Vaults voor Herstelservices** wordt weergegeven, klikt u erop om de lijst met vaults voor Herstelservices in uw abonnement te openen.

    ![Een Recovery Services-kluis maken, stap 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Klik in de lijst met kluizen op een kluis om het **dashboard Overzicht** te openen.

    ![dashboard van herstelservices](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Het dashboard Overzicht gebruikt tegels om waarschuwingen en back-uptaakgegevens te verstrekken.

## <a name="monitor-backup-jobs-and-alerts"></a>Back-uptaken en waarschuwingen controleren

Het dashboard Van het **kluisoverzicht** van Herstelservices bevat tegels voor controle- en gebruiksinformatie. De tegels in de sectie Controle geven kritieke en waarschuwingswaarschuwingen en lopende en mislukte taken weer. Klik op een bepaalde waarschuwing of taak om het menu Back-upwaarschuwingen of Back-uptaken te openen, gefilterd voor die taak of waarschuwing.

![Back-updashboardtaken](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

De sectie Monitoring toont de resultaten van vooraf gedefinieerde **back-upwaarschuwingen** en **back-uptakenquery's.** De controletegels geven actuele informatie over:

* Kritieke en waarschuwingswaarschuwingen voor back-uptaken (in de afgelopen 24 uur)
* Controleer de status vooraf voor Azure VM's. Zie [Status voor back-up vooraf controleren](#backup-pre-check-status)voor volledige informatie over de status van precheck.
* De back-uptaken in uitvoering en taken die zijn mislukt (in de afgelopen 24 uur).

De gebruikstegels bieden:

* Het aantal back-upitems dat is geconfigureerd voor de kluis.
* De Azure-opslag (gescheiden door LRS en GRS) die door de kluis wordt verbruikt.

Klik op de tegels (behalve back-upopslag) om het bijbehorende menu te openen. In de afbeelding hierboven ziet de tegel Back-upmeldingen drie kritieke waarschuwingen. Als u klikt op de rij Kritieke waarschuwingen in de tegel Back-upmeldingen, wordt de back-upwaarschuwingen geopend die zijn gefilterd op kritieke waarschuwingen.

![Menu Back-upwaarschuwingen gefilterd op kritieke waarschuwingen](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Het menu Back-upwaarschuwingen in de bovenstaande afbeelding wordt gefilterd door: Status is Actief, Ernst is kritiek en de tijd is de voorgaande 24 uur.

### <a name="backup-pre-check-status"></a>Status vooraf controleren van back-ups

Back-up pre-checks controleren de configuratie van uw VM's op problemen die een negatieve invloed kunnen hebben op back-ups. Ze verzamelen deze informatie zodat u deze rechtstreeks vanuit het Recovery Services Vault-dashboard bekijken en aanbevelingen doen voor corrigerende maatregelen om succesvolle bestandsconsistente of toepassingsconsistente back-ups te garanderen. Ze vereisen geen infrastructuur en hebben geen extra kosten.  

Back-uppre-checks worden uitgevoerd als onderdeel van de geplande back-upbewerkingen voor uw Azure VM's. Zij sluiten af met een van de volgende staten:

* **Geslaagd**: Deze status geeft aan dat de configuratie van uw VM moet leiden tot succesvolle back-ups en dat er geen corrigerende maatregelen hoeven te worden genomen.
* **Waarschuwing:** deze status geeft een of meer problemen in de configuratie van de VM aan die *kunnen* leiden tot back-upfouten. Het biedt *aanbevolen* stappen om succesvolle back-ups te garanderen. Als u bijvoorbeeld niet de nieuwste VM-agent hebt geïnstalleerd, kunnen back-ups met tussenpozen mislukken. Deze situatie zou een waarschuwingstoestand geven.
* **Kritiek:** deze status geeft een of meer kritieke problemen in de configuratie van de VM aan die *leiden* tot back-upfouten en biedt *vereiste* stappen om succesvolle back-ups te garanderen. Een netwerkprobleem dat wordt veroorzaakt door een update van de NSG-regels van een vm, zorgt er bijvoorbeeld voor dat back-ups mislukken, omdat de VM niet kan communiceren met de Azure Backup-service. Deze situatie zou een kritieke toestand opleveren.

Volg de onderstaande stappen om eventuele problemen op te lossen die zijn gemeld door Back-ups voor VM-back-ups in uw Vault voor herstelservices.

* Selecteer de tegel **Back-upvooraf controleren status (Azure VM's)** op het Dashboard Recovery Services Vault.
* Selecteer een vm met een back-uppre-checkstatus van **Kritiek** of **Waarschuwing**. Met deze actie wordt het deelvenster **VM-details** geopend.
* Selecteer de venstermelding boven aan het deelvenster om de beschrijving van het configuratieprobleem en de herstelstappen weer te geven.

## <a name="manage-backup-alerts"></a>Back-upwaarschuwingen beheren

Als u het menu Back-upwaarschuwingen wilt openen, klikt u in het kluismenu Herstelservices op **Back-upwaarschuwingen**.

![Waarschuwingen voor back-ups](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

In het rapport Back-upmeldingen worden de waarschuwingen voor de kluis weergegeven.

![Waarschuwingen voor back-ups](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Waarschuwingen

In de lijst Back-upwaarschuwingen worden de geselecteerde gegevens voor de gefilterde waarschuwingen weergegeven. In het menu Back-upwaarschuwingen u filteren op kritieke of waarschuwingswaarschuwingen.

| Waarschuwingsniveau | Gebeurtenissen die waarschuwingen genereren |
| ----------- | ----------- |
| Kritiek | U ontvangt kritieke waarschuwingen wanneer: Back-uptaken mislukken, hersteltaken mislukken en wanneer u de beveiliging op een server stopt, maar de gegevens behoudt.|
| Waarschuwing | U ontvangt waarschuwingen wanneer: Back-uptaken worden voltooid met waarschuwingen, bijvoorbeeld wanneer er geen back-up wordt gemaakt van minder dan 100 bestanden als gevolg van corruptieproblemen of wanneer er meer dan 1.000.000 bestanden met een back-up zijn gemaakt. |
| Informatief | momenteel zijn er geen informatieve waarschuwingen in gebruik. |

### <a name="viewing-alert-details"></a>Waarschuwingsdetails weergeven

Het rapport Back-upmeldingen houdt acht details over elke waarschuwing bij. Gebruik de knop **Kolommen kiezen** om de details in het rapport te bewerken.

![Waarschuwingen voor back-ups](./media/backup-azure-manage-windows-server/backup-alerts.png)

Standaard worden alle details, behalve **de laatste gebeurtenistijd,** in het rapport weergegeven.

* Waarschuwing
* Back-upitem
* Beveiligde server
* Severity
* Duur
* Creatietijd
* Status
* Laatste gebeurtenistijd

### <a name="change-the-details-in-alerts-report"></a>De details wijzigen in het waarschuwingsrapport

1. Als u de rapportgegevens wilt wijzigen, klikt u in het menu **Back-upwaarschuwingen** op **Kolommen kiezen**.

   ![Waarschuwingen voor back-ups](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Het menu **Kolommen kiezen** wordt geopend.

2. Kies **in** het menu Kolommen kiezen de details die u in het rapport wilt weergeven.

    ![Het menu Kolommen kiezen](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Klik **op Gereed** om de wijzigingen op te slaan en sluit het menu Kolommen kiezen.

   Als u wijzigingen aanbrengt, maar de wijzigingen niet wilt behouden, klikt u op **Opnieuw instellen** om de geselecteerde terug te sturen naar de laatst opgeslagen configuratie.

### <a name="change-the-filter-in-alerts-report"></a>Het filter wijzigen in het rapport waarschuwingen

Gebruik het menu **Filter** om de ernst, status, begintijd en eindtijd voor de waarschuwingen te wijzigen.

> [!NOTE]
> Als u het filter Back-upwaarschuwingen bewerkt, worden de kritieke of waarschuwingswaarschuwingen in het dashboard van het kluisoverzicht niet gewijzigd.
>  

1. Als u het filter Back-upmeldingen wilt wijzigen, klikt u in het menu Back-upwaarschuwingen op **Filter**.

   ![Filtermenu kiezen](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Het menu Filter wordt weergegeven.

   ![Filtermenu kiezen](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Bewerk de ernst, status, begintijd of eindtijd en klik op **Gereed** om de wijzigingen op te slaan.

## <a name="configuring-notifications-for-alerts"></a>Meldingen configureren voor waarschuwingen

Configureer meldingen om e-mails te genereren wanneer er een waarschuwing of kritieke waarschuwing optreedt. U elk uur e-mailwaarschuwingen verzenden of wanneer er een bepaalde waarschuwing plaatsvindt.

   ![Waarschuwingen filteren](./media/backup-azure-manage-windows-server/configure-notification.png)

Standaard zijn e-mailmeldingen **ingeschakeld**. Klik **op Uit** om de e-mailmeldingen te stoppen.

Kies in het **besturingselement Melden** de optie **Per waarschuwing** als u geen groepering wilt of niet veel items hebt die waarschuwingen kunnen genereren. Elke waarschuwing resulteert in één melding (de standaardinstelling) en een oplossingse-mail wordt onmiddellijk verzonden.

Als u **Hourly Digest**selecteert, wordt er een e-mail verzonden naar de ontvangers waarin de onopgeloste waarschuwingen worden uitgelegd die in het afgelopen uur zijn gegenereerd. Aan het einde van het uur wordt een e-mail met een resolutie verzonden.

Kies de waarschuwingsernst (Kritiek of Waarschuwing) die wordt gebruikt om e-mail te genereren. Momenteel zijn er geen informatiewaarschuwingen.

## <a name="manage-backup-items"></a>Back-upitems beheren

Een vault van Recovery Services bevat vele soorten back-upgegevens. [Meer informatie](backup-overview.md#what-can-i-back-up) over wat je maken. Als u de verschillende servers, computers, databases en workloads wilt beheren, klikt u op de tegel **Back-upitems** om de inhoud van de kluis weer te geven.

![Tegel Back-upitems](./media/backup-azure-manage-windows-server/backup-items.png)

De lijst met back-upitems, georganiseerd door Back-upbeheertype, wordt geopend.

![lijst met back-upitems](./media/backup-azure-manage-windows-server/list-backup-items.png)

Als u een specifiek type beveiligde instantie wilt verkennen, klikt u op het item in de kolom Back-upbeheertype. In de bovenstaande afbeelding zijn bijvoorbeeld twee Azure virtuele machines beschermd in deze kluis. Als u op **Azure Virtual Machine**klikt, opent u de lijst met beveiligde virtuele machines in deze kluis.

![lijst met back-uptype](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

De lijst met virtuele machines heeft nuttige gegevens: de bijbehorende resourcegroep, vorige [back-uppre-check,](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status)laatste back-upstatus en datum van het meest recente herstelpunt. De ellips, in de laatste kolom, opent het menu om veelvoorkomende taken te activeren. De nuttige gegevens in kolommen zijn verschillend voor elk back-uptype.

![lijst met back-uptype](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Back-uptaken beheren

De tegel **Back-uptaken** in het kluisdashboard toont het aantal taken dat in uitvoering is of mislukt in de afgelopen 24 uur. De tegel geeft een inkijkje in het menu Back-uptaken.

![Een back-up maken van items vanuit instellingen](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Als u meer informatie over de taken wilt zien, klikt u op **In uitvoering** of u het menu Back-uptaken **niet** openen dat voor die status is gefilterd.

### <a name="backup-jobs-menu"></a>Menu Back-uptaken

In het menu **Back-uptaken** worden informatie weergegeven over het artikeltype, de bewerking, de status, de begintijd en de duur.  

Als u het menu Back-uptaken wilt openen, klikt u in het hoofdmenu van de kluis op **Back-uptaken**.

![Een back-up maken van items vanuit instellingen](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

De lijst met back-uptaken wordt geopend.

![Een back-up maken van items vanuit instellingen](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

In het menu Back-uptaken wordt de status weergegeven voor alle bewerkingen, op alle back-uptypen, voor de afgelopen 24 uur. Gebruik **Filter** om de filters te wijzigen. De filters worden uitgelegd in de volgende secties.

Ga als u de filters wijzigen:

1. Klik in het menu Back-uptaken in de kluis op **Filter**.

   ![Een back-up maken van items vanuit instellingen](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Het menu Filter wordt geopend.

   ![Een back-up maken van items vanuit instellingen](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Kies de filterinstellingen en klik op **Gereed**. De gefilterde lijst wordt vernieuwd op basis van de nieuwe instellingen.

#### <a name="item-type"></a>Objecttype

Het itemtype is het type back-upbeheer van de beveiligde instantie. Er zijn vier soorten; zie de volgende lijst. U alle itemtypen of één itemtype weergeven. U geen twee of drie objecttypen selecteren. De beschikbare objecttypen zijn:

* Alle itemtypen
* Azure virtuele machine
* Bestanden en mappen
* Azure Storage
* Azure-werkbelasting

#### <a name="operation"></a>Bewerking

U één bewerking of alle bewerkingen bekijken. U geen twee of drie bewerkingen selecteren. De beschikbare bewerkingen zijn:

* Alle bewerkingen
* Registreren
* Back-up configureren
* Back-up
* Herstellen
* Back-up uitschakelen
* Back-upgegevens verwijderen

#### <a name="status"></a>Status

U alle status of een bekijken. U geen twee of drie statussen selecteren. De beschikbare statussen zijn:

* Alle status
* Voltooid
* Wordt uitgevoerd
* Mislukt
* Geannuleerd
* Aangevuld met waarschuwingen

#### <a name="start-time"></a>Begintijd

De dag en tijd waarop de query begint. De standaardinstelling is een periode van 24 uur.

#### <a name="end-time"></a>Eindtijd

De dag en het tijdstip waarop de query is beëindigd.

### <a name="export-jobs"></a>Exporttaken

Gebruik **Taken exporteren** om een spreadsheet te maken met alle menugegevens van Vacatures. De spreadsheet bevat één blad met een overzicht van alle taken en afzonderlijke bladen voor elke taak.

Als u de takengegevens wilt exporteren naar een spreadsheet, klikt u op **Taken exporteren**. De service maakt een spreadsheet met de naam van de kluis en datum, maar u de naam wijzigen.

## <a name="monitor-backup-usage"></a>Back-upgebruik controleren

De tegel Back-upopslag in het dashboard toont de opslag die in Azure is verbruikt. Opslaggebruik is voorzien in:

* Cloud LRS-opslaggebruik in verband met de kluis
* Cloud GRS-opslaggebruik in verband met de kluis

## <a name="troubleshooting-monitoring-issues"></a>Problemen met het oplossen van problemen met het oplossen van problemen

**Probleem:** Taken en/of waarschuwingen van de Azure Backup-agent worden niet weergegeven in de portal.

**Stappen voor het oplossen van problemen:** Het proces ```OBRecoveryServicesManagementAgent```verzendt de taak- en waarschuwingsgegevens naar de Azure Backup-service. Af en toe kan dit proces vast komen te zitten of af te sluiten.

1. Als u wilt controleren of het proces niet ```OBRecoveryServicesManagementAgent``` wordt uitgevoerd, opent u **Taakbeheer**en wordt de controle uitgevoerd.

2. Als het proces niet wordt uitgevoerd, opent u **het Configuratiescherm**en bladert u door de lijst met services. **Microsoft Azure Recovery Services Management Agent**starten of opnieuw starten .

    Voor meer informatie, blader door de logs op:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Bijvoorbeeld:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Volgende stappen

* [Windows Server of Windows-client herstellen vanuit Azure](backup-azure-restore-windows-server.md)
* Zie Overzicht van Azure [Backup](backup-introduction-to-azure-backup.md) voor meer informatie over Azure Backup
