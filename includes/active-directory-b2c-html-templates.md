---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189010"
---
## <a name="sample-templates"></a>Voorbeeldsjablonen
U kunt hier voorbeeld sjablonen voor UI-aanpassing vinden:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Dit project bevat de volgende sjablonen:
- [Oceaan blauw](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Pastel grijs](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Het voor beeld gebruiken:

1. Kloon de opslag plaats op uw lokale machine. Kies een sjabloon map `/ocean_blue` of `/slate_gray` .
1. Upload alle bestanden in de map Temp late en de `/assets` map naar Blob Storage, zoals beschreven in de vorige secties.
1. Open vervolgens elk `\*.html` bestand in de hoofdmap van ofwel `/ocean_blue` of `/slate_gray` , vervang alle exemplaren van relatieve Url's door de url's van de CSS-, afbeeldings-en letter typen bestanden die u hebt geüpload in stap 2. Bijvoorbeeld:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Handeling
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Sla de `\*.html` bestanden op en upload deze naar de Blob-opslag.
1. Pas het beleid aan, zoals eerder is vermeld, naar uw HTML-bestand.
1. Als u ontbrekende letter typen, afbeeldingen of CSS ziet, controleert u uw referenties in het uitbrei ding beleid en de \* . html-bestanden.
