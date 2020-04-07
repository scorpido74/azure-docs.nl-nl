---
title: Parameters gebruiken om dynamische blauwdrukken te maken
description: Leer meer over statische en dynamische parameters en hoe u deze gebruiken om veilige en dynamische blauwdrukken te maken.
ms.date: 03/12/2019
ms.topic: conceptual
ms.openlocfilehash: 36735d71b746301819e5079aba1697b55fe5e183
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677589"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Dynamische blauwdrukken maken door middel van parameters

Een volledig gedefinieerde blauwdruk met verschillende artefacten (zoals resourcegroepen, Resource Manager-sjablonen, beleidsregels of roltoewijzingen) biedt de snelle creatie en consistente creatie van objecten binnen Azure. Azure Blueprints ondersteunt parameters om flexibel gebruik van deze herbruikbare ontwerppatronen en containers mogelijk te maken. De parameter creëert flexibiliteit, zowel tijdens definitie als toewijzing, om eigenschappen te wijzigen op de artefacten die door de blauwdruk worden geïmplementeerd.

Een eenvoudig voorbeeld is het artefact van de brongroep. Wanneer een resourcegroep wordt gemaakt, heeft deze twee vereiste waarden die moeten worden opgegeven: naam en locatie. Wanneer u een resourcegroep toevoegt aan uw blauwdruk, als er geen parameters bestonden, definieert u die naam en locatie voor elk gebruik van de blauwdruk. Deze herhaling zou ertoe leiden dat elk gebruik van de blauwdruk artefacten in dezelfde brongroep maakt. Resources binnen die resourcegroep worden gedupliceerd en veroorzaken een conflict.

> [!NOTE]
> Het is geen probleem voor twee verschillende blauwdrukken om een resourcegroep met dezelfde naam op te nemen.
> Als er al een resourcegroep in een blauwdruk bestaat, blijft de blauwdruk de gerelateerde artefacten in die resourcegroep maken. Dit kan leiden tot een conflict omdat er geen twee resources met dezelfde naam en resourcetype kunnen bestaan binnen een abonnement.

De oplossing voor dit probleem zijn parameters. Met Azure Blueprints u de waarde voor elke eigenschap van het artefact definiëren tijdens toewijzing aan een abonnement. De parameter maakt het mogelijk om een blauwdruk opnieuw te gebruiken die een resourcegroep en andere bronnen binnen één abonnement maakt zonder conflict.

## <a name="blueprint-parameters"></a>Blauwdrukparameters

