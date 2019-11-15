---
title: Azure Backup-Server gebruiken om een back-up te maken van een share point-Farm naar Azure
description: Gebruik Azure Backup Server om back-ups te maken van uw share point-gegevens en deze te herstellen. In dit artikel vindt u informatie over het configureren van uw share point-Farm zodat gewenste gegevens kunnen worden opgeslagen in Azure. U kunt beveiligde share point-gegevens herstellen vanaf schijf of Azure.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: a7a1b088868add308f128c5a51d8cce4339c637c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091728"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Een back-up maken van een share point-Farm naar Azure met MABS

U maakt een back-up van een share point-Farm naar Microsoft Azure met behulp van Microsoft Azure Backup-Server (MABS) op ongeveer dezelfde manier als u een back-up maakt van andere gegevens bronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u Bewaar beleidsopties voor verschillende back-uppunten. Het biedt ook de mogelijkheid om lokale schijf kopieën op te slaan voor snelle herstel tijd (RTO) en om kopieën op te slaan in azure voor een voordelige, lange termijn retentie.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Ondersteunde versies van share point en gerelateerde beveiligings scenario's

Azure Backup voor DPM ondersteunt de volgende scenario's:

| Workload | Versie | Share point-implementatie | Beveiliging en herstel |
| --- | --- | --- | --- |
| SharePoint |Share point 2016, share point 2013, share point 2010, share Point 2007, share Point 3,0 |Share point is geïmplementeerd als een fysieke server of Hyper-V/VMware-virtuele machine <br> -------------- <br> SQL AlwaysOn | Opties voor herstel van share point-Farm beveiligen: herstel Farm, Data Base, en bestand of lijst item van schijf herstel punten.  Farm-en database herstel van Azure-herstel punten. |

## <a name="before-you-start"></a>Voordat u begint

Er zijn enkele dingen die u moet bevestigen voordat u een back-up van een share point-farm maakt in Azure.

### <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat u de Azure Backup Server voor het beveiligen van workloads hebt [geïnstalleerd en voor bereid](backup-azure-microsoft-azure-backup.md) .

### <a name="protection-agent"></a>Beveiligings agent

De Azure Backup-agent moet zijn geïnstalleerd op de server waarop share point wordt uitgevoerd, op de servers met SQL Server en alle andere servers die deel uitmaken van de share point-farm. Zie [beveiligings agent](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx)instellen voor meer informatie over het instellen van de beveiligings agent.  De enige uitzonde ring hierop is dat u de agent alleen op één Webfront-end (WFE)-Server installeert. Azure Backup Server moet de agent op één WFE-server alleen gebruiken als het toegangs punt voor de beveiliging.

### <a name="sharepoint-farm"></a>SharePoint-farm

Voor elke 10.000.000-items in de farm moet er ten minste 2 GB aan ruimte zijn op het volume waar de MABS-map zich bevindt. Deze ruimte is vereist voor het genereren van catalogi. Voor MABS om specifieke items te herstellen (site verzamelingen, sites, lijsten, document bibliotheken, mappen, afzonderlijke documenten en lijst items), maakt catalogus genereren een lijst van de Url's die zijn opgenomen in elke inhouds database. U kunt de lijst met Url's bekijken in het deel venster herstelbaar item in het taak gebied **herstel** van de MABS Administrator-console.

### <a name="sql-server"></a>SQL Server

Azure Backup Server wordt uitgevoerd als een LocalSystem-account. Voor het maken van back-ups van SQL Server-data bases heeft MABS sysadmin-bevoegdheden voor dat account nodig voor de server waarop SQL Server wordt uitgevoerd. Stel NT AUTHORITY\SYSTEM in op *sysadmin* op de server waarop SQL Server wordt uitgevoerd voordat u een back-up maakt.

Als de share point-Farm SQL Server-data bases heeft die zijn geconfigureerd met SQL Server aliassen, installeert u de SQL Server-client onderdelen op de front-endwebserver die MABS zal beveiligen.

### <a name="sharepoint-server"></a>SharePoint Server

Hoewel de prestaties afhankelijk zijn van veel factoren, zoals de grootte van de share point-Farm, is het mogelijk dat een MABS een share point-Farm van 25 TB kan beveiligen.

### <a name="whats-not-supported"></a>Wat wordt er niet ondersteund

* MABS die een share point-Farm beveiligt, beveiligt geen zoek indexen of Application Service-data bases. U moet de beveiliging van deze data bases afzonderlijk configureren.
* MABS biedt geen back-up van share point-SQL Server data bases die worden gehost op scale-out Bestands server (SOFS)-shares.

## <a name="configure-sharepoint-protection"></a>Share point-beveiliging configureren

Voordat u MABS kunt gebruiken om share point te beveiligen, moet u de share point VSS Writer-Service (WSS Writer-Service) configureren met behulp van **ConfigureSharePoint. exe**.

