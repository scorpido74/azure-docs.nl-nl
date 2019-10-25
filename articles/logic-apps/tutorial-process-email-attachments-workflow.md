---
title: Taken automatiseren met meerdere Azure-Services-Azure Logic Apps
description: 'Zelf studie: automatische werk stromen maken voor het verwerken van e-mail berichten met Azure Logic Apps, Azure Storage en Azure Functions'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 52c9a23e3e00075e934b9f9f22a835090e02f1b9
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820106"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Zelf studie: taken automatiseren voor het verwerken van e-mail berichten met behulp van Azure Logic Apps, Azure Functions en Azure Storage

Azure Logic Apps helpt u om uw werkstromen te automatiseren en om gegevens te integreren in Azure-services, Microsoft-services, andere SaaS-apps (software als een service) en on-premises systemen. In deze zelfstudie leert u hoe u een [logische app](../logic-apps/logic-apps-overview.md) bouwt die binnenkomende e-mails en eventuele bijlagen verwerkt. Deze logische app analyseert de inhoud van de e-mails, bewaart de inhoud in een Azure-opslag en verzendt een melding om die inhoud te bekijken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een [Azure-opslag](../storage/common/storage-introduction.md) en Storage Explorer instellen om opgeslagen e-mails en bijlagen te doorzoeken.
> * Maak een [Azure-functie](../azure-functions/functions-overview.md) waarmee HTML uit e-mailberichten wordt verwijderd. Deze zelfstudie bevat de code die u voor deze functie kunt gebruiken.
> * Een lege, logische app maken.
> * Voeg een trigger toe waarmee e-mailberichten op bijlagen worden gecontroleerd.
> * Voeg een voorwaarde toe waarmee wordt gecontroleerd of e-mails bijlagen bevatten.
> * Voeg een actie toe waarmee de Azure-functie wordt aangeroepen als een e-mail bijlagen bevat.
> * Voeg een actie toe waarmee opslagblobs voor e-mails en bijlagen worden gemaakt.
> * Voeg een actie toe waarmee e-mailmeldingen worden verzonden.

Wanneer u bent klaar, ziet uw logische app eruit als deze werkstroom op hoog niveau:

![Een voltooide logische app op hoog niveau](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een e-mailaccount van een e-mailprovider die door Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](https://docs.microsoft.com/connectors/).

  Deze logische app maakt gebruik van een Office 365 Outlook-account. Als u een ander e-mailaccount gebruikt, blijven de algemene stappen gelijk, maar uw gebruikersinterface kan er iets anders uitzien.

* Download en installeer de [gratis Microsoft Azure Storage Explorer](https://storageexplorer.com/). Dit hulpprogramma help u om te controleren of uw opslagcontainer correct is ingesteld.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

## <a name="set-up-storage-to-save-attachments"></a>De opslag instellen om bijlagen te bewaren

U kunt binnenkomende e-mails en bijlagen als blobs opslaan in een [Azure-opslagcontainer](../storage/common/storage-introduction.md).

1. Voordat u een opslag container kunt maken, [maakt u een opslag account](../storage/common/storage-quickstart-create-account.md) met deze instellingen op het tabblad **basis beginselen** in de Azure portal:

   | Instelling | Waarde | Beschrijving |
   |---------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |  
   | **Resourcegroep** | <*Azure-resource-group*> | De naam van de [Azure-resourcegroep](../azure-resource-manager/resource-group-overview.md) die wordt gebruikt om verwante resources te organiseren en te beheren. In dit voor beeld wordt ' LA-zelf studie-RG ' gebruikt. <p>**Opmerking:** resourcegroepen bestaan binnen een bepaalde regio. Hoewel de items in deze zelfstudie mogelijk niet in alle regio's beschikbaar zijn, dient u, wanneer mogelijk, dezelfde regio te gebruiken. |
   | **Naam van opslagaccount** | <*Azure-Storage-account-name* > | De naam van uw opslag account, die 3-24 tekens moet hebben en alleen kleine letters en cijfers kan bevatten. In dit voor beeld wordt ' attachmentstorageacct ' gebruikt. |
   | **Locatie** | <*Azure-regio*> | De regio waar informatie over uw opslag account moet worden opgeslagen. In dit voor beeld wordt ' West US ' gebruikt. |
   | **Prestaties** | Standard | Deze instelling bepaalt de gegevenstypen die worden ondersteund en de media die moeten worden opgeslagen. Zie [Typen opslagaccounts](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Type account** | Algemeen doel | Het [type opslagaccount](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replicatie** | Lokaal redundante opslag (LRS) | Deze instelling bepaalt hoe uw gegevens worden gekopieerd, opgeslagen, beheerd en gesynchroniseerd. Zie [lokaal redundante opslag (LRS): lage kosten voor gegevens redundantie voor Azure Storage](../storage/common/storage-redundancy-lrs.md). |
   | **Access-laag (standaard)** | Behoud de huidige instelling. |
   ||||

   Op het tabblad **Geavanceerd** selecteert u deze instelling:

   | Instelling | Waarde | Beschrijving |
   |---------|-------|-------------|
   | **Veilige overdracht vereist** | Uitgeschakeld | Deze instelling bepaalt de beveiliging die nodig is voor het aanvragen van verbindingen. Zie [Require secure transfer](../storage/common/storage-require-secure-transfer.md) (veilige overdracht vereist). |
   ||||

   U kunt ook [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) of [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md) gebruiken om uw opslagaccount te maken.

1. Wanneer u klaar bent, selecteert u **controleren + maken**.

1. Nadat Azure uw opslag account heeft geïmplementeerd, gaat u naar uw opslag account en haalt u de toegangs sleutel voor het opslag account op:

   1. In het menu van uw opslagaccount selecteert u onder het kopje **Instellingen** **Toegangssleutels**.

   1. Kopieer de naam van uw opslag account en **key1**, en sla deze waarden op een veilige plek op.

      ![Kopieer de naam en de sleutel van de opslagaccount en sla deze op](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   U kunt ook [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) of [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list) gebruiken om de toegangssleutel van uw opslagaccount op te halen.

1. Maak een Blob Storage-container voor uw e-mailbijlagen.

   1. Selecteer **Overzicht** in uw opslagaccountmenu. Onder **Services**selecteert u **containers**.

      ![Blob Storage-container toevoegen](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Nadat de pagina **Containers** opent in de werkbalk, selecteert u **Container**.

   1. Onder **nieuwe container**voert u `attachments` in als container naam. Selecteer onder **openbaar toegangs niveau** **container (anonieme lees toegang voor containers en blobs)**  > **OK**.

      Wanneer u klaar bent, vindt u uw opslagcontainer in uw opslagaccount hier in Azure Portal:

      ![Voltooide opslagcontainer](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Als u een opslag container wilt maken, kunt u ook [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer) of [Azure cli](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)gebruiken.

Koppel vervolgens Storage Explorer aan uw opslagaccount.

## <a name="set-up-storage-explorer"></a>Storage Explorer instellen

Koppel nu Storage Explorer aan uw opslagaccount, zodat u kunt bevestigen dat uw logische app bijlagen correct als blobs kan opslaan in uw opslagcontainer.

1. Start Microsoft Azure Storage Explorer.

   Storage Explorer vraagt u om een verbinding met uw opslagaccount.

1. Selecteer in het deel venster **verbinding maken met Azure Storage** de optie **een naam en sleutel voor het opslag account gebruiken** > **volgende**.

   ![Storage Explorer - verbinding maken met opslagaccount](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Als er geen prompt wordt weer gegeven, selecteert u op de Storage Explorer-werk balk **een account toevoegen**.

1. Geef onder **weergave naam**een beschrijvende naam op voor de verbinding. Onder **Accountnaam** geeft u de naam op van uw opslagaccount. Geef onder **account sleutel**de toegangs sleutel op die u eerder hebt opgeslagen en selecteer **volgende**.

1. Bevestig de verbindings gegevens en selecteer vervolgens **verbinding maken**.

   Storage Explorer maakt de verbinding en toont uw opslag account in het venster Verkenner onder **lokale & gekoppeld** > - **opslag accounts**.

1. Als u uw Blob Storage-container wilt vinden, vouwt u onder **opslag accounts**uw opslag account, dat hier **attachmentstorageacct** is, uit en vouwt u **BLOB-containers** uit waar u de **bijlagen** container vindt, bijvoorbeeld:

   ![Storage Explorer - opslagcontainer zoeken](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Maak vervolgens een [Azure-functie](../azure-functions/functions-overview.md) waarmee HTML uit binnenkomende e-mailberichten wordt verwijderd.

## <a name="create-function-to-clean-html"></a>Een functie maken om HTML te verwijderen

Gebruik nu het codefragment in deze stappen om een Azure-functie te maken waarmee HTML uit binnenkomende e-mailberichten wordt verwijderd. Op die manier is de inhoud van de e-mail schoner en eenvoudiger te verwerken. U kunt deze functie dan aanroepen vanaf uw logische app.

1. Voordat u deze functie kunt maken, [maakt u een functie-app](../azure-functions/functions-create-function-app-portal.md) met de volgende instellingen:

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Naam van app** | <*functie-app-naam* > | De naam van de functie-app, die wereld wijd uniek moet zijn in Azure. In dit voor beeld wordt CleanTextFunctionApp al gebruikt. Geef dus een andere naam op, bijvoorbeeld ' MyCleanTextFunctionApp-<*your-name*> ' |
   | **Abonnement** | <*your-Azure-subscription-name*> | Hetzelfde Azure-abonnement dat u eerder hebt gebruikt |
   | **Resourcegroep** | LA-Tutorial-RG | Dezelfde Azure-resourcegroep die u eerder hebt gebruikt |
   | **Besturingssysteem** | <*uw> voor uw besturings systeem* | Selecteer het besturings systeem dat de programmeer taal van uw favoriete functie ondersteunt. Voor dit voor beeld selecteert u **Windows**. |
   | **Hostingabonnement** | Verbruiksabonnement | Deze instelling bepaalt hoe de resources worden toegewezen en geschaald, bijvoorbeeld de rekenkracht, om uw functie-app uit te voeren. Bekijk [Vergelijking van hostingabonnementen](../azure-functions/functions-scale.md). |
   | **Locatie** | VS - west | Dezelfde regio die u eerder hebt gebruikt |
   | **Runtime stack** | Voorkeurstaal | Selecteer een runtime die de programmeer taal van uw favoriete functie ondersteunt. Selecteer **.net** voor C# en F# functions. |
   | **Storage** | cleantextfunctionstorageacct | Maak een opslagaccount voor uw functie-app. Gebruik alleen kleine letters en cijfers. <p>**Opmerking:** Dit opslag account bevat uw functie-apps en wijkt af van het eerder gemaakte opslag account voor e-mail bijlagen. |
   | **Application Insights** | Uitschakelen | Hiermee schakelt u toepassings bewaking in met [Application Insights](../azure-monitor/app/app-insights-overview.md), maar voor deze zelf studie selecteert u **uitschakelen** > **Toep assen**. |
   ||||

   Als uw functie-app niet automatisch wordt geopend na de implementatie, zoekt en selecteert u in het zoekvak van [Azure Portal](https://portal.azure.com) **functie-app**. Selecteer onder **functie-app**de functie-app.

   ![Functie-app selecteren](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Anders opent Azure automatisch uw functie-app, zoals hier wordt weergegeven:

   ![Gemaakte functie-app](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   U kunt ook [Azure cli](../azure-functions/functions-create-first-azure-function-azure-cli.md)-of [Power shell-en Resource Manager-sjablonen](../azure-resource-manager/resource-group-template-deploy.md)gebruiken om een functie-app te maken.

1. Vouw in de lijst **functie-apps** de functie-app uit, als deze nog niet is uitgevouwen. Selecteer **functies**onder uw functie-app. Selecteer op de functiewerkbalk **Nieuwe functie**.

   ![Nieuwe functie maken](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. Onder **Kies hieronder een sjabloon of ga naar de Snelstartgids**, selecteert u de sjabloon **http-trigger** .

   ![HTTP-trigger sjabloon selecteren](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure maakt een functie met een taalspecifieke sjabloon voor een HTTP-geactiveerde functie.

1. In het deelvenster **Nieuwe functie** voert u `RemoveHTMLFunction` in onder **Naam**. Laat het **autorisatie niveau** ingesteld op **functie**en selecteer **maken**.

   ![Een naam voor de functie opgeven](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Nadat de editor wordt geopend, vervangt u de sjablooncode door deze voorbeeldcode, waarmee de HTML wordt gewist en de resultaten worden geretourneerd aan de aanroeper:

   ```CSharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. Selecteer **Opslaan** als u klaar bent. Als u de functie wilt testen, selecteert u op de rechter rand van de editor onder het pijl ( **<** ) pictogram **testen**.

   ![Open het testpaneel](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. Voer in het deel venster **test** onder **hoofd tekst van aanvraag**deze regel in en selecteer **uitvoeren**.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Uw functie testen](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   In het venster **Uitvoer** wordt het resultaat van de functie weergegeven:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Nadat u hebt gecontroleerd of uw functie werkt, maakt u uw logische app. In deze zelfstudie wordt laten zien hoe u een functie maakt die HTML uit e-mails wist, maar Logic Apps biedt ook een connector waarmee **HTML in tekst** wordt omgezet.

## <a name="create-your-logic-app"></a>Uw logische app maken

1. Ga naar de start pagina van Azure en selecteer **Logic apps**in het zoekvak.

   ![Zoek en selecteer ' Logic Apps '](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. Selecteer op de pagina **Logic apps** **toevoegen**.

   ![Nieuwe logische app toevoegen](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. Onder **Logische app maken** geeft u informatie op over uw logische app zoals hier wordt weergegeven. Wanneer u klaar bent, selecteert u **maken**.

   ![Informatie over logische app opgeven](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Naam** | LA-ProcessAttachment | De naam voor uw logische app |
   | **Abonnement** | <*your-Azure-subscription-name*> | Hetzelfde Azure-abonnement dat u eerder hebt gebruikt |
   | **Resourcegroep** | LA-Tutorial-RG | Dezelfde Azure-resourcegroep die u eerder hebt gebruikt |
   | **Locatie** | VS - west | Dezelfde regio die u eerder hebt gebruikt |
   | **Log Analytics** | Uit | Voor deze zelf studie selecteert u de instelling **uit** . |
   ||||

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u in de Azure-werk balk het pictogram meldingen en selecteert **u Ga naar resource**.

   ![Selecteer in de lijst met meldingen van Azure de optie Ga naar resource](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Nadat de Logic Apps Designer wordt geopend, ziet u een pagina met een introductie video en sjablonen voor veelgebruikte logische app-patronen. Kies onder **Sjablonen** de optie **Lege logische app**.

   ![Sjabloon voor lege logische app selecteren](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Voeg vervolgens een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) toe die binnenkomende e-mails met bijlagen afwacht. Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer nieuwe gegevens voldoen aan een bepaalde voorwaarde. Bekijk [Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie.

## <a name="monitor-incoming-email"></a>Binnenkomende e-mail controleren

1. Voer op de ontwerper in het zoekvak `when new email arrives` in als uw filter. Selecteer deze trigger voor uw e-mailprovider: **Wanneer er nieuwe e-mail binnenkomt - <*uw e-mailprovider*>**

   Bijvoorbeeld:

   ![Selecteer deze trigger voor e-mailprovider: 'Wanneer er een nieuwe e-mail binnenkomt'](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.

   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u om uw referenties wordt gevraagd, meldt u zich aan bij uw e-mailaccount, zodat Logic Apps verbinding met uw e-mailaccount kan maken.

1. Geef nu de criteria op die de trigger gebruikt om nieuwe e-mails te filteren.

   1. Geef de instellingen op die hieronder worden beschreven voor het controleren van e-mail berichten.

      ![Geef map, interval en frequentie voor het controleren van e-mails op](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Map** | Postvak IN | De te controleren e-mailmap |
      | **Heeft bijlage** | Ja | Ontvang alleen e-mails met bijlagen. <p>**Opmerking:** de trigger verwijdert geen e-mails van uw account, maar controleert alleen op nieuwe berichten en verwerkt alleen e-mails die overeenkomen met het onderwerpfilter. |
      | **Bijlagen opnemen** | Ja | Haalt de bijlagen op als invoer voor uw werkstroom, in plaats van dat er alleen wordt gecontroleerd op bijlagen. |
      | **Interval** | 1 | Het aantal intervallen dat tussen controles moet worden gewacht |
      | **Frequentie** | Minuut | De tijdseenheid voor elk interval tussen controles |
      ||||
  
   1. Selecteer in de lijst **nieuwe para meter toevoegen** de optie **onderwerps filter**.

   1. Nadat het vak **onderwerps filter** in de actie wordt weer gegeven, geeft u het onderwerp op zoals hier wordt weer gegeven:

      | Instelling | Waarde | Beschrijving |
      | ------- | ----- | ----------- |
      | **Onderwerpfilter** | `Business Analyst 2 #423501` | De tekst die moet worden gezocht in het onderwerp van de e-mail |
      ||||

1. Als u de details van de trigger voorlopig wilt verbergen, klikt u in de titelbalk van de trigger.

   ![Shape samenvouwen om details te verbergen](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

   Uw logische app is nu live, maar kan niets anders doen dan uw e-mails controleren. Voeg vervolgens een voorwaarde toe waarmee criteria worden opgegeven, zodat de werkstroom door blijft gaan.

## <a name="check-for-attachments"></a>Controleren op bijlagen

Voeg nu een voorwaarde toe waarmee er alleen e-mails met bijlagen worden geselecteerd.

1. Selecteer **nieuwe stap**onder de trigger.

   !["Nieuwe stap"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. Voer in het zoekvak onder **Kies een actie**`condition`in. Selecteer deze actie: **voor waarde**

   ![Selecteer ' voor waarde '](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Geef de voorwaarde een naam met een betere beschrijving. Selecteer op de titel balk van de voor waarde de knop met weglatings tekens ( **...** ) > **naam wijzigen**.

      ![Naam voorwaarde wijzigen](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Verander de naam van uw voorwaarde in deze beschrijving: `If email has attachments and key subject phrase`

1. Voer een voorwaarde in waarmee er op e-mails met bijlagen wordt gecontroleerd.

   1. Klink in het linkervakje in de eerste rij onder **En**. Selecteer vanuit de lijst met dynamische inhoud de eigenschap **Heeft bijlage**.

      ![Voorwaarde bouwen](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. In het middelste vakje behoud u **is gelijk aan** voor de operator.

   1. Voer in het rechtervakje **waar** in als waarde, zodat de eigenschapswaarde **Heeft bijlage** wordt vergeleken met de trigger.

      ![Voorwaarde bouwen](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Als beide waarden gelijk zijn, bevat de e-mail ten minste één bijlage, wordt er aan de voorwaarde voldaan en gaat de werkstroom verder.

   In de onderliggende definitie van uw logische app, die u in het venster Code bewerken kunt bekijken, ziet de waarde eruit als in het volgende voorbeeld:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Sla uw logische app op. Selecteer **Opslaan**op de werk balk van de ontwerp functie.

### <a name="test-your-condition"></a>Uw verbinding testen

Test nu of de voorwaarde correct werkt:

1. Als uw logische app nog niet wordt uitgevoerd, selecteert u **uitvoeren** op de werk balk van de ontwerp functie.

   Met deze stap wordt u logische app handmatig gestart zonder dat u hoeft te wachten tot de door u opgegeven interval is verstreken. Er gebeurt echter niets totdat het test-e-mailbericht in uw postvak binnenkomt.

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van de e-mail bevat de tekst die u hebt opgegeven in het **Onderwerpfilter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mailbericht heeft één bijlage. Maak voor nu een leeg tekstbestand en voeg dat als bijlage toe aan uw e-mail.

   Wanneer de e-mail binnenkomt, controleert uw logische app op bijlagen en op de opgegeven onderwerptekst. Als er aan de voorwaarde wordt voldaan, wordt de trigger geactiveerd en maakt de Logic Apps-engine een exemplaar van een logische app en wordt de werkstroom in gang gezet.

1. Als u wilt controleren of de trigger is geactiveerd en de logische app met succes is uitgevoerd, selecteert u **overzicht**in het menu van de logische app.

   ![Controleer trigger en uitvoergeschiedenis](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Bekijk [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Problemen met uw logische app oplossen) als uw logische app niet is geactiveerd of uitgevoerd, hoewel de trigger wel succesvol is geactiveerd.

Bepaal vervolgens de te nemen acties voor de vertakking **Indien waar**. Als u uw e-mail met eventuele bijlagen wilt opslaan, wist u de HTML uit de hoofdtekst van de e-mail en maakt u blobs in de opslagcontainer voor de e-mail en de bijlagen.

> [!NOTE]
> Uw logische app hoeft niets te doen voor de vertakking **Indien onwaar** als een e-mail geen bijlagen heeft. Als extra oefening na deze zelfstudie, kunt u een geschikte actie toevoegen voor de vertakking **Indien onwaar**.

## <a name="call-removehtmlfunction"></a>RemoveHTMLFunction aanroepen

Met deze stap wordt uw eerder gemaakte Azure-functie toegevoegd aan uw logische app en wordt de tekstinhoud van de e-mail overgezet van de trigger naar uw functie.

1. Selecteer in het menu van de logische app **Logic App Designer**. Selecteer **een actie toevoegen**in de vertakking **Indien waar** .

   ![In vertakking 'indien waar', actie toevoegen](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Zoek in het zoekvenster naar 'azure-functies' en selecteer deze actie: **Een Azure-functie kiezen - Azure Functions**

   ![Selecteer actie voor 'Een Azure-functie kiezen'](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Selecteer uw eerder gemaakte functie-app, die in dit voor beeld `CleanTextFunctionApp` is:

   ![Selecteer uw Azure-functie-app](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Selecteer nu uw functie: **RemoveHTMLFunction**

   ![Selecteer uw Azure-functie](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Verander de naam van de functievorm in deze beschrijving: `Call RemoveHTMLFunction to clean email body`

1. Geef nu de invoer op die uw functie moet verwerken.

   1. Voer bij **Aanvraagtekst** deze tekst in met een spatie aan het einde:

      `{ "emailBody":`

      Terwijl u in de volgende stappen aan deze invoer werkt, verschijnt er een fout over ongeldige JSON, totdat uw invoer correct is geformatteerd als JSON. Wanneer u deze functie eerder hebt getest, gebruikte de voor deze functie opgegeven invoer JavaScript Object Notation (JSON). Diezelfde taal moet daarom ook door de aanvraagtekst worden gebruikt.

      Wanneer de cursor zich binnen het venster **Aanvraagtekst** bevindt, verschijnt de lijst met dynamische inhoud, zodat u beschikbare eigenschapswaarden van vorige acties kunt selecteren.

   1. Uit de lijst met dynamische inhoud, onder **Wanneer er een nieuwe e-mail binnenkomt**, selecteert u de eigenschap **Hoofdtekst**. Vergeet niet om na deze eigenschap de sluitende accolade toe te voegen: `}`

      ![Geef de aanvraagtekst op die aan de functie wordt doorgegeven](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Wanneer u klaar bent, ziet de invoer voor uw functie eruit als in dit voorbeeld:

   ![Afgemaakte aanvraagtekst om door te geven aan uw functie](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Sla uw logische app op.

Voeg vervolgens een actie toe waarmee er een blob wordt gemaakt in uw opslagcontainer, zodat u de hoofdtekst van de e-mail kunt opslaan.

## <a name="create-blob-for-email-body"></a>Een blob maken voor de hoofdtekst van de e-mail

1. Selecteer **een actie toevoegen**in het blok **Indien waar** en onder uw Azure-functie.

1. Voer `create blob` als uw filter in het zoekvak in en selecteer deze actie: **Blob maken**

   ![Voeg de actie toe om een blob voor de hoofdtekst van een e-mail te maken](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Maak verbinding met uw opslagaccount met de instellingen die hier worden weergegeven en beschreven. Als u gereed bent, selecteert u **Maken**.

   ![Maak verbinding met opslagaccount](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Verbindingsnaam** | AttachmentStorageConnection | Een beschrijvende naam voor de verbinding |
   | **Opslagaccount** | attachmentstorageacct | De naam voor de opslagaccount die u eerder hebt gemaakt om bijlagen op te slaan |
   ||||

1. Verander de naam van de actie **Blob maken** in deze beschrijving: `Create blob for email body`

1. Geef in de actie **Blob maken** de volgende informatie op en selecteer de volgende velden om de blob te maken zoals wordt weergegeven en beschreven:

   ![Blobinformatie opgeven voor hoofdtekst van de e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Mappad** | /attachments | Het pad en de naam van de container die u eerder hebt gemaakt. Voor dit voorbeeld klikt u op het mappictogram en selecteert u de container '/bijlagen'. |
   | **Blobnaam** | Veld **Van** | Voor dit voorbeeld gebruikt u de naam van de verzender als de naam van de blob. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens het veld **Van** onder de actie **Wanneer er een nieuwe e-mail binnenkomt**. |
   | **Blobinhoud** | Veld **Inhoud** | Voor dit voorbeeld gebruikt u de HTML-vrije hoofdtekst van de e-mail als de blobinhoud. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens **Hoofdtekst** onder de actie **RemoveHTMLFunction aanroepen om de hoofdtekst van de e-mail op te schonen**. |
   ||||

   Wanneer u klaar bent, ziet de actie eruit als in dit voorbeeld:

   ![Actie 'Blob maken' voltooid](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Sla uw logische app op.

### <a name="check-attachment-handling"></a>Controleer de verwerking van bijlagen

Test nu of uw logische app e-mails verwerkt zoals u hebt opgegeven:

1. Als uw logische app nog niet wordt uitgevoerd, selecteert u **uitvoeren** op de werk balk van de ontwerp functie.

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van de e-mail bevat de tekst die u hebt opgegeven in het **Onderwerpfilter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mailbericht heeft ten minste één bijlage. U kunt nu slechts één leeg tekst bestand maken en dat bestand koppelen aan uw e-mail adres.

   * Uw e-mail adres bevat een aantal test inhoud in de hoofd tekst, bijvoorbeeld: `Testing my logic app`

   Bekijk [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Problemen met uw logische app oplossen) als uw logische app niet is geactiveerd of uitgevoerd, hoewel de trigger wel succesvol is geactiveerd.

1. Controleer of uw logische app het e-mailbericht in de juiste opslagcontainer heeft opgeslagen.

   1. Vouw in Storage Explorer **lokale & die zijn gekoppeld** > **opslag accounts** > **Attachmentstorageacct (sleutel)**  > **BLOB-containers** > **bijlagen**uit.

   1. Controleer de container **bijlagen** op het e-mailbericht.

      Op dit moment verschijnt alleen de e-mail in de container, omdat de logische app de bijlagen nog niet verwerkt.

      ![Controleer Storage Explorer op opgeslagen e-mail](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Wanneer u klaar bent, verwijdert u de e-mail in Storage Explorer.

1. U kunt ook een e-mail verzenden die niet voldoet aan de criteria om de vertakking **Indien onwaar** te testen, maar deze doet momenteel nog niets.

Voeg vervolgens een lus toe om alle e-mailbijlagen te verwerken.

## <a name="process-attachments"></a>Bijlagen verwerken

Voeg de lus **Voor elke** toe aan de werkstroom van uw logische app om elke bijlage in de e-mail te verwerken.

1. Selecteer **een actie toevoegen**onder de vorm **Blob maken voor de hoofd tekst van de e-mail** .

   ![Lus 'voor elke' toevoegen](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. Voer onder **Kies een actie**in het zoekvak `for each` in als uw filter en selecteer deze actie: **voor elke**

   ![Selecteer voor elke](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Verander de naam van uw lus in deze beschrijving: `For each email attachment`

1. Geef nu de gegevens op die de lus gaat verwerken. Klik binnen het venster **Een uitvoer van vorige stappen selecteren**, zodat de lijst met dynamische inhoud opent, en selecteer **Bijlagen**.

   ![Selecteer 'Bijlagen'](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Het veld **Bijlagen** geeft een matrix door die alle bijlagen bevat die in een e-mail zijn bijgevoegd. De lus **Voor elke** herhaalt acties voor elk item dat met de matrix wordt ingevoerd.

1. Sla uw logische app op.

Vervolgens voegt u de actie toe waarmee elke bijlage als blob in uw opslagcontainer **bijlagen** wordt opgeslagen.

## <a name="create-blob-for-each-attachment"></a>Een blob maken voor elke bijlage

1. Selecteer **een actie toevoegen** in de lus **voor elke e-mail bijlage** zodat u de taak die u wilt uitvoeren op elke gevonden bijlage kunt opgeven.

   ![Actie aan lus toevoegen](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Voer `create blob` als uw filter in het zoekvak in en selecteer vervolgens deze actie: **Create BLOB**

   ![Actie toevoegen om blob te maken](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Verander de naam van de actie **Blob maken 2** in deze beschrijving: `Create blob for each email attachment`

1. Geef in de actie **Blob maken voor elke e-mailbijlage** de volgende informatie op en selecteer de eigenschappen voor elke blob die u wilt maken zoals wordt weergegeven en beschreven:

   ![Blobinformatie opgeven](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Instelling | Waarde | Beschrijving |
   | ------- | ----- | ----------- |
   | **Mappad** | /attachments | Het pad en de naam van de container die u eerder hebt gemaakt. Voor dit voorbeeld klikt u op het mappictogram en selecteert u de container '/bijlagen'. |
   | **Blobnaam** | Veld **Naam** | Voor dit voorbeeld gebruikt u de naam van de bijlage als de naam van de blob. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens het veld **Naam** onder de actie **Wanneer er een nieuwe e-mail binnenkomt**. |
   | **Blobinhoud** | Veld **Inhoud** | Gebruik voor dit voorbeeld het veld **Inhoud** als de blobinhoud. Klik binnen dit venster, zodat de lijst met dynamische inhoud verschijnt, en selecteer vervolgens **Inhoud** onder de actie **Wanneer er een nieuwe e-mail binnenkomt**. |
   ||||

   Wanneer u klaar bent, ziet de actie eruit als in dit voorbeeld:

   ![Actie 'Blob maken' voltooid](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Sla uw logische app op.

### <a name="check-attachment-handling"></a>Controleer de verwerking van bijlagen

Test vervolgens of uw logische app de bijlagen verwerkt zoals u hebt opgegeven:

1. Als uw logische app nog niet wordt uitgevoerd, selecteert u **uitvoeren** op de werk balk van de ontwerp functie.

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van uw e-mail bericht bevat de tekst die u hebt opgegeven in de eigenschap **subject filter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mailbericht heeft ten minste twee bijlagen. Maak voor nu twee lege tekstbestanden en voeg die als bijlagen toe aan uw e-mail.

   Bekijk [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Problemen met uw logische app oplossen) als uw logische app niet is geactiveerd of uitgevoerd, hoewel de trigger wel succesvol is geactiveerd.

1. Controleer of uw logische app het e-mailbericht en de bijlagen in de juiste opslagcontainer heeft opgeslagen.

   1. Vouw in Storage Explorer **lokale & die zijn gekoppeld** > **opslag accounts** > **Attachmentstorageacct (sleutel)**  > **BLOB-containers** > **bijlagen**uit.

   1. Controleer de container **bijlagen** op zowel de e-mail als op de bijlagen.

      ![Controleer op opgeslagen e-mails en bijlagen](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Wanneer u klaar bent, verwijdert u de e-mail en de bijlage in Storage Explorer.

Voeg vervolgens een actie toe, zodat uw logische app een e-mail verzendt om melding te maken van de bijlagen.

## <a name="send-email-notifications"></a>E-mailmeldingen verzenden

1. In de vertakking **Indien waar** , onder de lus **voor elke e-mail bijlage** , selecteert u **een actie toevoegen**.

   ![Actie toevoegen onder lus 'voor elke'](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Voer in het zoekvak `send email` in als uw filter en selecteer vervolgens de actie ' e-mail verzenden ' voor uw e-mail provider.

   Als u de lijst met acties wilt filteren op een bepaalde service, kunt u eerst de connector selecteren.

   ![Selecteer de actie 'e-mail verzenden' voor uw e-mailprovider](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Voor werk- of schoolaccounts van Azure selecteert u Outlook van Office 365.

   * Selecteer Outlook.com voor persoonlijke Microsoft-accounts.

1. Als u om uw referenties wordt gevraagd, meldt u zich aan bij uw e-mailaccount, zodat Logic Apps een verbinding met uw e-mailaccount kan maken.

1. Verander de naam van de actie **Een e-mail verzenden** in deze beschrijving: `Send email for review`

1. Geef de informatie voor deze actie op en selecteer de velden die u wilt opnemen in de e-mail zoals die wordt weergegeven en beschreven. Als u lege regels wilt toevoegen in een invoervak, drukt u op Shift + Enter.

   ![E-mailmelding verzenden](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Als u een verwacht veld niet kunt vinden in de lijst met dynamische inhoud, selecteert u **meer weer geven** naast **wanneer er een nieuwe e-mail binnenkomt**.

   | Instelling | Waarde | Opmerkingen |
   | ------- | ----- | ----- |
   | **Aan** | <*recipient-email-address*> | Voor testdoeleinden kunt u uw eigen e-mailadres gebruiken. |
   | **Onderwerp**  | ```ASAP - Review applicant for position:``` **Onderwerp** | Het e-mailonderwerp dat u wilt opnemen. Klik in dit venster, voer de voorbeeldtekst in en selecteer vanuit de lijst met dynamische inhoud het veld **Onderwerp** onder **Wanneer er een nieuwe e-mail binnenkomt**. |
   | **Hoofdtekst** | ```Please review new applicant:``` <p>```Applicant name:``` **Van** <p>```Application file location:``` **Pad** <p>```Application email content:``` **Hoofdtekst** | De hoofdtekst van het e-mailbericht. Klik in dit venster, voer de voorbeeldtekst in en selecteer de volgende velden uit de lijst met dynamische inhoud: <p>- Het veld **Van** onder het kopje **Wanneer er een nieuwe e-mail binnenkomt** </br>- Het veld **Pad** onder het kopje **Blob maken voor de hoofdtekst van de e-mail** </br>- Het veld **Hoofdtekst** onder het kopje **RemoveHTMLFunction aanroepen om de hoofdtekst van de e-mail op te schonen** |
   ||||

   > [!NOTE]
   > Als u een veld selecteert waarin een matrix is opgeslagen, zoals het veld **Inhoud**, wat een matrix is die bijlagen bevat, wordt in de ontwerpfunctie automatisch een lus 'Voor elke' toegevoegd rond de actie die naar het veld verwijst. Op die manier kan de actie voor elk matrixitem worden uitgevoerd. Als u de lus wilt verwijderen, verwijdert u het veld voor de matrix, verplaatst u de verwijzende actie naar buiten de lus, selecteert u de weglatings tekens ( **...** ) op de titel balk van de lus en selecteert u **verwijderen**.

1. Sla uw logische app op.

Test nu uw logische app, die er nu uitziet als in dit voorbeeld:

![Voltooide logische app](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Uw logische app uitvoeren

1. Verstuur een e-mail naar uzelf die aan de volgende criteria voldoet:

   * Het onderwerp van uw e-mail bericht bevat de tekst die u hebt opgegeven in de eigenschap **subject filter** van de trigger: `Business Analyst 2 #423501`

   * Uw e-mail heeft een of meer bijlagen. U kunt een leeg tekstbestand uit uw vorige tests opnieuw gebruiken. Voeg een cv toe voor een realistischer scenario.

   * De hoofdtekst van de e-mail bevat deze tekst, die u kunt kopiëren en plakken:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Voer uw logische app uit. Als dit succesvol is, verzendt uw logische app u een e-mailbericht dat op dit voorbeeld lijkt:

   ![E-mailmelding van logische app](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Als u geen een e-mailberichten ontvangt, controleert u de map met ongewenste e-mail. Het is mogelijk dat uw filter voor ongewenste e-mail dit soort e-mails in deze map zet. Als u niet zeker weet of uw logische app correct wordt uitgevoerd, kunt u [Problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md) raadplegen.

Gefeliciteerd, u hebt nu een logische app gemaakt en uitgevoerd die taken in verschillende Azure-services automatiseert en aangepaste code aanroept.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit voorbeeld niet meer nodig hebt, verwijdert u de resourcegroep die uw logische app en alle gerelateerde resources bevat.

1. Selecteer **Resourcegroepen** in het hoofdmenu van Azure. Selecteer in de lijst resource groepen de resource groep voor deze zelf studie. Selecteer in het deel venster **overzicht** de optie **resource groep verwijderen**.

   ![Resourcegroep van logische app verwijderen](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Wanneer het bevestigings venster wordt weer gegeven, voert u de naam van de resource groep in en selecteert u **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u door Azure-services, zoals Azure Storage en Azure Functions, te integreren een logische app gemaakt die e-mailbijlagen verwerkt en opslaat. U kunt nu meer leren over andere connectoren die u kunt gebruiken om logische apps te bouwen.

> [!div class="nextstepaction"]
> [Meer te weten komen over connectoren voor Logic Apps](../connectors/apis-list.md)
