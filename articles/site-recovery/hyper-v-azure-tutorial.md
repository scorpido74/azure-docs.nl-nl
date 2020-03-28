---
title: Hyper-V-herstel na noodgevallen instellen met Azure Site Recovery
description: Meer informatie over het instellen van noodherstel van on-premises Hyper-V VM's (zonder VMM) op Azure met behulp van Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239845"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Herstel na noodgevallen instellen voor on-premises Hyper-V-VM's naar Azure

De [Azure Site Recovery-service](site-recovery-overview.md) draagt bij aan uw strategie voor noodherstel door replicatie, failover en failback van on-premises machines en Virtuele Azure-machines (VM's) te beheren en te orkestreren.

Dit is de derde zelfstudie in een reeks. Het laat u zien hoe u noodherstel van on-premises Hyper-V VM's in Stelt in Azure. Deze zelfstudie past Hyper-V VM's toe die niet worden beheerd door Microsoft System Center Virtual Machine Manager (VMM).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De replicatiebron en het replicatiedoel selecteren.
> * Stel de bronreplicatieomgeving in, inclusief on-premises siteherstelcomponenten en de doelreplicatieomgeving.
> * Een replicatiebeleid maken.
> * Replicatie voor een VM inschakelen.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Bekijk de artikelen in de sectie **Handleidingen** van de [documentatie siteherstel](https://docs.microsoft.com/azure/site-recovery)voor gedetailleerde instructies.



## <a name="before-you-begin"></a>Voordat u begint

Dit is de derde zelfstudie in een reeks. Het gaat ervan uit dat u de taken in de vorige zelfstudies al hebt voltooid:

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. Ga in de Azure-portal naar **Vaults van Recovery Services** en selecteer de kluis. We bereidde de kluis **ContosoVMVault** in de vorige tutorial.
2. Selecteer **siteherstel**in **Aan de slag**en selecteer Infrastructuur **voorbereiden**.
3. In **Beschermingsdoel** > Waar bevinden uw **On-premises****machines zich?**
4. Selecteer In Waar wilt u uw **To Azure** **machines repliceren?**
5. Selecteer **In** **Zijn uw machines gevirtualiseerd?**
6. Selecteer **In Bent u System Center VMM gebruikt om uw Hyper-V-hosts te beheren?** **No**
7. Selecteer **OK**.

    ![Replicatiedoel](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

1. Als **u in Implementatieplanning**een grote implementatie plant, downloadt u de implementatieplanner voor Hyper-V via de koppeling op de pagina. [Meer informatie](hyper-v-deployment-planner-overview.md) over de implementatieplanning van Hyper V.
2. Voor deze zelfstudie hebben we de Implementatieplanner niet nodig. Selecteer In Heb je de **I will do it later** **implementatieplanning voltooid?** **OK**

    ![Implementatieplanning](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Als u de bronomgeving wilt instellen, maakt u een Hyper-V-site en voegt u aan die site de Hyper-V-hosts toe die VM's bevatten die u wilt repliceren. Vervolgens downloadt en installeert u de Azure Site Recovery Provider en de Azure Recovery Services-agent op elke host en registreert u de Hyper-V-site in de kluis.

1. Selecteer **Onder Infrastructuur voorbereiden**de optie **Bron**.
2. Selecteer in **Bron voorbereiden**de optie **+ Hyper-V-site**.
3. Geef **in Hyper-V-site maken**de sitenaam op. We gebruiken **ContosoHyperVSite.**

    ![Hyper-V-site](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Nadat de site is gemaakt, selecteert u in Groep 1 **voorbereiden:** > **Hyper-V-site selecteren,** de site die u hebt gemaakt.
5. Selecteer **+ Hyper-V Server**.

    ![Hyper-V-server](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Download het installatieprogramma voor de Microsoft Azure Site Recovery Provider.
7. Download de registratiesleutel voor de kluis. Je hebt deze sleutel nodig om de Provider te installeren. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Provider downloaden en registratiesleutel](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Provider installeren

Installeer het gedownloade installatiebestand (AzureSiteRecoveryProvider.exe) op elke Hyper-V-host die u aan de Hyper-V-site wilt toevoegen. Setup installeert de Azure Site Recovery Provider en Recovery Services-agent op elke Hyper-V-host.

1. Voer het installatiebestand uit.
2. Ga in de installatiewizard van Azure Site Recovery Provider naar **Microsoft Update**, en kies ervoor om Microsoft Update te gebruiken om te controleren op Provider-updates.
3. Accepteer in **Installatie**de standaardinstallatielocatie voor de provider en agent en selecteer **Installeren**.
4. Selecteer na de installatie in de wizard Microsoft Azure Site Recovery Registration > **Vault Settings**, selecteer **Bladeren**en in **Sleutelbestand**selecteert u het kluissleutelbestand dat u hebt gedownload.
5. Geef het Azure Site Recovery-abonnement, de kluisnaam (**ContosoVMVault**) en de Hyper-V-site (**ContosoHyperVSite**) op waartoe de Hyper-V-server behoort.
6. Selecteer in **Proxy-instellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**.
7. Selecteer **Registration** **Voltooien**nadat de server is geregistreerd in de kluis , nadat de server is geregistreerd in de kluis .

Metagegevens van de Hyper-V-server worden opgehaald door Azure Site Recovery en de server wordt weergegeven in**Hyper-V-hosts van** **siteherstelinfrastructuur.** >  Dit proces duurt maximaal 30 minuten.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>De provider installeren op een Hyper-V-kernserver

Als u een Hyper-V-kernserver uitvoert, downloadt u het installatiebestand en volgt u de volgende stappen:

1. Haal de bestanden uit AzureSiteRecoveryProvider.exe naar een lokale map door deze opdracht uit te voeren:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Voer `.\setupdr.exe /i` uit. Resultaten worden geregistreerd op %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registreer de server door deze opdracht uit te voeren:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en verifieer doelbronnen:

1. Selecteer > **Infrastructuurdoel** **voorbereiden**.
2. Selecteer het abonnement en de brongroep **ContosoRG** waarin de Azure VM's worden gemaakt na failover.
3. Selecteer het **Resource Manager**-implementatiemodel.

Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

1. Selecteer**Replicatieinstellingen** >  **voor infrastructuur** > voorbereiden **+Maken en koppelen**.
2. Geef **in Beleid maken en koppelen**een beleidsnaam op. We gebruiken **ContosoReplicationPolicy**.
3. Voor deze zelfstudie laten we de standaardinstellingen achter:
    - **Kopieerfrequentie** geeft aan hoe vaak deltagegevens (na de eerste replicatie) worden gerepliceerd. De standaardfrequentie is elke vijf minuten.
    - **Herstelpunt retentie** geeft aan dat herstelpunten gedurende twee uur worden bewaard.
    - **Frequentie van de app-consistente momentopname** geeft aan dat er elk uur herstelpunten met app-consistente momentopnamen worden gemaakt.
    - **De begintijd van de eerste replicatie** geeft aan dat de eerste replicatie onmiddellijk van start gaat.
4. Nadat het beleid is gemaakt, selecteert u **OK**. Wanneer u een nieuw beleid maakt, wordt dit automatisch gekoppeld aan de opgegeven Hyper-V-site. In onze tutorial, dat is **ContosoHyperVSite**.

    ![Beleid voor replicatie](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Replicatie inschakelen

1. Selecteer **Bron**in **De toepassing Repliceren**.
2. Selecteer in **Bron** de site **ContosoHyperVSite**. Selecteer vervolgens **OK**.
3. Controleer **in Target**het doel (Azure), het kluisabonnement en het **implementatiemodel Van Resource Manager.**
4. Als u zelfstudie-instellingen gebruikt, selecteert u het opslagaccount **contosovmsacct1910171607** dat in de vorige zelfstudie is gemaakt voor gerepliceerde gegevens. Selecteer ook het **ContosoASRnet-netwerk,** waarin Azure VM's zich na een failover bevinden.
5. Selecteer in **Virtuele machines** > **Selecteer**de VM die u wilt repliceren. Selecteer vervolgens **OK**.

   U kunt de voortgang van de actie **Beveiliging inschakelen** volgen via **Taken** > **Site Recovery-taken**. Nadat de taak **Beveiliging afronden** is voltooid, is de eerste replicatie voltooid en is de VM klaar voor failover.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)
