---
title: Gebruik de Azure Portal voor het configureren van door de klant beheerde sleutels
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van de Azure Portal voor het configureren van door de klant beheerde sleutels met Azure Key Vault voor Azure Storage versleuteling.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799163"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van de Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel wordt beschreven hoe u een Azure Key Vault met door de klant beheerde sleutels configureert met behulp van de [Azure Portal](https://portal.azure.com/). Voor informatie over het maken van een sleutel kluis met behulp van de Azure Portal, raadpleegt u [Quick Start: een geheim instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Het gebruik van door de klant beheerde sleutels met Azure Storage versleuteling vereist dat er twee eigenschappen worden ingesteld op de sleutel kluis, de functie **voorlopig verwijderen** en **niet leeg**te maken. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of Azure CLI in een nieuwe of bestaande sleutel kluis.

Voor meer informatie over het inschakelen van deze eigenschappen voor een bestaande sleutelkluis raadpleegt u de secties **Voorlopig verwijderen inschakelen** en **Beveiliging tegen leegmaken inschakelen** in een van de volgende artikelen:

- [Voorlopig verwijderen gebruiken met Power shell](../../key-vault/general/soft-delete-powershell.md).
- [Voorlopig verwijderen gebruiken met CLI](../../key-vault/general/soft-delete-cli.md).

Azure Storage Encryption ondersteunt RSA-en RSA-HSM-sleutels met een grootte van 2048, 3072 en 4096. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Ga naar uw opslagaccount.
1. Klik op de Blade **instellingen** voor het opslag account op **versleuteling**. Selecteer de optie door de **klant beheerde sleutels** , zoals wordt weer gegeven in de volgende afbeelding.

    ![Scherm opname van de portal met versleutelings optie](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, hebt u de mogelijkheid om een sleutel op te geven die u wilt koppelen aan het opslag account. U kunt ook aangeven of Azure Storage de door de klant beheerde sleutel automatisch moet bijwerken naar de nieuwste versie, of dat u de sleutel versie hand matig wilt bijwerken.

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutel kluis opgeven

Wanneer u een door de klant beheerde sleutel van een sleutel kluis selecteert, wordt automatisch bijwerken van de sleutel versie ingeschakeld. Als u de sleutel versie hand matig wilt beheren, geeft u in plaats daarvan de sleutel-URI op en neemt u de sleutel versie op. Zie [een sleutel opgeven als een URI](#specify-a-key-as-a-uri)voor meer informatie.

Voer de volgende stappen uit om een sleutel van een sleutel kluis op te geven:

1. Kies de optie **selecteren uit Key Vault** .
1. Selecteer **een sleutel kluis en sleutel selecteren**.
1. Selecteer de sleutel kluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutel kluis.

   ![Scherm afbeelding die laat zien hoe u de sleutel kluis en de sleutel selecteert](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Sla uw wijzigingen op.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als een URI

Azure Storage kunt de door de klant beheerde sleutel die wordt gebruikt voor versleuteling automatisch bijwerken om de nieuwste sleutel versie te gebruiken. Wanneer de door de klant beheerde sleutel is gedraaid in Azure Key Vault, zal Azure Storage automatisch de meest recente versie van de sleutel gebruiken voor versleuteling.

> [!NOTE]
> Als u een sleutel wilt draaien, maakt u een nieuwe versie van de sleutel in Azure Key Vault. Azure Storage de draai hoek van de sleutel in Azure Key Vault niet verwerkt. u moet de sleutel dus hand matig draaien of een functie maken om deze op een schema te draaien.

Wanneer u de sleutel-URI opgeeft, laat u de sleutel versie van de URI weg om automatisch bijwerken naar de nieuwste sleutel versie in te scha kelen. Als u de sleutel versie opneemt in de sleutel-URI, wordt automatisch bijwerken niet ingeschakeld en moet u de sleutel versie zelf beheren. Zie [de sleutel versie hand matig bijwerken](#manually-update-the-key-version)voor meer informatie over het bijwerken van de sleutel versie.

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutel-URI in de Azure Portal wilt zoeken, navigeert u naar uw sleutel kluis en selecteert u de instelling **sleutels** . Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de versies ervan weer te geven. Selecteer een sleutel versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **sleutel-id** , dat de URI levert.

    ![Scherm opname van sleutel kluis sleutel-URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Kies in de instellingen voor de versleutelings **sleutel** voor uw opslag account de optie **sleutel-URI opgeven** .
1. Plak de URI die u hebt gekopieerd in het veld **sleutel-URI** . Laat de sleutel versie van de URI weg om automatische updates van de sleutel versie in te scha kelen.

   ![Scherm afbeelding die laat zien hoe de sleutel-URI moet worden ingevoerd](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Geef het abonnement op dat de sleutel kluis bevat.
1. Sla uw wijzigingen op.

Nadat u de sleutel hebt opgegeven, geeft de Azure Portal aan of automatisch bijwerken van de sleutel versie is ingeschakeld en geeft de sleutel versie weer die momenteel wordt gebruikt voor versleuteling.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Scherm opname van het automatisch bijwerken van de sleutel versie ingeschakeld":::

## <a name="manually-update-the-key-version"></a>De sleutel versie hand matig bijwerken

Wanneer u een nieuwe versie van een door de klant beheerde sleutel in Key Vault maakt, gebruikt Azure Storage automatisch de nieuwe versie voor versleuteling met door de klant beheerde sleutels, zoals beschreven in de vorige secties. Als u ervoor kiest om de sleutel versie zelf te beheren, moet u telkens wanneer u een nieuwe versie van de sleutel maakt, de sleutel versie bijwerken die aan het opslag account is gekoppeld.

Voer de volgende stappen uit om het opslag account bij te werken voor het gebruik van de nieuwe sleutel versie:

1. Navigeer naar uw opslag account en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe versie van de sleutel. U kunt ook de sleutel kluis en de sleutel opnieuw selecteren om de versie bij te werken.
1. Sla uw wijzigingen op.

## <a name="switch-to-a-different-key"></a>Overschakelen naar een andere sleutel

Voer de volgende stappen uit om de sleutel te wijzigen die wordt gebruikt voor Azure Storage versleuteling:

1. Navigeer naar uw opslag account en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel. U kunt ook de sleutel kluis selecteren en een nieuwe sleutel kiezen.
1. Sla uw wijzigingen op.

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw opslag account opnieuw versleuteld met door micro soft beheerde sleutels. Voer de volgende stappen uit om door de klant beheerde sleutels uit te scha kelen:

1. Navigeer naar uw opslag account en geef de **versleutelings** instellingen weer.
1. Schakel het selectie vakje naast de instelling **uw eigen sleutel gebruiken** uit.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
