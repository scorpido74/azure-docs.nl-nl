---
title: Een node. js Express-app bouwen en implementeren in azure Cloud Services
titleSuffix: Azure Cloud Services
description: Een Express. js-toepassing in node. js maken en implementeren in azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360749"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Een node. js-webtoepassing bouwen en implementeren met behulp van Express op een Azure-Cloud Services

Node. js bevat een minimale set functionaliteit in de kern runtime.
Ontwikkel aars gebruiken vaak modules van derden om extra functionaliteit te bieden bij het ontwikkelen van een node. js-toepassing. In deze zelf studie maakt u een nieuwe toepassing met behulp van de [Express](https://github.com/expressjs/express) -module, die een MVC-Framework biedt voor het maken van node. js-webtoepassingen.

Hieronder ziet u een scherm opname van de voltooide toepassing:

![Een webbrowser die welkom in azure weergeeft](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Een Cloud service project maken
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Voer de volgende stappen uit om een nieuw Cloud service project met de naam ' expressapp ' te maken:

1. Zoek in het **menu Start** of in het **Start scherm**naar **Windows Power shell**. Klik ten slotte met de rechter muisknop op **Windows Power shell** en selecteer **als administrator uitvoeren**.
   
    ![Azure PowerShell pictogram](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Wijzig de mappen in de map **c:\\knoop punt** en voer de volgende opdrachten in om een nieuwe oplossing te maken met de naam **expressapp** en een webrol met de naam **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Standaard gebruikt **add-AzureNodeWebRole** een oudere versie van node. js. De instructie **set-AzureServiceProjectRole** hierboven geeft Azure opdracht om v 0.10.21 van het knoop punt te gebruiken.  Houd er rekening mee dat de para meters hoofdletter gevoelig zijn.  U kunt controleren of de juiste versie van node. js is geselecteerd door de eigenschap **engines** in **WebRole1\package.json**te controleren.
    > 
    > 

## <a name="install-express"></a>Express installeren
1. Installeer de Express-Generator door de volgende opdracht uit te geven:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    De uitvoer van de NPM-opdracht moet er ongeveer als volgt uitzien. 
   
    ![Windows Power shell die de uitvoer van de NPM-opdracht install Express weergeeft.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Wijzig de mappen in de map **WebRole1** en gebruik de snelle opdracht om een nieuwe toepassing te genereren:
   
        PS C:\node\expressapp\WebRole1> express
   
    U wordt gevraagd om uw eerdere toepassing te overschrijven. Voer **y** of **Ja** in om door te gaan. Met Express worden het bestand app. js en een mapstructuur gegenereerd voor het bouwen van uw toepassing.
   
    ![De uitvoer van de Express-opdracht](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Als u aanvullende afhankelijkheden wilt installeren die in het bestand Package. json zijn gedefinieerd, voert u de volgende opdracht in:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![De uitvoer van de NPM-installatie opdracht](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Gebruik de volgende opdracht om het **bin/www-** bestand te kopiëren naar **server. js**. Dit is zo dat de Cloud service het toegangs punt voor deze toepassing kan vinden.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Wanneer deze opdracht is voltooid, moet u een **server. js** -bestand in de WebRole1-map hebben.
5. Wijzig de **server. js** om een van de tekens '. ' uit de volgende regel te verwijderen.
   
       var app = require('../app');
   
   Nadat u deze wijziging hebt aangebracht, moet de regel er als volgt uitzien.
   
       var app = require('./app');
   
   Deze wijziging is vereist omdat het bestand (voorheen **bin/www**) is verplaatst naar dezelfde map als het vereiste app-bestand. Nadat u deze wijziging hebt aangebracht, slaat u het bestand **server. js** op.
6. Gebruik de volgende opdracht om de toepassing uit te voeren in de Azure-emulator:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Een webpagina met Welkom bij Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>De weer gave wijzigen
Wijzig nu de weer gave om het bericht ' Welkom bij Express in azure ' weer te geven.

1. Voer de volgende opdracht in om het bestand index. Jade te openen:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![De inhoud van het bestand index. Jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade is de standaard weergave-engine die door Express-toepassingen wordt gebruikt. Zie [http://jade-lang.com][http://jade-lang.com]voor meer informatie over de Jade-weergave-engine.
2. Wijzig de laatste tekst regel door toe te voegen **in azure**.
   
   ![Het bestand index. Jade, de laatste regel leest: p Welkom bij \#{title} in azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Sla het bestand op en sluit Klad blok.
4. Vernieuw uw browser en uw wijzigingen worden weer geven.
   
   ![Een browser venster bevat de pagina Welkom bij Express in azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Nadat u de toepassing hebt getest, gebruikt u de cmdlet **Stop-AzureEmulator** om de emulator te stoppen.

## <a name="publishing-the-application-to-azure"></a>De toepassing publiceren in azure
Gebruik in het Azure PowerShell-venster de cmdlet **Publish-AzureServiceProject** om de toepassing te implementeren in een Cloud service

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Zodra de implementatie bewerking is voltooid, wordt uw browser geopend en wordt de webpagina weer gegeven.

![Een webbrowser waarin de Express-pagina wordt weer gegeven. De URL geeft aan dat deze nu wordt gehost op Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Volgende stappen
Zie het [Node.js Developer Center](https://docs.microsoft.com/azure/javascript/) voor meer informatie.

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





