---
title: 'Zelf studie: beleid bouwen om naleving af te dwingen'
description: In deze zelf studie gebruikt u beleids regels voor het afdwingen van standaarden, het beheren van kosten, het onderhouden van de beveiliging en het opstellen van ontwerp principes op ondernemings niveau.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: dcebbbfcc2f86ace7ea4400a2fdb6f1392f4efe6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82190823"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Zelf studie: beleid maken en beheren om naleving af te dwingen

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

De eerste stap bij het afdwingen van naleving met Azure Policy bestaat uit het toewijzen van een beleidsdefinitie. Een beleidsdefinitie definieert onder welke voorwaarde een beleid wordt afgedwongen en welke actie moet worden uitgevoerd. In dit voor beeld wijst u de ingebouwde beleids definitie _een tag overnemen van de resource groep toe als_ u de opgegeven tag met de waarde van de bovenliggende resource groep wilt toevoegen aan de nieuwe of bijgewerkte resources die de tag ontbreken.

1. Ga naar de Azure Portal om beleid toe te wijzen. Zoek en selecteer **beleid**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Beleid zoeken in de zoek balk" border="false":::

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Toewijzingen selecteren op de pagina overzicht van beleid" border="false":::

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Een beleids definitie toewijzen van de pagina toewijzingen" border="false":::

1. Selecteer op de pagina **beleid toewijzen** en de **basis principes** het **bereik** door het weglatings teken te selecteren en ofwel een beheer groep of een abonnement te selecteren. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen.
   Selecteer vervolgens onder aan de **bereik** pagina de **optie selecteren** .

   In dit voorbeeld wordt het abonnement **Contoso** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. U kunt het **Type** van de beleidsdefinitie filteren op _Ingebouwd_ om alles te bekijken en de beschrijvingen te lezen.

1. Selecteer **een tag overnemen van de resource groep als deze ontbreekt**. Als u het niet meteen kunt vinden, typt u **een tag overnemen** in het zoekvak en drukt u op ENTER of selecteert u uit het zoekvak.
   Selecteer **selecteren** onder aan de pagina **beschik bare definities** wanneer u de beleids definitie hebt gevonden en geselecteerd.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Zoek filter gebruiken om een beleid te zoeken":::

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. Voor dit voor beeld moet u _een tag overnemen van de resource groep als deze ontbreekt_. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.

