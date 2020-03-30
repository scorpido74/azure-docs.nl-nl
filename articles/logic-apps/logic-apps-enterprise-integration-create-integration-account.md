---
title: B2B-integratieaccounts maken of beheren
description: Integratieaccounts maken, koppelen en beheren voor bedrijfsintegratie met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270327"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Integratieaccounts maken en beheren voor B2B-bedrijfsintegraties in Azure Logic Apps

Voordat u [bedrijfsintegratie en B2B-oplossingen](../logic-apps/logic-apps-enterprise-integration-overview.md) kunt bouwen met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md), moet u een integratieaccount maken. Dit is een afzonderlijke Azure-resource die een veilige, schaalbare en beheerbare container biedt voor de integratieartefacten die u definieert en gebruikt met de werkstromen in uw logische app.

U bijvoorbeeld B2B-artefacten maken, opslaan en beheren, zoals handelspartners, overeenkomsten, kaarten, schema's, certificaten en batchconfiguraties. Voordat uw logica-app met deze artefacten kan werken en de B2B-connectors van Logic Apps kan gebruiken, moet u [uw integratieaccount koppelen](#link-account) aan uw logische app. Zowel uw integratieaccount als de logische app moeten op *dezelfde* locatie of regio bestaan.

> [!TIP]
> Zie [Integratieaccounts maken in een ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)als u een integratieaccount wilt maken in een [integratieserviceomgeving.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

In dit onderwerp ziet u hoe u deze taken uitvoert:

* Maak uw integratieaccount aan.
* Koppel uw integratieaccount aan een logische app.
* Wijzig de prijscategorie voor uw integratieaccount.
* Koppel uw integratieaccount los uit een logische app.
* Uw integratieaccount verplaatsen naar een andere Azure-brongroep of -abonnement.
* Verwijder uw integratieaccount.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integratieaccount maken

Voor deze taak u de Azure-portal gebruiken door de stappen in deze sectie, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)of [Azure CLI te](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)volgen.

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het hoofdmenu van Azure. Typ in het zoekvak 'integratieaccount' als filter en selecteer **Integratieaccount**.

   ![Nieuw integratieaccount maken](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Selecteer **onder Integratieaccount**de optie **Maken**.

   ![Kies 'Toevoegen' om een integratieaccount te maken](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Geef deze informatie op over uw integratieaccount:

   ![Integratieaccountgegevens opgeven](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*integratie-accountnaam*> | De naam van uw integratieaccount, die alleen letters,`-`cijfers, koppeltekens ( ), onderstreept (`_`), haakjes ( ,`(` `)`en perioden (`.`). In dit voorbeeld wordt gebruik gemaakt van "Fabrikam-Integration". |
   | **Abonnement** | Ja | <*Azure-abonnementsnaam*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | Ja | <*Naam azure-resource-groep*> | De naam voor de [Azure-brongroep](../azure-resource-manager/management/overview.md) die moet worden gebruikt voor het organiseren van gerelateerde resources. Maak in dit voorbeeld een nieuwe resourcegroep met de naam "FabrikamIntegration-RG". |
   | **Prijsniveau** | Ja | <*prijsniveau*> | De prijscategorie voor het integratieaccount, die u later wijzigen. Selecteer in dit voorbeeld **Gratis**. Zie deze onderwerpen voor meer informatie: <p>- [Prijsmodel voor Logische Apps](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limieten en configuratie van Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Locatie** | Ja | <*Azure-regio*> | De regio waar u metagegevens van uw integratieaccount opslaan. Selecteer dezelfde locatie als uw logische app of maak uw logische apps op dezelfde locatie als uw integratieaccount. Gebruik in dit voorbeeld "West US". <p>**Opmerking:** Als u een integratieaccount wilt maken binnen een [integratieserviceomgeving (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selecteert u die ISE als locatie. Zie [Integratieaccounts maken in een ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)voor meer informatie. |
   | **Logboekanalyse** | Nee | Uit, aan | Houd de **instelling Uit** voor dit voorbeeld. |
   |||||

1. Wanneer u klaar bent, selecteert u **Maken**.

   Nadat de implementatie is voltooid, opent Azure uw integratieaccount.

   ![Azure opent integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Voordat uw logica-app uw integratieaccount kan gebruiken, voert u de volgende stappen uit om uw integratieaccount en logische app aan elkaar te koppelen.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Koppeling maken naar logische app

Als u uw logische apps toegang wilt geven tot een integratieaccount dat uw B2B-artefacten bevat, moet u eerst uw integratieaccount koppelen aan uw logische app. Zowel logic app als integratie-account moeten in dezelfde regio bestaan. Als u deze taak wilt voltooien, u de Azure-portal gebruiken. Als u Visual Studio gebruikt en uw logische app zich in een [Azure Resource Group-project bevindt,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)u [uw logische app koppelen aan een integratieaccount met Behulp van Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Zoek en open uw logische app in de Azure-portal.

1. Open in de [Azure-portal](https://portal.azure.com)een bestaande logische app of maak een nieuwe logische app.

1. Selecteer in het menu van uw logische app onder **Instellingen**de optie **Werkstroominstellingen**. Open **onder Integratieaccount**de lijst **Een integratieaccount selecteren.** Selecteer het integratieaccount dat u wilt koppelen aan uw logica-app.

   ![Selecteer uw integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Als u de koppeling wilt voltooien, selecteert u **Opslaan**.

   ![Selecteer uw integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Nadat uw integratieaccount is gekoppeld, wordt in Azure een bevestigingsbericht weergegeven.

   ![Azure bevestigt succesvolle koppeling](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Nu kan uw logische app de artefacten in uw integratieaccount gebruiken plus de B2B-connectors, zoals XML-validatie en flat-file codering of decodering.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Prijscategorie wijzigen

Als u de [limieten](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) voor een integratieaccount wilt verhogen, u [upgraden naar een hogere prijscategorie](#upgrade-pricing-tier), indien beschikbaar. U bijvoorbeeld upgraden van de laag Gratis naar de laag Basis of Standaard. U ook [downgraden naar een lagere laag,](#downgrade-pricing-tier)indien beschikbaar. Zie de volgende onderwerpen voor meer informatie over prijsinformatie:

* [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Prijsmodel voor logische apps](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Prijscategorie voor upgrade

Als u deze wijziging wilt aanbrengen, u de Azure-portal gebruiken door de stappen in deze sectie of de [Azure CLI te](#upgrade-tier-azure-cli)volgen.

#### <a name="azure-portal"></a>Azure Portal

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Typ in het belangrijkste Azure-zoekvak 'integratieaccounts' als filter en selecteer **Integratieaccounts**.

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure toont alle integratieaccounts in uw Azure-abonnementen.

1. Selecteer **onder Integratieaccounts**het integratieaccount dat u wilt verplaatsen. Selecteer **Overzicht**in het menu van uw integratieaccount .

   ![Selecteer in het menu van het integratieaccount de optie 'Overzicht'](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer in het deelvenster Overzicht de **optie Prijslaag Bijwerken**, waarin eventuele beschikbare hogere lagen worden weergegeven. Wanneer u een laag selecteert, wordt de wijziging onmiddellijk van kracht.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure-CLI

1. Als u dit nog niet hebt gedaan, [installeert u de Azure CLI-vereisten](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Open de Azure Cloud [**Shell-omgeving**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) in de Azure Cloud Shell-portal.

   ![Azure Cloud Shell openen](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Voer bij de opdrachtprompt de opdracht `skuName` [ **AZ-resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)in en stel in op de gewenste hogere laag.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Als u bijvoorbeeld de basislaag hebt, `skuName` `Standard`kunt u instellen op:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Prijscategorie downgraden

Als u deze wijziging wilt aanbrengen, gebruikt u de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Als u dit nog niet hebt gedaan, [installeert u de Azure CLI-vereisten](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Open de Azure Cloud [**Shell-omgeving**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) in de Azure Cloud Shell-portal.

   ![Azure Cloud Shell openen](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Voer bij de opdrachtprompt de opdracht `skuName` [ **AZ-resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) in en stel in op de gewenste lagere laag.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Als u bijvoorbeeld de standaardlaag hebt, `skuName` `Basic`kunt u instellen op:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Loskoppelen van logische app

Als u uw logische app wilt koppelen aan een ander integratieaccount of geen integratieaccount meer wilt gebruiken met uw logische app, verwijdert u de koppeling met Azure Resource Explorer.

1. Open uw browservenster en ga naar [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Meld u aan met dezelfde Azure-accountreferenties.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Voer in het zoekvak de naam van uw logische app in, zodat u uw logische app vinden en selecteren.

   ![Logische app zoeken en selecteren](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Selecteer **lezen/schrijven**op de titelbalk van de Verkenner .

   ![De modus Lezen/schrijven inschakelen](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **Selecteer**bewerken op het tabblad **Gegevens** .

   ![Selecteer op het tabblad 'Gegevens' de optie 'Bewerken'](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Zoek in de `integrationAccount` editor het object en verwijder die eigenschap, die deze indeling heeft:

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

   ![Object 'integrationAccount' zoeken](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Selecteer op het tabblad **Gegevens** de optie **Plaatsen** om uw wijzigingen op te slaan.

   ![Als u wijzigingen wilt opslaan, selecteert u 'Zetten'](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Zoek en selecteer uw logische app in de Azure-portal. Controleer onder de **werkstroominstellingen**van uw app of de eigenschap **Integratie-account** nu leeg wordt weergegeven.

   ![Controleren of het integratieaccount niet is gekoppeld](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integratieaccount verplaatsen

U uw integratieaccount verplaatsen naar een andere Azure-brongroep of Azure-abonnement. Wanneer u resources verplaatst, maakt Azure nieuwe bron-id's, dus zorg ervoor dat u de nieuwe id's gebruikt en werk alle scripts of hulpprogramma's bij die zijn gekoppeld aan de verplaatste resources. Als u het abonnement wilt wijzigen, moet u ook een bestaande of nieuwe brongroep opgeven.

Voor deze taak u de Azure-portal gebruiken door de stappen in deze sectie of de [Azure CLI te](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)volgen.

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Typ in het belangrijkste Azure-zoekvak 'integratieaccounts' als filter en selecteer **Integratieaccounts**.

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure toont alle integratieaccounts in uw Azure-abonnementen.

1. Selecteer **onder Integratieaccounts**het integratieaccount dat u wilt verplaatsen. Selecteer **Overzicht**in het menu van uw integratieaccount .

   ![Selecteer in het menu van het integratieaccount de optie 'Overzicht'](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer naast **de groep Resource** of de naam Van **abonnement**de optie **Wijzigen**.

   ![Resourcegroep of -abonnement wijzigen](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecteer alle gerelateerde resources die u ook wilt verplaatsen.

1. Volg op basis van uw selectie de volgende stappen om de brongroep of het abonnement te wijzigen:

   * Resourcegroep: selecteer in de lijst **resourcegroep** de doelbrongroep. Als u een andere resourcegroep wilt maken, selecteert u **Een nieuwe resourcegroep maken**.

   * Abonnement: Selecteer in de lijst **Abonnement** het bestemmingsabonnement. Selecteer in de lijst **Resourcegroep** de groep doelbron. Als u een andere resourcegroep wilt maken, selecteert u **Een nieuwe resourcegroep maken**.

1. Als u wilt erkennen dat scripts of hulpprogramma's die zijn gekoppeld aan de verplaatste resources niet werken totdat u ze hebt bijgewerkt met de nieuwe bron-id's, selecteert u het bevestigingsvak en selecteert u **OK**.

1. Zorg ervoor dat u alle scripts bijwerkt met de nieuwe bron-id's voor uw verplaatste resources.  

## <a name="delete-integration-account"></a>Integratieaccount verwijderen

Voor deze taak u de Azure-portal gebruiken door de stappen in deze sectie te volgen, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)of [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Typ in het belangrijkste Azure-zoekvak 'integratieaccounts' als filter en selecteer **Integratieaccounts**.

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure toont alle integratieaccounts in uw Azure-abonnementen.

1. Selecteer **onder Integratieaccounts**het integratieaccount dat u wilt verwijderen. Selecteer **Overzicht**in het menu van uw integratieaccount .

   ![Selecteer in het menu van het integratieaccount de optie 'Overzicht'](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer **Verwijderen**in het deelvenster Overzicht .

   ![Selecteer in het deelvenster 'Overzicht' de optie 'Verwijderen'](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Als u wilt bevestigen dat u uw integratieaccount wilt verwijderen, selecteert u **Ja**.

   ![Als u verwijderen wilt bevestigen, selecteert u 'Ja'.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Volgende stappen

* [Handelspartners maken in uw integratieaccount](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Afspraken maken tussen partners in uw integratieaccount](../logic-apps/logic-apps-enterprise-integration-agreements.md)
