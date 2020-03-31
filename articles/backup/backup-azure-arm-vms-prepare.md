---
title: Back-ups maken van Azure VM's in een vault van Recovery Services
description: Beschrijft hoe u een back-up maakt van Azure VM's in een vault van Recovery Services met behulp van de Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273512"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Back-ups maken van Azure VM's in een vault van Recovery Services

In dit artikel wordt beschreven hoe u een back-up maakt van Azure VM's in een vault van Recovery Services met behulp van de [Azure Backup-service.](backup-overview.md)

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Azure VM's voorbereiden.
> * Maak een kluis.
> * Ontdek VM's en configureer een back-upbeleid.
> * Back-up voor Azure VM's inschakelen.
> * De eerste back-up uitvoeren.

> [!NOTE]
> In dit artikel wordt beschreven hoe u een kluis instelt en vm's selecteert om een back-up te maken. Het is handig als u een back-up wilt maken van meerdere VM's. U ook [een back-up maken van één Azure-vm](backup-azure-vms-first-look-arm.md) rechtstreeks vanuit de VM-instellingen.

## <a name="before-you-start"></a>Voordat u begint

* [Bekijk](backup-architecture.md#architecture-built-in-azure-vm-backup) de Azure VM-back-uparchitectuur.
* [Meer informatie over](backup-azure-vms-introduction.md) Azure VM-back-up en de back-upextensie.
* [Bekijk de ondersteuningsmatrix](backup-support-matrix-iaas.md) voordat u een back-up configureert.

Daarnaast zijn er een paar dingen die je zou kunnen moeten doen in sommige omstandigheden:

* **Installeer de VM-agent op de VM:** Azure Backup maakt back-ups van Azure VM's door een extensie te installeren voor de Azure VM-agent die op de machine wordt uitgevoerd. Als uw VM is gemaakt op basis van een Azure-marketplace-afbeelding, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste vm maakt of als u een on-premises machine migreert, moet u de agent mogelijk [handmatig installeren.](#install-the-vm-agent)

## <a name="create-a-vault"></a>Een kluis maken

 Een kluis slaat back-ups en herstelpunten op die in de loop van de tijd zijn gemaakt en slaat back-upbeleid op dat is gekoppeld aan back-upapparaten. Maak als volgt een kluis:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Typ **Herstelservices**in zoekopdrachten . Klik **onder Services**op **Vaults van Herstelservices**.

     ![Zoeken naar vaults van Recovery Services](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. Klik in **het menu Vaults van Recovery Services** op **+Toevoegen**.

     ![Een Recovery Services-kluis maken, stap 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Typ in **de kluis Van Recovery Services**een vriendelijke naam om de kluis te identificeren.
    * De naam moet uniek zijn voor het Azure-abonnement.
    * Het kan 2 tot 50 tekens bevatten.
    * Het moet beginnen met een brief, en het kan alleen letters, cijfers en koppeltekens bevatten.
5. Selecteer het Azure-abonnement, de resourcegroep en de geografische regio waarin de kluis moet worden gemaakt. Klik vervolgens op **Maken**.
    * Het kan even duren voordat de kluis is gemaakt.
    * Controleer de statusmeldingen in het rechterbovengedeelte van de portal.

Nadat de kluis is gemaakt, wordt deze weergegeven in de lijst met kluizen van Herstelservices. Als u uw kluis niet ziet, selecteert u **Vernieuwen**.

![Lijst met back-upkluizen](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup maakt nu het mogelijk om de naam van de brongroep aan te passen die is gemaakt door de Azure Backup-service. Zie [Azure Backup-brongroep voor virtuele machines voor](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)meer informatie.

### <a name="modify-storage-replication"></a>Opslagreplicatie wijzigen

Standaard gebruiken kluizen [georedundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U [lokaal redundante opslag (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken voor een goedkopere optie.

Wijzig het type opslagreplicatie als volgt:

1. Klik in de nieuwe kluis op **Eigenschappen** in de sectie **Instellingen.**
2. Klik in **Eigenschappen**onder **Back-upconfiguratie**op **Bijwerken**.
3. Selecteer het type opslagreplicatie en klik op **Opslaan**.

      ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > U het type opslagreplicatie niet wijzigen nadat de kluis is ingesteld en bevat back-upitems. Als je dit wilt doen, moet je de kluis opnieuw maken.

## <a name="apply-a-backup-policy"></a>Een back-upbeleid toepassen

Een back-upbeleid configureren voor de kluis.

1. Klik in de kluis op **+Back-up** in de sectie **Overzicht.**

   ![Knop Back-up](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. In **Back-updoel** > **Waar wordt uw werkbelasting uitgevoerd?** selecteer **Azure**. In **Wat wilt u een back-up maken?** Selecteer Virtuele **machine** >  **OK**. Hiermee wordt de VM-extensie in de kluis geregistreerd.

   ![Deelvensters Back-upen en back-updoelen](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Selecteer **in Back-upbeleid**het beleid dat u aan de kluis wilt koppelen.
    * Met het standaardbeleid wordt één keer per dag een back-up van de vm gemaakt. De dagelijkse back-ups worden 30 dagen bewaard. Momentopnamen voor direct herstel worden twee dagen bewaard.
    * Als u het standaardbeleid niet wilt gebruiken, selecteert u **Nieuw maken**en maakt u een aangepast beleid zoals beschreven in de volgende procedure.

      ![Standaardback-upbeleid](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Selecteer **in Virtuele machines selecteren**de VM's waarop u een back-up wilt maken met behulp van het beleid. Klik vervolgens op **OK**.

   * De geselecteerde VM's worden gevalideerd.
   * U alleen VM's selecteren in dezelfde regio als de kluis.
   * VM's kunnen alleen worden geback-upt in één kluis.

     ![Deelvenster 'Virtuele machines selecteren'](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Klik **in Back-up**op **Back-up inschakelen**. Hiermee wordt het beleid geïmplementeerd in de kluis en naar de VM's en wordt de back-upextensie op de VM-agent die op de Azure VM wordt uitgevoerd, geïnstalleerd.

     ![Knop Back-up inschakelen](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Na het inschakelen van back-up:

* De back-upservice installeert de back-upextensie, ongeacht of de VM wordt uitgevoerd.
* Een eerste back-up wordt uitgevoerd in overeenstemming met uw back-upschema.
* Wanneer back-ups worden uitgevoerd, moet u er rekening mee houden dat:
  * Een VM die wordt uitgevoerd heeft de grootste kans voor het vastleggen van een toepassingsconsistent herstelpunt.
  * Echter, zelfs als de VM is uitgeschakeld is een back-up. Zo'n VM staat bekend als een offline VM. In dit geval is het herstelpunt crashconsistent.
* Expliciete uitgaande connectiviteit is niet vereist om back-ups van Azure VM's toe te staan.

### <a name="create-a-custom-policy"></a>Aangepast beleid maken

Als u hebt geselecteerd om een nieuw back-upbeleid te maken, vult u de beleidsinstellingen in.

1. Geef **in De naam van het beleid**een zinvolle naam op.
2. Geef in **back-upschema**aan wanneer back-ups moeten worden gemaakt. U dagelijkse of wekelijkse back-ups maken voor Azure VM's.
3. Geef in **Instant Restore**op hoe lang u momentopnamen lokaal wilt behouden voor direct herstel.
    * Wanneer u back-ups van VM-schijven herstelt, worden deze over het hele netwerk gekopieerd van opslag naar de locatie voor herstelopslag. Met direct herstel u gebruikmaken van lokaal opgeslagen momentopnamen die zijn gemaakt tijdens een back-uptaak, zonder te wachten tot back-upgegevens naar de kluis worden overgebracht.
    * U momentopnamen voor direct herstel tussen één tot vijf dagen behouden. Twee dagen is de standaardinstelling.
4. Geef in **het bewaarbereik**op hoe lang u uw dagelijkse of wekelijkse back-uppunten wilt behouden.
5. Geef **in Retentie van het maandelijkse back-uppunt**op of u een maandelijkse back-up van uw dagelijkse of wekelijkse back-ups wilt behouden.
6. Klik op **OK** het beleid op te slaan.

    ![Nieuw back-upbeleid](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup biedt geen ondersteuning voor automatische klokaanpassing voor wijzigingen bij het herstellen van daglicht voor Azure VM-back-ups. Als er tijdswijzigingen optreden, wijzigt u het back-upbeleid handmatig naar behoefte.

## <a name="trigger-the-initial-backup"></a>De eerste back-up activeren

De eerste back-up wordt uitgevoerd volgens het schema, maar u deze onmiddellijk als volgt uitvoeren:

1. Klik in het kluismenu op **Back-upitems**.
2. Klik **in Back-upitems**op **Azure Virtual Machine**.
3. Klik in de lijst **Back-upitems** op de ellips (...).
4. Klik **nu op Back-up**.
5. Gebruik **in Nu back-up**het agendabesturingselement om de laatste dag te selecteren waarop het herstelpunt moet worden behouden. Klik vervolgens op **OK**.
6. Controleer de portalmeldingen. U de voortgang van de taak in het kluisdashboard controleren > **Back-uptaken** > **in uitvoering**. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

## <a name="verify-backup-job-status"></a>Status back-uptaak verifiëren

De taakgegevens voor back-ups voor elke VM-back-up bestaan uit twee fasen, de **momentopnamefase** gevolgd door de **overdrachtsgegevens naar de kluisfase.**<br/>
De momentopnamefase garandeert de beschikbaarheid van een herstelpunt dat samen met de schijven is opgeslagen voor **Instant Restores** en is maximaal vijf dagen beschikbaar, afhankelijk van de momentopnameretentie die door de gebruiker is geconfigureerd. Het overbrengen van gegevens naar kluis creëert een herstelpunt in de kluis voor langdurige bewaring. Gegevens overbrengen naar kluis begint pas nadat de momentopnamefase is voltooid.

  ![Status van back-uptaak](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Er zijn twee **subtaken** die worden uitgevoerd op de backend, een voor front-end back-up taak die kan worden gecontroleerd vanuit de **Back-up Job** details blade zoals hieronder vermeld:

  ![Status van back-uptaak](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

De **overdrachtsgegevens naar de kluisfase** kunnen meerdere dagen in beslag nemen, afhankelijk van de grootte van de schijven, de verloop per schijf en verschillende andere factoren.

De taakstatus kan variëren afhankelijk van de volgende scenario's:

**Momentopname** | **Gegevens overbrengen naar kluis** | **Functiestatus**
--- | --- | ---
Voltooid | Wordt uitgevoerd | Wordt uitgevoerd
Voltooid | Overgeslagen | Voltooid
Voltooid | Voltooid | Voltooid
Voltooid | Mislukt | Aangevuld met waarschuwing
Mislukt | Mislukt | Mislukt

Nu met deze mogelijkheid, voor dezelfde VM, twee back-ups kunnen parallel worden uitgevoerd, maar in beide fasen (momentopname, overdracht van gegevens naar kluis) slechts een subtaak kan worden uitgevoerd. Dus in scenario's waren een back-up taak in uitvoering resulteerde in de volgende dag back-up te mislukken zal worden vermeden met deze ontkoppeling functionaliteit. De back-ups van de volgende dag kunnen momentopname hebben voltooid terwijl **Transfergegevens naar kluis** zijn overgeslagen als de back-uptaak van een eerdere dag wordt uitgevoerd.
Het incrementele herstelpunt dat in de kluis is gemaakt, legt alle churn vast van het laatste herstelpunt dat in de kluis is gemaakt. Er zijn geen kosten van invloed op de gebruiker.

## <a name="optional-steps"></a>Optionele stappen

### <a name="install-the-vm-agent"></a>De VM-agent installeren

Azure Backup maakt een back-up van Azure VM's door een extensie te installeren voor de Azure VM-agent die op de machine wordt uitgevoerd. Als uw vm is gemaakt op basis van een Azure Marketplace-afbeelding, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste vm maakt of als u een on-premises machine migreert, moet u de agent mogelijk handmatig installeren, zoals samengevat in de tabel.

**Vm** | **Details**
--- | ---
**Windows** | 1. [Download en installeer](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) het agent MSI-bestand.<br/><br/> 2. Installeer met beheerdersmachtigingen op de machine.<br/><br/> 3. Controleer de installatie. Klik in *C:\WindowsAzure\Packages* on the VM met de rechtermuisknop op **Eigenschappen van WaAppAgent.exe** > **Properties**. Op het tabblad **Details** moet **productversie** 2.6.1198.718 of hoger zijn.<br/><br/> Als u de agent bijwerkt, controleert u of er geen back-upbewerkingen worden uitgevoerd en [installeert u de agent opnieuw.](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)
**Linux** | Installeren met behulp van een RPM of een DEB-pakket uit de pakketrepository van uw distributie. Dit is de voorkeursmethode voor het installeren en upgraden van de Azure Linux-agent. Alle [goedgekeurde distributieproviders](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integreren het Azure Linux-agentpakket in hun afbeeldingen en repositories. De agent is beschikbaar op [GitHub,](https://github.com/Azure/WALinuxAgent)maar we raden niet aan om vanaf daar te installeren.<br/><br/> Als u de agent bijwerkt, controleert u of er geen back-upbewerkingen worden uitgevoerd en werkt u de binaire bestanden bij.

>[!NOTE]
> **Azure Backup ondersteunt nu selectieve schijfback-up en herstel met behulp van de Azure Virtual Machine-back-upoplossing.**
>
>Tegenwoordig ondersteunt Azure Backup een back-up van alle schijven (besturingssysteem en gegevens) in een VM samen met behulp van de back-upoplossing voor virtuele machines. Met de functionaliteit van de uitgesloten schijf krijgt u een optie om een of enkele back-ups te maken van de vele gegevensschijven in een vm. Dit biedt een efficiënte en kosteneffectieve oplossing voor uw back-up- en herstelbehoeften. Elk herstelpunt bevat gegevens van de schijven die zijn opgenomen in de back-upbewerking, waarmee u verder een subset van schijven laten herstellen vanaf het gegeven herstelpunt tijdens de herstelbewerking. Dit geldt voor het herstellen van zowel de momentopname als de kluis.
>
>**Als u zich wilt aanmelden voor de preview, schrijft u ons opAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Volgende stappen

* Problemen met [Azure VM-agents](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) of [Azure VM-back-up oplossen.](backup-azure-vms-troubleshoot.md)
* [Herstellen](backup-azure-arm-restore-vms.md) Azure VM's.
