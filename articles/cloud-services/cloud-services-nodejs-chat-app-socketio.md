---
title: Node.js-toepassing met Socket.io - Azure
description: Meer informatie over het gebruik van socket.io in een node.js-toepassing die wordt gehost op Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360766"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Een Node.js-chattoepassing bouwen met Socket.IO op een Azure Cloud-service

Socket.IO zorgt voor realtime communicatie tussen uw node.js server en clients. Deze tutorial loopt u door het hosten van een socket. IO-gebaseerde chattoepassing op Azure. Zie [socket.io](https://socket.io)voor meer informatie over Socket.IO .

Een screenshot van de voltooide applicatie is hieronder:

![Een browservenster met de service die wordt gehost op Azure][completed-app]  

## <a name="prerequisites"></a>Vereisten
Controleer of de volgende producten en versies zijn geïnstalleerd om het voorbeeld in dit artikel te voltooien:

* [Visual Studio installeren](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* [Node.js](https://nodejs.org/download/) installeren
* [Python-versie 2.7.10](https://www.python.org/) installeren

## <a name="create-a-cloud-service-project"></a>Een Cloud Service Project maken
Met de volgende stappen wordt het cloudserviceproject gemaakt dat de Socket.IO-toepassing host.

1. Zoek in het **startmenu** of **startscherm**naar **Windows PowerShell**. Klik tot slot met de rechtermuisknop op **Windows PowerShell** en selecteer **Uitvoeren als beheerder**.
   
    ![Pictogram Azure PowerShell][powershell-menu]
2. Een map maken met de naam **c:\\knooppunt**. 
   
        PS C:\> md node
3. Mappen wijzigen in de **\\c: node** directory
   
        PS C:\> cd node
4. Voer de volgende opdrachten in om een nieuwe oplossing te maken met de naam **chatapp** en een werknemersrol met de naam **WorkerRole1:**
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    U ziet het volgende antwoord:
   
    ![De uitvoer van de rolcmcmdlets voor nieuwe azureservice en add-azurenodeworker](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Het voorbeeld van de chat downloaden
Voor dit project gebruiken we het chatvoorbeeld van de [Socket.IO GitHub repository.] Voer de volgende stappen uit om het voorbeeld te downloaden en toe te voegen aan het project dat u eerder hebt gemaakt.

1. Maak een lokale kopie van de opslagplaats met behulp van de knop **Kloon.** U ook de **ZIP-knop** gebruiken om het project te downloaden.
   
   ![Een browservenster https://github.com/LearnBoost/socket.io/tree/master/examples/chatdat wordt weergegeven, met het zip-downloadpictogram gemarkeerd](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigeer door de directorystructuur van de lokale opslagplaats totdat u bij de **chatmap met voorbeelden\\** aankomt. Kopieer de inhoud van deze map naar de **C:\\\\chatapp WorkerRole1-map van knooppunt Chatapp\\** die eerder is gemaakt.
   
   ![Explorer, de inhoud weergeven\\van de voorbeelden die uit de chatmap zijn gehaald uit het archief][chat-contents]
   
   De gemarkeerde items in de screenshot hierboven zijn de bestanden gekopieerd uit de **voorbeelden\\chat** directory
3. Verwijder in de map **C:\\\\chatapp WorkerRole1 van knooppunt chatapp,\\** het **server.js-bestand** en wijzigt u de naam van het **app.js-bestand** naar **server.js**. Hiermee wordt het **standaardserver.js-bestand** verwijderd dat eerder is gemaakt door de cmdlet **Add-AzureNodeWorkerRole** en wordt het vervangen door het toepassingsbestand uit het chatvoorbeeld.

### <a name="modify-serverjs-and-install-modules"></a>Server.js en Installatiemodules wijzigen
Voordat we de toepassing in de Azure-emulator testen, moeten we enkele kleine wijzigingen aanbrengen. Voer de volgende stappen uit naar het server.js-bestand:

1. Open het **server.js-bestand** in Visual Studio of een teksteditor.
2. Zoek de sectie **Moduleafhankelijkheden** aan het begin van server.js en wijzig de regel met **sio = vereisen('.. //.. lib/socket.io')** tot **sio = require('socket.io')** zoals hieronder wordt weergegeven:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Open server.js in Notepad of uw favoriete editor om ervoor te zorgen dat de toepassing luistert op de juiste poort en wijzigt u vervolgens de volgende regel door **3000** te vervangen door **process.env.port** zoals hieronder weergegeven:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Nadat u de wijzigingen in **server.js**hebt opgeslagen, gebruikt u de volgende stappen om vereiste modules te installeren en test u de toepassing in de Azure-emulator:

1. Wijzig **met Azure PowerShell**mappen in de map **C:\\\\chatapp WorkerRole1 van knooppunt chatapp\\** en gebruik de volgende opdracht om de modules te installeren die door deze toepassing zijn vereist:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Hiermee worden de modules in het bestand package.json geïnstalleerd. Wanneer de opdracht is voltooid, ziet de uitvoer er als volgt uit:
   
   ![De uitvoer van de opdracht npm-installatie][The-output-of-the-npm-install-command]
2. Aangezien dit voorbeeld oorspronkelijk deel uitmaakte van de Socket.IO GitHub-repository en rechtstreeks naar de Socket.IO bibliotheek per relatief pad verwees, werd Socket.IO niet verwezen in het bestand package.json, dus we moeten het installeren door de volgende opdracht uit te geven:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testen en implementeren
1. Start de emulator door de volgende opdracht uit te geven:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Als u problemen ondervindt bij de lancering van emulator, bijvoorbeeld Start-AzureEmulator: Er is een onverwachte fout opgetreden.  Details: Er is een onverwachte fout opgetreden Het communicatieobject System.ServiceModel.Channels.ServiceChannel kan niet worden gebruikt voor communicatie omdat het in de foutstaande staat staat staat.
   > 
   > Installeer AzureAuthoringTools v 2.7.1 en AzureComputeEmulator v 2.7 opnieuw - zorg ervoor dat de versie overeenkomt.

2. Open een browser **http://127.0.0.1**en navigeer naar .
3. Wanneer het browservenster wordt geopend, voert u een bijnaam in en drukt u op Enter.
   Hiermee u berichten plaatsen als een specifieke bijnaam. Als u de functionaliteit van meerdere gebruikers wilt testen, opent u extra browservensters met dezelfde URL en voert u verschillende bijnamen in.
   
   ![Twee browservensters met chatberichten van Gebruiker1 en Gebruiker2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Na het testen van de toepassing, stop de emulator door het uitgeven van de volgende opdracht:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Als u de toepassing wilt implementeren in Azure, gebruikt u de cmdlet **Publish-AzureServiceProject.** Bijvoorbeeld:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Zorg ervoor dat u een unieke naam gebruikt, anders mislukt het publicatieproces. Nadat de implementatie is voltooid, wordt de browser geopend en naar de geïmplementeerde service genavigeerd.
   > 
   > Als u een fout ontvangt waarin staat dat de opgegeven abonnementsnaam niet bestaat in het geïmporteerde publicatieprofiel, moet u het publicatieprofiel voor uw abonnement downloaden en importeren voordat u wordt geïmplementeerd in Azure. Zie het gedeelte **Toepassing implementeren naar Azure** van Een [Node.js-toepassing implementeren en implementeren in een Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Een browservenster met de service die wordt gehost op Azure][completed-app]
   
   > [!NOTE]
   > Als u een fout ontvangt waarin staat dat de opgegeven abonnementsnaam niet bestaat in het geïmporteerde publicatieprofiel, moet u het publicatieprofiel voor uw abonnement downloaden en importeren voordat u wordt geïmplementeerd in Azure. Zie het gedeelte **Toepassing implementeren naar Azure** van Een [Node.js-toepassing implementeren en implementeren in een Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Uw toepassing wordt nu uitgevoerd op Azure en kan chatberichten doorgeven tussen verschillende clients met behulp van Socket.IO.

> [!NOTE]
> Voor de eenvoud is dit voorbeeld beperkt tot chatten tussen gebruikers die zijn verbonden met dezelfde instantie. Dit betekent dat als de cloudservice twee functie-exemplaren voor werknemers maakt, gebruikers alleen kunnen chatten met anderen die zijn verbonden met dezelfde functie-instantie van de werknemer. Als u de toepassing wilt schalen naar meerdere rolexemplaren, u een technologie zoals Service Bus gebruiken om de Socket.IO-winkelstatus te delen in verschillende instanties. Zie bijvoorbeeld de gebruiksvoorbeelden servicebuswachtrijen en onderwerpen in de [Azure SDK voor Node.js GitHub-opslagplaats](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een basischattoepassing maken die wordt gehost in een Azure Cloud Service. Zie [Een chattoepassing voor node.js maken met Socket.IO op een Azure-website][chatwebsite]voor meer informatie over het hosten van deze toepassing in een Azure-website.

Zie voor meer informatie ook het [Node.js Developer Center](https://docs.microsoft.com/azure/javascript/).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-repository]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png





