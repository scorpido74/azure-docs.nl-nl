---
title: Element IdentitySelector UI
description: Beschrijft het Microsoft.ManagedIdentity.IdentitySelector-gebruikersinterface-element voor Azure-portal. Beheerde identiteiten toewijzen aan een resource.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087542"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector UI-element

Een besturingselement voor het toewijzen van [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor een resource in een implementatie.

## <a name="ui-sample"></a>UI-voorbeeld

Het besturingselement bestaat uit de volgende elementen:

![Microsoft.ManagedIdentity.IdentitySelector eerste stap](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Wanneer de gebruiker **Toevoegen**selecteert, wordt het volgende formulier geopend. De gebruiker kan een of meer door de gebruiker toegewezen identiteiten voor de bron selecteren.

![Microsoft.ManagedIdentity.IdentitySelector tweede stap](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

De geselecteerde identiteiten worden weergegeven in de tabel. De gebruiker kan items uit deze tabel toevoegen of verwijderen.

![Microsoft.ManagedIdentity.IdentitySelector derde stap](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>Schema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Opmerkingen

- Gebruik **defaultValue.systemAssignedIdentity** om een initiële waarde in te stellen voor het besturingselement met systeemtoegewezen identiteitsopties. De standaardwaarde is **Uit**. De volgende waarden zijn toegestaan:
  - **Aan** : Een door het systeem toegewezen identiteit is toegewezen aan de resource.
  - **Uit** : een door het systeem toegewezen identiteit is niet toegewezen aan de resource.
  - **OnOnly** – Een door het systeem toegewezen identiteit is toegewezen aan de resource. Gebruikers kunnen deze waarde niet bewerken tijdens de implementatie.
  - **OffOnly** : een door het systeem toegewezen identiteit is niet toegewezen aan de resource. Gebruikers kunnen deze waarde niet bewerken tijdens de implementatie.

- Als **options.hideSystemAssignedIdentity** is ingesteld op **true,** wordt de gebruikersinterface om de toegewezen identiteit van het systeem te configureren, niet weergegeven. De standaardwaarde voor deze optie is **onwaar.**
- Als **options.hideUserAssignedIdentity** is ingesteld op **true,** wordt de gebruikersinterface om de door de gebruiker toegewezen identiteit te configureren, niet weergegeven. Aan de resource is geen door de gebruiker toegewezen identiteit toegewezen. De standaardwaarde voor deze optie is **onwaar.**

## <a name="next-steps"></a>Volgende stappen

- Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
- Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .