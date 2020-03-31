---
title: Noodherstel van fysieke on-premises servers instellen met Azure Site Recovery
description: Meer informatie over het instellen van noodherstel op Azure voor on-premises Windows- en Linux-servers, met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257925"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Disaster recovery instellen voor Azure voor on-premises fysieke servers

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgevallen door de replicatie, failover en failback van on-premises machines en virtuele Azure-machines te beheren en in te delen.

In deze zelfstudie ziet u hoe u noodherstel van on-premises fysieke Windows- en Linux-servers in Stelt op Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure- en on-premises vereisten instellen
> * Een Recovery Services-kluis maken voor Site Recovery 
> * De bron- en doelreplicatieomgevingen instellen
> * Een replicatiebeleid maken
> * Replicatie inschakelen voor een server

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

- Zorg ervoor dat u de [architectuur en componenten](physical-azure-architecture.md) voor dit scenario begrijpt.
- Raadpleeg de [ondersteuningsvereisten](vmware-physical-secondary-support-matrix.md) voor alle onderdelen.
- Zorg ervoor dat de servers die u wilt repliceren voldoen aan [de Azure VM-vereisten.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
- Azure voorbereiden. U hebt een Azure-abonnement, een virtueel Azure-netwerk en een opslagaccount nodig.
- Maak een account voor voor de automatische installatie van de Mobiliteitsservice op elke server die u wilt repliceren.

Voordat u begint, moet u er rekening mee houden dat:

- Na een fail-over naar Azure kunnen fysieke servers niet worden teruggezet naar on-premises fysieke machines. U alleen niet terug naar VMware VM's. 
- In deze zelfstudie wordt het herstel van fysieke serverramp ingesteld op Azure met de eenvoudigste instellingen. Als u meer wilt weten over andere opties, leest u onze handleidingen Voor How To:
    - Stel de [replicatiebron](physical-azure-set-up-source.md)in, inclusief de configuratieserver siteherstel.
    - Stel het [replicatiedoel](physical-azure-set-up-target.md) in.
    - Configureer een [replicatiebeleid](vmware-azure-set-up-replication.md) en schakel [replicatie in](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Een Azure-account instellen

Een Microsoft [Azure-account opdoen.](https://azure.microsoft.com/)

- U kunt beginnen met een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
- Meer informatie over [de prijzen voor siteherstel](site-recovery-faq.md#pricing)en het opvragen [van prijsgegevens](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ontdek welke [regio's worden ondersteund](https://azure.microsoft.com/pricing/details/site-recovery/) voor siteherstel.

### <a name="verify-azure-account-permissions"></a>Machtigingen voor Azure-accounts verifiëren

Controleer of uw Azure-account machtigingen heeft voor replicatie van VM's naar Azure.

- Bekijk de [machtigingen die](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) u nodig hebt om machines te repliceren naar Azure.
- Verificatie en wijziging [van op rollen gebaseerde toegangsmachtigingen.](../role-based-access-control/role-assignments-portal.md) 



### <a name="set-up-an-azure-network"></a>Een Azure-netwerk instellen

Een [Azure-netwerk](../virtual-network/quick-create-portal.md)instellen .

- Azure VM's worden in dit netwerk geplaatst wanneer ze zijn gemaakt na failover.
- Het netwerk moet zich in dezelfde regio bevinden als de kluis Van Herstelservices


## <a name="set-up-an-azure-storage-account"></a>Een Azure-opslagaccount instellen

Een [Azure-opslagaccount](../storage/common/storage-account-create.md)instellen .

- Siteherstel repliceert on-premises machines naar Azure-opslag. Azure VM's worden gemaakt van de opslag na failover optreedt.
- Het opslagaccount moet zich in dezelfde regio bevinden als de Recovery Services-kluis.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobiliteitsservice moet zijn geïnstalleerd op elke server die u wilt repliceren. Site recovery installeert deze service automatisch wanneer u replicatie voor de server inschakelt. Als u automatisch wilt installeren, moet u een account voorbereiden dat Site Recovery zal gebruiken om toegang te krijgen tot de server.

- U een domein of lokaal account gebruiken
- Als u voor Windows-VM's geen domeinaccount gebruikt, schakelt u extern gebruikerstoegangsbesturingselement op de lokale machine uit. Voeg hiervoor in het register onder **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**het DWORD-item **LocalAccountTokenFilterPolicy**toe met een waarde van 1.
- Als u de registervermelding wilt toevoegen om de instelling uit te schakelen vanuit een CLI, typt u het:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Voor Linux moet het account root zijn op de bron Linux-server.


## <a name="create-a-vault"></a>Een kluis maken

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

Selecteer wat u wilt repliceren en repliceren.

1. Klik op **Recovery Services-kluizen** > kluis.
2. Klik in het menu Resource op Doel > **Infrastructuurbeveiliging** > **Protection goal**voorbereiden van **siteherstel**.
3. Selecteer **in het doel Beveiliging**de optie **Azure** > **Niet gevirtualiseerd/Ander**.

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

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

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Klik **op Infrastructuurdoel** > **Target**voorbereiden en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het doelimplementatiemodel op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt.

   ![Doel](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Als u een nieuw replicatiebeleid wilt maken, klikt u op**Replicatiebeleid** >  **voor siteherstelinfrastructuur** > **+Replicatiebeleid**.
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op. Deze waarde geeft aan hoe vaak gegevensherstelpunten worden gemaakt. Wanneer de continue replicatie deze limiet overschrijdt, wordt er een waarschuwing gegenereerd.
4. Geef in **Bewaarperiode van het herstelpunt** op hoelang (in uren) de bewaarperiode voor elk herstelpunt is. Gerepliceerde VM’s kunnen worden hersteld naar een willekeurig punt in een tijdvenster. Voor computers die worden gerepliceerd naar Premium Storage, wordt een bewaarperiode van maximaal 24 uur ondersteund, en 72 uur voor computers die naar Standard Storage worden gerepliceerd.
5. Geef in **app-consistente momentopnamefrequentie**op hoe vaak (in minuten) herstelpunten met toepassingsconsistente momentopnamen worden gemaakt. Klik op **OK** om het beleid te maken.

    ![Beleid voor replicatie](./media/physical-azure-disaster-recovery/replication-policy.png)


Het beleid wordt automatisch gekoppeld aan de configuratieserver. Standaard wordt automatisch een bijbehorend beleid gemaakt voor failback. Als het replicatiebeleid bijvoorbeeld **rep-beleid** is, wordt er een **failback-policy rep-policy-failback** gemaakt. Dit beleid wordt pas gebruikt als u een failback initieert vanuit Azure.

## <a name="enable-replication"></a>Replicatie inschakelen

Replicatie voor elke server inschakelen.

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
10. Selecteer in**Eigenschappen Eigenschappen configureren**het account dat door de processerver wordt gebruikt om de Mobiliteitsservice automatisch op de machine te installeren. **Properties** > 
11. Controleer in **Replicatieinstellingen** > **Replicatie-instellingen**configureren of het juiste replicatiebeleid is geselecteerd. 
12. Klik op **Replicatie inschakelen**. U de voortgang van de taak **Beveiliging inschakelen** bijhouden in **vacatures** > voor**hersteltaken****van vacatures** > . Nadat de taak **Beveiliging afte ronden** is uitgevoerd, is de machine klaar voor failover.


Als u servers wilt controleren die u toevoegt, u de laatst ontdekte tijd voor deze servers controleren in **Configuration Servers** > **Last Contact At**. Als u machines wilt toevoegen zonder te wachten op een geplande detectietijd, markeert u de configuratieserver (klik er niet op) en klikt u op **Vernieuwen**.

## <a name="next-steps"></a>Volgende stappen

[Voer een noodhersteloefening uit.](tutorial-dr-drill-azure.md)
