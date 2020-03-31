---
title: Een web-app implementeren met een sjabloon - Azure Cosmos DB
description: Meer informatie over het implementeren van een Azure Cosmos DB-account, Azure App Service Web Apps en een voorbeeldwebtoepassing met behulp van een Azure Resource Manager-sjabloon.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128369"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Cosmos DB- en Azure App Service-webapps implementeren met behulp van een Azure Resource Manager-sjabloon
In deze zelfstudie ziet u hoe u een Azure Resource Manager-sjabloon gebruikt voor het implementeren en integreren van [Microsoft Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) [azure app Service en](https://go.microsoft.com/fwlink/?LinkId=529714) een voorbeeldwebtoepassing.

Met Azure Resource Manager-sjablonen u eenvoudig de implementatie en configuratie van uw Azure-bronnen automatiseren.  In deze zelfstudie ziet u hoe u een webtoepassing implementeert en azure cosmos DB-accountverbindingsgegevens automatisch configureert.

Na het voltooien van deze tutorial, zult u in staat zijn om de volgende vragen te beantwoorden:  

* Hoe kan ik een Azure Resource Manager-sjabloon gebruiken om een Azure Cosmos DB-account en een web-app in Azure App Service te implementeren en te integreren?
* Hoe kan ik een Azure Resource Manager-sjabloon gebruiken om een Azure Cosmos DB-account, een web-app in App Service Web Apps en een Webdeploy-toepassing te implementeren en te integreren?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Vereisten
> [!TIP]
> Hoewel deze zelfstudie geen eerdere ervaring opdoet met Azure Resource Manager-sjablonen of JSON, moet u, als u de sjablonen of implementatieopties wilt wijzigen, dan is kennis van elk van deze gebieden vereist.
> 
> 

Voordat u de instructies in deze zelfstudie volgt, moet u ervoor zorgen dat u een Azure-abonnement hebt. Azure is een op abonnementen gebaseerd platform.  Zie [Aankoopopties](https://azure.microsoft.com/pricing/purchase-options/), [Aanbiedingen voor leden](https://azure.microsoft.com/pricing/member-offers/)of Gratis [proefabonnement](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie over het verkrijgen van een abonnement.

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Stap 1: De sjabloonbestanden downloaden
Laten we beginnen met het downloaden van de sjabloonbestanden die deze zelfstudie vereist.

1. Download het Voorbeeldsjabloon Voor een demotoepassing maken in [Azure Cosmos DB-account, Web Apps en implementeer een voorbeeldsjabloon](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) voor demotoepassingen in een lokale map (bijvoorbeeld C:\Azure Cosmos DBTemplates). Met deze sjabloon wordt een Azure Cosmos DB-account, een App Service-web-app en een webtoepassing geïmplementeerd.  Het configureert ook automatisch de webtoepassing om verbinding te maken met het Azure Cosmos DB-account.
2. Download de [voorbeeldsjabloon Een Azure Cosmos DB-account en webapps maken](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) naar een lokale map (bijvoorbeeld C:\Azure Cosmos DBTemplates). Met deze sjabloon wordt een Azure Cosmos DB-account, een App Service-web-app, geïmplementeerd en worden de toepassingsinstellingen van de site aangepast om eenvoudig de verbindingsgegevens van Azure Cosmos DB te laten weergeven, maar geen webtoepassing.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Stap 2: Het Azure Cosmos DB-account, de web-app App Service en het voorbeeld van de demo-toepassing implementeren
Laten we nu uw eerste sjabloon implementeren.

> [!TIP]
> De sjabloon valideert niet dat de naam van de web-app en de naam van het Azure Cosmos DB-account die in de volgende sjabloon zijn ingevoerd, a) geldig en b) beschikbaar zijn.  Het wordt ten zeerste aanbevolen om de beschikbaarheid van de namen die u wilt leveren te verifiëren voordat u de implementatie indient.
> 
> 

1. Meld u aan bij de [Azure Portal,](https://portal.azure.com)klik op Nieuw en zoek naar 'Sjabloonimplementatie'.
    ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment1.png)
