---
title: Een back-up van SQL Server-databases maken in Azure
description: In dit artikel wordt uitgelegd hoe u een back-up maakt van SQL Server naar Azure. In het artikel wordt ook uitgelegd hoe u SQL Server kunt herstellen.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408757"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Over SQL Server-back-ups in virtuele Azure-machines

[Azure Backup](backup-overview.md) biedt een op stroom gebaseerde, gespecialiseerde oplossing om een back-up te maken van SQL Server die wordt uitgevoerd in Azure VM's. Deze oplossing sluit aan bij de voordelen van back-ups met zero-infrastructure, langdurige retentie en centraal beheer. Het biedt bovendien de volgende voordelen specifiek voor SQL Server:

1. Workloadbewuste back-ups die alle back-uptypen ondersteunen - volledig, differentieel en logboek
2. 15-min RPO (doel herstelpunt) met frequente logboekback-ups
3. Point-in-time herstel tot een seconde
4. Back-up en herstel op individueel databaseniveau

Als u de back-up- en herstelscenario's wilt bekijken die we vandaag ondersteunen, raadpleegt u de [ondersteuningsmatrix.](sql-support-matrix.md#scenario-support)

## <a name="backup-process"></a>Back-upproces

Deze oplossing maakt gebruik van de SQL native API's om back-ups van uw SQL-databases te maken.

