---
title: 'Snelstartgids: aangepaste gebeurtenissen verzenden naar Azure function-Event Grid'
description: 'Snelstartgids: gebruik Azure Event Grid en Azure CLI of portal om een onderwerp te publiceren en u te abonneren op deze gebeurtenis. Een Azure-functie wordt gebruikt voor het eind punt.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5e38571cf84537fd722093b96cd277743e8ce80c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80292151"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Snelstartgids: aangepaste gebeurtenissen door sturen naar een Azure-functie met Event Grid

Azure Event Grid is een gebeurtenisservice voor de cloud. Azure Functions is een van de ondersteunde gebeurtenis-handlers. In dit artikel gebruikt u Azure Portal om een aangepast onderwerp te maken, u op het aangepaste onderwerp te abonneren, en de gebeurtenis te activeren om het resultaat weer te geven. U verzendt de gebeurtenissen naar een Azure-functie.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Azure-functie maken

Voordat u zich abonneert op het aangepaste onderwerp, kunt u een functie maken om de gebeurtenissen te verwerken. Klik in de Azure Portal op een resource maken en typ functie en kies vervolgens functie-app en klik vervolgens op maken. Selecteer nieuwe maken onder resource groep en geef deze een naam. U gebruikt deze voor de rest van de zelf studie. Geef de functie-app een naam, laat de optie ' publiceren ' op ' code ' staan, selecteer een wille keurige runtime en regio en klik vervolgens op maken.

Als uw functie-app klaar is, gaat u naar deze en klikt u op + nieuwe functie. Selecteer in de portal voor de ontwikkel omgeving en klik op door gaan. Kies onder een functie maken de optie meer sjablonen om meer sjablonen weer te geven en zoek vervolgens naar ' Azure Event Grid trigger ' en selecteer deze. Als dit de eerste keer is dat u deze trigger gebruikt, moet u mogelijk op installeren klikken om de uitbrei ding te installeren.

![Trigger voor functie Event Grid](./media/custom-event-to-function/grid-trigger.png)

Nadat u de uitbrei ding hebt geïnstalleerd, klikt u op door gaan, geeft u een naam op voor de functie en klikt u vervolgens op maken.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een Event Grid-onderwerp biedt een door de gebruiker gedefinieerd eindpunt waarop u de gebeurtenissen kunt posten. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **alle services** in het navigatie menu links, zoek naar **Event Grid**en selecteer **Event grid onderwerpen**. 

    ![Event Grid onderwerpen selecteren](./media/custom-event-to-function/select-event-grid-topics.png)
