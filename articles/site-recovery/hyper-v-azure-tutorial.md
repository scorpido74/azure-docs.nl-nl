---
title: Herstel na nood gevallen voor Hyper-V instellen met behulp van Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen van on-premises virtuele Hyper-V-machines (zonder VMM) naar Azure met behulp van Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9a2bb636ba749cae50195cefeb8e7237d382fb99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82182341"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Herstel na noodgevallen instellen voor on-premises Hyper-V-VM's naar Azure

De [Azure site Recovery](site-recovery-overview.md) -service draagt bij aan uw strategie voor herstel na nood gevallen door replicatie, failover en failback van on-premises machines en virtuele Azure-machines (vm's) te beheren en te organiseren.

Dit is de derde zelfstudie in een reeks. U ziet hoe u herstel na nood gevallen instelt voor on-premises virtuele Hyper-V-machines naar Azure. Deze zelf studie is van toepassing op Hyper-V-Vm's die niet worden beheerd door micro soft System Center Virtual Machine Manager (VMM).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * Stel de bron replicatie omgeving in, met inbegrip van on-premises Site Recovery onderdelen en de doel replicatie omgeving.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de artikelen in de sectie **instructies** van de site Recovery- [documentatie](https://docs.microsoft.com/azure/site-recovery)voor gedetailleerde instructies.



## <a name="before-you-begin"></a>Voordat u begint

Dit is de derde zelfstudie in een reeks. Hierbij wordt ervan uitgegaan dat u de taken in de vorige zelf studies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga in het Azure Portal naar **Recovery Services kluizen** en selecteer de kluis. We hebben de kluis **ContosoVMVault** in de vorige zelf studie voor bereid.
2. Selecteer in **aan**de slag **site Recovery**en selecteer vervolgens **infra structuur voorbereiden**.
3. Selecteer **on-premises**in het **beveiligings doel** > **waar bevinden zich uw machines?**
4. In **waar wilt u uw computers repliceren?** Selecteer **naar Azure**.
5. In **worden uw machines gevirtualiseerd?**, selecteert u **Ja, met Hyper-V**.
6. In **gebruikt u System Center VMM om uw Hyper-V-hosts te beheren?**, selecteert u **Nee**.
7. Selecteer **OK**.

    ![Replicatiedoel](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Bij het plannen van de **implementatie**kunt u, als u een grote implementatie plant, de Deployment planner voor Hyper-V downloaden via de koppeling op de pagina. Meer [informatie](hyper-v-deployment-planner-overview.md) over de planning van de implementatie van Hyper-V.
2. Voor deze zelf studie hebben we de Deployment Planner niet nodig. In **hebt u de implementatie planning voltooid?** Selecteer **Ik ga dan later**en selecteer **OK**.

    ![Implementatieplanning](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Als u de bron omgeving wilt instellen, maakt u een Hyper-V-site en voegt u de Hyper-V-hosts met de virtuele machines die u wilt repliceren toe aan die site. Vervolgens downloadt en installeert u de Azure Site Recovery provider en de Azure Recovery Services-agent op elke host en registreert u de Hyper-V-site in de kluis.

1. Onder **infra structuur voorbereiden**selecteert u **bron**.
2. Selecteer in **bron voorbereiden**de optie **+ Hyper-V-site**.
3. Geef bij **Hyper-V-site maken**de naam van de site op. We gebruiken **ContosoHyperVSite**.

    ![Hyper-V-site](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Nadat de site is gemaakt, klikt u in stap 1 **voorbereiden** > op de**Hyper-V-site**en selecteert u de site die u hebt gemaakt.
5. Selecteer **+ Hyper-V-Server**.

    ![Hyper-V-server](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Down load het installatie programma voor de provider van de Microsoft Azure-Site Recovery.
7. Download de registratiesleutel voor de kluis. U hebt deze sleutel nodig om de provider te installeren. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Provider en registratie sleutel downloaden](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Provider installeren

Installeer het gedownloade installatie bestand (AzureSiteRecoveryProvider. exe) op elke Hyper-V-host die u wilt toevoegen aan de Hyper-V-site. Setup installeert de Azure Site Recovery provider en Recovery Services agent op elke Hyper-V-host.

1. Voer het installatie bestand uit.
2. Ga in de installatiewizard van Azure Site Recovery Provider naar **Microsoft Update**, en kies ervoor om Microsoft Update te gebruiken om te controleren op Provider-updates.
3. Accepteer in de **installatie**de standaard installatie locatie voor de provider en de agent en selecteer **installeren**.
4. Ga na de installatie naar de wizard Microsoft Azure Site Recovery registratie > **kluis instellingen**, selecteer **Bladeren**en selecteer in **sleutel bestand**het kluis sleutel bestand dat u hebt gedownload.
5. Geef het Azure Site Recovery-abonnement, de kluisnaam (**ContosoVMVault**) en de Hyper-V-site (**ContosoHyperVSite**) op waartoe de Hyper-V-server behoort.
6. Selecteer in **Proxy-instellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**.
7. Selecteer in **registratie**, nadat de server is geregistreerd in de kluis, de optie **volt ooien**.

Meta gegevens van de Hyper-V-Server worden opgehaald door Azure site Recovery en de server wordt weer gegeven in **site Recovery-infra structuur** > **hyper-v-hosts**. Dit proces duurt maximaal 30 minuten.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>De provider op een Hyper-V-kern server installeren

Als u een Hyper-V-kern server gebruikt, downloadt u het installatie bestand en voert u de volgende stappen uit:

1. Pak de bestanden uit AzureSiteRecoveryProvider. exe uit naar een lokale map door deze opdracht uit te voeren:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Voer `.\setupdr.exe /i` uit. Resultaten worden vastgelegd in%Programdata%\ASRLogs\DRASetupWizard.log.

3. Registreer de server door de volgende opdracht uit te voeren:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Doel resources selecteren en verifiëren:

1. Selecteer **infra structuur** > voorbereiden**doel**.
2. Selecteer het abonnement en de resource groep **ContosoRG** waarin de virtuele Azure-machines na een failover worden gemaakt.
3. Selecteer het **Resource Manager**-implementatiemodel.

Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer > **replicatie-instellingen** > voor **infra structuur voorbereiden****+ maken en koppelen**.
2. Geef in **beleid maken en koppelen**een beleids naam op. We gebruiken **ContosoReplicationPolicy**.
3. Voor deze zelf studie houden we de standaard instellingen in:
    - De **Kopieer frequentie** geeft aan hoe vaak de Delta gegevens (na de initiële replicatie) worden gerepliceerd. De standaard frequentie is om de vijf minuten.
    - **Bewaar periode van het herstel punt** geeft aan dat herstel punten twee uur worden bewaard. De Maxi maal toegestane waarde voor retentie bij het beveiligen van virtuele machines die worden gehost op Hyper-V-hosts is 24 uur.
    - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
    - **Start tijd van de initiële replicatie** geeft aan dat de initiële replicatie onmiddellijk wordt gestart.
4. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u een nieuw beleid maakt, wordt dit automatisch gekoppeld aan de opgegeven Hyper-V-site. In onze zelf studie is dat **ContosoHyperVSite**.

    ![Beleid voor replicatie](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Replicatie inschakelen

1. Selecteer **bron**in **toepassing repliceren**.
2. Selecteer in **Bron** de site **ContosoHyperVSite**. Selecteer vervolgens **OK**.
3. Controleer in **doel**het doel (Azure), het kluis abonnement en het **Resource Manager** -implementatie model.
4. Als u zelfstudie instellingen gebruikt, selecteert u het **contosovmsacct1910171607** -opslag account dat u in de vorige zelf studie hebt gemaakt voor gerepliceerde gegevens. Selecteer ook het **ContosoASRnet** -netwerk waarin Azure-vm's na een failover worden geplaatst.
5. Selecteer in **virtuele machines** > **selecteren**de virtuele machine die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Nadat de taak **beveiliging** volt ooien is voltooid, is de initiële replicatie voltooid en is de virtuele machine klaar voor failover.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
