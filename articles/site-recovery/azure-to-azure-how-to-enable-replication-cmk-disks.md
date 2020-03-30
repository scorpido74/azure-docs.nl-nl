---
title: Replicatie inschakelen voor versleutelde Azure VM's in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u replicatie voor VM's configureert met door de klant beheerde sleutel (CMK) die schijven van het ene Azure-gebied naar de andere is ingeschakeld met behulp van Siteherstel.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897960"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Machines repliceren met schijven met door de klant beheerde sleutels (CMK)

In dit artikel wordt beschreven hoe u Azure VM's repliceren met door de klant beheerde sleutels (CMK) ingeschakelde beheerde schijven, van de ene Azure-regio naar de andere.

## <a name="prerequisite"></a>Vereiste
U moet de schijfversleutelingsset(s) in het doelgebied voor het doelabonnement maken voordat replicatie wordt ingeschakeld voor uw virtuele machines met cmk-gestuurde beheerde schijven.

## <a name="enable-replication"></a>Replicatie inschakelen

De primaire Azure-regio is bijvoorbeeld Oost-Azië en de secundaire regio Zuidoost-Azië.

1. Selecteer **+Repliceren**in de kluis .
2. Let op de volgende velden.
    - **Bron:** Het punt van oorsprong van de VM's, dat in dit geval **Azure**is.
    - **Bronlocatie:** de Azure-regio waar u uw virtuele machines wilt beschermen. In dit voorbeeld is de bronlocatie 'Oost-Azië'.
    - **Implementatiemodel:** het Azure-implementatiemodel van de bronmachines.
    - **Bronabonnement**: Het abonnement waartoe uw bronvirtuele machines behoren. Het kan elk abonnement zijn dat zich in dezelfde Azure Active Directory-tenant bevindt als de kluis van uw herstelservices.
    - **Resourcegroep:** de resourcegroep waartoe uw bronvirtuele machines behoren. Alle VM's in de geselecteerde resourcegroep worden in de volgende stap voor bescherming weergegeven.

3. Selecteer **in virtuele machines** > **virtuele machines**elke virtuele machine die u wilt repliceren. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**.

