---
title: Azure-portal gebruiken om de servicecatalogus-app te implementeren
description: Laat consumenten van beheerde toepassingen zien hoe ze een servicecatalogus-app kunnen implementeren via de Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 9a69296ddfc93fd7e8a6650df91876829631f5d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473061"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Snelstart: servicecatalogus-app implementeren via Azure-portal

In de [voorafgaande quickstart](publish-managed-app-definition-quickstart.md)hebt u een beheerde toepassingsdefinitie gepubliceerd. In deze quickstart maakt u een servicecatalogus-app vanuit die definitie.

## <a name="create-service-catalog-app"></a>App servicecatalogus maken

Gebruik in de Azure-portal de volgende stappen:

1. Selecteer **Een resource maken**.

   ![Een resource maken](./media/deploy-service-catalog-quickstart/create-new.png)

1. Zoek naar **Service Catalog Managed Application** en selecteer deze uit de beschikbare opties.

   ![Zoeken naar servicecatalogustoepassing](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. U ziet een beschrijving van de managed application-service. Selecteer **Maken**.

   ![Selecteer Maken](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. De portal toont de beheerde toepassingsdefinities waartoe u toegang hebt. Selecteer in de beschikbare definities de definities die u wilt implementeren. Gebruik in deze snelstart de definitie **van beheerde opslagaccount** die u in de vorige quickstart hebt gemaakt. Selecteer **Maken**.

   ![Definitie selecteren om te implementeren](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Geef waarden op voor het tabblad **Basisbeginselen.** Selecteer het Azure-abonnement waarop de app voor uw servicecatalogus moet worden geïmplementeerd. Een nieuwe resourcegroep met de naam **applicationGroup maken**. Selecteer een locatie voor uw app. Selecteer **OK** als u klaar bent.

   ![Waarden opgeven voor basiswaarden](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Geef een voorvoegsel op voor de naam van het opslagaccount. Selecteer het type opslagaccount dat u wilt maken. Selecteer **OK** als u klaar bent.

   ![Waarden opgeven voor opslag](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Bekijk de samenvatting. Nadat de validatie is geslaagd, selecteert u **OK** om de implementatie te starten.

   ![Overzicht weergeven](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Resultaten weergeven

Nadat de app voor servicecatalogus is geïmplementeerd, hebt u twee nieuwe brongroepen. Eén resourcegroep bevat de app servicecatalogus. De andere resourcegroep bevat de bronnen voor de app servicecatalogus.

1. Bekijk de resourcegroep met de naam **applicationGroup** om de app voor de servicecatalogus weer te geven.

   ![Toepassing weergeven](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Bekijk de brongroep met de naam **applicationGroup{hash-characters}** om de bronnen voor de servicecatalogus-app weer te geven.

   ![Resources weergeven](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Een beheerde toepassingsdefinitie maken en publiceren](publish-service-catalog-app.md)voor meer informatie over het maken van de definitiebestanden voor een beheerde toepassing.
* Zie [Servicecatalogus-app implementeren met Azure CLI voor](./scripts/managed-application-cli-sample-create-application.md)Azure CLI .
* Zie [Servicecatalogus-app implementeren met PowerShell](./scripts/managed-application-poweshell-sample-create-application.md)voor PowerShell.
