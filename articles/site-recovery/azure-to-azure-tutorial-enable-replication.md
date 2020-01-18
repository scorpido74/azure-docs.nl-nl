---
title: Herstel na nood gevallen voor Azure VM met Azure Site Recovery instellen
description: Leer hoe u herstel na noodgevallen van Azure-VM’s naar een andere Azure-regio kunt instellen met de Azure Site Recovery-service.
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: 7d78064285057f6abd91c3ca95e01ec86f1a1a39
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169339"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Herstel na nood geval instellen voor virtuele Azure-machines

De [Azure Site Recovery](site-recovery-overview.md)-service draagt bij aan uw strategie voor herstel na noodgeval, door de replicatie, failover en failback van on-premises machines en Azure-VM’s (virtuele machines) te beheren en in te delen.

In deze zelf studie leert u hoe u herstel na nood gevallen instelt voor Azure-Vm's door ze te repliceren van de ene Azure-regio naar een andere. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Recovery Services-kluis maken
> * Doelresource-instellingen controleren
> * Uitgaande netwerk verbinding voor Vm's instellen
> * Replicatie inschakelen voor een VM

> [!NOTE]
> Dit artikel biedt instructies voor het implementeren van herstel na noodgeval met de eenvoudigste instellingen. Als u meer wilt weten over aangepaste instellingen, raadpleegt u de artikelen in de [sectie How to](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

- De [architectuur en onderdelen voor dit scenario](concepts-azure-to-azure-architecture.md) doornemen.
- Controleer de [ondersteunings vereisten](site-recovery-support-matrix-azure-to-azure.md) voordat u begint.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Maak de kluis in elke gewenste regio, met uitzondering van de bronregio.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Selecteer deze vervolgens **&-beheer hulpprogramma's** > **back-up en site Recovery**.
1. Geef in **Naam** een beschrijvende naam op om de kluis mee aan te duiden. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
1. Maak een resourcegroep of selecteer een bestaande resourcegroep. Geef een Azure-regio op. Zie Geografische beschikbaarheid in [Prijsinformatie voor Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) om na te gaan welke regio's er worden ondersteund.
1. Om toegang te krijgen tot de kluis vanuit het dash board, selecteert u vastmaken **aan dash board** en selecteert u **Create**.

   ![Nieuwe kluis](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

De nieuwe kluis wordt toegevoegd op het **Dashboard** onder **Alle resources** en op de hoofdpagina van **Recovery Services-kluizen**.

## <a name="verify-target-resource-settings"></a>Doelresource-instellingen controleren

Controleer uw Azure-abonnement voor de doel regio.

- Controleer of u een Azure-abonnement hebt waarmee u VM´s kunt maken in de doelregio. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.
- Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning bieden voor VM’s met grootten die overeenkomen met de bron-VM’s. Site Recovery kiest voor de doel-VM dezelfde of de dichtstbijzijnde grootte.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Uitgaande netwerk verbinding voor Vm's instellen

Om ervoor te zorgen dat Site Recovery werkt zoals verwacht, moet u de uitgaande netwerkconnectiviteit wijzigen van de VM's die u wilt repliceren.

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor het gebruiken van een verificatieproxy om netwerkconnectiviteit te beheren.

### <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een firewall proxy op basis van een URL gebruikt voor het beheren van uitgaande connectiviteit, kunt u toegang tot deze Url's toestaan:

| **URL** | **Details** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| `login.microsoftonline.com` | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| `*.servicebus.windows.net` | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Uitgaande connectiviteit voor IP-adresbereiken

Als u een netwerk beveiligings groep (NSG) gebruikt, moet u NSG-regels op basis van een service label maken voor toegang tot Azure Storage, Azure Active Directory, Site Recovery service en Site Recovery bewaking. [Meer informatie](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

> [!NOTE]
> Het is raadzaam om altijd NSG-regels met Service Tags voor uitgaande toegang te configureren.

Als u uitgaande verbindingen met IP-adressen wilt beheren, moet u deze adressen toestaan voor op IP gebaseerde firewalls, proxy-of NSG:

- [Microsoft Azure Datacenter IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)
- [Windows Azure Datacenter IP-bereiken in Duitsland](https://www.microsoft.com/download/details.aspx?id=54770)
- [Windows Azure Datacenter IP-bereiken in China](https://www.microsoft.com/download/details.aspx?id=42064)
- [Office 365 URLs and IP address ranges (URL's en IP-adresbereiken voor Office 365)](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
- [Site Recovery-service-eindpunt-IP-adressen](https://aka.ms/site-recovery-public-ips)

## <a name="verify-azure-vm-certificates"></a>Azure VM-certificaten controleren

Controleer of de VM’s die u wilt repliceren, beschikken over de meest recente basiscertificaten. Als dat niet het geval is, kan de virtuele machine niet worden geregistreerd om Site Recovery vanwege beveiligings beperkingen.

- Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
- Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM.

## <a name="set-permissions-on-the-account"></a>Machtigingen instellen voor het account

Azure Site Recovery heeft drie ingebouwde rollen voor het beheren van Site Recovery-beheerbewerkingen.

- **Site Recovery-inzender** - deze rol bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te kunnen beheren. Een gebruiker met deze rol kan echter geen Recovery Services-kluis maken of verwijderen, noch toegangsrechten aan andere gebruikers toewijzen. Deze rol is het meest geschikt voor beheerders die herstel na noodgevallen voor toepassingen of hele organisaties kunnen inschakelen en beheren.

- **Site Recovery-operator**: deze rol heeft machtigingen om failover- en failback-bewerkingen uit te voeren en te beheren. Een gebruiker met deze rol kan replicatie niet in- of uitschakelen, geen kluizen maken of verwijderen, en geen nieuwe infrastructuur registreren of toegangsrechten toewijzen aan andere gebruikers. Deze rol is het meest geschikt voor een operator die failover-overschakeling kan uitvoeren voor virtuele machines of applicaties in opdracht van toepassingseigenaren en IT-beheerders. De oplossing voor herstel na nood gevallen kan de virtuele machines opnieuw beveiligen en failback uitvoeren.

- **Site Recovery-lezer** - deze rol heeft machtigingen voor het weergeven van alle beheerbewerkingen van Site Recovery. Deze rol is het meest geschikt is voor een IT-leidinggevende die de huidige beschermingsstatus kan bewaken en ondersteuningstickets kan aanvragen.

Meer informatie over [ingebouwde Azure RBAC-rollen](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Replicatie inschakelen voor een VM

In de volgende secties wordt beschreven hoe u replicatie inschakelt.

### <a name="select-the-source"></a>De bron selecteren

Als u de replicatie wilt instellen, kiest u de bron waar de virtuele machines van Azure worden uitgevoerd.

1. Ga naar **Recovery Services kluizen**, selecteer de naam van de kluis en selecteer vervolgens **+ repliceren**.
1. Selecteer voor de **bron** **Azure**.
1. Bij **Bronlocatie** selecteert u de Azure-bronregio waar uw VM’s momenteel worden uitgevoerd.
1. Selecteer het **Bronabonnement** waar de virtuele machines worden uitgevoerd. Dit kan elk abonnement zijn binnen dezelfde Azure Active Directory-tenant waar uw Recovery Services-kluis zich bevindt.
1. Selecteer de **bron resource groep**en selecteer **OK** om de instellingen op te slaan.

   ![Bron instellen](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>De VM’s selecteren

Site Recovery haalt een lijst op van de VM’s die zijn gekoppeld aan het abonnement en de resourcegroep/cloudservice.

1. Selecteer in **Virtuele machines** de VM’s die u wilt repliceren.
1. Selecteer **OK**.

### <a name="configure-replication-settings"></a>Replicatie-instellingen configureren

Site Recovery maakt standaardinstellingen en replicatiebeleid voor de doelregio. U kunt deze instellingen zo nodig wijzigen.

1. Selecteer **instellingen** om de doel-en replicatie-instellingen weer te geven.

1. Als u de standaard doel instellingen wilt overschrijven, selecteert u **aanpassen** naast **resource groep, netwerk, opslag en beschik baarheid**.

   ![Instellingen configureren](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Doel instellingen aanpassen, zoals in de tabel wordt samenvatten.

   | **Instelling** | **Details** |
   | --- | --- |
   | **Doel abonnement** | Het doel abonnement is standaard hetzelfde als het bron abonnement. Selecteer **aanpassen** als u een ander doel abonnement binnen dezelfde Azure Active Directory Tenant wilt selecteren. |
   | **Doellocatie** | De doelregio die wordt gebruikt voor herstel na noodgevallen.<br/><br/> Wij raden aan dezelfde doellocatie te gebruiken als de locatie van de Site Recovery-kluis. |
   | **Doel resource groep** | De resourcegroep in de doelregio waarin Azure-VM’s zich na een failover bevinden.<br/><br/> Site Recovery maakt standaard een nieuwe resource groep in de doel regio met een `asr` achtervoegsel. De locatie van de doel resource groep kan een wille keurige regio zijn, behalve de regio waarin uw virtuele bron machines worden gehost. |
   | **Virtueel netwerk van doel** | Het netwerk in de doelregio waar VM’s zich na een failover bevinden.<br/><br/> Site Recovery maakt standaard een nieuw virtueel netwerk (en subnetten) in de doel regio met een `asr` achtervoegsel. |
   | **Cache-opslag accounts** | Site Recovery maakt gebruik van een opslagaccount in de bronregio. Wijzigingen in de bron-VM's worden naar dit account verzonden vóór replicatie naar de doellocatie.<br/><br/> Als u een cache-opslag account met ingeschakelde firewall gebruikt, zorg er dan voor dat u **vertrouwde micro soft-Services toestaan**inschakelt. [Meer informatie](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Zorg er ook voor dat u toegang tot ten minste één subnet van de bron-Vnet toestaat. |
   | **Doel opslag accounts (bron-VM maakt gebruik van niet-beheerde schijven)** | Met Site Recovery wordt standaard een nieuw opslagaccount in de doelregio gemaakt om het bron-VM-opslagaccount te spiegelen.<br/><br/> Schakel **vertrouwde micro soft-Services toestaan** in als u gebruikmaakt van een cache-opslag account met firewall mogelijkheden. |
   | **Beheerde replica schijven (als de bron-VM gebruikmaakt van beheerde schijven)** | Standaard maakt Site Recovery replica beheerde schijven in de doel regio om de beheerde schijven van de bron-VM te spie gelen met hetzelfde opslag type (Standard of Premium) als de beheerde schijf van de bron-VM. U kunt het schijf type alleen aanpassen. |
   | **Doel beschikbaarheids sets** | Azure Site Recovery maakt standaard een nieuwe beschikbaarheidsset in de doel regio met de naam `asr` achtervoegsel voor het onderdeel Vm's in een beschikbaarheidsset in de bron regio. Als er al een beschikbaarheidsset is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt. |
   | **Doel beschikbaarheids zones** | In Site Recovery wordt standaard hetzelfde zonenummer toegewezen als de bronregio in de doelregio, als de doelregio ondersteuning biedt voor beschikbaarheidszones.<br/><br/> Als de doel regio geen beschikbaarheids zones ondersteunt, worden de doel-Vm's standaard geconfigureerd als afzonderlijke exemplaren.<br/><br/> Selecteer **aanpassen** om vm's te configureren als onderdeel van een beschikbaarheidsset in de doel regio.<br/><br/> U kunt het beschikbaarheids type (één exemplaar, beschikbaarheidsset of beschikbaarheids zone) niet wijzigen nadat replicatie is ingeschakeld. Als u het beschikbaarheids type wilt wijzigen, schakelt u replicatie uit en inschakelen. |

1. Als u de instellingen voor het replicatie beleid wilt aanpassen, selecteert u **aanpassen** naast **replicatie beleid**en wijzigt u de instellingen naar wens.

   | **Instelling** | **Details** |
   | --- | --- |
   | **Naam van het replicatie beleid** | Beleidsnaam. |
   | **Bewaar periode van het herstel punt** | In Site Recovery worden herstelpunten standaard 24 uur bewaard. U kunt een waarde van 1 tot 72 uur configureren. |
   | **Frequentie van de app-consistente moment opname** | In Site Recovery wordt standaard elke 4 uur een app-consistente momentopname gemaakt. U kunt een waarde van 1 tot 12 uur configureren.<br/><br/> Een app-consistente moment opname is een tijdgebonden moment opname van de toepassings gegevens in de virtuele machine. Met Volume Shadow Copy Service (VSS) wordt ervoor gezorgd dat apps op de VM een consistente status hebben wanneer er een momentopname wordt gemaakt. |
   | **Replicatie groep** | Als uw toepassing consistentie tussen meerdere VM's nodig heeft, kunt u een replicatiegroep maken voor deze VM’s. Standaard maken de geselecteerde VM’s geen deel uit van een replicatiegroep. |

1. Selecteer in **Aanpassen** de optie **Ja** voor consistentie van multi-VM, als u VM’s wilt toevoegen aan een nieuwe of bestaande replicatiegroep. Selecteer vervolgens **OK**.

   > [!NOTE]
   > - Alle computers in een replicatie groep hebben gedeelde crash-consistente en toepassings consistente herstel punten wanneer er een failover wordt uitgevoerd.
   > - Het inschakelen van multi-VM-consistentie kan de prestaties van de werk belasting beïnvloeden (CPU-intensief). Deze moet alleen worden gebruikt als computers dezelfde werk belasting uitvoeren en u consistentie op meerdere computers nodig hebt.
   > - U kunt Maxi maal 16 virtuele machines in een replicatie groep hebben.
   > - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004. Zorg ervoor dat er geen firewall is die de interne communicatie tussen de virtuele machines op deze poort blokkeert.
   > - Zorg ervoor dat het uitgaande verkeer op poort 20004 hand matig wordt geopend in overeenstemming met de richt lijnen voor de Linux-versie voor Linux Vm's in een replicatie groep.

### <a name="configure-encryption-settings"></a>Versleutelingsinstellingen configureren

Als de bron-VM voor Azure Disk Encryption (ADE) is ingeschakeld, controleert u de instellingen.

1. Controleer de instellingen:
   1. **Sleutel kluizen voor schijf versleuteling**: standaard maakt site Recovery een nieuwe sleutel kluis op de versleutelings sleutels van de bron-VM-schijf, met een `asr` achtervoegsel. Als de sleutel kluis al bestaat, wordt deze opnieuw gebruikt.
   1. Key **Encryption Key-kluizen**: standaard maakt site Recovery een nieuwe sleutel kluis in de doel regio. De naam heeft een `asr` achtervoegsel en is gebaseerd op de sleutel versleutelings sleutels van de bron-VM. Als de sleutel kluis die is gemaakt door Site Recovery al bestaat, wordt deze opnieuw gebruikt.
1. Selecteer **aanpassen** om aangepaste sleutel kluizen te selecteren.

> [!NOTE]
> Momenteel worden alleen Azure-VM's met Windows-besturingssystemen die zijn [ingeschakeld voor versleuteling met Azure AD-app](https://aka.ms/ade-aad-app), ondersteund voor Azure Site Recovery.

### <a name="track-replication-status"></a>Replicatiestatus volgen

Nadat de replicatie is ingeschakeld, kunt u de status van de taak bijhouden.

1. Selecteer bij **instellingen**de optie **vernieuwen** om de meest recente status op te halen.
1. U kunt als volgt de voortgang en status bijhouden:
   1. Houd de voortgang bij van de taak **Beveiliging inschakelen** via **Instellingen** > **Taken** > **Site Recovery-taken**.
   1. In **Instellingen** > **Gerepliceerde Items** kunt u de status van VM’s en de voortgang van de initiële replicatie bekijken. Selecteer de virtuele machine om in te zoomen op de instellingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u herstel na noodgevallen geconfigureerd voor een Azure-VM. U kunt nu een nood herstel analyse uitvoeren om te controleren of de failover werkt zoals verwacht.

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](azure-to-azure-tutorial-dr-drill.md)
