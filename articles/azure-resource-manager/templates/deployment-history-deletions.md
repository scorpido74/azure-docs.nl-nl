---
title: Verwijderingen in de implementatiegeschiedenis
description: Hierin wordt beschreven hoe Azure Resource Manager automatisch implementaties uit de implementatie geschiedenis verwijdert. Implementaties worden verwijderd wanneer de geschiedenis bijna de limiet van 800 overschrijdt.
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 1ae0512f1d82da09c9e77b43ba57acb0cde11f5a
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391193"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatische verwijderingen van de implementatie geschiedenis

Telkens wanneer u een sjabloon implementeert, wordt informatie over de implementatie naar de implementatie geschiedenis geschreven. Elke resource groep is beperkt tot 800 implementaties in de implementatie geschiedenis.

Azure Resource Manager begint binnenkort met het automatisch verwijderen van implementaties uit uw geschiedenis als u de limiet hebt bereikt. Automatisch verwijderen is een wijziging ten opzichte van het gedrag van vorige. Voorheen moest u implementaties hand matig verwijderen uit de implementatie geschiedenis om te voor komen dat er een fout optreedt.

> [!NOTE]
> Het verwijderen van een implementatie uit de geschiedenis heeft geen invloed op de resources die zijn geïmplementeerd.
>
> Als u een [CanNotDelete-vergren deling](../management/lock-resources.md) op een resource groep hebt, kunnen de implementaties voor die resource groep niet worden verwijderd. U moet de vergren deling verwijderen om te profiteren van automatische verwijderingen in de implementatie geschiedenis.

## <a name="when-deployments-are-deleted"></a>Wanneer implementaties worden verwijderd

Implementaties worden verwijderd uit de implementatie geschiedenis wanneer u 790-implementaties bereikt. Azure Resource Manager verwijdert een kleine set van de oudste implementaties om ruimte vrij te maken voor toekomstige implementaties. De meeste geschiedenis blijft ongewijzigd. De oudste implementaties worden eerst altijd verwijderd.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Verwijderingen uit de implementatie geschiedenis":::

Naast implementaties triggert u ook verwijderingen wanneer u de [bewerking wat als'](template-deploy-what-if.md) uitvoert of een implementatie valideert.

Wanneer u een implementatie met dezelfde naam als één in de geschiedenis geeft, stelt u de locatie in de geschiedenis opnieuw in. De implementatie wordt verplaatst naar de meest recente plaats in de geschiedenis. U kunt ook de locatie van een implementatie opnieuw instellen wanneer u na een fout [terugkeert naar die implementatie](rollback-on-error.md) .

> [!NOTE]
> Als uw resource groep al de limiet van 800 is, mislukt de volgende implementatie met een fout. Het automatische verwijderings proces wordt onmiddellijk gestart. U kunt de implementatie opnieuw proberen na een korte wacht tijd.

## <a name="opt-out-of-automatic-deletions"></a>Automatische verwijderingen afmelden

U kunt geen automatische verwijderingen meer uit de geschiedenis afmelden. **Gebruik deze optie alleen als u de implementatie geschiedenis zelf wilt beheren.** De limiet van 800 implementaties in de geschiedenis wordt nog steeds afgedwongen. Als u meer dan 800 implementaties hebt, ontvangt u een fout melding en zal uw implementatie mislukken.

Als u automatisch verwijderen wilt uitschakelen, moet u de `Microsoft.Resources/DisableDeploymentGrooming` functie vlag registreren. Wanneer u de functie vlag registreert, hebt u geen automatische verwijderingen meer voor het hele Azure-abonnement. U kunt zich niet afmelden voor een bepaalde resource groep.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor Power shell [REGI ster-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Als u de huidige status van uw abonnement wilt zien, gebruikt u:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik [AZ feature REGI ster](/cli/azure/feature#az-feature-register)voor Azure cli.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Als u de huidige status van uw abonnement wilt zien, gebruikt u:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Gebruik voor REST API de [functies-registreren](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Als u de huidige status van uw abonnement wilt zien, gebruikt u:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [implementatie geschiedenis weer geven met Azure Resource Manager](deployment-history.md)voor meer informatie over het weer geven van de implementatie geschiedenis.
