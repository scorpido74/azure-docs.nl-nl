---
title: VMware-Vm's herstellen met Azure Backup Server
description: Gebruik Azure Backup Server (MABS) om VMware VM's te herstellen die worden uitgevoerd op een VMware vCenter/ESXi-server.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212363"
---
# <a name="restore-vmware-virtual-machines"></a>VMware virtuele machines herstellen

In dit artikel wordt uitgelegd hoe u Microsoft Azure Backup Server (MABS) gebruiken om VMware VM-herstelpunten te herstellen. Zie [Beveiligde gegevens herstellen](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)voor een overzicht over het gebruik van MABS om gegevens te herstellen. In de MABS Administrator Console zijn er twee manieren om herstelbare gegevens te vinden : zoeken of bladeren. Bij het herstellen van gegevens u gegevens of een virtuele machine niet naar dezelfde locatie herstellen. Om deze reden ondersteunt MABS drie herstelopties voor VMware VM-back-ups:

* **Oorspronkelijke locatieherstel (OLR)** - Gebruik OLR om een beveiligde VM naar de oorspronkelijke locatie te herstellen. U een vm alleen naar de oorspronkelijke locatie herstellen als er geen schijven zijn toegevoegd of verwijderd, omdat de back-up is opgetreden. Als schijven zijn toegevoegd of verwijderd, moet u alternatieve locatieherstel gebruiken.

* **Alternatieve locatieherstel (ALR)** - Wanneer de oorspronkelijke vm ontbreekt of u de oorspronkelijke vm niet wilt verstoren, herstelt u de VM naar een alternatieve locatie. Als u een VM wilt herstellen naar een alternatieve locatie, moet u de locatie opgeven van een ESXi-host, resourcegroep, map en het opslaggegevensarchief en -pad. Om de herstelde VM te onderscheiden van de oorspronkelijke VM, voegt MABS "-Recovered" toe aan de naam van de VM.

* **ILR (Individual file location recovery)** - Als de beveiligde VM een Windows Server VM is, kunnen afzonderlijke bestanden/mappen in de VM worden hersteld met behulp van de ILR-mogelijkheid van MABS. Zie de procedure later in dit artikel om afzonderlijke bestanden te herstellen.

## <a name="restore-a-recovery-point"></a>Een herstelpunt herstellen

1. Klik in de MABS Administrator Console op Herstelweergave.

