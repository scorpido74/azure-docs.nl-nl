---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67176702"
---
Volg deze stappen om verbinding te maken met het opslagaccount en de verbinding te controleren.

1. Open in Storage Explorer het dialoogvenster **Verbinding maken met Azure Storage**. Selecteer in het dialoogvenster **Verbinding maken met Azure Storage** de optie **Een opslagaccountnaam en -sleutel gebruiken**.

    ![Data Box-dashboard](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Plak uw **accountnaam** en **accountsleutel** (de waarde van sleutel 1 op de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface). Selecteer het domein van opslageindpunten als **Andere (hieronder opgeven)** en geef het eindpunt van de blob-service op, zoals hieronder weergegeven. Schakel de optie **HTTP gebruiken** alleen in wanneer u gegevens overbrengt via *http*. Als u *https* gebruikt, laat u de optie uitgeschakeld. Selecteer **Next**.

    ![Data Box-dashboard](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Controleer in het dialoogvenster **Samenvatting verbinding** de gegevens die u hebt opgegeven. Selecteer **Verbinden**.

    ![Data Box-dashboard](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Het account dat is toegevoegd, wordt weergegeven in het linkerdeelvenster van Storage Explorer met (Externe, Andere) toegevoegd aan de naam. Klik op **Blobcontainers** om de container weer te geven.

    ![Data Box-dashboard](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
