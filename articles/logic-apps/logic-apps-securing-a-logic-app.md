---
title: Toegang en gegevens beveiligen
description: Veilige toegang tot invoer, uitvoer, op aanvragen gebaseerde triggers, uitvoerings geschiedenis, beheer taken en toegang tot andere resources in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 3a7fc8028348ae20403df62cd03c76a266edf07c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191325"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Beveiligde toegang en gegevens in Azure Logic Apps

Voor het beheren van de toegang tot en het beveiligen van gegevens in Azure Logic Apps, kunt u de beveiliging op de volgende gebieden instellen:

* [Toegang tot activeringen op basis van een aanvraag](#secure-triggers)
* [Toegang tot logische app-bewerkingen](#secure-operations)
* [Toegang tot de invoer en uitvoer van de uitvoerings geschiedenis](#secure-run-history)
* [Toegang tot parameter invoer](#secure-action-parameters)
* [Toegang tot services en systemen die worden aangeroepen vanuit Logic apps](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Toegang tot activeringen op basis van een aanvraag

Als uw logische app gebruikmaakt van een trigger op basis van een aanvraag, die inkomende aanroepen of aanvragen ontvangt, zoals de trigger van de [aanvraag](../connectors/connectors-native-reqres.md) of de [webhook](../connectors/connectors-native-webhook.md) , kunt u de toegang beperken zodat alleen geautoriseerde clients uw logische app kunnen aanroepen. Alle aanvragen die door een logische app worden ontvangen, zijn versleuteld en beveiligd met het Secure Sockets Layer-Protocol (SSL).

Hier vindt u opties die u kunnen helpen bij het beveiligen van de toegang tot dit trigger type:

* [Hand tekeningen voor gedeelde toegang genereren](#sas)
* [Inkomende IP-adressen beperken](#restrict-inbound-ip-addresses)
* [Azure Active Directory OAuth of andere beveiliging toevoegen](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Hand tekeningen voor gedeelde toegang (SAS) genereren

Elk aanvraag eindpunt op een logische app heeft een [Shared Access Signature (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) in de URL van het eind punt, die de volgende indeling heeft:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Elke URL bevat de `sp`, `sv`en `sig` query parameter, zoals beschreven in deze tabel:

| Query parameter | Beschrijving |
|-----------------|-------------|
| `sp` | Hiermee geeft u de machtigingen op voor het gebruik van de toegestane HTTP-methoden. |
| `sv` | Hiermee geeft u de SAS-versie op die moet worden gebruikt voor het genereren van de hand tekening. |
| `sig` | Hiermee geeft u de hand tekening op die moet worden gebruikt voor het verifiëren van de toegang tot de trigger. Deze hand tekening wordt gegenereerd met behulp van de SHA256-algoritme met een geheime toegangs sleutel voor alle URL-paden en eigenschappen. Nooit beschikbaar of gepubliceerd, deze sleutel wordt versleuteld bewaard en opgeslagen met de logische app. Uw logische app machtigt alleen de triggers die een geldige hand tekening bevatten die is gemaakt met de geheime sleutel. |
|||

Zie de volgende secties in dit onderwerp voor meer informatie over het beveiligen van de toegang met SAS:

* [Toegangs sleutels opnieuw genereren](#access-keys)
* [Url's voor verlopende call back maken](#expiring-urls)
* [Url's met een primaire of secundaire sleutel maken](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Toegangssleutels regenereren

Als u op elk gewenst moment een nieuwe beveiligings toegangs sleutel wilt genereren, gebruikt u de Azure REST API of Azure Portal. Alle eerder gegenereerde Url's die gebruikmaken van de oude sleutel, zijn ongeldig en hebben geen autorisatie meer om de logische app te activeren. De Url's die u na het opnieuw genereren hebt opgehaald, worden ondertekend met de nieuwe toegangs sleutel.

1. Open in de [Azure Portal](https://portal.azure.com)de logische app met de sleutel die u opnieuw wilt genereren.

1. Selecteer **toegangs sleutels**onder **instellingen**in het menu van de logische app.

1. Selecteer de sleutel die u opnieuw wilt genereren en voltooi het proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Url's voor verlopende call back maken

Als u de eind punt-URL voor een op aanvragen gebaseerde trigger met andere partijen deelt, kunt u Url's voor terugbellen genereren die specifieke sleutels gebruiken en verloop datums hebben. Op die manier kunt u sleutels naadloos draaien of de toegang beperken tot het activeren van uw logische app op basis van een specifieke tijds duur. Als u een verval datum voor een URL wilt opgeven, gebruikt u de [Logic Apps rest API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), bijvoorbeeld:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Neem in de hoofd tekst de eigenschap `NotAfter`op met behulp van een JSON-datum teken reeks. Deze eigenschap retourneert een call back-URL die alleen geldig is voor de `NotAfter` datum en tijd.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Url's met een primaire of secundaire sleutel maken

Wanneer u call back-Url's voor een op aanvragen gebaseerde trigger genereert of lijsteert, kunt u de sleutel opgeven die moet worden gebruikt voor het ondertekenen van de URL. Als u een URL wilt genereren die is ondertekend met een specifieke sleutel, gebruikt u de [Logic Apps rest API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), bijvoorbeeld:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Neem in de hoofd tekst de `KeyType` eigenschap op als `Primary` of `Secondary`. Deze eigenschap retourneert een URL die is ondertekend door de opgegeven beveiligings sleutel.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Inkomende IP-adressen beperken

Samen met Shared Access Signature (SAS) kunt u de clients die uw logische app kunnen aanroepen, het beste beperken. Als u bijvoorbeeld het eind punt van de aanvraag beheert met behulp van Azure API Management, kunt u de logische app beperken tot het accepteren van aanvragen van het IP-adres voor het API Management exemplaar.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Binnenkomende IP-bereiken in Azure Portal beperken

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen**onder **instellingen**in het menu van de logische app.

1. Selecteer **specifieke IP-bereiken**onder **configuratie van toegangs beheer** > **toegestane binnenkomende IP-adressen**.

1. Geef onder **IP-bereiken voor triggers**de IP-adresbereiken op die de trigger accepteert.

   Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x.* x. x

Als u wilt dat uw logische app alleen als een geneste logische app wordt geactiveerd, selecteert u **alleen andere Logic apps**in de lijst **toegestane binnenkomende IP-adressen** . Met deze optie wordt een lege matrix naar de logische app-resource geschreven. Op die manier kunnen alleen aanroepen van de Logic Apps-service (bovenliggende Logic apps) de geneste logische app activeren.

> [!NOTE]
> Ongeacht het IP-adres kunt u nog steeds een logische app met een op aanvragen gebaseerde trigger uitvoeren met behulp van `/triggers/<trigger-name>/run` via de Azure REST API of via API Management. Voor dit scenario is echter nog steeds verificatie vereist voor de Azure-REST API. Alle gebeurtenissen worden weer gegeven in het controle logboek van Azure. Zorg ervoor dat u de beleids regels voor toegangs beheer dienovereenkomstig instelt.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Binnenkomende IP-bereiken in Azure Resource Manager sjabloon beperken

Als u de [implementatie van Logic apps automatiseert met behulp van Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u de IP-bereiken opgeven met behulp van de sectie `accessControl` met de `triggers` sectie in de resource definitie van de logische app, bijvoorbeeld:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory OAuth of andere beveiliging toevoegen

Als u meer autorisatie protocollen wilt toevoegen aan uw logische app, kunt u overwegen de [Azure API Management](../api-management/api-management-key-concepts.md) -service te gebruiken. Deze service helpt u uw logische app beschikbaar te maken als een API en biedt uitgebreide bewaking, beveiliging, beleid en documentatie voor elk eind punt. API Management kunt een openbaar of persoonlijk eind punt beschikbaar maken voor uw logische app. Als u toegang tot dit eind punt wilt verlenen, kunt u [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication), [client certificaat](#client-certificate-authentication)of andere beveiligings standaarden gebruiken om toegang tot het eind punt te verlenen. Wanneer API Management een aanvraag ontvangt, stuurt de service de aanvraag naar uw logische app, worden ook de nodige trans formaties of beperkingen door lopen. Als u uw logische app alleen API Management activeren, kunt u de instellingen voor het binnenkomende IP-adres bereik van uw logische app gebruiken.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Toegang tot logische app-bewerkingen

U kunt alleen specifieke gebruikers of groepen toestaan specifieke taken uit te voeren, zoals het beheren, bewerken en weer geven van logische apps. Als u de machtigingen wilt beheren, moet u gebruikmaken [van Azure Role Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) , zodat u aangepaste of ingebouwde rollen kunt toewijzen aan de leden van uw Azure-abonnement:

* [Inzender van Logic](../role-based-access-control/built-in-roles.md#logic-app-contributor)apps: Hiermee kunt u logische apps beheren, maar u kunt de toegang niet wijzigen.

* [Logische app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar u kunt ze niet bewerken of bijwerken.

U kunt [Azure resource Lock](../azure-resource-manager/management/lock-resources.md)gebruiken om te voor komen dat anderen uw logische app wijzigen of verwijderen. Deze mogelijkheid voor komt dat anderen productie resources wijzigen of verwijderen.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Toegang tot uitvoerings geschiedenis gegevens

Tijdens de uitvoering van een logische app worden alle gegevens [tijdens de overdracht versleuteld](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) met behulp van Transport Layer Security (TLS) en [in rust](../security/fundamentals/encryption-atrest.md). Wanneer de logische app wordt uitgevoerd, kunt u de geschiedenis voor die uitvoering bekijken, met inbegrip van de stappen die samen met de status, duur, invoer en uitvoer voor elke actie worden uitgevoerd. Deze uitgebreide details bieden inzicht in hoe uw logische app wordt uitgevoerd en waar u kunt beginnen met het oplossen van problemen die zich voordoen.

Wanneer u de uitvoerings geschiedenis van de logische app bekijkt, wordt uw toegang door Logic Apps geverifieerd en vindt u koppelingen naar de invoer en uitvoer voor de aanvragen en antwoorden voor elke uitvoering. Voor acties waarbij wacht woorden, geheimen, sleutels of andere gevoelige informatie worden verwerkt, wilt u echter voor komen dat anderen deze gegevens kunnen weer geven en gebruiken. Als uw logische app bijvoorbeeld een geheim krijgt van [Azure Key Vault](../key-vault/key-vault-overview.md) om te gebruiken bij het verifiëren van een http-actie, wilt u dat geheim verbergen in de weer gave.

Als u de toegang tot de invoer en uitvoer in de uitvoerings geschiedenis van de logische app wilt beheren, hebt u de volgende opties:

* De [toegang beperken op basis van het IP-adres bereik](#restrict-ip).

  Met deze optie kunt u de toegang tot de uitvoerings geschiedenis beveiligen op basis van de aanvragen van een specifiek IP-adres bereik.

* [Gegevens uit de uitvoerings geschiedenis verbergen door gebruik te maken van een donkere weer gegeven](#obfuscate).

  In veel triggers en acties kunt u de invoer, uitvoer of beide verbergen vanuit de uitvoerings geschiedenis van een logische app.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Toegang beperken op basis van IP-adres bereik

U kunt de toegang tot de invoer en uitvoer in de uitvoerings geschiedenis van de logische app beperken zodat alleen aanvragen van specifieke IP-adresbereiken die gegevens kunnen weer geven. Als u bijvoorbeeld wilt blok keren dat iedereen toegang heeft tot invoer en uitvoer, geeft u een IP-adres bereik op zoals `0.0.0.0-0.0.0.0`. Alleen gebruikers met beheerders machtigingen kunnen deze beperking verwijderen. Dit biedt de mogelijkheid om just-in-time toegang te krijgen tot de gegevens van uw logische app. U kunt de IP-bereiken opgeven die u wilt beperken door gebruik te maken van de Azure Portal of in een Azure Resource Manager sjabloon die u gebruikt voor de implementatie van logische apps.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>IP-bereiken in Azure Portal beperken

1. Open in de Azure Portal uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **werk stroom instellingen**onder **instellingen**in het menu van de logische app.

1. Selecteer **specifieke IP-bereiken**onder **configuratie van toegangs beheer** > **toegestane binnenkomende IP-adressen**.

1. Geef onder **IP-adresbereiken voor inhoud**de IP-adresbereiken op die toegang hebben tot inhoud van invoer en uitvoer. 

   Een geldig IP-bereik maakt gebruik van de volgende indelingen: *x. x. x. x/x* of *x. x. x. x-x. x.* x. x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>IP-bereiken in Azure Resource Manager sjabloon beperken

Als u de [implementatie van Logic apps automatiseert met behulp van Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u de IP-bereiken opgeven met behulp van de sectie `accessControl` met de `contents` sectie in de resource definitie van de logische app, bijvoorbeeld:

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

Veel triggers en acties hebben instellingen voor het verbergen van invoer, uitvoer of beide vanuit de uitvoerings geschiedenis van een logische app. Hier volgen enkele [aandachtspunten om te controleren](#obfuscation-considerations) wanneer u deze instellingen gebruikt om u te helpen bij het beveiligen van deze gegevens.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Invoer en uitvoer in de ontwerp functie verbergen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

   ![Logische app openen in de ontwerp functie voor logische apps](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Selecteer de knop met weglatings tekens ( **...** ) op de trigger of de actie waar u gevoelige gegevens wilt verbergen en selecteer vervolgens **instellingen**.

   ![Trigger-of actie-instellingen openen](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Schakel **beveiligde invoer**, **beveiligde uitvoer**of beide in. Wanneer u klaar bent, selecteert u **Gereed**.

   ![Schakel beveiligde invoer of beveiligde uitvoer in](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   In de actie of de trigger wordt nu een vergrendelings pictogram weer gegeven in de titel balk.

   ![De titel balk van de actie of de trigger toont het vergrendelings pictogram](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens die beveiligde uitvoer van vorige acties vertegenwoordigen, worden ook vergrendelings pictogrammen weer gegeven. Wanneer u bijvoorbeeld een dergelijke uitvoer van de lijst met dynamische inhoud selecteert die u wilt gebruiken in een actie, toont dat token een vergrendelings pictogram.

   ![Token selecteren voor beveiligde uitvoer](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Wanneer de logische app wordt uitgevoerd, kunt u de geschiedenis voor die uitvoering weer geven.

   1. Selecteer in het **overzichts** venster van de logische app de uitvoering die u wilt weer geven.

   1. Vouw in het deel venster voor het uitvoeren van de **logische app** de acties uit die u wilt controleren.

      Als u hebt gekozen voor het verbergen van beide invoer en uitvoer, worden deze waarden nu verborgen weer gegeven.

      ![Verborgen invoer en uitvoer in uitvoerings geschiedenis](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Invoer en uitvoer verbergen in de code weergave

In de onderliggende trigger of actie definitie kunt u de `runtimeConfiguration.secureData.properties`-matrix toevoegen of bijwerken met een van beide of beide van de volgende waarden:

* `"inputs"`: beveiligt invoer in uitvoerings geschiedenis.
* `"outputs"`: Hiermee worden uitvoer in de uitvoerings geschiedenis beveiligd.

Hier volgen enkele [aandachtspunten om te controleren](#obfuscation-considerations) wanneer u deze instellingen gebruikt om u te helpen bij het beveiligen van deze gegevens.

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

* Wanneer u de invoer of uitvoer van een trigger of actie verduidelijkt, worden Logic Apps de beveiligde gegevens niet naar Azure Log Analytics verzonden. U kunt ook geen [bijgehouden eigenschappen](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) toevoegen aan deze trigger of actie voor bewaking.

* De [Logic apps-API voor het verwerken van de werk stroom geschiedenis](https://docs.microsoft.com/rest/api/logic/) retourneert geen beveiligde uitvoer.

* Als u de uitvoer wilt verbergen van een actie die invoer verduidelijkt of als deze expliciet worden uitgevoerd, schakelt u de **beveiligde uitvoer** in die actie hand matig in.

* Zorg ervoor dat u **beveiligde invoer** of **beveiligde uitvoer** inschakelt in downstream-acties waarbij u verwacht dat de uitvoerings geschiedenis deze gegevens verduidelijkt.

  **Instelling voor beveiligde uitvoer**

  Wanneer u **beveiligde uitvoer** in een trigger of actie hand matig inschakelt, Logic apps deze uitvoer in de uitvoerings geschiedenis beveiligen. Als een stroomafwaartse actie deze beveiligde uitvoer expliciet gebruikt als invoer, Logic Apps de invoer van deze actie in de uitvoerings geschiedenis verbergen, maar wordt de instelling **beveiligde invoer** van de actie *niet ingeschakeld* .

  ![Beveiligde uitvoer als invoer en stroomafwaartse impact op de meeste acties](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  De acties opstellen, parseren JSON en Response hebben alleen de instelling **beveiligde invoer** . Wanneer deze optie is ingeschakeld, worden de uitvoer van deze acties ook verborgen. Als deze acties expliciet gebruikmaken van de upstream beveiligde uitvoer als invoer, worden de invoer en uitvoer van deze acties door Logic Apps verborgen, maar worden de instelling **beveiligde invoer waarden** *niet ingeschakeld* . Als een stroomafwaartse actie expliciet gebruikmaakt van de verborgen uitvoer van de acties opstellen, parseren van JSON of respons als invoer, Logic Apps *de invoer of uitvoer van deze stroomafwaartse actie niet verbergen*.

  ![Beveiligde uitvoer als invoer met downstream-impact op specifieke acties](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Instelling voor beveiligde invoer**

  Wanneer u **beveiligde invoer** in een trigger of actie hand matig inschakelt, Logic apps deze invoer in de uitvoerings geschiedenis beveiligen. Als een stroomafwaartse actie expliciet gebruikmaakt van de zicht bare uitvoer van die trigger of actie als invoer, Logic Apps de invoer van deze stroomafwaartse actie verbergen in de uitvoerings geschiedenis, maar worden *geen* **beveiligde invoer** in deze actie ingeschakeld en worden de uitvoer van deze actie niet verborgen.

  ![Beveiligde invoer en downstream van invloed op de meeste acties](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Als de acties opstellen, parseren van JSON en respons expliciet gebruikmaken van de zicht bare uitvoer van de trigger of actie die de beveiligde invoer heeft, Logic Apps de invoer en uitvoer van deze acties verbergen, maar schakelt de instelling **beveiligde invoer** van deze actie *niet in* . Als een stroomafwaartse actie expliciet gebruikmaakt van de verborgen uitvoer van de acties opstellen, parseren van JSON of respons als invoer, Logic Apps *de invoer of uitvoer van deze stroomafwaartse actie niet verbergen*.

  ![Beveiligde invoer en downstream-impact op specifieke acties](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Toegang tot parameter invoer

Als u in verschillende omgevingen implementeert, kunt u de waarden in uw werk stroom definitie parameterizingen die variëren op basis van deze omgevingen. Op die manier kunt u hardcoded gegevens voor komen door een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) te gebruiken om uw logische app te implementeren, gevoelige gegevens te beschermen door beveiligde para meters te definiëren en deze gegevens als afzonderlijke invoer door te geven via de [para meters van de sjabloon](../azure-resource-manager/templates/template-parameters.md) met behulp van een [parameter bestand](../azure-resource-manager/templates/parameter-files.md).

Als u bijvoorbeeld HTTP-acties met [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)verifieert, kunt u de para meters definiëren en verbergen die de client-id en het client geheim accepteren die worden gebruikt voor verificatie. Als u deze para meters in uw logische app wilt definiëren, gebruikt u de sectie `parameters` in de werk stroom definitie van de logische app en de Resource Manager-sjabloon voor implementatie. Als u parameter waarden wilt verbergen die u niet wilt weer geven bij het bewerken van de logische app of de weer gave van de uitvoerings geschiedenis, definieert u de para meters met behulp van de `securestring` of `secureobject` type en gebruikt u indien nodig code ring. Para meters van dit type worden niet geretourneerd met de resource definitie en zijn niet toegankelijk wanneer de resource wordt weer gegeven na de implementatie. Als u tijdens runtime toegang wilt krijgen tot deze parameter waarden, gebruikt u de `@parameters('<parameter-name>')` expressie in de definitie van uw werk stroom. Deze expressie wordt alleen geëvalueerd tijdens runtime en wordt beschreven door de [taal van de werk stroom definitie](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Als u een para meter gebruikt in een aanvraag header of hoofd tekst, is die para meter mogelijk zichtbaar wanneer u de uitvoerings geschiedenis van de logische app en uitgaande HTTP-aanvraag weergeeft. Zorg ervoor dat u ook uw beleids regels voor inhouds toegang dienovereenkomstig instelt. U kunt ook een afkorting [gebruiken om](#obfuscate) invoer en uitvoer in de uitvoerings geschiedenis te verbergen. Autorisatie headers worden nooit weer gegeven via invoer of uitvoer. Dus als er een geheim wordt gebruikt, kan dat geheim niet worden opgehaald.

Zie voor meer informatie deze secties in dit onderwerp:

* [Veilige para meters in werk stroom definities](#secure-parameters-workflow)
* [Gegevens uit de uitvoerings geschiedenis verbergen door gebruik te maken van een donkere weer geven](#obfuscate)

Als u de [implementatie van Logic apps automatiseert met behulp van Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u de [para meters](../azure-resource-manager/templates/template-parameters.md)voor beveiligde sjablonen definiëren die tijdens de implementatie worden geëvalueerd met behulp van de `securestring`-en `secureobject` typen. Voor het definiëren van sjabloon parameters gebruikt u de sectie `parameters` op het hoogste niveau van uw sjabloon, die gescheiden is en anders is dan de sectie `parameters` van uw werk stroom definitie. Als u de waarden voor sjabloon parameters wilt opgeven, gebruikt u een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md).

Als u bijvoorbeeld geheimen gebruikt, kunt u de para meters voor beveiligde sjablonen definiëren en gebruiken om deze geheimen op te halen uit [Azure Key Vault](../key-vault/key-vault-overview.md) tijdens de implementatie. U kunt vervolgens naar de sleutel kluis en het geheim in het parameter bestand verwijzen. Zie de volgende onderwerpen voor meer informatie:

* [Gevoelige waarden door geven tijdens de implementatie met behulp van Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Veilige para meters in azure Resource Manager sjablonen](#secure-parameters-deployment-template) verderop in dit onderwerp

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Veilige para meters in werk stroom definities

Als u gevoelige informatie in de werk stroom definitie van de logische app wilt beveiligen, gebruikt u beveiligde para meters zodat deze informatie niet zichtbaar is nadat u uw logische app hebt opgeslagen. Stel dat u voor een HTTP-actie basis verificatie nodig hebt, waarbij een gebruikers naam en wacht woord worden gebruikt. In de definitie van de werk stroom definieert de sectie `parameters` de `basicAuthPasswordParam` en `basicAuthUsernameParam` para meters met behulp van het type `securestring`. De actie definitie verwijst vervolgens naar deze para meters in de sectie `authentication`.

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

Een [Resource Manager-sjabloon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) voor een logische app heeft meerdere `parameters`-secties. Als u wacht woorden, sleutels, geheimen en andere gevoelige informatie wilt beveiligen, definieert u de beveiligde para meters op sjabloon niveau en op het niveau van de werk stroom definitie met behulp van het type `securestring` of `secureobject`. U kunt deze waarden vervolgens opslaan in [Azure Key Vault](../key-vault/key-vault-overview.md) en het [parameter bestand](../azure-resource-manager/templates/parameter-files.md) gebruiken om te verwijzen naar de sleutel kluis en het geheim. Uw sjabloon haalt vervolgens die informatie op tijdens de implementatie. Zie voor meer informatie [gevoelige waarden door geven tijdens de implementatie met behulp van Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Hier vindt u meer informatie over deze `parameters`-secties:

* Op het hoogste niveau van de sjabloon definieert een `parameters` sectie de para meters voor de waarden die de sjabloon gebruikt bij de *implementatie*. Deze waarden kunnen bijvoorbeeld verbindings reeksen bevatten voor een specifieke implementatie omgeving. U kunt deze waarden vervolgens opslaan in een afzonderlijk [parameter bestand](../azure-resource-manager/templates/parameter-files.md), waardoor het wijzigen van deze waarden eenvoudiger wordt.

* In de resource definitie van de logische app, maar buiten uw werk stroom definitie, geeft een `parameters` sectie de waarden op voor de para meters van uw werk stroom definitie. In deze sectie kunt u deze waarden toewijzen met behulp van sjabloon expressies die verwijzen naar de para meters van uw sjabloon. Deze expressies worden geëvalueerd tijdens de implementatie.

* In de definitie van de werk stroom definieert een `parameters` sectie de para meters die uw logische app gebruikt tijdens runtime. U kunt vervolgens naar deze para meters in de werk stroom van de logische app verwijzen met behulp van definitie expressies voor werk stromen, die tijdens runtime worden geëvalueerd.

Deze voorbeeld sjabloon met meerdere beveiligde parameter definities die gebruikmaken van het `securestring` type:

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

Hier volgen enkele manieren waarop u eind punten kunt beveiligen die oproepen of aanvragen ontvangen van uw logische app:

* Verificatie toevoegen aan uitgaande aanvragen.

  Wanneer u werkt met een HTTP-trigger of actie die uitgaande aanroepen uitvoert, zoals HTTP, HTTP + Swagger of webhook, kunt u verificatie toevoegen aan de aanvraag die wordt verzonden door uw logische app. U kunt bijvoorbeeld de volgende verificatie typen gebruiken:

  * [Basisverificatie](#basic-authentication)

  * [Verificatie van client certificaten](#client-certificate-authentication)

  * [Active Directory OAuth-verificatie](#azure-active-directory-oauth-authentication)

  * [Beheerde identiteits verificatie](#managed-identity-authentication)
  
  Zie [verificatie toevoegen aan uitgaande oproepen](#add-authentication-outbound) verderop in dit onderwerp voor meer informatie.

* Beperk de toegang tot IP-adressen van logische apps.

  Alle aanroepen naar eind punten van Logic apps zijn afkomstig van specifieke IP-adressen die zijn gebaseerd op de regio's van uw Logic apps. U kunt filters toevoegen die alleen aanvragen van deze IP-adressen accepteren. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md#configuration)om deze IP-adressen op te halen.

* Verbeter de beveiliging van verbindingen met on-premises systemen.

  Azure Logic Apps biedt integratie met deze services om veiligere en betrouw bare on-premises communicatie te bieden.

  * On-premises gegevens gateway

    Veel beheerde connectors in Azure Logic Apps ondersteunen beveiligde verbindingen met on-premises systemen, zoals bestands systeem, SQL, share point en DB2. De Gateway verzendt gegevens via on-premises bronnen op versleutelde kanalen via de Azure Service Bus. Al het verkeer is afkomstig van het beveiligde uitgaande verkeer van de gateway-agent. Meer informatie [over de werking van de on-premises gegevens gateway](logic-apps-gateway-install.md#gateway-cloud-service).

  * Verbinding maken via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) biedt opties voor on-premises verbindingen, zoals site-naar-site virtueel particulier netwerk en ExpressRoute-integratie voor beveiligde proxy en communicatie met on-premises systemen. Vanuit de werk stroom van uw logische app in de ontwerp functie voor logische apps kunt u een API selecteren die wordt weer gegeven door API Management, die snelle toegang biedt tot on-premises systemen.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Verificatie toevoegen aan uitgaande oproepen

HTTP-en HTTPS-eind punten ondersteunen verschillende soorten verificatie. Op basis van de trigger of actie die u gebruikt om uitgaande aanroepen of aanvragen voor toegang tot deze eind punten te maken, kunt u kiezen uit verschillende soorten verificatie typen. Gebruik beveiligde para meters en codeer gegevens als dat nodig is om ervoor te zorgen dat u gevoelige informatie beveiligt die door uw logische app wordt verwerkt. Zie [toegang tot para meters invoeren](#secure-action-parameters)voor meer informatie over het gebruik en het beveiligen van para meters.

> [!NOTE]
> In de ontwerp functie voor logische apps is de eigenschap **Authentication** mogelijk verborgen voor sommige triggers en acties waarbij u het verificatie type kunt opgeven. Als u wilt dat de eigenschap wordt weer gegeven in deze gevallen, opent u de lijst **nieuwe para meter toevoegen** op de trigger of actie en selecteert u **verificatie**. Zie [toegang verifiëren met beheerde identiteit](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)voor meer informatie.

| Verificatietype | Ondersteund door |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management, Azure-app Services, HTTP, HTTP + Swagger, HTTP-webhook |
| [Client certificaat](#client-certificate-authentication) | Azure API Management, Azure-app Services, HTTP, HTTP + Swagger, HTTP-webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure-app Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Uitgang](#raw-authentication) | Azure API Management, Azure-app Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Beheerde identiteit](#managed-identity-authentication) | Azure API Management, Azure-app Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Basisverificatie

Als de optie [basis](../active-directory-b2c/secure-rest-api-dotnet-basic-auth.md) beschikbaar is, geeft u de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | Basic | Het te gebruiken verificatie type |
| **Gebruikersnaam** | `username` | Ja | <*gebruikers naam*>| De gebruikers naam voor het verifiëren van de toegang tot het eind punt van de doel service |
| **Wachtwoord** | `password` | Ja | <*wachtwoord*> | Het wacht woord voor het verifiëren van de toegang tot het eind punt van de doel service |
||||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het afhandelen en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie geeft u de verificatie `type` als `Basic` en gebruikt u de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Verificatie van client certificaten

Als de optie [client certificaat](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) beschikbaar is, geeft u de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | **Client certificaat** <br>of <br>`ClientCertificate` | Het verificatie type dat moet worden gebruikt voor client certificaten voor Secure Sockets Layer (SSL). Zelfondertekende certificaten worden ondersteund, maar zelfondertekende certificaten voor SSL worden niet ondersteund. |
| **Pfx** | `pfx` | Ja | <*encoded-pfx-bestand-inhoud*> | De met base64 gecodeerde inhoud van een PFX-bestand (Personal Information Exchange) <p><p>Als u het PFX-bestand wilt converteren naar een base64-gecodeerde indeling, kunt u Power shell gebruiken door de volgende stappen uit te voeren: <p>1. Sla de certificaat inhoud op in een variabele: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converteer de certificaat inhoud met behulp van de functie `ToBase64String()` en sla die inhoud op in een tekst bestand: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Wachtwoord** | `password`| Nee | <*wacht woord voor pfx-bestand*> | Het wacht woord voor toegang tot het PFX-bestand |
|||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het afhandelen en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie geeft u de verificatie `type` als `ClientCertificate` en gebruikt u de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Zie de volgende onderwerpen voor meer informatie over het beveiligen van services met behulp van verificatie via client certificaten:

* [Verbeter de beveiliging van Api's met behulp van verificatie op basis van client certificaten in azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Verbeter de beveiliging van back-end-services door gebruik te maken van verificatie van client certificaten in azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Verbeter de beveiliging van uw REST-service door gebruik te maken van client certificaten](../active-directory-b2c/secure-rest-api-dotnet-certificate-auth.md)
* [Certificaat referenties voor toepassings verificatie](../active-directory/develop/active-directory-certificate-credentials.md)
* [Gebruik een SSL-certificaat in de code van uw toepassing in Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth-verificatie

Als de optie [OAuth Active Directory](../active-directory/develop/about-microsoft-identity-platform.md) beschikbaar is, geeft u de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | **Active Directory OAuth** <br>of <br>`ActiveDirectoryOAuth` | Het verificatietype te gebruiken. Het [OAuth 2,0-protocol](../active-directory/develop/v2-overview.md)wordt momenteel gevolgd door Logic apps. |
| **Provider** | `authority` | Nee | <*URL-voor-Authority-token-verlener*> | De URL voor de instantie die het verificatie token levert. Deze waarde is standaard `https://login.windows.net`. |
| **Bouw** | `tenant` | Ja | *Tenant-id* van <> | De Tenant-ID voor de Azure AD-Tenant |
| **Gericht** | `audience` | Ja | <> voor de goed keuring van *resources* | De resource die u wilt gebruiken voor autorisatie, bijvoorbeeld `https://management.core.windows.net/` |
| **Client ID** | `clientId` | Ja | <*client-ID*> | De client-ID voor de app die autorisatie aanvraagt |
| **Referentie type** | `credentialType` | Ja | Certificaat <br>of <br>Geheim | Het referentie type dat door de client wordt gebruikt voor het aanvragen van autorisatie. Deze eigenschap en waarde worden niet weer gegeven in de onderliggende definitie van de logische app, maar bepaalt de eigenschappen die worden weer gegeven voor het geselecteerde referentie type. |
| **Geheim** | `secret` | Ja, maar alleen voor het referentie type ' geheim ' | <*client-geheim*> | Het client geheim voor het aanvragen van autorisatie |
| **Pfx** | `pfx` | Ja, maar alleen voor het referentie type ' certificaat ' | <*encoded-pfx-bestand-inhoud*> | De met base64 gecodeerde inhoud van een PFX-bestand (Personal Information Exchange) |
| **Wachtwoord** | `password` | Ja, maar alleen voor het referentie type ' certificaat ' | <*wacht woord voor pfx-bestand*> | Het wacht woord voor toegang tot het PFX-bestand |
|||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het afhandelen en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie geeft u de verificatie `type` als `ActiveDirectoryOAuth`, het referentie type als `Secret`en gebruikt u de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Onbewerkte authenticatie

Als de **onbewerkte** optie beschikbaar is, kunt u dit verificatie type gebruiken wanneer u [verificatie schema's](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) moet gebruiken die niet voldoen aan het [OAuth 2,0-protocol](https://oauth.net/2/). Met dit type kunt u hand matig de waarde voor de autorisatie-header maken die u met de uitgaande aanvraag verzendt en die header waarde opgeven in de trigger of actie.

Hier ziet u een voor beeld van een header voor een HTTPS-aanvraag die volgt op het [OAuth 1,0-protocol](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Geef in de trigger of actie die onbewerkte authenticatie ondersteunt de volgende eigenschaps waarden op:

| Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | Uitgang | Het te gebruiken verificatie type |
| **Waarde** | `value` | Ja | <*autorisatie-header-waarde*> | De waarde van de autorisatie-header die moet worden gebruikt voor verificatie |
||||||

Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het afhandelen en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie geeft u de verificatie `type` als `Raw`en gebruikt u de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameter waarden op te halen:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Verificatie van beheerde identiteit

Als de optie [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) beschikbaar is, kan uw logische app gebruikmaken van de door het systeem toegewezen identiteit of een *enkele* hand matig gemaakte door de gebruiker toegewezen identiteit voor het verifiëren van toegang tot resources in andere Azure Active Directory (Azure AD)-tenants zonder zich aan te melden. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te draaien. Meer informatie over [Azure-Services die ondersteuning bieden voor beheerde identiteiten voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Voordat uw logische app een beheerde identiteit kan gebruiken, volgt u de stappen in [toegang tot Azure-resources verifiëren door beheerde identiteiten te gebruiken in azure Logic apps](../logic-apps/create-managed-service-identity.md). Met deze stappen wordt de beheerde identiteit voor uw logische app ingeschakeld en wordt de toegang tot de Azure-doel bron ingesteld.

1. Voordat een Azure-functie een beheerde identiteit kan gebruiken, moet u eerst [verificatie voor Azure functions inschakelen](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. Geef in de trigger of actie waar u de beheerde identiteit wilt gebruiken, de volgende eigenschaps waarden op:

   | Eigenschap (Designer) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
   |---------------------|-----------------|----------|-------|-------------|
   | **Verificatie** | `type` | Ja | **Beheerde identiteit** <br>of <br>`ManagedServiceIdentity` | Het te gebruiken verificatie type |
   | **Beheerde identiteit** | `identity` | Ja | door het **systeem toegewezen beheerde identiteit** *  <br>of <br>`SystemAssigned` <p><p>* < door de*gebruiker toegewezen identiteits*> | De beheerde identiteit die moet worden gebruikt |
   | **Gericht** | `audience` | Ja | <*doel-Resource-ID*> | De resource-ID voor de doel resource waartoe u toegang wilt krijgen. <p>`https://storage.azure.com/` maakt bijvoorbeeld de toegangs tokens voor verificatie geldig voor alle opslag accounts. U kunt echter ook een URL voor de basis service opgeven, zoals `https://fabrikamstorageaccount.blob.core.windows.net` voor een specifiek opslag account. <p>**Opmerking**: de eigenschap **doel groep** kan in sommige triggers of acties worden verborgen. Als u deze eigenschap zichtbaar wilt maken, opent u de lijst **nieuwe para meter toevoegen** in de trigger of actie en selecteert u **doel groep**. <p><p>**Belang rijk**: Zorg ervoor dat deze doel resource-id *precies overeenkomt* met de waarde die door Azure AD wordt verwacht, inclusief alle vereiste afsluitende slashes. Daarom vereist de `https://storage.azure.com/` Resource-ID voor alle Azure Blob Storage-accounts een afsluitende slash. De resource-ID voor een specifiek opslag account vereist echter geen afsluitende slash. Zie [Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)om deze resource-id's te vinden. |
   |||||

   Wanneer u [beveiligde para meters](#secure-action-parameters) gebruikt voor het afhandelen en beveiligen van gevoelige informatie, bijvoorbeeld in een [Azure Resource Manager sjabloon voor het automatiseren van de implementatie](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kunt u expressies gebruiken om toegang te krijgen tot deze parameter waarden tijdens runtime. In dit voor beeld van een HTTP-actie definitie geeft u de verificatie `type` als `ManagedServiceIdentity` en gebruikt u de [functie para meters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameter waarden op te halen:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Volgende stappen

* [Implementatie voor Azure Logic Apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Logische apps controleren](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Fouten en problemen van logische apps diagnosticeren](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
