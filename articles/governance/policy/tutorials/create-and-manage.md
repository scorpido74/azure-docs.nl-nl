---
title: 'Tutorial: Build policies to enforce compliance'
description: In this tutorial, you use policies to enforce standards, control costs, maintain security, and impose enterprise wide design principles.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 75a1d892a88f2b5bbdbec2a1b8d525245bb1e86f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482366"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Create and manage policies to enforce compliance

Als u aan uw bedrijfsnormen en serviceovereenkomsten wilt blijven voldoen, is het belangrijk dat u begrijpt hoe u beleidsregels kunt maken en beheren in Azure. In deze zelfstudie leert u hoe u Azure Policy gebruikt om enkele algemene taken voor het maken, toewijzen en beheren van beleid in uw organisatie uit te voeren, zoals:

> [!div class="checklist"]
> - Toewijzen van een beleid om een voorwaarde voor bronnen af te dwingen die u in de toekomst maakt
> - Maken en toewijzen van de definitie van een initiatief om naleving bij te houden voor meerdere bronnen
> - Problemen met een bron die niet voldoet of is geweigerd oplossen
> - Een nieuw beleid binnen een organisatie implementeren

Als u een beleid wilt toewijzen voor het identificeren van de huidige nalevingsstatus van uw bestaande bronnen, raadpleegt u de quickstart-artikelen over dit ontwerp.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="assign-a-policy"></a>Een beleid toewijzen

De eerste stap bij het afdwingen van naleving met Azure Policy bestaat uit het toewijzen van een beleidsdefinitie. Een beleidsdefinitie definieert onder welke voorwaarde een beleid wordt afgedwongen en welke actie moet worden uitgevoerd. In dit voorbeeld wijzen we een ingebouwde beleidsdefinitie toe met de naam *SQL Server-versie 12.0 vereisen* om de voorwaarde af te dwingen dat alle SQL Server-databases v12.0 moeten zijn om aan het beleid te voldoen.

1. Go to the Azure portal to assign policies. Search for and select **Policy**.

   ![Search for Policy in the search bar](../media/create-and-manage/search-policy.png)

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   ![Select Assignments from Policy Overview page](../media/create-and-manage/select-assignments.png)

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   ![Assign a policy definition from Assignments page](../media/create-and-manage/select-assign-policy.png)

1. On the **Assign Policy** page, select the **Scope** by selecting the ellipsis and selecting either a management group or subscription. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Then select **Select** at the bottom of the **Scope** page.

   In dit voorbeeld wordt het abonnement **Contoso** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. U kunt het **Type** van de beleidsdefinitie filteren op *Ingebouwd* om alles te bekijken en de beschrijvingen te lezen.

