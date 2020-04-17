---
title: De Azure-portal gebruiken om door de klant beheerde sleutels te configureren
titleSuffix: Cognitive Services
description: Meer informatie over het gebruik van de Azure-portal om door klanten beheerde sleutels te configureren met Azure Key Vault. Met door de klant beheerde toetsen u toegangsbesturingselementen maken, roteren, uitschakelen en intrekken.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455254"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van de Azure-portal

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. De resource Cognitive Services en de sleutelkluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

In dit artikel ziet u hoe u een Azure Key Vault configureert met door de klant beheerde sleutels met behulp van de [Azure-portal.](https://portal.azure.com/) Zie [Snelstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van de Azure-portal](../../key-vault/secrets/quick-create-portal.md)voor meer informatie over het maken van een sleutelkluis met de Azure-portal.

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Met behulp van door de klant beheerde sleutels moeten twee eigenschappen worden ingesteld op de sleutelkluis, **Soft Delete** en Do **Not Purge.** Deze eigenschappen zijn standaard niet ingeschakeld, maar kunnen worden ingeschakeld met PowerShell of Azure CLI op een nieuwe of bestaande sleutelkluis.

> [!IMPORTANT]
> Als u de eigenschappen **Soft Delete** and Do **Not Purge** niet hebt ingeschakeld en u uw sleutel verwijdert, u de gegevens niet herstellen in uw cognitive service-bron.

Zie de secties getiteld **Soft-delete** en **Inschakelen van zuiveringsbeveiliging** inschakelen in een van de volgende artikelen voor meer informatie over het inschakelen van deze eigenschappen op een bestaand sleutelkluis:

- [Soft-delete gebruiken met PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Soft-delete gebruiken met CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Alleen RSA-sleutels van grootte 2048 worden ondersteund met Azure Storage-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Voer de volgende stappen uit om door klanten beheerde sleutels in de Azure-portal in te schakelen:

1. Navigeer naar uw resource Cognitive Services.
1. Klik op het **pagina-pagina Instellingen** voor uw resource voor Cognitive Services op **Versleuteling**. Selecteer de optie **Beheerde sleutels van** de klant, zoals in de volgende afbeelding wordt weergegeven.

    ![Schermafbeelding van het selecteren van door klanten beheerde sleutels](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, u een sleutel opgeven om te koppelen aan de resource Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als URI

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutelURI in de Azure-portal wilt vinden, navigeert u naar uw sleutelkluis en selecteert u de instelling **Sleutels.** Selecteer de gewenste sleutel en klik op de toets om de versies te bekijken. Selecteer een belangrijke versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **Sleutel-id,** dat de URI biedt.

    ![Schermafbeelding van de sleutelkluissleutel URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Kies in de **coderingsinstellingen** voor uw opslagaccount de optie Uri van de **toets invoeren.**
1. Plak de URI die u hebt gekopieerd in het veld **Sleutel URI.**

   ![Schermafbeelding van het invoeren van sleutelURI](../media/cognitive-services-encryption/ssecmk2.png)

1. Geef het abonnement op dat de sleutelkluis bevat.
1. Sla uw wijzigingen op.

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutelkluis opgeven

Als u een sleutel uit een sleutelkluis wilt opgeven, moet u eerst ervoor zorgen dat u een sleutelkluis hebt die een sleutel bevat. Voer de volgende stappen uit om een sleutel uit een sleutelkluis op te geven:

1. Kies de optie **Selecteren in Key Vault.**
1. Selecteer de sleutelkluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutelkluis.

   ![Schermafbeelding van de optie sleuteloptie met door de klant beheerde knop](../media/cognitive-services-encryption/ssecmk3.png)

1. Sla uw wijzigingen op.

## <a name="update-the-key-version"></a>De belangrijkste versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, werkt u de bron Cognitive Services bij om de nieuwe versie te gebruiken. Volg deze stappen:

1. Navigeer naar uw resource Cognitive Services en geef de **versleutelingsinstellingen** weer.
1. Voer de URI in voor de nieuwe sleutelversie. U afwisselend de sleutelkluis en de sleutel opnieuw selecteren om de versie bij te werken.
1. Sla uw wijzigingen op.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Voer de volgende stappen uit om de sleutel voor versleuteling te wijzigen:

1. Navigeer naar uw resource Cognitive Services en geef de **versleutelingsinstellingen** weer.
1. Voer de URI in voor de nieuwe sleutel. U afwisselend de sleutelkluis selecteren en een nieuwe sleutel kiezen.
1. Sla uw wijzigingen op.

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw cognitive services-bron vervolgens versleuteld met door Microsoft beheerde sleutels. Voer de volgende stappen uit om door klanten beheerde sleutels uit te schakelen:

1. Navigeer naar uw resource Cognitive Services en geef de **versleutelingsinstellingen** weer.
1. Schakel het selectievakje naast de instelling **Uw eigen sleutel gebruiken** uit.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulier voor het aanvragen van een door de klant beheerde sleutelformulier voor de klant](https://aka.ms/cogsvc-cmk)
* [Face Services-versleuteling van gegevens in rust](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker encryptie van gegevens in rust](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Taalbegrijpende serviceversleuteling van gegevens in rust](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator encryptie van gegevens in rust](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Vertaler encryptie van gegevens in rust](../translator/translator-encryption-of-data-at-rest.md)
* [Personalisator encryptie van gegevens in rust](../personalizer/personalizer-encryption-of-data-at-rest.md)
