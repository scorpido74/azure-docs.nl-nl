---
title: UI-definitieelementen maken
description: Beschrijft de elementen die u moet gebruiken bij het maken van gebruikersinterfacedefinities voor Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086699"
---
# <a name="createuidefinition-elements"></a>Elementen van UiDefinition maken

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
| name | Ja | Een interne id om naar een specifieke instantie van een element te verwijzen. Het meest voorkomende gebruik van `outputs`de elementnaam is in , waar de uitvoerwaarden van de opgegeven elementen zijn toegewezen aan de parameters van de sjabloon. U het ook gebruiken om de uitvoerwaarde van een element aan het `defaultValue` andere element te binden. |
| type | Ja | De ui-besturingselement om te renderen voor het element. Zie [Elementen](#elements)voor een lijst met ondersteunde typen . |
| label | Ja | De weergavetekst van het element. Sommige elementtypen bevatten meerdere labels, dus de waarde kan een object zijn dat meerdere tekenreeksen bevat. |
| Standaardwaarde | Nee | De standaardwaarde van het element. Sommige elementtypen ondersteunen complexe standaardwaarden, zodat de waarde een object kan zijn. |
| Tooltip | Nee | De tekst die moet worden weergegeven in de knoppunt van het element. Net `label`als bij , sommige elementen ondersteunen meerdere tool tip strings. Inline-koppelingen kunnen worden ingesloten met de syntaxis Markdown.
| Beperkingen | Nee | Een of meer eigenschappen die worden gebruikt om het validatiegedrag van het element aan te passen. De ondersteunde eigenschappen voor beperkingen verschillen per elementtype. Sommige elementtypen ondersteunen geen aanpassing van het validatiegedrag en hebben dus geen eigenschap beperkingen. |
| opties | Nee | Extra eigenschappen die het gedrag van het element aanpassen. Net `constraints`als bij , de ondersteunde eigenschappen variëren per elementtype. |
| Zichtbaar | Nee | Geeft aan of het element wordt weergegeven. Als `true`, worden het element en de toepasselijke onderliggende elementen weergegeven. De standaardwaarde is `true`. Gebruik [logische functies](create-uidefinition-functions.md#logical-functions) om de waarde van deze eigenschap dynamisch te bepalen.

## <a name="elements"></a>Elementen

De documentatie voor elk element bevat een ui-voorbeeld, schema, opmerkingen over het gedrag van het element (meestal met betrekking tot validatie en ondersteunde aanpassing) en voorbeelduitvoer.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Volgende stappen

Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
