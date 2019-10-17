---
title: Een back-up van SQL Server-databases maken in Azure
description: In deze zelfstudie wordt uitgelegd hoe u een back-up van SQL Server maakt in Azure. In het artikel wordt ook uitgelegd hoe u SQL Server kunt herstellen.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 202d608e5d994cabd3d7e2e9a0887c8aab75af31
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437832"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Over SQL Server-back-ups in virtuele Azure-machines

SQL Server-databases zijn essentiële workloads waarvoor een laag Recovery Point Objective (RPO, beoogd herstelpunt) en langetermijnretentie nodig zijn. U kunt back-ups maken van SQL Server-data bases die worden uitgevoerd op virtuele Azure-machines met [Azure backup](backup-overview.md).

## <a name="backup-process"></a>Back-upproces

Deze oplossing maakt gebruik van de SQL Native Api's om back-ups te maken van uw SQL-data bases.

* Wanneer u de SQL Server virtuele machine hebt opgegeven die u wilt beveiligen en query's wilt uitvoeren voor de data bases erin, Azure Backup service een back-upextensie voor werk belasting op de virtuele machine installeert met de extensie `AzureBackupWindowsWorkload`.
* Deze uitbrei ding bestaat uit een coördinator en een SQL-invoeg toepassing. Hoewel de coördinator verantwoordelijk is voor het activeren van werk stromen voor verschillende bewerkingen, zoals het configureren van back-ups, back-ups maken en herstellen, is de invoeg toepassing verantwoordelijk voor de werkelijke gegevens stroom.
* Om data bases op deze VM te kunnen detecteren, maakt Azure Backup het account `NT SERVICE\AzureWLBackupPluginSvc`. Dit account wordt gebruikt voor back-up en herstel en vereist SQL sysadmin-machtigingen. Het `NT SERVICE\AzureWLBackupPluginSvc`-account is een [virtueel service account](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)en vereist daarom geen wachtwoord beheer. Azure Backup maakt gebruik van het `NT AUTHORITY\SYSTEM`-account voor database detectie/-informatie, zodat dit account een open bare aanmelding voor SQL moet zijn. Als u de SQL Server-VM niet hebt gemaakt vanuit de Azure Marketplace, ontvangt u mogelijk het foutbericht **UserErrorSQLNoSysadminMembership**. Volg in dit geval [deze instructies](#set-vm-permissions).
* Zodra u de beveiliging configureren voor de geselecteerde data bases hebt geactiveerd, stelt de back-upservice de coördinator in met de back-upschemaën en andere beleids Details, die de uitbrei ding lokaal op de virtuele machine opslaat.
* Op het geplande tijdstip communiceert de coördinator met de invoeg toepassing en begint deze met het streamen van de back-upgegevens van de SQL Server met VDI.  
* De invoeg toepassing verzendt de gegevens rechtstreeks naar de Recovery Services-kluis, waardoor er geen staging-locatie nodig is. De gegevens worden versleuteld en opgeslagen door de Azure Backup-service in opslag accounts.
* Wanneer de gegevens overdracht is voltooid, bevestigt de coördinator de door Voer met de back-upservice.

  ![SQL-back-uparchitectuur](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Voordat u begint

Voordat u begint, controleert u het onderstaande:

1. Zorg ervoor dat er een SQL Server-exemplaar wordt uitgevoerd in Azure. U kunt [snel een SQL Server-exemplaar maken](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) in de marketplace.
2. Bekijk de ondersteuning voor de [functie](#feature-consideration-and-limitations) en het [scenario](#scenario-support).
3. [Lees de veelgestelde vragen](faq-backup-sql-server.md) over dit scenario.

## <a name="scenario-support"></a>Scenario-ondersteuning

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde implementaties** | SQL Marketplace Azure-VM's en niet-Marketplace-VM's (SQL Server handmatig geïnstalleerd) worden ondersteund.
**Ondersteunde geografische gebieden** | Australië-Zuid-Oost (ASE), Oost-Australië (AE), Australië-centraal (AC), Australië-centraal 2 (AC) <br> Brazilië - zuid (BRS)<br> Canada-centraal (CNC), Canada-oost (CE)<br> Zuid-Azië-oost (zee), Azië-oost (EA) <br> VS-Oost (EUS), VS-Oost 2 (EUS2), VS-West-Centraal (WCUS), VS-West (WUS); VS-West 2 (WUS 2) Noord-Centraal VS (NCUS) centraal VS (CUS) Zuid-Centraal (SCUS) <br> India-centraal (INC), India-Zuid (INS), India-West <br> Japan-Oost (JPE), Japan-West (JPW) <br> Korea-centraal (KRC), Korea-zuid (KRS) <br> Europa-noord (NE), Europa-west <br> UK-zuid (UKS), UK-west (UKW) <br> US Gov-Arizona, US Gov-Virginia, US Gov-Texas, US DoD-centraal, US DoD-oost <br> Duitsland-noord, Duitsland-west-centraal <br> Zwitserland-noord, Zwitserland-west
**Ondersteunde besturingssystemen** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux wordt momenteel niet ondersteund.
**Ondersteunde SQL Server-versies** | SQL Server 2017, zoals [hier](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)wordt beschreven, SQL Server 2016 en SPS, zoals [hier](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)wordt beschreven, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Ondersteunde .NET-versies** | .NET Framework 4.5.2 en hoger geïnstalleerd op de VM

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Ondersteuning voor SQL Server 2008 en SQL Server 2008 R2

Azure Backup heeft onlangs ondersteuning aangekondigd voor [EOS SQL-servers](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) -SQL Server 2008 en SQL Server 2008 R2. De oplossing is momenteel als preview-versie beschikbaar voor EOS SQL Server en ondersteunt de volgende configuratie:

1. SQL Server 2008 en SQL Server 2008 R2 uitgevoerd op Windows 2008 R2 SP1
2. .NET Framework 4.5.2 en hierboven moeten worden geïnstalleerd op de VM
3. Back-ups voor FCI en gespiegelde data bases worden niet ondersteund

Gebruikers worden niet in rekening gebracht voor deze functie tot de tijd die het algemeen beschikbaar is. Alle andere [functie overwegingen en beperkingen](#feature-consideration-and-limitations) zijn ook van toepassing op deze versies. Raadpleeg de [vereisten](backup-sql-server-database-azure-vms.md#prerequisites) voor het configureren van beveiliging op SQL-servers 2008 en 2008 R2.

## <a name="feature-consideration-and-limitations"></a>Overwegingen voor functies en beperkingen

* SQL Server back-up kan worden geconfigureerd in de Azure Portal of **Power shell**. CLI wordt niet ondersteund.
* De oplossing wordt ondersteund op beide soorten [implementaties](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) -Azure Resource Manager vm's en klassieke vm's.
* Voor virtuele machines met SQL Server is Internet verbinding vereist voor toegang tot open bare IP-adressen van Azure.
* SQL Server **-FCI (failover-cluster exemplaar)** en SQL Server altijd op het failovercluster worden niet ondersteund.
* Back-up-en herstel bewerkingen voor spiegel databases en database momentopnamen worden niet ondersteund.
* Het gebruik van meerdere back-upoplossingen om een back-up te maken van uw zelfstandige SQL Server exemplaar of de beschikbaarheids groep SQL always kan leiden tot back-upfouten. Dit wordt onthouden.
* Als u een back-up maakt van twee knoop punten van een beschikbaarheids groep met dezelfde of een andere oplossing, kan dit ook leiden tot back-upfouten.
* Azure Backup ondersteunt alleen volledige en alleen-kopiëren volledige back-uptypen voor **alleen-lezen** data bases
* Databases met zeer veel bestanden kunnen niet worden beveiligd. Het maximum aantal ondersteunde bestanden is **~ 1000**.  
* U kunt back-ups maken naar **~ 2000** SQL server data bases in een kluis. U kunt meerdere kluizen maken voor het geval u een groter aantal data bases hebt.
* U kunt in één bezoek back-ups configureren voor Maxi maal **50** data bases. Deze beperking helpt bij het optimaliseren van back-upbelasting.
* Data bases worden ondersteund met een grootte van Maxi maal **2 TB** . voor grootten is het mogelijk dat er prestatie problemen zijn.
* Om een idee te hebben van het aantal data bases dat per server kan worden beveiligd, moet u rekening houden met factoren zoals band breedte, VM-grootte, back-upfrequentie, database grootte, enzovoort. [down load](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) de resource planner die het geschatte aantal data bases bevat dat u kunt hebben per server op basis van de VM-resources en het back-upbeleid.
* In het geval van beschikbaarheids groepen worden er back-ups gemaakt van de verschillende knoop punten op basis van een aantal factoren. Hieronder vindt u een overzicht van het back-upgedrag voor een beschikbaarheids groep.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Back-up van gedrag in het geval van AlwaysOn-beschikbaarheids groepen

Het is raadzaam om de back-up op slechts één knoop punt van een AG te configureren. De back-up moet altijd worden geconfigureerd in dezelfde regio als het primaire knoop punt. Met andere woorden, u hebt altijd het primaire knoop punt nodig om aanwezig te zijn in de regio waarin u de back-up configureert. Als alle knoop punten van de AG zich in dezelfde regio bevinden waarin de back-up is geconfigureerd, is er geen probleem.

#### <a name="for-cross-region-ag"></a>Voor de kruislingse regio AG

* Ongeacht de back-upvoorkeur worden er geen back-ups gemaakt van de knoop punten die zich niet in dezelfde regio bevinden als de back-up is geconfigureerd. Dit komt doordat de back-ups voor meerdere regio's niet worden ondersteund. Als u slechts twee knoop punten hebt en het secundaire knoop punt zich in de andere regio bevindt. in dit geval blijven de back-ups zich voordoen op het primaire knoop punt (tenzij uw voorkeurs instelling voor back-ups alleen ' secundair ' is).
* Als er een failover optreedt naar een andere regio dan die waarin de back-up is geconfigureerd, mislukken de back-ups van de knoop punten in het gebied met de mislukte failover.

Afhankelijk van de voor keuren voor back-ups en back-ups (volledig/differentieel/niet volledig), worden back-ups gemaakt van een bepaald knoop punt (primair/secundair).

* **Back-upvoorkeur: primair**

**Back-uptype** | **Node**
    --- | ---
    Volledig | Primair
    Differentiële | Primair
    Logboek |  Primair
    Alleen-kopiëren is volledig |  Primair

* **Back-upvoorkeur: alleen secundair**

**Back-uptype** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen-kopiëren is volledig |  Secundair

* **Back-upvoorkeur: secundair**

**Back-uptype** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen-kopiëren is volledig |  Secundair

* **Geen voorkeurs instelling voor back-up**

**Back-uptype** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen-kopiëren is volledig |  Secundair

## <a name="set-vm-permissions"></a>VM-machtigingen instellen

  Wanneer u detectie uitvoert op een SQL Server, Azure Backup de volgende handelingen uit:

* Hiermee voegt u de AzureBackupWindowsWorkload-extensie toe.
* Hiermee maakt u een NT SERVICE\AzureWLBackupPluginSvc-account om data bases op de virtuele machine te detecteren. Dit account wordt gebruikt voor back-up en herstel en vereist SQL sysadmin-machtigingen.
* Hiermee worden data bases gedetecteerd die worden uitgevoerd op een virtuele machine, Azure Backup gebruikt het NT AUTHORITY\SYSTEM-account. Dit account moet een open bare aanmelding bij SQL zijn.

Als u de SQL Server virtuele machine niet hebt gemaakt in de Azure Marketplace of als u zich in SQL 2008 en 2008 R2 bevindt, kan er een **UserErrorSQLNoSysadminMembership** -fout optreden.

Zie [hier](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)voor meer informatie over het verlenen van machtigingen in het geval van **SQL 2008** en **2008 R2** die worden uitgevoerd op Windows 2008 R2.

Voor alle andere versies herstelt u de machtigingen met de volgende stappen:

  1. Meld u bij SQL Server Management Studio (SSMS) aan met een account met systeembeheerdersrechten voor SQL Server. Windows-verificatie zou moeten werken, tenzij u speciale machtigingen nodig hebt.
  2. Open de map **Security/Logins** op de SQL-server.

      ![De map Security/Logins openen om accounts te bekijken](./media/backup-azure-sql-database/security-login-list.png)

  3. Klik met de rechtermuisknop op de map **Logins** en selecteer **Nieuwe aanmelding**. In **Aanmelding - Nieuw** selecteert u **Zoeken**.

      ![In het dialoogvenster Aanmelding - Nieuw selecteert u Zoeken](./media/backup-azure-sql-database/new-login-search.png)

  4. Het virtuele Windows-serviceaccount **NT SERVICE\AzureWLBackupPluginSvc** is gemaakt tijdens de registratie van de virtuele machine en de SQL-detectiefase. Geef de accountnaam op, zoals wordt weergegeven in **Objectnaam invoeren die moet worden geselecteerd**. Selecteer **Namen controleren** om de naam te herleiden. Klik op **OK**.

      ![Namen controleren selecteren om de naam van de onbekende service te herleiden](./media/backup-azure-sql-database/check-name.png)

  5. Controleer of in **Serverrollen** de rol **sysadmin** is geselecteerd. Klik op **OK**. De vereiste machtigingen moeten nu bestaan.

      ![Controleren of de serverfunctie sysadmin is geselecteerd](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Koppel de database nu aan de Recovery Services-kluis. Klik in de Azure-portal in de lijst **Beschermde servers** met de rechtermuisknop op de server met een foutstatus > **DB's opnieuw detecteren**.

      ![Controleren of de server de juiste machtigingen heeft](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Controleer de voortgang in het gebied **Meldingen**. Wanneer de geselecteerde databases zijn gevonden, wordt er een slagingsbericht weergegeven.

      ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Als uw SQL Server meerdere exemplaren van SQL Server heeft geïnstalleerd, moet u de sysadmin-machtiging voor het **NT Service\AzureWLBackupPluginSvc** -account toevoegen aan alle SQL-exemplaren.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin-machtigingen verlenen voor SQL 2008 en SQL 2008 R2

**NT AUTHORITY\SYSTEM** -en **NT Service\AzureWLBackupPluginSvc** -aanmeldingen toevoegen aan het SQL Server-exemplaar:

1. Ga naar het SQL Server-exemplaar in object Verkenner.
2. Navigeer naar beveiliging-> aanmeldingen
3. Klik met de rechter muisknop op de aanmeldingen en klik op *nieuwe aanmelding...*

    ![Nieuwe aanmelding met behulp van SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Ga naar het tabblad Algemeen en voer **NT AUTHORITY\SYSTEM** in als aanmeldings naam.

    ![aanmeldings naam voor SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Ga naar *Server rollen* en kies *open bare* en *sysadmin* -rollen.

    ![rollen kiezen in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ga naar *status*. *Verleen* de machtiging om verbinding te maken met data base-engine en meld u aan als *ingeschakeld*.

    ![Machtigingen verlenen in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klik op OK.
8. Herhaal dezelfde reeks stappen (1-7 hierboven) om NT Service\AzureWLBackupPluginSvc-aanmelding toe te voegen aan het SQL Server-exemplaar. Als de aanmelding al bestaat, moet u ervoor zorgen dat deze de serverrol sysadmin heeft en onder status heeft de machtiging verlenen om verbinding te maken met de data base-engine en de aanmelding als ingeschakeld.
9. Nadat u een machtiging hebt verleend, **detecteert u db's** in de portal: kluis **->** back-upinfrastructuur **->** werk belasting in azure VM:

    ![Db's in Azure Portal opnieuw detecteren](media/backup-azure-sql-database/sql-rediscover-dbs.png)

U kunt ook automatiseren om de machtigingen te verlenen door de volgende Power shell-opdrachten uit te voeren in de beheer modus. De naam van het exemplaar is standaard ingesteld op MSSQLserver. Wijzig het argument exemplaar naam in het script als dat nodig is:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over](backup-sql-server-database-azure-vms.md) het maken van back-ups van SQL server-data bases.
* [Meer informatie](restore-sql-database-azure-vm.md) over het herstellen van SQL Server-databases vanuit back-up.
* [Meer informatie](manage-monitor-sql-database-backup.md) over het beheren van SQL Server-databases vanuit back-up.
