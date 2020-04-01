---
title: Azure VM's verplaatsen tussen overheids- en openbare regio's met Azure Site Recovery
description: Gebruik Azure Site Recovery om Azure VM's tussen Azure-overheid en openbare regio's te verplaatsen.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: acaf16e7469b3ea4e5e391db91e37dc76be3b261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298527"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure-VM's verplaatsen tussen Azure Government-regio's en openbare regio's 

U uw IaaS VM's verplaatsen tussen Azure Government en Public-regio's om de beschikbaarheid van uw bestaande VM's te vergroten, de beheerbaarheid te verbeteren of om governanceredenen, zoals [hier](azure-to-azure-move-overview.md)beschreven.

Behalve dat u de service [Azure Site Recovery](site-recovery-overview.md) kunt gebruiken om herstel na noodgevallen van on-premises machines en Azure-VM’s te beheren en te organiseren met als doel de bedrijfscontinuïteit te waarborgen, en deze service kunt gebruiken voor herstel na een noodgeval (BCDR), kunt u Site Recovery ook gebruiken om Azure-VM’s te verplaatsen naar een secundaire regio.       

In deze zelfstudie ziet u hoe u Azure VM's verplaatst tussen Azure Government en Openbare regio's met Azure Site Recovery. Hetzelfde kan worden uitgebreid om VM's te verplaatsen tussen regioparen die zich niet binnen hetzelfde geografische cluster bevinden. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten verifiëren
> * De bron-VM’s voorbereiden
> * De doelregio voorbereiden
> * Gegevens kopiëren naar de doelregio
> * De configuratie testen
> * De verplaatsing uitvoeren
> * De resources in de bronregio verwijderen

> [!IMPORTANT]
> In deze zelfstudie ziet u hoe u Azure VM's verplaatst tussen Azure Government en Public-regio's of tussen regio's die niet worden ondersteund door de reguliere oplossing voor noodherstel voor Azure VM's. In het geval dat uw bron- en doelregioparen [worden ondersteund,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)verwijzen we u naar dit [document](azure-to-azure-tutorial-migrate.md) voor de verhuizing. Als het uw vereiste is om de beschikbaarheid te verbeteren door VM's te verplaatsen in een beschikbaarheidsset naar zone vastgemaakte VM's in een andere regio, raadpleegt u [de zelfstudie hier](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Het is niet raadzaam om deze methode te gebruiken om DR te configureren tussen niet-ondersteunde regioparen, omdat de paren zijn gedefinieerd om de latentie van gegevens in het achterhoofd te houden, wat essentieel is voor een DR-scenario.

## <a name="verify-prerequisites"></a>Vereisten verifiëren

> [!NOTE]
> Zorg ervoor dat u de [architectuur en componenten](physical-azure-architecture.md) voor dit scenario begrijpt. Deze architectuur wordt gebruikt om Azure VM's te **verplaatsen, door de VM's als fysieke servers te behandelen.**

- Raadpleeg de [ondersteuningsvereisten](vmware-physical-secondary-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat de servers die u wilt repliceren voldoen aan [de Azure VM-vereisten.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
- Maak een account voor voor de automatische installatie van de Mobiliteitsservice op elke server die u wilt repliceren.

- Houd er rekening mee dat nadat u niet meer naar het doelgebied in Azure bent overgelaten, u niet direct een fail back uitvoeren naar het brongebied. U moet replicatie opnieuw instellen terug naar het doel.

### <a name="verify-azure-account-permissions"></a>Machtigingen voor Azure-accounts verifiëren

Controleer of uw Azure-account machtigingen heeft voor replicatie van VM's naar Azure.

- Bekijk de [machtigingen die](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) u nodig hebt om machines te repliceren naar Azure.
- Verificatie en wijziging [van op rollen gebaseerde toegangsmachtigingen.](../role-based-access-control/role-assignments-portal.md) 

### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Stel een het doel [Azure-netwerk](../virtual-network/quick-create-portal.md)in.

- Azure VM's worden in dit netwerk geplaatst wanneer ze zijn gemaakt na failover.
- Het netwerk moet zich in dezelfde regio bevinden als de kluis Van Herstelservices


### <a name="set-up-an-azure-storage-account"></a>Een Azure-opslagaccount instellen

Een [Azure-opslagaccount](../storage/common/storage-account-create.md)instellen .

- Siteherstel repliceert on-premises machines naar Azure-opslag. Azure VM's worden gemaakt van de opslag na failover optreedt.
- Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.


## <a name="prepare-the-source-vms"></a>De bron-VM’s voorbereiden

### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobiliteitsservice moet zijn geïnstalleerd op elke server die u wilt repliceren. Site recovery installeert deze service automatisch wanneer u replicatie voor de server inschakelt. Als u automatisch wilt installeren, moet u een account voorbereiden dat Site Recovery zal gebruiken om toegang te krijgen tot de server.

- U een domein of lokaal account gebruiken
- Als u voor Windows-VM's geen domeinaccount gebruikt, schakelt u extern gebruikerstoegangsbesturingselement op de lokale machine uit. Voeg hiervoor in het register onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**het DWORD-item **LocalAccountTokenFilterPolicy**toe met een waarde van 1.
- Als u de registervermelding wilt toevoegen om de instelling uit te schakelen vanuit een CLI, typt u het:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Voor Linux moet het account root zijn op de bron Linux-server.


## <a name="prepare-the-target-region"></a>De doelregio voorbereiden

1. Controleer of u een Azure-abonnement hebt waarmee u in staat bent om virtuele machines te maken in de doelregio die wordt gebruikt voor herstel na noodgevallen. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

2. Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning kunnen bieden voor VM’s met grootten die overeenkomen met uw bron-VM’s. Site Recovery kiest voor de doel-VM dezelfde of de dichtstbijzijnde grootte om gegevens naar het doel te kopiëren.

3. Zorg ervoor dat u een doelresource maakt voor elk onderdeel dat is geïdentificeerd in de bronindeling voor netwerken. Dit is belangrijk om ervoor te zorgen dat, na het oversnijden naar de doelregio, uw VM's alle functionaliteit en functies hebben die u in de bron had.

    > [!NOTE]
    > In Azure Site Recovery wordt automatisch een virtueel netwerk gedetecteerd en gemaakt wanneer u replicatie inschakelt voor de bron-VM. U kunt er ook voor kiezen om vooraf een netwerk te maken en dit toe te wijzen aan de VM in de gebruikersstroom om replicatie in te schakelen. Maar alle andere resources moet u zoals hieronder wordt beschreven handmatig maken in de doelregio.

     Raadpleeg de volgende documenten om de meest gebruikte netwerkresources te maken die relevant zijn voor u, op basis van de bron-VM-configuratie.

    - [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load balancers](https://docs.microsoft.com/azure/load-balancer)
    - [Openbaar IP](../virtual-network/virtual-network-public-ip-address.md)
    
    Voor alle andere netwerkonderdelen verwijzen we u naar de [netwerkdocumentatie.](https://docs.microsoft.com/azure/?pivot=products&panel=network)

4. [Maak handmatig een niet-productienetwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in de doelregio als u de configuratie wilt testen voordat u het uiteindelijke knippen naar de doelregio uitvoert. Hiermee ontstaat er slechts minimale verstoring in de productie. Dit wordt aanbevolen.

## <a name="copy-data-to-the-target-region"></a>Gegevens kopiëren naar de doelregio
De onderstaande stappen begeleiden u bij het gebruik van Azure Site Recovery om gegevens te kopiëren naar de doelregio.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Maak de kluis in elke gewenste regio, met uitzondering van de bronregio.

1. Meld u aan bij de [Azure portal](https://portal.azure.com) > **Recovery Services**.
2. Klik op**Back-up- en siteherstel****van hulpprogramma's** > voor **bronnenbeheer** > maken .
3. Bij **Naam** geeft u de beschrijvende naam **ContosoVMVault** op. Als u meer dan één abonnement hebt, selecteert u het juiste abonnement.
4. Maak een resourcegroep met de naam **ContosoRG**.
5. Geef een Azure-regio op. Zie Geografische beschikbaarheid in [de prijsdetails van Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Klik in vaults van Recovery Services op **Overzicht** > **ConsotoVMVault** > **+Repliceren**
7. Selecteer **Naar Azure** > **Niet gevirtualiseerd/Ander**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Stel de configuratieserver in om VM's te detecteren.


Stel de configuratieserver in, registreer deze in de kluis en ontdek VM's.

1. Klik**op Infrastructuurbron voor voorbereiden van** >  **siteherstel** > **.**
2. Als u geen configuratieserver hebt, klikt u op **+Configuratieserver**.
3. Controleer in **Server toevoegen**of **configuratieserver** wordt weergegeven in **Servertype**.
4. Download het installatiebestand Site Recovery Unified Setup.
5. Download de registratiesleutel voor de kluis. Dit heb je nodig als je Unified Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

   ![Bron instellen](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>De configuratieserver registreren in de kluis

Ga als volgt te werk voordat u begint: 

#### <a name="verify-time-accuracy"></a>Tijdsnauwkeurigheid controleren
Controleer op de configuratieservermachine of de systeemklok is gesynchroniseerd met een [tijdserver.](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) Het zou moeten overeenkomen. Als het 15 minuten voor of achter, setup kan mislukken.

#### <a name="verify-connectivity"></a>Connectiviteit verifiëren
Zorg ervoor dat de machine toegang heeft tot deze URL's op basis van uw omgeving: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Ip-adresgebaseerde firewallregels moeten communicatie mogelijk maken naar alle Azure-URL's die hierboven zijn vermeld via https-poort (443). Om de IP-bereiken te vereenvoudigen en te beperken, wordt aanbevolen URL-filtering uit te geven.

- **Commerciële IP's** - Sta de [IP-bereiken van Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)en de HTTPS-poort (443) toe. Sta IP-adresbereiken toe voor het Azure-gebied van uw abonnement ter ondersteuning van de URL's AAD, Back-up, Replicatie en Opslag.  
- **Ip-ups van** de overheid - Sta de [IP-bereiken van Azure Government Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=57063)en de HTTPS-poort (443) voor alle USGov-regio's (Virginia, Texas, Arizona en Iowa) toe om AAD-, back-up-, replicatie- en opslag-URL's te ondersteunen.  

#### <a name="run-setup"></a>Voer het installatieprogramma uit
Voer Unified Setup uit als lokale beheerder om de configuratieserver te installeren. De processerver en de hoofddoelserver zijn ook standaard geïnstalleerd op de configuratieserver.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Nadat de registratie is voltooid, wordt de configuratieserver weergegeven op de pagina > **Instellingenservers** in de kluis. **Settings**

### <a name="configure-target-settings-for-replication"></a>Doelinstellingen configureren voor replicatie

Selecteer en controleer doelbronnen.

1. Klik **op Infrastructuurdoel** > **Target**voorbereiden en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het doelimplementatiemodel op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

   ![Doel](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Als u een nieuw replicatiebeleid wilt maken, klikt u op**Replicatiebeleid** >  **voor siteherstelinfrastructuur** > **+Replicatiebeleid**.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op. Deze waarde geeft aan hoe vaak gegevensherstelpunten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
4. Geef in **Bewaarperiode van het herstelpunt** op hoelang (in uren) de bewaarperiode voor elk herstelpunt is. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster. Voor computers die worden gerepliceerd naar Premium Storage, wordt een bewaarperiode van maximaal 24 uur ondersteund, en 72 uur voor computers die naar Standard Storage worden gerepliceerd.
5. Geef in **app-consistente momentopnamefrequentie**op hoe vaak (in minuten) herstelpunten met toepassingsconsistente momentopnamen worden gemaakt. Klik op **OK** om het beleid te maken.

    ![Beleid voor replicatie](./media/physical-azure-disaster-recovery/replication-policy.png)


Het beleid wordt automatisch gekoppeld aan de configuratieserver. Standaard wordt automatisch een bijbehorend beleid gemaakt voor failback. Als het replicatiebeleid bijvoorbeeld **rep-beleid** is, wordt er een **failback-policy rep-policy-failback** gemaakt. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

### <a name="enable-replication"></a>Replicatie inschakelen

- Site recovery installeert de Mobiliteitsservice wanneer replicatie is ingeschakeld.
- Wanneer u replicatie inschakelt voor een server, kan het 15 minuten of langer duren voordat wijzigingen van kracht worden en in de portal worden weergegeven.

1. Klik **op Toepassingsbron** > **Source**repliceren .
2. Selecteer in **Bron** de configuratieserver.
3. Selecteer **Fysieke machines**in **Machinetype**.
4. Selecteer de processerver (de configuratieserver). Klik vervolgens op **OK**.
5. Selecteer **in Target**het abonnement en de resourcegroep waarin u de Azure VM's wilt maken na een failover. Kies het implementatiemodel dat u wilt gebruiken in Azure (klassiek of resourcebeheer).
6. Selecteer het Azure-opslagaccount dat u wilt gebruiken voor het repliceren van gegevens. 
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines**om de netwerkinstelling toe te passen op alle machines die u selecteert voor bescherming. Selecteer **Later configureren** om het Azure-netwerk per machine te selecteren. 
9. Klik **in fysieke machines**en klik op **+Fysieke machine**. Geef de naam en het IP-adres op. Selecteer het besturingssysteem van de machine die u wilt repliceren. Het duurt een paar minuten voor de servers worden ontdekt en vermeld. 

   > [!WARNING]
   > U moet het IP-adres invoeren van de Azure VM die u wilt verplaatsen

10. Selecteer in**Eigenschappen Eigenschappen configureren**het account dat door de processerver wordt gebruikt om de Mobiliteitsservice automatisch op de machine te installeren. **Properties** > 
11. Controleer in **Replicatieinstellingen** > **Replicatie-instellingen**configureren of het juiste replicatiebeleid is geselecteerd. 
12. Klik op **Replicatie inschakelen**. U de voortgang van de taak **Beveiliging inschakelen** bijhouden in **vacatures** > voor**hersteltaken****van vacatures** > . Nadat de taak **Beveiliging afte ronden** is uitgevoerd, is de machine klaar voor failover.


Als u servers wilt controleren die u toevoegt, u de laatst ontdekte tijd voor deze servers controleren in **Configuration Servers** > **Last Contact At**. Als u machines wilt toevoegen zonder te wachten op een geplande detectietijd, markeert u de configuratieserver (klik er niet op) en klikt u op **Vernieuwen**.

## <a name="test-the-configuration"></a>De configuratie testen


1. Navigeer naar de kluis, klik in**Gerepliceerde items** **instellingen** > op de virtuele machine die u naar het doelgebied wilt verplaatsen, klik op het pictogram **Failover testen.**
2. Selecteer in **Failover testen** een herstelpunt om voor de failover te gebruiken:

   - **Laatst verwerkte**: voert een failover van de VM uit naar het laatste herstelpunt dat is verwerkt door de Site Recovery-service. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO (Recovery Time Objective) is
   - **Laatste toepassingsconsistente punt**: met deze optie wordt er een failover uitgevoerd van alle VM’s naar het laatste toepassingsconsistente herstelpunt. Het tijdstempel wordt weergegeven.
   - **Aangepast**: selecteer een herstelpunt.

3. Selecteer het Azure-doelnetwerk waar u de Azure VM's naartoe wilt verplaatsen, om de configuratie te testen. 

   > [!IMPORTANT]
   > We raden u aan een apart Azure VM-netwerk te gebruiken voor de testfailover en niet voor het productienetwerk waarin u uw VM's uiteindelijk wilt verplaatsen die zijn ingesteld toen u replicatie hebt ingeschakeld.

4. Klik op **OK** om de verplaatsing te testen. Klik op de VM om de eigenschappen ervan te openen als u de voortgang wilt volgen. U kunt ook op de taak **Failover testen** klikken in kluisnaam > **Instellingen** > **Taken** > **Site Recovery-taken**.
5. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM draait, de juiste grootte heeft en aangesloten is op het juiste netwerk.
6. Als u de VM wilt verwijderen die is gemaakt als onderdeel van de verplaatsingstest, klikt u op **Failovertest wissen** op het gerepliceerde item. Leg in **Notities** eventuele opmerkingen over de test vast en sla deze op.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Voer de overstap naar de doelregio uit en bevestig dit.

1. Navigeer naar de kluis, klik in**Gerepliceerde items** **instellingen** > op de virtuele machine en klik vervolgens op **Failover**.
2. Bij **Failover** selecteert u **Meest recente**. 
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery sluit de virtuele bronmachine af voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U de failovervoortgang volgen op de pagina **Vacatures.** 
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
