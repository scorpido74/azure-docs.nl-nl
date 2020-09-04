---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690988"
---
1. Ga in Visual Studio Code naar *src/edge*. U ziet het bestand *.env* en enkele implementatiesjabloonbestanden.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat, waarbij variabelen zijn gebruikt voor bepaalde eigenschappen. Het bestand *.env* bevat de waarden voor die variabelen.
1. Ga naar de map *src/cloud-to-device-console-app*. Hier ziet u het bestand *appsettings.json* en enkele andere bestanden:

    * ***operations.json***: een lijst met de bewerkingen die u het programma wilt laten uitvoeren.
    * **main.py**: de voorbeeldprogrammacode. Deze code:
    
      * De app-instellingen laden.
      * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om livevideostreams te analyseren door de bijbehorende [directe methoden](../../../direct-methods.md) aan te roepen.
      * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het **TERMINAL**-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het **UITVOER**-venster.
      * Roept directe methoden aan voor het opschonen van resources.   

