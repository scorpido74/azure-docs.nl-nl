---
title: Integratie accounts voor B2B-oplossingen maken en beheren-Azure Logic Apps
description: Integratie accounts maken, koppelen, verplaatsen en verwijderen voor zakelijke integratie-en B2B-oplossingen met behulp van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 55843f9acaafa0c5963cfac735fdb92eeeacdc02
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982935"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-by-using-azure-logic-apps"></a>Integratie accounts voor B2B-oplossingen maken en beheren met behulp van Azure Logic Apps

Voordat u [bedrijfs integratie-en B2B-oplossingen](../logic-apps/logic-apps-enterprise-integration-overview.md) kunt bouwen met behulp van [Azure Logic apps](../logic-apps/logic-apps-overview.md), moet u een integratie account maken. Dit is een afzonderlijke Azure-resource die een veilige, schaal bare en beheersbare container biedt voor de integratie artefacten die u definieert en gebruikt met uw logische app-werk stromen.

U kunt B2B-artefacten bijvoorbeeld maken, opslaan en beheren, zoals handels partners, overeenkomsten, kaarten, schema's, certificaten en batch configuraties. Voordat uw logische app met deze artefacten kan werken en de Logische apps B2B-connectors gebruikt, moet u ook [uw integratie account koppelen](#link-account) aan uw logische app. Het integratie account en de logische app moeten zich op *dezelfde* locatie of regio bevinden.

