---
title: 'Zelfstudie: Beleid maken om naleving af te dwingen'
description: In deze zelfstudie gebruikt u beleidsregels om standaarden af te dwingen, kosten te beheersen, de beveiliging te handhaven en bedrijfsbrede ontwerpprincipes op te leggen.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: 15a6c3df26938332d42ecbcfff43f958577062c4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239935"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Zelfstudie: Beleid maken en beheren om naleving af te dwingen

Als u aan uw bedrijfsnormen en serviceovereenkomsten wilt blijven voldoen, is het belangrijk dat u begrijpt hoe u beleidsregels kunt maken en beheren in Azure. In deze zelfstudie leert u hoe u Azure Policy gebruikt om enkele algemene taken voor het maken, toewijzen en beheren van beleid in uw organisatie uit te voeren, zoals:

> [!div class="checklist"]
> - Toewijzen van een beleid om een voorwaarde voor bronnen af te dwingen die u in de toekomst maakt
> - Maken en toewijzen van de definitie van een initiatief om naleving bij te houden voor meerdere bronnen
> - Problemen met een bron die niet voldoet of is geweigerd oplossen
> - Een nieuw beleid binnen een organisatie implementeren

Als u een beleid wilt toewijzen voor het identificeren van de huidige nalevingsstatus van uw bestaande bronnen, raadpleegt u de quickstart-artikelen over dit ontwerp.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="assign-a-policy"></a>Een beleid toewijzen

De eerste stap bij het afdwingen van naleving met Azure Policy bestaat uit het toewijzen van een beleidsdefinitie. Een beleidsdefinitie definieert onder welke voorwaarde een beleid wordt afgedwongen en welke actie moet worden uitgevoerd. Wijs in dit voorbeeld de ingebouwde beleidsdefinitie met de naam _Een tag overnemen van de brongroep toe als deze ontbreekt_ om de opgegeven tag met de waarde van de bovenliggende resourcegroep toe te voegen aan nieuwe of bijgewerkte bronnen die de tag missen.

1. Ga naar de Azure-portal om beleid toe te wijzen. Zoeken naar en selecteer **Beleid**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Zoeken naar beleid in de zoekbalk" border="false":::

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Toewijzingen selecteren op de pagina Beleidsoverzicht" border="false":::

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Een beleidsdefinitie toewijzen op de pagina Toewijzingen" border="false":::

1. Selecteer op de pagina **Beleid toewijzen** en **Basisbeginselen** het **bereik** door de ellips te selecteren en een beheergroep of abonnement te selecteren. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen.
   Selecteer vervolgens **Selecteren** onder aan de **pagina Bereik.**

   In dit voorbeeld wordt het abonnement **Contoso** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. U kunt het **Type** van de beleidsdefinitie filteren op _Ingebouwd_ om alles te bekijken en de beschrijvingen te lezen.

1. Selecteer **Een tag overnemen uit de brongroep als deze ontbreekt**. Als u deze niet meteen vinden, typt u **een tag in** het zoekvak en drukt u op ENTER of selecteert u uit het zoekvak.
   Selecteer **Selecteer** onder aan de pagina **Beschikbare definities** selecteren zodra u de beleidsdefinitie hebt gevonden en geselecteerd.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Zoekfilter gebruiken om een beleid te zoeken":::

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. Laat in dit voorbeeld _Een tag overnemen uit de brongroep als deze ontbreekt_. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.

