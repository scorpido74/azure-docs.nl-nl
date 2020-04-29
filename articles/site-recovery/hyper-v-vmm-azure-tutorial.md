---
title: Herstel na nood gevallen voor Hyper-V (met VMM) instellen met behulp van Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen van on-premises virtuele Hyper-V-machines in System Center VMM-Clouds naar Azure met behulp van Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067577"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Herstel na noodgeval van on-premises Hyper-V-VM's in VMM-clouds naar Azure

In deze zelf studie wordt beschreven hoe u replicatie inschakelt voor on-premises virtuele Hyper-V-machines (VM) die worden beheerd door System Center Virtual Machine Manager (VMM) naar Azure met behulp van [Azure site Recovery](site-recovery-overview.md). Als u VMM niet gebruikt, [volgt u deze zelf studie](hyper-v-azure-tutorial.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * Stel de bron replicatie omgeving in, met inbegrip van on-premises Site Recovery onderdelen en de doel replicatie omgeving.
> * Stel netwerk toewijzing in tussen VMM VM-netwerken en virtuele Azure-netwerken.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de artikelen in de sectie **instructies** van de site Recovery- [documentatie](/azure/site-recovery/)voor gedetailleerde instructies.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u de volgende zelf studies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
1. [On-premises Hyper-V-servers voorbereiden](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga in het Azure Portal naar **Recovery Services kluizen** en selecteer de **ContosoVMVault** -kluis die is gemaakt in de zelf studie [Azure voorbereiden](tutorial-prepare-azure.md#create-a-recovery-services-vault) .
1. Selecteer **site Recovery** > **infra structuur voorbereiden** en configureer de volgende instellingen om **aan**de slag te gaan:
    1. **Doel** > van beveiliging**waar uw machines zich bevinden**, selecteert u **on-premises**.
    1. **Waar wilt u uw computers repliceren?**, selecteer dan **naar Azure**.
    1. **Zijn uw machines gevirtualiseerd?**, selecteert u **Ja, met Hyper-V**.
    1. **Gebruikt u System Center VMM om uw Hyper-V-hosts te beheren?**, selecteert u **Ja**.
1. Selecteer **OK**.

   ![Replicatiedoel](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Bij het plannen van de **implementatie**kunt u, als u een grote implementatie plant, de Deployment planner voor Hyper-V downloaden via de koppeling op de pagina. Meer [informatie](hyper-v-deployment-planner-overview.md) over de planning van de implementatie van Hyper-V.
1. Voor deze zelf studie hebben we de Deployment Planner niet nodig. In **hebt u de implementatie planning voltooid?** Selecteer **Ik ga dan later**en selecteer **OK**.

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Wanneer u de bron omgeving instelt, installeert u de Azure Site Recovery provider op de VMM-server en registreert u de server in de kluis. U installeert de Azure Recovery Services-agent op elke Hyper-V-host.

1. **Infra structuur voorbereiden**. Selecteer **bron**.
1. **Bron voorbereiden**. Selecteer **+ VMM** om een VMM-server toe te voegen. Controleer in **Server toevoegen**of **System Center VMM-server** wordt weergegeven bij **Servertype**.
1. Down load het installatie programma voor de provider van de Microsoft Azure-Site Recovery.
1. Download de registratiesleutel voor de kluis. U hebt deze sleutel nodig wanneer u de provider-installatie uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.
1. Down load het installatie programma voor de Microsoft Azure Recovery Services-agent.

   ![Provider, registratie sleutel en agent downloaden](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>De provider installeren op de VMM-server

1. In de wizard Azure Site Recovery provider instellen **Microsoft Update**. Meld u aan om Microsoft Update te gebruiken om te controleren op provider updates.
1. **Installatie**. Accepteer de standaard installatie locatie voor de provider en selecteer **installeren**.
1. Selecteer na de installatie in de wizard Microsoft Azure Site Recovery registratie de optie **kluis instellingen**, **Bladeren**en selecteer het kluis **sleutel bestand dat**u hebt gedownload.
1. Geef het Azure Site Recovery abonnement en de kluis naam (**ContosoVMVault**) op. Geef een beschrijvende naam op voor de VMM-server om deze te identificeren in de kluis.
1. **Proxy-instellingen**. Selecteer **rechtstreeks verbinding maken met Azure site Recovery zonder een proxy**.
1. Accepteer de standaard locatie voor het certificaat dat wordt gebruikt voor het versleutelen van gegevens. Versleutelde gegevens worden ontsleuteld wanneer er een failover wordt uitgevoerd.
1. **Meta gegevens van de Cloud synchroniseren**. Selecteer **meta gegevens van Cloud synchroniseren met site Recovery Portal**. Deze actie moet slechts één keer op elke server plaatsvinden. Selecteer vervolgens **registreren**.
1. Nadat de server is geregistreerd in de kluis, selecteert u **volt ooien**.

Nadat de registratie is voltooid, worden de meta gegevens van de server opgehaald door Azure Site Recovery en wordt de VMM-server weer gegeven in **site Recovery-infra structuur**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>De Recovery Services-agent installeren op Hyper-V-hosts

Installeer de agent op elke Hyper-V-host met de virtuele machines die u wilt repliceren.

In de installatie wizard van de Microsoft Azure Recovery Services-agent configureert u de volgende instellingen:

1. **Controle van vereisten**. Selecteer **Next**. Eventuele ontbrekende vereiste onderdelen worden automatisch geïnstalleerd.
1. **Installatie-instellingen**. Accepteer de installatie locatie en de cache locatie. Het cache station heeft ten minste 5 GB aan opslag ruimte nodig. U wordt aangeraden een station met 600 GB of meer vrije ruimte te maken. Selecteer vervolgens **Install**.
1. **Installatie**. Wanneer de installatie is voltooid, selecteert u **sluiten** om de wizard te volt ooien.

   ![Agent installeren](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

1. Selecteer **infra structuur** > voorbereiden**doel**.
1. Selecteer het abonnement en de resource groep (**ContosoRG**) waarin de virtuele Azure-machines na een failover worden gemaakt.
1. Selecteer het **Resource Manager** -implementatie model.

Site Recovery controleert of er sprake is van een of meer compatibele Azure Storage-accounts en-netwerken.

## <a name="configure-network-mapping"></a>Netwerktoewijzing configureren

1. **Site Recovery Infrastructure** > **Network mappings** > **Netwerk toewijzing**van site Recovery infrastructuur netwerk. Selecteer het pictogram **+ netwerk toewijzing** .
1. **Netwerk toewijzing toevoegen**. Selecteer de **bron System Center VMM** -server. Selecteer voor het **doel**Azure.
1. Controleer na het uitvoeren van een failover het abonnement en het implementatiemodel.
1. **Bron netwerk**. Selecteer het on-premises bron-VM-netwerk.
1. **Doelnet doel netwerk**. Selecteer het Azure-netwerk waarin de replica's van virtuele Azure-machines worden opgeslagen wanneer ze na een failover worden gemaakt. Selecteer vervolgens **OK**.

   ![Netwerktoewijzing](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer > **replicatie-instellingen** > voor **infra structuur voorbereiden****+ maken en koppelen**.
1. Geef in **beleid maken en koppelen**een beleids naam op. We gebruiken **ContosoReplicationPolicy**.
1. Accepteer de standaard instellingen en selecteer **OK**:
   - De **Kopieer frequentie** geeft aan dat er na de initiële replicatie Delta gegevens elke vijf minuten worden gerepliceerd.
   - **Bewaar periode van het herstel punt** geeft aan dat elk herstel punt twee uur wordt bewaard.
   - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
   - **Start tijd van de initiële replicatie** geeft aan dat de initiële replicatie onmiddellijk wordt gestart.
   - **Gegevens versleutelen die zijn opgeslagen in azure** , worden ingesteld op de standaard waarde (**uit**) en geeft aan dat de rest-gegevens in azure niet zijn versleuteld.
1. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u een nieuw beleid maakt, wordt dit automatisch gekoppeld aan de VMM-Cloud.

## <a name="enable-replication"></a>Replicatie inschakelen

1. **Toepassing repliceren**. Selecteer **bron**.
1. **Bron**. Selecteer de VMM-Cloud. Selecteer vervolgens **OK**.
1. **Doel**. Controleer het doel (Azure), het kluis abonnement en selecteer het **Resource Manager** -model.
1. Selecteer het **contosovmsacct1910171607** -opslag account en het **ContosoASRnet** Azure-netwerk.
1. **Virtuele machines** > **selecteren**. Selecteer de virtuele machine die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Nadat de taak **beveiliging** volt ooien is voltooid, is de initiële replicatie voltooid en is de virtuele machine klaar voor failover.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
