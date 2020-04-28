---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67175843"
---
Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam para meters die alle parameter waarden bevat.
U moet een para meter definiëren voor die waarden die variëren op basis van het project dat u implementeert of op basis van de omgeving waarin u implementeert. Definieer geen para meters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd. 

Bij het definiëren van para meters, gebruikt u het veld **allowedValues** om op te geven welke waarden een gebruiker tijdens de implementatie kan opgeven. Gebruik het veld **DefaultValue** om een waarde toe te wijzen aan de para meter, als er geen waarde wordt gegeven tijdens de implementatie.

De para meters worden in de sjabloon beschreven.

### <a name="sitename"></a>siteName
De naam van de web-app die u wilt maken.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
De naam van het App Service plan dat moet worden gebruikt voor het hosten van de web-app.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
De prijs categorie voor het hosting abonnement.

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

De sjabloon definieert de waarden die zijn toegestaan voor deze para meter en wijst een standaard waarde (S1) toe als er geen waarde is opgegeven.

### <a name="workersize"></a>workerSize
De instantie grootte van het hosting plan (klein, gemiddeld of groot).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

De sjabloon definieert de waarden die zijn toegestaan voor deze para meter (0, 1 of 2) en wijst een standaard waarde (0) toe als er geen waarde is opgegeven. De waarden komen overeen met klein, gemiddeld en groot.

