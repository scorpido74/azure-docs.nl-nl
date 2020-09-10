---
title: Zelfstudie voor het activeren van een Azure Stack Edge-apparaat met GPU in de Azure-portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge GPU krijgt u instructie om uw fysieke apparaat te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267935"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Zelfstudie: Azure Stack Edge met GPU activeren

In deze zelfstudie wordt beschreven hoe u uw Azure Stack Edge-apparaat kunt activeren met een onboard GPU door de lokale webgebruikersinterface te gebruiken.

Het activeringsproces kan ongeveer 5 minuten duren.

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Het fysieke apparaat activeren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge-apparaat met GPU configureert en instelt:

* Voor uw fysieke apparaat: 
    
    - U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge installeren](azure-stack-edge-gpu-deploy-install.md).
    - U hebt het netwerk en de rekennetwerkinstellingen geconfigureerd zoals beschreven in [Netwerk, rekennetwerk en webproxy configureren](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - U hebt uw eigen apparaatcertificaten geüpload of de apparaatcertificaten gegenereerd op uw apparaat als u de apparaatnaam of het DNS-domein hebt gewijzigd via de pagina **Apparaat**. Als u deze stap niet hebt uitgevoerd, ziet u een foutbericht tijdens de apparaatactivering en wordt de activering geblokkeerd. Ga naar [Certificaten configureren](azure-stack-edge-gpu-deploy-configure-certificates.md) voor meer informatie.
    
* U hebt de activeringssleutel van de Azure Stack Edge-service die u hebt gemaakt om het Azure Stack Edge-apparaat te beheren. Ga voor meer informatie naar [Voorbereiding voor implementatie van Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Het apparaat activeren

1. Ga in de lokale webgebruikersinterface van het apparaat naar de pagina **Aan de slag**.
2. Op de tegel **Activering** selecteert u **Activeren**. 

    ![Pagina 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. Voer in het deelvenster **Activeren** de **Activeringssleutel** in die u hebt ontvangen in [De activeringssleutel krijgen voor Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Selecteer **Toepassen**.

    ![Pagina 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Eerst wordt het apparaat geactiveerd. Vervolgens wordt u gevraagd het sleutelbestand te downloaden.
    
    ![Pagina 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Selecteer **Sleutelbestand downloaden** en sla het bestand *keys.json* op een veilige locatie buiten het apparaat op. **Dit sleutelbestand bevat de herstelsleutels voor de besturingssysteemschijf en gegevensschijven op uw apparaat**. Hier vindt u de inhoud van het bestand *keys.json*:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    De volgende tabel bevat uitleg over de verschillende sleutels:
    
    |Veld  |Beschrijving  |
    |---------|---------|
    |`Id`    | Dit is de id van het apparaat.        |
    |`DataVolumeBitLockerExternalKeys`|Dit zijn de BitLockers-sleutels voor de gegevensschijven en deze worden gebruikt om de lokale gegevens op uw apparaat te herstellen.|
    |`SystemVolumeBitLockerRecoveryKey`| Dit is de BitLocker-sleutel voor het systeemvolume. Deze sleutel helpt met het herstel van de systeemconfiguratie en de systeemgegevens voor uw apparaat. |
    |`ServiceEncryptionKey`| Deze sleutel beveiligt de gegevens die door de Azure-service stromen. Deze sleutel verzekert dat opgeslagen gegevens niet zullen worden gecompromitteerd als de Azure-service wordt gecompromitteerd. |

6. Ga naar de pagina **Overzicht**. De apparaatstatus zou **Geactiveerd** moeten zijn.

    ![Pagina 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
De apparaatactivering is voltooid. U kunt nu shares toevoegen op uw apparaat.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Het fysieke apparaat activeren

Als u wilt weten hoe u gegevens overdraagt met uw Azure Stack Edge-apparaat, gaat u naar:

> [!div class="nextstepaction"]
> [Gegevens overdragen met Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
