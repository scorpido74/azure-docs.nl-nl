---
title: De Azure Portal gebruiken om problemen met Azure Stack Edge op te lossen | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen met Azure Stack Edge oplost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: 00aae3386b3f648eafdef31dccb640d8120f7ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564006"
---
# <a name="troubleshoot-your-azure-stack-edge-issues"></a>Problemen met de Azure Stack rand oplossen

In dit artikel wordt beschreven hoe u problemen op uw Azure Stack rand kunt oplossen. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Diagnose uitvoeren
> * Ondersteuningspakket verzamelen
> * Logboeken gebruiken om problemen op te lossen


## <a name="run-diagnostics"></a>Diagnose uitvoeren

U kunt de diagnostische tests uitvoeren om apparaatfouten vast te stellen en op te lossen. Voer de volgende stappen uit in de lokale webinterface van uw apparaat om diagnostische tests uit te voeren.

1. Ga in de lokale webinterface naar **Probleemoplossing > Diagnostische tests**. Selecteer de test die u wilt uitvoeren en klik op **Test uitvoeren**. Hiermee voert u de tests uit om mogelijke problemen met uw netwerk-, apparaat-, webproxy-, tijd- of cloudinstellingen vast te stellen. U ontvangt een melding dat er tests op het apparaat worden uitgevoerd.

    ![Testen selecteren](media/azure-stack-edge-troubleshoot/run-diag-1.png)
 
2. Wanneer de tests zijn voltooid, worden de resultaten weergegeven. 

    ![Test resultaten controleren](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Als een test mislukt, wordt er een URL voor de aanbevolen actie weergegeven. U kunt op de URL klikken om de aanbevolen actie weer te geven.
 
    ![Waarschuwingen voor mislukte testen controleren](media/azure-stack-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Ondersteuningspakket verzamelen

Er wordt een logboekpakket samengesteld van alle relevante logboeken die Microsoft Ondersteuning kunnen helpen bij het oplossen van eventuele problemen met het apparaat. U kunt een logboekpakket genereren via de lokale webinterface.

Voer de volgende stappen uit om een ondersteuningspakket te verzamelen. 

1. Ga in de lokale webinterface naar **Probleemoplossing > Ondersteuning**. Klik op **Ondersteuningspakket maken**. Het systeem begint met het verzamelen van het ondersteuningspakket. Het verzamelen van het pakket kan enkele minuten duren.

    ![Klikken op Gebruiker toevoegen](media/azure-stack-edge-troubleshoot/collect-logs-1.png)
 
2. Wanneer het ondersteuningspakket is gemaakt, klikt u op **Ondersteuningspakket downloaden**. Er wordt een gecomprimeerd pakket gedownload naar het pad dat u hebt gekozen. U kunt het pakket uitpakken en de systeemlogboekbestanden bekijken.

    ![Klikken op Gebruiker toevoegen](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Logboeken gebruiken om problemen op te lossen

Eventuele fouten die zich hebben voorgedaan tijdens de upload- en vernieuwingsprocessen zijn opgenomen in de respectieve foutbestanden.

1. Om de foutbestanden te bekijken, gaat u naar uw share en klikt u op de share om de inhoud weer te geven. 

      ![Verbinding maken met en de share-inhoud weer geven](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Klik op de _map Microsoft Azure stack Edge_. Deze map heeft twee submappen:

    - uploadmap met logboekbestanden van fouten tijdens het uploaden.
    - vernieuwingsmap met logboekbestanden van fouten tijdens het vernieuwen.

    Hier volgt een voorbeeld van een logboekbestand voor vernieuwen.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Als u in dit bestand een fout ziet (gemarkeerd in het voorbeeld), noteert u de foutcode (in dit geval 16001). Zoek de beschrijving van deze foutcode op in de volgende naslaginformatie voor fouten.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [bekende problemen in deze versie](data-box-gateway-release-notes.md).