2. Selecteer het implementatieitem Sjabloon en klik op **Schermafbeelding maken** ![van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment2.png)
3. Klik **op Sjabloon bewerken,** plak de inhoud van het sjabloonbestand DocDBWebsiteTodo.json en klik op **Opslaan**.
   ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment3.png)
4. Klik **op Parameters bewerken,** geef waarden op voor elk van de verplichte parameters en klik op **OK**.  De parameters zijn als volgt:
   
   1. SITENAME: Hiermee geeft u de naam van de App Service-webapp op en wordt deze gebruikt om de URL te construeren die u gebruikt `mydemodocdbwebapp.azurewebsites.net`om toegang te krijgen tot de web-app (bijvoorbeeld als u "mydemodocdbwebapp" opgeeft, dan is de URL waarmee u de web-app opent).
   2. HOSTINGPLANNAME: hiermee geeft u de naam op van het hostingplan van App Service dat moet worden gemaakt.
   3. LOCATIE: hiermee geeft u de Azure-locatie op waarin de bronnen Azure Cosmos DB en web-app moeten worden gemaakt.
   4. DATABASEACCOUNTNAME: hiermee geeft u de naam op van het Te maken Azure Cosmos DB-account.   
      
      ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment4.png)
5. Kies een bestaande resourcegroep of geef een naam op om een nieuwe resourcegroep te maken en kies een locatie voor de resourcegroep.

    ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment5.png)
6. Klik **op Juridische voorwaarden**controleren en **kopen**en klik vervolgens op **Maken** om de implementatie te starten.  Selecteer **Vastmaken aan het dashboard,** zodat de resulterende implementatie gemakkelijk zichtbaar is op de startpagina van uw Azure-portal.
   ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment6.png)
7. Wanneer de implementatie is voltooid, wordt het deelvenster Resourcegroep geopend.
   ![Schermafbeelding van het deelvenster resourcegroep](./media/create-website/TemplateDeployment7.png)  
8. Als u de toepassing wilt gebruiken, navigeert u naar de `http://mydemodocdbwebapp.azurewebsites.net`URL van de web-app (in het bovenstaande voorbeeld zou de URL ).  U ziet de volgende webtoepassing:
   
   ![Voorbeeld van Todo-toepassing](./media/create-website/image2.png)
9. Maak een paar taken in de web-app en keer vervolgens terug naar het groepsvenster Resource in de Azure-portal. Klik op de Azure Cosmos DB-accountbron in de lijst Resources en klik vervolgens op **Gegevensverkenner**.
10. Voer de standaardquery 'SELECT * FROM c' uit en inspecteer de resultaten.  De query heeft de JSON-weergave van de todo-items die u in stap 7 hebt gemaakt, opgehaald.  Voel je vrij om te experimenteren met query's; Probeer bijvoorbeeld SELECT * FROM c WHERE c.isComplete = true te gebruiken om alle todo-items die als voltooid zijn gemarkeerd, terug te sturen.
11. Bekijk gerust de Azure Cosmos DB-portalervaring of wijzig de voorbeeld-Todo-toepassing.  Als u er klaar voor bent, implementeren we een andere sjabloon.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Stap 3: Voorbeeld van documentaccount en web-app implementeren
Laten we nu uw tweede sjabloon implementeren.  Deze sjabloon is handig om te laten zien hoe u Azure Cosmos DB-verbindingsgegevens zoals accounteindpunt en hoofdsleutel injecteren in een web-app als toepassingsinstellingen of als aangepaste verbindingstekenreeks. U hebt bijvoorbeeld uw eigen webtoepassing die u wilt implementeren met een Azure Cosmos DB-account en die de verbindingsgegevens tijdens de implementatie automatisch vullen.

> [!TIP]
> De sjabloon valideert niet dat de web-app naam en Azure Cosmos DB account naam hieronder zijn a) geldig en b) beschikbaar.  Het wordt ten zeerste aanbevolen om de beschikbaarheid van de namen die u wilt leveren te verifiëren voordat u de implementatie indient.
> 
> 

