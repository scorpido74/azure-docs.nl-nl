---
title: Azure VM-herstel instellen met Azure Site Recovery
description: Leer hoe u herstel na noodgevallen van Azure-VM’s naar een andere Azure-regio kunt instellen met de Azure Site Recovery-service.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292485"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Disaster recovery instellen voor Azure VM's

De [Azure Site Recovery-service](site-recovery-overview.md) draagt bij aan uw strategie voor noodherstel door replicatie, failover en failback van on-premises machines en Virtuele Azure-machines (VM's) te beheren en te orkestreren.

In deze zelfstudie ziet u hoe u noodherstel voor Azure VM's instelt door ze van de ene Azure-regio naar de andere te repliceren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Recovery Services-kluis maken
> * Doelresource-instellingen controleren
> * Uitgaande netwerkconnectiviteit instellen voor VM's
> * Replicatie inschakelen voor een VM

> [!NOTE]
> Dit artikel biedt instructies voor het implementeren van herstel na noodgeval met de eenvoudigste instellingen. Als u meer wilt weten over aangepaste instellingen, bekijkt u de artikelen in de [sectie Hoe.](azure-to-azure-how-to-enable-replication.md)

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

- De [architectuur en onderdelen voor dit scenario](concepts-azure-to-azure-architecture.md) doornemen.
- Bekijk de [ondersteuningsvereisten](site-recovery-support-matrix-azure-to-azure.md) voordat u begint.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Maak de kluis in elke gewenste regio, met uitzondering van de bronregio.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**. Selecteer vervolgens **it-& Back-up-** > **en siteherstel van**beheertools .
1. Geef in **Naam**een vriendelijke naam op om de kluis te identificeren. Als u meer dan één abonnement hebt, selecteert u het gewenste abonnement.
1. Maak een resourcegroep of selecteer een bestaande resourcegroep. Geef een Azure-regio op. Zie Geografische beschikbaarheid in [de prijsdetails van Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Als u de kluis vanuit het dashboard wilt openen, selecteert u **Vastmaken aan het dashboard** en selecteert u **Vervolgens Maken**.

   ![Nieuwe kluis](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

De nieuwe kluis wordt toegevoegd op het **Dashboard** onder **Alle resources** en op de hoofdpagina van **Recovery Services-kluizen**.

## <a name="verify-target-resource-settings"></a>Doelresource-instellingen controleren

Controleer uw Azure-abonnement voor de doelregio.

- Controleer of u een Azure-abonnement hebt waarmee u VM´s kunt maken in de doelregio. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.
- Zorg ervoor dat uw abonnement voldoende resources heeft die ondersteuning bieden voor VM’s met grootten die overeenkomen met de bron-VM’s. Site Recovery kiest voor de doel-VM dezelfde of de dichtstbijzijnde grootte.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Uitgaande netwerkconnectiviteit instellen voor VM's

Om ervoor te zorgen dat Site Recovery werkt zoals verwacht, moet u de uitgaande netwerkconnectiviteit wijzigen van de VM's die u wilt repliceren.

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor het gebruiken van een verificatieproxy om netwerkconnectiviteit te beheren.

### <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een op URL gebaseerde firewallproxy gebruikt om uitgaande connectiviteit te beheren, geeft u toegang tot deze URL's:

| **Url** | **Details** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| `login.microsoftonline.com` | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| `*.servicebus.windows.net` | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Uitgaande connectiviteit voor IP-adresbereiken

Als u een NETWERKbeveiligingsgroep (NSG) gebruikt, maakt u nsg-regels op basis van servicetags voor toegang tot Azure Storage, Azure Active Directory, Site Recovery-service en siteherstelcontrole. [Meer informatie](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Azure VM-certificaten controleren

Controleer of de VM’s die u wilt repliceren, beschikken over de meest recente basiscertificaten. Als dit niet het zo is, kan de vm niet worden geregistreerd bij Site recovery vanwege beveiligingsbeperkingen.

- Voor Windows-VM’s moet u de meest recente Windows-updates op de VM installeren, zodat alle vertrouwde basiscertificaten op de machine aanwezig zijn. In een niet-verbonden omgeving moet u de standaardprocedures van Windows Update en de standaardprocedures voor het bijwerken van de certificaten van uw organisatie volgen.
- Voor Linux-VM’s volgt u de richtlijnen van de Linux-distributeur voor het verkrijgen van de meest recente basiscertificaten en de certificaatintrekkingslijst op de VM.

## <a name="set-permissions-on-the-account"></a>Machtigingen instellen voor het account

Azure Site Recovery heeft drie ingebouwde rollen voor het beheren van Site Recovery-beheerbewerkingen.

- **Site Recovery-inzender** - deze rol bevat alle machtigingen die nodig zijn om Azure Site Recovery-bewerkingen in een Recovery Services-kluis te kunnen beheren. Een gebruiker met deze rol kan echter geen Recovery Services-kluis maken of verwijderen, noch toegangsrechten aan andere gebruikers toewijzen. Deze rol is het meest geschikt voor beheerders die herstel na noodgevallen voor toepassingen of hele organisaties kunnen inschakelen en beheren.

- **Site Recovery-operator**: deze rol heeft machtigingen om failover- en failback-bewerkingen uit te voeren en te beheren. Een gebruiker met deze rol kan replicatie niet in- of uitschakelen, geen kluizen maken of verwijderen, en geen nieuwe infrastructuur registreren of toegangsrechten toewijzen aan andere gebruikers. Deze rol is het meest geschikt voor een operator die failover-overschakeling kan uitvoeren voor virtuele machines of applicaties in opdracht van toepassingseigenaren en IT-beheerders. Na de oplossing van de ramp kan de operator voor noodherstel de virtuele machines opnieuw beschermen en failbacken.

- **Site Recovery-lezer** - deze rol heeft machtigingen voor het weergeven van alle beheerbewerkingen van Site Recovery. Deze rol is het meest geschikt is voor een IT-leidinggevende die de huidige beschermingsstatus kan bewaken en ondersteuningstickets kan aanvragen.

Meer informatie over [ingebouwde azure RBAC-rollen](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Replicatie inschakelen voor een VM

In de volgende secties wordt beschreven hoe u replicatie inschakelen.

### <a name="select-the-source"></a>De bron selecteren

Als u wilt beginnen met het instellen van de replicatie, kiest u de bron waar uw Azure VM's worden uitgevoerd.

1. Ga naar **vaults van Recovery Services,** selecteer de naam van de kluis en selecteer **+Repliceren**.
1. Selecteer **Azure**voor de **bron**.
1. Bij **Bronlocatie** selecteert u de Azure-bronregio waar uw VM’s momenteel worden uitgevoerd.
1. Selecteer het **Bronabonnement** waar de virtuele machines worden uitgevoerd. Dit kan elk abonnement zijn binnen dezelfde Azure Active Directory-tenant waar uw Recovery Services-kluis zich bevindt.
1. Selecteer de **groep Bronbron**en selecteer **OK** om de instellingen op te slaan.

   ![Bron instellen](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>De VM’s selecteren

Site Recovery haalt een lijst op van de VM’s die zijn gekoppeld aan het abonnement en de resourcegroep/cloudservice.

1. Selecteer in **Virtuele machines** de VM’s die u wilt repliceren.
1. Selecteer **OK**.

### <a name="configure-replication-settings"></a>Replicatie-instellingen configureren

Site Recovery maakt standaardinstellingen en replicatiebeleid voor de doelregio. U deze instellingen naar behoefte wijzigen.

1. Selecteer **Instellingen** om de doel- en replicatie-instellingen weer te geven.

1. Als u de standaarddoelinstellingen wilt overschrijven, selecteert u **Aanpassen** naast **resourcegroep, netwerk, opslag en beschikbaarheid**.

   ![Instellingen configureren](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Pas doelinstellingen aan zoals samengevat in de tabel.

   | **Instelling** | **Details** |
   | --- | --- |
   | **Doelabonnement** | Standaard is het doelabonnement hetzelfde als het bronabonnement. Selecteer **Aanpassen** om een ander doelabonnement te selecteren binnen dezelfde Azure Active Directory-tenant. |
   | **Doellocatie** | De doelregio die wordt gebruikt voor herstel na noodgevallen.<br/><br/> Wij raden aan dezelfde doellocatie te gebruiken als de locatie van de Site Recovery-kluis. |
   | **Doelgroepgroep** | De resourcegroep in de doelregio waarin Azure-VM’s zich na een failover bevinden.<br/><br/> Siteherstel maakt standaard een nieuwe resourcegroep in `asr` het doelgebied met een achtervoegsel. De locatie van de doelgroep kan elke regio zijn, behalve de regio waarin uw virtuele bronmachines worden gehost. |
   | **Virtueel netwerk targeten** | Het netwerk in de doelregio waar VM’s zich na een failover bevinden.<br/><br/> Siteherstel maakt standaard een nieuw virtueel netwerk (en subnetten) `asr` in het doelgebied met een achtervoegsel. |
   | **Opslagaccounts in cache** | Site Recovery maakt gebruik van een opslagaccount in de bronregio. Wijzigingen in de bron-VM's worden naar dit account verzonden vóór replicatie naar de doellocatie.<br/><br/> Als u een cacheopslagaccount met firewalls gebruikt, controleert u of u **vertrouwde Microsoft-services toestaan inschakelt.** [Meer informatie](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Zorg er ook voor dat u toegang geeft tot ten minste één subnet van de bron Vnet. |
   | **Doelopslagaccounts (bron-VM maakt gebruik van niet-beheerde schijven)** | Met Site Recovery wordt standaard een nieuw opslagaccount in de doelregio gemaakt om het bron-VM-opslagaccount te spiegelen.<br/><br/> **Schakel Vertrouwde Microsoft-services toestaan in** als u een cacheopslagaccount met firewall gebruikt. |
   | **Door replicabeheerde schijven (Als bron-VM beheerde schijven gebruikt)** | Siteherstel maakt standaard replicabeheerde schijven in het doelgebied om de beheerde schijven van de bron-VM te spiegelen met hetzelfde opslagtype (standaard of premium) als de beheerde schijf van de bron-VM. U het schijftype alleen aanpassen. |
   | **Beschikbaarheidssets voor doel** | Azure Site Recovery maakt standaard een nieuwe beschikbaarheidsset in `asr` het doelgebied met de naam met achtervoegsel voor het VMs-deel van een beschikbaarheidsset in het brongebied. Als er al beschikbaarheidsset is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt. |
   | **Beschikbaarheidszones voor doel** | In Site Recovery wordt standaard hetzelfde zonenummer toegewezen als de bronregio in de doelregio, als de doelregio ondersteuning biedt voor beschikbaarheidszones.<br/><br/> Als het doelgebied geen beschikbaarheidszones ondersteunt, worden de doel-VM's standaard geconfigureerd als afzonderlijke exemplaren.<br/><br/> Selecteer **Aanpassen** om VM's te configureren als onderdeel van een beschikbaarheidsset in het doelgebied.<br/><br/> U het beschikbaarheidstype (enkele instantie, beschikbaarheidsset of beschikbaarheidszone) niet wijzigen nadat u replicatie hebt ingeschakeld. Als u het beschikbaarheidstype wilt wijzigen, schakelt u replicatie uit en schakelt u replicatie in. |

1. Als u de instellingen voor replicatiebeleid wilt aanpassen, selecteert u **Aanpassen** naast **replicatiebeleid**en wijzigt u de instellingen indien nodig.

   | **Instelling** | **Details** |
   | --- | --- |
   | **Naam replicatiebeleid** | Beleidsnaam. |
   | **Herstelpunt behoud** | In Site Recovery worden herstelpunten standaard 24 uur bewaard. U kunt een waarde van 1 tot 72 uur configureren. |
   | **App-consistente momentopnamefrequentie** | In Site Recovery wordt standaard elke 4 uur een app-consistente momentopname gemaakt. U kunt een waarde van 1 tot 12 uur configureren.<br/><br/> Een momentopname die consistent is in de app, is een point-in-time momentopname van de toepassingsgegevens in de VM. Met Volume Shadow Copy Service (VSS) wordt ervoor gezorgd dat apps op de VM een consistente status hebben wanneer er een momentopname wordt gemaakt. |
   | **Replicatiegroep** | Als uw toepassing consistentie tussen meerdere VM's nodig heeft, kunt u een replicatiegroep maken voor deze VM’s. Standaard maken de geselecteerde VM’s geen deel uit van een replicatiegroep. |

1. Selecteer in **Aanpassen** de optie **Ja** voor consistentie van multi-VM, als u VM’s wilt toevoegen aan een nieuwe of bestaande replicatiegroep. Selecteer vervolgens **OK**.

   > [!NOTE]
   > - Alle machines in een replicatiegroep hebben consistent crashconsistent en app-consistent herstelpunten gedeeld wanneer ze zijn mislukt.
   > - Het inschakelen van multi-VM-consistentie kan de prestaties van de werkbelasting beïnvloeden (het is CPU-intensief). Het mag alleen worden gebruikt als machines dezelfde werkbelasting uitvoeren en u consistentie nodig hebt voor meerdere machines.
   > - U maximaal 16 VM's in een replicatiegroep hebben.
   > - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004. Zorg ervoor dat er geen firewall is die de interne communicatie tussen de VM's via deze poort blokkeert.
   > - Voor Linux VM's in een replicatiegroep moet u ervoor zorgen dat het uitgaande verkeer op poort 20004 handmatig wordt geopend in overeenstemming met de richtlijnen voor de Linux-versie.

### <a name="configure-encryption-settings"></a>Versleutelingsinstellingen configureren

Als de bron-VM Azure-schijfversleuteling (ADE) heeft ingeschakeld, controleert u de instellingen.

1. Controleer de instellingen:
   1. **Schijfversleutelingssleutelkluizen:** Site recovery maakt standaard een nieuwe sleutelkluis op de `asr` bron-VM-schijfversleutelingssleutels, met een achtervoegsel. Als de sleutelkluis al bestaat, wordt deze opnieuw gebruikt.
   1. **Key encryption key vaults:** Standaard maakt Site Recovery een nieuwe sleutelkluis in het doelgebied. De naam `asr` heeft een achtervoegsel en is gebaseerd op de sleutelcoderingssleutels van de bron-VM-sleutel. Als de sleutelkluis die door Site Recovery is gemaakt, al bestaat, wordt deze opnieuw gebruikt.
1. Selecteer **Aanpassen** om aangepaste sleutelkluizen te selecteren.

> [!NOTE]
> Momenteel worden alleen Azure-VM's met Windows-besturingssystemen die zijn [ingeschakeld voor versleuteling met Azure AD-app](https://aka.ms/ade-aad-app), ondersteund voor Azure Site Recovery.

### <a name="track-replication-status"></a>Replicatiestatus volgen

Nadat replicatie is ingeschakeld, u de status van de taak bijhouden.

1. Selecteer **Vernieuwen** in **Instellingen**om de nieuwste status op te halen.
1. U kunt als volgt de voortgang en status bijhouden:
   1. Houd de voortgang bij van de taak **Beveiliging inschakelen** via **Instellingen** > **Taken** > **Site Recovery-taken**.
   1. In **Instellingen** > **gerepliceerde items**u de status van VM's en de voortgang van de eerste replicatie weergeven. Selecteer de VM om in te zoomen op de instellingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u herstel na noodgevallen geconfigureerd voor een Azure-VM. Nu u een noodherstelboor uitvoeren om te controleren of failover werkt zoals verwacht.

> [!div class="nextstepaction"]
> [Noodherstelanalyse uitvoeren](azure-to-azure-tutorial-dr-drill.md)
