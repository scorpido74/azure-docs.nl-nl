---
title: De OPC kluis Certificate Management-service implementeren-Azure | Microsoft Docs
description: Hoe u de OPC kluis Certificate Management-service helemaal volledig implementeert.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012987"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>De OPC kluis Certificate Management-service bouwen en implementeren

In dit artikel wordt uitgelegd hoe u de OPC kluis Certificate Management-service in azure implementeert.

> [!NOTE]
> Zie de GitHub [OPC kluis-opslag plaats](https://github.com/Azure/azure-iiot-opc-vault-service)voor meer informatie over de implementatie details en instructies.

## <a name="prerequisites"></a>Vereisten

### <a name="install-required-software"></a>Vereiste software installeren

De bewerking voor het maken en implementeren van het pakket is momenteel beperkt tot Windows.
De voor beelden zijn allemaal geschreven C# voor .NET Standard, die nodig zijn voor het bouwen van de service en voor beelden voor implementatie.
Alle hulpprogram ma's die u nodig hebt voor .NET Standard, worden geleverd met de .net core-hulpprogram ma's. [Hier](https://docs.microsoft.com/dotnet/articles/core/getting-started) kunt u zien wat u nodig hebt.

1. [Installeer .net Core 2.1 +][dotnet-install].
2. [Docker installeren][docker-url] (optioneel, alleen als de lokale docker-build is vereist).
4. Installeer de [Azure-opdracht regel Programma's voor Power shell][powershell-install].
5. Meld u aan voor een [Azure-abonnement][azure-free].

### <a name="clone-the-repository"></a>De opslagplaats klonen

Als u dit nog niet hebt gedaan, moet u deze GitHub-opslag plaats klonen.  Open een opdracht prompt of Terminal en voer het volgende uit:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

of kloon de opslag plaats rechtstreeks in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>De Azure-service in Windows bouwen en implementeren

Een Power shell-script biedt een eenvoudige manier om de OPC-kluis micro service en de toepassing te implementeren.<br>

1. Open een Power shell-venster in de hoofdmap opslag plaats. 
3. Ga naar de map Deploy`cd deploy`
3. Kies een naam `myResourceGroup` die waarschijnlijk geen conflict veroorzaakt met andere geïmplementeerde webpagina's. [Hieronder](#website-name-already-in-use) ziet u hoe de namen van webpagina's worden gekozen op basis van de naam van de resource groep.
5. De implementatie starten met `.\deploy.ps1` voor interactieve installatie<br>
of voer een volledige opdracht regel in:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Als u van plan bent om met deze implementatie te `-development 1` ontwikkelen, voegt u toe om de Swagger-gebruikers interface in te scha kelen en om fout opsporing te implementeren.
6. Volg de instructies in het script om u aan te melden bij uw abonnement en om aanvullende informatie te geven.
9. Nadat de bewerking is voltooid, wordt het volgende bericht weer gegeven:
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

Volg de [onderstaande](#troubleshooting-deployment-failures)stappen voor het geval u problemen ondervindt.

8. Open uw favoriete browser en open de toepassings pagina:`https://myResourceGroup.azurewebsites.net`
8. Geef de web-app en de OPC-kluis micro service een paar minuten om na de implementatie te warmten. De start pagina van het web kan op Maxi maal een minuut vastlopen totdat u de eerste reacties krijgt.
11. Ga als volgt te rond kijken naar de Swagger API:`https://myResourceGroup-service.azurewebsites.net`
13. Voor het starten van een lokale GDS-server `.\myResourceGroup-gds.cmd` met DotNet start of met `.\myResourceGroup-dockergds.cmd`docker start.

Als klikken is het mogelijk om een build opnieuw te implementeren met precies dezelfde instellingen. Houd er rekening mee dat een dergelijke bewerking alle toepassings geheimen vernieuwt en mogelijk sommige instellingen in de toepassings registraties van de Azure Active Directory (Azure AD) opnieuw kan instellen.

Het is ook mogelijk alleen de binaire bestanden van de web-app te implementeren. Met de para `-onlyBuild 1` meter New ZIP-pakketten van de service en de app worden geïmplementeerd in de webtoepassingen.

Nadat de implementatie is voltooid, kunt u beginnen met het gebruik van de services: [De OPC-kluis certificaat beheer service beheren](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>De services verwijderen uit het abonnement

1. Meld u aan bij de Azure Portal `https://portal.azure.com`:.
2. Ga naar de resource groep waarin de service is geïmplementeerd.
3. Selecteer `Delete resource group` en bevestig.
4. Na een korte tijd dat alle geïmplementeerde service onderdelen zijn verwijderd.
5. Ga nu naar `Azure Active Directory/App registrations`.
6. Er moeten drie registraties worden vermeld voor elke geïmplementeerde resource groep met de volgende namen `resourcegroup-client`: `resourcegroup-module`, `resourcegroup-service`,.
Elke registratie moet afzonderlijk worden verwijderd.
7. Nu worden alle geïmplementeerde onderdelen verwijderd.

## <a name="troubleshooting-deployment-failures"></a>Implementatie fouten oplossen

### <a name="resource-group-name"></a>Resourcegroepnaam

Zorg ervoor dat u een korte en eenvoudige naam voor de resource groep gebruikt.  De naam wordt ook gebruikt voor het benoemen van resources en het voor voegsel van de service-URL, maar moet voldoen aan de vereisten voor resource naamgeving.  

### <a name="website-name-already-in-use"></a>De naam van de website wordt al gebruikt

Het is mogelijk dat de naam van de website al in gebruik is.  Als u deze fout uitvoert, moet u een andere naam voor de resource groep gebruiken. De hostnamen die worden gebruikt door het implementatie script zijn https://resourcegroupname.azurewebsites.net : https://resourgroupname-service.azurewebsites.net en.
Andere namen van services worden gebouwd op basis van de combi natie van korte naam-hashes en zijn waarschijnlijk niet strijdig met andere services.

### <a name="azure-active-directory-azure-ad-registration"></a>Registratie van Azure Active Directory (Azure AD) 

Het implementatie script probeert drie Azure AD-toepassingen in Azure Active Directory te registreren.  
Afhankelijk van uw machtigingen in de geselecteerde Azure AD-Tenant kan deze bewerking mislukken.   Er zijn twee opties:

1. Als u een Azure AD-Tenant uit een lijst met tenants hebt gekozen, start u het script opnieuw en kiest u een ander in de lijst.
2. U kunt ook een persoonlijke Azure AD-Tenant in een ander abonnement implementeren, het script opnieuw starten en selecteren om het te gebruiken.

## <a name="deployment-script-options"></a>Opties voor implementatie script

Het script heeft de volgende para meters:


```
-resourceGroupName
```

Dit kan de naam van een bestaande of een nieuwe resource groep zijn.

```
-subscriptionId
```


Optioneel, het abonnement-ID waar resources worden geïmplementeerd.

```
-subscriptionName
```


Of u kunt ook de naam van het abonnement.

```
-resourceGroupLocation
```


Optioneel, een locatie van een resource groep. Als deze is opgegeven, wordt geprobeerd een nieuwe resource groep te maken op deze locatie.


```
-tenantId
```


Te gebruiken Azure AD-Tenant. 

```
-development 0|1
```

Optioneel, om te implementeren voor ontwikkeling. Gebruik debug build en stel de ASP.Net-omgeving in op Development. Maak '. publishsettings ' voor importeren in Visual Studio 2017 zodat de app en de service rechtstreeks kunnen worden geïmplementeerd.

```
-onlyBuild 0|1
```

Optioneel, om alleen de web-apps opnieuw te bouwen en opnieuw te implementeren en om de docker-containers opnieuw samen te stellen.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een nieuwe OPC-kluis kunt implementeren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [OPC-kluis beheren](howto-opc-vault-manage.md)
