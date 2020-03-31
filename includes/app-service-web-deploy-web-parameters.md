---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175843"
---
Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam Parameters die alle parameterwaarden bevat.
U moet een parameter definiëren voor de waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarop u implementeert. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd. 

Wanneer u parameters definieert, gebruikt u het veld **toegestane waarden** om op te geven welke waarden een gebruiker tijdens de implementatie kan opgeven. Gebruik het veld **standaardWaarde** om een waarde toe te wijzen aan de parameter, als er geen waarde wordt opgegeven tijdens de implementatie.

We beschrijven elke parameter in de sjabloon.

### <a name="sitename"></a>siteNaam
De naam van de web-app die u wilt maken.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
De naam van het App Service-plan dat moet worden gebruikt voor het hosten van de web-app.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
De prijscategorie voor het hostingplan.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

De sjabloon definieert de waarden die zijn toegestaan voor deze parameter en wijst een standaardwaarde (S1) toe als er geen waarde is opgegeven.

### <a name="workersize"></a>workerSize workerSize
De instantiegrootte van het hostingplan (klein, gemiddeld of groot).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

De sjabloon definieert de waarden die zijn toegestaan voor deze parameter (0, 1 of 2) en kent een standaardwaarde (0) toe als er geen waarde is opgegeven. De waarden komen overeen met klein, gemiddeld en groot.

