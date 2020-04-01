---
title: Herstel van Hyper-V (met VMM) instellen met Azure Site Recovery
description: Meer informatie over het instellen van noodherstel van on-premises Hyper V VM's in System Center VMM-clouds op Azure met behulp van Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067577"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Herstel na noodgeval van on-premises Hyper-V-VM's in VMM-clouds naar Azure

In deze zelfstudie wordt beschreven hoe u replicatie inschakelt voor on-premises virtuele machines (Hyper-V) die worden beheerd door System Center Virtual Machine Manager (VMM) naar Azure met [Azure Site Recovery](site-recovery-overview.md). Als u VMM niet gebruikt, [volgt u deze zelfstudie.](hyper-v-azure-tutorial.md)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * Stel de bronreplicatieomgeving in, inclusief on-premises siteherstelcomponenten en de doelreplicatieomgeving.
> * Netwerktoewijzing instellen tussen Vm VM-netwerken van VMM en virtuele Azure-netwerken.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Bekijk de artikelen in de sectie **Handleidingen** van de [documentatie siteherstel](/azure/site-recovery/)voor gedetailleerde instructies.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u de volgende zelfstudies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
1. [On-premises Hyper-V-servers voorbereiden](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga in de Azure-portal naar **Vaults van Recovery Services** en selecteer de **ContosoVMVault-kluis** die is gemaakt in de zelfstudie [Azure voorbereiden.](tutorial-prepare-azure.md#create-a-recovery-services-vault)
1. Selecteer >  **siteherstelinfrastructuur****vanaf** aan **de slag**en configureer de volgende instellingen:
    1. **Beschermingsdoel** > Waar bevinden uw machines **On-premises****zich?**
    1. **Waar wilt u uw machines repliceren?** **To Azure**
    1. **Worden uw machines gevirtualiseerd?** **Yes, with Hyper-V**
    1. **Gebruikt u System Center VMM om uw Hyper-V-hosts te beheren?** **Yes**
1. Selecteer **OK**.

   ![Replicatiedoel](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Als **u in Implementatieplanning**een grote implementatie plant, downloadt u de implementatieplanner voor Hyper-V via de koppeling op de pagina. [Meer informatie](hyper-v-deployment-planner-overview.md) over de implementatieplanning van Hyper V.
1. Voor deze zelfstudie hebben we de Implementatieplanner niet nodig. Selecteer In Heb je de **I will do it later** **implementatieplanning voltooid?** **OK**

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Wanneer u de bronomgeving instelt, installeert u de Azure Site Recovery Provider op de VMM-server en registreert u de server in de kluis. U installeert de Azure Recovery Services-agent op elke Hyper-V-host.

1. **Infrastructuur voorbereiden**. Selecteer **Bron**.
1. **Bron voorbereiden**. Selecteer **+ VMM** om een VMM-server toe te voegen. Controleer in **Server toevoegen**of **System Center VMM-server** wordt weergegeven bij **Servertype**.
1. Download het installatieprogramma voor de Microsoft Azure Site Recovery Provider.
1. Download de registratiesleutel voor de kluis. U hebt deze sleutel nodig wanneer u de provider-installatie uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.
1. Download het installatieprogramma voor de Microsoft Azure Recovery Services-agent.

   ![Provider, registratiesleutel en agent downloaden](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>De provider installeren op de VMM-server

1. Ga naar de wizard Microsoft **Update**van azure Site Recovery Provider Setup . U zich aanmelden om Microsoft Update te gebruiken om te controleren op providerupdates.
1. **Installatie**. Accepteer de standaardinstallatielocatie voor de provider en selecteer **Installeren**.
1. Selecteer na de installatie in de wizard Microsoft Azure Site Recovery Registration de optie **Vault Settings**, **Browse**en in **Key file,** selecteer het kluissleutelbestand dat u hebt gedownload.
1. Geef het Azure Site Recovery-abonnement en de kluisnaam op (**ContosoVMVault**). Geef een vriendelijke naam op voor de VMM-server om deze in de kluis te identificeren.
1. **Proxy-instellingen**. Selecteer **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxy**.
1. Accepteer de standaardlocatie voor het certificaat dat wordt gebruikt om gegevens te versleutelen. Versleutelde gegevens worden gedecodeerd wanneer u niet over.
1. **Cloudmetadata synchroniseren**. Selecteer **Cloudmetagegevens synchroniseren met portal Site Recovery**. Deze actie hoeft slechts één keer op elke server te gebeuren. Selecteer vervolgens **Registreren**.
1. Nadat de server is geregistreerd in de kluis, selecteert u **Voltooien**.

Nadat de registratie is voltooid, worden metagegevens van de server opgehaald door Azure Site Recovery en wordt de VMM-server weergegeven in **siteherstelinfrastructuur.**

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>De Recovery Services-agent installeren op Hyper-V-hosts

Installeer de agent op elke Hyper-V-host met VM's die u wilt repliceren.

Configureer de volgende instellingen in de wizard Setup van Microsoft Azure Recovery Services Agent:

1. **Voorwaarden Controleren**. Selecteer **Volgende**. Ontbrekende vereisten worden automatisch geïnstalleerd.
1. **Installatie-instellingen**. Accepteer de installatielocatie en de cachelocatie. Het cachestation heeft minstens 5 GB opslagruimte nodig. Wij raden een station met 600 GB of meer vrije ruimte. Selecteer vervolgens **Install**.
1. **Installatie**. Wanneer de installatie is voltooid, selecteert u **Sluiten** om de wizard te voltooien.

   ![Installatieagent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

1. Selecteer > **Infrastructuurdoel** **voorbereiden**.
1. Selecteer het abonnement en de brongroep (**ContosoRG**) waarin de Azure VM's worden gemaakt na failover.
1. Selecteer het implementatiemodel **voor ResourceBeheer.**

Siteherstel controleert of er een of meer compatibele Azure-opslagaccounts en -netwerken zijn.

## <a name="configure-network-mapping"></a>Netwerktoewijzing configureren

1. **Site Recovery Infrastructure** > **Network mappings** > **Network Mappings Network Mapping**. Selecteer het pictogram **+Netwerktoewijzing.**
1. **Netwerktoewijzing toevoegen**. Selecteer de **VMM-server van het Source System Center.** Selecteer Azure voor **het doel.**
1. Controleer na het uitvoeren van een failover het abonnement en het implementatiemodel.
1. **Bronnetwerk**. Selecteer het on-premises VM-netwerk voor bronnen.
1. **Doelnetwerk**. Selecteer het Azure-netwerk waarin replica Azure VM's worden gelokaliseerd wanneer ze zijn gemaakt na een failover. Selecteer vervolgens **OK**.

   ![Netwerktoewijzing](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer**Replicatieinstellingen** >  **voor infrastructuur** > voorbereiden **+Maken en koppelen**.
1. Geef **in Beleid maken en koppelen**een beleidsnaam op. We gebruiken **ContosoReplicationPolicy**.
1. Accepteer de standaardinstellingen en selecteer **OK:**
   - **Kopieerfrequentie** geeft aan dat deltagegevens na de eerste replicatie elke vijf minuten worden gerepliceerd.
   - **Herstelpunt retentie** geeft aan dat elk herstelpunt zal worden bewaard voor twee uur.
   - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
   - **De begintijd van de eerste replicatie** geeft aan dat de eerste replicatie onmiddellijk van start gaat.
   - **Gegevens versleutelen die zijn opgeslagen in Azure** is ingesteld op de standaard (**Uit**) en geeft aan dat at-rest-gegevens in Azure niet zijn versleuteld.
1. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u een nieuw beleid maakt, wordt het automatisch gekoppeld aan de VMM-cloud.

## <a name="enable-replication"></a>Replicatie inschakelen

1. **Toepassing repliceren**. Selecteer **Bron**.
1. **Bron**. Selecteer de VMM-cloud. Selecteer vervolgens **OK**.
1. **Doel**. Controleer het doel (Azure), het kluisabonnement, en selecteer het **Resource Manager-model.**
1. Selecteer het **contosovmsacct1910171607-opslagaccount** en het **ContosoASRnet** Azure-netwerk.
1. **Virtuele machines** > **Selecteren**. Selecteer de VM die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Nadat de taak **Beveiliging afronden** is voltooid, is de eerste replicatie voltooid en is de VM klaar voor failover.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
