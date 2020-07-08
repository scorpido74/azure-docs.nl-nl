---
title: UI-definitie-elementen maken
description: Beschrijft de elementen die moeten worden gebruikt bij het maken van UI-definities voor Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77086699"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition-elementen

In dit artikel worden het schema en de eigenschappen beschreven voor alle ondersteunde elementen van een CreateUiDefinition. 

## <a name="schema"></a>Schema

Het schema voor de meeste elementen is als volgt:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Eigenschap | Vereist | Beschrijving |
| -------- | -------- | ----------- |
| naam | Yes | Een interne id om te verwijzen naar een specifiek exemplaar van een element. Het meest voorkomende gebruik van de element naam bevindt zich in `outputs` , waarbij de uitvoer waarden van de opgegeven elementen worden toegewezen aan de para meters van de sjabloon. U kunt dit ook gebruiken om de uitvoer waarde van een element aan het `defaultValue` van een ander element te koppelen. |
| type | Yes | Het besturings element in de gebruikers interface dat voor het element moet worden weer gegeven. Zie [elementen](#elements)voor een lijst met ondersteunde typen. |
| label | Yes | De weergave tekst van het element. Sommige element typen bevatten meerdere labels. de waarde kan dus een object met meerdere teken reeksen zijn. |
| Standaard | No | De standaard waarde van het element. Sommige element typen ondersteunen complexe standaard waarden, dus de waarde kan een object zijn. |
| Knop | No | De tekst die moet worden weer gegeven in de knop Info van het element. Net als `label` sommige elementen ondersteunen meerdere knop info-teken reeksen. Inline-koppelingen kunnen worden inge sloten met de syntaxis voor de korting.
| standaardwaarde | No | Een of meer eigenschappen die worden gebruikt voor het aanpassen van het validatie gedrag van het element. De ondersteunde eigenschappen voor beperkingen variëren per element type. Sommige element typen bieden geen ondersteuning voor het aanpassen van het validatie gedrag en hebben daarom geen beperkingen voor de eigenschap. |
| opties | No | Aanvullende eigenschappen die het gedrag van het element aanpassen. Net als `constraints` de ondersteunde eigenschappen variëren per element type. |
| gezien | No | Hiermee wordt aangegeven of het element wordt weer gegeven. Als `true` , worden het element en de bijbehorende onderliggende elementen weer gegeven. De standaardwaarde is `true`. [Logische functies](create-uidefinition-functions.md#logical-functions) gebruiken om de waarde van deze eigenschap dynamisch te beheren.

## <a name="elements"></a>Elementen

De documentatie voor elk element bevat een UI-voor beeld, schema, opmerkingen over het gedrag van het element (meestal met betrekking tot validatie en ondersteunde aanpassing) en voorbeeld uitvoer.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Micro soft. common. InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Micro soft. common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Micro soft. common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Micro soft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Volgende stappen

Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
