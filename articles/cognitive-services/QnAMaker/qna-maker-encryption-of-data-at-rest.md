---
title: QnA Maker versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). Dit artikel bevat informatie over gegevens versleuteling in rust voor QnA Maker en hoe u CMK inschakelt en beheert.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: egeaney
ms.openlocfilehash: 1ca0dda046329e95c649540fd42f96ca43838c85
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086702"
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

## <a name="encryption-of-data-in-transit"></a>Versleuteling van gegevens die onderweg zijn

QnA Maker portal wordt uitgevoerd in de browser van de gebruiker. Elke actie triggert een directe aanroep naar de respectieve cognitieve Service-API. QnA Maker is daarom compatibel met gegevens die onderweg zijn.
Omdat de QnA Maker Portal service echter wordt gehost in West-US, is het nog steeds niet ideaal voor klanten die geen gebruik maken van de VS. 

## <a name="next-steps"></a>Volgende stappen

* [Versleuteling in Azure Search met behulp van CMKs in Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Gegevens versleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)