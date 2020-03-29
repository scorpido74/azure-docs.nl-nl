---
title: Aan de slag met de Azure CDN SDK voor Node.js | Microsoft Documenten
description: Meer informatie over het schrijven van Node.js-toepassingen om Azure CDN te beheren.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594132"
---
# <a name="get-started-with-azure-cdn-development"></a>Aan de slag met Azure CDN-ontwikkeling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

U de [Azure CDN SDK voor Node.js](https://www.npmjs.com/package/azure-arm-cdn) gebruiken om het maken en beheren van CDN-profielen en eindpunten te automatiseren.  Deze zelfstudie loopt door de creatie van een eenvoudige Node.js-consoletoepassing die een aantal van de beschikbare bewerkingen demonstreert.  Deze zelfstudie is niet bedoeld om alle aspecten van de Azure CDN SDK voor Node.js in detail te beschrijven.

Om deze zelfstudie te voltooien, moet u [Node.js](https://www.nodejs.org) **4.x.x** of hoger al hebben geïnstalleerd en geconfigureerd.  U elke teksteditor gebruiken die u wilt maken met uw Node.js-toepassing.  Om deze tutorial te schrijven, gebruikte ik [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> Het [voltooide project van deze zelfstudie](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) is beschikbaar om te downloaden op MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Uw project maken en NPM-afhankelijkheden toevoegen
Nu we een brongroep hebben gemaakt voor onze CDN-profielen en onze Azure AD-toepassingstoestemming hebben gegeven om CDN-profielen en eindpunten binnen die groep te beheren, kunnen we beginnen met het maken van onze toepassing.

Maak een map om uw toepassing op te slaan.  Stel vanaf een console met de hulpprogramma's Node.js in uw huidige pad uw huidige locatie in op deze nieuwe map en initialiseer uw project door het volgende uit te voeren:

    npm init

U krijgt dan een reeks vragen voorgeschoteld om uw project te initialiseren.  Voor **het ingangspunt**maakt deze zelfstudie gebruik van *app.js*.  U mijn andere keuzes in het volgende voorbeeld zien.

![NPM-init-uitvoer](./media/cdn-app-dev-node/cdn-npm-init.png)

Ons project is nu geïnitialiseerd met een *packages.json* bestand.  Ons project gaat een aantal Azure-bibliotheken gebruiken die zijn opgenomen in NPM-pakketten.  We gebruiken de Azure Client Runtime voor Node.js (ms-rest-azure) en de Azure CDN-clientbibliotheek voor Node.js (azure-arm-cd).  Laten we deze toevoegen aan het project als afhankelijkheden.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Nadat de pakketten klaar zijn met installeren, moet het bestand *package.json* er hetzelfde uitzien als dit voorbeeld (versienummers kunnen verschillen):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Maak ten slotte met uw teksteditor een leeg tekstbestand en sla het op in de hoofdmap van onze projectmap als *app.js*.  We zijn nu klaar om code te schrijven.

## <a name="requires-constants-authentication-and-structure"></a>Vereist, constanten, authenticatie en structuur
Met *app.js* open in onze editor, laten we de basisstructuur van ons programma geschreven.

1. Voeg de "requires" voor onze NPM pakketten aan de bovenkant toe met het volgende:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. We moeten een aantal constanten definiëren die onze methoden zullen gebruiken.  Voeg het volgende toe.  Zorg ervoor dat u de tijdelijke ** &lt;aanduidingen,&gt;** inclusief de hoekbeugels, vervangt door uw eigen waarden indien nodig.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Vervolgens zullen we de CDN-beheerclient instantiëren en onze referenties geven.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Als u individuele gebruikersverificatie gebruikt, zien deze twee regels er iets anders uit.
   
   > [!IMPORTANT]
   > Gebruik dit codevoorbeeld alleen als u kiest voor individuele gebruikersverificatie in plaats van een serviceprincipal.  Wees voorzichtig om uw individuele gebruikersreferenties te bewaken en ze geheim te houden.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Zorg ervoor dat u de items in ** &lt;hoekbeugels&gt; ** vervangt door de juiste informatie.  Gebruik `<redirect URI>`hiervoor de omleidings-URI die u hebt ingevoerd toen u de toepassing in Azure AD registreerde.
4. Onze Node.js console applicatie gaat een aantal command-line parameters nemen.  Laten we valideren dat er ten minste één parameter is doorgegeven.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Dat brengt ons bij het grootste deel van ons programma, waar we vertakken naar andere functies op basis van welke parameters werden doorgegeven.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Op verschillende plaatsen in ons programma moeten we ervoor zorgen dat het juiste aantal parameters zijn doorgegeven en wat hulp weergeven als ze er niet correct uitzien.  Laten we functies maken om dat te doen.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Ten slotte zijn de functies die we op de CDN-beheerclient gebruiken asynchroon, dus ze hebben een methode nodig om terug te bellen wanneer ze klaar zijn.  Laten we er een maken die de uitvoer van de CDN-beheerclient (indien aanwezig) kan weergeven en het programma op een elegante manier kan afsluiten.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nu de basisstructuur van ons programma is geschreven, moeten we de functies die op basis van onze parameters worden genoemd, creëren.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profielen en eindpunten weergeven
Laten we beginnen met code om onze bestaande profielen en eindpunten weer te geven.  Mijn codeopmerkingen bieden de verwachte syntaxis, zodat we weten waar elke parameter naartoe gaat.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profielen en eindpunten maken
Vervolgens schrijven we de functies om profielen en eindpunten te maken.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Een eindpunt zuiveren
Ervan uitgaande dat het eindpunt is gemaakt, is een veelvoorkomende taak die we in ons programma zouden willen uitvoeren, het zuiveren van inhoud in ons eindpunt.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profielen en eindpunten verwijderen
De laatste functie die we zullen opnemen verwijdert eindpunten en profielen.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Het programma uitvoeren
We kunnen nu ons Node.js-programma uitvoeren met behulp van onze favoriete debugger of op de console.

> [!TIP]
> Als u Visual Studio Code als foutopsporing gebruikt, moet u uw omgeving zo instellen dat de opdrachtregelparameters worden doorgegeven.  Visual Studio Code doet dit in het **launch.json-bestand.**  Zoek naar een eigenschap met de naam **args** en voeg een array met `"args": ["list", "profiles"]`tekenreekswaarden toe voor uw parameters, zodat deze op deze lijkt: .
> 
> 

Laten we beginnen met een opsomming van onze profielen.

![Lijstprofielen](./media/cdn-app-dev-node/cdn-list-profiles.png)

We hebben een lege array terug.  Aangezien we geen profielen in onze resourcegroep hebben, wordt dat verwacht.  Laten we nu een profiel aanmaken.

![Profiel maken](./media/cdn-app-dev-node/cdn-create-profile.png)

Laten we nu een eindpunt toevoegen.

![Eindpunt maken](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Tot slot, laten we ons profiel verwijderen.

![Profiel verwijderen](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Volgende stappen
Download het voorbeeld om het voltooide project van deze walkthrough te [bekijken.](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)

Als u de verwijzing voor de Azure CDN SDK voor Node.js wilt weergeven, raadpleegt u de [verwijzing](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Als u aanvullende documentatie wilt vinden op de Azure SDK voor Node.js, raadpleegt u de [volledige referentie](https://azure.github.io/azure-sdk-for-node/).

Beheer uw CDN-resources met [PowerShell.](cdn-manage-powershell.md)

