---
title: Herstel na nood gevallen instellen van fysieke on-premises servers met Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen voor Azure voor on-premises Windows-en Linux-servers, met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84699903"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Herstel na nood gevallen naar Azure instellen voor on-premises fysieke servers

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgevallen door de replicatie, failover en failback van on-premises machines en virtuele Azure-machines te beheren en in te delen.

Deze zelf studie laat zien hoe u herstel na nood gevallen instelt voor on-premises fysieke Windows-en Linux-servers naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure-en on-premises vereisten instellen
> * Een Recovery Services-kluis maken voor Site Recovery 
> * De bron-en doel replicatie omgevingen instellen
> * Een replicatiebeleid maken
> * Replicatie inschakelen voor een server

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- Zorg ervoor dat u bekend bent met de [architectuur en onderdelen](physical-azure-architecture.md) voor dit scenario.
- Raadpleeg de [ondersteuningsvereisten](vmware-physical-secondary-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat de servers die u wilt repliceren, voldoen aan de vereisten van de [Azure-VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Azure voorbereiden. U hebt een Azure-abonnement, een virtueel Azure-netwerk en een opslag account nodig.
- Een account voorbereiden voor de automatische installatie van de Mobility-service op elke server die u wilt repliceren.

Houd rekening met het volgende voordat u begint:

- Na een failover naar Azure kunnen fysieke servers niet meer worden teruggezet naar on-premises fysieke machines. U kunt alleen een failback uitvoeren naar virtuele VMware-machines. 
- In deze zelf studie stelt u de nood herstel van fysieke servers in op Azure met de eenvoudigste instellingen. Als u meer wilt weten over andere opties, lees dan onze hand leidingen:
    - Stel de [replicatie bron](physical-azure-set-up-source.md)in, met inbegrip van de site Recovery configuratie server.
    - Stel het [replicatiedoel](physical-azure-set-up-target.md) in.
    - Configureer een [replicatiebeleid](vmware-azure-set-up-replication.md) en schakel [replicatie in](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Een Azure-account instellen

Een micro soft [Azure-account](https://azure.microsoft.com/)ophalen.

- U kunt beginnen met een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
- Meer informatie over de [prijzen van site Recovery](site-recovery-faq.md#pricing)en ontvang [prijs informatie](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ontdek welke [regio's worden ondersteund](https://azure.microsoft.com/pricing/details/site-recovery/) voor site Recovery.

### <a name="verify-azure-account-permissions"></a>Controleer de machtigingen voor het Azure-account

Zorg ervoor dat uw Azure-account machtigingen heeft voor de replicatie van virtuele machines naar Azure.

- Bekijk de [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) die u nodig hebt om machines te repliceren naar Azure.
- Controleer en wijzig [op rollen gebaseerde toegangs](../role-based-access-control/role-assignments-portal.md) machtigingen. 



### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Stel een [Azure-netwerk](../virtual-network/quick-create-portal.md)in.

- Virtuele Azure-machines worden in dit netwerk geplaatst wanneer ze na een failover worden gemaakt.
- Het netwerk moet zich in dezelfde regio bevinden als de Recovery Services kluis


## <a name="set-up-an-azure-storage-account"></a>Een Azure-opslagaccount instellen

Stel een [Azure Storage-account](../storage/common/storage-account-create.md)in.

- Site Recovery repliceert on-premises machines naar Azure Storage. Virtuele Azure-machines worden gemaakt op basis van de opslag nadat de failover is uitgevoerd.
- Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet worden geïnstalleerd op elke server die u wilt repliceren. Site Recovery installeert deze service automatisch wanneer u replicatie inschakelt voor de-server. Als u automatisch wilt installeren, moet u een account voorbereiden dat Site Recovery gebruikt voor toegang tot de server.

- U kunt een domein-of lokaal account gebruiken
- Als u voor Windows-Vm's geen domein account gebruikt, schakelt u toegangs beheer voor externe gebruikers op de lokale computer uit. Als u dit wilt doen, voegt u in het REGI ster onder **HKEY_LOCAL_MACHINE \Software\Microsoft\Windows\CurrentVersion\Policies\System**de DWORD-vermelding **LocalAccountTokenFilterPolicy**met de waarde 1 toe.
- Als u de register vermelding wilt toevoegen om de instelling uit een CLI uit te scha kelen, typt u:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Voor Linux moet het account hoofdmap zijn op de Linux-bron server.


## <a name="create-a-vault"></a>Een kluis maken

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

Selecteer wat u wilt repliceren en waarnaar u het wilt repliceren.

1. Klik op **Recovery Services-kluizen** > kluis.
2. Klik in het menu resource op **site Recovery**  >  **Prepare Infrastructure**  >  **doel infrastructuur beveiliging**voorbereiden.
3. Selecteer in **doel van beveiliging**de optie **naar Azure**  >  **niet gevirtualiseerd/Overig**.

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

Stel de configuratie server in, Registreer deze in de kluis en ontdek Vm's.

1. Klik op **site Recovery**bron voor het voorbereiden van de  >  **infra structuur**  >  **Source**.
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

Nadat de registratie is voltooid, wordt de configuratie server weer gegeven op de pagina **instellingen**  >  **servers** in de kluis.

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Klik op **infra structuur voorbereiden**  >  **doel**en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het doel implementatie model op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

   ![Doel](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Als u een nieuw replicatie beleid wilt maken, klikt u op **site Recovery infrastructuur**beleid voor replicatie en beleid voor replicatie  >  **Replication Policies**  >  **+Replication Policy**.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op. Met deze waarde wordt bepaald hoe vaak gegevens herstel punten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
4. Geef in **Bewaarperiode van het herstelpunt** op hoelang (in uren) de bewaarperiode voor elk herstelpunt is. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster. Voor computers die worden gerepliceerd naar Premium Storage, wordt een bewaarperiode van maximaal 24 uur ondersteund, en 72 uur voor computers die naar Standard Storage worden gerepliceerd.
5. Geef in de frequentie van de **app-consistente moment opname**op hoe vaak (in minuten) er herstel punten moeten worden gemaakt met toepassings consistente moment opnamen. Klik op **OK** om het beleid te maken.

    ![Beleid voor replicatie](./media/physical-azure-disaster-recovery/replication-policy.png)


Het beleid wordt automatisch gekoppeld aan de configuratieserver. Standaard wordt automatisch een bijbehorend beleid gemaakt voor failback. Als het replicatie beleid bijvoorbeeld **rep-Policy** is **, wordt er een beleid voor** failbackbeleid gemaakt. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie in voor elke server.

- De Mobility-service wordt door Site Recovery geïnstalleerd wanneer replicatie is ingeschakeld.
- Wanneer u replicatie voor een server inschakelt, kan het 15 minuten of langer duren voordat de wijzigingen van kracht worden en worden weer gegeven in de portal.

1. Klik op **toepassings**  >  **bron**repliceren.
2. Selecteer in **Bron** de configuratieserver.
3. Selecteer in **machine type**de optie **fysieke machines**.
4. Selecteer de proces server (de configuratie server). Klik vervolgens op **OK**.
5. Selecteer in **doel**het abonnement en de resource groep waarin u wilt dat de virtuele Azure-machines na een failover worden gemaakt. Kies het implementatie model dat u wilt gebruiken in azure (klassiek of resource beheer).
6. Selecteer het Azure-opslagaccount dat u wilt gebruiken voor het repliceren van gegevens. 
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **nu configureren voor geselecteerde machines**om de netwerk instelling toe te passen op alle machines die u voor beveiliging selecteert. Selecteer **later configureren** om het Azure-netwerk per computer te selecteren. 
9. Op **fysieke computers**en klik op **+ fysieke machine**. Geef de naam en het IP-adres op. Selecteer het besturings systeem van de computer die u wilt repliceren. Het duurt enkele minuten voordat de servers zijn gedetecteerd en worden weer gegeven. 
10. Selecteer in **Eigenschappen**  >  **eigenschappen configureren**het account dat door de proces server wordt gebruikt om automatisch de Mobility-service op de computer te installeren.
11. Controleer in **replicatie-instellingen**  >  **replicatie-instellingen configureren**of het juiste replicatie beleid is geselecteerd. 
12. Klik op **Replicatie inschakelen**. U kunt de voortgang van de taak **beveiliging inschakelen** volgen via **instellingen**  >  **taken**  >  **site Recovery taken**. Nadat de taak **beveiliging volt ooien** is uitgevoerd, is de machine klaar voor failover.


Als u de servers die u toevoegt wilt bewaken, kunt u de laatst gedetecteerde tijd voor ze controleren in **configuratie servers**  >  **laatste contact op**. Als u computers wilt toevoegen zonder te wachten op een geplande detectie tijd, markeert u de configuratie server (klik er niet op) en klikt u op **vernieuwen**.

## <a name="next-steps"></a>Volgende stappen

[Voer een nood herstel analyse uit](tutorial-dr-drill-azure.md).
