---
title: Replicatie inschakelen voor versleutelde Azure VM's in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u replicatie configureert voor VM's met Azure-schijfversleuteling van de ene Azure-regio naar de andere met behulp van Siteherstel.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408627"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Virtuele machines met Azure-schijfversleuteling repliceren naar een andere Azure-regio

In dit artikel wordt beschreven hoe u Azure VM's repliceren met ADE-schijfversleuteling (Azure Disk Encryption) ingeschakeld, van de ene Azure-regio naar de andere.

>[!NOTE]
> Siteherstel ondersteunt momenteel ADE, met en zonder Azure Active Directory (AAD) voor VM's met Windows-besturingssystemen. Voor Linux-besturingssystemen ondersteunen we Alleen ADE zonder AAD. Bovendien moeten de VM's voor machines met ADE 1.1 (zonder AAD) beheerde schijven gebruiken. VM's met onbeheerde schijven worden niet ondersteund. Als u overschakelt van ADE 0.1 (met AAD) naar 1.1, moet u replicatie uitschakelen en replicatie voor een virtuele machine inschakelen nadat u 1.1 hebt ingeschakeld.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Vereiste gebruikersmachtigingen
Siteherstel vereist dat de gebruiker machtigingen heeft om de sleutelkluis in het doelgebied te maken en sleutels uit de kluis van de sleutelvan het brongebied naar de sleutelkluis van het doelgebied te kopiëren.

Om replicatie van vm's met schijfversleuteling vanuit de Azure-portal mogelijk te maken, heeft de gebruiker de volgende machtigingen nodig voor zowel het brongebied als de **bronsleutelkluizen.**

- Machtigingen voor sleutelkluizen
    - Lijst, Maken en oppakken
    
- Geheime machtigingen voor belangrijke kluizen
    - Geheime beheeroperaties
        - Oppakken, aanbieden en instellen
    
