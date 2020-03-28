---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176700"
---
1. Meld u aan op het Data Box-apparaat. Zorg ervoor dat het apparaat is ontgrendeld.

    ![Data Box-dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Ga naar **Netwerkinterfaces instellen**. Noteer het IP-adres van het apparaat voor de netwerkinterface die wordt gebruikt voor verbinding met de client.

    ![Data Box-dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Ga naar **Verbinding maken en kopieer** en klik op **Rest**.

    ![Data Box-dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Kopieer in het dialoogvenster **Opslagaccount openen en gegevens uploaden** het **eindpunt van de blob-service**.

    ![Data Box-dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Start **Kladblok** als beheerder en open vervolgens het bestand met **hosts** dat zich bevindt in `C:\Windows\System32\Drivers\etc`.
6. Voeg de volgende vermelding toe aan uw bestand met **hosts**: `<device IP address> <Blob service endpoint>`
7. Gebruik de volgende afbeelding ter referentie. Sla het bestand met **hosts** op.

    ![Data Box-dashboard](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