* Zodra u de SQL Server VM opgeeft die u wilt beveiligen en query's wilt uitvoeren voor `AzureBackupWindowsWorkload` de databases daarin, installeert Azure Backup-service een workloadback-extensie op de VM met de naamextensie.
* Deze extensie bestaat uit een coördinator en een SQL plugin. Terwijl de coördinator verantwoordelijk is voor het activeren van workflows voor verschillende bewerkingen, zoals het configureren van back-up, back-up en herstel, is de plugin verantwoordelijk voor de werkelijke gegevensstroom.
* Azure Backup maakt het account `NT SERVICE\AzureWLBackupPluginSvc`om databases op deze vm te ontdekken. Dit account wordt gebruikt voor back-up en herstel en vereist SQL sysadmin-machtigingen. Het `NT SERVICE\AzureWLBackupPluginSvc` account is een [Virtual Service-account](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)en vereist daarom geen wachtwoordbeheer. Azure Backup maakt `NT AUTHORITY\SYSTEM` gebruik van het account voor databasedetectie/-onderzoek, dus dit account moet een openbare login op SQL zijn. Als u de SQL Server-VM niet hebt gemaakt vanuit de Azure Marketplace, ontvangt u mogelijk het foutbericht **UserErrorSQLNoSysadminMembership**. Volg in dit geval [deze instructies](#set-vm-permissions).
* Zodra u de configure-beveiliging voor de geselecteerde databases activeert, stelt de back-upservice de coördinator in met de back-upschema's en andere beleidsdetails, die de extensie lokaal op de VM in slaat.
* Op het geplande tijdstip communiceert de coördinator met de plug-in en begint hij de back-upgegevens van de SQL-server te streamen met VDI.  
* De plugin stuurt de gegevens rechtstreeks naar de kluis van de herstelservices, waardoor een staging-locatie niet meer nodig is. De gegevens worden versleuteld en opgeslagen door de Azure Backup-service in opslagaccounts.
* Wanneer de gegevensoverdracht is voltooid, bevestigt de coördinator de commit met de back-upservice.

  ![SQL Backup-architectuur](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Voordat u begint

Controleer voordat u begint het onderstaande:

1. Zorg ervoor dat er een SQL Server-exemplaar wordt uitgevoerd in Azure. U kunt [snel een SQL Server-exemplaar maken](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) in de marketplace.
2. Bekijk de [overweging van](sql-support-matrix.md#feature-consideration-and-limitations) de functie en de ondersteuning [voor scenario's](sql-support-matrix.md#scenario-support).
3. [Lees de veelgestelde vragen](faq-backup-sql-server.md) over dit scenario.

## <a name="set-vm-permissions"></a>VM-machtigingen instellen

  Wanneer u detectie uitvoert op een SQL Server, doet Azure Backup het volgende:

* Voegt de extensie AzureBackupWindowsWorkload toe.
* Hiermee maakt u een NT-service\AzureWLBackupPluginSvc-account om databases op de virtuele machine te ontdekken. Dit account wordt gebruikt voor een back-up en herstel en vereist SQL sysadmin-machtigingen.
* Azure Backup detecteert databases die op een VM worden uitgevoerd en maakt gebruik van het NT AUTHORITY\SYSTEM-account. Dit account moet een openbare aanmelding op SQL zijn.

Als u de SQL Server VM niet hebt gemaakt in de Azure Marketplace of als u SQL 2008 en 2008 R2 hebt ingeschakeld, ontvangt u mogelijk een **userErrorSQLNoSysadminMembership-fout.**

Voor het geven van machtigingen in het geval van **SQL 2008** en **2008 R2** die op Windows 2008 R2 worden uitgevoerd, [raadpleegt](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)u hier .

Voor alle andere versies u machtigingen met de volgende stappen oplossen:

  1. Meld u bij SQL Server Management Studio (SSMS) aan met een account met systeembeheerdersrechten voor SQL Server. Windows-verificatie zou moeten werken, tenzij u speciale machtigingen nodig hebt.
  2. Open de map **Security/Logins** op de SQL-server.

      ![De map Security/Logins openen om accounts te bekijken](./media/backup-azure-sql-database/security-login-list.png)

  3. Klik met de rechtermuisknop op de map **Aanmeldingen** en selecteer **Nieuwe aanmelding**. In **Aanmelding - Nieuw** selecteert u **Zoeken**.

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
> Als op uw SQL Server meerdere exemplaren van SQL Server zijn geïnstalleerd, moet u sysadmin-machtigingen voor **NT Service\AzureWLBackupPluginSvc-account** toevoegen aan alle SQL-instanties.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL-sysadmin-machtigingen voor SQL 2008 en SQL 2008 R2 geven

**NT AUTHORITY\SYSTEM** and **NT Service\AzureWLBackupPluginSvc** logt in bij het SQL Server-exemplaar:

1. Ga naar de SQL Server Instance in de Object explorer.
2. Navigeren naar aanmeldingen voor beveiliging >
3. Klik met de rechtermuisknop op de aanmeldingen en klik op *Nieuwe aanmelding...*

    ![Nieuwe aanmelding met SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Ga naar het tabblad Algemeen en voer **NT AUTHORITY\SYSTEM** in als inlognaam.

    ![inlognaam voor SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Ga naar *Serverrollen* en kies *publieke* en *sysadmin* rollen.

    ![rollen kiezen in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ga naar *Status*. *Geef* de toestemming om verbinding te maken met database-engine en Aanmelding *als ingeschakeld*.

    ![Machtigingen verlenen in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klik op OK.
8. Herhaal dezelfde reeks stappen (1-7 hierboven) om NT Service\AzureWLBackupPluginSvc-aanmelding toe te voegen aan de SQL Server-instantie. Als de aanmelding al bestaat, controleert u of de sysadmin-serverrol is en onder Status de machtiging verlenen om verbinding te maken met de databaseengine en Aanmelding als ingeschakeld.
9. Na het verlenen van toestemming **herontdekt u DB's** in de portal: Vault **->** Backup Infrastructure **->** Workload in Azure VM:

    ![DB's opnieuw ontdekken in Azure-portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

U ook automatiseren met het geven van de machtigingen door de volgende PowerShell-opdrachten in de beheermodus uit te voeren. De instantienaam is standaard ingesteld op MSSQLSERVER. Wijzig indien nodig het argument van de instantienaam in het script:

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

* [Meer informatie over](backup-sql-server-database-azure-vms.md) het maken van back-ups van SQL Server-databases.
* [Meer informatie](restore-sql-database-azure-vm.md) over het herstellen van SQL Server-databases vanuit back-up.
* [Meer informatie](manage-monitor-sql-database-backup.md) over het beheren van SQL Server-databases vanuit back-up.
