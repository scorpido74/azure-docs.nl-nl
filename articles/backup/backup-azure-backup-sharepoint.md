---
title: Een back-up maken van een share point-Farm naar Azure met DPM
description: Dit artikel bevat een overzicht van DPM/Azure Backup Server-beveiliging van een share point-Farm naar Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 6640690f725c84899babef6825f817bad447b40f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673271"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Een back-up maken van een share point-Farm naar Azure met DPM

U maakt een back-up van een share point-Farm naar Microsoft Azure met behulp van System Center Data Protection Manager (DPM) op ongeveer dezelfde manier als u een back-up maakt van andere gegevens bronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u Bewaar beleidsopties voor verschillende back-uppunten. DPM biedt de mogelijkheid om lokale schijf kopieën op te slaan voor snelle herstel tijd (RTO) en om kopieën op te slaan in azure voor een voordelige, lange termijn retentie.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Ondersteunde versies van share point en gerelateerde beveiligings scenario's

Azure Backup voor DPM ondersteunt de volgende scenario's:

| Workload | Version | Share point-implementatie | Type DPM-implementatie | DPM-System Center 2012 R2 | Beveiliging en herstel |
| --- | --- | --- | --- | --- | --- |
| SharePoint |Share point 2013, share point 2010, share Point 2007, share Point 3,0 |Share point is geïmplementeerd als een fysieke server of Hyper-V/VMware-virtuele machine <br> -------------- <br> SQL AlwaysOn |Fysieke server of een on-premises Hyper-V-virtuele machine |Ondersteunt back-ups naar Azure via update pakket 5 |Opties voor herstel van share point-Farm beveiligen: herstel Farm, Data Base, en bestand of lijst item van schijf herstel punten.  Farm-en database herstel van Azure-herstel punten. |

## <a name="before-you-start"></a>Voordat u begint

Er zijn enkele dingen die u moet bevestigen voordat u een back-up van een share point-farm maakt in Azure.

### <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat u voldoet aan alle [vereisten voor het gebruik van Microsoft Azure backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het beveiligen van werk belastingen. Enkele taken voor vereisten zijn: Maak een back-upkluis, down load kluis referenties, installeer Azure Backup Agent en registreer DPM/Azure Backup Server met de kluis.

### <a name="dpm-agent"></a>DPM-agent

De DPM-agent moet zijn geïnstalleerd op de server waarop share point wordt uitgevoerd, op de servers met SQL Server en alle andere servers die deel uitmaken van de share point-farm. Zie [beveiligings agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)instellen voor meer informatie over het instellen van de beveiligings agent.  De enige uitzonde ring hierop is dat u de agent alleen op één Webfront-end (WFE)-Server installeert. DPM moet de agent op één WFE-server alleen gebruiken als het toegangs punt voor de beveiliging.

### <a name="sharepoint-farm"></a>SharePoint-farm

Voor elke 10 miljoen items in de farm moet er minimaal 2 GB vrije schijfruimte beschikbaar zijn op het volume waarin de DPM-map zich bevindt. Deze ruimte is vereist voor het genereren van de catalogus. Voor DPM om specifieke items te herstellen (site verzamelingen, sites, lijsten, document bibliotheken, mappen, afzonderlijke documenten en lijst items), maakt catalogus genereren een lijst van de Url's die zijn opgenomen in elke inhouds database. U kunt de lijst met Url's bekijken in het deel venster herstelbaar item in het taak gebied **herstel** van DPM Administrator-console.

### <a name="sql-server"></a>SQL Server

DPM wordt uitgevoerd als een LocalSystem-account. Voor het maken van back-ups van SQL Server-data bases heeft DPM sysadmin-bevoegdheden nodig voor dat account voor de server waarop SQL Server wordt uitgevoerd. Stel NT AUTHORITY\SYSTEM in op *sysadmin* op de server waarop SQL Server wordt uitgevoerd voordat u een back-up maakt.

Als de share point-Farm SQL Server-data bases heeft die zijn geconfigureerd met SQL Server aliassen, installeert u de SQL Server-client onderdelen op de front-endwebserver die door DPM wordt beveiligd.

### <a name="sharepoint-server"></a>SharePoint Server

