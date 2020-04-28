---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66249010"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Een Consumer groep toevoegen aan uw IoT-hub

[Consumenten groepen](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) bieden onafhankelijke weer gaven in de gebeurtenis stroom die apps en Azure-Services in staat stellen gegevens onafhankelijk van hetzelfde Event hub-eind punt te gebruiken. In deze sectie voegt u een Consumer groep toe aan het ingebouwde eind punt van uw IoT-hub die later in deze zelf studie wordt gebruikt om gegevens uit het eind punt op te halen.

Voer de volgende stappen uit om een Consumer groep toe te voegen aan uw IoT-hub:

1. Open uw IoT-hub in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het linkerdeel venster **ingebouwde eind punten**, selecteer **gebeurtenissen** in het rechterdeel venster en voer een naam in bij **consumenten groepen**. Selecteer **Opslaan**.

   ![Een Consumer groep maken in uw IoT-hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