Via de REST API kunnen parameters worden gemaakt op de blauwdruk zelf. Deze parameters zijn anders dan de parameters op elk van de ondersteunde artefacten. Wanneer een parameter wordt gemaakt op de blauwdruk, kan deze worden gebruikt door de artefacten in die blauwdruk. Een voorbeeld hiervan is het voorvoegsel voor het benoemen van de resourcegroep. Het artefact kan de blauwdrukparameter gebruiken om een "meestal dynamische" parameter te maken. Aangezien de parameter ook tijdens de toewijzing kan worden gedefinieerd, zorgt dit patroon voor een consistentie die kan voldoen aan naamgevingsregels. Zie voor stappen [statische parameters instellen - parameter op blauwdrukniveau](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString- en secureObject-parameters gebruiken

Terwijl een Resource _artifact_ Manager-sjabloonartefact parameters van de **secureString-** en **secureObject-typen** ondersteunt, vereist Azure Blueprints dat elk van deze typen is verbonden met een Azure Key Vault.
Deze veiligheidsmaatregel voorkomt de onveilige praktijk van het opslaan van geheimen samen met de Blauwdruk en moedigt de werkgelegenheid van veilige patronen. Azure Blueprints ondersteunt deze beveiligingsmaatregel en detecteert de opname van een beveiligde parameter in een _resourcemanager-sjabloonartefact._ De service vraagt vervolgens tijdens toewijzing naar de volgende Key Vault-eigenschappen per gedetecteerde beveiligde parameter:

- Key Vault-bron-id
- Geheime naam Key Vault
- Key Vault geheime versie

Als de blauwdruktoewijzing een **door het systeem toegewezen beheerde identiteit**gebruikt, moet de sleutelkluis _waarnaar wordt_ verwezen, bestaan in hetzelfde abonnement waaraan de blauwdrukdefinitie is toegewezen.

Als de blauwdruktoewijzing een **door de gebruiker toegewezen beheerde identiteit**gebruikt, _kan_ de sleutelkluis waarnaar wordt verwezen, bestaan in een gecentraliseerd abonnement. De beheerde identiteit moet de juiste rechten op de Sleutelkluis krijgen voordat de blauwdrukwordt toegewezen.

> [!IMPORTANT]
> In beide gevallen moet de Key Vault toegang tot Azure Resource Manager hebben **voor sjabloonimplementatie** die is geconfigureerd op de pagina **Access-beleid.** Zie [Key Vault - Sjabloonimplementatie inschakelen](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment)voor een routebeschrijving over het inschakelen van deze functie.

Zie [Overzicht van Key Vault](../../../key-vault/key-vault-overview.md)voor meer informatie over Azure Key Vault.

## <a name="parameter-types"></a>Parametertypen

### <a name="static-parameters"></a>Statische parameters

Een parameterwaarde die is gedefinieerd in de definitie van een blauwdruk wordt een **statische parameter**genoemd, omdat elk gebruik van de blauwdruk het artefact implementeert met behulp van die statische waarde. In het voorbeeld van de resourcegroep heeft dit geen zin voor de naam van de resourcegroep, maar is het zinvol voor de locatie. Vervolgens maakt elke toewijzing van de blauwdruk de resourcegroep, hoe deze ook wordt genoemd tijdens de toewijzing, op dezelfde locatie. Deze flexibiliteit stelt u in staat om selectief te zijn in wat u definieert als vereist, versus wat kan worden gewijzigd tijdens de toewijzing.

#### <a name="setting-static-parameters-in-the-portal"></a>Statische parameters instellen in de portal

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik op een bestaande blauwdruk en klik vervolgens op **Blauwdruk bewerken** OF klik + **Blueprint maken** en vul de informatie in op het tabblad **Basisbeginselen.**

1. Klik **op Volgende: Artefacten** of klik op het tabblad **Artefacten.**

1. Artefacten die aan de blauwdruk zijn toegevoegd en met parameteropties, geven **X van Y-parameters weer die zijn ingevuld** in de kolom **Parameters.** Klik op de artefactrij om de artefactparameters te bewerken.

   ![Blauwdrukparameters voor een blauwdrukdefinitie](../media/parameters/parameter-column.png)

1. Op de pagina **Artefact bewerken** worden waardeopties weergegeven die geschikt zijn voor het artefact waarop is geklikt. Elke parameter op het artefact heeft een titel, een waardevak en een selectievakje. Stel het vak in op onaangevinkt om er een **statische parameter**van te maken. In het onderstaande voorbeeld is alleen _locatie_ een **statische parameter** omdat deze niet is aangevinkt en de naam van _de resourcegroep_ is ingeschakeld.

   ![Statische parameters voor blauwdruk op een blauwdrukartefact](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Statische parameters instellen vanuit REST API

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- Vervang `{YourMG}` door de naam van uw beheergroep
- Vervang `{subscriptionId}` door uw abonnements-ID

##### <a name="blueprint-level-parameter"></a>Parameter Blauwdrukniveau

Bij het maken van een blauwdruk via REST API is het mogelijk om [blauwdrukparameters](#blueprint-parameters)te maken. Gebruik hiervoor de volgende REST API URI- en hoofdindeling:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Zodra een parameter op blauwdrukniveau is gemaakt, kan deze worden gebruikt op artefacten die aan die blauwdruk zijn toegevoegd.
In het volgende voorbeeld VAN REST API wordt een artefact voor roltoewijzing op de blauwdruk gemaakt en wordt de parameter blauwdrukniveau gebruikt.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

In dit voorbeeld gebruikt de eigenschap **principalIds** de parameter `[parameters('owners')]` **blauwdrukkenniveau** van eigenaren met behulp van een waarde van . Het instellen van een parameter op een artefact met behulp van een parameter op blauwdrukniveau is nog steeds een voorbeeld van een **statische parameter.** De parameter blauwdrukniveau kan niet worden ingesteld tijdens de blauwdruktoewijzing en heeft dezelfde waarde voor elke toewijzing.

##### <a name="artifact-level-parameter"></a>Parameter Artefact-niveau

Het maken van **statische parameters** op een artefact is `parameters()` vergelijkbaar, maar neemt een rechte waarde in plaats van het gebruik van de functie. In het volgende voorbeeld worden twee statische parameters, **tagName** en tagValue , aan elkaar **gelabelt.** De waarde op elk wordt direct opgegeven en maakt geen gebruik van een functieaanroep.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dynamische parameters

Het tegenovergestelde van een statische parameter is een **dynamische parameter**. Deze parameter wordt niet gedefinieerd op de blauwdruk, maar wordt in plaats daarvan gedefinieerd tijdens elke toewijzing van de blauwdruk. In het voorbeeld van de resourcegroep is het gebruik van een **dynamische parameter** zinvol voor de naam van de resourcegroep. Het geeft een andere naam voor elke opdracht van de blauwdruk. Zie de verwijzing [naar blauwdrukfuncties](../reference/blueprint-functions.md) voor een lijst met blauwdrukfuncties.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Dynamische parameters instellen in de portal

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik met de rechtermuisknop op de blauwdruk die u wilt toewijzen. Selecteer **Blauwdruk toewijzen** OF klik op de blauwdruk die u wilt toewijzen en klik vervolgens op de knop Blauwdruk **toewijzen.**

1. Zoek op de pagina **Blauwdruk toewijzen** de sectie **Artefact-parameters.** Elk artefact met ten minste één **dynamische parameter** geeft het artefact en de configuratieopties weer. Geef de vereiste waarden op aan de parameters voordat u de blauwdruk toewijs. In het onderstaande voorbeeld is _Naam_ een **dynamische parameter** die moet worden gedefinieerd om de blauwdruktoewijzing te voltooien.

   ![Dynamische parameter Blauwdruk tijdens blauwdruktoewijzing](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Dynamische parameters instellen vanuit REST API

Het instellen **van dynamische parameters** tijdens de toewijzing gebeurt door de waarde direct in te voeren. In plaats van een functie te gebruiken, zoals [parameters(),](../reference/blueprint-functions.md#parameters)is de opgegeven waarde een geschikte tekenreeks. Artefacten voor een resourcegroep worden gedefinieerd met een sjabloonnaam, **naam**en **locatie-eigenschappen.** Alle andere parameters voor opgenomen artefact worden gedefinieerd onder **parameters** met een ** \<naam\> ** en **waardesleutelpaar.** Als de blauwdruk is geconfigureerd voor een dynamische parameter die niet wordt geleverd tijdens de toewijzing, mislukt de toewijzing.

- REST API-URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Volgende stappen

- Zie de lijst met [blauwdrukfuncties](../reference/blueprint-functions.md).
- Meer informatie over de [levenscyclus van de blauwdruk](lifecycle.md).
- Leer de volgorde van de [blauwdrukvolgorde](sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)