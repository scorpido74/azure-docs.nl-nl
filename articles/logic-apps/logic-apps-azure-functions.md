---
title: Azure-functies toevoegen en aanroepen vanuit Azure Logic Apps
description: Aangepaste code in uw Azure-functies aanbellen en uitvoeren vanuit geautomatiseerde taken en werkstromen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284120"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Azure-functies aanroepen vanuit Azure Logic Apps

Wanneer u code wilt uitvoeren die een specifieke taak uitvoert in uw logische apps, u uw eigen functie maken met [Azure-functies](../azure-functions/functions-overview.md). Met deze service u functies Node.js, C#en F# maken, zodat u geen volledige app of infrastructuur hoeft te bouwen om code uit te voeren. U ook [logische apps aanroepen vanuit Azure-functies.](#call-logic-app) Azure Functions biedt serverless computing in de cloud en is handig voor het uitvoeren van taken zoals deze voorbeelden:

* Breid het gedrag van uw logische app uit met functies in Node.js of C#.
* Voer berekeningen uit in uw logische app-werkstroom.
* Pas geavanceerde opmaak- of rekenvelden toe in uw logische apps.

Als u codefragmenten wilt uitvoeren zonder Azure-functies te maken, leest u hoe [u inline-code toevoegt en uitvoert.](../logic-apps/logic-apps-add-run-inline-code.md)