2. Blader in het deelvenster Bladeren door te bladeren om de vm te vinden die u wilt herstellen. Zodra u een vm of map hebt geselecteerd, worden in het deelvenster Herstelpunten voor de beschikbare herstelpunten de beschikbare herstelpunten weergegeven.

    ![Beschikbare herstelpunten](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. Gebruik in het veld **Herstelpunten voor** de agenda en de vervolgkeuzemenu's een datum waarop een herstelpunt is ingenomen. Agendadatums in vet hebben beschikbare herstelpunten.

4. Klik op het gereedschapslint op **Herstellen** om de wizard Herstel te **openen.**

    ![Wizard Herstel, Selectie herstel controleren](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Klik **op Volgende** om door te gaan naar het scherm **Herstelopties opgeven.**

6. Klik in het scherm **Herstelopties opgeven** als u netwerkbandbreedtebeperking wilt inschakelen op **Wijzigen**. Als u netwerkbeperking uitgeschakeld wilt laten, klikt u op **Volgende**. Er zijn geen andere opties op dit wizardscherm beschikbaar voor VMware VM's. Als u ervoor kiest de gashendel voor de netwerkbandbreedte te wijzigen, selecteert u in het dialoogvenster Gaspedaal de **optie Netwerkbandbreedtebeperking inschakelen** om deze in te schakelen. Configureer, zodra deze is **ingeschakeld,** de instellingen en **het werkschema**.

7. Kies in het scherm **Hersteltype** selecteren of u wilt herstellen naar de oorspronkelijke instantie of naar een nieuwe locatie en klik op **Volgende**.

     * Als u **Herstellen naar de oorspronkelijke instantie**kiest, hoeft u geen keuzes meer te maken in de wizard. De gegevens voor de oorspronkelijke instantie worden gebruikt.

     * Als u **Herstellen als virtuele machine op een host**kiest, geeft u in het scherm Doel **opgeven** de informatie op voor **ESXi Host, Resource Pool, Folder** en **Path.**

      ![Hersteltype selecteren](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Controleer **in** het scherm Overzicht uw instellingen en klik op **Herstellen** om het herstelproces te starten. Het **statusscherm Herstel** toont de voortgang van de herstelbewerking.

## <a name="restore-an-individual-file-from-a-vm"></a>Een individueel bestand herstellen vanaf een virtuele machine

U afzonderlijke bestanden herstellen vanaf een beveiligd VM-herstelpunt. Deze functie is alleen beschikbaar voor Windows Server VM's. Het herstellen van afzonderlijke bestanden is vergelijkbaar met het herstellen van de hele VM, behalve dat u naar de VMDK bladert en het gewenste bestand(en) vindt voordat u het herstelproces start. Ga als een extern bestand te werk of bestanden uit een Windows Server-vm te selecteren:

>[!NOTE]
>Het herstellen van een afzonderlijk bestand vanaf een virtuele machine is alleen beschikbaar voor Windows VM- en Schijfherstelpunten.

1. Klik in de MABS Administrator Console op **Herstelweergave.**

2. Blader in het deelvenster **Bladeren** door te bladeren om de vm te vinden die u wilt herstellen. Zodra u een vm of map hebt geselecteerd, worden in het deelvenster Herstelpunten voor de beschikbare herstelpunten de beschikbare herstelpunten weergegeven.

    ![Beschikbare herstelpunten](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. Gebruik in het deelvenster **Herstelpunten voor:** de agenda om de datum te selecteren die het gewenste herstelpunt(en) bevat. Afhankelijk van hoe het back-upbeleid is geconfigureerd, kunnen datums meer dan één herstelpunt hebben. Zodra u de dag hebt geselecteerd waarop het herstelpunt is ingenomen, moet u ervoor zorgen dat u de juiste **hersteltijd**hebt gekozen. Als de geselecteerde datum meerdere herstelpunten heeft, kiest u uw herstelpunt door deze te selecteren in het vervolgkeuzemenu Hersteltijd. Zodra u het herstelpunt hebt gekozen, wordt de lijst met herstelbare items weergegeven in het deelvenster **Pad:**

4. Als u de bestanden wilt vinden die u wilt herstellen, dubbelklikt u in het deelvenster **Pad** op het item in de kolom **Herstelbaar item** om het te openen. Selecteer het bestand, de bestanden of mappen die u wilt herstellen. Als u meerdere items wilt selecteren, drukt u op **ctrl** terwijl u elk item selecteert. Gebruik het deelvenster **Pad** om in de lijst met bestanden of mappen te zoeken die worden weergegeven in de kolom **Herstelbaar item.** **Onderstaande zoeklijst** zoekt niet in submappen. Als u door submappen wilt zoeken, dubbelklikt u op de map. Gebruik de knop **Op** om van een onderliggende map naar de bovenliggende map te gaan. U meerdere items (bestanden en mappen) selecteren, maar ze moeten zich in dezelfde bovenliggende map bevinden. U items uit meerdere mappen in dezelfde hersteltaak niet herstellen.

    ![Herstelselectie controleren](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Wanneer u het item(en) voor herstel hebt geselecteerd, klikt u op het lint van het gereedschap Administratorconsole op **Herstellen** om de **wizard Herstel**te openen . In de wizard Herstel wordt in het scherm **Selectie van herstel** controleren de geselecteerde items weergegeven die moeten worden hersteld.

6. Klik in het scherm **Herstelopties opgeven** als u netwerkbandbreedtebeperking wilt inschakelen op **Wijzigen**. Als u netwerkbeperking uitgeschakeld wilt laten, klikt u op **Volgende**. Er zijn geen andere opties op dit wizardscherm beschikbaar voor VMware VM's. Als u ervoor kiest de gashendel voor de netwerkbandbreedte te wijzigen, selecteert u in het dialoogvenster Gaspedaal de **optie Netwerkbandbreedtebeperking inschakelen** om deze in te schakelen. Configureer, zodra deze is **ingeschakeld,** de instellingen en **het werkschema**.
7. Klik in het scherm **Hersteltype selecteren** op **Volgende**. U uw bestand(en) of map(s) alleen herstellen naar een netwerkmap.
8. Klik **in** het scherm Doel opgeven op **Bladeren** om een netwerklocatie voor uw bestanden of mappen te zoeken. MABS maakt een map waarin alle herstelde items worden gekopieerd. De mapnaam heeft het voorvoegsel, MABS_day-maandjaar. Wanneer u een locatie voor de herstelde bestanden of map selecteert, worden de gegevens voor die locatie (bestemming, bestemmingspad en beschikbare ruimte) verstrekt.

    ![Locatie opgeven om bestanden te herstellen](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Kies in het scherm **Herstelopties opgeven** welke beveiligingsinstelling u wilt toepassen. U ervoor kiezen om de beperking van het netwerkbandbreedtegebruik te wijzigen, maar beperking is standaard uitgeschakeld. **SAN-herstel** en **-melding** zijn ook niet ingeschakeld.
10. Controleer **in** het scherm Overzicht uw instellingen en klik op **Herstellen** om het herstelproces te starten. Het **statusscherm Herstel** toont de voortgang van de herstelbewerking.

## <a name="next-steps"></a>Volgende stappen

Controleer de [handleiding voor probleemoplossing voor Azure Backup Server voor](./backup-azure-mabs-troubleshoot.md)het oplossen van problemen bij het gebruik van Azure Backup Server.
