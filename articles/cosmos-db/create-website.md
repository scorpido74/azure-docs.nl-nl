---
title: Een web-app implementeren met een sjabloon-Azure Cosmos DB
description: Meer informatie over het implementeren van een Azure Cosmos DB-account, Azure App Service Web Apps en een voor beeld van een webtoepassing met behulp van een Azure Resource Manager sjabloon.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 7d1080abb35e556e97c34e77fdce4d553c169ee9
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266862"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Cosmos DB implementeren en Azure App Service Web Apps met behulp van een Azure Resource Manager sjabloon
In deze zelf studie wordt uitgelegd hoe u een Azure Resource Manager sjabloon gebruikt om [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714) web-app en een voor beeld-webtoepassing te implementeren en te integreren.

Met Azure Resource Manager sjablonen kunt u de implementatie en configuratie van uw Azure-resources eenvoudig automatiseren.  In deze zelf studie ziet u hoe u een webtoepassing implementeert en automatisch Azure Cosmos DB account verbindings gegevens configureert.

Nadat u deze zelf studie hebt voltooid, kunt u de volgende vragen beantwoorden:  

* Hoe kan ik een Azure Resource Manager-sjabloon gebruiken om een Azure Cosmos DB-account en een web-app in Azure App Service te implementeren en te integreren?
* Hoe kan ik een Azure Resource Manager-sjabloon gebruiken om een Azure Cosmos DB account, een web-app in App Service Web Apps en een webdeploy-toepassing te implementeren en te integreren?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Vereisten
> [!TIP]
> Hoewel in deze zelf studie geen eerdere ervaring met Azure Resource Manager sjablonen of JSON wordt uitgegaan, moet u de sjablonen of implementatie-opties waarnaar wordt verwezen, wijzigen. vervolgens is kennis van elk van deze gebieden vereist.
> 
> 

Voordat u de instructies in deze zelf studie volgt, moet u ervoor zorgen dat u over een Azure-abonnement beschikt. Azure is een platform op basis van abonnementen.  Zie voor meer informatie over het verkrijgen van een abonnement, [aankoop opties](https://azure.microsoft.com/pricing/purchase-options/), [leden aanbiedingen](https://azure.microsoft.com/pricing/member-offers/)of [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Stap 1: de sjabloon bestanden downloaden
Laten we beginnen met het downloaden van de sjabloon bestanden die deze zelf studie nodig heeft.

1. Down load het **Azure Cosmos DB-account maken, Web apps en implementeer een voorbeeld** sjabloon voor een demonstratie toepassing ( `https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json` ) naar een lokale map (bijvoorbeeld C:\Azure Cosmos DBTemplates). Met deze sjabloon wordt een Azure Cosmos DB-account, een App Service web-app en een webtoepassing geïmplementeerd.  Ook wordt de webtoepassing automatisch geconfigureerd om verbinding te maken met het Azure Cosmos DB-account.
2. Down load de sjabloon **een Azure Cosmos DB-account maken en web apps voor beeld** ( `https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json` ) naar een lokale map (bijvoorbeeld C:\Azure Cosmos DBTemplates). Met deze sjabloon implementeert u een Azure Cosmos DB-account, een App Service web-app en wijzigt u de toepassings instellingen van de site om eenvoudig Azure Cosmos DB verbindings gegevens in te stellen, maar bevat geen webtoepassing.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Stap 2: de Azure Cosmos DB-account, App Service web-app en voorbeeld toepassing implementeren
We gaan nu uw eerste sjabloon implementeren.

> [!TIP]
> De sjabloon valideert niet dat de naam van de web-app en het Azure Cosmos DB account in de volgende sjabloon zijn geldig en b) beschikbaar zijn.  Het is raadzaam om de beschik baarheid te controleren van de namen die u wilt leveren voordat u de implementatie verzendt.
> 
> 

1. Meld u aan bij [Azure Portal](https://portal.azure.com), klik op nieuw en zoek naar "Sjabloonimlementatie".
    ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment1.png)
