---
title: ServicePrincipalSelector UI-element
description: Hierin wordt het micro soft. common. ServicePrincipalSelector UI-element voor Azure Portal beschreven. Biedt een vervolg keuzelijst voor het kiezen van een toepassings-id en een tekstvak voor het invoeren van een wacht woord of vinger afdruk van het certificaat.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575993"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Micro soft. common. ServicePrincipalSelector UI-element

Een besturings element waarmee gebruikers een bestaande service-principal kunnen selecteren of een nieuwe kunnen registreren. Wanneer u **Nieuw maken**selecteert, gaat u door de stappen om een nieuwe toepassing te registreren. Wanneer u een bestaande toepassing selecteert, biedt het besturings element een tekstvak voor het invoeren van een wacht woord of vinger afdruk van het certificaat.

## <a name="ui-sample"></a>UI-voor beeld

De standaard weergave wordt bepaald door de waarden in de `defaultValue` eigenschap. Als de `principalId` eigenschap een geldig Globally Unique Identifier (GUID) bevat, zoekt het besturings element naar de object-id van de toepassing. De standaard waarde is van toepassing als de gebruiker geen selectie maakt in de vervolg keuzelijst.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Eerste weer gave van micro soft. common. ServicePrincipalSelector":::

Wanneer u **nieuwe** of een bestaande toepassings-id in de vervolg keuzelijst selecteert, wordt het **verificatie type** weer gegeven om een wacht woord of vinger afdruk van het certificaat in het tekstvak in te voeren.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Eerste weer gave van micro soft. common. ServicePrincipalSelector":::

## <a name="schema"></a>Schema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Opmerkingen

- De vereiste eigenschappen zijn:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Hiermee geeft u de standaard waarde `principalId` en op `name` .

- De optionele eigenschappen zijn:
  - `toolTip`: Hiermee wordt een knop Info `infoBalloon` aan elk label gekoppeld.
  - `visible`: Het besturings element verbergen of weer geven.
  - `options`: Hiermee geeft u op of de optie voor de vinger afdruk van het certificaat beschikbaar moet worden gemaakt.
  - `constraints`: Regex-beperkingen voor wachtwoord validatie.

## <a name="example"></a>Voorbeeld

Hier volgt een voor beeld van het `Microsoft.Common.ServicePrincipalSelector` besturings element. De `defaultValue` eigenschap wordt `principalId` ingesteld `<default guid>` als een tijdelijke aanduiding voor een standaard toepassings-id-GUID.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Voorbeeld uitvoer

De `appId` is de id van de registratie van de toepassing die u hebt geselecteerd of gemaakt. De `objectId` is een matrix met objectid's voor de service-principals die zijn geconfigureerd voor de registratie van de geselecteerde toepassing.

Als er geen selectie wordt gemaakt vanuit de vervolg keuzelijst, is de waarde van de `newOrExisting` eigenschap **Nieuw**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Wanneer een **nieuwe toepassing maken** of een bestaande toepassings-id is geselecteerd in de vervolg keuzelijst, wordt de waarde van de `newOrExisting` eigenschap **bestaande**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
- Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
