---
title: Back-ups maken van Windows-machines met behulp van de MARS-agent
description: Gebruik de MARS-agent (Microsoft Azure Recovery Services) om een back-up te maken van Windows-machines.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408909"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Back-ups maken van Windows-machines met de Azure Backup MARS-agent

In dit artikel wordt uitgelegd hoe u een back-up maakt van Windows-machines met behulp van de [Azure Backup-service](backup-overview.md) en de MARS-agent (Microsoft Azure Recovery Services). MARS wordt ook wel de Azure Backup-agent genoemd.

In dit artikel leert u hoe u:

> [!div class="checklist"]
>
> * De vereisten controleren
> * Maak een back-upbeleid en -planning.
> * Voer een on-demand back-up uit.

## <a name="before-you-start"></a>Voordat u begint

* Meer informatie over hoe [Azure Backup de MARS-agent gebruikt om een back-up te maken van Windows-machines.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Meer informatie over de [back-uparchitectuur](backup-architecture.md#architecture-back-up-to-dpmmabs) waarop de MARS-agent wordt uitgevoerd op een secundaire MABS- of Data Protection Manager-server.
* Bekijk [wat er wordt ondersteund en wat je back-ups](backup-support-matrix-mars-agent.md) maken door de MARS-agent.
* [Controleer de internettoegang](install-mars-agent.md#verify-internet-access) op de machines waarvan u een back-up wilt maken.
* Als de MARS-agent niet is geïnstalleerd, leest u hier hoe u deze [installeert.](install-mars-agent.md)

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Het back-upbeleid geeft aan wanneer momentopnamen van de gegevens moeten worden gemaakt om herstelpunten te maken. Het geeft ook aan hoe lang herstelpunten moeten worden behouden. U gebruikt de MARS-agent om een back-upbeleid te configureren.

Azure Backup houdt niet automatisch rekening met zomertijd (Zomertijd). Deze standaardinstelling kan enige discrepantie veroorzaken tussen de werkelijke tijd en de geplande back-uptijd.

Ga als volgt te werk om een back-upbeleid te maken:

1. Nadat u de MARS-agent hebt gedownload en geregistreerd, opent u de agentconsole. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.  

1. Selecteer **Onder Acties**de optie **Back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)
1. Selecteer Volgende in de wizard Back-up plannen de optie **Volgende aan de** > **slag**.
1. Selecteer Onder **Objecten selecteren om een back-up te maken,** items **toevoegen**.

    ![Items toevoegen om een back-up te maken](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Selecteer **in** het vak Items selecteren items om een back-up te maken en selecteer **OK**.

    ![Items selecteren om een back-up van te maken](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Selecteer volgende op de pagina **Items selecteren om een back-up te** **maken**.
1. Geef op de pagina **Back-upplanning opgeven** op wanneer u dagelijkse of wekelijkse back-ups moet maken. Selecteer **vervolgens Volgende**.

    * Er wordt een herstelpunt gemaakt wanneer een back-up wordt gemaakt.
    * Het aantal herstelpunten dat in uw omgeving is gemaakt, is afhankelijk van uw back-upschema.
    * U maximaal drie dagelijkse back-ups per dag plannen. In het volgende voorbeeld vinden twee dagelijkse back-ups plaats, één om middernacht en één om 18:00 uur.

        ![Een dagelijks back-upschema instellen](./media/backup-configure-vault/day-schedule.png)

    * U ook wekelijkse back-ups uitvoeren. In het volgende voorbeeld worden back-ups elke alternatieve zondag en woensdag om 9:30 en 01:00 uur genomen.

        ![Een wekelijks back-upschema instellen](./media/backup-configure-vault/week-schedule.png)

1. Geef op de pagina **Bewaarbeleid selecteren** op hoe u historische kopieën van uw gegevens opslaan. Selecteer **vervolgens Volgende**.

    * Bewaarinstellingen geven aan welke herstelpunten u moet opslaan en hoe lang deze moeten worden opgeslagen.
    * Voor een dagelijkse retentieinstelling geeft u aan dat op het tijdstip dat is opgegeven voor de dagelijkse retentie, het laatste herstelpunt voor het opgegeven aantal dagen wordt bewaard. U ook een maandelijks bewaarbeleid opgeven om aan te geven dat het herstelpunt dat op de 30e van elke maand is gemaakt, gedurende 12 maanden moet worden opgeslagen.
    * Retentie voor dagelijkse en wekelijkse herstelpunten valt meestal samen met het back-upschema. Dus wanneer de planning een back-up activeert, wordt het herstelpunt dat de back-up maakt opgeslagen voor de duur die het dagelijkse of wekelijkse bewaarbeleid opgeeft.
    * In het volgende voorbeeld:

        * Dagelijkse back-ups om middernacht en 18:00 uur worden zeven dagen bewaard.
        * Back-ups genomen op een zaterdag om middernacht en 6:00 PM worden bewaard voor vier weken.
        * Back-ups genomen op de laatste zaterdag van de maand om middernacht en 6:00 PM worden bewaard voor 12 maanden.
        * Back-ups genomen op de laatste zaterdag in maart worden bewaard voor 10 jaar.

        ![Voorbeeld van een bewaarbeleid](./media/backup-configure-vault/retention-example.png)

1. Bepaal op de pagina **Eerste back-uptype kiezen** of u de eerste back-up via het netwerk wilt maken of offline back-up wilt gebruiken. Als u de eerste back-up over het netwerk wilt nemen, selecteert u **Automatisch via het netwerk** > **Volgende**.

    Zie Azure Data Box [gebruiken voor offline back-up voor](offline-backup-azure-data-box.md)meer informatie over offline back-up.

    ![Een eerste back-uptype kiezen](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Controleer op de pagina **Bevestiging** de informatie en selecteer **Voltooien.**

    ![Het back-uptype bevestigen](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Nadat de wizard klaar is met het maken van het back-upschema, selecteert u **Sluiten**.

    ![De voortgang van het back-upschema weergeven](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Maak een beleid voor elke machine waarin de agent is geïnstalleerd.

### <a name="do-the-initial-backup-offline"></a>De eerste back-up offline doen

U een eerste back-up automatisch via het netwerk uitvoeren of offline een back-up maken. Offline zaaien voor een eerste back-up is handig als u grote hoeveelheden gegevens hebt waarvoor veel netwerkbandbreedte nodig is om over te dragen.

Ga als een offline transfer:

1. Schrijf de back-upgegevens naar een faseringslocatie.
1. Gebruik het hulpprogramma AzureOfflineBackupDiskPrep om de gegevens van de faseringslocatie naar een of meer SATA-schijven te kopiëren.

    Met de tool wordt een Azure Import-taak gestart. Zie [Wat is de azure import/exportservice](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)voor meer informatie .
1. Stuur de SATA-schijven naar een Azure-datacenter.

    In het datacenter worden de schijfgegevens gekopieerd naar een Azure-opslagaccount. Azure Backup kopieert de gegevens van het opslagaccount naar de kluis en incrementele back-ups zijn gepland.

Zie [Azure Data Box gebruiken voor offline back-up voor](offline-backup-azure-data-box.md)meer informatie over offline zaaien.

### <a name="enable-network-throttling"></a>Netwerkbeperking inschakelen

U bepalen hoe de MARS-agent netwerkbandbreedte gebruikt door netwerkbeperking in te schakelen. Beperking is handig als u tijdens werkuren een back-up van gegevens moet maken, maar u wilt bepalen hoeveel bandbreedte de back-up- en herstelactiviteit gebruikt.

Netwerkbeperking in Azure Backup maakt gebruik [van Quality of Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) op het lokale besturingssysteem.

Netwerkbeperking voor back-ups is beschikbaar op Windows Server 2012 en hoger en op Windows 8 en hoger. Besturingssystemen moeten de nieuwste servicepacks draaien.

Ga als een netwerkbeperking:

1. Selecteer **Eigenschappen wijzigen**in de AGENT MARS .
1. Selecteer op het tabblad **Beperking** de **optie Beperking van internetbandbreedte gebruik inschakelen voor back-upbewerkingen.**

    ![Netwerkbeperking instellen voor back-upbewerkingen](./media/backup-configure-vault/throttling-dialog.png)
1. Geef de toegestane bandbreedte op tijdens werkuren en niet-werkuren. Bandbreedtewaarden beginnen bij 512 Kbps en gaan tot 1.023 MBps. Selecteer vervolgens **OK**.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

1. Selecteer **back-up nu**in de MARS-agent .

    ![Nu een back-up maken in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Als de MARS-agentversie 2.0.9169.0 of nieuwer is, u een aangepaste bewaardatum instellen. Kies in de sectie **Back-up behouden tot** een datum in de agenda.

   ![De agenda gebruiken om een bewaardatum aan te passen](./media/backup-configure-vault/mars-ondemand.png)

1. Controleer op de pagina **Bevestiging** de instellingen en selecteer **Back-up maken.**
1. Selecteer **Sluiten** om de wizard te sluiten. Als u de wizard sluit voordat de back-up is voltooid, blijft de wizard op de achtergrond worden uitgevoerd.

Nadat de eerste back-up is voltooid, wordt de status **Taak voltooid** weergegeven in de back-upconsole.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>On-demand back-upbeleidsbehoudsgedrag instellen

> [!NOTE]
> Deze informatie is alleen van toepassing op MARS-agentversies die ouder zijn dan 2.0.9169.0.
>

| Optie Back-upplanning | Duur van het bewaren van gegevens
| -- | --
| Dag | **Standaardretentie:** Gelijk aan de 'retentie in dagen voor dagelijkse back-ups'. <br/><br/> **Uitzondering:** Als een dagelijkse geplande back-up die is ingesteld voor langdurige retentie (weken, maanden of jaren) mislukt, wordt een on-demand back-up die direct na de fout wordt geactiveerd, in aanmerking genomen voor langdurige retentie. Anders wordt de volgende geplande back-up overwogen voor langdurige retentie.<br/><br/> **Voorbeeldscenario:** De geplande back-up op donderdag om 8:00 uur is mislukt. Deze back-up moest worden overwogen voor wekelijkse, maandelijkse of jaarlijkse retentie. Dus de eerste on-demand back-up geactiveerd vóór de volgende geplande back-up op vrijdag om 8:00 uur wordt automatisch getagd voor wekelijkse, maandelijkse of jaarlijkse retentie. Deze back-up vervangt de donderdag 8:00 am back-up.
| Wekelijks | **Standaardretentie**: Één dag. On-demand back-ups die worden genomen voor een gegevensbron met een wekelijks back-upbeleid worden de volgende dag verwijderd. Ze worden verwijderd, zelfs als ze de meest recente back-ups voor de gegevensbron zijn. <br/><br/> **Uitzondering:** Als een wekelijkse geplande back-up die is ingesteld voor langdurige retentie (weken, maanden of jaren) mislukt, wordt een on-demand back-up die direct na de fout wordt geactiveerd, in aanmerking genomen voor langdurige retentie. Anders wordt de volgende geplande back-up overwogen voor langdurige retentie. <br/><br/> **Voorbeeldscenario:** De geplande back-up op donderdag om 8:00 uur is mislukt. Deze back-up moest worden overwogen voor maandelijkse of jaarlijkse retentie. Dus de eerste on-demand back-up die wordt geactiveerd voor de volgende geplande back-up op donderdag om 8:00 uur wordt automatisch getagd voor maandelijkse of jaarlijkse retentie. Deze back-up vervangt de donderdag 8:00 am back-up.

Zie [Een back-upbeleid maken](#create-a-backup-policy)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van bestanden in Azure](backup-azure-restore-windows-server.md).
* [Veelvoorkomende vragen vinden over het maken van back-ups van bestanden en mappen](backup-azure-file-folder-backup-faq.md)