4. In **Instellingen**u de volgende doelsite-instellingen configureren.

    - **Doellocatie:** de locatie waar uw brongegevens van virtuele machine worden gerepliceerd. Site recovery biedt een lijst met geschikte doelregio's op basis van de locatie van de geselecteerde machine. We raden u aan dezelfde locatie te gebruiken als de locatie van de Vault Recovery Services.
    - **Doelabonnement:** het doelabonnement dat wordt gebruikt voor noodherstel. Standaard is het doelabonnement hetzelfde als het bronabonnement.
    - **Doelgroepgroep**: de resourcegroep waartoe al uw gerepliceerde virtuele machines behoren. Siteherstel maakt standaard een nieuwe resourcegroep in de doelgroep. De naam `asr` krijgt het achtervoegsel. Als er al een resourcegroep bestaat die is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt. U er ook voor kiezen om het aan te passen, zoals in de volgende sectie wordt weergegeven. De locatie van de doelgroep kan elke Azure-regio zijn, behalve het gebied waar de virtuele bronmachines worden gehost.
    - **Virtueel netwerk target:** Siteherstel maakt standaard een nieuw virtueel netwerk in het doelgebied. De naam `asr` krijgt het achtervoegsel. Het is toegewezen aan uw bronnetwerk en wordt gebruikt voor toekomstige bescherming. [Meer informatie](site-recovery-network-mapping-azure-to-azure.md) over netwerktoewijzing.
    - **Doelopslagaccounts (als uw bron-VM geen beheerde schijven gebruikt)**: Siterecovery maakt standaard een nieuw doelopslagaccount door de configuratie van uw bron-VM-opslag na te bootsen. Als er al een opslagaccount bestaat, wordt het opnieuw gebruikt.
    - **Replicabeheerde schijven (als uw bron-VM beheerde schijven gebruikt):** Siterecovery maakt nieuwe replicabeheerde schijven in het doelgebied om de beheerde schijven van de bron-VM van hetzelfde opslagtype (standaard of premium) te spiegelen als de beheerde schijven van de bron-VM.
    - **Cacheopslagaccounts:** Siteherstel heeft een extra opslagaccount nodig, *cacheopslag* genaamd in het brongebied. Alle wijzigingen op de bron-VM's worden bijgehouden en naar het cacheopslagaccount verzonden. Ze worden dan gerepliceerd naar de doellocatie.
    - **Beschikbaarheidsset**: Siteherstel maakt standaard een nieuwe beschikbaarheiddie is ingesteld in het doelgebied. De naam `asr` heeft het achtervoegsel. Als er al een beschikbaarheidsset bestaat die is gemaakt door Site Recovery, wordt deze opnieuw gebruikt.
    - **Schijfversleutelingssets (DES):** Site recovery vereist dat de schijfcoderingsset(en) moet worden gebruikt voor replica- en doelbeheerde schijven. U moet DES vooraf maken in het doelabonnement en het doelgebied voordat u de replicatie inschakelt. Standaard is een DES niet geselecteerd. U moet op 'Aanpassen' klikken om een DES per bronschijf te kiezen.
    - **Replicatiebeleid:** definieert de instellingen voor herstelpuntbewaargeschiedenis en app-consistente momentopnamefrequentie. Siteherstel maakt standaard een nieuw replicatiebeleid met standaardinstellingen van *24 uur* voor herstelpuntbehoud en *60 minuten* voor de app-consistente momentopnamefrequentie.

    ![Replicatie inschakelen voor machine met CMK-schijven](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Doelbronnen aanpassen

Voer deze stappen uit om de standaarddoelinstellingen voor siteherstel te wijzigen.

1. Selecteer **Aanpassen** naast 'Doelabonnement' om het standaarddoelabonnement te wijzigen. Selecteer het abonnement in de lijst met abonnementen die beschikbaar zijn in de Azure AD-tenant.

2. Selecteer **Aanpassen** naast 'Resourcegroep, Netwerk-, opslag- en beschikbaarheidssets' om de volgende standaardinstellingen te wijzigen:
    - Selecteer **voor doelgroepgroep Target**de brongroep in de lijst met resourcegroepen op de doellocatie van het abonnement.
    - Selecteer **voor virtueel netwerk Target**het netwerk uit een lijst met virtuele netwerken op de doellocatie.
    - Voor **beschikbaarheidsset**u beschikbaarheidssetinstellingen toevoegen aan de VM, als deze deel uitmaken van een beschikbaarheidsset in het brongebied.
    - Selecteer **voor Doelopslagaccounts**het account dat u wilt gebruiken.

3. Selecteer **Aanpassen** naast 'Instellingen voor opslagversleuteling' om de doel-DES te selecteren voor elke door de klant beheerde sleutel (CMK) ingeschakelde bronbeheerde schijf. Op het moment van selectie u ook zien aan welke doelsleutelkluis de DES is gekoppeld.

4. Selecteer **Doelbron** > **maken Replicatie inschakelen**.
5. Nadat de VM's zijn ingeschakeld voor replicatie, u de status van de VM's controleren onder **Gerepliceerde items**.

![Replicatie inschakelen voor machine met CMK-schijven](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Tijdens de eerste replicatie kan het enige tijd duren voordat de status is vernieuwd, zonder duidelijke voortgang. Klik **op Vernieuwen** om de nieuwste status te krijgen.

## <a name="faqs"></a>Veelgestelde vragen

* Ik heb CMK ingeschakeld op een bestaand gerepliceerd item, hoe kan ik ervoor zorgen dat CMK ook op de doelregio wordt toegepast?

    U de naam van de door replica beheerde schijf (gemaakt door Azure Site Recovery in het doelgebied) achterhalen en DES aan deze replicaschijf koppelen. U de DES-details echter niet meer zien in het schijvenblad zodra u het hebt bevestigd. U er ook voor kiezen om de replicatie van de vm uit te schakelen en deze opnieuw in te schakelen. Het zorgt ervoor dat u DES- en sleutelkluisdetails in het schijvenblad voor het gerepliceerde item ziet.

* Ik heb een nieuwe CMK-schijf toegevoegd aan het gerepliceerde item. Hoe kan ik deze schijf repliceren met Azure Site Recovery?

    Toevoeging van een nieuwe CMK-schijf aan een bestaand gerepliceerd item wordt niet ondersteund. Schakel de replicatie uit en schakel de replicatie opnieuw in voor de virtuele machine.