> [!TIP]
> Zie [integratie accounts maken in een ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)voor meer informatie over het maken van een integratie account in een [integratie service omgeving](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

In dit onderwerp wordt beschreven hoe u deze taken uitvoert:

* Maak uw integratie account.
* Koppel uw integratie account aan een logische app.
* Wijzig de prijs categorie voor uw integratie account.
* Uw integratie account ontkoppelen van een logische app.
* Verplaats uw integratie account naar een andere Azure-resource groep of-abonnement.
* Verwijder het integratie account.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integratie account maken

Voor deze taak kunt u de Azure Portal gebruiken door de stappen in deze sectie, [Azure PowerShell](https://docs.microsoft.com//powershell/module/azurerm.logicapp/New-AzureRmIntegrationAccount)of [Azure cli](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)uit te voeren.

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het hoofdmenu van Azure. Voer in het zoekvak ' integratie account ' in als uw filter en selecteer **integratie account**.

   ![Nieuw integratie account maken](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Onder **integratie account**selecteert u **maken**.

   ![Kies toevoegen om een integratie account te maken](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Geef deze informatie over uw integratie account op:

   ![Details van het integratie account opgeven](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Eigenschap | Vereist | Value | Beschrijving |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*integratie-account-naam*> | De naam van uw integratie account, die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`), haakjes (`(`, `)`) en punten (`.`) kan bevatten. In dit voor beeld wordt ' fabrikam-Integration ' gebruikt. |
   | **Abonnement** | Ja | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | Ja | <*Azure-resource-group-name*> | De naam voor de [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) die moet worden gebruikt voor het ordenen van verwante resources. Voor dit voor beeld maakt u een nieuwe resource groep met de naam ' FabrikamIntegration-RG '. |
   | **Prijscategorie** | Ja | <*prijs niveau*> | De prijs categorie voor het integratie account, dat u later kunt wijzigen. Voor dit voor beeld selecteert u **gratis**. Zie de volgende onderwerpen voor meer informatie: <p>- [Logic Apps prijs model](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Location** | Ja | <*Azure-regio*> | De regio waar de meta gegevens van uw integratie account moeten worden opgeslagen. Selecteer dezelfde locatie als uw logische app of maak uw Logic apps op dezelfde locatie als uw integratie account. Gebruik voor dit voor beeld ' vs-West '. <p>**Opmerking**: Als u een integratie account in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wilt maken, selecteert u die ISE als de locatie. Zie [integratie accounts maken in een ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)voor meer informatie. |
   | **Log Analytics** | Nee | Uit, op | Laat de instelling **uitgeschakeld** voor dit voor beeld. |
   |||||

1. Wanneer u klaar bent, selecteert u **maken**.

   Nadat de implementatie is voltooid, opent Azure uw integratie account.

   ![Azure opent een integratie account](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Voordat uw logische app uw integratie account kan gebruiken, volgt u de volgende stappen om uw integratie account en logische app samen te koppelen.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Koppeling naar logische app

Om uw Logic apps toegang te geven tot een integratie account dat uw B2B-artefacten bevat, moet u eerst uw integratie account koppelen aan uw logische app. Zowel de logische app als het integratie account moeten zich in dezelfde regio bevinden. Voor deze taak kunt u de Azure Portal gebruiken door de stappen in deze sectie te volgen.

1. Zoek en open uw logische app in de Azure Portal.

1. Open in de [Azure Portal](https://portal.azure.com)een bestaande logische app of maak een nieuwe logische app.

1. Selecteer **werk stroom instellingen**onder **instellingen**in het menu van de logische app. Open de lijst **Selecteer een integratie account** onder **integratie account**. Selecteer het integratie account dat u wilt koppelen aan uw logische app.

   ![Het integratie account selecteren](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Selecteer **Opslaan**om de koppeling te volt ooien.

   ![Het integratie account selecteren](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Nadat het integratie account is gekoppeld, wordt er een bevestigings bericht weer gegeven in Azure.

   ![Azure bevestigt een geslaagde koppeling](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Uw logische app kan nu gebruikmaken van de artefacten in uw integratie account plus de B2B-connectors, zoals XML-validatie en platte bestands codering of-decodering.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Prijscategorie wijzigen

Als u de [limieten](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) voor een integratie account wilt verhogen, kunt u [een upgrade uitvoeren naar een hogere prijs categorie](#upgrade-pricing-tier), indien beschikbaar. U kunt bijvoorbeeld een upgrade uitvoeren van de gratis laag naar de laag basis of de laag standaard. U kunt ook [downgrade naar een lagere laag](#downgrade-pricing-tier), indien beschikbaar. Zie de volgende onderwerpen voor meer informatie over de prijs informatie:

* [Prijzen voor Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps prijs model](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Prijs categorie bijwerken

Als u deze wijziging wilt aanbrengen, kunt u de Azure Portal gebruiken door de stappen in deze sectie of de [Azure cli](#upgrade-tier-azure-cli)te volgen.

#### <a name="azure-portal"></a>Azure Portal

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. In het hoofd venster van Azure Search voert u "integratie accounts" in als uw filter en selecteert u **integratie accounts**.

   ![Integratie account zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   In Azure worden alle integratie accounts in uw Azure-abonnementen weer gegeven.

1. Onder **integratie accounts**selecteert u het integratie account dat u wilt verplaatsen. Selecteer **overzicht**in het menu integratie account.

   ![Selecteer overzicht in het menu integratie account](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer in het deel venster Overzicht de optie **prijs categorie bijwerken**, waarin alle beschik bare hogere lagen worden weer gegeven. Wanneer u een laag selecteert, wordt de wijziging onmiddellijk van kracht.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure-CLI

1. Als u dit nog niet hebt gedaan, [installeert u de vereisten voor Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Open de Azure [**Cloud shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) -omgeving In de Azure Portal.

   ![Azure Cloud Shell openen](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Voer bij de opdracht prompt de opdracht [ **AZ resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)in en stel `skuName` in op de gewenste hogere laag.

   ```Azure CLI
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Als u bijvoorbeeld de laag basis hebt, kunt u het `skuName` `Standard`volgende instellen:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Prijs categorie downgrade

Als u deze wijziging wilt aanbrengen, gebruikt u de [Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Als u dit nog niet hebt gedaan, [installeert u de vereisten voor Azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Open de Azure [**Cloud shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) -omgeving In de Azure Portal.

   ![Azure Cloud Shell openen](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Voer bij de opdracht prompt de opdracht [ **AZ resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) in en stel `skuName` in op de lagere laag die u wilt.

   ```Azure CLI
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Als u bijvoorbeeld de laag standaard hebt, kunt u het `skuName` `Basic`volgende instellen:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Ontkoppelen van logische app

Als u uw logische app wilt koppelen aan een ander integratie account of als u een integratie account niet meer wilt gebruiken met uw logische app, verwijdert u de koppeling met behulp van Azure Resource Explorer.

1. Open uw browser venster en ga naar [Azure resource Explorer (https://resources.azure.com)](https://resources.azure.com). Meld u aan met dezelfde referenties voor het Azure-account.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Voer in het zoekvak de naam van de logische app in, zodat u uw logische app kunt vinden en selecteren.

   ![Logische app zoeken en selecteren](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Selecteer **lezen/schrijven**op de titel balk van Explorer.

   ![Modus lezen/schrijven inschakelen](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Selecteer **bewerken**op het tabblad **gegevens** .

   ![Selecteer op het tabblad gegevens de optie bewerken.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Zoek het `integrationAccount` object in de editor en verwijder deze eigenschap. deze heeft de volgende indeling:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Bijvoorbeeld:

   ![Object ' Integration account ' zoeken](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Selecteer op het tabblad **gegevens** de optie **put** om uw wijzigingen op te slaan.

   ![Als u de wijzigingen wilt opslaan, selecteert u ' put '](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Zoek en selecteer uw logische app in de Azure Portal. Controleer onder **werk stroom instellingen**van uw app of de eigenschap **integratie account** nu leeg wordt weer gegeven.

   ![Controleer of het integratie account niet is gekoppeld](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integratie account verplaatsen

U kunt uw integratie account verplaatsen naar een andere Azure-resource groep of een Azure-abonnement. Wanneer u resources verplaatst, worden er nieuwe resource-Id's gemaakt in Azure. Zorg er dus voor dat u de nieuwe Id's gebruikt en eventuele scripts of hulpprogram ma's die zijn gekoppeld aan de verplaatste resources, bijwerkt. Als u het abonnement wilt wijzigen, moet u ook een bestaande of nieuwe resource groep opgeven.

Voor deze taak kunt u de Azure Portal gebruiken door de stappen in deze sectie of de [Azure cli](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)uit te voeren.

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. In het hoofd venster van Azure Search voert u "integratie accounts" in als uw filter en selecteert u **integratie accounts**.

   ![Integratie account zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   In Azure worden alle integratie accounts in uw Azure-abonnementen weer gegeven.

1. Onder **integratie accounts**selecteert u het integratie account dat u wilt verplaatsen. Selecteer **overzicht**in het menu integratie account.

   ![Selecteer overzicht in het menu integratie account](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Naast de **resource groep** of de **naam**van het abonnement selecteert u **wijzigen**.

   ![Resource groep of-abonnement wijzigen](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecteer alle gerelateerde resources die u ook wilt verplaatsen.

1. Voer de volgende stappen uit op basis van uw selectie om de resource groep of het abonnement te wijzigen:

   * Resourcegroep: Selecteer de doel resource groep in de lijst **resource groep** . Als u een andere resource groep wilt maken, selecteert u **een nieuwe resource groep maken**.

   * Abonnement: Selecteer in de lijst **abonnement** het doel abonnement. Selecteer de doel resource groep in de lijst **resource groep** . Als u een andere resource groep wilt maken, selecteert u **een nieuwe resource groep maken**.

1. Als u wilt weten dat scripts of hulpprogram ma's die zijn gekoppeld aan de verplaatste resources, niet werken totdat u ze bijwerkt met de nieuwe resource-Id's, selecteert u het bevestigings venster en selecteert u **OK**.

1. Wanneer u klaar bent, moet u alle scripts bijwerken met de nieuwe resource-Id's voor de verplaatste resources.  

## <a name="delete-integration-account"></a>Integratie account verwijderen

Voor deze taak kunt u de Azure Portal gebruiken door de stappen in deze sectie, [Azure cli](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)of [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/Remove-AzureRmIntegrationAccount)te volgen.

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. In het hoofd venster van Azure Search voert u "integratie accounts" in als uw filter en selecteert u **integratie accounts**.

   ![Integratie account zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   In Azure worden alle integratie accounts in uw Azure-abonnementen weer gegeven.

1. Onder **integratie accounts**selecteert u het integratie account dat u wilt verwijderen. Selecteer **overzicht**in het menu integratie account.

   ![Selecteer overzicht in het menu integratie account](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer **verwijderen**in het deel venster Overzicht.

   ![Selecteer verwijderen in het deel venster Overzicht.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Selecteer **Ja**om te bevestigen dat u uw integratie account wilt verwijderen.

   ![Selecteer Ja om het verwijderen te bevestigen](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Volgende stappen

* [Handels partners maken in uw integratie account](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Overeenkomsten tussen partners maken in uw integratie account](../logic-apps/logic-apps-enterprise-integration-agreements.md)
