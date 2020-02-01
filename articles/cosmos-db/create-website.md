---
title: 'Een web-app met een sjabloon: Azure Cosmos DB implementeren'
description: Informatie over het implementeren van een Azure Cosmos DB-account, Azure App Service Web Apps en een Voorbeeldwebtoepassing met een Azure Resource Manager-sjabloon.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 11b84877171fae8e788d3298973fd74645b35480
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901912"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Cosmos DB en Azure App Service Web Apps met behulp van een Azure Resource Manager-sjabloon implementeren
Deze zelfstudie leert u hoe u een Azure Resource Manager-sjabloon gebruiken om te implementeren en integreren [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), een [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) web-app en een Voorbeeldwebtoepassing.

Met behulp van Azure Resource Manager-sjablonen, kunt u eenvoudig automatiseren de implementatie en configuratie van uw Azure-resources.  Deze zelfstudie laat zien hoe u een webtoepassing implementeert en configureert automatisch verbindingsgegevens van Azure Cosmos DB-account.

Na het voltooien van deze zelfstudie, kunt u zich de volgende vragen beantwoorden:  

* Hoe kan ik een Azure Resource Manager-sjabloon gebruiken om te implementeren en integreren van een Azure Cosmos DB-account en een web-app in Azure App Service?
* Hoe kan ik een Azure Resource Manager-sjabloon gebruiken om te implementeren en integreren van een Azure Cosmos DB-account, een web-app in App Service Web Apps en Web Deploy-toepassing?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Vereisten
> [!TIP]
> Tijdens deze zelfstudie wordt niet vanuit gegaan eerdere ervaring met Azure Resource Manager-sjablonen of JSON, is moet u wilt wijzigen van de waarnaar wordt verwezen, sjablonen of de implementatie-opties, klikt u vervolgens kennis van elk van deze gebieden vereist.
> 
> 

Voordat u de instructies in deze zelfstudie, controleert u of het een Azure-abonnement. Azure is een platform op basis van een abonnement.  Zie voor meer informatie over het verkrijgen van een abonnement [Aankoopopties](https://azure.microsoft.com/pricing/purchase-options/), [aanbiedingen voor leden](https://azure.microsoft.com/pricing/member-offers/), of [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Stap 1: Download de sjabloonbestanden
Begin met het downloaden van de sjabloonbestanden die in deze zelfstudie is vereist.

1. Download de [een Azure Cosmos DB-account, Web-Apps maken en implementeren van een voorbeeld van de toepassing demo](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) sjabloon naar een lokale map (bijvoorbeeld C:\Azure Cosmos DBTemplates). Deze sjabloon wordt geïmplementeerd voor een Azure Cosmos DB-account, een App Service-web-app en een web-App.  Het is ook automatisch Hiermee configureert u de web-App verbinden met de Azure Cosmos DB-account.
2. Download de [maken van een Azure Cosmos DB-account en een voorbeeld van de Web-Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) sjabloon naar een lokale map (bijvoorbeeld C:\Azure Cosmos DBTemplates). Deze sjabloon implementeert u een Azure Cosmos DB-account, een App Service-web-app, en Hiermee wijzigt u de toepassingsinstellingen van de site te eenvoudig voor Azure Cosmos DB-verbindingsgegevens, maar maakt geen deel uit van een web-App.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Stap 2: De Azure Cosmos DB-account, een App Service-web-app en een voorbeeld van de demo-toepassing implementeren
Nu gaan we de eerste sjabloon implementeert.

> [!TIP]
> De sjabloon komt niet overeen voor de web-appnaam en ingevoerd in de volgende sjabloon de naam van de Azure Cosmos DB-account (a) geldig en (b) beschikbaar zijn.  Het is raadzaam dat u de beschikbaarheid van de namen die u van plan bent om op te geven voordat u verzendt de implementatie controleren.
> 
> 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), klikt u op Nieuw en zoek naar 'Sjabloonimplementatie'.
    ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment1.png)
2. Selecteer het item in de implementatie sjabloon en klik op **maken** ![schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment2.png)
3. Klik op **template bewerken**, plak de inhoud van het sjabloonbestand DocDBWebsiteTodo.json en klikt u op **opslaan**.
   ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment3.png)
4. Klik op **parameters bewerken**, geef waarden op voor elk van de verplichte parameters en klikt u op **OK**.  De parameters zijn als volgt:
   
   1. SITEROL: Hiermee geeft u de naam van de App Service web-app op. deze wordt gebruikt om de URL te maken die u gebruikt voor toegang tot de web-app (bijvoorbeeld als u ' mydemodocdbwebapp ' opgeeft, wordt de URL waarmee u toegang hebt tot de web-app `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: Hiermee geeft u de naam van App Service-hostingplan te maken.
   3. LOCATIE: Hiermee geeft u de Azure-locatie in voor het maken van de Azure Cosmos DB- en web app-resources.
   4. DATABASEACCOUNTNAME: Hiermee geeft u de naam van de Azure Cosmos DB-account te maken.   
      
      ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment4.png)
5. Kies een bestaande resourcegroep of geef een naam op voor een nieuwe resourcegroep maken en kies een locatie voor de resourcegroep.

    ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment5.png)
6. Klik op **juridische voorwaarden bekijken**, **aankoop**, en klik vervolgens op **maken** om te beginnen met de implementatie.  Selecteer **vastmaken aan dashboard** , zodat de resulterende implementatie eenvoudig zichtbaar op de startpagina van Azure portal is.
   ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment6.png)
7. Wanneer de implementatie is voltooid, wordt het deelvenster van de groep Resource wordt geopend.
   ![Schermafbeelding van het deelvenster van de groep resource](./media/create-website/TemplateDeployment7.png)  
8. Als u de toepassing wilt gebruiken, navigeert u naar de URL van de web-app (in bovenstaand voor beeld is de URL `http://mydemodocdbwebapp.azurewebsites.net`).  Hier ziet u de volgende web-App:
   
   ![Voorbeeld van Todo-toepassing](./media/create-website/image2.png)
9. Gaan en een aantal taken in de web-app maken en ga vervolgens terug naar het deelvenster van de groep Resource in Azure portal. Klik op de resource van Azure Cosmos DB-account in de lijst met Resources en klik vervolgens op **Data Explorer**.
10. De standaard-query uitvoeren "Selecteer * FROM c ' en de resultaten te inspecteren.  U ziet dat de query de JSON-weergave van de todo-items die u hebt gemaakt in stap 7 bovenstaande heeft opgehaald.  U kunt om te experimenteren met query's. bijvoorbeeld het voeren van SELECT * FROM c waar c.isComplete = true te retourneren van alle taken die zijn gemarkeerd als voltooid.
11. U kunt de portal-ervaring voor Azure Cosmos DB verkennen of wijzigen van de Todo-voorbeeldtoepassing.  Wanneer u klaar bent, gaat u een andere sjabloon te implementeren.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Stap 3: De voorbeeld-Document-account en web-app implementeren
Nu gaan we de tweede sjabloon implementeert.  Deze sjabloon is handig om te laten zien hoe u kunt invoeren Azure Cosmos DB-verbindingsgegevens, zoals de eindpunt-account en de hoofdsleutel in een web-app als toepassingsinstellingen of als een aangepaste verbindingsreeks. Bijvoorbeeld, wellicht hebt u uw eigen web-App die u wilt implementeren met een Azure Cosmos DB-account en beschikken over de verbindingsgegevens automatisch ingevuld tijdens de implementatie.

> [!TIP]
> De sjabloon komt niet overeen voor de web-appnaam en Azure Cosmos DB-accountnaam die is opgegeven onder een) geldig en (b) beschikbaar zijn.  Het is raadzaam dat u de beschikbaarheid van de namen die u van plan bent om op te geven voordat u verzendt de implementatie controleren.
> 
> 

1. In de [Azure Portal](https://portal.azure.com), klikt u op Nieuw en zoek naar 'Sjabloonimplementatie'.
    ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment1.png)
2. Selecteer het item in de implementatie sjabloon en klik op **maken** ![schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment2.png)
3. Klik op **template bewerken**, plak de inhoud van het sjabloonbestand DocDBWebSite.json en klikt u op **opslaan**.
   ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment3.png)
4. Klik op **parameters bewerken**, geef waarden op voor elk van de verplichte parameters en klikt u op **OK**.  De parameters zijn als volgt:
   
   1. SITENAAM: Hiermee geeft u de naam van de App Service-web-app en wordt gebruikt om de URL die u gebruiken wilt voor toegang tot de web-app (bijvoorbeeld, als u "mydemodocdbwebapp" en vervolgens de URL waarmee u toegang tot de web-app is mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Hiermee geeft u de naam van App Service-hostingplan te maken.
   3. LOCATIE: Hiermee geeft u de Azure-locatie in voor het maken van de Azure Cosmos DB- en web app-resources.
   4. DATABASEACCOUNTNAME: Hiermee geeft u de naam van de Azure Cosmos DB-account te maken.   
      
      ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment4.png)
5. Kies een bestaande resourcegroep of geef een naam op voor een nieuwe resourcegroep maken en kies een locatie voor de resourcegroep.

    ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment5.png)
6. Klik op **juridische voorwaarden bekijken**, **aankoop**, en klik vervolgens op **maken** om te beginnen met de implementatie.  Selecteer **vastmaken aan dashboard** , zodat de resulterende implementatie eenvoudig zichtbaar op de startpagina van Azure portal is.
   ![Schermafbeelding van de gebruikersinterface voor sjabloonimplementatie](./media/create-website/TemplateDeployment6.png)
7. Wanneer de implementatie is voltooid, wordt het deelvenster van de groep Resource wordt geopend.
   ![Schermafbeelding van het deelvenster van de groep resource](./media/create-website/TemplateDeployment7.png)  
8. Klik op de Web-App-resource in de lijst met Resources en klik vervolgens op **toepassingsinstellingen** ![schermafbeelding van de resourcegroep](./media/create-website/TemplateDeployment9.png)  
9. Houd er rekening mee hoe er toepassingsinstellingen aanwezig zijn voor het Azure Cosmos DB-eindpunt en elk van de Azure Cosmos DB-hoofdsleutels zijn.

    ![Schermafbeelding van toepassingsinstellingen](./media/create-website/TemplateDeployment10.png)  
10. Gerust om door te gaan met het verkennen van de Azure Portal of voert u een van onze Azure Cosmos DB [voorbeelden](https://go.microsoft.com/fwlink/?LinkID=402386) om uw eigen Azure Cosmos DB-toepassing te maken.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd. U kunt Azure Cosmos DB, App Service-web-app en een Voorbeeldwebtoepassing met behulp van Azure Resource Manager-sjablonen hebt geïmplementeerd.

* Voor meer informatie over Azure Cosmos DB, klikt u op [hier](https://azure.microsoft.com/services/cosmos-db/).
* Voor meer informatie over Azure App Service Web apps, klikt u op [hier](https://go.microsoft.com/fwlink/?LinkId=325362).
* Voor meer informatie over Azure Resource Manager-sjablonen, klikt u op [hier](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Wat is er veranderd
* Als u van Websites wilt overstappen op App Service, raadpleegt u de volgende handleiding: [Azure App Service en de invloed ervan op bestaande Azure Services](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://go.microsoft.com/fwlink/?LinkId=523751). Hier kunt u direct een tijdelijke web-app maken in App Service. Geen creditcards vereist en geen toezeggingen.
> 
> 

