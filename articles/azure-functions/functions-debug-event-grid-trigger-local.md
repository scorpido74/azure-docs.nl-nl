---
title: Lokale foutopsporing van Azure Functions Event Grid
description: Leren azure-functies lokaal te debuggen die worden geactiveerd door een gebeurtenis Gebeurtenisraster
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227074"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger

In dit artikel wordt uitgelegd hoe u een lokale functie debuggen waarmee een Azure Event Grid-gebeurtenis wordt verwerkt die wordt opgehaald door een opslagaccount. 

## <a name="prerequisites"></a>Vereisten

- Een bestaande functie-app maken of gebruiken
- Een bestaand opslagaccount maken of gebruiken
- Ngrok [downloaden](https://ngrok.com/) om Azure uw lokale functie te laten aanroepen

## <a name="create-a-new-function"></a>Een nieuwe functie maken

Open uw functie-app in Visual Studio en klik met de rechtermuisknop op de projectnaam in de Solution Explorer en klik op **> Nieuwe Azure-functie toevoegen.**

Selecteer in het venster *Nieuwe Azure-functie* **de trigger van gebeurtenisraster** en klik op **OK**.

![Nieuwe functie maken](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Zodra de functie is gemaakt, opent u het codebestand en kopieert u de URL die boven aan het bestand is weergegeven. Deze locatie wordt gebruikt bij het configureren van de trigger voor gebeurtenisraster.

![Locatie kopiëren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Stel vervolgens een breekpunt in `log.LogInformation`op de lijn die begint met .

![Breekpunt instellen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Druk vervolgens **op F5** om een foutopsporingssessie te starten.

## <a name="allow-azure-to-call-your-local-function"></a>Azure toestaan uw lokale functie aan te roepen

Als u wilt inbreken in een functie die op uw machine wordt gedebugged, moet u een manier inschakelen voor Azure om vanuit de cloud met uw lokale functie te communiceren.

Het [ngrok-hulpprogramma](https://ngrok.com/) biedt Azure een manier om de functie die op uw machine wordt uitgevoerd, aan te roepen. Begin *ngrok* met de volgende opdracht:

```bash
ngrok http -host-header=localhost 7071
```
Aangezien het hulpprogramma is ingesteld, moet het opdrachtvenster er hetzelfde uitzien als de volgende schermafbeelding:

![Start ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopieer de **HTTPS-URL** die wordt gegenereerd wanneer *ngrok* wordt uitgevoerd. Deze waarde wordt gebruikt bij het configureren van het eindpunt van het gebeurtenisrastergebeurtenis.

## <a name="add-a-storage-event"></a>Een opslaggebeurtenis toevoegen

Open de Azure-portal en navigeer naar een opslagaccount en klik op de optie **Gebeurtenissen.**

![Gebeurtenis voor opslagaccount toevoegen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Klik *in* het venster Gebeurtenissen op de knop **Gebeurtenisabonnement.** Klik in het venster *Zelfs abonnement* op de vervolgkeuzelijst *Eindpunttype* en selecteer **Webhaak**.

![Abonnementstype selecteren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Zodra het eindpunttype is geconfigureerd, klikt u op **Een eindpunt selecteren** om de eindpuntwaarde te configureren.

![Eindpunttype selecteren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

De waarde *van het eindpunt* van abonnees bestaat uit drie verschillende waarden. Het voorvoegsel is de HTTPS URL gegenereerd door *ngrok*. De rest van de URL is afkomstig van de URL in het functiecodebestand, met de functienaam aan het einde toegevoegd. Vanaf de URL van het functiecodebestand wordt `http://localhost:7071` de `{functionname}` *ngrok-URL* vervangen en wordt de functienaam vervangen.

De volgende schermafbeelding laat zien hoe de uiteindelijke URL eruit moet zien:

![Eindpuntselectie](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Nadat u de juiste waarde hebt ingevoerd, klikt u op **Selectie bevestigen**.

> [!IMPORTANT]
> Elke keer dat u *ngrok*start, wordt de HTTPS-URL geregenereerd en verandert de waarde. Daarom moet u elke keer dat u uw functie via ngrok aan Azure *blootstelt,* een nieuw gebeurtenisabonnement maken.

## <a name="upload-a-file"></a>Bestand uploaden

Nu u een bestand uploaden naar uw opslagaccount om een gebeurtenis Event Grid te activeren voor uw lokale functie. 

Open [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met het uw opslagaccount. 

- **Blob-containers uitvouwen** 
- Klik met de rechtermuisknop en selecteer **Blobcontainer maken**.
- Geef de **containertest** een naam
- De *testcontainer* selecteren
- Klik op de knop **Uploaden**
- Klik **op Bestanden uploaden**
- Een bestand selecteren en uploaden naar de blobcontainer

## <a name="debug-the-function"></a>De fout opsporing van de functie

Zodra het gebeurtenisraster herkent dat een nieuw bestand is geüpload naar de opslagcontainer, wordt het breekpunt geraakt in uw lokale functie.

![Start ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de bronnen in dit artikel wilt opschonen, verwijdert u de **testcontainer** in uw opslagaccount.

## <a name="next-steps"></a>Volgende stappen

- [Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Trigger voor gebeurtenisraster voor Azure-functies](./functions-bindings-event-grid.md)
