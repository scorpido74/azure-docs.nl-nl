---
title: Azure Migrate-replicatieapparaat
description: Meer informatie over het Azure Migrate-replicatietoestel voor vmware-migratie op basis van agenten.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4521fce6310b319d155a2f0c418cd934be7e2cb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245861"
---
# <a name="replication-appliance"></a>Replicatietoestel

In dit artikel wordt het replicatietoestel beschreven dat wordt gebruikt door [Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) tool bij het migreren van VMware VM's, fysieke machines en private/public cloud VM's naar Azure, met behulp van op agentgebaseerde migratie. 


## <a name="overview"></a>Overzicht

Het replicatietoestel wordt geïmplementeerd wanneer u op agentgebaseerde migratie van VMware VM's of fysieke servers instelt. Het wordt geïmplementeerd als één on-premises machine, als vmware-vm of als fysieke server. Het loopt:

- **Replicatietoestel:** het replicatietoestel coördineert de communicatie en beheert gegevensreplicatie voor on-premises Vm's vmware en fysieke servers die naar Azure worden gerepliceerd.
- **Processerver:** de processerver die standaard op het replicatietoestel is geïnstalleerd en gaat als volgt te werk:
    - **Replicatiegateway:** het fungeert als replicatiegateway. Het ontvangt replicatiegegevens van machines die zijn ingeschakeld voor replicatie. Het optimaliseert replicatiegegevens met caching, compressie en versleuteling en stuurt deze naar Azure.
    - **Agent installer**: Voert een push-installatie van de Mobility Service uit. Deze service moet worden geïnstalleerd en uitgevoerd op elke on-premises machine die u wilt repliceren voor migratie.

## <a name="appliance-deployment"></a>Toestelimplementatie

**Gebruikt voor** | **Details**
--- |  ---
VMware VM-agentgebaseerde migratie | U downloadt OVA-sjabloon van de Azure Migrate-hub en importeert naar vCenter Server om de toestel-VM te maken.
Migratie op basis van fysieke machineagent | Als u geen VMware-infrastructuur hebt of als u geen VMware-VM maken met een OVA-sjabloon, downloadt u een software-installatieprogramma van de Azure Migrate-hub en voert u deze uit om de toestelmachine in te stellen.

## <a name="appliance-requirements"></a>Toestelvereisten

Wanneer u het replicatietoestel instelt met de OVA-sjabloon in de Azure Migrate-hub, wordt Windows Server 2016 uitgevoerd en voldoet het aan de ondersteuningsvereisten. Als u het replicatietoestel handmatig instelt op een fysieke server, controleert u of het voldoet aan de vereisten.

**Component** | **Vereiste**
--- | ---
 | **VMware VM-toestel**
