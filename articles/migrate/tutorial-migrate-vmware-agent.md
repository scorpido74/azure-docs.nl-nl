---
title: On-premises virtuele VMware-machines migreren naar Azure met Azure Migrate server migratie op basis van een agent | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een op agents gebaseerde migratie van on-premises machines naar Azure uitvoert met Azure Migrate server migratie
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07e91abc1130505abc84f6687be7edd04522fa76
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720172"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Virtuele VMware-machines migreren naar Azure (op basis van een agent)

In dit artikel wordt beschreven hoe u on-premises virtuele VMware-machines naar Azure migreert met behulp van migratie op basis van een agent met het hulp programma voor migratie van Azure Migrate-server.

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van detectie, evaluatie en migratie van uw on-premises apps en werk belastingen en AWS/GCP VM-instanties naar Azure. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden.


In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel de bron omgeving in en implementeer een Azure Migrate replicatie-apparaat voor migratie op basis van een agent.
> * Stel de doel omgeving in voor de migratie.
> * Stel een replicatiebeleid in.
> * Schakel replicatie in.
> * Voer een test migratie uit om te controleren of alles werkt zoals verwacht.
> * Voer een volledige migratie naar Azure uit.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de How-Tos voor VMware-evaluatie en-migratie voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

We raden u aan de VMware VM-evaluatie uit te voeren met Azure Migrate server evaluatie voordat u Vm's naar Azure migreert. Stel als volgt een evaluatie in:

1. Volg de zelf studie voor het [voorbereiden van Azure en VMware](tutorial-prepare-vmware.md) voor evaluatie.
2. Volg [deze zelf studie](tutorial-assess-vmware.md) om een Azure migrate apparaat in te stellen voor evaluatie, en ontdek en evalueer vm's.


We raden u aan om een evaluatie uit te voeren, maar u hoeft geen beoordeling te verrichten voordat u virtuele machines migreert.

## <a name="migration-methods"></a>Migratie methoden

U kunt virtuele VMware-machines migreren naar Azure met behulp van het hulp programma voor migratie van Azure Migrate-server. Dit hulp programma biedt een aantal opties voor de migratie van virtuele VMware-machines:

- Replicatie zonder agent. Migreer Vm's zonder dat u iets hoeft te installeren.
- Migratie op basis van een agent. of replicatie. Installeer een agent (de Mobility Services-agent) op de virtuele machine voor replicatie.

Als u wilt beslissen of u zonder agents of migratie op basis van een agent wilt gebruiken, raadpleegt u de volgende artikelen:

- [Meer informatie over](server-migrate-overview.md) de opties voor VMware-migratie.
- [Migratie methoden vergelijken](server-migrate-overview.md#compare-migration-methods).
- [Volg dit artikel om de](tutorial-migrate-vmware.md) migratie zonder agents uit te proberen.



## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Bekijk](migrate-architecture.md) de architectuur van VMware-migratie.
2. Zorg ervoor dat aan uw Azure-account de rol Inzender voor virtuele machines is toegewezen, zodat u gemachtigd bent om:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijven naar een door Azure beheerde schijf. 

3. [Stel een Azure-netwerk](../virtual-network/manage-virtual-network.md#create-a-virtual-network)in. On-premises machines worden gerepliceerd naar Azure Managed disks. Wanneer u een failover naar Azure voor de migratie doorvoert, worden virtuele Azure-machines gemaakt op basis van deze beheerde schijven en gekoppeld aan een Azure-netwerk dat u opgeeft wanneer u de migratie instelt.


## <a name="prepare-azure"></a>Azure voorbereiden

Als u al een evaluatie met Azure Migrate server evaluatie hebt uitgevoerd, kunt u de instructies in deze sectie overs Laan, omdat u deze stappen al hebt uitgevoerd. 

Als u geen evaluatie hebt uitgevoerd, moet u de Azure-machtigingen instellen voordat u kunt migreren met Azure Migrate server migratie.

- **Een project maken**: uw Azure-account moet machtigingen hebben om een Azure migrate project te maken. 
- **Registreer het Azure migrate replicatie apparaat**: het replicatie apparaat maakt en registreert een Azure Active Directory-app in uw Azure-account. U moet de machtigingen hiervoor overdragen.
- **Maak Key Vault**: als u virtuele VMware-machines wilt migreren met Azure migrate server-migratie, maakt Azure Migrate een Key Vault in de resource groep, om toegangs sleutels te beheren voor het opslag account voor replicatie in uw abonnement. Als u de kluis wilt maken, moet u machtigingen voor roltoewijzing hebben voor de resource groep waarin het Azure Migrate-project zich bevindt. 


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Machtigingen toewijzen voor het registreren van het replicatie apparaat

Voor migratie op basis van een agent delegeren machtigingen voor Azure Migrate server migratie om een Azure AD-app in uw account te maken en registreren. U kunt machtigingen toewijzen met behulp van een van de volgende methoden:

- Een Tenant/globale beheerder kan machtigingen verlenen aan gebruikers in de Tenant om Azure AD-apps te maken en registreren.
- Een Tenant/globale beheerder kan de rol van toepassings ontwikkelaar (die de machtigingen heeft) toewijzen aan het account.

Het is een goed idee dat:

- De apps hebben geen andere toegangs machtigingen voor het abonnement dan de hierboven beschreven.
- U hebt deze machtigingen alleen nodig wanneer u een nieuw replicatie apparaat registreert. U kunt de machtigingen verwijderen nadat het replicatie apparaat is ingesteld. 


#### <a name="grant-account-permissions"></a>Account machtigingen verlenen

De Tenant/globale beheerder kan machtigingen als volgt verlenen

1. In azure AD moet de Tenant/globale beheerder naar **Azure Active Directory** > **gebruikers** > **gebruikers instellingen**navigeren.
2. De beheerder moet **app-registraties** instellen op **Ja**.

    ![Azure AD-machtigingen](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Dit is een standaard instelling die niet gevoelig is. [Meer informatie](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Rol toepassings ontwikkelaar toewijzen 

De Tenant/globale beheerder kan de rol van toepassings ontwikkelaar toewijzen aan een account. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Machtigingen voor het maken van Key Vault toewijzen

Wijs als volgt machtigingen voor roltoewijzing toe voor de resource groep waarin het Azure Migrate project zich bevindt:

1. Selecteer in de resource groep in de Azure Portal **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven. U hebt machtigingen voor **eigenaar** (of **Inzender** en **gebruikers toegang**) nodig.
3. Als u niet over de vereiste machtigingen beschikt, vraagt u deze aan bij de eigenaar van de resource groep. 


## <a name="prepare-on-premises-vmware"></a>On-premises VMware voorbereiden

### <a name="prepare-an-account-for-automatic-discovery"></a>Een account voorbereiden voor automatische detectie

Azure Migrate server-migratie heeft toegang tot VMware-servers nodig voor het volgende:

- Virtuele machines automatisch detecteren. Er is minimaal een alleen-lezen-account vereist.
- Replicatie, failover en failback orkestreren. U hebt een account nodig dat bewerkingen kan uitvoeren als het maken en verwijderen van schijven en het inschakelen van VM’s.

Ga als volgt te werk om het account te maken:

1. Als u een vast account wilt gebruiken, maakt u een rol op vCenter-niveau. Geef de rol een naam zoals **Azure_Site_Recovery**.
2. Wijs de rol de rechten toe die worden samengevat in onderstaande tabel.
3. Maak een gebruiker op de vCenter-server of vSphere-host. Wijs de rol toe aan de gebruiker.

#### <a name="vmware-account-permissions"></a>Machtigingen voor VMware-account

**Taak** | **Rol/machtigingen** | **Details**
--- | --- | ---
**VM-detectie** | Ten minste een alleen-lezen-gebruiker<br/><br/> Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Wijs om de toegang te beperken de rol **Geen toegang** met het object **Doorgeven naar onderliggend object** toe aan de onderliggende objecten (vSphere-hosts, gegevensopslag, VM’s en netwerken).
**Volledige replicatie, failover en failback** |  Maak een rol (Azure_Site_Recovery) met de vereiste machtigingen en wijs de rol toe aan een VMware-gebruiker of -groep<br/><br/> Datacentrumobject –> Doorgeven naar onderliggend object, rol=Azure_Site_Recovery<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Wijs om de toegang te beperken de rol **Geen toegang** met het object **Doorgeven naar onderliggend object** toe aan de onderliggende objecten (vSphere-hosts, gegevensopslag, VM’s en netwerken).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet zijn geïnstalleerd op de machines die u wilt repliceren.

- Het Azure Migrate replicatie apparaat kan een push-installatie van deze service uitvoeren wanneer u replicatie voor een machine inschakelt, of u kunt deze hand matig installeren of installatie Programma's gebruiken.
- In deze zelfstudie gaat u de Mobility-service installeren met een push-installatie.
- Voor een push-installatie moet u een account voorbereiden dat Azure Migrate server migratie kan gebruiken om toegang te krijgen tot de virtuele machine.

U bereidt het account als volgt voor:

1. Bereid een domein of lokaal account met machtigingen voor om op de VM te installeren.
2. Als u voor Windows-Vm's geen domein account gebruikt, schakelt u toegangs beheer voor externe gebruikers op de lokale computer uit door de DWORD-vermelding **LocalAccountTokenFilterPolicy**toe te voegen met een waarde in het REGI ster onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ Windows\CurrentVersion\Policies\System**
3. Voor Linux-Vm's bereidt u een hoofd account voor op de Linux-bron server.


### <a name="check-vmware-requirements"></a>Vereisten voor VMware controleren

Zorg ervoor dat VMware-servers en Vm's voldoen aan de vereisten voor migratie naar Azure. 


> [!NOTE]
> Migratie op basis van een agent met Azure Migrate server migratie is gebaseerd op de functies van de Azure Site Recovery-service. Sommige vereisten kunnen worden gekoppeld aan Site Recovery documentatie.

1. [Verifieer](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) de vereisten voor VMware-servers.
2. [Verifiëren](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) VM-ondersteunings vereisten voor migratie.
3. Controleer de VM-instellingen. On-premises Vm's die u naar Azure repliceert, moeten voldoen aan de vereisten van de [Azure-VM](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Het hulp programma voor migratie van Azure Migrate server toevoegen

Als u de zelf studie voor het beoordelen van virtuele VMware-machines niet hebt gevolgd, stelt u een Azure Migrate project in en voegt u vervolgens het hulp programma Azure Migrate server migratie toe:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.

    ![Azure Migrate instellen](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik onder **servers detecteren, evalueren en migreren**op **servers beoordelen en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
2. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
3. Geef in **Project Details**de project naam en geografie op waarin u het project wilt maken en klik op **volgende**

    ![Een Azure Migrate-project maken](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    U kunt in elk van deze geografische gebieden een Azure Migrate project maken.

    **Geografie** | **Regio**
    --- | ---
    Azië | Azië - zuidoost
    Europa | Europa - noord of Europa - west
    Verenigde Staten | US - oost of US - west-centraal

    De opgegeven geografie voor het project wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises virtuele machines. U kunt een wille keurige doel regio voor de daad werkelijke migratie selecteren.
4. Selecteer in **hulp programma voor beoordeling selecteren** **de optie overs Laan een evaluatie hulpmiddel voor nu toevoegen** > **volgende**.
5. Selecteer in **hulp programma voor migratie selecteren**de optie **Azure migrate: Server migratie** > **volgende**.
6. Controleer de instellingen in **hulp middelen voor beoordeling en toevoegen**en klik op **hulp middelen toevoegen**
7. Nadat u het hulp programma hebt toegevoegd, wordt het weer gegeven in het Azure Migrate project > **Servers** > **migratie hulpprogramma's**.

## <a name="set-up-the-replication-appliance"></a>Het replicatie apparaat instellen

De eerste stap van de migratie is het instellen van het replicatie apparaat. Het replicatie apparaat is een enkelvoudige, Maxi maal beschik bare virtuele VMware-machine die als host fungeert voor deze onderdelen:

- **Configuratie server**: de configuratie server coördineert de communicatie tussen on-premises en Azure, en beheert de gegevens replicatie.
- **Proces server**: de proces server fungeert als replicatie gateway. Het ontvangt replicatie gegevens; optimaliseert het met caching, compressie en versleuteling, en verzendt het naar een cache-opslag account in Azure. De proces server installeert ook de Mobility Service-agent op Vm's die u wilt repliceren en voert automatische detectie van on-premises virtuele VMware-machines uit.


Als u het replicatie apparaat wilt instellen, downloadt u een voor bereide eicellen-sjabloon (open Virtualization Application). U importeert de sjabloon in VMware en maakt de replicatie apparaat-VM. 

### <a name="download-the-replication-appliance-template"></a>De sjabloon voor het replicatie apparaat downloaden

Down load de sjabloon als volgt:

1. Klik in het Azure Migrate project op **servers** onder **migratie doelen**.
2. Klik in **Azure migrate-Servers** > **Azure migrate: Server migratie**op **ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** klikt u op **Ja, met VMWare vSphere-hypervisor**.
4. In **Hoe wilt u migreren? selecteert u** **replicatie op basis**van een agent.
5. Selecteer in **doel regio**de Azure-regio waarnaar u de machines wilt migreren.
6. Selecteer **controleren of de doel regio voor de migratie de regio naam is**.
7. Klik op **resources maken**. Hiermee maakt u een Azure Site Recovery kluis op de achtergrond.
    - U kunt de doel regio voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.

    ![Een Recovery Services-kluis maken](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. In wilt **u een nieuw replicatie apparaat installeren?** Selecteer **een replicatie apparaat installeren**.
9. Klik op **downloaden**om het replicatie apparaat te downloaden. Hiermee downloadt u een OVF-sjabloon die u gebruikt voor het maken van een nieuwe VMware-VM die het apparaat uitvoert.
    ![eicellen downloaden](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Noteer de naam van de resource groep en de Recovery Services kluis. U hebt deze nodig tijdens de implementatie van het apparaat.


### <a name="import-the-template-in-vmware"></a>De sjabloon in VMware importeren

Nadat u de OVF-sjabloon hebt gedownload, importeert u deze in VMware om de replicatie toepassing te maken op een virtuele VMware-machine met Windows Server 2016.

1. Meld u aan bij de VMware vCenter-server of vSphere ESXi-host met behulp van de VMWare vSphere-client.
2. Selecteer in het menu **Bestand** de optie **OVF-sjabloon implementeren** om de **wizard voor het implementeren van OVF-sjablonen** te starten. 
3. In **bron selecteren voert u**de locatie van de gedownloade OVF in.
4. Selecteer **volgende**onder **Details weer geven**.
5. Accepteer de standaard instellingen in **naam en map selecteren** en **configuratie selecteren**.
6. Selecteer in **opslag** > **virtuele schijf indeling**selecteren, voor de beste prestaties, de optie voor een brede bekrachtiging in de vorm van een zeer **nul**.
7. Accepteer de standaardinstellingen in de rest van de wizardpagina's.
8. Wanneer u **klaar bent om**de virtuele machine in te stellen met de standaard instellingen, selecteert u **inschakelen na de implementatie** > **volt ooien**.

   > [!TIP]
   > Als u een extra NIC wilt toevoegen, wist u **Inschakelen na de implementatie** > **Voltooien**. De sjabloon bevat standaard één NIC. Na de implementatie kunt u meer NIC’s toevoegen.

### <a name="kick-off-replication-appliance-setup"></a>Replicatie-instellingen voor het apparaat starten

1. Schakel de VM in vanuit de VMWare vSphere Client-console.
2. De VM wordt opgestart in een Windows Server 2016-installatie-ervaring. Accepteer de gebruiksrechtovereenkomst en voer een Administrator-wachtwoord in.
3. Nadat de installatie is voltooid, meldt u zich bij de virtuele machine aan als beheerder met behulp van het beheerders wachtwoord.
4. De eerste keer dat u zich aanmeldt, wordt het installatie programma voor replicatie apparaten (Azure Site Recovery configuratie hulpprogramma) binnen een paar seconden gestart.
5. Voer een naam in die moet worden gebruikt voor het registreren van het apparaat met Azure Migrate server migratie. Klik op **Volgende**.
6. Het hulpprogramma controleert of de VM verbinding kan maken met Azure. Nadat de verbinding tot stand is gebracht, selecteert u **Aanmelden** om u aan te melden bij uw Azure-abonnement.
7. Wacht totdat het hulp programma de registratie van een Azure AD-app heeft voltooid om het apparaat te identificeren. Het apparaat wordt opnieuw opgestart.
1. Meld u opnieuw aan bij de machine. Na enkele seconden wordt de wizard voor het beheer van de configuratieserver automatisch gestart.

### <a name="register-the-replication-appliance"></a>Het replicatie apparaat registreren

Het instellen en registreren van het replicatie apparaat volt ooien.

1. Selecteer in de wizard Configuratie Server beheer de optie **connectiviteit instellen**.
2. Selecteer de NIC (standaard is er slechts één NIC) die het replicatie apparaat gebruikt voor het detecteren van VM'S en om een push installatie van de Mobility-service op bron machines uit te voeren.
3. Selecteer de NIC die het replicatie apparaat gebruikt voor de connectiviteit met Azure. Selecteer vervolgens **Opslaan**. U kunt deze instelling niet wijzigen nadat deze is geconfigureerd.
4. Als het apparaat zich achter een proxy server bevindt, moet u proxy-instellingen opgeven.
    - Geef de proxy naam op als **http://ip-address** , of **http://FQDN** . HTTPS-proxy servers worden niet ondersteund.
5. Wanneer u wordt gevraagd om het abonnement, de resource groepen en de kluis gegevens, voegt u de details toe die u hebt genoteerd tijdens het downloaden van de sjabloon voor het apparaat.
6. Accepteer de gebruiksrechtovereenkomst in **Software van derden installeren**. Selecteer **Downloaden en installeren** om MySQL Server te installeren.
7. Selecteer **VMware PowerCLI installeren**. Zorg ervoor dat alle browservensters zijn gesloten voordat u dit doet. Selecteer vervolgens **Doorgaan**.
8. In **De configuratie van het apparaat valideren** worden de vereisten gecontroleerd voordat u doorgaat.
9. In **vCenter Server vSphere/ESXi-server configureren** voert u de FQDN of het IP-adres van de vCenter-server, of vSphere-host, in waar de VM's die u wilt repliceren zich bevinden. Voer de poort in waarop de server luistert. Voer een beschrijvende naam in voor de VMware-server in de kluis.
10. Voer de referenties in voor het account dat u hebt [gemaakt](#prepare-an-account-for-automatic-discovery) voor VMware-detectie. Selecteer **toevoegen** > **door gaan**.
11. Voer in **referenties voor virtuele machine configureren**de referenties in die u hebt [gemaakt](#prepare-an-account-for-mobility-service-installation) voor de push-installatie van de Mobility-service, wanneer u replicatie voor virtuele machines inschakelt.  
    - Voor Windows-machines moet het account lokale administrator-machtigingen hebben op de machines die u wilt repliceren.
    - Geef voor Linux de details voor de superuser op.
12. Selecteer **Configuratie voltooien** om de registratie te voltooien.


Nadat het replicatie apparaat is geregistreerd, maakt Azure Migrate server beoordeling verbinding met VMware-servers met behulp van de opgegeven instellingen en detecteert Vm's. U kunt gedetecteerde Vm's weer geven in > **gedetecteerde items** **beheren** op het tabblad **Overige** .


## <a name="replicate-vms"></a>VM's repliceren

Selecteer nu Vm's voor migratie.

> [!NOTE]
> U kunt Maxi maal 10 computers tegelijk repliceren. Als u meer wilt repliceren, repliceert u ze tegelijkertijd in batches van 10.

1. Klik in het Azure Migrate project > **servers**, **Azure migrate: Server migratie**op **repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. In **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere**.
3. Selecteer in **on-premises apparaat**de naam van het Azure migrate apparaat dat u hebt ingesteld.
4. Geef in **vCenter-Server**de naam op van de vCenter-Server die de vm's beheert, of de vSphere-server waarop de virtuele machines worden gehost.
5. Selecteer in **proces server**de naam van het replicatie apparaat.
6. Geef bij **gast referenties**het VM-beheerders account op dat wordt gebruikt voor de push-installatie van de Mobility-service. Klik vervolgens op **volgende: virtuele machines**.

    ![VM's repliceren](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Selecteer in **virtual machines**de computers die u wilt repliceren.

    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.

8. Controleer elke VM die u wilt migreren. Klik vervolgens op **volgende: doel instellingen**.
9. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
10. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
11. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik op **Volgende**.

12. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-grootte**: als u evaluatie aanbevelingen gebruikt, bevat de vervolg keuzelijst VM-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **Besturingssysteem schijf**: Geef de opstart schijf van het besturings systeem voor de virtuele machine op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset**: als de virtuele machine na de migratie moet worden besteld in een Azure-beschikbaarheidsset, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

13. Geef in **schijven**op of de VM-schijven moeten worden gerepliceerd naar Azure en selecteer het schijf type (standaard SSD/HDD of Premium Managed disks) in Azure. Klik op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

14. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart, > **replicerende machines** **beheren** . De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.




## <a name="track-and-monitor"></a>Bijhouden en controleren

- Wanneer **u op replicatie** starten klikt, begint de taak. 
- Wanneer de taak replicatie starten is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de replicatie van verschillen. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replica schijven in Azure.


U kunt de taak status volgen in de portal meldingen.

![Taak bijhouden](./media/tutorial-migrate-vmware-agent/jobs.png)

U kunt de replicatie status controleren door te klikken op **replicerende servers** in **Azure migrate: Server migratie**.
replicatie](./media/tutorial-migrate-vmware-agent/replicate-servers.png) ![controleren

## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer Delta replicatie begint, kunt u een test migratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om dit ten minste één keer te doen voor elke machine voordat u deze migreert.

- Bij het uitvoeren van een test migratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de on-premises machines, die operationeel blijven, en door gaan met repliceren. 
- Met test migratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde virtuele machine van Azure gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een test migratie uit te voeren:


1. In **migratie doelen** > **servers** > **Azure migrate: Server migratie**klikt u op **gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt gecontroleerd of de test migratie werkt zoals verwacht, kunt u de on-premises machines migreren.

1. Klik in het Azure Migrate project > **Servers** > **Azure migrate: Server migratie**op **servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. In **Migreren** > **Virtuele machines afsluiten en geplande migratie uitvoeren zonder gegevensverlies** selecteert u **Ja** > **OK**.
    - Azure Migrate sluit standaard de on-premises VM af en voert de replicatie op aanvraag uit om VM-wijzigingen die sinds de laatste replicatie zijn opgetreden, te synchroniseren. Zo gaan er geen gegevens verloren.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechter muisknop op de virtuele machine > de **migratie stoppen**. Hiermee wordt de replicatie voor de on-premises computer gestopt en worden de gegevens van de replicatie status voor de virtuele machine opgeschoond.
2. Installeer de Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -of [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -agent op de gemigreerde computers.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Verdeel het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

- On-premises
    - Leid appverkeer via de app die wordt uitgevoerd op het gemigreerde Azure VM-exemplaar.
    - Verwijder de on-premises VM's uit uw lokale VM-inventaris.
    - Verwijder de on-premises VM's uit de lokale back-ups.
    - Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's.
- Instellingen voor tweak Azure VM na migratie:
    - De [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) beheert de interactie van de VM met de Azure-Infrastructuurcontroller. Dit is vereist voor sommige Azure-services, zoals Azure Backup, Azure Site Recovery en Azure-beveiliging. Bij het migreren van VMare Vm's met migratie op basis van een agent installeert het installatie programma van de Mobility-service Azure VM-agent op Windows-computers. Op virtuele Linux-machines wordt u aangeraden de agent na de migratie te installeren.
    - Verwijder de Mobility-service hand matig van de virtuele Azure-machine na de migratie.
    - Verwijder de VMware-hulpprogram ma's hand matig na de migratie.
- In Azure:
    - Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
    - Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
- Bedrijfs continuïteit/herstel na nood gevallen
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang tot inkomend verkeer met [Azure Security Center-just-in-time-beheer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.




 ## <a name="next-steps"></a>Volgende stappen

Onderzoek de [reis voor Cloud migratie](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud-acceptatie Framework.
