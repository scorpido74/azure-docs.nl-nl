---
title: Logica-app-werkstromen sneller maken met vooraf gebouwde sjablonen
description: Snel logische app-werkstromen maken met vooraf gebouwde sjablonen van Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: aac0060527af7b7d880f971e7608be3fa44a2d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905108"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Werkstromen voor logische apps maken met vooraf gedefinieerde sjablonen

Om u sneller aan de slagwerkstromen te laten maken, biedt Logic Apps sjablonen, die vooraf gebouwde logische apps zijn die veelgebruikte patronen volgen. Gebruik deze sjablonen zoals deze zijn verstrekt of bewerk ze om aan uw scenario te voldoen.

Hier volgen enkele sjablooncategorieën:

| Sjabloontype | Beschrijving | 
| ------------- | ----------- | 
| Enterprise-cloudsjablonen | Voor de integratie van Azure Blob, Dynamics CRM, Salesforce, Box en andere connectoren voor uw zakelijke cloudbehoeften. U deze sjablonen bijvoorbeeld gebruiken om zakelijke leads te ordenen of een back-up van uw bedrijfsgegevens te maken. | 
| Persoonlijke productiviteitssjablonen | Verbeter de persoonlijke productiviteit door dagelijkse herinneringen in te stellen, belangrijke werkitems om te zetten in takenlijsten en langdurige taken te automatiseren tot één goedkeuringsstap van één gebruiker. | 
| Cloudsjablonen voor consumenten | Voor het integreren van sociale media diensten zoals Twitter, Slack en e-mail. Handig voor het versterken van social media marketing initiatieven. Deze sjablonen bevatten ook taken zoals het kopiëren in de cloud, wat de productiviteit verhoogt door tijd te besparen op traditioneel repetitieve taken. | 
| Sjablonen voor enterprise-integratiepakket | Voor het configureren van VETER-pijplijnen (valideren, extraheren, transformeren, verrijken, route) u een X12 EDI-document ontvangen via AS2 en transformeren naar XML en x12-, EDIFACT- en AS2-berichten verwerken. | 
| Protocolpatroonsjablonen | Voor het implementeren van protocolpatronen, zoals request-response via HTTP en integraties in FTP en SFTP. Gebruik deze sjablonen zoals voorzien, of bouw erop voorvoor op complexe protocolpatronen. | 
||| 

