---
title: VMware VM's migreren met Azure-migratie van Azure-migreren
description: Meer informatie over het uitvoeren van een op agent gebaseerde migratie van VMware VM's met Azure Migrate.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 64873c5185660c58cd4d07d60df3d086364d6288
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238438"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware VM's migreren naar Azure (op basis van agent)

In dit artikel ziet u hoe u on-premises Vm's vMware naar Azure migreert, met behulp van op agentgebaseerde migratie met het hulpprogramma Azure Migrate Server Migration.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en AWS/GCP VM-exemplaren naar Azure bij te houden. De hub biedt Azure Migrate-tools voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden.


In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel de bronomgeving in en implementeer een Azure Migrate-replicatietoestel voor op agentgebaseerde migratie.
> * Stel de doelomgeving voor migratie in.
> * Stel een replicatiebeleid in.
> * Schakel replicatie in.
> * Voer een testmigratie uit om te zien of alles werkt zoals verwacht.
> * Voer een volledige migratie naar Azure uit.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk voor gedetailleerde instructies de how-tos voor VMware-beoordeling en migratie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

We raden u aan vmware VM-beoordeling uit te proberen met Azure Migrate Server Assessment, voordat u VM's migreert naar Azure. Stel als volgt een beoordeling in:

1. Volg de zelfstudie om [Azure en VMware](tutorial-prepare-vmware.md) voor te bereiden op beoordeling.
2. Volg vervolgens [deze zelfstudie](tutorial-assess-vmware.md) om een Azure Migrate-toestel in te stellen voor beoordeling en vm's te ontdekken en te beoordelen.


Hoewel we u aanraden een beoordeling uit te proberen, hoeft u geen beoordeling uit te voeren voordat u VM's migreert.

## <a name="migration-methods"></a>Migratiemethoden

U VMware VM's migreren naar Azure met het hulpprogramma Azure Migrate Server Migration. Deze tool biedt een aantal opties voor VMware VM-migratie:

- Agentloze replicatie. Migreer VM's zonder er iets op te hoeven installeren.
- Op agenten gebaseerde migratie. of replicatie. Installeer een agent (de medewerker mobiliteitsservices) op de VM voor replicatie.

Als u wilt beslissen of u agentloze of op agentgebaseerde migratie wilt gebruiken, raadpleegt u deze artikelen:

