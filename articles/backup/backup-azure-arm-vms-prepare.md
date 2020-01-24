---
title: Back-ups maken van virtuele Azure-machines in een Recovery Services kluis
description: Hierin wordt beschreven hoe u back-ups maakt van virtuele Azure-machines in een Recovery Services kluis met behulp van de Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705544"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Back-ups maken van virtuele Azure-machines in een Recovery Services kluis

In dit artikel wordt beschreven hoe u back-ups maakt van virtuele Azure-machines in een Recovery Services kluis, met behulp van de [Azure backup](backup-overview.md) -service.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Virtuele Azure-machines voorbereiden.
> * Maak een kluis.
> * Virtuele machines detecteren en een back-upbeleid configureren.
> * Schakel back-ups voor virtuele Azure-machines in.
> * De eerste back-up uitvoeren.

> [!NOTE]
> In dit artikel wordt beschreven hoe u een kluis instelt en Vm's selecteert waarvan u een back-up wilt maken. Het is handig als u een back-up wilt maken van meerdere Vm's. U kunt ook rechtstreeks vanuit de VM-instellingen [een back-up maken van één Azure-VM](backup-azure-vms-first-look-arm.md) .

## <a name="before-you-start"></a>Voordat u begint

* [Bekijk](backup-architecture.md#architecture-built-in-azure-vm-backup) de Azure VM-back-uparchitectuur.
* [Meer informatie over](backup-azure-vms-introduction.md) Back-ups van Azure-VM'S en de uitbrei ding van back-ups.
* [Raadpleeg de ondersteunings matrix](backup-support-matrix-iaas.md) voordat u een back-up configureert.

Daarnaast zijn er een aantal dingen die u in bepaalde omstandigheden mogelijk moet doen:

* **Installeer de VM-agent op de VM**: Azure backup maakt back-ups van virtuele Azure-machines door een uitbrei ding te installeren in de Azure VM-agent die op de computer wordt uitgevoerd. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste VM maakt of een on-premises machine migreert, moet u [de agent mogelijk hand matig installeren](#install-the-vm-agent).

## <a name="create-a-vault"></a>Een kluis maken

 Een kluis slaat back-ups en herstel punten op die gedurende een periode zijn gemaakt en slaat back-upbeleid op dat is gekoppeld aan back-upcomputers. Maak als volgt een kluis:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Typ **Recovery Services**in zoeken. Klik onder **Services**op **Recovery Services kluizen**.

     ![Recovery Services kluizen zoeken](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. Klik in **Recovery Services menu kluizen** op **+ toevoegen**.

     ![Een Recovery Services-kluis maken, stap 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Typ in **Recovery Services kluis**een beschrijvende naam om de kluis aan te duiden.
    * De naam moet uniek zijn voor het Azure-abonnement.
    * Dit kan twee tot 50 tekens bevatten.
    * De naam moet beginnen met een letter en mag alleen letters, cijfers en afbreek streepjes bevatten.
5. Selecteer het Azure-abonnement, de resource groep en de geografische regio waarin de kluis moet worden gemaakt. Klik vervolgens op **Maken**.
    * Het kan even duren voordat de kluis is gemaakt.
    * Bewaak de status meldingen in de rechter bovenhoek van de portal.

Nadat de kluis is gemaakt, wordt deze weer gegeven in de lijst Recovery Services kluizen. Als uw kluis niet wordt weer geven, selecteert u **vernieuwen**.

![Lijst met back-upkluizen](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup kunt nu de naam van de resource groep aanpassen die is gemaakt door de Azure Backup-service. Zie voor meer informatie [Azure backup resource groep voor virtual machines](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Opslag replicatie wijzigen

Standaard gebruiken kluizen de [geo-redundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U kunt [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken voor een goedkopere optie.

Wijzig het type opslag replicatie als volgt:

1. Klik in de nieuwe kluis op **Eigenschappen** in de sectie **instellingen** .
2. In **Eigenschappen**, onder **back-upconfiguratie**, klikt u op **bijwerken**.
3. Selecteer het type opslag replicatie en klik op **Opslaan**.

      ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > U kunt het type opslag replicatie niet wijzigen nadat de kluis is ingesteld en back-upitems bevat. Als u dit wilt doen, moet u de kluis opnieuw maken.

## <a name="apply-a-backup-policy"></a>Een back-upbeleid Toep assen

Configureer een back-upbeleid voor de kluis.

1. Klik in de kluis op **+ back-up** in de sectie **overzicht** .

   ![Knop back-up](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. In **back-updoel** > **waar wordt uw workload uitgevoerd?** Selecteer **Azure**. In **waarvan wilt u een back-up maken?** Selecteer de **virtuele machine** >  **OK**. Hiermee wordt de VM-extensie in de kluis geregistreerd.

   ![Deel Vensters voor back-up en back-up](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Selecteer in **back-upbeleid**het beleid dat u aan de kluis wilt koppelen.
    * Het standaard beleid maakt eenmaal per dag een back-up van de VM. De dagelijkse back-ups worden 30 dagen bewaard. Moment opnamen voor direct herstel worden twee dagen bewaard.
    * Als u het standaard beleid niet wilt gebruiken, selecteert u **nieuwe maken**en maakt u een aangepast beleid zoals beschreven in de volgende procedure.

      ![Standaard back-upbeleid](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Selecteer in **virtuele machines selecteren**de vm's waarvan u een back-up wilt maken met behulp van het beleid. Klik vervolgens op **OK**.

   * De geselecteerde Vm's worden gevalideerd.
   * U kunt alleen Vm's in dezelfde regio als de kluis selecteren.
   * Er kan alleen een back-up van virtuele machines worden gemaakt in één kluis.

     ![Het deel venster virtuele machines selecteren](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Klik in **back-up**op **back-up inschakelen**. Hiermee wordt het beleid geïmplementeerd voor de kluis en de virtuele machines en wordt de back-upextensie geïnstalleerd op de VM-agent die wordt uitgevoerd op de virtuele machine van Azure.

     ![Knop back-up inschakelen](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Na het inschakelen van back-up:

* De back-upservice installeert de back-upextensie, ongeacht of de virtuele machine wordt uitgevoerd.
* Een eerste back-up wordt uitgevoerd in overeenstemming met uw back-upschema.
* Houd bij het uitvoeren van back-ups rekening met het volgende:
  * Een virtuele machine die wordt uitgevoerd, heeft de grootste kans om een toepassings consistent herstel punt vast te leggen.
  * Maar zelfs als de VM is uitgeschakeld, wordt er een back-up van gemaakt. Een dergelijke virtuele machine wordt ook wel een offline-VM genoemd. In dit geval wordt het herstel punt vastlopen consistent.
* Expliciete uitgaande verbinding is niet vereist voor het toestaan van back-ups van virtuele Azure-machines.

### <a name="create-a-custom-policy"></a>Aangepast beleid maken

Als u hebt geselecteerd voor het maken van een nieuw back-upbeleid, vult u de beleids instellingen in.

1. Geef in **beleids naam**een beschrijvende naam op.
2. Geef in **back-upschema**op wanneer back-ups moeten worden gemaakt. U kunt dagelijks of wekelijks back-ups maken voor Azure-Vm's.
3. Geef in **direct terugzetten**op hoe lang u moment opnamen lokaal wilt behouden voor direct terugzetten.
    * Wanneer u een back-up van VM-schijven herstelt, worden deze vanuit het netwerk gekopieerd naar de opslag locatie voor herstel. Met direct terugzetten kunt u lokaal opgeslagen moment opnamen gebruiken die zijn gemaakt tijdens een back-uptaak, zonder te wachten tot back-upgegevens worden overgebracht naar de kluis.
    * U kunt moment opnamen voor direct terugzetten tussen een en vijf dagen bewaren. Twee dagen is de standaard instelling.
4. Geef in **Bewaar termijn**op hoe lang u uw dagelijkse of wekelijkse back-uppunten wilt houden.
5. Geef bij het **bewaren van maandelijks back-uppunt**op of u een maandelijkse back-up van uw dagelijkse of wekelijkse back-ups wilt behouden.
6. Klik op **OK** het beleid op te slaan.

    ![Nieuw back-upbeleid](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup biedt geen ondersteuning voor automatische aanpassing van de klok voor zomer-en winter wijzigingen voor back-ups van Azure-VM'S. Wanneer er wijzigingen optreden, moet u het back-upbeleid hand matig aanpassen.

## <a name="trigger-the-initial-backup"></a>De eerste back-up activeren

De eerste back-up wordt uitgevoerd volgens de planning, maar u kunt deze als volgt direct uitvoeren:

1. Klik in het menu kluis op **Back-upitems**.
2. Klik in **Back-upitems**op **virtuele machine van Azure**.
3. Klik in de lijst **Back-upitems** op het weglatings teken (...).
4. Klik op **Nu back-up maken**.
5. In **Nu back-up**kunt u het besturings element kalender gebruiken om de laatste dag te selecteren dat het herstel punt moet worden bewaard. Klik vervolgens op **OK**.
6. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** > worden **uitgevoerd**. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

## <a name="verify-backup-job-status"></a>Status van back-uptaak controleren

De details van de back-uptaak voor elke VM-back-up bestaan uit twee fasen: de **momentopname** fase, gevolgd door de fase **gegevens overdragen naar de kluis** .<br/>
De momentopname fase garandeert de beschik baarheid van een herstel punt dat is opgeslagen samen met de schijven voor **onmiddellijke herstel** bewerkingen en is Maxi maal vijf dagen beschikbaar, afhankelijk van de retentie van de moment opname die door de gebruiker is geconfigureerd. Bij het overdragen van gegevens naar de kluis wordt een herstel punt in de kluis gemaakt voor lange termijn retentie. Het overdragen van gegevens naar de kluis begint alleen wanneer de fase van de moment opname is voltooid.

  ![Status van back-uptaak](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Er worden twee **subtaken** uitgevoerd op de back-end, een voor front-end back-uptaak die kan worden gecontroleerd op de Blade Details van de **back-uptaak** , zoals hieronder wordt vermeld:

  ![Status van back-uptaak](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

De **overdracht van gegevens naar de kluis** fase kan meerdere dagen duren, afhankelijk van de grootte van de schijven, het verloop per schijf en verschillende andere factoren.

De taak status kan variëren, afhankelijk van de volgende scenario's:

**Snapshot** | **Gegevens overdragen aan de kluis** | **Taak status**
--- | --- | ---
Voltooid | Wordt uitgevoerd | Wordt uitgevoerd
Voltooid | Overgeslagen | Voltooid
Voltooid | Voltooid | Voltooid
Voltooid | Mislukt | Voltooid met waarschuwing
Mislukt | Mislukt | Mislukt

Met deze mogelijkheid kunnen twee back-ups voor dezelfde virtuele machine parallel worden uitgevoerd, maar in beide fase (moment opname, gegevens overdragen naar de kluis) kan slechts één subtaak worden uitgevoerd. In scenario's werd een back-uptaak uitgevoerd waardoor de back-up van de volgende dag niet kan worden vermeden met deze ontkoppelings functionaliteit. De back-ups van de volgende dag kunnen een moment opname hebben voltooid terwijl **gegevens worden overgedragen naar de kluis die** wordt overgeslagen als de back-uptaak van een eerdere dag wordt uitgevoerd.
Met het incrementele herstel punt dat in de kluis is gemaakt, worden alle verloop van het laatste herstel punt dat in de kluis is gemaakt, vastgelegd. Er is geen kosten gevolgen voor de gebruiker.

## <a name="optional-steps"></a>Optionele stappen

### <a name="install-the-vm-agent"></a>De VM-agent installeren

Azure Backup maakt back-ups van virtuele Azure-machines door een uitbrei ding te installeren in de Azure VM-agent die op de computer wordt uitgevoerd. Als uw virtuele machine is gemaakt op basis van een installatie kopie van Azure Marketplace, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste VM maakt of een on-premises machine migreert, moet u de agent mogelijk hand matig installeren, zoals in de tabel wordt samenvatten.

**VM** | **Details**
--- | ---
**Windows** | 1. [down load en installeer](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) het MSI-bestand van de agent.<br/><br/> 2. Installeer met beheerders machtigingen op de computer.<br/><br/> 3. Controleer de installatie. Klik in *C:\WindowsAzure\Packages* op de virtuele machine met de rechter muisknop op **WaAppAgent. exe** > **Eigenschappen**. Op het tabblad **Details** moet de **product versie** 2.6.1198.718 of hoger zijn.<br/><br/> Als u de agent bijwerkt, moet u ervoor zorgen dat er geen back-upbewerkingen worden uitgevoerd en [installeert u de agent opnieuw](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installeer met behulp van een RPM-of een DEB-pakket vanuit de pakket opslagplaats van uw distributie. Dit is de aanbevolen methode voor het installeren en upgraden van de Azure Linux-agent. Alle [gewaarmerkte distributie providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integreren het Azure Linux-agent pakket in hun installatie kopieën en opslag plaatsen. De agent is beschikbaar op [github](https://github.com/Azure/WALinuxAgent), maar we raden niet aan om te installeren.<br/><br/> Als u de agent bijwerkt, zorg er dan voor dat er geen back-upbewerkingen worden uitgevoerd en werk de binaire bestanden bij.

>[!NOTE]
> **Azure Backup ondersteunt nu selectieve back-up en herstel met behulp van de back-upoplossing van Azure virtual machine.**
>
>Momenteel biedt Azure Backup ondersteuning voor het maken van back-ups van alle schijven (besturings systeem en gegevens) in een virtuele machine met behulp van de back-upoplossing van de VM. Met de functionaliteit voor uitsluiten van schijven krijgt u een optie om een back-up te maken van een of enkele van de vele gegevens schijven in een VM. Dit biedt een efficiënte en rendabele oplossing voor uw back-up-en herstel behoeften. Elk herstel punt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u een subset van schijven die zijn hersteld vanaf het opgegeven herstel punt tijdens de herstel bewerking kunt laten herstellen. Dit is van toepassing om beide te herstellen vanuit de moment opname en de kluis.
>
>**Als u zich wilt aanmelden voor de preview, schrijft u voor AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Volgende stappen

* Los eventuele problemen met [Azure VM-agents](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) of [back-ups van Azure VM](backup-azure-vms-troubleshoot.md)op.
* [Herstellen](backup-azure-arm-restore-vms.md) Azure-Vm's.
