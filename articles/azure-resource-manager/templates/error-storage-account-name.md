---
title: Fouten in de naam van het opslagaccount
description: Beschrijft de fouten die u tegenkomen bij het opgeven van de naam van een opslagaccount.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153969"
---
# <a name="resolve-errors-for-storage-account-names"></a>Fouten voor opslagaccountnamen oplossen

In dit artikel worden naamgevingsfouten beschreven die u tegenkomen bij het implementeren van een opslagaccount.

## <a name="symptom"></a>Symptoom

Als uw opslagaccountnaam verboden tekens bevat, ontvangt u een foutmelding zoals:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Voor opslagaccounts moet u een naam opgeven voor de bron die uniek is in Azure. Als u geen unieke naam opgeeft, krijgt u een foutmelding als:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Als u een opslagaccount implementeert met dezelfde naam als een bestaand opslagaccount in uw abonnement, maar een andere locatie opgeeft, ontvangt u een foutmelding dat het opslagaccount al op een andere locatie bestaat. Verwijder het bestaande opslagaccount of geef dezelfde locatie op als het bestaande opslagaccount.

## <a name="cause"></a>Oorzaak

De namen van het opslagaccount moeten tussen de 3 en 24 tekens lang zijn en alleen getallen en kleine letters gebruiken. De naam moet uniek zijn.

## <a name="solution"></a>Oplossing

Zorg ervoor dat de naam van het opslagaccount uniek is. U een unieke naam maken door uw naamgevingsconventie samen te stellen met het resultaat van de [uniekeString-functie.](template-functions-string.md#uniquestring)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Zorg ervoor dat de naam van uw opslagaccount niet meer dan 24 tekens bevat. De functie [uniqueString](template-functions-string.md#uniquestring) retourneert 13 tekens. Als u een voorvoegsel of postfix aan het **unieke resultaat String** concateert, geeft u een waarde op die 11 tekens of minder is.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Zorg ervoor dat de naam van uw opslagaccount geen hoofdletters of speciale tekens bevat.