2. Selecteer het Sjabloonimlementatie item en klik **Create** op ![ scherm opname van de gebruikers interface van de sjabloon implementatie maken](./media/create-website/TemplateDeployment2.png)
3. Klik op **sjabloon bewerken**, plak de inhoud van het sjabloon bestand DocDBWebsiteTodo. json en klik op **Opslaan**.
   ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment3.png)
4. Klik op **para meters bewerken**, geef waarden op voor elk van de verplichte para meters en klik op **OK**.  De parameters zijn als volgt:
   
   1. SITEROL: Hiermee geeft u de naam van de App Service web-app op. deze wordt gebruikt om de URL te maken die u gebruikt voor toegang tot de web-app (bijvoorbeeld als u ' mydemodocdbwebapp ' opgeeft, is dit de URL waarmee u toegang tot de web-app hebt `mydemodocdbwebapp.azurewebsites.net` ).
   2. HOSTINGPLANNAME: Hiermee geeft u de naam op van App Service hosting plan dat moet worden gemaakt.
   3. Locatie: Hiermee geeft u de Azure-locatie op waar u de Azure Cosmos DB-en web-app-resources wilt maken.
   4. DATABASEACCOUNTNAME: Hiermee geeft u de naam op van het Azure Cosmos DB-account dat moet worden gemaakt.   
      
      ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment4.png)
5. Kies een bestaande resource groep of geef een naam op om een nieuwe resource groep te maken en kies een locatie voor de resource groep.

    ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment5.png)
6. Klik op **juridische voor waarden controleren**, **kopen**en klik vervolgens op **maken** om de implementatie te starten.  Selecteer **vastmaken aan dash board** , zodat de resulterende implementatie eenvoudig zichtbaar is op uw Azure Portal-start pagina.
   ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment6.png)
7. Wanneer de implementatie is voltooid, wordt het deel venster Resource groep geopend.
   ![Scherm opname van het deel venster Resource groep](./media/create-website/TemplateDeployment7.png)  
8. Als u de toepassing wilt gebruiken, navigeert u naar de URL van de web-app (in bovenstaand voor beeld is dit de URL `http://mydemodocdbwebapp.azurewebsites.net` ).  U ziet de volgende webtoepassing:
   
   ![Voor beeld van een TODO-toepassing](./media/create-website/image2.png)
9. Maak een aantal taken in de web-app en ga vervolgens terug naar het deel venster Resource groep in de Azure Portal. Klik in de lijst met resources op de resource Azure Cosmos DB account en klik vervolgens op **Data Explorer**.
10. Voer de standaard query uit, selecteer * van c en controleer de resultaten.  U ziet dat de query de JSON-weer gave heeft opgehaald van de TODO-items die u in stap 7 hierboven hebt gemaakt.  Experimenteer graag met query's. Probeer bijvoorbeeld uit te voeren SELECT * FROM c WHERE c. isComplete = True om alle TODO-items te retour neren die zijn gemarkeerd als voltooid.
11. U kunt de Azure Cosmos DB Portal-ervaring verkennen of de voor beeld-toepassing wijzigen.  Wanneer u klaar bent, gaan we nog een sjabloon implementeren.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Stap 3: het document-en web-app-voor beeld implementeren
We gaan nu uw tweede sjabloon implementeren.  Deze sjabloon is handig om te laten zien hoe u Azure Cosmos DB verbindings gegevens, zoals account eindpunt en hoofd sleutel, kunt injecteren in een web-app als toepassings instellingen of als aangepaste connection string. Stel dat u uw eigen webtoepassing hebt die u wilt implementeren met een Azure Cosmos DB-account en dat de verbindings gegevens automatisch worden ingevuld tijdens de implementatie.

> [!TIP]
> De sjabloon valideert niet dat de naam van de web-app en de Azure Cosmos DB accountnaam die hieronder zijn opgegeven, geldig zijn en b) beschikbaar zijn.  Het is raadzaam om de beschik baarheid te controleren van de namen die u wilt leveren voordat u de implementatie verzendt.
> 
> 