- Key vault key permissions (alleen vereist als de VM's sleutelversleutelingssleutel gebruiken om schijfversleutelingssleutels te versleutelen)
    - Belangrijke beheerbewerkingen
        - Oppakken, aanbieden en maken
    - Cryptografische bewerkingen
        - Decoderen en versleutelen

Als u machtigingen wilt beheren, gaat u naar de sleutelbron in de portal. Voeg de vereiste machtigingen voor de gebruiker toe. In het volgende voorbeeld ziet u hoe u machtigingen inschakelt voor de sleutelkluis *ContosoWeb2Keyvault*, die zich in het brongebied bevindt.

1. Ga naar **Het** > Beleid van Home**Keyvaults** > **ContosoWeb2KeyVault > Toegang**.

   ![Venster Machtigingen voor sleutelkluizen](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. U zien dat er geen gebruikersmachtigingen zijn. Selecteer **Nieuw toevoegen**. Voer de gebruikers- en machtigingengegevens in.

   ![Keyvault-machtigingen](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Als de gebruiker die disaster recovery (DR) inschakelt geen machtigingen heeft om de sleutels te kopiëren, kan een beveiligingsbeheerder die over de juiste machtigingen beschikt, het volgende script gebruiken om de versleutelingsgeheimen en sleutels naar het doelgebied te kopiëren.

Als u machtigingen wilt oplossen, raadpleegt u later in dit artikel belangrijke problemen met de machtigingen voor [de kluis.](#trusted-root-certificates-error-code-151066)

>[!NOTE]
>Als u replicatie van vm's met schijfversleuteling vanaf de portal wilt inschakelen, hebt u ten minste machtigingen voor de sleutelkluizen, geheimen en sleutels nodig.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Schijfversleutelingssleutels naar het DR-gebied kopiëren met het PowerShell-script

1. [Open de raw-scriptcode 'CopyKeys'.](https://aka.ms/ade-asr-copy-keys-code)
2. Kopieer het script naar een bestand en noem het **Copy-keys.ps1**.
3. Open de Windows PowerShell-toepassing en ga naar de map waar u het bestand hebt opgeslagen.
4. Kopieersleutels.ps1 uitvoeren.
5. Geef Azure-referenties op om u aan te melden.
6. Selecteer het **Azure-abonnement** van uw VM's.
7. Wacht tot de resourcegroepen zijn geladen en selecteer vervolgens de **resourcegroep** van uw VM's.
8. Selecteer de VM's in de lijst die wordt weergegeven. Alleen VM's die zijn ingeschakeld voor schijfversleuteling staan op de lijst.
9. Selecteer de **doellocatie**.

    - **Kluiskluizen van schijfversleutelingssleutel**
    - **Key encryption key vaults**

   Siteherstel maakt standaard een nieuwe sleutelkluis in het doelgebied. De naam van de kluis heeft een 'asr'-achtervoegsel dat is gebaseerd op de versleutelingssleutels van de bron-VM-schijf. Als er al een sleutelkluis bestaat die is gemaakt door Site Recovery, wordt deze opnieuw gebruikt. Selecteer indien nodig een andere sleutelkluis in de lijst.

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
    - **Doelgroepgroep**: de resourcegroep waartoe al uw gerepliceerde virtuele machines behoren. Siteherstel maakt standaard een nieuwe resourcegroep in de doelgroep. De naam krijgt het "asr" achtervoegsel. Als er al een resourcegroep bestaat die is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt. U er ook voor kiezen om het aan te passen, zoals in de volgende sectie wordt weergegeven. De locatie van de doelgroep kan elke Azure-regio zijn, behalve het gebied waar de virtuele bronmachines worden gehost.
    - **Virtueel netwerk target:** Siteherstel maakt standaard een nieuw virtueel netwerk in het doelgebied. De naam krijgt het "asr" achtervoegsel. Het is toegewezen aan uw bronnetwerk en wordt gebruikt voor toekomstige bescherming. [Meer informatie](site-recovery-network-mapping-azure-to-azure.md) over netwerktoewijzing.
    - **Doelopslagaccounts (als uw bron-VM geen beheerde schijven gebruikt)**: Siterecovery maakt standaard een nieuw doelopslagaccount door de configuratie van uw bron-VM-opslag na te bootsen. Als er al een opslagaccount bestaat, wordt het opnieuw gebruikt.
    - **Replicabeheerde schijven (als uw bron-VM beheerde schijven gebruikt):** Siterecovery maakt nieuwe replicabeheerde schijven in het doelgebied om de beheerde schijven van de bron-VM van hetzelfde opslagtype (standaard of premium) te spiegelen als de beheerde schijven van de bron-VM.
    - **Cacheopslagaccounts:** Siteherstel heeft een extra opslagaccount nodig, *cacheopslag* genaamd in het brongebied. Alle wijzigingen op de bron-VM's worden bijgehouden en naar het cacheopslagaccount verzonden. Ze worden dan gerepliceerd naar de doellocatie.
    - **Beschikbaarheidsset**: Siteherstel maakt standaard een nieuwe beschikbaarheiddie is ingesteld in het doelgebied. De naam heeft het achtervoegsel "asr". Als er al een beschikbaarheidsset bestaat die is gemaakt door Site Recovery, wordt deze opnieuw gebruikt.
    - **Schijfversleutelingssleutelkluizen:** Siteherstel maakt standaard een nieuwe sleutelkluis in het doelgebied. Het heeft een "asr" achtervoegsel dat is gebaseerd op de bron VM schijf encryptiesleutels. Als er al een sleutelkluis is gemaakt die is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt.
    - **Key encryption key vaults:** Standaard maakt Site Recovery een nieuwe sleutelkluis in het doelgebied. De naam heeft een "asr" achtervoegsel dat is gebaseerd op de bron VM sleutel encryptiesleutels. Als er al een sleutelkluis is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt.
    - **Replicatiebeleid:** definieert de instellingen voor herstelpuntbewaargeschiedenis en app-consistente momentopnamefrequentie. Siteherstel maakt standaard een nieuw replicatiebeleid met standaardinstellingen van *24 uur* voor herstelpuntbehoud en *60 minuten* voor de app-consistente momentopnamefrequentie.

## <a name="customize-target-resources"></a>Doelbronnen aanpassen

Voer deze stappen uit om de standaarddoelinstellingen voor siteherstel te wijzigen.

1. Selecteer **Aanpassen** naast 'Doelabonnement' om het standaarddoelabonnement te wijzigen. Selecteer het abonnement in de lijst met abonnementen die beschikbaar zijn in de Azure AD-tenant.

2. Selecteer **Aanpassen** naast 'Resourcegroep, Netwerk-, opslag- en beschikbaarheidssets' om de volgende standaardinstellingen te wijzigen:
    - Selecteer **voor doelgroepgroep Target**de brongroep in de lijst met resourcegroepen op de doellocatie van het abonnement.
    - Selecteer **voor virtueel netwerk Target**het netwerk uit een lijst met virtuele netwerken op de doellocatie.
    - Voor **beschikbaarheidsset**u beschikbaarheidssetinstellingen toevoegen aan de VM, als deze deel uitmaken van een beschikbaarheidsset in het brongebied.
    - Selecteer **voor Doelopslagaccounts**het account dat u wilt gebruiken.

2. Selecteer **Aanpassen** naast 'Versleutelingsinstellingen' om de volgende standaardinstellingen te wijzigen:
   - Selecteer voor de kluis van de **doelschijfversleuteling**de kluis van de doelschijfversleuteling in de lijst met sleutelkluizen op de doellocatie van het abonnement.
   - Selecteer voor de kluis van de **doelsleutelsleutel**de kluis van de doelsleutelsleutel in de lijst met sleutelkluizen op de doellocatie van het abonnement.

3. Selecteer **Doelbron** > **maken Replicatie inschakelen**.
4. Nadat de VM's zijn ingeschakeld voor replicatie, u de status van de VM's controleren onder **Gerepliceerde items**.

>[!NOTE]
>Tijdens de eerste replicatie kan het enige tijd duren voordat de status is vernieuwd, zonder duidelijke voortgang. Klik **op Vernieuwen** om de nieuwste status te krijgen.

## <a name="update-target-vm-encryption-settings"></a>Doel-VM-coderingsinstellingen bijwerken
In de volgende scenario's moet u de instellingen voor doel-VM-versleuteling bijwerken:
  - U hebt de replicatie van siteherstel ingeschakeld op de VM. Later hebt u schijfversleuteling ingeschakeld op de bron-VM.
  - U hebt de replicatie van siteherstel ingeschakeld op de VM. Later hebt u de schijfversleutelingssleutel of sleutelversleutelingssleutel op de bron-VM gewijzigd.

U [een script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) gebruiken om de versleutelingssleutels naar het doelgebied te kopiëren en vervolgens de doelversleutelingsinstellingen in **Herstelservices vault** > *replicated item* > **Properties** > Compute and Network bij te**werken.**

![Dialoogvenster ADE-instellingen bijwerken](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Problemen met belangrijke machtigingen voor kluizen oplossen tijdens Azure-to-Azure VM-replicatie

Azure Site Recovery vereist ten minste leestoestemming op de kluis Sleutelbron bron en schrijf toestemming op de doelgroep sleutelkluis om het geheim te lezen en te kopiëren naar de doelgroep sleutel kluis. 

**Oorzaak 1:** U hebt geen "GET"-toestemming op het **brongebied Sleutelkluis** om de toetsen te lezen. </br>
**Hoe op te lossen:** Ongeacht of u een abonnementsbeheerder bent of niet, het is belangrijk dat u toestemming krijgt voor de sleutelkluis.

1. Ga naar bronregio Sleutelkluis die in dit voorbeeld 'ContososourceKeyvault' > **Access-beleid** is 
2. Voeg onder **Principal selecteren** uw gebruikersnaamdradmin@contoso.comtoe: "
3. Selecteer ONDER **Sleutelmachtigingen** GET 
4. Selecteer **onder Geheime machtiging** GET 
5. Het toegangsbeleid opslaan

**Oorzaak 2:** U hebt geen toestemming nodig voor de **kluis Sleutel van doelgebied** om de toetsen te schrijven. </br>

*Bijvoorbeeld:* U probeert een VM met sleutelkluis *ContososourceKeyvault* op een brongebied te repliceren.
U hebt alle machtigingen op de sleutelkluis van het brongebied. Maar tijdens de bescherming selecteert u de reeds gemaakte key vault ContosotargetKeyvault, die geen machtigingen heeft. Er treedt een fout op.

Toestemming vereist voor [de kluis Van doelsleutel](#required-user-permissions)

**Hoe op te lossen:** Ga naar **het** > Beleid**van Home Keyvaults** > **ContosotargetKeyvault** > **Access** en voeg de juiste machtigingen toe.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](site-recovery-test-failover-to-azure.md) over het uitvoeren van een testfailover.
