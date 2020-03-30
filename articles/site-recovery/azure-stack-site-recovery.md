---
title: Azure Stack VM's repliceren naar Azure met Azure Site Recovery | Microsoft Documenten
description: Meer informatie over het instellen van noodherstel voor Azure voor Azure Stack VM's met de Azure Site Recovery-service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 15cd729063545914f791de39a075af9084f72bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426572"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Virtuele Azure-machines repliceren naar Azure

In dit artikel ziet u hoe u Azure Stack VM's voor noodherstel instelt op Azure, met behulp van de [Azure Site Recovery-service](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Site Recovery draagt bij aan uw business continuity en disaster recovery (BCDR) strategie. De service zorgt ervoor dat uw VM-workloads beschikbaar blijven wanneer er onverwachte storingen optreden.

- Siteherstel orkesteert en beheert replicatie van VM's naar Azure-opslag.
- Wanneer er een storing optreedt op uw primaire site, gebruikt u Siteherstel om niet over te geven aan Azure.
- Bij failover worden Azure VM's gemaakt op basis van de opgeslagen VM-gegevens en kunnen gebruikers toegang blijven krijgen tot workloads die op die Azure VM's worden uitgevoerd.
- Wanneer alles weer in gebruik is, u Azure VM's weer naar uw primaire site terugsturen en opnieuw beginnen met repliceren naar Azure-opslag.


In dit artikel leert u het volgende:

> [!div class="checklist"]
> * **Stap 1: Azure stack VM's voorbereiden op replicatie.** Controleer of VM's voldoen aan de vereisten voor siteherstel en bereid u voor op de installatie van de site recovery Mobility-service. Deze service is geïnstalleerd op elke vm die u wilt repliceren.
> * **Stap 2: Een kluis van Recovery Services instellen.** Stel een kluis in voor Siteherstel en geef op wat u wilt repliceren. Siteherstelcomponenten en -acties worden geconfigureerd en beheerd in de kluis.
> * **Stap 3: De bronreplicatieomgeving instellen.** Stel een configuratieserver voor siteherstel in. De configuratieserver is één Azure Stack VM die alle componenten uitvoert die nodig zijn voor Site Recovery. Nadat u de configuratieserver hebt ingesteld, registreert u deze in de kluis.
> * **Stap 4: De doelreplicatieomgeving instellen.** Selecteer uw Azure-account en het Azure-opslagaccount en -netwerk dat u wilt gebruiken. Tijdens de replicatie worden VM-gegevens gekopieerd naar Azure-opslag. Na failover worden Azure VM's samengevoegd met het opgegeven netwerk.
> * **Stap 5: Replicatie inschakelen**. Configureer replicatie-instellingen en schakel replicatie voor VM's in. De Mobiliteitsservice wordt op een VM geïnstalleerd wanneer replicatie is ingeschakeld. Siteherstel voert een eerste replicatie van de VM uit en begint vervolgens met de lopende replicatie.
> * **Stap 6: Voer een noodhersteloefening uit:** Nadat de replicatie is uitgevoerd, controleert u of failover werkt zoals verwacht door een drill uit te voeren. Als u de drill wilt starten, voert u een testfailover uit in Site recovery. De testfailover heeft geen invloed op uw productieomgeving.

Als deze stappen zijn voltooid, u vervolgens een volledige failover naar Azure uitvoeren wanneer dat nodig is.

## <a name="architecture"></a>Architectuur

![Architectuur](./media/azure-stack-site-recovery/architecture.png)

**Locatie** | **Component** |**Details**
--- | --- | ---
**Configuratieserver** | Wordt uitgevoerd op één Azure Stack VM. | In elk abonnement stelt u een vm van de configuratieserver in. Met deze VM worden de volgende siteherstelcomponenten uitgevoerd:<br/><br/> - Configuratieserver: coördineert de communicatie tussen on-premises en Azure en beheert gegevensreplicatie. - Processerver: fungeert als replicatiegateway. Het ontvangt replicatiegegevens, optimaliseert met caching, compressie en versleuteling; en stuurt het naar Azure-opslag.<br/><br/> Als VM's die u wilt repliceren de onderstaande limieten overschrijden, u een aparte zelfstandige processerver instellen. [Meer informatie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Mobiliteitsdienst** | Geïnstalleerd op elke vm die u wilt repliceren. | In de stappen in dit artikel stellen we een account op zodat de Mobiliteitsservice automatisch op een vm wordt geïnstalleerd wanneer replicatie is ingeschakeld. Als u de service niet automatisch wilt installeren, zijn er een aantal andere methoden die u gebruiken. [Meer informatie](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | In Azure hebt u een Vault recovery services, een opslagaccount en een virtueel netwerk nodig. |  Gerepliceerde gegevens worden opgeslagen in het opslagaccount. Azure VM's worden toegevoegd aan het Azure-netwerk wanneer er een fail-over plaatsvindt. 