1. Klik in [Azure Portal](https://portal.azure.com)op nieuw en zoek naar ' Sjabloonimlementatie '.
    ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment1.png)
2. Selecteer het Sjabloonimlementatie item en klik **Create** op ![ scherm opname van de gebruikers interface van de sjabloon implementatie maken](./media/create-website/TemplateDeployment2.png)
3. Klik op **sjabloon bewerken**, plak de inhoud van het sjabloon bestand DocDBWebSite. json en klik op **Opslaan**.
   ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment3.png)
4. Klik op **para meters bewerken**, geef waarden op voor elk van de verplichte para meters en klik op **OK**.  De parameters zijn als volgt:
   
   1. SITEROL: Hiermee geeft u de naam van de App Service web-app op. deze wordt gebruikt om de URL te maken die u gaat gebruiken voor toegang tot de web-app (bijvoorbeeld als u ' mydemodocdbwebapp ' opgeeft, wordt de URL waarmee u toegang tot de web-app hebt, mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Hiermee geeft u de naam op van App Service hosting plan dat moet worden gemaakt.
   3. Locatie: Hiermee geeft u de Azure-locatie op waar u de Azure Cosmos DB-en web-app-resources wilt maken.
   4. DATABASEACCOUNTNAME: Hiermee geeft u de naam op van het Azure Cosmos DB-account dat moet worden gemaakt.   
      
      ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment4.png)
5. Kies een bestaande resource groep of geef een naam op om een nieuwe resource groep te maken en kies een locatie voor de resource groep.

    ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment5.png)
6. Klik op **juridische voor waarden controleren**, **kopen**en klik vervolgens op **maken** om de implementatie te starten.  Selecteer **vastmaken aan dash board** , zodat de resulterende implementatie eenvoudig zichtbaar is op uw Azure Portal-start pagina.
   ![Scherm afbeelding van de gebruikers interface van de sjabloon implementatie](./media/create-website/TemplateDeployment6.png)
7. Wanneer de implementatie is voltooid, wordt het deel venster Resource groep geopend.
   ![Scherm opname van het deel venster Resource groep](./media/create-website/TemplateDeployment7.png)  
8. Klik op de resource van de web-app in de lijst met resources en klik vervolgens op de scherm afbeelding **Toepassings instellingen** ![ van de resource groep](./media/create-website/TemplateDeployment9.png)  
9. Houd er rekening mee dat er toepassings instellingen aanwezig zijn voor het Azure Cosmos DB-eind punt en elk van de Azure Cosmos DB hoofd sleutels.

    ![Scherm opname van toepassings instellingen](./media/create-website/TemplateDeployment10.png)  
10. U kunt door gaan met het verkennen van de Azure portal of een van onze Azure Cosmos DB-voor [beelden](https://go.microsoft.com/fwlink/?LinkID=402386) volgen om uw eigen Azure Cosmos DB-toepassing te maken.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd U hebt Azure Cosmos DB, App Service web-app en een voor beeld-webtoepassing geïmplementeerd met behulp van Azure Resource Manager sjablonen.

* Klik [hier](https://azure.microsoft.com/services/cosmos-db/)voor meer informatie over Azure Cosmos db.
* Klik [hier](https://go.microsoft.com/fwlink/?LinkId=325362)voor meer informatie over Azure app service Web-apps.
* Klik [hier](https://msdn.microsoft.com/library/azure/dn790549.aspx)voor meer informatie over Azure Resource Manager sjablonen.

## <a name="whats-changed"></a>Wat is er gewijzigd
* Voor een hand leiding voor het wijzigen van websites naar App Service ziet u: [Azure app service en de invloed ervan op bestaande Azure-Services](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://go.microsoft.com/fwlink/?LinkId=523751). Hier kunt u direct een tijdelijke web-app maken in App Service. U hebt geen creditcard nodig en u doet geen toezeggingen.
> 
> 