U vindt **ConfigureSharePoint. exe** in de map [MABS installatiepad] \Bin op de front-endwebserver. Dit hulp programma biedt de beveiligings agent de referenties voor de share point-farm. U voert dit op één WFE-server uit. Als u meerdere WFE-servers hebt, selecteert u slechts één wanneer u een beveiligings groep configureert.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>De share point VSS Writer-service configureren

1. Ga op de WFE-server bij een opdracht prompt naar [MABS installatie locatie] \bin\
2. Voer ConfigureSharePoint-EnableSharePointProtection in.
3. Voer de referenties van de farm beheerder in. Dit account moet lid zijn van de lokale beheerders groep op de WFE-server. Als de farm beheerder geen lokale beheerder is, wijst u de volgende machtigingen toe op de WFE-server:
   * Ken de WSS_Admin_WPG groep volledig beheer toe aan de DPM-map (% Program Files%\Microsoft Azure Backup\DPM).
   * Verleen de WSS_Admin_WPG groep lees toegang tot de DPM-register sleutel (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> U moet ConfigureSharePoint. exe opnieuw uitvoeren wanneer er een wijziging is in de beheerders referenties van de share point-farm.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Een back-up maken van een share point-farm met behulp van MABS

Nadat u MABS en de share point-farm hebt geconfigureerd zoals eerder is uitgelegd, kan share point worden beveiligd door MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Een share point-Farm beveiligen

1. Klik op het tabblad **beveiliging** van de MABS Administrator-console op **Nieuw**.
    ![tabblad nieuwe beveiliging](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Selecteer op de pagina **type beveiligings groep selecteren** van de wizard **nieuwe beveiligings groep maken** de optie **servers**en klik vervolgens op **volgende**.

    ![Type beveiligings groep selecteren](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Schakel op het scherm **groeps leden selecteren** het selectie vakje in voor de share Point-server die u wilt beveiligen en klik op **volgende**.

    ![Groeps leden selecteren](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Als de beveiligings agent is geïnstalleerd, ziet u de server in de wizard. De structuur van MABS wordt ook weer gegeven. Omdat u ConfigureSharePoint. exe hebt uitgevoerd, communiceert MABS met de share point VSS Writer-service en de bijbehorende SQL Server data bases en herkent de share point-Farm structuur, de gekoppelde inhouds databases en eventuele bijbehorende items.
   >
   >
4. Voer op de pagina **methode voor gegevens beveiliging selecteren** de naam van de **beveiligings groep**in en selecteer de gewenste *beveiligings methoden*. Klik op **Volgende**.

    ![Methode voor gegevens beveiliging selecteren](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Met de methode voor schijf beveiliging kunt u aan korte herstel tijd doelen voldoen.
   >
   >
5. Selecteer op de pagina doelen voor de **korte termijn opgeven** de gewenste **Bewaar termijn** en bepaal wanneer u back-ups wilt maken.

    ![Doelen voor de korte termijn opgeven](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Omdat herstel het vaakst vereist is voor gegevens die minder dan vijf dagen oud zijn, hebben we een Bewaar termijn van vijf dagen op de schijf geselecteerd en wordt ervoor gezorgd dat de back-up plaatsvindt tijdens niet-productie-uren, voor dit voor beeld.
   >
   >
6. Controleer de schijf ruimte die is toegewezen aan de opslag groep voor de beveiligings groep en klik vervolgens op **volgende**.
7. Voor elke beveiligings groep wijst MABS schijf ruimte toe voor het opslaan en beheren van replica's. Op dit moment moet MABS een kopie maken van de geselecteerde gegevens. Selecteer hoe en wanneer u de replica wilt maken en klik vervolgens op **volgende**.

    ![Methode voor maken van replica selecteren](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Om ervoor te zorgen dat het netwerk verkeer niet wordt verzorgd, selecteert u een tijd buiten productie uren.
   >
   >
8. MABS zorgt voor gegevens integriteit door consistentie controles uit te voeren op de replica. Er zijn twee opties beschikbaar. U kunt een schema definiëren om consistentie controles uit te voeren, of DPM kan consistentie controles automatisch uitvoeren op de replica wanneer deze inconsistent wordt. Selecteer de gewenste optie en klik vervolgens op **volgende**.

    ![Consistentie controle](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Selecteer op de pagina **online beveiligings gegevens opgeven** de share point-farm die u wilt beveiligen en klik vervolgens op **volgende**.

    ![DPM share point-Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Selecteer uw voorkeurs schema op de pagina **online back-upschema opgeven** en klik vervolgens op **volgende**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS biedt een maximum van twee dagelijkse back-ups naar Azure vanaf het meest beschik bare meest recente back-uppunt van de schijf. Azure Backup kunt ook de hoeveelheid WAN-band breedte bepalen die kan worden gebruikt voor back-ups in piek-en piek uren met behulp van [Azure backup netwerk beperking](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Afhankelijk van het back-upschema dat u hebt geselecteerd, selecteert u op de pagina **online retentie beleid opgeven** het Bewaar beleid voor dagelijkse, wekelijkse, maandelijkse en jaarlijkse back-uppunten.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS maakt gebruik van een Bewaar schema van groot vader vader-zoon waarin een ander Bewaar beleid kan worden gekozen voor verschillende back-uppunten.
    >
    >
12. Net als bij schijf moet een eerste referentie punt replica worden gemaakt in Azure. Selecteer de gewenste optie voor het maken van een eerste back-upkopie naar Azure en klik op **volgende**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Controleer de geselecteerde instellingen op de pagina **samen vatting** en klik vervolgens op **groep maken**. Er wordt een bericht weer gegeven nadat de beveiligings groep is gemaakt.

    ![Samenvatting](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Een share point-item herstellen vanaf schijf met behulp van MABS

In het volgende voor beeld is het herstel van het *share point-item* per ongeluk verwijderd en moet het worden hersteld.
![MABS share point Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Open de **DPM Administrator-console**. Alle share point-Farms die door DPM worden beveiligd, worden weer gegeven op het tabblad **beveiliging** .

    ![MABS share point Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Selecteer het tabblad **herstel** om te beginnen met het herstellen van het item.

    ![MABS share point Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. U kunt in share Point zoeken naar het herstellen van een *share point-item* met behulp van een zoek opdracht op basis van joker tekens binnen een herstel punt bereik.

    ![MABS share point Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecteer het juiste herstel punt in de zoek resultaten, klik met de rechter muisknop op het item en selecteer vervolgens **herstellen**.
5. U kunt ook bladeren door verschillende herstel punten en een Data Base of een item selecteren dat u wilt herstellen. Selecteer **datum > herstel tijd**en selecteer vervolgens de juiste **Data Base > share point-Farm > herstel punt > item**.

    ![MABS share point Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
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
9. Geef een staging SQL Server locatie van het exemplaar op om de data base tijdelijk te herstellen en geef een staging-bestands share op MABS en de server waarop share point wordt uitgevoerd om het item te herstellen.

    ![Staging-Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS koppelt de inhouds database die als host fungeert voor het share point-item aan het tijdelijke SQL Server-exemplaar. Vanuit de inhouds database wordt het item hersteld en geplaatst op de locatie van het staging-bestand op MABS. Het herstelde item op de faserings locatie moet nu worden geëxporteerd naar de faserings locatie in de share point-farm.

    ![Staging-Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecteer **herstel opties opgeven**en pas beveiligings instellingen toe op de share point-Farm of pas de beveiligings instellingen van het herstel punt toe. Klik op **Volgende**.

    ![Herstel opties](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > U kunt ervoor kiezen het netwerk bandbreedte gebruik te beperken. Dit beperkt de gevolgen voor de productie server tijdens productie-uren.
    >
    >
11. Controleer de samenvattings informatie en klik vervolgens op **herstellen** om het bestand te herstellen.

    ![Samen vatting van herstel](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecteer nu het tabblad **controle** in de **MABS-beheer console** om de **status** van het herstel weer te geven.

    ![Herstel status](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Het bestand wordt nu teruggezet. U kunt de share point-site vernieuwen om het herstelde bestand te controleren.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Een share point-data base terugzetten vanuit Azure met behulp van DPM

1. Als u een share point-inhouds database wilt herstellen, bladert u door verschillende herstel punten (zoals eerder weer gegeven) en selecteert u het herstel punt dat u wilt herstellen.

    ![MABS share point Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbel klik op het share point-herstel punt om de beschik bare share point-catalogus gegevens weer te geven.

   > [!NOTE]
   > Omdat de share point-Farm is beveiligd voor lange termijn retentie in azure, is er geen catalogus informatie (meta gegevens) beschikbaar op MABS. Wanneer een tijdgebonden share point-inhouds database moet worden hersteld, moet u de share point-Farm opnieuw catalogiseren.
   >
   >
3. Klik op **opnieuw catalogiseren**.

    ![MABS share point Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het venster status van **Cloud opnieuw catalogiseren** wordt geopend.

    ![MABS share point Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *geslaagd*. Klik op **Sluiten**.

    ![MABS share point Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klik op het share point-object dat wordt weer gegeven op het tabblad MABS- **herstel** om de structuur van de inhouds database op te halen. Klik met de rechter muisknop op het item en klik vervolgens op **herstellen**.

    ![MABS share point Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg op dit punt de herstel stappen eerder in dit artikel om een share point-inhouds database van schijf te herstellen.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [back-up van Exchange Server](backup-azure-exchange-mabs.md) .
Zie het artikel [back up SQL Server](backup-azure-sql-mabs.md) .