3. Selecteer op de pagina **Event grid onderwerpen** **+ toevoegen** op de werk balk. 

    ![Knop Event Grid onderwerp toevoegen](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Voer op de pagina **onderwerp maken** de volgende stappen uit:

    1. Geef een unieke **naam** op voor het aangepaste onderwerp. De onderwerpnaam moet uniek zijn omdat deze wordt vertegenwoordigd door een DNS-vermelding. Gebruik niet de naam die in de afbeelding wordt weergegeven. Maak in plaats daarvan uw eigen naam - deze moet tussen 3 en 50 tekens lang zijn en mag alleen de waarden a-z, A-Z, 0-9 en '-' bevatten.
    2. Selecteer uw Azure- **abonnement**.
    3. Selecteer dezelfde resource groep uit de vorige stappen.
    4. Selecteer een **locatie** voor het onderwerp Event grid.
    5. Behoud de standaard waarde **Event grid schema** voor het veld **gebeurtenis schema** . 

       ![Pagina onderwerp maken](./media/custom-event-to-function/create-custom-topic.png)
    6. Selecteer **Maken**. 

5. Nadat het aangepaste onderwerp is gemaakt, ziet u een melding dat de implementatie gelukt is. Selecteer **Ga naar resource groep**. 

   ![Zie melding implementatie gelukt](./media/custom-event-to-function/success-notification.png)

6. Selecteer op de pagina **resource groep** het onderwerp Event grid. 

   ![De resource van het event grid-onderwerp selecteren](./media/custom-event-to-function/select-event-grid-topic.png)

7. U ziet de pagina **Event grid onderwerp** voor uw event grid. Laat deze pagina geopend. U kunt deze later in de Quick Start gebruiken. 

    ![Start pagina van Event Grid onderwerp](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Abonneren op aangepast onderwerp

U abonneert u op een Event Grid-onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar de gebeurtenissen naartoe moeten worden gestuurd.

1. Selecteer nu op de pagina **onderwerp Event grid** voor uw aangepaste onderwerp **+ gebeurtenis abonnement** op de werk balk.

   ![Gebeurtenisabonnement toevoegen](./media/custom-event-to-function/new-event-subscription.png)

2. Voer op de pagina **gebeurtenis abonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het gebeurtenis abonnement.
    3. Selecteer **Azure-functie** voor het **type eind punt**. 
    4. Kies **een eind punt selecteren**. 

       ![Waarden opgeven voor gebeurtenisabonnement](./media/custom-event-to-function/provide-subscription-values.png)

    5. Voor het eind punt selecteert u het Azure-abonnement en de resource groep waarin uw functie-app zich bevindt en selecteert u vervolgens de functie-app en de functie die u eerder hebt gemaakt. Selecteer **Confirm Selection** (Selectie bevestigen).

       ![Eindpunt-URL opgeven](./media/custom-event-to-function/provide-endpoint.png)

    6. Selecteer op de pagina **gebeurtenis abonnement maken** de optie **maken**.

## <a name="send-an-event-to-your-topic"></a>Een gebeurtenis verzenden naar het onderwerp

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. Gebruik Azure CLI of PowerShell om een testgebeurtenis te verzenden naar uw aangepaste onderwerp. Meestal worden de gebeurtenisgegevens verzonden via een toepassing of Azure-service.

Het eerste voorbeeld maakt gebruik van Azure CLI. In dit voorbeeld worden de URL en de sleutel voor het aangepaste onderwerp, plus de voorbeeldgegevens van de gebeurtenis opgehaald. Gebruik de naam van het aangepaste onderwerp voor `<topic name>`. Hiermee worden voorbeeldgebeurtenisgegevens gemaakt. Het element `data` van de JSON is de nettolading van de gebeurtenis. Elke juist opgemaakte JSON kan in dit veld worden ingevoerd. U kunt het onderwerpveld ook gebruiken voor geavanceerd routeren en filteren. CURL is een hulpprogramma waarmee HTTP-aanvragen worden verzonden.


### <a name="azure-cli"></a>Azure CLI
1. Selecteer **Cloud shell**In het Azure Portal. Selecteer **bash** in de linkerbovenhoek van het Cloud shell-venster. 

    ![Cloud Shell-bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Voer de volgende opdracht uit om het **eind punt** voor het onderwerp op te halen: nadat u de opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en de naam van de **resource groep** bij voordat u de opdracht uitvoert. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Voer de volgende opdracht uit om de **sleutel** op te halen voor het aangepaste onderwerp: nadat u de opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en de naam van de **resource groep** bij voordat u de opdracht uitvoert. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopieer de volgende instructie met de gebeurtenis definitie en druk op **Enter**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Voer de volgende **krul** opdracht uit om de gebeurtenis te plaatsen:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
In het tweede voorbeeld wordt PowerShell gebruikt om gelijksoortige stappen uit te voeren.

1. Selecteer in de Azure Portal **Cloud shell** (ook naar `https://shell.azure.com/`). Selecteer **Power shell** in de linkerbovenhoek van het venster Cloud shell. Zie de **Cloud shell** venster afbeelding voor beeld in de sectie Azure cli.
2. Stel de volgende variabelen in. Nadat u elke opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en de naam van de **resource groep** bij voordat u de opdracht uitvoert:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Voer de volgende opdrachten uit om het **eind punt** en de **sleutels** voor het onderwerp op te halen:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. De gebeurtenis voor te bereiden. Kopieer de instructies en voer deze uit in het venster Cloud Shell. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Gebruik de cmdlet **invoke-WebRequest** om de gebeurtenis te verzenden. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Controleren in de Event Grid viewer
U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Navigeer naar uw door Event Grid geactiveerde functie en open de logboeken. U ziet een kopie van de gegevens lading van de gebeurtenis in de logboeken. Als u niet zeker weet of u het venster Logboeken eerst opent of op opnieuw verbinding maken klikt, moet u opnieuw proberen om een test gebeurtenis te verzenden.

![Het trigger logboek voor de functie is voltooid](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Resources opschonen
Als u verder wilt werken met deze gebeurtenis, schoon dan de resources die u in dit artikel hebt gemaakt, niet op. Verwijder anders de resources die u in dit artikel hebt gemaakt.

1. Selecteer **resource groepen** in het menu links. Als u deze niet ziet, selecteert u in het menu links **alle services** en selecteert u **resource groepen**. 
2. Selecteer de resource groep om de pagina **resource groep** te starten. 
3. Selecteer **resource groep verwijderen** op de werk balk. 
4. Bevestig de verwijdering door de naam van de resource groep in te voeren en **verwijderen**te selecteren. 

    ![Resourcegroepen](./media/custom-event-to-function/delete-resource-groups.png)

    De andere resource groep die u in de installatie kopie ziet, is gemaakt en gebruikt door het Cloud Shell venster. Als u het venster Cloud Shell later niet wilt gebruiken, moet u het verwijderen. 

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Over Event Grid](overview.md)
- [Blob Storage-gebeurtenissen naar een aangepast eindpunt op het web routeren](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps)
- [Big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)
