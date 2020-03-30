---
title: De OPC Vault-certificaatbeheerservice implementeren - Azure | Microsoft Documenten
description: De OPC Vault-certificaatbeheerservice helemaal opnieuw implementeren.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f577059e1ebf70e3a9dfe9e538a9d3d49d7c8e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71200006"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>De OPC Vault-certificaatbeheerservice bouwen en implementeren

In dit artikel wordt uitgelegd hoe u de OPC Vault-certificaatbeheerservice implementeert in Azure.

> [!NOTE]
> Zie voor meer informatie de GitHub [OPC Vault repository](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Vereisten

### <a name="install-required-software"></a>Vereiste software installeren

Momenteel is de build- en implementatiebewerking beperkt tot Windows.
De voorbeelden zijn allemaal geschreven voor C# .NET Standard, die u nodig hebt om de service en voorbeelden te bouwen voor implementatie.
Alle tools die je nodig hebt voor .NET Standard worden geleverd met de .NET Core tools. Zie [Aan de slag met .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Installeer .NET Core 2.1+][dotnet-install].
2. [Installeer Docker][docker-url] (optioneel, alleen als de lokale Docker-build vereist is).
4. Installeer de [Azure-opdrachtregelgereedschappen voor PowerShell][powershell-install].
5. Meld u aan voor een [Azure-abonnement][azure-free].

### <a name="clone-the-repository"></a>De opslagplaats klonen

Als je dit nog niet hebt gedaan, kloon deze GitHub repository. Open een opdrachtprompt of terminal en voer het volgende uit:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

U de repo ook rechtstreeks klonen in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>De Azure-service op Windows bouwen en implementeren

Een PowerShell-script biedt een eenvoudige manier om de OPC Vault-microservice en de toepassing te implementeren.

1. Open een PowerShell-venster bij de repo-root. 
3. Ga naar de `cd deploy`map implementeren .
3. Kies een `myResourceGroup` naam waarvoor waarschijnlijk geen conflict zal ontstaan met andere geïmplementeerde webpagina's. Zie de sectie "Website naam al in gebruik" later in dit artikel.
5. Start de `.\deploy.ps1` implementatie met voor interactieve installatie of voer een volledige opdrachtregel in:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Als u van plan bent `-development 1` om met deze implementatie te ontwikkelen, voegt u toe om de Swagger-gebruikersinterface in te schakelen en foutopsporingsbuilds te implementeren.
6. Volg de instructies in het script om u aan te melden bij uw abonnement en om aanvullende informatie te verstrekken.
9. Na een succesvolle bewerking voor het bouwen en implementeren ziet u het volgende bericht:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Zie in het geval van problemen de sectie 'Implementatiefouten oplossen' later in het artikel.

8. Open uw favoriete browser en open de toepassingspagina:`https://myResourceGroup.azurewebsites.net`
8. Geef de web-app en de OPC Vault-microservice een paar minuten om op te warmen na de implementatie. De startpagina van de webpagina kan maximaal een minuut lang bij het eerste gebruik blijven hangen totdat u de eerste reacties krijgt.
11. Open de swagger API om een kijkje te nemen in de Swagger API:`https://myResourceGroup-service.azurewebsites.net`
13. Als u een lokale GDS-server `.\myResourceGroup-gds.cmd`met dotnet wilt starten, start u . Met Docker, `.\myResourceGroup-dockergds.cmd`begin .

Het is mogelijk om een build opnieuw te implementeren met precies dezelfde instellingen. Houd er rekening mee dat een dergelijke bewerking alle toepassingsgeheimen vernieuwt en sommige instellingen in de Azure AD-toepassingsregistraties (Azure Directory) kan opnieuw instellen.

Het is ook mogelijk om alleen de binaries van de web-app opnieuw te implementeren. Met de `-onlyBuild 1`parameter worden nieuwe zip-pakketten van de service en de app geïmplementeerd in de webtoepassingen.

Na een succesvolle implementatie u de services gaan gebruiken. Zie [De OPC Vault-certificaatbeheerservice beheren](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>De services uit het abonnement verwijderen

Dit doet u al volgt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar de resourcegroep waarin de service is geïmplementeerd.
3. Selecteer **Resourcegroep verwijderen** en bevestig dit.
4. Na een korte tijd worden alle geïmplementeerde servicecomponenten verwijderd.
5. Ga naar **Azure Active Directory** > **App-registraties**.
6. Er moeten drie registraties worden vermeld voor elke geïmplementeerde resourcegroep. De inschrijvingen hebben `resourcegroup-client`de `resourcegroup-module` `resourcegroup-service`volgende namen: , , . Verwijder elke registratie afzonderlijk.

Nu worden alle geïmplementeerde onderdelen verwijderd.

## <a name="troubleshooting-deployment-failures"></a>Implementatiefouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Gebruik een korte en eenvoudige naam van de resourcegroep. De naam wordt ook gebruikt om bronnen en het voorvoegsel van de service-URL te benoemen. Als zodanig moet het voldoen aan de vereisten voor het benoemen van resources.  

### <a name="website-name-already-in-use"></a>Websitenaam al in gebruik

Het is mogelijk dat de naam van de website al in gebruik is. U moet een andere naam van de resourcegroep gebruiken. De hostnamen die door het https://resourcegroupname.azurewebsites.net implementatiescript worden gebruikt, zijn: en https://resourgroupname-service.azurewebsites.net.
Andere namen van diensten worden gebouwd door de combinatie van korte naam hashes, en zijn waarschijnlijk niet in strijd met andere diensten.

### <a name="azure-ad-registration"></a>Azure AD-registratie 

Het implementatiescript probeert drie Azure AD-toepassingen te registreren in Azure AD. Afhankelijk van uw machtigingen in de geselecteerde Azure AD-tenant kan deze bewerking mislukken. Er zijn twee opties:

- Als u een Azure AD-tenant hebt gekozen uit een lijst met tenants, start u het script opnieuw en kiest u een ander script uit de lijst.
- U ook een private Azure AD-tenant implementeren in een ander abonnement. Start het script opnieuw en selecteer om het te gebruiken.

## <a name="deployment-script-options"></a>Opties voor implementatiescript

Het script neemt de volgende parameters:


```
-resourceGroupName
```

Dit kan de naam zijn van een bestaande of een nieuwe resourcegroep.

```
-subscriptionId
```


Dit is de abonnements-ID waar resources worden geïmplementeerd. Dit is niet verplicht.

```
-subscriptionName
```


U ook de naam van het abonnement gebruiken.

```
-resourceGroupLocation
```


Dit is een locatie van een resourcegroep. Indien opgegeven, probeert deze parameter een nieuwe resourcegroep op deze locatie te maken. Deze parameter is ook optioneel.


```
-tenantId
```


Dit is de Azure AD-tenant die u wilt gebruiken. 

```
-development 0|1
```

Dit is te implementeren voor ontwikkeling. Gebruik foutopsporingsbuild en stel de ASP.NET omgeving in op Ontwikkeling. Maak `.publishsettings` voor import in Visual Studio 2017, zodat de app en de service rechtstreeks kunnen worden geïmplementeerd. Deze parameter is ook optioneel.

```
-onlyBuild 0|1
```

Dit is om alleen de web-apps opnieuw op te bouwen en opnieuw te implementeren en de Docker-containers opnieuw op te bouwen. Deze parameter is ook optioneel.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Vault vanaf nul implementeren, u:

> [!div class="nextstepaction"]
> [OPC-kluis beheren](howto-opc-vault-manage.md)
