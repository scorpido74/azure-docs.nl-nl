---
title: Een functie in azure maken die wordt geactiveerd door wachtrij berichten
description: Gebruik Azure Functions voor het maken van een functie zonder server die wordt aangeroepen door berichten die zijn verzonden naar een Azure Storage-wachtrij.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 98f0290aad9971bdb0c0b265d96e96d8ac34b99d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756519"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Een door Azure Queue Storage geactiveerde functie maken

Ontdek hoe u een functie maakt die wordt geactiveerd wanneer er berichten worden verzonden naar een Azure Storage-wachtrij.

![Bekijk het bericht in de logboeken.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Vereisten

- De [Microsoft Azure Storage Explorer](https://storageexplorer.com/) downloaden en installeren.

- Een Azure-abonnement. Als u er nog geen hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-azure-function-app"></a>Een Azure-functie-app maken

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![De functie-app is gemaakt.](./media/functions-create-first-azure-function/function-app-create-success.png)

Vervolgens maakt u een functie in de nieuwe functie-app.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Een door een wachtrij geactiveerde functie maken

1. Vouw de functie-app uit en **+** Klik op de knop naast **functies**. Als dit de eerste functie in de functie-app is, selecteert u **In de portal** en vervolgens **Doorgaan**. Anders gaat u verder met stap drie.

   ![De Quick Start-pagina van Functions in Azure Portal](./media/functions-create-storage-queue-triggered-function/function-app-quickstart-choose-portal.png)

1. Kies **Meer sjablonen** en vervolgens **Voltooien en sjablonen weergeven**.

    ![De Quick Start-pagina ‘Meer sjablonen kiezen’ van Functions](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

1. Typ `queue` in het zoekveld en kies vervolgens de sjabloon **Wachtrijtrigger**.

1. Als hierom wordt gevraagd, selecteert u **installeren** om de Azure Storage extensie en eventuele afhankelijkheden in de functie-app te installeren. Wanneer de installatie is voltooid, selecteert u **Doorgaan**.

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

1. Vouw het gekoppelde opslag account uit, klik met de rechter muisknop op **wacht rijen**, `myqueue-items`Klik op **wachtrij maken**, typ en druk op ENTER.

    ![Maak een opslagwachtrij.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Nu u een opslagwachtrij hebt, kunt u de functie testen door een bericht toe te voegen aan de wachtrij.

## <a name="test-the-function"></a>De functie testen

1. Blader in het Azure Portal naar uw functie, vouw de **Logboeken** aan de onderkant van de pagina uit en zorg ervoor dat logboek streaming niet wordt onderbroken.

1. Vouw in Storage Explorer uw opslag account, **wacht rijen**en **myqueue-items**uit en klik vervolgens op **bericht toevoegen**.

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
