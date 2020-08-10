---
title: Herstel na noodgevallen van Azure VM’s instellen met Azure Site Recovery
description: Leer hoe u herstel na noodgevallen van Azure-VM’s naar een andere Azure-regio kunt instellen met de Azure Site Recovery-service.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 50bf1ec7f21ccbc3a3fa8feaea02e45bd08a158a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421413"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Herstel na noodgevallen instellen voor Azure-VM's

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgeval, door de replicatie, failover en failback van on-premises machines en Azure-VM’s (virtuele machines) te beheren en in te delen.

Deze zelfstudie laat zien hoe u voor Azure-VM's herstel na noodgevallen kunt instellen door deze van de ene Azure-regio naar een andere te repliceren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Recovery Services-kluis maken
> * Doelresource-instellingen controleren
> * Uitgaande netwerkconnectiviteit voor VM's instellen
> * Replicatie inschakelen voor een VM

> [!NOTE]
> Dit artikel biedt instructies voor het implementeren van herstel na noodgeval met de eenvoudigste instellingen. Als u informatie wilt over aangepaste instellingen, neem dan de artikelen in de [Instructiesectie](azure-to-azure-how-to-enable-replication.md) door.

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- De [architectuur en onderdelen voor dit scenario](./azure-to-azure-architecture.md) doornemen.
- Raadpleeg de [ondersteuningsvereisten](./azure-to-azure-support-matrix.md) voordat u begint.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Maak de kluis in elke gewenste regio, met uitzondering van de bronregio.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. Selecteer vervolgens **IT- en beheerhulpprogramma's** > **Backup en Site Recovery**.
1. Geef in **Naam** een beschrijvende naam op om de kluis mee aan te duiden. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
1. Maak een resourcegroep of selecteer een bestaande resourcegroep. Geef een Azure-regio op. Zie Geografische beschikbaarheid in [Prijsinformatie voor Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) om na te gaan welke regio's er worden ondersteund.
1. Voor toegang tot de kluis vanuit het dashboard, selecteert u **Aan dashboard vastmaken** en vervolgens **Maken**.

   ![Nieuwe kluis](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

De nieuwe kluis wordt toegevoegd op het **Dashboard** onder **Alle resources** en op de hoofdpagina van **Recovery Services-kluizen**.

## <a name="verify-target-resource-settings"></a>Doelresource-instellingen controleren

Controleer uw Azure-abonnement voor de doelregio.

- Controleer of u een Azure-abonnement hebt waarmee u VM´s kunt maken in de doelregio. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.
- Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning bieden voor VM’s met grootten die overeenkomen met de bron-VM’s. Site Recovery kiest voor de doel-VM dezelfde of de dichtstbijzijnde grootte.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Uitgaande netwerkconnectiviteit voor VM's instellen

Om ervoor te zorgen dat Site Recovery werkt zoals verwacht, moet u de uitgaande netwerkconnectiviteit wijzigen van de VM's die u wilt repliceren.

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor het gebruiken van een verificatieproxy om netwerkconnectiviteit te beheren.

### <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze URL’s toe:

| **Naam**                  | **Commercieel**                               | **Overheid**                                 | **Beschrijving** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| Replicatie               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Uitgaande connectiviteit voor IP-adresbereiken

Als u een netwerkbeveiligingsgroep (NSG) gebruikt, moet u NSG-regels op basis van een servicetag maken om toegang te krijgen tot Azure Storage, Azure Active Directory, de Site Recovery-service en Site Recovery-bewaking. [Meer informatie](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Azure VM-certificaten controleren

Controleer of de VM’s die u wilt repliceren, beschikken over de meest recente basiscertificaten. Als dit niet het geval is, kunnen de VM's vanwege beveiligingsbeperkingen niet worden geregistreerd bij Site Recovery.

- Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
- Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM.

## <a name="set-permissions-on-the-account"></a>Machtigingen instellen voor het account

Azure Site Recovery heeft drie ingebouwde rollen voor het beheren van Site Recovery-beheerbewerkingen.

- **Site Recovery-inzender** - deze rol bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te kunnen beheren. Een gebruiker met deze rol kan echter geen Recovery Services-kluis maken of verwijderen, noch toegangsrechten aan andere gebruikers toewijzen. Deze rol is het meest geschikt voor beheerders die herstel na noodgevallen voor toepassingen of hele organisaties kunnen inschakelen en beheren.

- **Site Recovery-operator**: deze rol heeft machtigingen om failover- en failback-bewerkingen uit te voeren en te beheren. Een gebruiker met deze rol kan replicatie niet in- of uitschakelen, geen kluizen maken of verwijderen, en geen nieuwe infrastructuur registreren of toegangsrechten toewijzen aan andere gebruikers. Deze rol is het meest geschikt voor een operator die failover-overschakeling kan uitvoeren voor virtuele machines of applicaties in opdracht van toepassingseigenaren en IT-beheerders. Na het noodherstel kan de noodhersteloperator de virtuele machines opnieuw beschermen en een failback uitvoeren.

- **Site Recovery-lezer** - deze rol heeft machtigingen voor het weergeven van alle beheerbewerkingen van Site Recovery. Deze rol is het meest geschikt is voor een IT-leidinggevende die de huidige beschermingsstatus kan bewaken en ondersteuningstickets kan aanvragen.

Meer informatie over [ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Replicatie inschakelen voor een VM

In de volgende secties wordt beschreven hoe u replicatie kunt inschakelen.

### <a name="select-the-source"></a>De bron selecteren

Als u de replicatie wilt instellen, begint u met het kiezen van de bron waar de Azure-VM's worden uitgevoerd.

1. Ga naar **Recovery Services-kluizen**, selecteer de kluisnaam en vervolgens **+Repliceren**.
1. Als **bron** selecteert u **Azure**.
1. Bij **Bronlocatie** selecteert u de Azure-bronregio waar uw VM’s momenteel worden uitgevoerd.
1. Selecteer het **Bronabonnement** waar de virtuele machines worden uitgevoerd. Dit kan elk abonnement zijn binnen dezelfde Azure Active Directory-tenant waar uw Recovery Services-kluis zich bevindt.
1. Selecteer de **bronresourcegroep** en selecteer **OK** om de instellingen op te slaan.

   ![Bron instellen](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>De VM’s selecteren

Site Recovery haalt een lijst op van de VM’s die zijn gekoppeld aan het abonnement en de resourcegroep/cloudservice.

1. Selecteer in **Virtuele machines** de VM’s die u wilt repliceren.
1. Selecteer **OK**.

### <a name="configure-replication-settings"></a>Replicatie-instellingen configureren

Site Recovery maakt standaardinstellingen en replicatiebeleid voor de doelregio. U kunt deze instellingen naar behoefte wijzigen.

1. Selecteer **Instellingen** om de doel- en replicatie-instellingen te bekijken.

1. Selecteer **Aanpassen** naast **Resourcegroep, netwerk, opslag en beschikbaarheid** als u de standaarddoelinstellingen wilt overschrijven.

   ![Instellingen configureren](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Pas de doelinstellingen aan overeenkomstig het overzicht in de tabel.

   | **Instelling** | **Details** |
   | --- | --- |
   | **Doelabonnement** | Standaard is het doelabonnement hetzelfde als het bronabonnement. Selecteer **Aanpassen** om een ander doelabonnement binnen dezelfde Azure Active Directory-tenant te selecteren. |
   | **Doellocatie** | De doelregio die wordt gebruikt voor herstel na noodgevallen.<br/><br/> Wij raden aan dezelfde doellocatie te gebruiken als de locatie van de Site Recovery-kluis. |
   | **Doelresourcegroep** | De resourcegroep in de doelregio waarin Azure-VM’s zich na een failover bevinden.<br/><br/> Site Recovery maakt standaard een nieuwe resourcegroep in de doelregio met het achtervoegsel `asr`. De resourcegroep-locatie van de doelresourcegroep mag elke regio zijn, behalve de regio waar uw virtuele bronmachines worden gehost. |
   | **Virtueel doelnetwerk** | Het netwerk in de doelregio waar VM’s zich na een failover bevinden.<br/><br/> Site Recovery maakt standaard een nieuw virtueel netwerk (met subnetten) in de doelregio met het achtervoegsel `asr`. |
   | **Cacheopslagaccounts** | Site Recovery maakt gebruik van een opslagaccount in de bronregio. Wijzigingen in de bron-VM's worden naar dit account verzonden vóór replicatie naar de doellocatie.<br/><br/> Als u gebruikmaakt van een cacheopslagaccount met een firewall, moet u **Vertrouwde Microsoft-services toestaan** inschakelen. [Meer informatie](../storage/common/storage-network-security.md#exceptions). Zorg er ook voor dat u toegang tot ten minste één subnet van het bron-Vnet toestaat. |
   | **Doelopslagaccounts (bron-VM maakt gebruik van niet-beheerde schijven)** | Met Site Recovery wordt standaard een nieuw opslagaccount in de doelregio gemaakt om het bron-VM-opslagaccount te spiegelen.<br/><br/> Schakel **Vertrouwde Microsoft-services toestaan** in als u gebruikmaakt van een cacheopslagaccount met een firewall. |
   | **Beheerde replicaschijven (als de bron-VM gebruikmaakt van beheerde schijven)** | Met Site Recovery worden standaard replicaschijven in de doelregio gemaakt om de beheerde schijven van de bron-VM's met hetzelfde opslagtype (Standard of Premium) te spiegelen als de beheerde schijf van de bron-VM's. U kunt alleen het schijftype aanpassen. |
   | **Doelbeschikbaarheidssets** | In Azure Site Recovery wordt standaard een nieuwe beschikbaarheidsset gemaakt in de doelregio, met een naam die eindigt op `asr` voor het VM-gedeelte van een beschikbaarheidsset in de bronregio. Als de beschikbaarheidsset die wordt gemaakt met Azure Site Recovery, al bestaat, wordt deze opnieuw gebruikt. |
   | **Doelbeschikbaarheidszones** | In Site Recovery wordt standaard hetzelfde zonenummer toegewezen als de bronregio in de doelregio, als de doelregio ondersteuning biedt voor beschikbaarheidszones.<br/><br/> Als de doelregio geen ondersteuning biedt voor beschikbaarheidszones, worden de doel-VM's standaard geconfigureerd als enkele instanties.<br/><br/> Selecteer **Aanpassen** als u VM's wilt configureren als onderdeel van een beschikbaarheidsset in de doelregio.<br/><br/> Nadat u replicatie hebt ingeschakeld, kunt u het type beschikbaarheid (enkele instantie, beschikbaarheidsset of beschikbaarheidszone) niet meer wijzigen. Als u het type beschikbaarheid wilt wijzigen, moet u replicatie uitschakelen en opnieuw inschakelen. |

1. Selecteer naast **Replicatiebeleid** de optie **Aanpassen** en pas de volgende instellingen naar behoefte aan om instellingen voor het replicatiebeleid aan te passen.

   | **Instelling** | **Details** |
   | --- | --- |
   | **Naam van replicatiebeleid** | Beleidsnaam. |
   | **Bewaarperiode van herstelpunt** | In Site Recovery worden herstelpunten standaard 24 uur bewaard. U kunt een waarde van 1 tot 72 uur configureren. |
   | **Frequentie van de app-consistente momentopname** | In Site Recovery wordt standaard elke 4 uur een app-consistente momentopname gemaakt. U kunt een waarde van 1 tot 12 uur configureren.<br/><br/> Een app-consistente momentopname is een momentopname van de toepassingsgegevens in de VM op een bepaald tijdstip. Met Volume Shadow Copy Service (VSS) wordt ervoor gezorgd dat apps op de VM een consistente status hebben wanneer er een momentopname wordt gemaakt. |
   | **Replicatiegroep** | Als uw toepassing consistentie tussen meerdere VM's nodig heeft, kunt u een replicatiegroep maken voor deze VM’s. Standaard maken de geselecteerde VM’s geen deel uit van een replicatiegroep. |

1. Selecteer in **Aanpassen** de optie **Ja** voor consistentie van multi-VM, als u VM’s wilt toevoegen aan een nieuwe of bestaande replicatiegroep. Selecteer vervolgens **OK**.

   > [!NOTE]
   > - Alle machines in een replicatiegroep hebben gedeelde crash-consistente en app-consistente herstelpunten bij een failover.
   > - Het inschakelen van multi-VM-consistentie kan de prestaties van de workloads beïnvloeden (omdat dit CPU-intensief is). Dit moet dus alleen worden gebruikt als de computers dezelfde workload uitvoeren en u consistentie tussen meerdere computers nodig hebt.
   > - U kunnen zich maximaal 16 virtuele machines in een replicatiegroep bevinden.
   > - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004. Zorg ervoor dat de interne communicatie tussen de VM's via deze poort niet door een firewall wordt geblokkeerd.
   > - Bij Linux VM’s in een replicatiegroep moet u ervoor zorgen dat het uitgaande verkeer op poort 20004 handmatig wordt geopend overeenkomstig de richtlijnen voor die Linux-versie.

### <a name="configure-encryption-settings"></a>Versleutelingsinstellingen configureren

Controleer de instellingen als voor de bron-VM Azure Disk Encryption (ADE) is ingeschakeld.

1. De instellingen controleren:
   1. **Sleutelkluizen voor schijfversleuteling**: Site Recovery maakt standaard een nieuwe sleutelkluis met het achtervoegsel `asr` op basis van de schijfversleutelingssleutels van de bron-VM. Als de sleutelkluis al bestaat, wordt deze opnieuw gebruikt.
   1. **Sleutelkluizen voor versleutelingssleutel**: Site Recovery maakt standaard een nieuwe sleutelkluis in de doelregio. De naam heeft een achtervoegsel `asr` en is gebaseerd op de versleutelingssleutels van de bron-VM. Als sleutelkluis die door Site Recovery wordt gemaakt al bestaat, wordt deze opnieuw gebruikt.
1. Selecteer **Aanpassen** om de aangepaste sleutelkluizen te selecteren.

>[!NOTE]
> Site Recovery biedt momenteel ondersteuning voor ADE, met en zonder Azure Active Directory (AAD), voor virtuele machines waarop Windows-besturingssystemen worden uitgevoerd. Voor Linux-besturingssystemen wordt alleen ADE zonder AAD ondersteund. Daarnaast moeten op computers waarop ADE 1.1 (zonder AAD) wordt uitgevoerd, de virtuele machines gebruikmaken van beheerde schijven. VM's met niet-beheerde schijven worden niet ondersteund. Als u overstapt van ADE 0.1 (met AAD) naar 1.1, moet u voor een VM replicatie uitschakelen en weer inschakelen nadat u 1.1 hebt ingeschakeld.

### <a name="track-replication-status"></a>Replicatiestatus volgen

Nadat de replicatie is ingeschakeld, kunt u de status van de taak bijhouden.

1. Selecteer in **Instellingen** de optie **Vernieuwen** om de nieuwste status op te halen.
1. U kunt als volgt de voortgang en status bijhouden:
   1. Houd de voortgang bij van de taak **Beveiliging inschakelen** via **Instellingen** > **Taken** > **Site Recovery-taken**.
   1. In **Instellingen** > **Gerepliceerde Items** kunt u de status van VM’s en de voortgang van de initiële replicatie bekijken. Selecteer de VM om in te zoomen op de instellingen ervan.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u herstel na noodgevallen geconfigureerd voor een Azure-VM. U kunt nu een noodherstelanalyse uitvoeren om te controleren of de failover werkt zoals verwacht.

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](azure-to-azure-tutorial-dr-drill.md)