PowerCLI (PowerCLI) | [PowerCLI-versie 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als het replicatietoestel op een Vm van VMware wordt uitgevoerd.
NIC-type | VMXNET3 (als het toestel een VMware VM is)
 | **Hardware-instellingen**
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | Drie: de os-schijf, de servercacheschijf en het bewaarstation.
Vrije schijfruimte (cache) | 600 GB
Vrije schijfruimte (bewaarschijf) | 600 GB
**Software-instellingen** |
Besturingssysteem | Windows Server 2016 of Windows Server 2012 R2
Licentie | Het toestel wordt geleverd met een Windows Server 2016-evaluatielicentie, die 180 dagen geldig is.<br/><br/> Als de evaluatieperiode bijna afloopt, raden we u aan een nieuw toestel te downloaden en te implementeren of de licentie van het besturingssysteem van de toestel-VM te activeren.
Landinstelling van het besturingssysteem | Engels (en-us)
TLS | TLS 1.2 moet worden ingeschakeld.
.NET Framework | .NET Framework 4.6 of hoger moet op de machine worden geïnstalleerd (met sterke cryptografie ingeschakeld.
MySQL | MySQL moet op het toestel worden geïnstalleerd.<br/> MySQL moet worden geïnstalleerd. U handmatig installeren of Site Recovery kan het installeren tijdens de implementatie van het toestel.
Andere apps | Voer geen andere apps uit op het replicatietoestel.
Windows Server-functies | Schakel deze rollen niet in: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Groepsbeleid | Schakel dit groepsbeleid niet in: <br> - Voorkom toegang tot de opdrachtprompt. <br> - Voorkom toegang tot registerbewerkingstools. <br> - Logica vertrouwen voor bestandsbijlagen. <br> - Scriptuitvoering inschakelen. <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Geen reeds bestaande standaardwebsite <br> - Geen reeds bestaande website/applicatie luisteren op poort 443 <br>- [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br> - [FastCGI-instelling](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inschakelen
**Netwerkinstellingen** |
Type IP-adres | Statisch
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport)
NIC-type | VMXNET3

## <a name="mysql-installation"></a>MySQL-installatie 

MySQL moet op de machine van het replicatietoestel worden geïnstalleerd. Het kan worden geïnstalleerd met behulp van een van deze methoden.

**Methode** | **Details**
--- | ---
Handmatig downloaden en installeren | MySQL-toepassing downloaden & deze in de map C:\Temp\ASRSetup plaatsen en vervolgens handmatig installeren.<br/> Wanneer u het toestel instelt, wordt MySQL weergegeven als reeds geïnstalleerd.
Zonder online download | Plaats de installatietoepassing MySQL in de map C:\Temp\ASRSetup. Wanneer u het toestel installeert en klikt om MySQL te downloaden en te installeren, gebruikt de installatie het installatieprogramma dat u hebt toegevoegd.
Downloaden en installeren in Azure Migrate | Wanneer u het toestel installeert en wordt gevraagd om MySQL, selecteert u **Downloaden en installeren**.

## <a name="url-access"></a>URL-toegang

Het replicatietoestel heeft toegang nodig tot deze URL's.

**Url** | **Details**
--- | ---
\*.backup.windowsazure.com | Gebruikt voor gerepliceerde gegevensoverdracht en -coördinatie
\*.store.core.windows.net | Gebruikt voor gerepliceerde gegevensoverdracht en -coördinatie
\*.blob.core.windows.net | Wordt gebruikt om toegang te krijgen tot opslagaccount dat gerepliceerde gegevens opslaat
\*.hypervrecoverymanager.windowsazure.com | Wordt gebruikt voor replicatiebeheerbewerkingen en -coördinatie
https:\//management.azure.com | Wordt gebruikt voor replicatiebeheerbewerkingen en -coördinatie
*.services.visualstudio.com | Gebruikt voor telemetriedoeleinden (Dit is optioneel)
time.nist.gov | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
time.windows.com | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF-instellingen hebben toegang nodig tot deze URL's. Ze worden gebruikt voor toegangscontrole en identiteitsbeheer door Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL-download voltooien

## <a name="port-access"></a>Poorttoegang

**Apparaat** | **Verbinding**
--- | ---
VM's | De Mobiliteitsservice die op VM's wordt uitgevoerd, communiceert met het on-premises replicatietoestel (configuratieserver) op poort HTTPS 443 inbound, voor replicatiebeheer.<br/><br/> VM's verzenden replicatiegegevens naar de processerver (uitgevoerd op de configuratieservermachine) op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
Replicatietoestel | Het replicatietoestel orkestreert replicatie met Azure via poort HTTPS 443 outbound.
Processerver | De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag via poort 443-uitgaand.<br/> Standaard wordt de processerver uitgevoerd op het replicatietoestel.


## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie inschakelt voor een virtuele machine, begint de eerste replicatie naar Azure-opslag met behulp van het opgegeven replicatiebeleid. 
2. Verkeer wordt via internet gerepliceerd naar openbare eindpunten voor Azure-opslag. Het repliceren van verkeer via een vpn (site-to-site virtual private network) van een on-premises site naar Azure wordt niet ondersteund.
3. Nadat de eerste replicatie is voltooid, wordt deltareplicatie gestart. Bijgehouden wijzigingen voor een machine worden geregistreerd.
4. Communicatie gebeurt als volgt:
    - VM's communiceren met het replicatietoestel op poort HTTPS 443 inkomende, voor replicatiebeheer.
    - Het replicatietoestel orkestreert replicatie met Azure via poort HTTPS 443 outbound.
    - VM's verzenden replicatiegegevens naar de processerver (uitgevoerd op het replicatietoestel) op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
    - De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag via poort 443-uitgaand.
5. De replicatiegegevens worden eerst in een cacheopslagaccount in Azure opgeslagen. Deze logboeken worden verwerkt en de gegevens worden opgeslagen in een door Azure beheerde schijf.

![Architectuur](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Toestelupgrades

Het toestel wordt handmatig geüpgraded vanuit de Azure Migrate-hub. We raden u aan altijd de nieuwste versie uit te voeren.

1. In Azure migreren > servers > Azure Migreren: Serverbeoordeling, Infrastructuurservers, klik op **Configuratieservers**.
2. In **Configuratieservers**wordt een koppeling weergegeven in **de agentversie** wanneer een nieuwe versie van het replicatietoestel beschikbaar is. 
3. Download het installatieprogramma naar de machine van het replicatietoestel en installeer de upgrade. De installateur detecteert de versiestroom die op het toestel wordt uitgevoerd.
 
## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) instellen van het replicatietoestel voor VMware VM-migratie op basis van agent.
- [Meer informatie over het](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) instellen van het replicatietoestel voor fysieke servers.
