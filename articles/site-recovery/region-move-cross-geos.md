---
title: Virtuele Azure-machines verplaatsen tussen overheid en open bare regio's met Azure Site Recovery
description: Gebruik Azure Site Recovery om virtuele Azure-machines te verplaatsen tussen Azure Government en open bare regio's.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2a749e9345fec0e91751641cd15805d7f7d62d95
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961415"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Virtuele Azure-machines verplaatsen tussen Azure Government en open bare regio's 

Misschien wilt u uw IaaS-Vm's verplaatsen tussen Azure Government en open bare regio's om de beschik baarheid van uw bestaande Vm's te verg Roten, de beheersbaarheid te verbeteren of om beheer redenen, zoals [hier](azure-to-azure-move-overview.md)wordt beschreven.

Behalve dat u de service [Azure Site Recovery](site-recovery-overview.md) kunt gebruiken om herstel na noodgevallen van on-premises machines en Azure-VM’s te beheren en te organiseren met als doel de bedrijfscontinuïteit te waarborgen, en deze service kunt gebruiken voor herstel na een noodgeval (BCDR), kunt u Site Recovery ook gebruiken om Azure-VM’s te verplaatsen naar een secundaire regio.       

In deze zelf studie leert u hoe u virtuele Azure-machines tussen Azure Government en open bare regio's kunt verplaatsen met behulp van Azure Site Recovery. Hetzelfde kan worden uitgebreid om Vm's te verplaatsen tussen regio paren die zich niet binnen hetzelfde geografische cluster bevinden. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten verifiëren
> * De bron-VM’s voorbereiden
> * De doelregio voorbereiden
> * Gegevens kopiëren naar de doelregio
> * De configuratie testen
> * De verplaatsing uitvoeren
> * De resources in de bronregio verwijderen

