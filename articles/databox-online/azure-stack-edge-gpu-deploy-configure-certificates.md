---
title: Zelfstudie voor configureren van certificaten voor een Azure Stack Edge-apparaat met GPU in de Azure-portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge met GPU krijgt u de instructie om certificaten op uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 80a857f80fd2c164637e591fbab43123659cd2f7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268173"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Zelfstudie: Certificaten voor uw Azure Stack Edge met GPU configureren

In deze zelfstudie wordt beschreven hoe u certificaten voor uw Azure Stack Edge-apparaat met een onboard GPU kunt configureren door de lokale webinterface te gebruiken.

De tijd die nodig is voor deze stap kan variëren afhankelijk van de specifieke optie die u kiest en hoe de certificaatstroom is opgezet in uw omgeving.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u uw Azure Stack Edge-apparaat met GPU configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge installeren](azure-stack-edge-gpu-deploy-install.md).
* Als u van plan bent uw eigen certificaten te gebruiken:
    - Zorg ervoor dat uw certificaten bij de hand hebt in de juiste indeling, inclusief het certificaat van de ondertekeningsketen.
<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-j-series-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Certificaten configureren voor apparaat


1. Selecteer **Configureren** voor certificaten op de tegel **Beveiliging**. 

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. Afhankelijk van of u de apparaatnaam of het DNS-domein hebt gewijzigd in de tegel **Apparaatinstallatie**, kunt u een van de volgende opties voor uw certificaten kiezen.

    - Als u de apparaatnaam of het DNS-domein in de vorige stap niet hebt gewijzigd en u niet uw eigen certificaten wilt gebruiken, kunt u deze stap overslaan en met de volgende stap verdergaan. Om te beginnen heeft het apparaat automatisch zelfondertekende certificaten gegenereerd. 

        ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd, kunt u een van de volgende opties kiezen om uw apparaat te activeren: 
    
        - **Alle apparaatcertificaten genereren**. De apparaatcertificaten mogen alleen worden gebruikt voor testdoeleinden en niet worden gebruikt met productieworkloads.** Ga naar [Apparaatcertificaten genereren op uw Azure Stack Edge](#generate-device-certificates) voor meer informatie.

        - **Uw eigen certificaten gebruiken**. U kunt uw eigen ondertekende eindpuntcertificaten en de bijbehorende ondertekeningsketens gebruiken. U voegt eerst de ondertekeningsketens toe en uploadt vervolgens de eindpuntcertificaten. **U wordt aangeraden altijd uw eigen certificaten te maken voor productieworkloads.** Ga voor meer informatie naar [Uw eigen certificaten op uw Azure Stack Edge-apparaat gebruiken](#bring-your-own-certificates).
    
        - U kunt een aantal van uw eigen certificaten gebruiken en een aantal apparaatcertificaten genereren. Met de optie **Certificaten genereren** worden alleen de apparaatcertificaten opnieuw gegenereerd.

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd en u geen certificaten genereert of als u uw eigen certificaten gebruikt, wordt de activering geblokkeerd.


### <a name="generate-device-certificates"></a>Apparaatcertificaten genereren

Volg deze stappen om apparaatcertificaten te genereren.

Gebruik de volgende stappen om de Azure Stack Edge-apparaatcertificaten opnieuw te genereren en te downloaden:

1. Ga in de lokale gebruikersinterface van uw apparaat naar **Configuratie > Certificaten**. Selecteer **Certificaten genereren**.

    ![Genereer en download certificaat 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Selecteer **Genereren** bij **Apparaatcertificaten genereren**.

    ![Genereer en download certificaat 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    De apparaatcertificaten worden nu gegenereerd en toegepast. 
    
    > [!IMPORTANT]
    > Terwijl de bewerking voor het genereren van het certificaat wordt uitgevoerd, moet u uw eigen certificaten niet gebruiken, en probeer deze toe te voegen met de optie **+ Certificaat toevoegen**.

    U krijgt een melding wanneer de bewerking is voltooid. Start de browser opnieuw om mogelijke problemen met de cache te voorkomen. 
    
    ![Genereer en download certificaat 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Op de pagina **Certificaten** ziet u dat de kolom **Downloaden** nu is ingevuld en dat er koppelingen voor het downloaden van de opnieuw gegenereerde certificaten beschikbaar zijn. 

    ![Genereer en download certificaat 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Selecteer de downloadkoppeling voor een certificaat en sla het certificaat op wanneer dit wordt gevraagd. 

    ![Genereer en download certificaat 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Herhaal dit proces voor alle certificaten die u wilt downloaden. 
    
    ![Genereer en download certificaat 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    De door het apparaat gegenereerde certificaten worden opgeslagen als DER-certificaten met de volgende naamindeling: 

    `<Device name>_<Endpoint name>.cer`. Deze certificaten bevatten de openbare sleutel voor de bijbehorende certificaten op het apparaat. 

U moet deze certificaten installeren op het clientsysteem dat u gebruikt om toegang tot de eindpunten op het ASE-apparaat te krijgen. Met deze certificaten wordt een vertrouwensrelatie tussen de client en het apparaat tot stand gebracht.

Als u deze certificaten wilt importeren en installeren op de client die u gebruikt om toegang tot het apparaat te krijgen, volgt u de stappen in [Certificaten importeren op de clients die toegang proberen te krijgen tot uw Azure Stack Edge-apparaat](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Als Azure Storage Explorer wordt gebruikt, moet u certificaten op uw client in de PEM-indeling installeren en moet u de door het apparaat gegenereerde certificaten converteren naar de PEM-indeling. 

> [!IMPORTANT]
> - De downloadkoppeling is alleen beschikbaar voor de door het apparaat gegenereerde certificaten en niet als u uw eigen certificaten gebruikt.
> - U kunt ervoor kiezen om een combinatie van door apparaten gegenereerde certificaten te hebben en uw eigen certificaten te gebruiken, zolang er maar aan andere certificaatvereisten wordt voldaan. Ga naar [Certificaatvereisten](azure-stack-edge-j-series-certificate-requirements.md) voor meer informatie.
    

### <a name="bring-your-own-certificates"></a>Uw eigen certificaten gebruiken

Volg deze stappen om uw eigen certificaten met inbegrip van de ondertekeningsketen toe te voegen.

1. Als u een certificaat wilt uploaden, selecteert u op de pagina **Certificaat** de optie **+ Certificaat toevoegen**.

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Upload eerst de ondertekeningsketen en selecteer **Valideren en toevoegen**.

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. U kunt nu andere certificaten uploaden. U kunt bijvoorbeeld de Azure Resource Manager- en Blob Storage-eindpuntcertificaten uploaden.

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    U kunt ook het lokale webinterfacecertificaat uploaden. Nadat u dit certificaat hebt geüpload, wordt u gevraagd om uw browser te starten en de cache te wissen. Vervolgens moet u verbinding maken met de lokale webinterface van het apparaat.  

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    U kunt ook het knooppuntcertificaat uploaden.

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    De certificaatpagina zou moeten worden bijgewerkt en de zojuist toegevoegde certificaten weergeven.

    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > Met uitzondering van de openbare Azure-cloud moeten ondertekeningsketencertificaten worden ingesteld voordat alle cloudconfiguraties worden geactiveerd (Azure Government of Azure Stack).


Het apparaat is nu gereed om te worden geactiveerd.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren

Als u wilt weten hoe u uw Azure Stack Edge-apparaat kunt activeren, gaat u naar:

> [!div class="nextstepaction"]
> [Azure Stack Edge-apparaat activeren](./azure-stack-edge-gpu-deploy-activate.md)
