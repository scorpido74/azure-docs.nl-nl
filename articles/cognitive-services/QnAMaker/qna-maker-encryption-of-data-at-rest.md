---
title: QnA Maker versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: QnA Maker versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372093"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker versleuteling van gegevens in rust

QnA Maker versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard, zodat u kunt voldoen aan de beveiligings-en nalevings doelen van uw organisatie.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Uw abonnement maakt standaard gebruik van door micro soft beheerde versleutelings sleutels. Er is ook een optie voor het beheren van uw abonnement met uw eigen sleutels. Door de klant beheerde sleutels (CMK) bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.

QnA Maker maakt gebruik van CMK-ondersteuning van Azure Search. U moet CMK maken [in azure Search met behulp van Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Dit Azure-exemplaar moet worden gekoppeld aan QnA Maker service om ervoor te zorgen dat het CMK is ingeschakeld.

> [!IMPORTANT]
> Uw Azure Search-service resource moet na januari 2019 zijn gemaakt en kan niet in de laag gratis (gedeeld) staan. Er is geen ondersteuning voor het configureren van door de klant beheerde sleutels in de Azure Portal.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

De QnA Maker-service gebruikt CMK uit de Azure Search-service. Volg deze stappen om CMKs in te scha kelen:

1. Maak een nieuw exemplaar van Azure Search en schakel de vereisten in die worden vermeld in de door de [klant beheerde sleutel vereisten voor Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Versleutelings instellingen weer geven](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Wanneer u een QnA Maker resource maakt, wordt deze automatisch gekoppeld aan een Azure Search-exemplaar. Deze kan niet worden gebruikt met CMK. Als u CMK wilt gebruiken, moet u het nieuwe exemplaar van Azure Search koppelen dat in stap 1 is gemaakt. U moet in het bijzonder de `AzureSearchAdminKey` en `AzureSearchName` in uw QnA Maker resource bijwerken.

   ![Versleutelings instellingen weer geven](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Maak vervolgens een nieuwe toepassings instelling:
   * **Naam**: Stel dit in op`CustomerManagedEncryptionKeyUrl`
   * **Waarde**: dit is de waarde die u in stap 1 hebt gekregen bij het maken van uw Azure Search-exemplaar.

   ![Versleutelings instellingen weer geven](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Wanneer u klaar bent, start u de runtime opnieuw. Uw QnA Maker-service is nu CMK ingeschakeld.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn beschikbaar in alle Azure Search regio's.

## <a name="next-steps"></a>Volgende stappen

* [Versleuteling in Azure Search met behulp van CMKs in Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Gegevens versleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