Als u nog geen abonnement op Azure hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint. Zie [Een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over het bouwen van een logische app.

## <a name="create-logic-apps-from-templates"></a>Logische apps maken met sjablonen

1. Als u dit nog niet hebt gedaan, meldt u zich aan bij de [Azure-portal.](https://portal.azure.com "Azure Portal")

2. Kies in het hoofdmenu van Azure de optie **Een resource** > **Enterprise Integration** > **Logic App maken**.

   ![Azure Portal, Nieuw, Enterprise Integration, Logische app](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Maak de logische app aan de hand van de instellingen die in de tabel onder deze afbeelding staan opgegeven:

   ![Gegevens van logische app opgeven](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Instelling | Waarde | Beschrijving | 
   | ------- | ----- | ----------- | 
   | **Naam** | *naam-van-uw-logische-app* | Geef een unieke naam voor de logische app op. | 
   | **Abonnement** | *naam-van-uw-Azure-abonnement* | Selecteer het Azure-abonnement dat u wilt gebruiken. | 
   | **Resourcegroep** | *naam-van-uw-Azure-resourcegroep* | Maak of selecteer een [Azure-brongroep](../azure-resource-manager/management/overview.md) voor deze logische app en organiseer alle resources die aan deze app zijn gekoppeld. | 
   | **Locatie** | *uw-Azure-datacenter-regio* | Selecteer de regio van het datacenter voor het implementeren van uw logische app, bijvoorbeeld VS - west. | 
   | **Logboekanalyse** | **Uit** (standaard) of **aan** | Stel [diagnostische logboekregistratie](../logic-apps/monitor-logic-apps-log-analytics.md) in voor uw logische app met [Azure Monitor-logboeken.](../log-analytics/log-analytics-overview.md) Vereist dat u al een Log Analytics-werkruimte hebt. | 
   |||| 

4. Wanneer u klaar bent, selecteert u **Vastmaken aan dashboard**. Op die manier wordt uw logische app automatisch weergegeven op uw Azure-dashboard en wordt geopend na de implementatie. Kies **Maken**.

   > [!NOTE]
   > Als u de logische app niet wilt vastmaken, moet u deze handmatig zoeken en openen na de implementatie, zodat u kunt doorgaan.

   Nadat Azure uw logische app heeft geïmplementeerd, wordt de ontwerper voor logische apps geopend en ziet u een pagina met een inleidende video. 
   Onder de video ziet u sjablonen voor veelvoorkomende patronen voor logische apps. 

5. Blader voorbij de introductievideo en algemene triggers naar **Sjablonen**. Kies een vooraf gebouwde sjabloon. Bijvoorbeeld:

   ![Een sjabloon voor logische apps kiezen](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Als u uw logische app helemaal opnieuw wilt maken, kiest u **Blank Logic App**.

   Wanneer u een vooraf gebouwde sjabloon selecteert, u meer informatie over die sjabloon weergeven. 
   Bijvoorbeeld:

   ![Een vooraf gebouwde sjabloon kiezen](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Als u verder wilt gaan met de geselecteerde sjabloon, kiest **u Deze sjabloon gebruiken**. 

7. Op basis van de connectors in de sjabloon wordt u gevraagd een van de volgende stappen uit te voeren:

   * Meld u aan met uw referenties voor systemen of services waarnaar wordt verwezen door de sjabloon.

   * Verbindingen maken voor services of systemen waarnaar de sjabloon verwijst. Als u een verbinding wilt maken, geeft u een naam op voor uw verbinding en selecteert u indien nodig de bron die u wilt gebruiken. 

   * Als u deze verbindingen al hebt ingesteld, kiest u **Doorgaan**.

   Bijvoorbeeld:

   ![Verbindingen maken](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Wanneer u klaar bent, wordt uw logische app geopend en weergegeven in de Logic Apps Designer.

   > [!TIP]
   > Als u wilt terugkeren naar de sjabloonviewer, kiest u **Sjablonen** op de werkbalk van de ontwerper. Met deze actie worden niet-opgeslagen wijzigingen verwijderd, zodat er een waarschuwingsbericht wordt weergegeven om uw verzoek te bevestigen.

8. Ga door met het bouwen van uw logica-app.

   > [!NOTE] 
   > Veel sjablonen bevatten connectors die mogelijk al vooraf ingevulde vereiste eigenschappen hebben. Voor sommige sjablonen moet u echter nog steeds waarden opgeven voordat u de logische app correct implementeren. Als u probeert te implementeren zonder de ontbrekende eigenschapsvelden te voltooien, krijgt u een foutmelding. 

## <a name="update-logic-apps-with-templates"></a>Logische apps bijwerken met sjablonen

1. Zoek en open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com "Azure Portal")

2. Kies Sjablonen op de werkbalk **van**de ontwerper . Met deze actie worden niet-opgeslagen wijzigingen verwijderd, zodat er een waarschuwingsbericht wordt weergegeven, zodat u bevestigen dat u door wilt gaan. Kies **OK**. Bijvoorbeeld:

   ![Kies 'Sjablonen'](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Blader voorbij de introductievideo en algemene triggers naar **Sjablonen**. Kies een vooraf gebouwde sjabloon. Bijvoorbeeld:

   ![Een sjabloon voor logische apps kiezen](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Wanneer u een vooraf gebouwde sjabloon selecteert, u meer informatie over die sjabloon weergeven. 
   Bijvoorbeeld:

   ![Een vooraf gebouwde sjabloon kiezen](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Als u verder wilt gaan met de geselecteerde sjabloon, kiest **u Deze sjabloon gebruiken**. 

5. Op basis van de connectors in de sjabloon wordt u gevraagd een van de volgende stappen uit te voeren:

   * Meld u aan met uw referenties voor systemen of services waarnaar wordt verwezen door de sjabloon.

   * Verbindingen maken voor services of systemen waarnaar de sjabloon verwijst. Als u een verbinding wilt maken, geeft u een naam op voor uw verbinding en selecteert u indien nodig de bron die u wilt gebruiken. 

   * Als u deze verbindingen al hebt ingesteld, kiest u **Doorgaan**.

   ![Verbindingen maken](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Uw logica-app wordt nu geopend en wordt weergegeven in de Logic Apps Designer.

8. Ga door met het bouwen van uw logica-app. 

   > [!TIP]
   > Als u de wijzigingen niet hebt opgeslagen, u uw werk weggooien en terugkeren naar uw vorige logische app. Kies op de werkbalk van de ontwerper de optie **Verwijderen**.

> [!NOTE] 
> Veel sjablonen bevatten connectors die mogelijk al vooraf ingevulde vereiste eigenschappen hebben. Voor sommige sjablonen moet u echter nog steeds waarden opgeven voordat u de logische app correct implementeren. Als u probeert te implementeren zonder de ontbrekende eigenschapsvelden te voltooien, krijgt u een foutmelding.

## <a name="deploy-logic-apps-built-from-templates"></a>Logische apps implementeren die zijn opgebouwd uit sjablonen

Nadat u de sjabloon hebt gewijzigd, u de wijzigingen opslaan. Met deze actie wordt ook automatisch uw logische app gepubliceerd.

Kies **Opslaan** op de werkbalk van de ontwerper.

![Uw logische app opslaan en publiceren](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen van logische apps aan de andere kant van voorbeelden, scenario's, klantverhalen en walkthroughs.

> [!div class="nextstepaction"]
> [Voorbeelden van logische app's, scenario's en walkthroughs bekijken](../logic-apps/logic-apps-examples-and-scenarios.md)