---
title: Een nood herstel voor de VMware-VM instellen op Azure met Azure Site Recovery
description: Leer hoe u herstel na noodgevallen naar Azure instelt voor on-premises VMware-VM’s met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 37fdd42adf66ebcb11b357ece6ea63384630d9f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458932"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Herstel van on-premises VMware-VM’s naar Azure na een noodgeval instellen

In dit artikel wordt beschreven hoe u replicatie inschakelt voor on-premises VMware-Vm's, voor herstel na nood gevallen naar Azure met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.

Dit is de derde zelf studie in een serie die laat zien hoe u herstel na nood gevallen voor Azure instelt voor on-premises VMware-Vm's. In de vorige zelf studie hebben we [de on-premises VMware-omgeving voor bereid](vmware-azure-tutorial-prepare-on-premises.md) op herstel na nood geval naar Azure.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel de bron replicatie-instellingen en een on-premises Site Recovery configuratie server in.
> * De instellingen voor de replicatie doel instellen.
> * Een replicatiebeleid maken.
> * Schakel replicatie in voor een virtuele VMware-machine.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg het artikel in het gedeelte How to van de inhouds opgave van Site Recovery voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

Voltooi de vorige zelf studies:
1. Zorg ervoor dat u [Azure hebt ingesteld](tutorial-prepare-azure.md) voor on-premises VMware-herstel na nood gevallen naar Azure.
2. Volg [deze stappen](vmware-azure-tutorial-prepare-on-premises.md) om uw on-premises VMware-implementatie voor te bereiden op herstel na nood geval naar Azure.
3. In deze zelf studie wordt uitgelegd hoe u één virtuele machine repliceert. Als u meerdere virtuele VMware-machines implementeert, moet u het [hulp programma Deployment planner](https://aka.ms/asr-deployment-planner)gebruiken. [Meer informatie](site-recovery-deployment-planner.md) over dit hulpprogramma.
4. In deze zelf studie wordt een aantal opties gebruikt die u mogelijk anders wilt doen:
    - In de zelf studie wordt een eicellen-sjabloon gebruikt voor het maken van de configuratie server VMware VM. Als u dit niet om een bepaalde reden kunt doen, volgt u [deze instructies](physical-manage-configuration-server.md) om de configuratie server hand matig in te stellen.
    - In deze zelf studie Site Recovery de MySQL automatisch gedownload en geïnstalleerd op de configuratie server. Als u wilt, kunt u dit in plaats daarvan hand matig instellen. [Meer informatie](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

1. Selecteer de naam van de kluis in **Recovery Services-kluizen**. We gebruiken **ContosoVMVault** voor dit scenario.
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. In **Beveiligingsdoel** > **Waar bevinden de machines zich**, selecteert u **On-premises**.
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **Zijn de machines gevirtualiseerd** selecteert **Ja, met VMware vSphere Hypervisor**. Selecteer vervolgens **OK**.



## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

In uw bron omgeving hebt u één Maxi maal beschik bare on-premises computer nodig om deze on-premises Site Recovery onderdelen te hosten:

- **Configuratie server**: de configuratie server coördineert de communicatie tussen on-premises en Azure, en beheert de gegevens replicatie.
- **Proces server**: de proces server fungeert als replicatie gateway. Het ontvangt replicatie gegevens; optimaliseert het met caching, compressie en versleuteling, en verzendt het naar een cache-opslag account in Azure. De proces server installeert ook de Mobility Service-agent op Vm's die u wilt repliceren en voert automatische detectie van on-premises virtuele VMware-machines uit.
- **Hoofddoel server**: de hoofddoel server verwerkt de replicatie gegevens tijdens de failback van Azure.


Al deze onderdelen worden samen geïnstalleerd op de afzonderlijke on-premises machines die de *Configuratie server*worden genoemd. Voor VMware-nood herstel stellen we standaard de configuratie server in als een Maxi maal beschik bare virtuele VMware-machine. Hiervoor downloadt u een voor bereide open Virtualization Application (eicellen) sjabloon en importeert u de sjabloon in VMware om de VM te maken. 

- De meest recente versie van de configuratie server is beschikbaar in de portal. U kunt deze ook rechtstreeks downloaden vanuit het [micro soft Download centrum](https://aka.ms/asrconfigurationserver).
- Als u om een of andere reden geen eicellen-sjabloon kunt gebruiken om een virtuele machine in te stellen, volgt u [deze instructies](physical-manage-configuration-server.md) om de configuratie server hand matig in te stellen.
- De licentie die is opgenomen in OVF-sjabloon is een evaluatie licentie die gedurende 180 dagen geldig is. Windows die op de virtuele machine wordt uitgevoerd, moet worden geactiveerd met de vereiste licentie. 


### <a name="download-the-vm-template"></a>De VM-sjabloon downloaden

1. Ga in de kluis naar **Infrastructuur voorbereiden** > **Bron**.
2. Selecteer **+Configuratieserver** in **Bron voorbereiden**.
3. Controleer in **Server toevoegen** of **Configuratieserver voor VMware** wordt weergegeven in **Servertype**.
4. Download de OVF-sjabloon voor de configuratieserver.



## <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren


1. Meld u aan bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMWare vSphere-client.
2. Selecteer in het menu **Bestand** de optie **OVF-sjabloon implementeren** om de **wizard voor het implementeren van OVF-sjablonen** te starten. 

     ![OVF-sjabloon](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Voer in **Bron selecteren** de locatie van de gedownloade OVF in.
4. Selecteer **Volgende** in **Beoordelingsdetails**.
5. Accepteer de standaardinstellingen in **Naam en map selecteren** en **Configuratie selecteren**.
6. Selecteer in **Opslag selecteren** **Thick Provision Eager Zeroed** in **Indeling virtuele schijf selecteren**.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. Bij **Gereed om te voltooien** selecteert u **Inschakelen na de implementatie** > **Voltooien** om de virtuele machine in te stellen met de standaardinstellingen.

   > [!TIP]
   > Als u een extra NIC wilt toevoegen, wist u **Inschakelen na de implementatie** > **Voltooien**. De sjabloon bevat standaard één NIC. Na de implementatie kunt u meer NIC’s toevoegen.

## <a name="add-an-additional-adapter"></a>Een extra adapter toevoegen

Als u een extra NIC aan de configuratie server wilt toevoegen, voegt u deze toe voordat u de server in de kluis registreert. Het toevoegen van extra adapters wordt niet ondersteund na registratie.

1. Klik in de vSphere Client-inventaris met de rechtermuisknop op de VM en selecteer **Instellingen bewerken**.
2. Selecteer **Toevoegen** > **Ethernet-adapter** bij **Hardware**. Selecteer vervolgens **Volgende**.
3. Selecteer een adaptertype en een netwerk. 
4. Als u verbinding wilt maken met de virtuele NIC verbinding wanneer de VM wordt ingeschakeld, selecteert u **Verbinding maken bij inschakelen**. Selecteer **Volgende** > **voltooien**. Selecteer vervolgens **OK**.


## <a name="register-the-configuration-server"></a>De configuratieserver registreren 

Nadat de configuratie server is ingesteld, registreert u deze in de kluis.

1. Schakel de VM in vanuit de VMWare vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Meld u nadat de installatie is voltooid bij de virtuele machine aan als de administrator.
4. De eerste keer dat u zich aanmeldt, wordt het configuratieprogramma van Azure Site Recovery binnen enkele seconden gestart.
5. Voer een naam in die wordt gebruikt voor het registreren van de configuratieserver bij Site Recovery. Selecteer vervolgens **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement. De referenties moeten toegang hebben tot de kluis waarin u de configuratieserver wilt registreren. Zorg ervoor dat de benodigde [rollen](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) aan deze gebruiker zijn toegewezen.
7. Het hulpprogramma voert enkele configuratietaken uit en start opnieuw op.
8. Meld u opnieuw aan bij de machine. Na enkele seconden wordt de wizard voor het beheer van de configuratieserver automatisch gestart.


### <a name="configure-settings-and-add-the-vmware-server"></a>Instellingen configureren en de VMware-server toevoegen

Instellen en registreren van de configuratie server volt ooien. Voordat u verder gaat, moet u controleren of aan alle vereisten wordt voldaan voor [het](vmware-azure-deploy-configuration-server.md#prerequisites) instellen van de configuratie server.


1. Selecteer in de wizard Configuratie Server beheer de optie **connectiviteit instellen**. Selecteer in de vervolg keuzelijsten eerst de NIC die de ingebouwde proces server gebruikt voor detectie en push-installatie van Mobility service op bron machines, en selecteer vervolgens de NIC die door de configuratie server wordt gebruikt voor connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U kunt deze instelling niet wijzigen nadat deze is geconfigureerd.
2. Selecteer in **Selecteer Recovery Services-kluis** uw Azure-abonnement en de relevante resourcegroep en kluis.
3. Accepteer de gebruiksrechtovereenkomst in **Software van derden installeren**. Selecteer **Downloaden en installeren** om MySQL Server te installeren. Als u MySQL in het pad hebt geplaatst, kan deze stap worden overgeslagen. [Meer informatie](vmware-azure-deploy-configuration-server.md#configure-settings)
4. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
5. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
6. Voer gebruikersreferenties in die door de configuratieserver moeten worden gebruikt voor verbinding met de VMware-server. Zorg ervoor dat de gebruikersnaam en het wachtwoord juist zijn en deel uitmaken van de groep Administrators van de virtuele machine die moet worden beveiligd. Site Recovery gebruikt deze referenties voor het automatisch detecteren van VMware-VM’s die beschikbaar zijn voor replicatie. Selecteer **Toevoegen** en vervolgens **Doorgaan**.
7. Voer in **Referenties voor virtuele machine configureren** de gebruikersnaam en het wachtwoord in die worden gebruikt om Mobility Service automatisch op virtuele machines te installeren wanneer replicatie wordt ingeschakeld.
    - Voor Windows-machines moet het account lokale administrator-machtigingen hebben op de machines die u wilt repliceren.
    - Geef voor Linux de details voor de superuser op.
8. Selecteer **Configuratie voltooien** om de registratie te voltooien.
9. Nadat de registratie is voltooid, opent u de Azure Portal en controleert u of de configuratie server en de VMware-Server worden weer gegeven op **Recovery Services kluis** >  > **site Recovery infrastructuur** > **configuratie servers** **beheren** .


Nadat de configuratie server is geregistreerd, maakt Site Recovery verbinding met VMware-servers met behulp van de opgegeven instellingen en detecteert Vm's.

> [!NOTE]
> Het duurt 15 minuten of langer voordat de accountnaam wordt weergegeven in de portal. Selecteer **Configuratieservers** > ***servernaam*** > **Server vernieuwen** om direct bij te werken.

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Selecteer **Infrastructuur voorbereiden** > **Doel**. Selecteer het Azure-abonnement dat u wilt gebruiken. Er wordt gebruikgemaakt van een Resource Manager-model.
2. Site Recovery controleert of u een of meer virtuele netwerken hebt. U zou deze moeten hebben als u de Azure-onderdelen in het [eerste deel](tutorial-prepare-azure.md) van deze reeks zelfstudies hebt ingesteld.

   ![Doeltabblad](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Open de [Azure Portal](https://portal.azure.com). Zoek en selecteer **Recovery Services kluizen**.
2. Selecteer de Recovery Services-kluis (**ContosoVMVault** in deze zelfstudie).
3. Selecteer **Infrastructuur voor Site Recovery** > **Herstelbeleid** >  **+Herstelbeleid** om een replicatiebeleid te maken.
4. Voer bij **Replicatiebeleid maken** de naam van het beleid in. Hiervoor wordt **VMwareRepPolicy** gebruikt.
5. Gebruik in **RPO-drempelwaarde** de standaardwaarde van 60 minuten. Deze waarde bepaalt hoe vaak herstelpunten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
6. Bij **Bewaarperiode van het herstelpunt** geeft u op hoe lang elk herstelpunt moet worden bewaard. Voor deze zelfstudie wordt 72 uur gebruikt. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster.
7. Bij **Frequentie van de app-consistente momentopname** geeft u op hoe vaak app-consistente momentopnamen moeten worden gemaakt. Gebruik de standaardwaarde van 60 minuten. Selecteer **OK** om het beleid te maken.

   ![Replicatiebeleid maken](./media/vmware-azure-tutorial/replication-policy.png)

- Het beleid wordt automatisch gekoppeld aan de configuratieserver.
- Standaard wordt automatisch een bijbehorend beleid voor failback gemaakt. Als het replicatiebeleid bijvoorbeeld**repbeleid** is, is het failbackbeleid **repbeleid-failback**. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie voor virtuele machines als volgt in:

1. Selecteer **Toepassing repliceren** > **Bron**.
2. Bij **Bron** selecteert u **On-premises** en selecteert u de configuratieserver bij **Bronlocatie**.
3. Selecteer **Virtuele machines** in **Type machine**.
4. Selecteer bij **vCenter/vSphere-hypervisor** de vSphere-host of de vCenter-server waarmee de host wordt beheerd.
5. Selecteer de processerver (standaard geïnstalleerd op de VM met de rol van configuratieserver). Selecteer vervolgens **OK**. De integriteits status van elke proces server wordt aangegeven volgens de aanbevolen limieten en andere para meters. Kies een goede proces server. Er kan geen [essentiële](vmware-physical-azure-monitor-process-server.md#process-server-alerts) proces server worden gekozen. U kunt [problemen oplossen en](vmware-physical-azure-troubleshoot-process-server.md) de fouten oplossen **of** een [scale-out proces server](vmware-azure-set-up-process-server-scale.md)instellen.
6. Selecteer in **Doel** het abonnement en de resourcegroep waarin u de failover-VM's wilt maken. Het implementatiemodel van Resource Manager wordt gebruikt. 
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle virtuele machines waarop u replicatie inschakelt. Selecteer **Later configureren** om per machine een Azure-netwerk te selecteren.
9. Selecteer in **Virtuele machines** > **Virtuele machines selecteren** alle machines die u wilt repliceren. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Als u een bepaalde virtuele machine niet kunt weer geven/selecteren, [leest u meer](https://aka.ms/doc-plugin-VM-not-showing) over het oplossen van het probleem.
10. Selecteer in **Eigenschappen** > **Eigenschappen configureren** het account dat door de processerver moet worden gebruikt om automatisch Mobility Service op de computer te installeren.
11. Controleer of het juiste replicatiebeleid is geselecteerd in **Replicatie-instellingen** > **Replicatie-instellingen configureren**.
12. Selecteer **Replicatie inschakelen**. Site Recovery installeert de Mobility-service wanneer replicatie wordt ingeschakeld voor een VM.
13. U kunt de voortgang van de taak **Beveiliging inschakelen** volgen via **Instellingen** > **Taken** > **Site Recovery-taken**. Nadat de taak **beveiliging volt ooien** is uitgevoerd en er een herstel punt is gegenereerd, is de machine klaar voor failover.
14. Het kan 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en in de portal worden weergegeven.
15. Voor het bewaken van virtuele machines die u toevoegt, controleert u de laatste detectietijd voor VM's in **Configuratieservers** > **Laatst contact om**. Als u VM's wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratieserver (zonder deze te selecteren), en selecteert u **Vernieuwen**.

## <a name="next-steps"></a>Volgende stappen
Nadat de replicatie is ingeschakeld, voert u een analyse uit om te controleren of alles werkt zoals verwacht.
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](site-recovery-test-failover-to-azure.md)
