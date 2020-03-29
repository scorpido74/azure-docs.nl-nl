---
title: FileUpload-gebruikersinterface-element
description: Beschrijft het Microsoft.Common.FileUpload-gebruikersinterfaceelement voor Azure-portal. Hiermee moeten gebruikers bestanden uploaden bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652487"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload-element

Een besturingselement waarmee een gebruiker een of meer bestanden kan opgeven om te uploaden.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

Als options.multiple onwaar is en options.uploadMode is bestand, dan is de uitvoer de inhoud van het bestand als json-tekenreeks:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Als options.multiple waar is en'options.uploadMode bestand is, heeft de uitvoer de inhoud van de bestanden als JSON-array:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Als options.multiple onwaar is en options.uploadMode url is, heeft de uitvoer een URL als JSON-tekenreeks:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Als options.multiple waar is en options.uploadMode url is, heeft de uitvoer een lijst met URL's als JSON-array:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Bij het testen van een CreateUiDefinition worden in sommige browsers (zoals Google Chrome) URL's gekapt die zijn gegenereerd door het element Microsoft.Common.FileUpload in de browserconsole. Mogelijk moet u met de rechtermuisknop op afzonderlijke koppelingen klikken om de volledige URL's te kopiëren.

## <a name="remarks"></a>Opmerkingen

- `constraints.accept`hiermee worden de typen bestanden opgegeven die worden weergegeven in het bestandsdialoogvenster van de browser. Zie de [HTML5-specificatie](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) voor toegestane waarden. De standaardwaarde is **null**.
- Als `options.multiple` deze is ingesteld op **true,** mag de gebruiker meer dan één bestand selecteren in het bestandsdialoogvenster van de browser. De standaardwaarde is **onwaar**.
- Dit element ondersteunt het uploaden van bestanden `options.uploadMode`in twee modi op basis van de waarde van . Als **het bestand** is opgegeven, heeft de uitvoer de inhoud van het bestand als blob. Als **de url** is opgegeven, wordt het bestand geüpload naar een tijdelijke locatie en heeft de uitvoer de URL van de blob. Tijdelijke blobs worden na 24 uur verwijderd. De standaardwaarde is **bestand**.
- Een geüpload bestand is beveiligd. De uitvoer-URL bevat een [SAS-token](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor toegang tot het bestand tijdens de implementatie.
- De waarde `options.openMode` van bepaalt hoe het bestand wordt gelezen. Als het bestand naar verwachting platte tekst is, geeft u **tekst**op ; anders, geef **binaire**. De standaardwaarde is **tekst**.
- Als `options.uploadMode` is **file** ingesteld `options.openMode` op bestand en is ingesteld op **binair,** is de uitvoer base64-gecodeerd.
- `options.encoding`hiermee wordt de codering opgegeven die moet worden gebruikt bij het lezen van het bestand. De standaardwaarde is **UTF-8**en `options.openMode` wordt alleen gebruikt wanneer deze is ingesteld op **tekst**.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
