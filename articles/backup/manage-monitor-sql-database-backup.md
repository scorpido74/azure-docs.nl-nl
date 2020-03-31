---
title: SQL Server-db's beheren en bewaken op een Azure VM
description: In dit artikel wordt beschreven hoe u SQL Server-databases beheert en bewaakt die op een Azure VM worden uitgevoerd.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 4daf068e97a08d1a611ef64cb64569cacd5d7420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172163"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Back-ups van SQL Server-databases beheren en bewaken

In dit artikel worden veelvoorkomende taken beschreven voor het beheren en bewaken van SQL Server-databases die worden uitgevoerd op een virtuele Azure-machine (VM) en waarvan een back-up wordt gemaakt van een Azure Backup Recovery Services-kluis van de [Azure Backup-service.](backup-overview.md) U leert hoe u taken en waarschuwingen controleren, databasebeveiliging stoppen en hervatten, back-uptaken uitvoeren en een VM uitschrijven van back-ups.

Zie [Back-ups van SQL Server-databases maken op Azure VM's](backup-azure-sql-database.md) als u back-ups nog niet hebt geconfigureerd voor uw SQL Server-databases.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Handmatige back-uptaken in de portal controleren

Azure Backup toont alle handmatig geactiveerde taken in de **portal Back-uptaken.** De taken die u in deze portal ziet, zijn databasedetectie en -registratie en back-up- en herstelbewerkingen.

![De portal Back-uptaken](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> De portal **Back-uptaken** toont geen geplande back-uptaken. Geplande back-uptaken kunt u bewaken met SQL Server Management Studio, zoals wordt beschreven in het volgende gedeelte.
>

Ga voor meer informatie over bewakingsscenario's naar [Controleren in de Azure-portal](backup-azure-monitoring-built-in-monitor.md) en [Bewaking met Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Waarschuwingen voor back-ups weergeven

Omdat logboekbackups elke 15 minuten optreden, kan het controleren van back-uptaken vervelend zijn. Azure Backup vereenvoudigt het bewaken door e-mailwaarschuwingen te verzenden. E-mailwaarschuwingen zijn:

- Geactiveerd voor alle back-upfouten.
- Geconsolideerd op databaseniveau door foutcode.
- Alleen verzonden voor de eerste back-upstoring van een database.

Ga als volgt te werk om waarschuwingen voor databaseback-ups te controleren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer op het dashboard van de kluis de optie **Waarschuwingen en gebeurtenissen**.

   ![Waarschuwingen en gebeurtenissen selecteren](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Selecteer **back-upwaarschuwingen**in **Waarschuwingen en gebeurtenissen**.

   ![Waarschuwingen voor back-ups selecteren](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Beveiliging van een SQL Server-database stoppen

U op een aantal manieren stoppen met het maken van back-ups van een SQL Server-database:

- Stop alle toekomstige back-uptaken en verwijder alle herstelpunten.
- Stop alle toekomstige back-uptaken en laat de herstelpunten intact.

Als u ervoor kiest om herstelpunten achter te laten, houdt u rekening met deze gegevens:

- Alle herstelpunten blijven voor altijd intact, alle snoeien stopt bij stopbescherming met bewarende gegevens.
- Er worden kosten in rekening gebracht voor de beveiligde instantie en de verbruikte opslag. Zie [Azure Backup-prijzen](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie.
- Als u een gegevensbron verwijdert zonder back-ups te stoppen, mislukken nieuwe back-ups.

Ga als volgt te werk om de beveiliging van een database te stoppen:

1. Selecteer **back-upitems op**het kluisdashboard .

2. Selecteer SQL in **Azure VM** **onder Type Back-upbeheer**.

    ![SQL in Azure VM selecteren](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selecteer de database waarvoor u de beveiliging wilt stoppen.

    ![De database selecteren waarvoor u de beveiliging wilt stoppen](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Selecteer **back-up stoppen**in het databasemenu.

    ![Back-up stoppen selecteren](./media/backup-azure-sql-database/stop-db-button.png)

5. Selecteer in het menu **Back-up stoppen** of u gegevens wilt bewaren of verwijderen. Als u wilt, geef een reden en commentaar.

    ![Gegevens in het menu Back-up stoppen behouden of verwijderen](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selecteer **Back-up stoppen**.

> [!NOTE]
>
>Zie onderstaande veelgestelde vragen voor meer informatie over de optie Gegevens verwijderen:
>
>- [Als ik een database verwijder uit een automatisch beveiligde instantie, wat gebeurt er dan met de back-ups?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Als ik stop met de back-upwerking van een automatisch beveiligde database, wat is dan het gedrag ervan?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Beveiliging voor een SQL-database hervatten

Wanneer u de beveiliging van de SQL-database stopt, u de beveiliging later hervatten als u de optie **Back-upgegevens behouden** selecteert. Als u de back-upgegevens niet bewaart, u de beveiliging niet hervatten.

Ga als lid van het NIEUWE LEVEN naar de beveiliging van een SQL-database:

1. Open het back-upitem en selecteer **Back-up hervatten**.

    ![Back-up hervatten selecteren om de databasebeveiliging te hervatten](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selecteer een beleid in het menu **Back-upbeleid** en selecteer vervolgens **Opslaan**.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

U verschillende typen on-demand back-ups uitvoeren:

- Volledige back-up
- Alleen te kopiëren volledige back-up
- Differentiële back-up
- Logboekback-up

Hoewel u de bewaarduur voor volledige back-up kopiëren moet opgeven, wordt het bewaarbereik voor on-demand volledige back-up automatisch ingesteld op 45 dagen vanaf de huidige tijd.

Zie [SQL Server-back-uptypen](backup-architecture.md#sql-server-backup-types)voor meer informatie.

## <a name="unregister-a-sql-server-instance"></a>Registratie van een SQL Server-exemplaar ongedaan maken

Het registreren van een SQL Server-exemplaar uitnadat u de beveiliging hebt uitgeschakeld, maar voordat u de kluis verwijdert:

1. Selecteer **back-upinfrastructuur**onder **Beheren**.  

   ![Back-upinfrastructuur selecteren](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Onder **Beheerservers** selecteert u **Beveiligde servers**.

   ![Beveiligde servers selecteren](./media/backup-azure-sql-database/protected-servers.png)

3. Selecteer **in Beveiligde servers**de server om de registratie uit te schrijven. Als u de kluis wilt verwijderen, moet u de registratie van alle servers ongedaan maken.

4. Klik met de rechtermuisknop op de beveiligde server en selecteer **Uitschrijven.**

   ![Verwijderen selecteren](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Beleid wijzigen

Wijzig het beleid om de back-upfrequentie of het bewaarbereik te wijzigen.

> [!NOTE]
> Elke wijziging in de bewaartermijn zal achteraf worden toegepast op alle oudere herstelpunten naast de nieuwe.

Ga in het dashboard van de kluis naar**Back-upbeleid** **beheren** > en kies het beleid dat u wilt bewerken.

  ![Back-upbeleid beheren](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Back-upbeleid wijzigen](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Beleidswijziging heeft invloed op alle bijbehorende back-upitems en activeert bijbehorende **configureprotectiontaken.**

### <a name="inconsistent-policy"></a>Inconsistent beleid

Soms kan een beleidsbewerking wijzigen leiden tot een **inconsistente** beleidsversie voor sommige back-upitems. Dit gebeurt wanneer de bijbehorende **beveiligingstaak configureren** mislukt voor het back-upitem nadat een wijzigingsbeleidsbewerking is geactiveerd. Het wordt als volgt weergegeven in de weergave back-upitem:

  ![Inconsistent beleid](./media/backup-azure-sql-database/inconsistent-policy.png)

U de beleidsversie voor alle getroffen items in één klik herstellen:

  ![Inconsistent beleid oplossen](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Extensie opnieuw registreren op de SQL Server VM

Soms kan de werkbelastingextensie op de VM om de een of andere reden worden beïnvloed. In dergelijke gevallen beginnen alle bewerkingen die op de VM worden geactiveerd, te mislukken. Het kan dan nodig zijn om de extensie opnieuw te registreren op de VM. Als u de bewerking **opnieuw registreert,** wordt de back-upextensie voor werkbelasting op de VM opnieuw geïnstalleerd om bewerkingen voort te zetten.

Gebruik deze optie met de nodige voorzichtigheid; wanneer deze wordt geactiveerd op een vm met een reeds gezonde extensie, zorgt deze bewerking ervoor dat de extensie opnieuw wordt gestart. Dit kan ertoe leiden dat alle lopende taken mislukken. Controleer vriendelijk op een of meer van de [symptomen](backup-sql-server-azure-troubleshoot.md#re-registration-failures) voordat u de bewerking opnieuw registreert.

## <a name="next-steps"></a>Volgende stappen

Zie [Back-ups oplossen in een SQL Server-database](backup-sql-server-azure-troubleshoot.md)voor meer informatie.