> [!IMPORTANT]
> In deze zelf studie ziet u hoe u virtuele Azure-machines verplaatst tussen Azure Government en open bare regio's, of tussen de paren van regio's die niet worden ondersteund door de reguliere oplossing voor herstel na nood gevallen voor Azure-Vm's. In het geval worden de paren van de bron-en doel regio's [ondersteund](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Raadpleeg dit [document](azure-to-azure-tutorial-migrate.md) voor de verplaatsing. Raadpleeg [de zelf studie](move-azure-VMs-AVset-Azone.md)als u de beschik baarheid wilt verbeteren door vm's in een beschikbaarheidsset te verplaatsen naar een zone vastgemaakte vm's in een andere regio.

> [!IMPORTANT]
> Het is niet raadzaam om deze methode te gebruiken voor het configureren van DR tussen de niet-ondersteunde regio paren, aangezien de paren worden gedefinieerd, wat van belang is voor een nood herstel scenario.

## <a name="verify-prerequisites"></a>Vereisten verifiëren

> [!NOTE]
> Zorg ervoor dat u bekend bent met de [architectuur en onderdelen](physical-azure-architecture.md) voor dit scenario. Deze architectuur wordt gebruikt om virtuele Azure-machines te verplaatsen **door de vm's als fysieke servers te behandelen**.

- Raadpleeg de [ondersteuningsvereisten](vmware-physical-secondary-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat de servers die u wilt repliceren, voldoen aan de vereisten van de [Azure-VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Een account voorbereiden voor de automatische installatie van de Mobility-service op elke server die u wilt repliceren.

- Let op: nadat u een failover hebt uitgevoerd naar de doel regio in azure, kunt u een failback naar de bron regio niet rechtstreeks uitvoeren. U moet de replicatie opnieuw instellen naar het doel.

### <a name="verify-azure-account-permissions"></a>Azure-account machtigingen verifiëren

Zorg ervoor dat uw Azure-account machtigingen heeft voor de replicatie van virtuele machines naar Azure.

- Bekijk de [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) die u nodig hebt om machines te repliceren naar Azure.
- Controleer en wijzig [op rollen gebaseerde toegangs](../role-based-access-control/role-assignments-portal.md) machtigingen. 

### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Stel een Azure-doel [netwerk](../virtual-network/quick-create-portal.md)in.

- Virtuele Azure-machines worden in dit netwerk geplaatst wanneer ze na een failover worden gemaakt.
- Het netwerk moet zich in dezelfde regio bevinden als de Recovery Services kluis


### <a name="set-up-an-azure-storage-account"></a>Een Azure-opslagaccount instellen

Stel een [Azure Storage-account](../storage/common/storage-quickstart-create-account.md)in.

- Site Recovery repliceert on-premises machines naar Azure Storage. Virtuele Azure-machines worden gemaakt op basis van de opslag nadat de failover is uitgevoerd.
- Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.


## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet worden geïnstalleerd op elke server die u wilt repliceren. Site Recovery installeert deze service automatisch wanneer u replicatie inschakelt voor de-server. Als u automatisch wilt installeren, moet u een account voorbereiden dat Site Recovery gebruikt voor toegang tot de server.

- U kunt een domein-of lokaal account gebruiken
- Als u voor Windows-Vm's geen domein account gebruikt, schakelt u toegangs beheer voor externe gebruikers op de lokale computer uit. Als u dit wilt doen, voegt u in het REGI ster onder **HKEY_LOCAL_MACHINE \Software\Microsoft\Windows\CurrentVersion\Policies\System**de DWORD-vermelding **LocalAccountTokenFilterPolicy**met de waarde 1 toe.
- Als u de register vermelding wilt toevoegen om de instelling uit te scha kelen vanuit een CLI, typt u: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Voor Linux moet het account hoofdmap zijn op de Linux-bron server.


## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer of u een Azure-abonnement hebt waarmee u in staat bent om virtuele machines te maken in de doelregio die wordt gebruikt voor herstel na noodgevallen. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

2. Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning kunnen bieden voor VM’s met grootten die overeenkomen met uw bron-VM’s. Site Recovery kiest voor de doel-VM dezelfde of de dichtstbijzijnde grootte om gegevens naar het doel te kopiëren.

3. Zorg ervoor dat u een doelresource maakt voor elk onderdeel dat is geïdentificeerd in de bronindeling voor netwerken. Dit is belang rijk om ervoor te zorgen dat, na het uitsnijden naar de doel regio, uw Vm's beschikken over alle functionaliteit en functies die u in de bron had.

    > [!NOTE]
    > In Azure Site Recovery wordt automatisch een virtueel netwerk gedetecteerd en gemaakt wanneer u replicatie inschakelt voor de bron-VM. U kunt er ook voor kiezen om vooraf een netwerk te maken en dit toe te wijzen aan de VM in de gebruikersstroom om replicatie in te schakelen. Maar alle andere resources moet u zoals hieronder wordt beschreven handmatig maken in de doelregio.

     Raadpleeg de volgende documenten om de meest gebruikte netwerkresources te maken die relevant zijn voor u, op basis van de bron-VM-configuratie.

    - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
    - [Openbare IP](../virtual-network/virtual-network-public-ip-address.md)
    
    Voor andere netwerkonderdelen raadpleegt u de [netwerkondersteuningsdocumentatie.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. [Maak handmatig een niet-productienetwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in de doelregio als u de configuratie wilt testen voordat u het uiteindelijke knippen naar de doelregio uitvoert. Hiermee ontstaat er slechts minimale verstoring in de productie. Dit wordt aanbevolen.

## <a name="copy-data-to-the-target-region"></a>Gegevens kopiëren naar de doelregio
De onderstaande stappen begeleiden u bij het gebruik van Azure Site Recovery om gegevens te kopiëren naar de doelregio.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Maak de kluis in elke gewenste regio, met uitzondering van de bronregio.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klik op **Een resource maken** > **Beheerhulpprogramma's** > **Backup en Site Recovery**.
3. Bij **Naam** geeft u de beschrijvende naam **ContosoVMVault** op. Als u meer dan één abonnement hebt, selecteert u het juiste abonnement.
4. Maak een resourcegroep met de naam **ContosoRG**.
5. Geef een Azure-regio op. Zie Geografische beschikbaarheid in [Prijsinformatie voor Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) om na te gaan welke regio's er worden ondersteund.
6. Klik in Recovery Services kluizen op **overzicht** > **ConsotoVMVault** > **en repliceren**
7. selecteer **Naar Azure** > **Niet gevirtualiseerde/overige**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Stel de configuratie server in om virtuele machines te detecteren.


Stel de configuratie server in, Registreer deze in de kluis en ontdek Vm's.

1. Klik op **Site Recovery** > infra structuur > **bron**voor te **bereiden** .
2. Als u geen configuratie server hebt, klikt u op **+ Configuratie server**.
3. Controleer in **server toevoegen**of de **Configuratie server** wordt weer gegeven bij **server type**.
4. Down load het installatie bestand voor de Site Recovery Unified Setup.
5. Download de registratiesleutel voor de kluis. U hebt deze nodig wanneer u Unified Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

   ![Bron instellen](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>De configuratie server in de kluis registreren

Doe het volgende voordat u begint: 

#### <a name="verify-time-accuracy"></a>Nauw keurigheid van tijd controleren
Zorg ervoor dat op de computer met de configuratie server de systeem klok is gesynchroniseerd met een [tijd server](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Dit moet overeenkomen. Als dat niet het geval is, kan de installatie mislukken.

#### <a name="verify-connectivity"></a>Connectiviteit controleren
Zorg ervoor dat de computer toegang kan krijgen tot deze Url's op basis van uw omgeving: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Op IP-adres gebaseerde firewall regels moeten communicatie toestaan voor alle Azure-Url's die hierboven worden vermeld via de HTTPS-poort (443). Om het IP-bereik te vereenvoudigen en te beperken, is het raadzaam om URL-filtering uit te voeren.

- **Commerciële ip's** : sta de [IP-adresbereiken van het Azure-Data Center](https://www.microsoft.com/download/confirmation.aspx?id=41653)en de HTTPS-poort (443) toe. IP-adresbereiken toestaan voor de Azure-regio van uw abonnement ter ondersteuning van de AAD-, back-up-, replicatie-en opslag-Url's.  
- **Overheids ip's** : Hiermee staat u de [IP-adresbereiken van Azure Government Data Center](https://www.microsoft.com/en-us/download/details.aspx?id=57063)en de HTTPS-poort (443) toe voor alle USGov-regio's (Virginia, Texas, Arizona en Iowa) ter ondersteuning van Aad-, back-up-, replicatie-en opslag-url's.  

#### <a name="run-setup"></a>Voer het installatieprogramma uit
Voer Unified Setup uit als een lokale beheerder om de configuratie server te installeren. De proces server en de hoofddoel server worden ook standaard geïnstalleerd op de configuratie server.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Nadat de registratie is voltooid, wordt de configuratie server weer gegeven op de pagina **instellingen** > **servers** in de kluis.

### <a name="configure-target-settings-for-replication"></a>Doel instellingen voor replicatie configureren

Selecteer en controleer doelbronnen.

1. Klik op **Infrastructuur voorbereiden** > **Doel** en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het doel implementatie model op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

   ![Doel](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Klik op **Infrastructuur voor Site Recovery** > **Herstelbeleid** >  **+Herstelbeleid** om een nieuw replicatiebeleid te maken.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op. Met deze waarde wordt bepaald hoe vaak gegevens herstel punten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
4. Geef in **Bewaarperiode van het herstelpunt** op hoelang (in uren) de bewaarperiode voor elk herstelpunt is. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster. Voor computers die worden gerepliceerd naar Premium Storage, wordt een bewaarperiode van maximaal 24 uur ondersteund, en 72 uur voor computers die naar Standard Storage worden gerepliceerd.
5. Geef in de frequentie van de **app-consistente moment opname**op hoe vaak (in minuten) er herstel punten moeten worden gemaakt met toepassings consistente moment opnamen. Klik op **OK** om het beleid te maken.

    ![Beleid voor replicatie](./media/physical-azure-disaster-recovery/replication-policy.png)


Het beleid wordt automatisch gekoppeld aan de configuratieserver. Standaard wordt automatisch een bijbehorend beleid gemaakt voor failback. Als het replicatie beleid bijvoorbeeld **rep-Policy** is **, wordt er een beleid voor** failbackbeleid gemaakt. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

### <a name="enable-replication"></a>Replicatie inschakelen

- De Mobility-service wordt door Site Recovery geïnstalleerd wanneer replicatie is ingeschakeld.
- Wanneer u replicatie voor een server inschakelt, kan het 15 minuten of langer duren voordat de wijzigingen van kracht worden en worden weer gegeven in de portal.

1. Klik op **Toepassing repliceren** > **Bron**.
2. Selecteer in **Bron** de configuratieserver.
3. Selecteer in **machine type**de optie **fysieke machines**.
4. Selecteer de proces server (de configuratie server). Klik vervolgens op **OK**.
5. Selecteer in **doel**het abonnement en de resource groep waarin u wilt dat de virtuele Azure-machines na een failover worden gemaakt. Kies het implementatie model dat u wilt gebruiken in azure (klassiek of resource beheer).
6. Selecteer het Azure-opslagaccount dat u wilt gebruiken voor het repliceren van gegevens. 
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle machines die u voor beveiliging selecteert. Selecteer **Later configureren** om per machine een Azure-netwerk te selecteren. 
9. Op **fysieke computers**en klik op **+ fysieke machine**. Geef de naam en het IP-adres op. Selecteer het besturings systeem van de computer die u wilt repliceren. Het duurt enkele minuten voordat de servers zijn gedetecteerd en worden weer gegeven. 

   > [!WARNING]
   > U moet het IP-adres invoeren van de virtuele Azure-machine die u wilt verplaatsen

10. Selecteer in **eigenschappen** > **eigenschappen configureren**het account dat door de proces server wordt gebruikt om automatisch de Mobility-service op de computer te installeren.
11. Controleer of het juiste replicatiebeleid is geselecteerd in **Replicatie-instellingen** > **Replicatie-instellingen configureren**. 
12. Klik op **Replicatie inschakelen**. U kunt de voortgang van de taak **Beveiliging inschakelen** volgen via **Instellingen** > **Taken** > **Site Recovery-taken**. Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de machine klaar voor een mogelijke failover.


Als u de servers die u toevoegt wilt bewaken, kunt u de laatst gedetecteerde tijd voor ze controleren op **configuratie servers** > **laatste contact persoon op**. Als u computers wilt toevoegen zonder te wachten op een geplande detectie tijd, markeert u de configuratie server (klik er niet op) en klikt u op **vernieuwen**.

## <a name="test-the-configuration"></a>De configuratie testen


1. Ga naar de kluis, klik in **Instellingen** > **Gerepliceerde items** op de virtuele machine die u wilt verplaatsen naar de doelregio. Klik op het pictogram **+Failover testen**.
2. Selecteer in **Failover testen** een herstelpunt om voor de failover te gebruiken:

   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO (Recovery Time Objective) is
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doelnetwerk waar u de Azure VM's naartoe wilt verplaatsen, om de configuratie te testen. 

   > [!IMPORTANT]
   > We raden u aan om een afzonderlijk Azure VM-netwerk te gebruiken voor de testfailover en niet het productie netwerk waarin u de virtuele machines wilt verplaatsen die u uiteindelijk hebt ingesteld toen u replicatie inschakelde.

4. Klik op **OK** om de verplaatsing te testen. Klik op de VM om de eigenschappen ervan te openen als u de voortgang wilt volgen. U kunt ook op de taak **Failover testen** klikken in kluisnaam > **Instellingen** > **Taken** > **Site Recovery-taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de VM wilt verwijderen die is gemaakt als onderdeel van de verplaatsingstest, klikt u op **Failovertest wissen** op het gerepliceerde item. Leg in **Notities** eventuele opmerkingen over de test vast en sla deze op.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Voer de overstap naar de doelregio uit en bevestig dit.

1. Ga naar de kluis, klik in **Instellingen** > **Gerepliceerde items** op de virtuele machine en klik vervolgens op **Failover**.
2. Bij **Failover** selecteert u **Meest recente**. 
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**. 
4. Nadat de taak is voltooid, controleert u of de VM zoals verwacht wordt weergegeven in de Azure-doelregio.
5. Klik in **Gerepliceerde items** met de rechtermuisknop op de virtuele machine > **Doorvoeren**. De verplaatsing naar de doelregio wordt voltooid. Wacht tot de doorvoertaak is voltooid.

## <a name="discard-the-resource-in-the-source-region"></a>Verwijder de resource in de bronregio 

- Navigeer naar de VM.  Klik op **Replicatie uitschakelen**.  Hiermee wordt het proces van het kopiëren van de gegevens voor de VM beëindigd.  

   > [!IMPORTANT]
   > Het is belangrijk dat u deze stap uitvoert om te vermijden dat kosten in rekening worden gebracht voor ASR-replicatie.

Als u van plan bent om een of meer van de bronresources opnieuw te gebruiken, gaat u verder met de volgende reeks stappen.

1. Ga verder met het verwijderen van alle relevante netwerkresources in de bronregio die u hebt vermeld als onderdeel van stap 4 in [De bron-VM’s voorbereiden](#prepare-the-source-vms) 
2. Verwijder het bijbehorende opslagaccount in de bronregio.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-VM verplaatst naar een andere Azure-regio. U kunt nu herstel na noodgevallen configureren voor de verplaatste VM.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen na een migratie](azure-to-azure-quickstart.md)