1. **Beleidshandhaving als** _ingeschakeld laten_. Wanneer _uitgeschakeld,_ deze instelling maakt het mogelijk het testen van de uitkomst van het beleid zonder triggering het effect. Zie [de handhavingsmodus](../concepts/assignment-structure.md#enforcement-mode)voor meer informatie .

1. **Toegewezen door** wordt automatisch ingevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Selecteer het tabblad **Parameters** boven aan de wizard.

1. Voer **voor Tagnaam** _de omgeving_in .

1. Selecteer het tabblad **Herstel** boven aan de wizard.

1. Laat **Een hersteltaak** maken onaangevinkt. Met dit vak u een taak maken om bestaande bronnen te wijzigen naast nieuwe of bijgewerkte bronnen. Zie resources [voor](../how-to/remediate-resources.md)meer informatie.

1. **Een beheerde identiteit maken** wordt automatisch gecontroleerd omdat deze beleidsdefinitie het [wijzigingseffect](../concepts/effects.md#modify) gebruikt. **Machtigingen** worden automatisch ingesteld op _Inzender_ op basis van de beleidsdefinitie. Zie voor meer informatie [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecteer het tabblad **Controleren + maken** boven aan de wizard.

1. Bekijk uw selecties en selecteer **Maken** onder aan de pagina.

## <a name="implement-a-new-custom-policy"></a>Een nieuw aangepast beleid implementeren

Nu u een ingebouwde beleidsdefinitie hebt toegewezen, kunt u meer kunt doen met Azure Policy. Maak nu een nieuw aangepast beleid om kosten te besparen door te controleren of virtuele machines die in uw omgeving worden gemaakt niet van de G-serie zijn. Hiermee wordt telkens wanneer een gebruiker in uw organisatie de virtuele machine in de G-serie wil maken, de aanvraag geweigerd.

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definitiepagina onder Groep Ontwerpen" border="false":::

1. Selecteer **+ Beleidsdefinitie** bovenaan de pagina. Met deze knop opent u de pagina **Beleidsdefinitie**.

1. Voer de volgende informatie in:

   - De beheergroep of het abonnement waarin de beleidsdefinitie is opgeslagen. Selecteer met behulp van het weglatingsteken **Definitielocatie**.

     > [!NOTE]
     > Als u van plan bent deze toe te passen op meerdere abonnementen, moet de locatie een beheergroep zijn met de abonnementen waaraan u het beleid toewijst. Hetzelfde geldt voor de definitie van een initiatief.

   - De naam van de beleidsdefinitie - _*_VM SKU's vereisen die kleiner zijn dan de G-serie_
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

   De _veldeigenschap_ in de beleidsregel moet een ondersteunde waarde hebben. Een volledige lijst met waarden is te vinden op [de velden beleidsdefinitiestructuur](../concepts/definition-structure.md#fields). Een voorbeeld van een alias is mogelijk `"Microsoft.Compute/VirtualMachines/Size"`.

   Meer Azure Policy-voorbeelden vindt u in [Voorbeelden van Azure Policy](../samples/index.md).

1. Selecteer **Opslaan**.

## <a name="create-a-policy-definition-with-rest-api"></a>Een beleidsdefinitie maken met de REST-API

U een beleid maken met de REST API voor Azure Policy Definitions. Met de REST API kunt u beleidsdefinities maken en verwijderen en informatie over bestaande definities ophalen. Gebruik het volgende voorbeeld om een beleidsdefinitie te maken:

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

Voordat u verdergaat met het PowerShell-voorbeeld, moet u ervoor zorgen dat u de nieuwste versie van de Azure PowerShell Az-module hebt geïnstalleerd.

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

Met de `az policy definition` opdracht u een beleidsdefinitie maken met Azure CLI. Gebruik het volgende voorbeeld om een beleidsdefinitie met een inline regel te maken:

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

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definitie selecteren op de pagina Definities" border="false":::

1. Selecteer **+ Initiatiefdefinitie** bovenaan de pagina. U gaat dan naar de pagina **Initiatiefdefinitie**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Pagina voor de definitie van initiatieven bekijken" border="false":::

1. Gebruik **Definitielocatie** om een beheergroep of abonnement voor het opslaan van de definitie te selecteren. Als de vorige pagina is gericht op een enkele beheergroep of enkel abonnement, wordt **Definitielocatie** automatisch ingevuld. Eenmaal geselecteerd, worden **beschikbare definities** ingevuld.

1. Voer de **naam** en **beschrijving** van het initiatief in.

   Dit voorbeeld zorgt ervoor worden dat bronnen voldoen aan beleidsdefinities over beveiliging. Geef het initiatief de naam **Beveiligen** en stel de volgende beschrijving in: **Dit initiatief is gemaakt voor het afhandelen van alle beleidsregels die zijn gekoppeld aan het beveiligen van resources**.

1. Kies voor **Categorie** uit bestaande opties of maak een nieuwe categorie.

1. Blader door de lijst met **Beschikbare definities** (rechterhelft van de pagina **Initiatiefdefinitie**) en selecteer het beleidsdefinitie(s) die u wilt toevoegen aan dit initiatief. Voeg voor het initiatief **Veilig worden** de volgende ingebouwde beleidsdefinities toe door het **+** volgende naast de beleidsdefinitiegegevens te selecteren of een rij beleidsdefinitie te selecteren en vervolgens de optie + **Toevoegen** op de pagina details te selecteren:

   - Toegestane locaties
   - Ontbrekende endpointbeveiliging controleren in Azure Security Center
   - Netwerkbeveiligingsgroepregels voor internet waarmee virtuele machines worden geconfronteerd, moeten worden verhard
   - Azure Backup moet zijn ingeschakeld voor virtuele machines
   - Schijfversleuteling moet worden toegepast op virtuele machines

   Nadat u de beleidsdefinitie in de lijst hebt geselecteerd, wordt deze onder **Categorie**toegevoegd .

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Parameters voor de definitie van initiatieven herzien" border="false":::

1. Als een beleidsdefinitie die aan het initiatief wordt toegevoegd, parameters heeft, worden deze weergegeven onder de beleidsnaam in het gebied onder **categoriegebied.** U kunt de _waarde_ instellen op Waarde instellen (in code vastgelegd voor alle toewijzingen van dit initiatief) of Initiatiefparameter gebruiken (wordt ingesteld bij elke toewijzing van een initiatief). Als 'Waarde instellen' is geselecteerd, staat de vervolgkeuzelijst rechts van _Waarde(en)_ het invoeren of selecteren van de waarde(en) toe. Als Initiatiefparameter gebruiken is geselecteerd, wordt een nieuwe sectie **Initiatiefparameters** weergegeven waarin u de parameter kunt definiëren die wordt ingesteld tijdens de initiatieftoewijzing. Via de toegestane waarden voor deze initiatiefparameter kunt u verder beperken wat er mag worden ingesteld tijdens de initiatieftoewijzing.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Parameters voor initiatiefdefinitie wijzigen van toegestane waarden" border="false":::

   > [!NOTE]
   > Voor sommige `strongType`-parameters kan de lijst met waarden niet automatisch worden bepaald. In deze gevallen wordt een beletselteken rechts van de parameterrij weergegeven. Als u deze selecteert,&lt;wordt&gt;de pagina 'Parameterbereik ( parameternaam)' geopend. Op deze pagina selecteert u het abonnement dat u wilt gebruiken om de waardeopties op te geven. Dit parameterbereik wordt alleen gebruikt tijdens het maken van de initiatiefdefinitie en heeft geen invloed op de evaluatie van het beleid of het bereik van het initiatief na het toewijzen.

   Stel de parameter 'Toegestane locaties' in op 'Oost US 2' en laat de anderen achter als de standaard 'AuditifNotExists'.

1. Selecteer **Opslaan**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Een beleidsinitiatiefdefinitie maken met Azure CLI

Met de `az policy set-definition` opdracht u een beleidsinitiatiefdefinitie maken met Azure CLI. Als u een beleidsinitiatiefdefinitie wilt maken met een bestaande beleidsdefinitie, gebruikt u het volgende voorbeeld:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Een beleidsinitiatiefdefinitie maken met Azure PowerShell

U een beleidsinitiatiefdefinitie maken met `New-AzPolicySetDefinition` Azure PowerShell met de cmdlet. Als u een beleidsinitiatiefdefinitie wilt maken met een bestaande `VMPolicySet.json`beleidsdefinitie, gebruikt u het volgende beleidsinitiatiefdefinitiebestand als:

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

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Een definitie toewijzen op de pagina Van Initiative-definitie" border="false":::

   U ook met de rechtermuisknop op de geselecteerde rij klikken of de ellips aan het einde van de rij selecteren voor een contextueel menu. Selecteer daarna **Toewijzen**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Alternatieve opties voor een initiatief" border="false":::

1. Vul het formulier **Veilig worden: definitie toewijzen** in door de volgende voorbeeldinformatie in te voeren. U kunt uw eigen gegevens gebruiken.

   - Bereik: de beheergroep of het abonnement waarin u het initiatief hebt opgeslagen wordt de standaardwaarde.
     U kunt het bereik wijzigen om het initiatief toe te wijzen aan een abonnement of resourcegroep in de opslaglocatie.
   - Uitsluitingen: Configureer alle resources binnen het bereik om te voorkomen dat de initiatieftoewijzing erop wordt toegepast.
   - Initiatiefdefinitie en naam van de Toewijzing: Veilig worden (vooraf ingevuld als de naam van het initiatief dat wordt toegewezen).
   - Beschrijving: deze initiatieftoewijzing heeft als doel deze groep beleidsdefinities af te dwingen.
   - Beleidshandhaving: Vertrek als standaard _ingeschakeld_.
   - Toegewezen door: automatisch gevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Selecteer het tabblad **Parameters** boven aan de wizard. Als u in eerdere stappen een parameter voor een initiatief hebt geconfigureerd, stelt u hier een waarde in.

1. Selecteer het tabblad **Herstel** boven aan de wizard. Laat **Beheerde identiteit maken** uitgeschakeld. Dit vakje _moet_ worden aangevinkt wanneer het toegewezen beleid of initiatief een beleid bevat met de [effecten deployIfNotExists](../concepts/effects.md#deployifnotexists) of [wijzigen.](../concepts/effects.md#modify) Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie voor meer informatie [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecteer het tabblad **Controleren + maken** boven aan de wizard.

1. Bekijk uw selecties en selecteer **Maken** onder aan de pagina.

## <a name="check-initial-compliance"></a>Eerste naleving controleren

1. Selecteer **Naleving** links op de Azure Policy-pagina.

1. Zoek het **Get Secure-initiatief.** De _Nalevingsstatus_ hiervan is waarschijnlijk nog **Niet gestart**.
   Selecteer het initiatief om alle details te krijgen over de voortgang van de opdracht.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Nalevingspagina voor initiatieven - evaluaties niet gestart" border="false":::

1. Wanneer de initiatieftoewijzing is voltooid, wordt de pagina voor naleving bijgewerkt met de _Nalevingsstatus_**Compatibel**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Pagina naleving van initiatieven: resources voldoen" border="false":::

1. Als u een beleid selecteert op de pagina naleving van het initiatief, wordt de pagina nalevingsdetails voor dat beleid geopend. Op deze pagina staan de nalevingsdetails op resourceniveau.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Een bron die niet voldoet of wordt geweigerd uitsluiten met behulp van Uitsluiten

Nadat u een beleidsinitiatief hebt toebedeeld om een specifieke locatie te vereisen, wordt elke resource die op een andere locatie is gemaakt, geweigerd. In deze sectie loopt u door het oplossen van een geweigerd verzoek om een resource te maken door een uitsluiting te maken voor één resourcegroep. De uitsluiting verhindert de handhaving van het beleid (of initiatief) op die resourcegroep. In het volgende voorbeeld is elke locatie toegestaan in de uitgesloten resourcegroep. Een uitsluiting kan van toepassing zijn op een abonnement, een resourcegroep of een afzonderlijke bron.

Implementaties die worden voorkomen door een toegewezen beleid of initiatief, kunnen worden weergegeven in de resourcegroep die is getarget door de implementatie: Selecteer **Implementaties** aan de linkerkant van de pagina en selecteer vervolgens de **implementatienaam** van de mislukte implementatie. De resource die is geweigerd, wordt weergegeven met de status _Verboden_. Als u het beleid of initiatief en de toewijzing wilt bepalen die de resource heeft geweigerd, selecteert u **Mislukt. Klik hier voor meer informatie ->** op de pagina Implementatieoverzicht. Aan de rechterkant van de pagina wordt een venster geopend met de informatie over de fout. Onder **Foutdetails** staan de GUID's van de bijbehorende beleidstoewijzingsobjecten.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Implementatie geweigerd door beleidstoewijzing" border="false":::

Op de pagina Azure Policy: Selecteer **Compliance** aan de linkerkant van de pagina en selecteer het beleidsinitiatief **Veilig beveiligen.** Op deze pagina is er een toename van het aantal **weigeringen** voor geblokkeerde resources. Onder het tabblad **Gebeurtenissen** vindt u informatie over wie de resource heeft geprobeerd te maken of te implementeren die is geweigerd door de beleidsdefinitie.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Overzicht van de naleving van een toegewezen beleid" border="false":::

In dit voorbeeld deed een van de senior virtualisatie-experts van Contoso vereist werk. We moeten Trent een plek geven voor een uitzondering. Hiermee heeft u een nieuwe resourcegroep **gemaakt, Locaties Uitgesloten**en deze vervolgens een uitzondering op deze beleidstoewijzing verlenen.

### <a name="update-assignment-with-exclusion"></a>Toewijzing met uitsluiting bijwerken

1. Selecteer **Toewijzingen** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Blader door alle beleidstoewijzingen en open de beleidstoewijzing _Veilig zoeken._

1. Stel de **uitsluiting** in door de ellips te selecteren en de resourcegroep te selecteren die u wilt uitsluiten, _Locaties Uitgesloten_ in dit voorbeeld. Selecteer **Toevoegen aan geselecteerd bereik** en selecteer **Opslaan**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Een uitgesloten resourcegroep toevoegen aan de beleidstoewijzing" border="false":::

   > [!NOTE]
   > Afhankelijk van de beleidsdefinitie en het effect ervan kan de uitsluiting ook worden toegestaan aan specifieke resources binnen een resourcegroep binnen het bereik van de toewijzing. Aangezien een **Weigeren-effect** werd gebruikt in deze zelfstudie, zou het geen zin hebben om de uitsluiting in te stellen op een specifieke resource die al bestaat.

1. Selecteer **Controleren + opslaan** en selecteer **Opslaan**.

In deze sectie hebt u de geweigerde aanvraag toch uitgevoerd door een uitsluiting te maken in één resourcegroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met het werken met bronnen uit deze zelfstudie, gebruikt u de volgende stappen om een van de hierboven gemaakte beleidstoewijzingen of -definities te verwijderen:

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