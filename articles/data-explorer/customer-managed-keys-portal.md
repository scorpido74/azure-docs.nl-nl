---
title: Door de klant beheerde sleutels configureren met behulp van de Azure-portal
description: In dit artikel wordt beschreven hoe u versleuteling van door de klant beheerde sleutels op uw gegevens configureren in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 180196f2c368207b76811700fd845406098600df
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529443"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren met behulp van de Azure-portal

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sjabloon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Versleuteling inschakelen met door de klant beheerde sleutels in de Azure-portal

In dit artikel ziet u hoe u versleuteling met beheerde sleutels van klanten inschakelen met behulp van de Azure-portal. Standaard maakt Azure Data Explorer-versleuteling gebruik van door Microsoft beheerde sleutels. Configureer uw Azure Data Explorer-cluster om door de klant beheerde sleutels te gebruiken en geef de sleutel op die aan het cluster moet worden gekoppeld.

1. Ga in de [Azure-portal](https://portal.azure.com/)naar uw [Azure Data Explorer-clusterbron.](create-cluster-database-portal.md#create-a-cluster) 
1. Selecteer **Instellingenversleuteling** > **Encryption** in het linkerdeelvenster van de portal.
1. Selecteer **in** het deelvenster Versleuteling de optie **Aan** voor de instelling met door de **klant beheerde sleutel.**
1. Klik **op Sleutel selecteren**.

    ![Door klant beheerde sleutels configureren](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Selecteer in de **Key vault** **vervolgkeuzelijst De sleutelkluis selecteren in de optietoetssleutelvan Azure Key Vault.** Als u **Nieuw maken** selecteert om een nieuwe Sleutelkluis te [maken,](/azure/key-vault/quick-create-portal#create-a-vault)wordt u doorgestuurd naar het scherm **Sleutelkluis maken.**

1. Selecteer **Toets**.
1. Selecteer **Versie**.
1. Klik **op Selecteren**.

    ![Sleutelsleutel selecteren in Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. Selecteer **Opslaan**in het deelvenster **Versleuteling** dat nu uw sleutel bevat. Wanneer CMK-creatie slaagt, ziet u een succesbericht in **Meldingen**.

    ![Door de klant beheerde sleutel opslaan](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Door door de klantbeheersleutels in te schakelen voor uw Azure Data Explorer-cluster, maakt u een door het systeem toegewezen identiteit voor het cluster als deze niet bestaat. Daarnaast biedt u de vereiste machtigingen voor de beschikbaarheid, wrapKey en unwarpKey naar uw Azure Data Explorer-cluster op de geselecteerde Key Vault en krijgt u de eigenschappen Key Vault. 

> [!NOTE]
> Selecteer **Uit** om de door de klant beheerde sleutel te verwijderen nadat deze is gemaakt.

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in Azure](security.md)
* [Beveilig uw cluster in Azure Data Explorer - Azure-portal](manage-cluster-security.md) door versleuteling in rust in te schakelen.
* [Door de klant beheerde sleutels configureren met de sjabloon Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Door de klant beheerde sleutels configureren met C #](customer-managed-keys-csharp.md)



