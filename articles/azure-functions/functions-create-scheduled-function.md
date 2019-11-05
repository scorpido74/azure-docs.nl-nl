---
title: Een functie maken in Azure die volgens een schema wordt uitgevoerd | Microsoft Docs
description: Ontdek hoe u in Azure een functie maakt die wordt uitgevoerd op basis van een schema dat u definieert.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: a070a7da48af8abe138900dd0221bace2149aa20
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469313"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Maak een functie in Azure die wordt geactiveerd door een timer

Meer informatie over het gebruik van Azure Functions om een functie zonder [Server](https://azure.microsoft.com/solutions/serverless/) te maken die wordt uitgevoerd op basis van een schema dat u definieert.

![Functie-app maken in Azure Portal](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

+ Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-azure-function-app"></a>Een Azure-functie-app maken

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![De functie-app is gemaakt.](./media/functions-create-first-azure-function/function-app-create-success.png)

Vervolgens maakt u een functie in de nieuwe functie-app.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Een door een timer geactiveerde functie maken

1. Vouw de functie-app uit en klik op de knop **+** naast **Functies**. Als dit de eerste functie in de functie-app is, selecteert u **In de portal** en vervolgens **Doorgaan**. Als dat niet het geval is, gaat u naar stap 3.

   ![De Quick Start-pagina van Functions in Azure Portal](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Kies **Meer sjablonen** en vervolgens **Voltooien en sjablonen weergeven**.

    ![De Quick Start-pagina 'Meer sjablonen kiezen' van Functions](./media/functions-create-scheduled-function/add-first-function.png)

3. Typ `timer` in het zoekveld en configureer de nieuwe trigger met de instellingen zoals opgegeven in de tabel onder de afbeelding.

    ![Maak een door een timer geactiveerde functie in Azure Portal.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Naam** | Standaard | Bepaalt de naam van de door de timer geactiveerde functie. |
    | **Planning** | 0 \*/1 \* \* \* \* | Een [CRON-expressie](functions-bindings-timer.md#ncrontab-expressions) met zes velden aan de hand waarvan uw functie elke minuut wordt uitgevoerd. |

4. Klik op **Maken**. Er wordt een functie gemaakt in uw gekozen taal en deze wordt elke minuut uitgevoerd.

5. Controleer of dit correct wordt uitgevoerd door de traceringsinformatie die naar logboeken wordt geschreven te bekijken.

    ![De viewer voor functielogboeken in Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

U kunt het schema van de functie nu wijzigen zodat deze één keer per uur wordt uitgevoerd in plaats van elke minuut.

## <a name="update-the-timer-schedule"></a>Het timerschema bijwerken

1. Vouw de functie uit en klik op **Integreren**. Dit is waar u de invoer- en uitvoerbindingen voor de functie definieert en het schema instelt. 

2. Voer een nieuwe waarde voor **Planning** per uur in van `0 0 */1 * * *` in, en klik vervolgens op **Opslaan**.  

![Het timerschema voor het bijwerken van functies in Azure Portal.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

U hebt nu een functie die één keer per uur wordt uitgevoerd. 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie gemaakt die wordt uitgevoerd op basis van een schema. Zie [Schedule code execution with Azure Functions](functions-bindings-timer.md) (Code-uitvoering plannen met Azure Functions) voor meer informatie over timeractiveringen.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
