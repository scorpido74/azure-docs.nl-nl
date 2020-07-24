---
title: Gebruik de Azure Portal voor het configureren van door de klant beheerde sleutels voor Azure Data Box
description: Meer informatie over het gebruik van de Azure Portal voor het configureren van door de klant beheerde sleutels met Azure Key Vault voor Azure Data Box. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 60e621b34250b036888b233b084ba1ddff939048
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087795"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Door de klant beheerde sleutels gebruiken in Azure Key Vault voor Azure Data Box

Azure Data Box beveiligt de ontgrendelings sleutel van het apparaat (ook wel apparaatwachtwoord genoemd) dat wordt gebruikt om het apparaat te vergren delen via een versleutelings sleutel. De ontgrendelings sleutel voor een apparaat voor een Data Box order wordt standaard versleuteld met een door micro soft beheerde sleutel. Voor extra controle over de ontgrendelings sleutel van het apparaat kunt u ook een door de klant beheerde sleutel opgeven. 

Door de klant beheerde sleutels moeten worden gemaakt en opgeslagen in een Azure Key Vault. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u door de klant beheerde sleutels kunt gebruiken met Azure Data Box in de [Azure Portal](https://portal.azure.com/). Dit artikel is van toepassing op zowel Azure Data Box apparaten als Azure Data Box Heavy apparaten.

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt een Azure Data Box volgorde gemaakt volgens de instructies in de [zelf studie: order Azure data Box](data-box-deploy-ordered.md).

2. U hebt een bestaande Azure Key Vault met een sleutel die u kunt gebruiken om de ontgrendelings sleutel van uw apparaat te beveiligen. Voor informatie over het maken van een sleutel kluis met behulp van de Azure Portal, raadpleegt u [Quick Start: een geheim instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../key-vault/secrets/quick-create-portal.md).

    - **Voorlopig verwijderen** en **niet wissen** worden ingesteld op uw bestaande sleutel kluis. Deze eigenschappen zijn niet standaard ingeschakeld. Als u deze eigenschappen wilt inschakelen, raadpleegt u de secties het **inschakelen van zacht verwijderen** en het inschakelen van de **beveiliging opschonen** in een van de volgende artikelen:

        - [Voorlopig verwijderen gebruiken met Power shell](../key-vault/general/soft-delete-powershell.md).
        - [Voorlopig verwijderen gebruiken met CLI](../key-vault/general/soft-delete-cli.md).
    - De bestaande sleutel kluis moet een RSA-sleutel hebben van 2048-grootte of meer. Zie [informatie over Azure Key Vault sleutels](../key-vault/keys/about-keys.md)voor meer informatie over sleutels.
    - De sleutel kluis moet zich in dezelfde regio bevinden als de opslag accounts die worden gebruikt voor uw gegevens. Meerdere opslag accounts kunnen worden gekoppeld aan uw Azure Data Box-resource.
    - Als u geen bestaande sleutel kluis hebt, kunt u deze inline ook maken zoals beschreven in de volgende sectie.

## <a name="enable-keys"></a>Sleutels inschakelen

Het configureren van door de klant beheerde sleutel voor uw Azure Data Box is optioneel. Data Box maakt standaard gebruik van een door micro soft beheerde sleutel voor het beveiligen van uw BitLocker-sleutel. Voer de volgende stappen uit om een door de klant beheerde sleutel in te scha kelen in de Azure Portal:

1. Ga naar de Blade **overzicht** voor uw data Box order.

    ![Blade overzicht van Data Box order](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Ga naar **instellingen > versleuteling**. Onder **versleutelings type**kunt u kiezen hoe u de ontgrendelings sleutel van het apparaat wilt beveiligen. Een door micro soft beheerde sleutel wordt standaard gebruikt om het wacht woord voor het ontgrendelen van uw apparaat te beveiligen. 

    ![Versleutelings optie kiezen](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Selecteer versleutelings type als door de **klant beheerde sleutel**. Nadat u de door de klant beheerde sleutel hebt geselecteerd, **selecteert u een sleutel kluis en sleutel**.

    ![Door de klant beheerde sleutel selecteren](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. In de Blade **sleutel selecteren op basis van Azure Key Vault** wordt het abonnement automatisch ingevuld. Voor **sleutel kluis**kunt u een bestaande sleutel kluis selecteren in de vervolg keuzelijst.

    ![Nieuwe Azure Key Vault maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    U kunt ook **nieuwe maken** selecteren om een nieuwe sleutel kluis te maken. Voer op de **Blade sleutel kluis maken**de resource groep en de naam van de sleutel kluis in. Zorg ervoor dat de functie voor het **voorlopig verwijderen** en leegmaken van de **beveiliging** is ingeschakeld. Accepteer alle overige standaard waarden. Selecteer **Controleren + maken**.

    ![Nieuwe Azure Key Vault maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Controleer de informatie die is gekoppeld aan uw sleutel kluis en selecteer **maken**. Wacht enkele minuten totdat de sleutel kluis is gemaakt.

    ![Azure Key Vault maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. U kunt in de **sleutel Select from Azure Key Vault**een sleutel selecteren in de bestaande sleutel kluis.

    ![Nieuwe sleutel maken in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Als u een nieuwe sleutel wilt maken, selecteert u **nieuwe maken** om een sleutel te maken. RSA-sleutel grootte kan 2048 of hoger zijn.

    ![Nieuwe sleutel maken in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Geef de naam voor de sleutel op, accepteer de andere standaard waarden en selecteer **maken**. 

    ![Nieuwe sleutel maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. U wordt gewaarschuwd dat er een sleutel in uw sleutel kluis is gemaakt. Selecteer de **versie** en kies vervolgens **selecteren**.

    ![Nieuwe sleutel gemaakt in sleutel kluis](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. In het deel venster **versleutelings type** ziet u de sleutel kluis en de sleutel die is geselecteerd voor de door de klant beheerde sleutel.

    ![Sleutel en sleutel kluis voor door de klant beheerde sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Sla de sleutel op. 

    ![Door de klant beheerde sleutel opslaan](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    De sleutel-URL wordt weer gegeven onder **versleutelings type**.

    ![Door de klant beheerde sleutel-URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> U kunt micro soft Managed Key uitschakelen en verplaatsen naar door de klant beheerde sleutel op elke fase van de Data Box order. Als u echter de door de klant beheerde sleutel hebt gemaakt, kunt u niet teruggaan naar de door micro soft beheerde sleutel.

## <a name="troubleshoot-errors"></a>Problemen oplossen

Als u fouten met betrekking tot uw door de klant beheerde sleutel ontvangt, gebruikt u de volgende tabel om problemen op te lossen.

| Fout code| Foutdetails| Herstel bare?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel is uitgeschakeld.| Ja, door de sleutel versie in te scha kelen.|
| SsemUserErrorEncryptionKeyExpired| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel is verlopen.| Ja, door de sleutel versie in te scha kelen.|
| SsemUserErrorKeyDetailsNotFound| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel niet is gevonden.| Als u de sleutel kluis hebt verwijderd, kunt u de door de klant beheerde sleutel niet herstellen.  Als u de sleutel kluis naar een andere Tenant hebt gemigreerd, raadpleegt u [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../key-vault/general/move-subscription.md). Als u de sleutel kluis hebt verwijderd:<ol><li>Ja, als het zich in de duur van de schone beveiliging bevindt, gebruikt u de stappen in [een sleutel kluis herstellen](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nee, als het na de duur van de schone beveiliging valt.</li></ol><br>Als de sleutel kluis een Tenant migratie heeft ontvangen, ja, kan deze worden hersteld met behulp van een van de volgende stappen: <ol><li>Herstel de sleutel kluis terug naar de oude Tenant.</li><li>Stel `Identity = None` in en stel vervolgens de waarde weer in op `Identity = SystemAssigned` . Hiermee wordt de identiteit verwijderd en opnieuw gemaakt zodra de nieuwe identiteit is gemaakt. Inschakelen `Get` , `Wrap` , en `Unwrap` machtigingen voor de nieuwe identiteit in het toegangs beleid van de sleutel kluis.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel toegang is ingetrokken.| Ja, controleren of: <ol><li>Sleutel kluis heeft nog steeds de MSI in het toegangs beleid.</li><li>Toegangs beleid voorziet in machtigingen voor ophalen, teruglopen, in-en uitpakken.</li><li>Als de sleutel kluis zich in een vNet achter de firewall bevindt, controleert u of **micro soft Trusted Services toestaan** is ingeschakeld.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Kan de sleutel niet ophalen omdat de bijbehorende sleutel kluis voor de door de klant beheerde sleutel niet is gevonden. | Als u de sleutel kluis hebt verwijderd, kunt u de door de klant beheerde sleutel niet herstellen.  Als u de sleutel kluis naar een andere Tenant hebt gemigreerd, raadpleegt u [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../key-vault/general/move-subscription.md). Als u de sleutel kluis hebt verwijderd:<ol><li>Ja, als het zich in de duur van de schone beveiliging bevindt, gebruikt u de stappen in [een sleutel kluis herstellen](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nee, als het na de duur van de schone beveiliging valt.</li></ol><br>Als de sleutel kluis een Tenant migratie heeft ontvangen, ja, kan deze worden hersteld met behulp van een van de volgende stappen: <ol><li>Herstel de sleutel kluis terug naar de oude Tenant.</li><li>Stel `Identity = None` in en stel vervolgens de waarde weer in op `Identity = SystemAssigned` . Hiermee wordt de identiteit verwijderd en opnieuw gemaakt zodra de nieuwe identiteit is gemaakt. Inschakelen `Get` , `Wrap` , en `Unwrap` machtigingen voor de nieuwe identiteit in het toegangs beleid van de sleutel kluis.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel niet is gevonden.| Ja, controleren of: <ol><li>Sleutel kluis heeft nog steeds de MSI in het toegangs beleid.</li><li>Identiteit is van het type systeem toegewezen.</li><li>Stel Get-, wrap-en Unwrap-machtigingen in voor de identiteit in het toegangs beleid van de sleutel kluis.</li></ol>|
| Algemene fout  | Kan de sleutel niet ophalen.| Dit is een algemene fout. Neem contact op met Microsoft Ondersteuning om de fout op te lossen en de volgende stappen te bepalen.|


## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault](../key-vault/general/overview.md)?