- [Meer informatie over](server-migrate-overview.md) de VMware-migratieopties.
- [Migratiemethoden vergelijken](server-migrate-overview.md#compare-migration-methods).
- [Volg dit artikel](tutorial-migrate-vmware.md) om agentloze migratie uit te proberen.



## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Bekijk](migrate-architecture.md) de VMware-migratiearchitectuur.
2. Zorg ervoor dat aan uw Azure-account de rol Virtuele machineinzender is toegewezen, zodat u machtigingen hebt voor:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijf naar een door Azure beheerde schijf. 

3. [Een Azure-netwerk instellen](../virtual-network/manage-virtual-network.md#create-a-virtual-network). On-premises machines worden gerepliceerd naar door Azure beheerde schijven. Wanneer u niet overgaat naar Azure voor migratie, worden Azure VM's gemaakt van deze beheerde schijven en verbonden aan een Azure-netwerk dat u opgeeft wanneer u migratie instelt.


## <a name="prepare-azure"></a>Azure voorbereiden

Als u al een beoordeling met Azure Migrate Server Assessment hebt uitgevoerd, u de instructies in deze sectie overslaan omdat u deze stappen al hebt voltooid. 

Als u geen beoordeling hebt uitgevoerd, moet u Azure-machtigingen instellen voordat u migreren met Azure Migrate Server Migration.

- **Een project maken:** uw Azure-account heeft machtigingen nodig om een Azure Migrate-project te maken. 
- **Het Azure Migrate-replicatietoestel registreren:** het replicatietoestel maakt en registreert een Azure Active Directory-app in uw Azure-account. U moet hiervoor machtigingen delegeren.
- **Key Vault maken:** Als u VMware VM's wilt migreren met Azure Migrate Server Migration, maakt Azure Migrate een Key Vault in de brongroep om toegangssleutels te beheren voor het replicatieopslagaccount in uw abonnement. Als u de kluis wilt maken, hebt u machtigingen voor toewijzing van rollen nodig voor de resourcegroep waarin het Azure Migrate-project zich bevindt. 


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen om project te maken

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.
3. U moet **machtigingen voor inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Machtigingen toewijzen om het replicatietoestel te registreren

Delegeren machtigingen voor Azure Migrate Server Migration voor op agentgebaseerde migratie om een Azure AD-app in uw account te maken en te registreren. U machtigingen toewijzen met een van de volgende methoden:

- Een tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de tenant om Azure AD-apps te maken en te registreren.
- Een tenant/globale beheerder kan de rol Toepassingsontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

Het is vermeldenswaard dat:

- De apps hebben geen andere toegangsmachtigingen voor het abonnement dan de hierboven beschreven.
- U hebt deze machtigingen alleen nodig wanneer u een nieuw replicatietoestel registreert. U de machtigingen verwijderen nadat het replicatietoestel is ingesteld. 


#### <a name="grant-account-permissions"></a>Accountmachtigingen verlenen

De tenant/globale beheerder kan als volgt machtigingen verlenen

1. In Azure AD moet de tenant/globale beheerder naar > **gebruikersinstellingen**van Azure Active > **Directory-gebruikers**navigeren. **Azure Active Directory**
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Dit is een standaardinstelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Rol Toepassingsontwikkelaar toewijzen 

De tenant/globale beheerder kan de rol Toepassingsontwikkelaar toewijzen aan een account. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Machtigingen toewijzen om Key Vault te maken

Wijs als volgt machtigingen voor taaktoewijzing toe aan de resourcegroep waarin het Azure Migrate-project zich bevindt:

1. Selecteer **Access control (IAM) in**de brongroep in de Azure-portal .
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven. U hebt machtigingen **voor eigenaar** (of **inzender** en **gebruikerstoegangsbeheerder)** nodig.
3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de brongroep. 


## <a name="prepare-on-premises-vmware"></a>On-premises VMware voorbereiden

### <a name="prepare-an-account-for-automatic-discovery"></a>Een account voorbereiden voor automatische detectie

Azure Migrate Server Migration heeft toegang tot VMware-servers nodig om:

- Virtuele machines automatisch detecteren. Er is minimaal een alleen-lezen-account vereist.
- Replicatie, failover en failback orkestreren. U hebt een account nodig dat bewerkingen kan uitvoeren als het maken en verwijderen van schijven en het inschakelen van VM’s.

Ga als volgt te werk om het account te maken:

1. Als u een vast account wilt gebruiken, maakt u een rol op vCenter-niveau. Geef de rol een naam zoals **Azure_Site_Recovery**.
2. Wijs de rol de rechten toe die worden samengevat in onderstaande tabel.
3. Maak een gebruiker op de vCenter-server of vSphere-host. Wijs de rol toe aan de gebruiker.

#### <a name="vmware-account-permissions"></a>Machtigingen voor VMware-account

**Taak** | **Rol/machtigingen** | **Details**
--- | --- | ---
**VM-detectie** | Ten minste een alleen-lezen-gebruiker<br/><br/> Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **Geen toegang** met het object Doorgeven toe aan het **onderliggende** object aan de onderliggende objecten (vSphere-hosts, gegevensopslag, VM's en netwerken).
**Volledige replicatie, failover en failback** |  Maak een rol (Azure_Site_Recovery) met de vereiste machtigingen en wijs de rol toe aan een VMware-gebruiker of -groep<br/><br/> Datacentrumobject –> Doorgeven naar onderliggend object, rol=Azure_Site_Recovery<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **Geen toegang** met het object Doorgeven toe aan het **onderliggende** object aan de onderliggende objecten (vSphere-hosts, gegevensopslag, VM's en netwerken).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet zijn geïnstalleerd op de machines die u wilt repliceren.

- Het Azure Migrate-replicatietoestel kan een push-installatie van deze service doen wanneer u replicatie voor een machine inschakelt, of u deze handmatig installeren of installatiehulpprogramma's gebruiken.
- In deze zelfstudie gaat u de Mobility-service installeren met een push-installatie.
- Voor push-installatie moet u een account voorbereiden dat Azure Migrate Server Migration kan gebruiken om toegang te krijgen tot de VM. Dit account wordt alleen gebruikt voor de push-installatie als u de Mobility-service niet handmatig installeert.

U bereidt het account als volgt voor:

1. Bereid een domein of lokaal account met machtigingen voor om op de VM te installeren.
2. Als u voor Windows VM's geen domeinaccount gebruikt, schakelt u extern gebruikerstoegangsbeheer op de lokale machine uit door het DWORD-item **LocalAccountTokenFilterPolicy**toe te voegen met een waarde van in het register, onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Voor Linux VM's, maak een root-account op de bron Linux-server.


### <a name="check-vmware-requirements"></a>Vereisten voor VMware controleren

Zorg ervoor dat VMware-servers en VM's voldoen aan de vereisten voor migratie naar Azure. 


> [!NOTE]
> Agent-gebaseerde migratie met Azure Migrate Server Migration is gebaseerd op functies van de Azure Site Recovery-service. Sommige vereisten kunnen worden gekoppeld aan documentatie voor siteherstel.

1. [Verifieer](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) de vereisten voor VMware-servers.
2. [Verifiëren](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) VM-ondersteuningsvereisten voor migratie.
3. Controleer vm-instellingen. On-premises VM's die u naar Azure repliceert, moeten voldoen aan [de Azure VM-vereisten.](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)



## <a name="add-the-azure-migrate-server-migration-tool"></a>Het hulpprogramma voor migratie van Azure-migrerende server toevoegen

Als u de zelfstudie om VMware VM's te beoordelen niet hebt gevolgd, stelt u een Azure Migrate-project in en voegt u het hulpprogramma Azure Migrate Server Migration toe:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.

    ![Azure-migreren instellen](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik **onder Servers ontdekken, beoordelen en migreren**op Servers beoordelen en **migreren.**

    ![Servers ontdekken en beoordelen](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
2. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
3. Geef in **Projectdetails**de projectnaam en geografie op waarin u het project wilt maken en klik op **Volgende**

    ![Een Azure-migratieproject maken](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    U een Azure Migrate-project maken in een van deze regio's.

    **Geografie** | **Regio**
    --- | ---
    Azië | Azië - zuidoost
    Europa | Europa - noord of Europa - west
    Verenigde Staten | VS - oost of VS - west-centraal

    De opgegeven geografie voor het project wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises virtuele machines. U elk doelgebied selecteren voor de werkelijke migratie.
4. Selecteer **in Het beoordelingsprogramma selecteren**de optie Een **beoordelingstool overslaan voor nu** > **Volgende**.
5. Selecteer Azure Migreren in **Het migratiehulpprogramma selecteren:** **Servermigratie** > **volgende**.
6. Bekijk in **Hulpmiddelen voor controleren + toevoegen**de instellingen en klik op Hulpmiddelen **toevoegen**
7. Na het toevoegen van het hulpprogramma wordt het weergegeven in de hulpprogramma's voor azure migreren > > **serversmigratie.** **Servers**

## <a name="set-up-the-replication-appliance"></a>Het replicatietoestel instellen

De eerste stap van migratie is het instellen van het replicatietoestel. Het replicatietoestel is één, zeer beschikbare, on-premises VMware VM die deze componenten host:

- **Configuratieserver:** de configuratieserver coördineert de communicatie tussen on-premises en Azure en beheert gegevensreplicatie.
- **Processerver:** de processerver fungeert als replicatiegateway. Het ontvangt replicatiegegevens; optimaliseert het met caching, compressie en versleuteling en stuurt het naar een cacheopslagaccount in Azure. De processerver installeert ook de Mobility Service-agent op VM's die u wilt repliceren en voert automatische detectie uit van on-premises VM's vMware.


Als u het replicatietoestel wilt instellen, downloadt u een geprepareerde OVA-sjabloon (Open Virtualisatie Application) om deze in te stellen. U importeert de sjabloon in VMware en maakt de vm voor replicatietoestel. 

### <a name="download-the-replication-appliance-template"></a>De sjabloon voor het replicatietoestel downloaden

Download de sjabloon als volgt:

1. Klik in het Azure Migrate project op **Servers** onder **Migratiedoelen**.
2. In **Azure Migrate - Servers** > **Azure Migrate: ServerMigration**, klik op **Ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. In **Discover machines** > **Worden uw machines gevirtualiseerd?**, klik op **Ja, met VMWare vSphere hypervisor**.
4. Selecteer In Hoe wilt u **Using agent-based replication** **migreren?**
5. Selecteer **in doelgebied**het Azure-gebied waarnaar u de machines wilt migreren.
6. Selecteer **Bevestigen dat het doelgebied voor migratie een regionaam is.**
7. Klik **op Resources maken**. Hiermee wordt een Azure Site Recovery-kluis op de achtergrond geopperd.
    - U het doelgebied voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.

    ![Een Recovery Services-kluis maken](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Wilt u in Wilt u een nieuw **Install a replication appliance** **replicatietoestel installeren?**
9. Klik **op Downloaden**om het replicatietoestel te downloaden. Hiermee wordt een OVF-sjabloon gedownload die u gebruikt om een nieuwe VMware-VM te maken waarmee het toestel wordt uitgevoerd.
    ![OVA downloaden](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Let op de naam van de resourcegroep en de kluis Herstelservices. U hebt deze nodig tijdens de inzet van het apparaat.


### <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren

Nadat u de OVF-sjabloon hebt gedownload, importeert u deze in VMware om de replicatietoepassing te maken op een VMware VM met Windows Server 2016.

1. Meld u aan bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMWare vSphere-client.
2. Selecteer **in** het menu Bestand de optie **OVF-sjabloon implementeren** om de **wizard OVF-sjabloon implementeren**te starten. 
3. Voer in **De bron selecteren**de locatie van de gedownloade OVF in.
4. Selecteer **Volgende**in **Details bekijken**.
5. Accepteer **in Naam en map selecteren** en Configuratie **selecteren**de standaardinstellingen.
6. Selecteer **opslag** > **Selecteer virtuele schijfindeling**, selecteer Voor de beste prestaties **Dikke voorziening Eager Zeroed**.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. Selecteer In **Klaar om te voltooien**, als u de VM wilt instellen met de standaardinstellingen, de optie Aan na **implementatieeinde** > **Finish**.

   > [!TIP]
   > Als u een extra NIC wilt toevoegen, u **De stroom na implementatie** > **voltooien wissen.** De sjabloon bevat standaard één NIC. Na de implementatie kunt u meer NIC’s toevoegen.

### <a name="kick-off-replication-appliance-setup"></a>Installatie van replicatietoestel starten

1. Schakel de VM in vanuit de VMWare vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Nadat de installatie is voltooid, meldt u zich aan bij de VM als beheerder met het beheerderswachtwoord.
4. De eerste keer dat u zich aanmeldt, wordt het hulpprogramma voor het instellen van replicatieapparaten (Azure Site Recovery Configuration Tool) binnen enkele seconden gestart.
5. Voer een naam in die u wilt gebruiken voor het registreren van het toestel met Azure Migrate Server Migration. Klik vervolgens op **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement.
7. Wacht tot de tool is voltooid om een Azure AD-app te registreren om het toestel te identificeren. Het apparaat wordt opnieuw opgestart.
1. Meld u opnieuw aan bij de machine. Na enkele seconden wordt de wizard voor het beheer van de configuratieserver automatisch gestart.

### <a name="register-the-replication-appliance"></a>Het replicatietoestel registreren

Het instellen en registreren van het replicatietoestel voltooien.

1. Selecteer in de wizard Configuratieserverbeheer de optie **Configuratieconnectiviteit**.
2. Selecteer de NIC (er is standaard slechts één NIC) die het replicatietoestel gebruikt voor VM-detectie en om een push-installatie van de Mobiliteitsservice op bronmachines uit te stellen.
3. Selecteer de NIC die het replicatietoestel gebruikt voor connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U deze instelling niet wijzigen nadat deze is geconfigureerd.
4. Als het toestel zich achter een proxyserver bevindt, moet u proxy-instellingen opgeven.
    - Geef de proxynaam op als **http://ip-address**, of **http://FQDN**. HTTPS-proxyservers worden niet ondersteund.
5. Voeg de details toe die u hebt opgemerkt toen u de toestelsjabloon downloadde wanneer u om het abonnement wordt gevraagd, brongroepen en kluisgegevens.
6. Accepteer de gebruiksrechtovereenkomst in **Software van derden installeren**. Selecteer **Downloaden en installeren** om MySQL Server te installeren.
7. Selecteer **VMware PowerCLI installeren**. Zorg ervoor dat alle browservensters zijn gesloten voordat u dit doet. Selecteer vervolgens **Doorgaan**.
8. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
9. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
10. Voer de referenties in voor het account dat u [hebt gemaakt](#prepare-an-account-for-automatic-discovery) voor VMware-detectie. Selecteer **Doorgaan toevoegen** > **Continue**.
11. Voer in **De referenties van virtuele machines**configureren de referenties in die u hebt [gemaakt](#prepare-an-account-for-mobility-service-installation) voor push-installatie van de Mobiliteitsservice, wanneer u replicatie voor VM's inschakelt.  
    - Voor Windows-machines moet het account lokale administrator-machtigingen hebben op de machines die u wilt repliceren.
    - Geef voor Linux de details voor de superuser op.
12. Selecteer **Configuratie voltooien** om de registratie te voltooien.


Nadat het replicatietoestel is geregistreerd, maakt Azure Migrate Server Assessment verbinding met VMware-servers met behulp van de opgegeven instellingen en detecteert het VM's. U gedetecteerde VM's bekijken in**Gedetecteerde items** **beheren** > op het tabblad **Andere.**


## <a name="replicate-vms"></a>VM's repliceren

Selecteer nu VM's voor migratie.

> [!NOTE]
> U maximaal 10 machines samen repliceren. Als u meer moet repliceren, repliceer ze dan tegelijkertijd in batches van 10.

1. In het Azure Migrate project > **Servers**, **Azure Migrate: Server Migration**, klik op **Repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. In **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere**.
3. Selecteer **in On-premises toestel**de naam van het Azure Migrate-toestel dat u hebt ingesteld.
4. Geef in **vCenter-server**de naam op van de vCenter-server die de VM's beheert of de vSphere-server waarop de VM's worden gehost.
5. Selecteer **in Process Server**de naam van het replicatietoestel.
6. Geef **in gastreferenties**het VM-beheerdersaccount op dat wordt gebruikt voor push-installatie van de Mobiliteitsservice. Klik vervolgens op **Volgende: Virtuele machines**.

    ![VM's repliceren](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Selecteer **in virtuele machines**de machines die u wilt repliceren.

    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.

8. Controleer elke vm die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.
9. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
10. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
11. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik vervolgens op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik vervolgens op **Volgende**.

12. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **VM-grootte:** als u beoordelingsaanbevelingen gebruikt, bevat de vervolgkeuzelijst vm-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **OS-schijf**: Geef de schijf OS (opstart) op voor de VM. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset:** Als de VM zich in een Azure-beschikbaarheidsset moet bevinden na migratie, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

13. Geef in **schijven**op of de VM-schijven moeten worden gerepliceerd naar Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik vervolgens op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

14. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart,**Replicerende machines** **beheren** > . De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.




## <a name="track-and-monitor"></a>Bijhouden en bewaken

- Wanneer u op Een taak voor startreplicatie op **repliceren** klikt, begint deze. 
- Wanneer de taak Replicatie starten is voltooid, beginnen de machines met hun eerste replicatie naar Azure.
- Nadat de eerste replicatie is voltooid, wordt deltareplicatie gestart. Incrementele wijzigingen in on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.


U de taakstatus bijhouden in de portalmeldingen.

![Taak bijhouden](./media/tutorial-migrate-vmware-agent/jobs.png)

U de replicatiestatus controleren door te klikken op **Servers repliceren** in **Azure Migrate: Servermigratie**.
![Replicatie controleren](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer deltareplicatie begint, u een testmigratie uitvoeren voor de VM's voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan dit minstens één keer voor elke machine te doen, voordat u deze migreert.

- Het uitvoeren van een testmigratie controleert of migratie werkt zoals verwacht, zonder dat dit gevolgen heeft voor de on-premises machines, die operationeel blijven en blijven repliceren. 
- Testmigratie simuleert de migratie door een Azure VM te maken met behulp van gerepliceerde gegevens (meestal migreren naar een niet-productieVNet in uw Azure-abonnement).
- U de gerepliceerde test Azure VM gebruiken om de migratie te valideren, app-tests uit te voeren en eventuele problemen op te lossen voordat de volledige migratie wordt uitgevoerd.

Doe een testmigratie als volgt:


1. Klik in **Migratiedoelen** > **Servers** > **Azure Migreren: Servermigratie**, klik op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie werkt zoals verwacht, u de on-premises machines migreren.

1. Klik in het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie**, klik op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. Selecteer In **Het afsluiten van** > virtuele machines migreren**en een geplande migratie uitvoeren zonder gegevensverlies,** de optie **Ja** > **OK**.
    - Standaard wordt de on-premises VM uitgeschakeld in Azure Migrate om zo min mogelijk gegevensverlies te garanderen. 
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is uitgevoerd, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - Hiermee stopt u de replicatie voor de on-premises machine.
    - Hiermee verwijdert u de machine uit het aantal **replicerende servers** in Azure Migrate: Servermigratie.
    - Ruimt de informatie over replicatiestatus voor de VM op.
2. Installeer de Azure VM [Windows-](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) of [Linux-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) op de gemigreerde machines.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Verkeer naar de gemigreerde Azure VM-instantie omknippen.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Aanbevolen procedures na migratie

- On-premises
    - Leid appverkeer via de app die wordt uitgevoerd op het gemigreerde Azure VM-exemplaar.
    - Verwijder de on-premises VM's uit uw lokale VM-inventaris.
    - Verwijder de on-premises VM's uit de lokale back-ups.
    - Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's.
- Azure VM-instellingen aanpassen na migratie:
    - De [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) beheert de interactie van de VM met de Azure-Infrastructuurcontroller. Dit is vereist voor sommige Azure-services, zoals Azure Backup, Azure Site Recovery en Azure-beveiliging. Bij het migreren van Vm's met op agentgebaseerde migratie installeert het installatieprogramma Van Mobility Service Azure VM-agent op Windows-machines. Op Linux VM's raden we u aan de agent na migratie te installeren.
    - Verwijder de Mobiliteitsservice handmatig van de Azure VM na migratie.
    - Verwijder VMware-hulpprogramma's handmatig na migratie.
- In Azure:
    - Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
    - Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
- Bedrijfscontinuïteit/noodherstel
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - De toegang tot binnenkomend verkeer vergrendelen en beperken met [Azure Security Center - Net op tijd beheer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.




 ## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.
