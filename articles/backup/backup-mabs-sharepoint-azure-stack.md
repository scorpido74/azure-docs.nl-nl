---
title: Een back-up maken van een SharePoint-farm op Azure Stack
description: Gebruik Azure Backup Server om een back-up te maken en uw SharePoint-gegevens in Azure Stack te herstellen. In dit artikel vindt u de informatie om uw SharePoint-farm zo te configureren dat gewenste gegevens kunnen worden opgeslagen in Azure. U beveiligde SharePoint-gegevens herstellen van schijf of azure.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d080605022cadf121fa6be99c9758fe9c0d878ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673053"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Een back-up maken van een SharePoint-farm op Azure Stack

U maakt een back-up van een SharePoint-farm op Azure Stack naar Microsoft Azure door Microsoft Azure Backup Server (MABS) te gebruiken op vrijwel dezelfde manier als een back-up van andere gegevensbronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u bewaarbeleidsopties voor verschillende back-uppunten. Het biedt ook de mogelijkheid om lokale schijfkopieën op te slaan voor snelle hersteltijddoelstellingen (RTO) en kopieën op te slaan naar Azure voor economische, langdurige retentie.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Door SharePoint ondersteunde versies en gerelateerde beveiligingsscenario's

Azure Backup for MABS ondersteunt de volgende scenario's:

| Workload | Versie | SharePoint-implementatie | Beveiliging en herstel |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint geïmplementeerd als een virtuele Azure Stack-machine <br> -------------- <br> SQL AlwaysOn | SharePoint Farm-herstelopties beveiligen: herstelfarm, database en bestand of lijstitem vanaf schijfherstelpunten.  Herstel van farm- en databasevan Azure-herstelpunten. |

## <a name="before-you-start"></a>Voordat u begint

Er zijn een paar dingen die u moet bevestigen voordat u een back-up maakt van een SharePoint-farm naar Azure.

### <a name="prerequisites"></a>Vereisten

Voordat u verdergaat, moet u ervoor zorgen dat u [de Azure Backup Server](backup-mabs-install-azure-stack.md) hebt geïnstalleerd en voorbereid om workloads te beschermen.

### <a name="protection-agent"></a>Beschermingsmiddel

De Azure Backup-agent moet zijn geïnstalleerd op de server waarop SharePoint wordt uitgevoerd, de servers waarop SQL Server wordt uitgevoerd en alle andere servers die deel uitmaken van de SharePoint-farm. Zie [Setup Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)voor meer informatie over het instellen van de beveiligingsagent.  De enige uitzondering is dat u de agent alleen op één WFE-server (Web Front End) installeert. Azure Backup Server heeft de agent op één WFE-server alleen nodig om te dienen als toegangspunt voor bescherming.

### <a name="sharepoint-farm"></a>SharePoint-farm

Voor elke 10 miljoen items in de boerderij moet er ten minste 2 GB ruimte zijn op het volume waar de MABS-map zich bevindt. Deze ruimte is vereist voor het genereren van de catalogus. Als MABS specifieke items (siteverzamelingen, sites, lijsten, documentbibliotheken, mappen, afzonderlijke documenten en lijstitems) wil herstellen, maakt het genereren van catalogi een lijst met URL's die zich in elke inhoudsdatabase bevinden. U de lijst met URL's weergeven in het herstelbare itemvenster in het taakgebied **Herstel** van MABS Administrator Console.

### <a name="sql-server"></a>SQL Server

Azure Backup Server wordt uitgevoerd als een LocalSystem-account. Als u een back-up van SQL Server-databases wilt maken, heeft MABS sysadmin-bevoegdheden voor dat account nodig voor de server waarop SQL Server wordt uitgevoerd. Stel NT AUTHORITY\SYSTEM in op *sysadmin* op de server waarop SQL Server wordt uitgevoerd voordat u een back-up maakt.

Als de SharePoint-farm SQL Server-databases heeft die zijn geconfigureerd met SQL Server-aliassen, installeert u de SQL Server-clientcomponenten op de front-end webserver die MABS beschermt.

### <a name="whats-not-supported"></a>Wat wordt er niet ondersteund