1. Sluit het **afdwingen van beleid** af als _ingeschakeld_. Als deze instelling is _uitgeschakeld_, is het mogelijk om het resultaat van het beleid te testen zonder het effect te activeren. Zie de [afdwingings modus](../concepts/assignment-structure.md#enforcement-mode)voor meer informatie.

1. **Toegewezen door** wordt automatisch ingevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Selecteer het tabblad **para meters** boven aan de wizard.

1. Voer voor **label naam** _omgeving_in.

1. Selecteer het tabblad **herstel** bovenaan de wizard.

1. **Een herstel taak maken** uitschakelen is uitgeschakeld. In dit vak kunt u een taak maken voor het wijzigen van bestaande resources naast nieuwe of bijgewerkte resources. Zie [bronnen herstellen](../how-to/remediate-resources.md)voor meer informatie.

1. Het **maken van een beheerde identiteit** wordt automatisch gecontroleerd omdat deze beleids definitie het [wijzigings](../concepts/effects.md#modify) effect gebruikt. **Machtigingen** worden automatisch ingesteld op _Inzender_ op basis van de beleids definitie. Zie voor meer informatie [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecteer boven aan de wizard het tabblad **controleren + maken** .

1. Controleer uw selecties en selecteer vervolgens **maken** onder aan de pagina.

## <a name="implement-a-new-custom-policy"></a>Een nieuw aangepast beleid implementeren

Nu u een ingebouwde beleidsdefinitie hebt toegewezen, kunt u meer kunt doen met Azure Policy. Maak nu een nieuw aangepast beleid om kosten te besparen door te controleren of virtuele machines die in uw omgeving worden gemaakt niet van de G-serie zijn. Hiermee wordt telkens wanneer een gebruiker in uw organisatie de virtuele machine in de G-serie wil maken, de aanvraag geweigerd.

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="De definitie pagina onder de groep ontwerpen" border="false":::

1. Selecteer **+ Beleidsdefinitie** bovenaan de pagina. Met deze knop opent u de pagina **Beleidsdefinitie**.

1. Voer de volgende informatie in:

   - De beheergroep of het abonnement waarin de beleidsdefinitie is opgeslagen. Selecteer met behulp van het weglatingsteken **Definitielocatie**.

     > [!NOTE]
     > Als u van plan bent deze toe te passen op meerdere abonnementen, moet de locatie een beheergroep zijn met de abonnementen waaraan u het beleid toewijst. Hetzelfde geldt voor de definitie van een initiatief.

   - De naam van de beleids definitie-_ *_vereist dat VM-sku's kleiner zijn dan de G-serie_
   - De beschrijving van het doel van de beleidsdefinitie: _Deze beleidsdefinitie dwingt af dat alle virtuele machines die zijn gemaakt in dit bereik, SKU's hebben die kleiner zijn dan de G-serie om kosten te beperken._
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

   De _veld_ eigenschap in de beleids regel moet een ondersteunde waarde zijn. Er is een volledige lijst met waarden gevonden in de [structuur velden van de beleids definitie](../concepts/definition-structure.md#fields). Een voorbeeld van een alias is mogelijk `"Microsoft.Compute/VirtualMachines/Size"`.

   Zie [Azure Policy voor beelden](../samples/index.md)om meer Azure Policy voor beelden te bekijken.

1. Selecteer **Opslaan**.

## <a name="create-a-policy-definition-with-rest-api"></a>Een beleidsdefinitie maken met de REST-API

U kunt een beleid maken met de REST API voor Azure Policy definities. Met de REST API kunt u beleidsdefinities maken en verwijderen en informatie over bestaande definities ophalen. Gebruik het volgende voorbeeld om een beleidsdefinitie te maken:

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

Voordat u doorgaat met het Power shell-voor beeld, moet u ervoor zorgen dat u de nieuwste versie van de Azure PowerShell AZ-module hebt geïnstalleerd.

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

Met de `az policy definition` opdracht kunt u een beleids definitie maken met behulp van Azure cli. Gebruik het volgende voorbeeld om een beleidsdefinitie met een inline regel te maken:

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

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definitie selecteren op de pagina definities" border="false":::

1. Selecteer **+ Initiatiefdefinitie** bovenaan de pagina. U gaat dan naar de pagina **Initiatiefdefinitie**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Pagina definitie van het Review-initiatief" border="false":::

1. Gebruik **Definitielocatie** om een beheergroep of abonnement voor het opslaan van de definitie te selecteren. Als de vorige pagina is gericht op een enkele beheergroep of enkel abonnement, wordt **Definitielocatie** automatisch ingevuld. Zodra dit is geselecteerd, worden de **beschik bare definities** ingevuld.

1. Voer de **naam** en **beschrijving** van het initiatief in.

   Dit voorbeeld zorgt ervoor worden dat bronnen voldoen aan beleidsdefinities over beveiliging. Geef het initiatief de naam **Beveiligen** en stel de volgende beschrijving in: **Dit initiatief is gemaakt voor het afhandelen van alle beleidsregels die zijn gekoppeld aan het beveiligen van resources**.

1. Kies voor **Categorie** uit bestaande opties of maak een nieuwe categorie.

1. Blader door de lijst met **Beschikbare definities** (rechterhelft van de pagina **Initiatiefdefinitie**) en selecteer het beleidsdefinitie(s) die u wilt toevoegen aan dit initiatief. Voor het initiatief **Secure** Initiative voegt u de volgende ingebouwde beleids definities toe door het **+** selectie vakje naast de beleids definitie-informatie te selecteren of door een regel voor een beleids definitie te selecteren en vervolgens op de optie **+ toevoegen** te klikken op de pagina Details:

   - Toegestane locaties
   - Ontbrekende Endpoint Protection in Azure Security Center controleren
   - De regels voor de netwerk beveiligings groep voor virtuele machines die zijn gericht op internet, moeten worden gehard
   - Azure Backup moet zijn ingeschakeld voor Virtual Machines
   - Schijf versleuteling moet worden toegepast op virtuele machines

   Nadat u de beleids definitie hebt geselecteerd in de lijst, wordt deze toegevoegd onder **categorie**.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Para meters voor beoordelings initiatief" border="false":::

1. Als een beleids definitie die wordt toegevoegd aan het initiatief para meters heeft, worden deze weer gegeven onder de naam van het beleid in het gebied onder **categorie** gebied. U kunt de _waarde_ instellen op Waarde instellen (in code vastgelegd voor alle toewijzingen van dit initiatief) of Initiatiefparameter gebruiken (wordt ingesteld bij elke toewijzing van een initiatief). Als ' set-waarde ' is geselecteerd, kan de vervolg keuzelijst rechts van de _waarde (n)_ de waarde (n) invoeren of selecteren. Als Initiatiefparameter gebruiken is geselecteerd, wordt een nieuwe sectie **Initiatiefparameters** weergegeven waarin u de parameter kunt definiëren die wordt ingesteld tijdens de initiatieftoewijzing. Via de toegestane waarden voor deze initiatiefparameter kunt u verder beperken wat er mag worden ingesteld tijdens de initiatieftoewijzing.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Para meters voor initiatief definities wijzigen van toegestane waarden" border="false":::

   > [!NOTE]
   > Voor sommige `strongType`-parameters kan de lijst met waarden niet automatisch worden bepaald. In deze gevallen wordt een beletselteken rechts van de parameterrij weergegeven. Als u dit selecteert, wordt de pagina&lt;parameter bereik&gt;(parameter naam) geopend. Op deze pagina selecteert u het abonnement dat u wilt gebruiken om de waardeopties op te geven. Dit parameterbereik wordt alleen gebruikt tijdens het maken van de initiatiefdefinitie en heeft geen invloed op de evaluatie van het beleid of het bereik van het initiatief na het toewijzen.

   Stel de para meter ' Allowed locations ' in op ' Oost-VS 2 ' en wijzig de standaard waarde ' AuditifNotExists '.

1. Selecteer **Opslaan**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Een beleids initiatief definitie maken met Azure CLI

Met de `az policy set-definition` opdracht kunt u een beleids initiatief definitie maken met behulp van Azure cli. Gebruik het volgende voor beeld om een beleids initiatief definitie te maken met een bestaande beleids definitie:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Een beleids initiatief definitie maken met Azure PowerShell

U kunt een beleids initiatief definitie maken met behulp van `New-AzPolicySetDefinition` Azure PowerShell met de cmdlet. Als u een beleids initiatief definitie met een bestaande beleids definitie wilt maken, gebruikt u het volgende definitie bestand `VMPolicySet.json`voor het beleid als:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Een initiatiefdefinitie maken

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Zoek de initiatiefdefinitie **Veilig worden** die u eerder hebt gemaakt en selecteer deze. Selecteer **Toewijzen** bovenaan de pagina om de pagina **Veilig worden: initiatief toewijzen** te openen.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="De pagina definitie van de definitie van het initiatief toewijzen" border="false":::

   U kunt ook met de rechter muisknop op de geselecteerde rij klikken of het weglatings teken aan het einde van de rij voor een context menu selecteren. Selecteer daarna **Toewijzen**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Alternatieve opties voor een initiatief" border="false":::

1. Vul het formulier **Veilig worden: definitie toewijzen** in door de volgende voorbeeldinformatie in te voeren. U kunt uw eigen gegevens gebruiken.

   - Bereik: de beheergroep of het abonnement waarin u het initiatief hebt opgeslagen wordt de standaardwaarde.
     U kunt het bereik wijzigen om het initiatief toe te wijzen aan een abonnement of resourcegroep in de opslaglocatie.
   - Uitsluitingen: Configureer alle resources binnen het bereik om te voorkomen dat de initiatieftoewijzing erop wordt toegepast.
   - Initiatiefdefinitie en naam van de Toewijzing: Veilig worden (vooraf ingevuld als de naam van het initiatief dat wordt toegewezen).
   - Beschrijving: deze initiatieftoewijzing heeft als doel deze groep beleidsdefinities af te dwingen.
   - Beleids afdwinging: als de standaard instelling is _ingeschakeld_.
   - Toegewezen door: automatisch gevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Selecteer het tabblad **para meters** boven aan de wizard. Als u in de vorige stappen een initiatief parameter hebt geconfigureerd, stelt u hier een waarde in.

1. Selecteer het tabblad **herstel** bovenaan de wizard. Laat **Beheerde identiteit maken** uitgeschakeld. Dit selectie vakje _moet_ worden ingeschakeld wanneer het beleid of initiatief dat wordt toegewezen, een beleid bevat met het [deployIfNotExists](../concepts/effects.md#deployifnotexists) of [aanpassen](../concepts/effects.md#modify) van effecten. Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie voor meer informatie [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecteer boven aan de wizard het tabblad **controleren + maken** .

1. Controleer uw selecties en selecteer vervolgens **maken** onder aan de pagina.

## <a name="check-initial-compliance"></a>Eerste naleving controleren

1. Selecteer **Naleving** links op de Azure Policy-pagina.

1. Zoek het initiatief **Get Secure** . De _Nalevingsstatus_ hiervan is waarschijnlijk nog **Niet gestart**.
   Selecteer het initiatief om alle details te bekijken over de voortgang van de toewijzing.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Nalevings pagina initiatief-evaluaties niet gestart" border="false":::

1. Wanneer de initiatieftoewijzing is voltooid, wordt de pagina voor naleving bijgewerkt met de _Nalevingsstatus_**Compatibel**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Nalevings pagina voor initiatief-bronnen compatibel" border="false":::

1. Als u een wille keurig beleid selecteert op de pagina initiatief naleving, wordt de pagina nalevings Details voor dat beleid geopend. Op deze pagina staan de nalevingsdetails op resourceniveau.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Een bron die niet voldoet of wordt geweigerd uitsluiten met behulp van Uitsluiten

Na het toewijzen van een beleids initiatief om een specifieke locatie te vereisen, wordt een resource die op een andere locatie is gemaakt, geweigerd. In deze sectie gaat u een geweigerde aanvraag voor het maken van een resource oplossen door een uitsluiting voor één resource groep te maken. De uitsluiting voor komt het afdwingen van het beleid (of initiatief) voor die resource groep. In het volgende voor beeld is elke locatie toegestaan in de uitgesloten resource groep. Een uitsluiting kan worden toegepast op een abonnement, een resource groep of een afzonderlijke resources.

Implementaties die worden voor komen door een toegewezen beleid of initiatief, kunnen worden weer gegeven op de resource groep waarop de implementatie is gericht: Selecteer **implementaties** aan de linkerkant van de pagina en selecteer vervolgens de **implementatie naam** van de mislukte implementatie. De resource die is geweigerd, wordt weergegeven met de status _Verboden_. Selecteer mislukt om het beleid of initiatief en de toewijzing te bepalen die de resource **heeft geweigerd. Klik hier voor meer informatie->** op de pagina implementatie overzicht. Aan de rechterkant van de pagina wordt een venster geopend met de informatie over de fout. Onder **Foutdetails** staan de GUID's van de bijbehorende beleidstoewijzingsobjecten.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Implementatie geweigerd door beleidstoewijzing" border="false":::

Selecteer op de pagina Azure Policy: **naleving** selecteren aan de linkerkant van de pagina en selecteer het initiatief **veilig beleid ophalen** . Op deze pagina wordt het aantal **weigeringen** voor geblokkeerde resources verhoogd. Op het tabblad **gebeurtenissen** vindt u informatie over wie heeft geprobeerd de resource te maken of te implementeren die is geweigerd door de beleids definitie.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Overzicht van de naleving van een toegewezen beleid" border="false":::

In dit voorbeeld deed een van de senior virtualisatie-experts van Contoso vereist werk. We moeten Trent een ruimte toekennen voor een uitzonde ring. Er is een nieuwe resource groep gemaakt, **LocationsExcluded**en vervolgens wordt er een uitzonde ring op deze beleids toewijzing verleend.

### <a name="update-assignment-with-exclusion"></a>Toewijzing met uitsluiting bijwerken

1. Selecteer **Toewijzingen** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Blader door alle beleids toewijzingen en open de toewijzing _beveiligd beleid ophalen_ .

1. Stel de **uitsluiting** in door het beletsel teken te selecteren en de resource groep te selecteren die u wilt uitsluiten, _LocationsExcluded_ in dit voor beeld. Selecteer **toevoegen aan geselecteerde scope** en selecteer vervolgens **Opslaan**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Een uitgesloten resource groep toevoegen aan de beleids toewijzing" border="false":::

   > [!NOTE]
   > Afhankelijk van de beleids definitie en het effect daarvan kan de uitsluiting ook worden verleend aan specifieke resources binnen het bereik van de toewijzing. Als een **deny** -effect in deze zelf studie wordt gebruikt, is het niet logisch om de uitsluiting in te stellen voor een specifieke resource die al bestaat.

1. Selecteer **controleren en opslaan** en selecteer vervolgens **Opslaan**.

In deze sectie hebt u de geweigerde aanvraag toch uitgevoerd door een uitsluiting te maken in één resourcegroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met het werken met resources uit deze zelf studie, gebruikt u de volgende stappen om de beleids toewijzingen of-definities te verwijderen die hierboven zijn gemaakt:

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
> [Structuur van Azure-beleidsdefinities](../concepts/definition-structure.md)