1. Klik in de [Azure Portal](https://portal.azure.com)op Nieuw en zoek naar 'Sjabloonimplementatie'.
    ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment1.png)
2. Selecteer het implementatieitem Sjabloon en klik op **Schermafbeelding maken** ![van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment2.png)
3. Klik **op Sjabloon bewerken,** plak de inhoud van het sjabloonbestand DocDBWebSite.json en klik op **Opslaan**.
   ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment3.png)
4. Klik **op Parameters bewerken,** geef waarden op voor elk van de verplichte parameters en klik op **OK**.  De parameters zijn als volgt:
   
   1. SITENAME: Hiermee geeft u de naam van de App Service-webapp op en wordt deze gebruikt om de URL te construeren die u gebruikt om toegang te krijgen tot de web-app (bijvoorbeeld als u "mydemodocdbwebapp" opgeeft, wordt de URL waarmee u de web-app opent mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: hiermee geeft u de naam op van het hostingplan van App Service dat moet worden gemaakt.
   3. LOCATIE: hiermee geeft u de Azure-locatie op waarin de bronnen Azure Cosmos DB en web-app moeten worden gemaakt.
   4. DATABASEACCOUNTNAME: hiermee geeft u de naam op van het Te maken Azure Cosmos DB-account.   
      
      ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment4.png)
5. Kies een bestaande resourcegroep of geef een naam op om een nieuwe resourcegroep te maken en kies een locatie voor de resourcegroep.

    ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment5.png)
6. Klik **op Juridische voorwaarden**controleren en **kopen**en klik vervolgens op **Maken** om de implementatie te starten.  Selecteer **Vastmaken aan het dashboard,** zodat de resulterende implementatie gemakkelijk zichtbaar is op de startpagina van uw Azure-portal.
   ![Schermafbeelding van de gebruikersinterface van de sjabloonimplementatie](./media/create-website/TemplateDeployment6.png)
7. Wanneer de implementatie is voltooid, wordt het deelvenster Resourcegroep geopend.
   ![Schermafbeelding van het deelvenster resourcegroep](./media/create-website/TemplateDeployment7.png)  
8. Klik op de webapp-bron in de lijst Resources en klik vervolgens op Schermafbeelding van **toepassingsinstellingen** ![van de brongroep](./media/create-website/TemplateDeployment9.png)  
9. Houd er rekening mee hoe er toepassingsinstellingen aanwezig zijn voor het Azure Cosmos DB-eindpunt en elk van de Azure Cosmos DB-hoofdsleutels.

    ![Schermafbeelding van toepassingsinstellingen](./media/create-website/TemplateDeployment10.png)  
10. Kun je de Azure Portal blijven verkennen of een van onze Azure Cosmos [DB-voorbeelden](https://go.microsoft.com/fwlink/?LinkID=402386) volgen om uw eigen Azure Cosmos DB-toepassing te maken.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd! U hebt Azure Cosmos DB, App Service-web-app en een voorbeeldwebtoepassing geïmplementeerd met Azure Resource Manager-sjablonen.

* Klik [hier](https://azure.microsoft.com/services/cosmos-db/)voor meer informatie over Azure Cosmos DB.
* Klik [hier](https://go.microsoft.com/fwlink/?LinkId=325362)voor meer informatie over Azure App Service Web-apps.
* Klik [hier](https://msdn.microsoft.com/library/azure/dn790549.aspx)voor meer informatie over Azure Resource Manager-sjablonen.

## <a name="whats-changed"></a>Wat is er gewijzigd
* Zie voor een handleiding voor de wijziging van websites naar app-service: [Azure App Service en de impact ervan op bestaande Azure-services](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://go.microsoft.com/fwlink/?LinkId=523751). Hier kunt u direct een tijdelijke web-app maken in App Service. U hebt geen creditcard nodig en u doet geen toezeggingen.
> 
> 

