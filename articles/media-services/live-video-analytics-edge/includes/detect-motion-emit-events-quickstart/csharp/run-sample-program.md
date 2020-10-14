---
ms.openlocfilehash: 2349939d4997ddc57d0c0c56a21eeec0357bf0ec
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828838"
---
Volg deze stappen om de voorbeeldcode uit te voeren:

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Extensie-instellingen"
          }
        ]
      }
    }
    ```
     
    * Een aanroep van `GraphInstanceActivate` waarmee de instantie van de grafiek en de videostroom worden gestart.
    * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat het grafiekexemplaar wordt uitgevoerd.
1. De uitvoer in het **TERMINAL**-venster wordt gepauzeerd bij `Press Enter to continue`. Selecteer Enter nog niet. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze quickstart worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagraaf wilt stoppen, keert u terug naar het **TERMINAL**-venster en selecteert u Enter. 

    Met de volgende reeks aanroepen worden resources opgeschoond:

    * Met een aanroep van `GraphInstanceDeactivate` wordt het graafexemplaar gedeactiveerd.
    * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
    * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
    * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst leeg is.
