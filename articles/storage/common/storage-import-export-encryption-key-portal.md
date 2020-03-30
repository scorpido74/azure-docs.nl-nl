---
title: De Azure-portal gebruiken om door de klant beheerde sleutels voor de service Importeren/exporteren te configureren
description: Meer informatie over het gebruik van de Azure-portal om door klanten beheerde sleutels te configureren met Azure Key Vault voor Azure Import/Export-service. Met door de klant beheerde toetsen u toegangsbesturingselementen maken, roteren, uitschakelen en intrekken.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ca1327a547e8550e47ff37e4ba100fcbd2b7a79f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282457"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Door de klant beheerde sleutels gebruiken in Azure Key Vault voor import/exportservice

Azure Import/Export beschermt de BitLocker-sleutels die worden gebruikt om de schijven te vergrendelen via een versleutelingssleutel. BitLocker-sleutels worden standaard versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u ook door de klant beheerde sleutels verstrekken.

Door de klant beheerde sleutels moeten worden gemaakt en opgeslagen in een Azure Key Vault. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../../key-vault/key-vault-overview.md)

In dit artikel ziet u hoe u door de klant beheerde sleutels gebruikt met de service Importeren/exporteren in de [Azure-portal.](https://portal.azure.com/)

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt een import- of exporttaak gemaakt volgens de instructies in:

    - [Een importtaak maken voor blobs](storage-import-export-data-to-blobs.md).
    - [Een importtaak voor bestanden maken](storage-import-export-data-to-files.md).
    - [Een exporttaak voor blobs maken](storage-import-export-data-from-blobs.md)

2. U hebt een bestaande Azure Key Vault met een sleutel erin die u gebruiken om uw BitLocker-sleutel te beschermen. Zie [Snelstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van de Azure-portal](../../key-vault/quick-create-portal.md)voor meer informatie over het maken van een sleutelkluis met de Azure-portal.

    - **Soft delete** en **Do not purge** zijn ingesteld op je bestaande Key Vault. Deze eigenschappen zijn standaard niet ingeschakeld. Zie de secties getiteld **Soft-delete** en **Inschakelen van zuiveringsbeveiliging** inschakelen in een van de volgende artikelen als u deze eigenschappen wilt inschakelen:

        - [Soft-delete gebruiken met PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
        - [Soft-delete gebruiken met CLI](../../key-vault/key-vault-soft-delete-cli.md).
    - De bestaande sleutelkluis moet een RSA-toets van 2048 grootte of meer hebben. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.
    - De sleutelkluis moet zich in dezelfde regio bevinden als het opslagaccount voor uw gegevens.  
    - Als u geen bestaande Azure Key Vault hebt, u deze ook inline maken zoals beschreven in de volgende sectie.

## <a name="enable-keys"></a>Toetsen inschakelen

Het configureren van de door de klant beheerde sleutel voor uw import/exportservice is optioneel. Standaard gebruikt de service Importeren/exporteren een door Microsoft beheerde sleutel om uw BitLocker-sleutel te beveiligen. Voer de volgende stappen uit om door klanten beheerde sleutels in de Azure-portal in te schakelen:

1. Ga naar het **overzichtsblad** voor uw taak Importeren.
2. Selecteer in het rechterdeelvenster **Kiezen hoe uw BitLocker-sleutels zijn versleuteld.**

    ![Versleutelingsoptie kiezen](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. In het **versleutelingsblad** u de BitLocker-sleutel van het apparaat bekijken en kopiëren. Onder **Versleutelingstype**u kiezen hoe u uw BitLocker-sleutel wilt beveiligen. Standaard wordt een door Microsoft beheerde sleutel gebruikt.

    ![BitLocker-sleutel weergeven](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. U hebt de optie om een door de klant beheerde sleutel op te geven. Nadat u de door de klant beheerde sleutel hebt geselecteerd, **selecteert u de sleutelkluis en een sleutel**.

    ![Klantbeheersleutel selecteren](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. In de **select-toets van azure key vault-blade** wordt het abonnement automatisch ingevuld. Voor **Key vault**u een bestaande sleutelkluis selecteren in de vervolgkeuzelijst.

    ![Azure Key Vault selecteren of maken](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. U ook **Nieuw maken** selecteren om een nieuwe sleutelkluis te maken. Voer in **het sleutelgewelfblad Sleutelgewelf maken**de brongroep en de naam van de sleutelkluis in. Accepteer alle andere standaardinstellingen. Selecteer **Controleren + maken**.

    ![Nieuwe Azure Key Vault maken](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Bekijk de informatie die is gekoppeld aan uw sleutelkluis en selecteer **Maken**. Wacht een paar minuten tot de sleutelkluis is voltooid.

    ![Azure Key Vault maken](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. In de **select-toets van Azure Key Vault**u een sleutel selecteren in de bestaande sleutelkluis.

9. Als u een nieuwe sleutelkluis hebt gemaakt, selecteert u **Nieuw maken** om een sleutel te maken. RSA sleutelgrootte kan 2048 of hoger zijn.

    ![Nieuwe sleutel maken in Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Als de bescherming voor zachte verwijdering en zuivering niet is ingeschakeld wanneer u de sleutelkluis maakt, wordt de sleutelkluis bijgewerkt om zachte verwijderings- en verwijderingsbeveiliging ingeschakeld te hebben.

10. Geef de naam van uw sleutel op, accepteer de andere standaardinstellingen en selecteer **Maken**.

    ![Nieuwe sleutel maken](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selecteer de **versie** en kies **Selecteer .** U krijgt een melding dat er een sleutel is gemaakt in uw sleutelkluis.

    ![Nieuwe sleutel gemaakt in sleutelkluis](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

In het **versleutelingsblad** ziet u de sleutelkluis en de sleutel die is geselecteerd voor de door de klant beheerde sleutel.

## <a name="disable-keys"></a>Toetsen uitschakelen

U alleen beheerde sleutels van Microsoft uitschakelen en naar door de klant beheerde sleutels verplaatsen in elk stadium van de import/exporttaak. U de door de klant beheerde sleutel echter niet uitschakelen nadat u deze hebt gemaakt.

## <a name="troubleshoot-customer-managed-key-errors"></a>Problemen met door klanten beheerde sleutelfouten oplossen

Als u fouten ontvangt met betrekking tot de door uw klant beheerde sleutel, gebruikt u de volgende tabel om problemen op te lossen:

| Foutcode     |Details     | Herstelbare?    |
|----------------|------------|-----------------|
| CmkErrorAccessingetrokken | Toegepast een door de klant beheerde sleutel, maar de belangrijkste toegang is momenteel ingetrokken. Zie voor meer informatie hoe u [de sleuteltoegang inschakelt](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Ja, controleer of: <ol><li>Key vault heeft nog steeds de MSI in het toegangsbeleid.</li><li>Toegangsbeleid biedt machtigingen voor Get, Wrap, Unwrap.</li><li>Als de sleutelkluis zich in een vNet achter de firewall bevindt, controleert u of **Microsoft Trusted Services toestaan** is ingeschakeld.</li></ol>                                                                                            |
| CmkErrorDisabled      | Toegepast een klant beheerde sleutel, maar de sleutel is uitgeschakeld. Zie voor meer informatie hoe u [de sleutel inschakelt.](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)                                                                             | Ja, door de sleutelversie in te schakelen     |
| CmkErrorNotFound      | Applied een door de klant beheerde sleutel toe, maar kan de sleutel niet vinden. <br>Als de sleutel na de bewaarperiode wordt verwijderd en verwijderd, u de sleutel niet herstellen. Als u een back-up van de sleutel hebt, u de sleutel herstellen om dit probleem op te lossen. | Nee, de sleutel is verwijderd en ook verwijderd na de bewaartermijn. <br>Ja, alleen als de klant de sleutel heeft, wordt deze back-upondersteund en hersteld.  |
| CmkErrorVaultNotFound | Applied een door de klant beheerde sleutel toe, maar kan de sleutelkluis die aan de sleutel is gekoppeld niet vinden.<br>Als u de sleutelkluis hebt verwijderd, u de door de klant beheerde sleutel niet herstellen.  Zie [Een sleutelkluistenant-id wijzigen na een abonnementswijziging](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)als u de sleutelkluishebt gemigreerd naar een andere tenant. |   Nee, als de klant de sleutelkluis heeft verwijderd.<br> Ja, als sleutelkluis een tenantmigratie onderging, doe dan een van: <ol><li>verplaats de sleutelkluis terug naar de oude huurder.</li><li>stel Identiteit = Geen en vervolgens terug naar Identiteit = SystemAssigned, dit verwijdert en reconstrueert de identiteit</li></ol><br>Opmerking: De migratiecase voor tenant's is gebaseerd op beperkt begrip, moet het werkelijke gedrag testen en bevestigen en kan later worden herzien. |

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
