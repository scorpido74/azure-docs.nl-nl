---
title: Beveiligde toegang en gegevens Azure Logic Apps
description: Schakel parameter invoer, HTTP-aanvraag triggers, uitvoerings geschiedenis, logische app-bewerkingen en verbindingen met andere services in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b1331865224d34f731dbd388cebdaec8b7c9fe7f
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264659"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Beveiligde toegang en gegevens in Azure Logic Apps

Voor het beheren van de toegang tot en het beveiligen van gegevens in Azure Logic Apps, kunt u de beveiliging op de volgende gebieden instellen:

* [Toegang tot HTTP-aanvraag triggers](#secure-triggers)
* [Toegang tot logische app-bewerkingen](#secure-operations)
* [Toegang tot de invoer en uitvoer van de uitvoerings geschiedenis](#secure-run-history)
* [Toegang tot parameter invoer](#secure-action-parameters)
* [Toegang tot services en systemen die worden aangeroepen vanuit Logic apps](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Toegang tot HTTP-aanvraag triggers

Wanneer uw logische app gebruikmaakt van een activering op basis van een HTTP-aanvraag, zoals de trigger [aanvraag](../connectors/connectors-native-reqres.md) of [webhook](../connectors/connectors-native-webhook.md) , kunt u de toegang beperken zodat alleen geautoriseerde clients uw logische app kunnen starten. Alle aanvragen die door een logische app worden ontvangen, zijn versleuteld en beveiligd met het Secure Sockets Layer-Protocol (SSL). 

Hier volgen de manieren waarop u de toegang tot dit trigger type kunt beveiligen:

* [Hand tekeningen voor gedeelde toegang genereren](#sas)
* [Binnenkomende IP-adressen beperken](#restrict-incoming-ip-addresses)
* [Azure Active Directory, OAuth of andere beveiliging toevoegen](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Hand tekeningen voor gedeelde toegang (SAS) genereren

Elk aanvraag eindpunt op een logische app heeft een [Shared Access Signature (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) in de URL van het eind punt, die de volgende indeling heeft:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Elke URL bevat een `sp`, `sv` en de query parameter `sig` zoals beschreven in deze tabel:

| Query parameter | Beschrijving |
|-----------------|-------------|
| `sp` | Hiermee geeft u de machtigingen op voor het gebruik van de toegestane HTTP-methoden. |
| `sv` | Hiermee geeft u de SAS-versie op die moet worden gebruikt voor het genereren van de hand tekening. |
| `sig` | Hiermee geeft u de hand tekening op die moet worden gebruikt voor het verifiëren van de toegang tot de trigger. Deze hand tekening wordt gegenereerd met behulp van de SHA256-algoritme met een geheime toegangs sleutel voor alle URL-paden en eigenschappen. Nooit beschikbaar of gepubliceerd, deze sleutel wordt versleuteld bewaard en opgeslagen met de logische app. Uw logische app machtigt alleen de triggers die een geldige hand tekening bevatten die is gemaakt met de geheime sleutel. |
|||

Zie de volgende secties in dit onderwerp voor meer informatie over het beveiligen van de toegang met Shared Access Signature:

* [Toegangs sleutels opnieuw genereren](#access-keys)
* [Url's voor verlopende call back maken](#expiring-urls)
* [Url's met een primaire of secundaire sleutel maken](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Toegangssleutels regenereren

Als u op elk gewenst moment een nieuwe beveiligde toegangs sleutel wilt genereren, gebruikt u de Azure REST API of Azure Portal. Alle eerder gegenereerde Url's die gebruikmaken van de oude sleutel, zijn ongeldig en hebben geen autorisatie meer om de logische app te activeren. De Url's die u na het opnieuw genereren hebt opgehaald, worden ondertekend met de nieuwe toegangs sleutel.

1. Open in de Azure Portal de logische app met de sleutel die u opnieuw wilt genereren.

1. Selecteer **toegangs sleutels**onder **instellingen**in het menu van de logische app.

1. Selecteer de sleutel die u opnieuw wilt genereren en voltooi het proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Url's voor verlopende call back maken

Als u de eind punt-URL voor een activering op basis van een HTTP-aanvraag met andere partijen deelt, kunt u call back-Url's genereren die specifieke sleutels gebruiken en verloop datums hebben. Op die manier kunt u sleutels naadloos draaien of de toegang beperken tot het activeren van uw logische app op basis van een specifieke tijds duur. Als u een verval datum voor een URL wilt opgeven, gebruikt u de [Logic Apps rest API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), bijvoorbeeld:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Neem in de hoofd tekst de `NotAfter`property op met behulp van een JSON-datum teken reeks. Deze eigenschap retourneert een call back-URL die alleen geldig is tot de datum en tijd van de @no__t 0.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Url's met een primaire of secundaire sleutel maken

Wanneer u call back-Url's voor op HTTP-aanvraag gebaseerde triggers genereert of lijsteert, kunt u de sleutel opgeven die moet worden gebruikt voor het ondertekenen van de URL. Als u een URL wilt genereren die is ondertekend met een specifieke sleutel, gebruikt u de [Logic Apps rest API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), bijvoorbeeld:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Neem in de hoofd tekst de eigenschap `KeyType` op als `Primary` of `Secondary`. Deze eigenschap retourneert een URL die is ondertekend met de opgegeven beveiligde sleutel.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Binnenkomende IP-adressen beperken

Naast Shared Access Signature, wilt u mogelijk de specifieke clients beperken die uw logische app kunnen aanroepen. Als u bijvoorbeeld het eind punt van de aanvraag beheert met behulp van Azure API Management, kunt u de logische app beperken tot het accepteren van aanvragen van het IP-adres voor het API Management exemplaar.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Binnenkomende IP-bereiken in Azure Portal beperken

1. Open in de Azure Portal uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen**onder **instellingen**in het menu van de logische app.

1. Selecteer **specifieke IP-bereiken**onder **configuratie van toegangs beheer** > **toegestane inkomende IP-adressen**.

1. Geef onder **IP-bereiken voor triggers**de IP-adresbereiken op die de trigger accepteert.

   Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x.* x. x

Als u wilt dat uw logische app alleen als een geneste logische app wordt geactiveerd, selecteert u **alleen andere Logic apps**in de lijst **toegestane binnenkomende IP-adressen** . Met deze optie wordt een lege matrix naar de logische app-resource geschreven. Op die manier kunnen alleen aanroepen van de Logic Apps-service (bovenliggende Logic apps) de geneste logische app activeren.

> [!NOTE]
> Ongeacht het IP-adres kunt u nog steeds een logische app uitvoeren die een op HTTP-aanvraag gebaseerde trigger heeft met behulp van `/triggers/<trigger-name>/run` via de Azure REST API of via API Management. Voor dit scenario is echter nog steeds verificatie vereist voor de Azure-REST API. Alle gebeurtenissen worden weer gegeven in het controle logboek van Azure. Zorg ervoor dat u de beleids regels voor toegangs beheer dienovereenkomstig instelt.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Binnenkomende IP-bereiken in Azure Resource Manager sjabloon beperken

Als u implementaties van logische apps automatiseert met behulp van een [Azure Resource Manager sjabloon](../logic-apps/logic-apps-create-deploy-template.md), kunt u de IP-adresbereiken opgeven met behulp van de sectie `accessControl` met de sectie `triggers` in de resource definitie van de logische app, bijvoorbeeld:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth of andere beveiliging toevoegen

Als u meer autorisatie protocollen wilt toevoegen aan uw logische app, kunt u overwegen [Azure API Management](../api-management/api-management-key-concepts.md)te gebruiken. Deze service biedt u de mogelijkheid om uw logische app beschikbaar te maken als een API en biedt uitgebreide bewaking, beveiliging, beleid en documentatie voor elk eind punt. API Management kunt een openbaar of persoonlijk eind punt beschikbaar maken voor uw logische app, die vervolgens Azure Active Directory, OAuth, certificaat of andere beveiligings standaarden kan gebruiken. Wanneer API Management een aanvraag ontvangt, stuurt de service de aanvraag naar uw logische app, worden ook de nodige trans formaties of beperkingen door lopen. Als u uw logische app alleen API Management activeren, kunt u de instellingen voor het binnenkomende IP-bereik van uw logische app gebruiken.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Toegang tot logische app-bewerkingen

U kunt alleen specifieke gebruikers of groepen toestaan specifieke bewerkingen uit te voeren, zoals het beheren, bewerken en weer geven van logische apps. Als u de machtigingen wilt beheren, gebruikt u op [Azure Role gebaseerde Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) om aangepaste of ingebouwde rollen toe te wijzen aan leden in uw Azure-abonnement:

* [Inzender van Logic](../role-based-access-control/built-in-roles.md#logic-app-contributor)apps: Hiermee kunt u logische apps beheren, maar u kunt de toegang niet wijzigen.

* [Logische app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar u kunt ze niet bewerken of bijwerken.

Als u wilt voor komen dat anderen uw logische app wijzigen of verwijderen, kunt u gebruikmaken van [Azure resource Lock](../azure-resource-manager/resource-group-lock-resources.md), waarmee wordt voor komen dat anderen productie resources kunnen wijzigen of verwijderen.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Toegang tot uitvoerings geschiedenis gegevens

Tijdens de uitvoering van een logische app worden alle gegevens tijdens de overdracht versleuteld met behulp van [Transport Layer Security (TLS)](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) en in [rust](../security/fundamentals/encryption-atrest.md). Wanneer de logische app wordt uitgevoerd, kunt u de geschiedenis voor die uitvoering bekijken, met inbegrip van de stappen die samen met de status, duur, invoer en uitvoer voor elke actie worden uitgevoerd. Deze uitgebreide details bieden inzicht in hoe uw logische app wordt uitgevoerd en waar u kunt beginnen met het oplossen van problemen die zich voordoen.

Wanneer u de uitvoerings geschiedenis van de logische app opent, wordt uw toegang door Logic Apps geverifieerd en vindt u koppelingen naar de invoer en uitvoer van de aanvragen en antwoorden in de uitvoering van uw logische app. Voor acties waarbij wacht woorden, geheimen, sleutels of andere gevoelige informatie worden verwerkt, wilt u echter voor komen dat anderen deze gegevens kunnen weer geven en gebruiken. Als uw logische app bijvoorbeeld een geheim krijgt van [Azure Key Vault](../key-vault/key-vault-overview.md) om te gebruiken bij het verifiëren van een http-actie, wilt u dat geheim verbergen in de weer gave.

Als u de toegang tot de invoer en uitvoer in de uitvoerings geschiedenis van de logische app wilt beheren, hebt u de volgende opties:

* De [toegang beperken op basis van het IP-adres bereik](#restrict-ip).

  Met deze optie kunt u de toegang tot de uitvoerings geschiedenis beveiligen op basis van de aanvragen van een specifiek IP-adres bereik.

* [Gegevens uit de uitvoerings geschiedenis verbergen door gebruik te maken van een donkere weer gegeven](#obfuscate).

  In veel triggers en acties kunt u de invoer, uitvoer of beide verbergen vanuit de uitvoerings geschiedenis van een logische app.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Toegang beperken op basis van IP-adres bereik

U kunt de toegang tot de invoer en uitvoer in de uitvoerings geschiedenis van de logische app beperken zodat alleen aanvragen van specifieke IP-adresbereiken die gegevens kunnen weer geven. Als u bijvoorbeeld wilt blok keren dat iedereen toegang heeft tot invoer en uitvoer, geeft u een IP-adres bereik op, bijvoorbeeld `0.0.0.0-0.0.0.0`. Alleen gebruikers met beheerders machtigingen kunnen deze beperking verwijderen. Dit biedt de mogelijkheid om just-in-time toegang te krijgen tot de gegevens van uw logische app. U kunt de IP-bereiken opgeven die u wilt beperken door gebruik te maken van de Azure Portal of in een Azure Resource Manager sjabloon die u gebruikt voor de implementatie van logische apps.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>IP-bereiken in Azure Portal beperken

1. Open in de Azure Portal uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen**onder **instellingen**in het menu van de logische app.

1. Selecteer **specifieke IP-bereiken**onder **configuratie van toegangs beheer** > **toegestane inkomende IP-adressen**.

1. Geef onder **IP-adresbereiken voor inhoud**de IP-adresbereiken op die toegang hebben tot inhoud van invoer en uitvoer. 

   Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x.* x. x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>IP-bereiken in Azure Resource Manager sjabloon beperken

Als u implementaties van logische apps automatiseert met behulp van een [Azure Resource Manager sjabloon](../logic-apps/logic-apps-create-deploy-template.md), kunt u de IP-adresbereiken opgeven met behulp van de sectie `accessControl` met de sectie `contents` in de resource definitie van de logische app, bijvoorbeeld:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Gegevens uit de uitvoerings geschiedenis verbergen door gebruik te maken van een donkere weer geven

Veel triggers en acties hebben instellingen voor het verbergen van invoer, uitvoer of beide vanuit de uitvoerings geschiedenis van een logische app. Hier volgen enkele [aandachtspunten om te controleren](#obfuscation-considerations) wanneer u deze instellingen gebruikt om deze gegevens te beveiligen.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Invoer en uitvoer in de ontwerp functie beveiligen

1. Als uw logische app niet al is geopend in de [Azure Portal](https://portal.azure.com), opent u de logische app in de ontwerp functie voor logische apps.

   ![Voorbeeld logische app openen](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. Op de trigger of actie waar u gegevens wilt beveiligen, selecteert u de knop met weglatings tekens ( **...** ) en selecteert u vervolgens **instellingen**.

   ![Instellingen openen](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Schakel **beveiligde invoer**, **beveiligde uitvoer**of beide in. Wanneer u klaar bent, selecteert u **Gereed**.

   ![Veilige invoer of uitvoer inschakelen](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   In de actie of de trigger wordt nu een vergrendelings pictogram weer gegeven in de titel balk.

   ![Vergrendelings pictogram op de titel balk](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   Tokens die beveiligde uitvoer van vorige acties vertegenwoordigen, worden ook vergrendelings pictogrammen weer gegeven. Wanneer u bijvoorbeeld een dergelijke uitvoer van de lijst met dynamische inhoud selecteert die u wilt gebruiken in een actie, toont dat token een vergrendelings pictogram.

   ![Uitvoer selecteren](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Wanneer de logische app wordt uitgevoerd, kunt u de geschiedenis voor die uitvoering weer geven.

   1. Selecteer in het **overzichts** venster van de logische app de uitvoering die u wilt weer geven.

   1. Vouw in het deel venster voor het uitvoeren van de **logische app** de acties uit die u wilt controleren.

      Als u ervoor kiest om zowel invoer als uitvoer te beveiligen, worden deze waarden nu verborgen weer gegeven.

      ![Verborgen gegevens in de uitvoerings geschiedenis](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Invoer en uitvoer in de code weergave beveiligen

Voeg in de onderliggende trigger of actie definitie de matrix `runtimeConfiguration.secureData.properties` toe aan een van beide of beide waarden:

* `"inputs"`: de invoer wordt beveiligd in de uitvoerings geschiedenis.
* `"outputs"`: Hiermee worden uitvoer in de uitvoerings geschiedenis beveiligd.

Hier volgen enkele [aandachtspunten om te controleren](#obfuscation-considerations) wanneer u deze instellingen gebruikt om deze gegevens te beveiligen.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Overwegingen bij het verbergen van invoer en uitvoer

* Wanneer u de invoer of uitvoer van een trigger of actie beveiligt, verzendt Logic Apps de beveiligde gegevens niet naar Azure Log Analytics. U kunt ook geen [bijgehouden eigenschappen](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) toevoegen aan deze trigger of actie voor bewaking.

* De [Logic apps-API voor het verwerken van de werk stroom geschiedenis](https://docs.microsoft.com/rest/api/logic/) retourneert geen beveiligde uitvoer.

* In een actie waarbij u invoer beveiligt of die expliciet beveiligde uitvoer gebruikt, en deze actie een reactie retourneert met uitvoer die deze beveiligde gegevens bevat, moet u hand matig **beveiligde uitvoer** in deze actie inschakelen om deze uitvoer te beveiligen.

* Zorg ervoor dat u **beveiligde invoer** of **beveiligde uitvoer** inschakelt in downstream-acties waarbij u verwacht dat de uitvoerings geschiedenis die gegevens beveiligt.

  **Instelling voor beveiligde uitvoer**

  Wanneer u **beveiligde uitvoer** in een trigger of actie hand matig inschakelt, Logic apps deze uitvoer in de uitvoerings geschiedenis beveiligen. Als een stroomafwaartse actie deze beveiligde uitvoer expliciet gebruikt als invoer, Logic Apps de invoer van deze actie in de uitvoerings geschiedenis verbergen, maar wordt de instelling **beveiligde invoer** van de actie *niet ingeschakeld* .

  ![Beveiligde uitvoer als invoer en stroomafwaartse impact op de meeste acties](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  De acties opstellen, parseren JSON en Response hebben alleen de instelling **beveiligde invoer** . Wanneer deze optie is ingeschakeld, worden de uitvoer van deze acties ook verborgen. Als deze acties expliciet gebruikmaken van de upstream beveiligde uitvoer als invoer, worden de invoer en uitvoer van deze acties door Logic Apps verborgen, maar worden de instelling **beveiligde invoer waarden** *niet ingeschakeld* . Als een stroomafwaartse actie expliciet gebruikmaakt van de verborgen uitvoer van de acties opstellen, parseren van JSON of respons als invoer, Logic Apps *de invoer of uitvoer van deze stroomafwaartse actie niet verbergen*.

  ![Beveiligde uitvoer als invoer met downstream-impact op specifieke acties](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Instelling voor beveiligde invoer**

  Wanneer u **beveiligde invoer** in een trigger of actie hand matig inschakelt, Logic apps deze invoer in de uitvoerings geschiedenis beveiligen. Als een stroomafwaartse actie expliciet gebruikmaakt van de zicht bare uitvoer van die trigger of actie als invoer, Logic Apps de invoer van deze stroomafwaartse actie verbergen in de uitvoerings geschiedenis, maar worden *geen* **beveiligde invoer waarden** in deze actie ingeschakeld en wordt deze actie niet verborgen uitvoer.

  ![Beveiligde invoer en downstream van invloed op de meeste acties](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Als de acties opstellen, JSON en respons maken expliciet gebruikmaken van de zicht bare uitvoer van de trigger of actie die de beveiligde invoer heeft, Logic Apps de invoer en uitvoer van deze acties verbergen, maar worden de **beveiligde invoer** van deze actie *niet ingeschakeld* stelt. Als een stroomafwaartse actie expliciet gebruikmaakt van de verborgen uitvoer van de acties opstellen, parseren van JSON of respons als invoer, Logic Apps *de invoer of uitvoer van deze stroomafwaartse actie niet verbergen*.

  ![Beveiligde invoer en downstream-impact op specifieke acties](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Toegang tot parameter invoer

Als u in verschillende omgevingen implementeert, kunt u de waarden in uw werk stroom definitie parameterizingen die variëren op basis van deze omgevingen. Op die manier kunt u een [Azure Resource Manager sjabloon](../azure-resource-manager/resource-group-authoring-templates.md#parameters) gebruiken om uw logische app te implementeren, gevoelige informatie te beschermen door de beveiligde para meters te definiëren en deze parameter invoer afzonderlijk via de para meters van de sjabloon te gebruiken met behulp van een [para meter bestand](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Als u bijvoorbeeld HTTP-acties verifieert met [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), kunt u de para meters definiëren en beveiligen die de client-id en het client geheim accepteren dat voor verificatie wordt gebruikt. Als u deze para meters voor uw logische app wilt definiëren, gebruikt u de sectie `parameters` in de werk stroom definitie van de logische app. Als u parameter waarden wilt beveiligen die niet moeten worden weer gegeven bij het bewerken van uw logische app of het weer geven van uitvoerings geschiedenis, definieert u de para meters met behulp van het `securestring`-of `secureobject`-type en gebruikt u indien nodig code ring. Para meters van dit type worden niet geretourneerd met de resource definitie en zijn niet toegankelijk wanneer de resource wordt weer gegeven na de implementatie. Als u tijdens runtime toegang wilt krijgen tot deze parameter waarden, gebruikt u de `@parameters('<parameter-name>')`-expressie in de definitie van de werk stroom. Deze expressie wordt alleen geëvalueerd tijdens runtime en wordt beschreven door de [taal van de werk stroom definitie](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Als u een para meter in de headers of hoofd tekst van een HTTP-aanvraag gebruikt, is die para meter mogelijk zichtbaar wanneer u de uitvoerings geschiedenis van de logische app en de uitgaande HTTP-aanvraag weergeeft. Zorg ervoor dat u ook uw beleids regels voor inhouds toegang dienovereenkomstig instelt. Autorisatie headers worden nooit weer gegeven via invoer of uitvoer. Dus als er een geheim wordt gebruikt, kan dat geheim niet worden opgehaald.

Zie voor meer informatie [veilige para meters in werk stroom definities](#secure-parameters-workflow) verderop in dit onderwerp.

Wanneer u implementaties automatiseert met [Azure Resource Manager sjablonen](../azure-resource-manager/resource-group-authoring-templates.md#parameters), kunt u de para meters voor beveiligde sjablonen definiëren die tijdens de implementatie worden geëvalueerd, met behulp van de `securestring`-en `secureobject`-typen. Voor het definiëren van sjabloon parameters gebruikt u de sectie `parameters` van het hoogste niveau van uw sjabloon, die gescheiden is en anders is dan de sectie `parameters` van uw werk stroom definitie. Als u de waarden voor sjabloon parameters wilt opgeven, gebruikt u een afzonderlijk [parameter bestand](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Als u bijvoorbeeld geheimen gebruikt, kunt u de para meters voor beveiligde sjablonen definiëren en gebruiken om deze geheimen op te halen uit [Azure Key Vault](../key-vault/key-vault-overview.md) tijdens de implementatie. U kunt vervolgens naar de sleutel kluis en het geheim in het parameter bestand verwijzen. Zie de volgende onderwerpen voor meer informatie:

* [Azure Key Vault gebruiken om beveiligde parameter waarden door te geven tijdens de implementatie](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Veilige para meters in azure Resource Manager sjablonen](#secure-parameters-deployment-template) verderop in dit onderwerp

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Veilige para meters in werk stroom definities

Als u gevoelige informatie in de werk stroom definitie van de logische app wilt beveiligen, gebruikt u beveiligde para meters zodat deze informatie niet zichtbaar is nadat u uw logische app hebt opgeslagen. Stel dat u voor een HTTP-actie basis verificatie nodig hebt, waarbij een gebruikers naam en wacht woord worden gebruikt. In de definitie van de werk stroom definieert de sectie `parameters` de para meters `basicAuthPasswordParam` en `basicAuthUsernameParam` door gebruik te maken van het type `securestring`. De actie definitie verwijst vervolgens naar deze para meters in de sectie `authentication`.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Veilige para meters in Azure Resource Manager sjablonen

Een resource manager-sjabloon voor een logische app heeft meerdere secties `parameters`. Als u wacht woorden, sleutels, geheimen en andere gevoelige informatie wilt beveiligen, definieert u beveiligde para meters op sjabloon niveau en op het niveau van de werk stroom definitie met behulp van het `securestring`-of `secureobject`-type. U kunt deze waarden vervolgens opslaan in [Azure Key Vault](../key-vault/key-vault-overview.md) en het [parameter bestand](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) gebruiken om te verwijzen naar de sleutel kluis en het geheim. Uw sjabloon haalt vervolgens die informatie op tijdens de implementatie. Zie [Azure Key Vault gebruiken om beveiligde parameter waarden door te geven tijdens de implementatie](../azure-resource-manager/resource-manager-keyvault-parameter.md)voor meer informatie.

Hier vindt u meer informatie over deze `parameters`-secties:

* Op het hoogste niveau van de sjabloon definieert een `parameters`-sectie de para meters voor de waarden die de sjabloon gebruikt bij de *implementatie*. Deze waarden kunnen bijvoorbeeld verbindings reeksen bevatten voor een specifieke implementatie omgeving. U kunt deze waarden vervolgens opslaan in een afzonderlijk [parameter bestand](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values), waardoor het wijzigen van deze waarden eenvoudiger wordt.

* In de resource definitie van de logische app, maar buiten uw werk stroom definitie, worden in een sectie `parameters` de waarden voor de para meters van uw werk stroom definitie opgegeven. In deze sectie kunt u deze waarden toewijzen met behulp van sjabloon expressies die verwijzen naar de para meters van uw sjabloon. Deze expressies worden geëvalueerd tijdens de implementatie.

* In de definitie van uw werk stroom definieert een `parameters`-sectie de para meters die uw logische app gebruikt tijdens runtime. U kunt vervolgens naar deze para meters in de werk stroom van de logische app verwijzen met behulp van definitie expressies voor werk stromen, die tijdens runtime worden geëvalueerd.

Deze voorbeeld sjabloon met meerdere beveiligde parameter definities die gebruikmaken van het `securestring`-type:

| Parameternaam | Beschrijving |
|----------------|-------------|
| `TemplatePasswordParam` | Een sjabloon parameter die vervolgens een wacht woord accepteert dat vervolgens wordt door gegeven aan de para meter `basicAuthPasswordParam` van de werk stroom definitie |
| `TemplateUsernameParam` | Een sjabloon parameter die vervolgens een gebruikers naam accepteert die vervolgens wordt door gegeven aan de para meter `basicAuthUserNameParam` van de werk stroom definitie |
| `basicAuthPasswordParam` | Een para meter voor de werk stroom definitie die het wacht woord voor basis verificatie in een HTTP-actie accepteert |
| `basicAuthUserNameParam` | Een para meter voor de werk stroom definitie die de gebruikers naam voor basis verificatie in een HTTP-actie accepteert |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Toegang tot services en systemen die worden aangeroepen vanuit Logic apps

Hier volgen enkele manieren waarop u eind punten kunt beveiligen waar uw logische app toegang moet hebben tot het verzenden van aanvragen:

* Verificatie voor uitgaande aanvragen toevoegen.

  Wanneer u werkt met een HTTP-, HTTP + Swagger (open API) of webhook-actie, kunt u verificatie toevoegen aan de aanvraag die wordt verzonden door uw logische app. U kunt bijvoorbeeld basis verificatie, certificaat verificatie of Azure Active Directory verificatie gebruiken. Zie [Triggers of acties verifiëren](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)voor meer informatie.

* Beperk de toegang tot IP-adressen van logische apps.

  Alle aanroepen naar eind punten van Logic apps zijn afkomstig van specifieke IP-adressen die zijn gebaseerd op de regio's van uw Logic apps. U kunt filters toevoegen die alleen aanvragen van deze IP-adressen accepteren. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md#configuration)om deze IP-adressen op te halen.

* Bronnen verifiëren en openen als beheerde identiteiten.

  Om toegang te krijgen tot resources in andere Azure Active Directory (Azure AD)-tenants, kan uw logische app uw identiteit verifiëren zonder zich aan te melden met behulp van een beheerde identiteit (voorheen bekend als Managed Service Identity of MSI), in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te draaien. Zie voor meer informatie over het instellen en gebruiken van een door het systeem toegewezen beheerde identiteit voor uw logische app het [verifiëren en openen van resources met beheerde identiteiten in azure Logic apps](../logic-apps/create-managed-service-identity.md).

* Beveiligde verbindingen met on-premises systemen.

  Azure Logic Apps biedt integratie met deze services voor veilige en betrouw bare on-premises communicatie.

  * On-premises gegevensgateway

    Veel beheerde connectors in Azure Logic Apps bieden beveiligde verbindingen met on-premises systemen, zoals bestands systeem, SQL, share point en DB2. De Gateway verzendt gegevens via on-premises bronnen op versleutelde kanalen via de Azure Service Bus. Al het verkeer is afkomstig van het beveiligde uitgaande verkeer van de gateway-agent. Meer informatie [over de werking van de on-premises gegevens gateway](logic-apps-gateway-install.md#gateway-cloud-service).

  * Verbinding maken via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) biedt opties voor on-premises verbindingen, zoals site-naar-site virtueel particulier netwerk en ExpressRoute-integratie voor beveiligde proxy en communicatie met on-premises systemen. Vanuit de werk stroom van uw logische app in de ontwerp functie voor logische apps kunt u een API selecteren die wordt weer gegeven door API Management, die snelle toegang biedt tot on-premises systemen.

## <a name="next-steps"></a>Volgende stappen

* [Implementatie sjablonen maken](logic-apps-create-deploy-template.md)  
* [Uw logische apps bewaken](logic-apps-monitor-your-logic-apps.md)  
* [Fouten en problemen van logische apps diagnosticeren](logic-apps-diagnosing-failures.md)  
* [Implementatie van logische apps automatiseren](logic-apps-azure-resource-manager-templates-overview.md)