> [!NOTE]
> Integratie tussen Logic Apps en Azure Functions werkt momenteel niet met sleuven ingeschakeld.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Azure-functie-app, een container voor Azure-functies, samen met uw Azure-functie. Als u geen functie-app hebt, [maakt u eerst de functie-app](../azure-functions/functions-create-first-azure-function.md). Vervolgens u uw functie buiten uw logische app maken in de Azure-portal of [vanuit uw logische app](#create-function-designer) in de Logic App Designer.

* Bij het werken met logische apps gelden dezelfde vereisten voor functie-apps en -functies, ongeacht of ze bestaan of nieuw zijn:

  * Uw functie-app en logische app moeten hetzelfde Azure-abonnement gebruiken.

  * Nieuwe functie-apps moeten de .NET of JavaScript als runtime-stack gebruiken. Wanneer u een nieuwe functie toevoegt aan bestaande functie-apps, u C# of JavaScript selecteren.

  * De functie gebruikt de **HTTP-triggersjabloon.**

    De HTTP-triggersjabloon kan `application/json` inhoud accepteren die tekst heeft uit uw logische app. Wanneer u een Azure-functie toevoegt aan uw logische app, worden in logic app designer aangepaste functies weergegeven die zijn gemaakt van deze sjabloon in uw Azure-abonnement.

  * Uw functie gebruikt geen aangepaste routes, tenzij u een [OpenAPI-definitie](../azure-functions/functions-openapi-definition.md) hebt gedefinieerd (voorheen bekend als een [Swagger-bestand).](https://swagger.io/)

  * Als u een OpenAPI-definitie voor uw functie hebt, biedt de Logic Apps Designer u een rijkere ervaring wanneer u werkt met functieparameters. Voordat uw logische app functies met OpenAPI-definities kan vinden en openen, [stelt u uw functie-app in door deze stappen te volgen.](#function-swagger)

* De logische app waarin u de functie wilt toevoegen, inclusief een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) als eerste stap in uw logische app

  Voordat u acties toevoegen die functies uitvoeren, moet uw logische app beginnen met een trigger. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Functies zoeken met OpenAPI-beschrijvingen

Voor een rijkere ervaring wanneer u werkt met functieparameters in de Logic Apps Designer, [genereert u een OpenAPI-definitie](../azure-functions/functions-openapi-definition.md), voorheen bekend als een [Branieer-bestand,](https://swagger.io/)voor uw functie. Voer de volgende stappen uit om uw functie-app zo in te stellen dat uw logica-app functies met Swagger-beschrijvingen kan vinden en gebruiken:

1. Zorg ervoor dat de functie-app actief wordt uitgevoerd.

1. Stel in uw [functie-app Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in, zodat alle oorsprongen zijn toegestaan door de volgende stappen te volgen:

   1. Selecteer in de lijst **Functie-apps** de functie-app. Selecteer in het rechterdeelvenster de optie **Platformfuncties** > **CORS**.

      ![Selecteer uw functie-app > "Platformfuncties" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Voeg onder **CORS**het jokerteken (**`*`**) jokerteken toe, maar verwijder alle andere oorsprongin de lijst en selecteer **Opslaan**.

      ![Stel "CORS* in op het jokerteken "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Eigenschapswaarden binnen HTTP-aanvragen openen

Webhook-functies kunnen HTTP-aanvragen accepteren als invoer en deze verzoeken doorgeven aan andere functies. Hoewel Logic Apps bijvoorbeeld functies heeft [die DatumTijd-waarden converteren,](../logic-apps/workflow-definition-language-functions-reference.md)geeft deze basisvoorbeeld JavaScript-functie weer hoe u toegang hebt tot een eigenschap in een aanvraagobject dat is doorgegeven aan de functie en bewerkingen uitvoert op die eigenschapswaarde. Om toegang te krijgen tot eigenschappen in objecten, wordt in dit voorbeeld de [operator punt (.) gebruikt:](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Dit gebeurt er in deze functie:

1. De functie `data` maakt een variabele `body` en `request` wijst het object in het object toe aan die variabele. De functie gebruikt de operator punt (.) om naar het `body` object in het `request` object te verwijzen:

   ```javascript
   var data = request.body;
   ```

1. De functie heeft `date` nu toegang `data` tot de eigenschap via de variabele en converteert `ToDateString()` die eigenschapswaarde van DateTime-type naar DateString-type door de functie aan te roepen. De functie retourneert `body` het resultaat ook via de eigenschap in het antwoord van de functie:

   ```javascript
   body: data.date.ToDateString();
   ```

Nu u uw Azure-functie hebt gemaakt, voert u de stappen uit voor het [toevoegen van functies aan logische apps.](#add-function-logic-app)

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Functies maken in logische apps

Voordat u een Azure-functie maken die vanuit uw logische app begint met de Logic App Designer, moet u eerst een Azure-functie-app hebben, een container voor uw functies. Als u geen functie-app hebt, maakt u eerst die functie-app. Zie [Uw eerste functie maken in de Azure-portal](../azure-functions/functions-create-first-azure-function.md).

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Als u uw functie wilt maken en toevoegen, volgt u de stap die van toepassing is op uw scenario:

   * Selecteer onder de laatste stap in de werkstroom van uw logische app de optie **Nieuwe stap**.

   * Als u tussen de bestaande stappen in de werkstroom van uw logische app werkt, beweegt u de muis over de pijl, selecteert u het plusteken (+) en selecteert u **Een actie toevoegen**.

1. Voer in het zoekvak 'azure-functies' in als filter. Selecteer in de lijst met acties de functieactie **Kiezen voor een Azure:**

   ![Zoeken naar 'Azure-functies'](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Selecteer in de lijst met functie-apps de functie-app. Nadat de lijst met acties is geopend, selecteert u deze actie: **Nieuwe functie maken**

   ![Selecteer de functie-app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Definieer in de functiedefinitie-editor uw functie:

   1. Geef in het vak **Functienaam** een naam voor uw functie op.

   1. Voeg in het vak **Code** uw code toe aan de functiesjabloon, inclusief de respons en de payload die u wilt teruggeven naar uw logische app nadat uw functie is voltooid. Als u gereed bent, selecteert u **Maken**.

   Bijvoorbeeld:

   ![Uw functie definiëren](./media/logic-apps-azure-functions/add-code-function-definition.png)

   In de code van * `context` * de sjabloon verwijst het object naar het bericht dat uw logische app in een latere stap via het veld **Hoofdaanvraag** verzendt. Als u `context` de eigenschappen van het object vanuit uw functie wilt openen, gebruikt u de als volgt:

   `context.body.<property-name>`

   Als u bijvoorbeeld `content` wilt verwijzen `context` naar de eigenschap in het object, gebruikt u de als volgt:

   `context.body.content`

   De sjablooncode bevat `input` ook een variabele, `data` die de waarde van de parameter opslaat, zodat uw functie bewerkingen op die waarde kan uitvoeren. Binnen JavaScript-functies `data` is de variabele `context.body`ook een snelkoppeling voor .

   > [!NOTE]
   > De `body` eigenschap hier `context` is van toepassing op het object en is niet hetzelfde als het **bodytoken** van de uitvoer van een actie, die u mogelijk ook aan uw functie doorgeeft.

1. Geef in het vak **Hoofdaanvraag** de invoer van uw functie op, die moet worden opgemaakt als een JSON-object (JavaScript Object Notation).

   Deze invoer is het *contextobject* of bericht dat uw logische app naar uw functie verzendt. Wanneer u in het veld **Hoofdaanvraag** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u tokens selecteren voor uitvoer uit eerdere stappen. In dit voorbeeld wordt opgegeven dat `content` de contextpayload een eigenschap bevat met de naam **From-token** van de e-mailtrigger.

   ![Voorbeeld van 'Aanvraaglichaam'- payload van contextobjecten](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wordt het contextobject niet gegoten als een tekenreeks, zodat de inhoud van het object rechtstreeks wordt toegevoegd aan de JSON-payload. Als het contextobject echter geen JSON-token is dat een tekenreeks, een JSON-object of een JSON-array passeert, krijgt u een foutmelding. Als in dit voorbeeld het token **Ontvangen tijd** wordt gebruikt, u het contextobject dus als tekenreeks casten door dubbele aanhalingstekens toe te voegen.

   ![Object casten als tekenreeks](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Als u andere details wilt opgeven, zoals de methode die u wilt gebruiken, kopteksten of queryparameters of verificatie aanvragen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste opties. Voor verificatie verschillen uw opties op basis van de geselecteerde functie. Zie [Verificatie inschakelen voor Azure-functies](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Bestaande functies toevoegen aan logische apps

Als u bestaande Azure-functies vanuit uw logische apps wilt aanroepen, u Azure-functies toevoegen zoals elke andere actie in de Logic App Designer.

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Selecteer Nieuwe **stap**onder de stap waarin u de functie wilt toevoegen .

1. Voer **onder Een actie kiezen**in het zoekvak 'azure-functies' in als filter. Selecteer in de lijst met acties de **functieactie Kiezen voor een Azure.**

   ![Zoeken naar 'Azure-functies'](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Selecteer in de lijst met functie-apps de functie-app. Nadat de lijst met functies is weergegeven, selecteert u de functie.

   ![Selecteer uw functie-app en Azure-functie](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Voor functies met API-definities (Swagger-beschrijvingen) en die zijn [ingesteld zodat uw logische app deze functies kan vinden en openen,](#function-swagger)u **Swagger-acties**selecteren.

   ![Selecteer uw functie-app, 'Swagger-acties' en uw Azure-functie](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Geef in het vak **Hoofdaanvraag** de invoer van uw functie op, die moet worden opgemaakt als een JSON-object (JavaScript Object Notation).

   Deze invoer is het *contextobject* of bericht dat uw logische app naar uw functie verzendt. Wanneer u in het veld **Hoofdaanvraag** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u tokens selecteren voor uitvoer uit eerdere stappen. In dit voorbeeld wordt opgegeven dat `content` de contextpayload een eigenschap bevat met de naam **From-token** van de e-mailtrigger.

   ![Voorbeeld van 'Aanvraaglichaam'- payload van contextobjecten](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wordt het contextobject niet gegoten als een tekenreeks, zodat de inhoud van het object rechtstreeks wordt toegevoegd aan de JSON-payload. Als het contextobject echter geen JSON-token is dat een tekenreeks, een JSON-object of een JSON-array passeert, krijgt u een foutmelding. Als in dit voorbeeld het token **Ontvangen tijd** wordt gebruikt, u het contextobject dus als tekenreeks casten door dubbele aanhalingstekens toe te voegen:

   ![Object casten als tekenreeks](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Als u andere details wilt opgeven, zoals de methode die u wilt gebruiken, moet u kopteksten, queryparameters of verificatie aanvragen, opent u de lijst **Nieuwe parameter toevoegen** en selecteert u de gewenste opties. Voor verificatie verschillen uw opties op basis van de geselecteerde functie. Zie [Verificatie inschakelen in Azure-functies](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Logische apps bellen vanuit Azure-functies

Wanneer u een logische app vanuit een Azure-functie wilt activeren, moet de logische app beginnen met een trigger die een aanroepbaar eindpunt biedt. U de logische app bijvoorbeeld starten met de trigger **HTTP**, **Request**, **Azure Queues**of **Event Grid.** Stuur in uw functie een HTTP POST-verzoek naar de URL van de trigger en voeg de payload toe die u wilt dat die logische app verwerkt. Zie [Logische-apps bellen, activeren of nesten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie.

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Verificatie inschakelen voor Azure-functies

Als u de toegang tot resources in andere Azure Active Directory-tenants (Azure AD) wilt verifiëren zonder u aan te melden en referenties of geheimen te hoeven opgeven, kan uw logische app een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) gebruiken (voorheen managed service identity of MSI). Azure beheert deze identiteit voor u en helpt uw referenties te beveiligen omdat u geen geheimen hoeft op te geven of te roteren. Meer informatie over [Azure-services die beheerde identiteiten voor Azure AD-verificatie ondersteunen.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

Als u uw logische app zo instelt dat de door het systeem toegewezen identiteit of een door de gebruiker zelf toegewezen identiteit met de hand wordt gebruikt, kunnen de Azure-functies in uw logische app dezelfde identiteit ook gebruiken voor verificatie. Zie [Verificatie toevoegen aan uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over verificatieondersteuning voor Azure-functies in logische apps.

Voer de volgende stappen uit om de beheerde identiteit met uw functie in te stellen en te gebruiken:

1. Schakel de beheerde identiteit in uw logische app in en stel de toegang van die identiteit tot de doelbron in. Zie [Toegang tot Azure-bronnen verifiëren met beheerde identiteiten in Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Schakel verificatie in uw Azure-functie en functie-app in door de volgende stappen te volgen:

   * [Anonieme verificatie instellen in uw functie](#set-authentication-function-app)
   * [Azure AD-verificatie instellen in uw functie-app](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Anonieme verificatie instellen in uw functie

Als u de beheerde identiteit van uw logische app wilt gebruiken in uw Azure-functie, hebt u het verificatieniveau van uw functie ingesteld op anoniem. Anders wordt in uw logische app een fout van 'BadRequest' weergegeven.

1. Zoek en selecteer uw functie-app in de [Azure-portal.](https://portal.azure.com) In deze stappen wordt "FabrikamFunctionApp" gebruikt als voorbeeldfunctie-app.

1. Selecteer in het deelvenster Functie-app de optie **Platformfuncties**. Selecteer onder **Hulpmiddelen voor Ontwikkeling**geavanceerde gereedschappen **(Kudu)**.

   ![Open geavanceerde tools voor Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Selecteer **CMD**in de titelbalk van de Kudu-website in het menu **Debug Console.**

   ![Selecteer in het menu bugopsporingconsole de optie 'CMD'](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Nadat de volgende pagina is weergegeven, selecteert u in de mappenlijst **de optie site** > **wwwroot** > *your-function*. In deze stappen wordt "FabrikamAzureFunction" als voorbeeldfunctie gebruikt.

   ![Selecteer "site" > "wwwroot" > uw functie](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Open `function.json` het bestand voor bewerking.

   ![Klik op bewerken voor bestand 'function.json'.](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Controleer `bindings` in het object `authLevel` of de eigenschap bestaat. Als de eigenschap bestaat, stelt `anonymous`u de eigenschapswaarde in op . Voeg anders die eigenschap toe en stel de waarde in.

   ![De eigenschap 'authLevel' toevoegen en instellen op 'anoniem'](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Wanneer u klaar bent, slaat u uw instellingen op en gaat u verder naar de volgende sectie.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Azure AD-verificatie instellen voor uw functie-app

Voordat u deze taak start, moet u deze waarden zoeken en opzij zetten voor later gebruik:

* De object-id die wordt gegenereerd voor de door het systeem toegewezen identiteit die uw logische app vertegenwoordigt

  * Als u deze object-id wilt genereren, [schakelt u de systeemtoegewezen identiteit van uw logische app in.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * Als u deze object-id wilt vinden, opent u de logische app in de Logische App Designer. Selecteer in het menu van uw logische app onder **Instellingen**de optie **Identiteitssysteem** > **toegewezen**.

* De adresmap-id voor uw tenant in Azure Active Directory (Azure AD)

  Als u de directory-id van uw [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) tenant wilt krijgen, u de opdracht Powershell uitvoeren. Of volg in de Azure-portal de volgende stappen:

  1. Zoek en selecteer uw functie-app in de [Azure-portal.](https://portal.azure.com)

  1. Zoek en selecteer uw Azure AD-tenant. Deze stappen gebruiken "Fabrikam" als voorbeeldtenant.

  1. Selecteer **Eigenschappen**onder Beheren onder **Beheren**.

  1. Kopieer bijvoorbeeld de directory-id van uw tenant en sla die id op voor later gebruik.

     ![Directory-id van Azure AD-tenant zoeken en kopiëren](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* De resource-id voor de doelbron die u wilt openen

  * Als u deze bron-id's wilt vinden, controleert u de [Azure-services die Azure AD ondersteunen.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

  > [!IMPORTANT]
  > Deze resource-id moet exact overeenkomen met de waarde die Azure AD verwacht, inclusief eventuele vereiste trailing slashes.

  Deze resource-id is ook dezelfde waarde die u later gebruikt in de eigenschap **Doelgroep** wanneer u [uw functieactie instelt om de door het systeem toegewezen identiteit te gebruiken.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

Nu u Azure AD-verificatie instellen voor uw functie-app.

1. Zoek en selecteer uw functie-app in de [Azure-portal.](https://portal.azure.com)

1. Selecteer in het deelvenster Functie-app de optie **Platformfuncties**. Selecteer **onder Netwerken**de optie Verificatie / **Autorisatie**.

   ![Verificatie- en autorisatie-instellingen weergeven](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Wijzig de instelling **Voor verificatie van de appservice** in **Aan**. Selecteer **Aanmelden met Azure Active Directory**als u actie wilt uitvoeren wanneer de lijst met aanvragen niet is **geverifieerd.** Selecteer Azure **Active Directory**onder **Verificatieproviders**.

   ![Verificatie inschakelen met Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Voer in het deelvenster **Azure Active Directory-instellingen** de volgende stappen uit:

   1. **Beheermodus** instellen op **Geavanceerd**.

   1. Voer in de eigenschap **Client ID** de object-id in voor de door de logische app toegewezen identiteit.

   1. Voer in de eigenschap `https://sts.windows.net/` Url van de **uitgever** de URL in en sluit de directory-id van de Azure AD-tenant toe.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Voer in de eigenschap **Toegestane tokendoelgroepen** de resource-id in voor de doelbron die u wilt openen.

      Deze resource-id is dezelfde waarde die u later gebruikt in de eigenschap **Doelgroep** wanneer u [uw functieactie instelt om de door het systeem toegewezen identiteit te gebruiken.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

   Op dit moment lijkt uw versie op dit voorbeeld:

   ![Azure Active Directory-verificatie-instellingen](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Wanneer u gereed bent, selecteert u **OK**.

1. Ga terug naar de Logic App Designer en volg de [stappen om de toegang met de beheerde identiteit te verifiëren.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic Apps-connectors](../connectors/apis-list.md)
