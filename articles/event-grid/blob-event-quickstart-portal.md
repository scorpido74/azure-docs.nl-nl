---
title: 'Snelstartgids: Blob Storage-gebeurtenissen verzenden naar web-eind punt-Portal'
description: 'Snelstartgids: gebruik Azure Event Grid en Azure Portal voor het maken van een Blob Storage-account en Abonneer u op de gebeurtenissen. De gebeurtenissen naar een webhook verzenden.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 04/16/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ada451b6bb3578a2903e9bd832b98981d7029d1d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605831"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Snelstartgids: Blob Storage-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal

Azure Event Grid is een gebeurtenisservice voor de cloud. In dit artikel gebruikt u Azure Portal om een Blob-opslagaccount te maken, u aan te melden bij gebeurtenissen voor die blob-opslag en een gebeurtenis te activeren om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van dit artikel stuurt u hier de gebeurtenissen echter naar een web-app die de berichten verzamelt en weergeeft.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Wanneer u klaar bent, ziet u dat de gebeurtenisgegevens naar de web-app zijn verzonden.

![Resultaten weergeven](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Create a storage account

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Een resource maken** als u een Blob-opslag wilt maken. 

1. Selecteer **Opslag** als u de beschikbare opties wilt filteren en selecteer **Storage-account - blob, file, table, queue**.

   ![Opslag selecteren](./media/blob-event-quickstart-portal/create-storage.png)

   Voor het abonneren op gebeurtenissen moet u een algemeen v2-opslagaccount of een Blob-opslagaccount maken.
   
1. Voer de volgende stappen uit op de pagina **opslag account maken** :
    1. Selecteer uw Azure-abonnement. 
    2. Maak voor de **resource groep**een nieuwe resource groep of selecteer een bestaande. 
    3. Voer de naam van uw opslagaccount in. 
    4. Selecteer **controleren + maken**. 

       ![Eerste stappen](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. Controleer de instellingen op de pagina **controleren en maken** en selecteer **maken**. 

        >[!NOTE]
        > Alleen opslag accounts van het type **StorageV2 (algemeen gebruik v2)** en **BlobStorage** ondersteunings gebeurtenis integratie. **Storage (Genral doel v1)** biedt *geen* ondersteuning voor integratie met Event grid.

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich kunt abonneren op gebeurtenissen voor de Blob-opslag, moet u het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Voer op de pagina **aangepaste implementatie** de volgende stappen uit: 
    1. Voor **resource groep**selecteert u de resource groep die u hebt gemaakt bij het maken van het opslag account. Het is eenvoudiger om de opschoon bewerking uit te voeren nadat u klaar bent met de zelf studie door de resource groep te verwijderen.  
    2. Voer voor **site naam**een naam in voor de web-app.
    3. Voer voor de naam van het **hosting plan**een naam in voor het app service plan dat moet worden gebruikt voor het hosten van de web-app.
    4. Schakel het selectie vakje in **als ik ga akkoord met de bovenstaande voor waarden**. 
    5. Selecteer **Aankoop**. 

       ![Implementatie parameters](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. De implementatie kan enkele minuten duren. Selecteer waarschuwingen (klok pictogram) in de portal en selecteer vervolgens **Ga naar resource groep**. 

    ![Waarschuwing: Navigeer naar resource groep](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Selecteer de web-app die u hebt gemaakt op de pagina **resource groep** in de lijst met resources. U ziet ook het App Service plan en het opslag account in deze lijst. 

    ![Website selecteren](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. Selecteer op de pagina **app service** voor uw web-app de URL om naar de website te navigeren. De URL moet de volgende indeling hebben: `https://<your-site-name>.azurewebsites.net`.
    
    ![Naar website navigeren](./media/blob-event-quickstart-portal/web-site.png)

6. Controleer of u de site ziet, maar er zijn nog geen gebeurtenissen naar verzonden.

   ![Nieuwe site weergeven](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Abonneren op de Blob-opslag

U abonneert u op een onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar de gebeurtenissen naartoe moeten worden gestuurd.

1. Navigeer in de portal naar uw Azure Storage-account dat u eerder hebt gemaakt. Selecteer in het menu links **alle resources** en selecteer uw opslag account. 
2. Selecteer op de pagina **opslag account** **gebeurtenissen** in het menu links.
1. Selecteer **Meer opties** en **Webhook**. U verzendt gebeurtenissen naar uw viewer-app met behulp van een webhook voor het eind punt. 

   ![Webhook selecteren](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Voer op de pagina **gebeurtenis abonnement maken** de volgende stappen uit: 
    1. Voer een **naam** in voor het gebeurtenis abonnement.
    2. Selecteer een **webhook** voor het **type eind punt**. 

       ![Type webhook-eind punt selecteren](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. Klik voor **eind punt**op **Selecteer een eind punt**en voer de URL van uw web-app `api/updates` in en voeg deze toe aan de URL van `https://spegridsite.azurewebsites.net/api/updates`de start pagina (bijvoorbeeld:) en selecteer vervolgens **selectie bevestigen**.

   ![Selecteren van het eind punt bevestigen](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Selecteer nu **maken** op de pagina **gebeurtenis abonnement maken** om het gebeurtenis abonnement te maken. 

   ![Logboeken selecteren](./media/blob-event-quickstart-portal/create-subscription.png)

1. Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

   ![Een abonnementgebeurtenis weergeven](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd.

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

U activeert een gebeurtenis voor de Blob-opslag door een bestand te uploaden. Het bestand heeft geen specifieke inhoud nodig. In de artikelen wordt ervan uitgegaan dat u een bestand met de naam testfile.txt hebt, maar het bestand kan elke willekeurige naam hebben.

1. Navigeer in het Azure Portal naar uw Blob Storage-account en selecteer **containers** op de pagina **overzicht** .

   ![Blobs selecteren](./media/blob-event-quickstart-portal/select-blobs.png)

1. Selecteer **+ Container**. Geef een naam op voor de container en gebruik een toegangs niveau en selecteer **maken**. 

   ![Container toevoegen](./media/blob-event-quickstart-portal/add-container.png)

1. Selecteer de nieuwe container.

   ![Container selecteren](./media/blob-event-quickstart-portal/select-container.png)

1. Als u een bestand wilt uploaden, selecteert u **Uploaden**. Blader op de pagina **BLOB uploaden** naar een bestand dat u wilt uploaden om te testen en selecteer vervolgens **uploaden** op die pagina. 

   ![Uploaden selecteren](./media/blob-event-quickstart-portal/upload-file.png)

1. Blader naar het testbestand en upload het.

1. U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Het bericht bevindt zich in de JSON-indeling en bevat een matrix met een of meer gebeurtenissen. In het volgende voor beeld bevat het JSON-bericht een matrix met één gebeurtenis. Bekijk uw web-app en u ziet dat er een gebeurtenis is gemaakt voor het maken van een **BLOB** . 

   ![Gebeurtenis BLOB gemaakt](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt werken met deze gebeurtenis, schoon dan de resources die u in dit artikel hebt gemaakt, niet op. Verwijder anders de resources die u in dit artikel hebt gemaakt.

Selecteer de resourcegroep en klik op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u aangepaste onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Over Event Grid](overview.md)
- [Blob Storage-gebeurtenissen naar een aangepast eindpunt op het web routeren](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps)
- [Big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)