1. Selecteer **SQL Server-versie 12.0 vereisen**. If you can't find it right away, type **require sql server** into the search box and then press ENTER or select out of the search box. Select **Select** at the bottom of the **Available Definitions** page once you have found and selected the policy definition.

   ![Use search filter to locate a policy](../media/create-and-manage/select-available-definition.png)

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. In dit voorbeeld houden we *SQL Server-versie 12.0 vereisen*. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.
   **Toegewezen door** wordt automatisch ingevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Laat **Beheerde identiteit maken** uitgeschakeld. Dit vak _moet_ worden ingeschakeld wanneer het beleid of initiatief dat wordt toegewezen beleid bevat met het effect [deployIfNotExists](../concepts/effects.md#deployifnotexists). Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [Hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works) voor meer informatie.

1. Selecteer **Toewijzen**.

## <a name="implement-a-new-custom-policy"></a>Een nieuw aangepast beleid implementeren

Nu u een ingebouwde beleidsdefinitie hebt toegewezen, kunt u meer kunt doen met Azure Policy. Maak nu een nieuw aangepast beleid om kosten te besparen door te controleren of virtuele machines die in uw omgeving worden gemaakt niet van de G-serie zijn. Hiermee wordt telkens wanneer een gebruiker in uw organisatie de virtuele machine in de G-serie wil maken, de aanvraag geweigerd.

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

   ![Definition page under Authoring group](../media/create-and-manage/definition-under-authoring.png)

1. Selecteer **+ Beleidsdefinitie** bovenaan de pagina. Met deze knop opent u de pagina **Beleidsdefinitie**.

1. Voer de volgende informatie in:

   - De beheergroep of het abonnement waarin de beleidsdefinitie is opgeslagen. Selecteer met behulp van het weglatingsteken **Definitielocatie**.

     > [!NOTE]
     > Als u van plan bent deze toe te passen op meerdere abonnementen, moet de locatie een beheergroep zijn met de abonnementen waaraan u het beleid toewijst. Hetzelfde geldt voor de definitie van een initiatief.

   - De naam van de beleidsdefinitie - *Vereisen dat VM-SKU's kleiner zijn dan de G-serie*
   - De beschrijving van het doel van de beleidsdefinitie: *Deze beleidsdefinitie dwingt af dat alle virtuele machines die zijn gemaakt in dit bereik, SKU's hebben die kleiner zijn dan de G-serie om kosten te beperken.*
   - Kies een van de bestaande opties (zoals _Compute_) of maak een nieuwe categorie voor deze beleidsdefinitie.
   - Kopieer de volgende JSON-code en werk deze vervolgens naar wens bij:
      - De beleidsparameters.
      - De beleidsregels/-voorwaarden, in dit geval – VM SKU-grootte gelijk aan G-serie
      - Het effect van dit beleid, in dit geval: **Weigeren**.

   Zo moet de JSON eruitzien. Plak uw gewijzigde code in Azure Portal.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   The *field* property in the policy rule must be one of the following values: Name, Type, Location, Tags, or an alias. Een voorbeeld van een alias is mogelijk `"Microsoft.Compute/VirtualMachines/Size"`.

   Meer Azure Policy-voorbeelden vindt u in [Voorbeelden van Azure Policy](../samples/index.md).

1. Selecteer **Opslaan**.

## <a name="create-a-policy-definition-with-rest-api"></a>Een beleidsdefinitie maken met de REST API

You can create a policy with the REST API for Azure Policy Definitions. Met de REST API kunt u beleidsdefinities maken en verwijderen en informatie over bestaande definities ophalen. Gebruik het volgende voorbeeld om een beleidsdefinitie te maken:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Voeg aanvraagtekst toe die soortgelijk is aan het volgende voorbeeld:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Een beleidsdefinitie maken met PowerShell

Before proceeding with the PowerShell example, make sure you've installed the latest version of the Azure PowerShell Az module.

U kunt een beleidsdefinitie maken met de `New-AzPolicyDefinition`-cmdlet.

Als u een beleidsdefinitie wilt maken op basis van een bestand, geeft u het pad naar het bestand op. Gebruik het volgende voorbeeld voor een extern bestand:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Gebruik het volgende voorbeeld voor een lokaal bestand:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Gebruik het volgende voorbeeld om een beleidsdefinitie met een inline regel te maken:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

De uitvoer wordt opgeslagen in een `$definition`-object, dat wordt gebruikt tijdens de toewijzing van configuratiebeleid. In het volgende voorbeeld wordt een beleidsdefinitie met parameters gemaakt:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Beleidsdefinities met PowerShell bekijken

Als u alle beleidsdefinities in uw abonnement wilt weergeven, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Hiermee worden alle beschikbare beleidsdefinities geretourneerd, inclusief ingebouwd beleid. Elk beleid wordt geretourneerd in de volgende indeling:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Een beleidsdefinitie maken met Azure CLI

U kunt een beleidsdefinitie maken met Azure CLI met behulp van de beleidsdefinitieopdracht. Gebruik het volgende voorbeeld om een beleidsdefinitie met een inline regel te maken:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Beleidsdefinities met Azure CLI bekijken

Als u alle beleidsdefinities in uw abonnement wilt weergeven, gebruikt u de volgende opdracht:

```azurecli-interactive
az policy definition list
```

Hiermee worden alle beschikbare beleidsdefinities geretourneerd, inclusief ingebouwd beleid. Elk beleid wordt geretourneerd in de volgende indeling:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Een initiatiefdefinitie maken en toewijzen

Met een initiatiefdefinitie kunt u verschillende beleidsdefinities groeperen om één overkoepelend doel te bereiken. Een initiatief evalueert resources binnen het bereik van de toewijzing op naleving van de opgenomen beleidsregels. Zie het [Overzicht van Azure Policy](../overview.md) voor meer informatie over initiatiefdefinities.

### <a name="create-an-initiative-definition"></a>Een initiatiefdefinitie maken

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

   ![Select definition from the Definitions page](../media/create-and-manage/definition-under-authoring.png)

1. Selecteer **+ Initiatiefdefinitie** bovenaan de pagina. U gaat dan naar de pagina **Initiatiefdefinitie**.

   ![Review initiative definition page](../media/create-and-manage/initiative-definition.png)

1. Gebruik **Definitielocatie** om een beheergroep of abonnement voor het opslaan van de definitie te selecteren. Als de vorige pagina is gericht op een enkele beheergroep of enkel abonnement, wordt **Definitielocatie** automatisch ingevuld.

1. Voer de **naam** en **beschrijving** van het initiatief in.

   Dit voorbeeld zorgt ervoor worden dat bronnen voldoen aan beleidsdefinities over beveiliging. Geef het initiatief de naam **Beveiligen** en stel de volgende beschrijving in: **Dit initiatief is gemaakt voor het afhandelen van alle beleidsregels die zijn gekoppeld aan het beveiligen van resources**.

1. Kies voor **Categorie** uit bestaande opties of maak een nieuwe categorie.

1. Blader door de lijst met **Beschikbare definities** (rechterhelft van de pagina **Initiatiefdefinitie**) en selecteer het beleidsdefinitie(s) die u wilt toevoegen aan dit initiatief. For the **Get secure** initiative, add the following built-in policy definitions by selecting the **+** next to the policy definition information or selecting a policy definition row and then the **+ Add** option in the details page:

   - SQL Server-versie 12.0 vereisen
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Na het selecteren van de beleidsdefinitie uit de lijst, wordt deze toegevoegd onder **Beleidsregels en parameters**.

   ![Review initiative definition parameters](../media/create-and-manage/initiative-definition-2.png)

1. Als een beleidsdefinitie die aan het initiatief wordt toegevoegd, parameters heeft, worden deze weergegeven onder de beleidsnaam in het gebied **Beleid en parameters**. U kunt de _waarde_ instellen op Waarde instellen (in code vastgelegd voor alle toewijzingen van dit initiatief) of Initiatiefparameter gebruiken (wordt ingesteld bij elke toewijzing van een initiatief). Als 'Waarde instellen' is geselecteerd, kunt u met het vervolgkeuzemenu rechts van _Waarden_ de waarde(n) invoeren of selecteren. Als Initiatiefparameter gebruiken is geselecteerd, wordt een nieuwe sectie **Initiatiefparameters** weergegeven waarin u de parameter kunt definiëren die wordt ingesteld tijdens de initiatieftoewijzing. Via de toegestane waarden voor deze initiatiefparameter kunt u verder beperken wat er mag worden ingesteld tijdens de initiatieftoewijzing.

   ![Change initiative definition parameters from allowed values](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > Voor sommige `strongType`-parameters kan de lijst met waarden niet automatisch worden bepaald. In deze gevallen wordt een beletselteken rechts van de parameterrij weergegeven. Selecting it opens the 'Parameter scope (&lt;parameter name&gt;)' page. Op deze pagina selecteert u het abonnement dat u wilt gebruiken om de waardeopties op te geven. Dit parameterbereik wordt alleen gebruikt tijdens het maken van de initiatiefdefinitie en heeft geen invloed op de evaluatie van het beleid of het bereik van het initiatief na het toewijzen.

1. Selecteer **Opslaan**.

### <a name="assign-an-initiative-definition"></a>Een initiatiefdefinitie maken

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Zoek de initiatiefdefinitie **Veilig worden** die u eerder hebt gemaakt en selecteer deze. Selecteer **Toewijzen** bovenaan de pagina om de pagina **Veilig worden: initiatief toewijzen** te openen.

   ![Assign a definition from Initiative definition page](../media/create-and-manage/assign-definition.png)

   You can also right-click on the selected row or select the ellipsis at the end of the row for a contextual menu. Selecteer daarna **Toewijzen**.

   ![Alternative options for an initiative](../media/create-and-manage/select-right-click.png)

1. Vul het formulier **Veilig worden: definitie toewijzen** in door de volgende voorbeeldinformatie in te voeren. U kunt uw eigen gegevens gebruiken.

   - Bereik: de beheergroep of het abonnement waarin u het initiatief hebt opgeslagen wordt de standaardwaarde.
     U kunt het bereik wijzigen om het initiatief toe te wijzen aan een abonnement of resourcegroep in de opslaglocatie.
   - Uitsluitingen: Configureer alle resources binnen het bereik om te voorkomen dat de initiatieftoewijzing erop wordt toegepast.
   - Initiatiefdefinitie en naam van de Toewijzing: Veilig worden (vooraf ingevuld als de naam van het initiatief dat wordt toegewezen).
   - Beschrijving: deze initiatieftoewijzing heeft als doel deze groep beleidsdefinities af te dwingen.
   - Toegewezen door: automatisch gevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Laat **Beheerde identiteit maken** uitgeschakeld. Dit vak _moet_ worden ingeschakeld wanneer het beleid of initiatief dat wordt toegewezen beleid bevat met het effect [deployIfNotExists](../concepts/effects.md#deployifnotexists). Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [Hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works) voor meer informatie.

1. Selecteer **Toewijzen**.

## <a name="check-initial-compliance"></a>Eerste naleving controleren

1. Selecteer **Naleving** links op de Azure Policy-pagina.

1. Locate the **Get Secure** initiative. De _Nalevingsstatus_ hiervan is waarschijnlijk nog **Niet gestart**.
   Select the initiative to get full details on the progress of the assignment.

   ![Initiative compliance page - evaluations not started](../media/create-and-manage/compliance-status-not-started.png)

1. Wanneer de initiatieftoewijzing is voltooid, wordt de pagina voor naleving bijgewerkt met de _Nalevingsstatus_ **Compatibel**.

   ![Initiative compliance page- resources compliant](../media/create-and-manage/compliance-status-compliant.png)

1. Selecting on any policy on the initiative compliance page opens the compliance details page for the policy. Op deze pagina staan de nalevingsdetails op resourceniveau.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Een bron die niet voldoet of wordt geweigerd uitsluiten met behulp van Uitsluiten

Volgens het bovenstaande voorbeeld wordt na het toewijzen van de beleidsdefinitie die SQL server-versie 12.0 vereist, een SQL-server die is gemaakt met een andere versie dan 12.0 geweigerd. In deze sectie doorloopt u het oplossen van een geweigerde poging om een SQL-server te maken door een uitsluiting te maken voor een specifieke groep resources. Uitsluiting voorkomt afdwingen van het beleid (of het initiatief) op de resource.
In het volgende voorbeeld is elke willekeurige versie van een SQL-server toegestaan in één resourcegroep. Een uitsluiting kan worden toegepast op een abonnement of resourcegroep of u kunt de uitsluiting beperken tot afzonderlijke resources.

Een implementatie die wordt verhinderd door een toegewezen beleid of initiatief kan worden weergegeven op twee locaties:

- On the resource group targeted by the deployment: Select **Deployments** in the left side of the page, then select the **Deployment Name** of the failed deployment. De resource die is geweigerd, wordt weergegeven met de status _Verboden_. To determine the policy or initiative and assignment that denied the resource, select **Failed. Click here for details ->** on the Deployment Overview page.
  Aan de rechterkant van de pagina wordt een venster geopend met de informatie over de fout. Onder **Foutdetails** staan de GUID's van de bijbehorende beleidstoewijzingsobjecten.

  ![Implementatie geweigerd door beleidstoewijzing](../media/create-and-manage/rg-deployment-denied.png)

- On the Azure Policy page: Select **Compliance** in the left side of the page and select the **Require SQL Server version 12.0** policy. Op de pagina die wordt geopend, ziet u een toename van de teller **Weigeren**. Op het tabblad **Gebeurtenissen** ziet u ook wie heeft geprobeerd de door het beleid geweigerde implementatie uit te voeren.

  ![Overzicht van de naleving van een toegewezen beleid](../media/create-and-manage/compliance-overview.png)

In dit voorbeeld deed een van de senior virtualisatie-experts van Contoso vereist werk. Wij moeten voor Trent een uitzondering verlenen, maar willen de SQL-servers met een andere versie dan 12.0 niet zomaar in elke resourcegroep toestaan. We hebben een nieuwe resourcegroep gemaakt, **SQLServers_Excluded** en geven deze nu een uitzondering op deze beleidstoewijzing.

### <a name="update-assignment-with-exclusion"></a>Toewijzing met uitsluiting bijwerken

1. Selecteer **Toewijzingen** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Blader door alle beleidstoewijzingen en open de toewijzing *SQL Server-versie 12.0 vereisen*.

1. Set the **Exclusion** by selecting the ellipsis and selecting the resource group to exclude, *SQLServers_Excluded* in this example.

   ![Add an excluded resource group to the policy assignment](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Afhankelijk van het beleid en de invloed ervan, kan de uitsluiting ook worden toegekend aan specifieke resources binnen een resourcegroep binnen het bereik van de toewijzing. Als een **Weigeren**-effect is gebruikt in deze zelfstudie, zou het niet verstandig om de uitsluiting in te stellen op een specifieke resource die al bestaat.

1. Select **Select** and then select **Save**.

In deze sectie hebt u de geweigerde aanvraag toch uitgevoerd door een uitsluiting te maken in één resourcegroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle bovenstaande toewijzingen of definities te verwijderen:

1. Selecteer **Definities** (of **Toewijzingen** als u een toewijzing wilt verwijderen) onder **Ontwerpen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek naar de nieuwe initiatief- of beleidsdefinitie (of toewijzing) die u zojuist hebt gemaakt.

1. Klik met de rechtermuisknop op de rij of selecteer de weglatingstekens aan het einde van de definitie (of de toewijzing) en selecteer **Definitie verwijderen** (of **Toewijzing verwijderen**).

## <a name="review"></a>Beoordelen

In deze zelfstudie hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> - Een beleid toegewezen voor het afdwingen van een voorwaarde voor bronnen die u in de toekomst maakt
> - Een initiatiefdefinitie gemaakt en toegewezen om naleving bij te houden voor meerdere bronnen
> - Problemen met een bron die niet voldoet of is geweigerd, opgelost
> - Een nieuw beleid binnen een organisatie geïmplementeerd

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel voor meer informatie over de structuur van beleidsdefinities:

> [!div class="nextstepaction"]
> [Structuur van Azure Policy-definities](../concepts/definition-structure.md)