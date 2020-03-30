---
title: Back-ups maken en versleutelde Azure VM's herstellen
description: Beschrijft hoe u versleutelde Azure VM's back-ups maken en herstellen met de Azure Backup-service.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206687"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Back-ups maken en versleutelde Azure VM herstellen

In dit artikel wordt beschreven hoe u een back-up maken en virtuele virtuele machines (VM's) van Windows of Linux Azure herstellen met versleutelde schijven met behulp van de [Azure Backup-service.](backup-overview.md)

Als u meer wilt weten over de manier waarop Azure Backup samenwerkt met Azure VM's voordat u begint, controleert u de volgende bronnen:

- [Bekijk](backup-architecture.md#architecture-built-in-azure-vm-backup) de Azure VM-back-uparchitectuur.
- [Meer informatie over](backup-azure-vms-introduction.md) Azure VM-back-up en de Azure Backup-extensie.

## <a name="encryption-support"></a>Ondersteuning voor versleuteling

Azure Backup ondersteunt back-ups van Azure VM's waarbij hun BE/data-schijven zijn versleuteld met Azure Disk Encryption (ADE). ADE gebruikt BitLocker voor versleuteling van Windows VM's en de dm-crypt-functie voor Linux VM's. ADE integreert met Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren. Key Vault Key Encryption Keys (KEKs) kan worden gebruikt om een extra beveiligingslaag toe te voegen, waardoor versleutelingsgeheimen worden versleuteld voordat ze naar Key Vault worden geschreven.

Azure Backup kan een back-up maken en Azure VM's herstellen met ADE met en zonder de Azure AD-app, zoals samengevat in de volgende tabel.

**VM-schijftype** | **ADE (BEK/dm-crypt)** | **ADE en KEK**
--- | --- | ---
**Niet-beheerd** | Ja | Ja
**Beheerd**  | Ja | Ja

- Meer informatie over [ADE,](../security/azure-security-disk-encryption-overview.md) [Key Vault](../key-vault/key-vault-overview.md)en [KEKs](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Lees de [veelgestelde vragen](../security/azure-security-disk-encryption-faq.md) voor Azure VM-schijfversleuteling.

### <a name="limitations"></a>Beperkingen

- U een back-up maken en versleutelde VM's herstellen binnen hetzelfde abonnement en dezelfde regio.
- Azure Backup ondersteunt VM's versleuteld met zelfstandige sleutels. Elke sleutel die deel uitmaakt van een certificaat dat wordt gebruikt om een vm te versleutelen, wordt momenteel niet ondersteund.
- U een back-up maken en versleutelde VM's herstellen binnen hetzelfde abonnement en dezelfde regio als de vault Recovery Services Backup.
- Versleutelde VM's kunnen niet worden hersteld op bestands-/mapniveau. U moet de hele virtuele machine herstellen om bestanden en mappen te herstellen.
- Wanneer u een vm herstelt, u de [bestaande VM-optie vervangen](backup-azure-arm-restore-vms.md#restore-options) niet gebruiken voor versleutelde VM's. Deze optie wordt alleen ondersteund voor niet-versleutelde beheerde schijven.

## <a name="before-you-start"></a>Voordat u begint

Ga als volgt te werk voordat u begint:

1. Zorg ervoor dat u een of meer [Windows-](../security/azure-security-disk-encryption-windows.md) of Linux-vm's hebt met ADE ingeschakeld. [Linux](../virtual-machines/linux/disk-encryption-overview.md)
2. [De ondersteuningsmatrix](backup-support-matrix-iaas.md) voor Azure VM-back-up controleren
3. [Maak](backup-azure-arm-vms-prepare.md#create-a-vault) een back-upkluis voor Herstelservices als u die niet hebt.
4. Als u versleuteling inschakelt voor VM's die al zijn ingeschakeld voor back-up, hoeft u alleen back-up te voorzien van machtigingen om toegang te krijgen tot de Key Vault, zodat back-ups zonder onderbreking kunnen worden voortgezet. [Meer informatie](#provide-permissions) over het toewijzen van deze machtigingen.

Daarnaast zijn er een paar dingen die je zou kunnen moeten doen in sommige omstandigheden:

- **Installeer de VM-agent op de VM:** Azure Backup maakt back-ups van Azure VM's door een extensie te installeren voor de Azure VM-agent die op de machine wordt uitgevoerd. Als uw VM is gemaakt op basis van een Azure-marketplace-afbeelding, wordt de agent geïnstalleerd en uitgevoerd. Als u een aangepaste vm maakt of als u een on-premises machine migreert, moet u de agent mogelijk [handmatig installeren.](backup-azure-arm-vms-prepare.md#install-the-vm-agent)

## <a name="configure-a-backup-policy"></a>Een back-upbeleid configureren

1. Als u nog geen back-upkluis van Recovery Services hebt gemaakt, volgt u [deze instructies](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Open de kluis in de portal en selecteer **Back-up** in de sectie **Aan de slag.**

    ![Back-upblad](./media/backup-azure-vms-encryption/select-backup.png)

3. In **Back-updoel** > **Waar wordt uw werkbelasting uitgevoerd?** selecteer **Azure**.
4. In **Wat wilt u een back-up maken?** Selecteer Virtuele **machine** > **OK**.

      ![Scenarioblad](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Selecteer **in Back-upbeleid** > **Selecteer back-upbeleid**het beleid dat u aan de kluis wilt koppelen. Klik vervolgens op **OK**.
    - Een back-upbeleid geeft aan wanneer back-ups worden gemaakt en hoe lang deze worden opgeslagen.
    - De details van het standaardbeleid worden onder de vervolgkeuzelijst weergegeven.

    ![Blade Scenario openen](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Als u het standaardbeleid niet wilt gebruiken, selecteert u **Nieuw maken**en maakt u een [aangepast beleid](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Kies de versleutelde VM's waaru een back-up wilt maken met het selectiebeleid en selecteer **OK**.

      ![Versleutelde VM's selecteren](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Als u Azure Key Vault gebruikt, ziet u op de kluispagina een bericht dat Azure Backup alleen-lezen toegang nodig heeft tot de sleutels en geheimen in de Key Vault.

    - Als u dit bericht ontvangt, is er geen actie vereist.

        ![Toegang OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Als u dit bericht ontvangt, moet u machtigingen instellen zoals beschreven in de [onderstaande procedure.](#provide-permissions)

        ![Waarschuwing voor toegang](./media/backup-azure-vms-encryption/access-warning.png)

9. Klik **op Back-up inschakelen** om het back-upbeleid in de kluis te implementeren en back-ups voor de geselecteerde VM's in te schakelen.

## <a name="trigger-a-backup-job"></a>Een back-uptaak activeren

De eerste back-up wordt uitgevoerd volgens het schema, maar u deze onmiddellijk als volgt uitvoeren:

1. Klik in het kluismenu op **Back-upitems**.
2. Klik **in Back-upitems**op **Azure Virtual Machine**.
3. Klik in de lijst **Back-upitems** op de ellips (...).
4. Klik **nu op Back-up**.
5. Gebruik **in Nu back-up**het agendabesturingselement om de laatste dag te selecteren waarop het herstelpunt moet worden behouden. Klik vervolgens op **OK**.
6. Controleer de portalmeldingen. U de voortgang van de taak in het kluisdashboard controleren > **Back-uptaken** > **in uitvoering**. Afhankelijk van de grootte van de virtuele machine kan het maken van de eerste back-up even duren.

## <a name="provide-permissions"></a>Machtigingen geven

Azure VM heeft alleen-lezen toegang nodig om een back-up te maken van de sleutels en geheimen, samen met de bijbehorende VM's.

- Uw Key Vault is gekoppeld aan de Azure AD-tenant van het Azure-abonnement. Als u **lid bent,** krijgt Azure Backup zonder verdere actie toegang tot de Key Vault.
- Als u **gastgebruiker**bent, moet u machtigingen opgeven voor Azure Backup om toegang te krijgen tot de sleutelkluis.

Machtigingen instellen:

1. Selecteer in de Azure-portal **Alle services**en zoek naar **sleutelkluizen**.
2. Selecteer de sleutelkluis die is gekoppeld aan de versleutelde vm die u een back-up maakt.
3. Selecteer **Toegangsbeleid** > **Nieuw toevoegen**.
4. Selecteer **Hoofdselecteren**en typ **Back-upbeheer**.
5. Selecteer **Back-upbeheerservice** > **selecteren**.

    ![Back-upserviceselectie](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Selecteer **Azure Backup**in **Toegangsbeleid** > **toevoegen Configureren vanuit sjabloon (optioneel**).
    - De vereiste machtigingen zijn vooraf ingevuld **voor Belangrijke machtigingen** en Geheime **machtigingen.**
    - Als uw vm alleen met **BEK**is versleuteld, verwijdert u de selectie voor **Sleutelmachtigingen,** omdat u alleen machtigingen voor geheimen nodig hebt.

    ![Azure-back-upselectie](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Klik op **OK**. **Backup Management Service** wordt toegevoegd aan **het toegangsbeleid**.

    ![Toegangsbeleidsregels](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Klik **op Opslaan** om Azure Backup met de machtigingen te voorzien.

## <a name="restore-an-encrypted-vm"></a>Een versleutelde vm herstellen

U herstelt versleutelde VM's als volgt:

1. [De VM-schijf herstellen](backup-azure-arm-restore-vms.md#restore-disks).
2. Maak de virtuele machine-instantie opnieuw door een van de volgende handelingen uit te voeren:
    1. Gebruik de sjabloon die wordt gegenereerd tijdens de herstelbewerking om vm-instellingen aan te passen en vm-implementatie te activeren. [Meer informatie](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Maak een nieuwe vm van de herstelde schijven met PowerShell. [Meer informatie](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Voor Linux VM's installeert u de ADE-extensie opnieuw, zodat de gegevensschijven open en gemonteerd zijn.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, bekijkt u de volgende artikelen:

- [Veelvoorkomende fouten](backup-azure-vms-troubleshoot.md) bij het maken van back-ups en het herstellen van versleutelde Azure VM's.
- [Problemen met azure VM-agent/back-upextensie.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
