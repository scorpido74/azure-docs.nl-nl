---
title: Verifiëren met beheerde identiteiten
description: Toegang tot bronnen in andere Azure Active Directory-tenants zonder u aan te melden met referenties of geheimen met behulp van een beheerde identiteit
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117358"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Toegang tot Azure-bronnen verifiëren met beheerde identiteiten in Azure Logic Apps

Als u toegang wilt krijgen tot resources in andere Azure Active Directory-tenants (Azure AD) en uw identiteit wilt verifiëren zonder u aan te melden, kan uw logische app een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) (voorheen Managed Service Identity of MSI) gebruiken, in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt uw referenties te beveiligen omdat u geen geheimen hoeft op te geven of te roteren.

Azure Logic Apps ondersteunt zowel [*door het systeem toegewezen*](../active-directory/managed-identities-azure-resources/overview.md) als door de gebruiker [*toegewezen*](../active-directory/managed-identities-azure-resources/overview.md) beheerde identiteiten. Uw logische app kan de door het systeem toegewezen identiteit of *een enkele* door de gebruiker toegewezen identiteit gebruiken, die u delen in een groep logische apps, maar niet beide. Momenteel ondersteunen alleen [specifieke ingebouwde triggers en acties](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) beheerde identiteiten, niet beheerde connectors of verbindingen, bijvoorbeeld:

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

In dit artikel ziet u hoe u beide soorten beheerde identiteiten instelt voor uw logische app. Zie deze onderwerpen voor meer informatie:

* [Triggers en acties die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Ondersteunde verificatietypen bij uitgaande gesprekken](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Beheerde identiteitslimieten voor logische apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-services die Azure AD-verificatie ondersteunen met beheerde identiteiten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). Zowel de beheerde identiteit als de beoogde Azure-bron waar u toegang nodig hebt, moeten hetzelfde Azure-abonnement gebruiken.

* Als u een beheerde identiteit toegang wilt geven tot een Azure-bron, moet u een rol toevoegen aan de doelbron voor die identiteit. Als u rollen wilt toevoegen, hebt u [Azure AD-beheerdersmachtigingen](../active-directory/users-groups-roles/directory-assign-admin-roles.md) nodig die rollen kunnen toewijzen aan identiteiten in de bijbehorende Azure AD-tenant.

* De beoogde Azure-bron die u wilt openen. Op deze bron voegt u een rol toe voor de beheerde identiteit, waarmee de logische app de toegang tot de doelbron kan verifiëren.

