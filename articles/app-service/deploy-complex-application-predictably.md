---
title: Apps voorspelbaar implementeren met ARM
description: Meer informatie over het implementeren van meerdere Azure App Service-apps als één eenheid en op een voorspelbare manier met Azure Resource Management-sjablonen en PowerShell-scripting.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75967383"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Microservices voorspelbaar inrichten en implementeren in Azure
In deze zelfstudie ziet u hoe u een toepassing die bestaat uit [microservices](https://en.wikipedia.org/wiki/Microservices) in [Azure App Service](https://azure.microsoft.com/services/app-service/) in één eenheid en op een voorspelbare manier implementeren en implementeren met JSON-brongroepsjablonen en PowerShell-scripting. 

Bij het inrichten en implementeren van grootschalige applicaties die bestaan uit sterk ontkoppelde microservices, zijn herhaalbaarheid en voorspelbaarheid cruciaal voor succes. [Met Azure App Service](https://azure.microsoft.com/services/app-service/) u microservices maken die web-apps, mobiele back-ends en API-apps bevatten. [Met Azure Resource Manager](../azure-resource-manager/management/overview.md) u alle microservices als eenheid beheren, samen met resourceafhankelijkheden zoals database- en bronbeheerinstellingen. Nu u een dergelijke toepassing ook implementeren met JSON-sjablonen en eenvoudige PowerShell-scripting. 

## <a name="what-you-will-do"></a>Wat u gaat doen
In de zelfstudie implementeert u een toepassing die het volledige nieuwe programma bevat:

* Twee App Service-apps (d.w.z. twee microservices)
* Een backend SQL-database
* App-instellingen, verbindingstekenreeksen en bronbeheer
* Toepassingsinzichten, waarschuwingen, instellingen voor automatisch schalen

## <a name="tools-you-will-use"></a>Tools die u zult gebruiken
In deze zelfstudie gebruikt u de volgende hulpmiddelen. Aangezien het niet uitgebreide discussie over tools, ik ga vasthouden aan de end-to-end scenario en geef je een korte intro voor elk, en waar u meer informatie over te vinden. 

### <a name="azure-resource-manager-templates-json"></a>Json (Azure Resource Manager-sjablonen)
Elke keer dat u een app maakt in Azure App Service, gebruikt Azure Resource Manager bijvoorbeeld een JSON-sjabloon om de hele resourcegroep met de componentresources te maken. Een complexe sjabloon uit azure [marketplace](/azure/marketplace) kan de database, opslagaccounts, het App Service-abonnement, de app zelf, waarschuwingsregels, app-instellingen, instellingen voor automatisch schalen en meer bevatten, en al deze sjablonen zijn voor u beschikbaar via PowerShell. Zie Sjablonen voor Azure Resource Manager voor meer informatie over de Azure Resource [Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 voor Visual Studio
De nieuwste SDK bevat verbeteringen in de resourcemanager-sjabloonondersteuning in de JSON-editor. U dit gebruiken om snel een resourcegroepsjabloon helemaal opnieuw te maken of een bestaande JSON-sjabloon (zoals een gedownloade galeriesjabloon) te openen voor wijziging, het parametersbestand te vullen en de brongroep zelfs rechtstreeks vanuit een Azure Resource-bron te implementeren Groepsoplossing.

Zie [Azure SDK 2.6 voor Visual Studio voor](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/)meer informatie.

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 of hoger
Vanaf versie 0.8.0 bevat de Azure PowerShell-installatie naast de Azure-module de Azure Resource Manager-module. Met deze nieuwe module u de implementatie van resourcegroepen scripten.

Zie [Azure PowerShell gebruiken met Azure Resource Manager](../powershell-azure-resource-manager.md) voor meer informatie

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Met [deze preview-tool](https://resources.azure.com) u de JSON-definities van alle resourcegroepen in uw abonnement en de afzonderlijke bronnen verkennen. In het hulpprogramma u de JSON-definities van een resource bewerken, een hele hiërarchie van resources verwijderen en nieuwe resources maken.  De informatie die direct beschikbaar is in deze tool is zeer nuttig voor het ontwerpen van sjablonen, omdat het u laat zien welke eigenschappen u moet instellen voor een bepaald type resource, de juiste waarden, enz. U zelfs uw resourcegroep maken in de [Azure Portal](https://portal.azure.com/)en vervolgens de JSON-definities inspecteren in het explorer-hulpprogramma om u te helpen de brongroep te mplatiseren.

### <a name="deploy-to-azure-button"></a>De knop Implementeren in Azure
Als u GitHub gebruikt voor bronbeheer, u een [knop Implementeren naar Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) in uw README plaatsen. MD, waarmee een turn-key implementatie-gebruikersinterface naar Azure kan worden geïmplementeerd. Hoewel u dit voor elke eenvoudige app doen, u dit uitbreiden om het implementeren van een hele brongroep in te schakelen door een azuredeploy.json-bestand in de bronmapte plaatsen. Dit JSON-bestand, dat de sjabloon resourcegroep bevat, wordt gebruikt door de knop Implementeren naar Azure om de brongroep te maken. Zie bijvoorbeeld het [Voorbeeld van ToDoApp,](https://github.com/azure-appservice-samples/ToDoApp) dat u in deze zelfstudie gebruikt.

## <a name="get-the-sample-resource-group-template"></a>De sjabloon voorbeeldbrongroep downloaden
Dus laten we er nu meteen aan gaan.

1. Navigeer naar het voorbeeld [van ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service.
2. Klik in readme.md op **Implementeren naar Azure**.
3. U wordt naar de [deploy-to-azure-site](https://deploy.azure.com) gebracht en gevraagd om implementatieparameters in te voeren. Merk op dat de meeste velden zijn gevuld met de naam van de opslagplaats en een aantal willekeurige tekenreeksen voor u. U alle velden wijzigen als u wilt, maar de enige dingen die u moet invoeren zijn de SQL Server-beheerlogin en het wachtwoord en vervolgens op **Volgende**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klik vervolgens op **Implementeren** om het implementatieproces te starten. Zodra het proces is voltooid, klikt u op de http://todoappkoppeling *XXXX*.azurewebsites.net om door de geïmplementeerde toepassing te bladeren. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   De Gebruikersinterface zou een beetje traag zijn wanneer u voor het eerst naar het bladert omdat de apps net opstarten, maar overtuig uzelf dat het een volledig functionele toepassing is.
5. Klik terug op de pagina Implementeren op de koppeling **Beheren** om de nieuwe toepassing in de Azure Portal weer te geven.
6. Klik in de vervolgkeuzelijst **Essentials** op de koppeling resourcegroep. Houd er ook rekening mee dat de app al is verbonden met de GitHub-opslagplaats onder **Extern project**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Houd er in het beheer van de brongroep rekening mee dat er al twee apps en één SQL-database in de brongroep zijn.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Alles wat u zojuist in een paar minuten hebt gezien, is een volledig geïmplementeerde tweemicroservicetoepassing, met alle componenten, afhankelijkheden, instellingen, database en continue publicatie, ingesteld door een geautomatiseerde orchestration in Azure Resource Manager. Dit alles werd gedaan door twee dingen:

* De knop Implementeren naar Azure
* azuredeploy.json in de repo-root

U dezelfde toepassing tientallen, honderden of duizenden keren implementeren en elke keer exact dezelfde configuratie hebben. De herhaalbaarheid en voorspelbaarheid van deze aanpak stelt u in staat om met gemak en vertrouwen grootschalige applicaties te implementeren.

## <a name="examine-or-edit-azuredeployjson"></a>AzureDEPLOY onderzoeken (of bewerken). Json
Laten we nu eens kijken hoe de GitHub repository is opgezet. U gebruikt de JSON-editor in de Azure .NET SDK, dus als u [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/)nog niet hebt geïnstalleerd, moet u dit nu doen.

1. Kloon de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) repository met je favoriete git tool. In de screenshot hieronder doe ik dit in de Team Explorer in Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Open azuredeploy.json in Visual Studio in de hoofdmap van de repository. Als u het deelvenster JSON-overzicht niet ziet, moet u Azure .NET SDK installeren.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Ik ga niet elk detail van de JSON-indeling beschrijven, maar de sectie [Meer resources](#resources) bevat koppelingen voor het leren van de sjabloontaal voor resourcegroepen. Hier laat ik je de interessante functies zien die je aan de slag kunnen helpen bij het maken van je eigen aangepaste sjabloon voor app-implementatie.

### <a name="parameters"></a>Parameters
Bekijk de sectie Parameters om te zien dat de meeste van deze parameters zijn wat de knop **Implementeren naar Azure** u vraagt om invoer. De site achter de knop **Implementeren naar Azure** vult de invoer-gebruikersinterface met behulp van de parameters die zijn gedefinieerd in azuredeploy.json. Deze parameters worden gebruikt in de resourcedefinities, zoals resourcenamen, eigenschapswaarden, enz.

### <a name="resources"></a>Resources
In het knooppunt resources u zien dat er 4 bronnen op het hoogste niveau zijn gedefinieerd, waaronder een SQL Server-exemplaar, een App Service-abonnement en twee apps. 

#### <a name="app-service-plan"></a>App Service-plan
Laten we beginnen met een eenvoudige root-level resource in de JSON. Klik in het JSON-overzicht op het App-serviceplan met de naam **[hostingPlanName]** om de bijbehorende JSON-code te markeren. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Houd er `type` rekening mee dat het element de tekenreeks voor een App Service-abonnement opgeeft (het werd lange, lange tijd geleden een serverfarm genoemd) en dat andere elementen en eigenschappen worden ingevuld met behulp van de parameters die zijn gedefinieerd in het JSON-bestand en dat deze bron geen geneste bronnen bevat.

> [!NOTE]
> Houd er ook `apiVersion` rekening mee dat de waarde van Azure vertelt met welke versie van de REST-API `{}`de JSON-brondefinitie moet worden gebruikt en dat deze van invloed kan zijn op de manier waarop de bron moet worden opgemaakt binnen de . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klik vervolgens op de SQL Server-bron met de naam **SQLServer** in de JSON-contour.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Let op het volgende over de gemarkeerde JSON-code:

* Het gebruik van parameters zorgt ervoor dat de gemaakte resources worden benoemd en geconfigureerd op een manier die ze consistent maakt met elkaar.
* De SQLServer-bron heeft twee geneste resources, `type`elk heeft een andere waarde voor .
* De geneste `“resources”: […]`resources binnen , waar de database en `dependsOn` de firewallregels zijn gedefinieerd, hebben een element dat de resource-id van de SQLServer-bron op rootniveau opgeeft. Dit vertelt Azure Resource Manager, "voordat u deze bron maakt, dat er al andere bronnen moeten bestaan; en als die andere resource is gedefinieerd in de sjabloon, maak dan die ene eerst".
  
  > [!NOTE]
  > Zie [Sjabloonfuncties azure resource manager](../azure-resource-manager/templates/template-functions-resource.md#resourceid)voor meer informatie over het gebruik van de `resourceId()` functie .
  > 
  > 
* Het effect `dependsOn` van het element is dat Azure Resource Manager kan weten welke resources parallel kunnen worden gemaakt en welke resources achtereenvolgens moeten worden gemaakt. 

#### <a name="app-service-app"></a>App Service-app
Nu, laten we verder gaan met de werkelijke apps zelf, die ingewikkelder zijn. Klik op de app [variabelen('apiSiteName')] in de JSON-overzicht om de JSON-code te markeren. U zult merken dat de dingen worden steeds veel interessanter. Voor dit doel, zal ik praten over de functies een voor een:

##### <a name="root-resource"></a>Hoofdbron
De app is afhankelijk van twee verschillende bronnen. Dit betekent dat Azure Resource Manager de app pas maakt nadat zowel het App Service-abonnement als het SQL Server-exemplaar zijn gemaakt.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Instellingen voor apps
De app-instellingen worden ook gedefinieerd als een geneste bron.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

In `properties` het `config/appsettings`element voor , heb je `"<name>" : "<value>"`twee app-instellingen in de indeling .

* `PROJECT`is een [KUDU-instelling](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) die azure-implementatie vertelt welk project moet worden gebruikt in een Visual Studio-oplossing voor meerdere projecten. Ik zal u later laten zien hoe broncontrole is geconfigureerd, maar aangezien de ToDoApp-code zich in een Visual Studio-oplossing voor meerdere projecten bevindt, hebben we deze instelling nodig.
* `clientUrl`is gewoon een app-instelling die de toepassingscode gebruikt.

##### <a name="connection-strings"></a>Verbindingsreeksen
De verbindingstekenreeksen worden ook gedefinieerd als een geneste bron.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

In `properties` het `config/connectionstrings`element voor wordt elke verbindingstekenreeks ook gedefinieerd als een naam:waardepaar, met de specifieke notatie van `"<name>" : {"value": "…", "type": "…"}`. Voor `type` het element zijn `MySql` `SQLServer`mogelijke `SQLAzure`waarden `Custom`, , en .

> [!TIP]
> Voer voor een definitieve lijst met de typen verbindingstekenreeksen \[de volgende opdracht uit in Azure PowerShell: Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Broncodebeheer
De instellingen voor bronbeheer worden ook gedefinieerd als een geneste bron. Azure Resource Manager gebruikt deze bron om continue `IsManualIntegration` publicatie te configureren (zie voorbehoud later) en ook om de implementatie van toepassingscode automatisch te starten tijdens de verwerking van het JSON-bestand.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`en `branch` moet vrij intuïtief en moet wijzen op de Git repository en de naam van de tak te publiceren van. Nogmaals, deze worden gedefinieerd door invoerparameters. 

Merk op `dependsOn` in het element dat, naast `sourcecontrols/web` de app-bron zelf, ook afhankelijk is van `config/appsettings` en `config/connectionstrings`. Dit komt `sourcecontrols/web` omdat zodra het Azure-implementatieproces is geconfigureerd, automatisch wordt geprobeerd de toepassingscode te implementeren, te bouwen en te starten. Daarom u deze afhankelijkheid invoegen ervoor zorgen dat de toepassing toegang heeft tot de vereiste app-instellingen en verbindingstekenreeksen voordat de toepassingscode wordt uitgevoerd. 

> [!NOTE]
> Let ook `IsManualIntegration` op `true`dat is ingesteld op . Deze eigenschap is nodig in deze zelfstudie omdat u niet echt eigenaar bent van de GitHub-repository en dus geen toestemming kan verlenen aan Azure om continue publicatie van [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) te configureren (d.w.z. automatische repository-updates naar Azure te pushen). U de `false` standaardwaarde voor de opgegeven opslagplaats alleen gebruiken als u de GitHub-referenties van de eigenaar al eerder in de [Azure-portal](https://portal.azure.com/) hebt geconfigureerd. Met andere woorden, als u eerder bronbeheer hebt ingesteld op GitHub of BitBucket voor een app in de [Azure Portal,](https://portal.azure.com/) met behulp van uw gebruikersreferenties, onthoudt Azure de referenties en gebruikt deze wanneer u in de toekomst een app implementeert vanuit GitHub of BitBucket. Als u dit echter nog niet hebt gedaan, mislukt de implementatie van de JSON-sjabloon wanneer Azure Resource Manager de bronbeheerinstellingen van de app probeert te configureren omdat deze zich niet kan aanmelden bij GitHub of BitBucket met de referenties van de eigenaar van de repository.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>De JSON-sjabloon vergelijken met geïmplementeerde resourcegroep
Hier u alle blades van de app in de [Azure Portal](https://portal.azure.com/)doorlopen, maar er is een ander hulpmiddel dat net zo nuttig is, zo niet meer. Ga naar het voorbeeldhulpprogramma azure [resource explorer,](https://resources.azure.com) waarmee u een JSON-weergave krijgt van alle brongroepen in uw abonnementen, omdat deze daadwerkelijk bestaan in de Azure-backend. U ook zien hoe de JSON-hiërarchie van de resourcegroep in Azure overeenkomt met de hiërarchie in het sjabloonbestand dat wordt gebruikt om het te maken.

Als ik bijvoorbeeld naar het Azure [Resource Explorer-hulpprogramma](https://resources.azure.com) ga en de knooppunten in de verkenner uitvouw, zie ik de brongroep en de resources op basisniveau die worden verzameld onder hun respectievelijke resourcetypen.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Als u inzoomt op een app, moet u app-configuratiegegevens kunnen zien die vergelijkbaar zijn met de onderstaande schermafbeelding:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Nogmaals, de geneste resources moeten een hiërarchie hebben die sterk lijkt op die in uw JSON-sjabloonbestand en u moet de app-instellingen, verbindingstekenreeksen, enz., correct weergegeven in het JSON-deelvenster. Het ontbreken van instellingen hier kan duiden op een probleem met uw JSON-bestand en kan u helpen bij het oplossen van uw JSON-sjabloonbestand.

## <a name="deploy-the-resource-group-template-yourself"></a>De sjabloon resourcegroep zelf implementeren
De knop **Implementeren naar Azure** is geweldig, maar hiermee u de sjabloon voor resourcesgroepen alleen implementeren in azuredeploy.json als u azuredeploy.json al naar GitHub hebt geduwd. De Azure .NET SDK biedt u ook de hulpprogramma's om een JSON-sjabloonbestand rechtstreeks vanuit uw lokale machine te implementeren. Volg hiervoor de onderstaande stappen:

1. Klik in Visual**New** > Studio op Nieuw**project** **bestand** > .
2. Klik **op Visual C#** > **Cloud** > **Azure Resource Group**en klik vervolgens op **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Selecteer in **Azure-sjabloon selecteren**de optie **Lege sjabloon** en klik op **OK**.
4. Sleep azuredeploy.json naar de **map Sjabloon** van uw nieuwe project.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Open vanuit Solution Explorer de gekopieerde azuredeploy.json.
6. Laten we omwille van de demonstratie een aantal standaard Application Insight-bronnen toevoegen aan ons JSON-bestand door op **Resource toevoegen**te klikken. Als u alleen geïnteresseerd bent in het implementeren van het JSON-bestand, gaat u naar de implementatiestappen.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selecteer **Toepassingsinzichten voor webapps,** controleer of een bestaand App Service-abonnement en -app is geselecteerd en klik vervolgens op **Toevoegen**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   U nu verschillende nieuwe bronnen zien die, afhankelijk van de bron en wat deze zich bevindt, afhankelijk zijn van het App Service-abonnement of de app. Deze resources zijn niet ingeschakeld door hun bestaande definitie en je gaat dat veranderen.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Klik in het JSON-overzicht op **appInsights AutoScale** om de JSON-code te markeren. Dit is de schaalinstelling voor uw App Service-abonnement.
9. Zoek in de gemarkeerde `location` JSON-code de eigenschappen en `enabled` de eigenschappen en stel deze in zoals hieronder wordt weergegeven.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Klik in de JSON Outline op **CPUHigh appInsights** om de JSON-code te markeren. Dit is een waarschuwing.
11. Zoek `location` de `isEnabled` eigenschappen en eigenschappen en stel ze in zoals hieronder weergegeven. Doe hetzelfde voor de andere drie waarschuwingen (paarse bollen).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Je bent nu klaar om te worden ingezet. Klik met de rechtermuisknop op het project en selecteer**Nieuwe implementatie** **implementeren** > .
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Meld u aan bij uw Azure-account als u dit nog niet hebt gedaan.
14. Selecteer een bestaande brongroep in uw abonnement of maak een nieuw abonnement, selecteer **azuredeploy.json**en klik vervolgens op **Parameters bewerken**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    U nu alle parameters bewerken die in het sjabloonbestand zijn gedefinieerd in een mooie tabel. Parameters die standaardwaarden definiëren, hebben al hun standaardwaarden en parameters die een lijst met toegestane waarden definiëren, worden weergegeven als vervolgkeuzewaarden.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Vul alle lege parameters in en gebruik het [GitHub-repo-adres voor ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) in **repoUrl**. Klik vervolgens op **Opslaan.**
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatisch schalen is een functie die wordt aangeboden in **standaardlaag** of hoger, en waarschuwingen op planniveau zijn functies die worden aangeboden in **de basislaag** of hoger, u moet de **sku-parameter** instellen op **Standaard** of **Premium** om al uw nieuwe App Insights-bronnen te zien oplichten.
    > 
    > 
16. Klik op **Implementeren**. Als u **Wachtwoorden opslaan**hebt geselecteerd, wordt het wachtwoord opgeslagen in het parameterbestand in platte **tekst.** Anders wordt u gevraagd om het databasewachtwoord in te voeren tijdens het implementatieproces.

Dat is alles. Nu hoeft u alleen nog maar naar de [Azure Portal](https://portal.azure.com/) en het Azure [Resource Explorer-hulpprogramma](https://resources.azure.com) te gaan om de nieuwe waarschuwingen en instellingen voor automatisch schalen te zien die zijn toegevoegd aan uw geïmplementeerde JSON-toepassing.

Uw stappen in deze sectie hebben voornamelijk het volgende bereikt:

1. Het sjabloonbestand voorbereiden
2. Een parameterbestand maken dat bij het sjabloonbestand past
3. Het sjabloonbestand met het parameterbestand geïmplementeerd

De laatste stap is eenvoudig te doen met een PowerShell cmdlet. Als u wilt zien wat Visual Studio heeft gedaan toen de toepassing werd geïmplementeerd, opent u Scripts\Deploy-AzureResourceGroup.ps1. Er is veel code daar, maar ik ga gewoon alle relevante code benadrukken die je nodig hebt om het sjabloonbestand te implementeren met het parameterbestand.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

De laatste cmdlet, `New-AzureResourceGroup`, is degene die daadwerkelijk voert de actie. Dit alles moet u aantonen dat het met behulp van tooling relatief eenvoudig is om uw cloudtoepassing voorspelbaar te implementeren. Elke keer dat u de cmdlet op dezelfde sjabloon met hetzelfde parameterbestand uitvoert, krijgt u hetzelfde resultaat.

## <a name="summary"></a>Samenvatting
In DevOps zijn herhaalbaarheid en voorspelbaarheid de sleutels tot elke succesvolle implementatie van een grootschalige toepassing die bestaat uit microservices. In deze zelfstudie hebt u een tweemicroservicetoepassing geïmplementeerd in Azure als één resourcegroep met behulp van de sjabloon Azure Resource Manager. Hopelijk heeft het u de kennis gegeven die u nodig hebt om uw toepassing in Azure om te zetten in een sjabloon en deze voorspelbaar te kunnen inrichten en implementeren. 

<a name="resources"></a>

## <a name="more-resources"></a>Meer bronnen
* [Sjabloontaal voor Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md)
* [Sjabloonfuncties Azure Resource Manager](../azure-resource-manager/templates/template-functions.md)
* [Een toepassing implementeren met azure resource manager-sjabloon](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Problemen met resourcegroepimplementaties in Azure oplossen](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de syntaxis en eigenschappen van JSON voor resourcetypen die in dit artikel zijn geïmplementeerd:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRegels](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/instellingen voor automatisch schalen](/azure/templates/microsoft.insights/autoscalesettings)