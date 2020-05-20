---
title: Oorzaken van niet-naleving bepalen
description: Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Meer informatie over de oorzaak van de niet-naleving.
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: 2b26357e9957259470049209913501cc024caeaa
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684241"
---
# <a name="determine-causes-of-non-compliance"></a>Oorzaken van niet-naleving bepalen

Wanneer een Azure-resource wordt vastgesteld dat deze niet compatibel is met een beleids regel, is het handig om te begrijpen met welk gedeelte van de regel de resource niet compatibel is. Het is ook handig om te begrijpen welke wijziging een eerder compatibele resource heeft gewijzigd om deze niet-compatibel te maken. Er zijn twee manieren om deze informatie te vinden:

> [!div class="checklist"]
> - [Compatibiliteits Details](#compliance-details)
> - [Wijzigings overzicht (preview-versie)](#change-history)

## <a name="compliance-details"></a>Compatibiliteits Details

Wanneer een bron niet compatibel is, zijn de compatibiliteits Details voor die bron beschikbaar op de pagina **naleving van beleid** . Het deel venster nalevings Details bevat de volgende informatie:

- Resource Details, zoals naam, type, locatie en Resource-ID
- Nalevings status en tijds tempel van de laatste evaluatie van de huidige beleids toewijzing
- Een lijst met _redenen_ voor niet-naleving van de resource

> [!IMPORTANT]
> Als de compatibiliteits Details voor een _niet-compatibele_ resource de huidige waarde van eigenschappen voor die resource weer geven, moet de gebruiker een **Lees** bewerking hebben voor het **type** resource. Als de _niet-compatibele_ resource bijvoorbeeld **micro soft. Compute/informatie** is, moet de gebruiker de bewerking **micro soft. Compute/informatie/Read** hebben. Als de gebruiker niet de benodigde bewerking heeft, wordt er een toegangs fout weer gegeven.

Voer de volgende stappen uit om de compatibiliteits gegevens weer te geven:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer op de pagina **overzicht** of **naleving** een beleids regel in een **nalevings status** die _niet compatibel_is.

1. Klik op het tabblad **resource naleving** van de pagina **naleving van beleid** met de rechter muisknop of selecteer het weglatings teken van een resource in een **compatibiliteits status** die _niet compatibel_is. Selecteer vervolgens **nalevings details weer geven**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Optie compatibiliteits details weer geven" border="false":::

1. In het deel venster **nalevings Details** worden de gegevens van de laatste evaluatie van de resource weer gegeven aan de huidige beleids toewijzing. In dit voor beeld wordt het veld **micro soft. SQL/servers/versie** _12,0_ aangetroffen terwijl de beleids definitie _14,0_werd verwacht. Als de resource meerdere redenen niet voldoet, wordt deze in dit deel venster weer gegeven.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Deel venster nalevings Details en redenen voor niet-naleving" border="false":::

   Voor een **auditIfNotExists** -of **deployIfNotExists** -beleids definitie bevatten de details de eigenschap **Details. type** en eventuele optionele eigenschappen. Zie Eigenschappen van [auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) en [deployIfNotExists](../concepts/effects.md#deployifnotexists-properties)voor een lijst. De **laatste geëvalueerde resource** is een gerelateerde resource uit de sectie **Details** van de definitie.

   Voor beeld van een gedeeltelijke **deployIfNotExists** definitie:

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

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Deel venster nalevings Details-* ifNotExists" border="false":::

> [!NOTE]
> Voor het beveiligen van gegevens, wanneer een eigenschaps waarde een _geheim_ is, wordt de huidige waarde sterretjes weer gegeven.

In deze details wordt uitgelegd waarom een resource momenteel niet compatibel is, maar niet wanneer de wijziging is aangebracht in de resource waardoor deze niet-compatibel is. Zie voor deze informatie de [wijzigings geschiedenis (preview-versie)](#change-history) hieronder.

### <a name="compliance-reasons"></a>Nalevings redenen

De volgende matrix wijst elke mogelijke _reden_ toe aan de verantwoordelijke [voor waarde](../concepts/definition-structure.md#conditions) in de beleids definitie:

|Reden | Voorwaarde |
|-|-|
|De huidige waarde moet de doel waarde als sleutel bevatten. |containsKey of **niet** notContainsKey |
|De huidige waarde moet de doel waarde bevatten. |bevat of **niet** notContains |
|De huidige waarde moet gelijk zijn aan de doel waarde. |is gelijk aan of **niet** notEquals |
|De huidige waarde moet kleiner zijn dan de doel waarde. |minder of **niet** greaterOrEquals |
|De huidige waarde moet groter zijn dan of gelijk zijn aan de doel waarde. |greaterOrEquals of **niet** minder |
|De huidige waarde moet groter zijn dan de doel waarde. |groter of **niet** lessOrEquals |
|De huidige waarde moet kleiner zijn dan of gelijk zijn aan de doel waarde. |lessOrEquals of **niet** groter |
|De huidige waarde moet bestaan. |reeds |
|De huidige waarde moet de doel waarde hebben. |in of **niet** notIn |
|De huidige waarde moet vergelijkbaar zijn met de doel waarde. |zoals of **niet** notLike |
|De huidige waarde moet hoofdletter gevoelig overeenkomen met de doel waarde. |komt overeen met of **niet** notMatch |
|De huidige waarde moet hoofdletter gevoelig overeenkomen met de doel waarde. |matchInsensitively of **niet** notMatchInsensitively |
|De huidige waarde mag niet de doel waarde als sleutel bevatten. |notContainsKey of **niet** containsKey|
|De huidige waarde mag niet de doel waarde bevatten. |notContains of bevat **niet** |
|De huidige waarde mag niet gelijk zijn aan de doel waarde. |notEquals of is **niet** gelijk aan |
|De huidige waarde mag niet bestaan. |bestaat **niet**  |
|De huidige waarde mag niet de doel waarde hebben. |notIn of **niet** in |
|De huidige waarde mag niet gelijk zijn aan de doel waarde. |notLike of **niet** als |
|De huidige waarde mag niet hoofdletter gevoelig overeenkomen met de doel waarde. |notMatch of komt **niet** overeen |
|De huidige waarde mag niet hoofdletter gevoelig overeenkomen met de doel waarde. |notMatchInsensitively of **niet** matchInsensitively |
|Er zijn geen gerelateerde resources die overeenkomen met de effect Details in de beleids definitie. |Een resource van het type dat is gedefinieerd in **then. Details. type** en gerelateerd aan de resource die is gedefinieerd in het **als** gedeelte van de beleids regel bestaat niet. |

## <a name="compliance-details-for-guest-configuration"></a>Compatibiliteits Details voor gast configuratie

Voor _auditIfNotExists_ -beleid in de categorie _gast configuratie_ kunnen er meerdere instellingen worden geëvalueerd in de virtuele machine en moet u Details per instelling weer geven. Als u bijvoorbeeld wilt controleren op een lijst met wachtwoord beleid en slechts een van de statussen _niet-compatibel_is, moet u weten welke specifieke wachtwoord beleidsregels niet voldoen aan de vereisten en waarom.

Het is ook mogelijk dat u niet rechtstreeks toegang hebt tot de virtuele machine, maar u moet rapporteren waarom de virtuele machine _niet compatibel_is.

### <a name="azure-portal"></a>Azure Portal

Volg dezelfde stappen in de bovenstaande sectie voor het weer geven van nalevings Details van het beleid.

Klik in de weer gave nalevings detail venster op de koppeling **laatste geëvalueerde resource**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Details van auditIfNotExists-definitie weer geven" border="false":::

Op de pagina **gast toewijzing** worden alle beschik bare nalevings details weer gegeven. Elke rij in de weer gave vertegenwoordigt een evaluatie die in de machine is uitgevoerd. In de kolom **reden** wordt een woord groep weer gegeven waarin wordt beschreven waarom de gast toewijzing _niet compatibel_is. Als u bijvoorbeeld wachtwoord beleid controleert, wordt in de kolom **reden** tekst weer gegeven, inclusief de huidige waarde voor elke instelling.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Nalevings details weer geven" border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

U kunt ook nalevings Details van Azure PowerShell weer geven. Controleer eerst of de module gast configuratie is geïnstalleerd.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

U kunt de huidige status van alle gast toewijzingen voor een virtuele machine weer geven met behulp van de volgende opdracht:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Als u alleen de _reden_ wilt bekijken waarin wordt beschreven waarom de virtuele machine _niet compatibel_is, retourneert u alleen de onderliggende eigenschap reden.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

U kunt ook een nalevings geschiedenis voor gast toewijzingen in het bereik voor de machine uitvoeren. De uitvoer van deze opdracht bevat de details van elk rapport voor de virtuele machine.

> [!NOTE]
> De uitvoer kan een grote hoeveelheid gegevens retour neren. Het is raadzaam om de uitvoer op te slaan in een variabele.

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

Als u deze weer gave wilt vereenvoudigen, gebruikt u de para meter **ShowChanged** . De uitvoer van deze opdracht bevat alleen de rapporten die een wijziging in de nalevings status hebben gevolgd.

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

## <a name="change-history-preview"></a><a name="change-history"/>Wijzigings overzicht (preview-versie)

Als onderdeel van een nieuwe **open bare preview**zijn de laatste 14 dagen aan wijzigings geschiedenis beschikbaar voor alle Azure-resources die de verwijdering van de [volledige modus](../../../azure-resource-manager/templates/complete-mode-deletion.md)ondersteunen. Wijzigings overzicht bevat details over wanneer een wijziging is gedetecteerd en een _visueel verschil_ voor elke wijziging. Een wijzigings detectie wordt geactiveerd wanneer de eigenschappen van de Resource Manager worden toegevoegd, verwijderd of gewijzigd.

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer op de pagina **overzicht** of **naleving** een beleids regel in elke **compatibiliteits status**.

1. Selecteer een resource onder het tabblad **resource naleving** van de pagina **naleving van beleid** .

1. Selecteer het tabblad **wijzigings overzicht (preview)** op de pagina **resource naleving** . Er wordt een lijst weer gegeven met gedetecteerde wijzigingen, indien aanwezig.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Azure Policy tabblad wijzigings overzicht op de pagina Resource naleving" border="false":::

1. Selecteer een van de gedetecteerde wijzigingen. Het _visuele verschil_ voor de resource wordt weer gegeven op de pagina **wijzigings overzicht** .

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Azure Policy wijzigings overzicht voor visueel verschil op pagina met wijzigings geschiedenis" border="false":::

De _visuele diff_ -aideds bij het identificeren van wijzigingen aan een resource. De gedetecteerde wijzigingen zijn mogelijk niet gerelateerd aan de huidige nalevings status van de resource.

De wijzigings geschiedenis gegevens worden door [Azure resource Graph](../../resource-graph/overview.md)verschaft. Als u wilt zoeken naar deze informatie buiten de Azure Portal, raadpleegt u [resource wijzigingen ophalen](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