Hoewel de prestaties afhankelijk zijn van veel factoren, zoals de grootte van de share point-Farm, is het mogelijk dat een DPM-server een share point-Farm van 25 TB kan beveiligen.

### <a name="dpm-update-rollup-5"></a>Update pakket 5 voor DPM

Als u de beveiliging van een share point-farm wilt starten naar Azure, moet u DPM Update Rollup 5 of hoger installeren. Update pakket 5 biedt de mogelijkheid om een share point-Farm te beveiligen naar Azure als de farm is geconfigureerd met behulp van SQL AlwaysOn.
Zie voor meer informatie het blog bericht dat [DPM Update Rollup 5](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx) introduceert

### <a name="whats-not-supported"></a>Wat wordt er niet ondersteund

* DPM die een share point-Farm beveiligt, beveiligt geen zoek indexen of Application Service-data bases. U moet de beveiliging van deze data bases afzonderlijk configureren.
* DPM biedt geen back-up van share point-SQL Server data bases die worden gehost op scale-out Bestands server-shares (SOFS).

## <a name="configure-sharepoint-protection"></a>SharePoint-beveiliging configureren

Voordat u DPM kunt gebruiken om share point te beveiligen, moet u de share point VSS Writer-Service (WSS Writer Service) configureren met behulp van **ConfigureSharePoint. exe**.

U kunt **ConfigureSharePoint. exe** vinden in de map [DPM-installatiepad] \Bin op de front-endwebserver. Dit hulp programma biedt de beveiligings agent de referenties voor de share point-farm. U voert de agent uit op één WFE-server. Als u meerdere WFE-servers hebt, selecteert u slechts één wanneer u een beveiligings groep configureert.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>De share point VSS Writer-service configureren

