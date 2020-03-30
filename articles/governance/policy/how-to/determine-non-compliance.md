---
title: Oorzaken van niet-naleving bepalen
description: Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Meer informatie over de oorzaak van de niet-naleving.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264633"
---
# <a name="determine-causes-of-non-compliance"></a>Oorzaken van niet-naleving bepalen

Wanneer wordt vastgesteld dat een Azure-bron niet aan een beleidsregel voldoet, is het handig om te begrijpen met welk gedeelte van de regel de resource niet voldoet. Het is ook handig om te begrijpen welke wijziging een eerder conforme bron heeft gewijzigd om deze niet-compatibel te maken. Er zijn twee manieren om deze informatie te vinden:

> [!div class="checklist"]
> - [Nalevingsgegevens](#compliance-details)
> - [Geschiedenis wijzigen (Voorbeeld)](#change-history)

## <a name="compliance-details"></a>Nalevingsgegevens

Wanneer een resource niet voldoet, zijn de nalevingsgegevens voor die resource beschikbaar op de pagina **Naleving van het beleid.** Het deelvenster Nalevingsgegevens bevat de volgende informatie:

- Brongegevens zoals naam, type, locatie en resource-id
- Nalevingsstatus en tijdstempel van de laatste evaluatie voor de huidige beleidstoewijzing
- Een lijst met _redenen_ voor de niet-naleving van de resource

> [!IMPORTANT]
> Aangezien de nalevingsgegevens voor een _niet-compatibele_ resource de huidige waarde van eigenschappen op die resource weergeven, moet de gebruiker de **leesbewerking** hebben voor het **type** resource. Als de _niet-compatibele_ bron bijvoorbeeld **Microsoft.Compute/virtualMachines** is, moet de gebruiker de **microsoft.compute/virtualMachines/read-bewerking** hebben. Als de gebruiker niet de benodigde bewerking heeft, wordt er een toegangsfout weergegeven.

Voer de volgende stappen uit om de nalevingsgegevens te bekijken:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **op** de pagina Overzicht of **Naleving** een beleid in een **nalevingsstatus** die _niet-compatibel_is.

1. Klik onder het tabblad Naleving van **resources** van de pagina **Naleving van het beleid** met de rechtermuisknop of selecteer de ellips van een resource in een **nalevingsstatus** die _niet-compatibel_is. Selecteer vervolgens **Nalevingsgegevens weergeven**.

   ![Optie Nalevingsdetails weergeven](../media/determine-non-compliance/view-compliance-details.png)

1. In het deelvenster **Nalevingsgegevens** worden informatie weergegeven van de meest recente evaluatie van de resource tot de huidige beleidstoewijzing. In dit voorbeeld wordt het veld **Microsoft.Sql/servers/versie** _12.0_ gevonden, terwijl de beleidsdefinitie _14.0_verwacht. Als de resource om meerdere redenen niet voldoet, wordt deze in dit deelvenster weergegeven.

   ![Nalevingsgegevensvenster en redenen voor niet-naleving](../media/determine-non-compliance/compliance-details-pane.png)

   Voor een **beleidsdefinitie van auditIfNotExists** of **deployIfNotExists** bevatten de details de eigenschap **details.type** en eventuele optionele eigenschappen. Zie [eigenschappen auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) en [deployIfNotExists](../concepts/effects.md#deployifnotexists-properties)voor een lijst . **Laatst geëvalueerde resource** is een gerelateerde bron uit de **detailssectie** van de definitie.

   Voorbeeld van gedeeltelijke **implementatieIfNotExists-definitie:**

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Deelvenster Nalevingsgegevens - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Om gegevens te beschermen, geeft de huidige waarde sterretjes weer wanneer een eigenschapswaarde een _geheim_ is.

Deze details verklaren waarom een resource momenteel niet-compatibel is, maar worden niet weergegeven wanneer de wijziging is aangebracht in de bron waardoor deze niet-compatibel is geworden. Zie [Geschiedenis wijzigen (voorbeeld)](#change-history) hieronder voor die informatie.

### <a name="compliance-reasons"></a>Nalevingsredenen

De volgende matrijs brengt elke mogelijke _reden_ aan de verantwoordelijke [voorwaarde](../concepts/definition-structure.md#conditions) in de beleidsdefinitie toe:

|Reden | Voorwaarde |
|-|-|
|De huidige waarde moet de doelwaarde als sleutel bevatten. |containsKey or notNotContainsKey containsKey or notNotContainsKey containsKey or **not** |
|De huidige waarde moet de doelwaarde bevatten. |bevat of **nietBevat** |
|De huidige waarde moet gelijk zijn aan de doelwaarde. |gelijk is of **nietGelijk** aan |
|De huidige waarde moet lager zijn dan de doelwaarde. |minder of **niet** greaterOrEquals |
|De huidige waarde moet groter zijn dan of gelijk zijn aan de doelwaarde. |greaterOrEquals of **niet** minder |
|De huidige waarde moet groter zijn dan de doelwaarde. |groter of **niet** lessOrEquals |
|De huidige waarde moet lager zijn dan of gelijk zijn aan de doelwaarde. |lessOrEquals of **niet** groter |
|De huidige waarde moet bestaan. |Bestaat |
|De huidige waarde moet de doelwaarde hebben. |in of **nietIn** |
|De huidige waarde moet gelijk zijn aan de doelwaarde. |like of **notnotLike** |
|De huidige waarde moet hoofdlettergevoelig overeenkomen met de doelwaarde. |match of **nietMatch** |
|De huidige waarde moet als hoofdletterongevoelig overeenkomen met de doelwaarde. |matchInsensitively **not** of nietMatchInsensitively |
|De huidige waarde mag de doelwaarde niet als sleutel bevatten. |notContainsKey of **niet** bevatSleutel|
|De huidige waarde mag de doelwaarde niet bevatten. |notContains of **niet** bevat |
|De huidige waarde mag niet gelijk zijn aan de doelwaarde. |notEquals or **not** equals |
|De huidige waarde mag niet bestaan. |**bestaat niet**  |
|De huidige waarde mag niet in de doelwaarde liggen. |notIn of **niet** in |
|De huidige waarde mag niet gelijk zijn aan de doelwaarde. |notLike of **niet** leuk |
|De huidige waarde mag niet hoofdlettergevoelig zijn voor de doelwaarde. |notMatch of **niet** overeenkomen |
|De huidige waarde mag niet als ongevoelig overeenkomen met de doelwaarde. |notMatchInsensitively of **niet** matchInsensitively |
|Geen gerelateerde resources komen overeen met de effectdetails in de beleidsdefinitie. |Een resource van het type dat is gedefinieerd in **die.details.type** en gerelateerd aan de resource die is gedefinieerd in het **als** gedeelte van de beleidsregel niet bestaat. |

## <a name="compliance-details-for-guest-configuration"></a>Nalevingsgegevens voor gastconfiguratie

Voor _auditIfNotExists-beleidsregels_ in de categorie _Gastconfiguratie_ kunnen er meerdere instellingen in de VM worden geëvalueerd en moet u details per instelling weergeven. Als u bijvoorbeeld controleert op een lijst met wachtwoordbeleidsregels en slechts één van deze regels de status _Niet-compatibel_heeft, moet u weten welk specifiek wachtwoordbeleid niet voldoet en waarom.

U hebt mogelijk ook geen toegang tot rechtstreeks aanmelden bij de virtuele machine, maar u moet rapporteren waarom de vm _niet-compatibel_is.

### <a name="azure-portal"></a>Azure Portal

Begin met het volgen van dezelfde stappen in het bovenstaande gedeelte voor het bekijken van beleidsnalevingsdetails.

Klik in de weergave **Nalevingsdetails** op de koppeling **Laatst geëvalueerde bron**.

   ![Controle bekijkenIfNotExists definitie details](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

Op de pagina **Gasttoewijzing** worden alle beschikbare nalevingsgegevens weergegeven. Elke rij in de weergave vertegenwoordigt een evaluatie die in de machine is uitgevoerd. In de kolom **Reden** wordt een woordgroep weergegeven waarin wordt beschreven waarom de gasttoewijzing _niet-compatibel_ is. Als u bijvoorbeeld wachtwoordbeleid controleert, wordt in de kolom **Reden** tekst weergegeven met de huidige waarde voor elke instelling.

![Nalevingsgegevens weergeven](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

U ook nalevingsgegevens bekijken vanuit Azure PowerShell. Zorg er eerst voor dat de gastconfiguratiemodule is geïnstalleerd.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

U de huidige status van alle gasttoewijzingen voor een virtuele machine bekijken met de volgende opdracht:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Als u alleen de _redenzin_ wilt weergeven die beschrijft waarom de VM _niet-compatibel_is, geeft u alleen de eigenschap Reden onderliggend terug.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

U ook een nalevingsgeschiedenis voor gasttoewijzingen uitvoeren in het bereik van de machine. De uitvoer van deze opdracht bevat de details van elk rapport voor de VM.

> [!NOTE]
> De uitvoer kan een grote hoeveelheid gegevens retourneren. Het wordt aanbevolen om de uitvoer in een variabele op te slaan.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Als u deze weergave wilt vereenvoudigen, gebruikt u de parameter **ShowChanged.** De uitvoer van deze opdracht bevat alleen de rapporten die volgden op een wijziging in de nalevingsstatus.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"/>Geschiedenis wijzigen (Voorbeeld)

Als onderdeel van een nieuwe **openbare preview**zijn de laatste 14 dagen van de wijzigingsgeschiedenis beschikbaar voor alle Azure-resources die ondersteuning bieden voor het verwijderen van de [volledige modus](../../../azure-resource-manager/templates/complete-mode-deletion.md). Wijzigingsgeschiedenis bevat details over wanneer een wijziging is gedetecteerd en een _visuele diff_ voor elke wijziging. Er wordt een wijzigingsdetectie geactiveerd wanneer de eigenschappen van Resource Manager worden toegevoegd, verwijderd of gewijzigd.

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **op** de pagina Overzicht of **Naleving** een beleid in een **nalevingsstatus.**

1. Selecteer onder het tabblad **Naleving van resources** van de pagina Naleving van **beleid** een resource.

1. Selecteer het tabblad **Geschiedenis wijzigen (voorbeeld)** op de pagina **Resourcecompliance.** Een lijst met gedetecteerde wijzigingen, indien aanwezig, wordt weergegeven.

   ![Tabblad Geschiedenis van Azure-beleidswijziging op de pagina Resourcecompliance](../media/determine-non-compliance/change-history-tab.png)

1. Selecteer een van de gedetecteerde wijzigingen. De _visuele diff_ voor de resource wordt weergegeven op de pagina Geschiedenis **wijzigen.**

   ![Azure Policy Change History Visual Diff op de pagina Geschiedenis wijzigen](../media/determine-non-compliance/change-history-visual-diff.png)

De _visuele diff_ aides in het identificeren van wijzigingen in een resource. De gedetecteerde wijzigingen zijn mogelijk niet gerelateerd aan de huidige nalevingsstatus van de resource.

Gegevens over de wijzigingsgeschiedenis worden geleverd door [Azure Resource Graph](../../resource-graph/overview.md). Zie [Resourcewijzigingen opvragen](../../resource-graph/how-to/get-resource-changes.md)om deze informatie buiten de Azure-portal op te vragen.

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