Replicatie werkt als volgt:

1. In de kluis geeft u de replicatiebron en het doel op, stelt u de configuratieserver in, maakt u een replicatiebeleid en schakelt u replicatie in.
2. De Mobiliteitsservice is geïnstalleerd op de machine (als u push-installatie hebt gebruikt) en machines beginnen met replicatie in overeenstemming met het replicatiebeleid.
3. Een eerste kopie van de servergegevens wordt gerepliceerd naar Azure-opslag.
4. Nadat de eerste replicatie is voltooid, wordt de replicatie van deltawijzigingen in Azure gestart. Bijgehouden wijzigingen voor een machine worden opgeslagen in een .hrl-bestand.
5. De configuratieserver orkestreert replicatiebeheer met Azure (poort HTTPS 443 uitgaand).
6. De processerver ontvangt gegevens van bronmachines, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag (poort 443 uitgaand).
7. Gerepliceerde machines communiceren met de configuratieserver (poort HTTPS 443 binnenkomend, voor replicatiebeheer. Machines sturen replicatiegegevens naar de processerver (poort HTTPS 9443 inkomende - kan worden gewijzigd).
8. Verkeer wordt via internet gerepliceerd naar de openbare eindpunten van Azure Storage. U kunt ook openbare peering van Azure ExpressRoute gebruiken. Het repliceren van verkeer via een site-naar-site-VPN van een on-premises site naar Azure wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Dit is wat u nodig hebt om dit scenario in te stellen.

**Vereiste** | **Details**
--- | ---
**Azure-abonnementsaccount** | Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/)aan.
**Azure-accountmachtigingen** | Het Azure-account dat u gebruikt, heeft machtigingen nodig om:<br/><br/> - Een kluis van de herstelservice maken<br/><br/> - Een virtuele machine maken in de resourcegroep en het virtuele netwerk dat u voor het scenario gebruikt<br/><br/> - Schrijf naar het opslagaccount dat u opgeeft<br/><br/> Opmerking:<br/><br/> -Als u een account maakt, bent u de beheerder van uw abonnement en u alle acties uitvoeren.<br/><br/> - Als u een bestaand abonnement gebruikt en u bent niet de beheerder, moet u samenwerken met de beheerder om u machtigingen voor eigenaar of inzender toe te wijzen.<br/><br/> - Als je meer gedetailleerde machtigingen nodig hebt, bekijk [dan dit artikel](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Azure Stack VM** | U hebt een Azure Stack VM nodig in het tenantabonnement, dat wordt geïmplementeerd als de configuratieserver siteherstel. 


### <a name="prerequisites-for-the-configuration-server"></a>Vereisten voor de configuratieserver

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Stap 1: Azure Stack VM's voorbereiden

### <a name="verify-the-operating-system"></a>Het besturingssysteem verifiëren

Zorg ervoor dat de VM's een van de besturingssystemen uitvoeren die in de tabel zijn samengevat.


**Besturingssysteem** | **Details**
--- | ---
**64-bits Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (vanaf SP1)
**Centos** | 5.2 tot 5.11, 6.1 tot 6.9, 7.0 tot 7.3
**Ubuntu** | 14.04 LTS server, 16.04 LTS server. [Ondersteunde kernels bekijken](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Voorbereiden op mobility-service-installatie

Elke vm die u wilt repliceren, moet de Mobiliteitsservice hebben geïnstalleerd. Controleer de VM-instellingen om ervoor te zorgen dat de processerver de service automatisch op de virtuele machine installeert wanneer replicatie is ingeschakeld.

#### <a name="windows-machines"></a>Windows-machines

- U hebt netwerkconnectiviteit nodig tussen de VM waarop u replicatie wilt inschakelen en de machine waarop de processerver wordt uitgevoerd (dit is standaard de vm van de configuratieserver).
- U hebt een account nodig met beheerdersrechten (domein of lokaal) op de machine waarvoor u replicatie inschakelt.
    - U geeft dit account op wanneer u Siteherstel instelt. Vervolgens gebruikt de processerver dit account om de Mobiliteitsservice te installeren wanneer replicatie is ingeschakeld.
    - Dit account wordt alleen gebruikt door Site Recovery voor de push-installatie en om de Mobiliteitsservice bij te werken.
    - Als u geen domeinaccount gebruikt, moet u het besturingselement voor rasgebruikerstoegang op de VM uitschakelen:
        - Maak in het register DWORD-waarde **LocalAccountTokenFilterPolicy** onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Stel de waarde in op 1.
        - Typ hiervoor het volgende: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- In de Windows Firewall op de VM die u wilt repliceren, u het delen van bestanden en printers en WMI toestaan.
    - Voer hiervoor **wf.msc** uit om de Windows Firewall-console te openen. Klik met de rechtermuisknop op**Nieuwe regel** **inkomende regels** > . Selecteer **Vooraf gedefinieerd**en kies Delen van bestanden en **printers** in de lijst. Voltooi de wizard en selecteer de verbinding > **Voltooien**.
    - Voor domeincomputers u hiervoor een GPO gebruiken.

    
#### <a name="linux-machines"></a>Linux machines

- Zorg dat er een netwerkverbinding is tussen de Linux-computer en de processerver.
- Op de machine waarvoor u replicatie inschakelt, hebt u een account nodig dat een hoofdgebruiker is op de bron-Linux-server:
    - U geeft dit account op wanneer u Siteherstel instelt. Vervolgens gebruikt de processerver dit account om de Mobiliteitsservice te installeren wanneer replicatie is ingeschakeld.
    - Dit account wordt alleen gebruikt door Site Recovery voor de push-installatie en om de Mobiliteitsservice bij te werken.
- Controleer of het bestand /etc/hosts op de Linux-bronserver vermeldingen bevat die de lokale hostnaam toewijzen aan IP-adressen die zijn gekoppeld aan alle netwerkadapters.
- Installeer de meest recente openssh, openssh-server en openssl-pakketten op de computer die u wilt repliceren.
- Zorg ervoor dat SSH (Secure Shell) is ingeschakeld en wordt uitgevoerd op poort 22.
- Schakel het SFTP-subsysteem en wachtwoordverificatie in het bestand sshd_config in:
    1. Log hiervoor in als root.
    2. Zoek de regel die begint met **PasswordAuthentication,** in het /etc/ssh/sshd_config bestand. Verwijder opmerkingen bij de regel en wijzig de waarde in **ja**.
    3. Zoek de regel die begint met **Subsystem** en verwijder opmerkingen bij de regel.

        ![Linux Mobility service](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Start de service sshd opnieuw.


### <a name="note-the-vm-private-ip-address"></a>Let op het privé-IP-adres van VM

Zoek voor elke machine die u wilt repliceren het IP-adres:

1. Klik in de Azure Stack Portal op de VM.
2. Klik in het menu **Resource** op **Netwerkinterfaces**.
3. Noteer het privé-IP-adres.

    ![Privé IP-adres](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Stap 2: Een kluis maken en een replicatiedoel selecteren

1. Selecteer in de Azure-portal de optie**Back-up- en siteherstel**van > **hulpprogramma's** >  **voor resources**maken .
2. Voer in **Naam** een beschrijvende naam in om de kluis aan te duiden. 
3. Maak of selecteer in **de groep Resource**een resourcegroep. We gebruiken **contosoRG.**
4. Voer **in Locatie**het Azure-gebied in. gebruiken we **Europa - west**.
5. Als u snel toegang wilt krijgen tot de kluis vanuit het dashboard, selecteert u **Vastmaken aan dashboard** > **Maken**.

   ![Een nieuwe kluis maken](./media/azure-stack-site-recovery/new-vault-settings.png)

   De nieuwe kluis wordt weergegeven op **Dashboard** > **Alle resources**en op de pagina **Overherstelservices.**

### <a name="select-a-replication-goal"></a>Een replicatiedoel selecteren

1. In **Vaults van Recovery Services** > een kluisnaam opgeven. We gebruiken **ContosoVMVault.**
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. Selecteer **On-premises**in **Beschermingsdoel** > **Waar uw machines zich bevinden.**
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. Selecteer **Niet gevirtualiseerd/Overige**in **Zijn uw machines gevirtualiseerd.** Selecteer vervolgens **OK**.

    ![Beveiligingsdoel](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Stap 3: De bronomgeving instellen

Stel de configuratieservermachine in, registreer deze in de kluis en ontdek machines die u wilt repliceren.

1. Klik **op Infrastructuurbron** > **Source**voorbereiden .
2. Klik in **Bron voorbereiden** op **+Configuratieserver**.

    ![Bron instellen](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Controleer in **Server toevoegen**of **configuratieserver** wordt weergegeven in **Servertype**.
5. Download het installatiebestand Site Recovery Unified Setup.
6. Download de registratiesleutel voor de kluis. U hebt de registratiesleutel nodig wanneer u Unified Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Bron instellen](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Installatie van Azure Site Recovery Unified uitvoeren

Als u de configuratieserver wilt installeren en registreren, voert u een RDP-verbinding uit met de VM die u wilt gebruiken voor de configuratieserver en voert u Unified Setup uit.

Controleer voordat u begint of de klok is [gesynchroniseerd met een tijdserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) op de VM voordat u begint. De installatie mislukt als de tijd meer dan vijf minuten lokale tijd is.

Installeer nu de configuratieserver:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> De configuratieserver kan ook vanaf de opdrachtregel worden geïnstalleerd. [Meer informatie](physical-manage-configuration-server.md#install-from-the-command-line).
> 
> Het duurt 15 minuten of langer voordat de accountnaam wordt weergegeven in de portal. Als u deze onmiddellijk wilt bijwerken, selecteert u De***servernaam*** > **Vernieuwserver van** **configuratieservers** > .

## <a name="step-4-set-up-the-target-environment"></a>Stap 4: De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Selecteer **in Target voor infrastructuur** > **voorbereiden**het Azure-abonnement dat u wilt gebruiken.
2. Geef het doelimplementatiemodel op.
3. Site Recovery controleert of u een of meer compatibele Azure-opslagaccounts en -netwerken hebt. Als het ze niet vindt, moet u ten minste één opslagaccount en virtueel netwerk maken om de wizard te voltooien.


## <a name="step-5-enable-replication"></a>Stap 5: replicatie inschakelen

### <a name="create-a-replication-policy"></a>Een replicatiebeleid maken

1. Klik **op Instellingen voor infrastructuurreplicatie** > **Replication Settings**voorbereiden .
2. Geef in **Replicatiebeleid maken** een beleidsnaam op.
3. Geef in **RPO-drempelwaarde** de limiet van de Recovery Point Objective (RPO) op.
    - Herstelpunten voor gerepliceerde gegevens worden gemaakt in overeenstemming met de ingestelde tijd.
    - Deze instelling heeft geen invloed op replicatie, die continu is. Het geeft gewoon een waarschuwing als de drempelwaarde wordt bereikt zonder dat er een herstelpunt wordt gemaakt.
4. Geef **in Herstelpuntbehoud**aan hoe lang elk herstelpunt wordt bewaard. Gerepliceerde VM's kunnen op elk punt in het opgegeven tijdvenster worden hersteld.
5. Geef in **app-consistente momentopnamefrequentie**op hoe vaak toepassingsconsistente momentopnamen worden gemaakt.

    - Een momentopname die consistent is in de app, is een point-in-time momentopname van de app-gegevens in de vm.
    - Vss (Volume Shadow Copy Service) zorgt ervoor dat apps op de VM consistent zijn wanneer de momentopname wordt gemaakt.
6. Selecteer **OK** om het beleid te maken.


### <a name="confirm-deployment-planning"></a>Implementatieplanning bevestigen

Je deze stap nu overslaan. Klik in de vervolgkeuzelijst **Implementatieplanning** op **Ja, ik heb het gedaan.**



### <a name="enable-replication"></a>Replicatie inschakelen

Zorg ervoor dat u alle taken in stap 1 hebt [voltooid: Machine voorbereiden](#step-1-prepare-azure-stack-vms). Schakel vervolgens replicatie als volgt in:

1. Selecteer **Bron van toepassingen** > repliceren **.**
2. Selecteer in **Bron** de configuratieserver.
3. Selecteer **Fysieke machines**in **Machinetype**.
4. Selecteer de processerver (configuratieserver). Klik vervolgens op **OK**.
5. Selecteer **in Target**het abonnement en de resourcegroep waarin u de VM's wilt maken na een failover. Kies het implementatiemodel dat u wilt gebruiken voor de mislukte VM's.
6. Selecteer het Azure-opslagaccount waarin u de gerepliceerde gegevens wilt opslaan.
7. Selecteer het Azure-netwerk en -subnet waarmee virtuele Azure-machines verbinding maken wanneer ze na een failover worden gemaakt.
8. Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle machines die u voor beveiliging selecteert. Selecteer **Later configureren** als u het Azure-netwerk voor elke machine afzonderlijk wilt selecteren.
9. Klik **in Fysieke machines**op **+Fysieke machine**. Geef de naam, het IP-adres en het ostype op van elke machine die u wilt repliceren.

    - Gebruik het interne IP-adres van de machine.
    - Als u het openbare IP-adres opgeeft, werkt replicatie mogelijk niet zoals verwacht.

10. Selecteer in **Eigenschappen** > **Eigenschappen configureren**het account dat de processerver gebruikt om Mobiliteitsservice automatisch op de machine te installeren.
11. Controleer in **Replicatieinstellingen** > **Replicatieinstellingen configureren**of het juiste replicatiebeleid is geselecteerd.
12. Klik op **Replicatie inschakelen**.
13. De voortgang bijhouden van de taak **Beveiliging inschakelen** in**hersteltaken**van **de taak Voor** > **vacatures** > . Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de machine klaar voor een mogelijke failover.

> [!NOTE]
> Site Recovery installeert Mobility Service wanneer replicatie wordt ingeschakeld voor een VM.
> 
> Het kan 15 minuten of langer duren voordat wijzigingen zijn doorgevoerd en in de portal worden weergegeven.
> 
> Als u VM's wilt controleren die u toevoegt, controleert u de laatst ontdekte tijd voor VM's in **Configuration Servers** > **Last Contact At**. Als u VM's wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratieserver (zonder deze te selecteren), en selecteert u **Vernieuwen**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Stap 6: Een noodherstelboor uitvoeren

U voert een testfailover uit naar Azure om ervoor te zorgen dat alles werkt zoals verwacht. Deze failover heeft geen invloed op uw productieomgeving.

### <a name="verify-machine-properties"></a>Machine-eigenschappen controleren

Voordat u een testfailover uitvoert, controleert u de eigenschappen van de machine en controleert u of deze voldoen aan [de Azure-vereisten.](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements) U eigenschappen als volgt bekijken en wijzigen:

1. Klik in **Beveiligde items** op **Gerepliceerde items** > VM.
2. In het deelvenster **Gerepliceerd item** bevindt zich een overzicht van VM-informatie, status, en de laatste beschikbare herstelpunten. Klik op **Eigenschappen** om meer details te bekijken.
3. Wijzig **in Compute en Netwerk**de instellingen indien nodig.

    - U de Azure VM-naam, resourcegroep, doelgrootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md)en beheerde schijfinstellingen wijzigen.
    - U ook netwerkinstellingen bekijken en wijzigen. Deze omvatten het netwerk/subnet waaraan de Azure VM is verbonden na een failover en het IP-adres dat aan de VM wordt toegewezen.
1. Bekijk **in schijven**informatie over het besturingssysteem en gegevensschijven op de VM.
   

### <a name="run-a-test-failover"></a>Een testfailover uitvoeren

Wanneer u een testfailover uitvoert, gebeurt het volgende:

1. Er wordt een controle uitgevoerd om na te gaan of aan alle vereiste voorwaarden voor failover wordt voldaan.
2. Failover verwerkt de gegevens met behulp van het opgegeven herstelpunt:
    - **Laatste verwerkt**: De machine mislukt over naar het laatste herstelpunt verwerkt door Site Recovery. Het tijdstempel wordt weergegeven. Met deze optie wordt er geen tijd besteed aan het verwerken van gegevens, zodat er een lage RTO (Recovery Time Objective) is.
    - **Nieuwste app-consistent:** De machine mislukt naar het nieuwste app-consistente herstelpunt.
    - **Aangepast:** selecteer het herstelpunt dat wordt gebruikt voor failover.

3. Er wordt een Azure VM gemaakt met behulp van de verwerkte gegevens.
4. Testfailover kan automatisch Azure VM's opruimen die tijdens de drill zijn gemaakt.

Voer als volgt een testfailover voor een VM uit:

1. Klik in**Gerepliceerde items** **instellingen** > op de VM-> **+Testfailover**.
2. Voor deze walkthrough selecteren we het **laatste verwerkte** herstelpunt. 
3. Selecteer **in Test Failover**het doelAzure-netwerk.
4. Klik op **OK** om te beginnen met de failover.
5. Houd de voortgang bij door op de VM te klikken om de eigenschappen ervan te openen. U ook op de taak **Failover testen** in taak van *de taak* > Vacature**Instellingen** > **voor taak voor vacatures** >**voor**vacatures.
6. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Controleer of de VM de juiste grootte heeft, is verbonden met het juiste netwerk en wordt uitgevoerd.
7. Nu moet u verbinding maken met de gerepliceerde virtuele machine in Azure. [Meer informatie](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Als u VM's van Azure wilt verwijderen die tijdens de failovertest zijn gemaakt, klikt u in de VM op **Failovertest opschonen**. Sla in **Notities**alle waarnemingen op die verband houden met de testfailover.

## <a name="fail-over-and-fail-back"></a>Failover en failback

Nadat u replicatie hebt ingesteld en een drill hebt uitgevoerd om te zien of alles werkt, u machines naar Azure naar behoefte laten mislukken.

Voordat u een failover uitvoert, wilt u na de failover verbinding maken met de machine in Azure, [bereid u zich voor om verbinding te maken](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) voordat u begint.

Voer vervolgens een failover als volgt uit:


1. Klik in **Gerepliceerde** > **instellingen**op de machine > **failover**.
2. Selecteer het herstelpunt dat u wilt gebruiken.
3. Selecteer **in Test Failover**het doelAzure-netwerk.
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Met deze instelling probeert Site Recovery de bronmachine uit te schakelen voordat de failover wordt gestart. Nochtans failover gaat zelfs als de sluiting uitvalt. 
5. Klik op **OK** om te beginnen met de failover. U de failovervoortgang volgen op de pagina **Vacatures.**
6. Nadat de failover is voltooid, wordt de replica-Azure-VM weergegeven in Azure Portal > **Virtuele machines**. Als u bereid bent verbinding te maken na een failover, controleert u of de VM de juiste grootte heeft, is verbonden met het juiste netwerk en wordt uitgevoerd.
7. Nadat u de VM hebt geverifieerd, klikt u op **Vastleggen** om de failover te voltooien. Hiermee worden alle beschikbare herstelpunten verwijderd.

> [!WARNING]
> Annuleer een failover die in voortgang is niet: voordat de failover wordt gestart, wordt de VM-replicatie gestopt. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.


### <a name="fail-back-to-azure-stack"></a>Niet terug naar Azure Stack

Wanneer uw primaire site weer actief is, u weer van Azure naar Azure Stack gaan. Hiervoor moet u de Azure VM VHD downloaden en uploaden naar Azure Stack.

1. Sluit de Azure VM af, zodat de VHD kan worden gedownload. 
2. Als u wilt beginnen met het downloaden van de VHD, installeert u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Navigeer naar de VM in de Azure Portal (met de VM-naam).
4. Klik **in schijven**op de schijfnaam en verzamel instellingen.

    - Als voorbeeld, de VHD URI gebruikt https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd in onze test: kan worden opgesplitst om de volgende input parameters die worden gebruikt om de VHD downloaden te krijgen.
        - Opslagrekening: 502055westcentralus
        - Container: wahv9b8d2ceb284fb59287
        - VHD-naam: gekopieerd-3676553984.vhd

5. Gebruik nu Azure Storage Explorer om de VHD te downloaden.
6. Upload de VHD naar Azure Stack met [deze stappen](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm).
7. Voeg in de bestaande VM of nieuwe VM de geüploade VHD's toe.
8. Controleer of de OS-schijf correct is en start de VM.


In dit stadium failback is voltooid.


## <a name="conclusion"></a>Conclusie

In dit artikel hebben we Azure Stack VM's gerepliceerd naar Azure. Met replicatie hebben we een noodhersteloefening uitgevoerd om ervoor te zorgen dat failover naar Azure werkte zoals verwacht. Het artikel bevat ook stappen voor het uitvoeren van een volledige failover naar Azure en het niet terugnaar Azure Stack.

## <a name="next-steps"></a>Volgende stappen

Nadat u niet terug bent gegaan, u de VM opnieuw beveiligen en opnieuw repliceren naar Azure Om dit te doen, herhaalt u de stappen in dit artikel.