1. Ga op de WFE-server, vanaf een opdracht prompt, naar [DPM-installatie locatie] \bin\
2. Voer ConfigureSharePoint-EnableSharePointProtection in.
3. Geef de referenties van de farmbeheerder op. Deze account moet een lid zijn van een lokale beheerdersgroep op de WFE-server. Als de farmbeheerder geen lokale beheerder is, wijst u de volgende machtigingen toe op de WFE-server:
   * Ken de WSS_Admin_WPG groep volledig beheer toe aan de DPM-map (% Program Files%\Microsoft Data Protection Manager\DPM).
   * Verleen de WSS_Admin_WPG groep lees toegang tot de DPM-register sleutel (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> U moet ConfigureSharePoint. exe opnieuw uitvoeren wanneer er een wijziging is in de beheerders referenties van de share point-farm.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Een back-up maken van een share point-farm met behulp van DPM

Nadat u DPM en de share point-farm hebt geconfigureerd zoals eerder is uitgelegd, kan share point worden beveiligd door DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Een share point-Farm beveiligen

1. Klik op het tabblad **beveiliging** van de DPM Administrator-console op **Nieuw**.
    ![tabblad nieuwe beveiliging](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Selecteer op de pagina **type beveiligings groep selecteren** van de wizard **nieuwe beveiligings groep maken** de optie **servers**en klik vervolgens op **volgende**.

    ![Type beveiligings groep selecteren](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Schakel op het scherm **groeps leden selecteren** het selectie vakje in voor de share Point-server die u wilt beveiligen en klik op **volgende**.

    ![Groeps leden selecteren](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Als de DPM-agent is geïnstalleerd, ziet u de server in de wizard. DPM toont ook de structuur ervan. Omdat u ConfigureSharePoint. exe hebt uitgevoerd, communiceert DPM met de share point VSS Writer-service en de bijbehorende SQL Server data bases en herkent de share point-Farm structuur, de gekoppelde inhouds databases en eventuele bijbehorende items.
   >
   >
4. Voer op de pagina **methode voor gegevens beveiliging selecteren** de naam van de **beveiligings groep**in en selecteer de gewenste *beveiligings methoden*. Klik op **Volgende**.

    ![Methode voor gegevens beveiliging selecteren](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Met de methode voor schijf beveiliging kunt u aan korte herstel tijd doelen voldoen. Azure is een betaal bare, lange termijn beveiliging doel vergeleken met tapes. Zie [Azure Backup gebruiken om uw tape infrastructuur te vervangen](./backup-azure-backup-cloud-as-tape.md) voor meer informatie
   >
   >
5. Selecteer op de pagina doelen voor de **korte termijn opgeven** de gewenste **Bewaar termijn** en bepaal wanneer u back-ups wilt maken.

    ![Korte-termijndoelstellingen opgeven](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Omdat herstel het vaakst vereist is voor gegevens die minder dan vijf dagen oud zijn, hebben we een Bewaar termijn van vijf dagen op de schijf geselecteerd en wordt ervoor gezorgd dat de back-up plaatsvindt tijdens niet-productie-uren, voor dit voor beeld.
   >
   >
6. Controleer de schijf ruimte die is toegewezen aan de opslag groep voor de beveiligings groep en klik vervolgens op **volgende**.
7. Voor elke beveiligings groep wijst DPM schijf ruimte toe voor het opslaan en beheren van replica's. Op dit punt moet DPM een kopie maken van de geselecteerde gegevens. Selecteer hoe en wanneer u de replica wilt maken en klik vervolgens op **volgende**.

    ![Methode voor maken van replica selecteren](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Om ervoor te zorgen dat het netwerk verkeer niet wordt verzorgd, selecteert u een tijd buiten productie uren.
   >
   >
8. DPM zorgt voor gegevens integriteit door consistentie controles uit te voeren op de replica. Er zijn twee opties beschikbaar. U kunt een schema definiëren om consistentie controles uit te voeren, of DPM kan consistentie controles automatisch uitvoeren op de replica wanneer deze inconsistent wordt. Selecteer de gewenste optie en klik vervolgens op **volgende**.

    ![Consistentie controle](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Selecteer op de pagina **online beveiligings gegevens opgeven** de share point-farm die u wilt beveiligen en klik vervolgens op **volgende**.

    ![DPM share point-Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Selecteer uw voorkeurs schema op de pagina **online back-upschema opgeven** en klik vervolgens op **volgende**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > DPM biedt op verschillende momenten Maxi maal twee dagelijkse back-ups naar Azure. Azure Backup kunt ook de hoeveelheid WAN-band breedte bepalen die kan worden gebruikt voor back-ups in piek-en piek uren met behulp van [Azure backup netwerk beperking](backup-windows-with-mars-agent.md#enable-network-throttling).
    >
    >
11. Afhankelijk van het back-upschema dat u hebt geselecteerd, selecteert u op de pagina **online retentie beleid opgeven** het Bewaar beleid voor dagelijkse, wekelijkse, maandelijkse en jaarlijkse back-uppunten.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > DPM maakt gebruik van een Bewaar schema voor groot vader-vader-zoon waarin een ander Bewaar beleid kan worden gekozen voor verschillende back-uppunten.
    >
    >
12. Net als bij schijf moet een eerste referentie punt replica worden gemaakt in Azure. Selecteer de gewenste optie voor het maken van een eerste back-upkopie naar Azure en klik op **volgende**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Controleer de geselecteerde instellingen op de pagina **samen vatting** en klik vervolgens op **groep maken**. Er wordt een bericht weer gegeven nadat de beveiligings groep is gemaakt.

    ![Samenvatting](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Een share point-item herstellen vanaf schijf met behulp van DPM

In het volgende voor beeld is het herstel van het *share point-item* per ongeluk verwijderd en moet het worden hersteld.
![DPM share point Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Open de **DPM Administrator-console**. Alle share point-Farms die door DPM worden beveiligd, worden weer gegeven op het tabblad **beveiliging** .

    ![DPM share point-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Selecteer het tabblad **herstel** om te beginnen met het herstellen van het item.

    ![DPM share point-Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. U kunt in share Point zoeken naar het herstellen van een *share point-item* met behulp van een zoek opdracht op basis van joker tekens binnen een herstel punt bereik.

    ![DPM share point-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecteer het juiste herstel punt in de zoek resultaten, klik met de rechter muisknop op het item en selecteer vervolgens **herstellen**.
5. U kunt ook bladeren door verschillende herstel punten en een Data Base of een item selecteren dat u wilt herstellen. Selecteer **datum > herstel tijd**en selecteer vervolgens de juiste **Data Base > share point-Farm > herstel punt > item**.

    ![DPM share point-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klik met de rechter muisknop op het item en selecteer vervolgens **herstellen** om de **wizard herstellen**te openen. Klik op **Volgende**.

    ![Selectie voor herstel controleren](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecteer het type herstel dat u wilt uitvoeren en klik vervolgens op **volgende**.

    ![Herstel type](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > De selectie van **herstellen naar origineel** in het voor beeld herstelt het item naar de oorspronkelijke share point-site.
   >
   >
8. Selecteer het **herstel proces** dat u wilt gebruiken.

   * Selecteer **herstellen zonder een herstel Farm te gebruiken** als de share point-Farm niet is gewijzigd en gelijk is aan het herstel punt dat wordt hersteld.
   * Selecteer **herstellen met een herstel Farm** als de share point-Farm is gewijzigd sinds het herstel punt is gemaakt.

     ![Herstel proces](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Geef een staging SQL Server locatie van het exemplaar op om de data base tijdelijk te herstellen en geef een staging-bestands share op de DPM-server en de server waarop share point wordt uitgevoerd om het item te herstellen.

    ![Staging-Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM koppelt de inhouds database die als host fungeert voor het share point-item aan het tijdelijke SQL Server-exemplaar. De DPM-server herstelt vanuit de inhouds database het item en plaatst het op de locatie van het staging-bestand op de DPM-server. Het herstelde item op de faserings locatie van de DPM-server moet nu worden geëxporteerd naar de faserings locatie in de share point-farm.

    ![Staging-Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecteer **herstel opties opgeven**en pas beveiligings instellingen toe op de share point-Farm of pas de beveiligings instellingen van het herstel punt toe. Klik op **Volgende**.

    ![Herstel opties](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > U kunt ervoor kiezen het netwerk bandbreedte gebruik te beperken. Dit beperkt de gevolgen voor de productie server tijdens productie-uren.
    >
    >
11. Controleer de samenvattings informatie en klik vervolgens op **herstellen** om het bestand te herstellen.

    ![Samen vatting van herstel](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecteer nu het tabblad **bewaking** in het **DPM Administrator-console** om de **status** van het herstel weer te geven.

    ![Herstel status](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Het bestand wordt nu teruggezet. U kunt de share point-site vernieuwen om het herstelde bestand te controleren.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een share point-data base terugzetten vanuit Azure met behulp van DPM

1. Als u een share point-inhouds database wilt herstellen, bladert u door verschillende herstel punten (zoals eerder weer gegeven) en selecteert u het herstel punt dat u wilt herstellen.

    ![DPM share point-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbel klik op het share point-herstel punt om de beschik bare share point-catalogus gegevens weer te geven.

   > [!NOTE]
   > Omdat de share point-Farm is beveiligd voor lange termijn retentie in azure, is er geen catalogus informatie (meta gegevens) beschikbaar op de DPM-server. Wanneer een tijdgebonden share point-inhouds database moet worden hersteld, moet u de share point-Farm opnieuw catalogiseren.
   >
   >
3. Klik op **opnieuw catalogiseren**.

    ![DPM share point-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het venster status van **Cloud opnieuw catalogiseren** wordt geopend.

    ![DPM share point-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *geslaagd*. Klik op **Sluiten**.

    ![DPM share point-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klik op het share point-object dat wordt weer gegeven op het tabblad DPM **Recovery** om de structuur van de inhouds database op te halen. Klik met de rechter muisknop op het item en klik vervolgens op **herstellen**.

    ![DPM share point-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg op dit punt de herstel stappen eerder in dit artikel om een share point-inhouds database van schijf te herstellen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over DPM-beveiliging van share point-Zie [video serie-DPM-beveiliging van share point](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Raadpleeg de [release opmerkingen voor System Center 2012-Data Protection Manager](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj860415(v=sc.12)?redirectedfrom=MSDN)
* Raadpleeg de [release opmerkingen voor Data Protection Manager in System Center 2012 SP1](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj860394(v=sc.12)?redirectedfrom=MSDN)
