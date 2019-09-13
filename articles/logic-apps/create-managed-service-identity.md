---
title: Verifiëren met beheerde identiteiten-Azure Logic Apps
description: Als u zich wilt verifiëren zonder u aan te melden, kunt u een beheerde identiteit (voorheen Managed Service Identity of MSI genoemd) maken zodat uw logische app toegang heeft tot bronnen in andere Azure Active Directory (Azure AD)-tenants zonder referenties of geheimen
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: bb1443afa14f2a23b807af52ab8fef6ac41ea200
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934037"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Verifiëren en toegang krijgen tot resources met beheerde identiteiten in Azure Logic Apps

Om toegang te krijgen tot resources in andere Azure Active Directory (Azure AD)-tenants en uw identiteit te verifiëren zonder zich aan te melden, kan uw logische app gebruikmaken van een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) (voorheen bekend als managed service Identity of MSI), in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te draaien. In dit artikel wordt uitgelegd hoe u een door het systeem toegewezen beheerde identiteit voor uw logische app kunt instellen en gebruiken. Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie over beheerde identiteiten.

> [!NOTE]
> Uw logische app kan beheerde identiteiten alleen gebruiken met connectors die beheerde identiteiten ondersteunen. Op dit moment ondersteunt alleen beheerde identiteiten alleen de HTTP-connector.
>
> In elk Azure-abonnement beschikt u over Maxi maal 10 werk stromen voor logische apps met door het systeem toegewezen beheerde identiteiten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement, of als u geen abonnement hebt, kunt [u zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De logische app waarvoor u de door het systeem toegewezen beheerde identiteit wilt gebruiken. Als u geen logische app hebt, raadpleegt u [uw eerste werk stroom voor logische apps maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Beheerde identiteit inschakelen

Voor door het systeem toegewezen beheerde identiteiten hoeft u deze identiteit niet hand matig te maken. Als u een door het systeem toegewezen beheerde identiteit voor uw logische app wilt instellen, kunt u op de volgende manieren te werk gaat: 

* [Azure-portal](#azure-portal) 
* [Azure Resource Manager sjablonen](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Als u via de Azure Portal een door het systeem toegewezen beheerde identiteit voor uw logische app wilt inschakelen, schakelt u de instelling **toegewezen systeem** in de identiteits instellingen van uw logische app in.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **identiteit**onder **instellingen**in het menu van de logische app.

1. Selecteer **aan**onder **toegewezen** > systeem**status**. Selecteer vervolgens **Opslaan** > **Ja**.

   ![De instelling beheerde identiteit inschakelen](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Uw logische app heeft nu een door het systeem toegewezen beheerde identiteit die is geregistreerd in Azure Active Directory:

   ![GUID'S voor object-ID](./media/create-managed-service-identity/object-id.png)

   | Eigenschap | Waarde | Description |
   |----------|-------|-------------|
   | **Object-ID** | <*identity-resource-ID*> | Een GUID (Globally Unique Identifier) die de door het systeem toegewezen beheerde identiteit voor uw logische app vertegenwoordigt in een Azure AD-Tenant |
   ||||

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Als u het maken en implementeren van Azure-resources, zoals Logic apps, wilt automatiseren, kunt u [Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)gebruiken. Als u via een sjabloon een door het systeem toegewezen beheerde identiteit voor uw logische app wilt maken `"identity"` , voegt `"type"` u het element en de eigenschap toe aan de werk stroom definitie van de logische app in uw implementatie sjabloon: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Bijvoorbeeld:

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
         "actions": {}, 
         "parameters": {}, 
         "triggers": {}, 
         "contentVersion": "1.0.0.0", 
         "outputs": {} 
   }, 
   "parameters": {}, 
   "dependsOn": [] 
}
```

Wanneer Azure uw logische app maakt, bevat deze werk stroom definitie van de logische app de volgende aanvullende eigenschappen:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschap | Waarde | Description |
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Een GUID (Globally Unique Identifier) die de logische app in de Azure AD-Tenant vertegenwoordigt en soms wordt weer gegeven als een ' object-ID ' of`objectID` |
| **tenantId** | <*Azure-AD-tenant-ID*> | Een GUID (Globally Unique Identifier) die de Azure AD-Tenant vertegenwoordigt waarbij de logische app nu lid is. De Service-Principal in de Azure AD-Tenant heeft dezelfde naam als de logische app-instantie. |
||||

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Toegang tot resources met beheerde identiteit

Nadat u een door het systeem toegewezen beheerde identiteit voor uw logische app hebt gemaakt, kunt u [die identiteit toegang geven tot andere Azure-resources](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). U kunt deze identiteit vervolgens gebruiken voor verificatie, net zoals elke andere [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Zowel de door het systeem toegewezen beheerde identiteit als de resource waaraan u toegang wilt toewijzen, moeten hetzelfde Azure-abonnement hebben.

### <a name="assign-access-to-managed-identity"></a>Toegang tot beheerde identiteit toewijzen

Voer de volgende stappen uit om toegang te geven tot een andere Azure-resource voor de door het systeem toegewezen beheerde identiteit van de logische app:

1. Ga in het Azure Portal naar de Azure-resource waaraan u toegang wilt toewijzen voor uw beheerde identiteit.

1. Selecteer **toegangs beheer (IAM)** in het menu van de resource. Kies op de werk balk de optie >  **roltoewijzing toevoegen.**

   ![Roltoewijzing toevoegen](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Onder roltoewijzing **toevoegen**selecteert u de gewenste **rol** voor de identiteit.

1. Selecteer in de eigenschap **toegang toewijzen aan** de optie **Azure AD-gebruiker,-groep of Service-Principal**als dat nog niet is geselecteerd.

1. Typ in het vak **selecteren** , beginnend met het eerste teken in de naam van de logische app, de naam van de logische app. Wanneer uw logische app wordt weer gegeven, selecteert u de logische app.

   ![Logische app met beheerde identiteit selecteren](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Als u bent klaar, kiest u **Opslaan**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Verifiëren met beheerde identiteit in de logische app

Nadat u uw logische app hebt ingesteld met een door het systeem toegewezen beheerde identiteit en toegang hebt toegewezen aan de resource die u voor die identiteit wilt gebruiken, kunt u deze identiteit nu voor verificatie gebruikt. U kunt bijvoorbeeld een HTTP-actie gebruiken zodat uw logische app een HTTP-aanvraag kan verzenden of aanroepen naar die resource. 

1. Voeg in uw logische app de **http-** actie toe.

1. Geef de benodigde gegevens voor die actie op, zoals de aanvraag **methode** en **URI** -locatie voor de resource die u wilt aanroepen.

   Stel dat u gebruikmaakt van Azure Active Directory-verificatie (Azure AD) met [een van deze Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Voer in het vak **URI** de eind punt-URL voor de Azure-service in. Als u Azure Resource Manager gebruikt, voert u deze waarde in de **URI** -eigenschap in:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. Kies in de actie HTTP de optie **Geavanceerde opties weer geven**.

1. Selecteer in de lijst **verificatie** de optie **beheerde identiteit**. Nadat u deze verificatie hebt geselecteerd, wordt de eigenschap **doel groep** weer gegeven met de standaard waarde voor de resource-id:

   ![Beheerde identiteit selecteren](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > In de eigenschap **doel groep** moet de resource-id-waarde exact overeenkomen met wat Azure AD verwacht, inclusief alle vereiste afsluitende slashes. 
   > U vindt deze resource-ID-waarden in deze [tabel met een beschrijving van de Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Als u bijvoorbeeld de resource-ID van Azure Resource Manager gebruikt, moet u ervoor zorgen dat de URI een afsluitende slash heeft.

1. Ga door met het bouwen van de logische app op de gewenste manier.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Beheerde identiteit verwijderen

Als u een door het systeem toegewezen beheerde identiteit wilt uitschakelen voor uw logische app, kunt u de stappen volgen die vergelijkbaar zijn met de manier waarop u de identiteit instelt via de Azure Portal, Azure Resource Manager implementatie sjablonen of Azure PowerShell.

Wanneer u uw logische app verwijdert, verwijdert Azure automatisch de door het systeem toegewezen identiteit van de logische app uit Azure AD.

### <a name="azure-portal"></a>Azure Portal

Als u een door het systeem toegewezen beheerde identiteit voor uw logische app wilt verwijderen via de Azure Portal, moet u de instelling **systeem toegewezen** uitschakelen in de identiteits instellingen van uw logische app.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **identiteit**onder **instellingen**in het menu van de logische app.

1. Kies **uit**onder **toegewezen** > systeem**status**. Kies vervolgens **Opslaan** > **Ja**.

   ![Instelling voor beheerde identiteit uitschakelen](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Implementatiesjabloon

Als u de door het systeem toegewezen beheerde identiteit van de logische app met een Azure Resource Manager-implementatie sjabloon hebt `"identity"` gemaakt, `"type"` stelt u `"None"`de eigenschap van het element in op. Met deze actie wordt ook de principal-ID uit Azure AD verwijderd.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)