* De logische app waarin u de trigger of acties wilt gebruiken [die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Beheerde identiteit inschakelen

Als u de beheerde identiteit wilt instellen die u wilt gebruiken, volgt u de koppeling voor die identiteit:

* [Systeemtoegewezen identiteit](#system-assigned)
* [Door de gebruiker toegewezen identiteit](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Systeemtoegewezen identiteit inschakelen

In tegenstelling tot door de gebruiker toegewezen identiteiten, hoeft u de door het systeem toegewezen identiteit niet handmatig te maken. Als u de door het systeem toegewezen identiteit voor uw logische app wilt instellen, u het gewenste aantal opties gebruiken:

* [Azure-portal](#azure-portal-system-logic-app)
* [Azure Resource Manager-sjablonen](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Systeemtoegewezen identiteit inschakelen in Azure-portal

1. Open uw logische app in de [Azure-portal](https://portal.azure.com)in Logic App Designer.

1. Selecteer In het menu van de logische app onder **Instellingen**de optie **Identiteit**. Selecteer **Systeem toegewezen** > **op** > **opslaan**. Wanneer Azure u vraagt om te bevestigen, selecteert u **Ja**.

   ![De aan het systeem toegewezen identiteit inschakelen](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Als u een foutmelding krijgt dat u slechts één beheerde identiteit hebben, is uw logische app al gekoppeld aan de door de gebruiker toegewezen identiteit. Voordat u de door het systeem toegewezen identiteit toevoegen, moet u eerst de door de gebruiker toegewezen identiteit uit uw logische app *verwijderen.*

   Uw logische app kan nu de door het systeem toegewezen identiteit gebruiken, die is geregistreerd bij Azure Active Directory en wordt vertegenwoordigd door een object-id.

   ![Object-id voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Object-id** | <*identiteits-resource-ID*> | Een GUID (Globally Unique Identifier) die de door het systeem toegewezen identiteit voor uw logische app in een Azure AD-tenant vertegenwoordigt |
   ||||

1. Volg nu de stappen die die identiteit later in dit onderwerp [toegang geven tot de bron.](#access-other-resources)

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Systeemtoegewezen identiteit inschakelen in Azure Resource Manager-sjabloon

Als u het maken en implementeren van Azure-bronnen zoals logische apps wilt automatiseren, u [Azure Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)gebruiken. Als u de door het systeem toegewezen beheerde identiteit `identity` voor `type` uw logische app in de sjabloon wilt inschakelen, voegt u het object en de eigenschap onderliggend toe aan de resourcedefinitie van de logische app in de sjabloon, bijvoorbeeld:

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

Wanneer Azure de definitie van `identity` uw logische app-bron maakt, krijgt het object de volgende extra eigenschappen:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschap (JSON) | Waarde | Beschrijving |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | De GUID (Globally Unique Identifier) van het hoofdobject van de service voor de beheerde identiteit die uw logische app in de Azure AD-tenant vertegenwoordigt. Deze GUID wordt soms weergegeven als `objectID`een "object-ID" of . |
| `tenantId` | <*Azure-AD-tenant-ID*> | De GUID (Globally Unique Identifier) die de Azure AD-tenant vertegenwoordigt waar de logische app nu lid van is. Binnen de Azure AD-tenant heeft de serviceprincipal dezelfde naam als de instantie logische app. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Door de gebruiker toegewezen identiteit inschakelen

Als u een door de gebruiker toegewezen beheerde identiteit voor uw logische app wilt instellen, moet u die identiteit eerst maken als een afzonderlijke zelfstandige Azure-bron. Dit zijn de opties die u gebruiken:

* [Azure-portal](#azure-portal-user-identity)
* [Azure Resource Manager-sjablonen](#template-user-identity)
* Azure PowerShell
  * [Identiteit die door de gebruiker is toegewezen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Roltoewijzing toevoegen](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure-CLI
  * [Identiteit die door de gebruiker is toegewezen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Roltoewijzing toevoegen](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Identiteit die door de gebruiker is toegewezen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Roltoewijzing toevoegen](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Identiteit met gebruikers toegewezen in de Azure-portal

1. Voer in de [Azure-portal](https://portal.azure.com)in het `managed identities`zoekvak op een pagina **Beheerde identiteiten in**en selecteer deze .

   ![Zoeken en selecteren "Beheerde identiteiten"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Selecteer Onder **Beheerde identiteiten**de optie **Toevoegen**.

   ![Nieuwe beheerde identiteit toevoegen](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Geef informatie op over uw beheerde identiteit en selecteer vervolgens **Maken,** bijvoorbeeld:

   ![Beheerde identiteit met gebruiker maken](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Resourcenaam** | Ja | <*gebruikersnaam toegewezen-identiteit*> | De naam om uw door de gebruiker toegewezen identiteit te geven. In dit voorbeeld wordt gebruik gemaakt van "Fabrikam-user-assigned-identity". |
   | **Abonnement** | Ja | <*Azure-abonnementsnaam*> | De naam voor het Azure-abonnement dat moet worden gebruikt |
   | **Resourcegroep** | Ja | <*Naam azure-resource-groep*> | De naam voor de resourcegroep die u wilt gebruiken. Maak een nieuwe groep of selecteer een bestaande groep. In dit voorbeeld wordt een nieuwe groep gemaakt met de naam "fabrikam-managed-identities-RG". |
   | **Locatie** | Ja | <*Azure-regio*> | Het Azure-gebied waar informatie over uw bron kan worden opgeslagen. In dit voorbeeld wordt "West US" gebruikt. |
   |||||

   Nu u de door de gebruiker toegewezen identiteit toevoegen aan uw logische app. U niet meer dan één door de gebruiker toegewezen identiteit toevoegen aan uw logische app.

1. Zoek en open uw logische app in de Azure-portal in Logic App Designer.

1. Selecteer in het menu van de logische app onder **Instellingen**de optie **Identiteit**en selecteer **Vervolgens Gebruiker toegewezen** > **Toevoegen**.

   ![Beheerde identiteit met gebruiker toevoegen](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Selecteer in het deelvenster **Beheerde identiteit toevoegen van gebruikers** in de lijst **Abonnement** uw Azure-abonnement als dit nog niet is geselecteerd. Zoek en selecteer in de lijst met *alle* beheerde identiteiten in dat abonnement de gewenste identiteit die door de gebruiker is toegewezen. Als u de lijst wilt filteren, voert u in het zoekvak Beheerde identiteit en de naam voor de identiteit of resourcegroep in in het zoekvak Met **gebruikers toegewezen beheerde identiteit.** Als u klaar bent, selecteert u **Toevoegen**.

   ![De door de gebruiker toegewezen identiteit selecteren die moet worden gebruikt](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Als u een foutmelding krijgt dat u slechts één beheerde identiteit hebben, is uw logische app al gekoppeld aan de door het systeem toegewezen identiteit. Voordat u de door de gebruiker toegewezen identiteit toevoegen, moet u eerst de door het systeem toegewezen identiteit uitschakelen in uw logische app.

   Uw logische app is nu gekoppeld aan de door de gebruiker toegewezen beheerde identiteit.

   ![Koppeling met door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Volg nu de stappen die die identiteit later in dit onderwerp [toegang geven tot de bron.](#access-other-resources)

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Identiteit met gebruikers toegewezen in een Azure Resource Manager-sjabloon

Als u het maken en implementeren van Azure-bronnen zoals logische apps wilt automatiseren, u [Azure Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)gebruiken die [door gebruikers toegewezen identiteiten ondersteunen voor verificatie.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) In de sectie `resources` van uw sjabloon vereist de resourcedefinitie van uw logische app de volgende items:

* Een `identity` object `type` waarop de eigenschap is ingesteld`UserAssigned`

* Een `userAssignedIdentities` onderliggend object dat de resource-id van de identiteit `principalId` opgeeft, een ander onderliggend object met de eigenschappen en `clientId` eigenschappen

In dit voorbeeld wordt een logische app-brondefinitie voor een `identity` HTTP PUT-aanvraag weergegeven en een niet-geparameteriseerd object. Het antwoord op de PUT-aanvraag en `identity` de daaropvolgende GET-bewerking hebben ook dit object:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Eigenschap (JSON) | Waarde | Beschrijving |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | De GUID (Globally Unique Identifier) voor de door de gebruiker toegewezen beheerde identiteit in de Azure AD-tenant |
| `clientId` | <*client-ID*> | Een GUID (Globally Unique Identifier) voor de nieuwe identiteit van uw logische app die wordt gebruikt voor gesprekken tijdens runtime |
||||

Als uw sjabloon ook de resourcedefinitie van de beheerde `identity` identiteit bevat, u het object parameteriseren. In dit voorbeeld `userAssignedIdentities` ziet u `userAssignedIdentity` hoe het onderliggende object `variables` verwijst naar een variabele die u definieert in de sectie van uw sjabloon. Deze variabele verwijst naar de resource-id voor uw door de gebruiker toegewezen identiteit.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Eigenschap (JSON) | Waarde | Beschrijving |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-tenant-ID*> | De GUID (Globally Unique Identifier) die de Azure AD-tenant vertegenwoordigt waarvan de door de gebruiker toegewezen identiteit nu lid is. Binnen de Azure AD-tenant heeft de serviceprincipal dezelfde naam als de door de gebruiker toegewezen identiteitsnaam. |
| `principalId` | <*principal-ID*> | De GUID (Globally Unique Identifier) voor de door de gebruiker toegewezen beheerde identiteit in de Azure AD-tenant |
| `clientId` | <*client-ID*> | Een GUID (Globally Unique Identifier) voor de nieuwe identiteit van uw logische app die wordt gebruikt voor gesprekken tijdens runtime |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Identiteitstoegang geven tot bronnen

Voordat u de beheerde identiteit van uw logische app gebruiken voor verificatie, stelt u toegang voor die identiteit in op de Azure-bron waar u de identiteit wilt gebruiken. Als u deze taak wilt voltooien, wijst u de juiste rol toe aan die identiteit op de doelazure-bron. Dit zijn de opties die u gebruiken:

* [Azure-portal](#azure-portal-assign-access)
* [Azure Resource Manager-sjabloon](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - Zie [Roltoewijzing toevoegen met Azure RBAC en Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)voor meer informatie.
* Azure CLI ([az-roltoewijzing maken](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - Zie [Roltoewijzing toevoegen met Azure RBAC en Azure CLI](../role-based-access-control/role-assignments-cli.md)voor meer informatie.
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Toegang toewijzen in de Azure-portal

1. Ga in de [Azure-portal](https://portal.azure.com)naar de Azure-bron waar u wilt dat uw beheerde identiteit toegang heeft.

1. Selecteer in het menu van de resource de**roltoewijzingen** **(Access control)** > waar u de huidige roltoewijzingen voor die resource bekijken. Selecteer op de werkbalk **Roltoewijzing** > **toevoegen**.

   ![Selecteer 'Toevoegen' > 'Roltoewijzing toevoegen'](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Als de optie **Roltoewijzing toevoegen** is uitgeschakeld, hebt u waarschijnlijk geen machtigingen. Zie [Beheerdersrolmachtigingen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de machtigingen waarmee u rollen voor resources beheren.

1. Selecteer **onder Roltoewijzing toevoegen**een **rol** die uw identiteit de nodige toegang geeft tot de doelbron.

   In het voorbeeld van dit onderwerp heeft uw identiteit een rol nodig [die toegang heeft tot de blob in een Azure Storage-container.](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![De rol 'Opslagblob-gegevensbijdrager' selecteren](./media/create-managed-service-identity/select-role-for-identity.png)

1. Volg de volgende stappen voor uw beheerde identiteit:

   * **Systeemtoegewezen identiteit**

     1. Selecteer **Logic App**in het vak Toegang **toewijzen tot** . Wanneer de eigenschap **Abonnement** wordt weergegeven, selecteert u het Azure-abonnement dat is gekoppeld aan uw identiteit.

        ![Toegang selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/assign-access-system.png)

     1. Selecteer onder het vak **Selecteren** de optie uw logische app in de lijst. Als de lijst te lang is, gebruikt u het vak **Selecteren** om de lijst te filteren.

        ![Logische app selecteren voor aan het systeem toegewezen identiteit](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Door de gebruiker toegewezen identiteit**

     1. Selecteer **in** het vak Toegang toewijzen tot de **optie Door gebruiker toegewezen beheerde identiteit**. Wanneer de eigenschap **Abonnement** wordt weergegeven, selecteert u het Azure-abonnement dat is gekoppeld aan uw identiteit.

        ![Toegang selecteren voor door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/assign-access-user.png)

     1. Selecteer onder het vak **Selecteren** uw identiteit in de lijst. Als de lijst te lang is, gebruikt u het vak **Selecteren** om de lijst te filteren.

        ![Selecteer uw door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Selecteer **Opslaan** als u klaar bent.

   De lijst met roltoewijzingen van de doelbron toont nu de geselecteerde beheerde identiteit en rol. In dit voorbeeld ziet u hoe u de door het systeem toegewezen identiteit gebruiken voor één logische app en een door de gebruiker toegewezen identiteit voor een groep andere logische apps.

   ![Beheerde identiteiten en rollen toegevoegd aan doelbron](./media/create-managed-service-identity/added-roles-for-identities.png)

   Voor meer informatie [wijst u een beheerde identiteitstoegang toe tot een bron met behulp van de Azure-portal.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

1. Volg nu de [stappen om toegang met de identiteit te verifiëren](#authenticate-access-with-identity) in een trigger of actie die beheerde identiteiten ondersteunt.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Toegang verifiëren met beheerde identiteit

Nadat u [de beheerde identiteit van uw logische app](#azure-portal-system-logic-app) hebt ingeschakeld en die identiteit toegang hebt geven tot de [doelbron of -entiteit,](#access-other-resources)u die identiteit gebruiken in [triggers en acties die beheerde identiteiten ondersteunen.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Als u een Azure-functie hebt waar u de door het systeem toegewezen identiteit wilt gebruiken, [schakelt u eerst verificatie in voor Azure-functies.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

In deze stappen wordt uitgelegd hoe u de beheerde identiteit gebruiken met een trigger of actie via de Azure-portal. Zie [Beheerde identiteitsverificatie](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)als u de beheerde identiteit wilt opgeven in de onderliggende JSON-definitie van een trigger of actie.

1. Open uw logische app in de Logische App Designer in de [Azure-portal.](https://portal.azure.com)

1. Als u dit nog niet hebt gedaan, voegt u de trigger of actie toe [die beheerde identiteiten ondersteunt.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

   De HTTP-trigger of -actie kan bijvoorbeeld de door het systeem toegewezen identiteit gebruiken die u hebt ingeschakeld voor uw logische app. In het algemeen gebruikt de HTTP-trigger of actie deze eigenschappen om de bron of entiteit op te geven die u wilt openen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Methode** | Ja | De HTTP-methode die wordt gebruikt door de bewerking die u wilt uitvoeren |
   | **Uri** | Ja | De URL van het eindpunt voor toegang tot de doelbron of -entiteit. De syntaxis van URI bevat meestal de [resource-id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) voor de Azure-bron of -service. |
   | **Headers** | Nee | Kopwaarden die u nodig hebt of wilt opnemen in de uitgaande aanvraag, zoals het inhoudstype |
   | **Query's** | Nee | Alle queryparameters die u in de aanvraag nodig hebt of wilt opnemen, zoals de parameter voor een specifieke bewerking of de API-versie voor de bewerking die u wilt uitvoeren |
   | **Verificatie** | Ja | Het verificatietype dat moet worden gebruikt voor het verifiëren van toegang tot de doelbron of -entiteit |
   ||||

   Stel dat u in het specifieke voorbeeld de [bewerking Momentopname blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) wilt uitvoeren op een blob in het Azure Storage-account waar u eerder toegang voor uw identiteit hebt ingesteld. De [Azure Blob Storage-connector](https://docs.microsoft.com/connectors/azureblob/) biedt deze bewerking momenteel echter niet. In plaats daarvan u deze bewerking uitvoeren met de [HTTP-actie](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) of een andere [Blob Service REST API-bewerking](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Als u toegang wilt krijgen tot Azure-opslagaccounts achter firewalls met HTTP-aanvragen en beheerde identiteiten, moet u ervoor zorgen dat u ook uw opslagaccount instelt met uitzondering [waarmee toegang wordt verkrijgd door vertrouwde Microsoft-services.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

   Als u de [bewerking Momentopnameblob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)wilt uitvoeren, geeft de HTTP-actie de volgende eigenschappen op:

   | Eigenschap | Vereist | Voorbeeldwaarde | Beschrijving |
   |----------|----------|---------------|-------------|
   | **Methode** | Ja | `PUT`| De HTTP-methode die de opnameblobbewerking gebruikt |
   | **Uri** | Ja | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | De bron-id voor een Azure Blob Storage-bestand in de Azure Global -omgeving (openbare) die deze syntaxis gebruikt |
   | **Headers** | Ja, voor Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | De `x-ms-blob-type` `x-ms-version` kopwaarden en kopwaarden die nodig zijn voor Azure Storage-bewerkingen. <p><p>**Belangrijk:** in uitgaande HTTP-trigger- en actieaanvragen `x-ms-version` voor Azure Storage vereist de header de eigenschap en de API-versie voor de bewerking die u wilt uitvoeren. <p>Zie deze onderwerpen voor meer informatie: <p><p>- [Kopteksten aanvragen - Momentopname Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Versiebeheer voor Azure Storage-services](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query's** | Ja, voor deze bewerking | `comp` = `snapshot` | De naam en de waarde van de queryparameter voor de bewerking Momentopnameblob. |
   |||||

   Hier is het voorbeeld HTTP-actie die al deze eigenschapswaarden weergeeft:

   ![Een HTTP-actie toevoegen om toegang te krijgen tot een Azure-bron](./media/create-managed-service-identity/http-action-example.png)

1. Voeg nu de eigenschap **Verificatie** toe aan de HTTP-actie. Selecteer **Verificatie**in de lijst **Nieuwe parameter toevoegen** .

   ![Eigenschap 'Verificatie' toevoegen aan HTTP-actie](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Niet alle triggers en acties ondersteunen waarmee u een verificatietype toevoegen. Zie [Verificatie toevoegen aan uitgaande gesprekken voor](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)meer informatie .

1. Selecteer **Beheerde identiteit**in de lijst **Met verificatietype** .

   ![Selecteer voor 'Verificatie' de optie 'Beheerde identiteit'](./media/create-managed-service-identity/select-managed-identity.png)

1. Kies in de lijst met beheerde identiteit de beschikbare opties op basis van uw scenario.

   * Als u de door het systeem toegewezen identiteit instelt, selecteert u **System Assigned Managed Identity** als deze nog niet is geselecteerd.

     ![Selecteer 'Beheerde identiteit met systeemtoegewezen'](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Als u een door de gebruiker toegewezen identiteit instelt, selecteert u die identiteit als deze nog niet is geselecteerd.

     ![De door de gebruiker toegewezen identiteit selecteren](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Dit voorbeeld gaat verder met de **beheerde identiteit met systeemtoegewezen identiteit**.

1. Bij sommige triggers en acties wordt de eigenschap **Doelgroep** ook weergegeven voor het instellen van de doelbron-id. Stel de eigenschap **Doelgroep** in op de [resource-id voor de doelbron of -service](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Anders gebruikt de eigenschap **Audience** `https://management.azure.com/` standaard de resource-id, de bron-id voor Azure Resource Manager.

   > [!IMPORTANT]
   > Zorg ervoor dat de doelbron-id *exact overeenkomt met* de waarde die Azure Active Directory (AD) verwacht, inclusief eventuele vereiste trailing slashes. De resource-id voor alle Azure Blob Storage-accounts vereist bijvoorbeeld een trailing slash. De resource-id voor een specifiek opslagaccount vereist echter geen trailing slash. Controleer de [bron-id's voor de Azure-services die Azure AD ondersteunen.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   In dit **Audience** voorbeeld wordt `https://storage.azure.com/` de eigenschap Doelgroep ingesteld op de toegang, zodat de toegangstokens die worden gebruikt voor verificatie geldig zijn voor alle opslagaccounts. U echter ook de URL `https://fabrikamstorageaccount.blob.core.windows.net`van de hoofdservice opgeven voor een specifiek opslagaccount.

   ![Eigenschap Doelgroep instellen op doelbron-id](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Zie de volgende onderwerpen voor meer informatie over het toestaan van toegang met Azure AD voor Azure Storage:

   * [Toegang tot Azure-blobs en wachtrijen autoriseren met Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Toegang tot Azure Storage autoriseren met Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Ga door met het bouwen van de logische app zoals u dat wilt.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Beheerde identiteit uitschakelen

Als u wilt stoppen met het gebruik van een beheerde identiteit voor uw logische app, hebt u de volgende opties:

* [Azure-portal](#azure-portal-disable)
* [Azure Resource Manager-sjablonen](#template-disable)
* Azure PowerShell
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-powershell.md)
  * [Identiteit met gebruiker verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure-CLI
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-cli.md)
  * [Identiteit met gebruiker verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-rest.md)
  * [Identiteit met gebruiker verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Als u uw logische app verwijdert, verwijdert Azure automatisch de beheerde identiteit uit Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Beheerde identiteit uitschakelen in de Azure-portal

Verwijder in de Azure-portal eerst de toegang van de identiteit tot [uw doelbron.](#disable-identity-target-resource) Schakel vervolgens de door het systeem toegewezen identiteit uit of verwijder de door de gebruiker toegewezen identiteit uit [uw logische app.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identiteitstoegang uit bronnen verwijderen

1. Ga in de [Azure-portal](https://portal.azure.com)naar de doelbron azure waar u de toegang voor de beheerde identiteit wilt verwijderen.

1. Selecteer **access control (IAM)** in het menu van de doelbron. Selecteer onder de werkbalk **Roltoewijzingen**.

1. Selecteer in de lijst met rollen de beheerde identiteiten die u wilt verwijderen. Selecteer op de werkbalk **Verwijderen**.

   > [!TIP]
   > Als de optie **Verwijderen** is uitgeschakeld, hebt u waarschijnlijk geen machtigingen. Zie [Beheerdersrolmachtigingen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de machtigingen waarmee u rollen voor resources beheren.

De beheerde identiteit wordt nu verwijderd en heeft geen toegang meer tot de doelbron.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Beheerde identiteit uitschakelen in de logische app

1. Open uw logische app in de [Azure-portal](https://portal.azure.com)in Logic App Designer.

1. Selecteer in het menu van de logische app onder **Instellingen**de optie **Identiteit**en volg de stappen voor uw identiteit:

   * Selecteer **Systeem toegewezen** > **op** > **opslaan**. Wanneer Azure u vraagt om te bevestigen, selecteert u **Ja**.

     ![De door het systeem toegewezen identiteit uitschakelen](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecteer **Toegewezen gebruiker** en de beheerde identiteit en selecteer **Verwijderen**. Wanneer Azure u vraagt om te bevestigen, selecteert u **Ja**.

     ![De door de gebruiker toegewezen identiteit verwijderen](./media/create-managed-service-identity/remove-user-assigned-identity.png)

De beheerde identiteit is nu uitgeschakeld in uw logische app.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Beheerde identiteit uitschakelen in azure resource manager-sjabloon

Als u de beheerde identiteit van de logische app hebt `identity` gemaakt `type` met behulp `None`van een Azure Resource Manager-sjabloon, stelt u de onderliggende eigenschap van het object in op . Voor de door het systeem beheerde identiteit wordt met deze actie ook de hoofd-id uit Azure AD verwijderd.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)