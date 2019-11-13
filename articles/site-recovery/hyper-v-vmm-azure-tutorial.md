---
title: Herstel na nood gevallen voor Hyper-V (met VMM) instellen met behulp van Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen van on-premises virtuele Hyper-V-machines in System Center VMM-Clouds naar Azure met behulp van Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fdf6d9674305ca13af51f3f7b97e0a40568738b6
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953975"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Herstel na nood gevallen instellen voor on-premises virtuele Hyper-V-machines in VMM-Clouds naar Azure

In dit artikel wordt beschreven hoe u replicatie inschakelt voor on-premises virtuele Hyper-V-machines die worden beheerd door System Center Virtual Machine Manager (VMM), voor herstel na nood gevallen naar Azure met behulp van de [Azure site Recovery](site-recovery-overview.md) -service. Als u VMM niet gebruikt, [volgt u deze zelf studie](hyper-v-azure-tutorial.md) .

Dit is de derde zelf studie in een serie die laat zien hoe u herstel na nood gevallen voor Azure instelt voor on-premises VMware-Vm's. In de vorige zelf studie hebben we [de on-premises Hyper-V-omgeving voor bereid](hyper-v-prepare-on-premises-tutorial.md) op herstel na nood geval naar Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * Stel de bron replicatie omgeving in, met inbegrip van on-premises Site Recovery onderdelen en de doel replicatie omgeving.
> * Stel netwerk toewijzing in om toe te wijzen tussen VMM VM-netwerken en virtuele Azure-netwerken.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de artikelen in de sectie **instructies** van de site Recovery- [documentatie](https://docs.microsoft.com/azure/site-recovery)voor gedetailleerde instructies.

> [!WARNING]
> Ondersteuning voor herstel na nood gevallen van virtuele Hyper-V-machines met System Center VMM zal in de nabije toekomst worden afgeschaft. We raden u aan om de details van de [afschaffing](scvmm-site-recovery-deprecation.md) te lezen voordat u doorgaat.


## <a name="before-you-begin"></a>Voordat u begint

Dit is de derde zelfstudie in een reeks. Hierbij wordt ervan uitgegaan dat u de taken in de vorige zelf studies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga in het Azure Portal naar **Recovery Services kluizen** en selecteer de kluis. We hebben de kluis **ContosoVMVault** in de vorige zelf studie voor bereid.
2. Selecteer in **aan**de slag **site Recovery**en selecteer vervolgens **infra structuur voorbereiden**.
3. Selecteer **on-premises**in het **beveiligings doel** > **waar bevinden zich uw machines?**
4. In **waar wilt u uw computers repliceren?** Selecteer **naar Azure**.
5. In **worden uw machines gevirtualiseerd?** , selecteert u **Ja, met Hyper-V**.
6. In **gebruikt u System Center VMM om uw Hyper-V-hosts te beheren?** , selecteert u **Ja**.
7.  Selecteer **OK**.

    ![Replicatiedoel](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Bij het plannen van de **implementatie**kunt u, als u een grote implementatie plant, de Deployment planner voor Hyper-V downloaden via de koppeling op de pagina. Meer [informatie](hyper-v-deployment-planner-overview.md) over de planning van de implementatie van Hyper-V.
2. Voor deze zelf studie hebben we de Deployment Planner niet nodig. In **hebt u de implementatie planning voltooid?** Selecteer **Ik ga dan later**en selecteer **OK**.

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Wanneer u de bron omgeving instelt, installeert u de Azure Site Recovery provider op de VMM-server en registreert u de server in de kluis. U installeert de Azure Recovery Services-agent op elke Hyper-V-host.

1. Selecteer **bron**in **infra structuur voorbereiden**.
2. Selecteer in **bron voorbereiden**de optie **+ VMM** om een VMM-server toe te voegen. Controleer in **Server toevoegen**of **System Center VMM-server** wordt weergegeven bij **Servertype**.
3. Down load het installatie programma voor de provider van de Microsoft Azure-Site Recovery.
4. Download de registratiesleutel voor de kluis. U hebt deze sleutel nodig wanneer u de provider-installatie uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.
5. Down load het installatie programma voor de Microsoft Azure Recovery Services-agent.

    ![Provider, registratie sleutel en agent downloaden](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>De provider installeren op de VMM-server

1. Ga in de installatiewizard van Azure Site Recovery Provider naar **Microsoft Update**, en kies ervoor om Microsoft Update te gebruiken om te controleren op Provider-updates.
2. Accepteer in de **installatie**de standaard installatie locatie voor de provider en selecteer **installeren**.
3. Ga na de installatie naar de wizard Microsoft Azure Site Recovery registratie > **kluis instellingen**, selecteer **Bladeren**en selecteer in **sleutel bestand**het kluis sleutel bestand dat u hebt gedownload.
4. Geef het Azure Site Recovery abonnement en de kluis naam (**ContosoVMVault**) op. Geef een beschrijvende naam op voor de VMM-server om deze te identificeren in de kluis.
5. Selecteer in **Proxy-instellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**.
6. Accepteer de standaard locatie voor het certificaat dat wordt gebruikt voor het versleutelen van gegevens. Versleutelde gegevens worden ontsleuteld wanneer er een failover wordt uitgevoerd.
7. Selecteer in de **meta gegevens van de Cloud synchroniseren** **meta gegevens van cloud synchroniseren met site Recovery Portal**. Deze actie moet slechts één keer op elke server plaatsvinden. Selecteer vervolgens **registreren**.
8. Nadat de server is geregistreerd in de kluis, selecteert u **volt ooien**.

Nadat de registratie is voltooid, worden de meta gegevens van de server opgehaald door Azure Site Recovery, waarna de VMM-server wordt weer gegeven in **site Recovery-infra structuur**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>De Recovery Services-agent op Hyper-V-hosts installeren

Installeer de agent op elke Hyper-V-host met de virtuele machines die u wilt repliceren.

1. Selecteer **volgende**in de wizard Setup van Microsoft Azure Recovery Services-agent > **vereisten controleren**. Eventuele ontbrekende vereiste onderdelen worden automatisch geïnstalleerd.
2. Accepteer in **installatie-instellingen**de installatie locatie en de cache locatie. Het cache station heeft ten minste 5 GB aan opslag ruimte nodig. U wordt aangeraden een station met 600 GB of meer vrije ruimte te maken. Selecteer vervolgens **Install**.
3. Wanneer de installatie is voltooid, selecteert u in **installatie**de optie **sluiten** om de wizard te volt ooien.

    ![Agent installeren](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

1. Selecteer **Infrastructuur voorbereiden** > **Doel**.
2. Selecteer het abonnement en de resource groep (**ContosoRG**) waarin de virtuele Azure-machines na een failover worden gemaakt.
3. Selecteer het **Resource Manager** -implementatie model.

Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

## <a name="configure-network-mapping"></a>Netwerktoewijzing configureren

1. Selecteer in **site Recovery infrastructuur** > **netwerk toewijzingen** > **netwerk toewijzing**het pictogram **+ netwerk toewijzing** .
2. Selecteer in **netwerk toewijzing toevoegen**de Bron-VMM-server. Selecteer **Azure** als doel.
3. Controleer na het uitvoeren van een failover het abonnement en het implementatiemodel.
4. Selecteer in **bron netwerk**het on-premises bron-VM-netwerk.
5. Selecteer in **doelnet**werk het Azure-netwerk waarin de Replica's van virtuele Azure-machines worden opgeslagen wanneer ze na een failover worden gemaakt. Selecteer vervolgens **OK**.

    ![Netwerktoewijzing](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer **infra structuur voorbereiden** > **replicatie-instellingen** >  **+ maken en koppelen**.
2. Geef in **Beleid maken en koppelen** een beleidsnaam op. We gebruiken **ContosoReplicationPolicy**.
3. Wijzig de standaard instellingen en selecteer **OK**.
    - De **Kopieer frequentie** geeft aan dat er na de initiële replicatie Delta gegevens elke vijf minuten worden gerepliceerd.
    - **Bewaar periode van het herstel punt** geeft aan dat elk herstel punt twee uur wordt bewaard.
    - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
    - **Start tijd van de initiële replicatie** geeft aan dat de initiële replicatie onmiddellijk wordt gestart.
    - **Gegevens versleutelen die zijn opgeslagen in azure** , worden ingesteld op de standaard waarde (**uit**) en geeft aan dat de rest-gegevens in azure niet zijn versleuteld.
4. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u een nieuw beleid maakt, wordt dit automatisch gekoppeld aan de VMM-Cloud.

## <a name="enable-replication"></a>Replicatie inschakelen

1. Selecteer **bron**in **toepassing repliceren**.
2. Selecteer in **bron**de VMM-Cloud. Selecteer vervolgens **OK**.
3. Controleer in **doel**het doel (Azure), het kluis abonnement en selecteer het **Resource Manager** -model.
4. Selecteer het **contosovmsacct1910171607** -opslag account en het **ContosoASRnet** Azure-netwerk.
5. Selecteer in **virtuele machines** > **selecteren**de virtuele machine die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Nadat de taak **beveiliging** volt ooien is voltooid, is de initiële replicatie voltooid en is de virtuele machine klaar voor failover.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
