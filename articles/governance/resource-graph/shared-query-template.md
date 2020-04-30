---
title: 'Snelstartgids: een gedeelde query maken met sjablonen'
description: In deze Snelstartgids gebruikt u een resource manager-sjabloon om een gedeelde query van resource grafiek te maken die virtuele machines per OS telt.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234330"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Snelstartgids: een gedeelde query maken met behulp van een resource manager-sjabloon

Resource grafiek query's kunnen worden opgeslagen als een _persoonlijke query_ of een _gedeelde query_. Een persoonlijke query wordt opgeslagen in het portal Profiel van de persoon en is niet zichtbaar voor anderen. Een gedeelde query is een Resource Manager-object dat met anderen kan worden gedeeld via machtigingen en toegang op basis van rollen. Een gedeelde query biedt een algemene en consistente uitvoering van bron detectie. In deze Snelstartgids wordt een resource manager-sjabloon gebruikt om een gedeelde query te maken.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="create-a-shared-query"></a>Een gedeelde query maken

In deze Quick Start maakt u een gedeelde query met de naam _Vm's tellen per besturings systeem_. Zie voor [beelden: aantal virtuele machines per OS-type](./samples/starter.md#count-os)om deze query te proberen in SDK of in de portal met resource Graph Explorer.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/)start-sjablonen.

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

De resource die in de sjabloon is gedefinieerd, is:

- [Micro soft. ResourceGraph/query's](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> De Azure resource Graph-service is gratis. Zie [overzicht van Azure resource Graph](./overview.md)voor meer informatie.

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure-portal en open de sjabloon:

   [![De beleids sjabloon implementeren in azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Selecteer of voer de volgende waarden in:

   | Naam | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **nieuwe maken**, geef een naam op en selecteer **OK**. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **VS - centraal**. |
   | Query naam | Behoud de standaard waarde **vm's per besturings systeem**. |
   | Query code | De standaard waarde behouden`Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Query Beschrijving | De standaard waarde behouden **deze gedeelde query telt alle resources van de virtuele machine en geeft een samen vatting van het type besturings systeem.** |
   | Ik ga akkoord met de bovenstaande voor waarden | Uitgeschakeld |

1. Selecteer **Aankoop**.

Enkele aanvullende bronnen:

- Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)start-sjabloon voor meer voor beelden van sjablonen.
- Voor een overzicht van de sjabloon verwijzing gaat u naar de [Naslag informatie voor Azure-sjablonen](/azure/templates/microsoft.resourcegraph/allversions).
- Zie [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [resource groepen en-resources op abonnements niveau maken](../../azure-resource-manager/templates/deploy-to-subscription.md)voor meer informatie over implementatie op abonnements niveau.

## <a name="validate-the-deployment"></a>De implementatie valideren

Voer de volgende stappen uit om de nieuwe gedeelde query uit te voeren:

1. Zoek in de zoek balk van de portal naar **resource grafiek query's** en selecteer deze.

1. Selecteer de gedeelde query met de naam **aantal vm's per besturings systeem**en selecteer vervolgens het tabblad **resultaten** op de pagina **overzicht** .

U kunt de gedeelde query ook openen vanuit resource Graph Explorer:

1. Zoek in de portal-zoek balk naar **resource Graph Verkenner** en selecteer deze.

1. Selecteer de knop **een query openen** .

1. Wijzig het **type** in _gedeelde query's_. Als u het **aantal vm's per besturings systeem** in de lijst niet ziet, gebruikt u het vak filteren om de resultaten te beperken. Zodra het **aantal vm's per OS** -gedeelde query zichtbaar is, selecteert u de naam.

1. Wanneer de query is geladen, selecteert u de knop **query uitvoeren** . De resultaten worden weer gegeven op het tabblad **resultaten** hieronder.

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende stappen uit om de gedeelde query te verwijderen:

1. Zoek in de zoek balk van de portal naar **resource grafiek query's** en selecteer deze.

1. Stel het selectie vakje in naast de gedeelde query met de naam **aantal vm's per besturings systeem**.

1. Selecteer de knop **verwijderen** boven aan de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een gedeelde query voor resource Graph gemaakt.

Voor meer informatie over gedeelde query's gaat u verder met de zelf studie voor:

> [!div class="nextstepaction"]
> [Query's beheren in Azure Portal](./tutorials/create-share-query.md)