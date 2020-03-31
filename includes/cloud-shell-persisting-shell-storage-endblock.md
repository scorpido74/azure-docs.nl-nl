---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176372"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Lokale bestanden overbrengen naar Cloud Shell
De `clouddrive` map synchroniseert met het Azure-portalopslagblad. Gebruik dit blad om lokale bestanden over te zetten van of naar uw bestandsshare. Het bijwerken van bestanden vanuit Cloud Shell wordt weergegeven in de GUI voor bestandsopslag wanneer u het blad vernieuwt.

### <a name="download-files"></a>Bestanden downloaden

![Lijst met lokale bestanden](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Ga in de Azure-portal naar het gemonteerde bestandsshare.
2. Selecteer het doelbestand.
3. Selecteer de knop **Downloaden.**

### <a name="upload-files"></a>Bestanden uploaden

![Lokale bestanden die moeten worden geüpload](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Ga naar uw gemonteerde bestandsshare.
2. Selecteer de knop **Uploaden**.
3. Selecteer het bestand of de bestanden die u wilt uploaden.
4. Bevestig de upload.

U ziet nu de bestanden die `clouddrive` toegankelijk zijn in uw directory in Cloud Shell.