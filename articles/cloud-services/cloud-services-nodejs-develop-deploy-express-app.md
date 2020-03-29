---
title: Een Node.js Express-app bouwen en implementeren voor Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Een Express.js-toepassing in Node.js bouwen en implementeren voor Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360749"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Een Node.js-webtoepassing bouwen en implementeren met Express op een Azure Cloud Services

Node.js bevat een minimale set functionaliteit in de kernruntijd.
Ontwikkelaars gebruiken vaak modules van derden om extra functionaliteit te bieden bij het ontwikkelen van een Node.js-toepassing. In deze zelfstudie maakt u een nieuwe toepassing met behulp van de [Express-module,](https://github.com/expressjs/express) die een MVC-framework biedt voor het maken van Node.js-webtoepassingen.

Een screenshot van de voltooide applicatie is hieronder:

![Een webbrowser met Welkom bij Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Een Cloud Service Project maken
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Voer de volgende stappen uit om een nieuw cloudserviceproject met de naam 'expressapp' te maken:

1. Zoek in het **startmenu** of **startscherm**naar **Windows PowerShell**. Klik tot slot met de rechtermuisknop op **Windows PowerShell** en selecteer **Uitvoeren als beheerder**.
   
    ![Pictogram Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Mappen wijzigen in de **c:\\node** directory en voer vervolgens de volgende opdrachten in om een nieuwe oplossing met de naam **expressapp** en een webrol met de naam **WebRole1**te maken:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Standaard gebruikt **Add-AzureNodeWebRole** een oudere versie van Node.js. De bovenstaande instructie **Set-AzureServiceProjectRole** geeft Azure de opdracht om v0.10.21 van Knooppunt te gebruiken.  Let op: de parameters zijn hoofdlettergevoelig.  U controleren of de juiste versie van Node.js is geselecteerd door de eigenschap **engines** in **WebRole1\package.json**te controleren.
    > 
    > 

## <a name="install-express"></a>Express installeren
1. Installeer de Express-generator door de volgende opdracht uit te geven:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    De uitvoer van de npm-opdracht moet er vergelijkbaar uitzien met het resultaat hieronder. 
   
    ![Windows PowerShell toont de uitvoer van de express-opdracht npm installeren.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Mappen wijzigen in de **map WebRole1** en gebruik de expresopdracht om een nieuwe toepassing te genereren:
   
        PS C:\node\expressapp\WebRole1> express
   
    U wordt gevraagd uw eerdere aanvraag te overschrijven. Voer **y** of **ja** in om door te gaan. Express genereert het app.js-bestand en een mapstructuur voor het bouwen van uw toepassing.
   
    ![De uitvoer van de expresopdracht](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Als u extra afhankelijkheden wilt installeren die zijn gedefinieerd in het bestand package.json, voert u de volgende opdracht in:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![De uitvoer van de opdracht npm-installatie](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Gebruik de volgende opdracht om het **opslagformulier bin/www** naar **server.js**te kopiëren. Dit is zodat de cloudservice het ingangspunt voor deze toepassing kan vinden.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Nadat deze opdracht is voltooid, moet u een **server.js-bestand** in de WebRole1-map hebben.
5. Wijzig de **server.js** om een van de tekens '.' uit de volgende regel te verwijderen.
   
       var app = require('../app');
   
   Na het maken van deze wijziging moet de lijn als volgt worden weergegeven.
   
       var app = require('./app');
   
   Deze wijziging is vereist omdat we het bestand (voorheen **bin/www,)** hebben verplaatst naar dezelfde map als het app-bestand dat vereist is. Nadat u deze wijziging hebt gewijzigd, slaat u het **server.js-bestand op.**
6. Gebruik de volgende opdracht om de toepassing uit te voeren in de Azure-emulator:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Een webpagina met welkom om uit te drukken.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>De weergave wijzigen
Wijzig nu de weergave om het bericht 'Welkom bij Express in Azure' weer te geven.

1. Voer de volgende opdracht in om het bestand index.jade te openen:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![De inhoud van het index.jade bestand.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade is de standaard weergave-engine die wordt gebruikt door Express-toepassingen. Voor meer informatie over de Jade view engine, zie [http://jade-lang.com][http://jade-lang.com].
2. Wijzig de laatste regel tekst door te worden toegevoegd **in Azure**.
   
   ![Het bestand index.jade, de laatste regel \#luidt: p Welkom bij {title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Sla het bestand op en sluit het Kladblok af.
4. Vernieuw uw browser en u ziet uw wijzigingen.
   
   ![Een browservenster, de pagina bevat Welkom bij Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Gebruik na het testen van de toepassing de **cmdlet Stop-AzureEmulator** om de emulator te stoppen.

## <a name="publishing-the-application-to-azure"></a>De toepassing publiceren naar Azure
Gebruik in het Azure PowerShell-venster de cmdlet **Publish-AzureServiceProject** om de toepassing te implementeren op een cloudservice

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Zodra de implementatiebewerking is voltooid, wordt de webpagina geopend en weergegeven.

![Een webbrowser die de Express-pagina weergeeft. De URL geeft aan dat deze nu wordt gehost op Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Volgende stappen
Zie het [Node.js-ontwikkelaarscentrum](https://docs.microsoft.com/azure/javascript/) voor meer informatie.

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





