---
title: VMware VM-noodherstel instellen in Azure met Azure Site Recovery
description: Leer hoe u herstel na noodgevallen naar Azure instelt voor on-premises VMware-VM’s met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 37fdd42adf66ebcb11b357ece6ea63384630d9f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238865"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Herstel van on-premises VMware-VM’s naar Azure na een noodgeval instellen

In dit artikel wordt beschreven hoe u replicatie voor on-premises Vm's vMware inschakelt, voor noodherstel naar Azure met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)

Dit is de derde zelfstudie in een reeks die laat zien hoe u disaster recovery instellen op Azure voor on-premises Vm's vmware. In de vorige zelfstudie hebben we [de on-premises VMware-omgeving voorbereid](vmware-azure-tutorial-prepare-on-premises.md) op herstel na noodgevallen in Azure.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel de bronreplicatie-instellingen en een on-premises siteherstelconfiguratieserver in.
> * Stel de instellingen voor replicatiedoel in.
> * Een replicatiebeleid maken.
> * Replicatie inschakelen voor een Vm vmware.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Bekijk het artikel in de sectie Hoe naar de inhoudsopgave van de site herstel voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

Voltooi de vorige tutorials:
1. Zorg ervoor dat u Azure hebt [ingesteld](tutorial-prepare-azure.md) voor on-premises VMware-noodherstel naar Azure.
2. Volg [deze stappen](vmware-azure-tutorial-prepare-on-premises.md) om uw on-premises VMware-implementatie voor te bereiden op noodherstel naar Azure.
3. In deze zelfstudie laten we u zien hoe u één vm repliceren. Als u meerdere VMware-VM's implementeert, moet u het [Hulpprogramma implementatieplanner](https://aka.ms/asr-deployment-planner)gebruiken. [Meer informatie](site-recovery-deployment-planner.md) over dit hulpprogramma.
4. In deze zelfstudie worden een aantal opties gebruikt die u mogelijk anders wilt doen:
    - De zelfstudie maakt gebruik van een OVA-sjabloon om de configuratieserver VMware VM te maken. Als u dit om de een of andere reden niet doen, volgt u [deze instructies](physical-manage-configuration-server.md) om de configuratieserver handmatig in te stellen.
    - In deze zelfstudie downloadt en installeert Site Recovery MySQL automatisch naar de configuratieserver. Als u dat liever hebt, u het handmatig instellen. [Meer informatie](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

1. Selecteer de naam van de kluis in **Recovery Services-kluizen**. We gebruiken **ContosoVMVault** voor dit scenario.
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. Selecteer **On-premises**in **Beschermingsdoel** > **Waar uw machines zich bevinden.**
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **Zijn de machines gevirtualiseerd** selecteert **Ja, met VMware vSphere Hypervisor**. Selecteer vervolgens **OK**.



## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

In uw bronomgeving hebt u één, zeer beschikbare on-premises machine nodig om deze on-premises siterecoverycomponenten te hosten:

- **Configuratieserver:** de configuratieserver coördineert de communicatie tussen on-premises en Azure en beheert gegevensreplicatie.
- **Processerver:** de processerver fungeert als replicatiegateway. Het ontvangt replicatiegegevens; optimaliseert het met caching, compressie en versleuteling en stuurt het naar een cacheopslagaccount in Azure. De processerver installeert ook de Mobility Service-agent op VM's die u wilt repliceren en voert automatische detectie uit van on-premises VM's vMware.
- **Hoofddoelserver:** de hoofddoelserver verwerkt replicatiegegevens tijdens failback vanuit Azure.


Al deze componenten worden samen geïnstalleerd op de afzonderlijke on-premises machines die bekend staat als de *configuratieserver.* Standaard hebben we voor VMware disaster recovery de configuratieserver ingesteld als een zeer beschikbare VMware VM. Hiervoor downloadt u een geprepareerde OVA-sjabloon (Open Virtualisatie Application) en importeert u de sjabloon in VMware om de VM te maken. 

- De nieuwste versie van de configuratieserver is beschikbaar in de portal. U het ook rechtstreeks downloaden vanuit het [Microsoft Download Center.](https://aka.ms/asrconfigurationserver)
- Als u om de een of andere reden geen OVA-sjabloon gebruiken om een VM in te stellen, volgt u [deze instructies](physical-manage-configuration-server.md) om de configuratieserver handmatig in te stellen.
- De licentie met OVF-sjabloon is een evaluatielicentie die 180 dagen geldig is. Windows die op de VM worden uitgevoerd, moet worden geactiveerd met de vereiste licentie. 


### <a name="download-the-vm-template"></a>De VM-sjabloon downloaden

1. Ga in de kluis naar > **Infrastructuurbron** **voorbereiden.**
2. Selecteer **+Configuratieserver** in **Bron voorbereiden**.
3. Controleer in **Server toevoegen** of **Configuratieserver voor VMware** wordt weergegeven in **Servertype**.
4. Download de OVF-sjabloon voor de configuratieserver.



## <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren


1. Meld u aan bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMWare vSphere-client.
2. Selecteer **in** het menu Bestand de optie **OVF-sjabloon implementeren** om de **wizard OVF-sjabloon implementeren**te starten. 

     ![OVF-sjabloon](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Voer in **Bron selecteren** de locatie van de gedownloade OVF in.
4. Selecteer **Volgende** in **Beoordelingsdetails**.
5. Accepteer de standaardinstellingen in **Naam en map selecteren** en **Configuratie selecteren**.
6. Selecteer in **Opslag selecteren****Thick Provision Eager Zeroed** in **Indeling virtuele schijf selecteren**.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. Bij **Gereed om te voltooien** selecteert u **Inschakelen na de implementatie** > **Voltooien** om de virtuele machine in te stellen met de standaardinstellingen.

   > [!TIP]
   > Als u een extra NIC wilt toevoegen, u **De stroom na implementatie** > **voltooien wissen.** De sjabloon bevat standaard één NIC. Na de implementatie kunt u meer NIC’s toevoegen.

## <a name="add-an-additional-adapter"></a>Een extra adapter toevoegen

Als u een extra NIC aan de configuratieserver wilt toevoegen, voegt u deze toe voordat u de server in de kluis registreert. Het toevoegen van extra adapters wordt niet ondersteund na registratie.

1. Klik in de vSphere Client-inventaris met de rechtermuisknop op de VM en selecteer **Instellingen bewerken**.
2. Selecteer **Toevoegen** > **Ethernet-adapter** bij **Hardware**. Selecteer **vervolgens Volgende**.
3. Selecteer een adaptertype en een netwerk. 
4. Als u verbinding wilt maken met de virtuele NIC verbinding wanneer de VM wordt ingeschakeld, selecteert u **Verbinding maken bij inschakelen**. Selecteer **Volgende** > **einddatum**. Selecteer vervolgens **OK**.


## <a name="register-the-configuration-server"></a>De configuratieserver registreren 

Nadat de configuratieserver is ingesteld, registreert u deze in de kluis.

1. Schakel de VM in vanuit de VMWare vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Meld u nadat de installatie is voltooid bij de virtuele machine aan als de administrator.
4. De eerste keer dat u zich aanmeldt, wordt het configuratieprogramma van Azure Site Recovery binnen enkele seconden gestart.
5. Voer een naam in die wordt gebruikt voor het registreren van de configuratieserver bij Site Recovery. Selecteer **vervolgens Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement. De referenties moeten toegang hebben tot de kluis waarin u de configuratieserver wilt registreren. Zorg ervoor dat de benodigde [rollen](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) aan deze gebruiker worden toegewezen.
7. Het hulpprogramma voert enkele configuratietaken uit en start opnieuw op.
8. Meld u opnieuw aan bij de machine. Na enkele seconden wordt de wizard voor het beheer van de configuratieserver automatisch gestart.


### <a name="configure-settings-and-add-the-vmware-server"></a>Instellingen configureren en de VMware-server toevoegen

Voltooi het instellen en registreren van de configuratieserver. Voordat u verdergaat, moet u ervoor zorgen dat aan alle [vereisten](vmware-azure-deploy-configuration-server.md#prerequisites) wordt voldaan voor het succesvol instellen van de configuratieserver.


1. Selecteer in de wizard Configuratieserverbeheer de optie **Configuratie-connectiviteit**. Selecteer in de vervolgkeuzelijst eerst de NIC die de ingebouwde processerver gebruikt voor detectie en pushinstallatie van mobiliteitsservice op bronmachines en selecteer vervolgens de NIC die configuratieserver gebruikt voor connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U deze instelling niet wijzigen nadat deze is geconfigureerd.
2. Selecteer **in de kluis Select Recovery Services**uw Azure-abonnement en de relevante brongroep en kluis.
3. Accepteer de gebruiksrechtovereenkomst in **Software van derden installeren**. Selecteer **Downloaden en installeren** om MySQL Server te installeren. Als u MySQL in het pad hebt geplaatst, kan deze stap worden overgeslagen. Meer [weten?](vmware-azure-deploy-configuration-server.md#configure-settings)
4. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
5. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
6. Voer gebruikersreferenties in die door de configuratieserver moeten worden gebruikt voor verbinding met de VMware-server. Zorg ervoor dat de gebruikersnaam en het wachtwoord juist zijn en deel uitmaken van de groep Administrators van de virtuele machine die moet worden beveiligd. Site Recovery gebruikt deze referenties voor het automatisch detecteren van VMware-VM’s die beschikbaar zijn voor replicatie. Selecteer **Toevoegen** en vervolgens **Doorgaan**.
7. Voer in **Referenties voor virtuele machine configureren** de gebruikersnaam en het wachtwoord in die worden gebruikt om Mobility Service automatisch op virtuele machines te installeren wanneer replicatie wordt ingeschakeld.
    - Voor Windows-machines moet het account lokale administrator-machtigingen hebben op de machines die u wilt repliceren.
    - Geef voor Linux de details voor de superuser op.
8. Selecteer **Configuratie voltooien** om de registratie te voltooien.
9. Nadat de registratie is voltooid, opent u de Azure-portal en controleert u of de configuratieserver en vmware-server worden vermeld op de**configuratieservers**van **Recovery Services Vault** > **Manage** > **Site Recovery Infrastructure** > .


Nadat de configuratieserver is geregistreerd, maakt Site Recovery verbinding met VMware-servers met behulp van de opgegeven instellingen en detecteert u VM's.

> [!NOTE]
> Het duurt 15 minuten of langer voordat de accountnaam wordt weergegeven in de portal. Als u deze onmiddellijk wilt bijwerken, selecteert u De***servernaam*** > **Vernieuwserver van** **configuratieservers** > .

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Selecteer > **Infrastructuurdoel** **voorbereiden**. Selecteer het Azure-abonnement dat u wilt gebruiken. Er wordt gebruikgemaakt van een Resource Manager-model.
2. Site recovery controleert of u een of meer virtuele netwerken hebt. U zou deze moeten hebben als u de Azure-onderdelen in het [eerste deel](tutorial-prepare-azure.md) van deze reeks zelfstudies hebt ingesteld.

   ![Doeltabblad](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Open de [Azure Portal](https://portal.azure.com). Zoeken naar en selecteer **vaults van Recovery Services**.
2. Selecteer de Recovery Services-kluis (**ContosoVMVault** in deze zelfstudie).
3. Als u een replicatiebeleid wilt maken, selecteert u**Replicatiebeleid voor siteherstel-infrastructuur** >  **Site Recovery infrastructure** > **+Replicatiebeleid**.
4. Voer bij **Replicatiebeleid maken** de naam van het beleid in. Hiervoor wordt **VMwareRepPolicy** gebruikt.
5. Gebruik in **RPO-drempelwaarde** de standaardwaarde van 60 minuten. Deze waarde bepaalt hoe vaak herstelpunten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
6. Bij **Bewaarperiode van het herstelpunt** geeft u op hoe lang elk herstelpunt moet worden bewaard. Voor deze zelfstudie wordt 72 uur gebruikt. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster.
7. Bij **Frequentie van de app-consistente momentopname** geeft u op hoe vaak app-consistente momentopnamen moeten worden gemaakt. Gebruik de standaardwaarde van 60 minuten. Selecteer **OK** om het beleid te maken.

   ![Replicatiebeleid maken](./media/vmware-azure-tutorial/replication-policy.png)

- Het beleid wordt automatisch gekoppeld aan de configuratieserver.
- Standaard wordt automatisch een bijbehorend beleid voor failback gemaakt. Als het replicatiebeleid bijvoorbeeld**repbeleid** is, is het failbackbeleid **repbeleid-failback**. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

## <a name="enable-replication"></a>Replicatie inschakelen

Replicatie voor VM's als volgt inschakelen:

1. Selecteer **Bron van toepassingen** > repliceren **.**
2. Bij **Bron** selecteert u **On-premises** en selecteert u de configuratieserver bij **Bronlocatie**.
3. Selecteer **Virtuele machines** in **Type machine**.
4. Selecteer bij **vCenter/vSphere-hypervisor** de vSphere-host of de vCenter-server waarmee de host wordt beheerd.
5. Selecteer de processerver (standaard geïnstalleerd op de VM met de rol van configuratieserver). Selecteer vervolgens **OK**. De status van elke processerver wordt aangegeven volgens aanbevolen limieten en andere parameters. Kies een gezonde processerver. Een [kritieke](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver kan niet worden gekozen. U de fouten [oplossen en oplossen](vmware-physical-azure-troubleshoot-process-server.md) **of** een [scale-outprocesserver](vmware-azure-set-up-process-server-scale.md)instellen.
6. Selecteer in **Doel** het abonnement en de resourcegroep waarin u de failover-VM's wilt maken. Het implementatiemodel van Resource Manager wordt gebruikt. 
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle virtuele machines waarop u replicatie inschakelt. Selecteer **Later configureren** om het Azure-netwerk per machine te selecteren.
9. Selecteer **in virtuele machines** > **virtuele machines**elke machine die u wilt repliceren. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Als u een bepaalde virtuele machine niet bekijken/selecteren, [leest u meer](https://aka.ms/doc-plugin-VM-not-showing) over het oplossen van het probleem.
10. Selecteer in **Eigenschappen** > **Eigenschappen configureren**het account dat door de processerver moet worden gebruikt om mobiliteitsservice automatisch op de machine te installeren.
11. Controleer in **Replicatieinstellingen** > **Replicatie-instellingen**configureren of het juiste replicatiebeleid is geselecteerd.
12. Selecteer **Replicatie inschakelen**. Site Recovery installeert de Mobility-service wanneer replicatie wordt ingeschakeld voor een VM.
13. U de voortgang van de taak **Beveiliging inschakelen** bijhouden in **vacatures** > voor**hersteltaken****van vacatures** > . Nadat de **taak Beveiliging voltooien** is uitgevoerd en een herstelpuntgeneratie is voltooid, is de machine klaar voor failover.
14. Het kan 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en in de portal worden weergegeven.
15. Als u VM's wilt controleren die u toevoegt, controleert u de laatst ontdekte tijd voor VM's in **Configuration Servers** > **Last Contact At**. Als u VM's wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratieserver (zonder deze te selecteren), en selecteert u **Vernieuwen**.

## <a name="next-steps"></a>Volgende stappen
Nadat u replicatie hebt ingemaakt, voert u een drill uit om te zien of alles werkt zoals verwacht.
> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](site-recovery-test-failover-to-azure.md)
