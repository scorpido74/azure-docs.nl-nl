---
title: Een GPU-module op Microsoft Azure Stack Edge GPU-apparaat uitvoeren | Microsoft Docs
description: Hierin wordt beschreven hoe u via de Azure Portal een module op GPU kunt configureren en uitvoeren op een Azure Stack edge-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 19b6001e7bf5038b4c5e6112266d1e5dfc0a792b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146314"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-device"></a>Een module op GPU configureren en uitvoeren op Azure Stack edge-apparaat

Uw Azure Stack edge-apparaat bevat een of meer GPU (graphics processing unit). Gpu's zijn een populaire keuze voor AI-berekeningen wanneer ze parallelle verwerkings mogelijkheden bieden en sneller zijn bij het weer geven van afbeeldingen dan Cpu's (Central Processing Units). Voor meer informatie over de GPU die zich in uw Azure Stack edge-apparaat bevindt, gaat u naar de [technische specificaties van het Azure stack edge-apparaat](azure-stack-edge-gpu-technical-specifications-compliance.md).

In dit artikel wordt beschreven hoe u een module kunt configureren en uitvoeren op de GPU op uw Azure Stack edge-apparaat. In dit artikel gebruikt u een openbaar toegankelijke container module **cijfers** die zijn geschreven voor nvidia T4-gpu's. Deze procedure kan worden gebruikt voor het configureren van andere modules die door NVIDIA worden gepubliceerd voor deze Gpu's.


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een met GPU ingeschakelde 1-knoop punt Azure Stack edge-apparaat. Dit apparaat wordt geactiveerd met een resource in Azure.  

## <a name="configure-module-to-use-gpu"></a>Module configureren voor het gebruik van GPU

Als u een module wilt configureren voor het gebruik van de GPU op uw Azure Stack edge-apparaat om een module uit te voeren, volgt u deze stappen.

1. Ga in het Azure Portal naar de resource die aan uw apparaat is gekoppeld. 

2. Ga naar **Edge-rekenproces > Aan de slag**. Selecteer configureren in de tegel **Edge Compute configureren** .

    ![Module configureren voor het gebruik van GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. Op de Blade **Edge Compute configureren** :

    1. Kies voor **IOT hub**de optie **nieuwe maken**.
    2. Geef een naam op voor de IoT Hub resource die u voor uw apparaat wilt maken. Als u een gratis laag wilt gebruiken, selecteert u een bestaande resource. 
    3. Noteer het IoT Edge apparaat en het IoT-gateway apparaat dat is gemaakt met de IoT Hub bron. U gebruikt deze informatie in de volgende stappen.

    ![Module configureren voor het gebruik van GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Het duurt enkele minuten om de IoT Hub resource te maken. Nadat de resource is gemaakt, selecteert u in de tegel **Edge Compute configureren** de optie **configuratie weer geven** om de details van de IOT hub resource weer te geven.

    ![Module configureren voor het gebruik van GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Ga naar **automatische apparaatbeheer > IOT Edge**.

    ![Module configureren voor het gebruik van GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    In het rechterdeel venster ziet u het IoT Edge apparaat dat is gekoppeld aan uw Azure Stack edge-apparaat. Dit komt overeen met het IoT Edge apparaat dat u in de vorige stap hebt gemaakt bij het maken van de IoT Hub resource. 
    
6. Selecteer dit IoT Edge apparaat.

   ![Module configureren voor het gebruik van GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Selecteer **Modules instellen**.

    ![Module configureren voor het gebruik van GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. Selecteer **+ toevoegen** en selecteer vervolgens **+ IOT Edge module**. 

    ![Module configureren voor het gebruik van GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. Op het tabblad **IOT Edge module toevoegen** :

    1. Geef de **afbeeldings-URI**op. U gebruikt de openbaar beschik bare nvidia-module **cijfers** . 
    
    2. Stel **beleid voor opnieuw opstarten** in op **altijd**.
    
    3. Stel de **gewenste status** in op **actief**.
    
    ![Module configureren voor het gebruik van GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. Geef op het tabblad **omgevings variabelen** de naam op van de variabele en de bijbehorende waarde. 

    1. Gebruik de NVIDIA_VISIBLE_DEVICES om de huidige module één GPU op dit apparaat te laten gebruiken. 

    2. Stel de waarde in op 0 of 1. Dit zorgt ervoor dat er ten minste één GPU door het apparaat wordt gebruikt voor deze module. Wanneer u de waarde instelt op 0, 1, betekent dit dat zowel de Gpu's op het apparaat door deze module worden gebruikt.

        ![Module configureren voor het gebruik van GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Voor meer informatie over omgevings variabelen die u met de NVIDIA GPU kunt gebruiken, gaat u naar [NVIDIA container runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Een GPU kan alleen worden toegewezen aan een module. Een module kan echter een van beide of geen Gpu's gebruiken. 

11. Voer een naam in voor uw module. Op dit moment kunt u de optie voor het maken van een container opgeven en de dubbele instellingen voor de module wijzigen of als u klaar bent, selecteert u **toevoegen**. 

    ![Module configureren voor het gebruik van GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Zorg ervoor dat de module wordt uitgevoerd en selecteer **controleren + maken**.    

    ![Module configureren voor het gebruik van GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. Op het tabblad **controleren en maken** worden de implementatie opties weer gegeven die u hebt geselecteerd. Controleer de opties en selecteer **maken**.
    
    ![Module configureren voor het gebruik van GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Noteer de **runtime status** van de module. 
    
    ![Module configureren voor het gebruik van GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Het duurt enkele minuten voordat de module is geïmplementeerd. Selecteer **vernieuwen** en controleer of de **runtime status** update **actief**is.

    ![Module configureren voor het gebruik van GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [omgevings variabelen die u met de NVIDIA GPU kunt gebruiken](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
