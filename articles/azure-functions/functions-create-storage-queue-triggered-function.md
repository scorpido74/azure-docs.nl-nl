---
title: Een functie in Azure maken die wordt geactiveerd door wachtrijberichten
description: Gebruik Azure Functions voor het maken van een functie zonder server die wordt aangeroepen door berichten die zijn verzonden naar een Azure Storage-wachtrij.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: quickstart
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3d4cfc40f1849ecd2745b1d662973c7f64a0a60c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75769248"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Een door Azure Queue Storage geactiveerde functie maken

Ontdek hoe u een functie maakt die wordt geactiveerd wanneer er berichten worden verzonden naar een Azure Storage-wachtrij.

![Bekijk het bericht in de logboeken.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Vereisten

- De [Microsoft Azure Storage Explorer](https://storageexplorer.com/) downloaden en installeren.

- Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

## <a name="create-an-azure-function-app"></a>Een Azure-functie-app maken

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![De functie-app is gemaakt.](./media/functions-create-first-azure-function/function-app-create-success.png)

Vervolgens maakt u een functie in de nieuwe functie-app.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Een door een wachtrij geactiveerde functie maken

1. Vouw de functie-app **+** uit en klik op de knop naast **Functies**. Als dit de eerste functie in de functie-app is, selecteert u **In de portal** en vervolgens **Doorgaan**. Anders gaat u verder met stap drie.

   ![De Quick Start-pagina van Functions in Azure Portal](./media/functions-create-storage-queue-triggered-function/function-app-quickstart-choose-portal.png)

1. Kies **Meer sjablonen** en vervolgens **Voltooien en sjablonen weergeven**.

    ![De Quick Start-pagina ‘Meer sjablonen kiezen’ van Functions](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

1. Typ `queue` in het zoekveld en kies vervolgens de sjabloon **Wachtrijtrigger**.

1. Selecteer deoptie **Installeren** om de Azure Storage-extensie en eventuele afhankelijkheden in de functie-app te installeren als u daarom wordt gevraagd. Wanneer de installatie is voltooid, selecteert u **Doorgaan**.

    ![Binding-extensies installeren](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

1. Gebruik de instellingen zoals opgegeven in de tabel onder de afbeelding.

    ![Configureer de door de opslagwachtrij geactiveerde functie.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Naam** | Uniek in uw functie-app | Naam van deze door een wachtrij geactiveerde functie. |
    | **Wachtrijnaam**   | myqueue-items    | De naam van de wachtrij waarmee u verbinding moet maken in uw opslagaccount. |
    | **Opslagaccountverbinding** | AzureWebJobsStorage | U kunt de opslagaccountverbinding gebruiken die al door de functie-app wordt gebruikt of u kunt een nieuwe maken.  |    

1. Klik op **Maken** om de functie te maken.

Vervolgens maakt u verbinding met uw Azure Storage-account en maakt u de opslagwachtrij **myqueue-items**.

## <a name="create-the-queue"></a>De wachtrij maken

1. Klik in de functie op **Integreren**, vouw **Documentatie** uit en kopieer de **Accountnaam** en de **Accountsleutel**. Met deze referenties kunt u verbinding maken met het opslagaccount in Azure Storage Explorer. Als u uw opslagaccount al hebt verbonden, gaat u naar stap 4.

    ![Haal de verbindingsreferenties voor het opslagaccount op.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Voer het hulpprogramma [Microsoft Azure Storage Explorer](https://storageexplorer.com/) uit, klik op het verbindingspictogram aan de linkerkant, kies **Een opslagaccountnaam en -sleutel gebruiken** en klik op **Volgende**.

    ![Voer het hulpprogramma Storage Account Explorer uit.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Voer de **Accountnaam** en de **Accountsleutel** van stap 1 in. Klik op **Volgende** en vervolgens op **Verbinding maken**.

    ![Voer de opslagreferenties in en maak verbinding.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Vouw het bijgevoegde opslagaccount uit, klik met `myqueue-items`de rechtermuisknop op **Wachtrijen,** klik op **Wachtrij maken,** typ en druk op Enter.

    ![Maak een opslagwachtrij.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Nu u een opslagwachtrij hebt, kunt u de functie testen door een bericht toe te voegen aan de wachtrij.

## <a name="test-the-function"></a>De functie testen

1. Terug in de Azure-portal blader je naar je functie, vouw je de **logboeken** onder aan de pagina uit en zorg ervoor dat logboekstreaming niet wordt onderbroken.

1. Vouw in Storage Explorer uw opslagaccount, **wachtrijen**en **myqueue-items**uit en klik vervolgens op **Bericht toevoegen**.

    ![Voeg een bericht toe aan de wachtrij.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Typ uw "Hallo wereld"- bericht in **Berichttekst** en klik op **OK**.

1. Wacht een paar seconden en ga vervolgens terug naar de functielogboeken en controleer of het nieuwe bericht is gelezen.

    ![Bekijk het bericht in de logboeken.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Klik in Storage Explorer op **Vernieuwen** en controleer of het bericht is verwerkt en niet langer in de wachtrij staat.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie gemaakt die wordt uitgevoerd wanneer er een bericht wordt toegevoegd aan een opslagwachtrij. Zie [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Opslagwachtrijbindingen van Azure Functions) voor meer informatie over activeringen van Queue Storage.

Nu u uw eerste functie hebt gemaakt, gaan we een uitvoerbinding aan de functie toevoegen die een bericht terug schrijft naar een andere wachtrij.

> [!div class="nextstepaction"]
> [Berichten aan een Azure Storage-wachtrij toevoegen met behulp van Functies](functions-integrate-storage-queue-output-binding.md)
