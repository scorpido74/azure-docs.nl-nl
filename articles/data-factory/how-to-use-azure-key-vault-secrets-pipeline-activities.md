---
title: Azure Key Vault-geheimen gebruiken in pijplijnactiviteiten
description: Meer informatie over het ophalen van opgeslagen referenties uit Azure-sleutelkluis en deze gebruiken tijdens het uitvoeren van de pijplijn in de gegevensfabriek.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200109"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Azure Key Vault-geheimen gebruiken in pijplijnactiviteiten

U referenties of geheime waarden opslaan in een Azure Key Vault en deze gebruiken tijdens de uitvoering van de pijplijn om door te geven aan uw activiteiten.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de door de gegevensfabriek beheerde identiteit.  Ontdek hoe het werkt [vanuit Beheerde identiteit voor Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) en zorg ervoor dat er een is gekoppeld aan uw gegevensfabriek.

## <a name="steps"></a>Stappen

1. Open de eigenschappen van uw gegevensfabriek en kopieer de waarde Van de beheerde identiteitstoepassing-id.

    ![Beheerde identiteitswaarde](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Open het toegangsbeleid voor sleutelkluizen en voeg de beheerde identiteitsmachtigingen toe aan Geheimen opvragen en aanbieden.

    ![Toegangsbeleid voor belangrijke vaults](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Toegangsbeleid voor belangrijke vaults](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klik **op Toevoegen**en klik vervolgens op **Opslaan.**

3. Navigeer naar uw Key Vault-geheim en kopieer de geheime id.

    ![Geheime id](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Noteer uw geheime URI die u wilt krijgen tijdens uw pijplijn in de gegevensfabriek.

4. Voeg in de pijplijn Gegevensfabriek een nieuwe webactiviteit toe en configureer deze als volgt.  

    |Eigenschap  |Waarde  |
    |---------|---------|
    |Veilige uitvoer     |True         |
    |URL     |[Uw geheime URI-waarde]?api-versie=7.0         |
    |Methode     |GET         |
    |Authentication     |MSI         |
    |Resource        |https://vault.azure.net       |

    ![Activiteit Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > U moet **?api-version=7.0** toevoegen aan het einde van uw geheime URI.  

    > [!CAUTION]
    > Stel de optie Veilige uitvoer in op true om te voorkomen dat de geheime waarde wordt vastgelegd in platte tekst.  Alle andere activiteiten die deze waarde verbruiken, moeten hun secure input-optie op true hebben ingesteld.

5. Als u de waarde in een andere activiteit wilt gebruiken, gebruikt u de volgende codeexpressie ** @activity('Web1').output.value**.

    ![Codeexpressie](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Store-referenties in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor meer informatie over het gebruik van Azure Key Vault om referenties op te slaan voor gegevensopslag en computes
