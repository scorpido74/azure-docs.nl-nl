---
title: Azure Functions Event Grid lokale fout opsporing
description: Meer informatie over het lokaal opsporen van Azure-functies die worden geactiveerd door een Event Grid gebeurtenis
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227074"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure function Event Grid lokale fout opsporing activeren

In dit artikel wordt beschreven hoe u fouten opspoort in een lokale functie die een Azure Event Grid gebeurtenis veroorzaakt door een opslag account. 

## <a name="prerequisites"></a>Vereisten

- Een bestaande functie-app maken of gebruiken
- Een bestaand opslag account maken of gebruiken
- [Ngrok](https://ngrok.com/) downloaden zodat Azure uw lokale functie kan aanroepen

## <a name="create-a-new-function"></a>Een nieuwe functie maken

Open de functie-app in Visual Studio en klik met de rechter muisknop op de naam van het project in de Solution Explorer en vervolgens op **> nieuwe Azure-functie toevoegen**.

Selecteer in het venster *nieuwe Azure-functie* de optie **Event grid trigger** en klik op **OK**.

![Nieuwe functie maken](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Zodra de functie is gemaakt, opent u het code bestand en kopieert u de URL naar de bovenkant van het bestand. Deze locatie wordt gebruikt bij het configureren van de Event Grid trigger.

![Locatie kopiëren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Stel vervolgens een onderbrekings punt in op de regel die begint met `log.LogInformation`.

![Onderbrekings punt instellen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Druk vervolgens **op F5** om een foutopsporingssessie te starten.

## <a name="allow-azure-to-call-your-local-function"></a>Azure toestaan uw lokale functie aan te roepen

Als u een fout wilt opsporen in een functie die wordt opgespoord op uw computer, moet u een manier inschakelen waarop Azure met uw lokale functie vanuit de cloud kan communiceren.

Het hulp programma [ngrok](https://ngrok.com/) biedt een manier om Azure de functie die wordt uitgevoerd op uw computer aan te roepen. Start *ngrok* met de volgende opdracht:

```bash
ngrok http -host-header=localhost 7071
```
Wanneer het hulp programma is ingesteld, moet het opdracht venster er ongeveer uitzien als in de volgende scherm afbeelding:

![Ngrok starten](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopieer de **https** -URL die wordt gegenereerd wanneer *ngrok* wordt uitgevoerd. Deze waarde wordt gebruikt bij het configureren van het gebeurtenis eind punt gebeurtenis raster.

## <a name="add-a-storage-event"></a>Een opslag gebeurtenis toevoegen

Open de Azure Portal en navigeer naar een opslag account en klik op de optie **gebeurtenissen** .

![Gebeurtenis van opslag account toevoegen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Klik in het venster *gebeurtenissen* op de knop **gebeurtenis abonnement** . Klik in het venster *even abonnement* op de vervolg keuzelijst *type eind punt* en selecteer **webhook**.

![Type abonnement selecteren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Zodra het eindpunt type is geconfigureerd, klikt u op **Selecteer een eind punt** om de eindpunt waarde te configureren.

![Type eind punt selecteren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

De *abonnee-eindpunt* waarde bestaat uit drie verschillende waarden. Het voor voegsel is de HTTPS-URL die door *ngrok*wordt gegenereerd. De rest van de URL is afkomstig van de URL die in het functie code bestand is gevonden, waarbij de functie naam aan het einde wordt toegevoegd. Met ingang van de URL van het bestand met de functie code wordt de *ngrok* -url vervangen `http://localhost:7071` en wordt de functie naam vervangen door `{functionname}`.

In de volgende scherm afbeelding ziet u hoe de uiteindelijke URL eruit moet zien:

![Eindpunt selectie](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Zodra u de juiste waarde hebt ingevoerd, klikt u op **selectie bevestigen**.

> [!IMPORTANT]
> Telkens wanneer u *ngrok*start, wordt de HTTPS-URL opnieuw gegenereerd en wordt de waarde gewijzigd. Daarom moet u telkens wanneer u uw functie beschikbaar maakt, een nieuw gebeurtenis abonnement maken op Azure via *ngrok*.

## <a name="upload-a-file"></a>Bestand uploaden

U kunt nu een bestand uploaden naar uw opslag account om een Event Grid gebeurtenis te activeren, zodat uw lokale functie kan worden afgehandeld. 

Open [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met het opslag account. 

- **BLOB-containers** uitvouwen 
- Klik met de rechter muisknop en selecteer **BLOB-container maken**.
- De container **test** een naam
- De *test* container selecteren
- Klik op de knop **uploaden**
- Klik op **bestanden uploaden**
- Een bestand selecteren en dit uploaden naar de BLOB-container

## <a name="debug-the-function"></a>Fouten opsporen in de functie

Zodra de Event Grid een nieuw bestand detecteert dat naar de opslag container is geüpload, wordt het breek punt in uw lokale functie bereikt.

![Ngrok starten](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt, wilt opschonen, verwijdert u de **test** container in uw opslag account.

## <a name="next-steps"></a>Volgende stappen

- [Het wijzigen van het formaat van geüploade afbeeldingen automatiseren met behulp van Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid trigger voor Azure Functions](./functions-bindings-event-grid.md)
