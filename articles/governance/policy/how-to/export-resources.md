---
title: Azure Policy-beleid exporteren
description: Meer informatie over het exporteren van Azure Policy-resources naar GitHub, zoals beleids definities en beleids toewijzingen.
ms.date: 09/30/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 691e0a026c5f4f1a0a68c744ee81b1da8da9e70b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777085"
---
# <a name="export-azure-policy-resources"></a>Azure Policy-beleid exporteren

Dit artikel bevat informatie over het exporteren van uw bestaande Azure Policy-resources. Het exporteren van uw resources is nuttig en aanbevolen voor back-ups, maar is ook een belang rijke stap in uw reis met Cloud governance en het behandelen van uw [beleid als code](../concepts/policy-as-code.md). Azure Policy resources kunnen worden geëxporteerd via [Azure Portal](#export-with-azure-portal), [Azure cli](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)en elk van de ondersteunde sdk's.

## <a name="export-with-azure-portal"></a>Exporteren met Azure Portal

Voer de volgende stappen uit om een beleids definitie te exporteren uit Azure Portal:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **definities** aan de linkerkant van de pagina Azure Policy.

1. Gebruik de knop **definities exporteren** of selecteer het weglatings teken in de rij van een beleids definitie en selecteer vervolgens **definitie exporteren**.

1. Selecteer de knop **Aanmelden met github** . Als u nog niet hebt geverifieerd met GitHub om Azure Policy te autoriseren voor het exporteren van de resource, controleert u de toegang tot de [github-actie](https://github.com/features/actions) behoeften in het nieuwe venster dat wordt geopend en selecteert u **AzureGitHubActions autoriseren** om door te gaan met het export proces. Zodra de bewerking is voltooid, wordt het nieuwe venster automatisch gesloten.

1. Stel op het tabblad **basis beginselen** de volgende opties in en selecteer vervolgens het tabblad **beleid** of **volgende:** de knop beleid onder aan de pagina.

   - **Opslagplaats filter**: Stel deze in op _mijn opslag_ plaatsen om alleen opslag plaatsen weer te geven waarvan u de eigenaar bent of _alle opslag_ plaatsen om alles weer te geven waaraan u de GitHub-actie toegang hebt verleend.
   - **Opslag plaats**: Stel in de opslag plaats in waarnaar u de Azure Policy resources wilt exporteren.
   - **Vertakking**: Stel de vertakking in de opslag plaats in. Het gebruik van een andere vertakking dan het standaard filiaal is een goede manier om uw updates te valideren voordat u de bron code verder samenvoegt.
   - **Map**: de _map op het hoofd niveau_ waarnaar de Azure Policy resources moeten worden geëxporteerd. Submappen onder deze map worden gemaakt op basis van de resources die worden geëxporteerd.

1. Stel op het tabblad **beleid** het bereik in op zoeken door het weglatings teken te selecteren en een combi natie van beheer groepen, abonnementen of resource groepen te kiezen.
   
1. Gebruik de knop **beleids definities toevoegen** om te zoeken naar de scope waarvan de objecten moeten worden geëxporteerd. Selecteer in het side-venster dat wordt geopend elk object dat u wilt exporteren. De selectie filteren op het zoekvak of het type. Als u alle objecten hebt geselecteerd die u wilt exporteren, klikt u op de knop **toevoegen** onder aan de pagina.

1. Selecteer voor elk geselecteerd object de gewenste export opties zoals _definitie_ of _definitie en toewijzing (en)_ voor een beleids definitie. Selecteer vervolgens het tabblad **controleren + exporteren** of **volgende: de knop controleren + exporteren** onder aan de pagina.

   > [!NOTE]
   > Als optie _definitie en toewijzing (en)_ is gekozen, worden alleen beleids toewijzingen binnen het bereik dat is ingesteld door het filter bij het toevoegen van de beleids definitie, geëxporteerd.

1. Controleer op het tabblad **controleren en exporteren** de gegevens overeenkomst en gebruik vervolgens de knop **exporteren** onder aan de pagina.

1. Controleer uw GitHub-opslag plaats, vertakkings-en _hoofdmap_ om te zien of de geselecteerde resources nu zijn geëxporteerd naar het bron beheer.

De Azure Policy resources worden geëxporteerd naar de volgende structuur in de geselecteerde GitHub-opslag plaats en- _hoofdmap_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exporteren met Azure CLI

Azure Policy definities, initiatieven en toewijzingen kunnen elk worden geëxporteerd als JSON met [Azure cli](/cli/azure/install-azure-cli). In elk van deze opdrachten wordt een para meter **name** gebruikt om op te geven voor welk object de JSON moet worden opgehaald. De eigenschap **name** is vaak een _GUID_ en is niet de **DisplayName** van het object.

- Definitie- [AZ Policy Definition show](/cli/azure/policy/definition#az-policy-definition-show)
- Initiative- [AZ policy set-definition show](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- Toewijzing- [AZ-beleids toewijzing weer geven](/cli/azure/policy/assignment#az-policy-assignment-show)

Hier volgt een voor beeld van het ophalen van de JSON voor een beleids definitie met de **naam** _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exporteren met Azure PowerShell

Azure Policy definities, initiatieven en toewijzingen kunnen worden geëxporteerd als JSON met [Azure PowerShell](/powershell/azure/). Elk van deze cmdlets maakt gebruik van een para meter **name** om op te geven voor welk object de JSON moet worden opgehaald. De eigenschap **name** is vaak een _GUID_ en is niet de **DisplayName** van het object.

- Definitie- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Initiative- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Toewijzing- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Hier volgt een voor beeld van het ophalen van de JSON voor een beleids definitie met de **naam** _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage'
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
