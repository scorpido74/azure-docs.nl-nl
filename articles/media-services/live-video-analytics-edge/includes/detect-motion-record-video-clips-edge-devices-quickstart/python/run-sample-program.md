---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682207"
---
1. Start een foutopsporingssessie door de toets F5 te selecteren. Het **TERMINAL**-venster geeft enkele berichten weer.
1. De code *operations.json* roept de directe methodes `GraphTopologyList` en `GraphInstanceList` aan. Als u na de vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Druk op de Enter-toets.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  In het **TERMINAL**-venster wordt de volgende set aanroepen van directe methoden weergegeven:  
  
  * Een aanroep van `GraphTopologySet` waarin gebruik wordt gemaakt van de `topologyUrl` 
  * Een aanroep van `GraphInstanceSet` waarin gebruik wordt gemaakt van de volgende hoofdtekst:
  
  ```
  {
    "@apiVersion": "1.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
    }
  }
  ```
    
  * Een aanroep van `GraphInstanceActivate` waarmee de instantie van de grafiek en de videostroom worden gestart.
  * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat de instantie van de grafiek wordt uitgevoerd.
1. De uitvoer in het **TERMINAL**-venster wordt gepauzeerd bij `Press Enter to continue`. Druk nog niet op Enter. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze quickstart worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagrafiek wilt stoppen, keert u terug naar het **TERMINAL**-venster en drukt u op Enter. 

    Met de volgende reeks aanroepen worden de resources opgeschoond:

    * Met een aanroep van `GraphInstanceDeactivate` wordt de instantie van de grafiek gedeactiveerd.
    * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
    * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
    * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst nu leeg is.