* MABS die een SharePoint-farm beschermt, beschermt zoekindexen of toepassingsservicedatabases niet. U moet de beveiliging van deze databases afzonderlijk configureren.
* MABS biedt geen back-up van SharePoint SQL Server-databases die worden gehost op sofs-shares (scale-out file server).

## <a name="configure-sharepoint-protection"></a>SharePoint-beveiliging configureren

Voordat u MABS gebruiken om SharePoint te beveiligen, moet u de SharePoint VSS Writer-service (WSS Writer-service) configureren met Behulp van **ConfigureSharePoint.exe**.

U **ConfigureSharePoint.exe** vinden in de map [MABS Installatiepad]\bin op de front-end webserver. Met deze tool beschikt u over de beveiligingsagent met de referenties voor de SharePoint-farm. U voert het uit op één WFE-server. Als u meerdere WFE-servers hebt, selecteert u er slechts één wanneer u een beveiligingsgroep configureert.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>De SharePoint VSS Writer-service configureren

1. Ga op de WFE-server bij een opdrachtprompt naar [MABS-installatielocatie]\bin\
2. Voer ConfigureSharePoint -EnableSharePointProtection in.
3. Geef de referenties van de farmbeheerder op. Deze account moet een lid zijn van een lokale beheerdersgroep op de WFE-server. Als de farmbeheerder geen lokale beheerder is, verleent u de volgende machtigingen op de WFE-server:
   * Geef de WSS_Admin_WPG groep volledige controle toe aan de DPM-map (%Program Files%\Microsoft Azure Backup\DPM).
   * Geef de WSS_Admin_WPG groep leestoegang tot de DPM-registersleutel (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> U moet ConfigureSharePoint.exe opnieuw uitvoeren wanneer er een wijziging is in de referenties van de SharePoint-farmbeheerder.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Een back-up maken van een SharePoint-farm met MABS

Nadat u MABS en de SharePoint-farm hebt geconfigureerd zoals eerder is uitgelegd, kan SharePoint worden beschermd door MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Een SharePoint-farm beveiligen

1. Klik op het tabblad **Beveiliging** van de MABS Administrator Console op **Nieuw**.
    ![Tabblad Nieuwe beveiliging](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Selecteer op de pagina **Type beveiligingsgroep selecteren** van de wizard **Nieuwe beveiligingsgroep maken** de optie **Servers**en klik op **Volgende**.

    ![Type Beveiligingsgroep selecteren](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Schakel in het scherm **Groepsleden selecteren** het selectievakje in voor de SharePoint-server die u wilt beveiligen en klik op **Volgende**.

    ![Groepsleden selecteren](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Als de beveiligingsagent is geïnstalleerd, u de server in de wizard zien. MABS toont ook zijn structuur. Omdat u ConfigureSharePoint.exe hebt uitgevoerd, communiceert MABS met de SharePoint VSS Writer-service en de bijbehorende SQL Server-databases en herkent u de SharePoint-farmstructuur, de bijbehorende inhoudsdatabases en eventuele bijbehorende items.
   >
   >
4. Voer op de pagina **Methode gegevensbescherming selecteren** de naam van de **beveiligingsgroep**in en selecteer de *gewenste beveiligingsmethoden*. Klik op **Volgende**.

    ![Methode voor gegevensbeveiliging selecteren](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > De schijfbeveiligingsmethode helpt om korte hersteltijddoelstellingen te bereiken.
   >
   >
5. Selecteer **op de** pagina Doelen voor korte termijn opgeven het gewenste **bewaarbereik**en bepaal wanneer u back-ups wilt maken.

    ![Kortetermijndoelen opgeven](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Omdat herstel meestal nodig is voor gegevens die minder dan vijf dagen oud zijn, hebben we een bewaarbereik van vijf dagen op schijf geselecteerd en ervoor gezorgd dat de back-up plaatsvindt tijdens niet-productie-uren, bijvoorbeeld.
   >
   >
6. Controleer de schijfruimte voor de opslaggroep die is toegewezen aan de beveiligingsgroep en klik vervolgens op **Volgende**.
7. Voor elke beveiligingsgroep wijst MABS schijfruimte toe om replica's op te slaan en te beheren. Op dit punt moet MABS een kopie van de geselecteerde gegevens maken. Selecteer hoe en wanneer u de replica wilt maken en klik op **Volgende**.

    ![Methode voor het maken van replica's kiezen](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Als u wilt dat het netwerkverkeer niet wordt uitgevoerd, selecteert u een tijd buiten productie-uren.
   >
   >
8. MABS zorgt voor de integriteit van gegevens door consistentiecontroles op de replica uit te voeren. Er zijn twee beschikbare opties. U een planning definiëren om consistentiecontroles uit te voeren of DPM kan consistentiecontroles automatisch uitvoeren op de replica wanneer deze inconsistent wordt uitgevoerd. Selecteer de voorkeursoptie en klik op **Volgende**.

    ![Consistentiecontrole](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Selecteer op de pagina **Gegevens voor onlinebeveiliging opgeven** de SharePoint-farm die u wilt beveiligen en klik op **Volgende**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Selecteer op de pagina **Online back-upplanning opgeven** de gewenste planning en klik op **Volgende**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS biedt maximaal twee dagelijkse back-ups naar Azure vanaf het toen beschikbare nieuwste schijfback-uppunt. Azure Backup kan ook de hoeveelheid WAN-bandbreedte beheren die kan worden gebruikt voor back-ups in piek- en daluren met behulp van [Azure Backup Network Throttling.](backup-windows-with-mars-agent.md#enable-network-throttling)
    >
    >
11. Afhankelijk van het back-upschema dat u hebt geselecteerd, selecteert u op de pagina **Onlineretentiebeleid opgeven** het bewaarbeleid voor dagelijkse, wekelijkse, maandelijkse en jaarlijkse back-uppunten.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS maakt gebruik van een grandfather-vader-zoon retentie regeling waarin een ander retentiebeleid kan worden gekozen voor verschillende back-up punten.
    >
    >
12. Net als bij schijf moet een eerste replica van het referentiepunt worden gemaakt in Azure. Selecteer de voorkeursoptie voor het maken van een eerste back-upnaar Azure en klik op **Volgende**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Controleer de geselecteerde instellingen op de **pagina Overzicht** en klik op **Groep maken**. U ziet een succesbericht nadat de beveiligingsgroep is gemaakt.

    ![Samenvatting](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Een SharePoint-item van schijf herstellen met MABS

In het volgende voorbeeld is het *SharePoint-item herstellen* per ongeluk verwijderd en moet het worden hersteld.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Open de **DPM Administrator Console**. Alle SharePoint-farms die door DPM worden beschermd, worden weergegeven op het tabblad **Beveiliging.**

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Als u wilt beginnen met het herstellen van het item, selecteert u het tabblad **Herstel.**

    ![MABS SharePoint-beveiliging5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. U zoeken in SharePoint-item voor *het herstellen van SharePoint-item* met behulp van een zoekopdracht op basis van een joker binnen een herstelpuntbereik.

    ![MABS SharePoint-beveiliging6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecteer het juiste herstelpunt in de zoekresultaten, klik met de rechtermuisknop op het item en selecteer **Herstellen**.
5. U ook bladeren door verschillende herstelpunten en selecteer een database of item te herstellen. Selecteer **Datum > hersteltijd**en selecteer vervolgens de juiste **database > SharePoint-farm > herstelpunt > Item**.

    ![MABS SharePoint-beveiliging7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klik met de rechtermuisknop op het item en selecteer **Herstellen** om de wizard Herstel te **openen.** Klik op **Volgende**.

    ![Herstelselectie controleren](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecteer het type herstel dat u wilt uitvoeren en klik op **Volgende**.

    ![Hersteltype](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > De selectie **van Herstellen naar origineel** in het voorbeeld herstelt het item naar de oorspronkelijke SharePoint-site.
   >
   >
8. Selecteer het **herstelproces** dat u wilt gebruiken.

   * Selecteer **Herstellen zonder een herstelfarm te gebruiken** als het SharePoint-farm niet is gewijzigd en hetzelfde is als het herstelpunt dat wordt hersteld.
   * Selecteer **Herstellen met een herstelfarm** als het SharePoint-farm is gewijzigd sinds het herstelpunt is gemaakt.

     ![Herstelproces](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Geef een sql server-instantielocatie met tijdelijke fasering op om de database tijdelijk te herstellen en geef een faseringsbestandsshare op MABS en de server waarop SharePoint wordt uitgevoerd om het item te herstellen.

    ![Faseringslocatie1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS koppelt de inhoudsdatabase die het SharePoint-item host, aan de tijdelijke SQL Server-instantie. Vanuit de inhoudsdatabase herstelt het item en plaatst het op de locatie van het faseringsbestand op MABS. Het herstelde item dat zich op de faseringslocatie bevindt, moet nu worden geëxporteerd naar de halteplaats in de SharePoint-farm.

    ![Faseringslocatie2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecteer **Herstelopties opgeven**en beveiligingsinstellingen toepassen op de SharePoint-farm of pas de beveiligingsinstellingen van het herstelpunt toe. Klik op **Volgende**.

    ![Herstelopties](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > U ervoor kiezen om het gebruik van de netwerkbandbreedte te beperken. Dit minimaliseert de impact op de productieserver tijdens productieuren.
    >
    >
11. Controleer de overzichtsgegevens en klik op **Herstellen** om te beginnen met het herstel van het bestand.

    ![Hersteloverzicht](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecteer nu het tabblad **Controle** in de **MABS Administrator Console** om de **status** van het herstel weer te geven.

    ![Herstelstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Het bestand is nu hersteld. U de SharePoint-site vernieuwen om het herstelde bestand te controleren.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een SharePoint-database herstellen vanuit Azure met Behulp van DPM

1. Als u een SharePoint-inhoudsdatabase wilt herstellen, bladert u door verschillende herstelpunten (zoals eerder is weergegeven) en selecteert u het herstelpunt dat u wilt herstellen.

    ![MABS SharePoint-beveiliging8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklik op het SharePoint-herstelpunt om de beschikbare SharePoint-catalogusgegevens weer te geven.

   > [!NOTE]
   > Omdat de SharePoint-farm is beveiligd voor langdurige bewaring in Azure, zijn er geen catalogusgegevens (metagegevens) beschikbaar op MABS. Als gevolg hiervan moet u de SharePoint-farm opnieuw catalogiseren wanneer een point-in-time SharePoint-inhoudsdatabase moet worden hersteld.
   >
   >
3. Klik **op Opnieuw catalogus**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het statusvenster **Voor de catalogus van de cloud** wordt geopend.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *Succes*. Klik op **Sluiten**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klik op het SharePoint-object dat wordt weergegeven op het tabblad **MABS-herstel** om de structuur van de inhoudsdatabase op te halen. Klik met de rechtermuisknop op het item en klik vervolgens op **Herstellen**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg op dit moment de herstelstappen eerder in dit artikel om een SharePoint-inhoudsdatabase van schijf te herstellen.

## <a name="faqs"></a>Veelgestelde vragen

V: Kan ik een SharePoint-item herstellen naar de oorspronkelijke locatie als SharePoint is geconfigureerd met SQL AlwaysOn (met beveiliging op schijf)?<br>
A: Ja, het item kan worden hersteld naar de oorspronkelijke SharePoint-site.

V: Kan ik een SharePoint-database herstellen naar de oorspronkelijke locatie als SharePoint is geconfigureerd met SQL AlwaysOn?<br>
A: Omdat SharePoint-databases zijn geconfigureerd in SQL AlwaysOn, kunnen ze alleen worden gewijzigd als de beschikbaarheidsgroep is verwijderd. Als gevolg hiervan kan MABS een database niet herstellen naar de oorspronkelijke locatie. U een SQL Server-database herstellen naar een andere SQL Server-instantie.

## <a name="next-steps"></a>Volgende stappen

Zie het [artikel Back-upbestanden en toepassingen.](backup-mabs-files-applications-azure-stack.md)
Zie het artikel [Backup SQL Server on Azure Stack.](backup-mabs-sql-azure-stack.md)
