---
title: Toegang en gegevens beveiligen
description: Beveiligde toegang tot ingangen, uitvoer, triggers op basis van aanvragen, rungeschiedenis, beheertaken en toegang tot andere bronnen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657162"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Beveiligde toegang en gegevens in Azure Logic Apps

Als u de toegang tot en de beveiliging van gegevens in Azure Logic Apps wilt beheren, u beveiliging instellen in deze gebieden:

* [Toegang tot triggers op basis van aanvragen](#secure-triggers)
* [Toegang tot logische app-bewerkingen](#secure-operations)
* [Toegang tot beheergeschiedenisingangen en -uitvoer](#secure-run-history)
* [Toegang tot parameteringangen](#secure-action-parameters)
* [Toegang tot services en systemen die vanuit logische apps worden aangeroepen](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Toegang tot triggers op basis van aanvragen

Als uw logische app een trigger op basis van aanvragen gebruikt, die binnenkomende oproepen of verzoeken ontvangt, zoals de trigger [Request](../connectors/connectors-native-reqres.md) of [Webhook,](../connectors/connectors-native-webhook.md) u de toegang beperken zodat alleen geautoriseerde clients uw logische app kunnen bellen. Alle aanvragen die door een logische app worden ontvangen, worden versleuteld en beveiligd met TLS (Transport Layer Security), voorheen bekend als Secure Sockets Layer (SSL), protocol.

Hier volgen opties waarmee u de toegang tot dit triggertype beveiligen:

* [Handtekeningen voor gedeelde toegang genereren](#sas)
* [Binnenkomende IP-adressen beperken](#restrict-inbound-ip-addresses)
* [Azure Active Directory OAuth of andere beveiliging toevoegen](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gedeelde toegangshandtekeningen (SAS) genereren

Elk eindpunt van het verzoek in een logische app heeft een [SAS (Shared Access Signature)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) in de URL van het eindpunt, die deze indeling volgt:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Elke URL `sp`bevat `sv`de `sig` parameter , en query zoals beschreven in deze tabel:

| Queryparameter | Beschrijving |
|-----------------|-------------|
| `sp` | Hiermee geeft u machtigingen op voor de toegestane HTTP-methoden die moeten worden gebruikt. |
| `sv` | Hiermee geeft u de SAS-versie op die moet worden gebruikt voor het genereren van de handtekening. |
| `sig` | Hiermee geeft u de handtekening op die moet worden gebruikt voor het verifiëren van de toegang tot de trigger. Deze handtekening wordt gegenereerd met behulp van het SHA256-algoritme met een geheime toegangssleutel op alle URL-paden en -eigenschappen. Nooit blootgesteld of gepubliceerd, wordt deze sleutel versleuteld bewaard en opgeslagen met de logische app. Uw logische app autoriseert alleen de triggers die een geldige handtekening bevatten die met de geheime sleutel is gemaakt. |
|||

Zie de volgende secties in dit onderwerp voor meer informatie over het beveiligen van toegang met SAS:

* [Toegangssleutels regenereren](#access-keys)
* [Verlopende terugroep-URL's maken](#expiring-urls)
* [URL's maken met primaire of secundaire toets](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Toegangssleutels regenereren

Als u op elk gewenst moment een nieuwe beveiligingstoegangssleutel wilt genereren, gebruikt u de Azure REST API of Azure-portal. Alle eerder gegenereerde URL's die de oude sleutel gebruiken, worden ongeldig verklaard en hebben geen toestemming meer om de logische app te activeren. De URL's die u ophaalt na regeneratie, worden ondertekend met de nieuwe toegangssleutel.

1. Open in de [Azure-portal](https://portal.azure.com)de logische app met de sleutel die u wilt regenereren.

1. Selecteer in het menu van de logische app onder **Instellingen**de optie **Toegangstoetsen**.

1. Selecteer de toets die u wilt regenereren en voltooi het proces.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Verlopende terugroep-URL's maken

Als u de URL van het eindpunt voor een trigger op basis van aanvragen deelt met andere partijen, u callback-URL's genereren die specifieke sleutels gebruiken en vervaldatums hebben. Op die manier u sleutels naadloos rollen of de toegang beperken tot het activeren van uw logische app op basis van een specifieke tijdspanne. Als u een vervaldatum voor een URL wilt opgeven, gebruikt u de [API Voor Logic Apps REST,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)bijvoorbeeld:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

In het lichaam, `NotAfter`omvatten de eigenschap met behulp van een JSON datum string. Deze eigenschap retourneert een terugbel-URL `NotAfter` die alleen geldig is tot de datum en tijd.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>URL's maken met primaire of secundaire geheime sleutel

Wanneer u url's voor een trigger op basis van aanvragen genereert of aangeeft, u de sleutel opgeven die u moet gebruiken voor het ondertekenen van de URL. Als u een URL wilt genereren die is ondertekend door een specifieke sleutel, gebruikt u de [LOGIC Apps REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)bijvoorbeeld:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

In het lichaam, `KeyType` omvatten `Primary` de `Secondary`eigenschap als een of . Deze eigenschap retourneert een URL die is ondertekend door de opgegeven beveiligingssleutel.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Binnenkomende IP-adressen beperken

Samen met Shared Access Signature (SAS) u specifiek de clients beperken die uw logische app kunnen aanroepen. Als u bijvoorbeeld het eindpunt van uw aanvraag beheert met Azure API Management, u uw logische app beperken om alleen aanvragen te accepteren vanaf het IP-adres voor het exemplaar API Beheer.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Binnenkomende IP-bereiken in Azure-portal beperken

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Selecteer in het menu van uw logische app onder **Instellingen**de optie **Werkstroominstellingen**.

1. Selecteer onder **Toegangscontrole-configuratie** > **Toegestane binnenkomende IP-adressen** **specifieke IP-bereiken**.

1. Geef onder **IP-bereiken voor triggers**de IP-adresbereiken op die de trigger accepteert.

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

Als u wilt dat uw logische app alleen wordt geactiveerd als een geneste logische app, selecteert u in de lijst **Toegestane binnenkomende IP-adressen** **alleen andere Logische apps**. Met deze optie wordt een lege array geschreven naar uw logische app-bron. Op die manier kunnen alleen oproepen van de Logic Apps-service (bovenliggende logica-apps) de geneste logica-app activeren.

> [!NOTE]
> Ongeacht het IP-adres u nog steeds een logische app `/triggers/<trigger-name>/run` uitvoeren met een trigger op basis van aanvragen via de Azure REST API of via API-beheer. Dit scenario vereist echter nog steeds verificatie met de Azure REST API. Alle gebeurtenissen worden weergegeven in het Azure-controlelogboek. Zorg ervoor dat u het toegangscontrolebeleid dienovereenkomstig instelt.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Binnenkomende IP-bereiken in azure resourcebeheersjabloon beperken

Als u [de implementatie van logische apps automatiseert met behulp van Resource Manager-sjablonen,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u de IP-bereiken opgeven met behulp van de `accessControl` sectie met de sectie in de `triggers` resourcedefinitie van uw logische app, bijvoorbeeld:

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

Als u meer autorisatieprotocollen wilt toevoegen aan uw logische app, u overwegen de [Azure API Management-service te](../api-management/api-management-key-concepts.md) gebruiken. Met deze service u uw logische app als API blootleggen en biedt u uitgebreide monitoring, beveiliging, beleid en documentatie voor elk eindpunt. API-beheer kan een openbaar of privé eindpunt voor uw logische app blootleggen. Als u toegang tot dit eindpunt wilt autoriseren, u [Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication) [clientcertificaat](#client-certificate-authentication)of andere beveiligingsstandaarden gebruiken voor het toestaan van toegang tot dat eindpunt. Wanneer API Management een aanvraag ontvangt, stuurt de service het verzoek naar uw logische app, waardoor ook onderweg de nodige transformaties of beperkingen worden gemaakt. Als u alleen API-beheer uw logische app wilt laten activeren, u de inkomende IP-bereikinstellingen van uw logische app gebruiken.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Toegang tot logische app-bewerkingen

U alleen specifieke gebruikers of groepen toestaan specifieke taken uit te voeren, zoals het beheren, bewerken en weergeven van logische apps. Als u hun machtigingen wilt beheren, gebruikt u [RBAC (Azure Role-Based Access Control)](../role-based-access-control/role-assignments-portal.md) zodat u aangepaste of ingebouwde rollen toewijzen aan de leden in uw Azure-abonnement:

* [Logic App Contributor:](../role-based-access-control/built-in-roles.md#logic-app-contributor)hiermee u logische apps beheren, maar u de toegang er niet toe wijzigen.

* [Logic App Operator:](../role-based-access-control/built-in-roles.md#logic-app-operator)hiermee u logische apps lezen, inschakelen en uitschakelen, maar u ze niet bewerken of bijwerken.

Om te voorkomen dat anderen uw logische app wijzigen of verwijderen, u [Azure Resource Lock gebruiken.](../azure-resource-manager/management/lock-resources.md) Deze mogelijkheid voorkomt dat anderen productieresources wijzigen of verwijderen.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Toegang tot geschiedenisgegevens

Tijdens een logische app-run [worden](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) alle gegevens versleuteld tijdens het transport met tls (Transport Layer Security) en [in rust.](../security/fundamentals/encryption-atrest.md) Wanneer uw logische app klaar is met hardlopen, u de geschiedenis voor die uitvoering bekijken, inclusief de stappen die samen met de status, duur, invoer en uitvoer voor elke actie zijn uitgevoerd. Dit uitgebreide detail geeft inzicht in hoe uw logische app is uitgevoerd en waar u eventuele problemen oplossen.

Wanneer u de runsgeschiedenis van uw logische app bekijkt, verifieert Logic Apps uw toegang en biedt het vervolgens koppelingen naar de invoer en uitvoer voor de aanvragen en antwoorden voor elke run. Voor acties die wachtwoorden, geheimen, sleutels of andere gevoelige informatie verwerken, wilt u echter voorkomen dat anderen die gegevens kunnen bekijken en openen. Als uw logica-app bijvoorbeeld een geheim van [Azure Key Vault](../key-vault/key-vault-overview.md) krijgt om te gebruiken bij het verifiëren van een HTTP-actie, wilt u dat geheim uit de weergave verbergen.

Als u de toegang tot de ingangen en uitvoer in de rungeschiedenis van uw logische app wilt beheren, hebt u de volgende opties:

* [Toegang per IP-adresbereik beperken](#restrict-ip).

  Met deze optie u de toegang tot de geschiedenis beveiligen op basis van de aanvragen uit een specifiek IP-adresbereik.

* [Verberg gegevens voor runhistory met behulp van verduistering](#obfuscate).

  In veel triggers en acties u hun invoer, uitvoer of beide verbergen voor de rungeschiedenis van een logische app.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Toegang per IP-adresbereik beperken

U de toegang tot de invoer en uitvoer in de rungeschiedenis van uw logische app beperken, zodat alleen aanvragen uit specifieke IP-adresbereiken die gegevens kunnen weergeven. Als u bijvoorbeeld wilt voorkomen dat iemand toegang krijgt tot ingangen en uitvoer, geeft u een IP-adresbereik op, zoals `0.0.0.0-0.0.0.0`. Alleen een persoon met beheerdersmachtigingen kan deze beperking verwijderen, die de mogelijkheid biedt voor "just-in-time" toegang tot de gegevens van uw logische app. U de IP-bereiken opgeven die u wilt beperken door de Azure-portal te gebruiken of in een Azure Resource Manager-sjabloon die u gebruikt voor de implementatie van logische apps.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>IP-bereiken in Azure-portal beperken

1. Open uw logische app in de Logische App Designer in de Azure-portal.

1. Selecteer in het menu van uw logische app onder **Instellingen**de optie **Werkstroominstellingen**.

1. Selecteer onder **Toegangscontrole-configuratie** > **Toegestane binnenkomende IP-adressen** **specifieke IP-bereiken**.

1. Geef onder **IP-bereiken voor inhoud**de IP-adresbereiken op die toegang hebben tot inhoud van ingangen en uitvoer. 

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>IP-bereiken beperken in azure resourcemanager-sjabloon

Als u [de implementatie van logische apps automatiseert met behulp van Resource Manager-sjablonen,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u de IP-bereiken opgeven met behulp van de `accessControl` sectie met de sectie in de `contents` resourcedefinitie van uw logische app, bijvoorbeeld:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Gegevens verbergen voor rungeschiedenis met behulp van verduistering

Veel triggers en acties hebben instellingen om invoer, uitvoer of beide te verbergen voor de rungeschiedenis van een logische app. Hier volgen enkele [overwegingen om te controleren](#obfuscation-considerations) wanneer u deze instellingen gebruikt om u te helpen deze gegevens te beveiligen.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>Ingangen en uitgangen in de ontwerper verbergen

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

   ![Logische app openen in Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Selecteer op de trigger of actie waar u gevoelige gegevens wilt verbergen de knop ellipsen (**... )** en selecteer **Vervolgens Instellingen**.

   ![Trigger- of actie-instellingen openen](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Schakel **beveiligde ingangen,** **veilige uitvoer**of beide in. Wanneer u klaar bent, selecteert u **Gereed**.

   ![Schakel 'Beveiligde ingangen' of 'Veilige uitvoer' in](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   De actie of trigger toont nu een vergrendelingspictogram in de titelbalk.

   ![Op het pictogram Vergrendelen van actie of trigger](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens die beveiligde uitvoer van eerdere acties vertegenwoordigen, tonen ook vergrendelingspictogrammen. Wanneer u bijvoorbeeld een dergelijke uitvoer selecteert in de lijst met dynamische inhoud die u in een actie wilt gebruiken, wordt in dat token een vergrendelingspictogram weergegeven.

   ![Token selecteren voor beveiligde uitvoer](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Nadat de logische app is uitgevoerd, u de geschiedenis voor die uitvoering bekijken.

   1. Selecteer in het **deelvenster Overzicht** van de logische app de run die u wilt weergeven.

   1. Vouw in het deelvenster **Uitvoeren van de Logica-app** de acties uit die u wilt controleren.

      Als u ervoor kiest om zowel ingangen als uitvoer te verbergen, worden deze waarden nu verborgen weergegeven.

      ![Verborgen ingangen en uitgangen in de rungeschiedenis](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>Invoer en uitvoer verbergen in de codeweergave

Voeg in de onderliggende trigger- `runtimeConfiguration.secureData.properties` of actiedefinitie de array toe of werk deze bij met een of beide waarden:

* `"inputs"`: Beveiligt ingangen in de run geschiedenis.
* `"outputs"`: Beveiligt uitvoer in de rungeschiedenis.

Hier volgen enkele [overwegingen om te controleren](#obfuscation-considerations) wanneer u deze instellingen gebruikt om u te helpen deze gegevens te beveiligen.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Overwegingen bij het verbergen van ingangen en uitgangen

* Wanneer u de invoer of uitvoer op een trigger of actie verduistert, verzendt Logic Apps de beveiligde gegevens niet naar Azure Log Analytics. U ook geen [bijgehouden eigenschappen](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) toevoegen aan die trigger of actie voor controle.

* De [Logic Apps API voor het verwerken van werkstroomgeschiedenis](https://docs.microsoft.com/rest/api/logic/) retourneert geen beveiligde uitvoer.

* Als u uitvoer wilt verbergen voor een actie die invoer verduistert of uitvoer expliciet verduistert, schakelt u **handmatig Beveiligde uitvoer** in die actie in.

* Zorg ervoor dat u **Beveiligde invoer** of **Beveiligde uitvoer** inschakelt in downstream-acties waarbij u verwacht dat de rungeschiedenis die gegevens verduistert.

  **Instelling Veilige uitvoer**

  Wanneer u Secure **Outputs** handmatig inschakelt in een trigger of actie, beveiligt Logic Apps deze uitvoer in de runhistory. Als een downstreamactie deze beveiligde uitvoer expliciet gebruikt als invoer, verbergt Logic Apps de ingangen van deze actie in de run-geschiedenis, maar schakelt de instelling **voor beveiligde invoer** van de actie niet *in.*

  ![Beveiligde uitgangen als inputs en downstream impact op de meeste acties](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  De acties Compose, Parse JSON en Response hebben alleen de instelling **Veilige invoer.** Wanneer de instelling is ingeschakeld, worden ook de uitvoer van deze acties verborgen. Als deze acties expliciet de upstream beveiligde uitgangen als ingangen gebruiken, verbergt Logic Apps de ingangen en uitvoer van deze acties, maar schakelt deze instelling voor **beveiligde invoer** van deze acties *niet in.* Als een downstreamactie expliciet de verborgen uitvoer van de acties Compose, Parse JSON of Response als invoer gebruikt, verbergt Logic Apps *de ingangen of uitvoer van deze downstream-actie niet.*

  ![Beveiligde uitgangen als inputs met downstream-impact op specifieke acties](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Instelling Veilige invoer**

  Wanneer u handmatig **Beveiligde invoer** inschakelt in een trigger of actie, beveiligt Logic Apps deze ingangen in de run-geschiedenis. Als een downstreamactie expliciet de zichtbare uitvoer van die trigger of actie als invoer gebruikt, verbergt Logic Apps de ingangen van deze downstream-actie in de run-geschiedenis, maar *schakelt het geen* beveiligde **invoer** in deze actie in en verbergt het de uitvoer van deze actie niet.

  ![Beveiligde ingangen en downstream-impact op de meeste acties](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Als de acties Compose, Parse JSON en Response expliciet de zichtbare uitvoer van de trigger of actie met de beveiligde invoer gebruiken, verbergt Logic Apps de ingangen en uitvoer van deze acties, maar schakelt deze actie de **instelling Secure Inputs** niet *in.* Als een downstreamactie expliciet de verborgen uitvoer van de acties Compose, Parse JSON of Response als invoer gebruikt, verbergt Logic Apps *de ingangen of uitvoer van deze downstream-actie niet.*

  ![Beveiligde inputs en downstream-impact op specifieke acties](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Toegang tot parameteringangen

Als u in verschillende omgevingen implementeert, u overwegen de waarden in uw werkstroomdefinitie te parameteriseren die variëren op basis van die omgevingen. Op die manier u hardgecodeerde gegevens vermijden door een [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md) te gebruiken om uw logische app te implementeren, gevoelige gegevens te beschermen door beveiligde parameters te definiëren en die gegevens als afzonderlijke ingangen door de [parameters van](../azure-resource-manager/templates/template-parameters.md) de sjabloon te geven met behulp van een [parameterbestand.](../azure-resource-manager/templates/parameter-files.md)

Als u bijvoorbeeld HTTP-acties verifieert met [Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication)u de parameters definiëren en verbergen die de client-id en het clientgeheim accepteren die worden gebruikt voor verificatie. Als u deze parameters wilt definiëren `parameters` in uw logische app, gebruikt u de sectie in de werkstroomdefinitie van uw logische app en resourcebeheersjabloon voor implementatie. Als u parameterwaarden wilt verbergen die u niet wilt weergeven bij het bewerken van `securestring` `secureobject` uw logische app of het weergeven van run-geschiedenis, definieert u de parameters met behulp van de code of typ u codering indien nodig. Parameters met dit type worden niet geretourneerd met de resourcedefinitie en zijn niet toegankelijk bij het bekijken van de bron na implementatie. Als u deze parameterwaarden wilt `@parameters('<parameter-name>')` openen tijdens de uitvoering, gebruikt u de expressie in uw werkstroomdefinitie. Deze expressie wordt alleen geëvalueerd tijdens uitvoering en wordt beschreven door de taal van de [werkstroomdefinitie](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Als u een parameter gebruikt in een aanzoekkop of -hoofd, kan die parameter zichtbaar zijn wanneer u de runhistory en uitgaande HTTP-aanvraag van uw logische app bekijkt. Zorg ervoor dat u ook uw inhoudstoegangsbeleid dienovereenkomstig instelt. U ook [verduistering](#obfuscate) gebruiken om invoer en uitvoer in uw run-geschiedenis te verbergen. Autorisatiekoppen zijn nooit zichtbaar via ingangen of uitgangen. Dus als daar een geheim wordt gebruikt, is dat geheim niet terug te vinden.

Zie voor meer informatie deze secties in dit onderwerp:

* [Parameters beveiligen in werkstroomdefinities](#secure-parameters-workflow)
* [Gegevens verbergen voor rungeschiedenis met behulp van verduistering](#obfuscate)

Als u [de implementatie van logische apps automatiseert met behulp van Resource Manager-sjablonen,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u beveiligde [sjabloonparameters](../azure-resource-manager/templates/template-parameters.md)definiëren, die worden geëvalueerd bij implementatie, met behulp van de `securestring` typen en `secureobject` typen. Als u sjabloonparameters wilt definiëren, `parameters` gebruikt u de sectie op het hoogste `parameters` niveau van uw sjabloon, die gescheiden is en verschilt van de sectie van uw werkstroomdefinitie. Als u de waarden voor sjabloonparameters wilt opgeven, gebruikt u een afzonderlijk [parameterbestand](../azure-resource-manager/templates/parameter-files.md).

Als u bijvoorbeeld geheimen gebruikt, u beveiligde sjabloonparameters definiëren en gebruiken die deze geheimen ophalen uit [Azure Key Vault](../key-vault/key-vault-overview.md) bij implementatie. U vervolgens verwijzen naar de sleutelkluis en het geheim in uw parameterbestand. Zie deze onderwerpen voor meer informatie:

* [Gevoelige waarden doorgeven bij implementatie met Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Parameters beveiligen in Azure Resource Manager-sjablonen](#secure-parameters-deployment-template) later in dit onderwerp

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Parameters beveiligen in werkstroomdefinities

Als u gevoelige informatie wilt beschermen in de werkstroomdefinitie van uw logica-app, gebruikt u beveiligde parameters zodat deze informatie niet zichtbaar is nadat u uw logische app hebt opgeslagen. Stel dat u een HTTP-actie hebt, vereist basisverificatie, waarbij een gebruikersnaam en wachtwoord worden gebruikt. In de werkstroomdefinitie `parameters` definieert de sectie de `basicAuthPasswordParam` parameters en `basicAuthUsernameParam` parameters met behulp van het `securestring` type. De actiedefinitie verwijst vervolgens naar `authentication` deze parameters in de sectie.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Parameters beveiligen in Azure Resource Manager-sjablonen

Een [resourcemanagersjabloon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) voor een `parameters` logische app heeft meerdere secties. Als u wachtwoorden, sleutels, geheimen en andere gevoelige informatie wilt beveiligen, definieert `securestring` `secureobject` u beveiligde parameters op sjabloonniveau en werkstroomdefinitieniveau met behulp van de code of type. U deze waarden vervolgens opslaan in [Azure Key Vault](../key-vault/key-vault-overview.md) en het [parameterbestand](../azure-resource-manager/templates/parameter-files.md) gebruiken om naar de sleutelkluis en het geheim te verwijzen. Uw sjabloon haalt die informatie vervolgens op bij de implementatie. Zie [Gevoelige waarden doorgeven bij implementatie voor](../azure-resource-manager/templates/key-vault-parameter.md)meer informatie met Azure Key Vault .

Hier vindt u `parameters` meer informatie over deze secties:

* Op het hoogste niveau van `parameters` de sjabloon definieert een sectie de parameters voor de waarden die de sjabloon bij de *implementatie*gebruikt. Deze waarden kunnen bijvoorbeeld verbindingstekenreeksen bevatten voor een specifieke implementatieomgeving. U deze waarden vervolgens opslaan in een afzonderlijk [parameterbestand](../azure-resource-manager/templates/parameter-files.md), waardoor het wijzigen van deze waarden eenvoudiger wordt.

* Binnen de resourcedefinitie van uw logische app, `parameters` maar buiten uw werkstroomdefinitie, geeft een sectie de waarden voor de parameters van uw werkstroomdefinitie op. In deze sectie u deze waarden toewijzen door sjabloonexpressies te gebruiken die verwijzen naar de parameters van uw sjabloon. Deze expressies worden geëvalueerd bij implementatie.

* In de werkstroomdefinitie definieert een `parameters` sectie de parameters die uw logische app gebruikt tijdens runtime. U vervolgens verwijzen naar deze parameters in de werkstroom van uw logische app met behulp van werkstroomdefinitieexpressies, die bij uitvoering worden geëvalueerd.

Deze voorbeeldsjabloon met meerdere beveiligde parameterdefinities die het `securestring` type gebruiken:

| Parameternaam | Beschrijving |
|----------------|-------------|
| `TemplatePasswordParam` | Een sjabloonparameter die een wachtwoord accepteert dat vervolgens wordt `basicAuthPasswordParam` doorgegeven aan de parameter van de werkstroomdefinitie |
| `TemplateUsernameParam` | Een sjabloonparameter die een gebruikersnaam accepteert die vervolgens wordt `basicAuthUserNameParam` doorgegeven aan de parameter van de werkstroomdefinitie |
| `basicAuthPasswordParam` | Een parameter voor werkstroomdefinitie die het wachtwoord voor basisverificatie in een HTTP-actie accepteert |
| `basicAuthUserNameParam` | Een parameter voor werkstroomdefinitie die de gebruikersnaam voor basisverificatie in een HTTP-actie accepteert |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Toegang tot services en systemen die vanuit logische apps worden aangeroepen

Hier volgen enkele manieren waarop u eindpunten beveiligen die oproepen of verzoeken van uw logische app ontvangen:

* Voeg verificatie toe aan uitgaande aanvragen.

  Wanneer u werkt met een http-gebaseerde trigger of actie die uitgaande oproepen voert, zoals HTTP, HTTP + Swagger of Webhook, u verificatie toevoegen aan het verzoek dat door uw logische app wordt verzonden. U bijvoorbeeld de volgende verificatietypen gebruiken:

  * [Basisverificatie](#basic-authentication)

  * [Verificatie van clientcertificaat](#client-certificate-authentication)

  * [Active Directory OAuth-verificatie](#azure-active-directory-oauth-authentication)

  * [Verificatie van beheerde identiteit](#managed-identity-authentication)
  
  Zie [Verificatie toevoegen aan uitgaande gesprekken](#add-authentication-outbound) later in dit onderwerp voor meer informatie.

* Beperk de toegang vanaf ip-adressen van logische apps.

  Alle oproepen naar eindpunten van logische apps zijn afkomstig van specifieke aangewezen IP-adressen die zijn gebaseerd op de regio's van uw logische apps. U filtering toevoegen die alleen aanvragen van die IP-adressen accepteert. Zie Limieten en configuratie [voor Azure Logic Apps voor](logic-apps-limits-and-config.md#configuration)meer toegang tot deze IP-adressen.

* Verbeter de beveiliging voor verbindingen met on-premises systemen.

  Azure Logic Apps biedt integratie met deze services om veiligere en betrouwbaardere on-premises communicatie te bieden.

  * On-premises gegevensgateway

    Veel beheerde connectors in Azure Logic Apps vergemakkelijken beveiligde verbindingen met on-premises systemen, zoals Bestandssysteem, SQL, SharePoint en DB2. De gateway verzendt gegevens van on-premises bronnen op versleutelde kanalen via de Azure Service Bus. Al het verkeer is afkomstig van beveiligd uitgaand verkeer van de gatewayagent. Ontdek [hoe de on-premises datagateway werkt.](logic-apps-gateway-install.md#gateway-cloud-service)

  * Verbinding maken via Azure API-beheer

    [Azure API Management](../api-management/api-management-key-concepts.md) biedt on-premises verbindingsopties, zoals site-to-site virtual private network en ExpressRoute-integratie voor beveiligde proxy en communicatie naar on-premises systemen. Vanuit de workflow van uw logische app in de Logic App Designer u een API selecteren die wordt blootgesteld door API Management, die snelle toegang biedt tot on-premises systemen.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Verificatie toevoegen aan uitgaande gesprekken

HTTP- en HTTPS-eindpunten ondersteunen verschillende soorten verificatie. Op basis van de trigger of actie die u gebruikt om uitgaande oproepen of verzoeken te voeren die toegang hebben tot deze eindpunten, u kiezen uit verschillende bereiken van verificatietypen. Gebruik beveiligde parameters en codeer gegevens indien nodig om ervoor te zorgen dat u gevoelige informatie beschermt die uw logica-app verwerkt. Zie [Toegang tot parameteringangen](#secure-action-parameters)voor meer informatie over het gebruik en beveiligen van parameters.

> [!NOTE]
> In de logic-appdesigner kan de eigenschap **Verificatie** worden verborgen op sommige triggers en acties waarbij u het verificatietype opgeven. Als u de eigenschap in deze gevallen wilt laten weergeven, opent u op de trigger of actie de lijst **Nieuwe parameter toevoegen** en selecteert u **Verificatie**. Zie [Toegang verifiëren met beheerde identiteit voor](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)meer informatie .

| Verificatietype | Ondersteund door |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Clientcertificaat](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Onbewerkt](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Beheerde identiteit](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Basisverificatie

Als de optie [Basis](../active-directory-b2c/secure-rest-api.md) beschikbaar is, geeft u de volgende eigenschapswaarden op:

| Eigenschap (ontwerper) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | Basic | Het te gebruiken verificatietype |
| **Gebruikersnaam** | `username` | Ja | <*gebruikersnaam*>| De gebruikersnaam voor het verifiëren van toegang tot het eindpunt van de doelservice |
| **Wachtwoord** | `password` | Ja | <*Wachtwoord*> | Het wachtwoord voor het verifiëren van de toegang tot het eindpunt van de doelservice |
||||||

Wanneer u [beveiligde parameters](#secure-action-parameters) gebruikt om gevoelige informatie te verwerken en te beschermen, bijvoorbeeld in een Azure Resource [Manager-sjabloon voor het automatiseren van implementatie,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u expressies gebruiken om toegang te krijgen tot deze parameterwaarden tijdens uitvoering. In dit voorbeeld geeft `type` http-actiedefinitie de verificatie op als `Basic` en gebruikt u de functie [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameterwaarden op te halen:

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

### <a name="client-certificate-authentication"></a>Clientcertificaatverificatie

Als de optie [Clientcertificaat](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) beschikbaar is, geeft u de volgende eigenschapswaarden op:

| Eigenschap (ontwerper) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | **Clientcertificaat** <br>of <br>`ClientCertificate` | Het verificatietype dat moet worden gebruikt voor TLS/SSL-clientcertificaten. Hoewel zelfondertekende certificaten worden ondersteund, worden zelfondertekende certificaten voor TLS/SSL niet ondersteund. |
| **Pfx Pfx** | `pfx` | Ja | <*gecodeerde pfx-bestandsinhoud*> | De basis64-gecodeerde inhoud uit een PFX-bestand (Personal Information Exchange) <p><p>Als u het PFX-bestand wilt converteren naar een basisindeling met 64 code, u PowerShell gebruiken door de volgende stappen te volgen: <p>1. Sla de inhoud van het certificaat op in een variabele: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converteer de `ToBase64String()` certificaatinhoud met de functie en sla die inhoud op in een tekstbestand: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Wachtwoord** | `password`| Nee | <*password-for-pfx-bestand*> | Het wachtwoord voor toegang tot het PFX-bestand |
|||||

Wanneer u [beveiligde parameters](#secure-action-parameters) gebruikt om gevoelige informatie te verwerken en te beschermen, bijvoorbeeld in een Azure Resource [Manager-sjabloon voor het automatiseren van implementatie,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u expressies gebruiken om toegang te krijgen tot deze parameterwaarden tijdens uitvoering. In dit voorbeeld geeft `type` http-actiedefinitie de verificatie op als `ClientCertificate` en gebruikt u de functie [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameterwaarden op te halen:

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

Zie de volgende onderwerpen voor meer informatie over het beveiligen van services met verificatie van clientcertificaaten:

* [Beveiliging voor API's verbeteren door verificatie van clientcertificaat in Azure API-beheer te gebruiken](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Beveiliging voor back-endservices verbeteren door verificatie van clientcertificaat in Azure API-beheer te gebruiken](../api-management/api-management-howto-mutual-certificates.md)
* [Verbeter de beveiliging van uw RESTfuL-service met behulp van clientcertificaten](../active-directory-b2c/secure-rest-api.md)
* [Certificaatreferenties voor toepassingsverificatie](../active-directory/develop/active-directory-certificate-credentials.md)
* [Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth-verificatie

Als de optie [Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) beschikbaar is, geeft u de volgende eigenschapswaarden op:

| Eigenschap (ontwerper) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | **Active Directory OAuth** <br>of <br>`ActiveDirectoryOAuth` | Het te gebruiken verificatietype. Logic Apps volgt momenteel het [OAuth 2.0-protocol.](../active-directory/develop/v2-overview.md) |
| **Instantie** | `authority` | Nee | <*URL-voor-autoriteit-token-uitgever*> | De URL voor de autoriteit die het verificatietoken verstrekt. Standaard is `https://login.windows.net`deze waarde . |
| **Tenant** | `tenant` | Ja | <*tenant-ID*> | De tenant-id voor de Azure AD-tenant |
| **Doelgroep** | `audience` | Ja | <*resource-to-authorized*> | De bron die u wilt gebruiken voor autorisatie, bijvoorbeeld`https://management.core.windows.net/` |
| **Client-id** | `clientId` | Ja | <*client-ID*> | De client-id voor de app die autorisatie aanvraagt |
| **Referentietype** | `credentialType` | Ja | Certificaat <br>of <br>Geheim | Het referentietype dat de client gebruikt voor het aanvragen van autorisatie. Deze eigenschap en waarde worden niet weergegeven in de onderliggende definitie van uw logische app, maar bepaalt de eigenschappen die worden weergegeven voor het geselecteerde referentietype. |
| **Geheim** | `secret` | Ja, maar alleen voor het type 'Geheim' | <*klantgeheim*> | Het geheim van de klant voor het aanvragen van autorisatie |
| **Pfx Pfx** | `pfx` | Ja, maar alleen voor het referentietype 'Certificaat'. | <*gecodeerde pfx-bestandsinhoud*> | De basis64-gecodeerde inhoud uit een PFX-bestand (Personal Information Exchange) |
| **Wachtwoord** | `password` | Ja, maar alleen voor het referentietype 'Certificaat'. | <*password-for-pfx-bestand*> | Het wachtwoord voor toegang tot het PFX-bestand |
|||||

Wanneer u [beveiligde parameters](#secure-action-parameters) gebruikt om gevoelige informatie te verwerken en te beschermen, bijvoorbeeld in een Azure Resource [Manager-sjabloon voor het automatiseren van implementatie,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u expressies gebruiken om toegang te krijgen tot deze parameterwaarden tijdens uitvoering. In dit voorbeeld geeft `type` http-actiedefinitie de verificatie op als `ActiveDirectoryOAuth`, het referentietype als `Secret`, en gebruikt de functie [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameterwaarden op te halen:

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

### <a name="raw-authentication"></a>Raw-verificatie

Als de optie **Raw** beschikbaar is, u dit verificatietype gebruiken wanneer u [verificatieschema's](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) moet gebruiken die het [OAuth 2.0-protocol](https://oauth.net/2/)niet volgen. Met dit type maakt u handmatig de waarde van de autorisatiekopwaarde die u met de uitgaande aanvraag verzendt en geeft u die kopwaarde op in uw trigger of actie.

Hier vindt u bijvoorbeeld een voorbeeldkop voor een HTTPS-verzoek dat het [OAuth 1.0-protocol](https://tools.ietf.org/html/rfc5849)volgt:

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Geef deze eigenschapswaarden op in de trigger of actie die onbewerkte verificatie ondersteunt:

| Eigenschap (ontwerper) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
|---------------------|-----------------|----------|-------|-------------|
| **Verificatie** | `type` | Ja | Onbewerkt | Het te gebruiken verificatietype |
| **Waarde** | `value` | Ja | <*autorisatie-header-waarde*> | De machtigingskopwaarde die moet worden gebruikt voor verificatie |
||||||

Wanneer u [beveiligde parameters](#secure-action-parameters) gebruikt om gevoelige informatie te verwerken en te beschermen, bijvoorbeeld in een Azure Resource [Manager-sjabloon voor het automatiseren van implementatie,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u expressies gebruiken om toegang te krijgen tot deze parameterwaarden tijdens uitvoering. In dit voorbeeld geeft `type` http-actiedefinitie de verificatie op als `Raw`de functie [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameterwaarden op te halen:

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

Als de optie [Beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) beschikbaar is, kan uw logische app de door het systeem toegewezen identiteit of *een enkele* handmatig aan gebruikers toegewezen identiteit gebruiken voor het verifiëren van toegang tot bronnen in andere Azure AD-tenants (Azure AD) zonder u aan te melden. Azure beheert deze identiteit voor u en helpt uw referenties te beveiligen omdat u geen geheimen hoeft op te geven of te roteren. Meer informatie over [Azure-services die beheerde identiteiten voor Azure AD-verificatie ondersteunen.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Voordat uw logische app een beheerde identiteit kan gebruiken, volgt u de stappen in [Toegang tot Azure-bronnen verifiëren met beheerde identiteiten in Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Met deze stappen u de beheerde identiteit van uw logische app inschakelen en de toegang van die identiteit tot de doel-Azure-bron instellen.

1. Voordat een Azure-functie een beheerde identiteit kan gebruiken, [schakelt u eerst verificatie in voor Azure-functies.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

1. Geef deze eigenschapswaarden op in de trigger of actie waarin u de beheerde identiteit wilt gebruiken:

   | Eigenschap (ontwerper) | Eigenschap (JSON) | Vereist | Waarde | Beschrijving |
   |---------------------|-----------------|----------|-------|-------------|
   | **Verificatie** | `type` | Ja | **Beheerde identiteit** <br>of <br>`ManagedServiceIdentity` | Het te gebruiken verificatietype |
   | **Beheerde identiteit** | `identity` | Ja | * **Beheerde identiteit met systeemtoegewezen** <br>of <br>`SystemAssigned` <p><p>* <*door de gebruiker toegewezen identiteit-naam*> | De beheerde identiteit te gebruiken |
   | **Doelgroep** | `audience` | Ja | <*target-resource-ID*> | De resource-id voor de doelbron die u wilt openen. <p>Hiermee `https://storage.azure.com/` worden bijvoorbeeld de toegangstokens voor verificatie geldig gemaakt voor alle opslagaccounts. U echter ook een URL van `https://fabrikamstorageaccount.blob.core.windows.net` de hoofdservice opgeven, bijvoorbeeld voor een specifiek opslagaccount. <p>**Opmerking:** de eigenschap **Doelgroep** is mogelijk verborgen in sommige triggers of acties. Als u deze eigenschap zichtbaar wilt maken, opent u in de trigger of actie de lijst **Nieuwe parameter toevoegen** en selecteert u **Doelgroep**. <p><p>**Belangrijk:** zorg ervoor dat deze doelbron-id *exact overeenkomt met* de waarde die Azure AD verwacht, inclusief eventuele vereiste trailing slashes. De `https://storage.azure.com/` bron-id voor alle Azure Blob Storage-accounts vereist dus een trailing slash. De resource-id voor een specifiek opslagaccount vereist echter geen trailing slash. Zie [Azure-services die Azure AD ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)als u deze bron-id's wilt vinden. |
   |||||

   Wanneer u [beveiligde parameters](#secure-action-parameters) gebruikt om gevoelige informatie te verwerken en te beschermen, bijvoorbeeld in een Azure Resource [Manager-sjabloon voor het automatiseren van implementatie,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)u expressies gebruiken om toegang te krijgen tot deze parameterwaarden tijdens uitvoering. In dit voorbeeld geeft `type` http-actiedefinitie de verificatie op als `ManagedServiceIdentity` en gebruikt u de functie [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) om de parameterwaarden op te halen:

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
* [Logische apps bewaken](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Logische app-fouten en -problemen diagnosticeren](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
