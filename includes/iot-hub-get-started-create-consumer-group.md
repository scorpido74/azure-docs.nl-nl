---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66249010"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Een consumentengroep toevoegen aan uw IoT-hub

[Consumentengroepen](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) bieden onafhankelijke weergaven in de gebeurtenisstream waarmee apps en Azure-services onafhankelijk gegevens van hetzelfde eindpunt van Event Hub kunnen gebruiken. In deze sectie voegt u een consumentengroep toe aan het ingebouwde eindpunt van uw IoT-hub, dat later in deze zelfstudie wordt gebruikt om gegevens uit het eindpunt te halen.

Voer de volgende stappen uit om een consumentengroep toe te voegen aan uw IoT-hub:

1. Open uw IoT-hub in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het linkerdeelvenster **ingebouwde eindpunten,** selecteer **Gebeurtenissen** in het rechterdeelvenster en voer een naam in onder **Consumentengroepen**. Selecteer **Opslaan**.

   ![Een consumentengroep maken in uw IoT-hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
