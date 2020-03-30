---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189010"
---
## <a name="sample-templates"></a>Voorbeeldsjablonen
U vindt hier voorbeeldsjablonen voor aanpassing aan de gebruikersinterface:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Dit project bevat de volgende sjablonen:
- [Oceaanblauw](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Leigrijs](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Ga als volgende over het volgende voorbeeld:

1. Kloon de repo op je lokale machine. Kies een `/ocean_blue` sjabloonmap of `/slate_gray`.
1. Upload alle bestanden onder de `/assets` sjabloonmap en de map naar Blob-opslag zoals beschreven in de vorige secties.
1. Open vervolgens `\*.html` elk bestand in `/ocean_blue` de `/slate_gray`hoofdmap van een van beide of , vervang alle exemplaren van relatieve URL's door de URL's van de css-, afbeeldingen- en lettertypenbestanden die u in stap 2 hebt geüpload. Bijvoorbeeld:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Handeling
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Sla `\*.html` de bestanden op en upload ze naar Blob-opslag.
1. Wijzig nu het beleid, als u naar uw HTML-bestand wijst, zoals eerder vermeld.
1. Als u ontbrekende lettertypen, afbeeldingen of CSS ziet, controleert u \*uw referenties in het extensiebeleid en de HTML-bestanden.
