---
title: Verifiëren met beheerde identiteiten
description: Toegang krijgen tot bronnen in andere Azure Active Directory-tenants zonder u aan te melden met referenties of geheimen met behulp van een beheerde identiteit
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 49c925cfe61084d8fedfdf953d469db4bd2c10b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792669"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Toegang tot Azure-bronnen verifiëren door beheerde identiteiten te gebruiken in Azure Logic Apps

Om toegang te krijgen tot de resources in andere Azure Active Directory (Azure AD)-tenants en uw identiteit te verifiëren zonder zich aan te melden, kan uw logische app gebruikmaken van de door het systeem toegewezen [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) (voorheen bekend als managed service Identity of MSI), in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te draaien. In dit artikel wordt beschreven hoe u de door het systeem toegewezen beheerde identiteit kunt instellen en gebruiken in uw logische app. Beheerde identiteiten werken momenteel alleen met [specifieke ingebouwde triggers en acties](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls), niet beheerde connectors of verbindingen.

Zie de volgende onderwerpen voor meer informatie:

* [Triggers en acties die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Azure-Services die ondersteuning bieden voor Azure AD-verificatie met beheerde identiteiten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Ondersteunde verificatie typen voor uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Beheerde identiteits limieten voor Logic apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement, of als u geen abonnement hebt, kunt [u zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/). Zowel de beheerde identiteit als de doel-Azure-resource waar u wilt dat Access hetzelfde Azure-abonnement moet gebruiken.

* [Azure AD-beheerders machtigingen](../active-directory/users-groups-roles/directory-assign-admin-roles.md) waarmee rollen kunnen worden toegewezen aan beheerde identiteiten in dezelfde Azure AD-Tenant als de doel resource. Als u een beheerde identiteit toegang tot een Azure-resource wilt geven, moet u een rol voor die identiteit toevoegen op de doel resource.

* De doel-Azure-resource waartoe u toegang wilt krijgen

* Een logische app die gebruikmaakt van [Triggers en acties die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Door het systeem toegewezen identiteit inschakelen

In tegens telling tot door de gebruiker toegewezen identiteiten hoeft u de door het systeem toegewezen identiteit niet hand matig te maken. Als u de door het systeem toegewezen identiteit van de logische app wilt instellen, kunt u de volgende opties gebruiken:

* [Azure-portal](#azure-portal-system-logic-app)
* [Azure Resource Manager sjablonen](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Door het systeem toegewezen identiteit inschakelen in Azure Portal

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer onder **instellingen**in het menu van de logische app de optie **identiteit** > **systeem is toegewezen**. Selecteer onder **status**de optie **aan** >  > **Ja**wilt **Opslaan** .

   ![De door het systeem toegewezen identiteit inschakelen](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Uw logische app kan nu gebruikmaken van de door het systeem toegewezen identiteit, die is geregistreerd bij Azure Active Directory en wordt vertegenwoordigd door een object-ID.

   ![Object-ID voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/object-id.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Object-ID** | <*identiteit-Resource-ID*> | Een GUID (Globally Unique Identifier) die de door het systeem toegewezen identiteit voor uw logische app vertegenwoordigt in uw Azure AD-Tenant |
   ||||

1. Volg nu de [stappen waarmee de identiteit toegang krijgt tot de resource](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Door het systeem toegewezen identiteit in Azure Resource Manager sjabloon inschakelen

U kunt [Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)gebruiken om het maken en implementeren van Azure-resources zoals Logic apps te automatiseren. Als u de door het systeem toegewezen beheerde identiteit voor uw logische app in de sjabloon wilt inschakelen, voegt u het `identity`-object en de `type` onderliggende eigenschap toe aan de resource definitie van de logische app in de sjabloon, bijvoorbeeld:

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

Wanneer Azure de resource definitie van de logische app maakt, haalt het `identity`-object deze aanvullende eigenschappen op:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschap (JSON) | Waarde | Beschrijving |
|-----------------|-------|-------------|
| `principalId` | <*Principal-ID*> | De GUID (Globally Unique Identifier) van het Service-Principal-object voor de beheerde identiteit die uw logische app vertegenwoordigt in de Azure AD-Tenant. Deze GUID wordt soms weer gegeven als een ' object-ID ' of `objectID`. |
| `tenantId` | <*Azure-AD-Tenant-ID*> | De GUID (Globally Unique Identifier) die de Azure AD-Tenant vertegenwoordigt waarbij de logische app nu lid is. De Service-Principal in de Azure AD-Tenant heeft dezelfde naam als de logische app-instantie. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Identiteits toegang geven tot resources

Nadat u een beheerde identiteit hebt ingesteld voor uw logische app, kunt u [die identiteit toegang geven tot andere Azure-resources](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). U kunt deze identiteit vervolgens gebruiken voor verificatie.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de Azure-resource waar u de beheerde identiteit toegang wilt geven.

1. Selecteer in het menu resource **toegangs beheer (IAM)**  > **roltoewijzingen** , waar u de huidige roltoewijzingen voor die resource kunt controleren. Selecteer op de werk balk > **functie toewijzing** **toevoegen.**

   ![Selecteer > functie toewijzing toevoegen.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Als de optie **roltoewijzing toevoegen** is uitgeschakeld, hebt u waarschijnlijk niet de juiste machtigingen. Zie [Administrator role permissions](../active-directory/users-groups-roles/directory-assign-admin-roles.md)(Engelstalig) in azure Active Directory voor meer informatie over de machtigingen waarmee u rollen voor resources kunt beheren.

1. Selecteer onder roltoewijzing **toevoegen**een **rol** die uw identiteit de benodigde toegang tot de doel resource geeft.

   Voor het voor beeld van dit onderwerp moet uw identiteit een [rol hebben die toegang heeft tot de BLOB in een Azure storage container](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Selecteer de rol ' BLOB data Inzender voor opslag '](./media/create-managed-service-identity/assign-role.png)

1. In het vak **toegang toewijzen aan** selecteert u **Azure AD-gebruiker,-groep of Service-Principal**.

   ![Toegang selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/assign-access-system.png)

1. Zoek en selecteer uw logische app in het vak **selecteren** .

   ![Logische app selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Selecteer **Opslaan** als u klaar bent.

   De lijst met roltoewijzingen van de doel resource bevat nu de geselecteerde beheerde identiteit en rol.

   ![Beheerde identiteiten en rollen toegevoegd aan doel bron](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Volg nu de [stappen om toegang te verifiëren met de identiteit](#authenticate-access-with-identity) in een trigger of actie die beheerde identiteiten ondersteunt.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Toegang verifiëren met beheerde identiteit

Nadat u [de beheerde identiteit voor uw logische app hebt ingeschakeld](#azure-portal-system-logic-app) en [deze identiteit toegang geeft tot de doel resource of entiteit](#access-other-resources), kunt u die identiteit gebruiken in [Triggers en acties die beheerde identiteiten ondersteunen](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Als u een Azure-functie hebt waarbij u de door het systeem toegewezen identiteit wilt gebruiken, moet u eerst [verificatie voor Azure functions inschakelen](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Deze stappen laten zien hoe u de beheerde identiteit met een trigger of actie kunt gebruiken via de Azure Portal. Zie [beheerde identiteits verificatie](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)voor het opgeven van de beheerde identiteit in een trigger of de onderliggende JSON-definitie van een actie.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Als u dit nog niet hebt gedaan, voegt u de [trigger of actie toe die beheerde identiteiten ondersteunt](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   De HTTP-trigger of actie kan bijvoorbeeld de door het systeem toegewezen identiteit gebruiken die u hebt ingeschakeld voor uw logische app. Over het algemeen gebruikt de HTTP-trigger of actie deze eigenschappen om de resource of entiteit op te geven waartoe u toegang wilt krijgen:

   | Eigenschap | Verplicht | Beschrijving |
   |----------|----------|-------------|
   | **Methode** | Ja | De HTTP-methode die wordt gebruikt door de bewerking die u wilt uitvoeren |
   | **URI** | Ja | De eind punt-URL voor toegang tot de Azure-doel bron of-entiteit. De URI-syntaxis bevat doorgaans de [resource-id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) voor de Azure-resource of-service. |
   | **Headers** | Nee | Eventuele header waarden die u nodig hebt of wilt toevoegen in de uitgaande aanvraag, zoals het inhouds type |
   | **Query's** | Nee | Alle query parameters die u nodig hebt of wilt toevoegen in de aanvraag, zoals de para meter voor een specifieke bewerking of de API-versie voor de bewerking die u wilt uitvoeren |
   | **Verificatie** | Ja | Het verificatie type dat moet worden gebruikt voor het verifiëren van de toegang tot de doel bron of entiteit |
   ||||

   Stel dat u de bewerking voor het maken van een [moment opname-BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) wilt uitvoeren op een BLOB in het Azure Storage account waar u eerder toegang hebt ingesteld voor uw identiteit. De [Azure Blob Storage-connector](https://docs.microsoft.com/connectors/azureblob/) biedt deze bewerking echter momenteel niet. In plaats daarvan kunt u deze bewerking uitvoeren met behulp van de [http-actie](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) of een andere [Blob-service rest API bewerking](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Om toegang te krijgen tot Azure Storage-accounts achter firewalls met behulp van HTTP-aanvragen en beheerde identiteiten, moet u ervoor zorgen dat u ook uw opslag account hebt ingesteld met de [uitzonde ring die toegang verleent aan vertrouwde micro soft-Services](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Als u de [BLOB-bewerking van de moment opname](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)wilt uitvoeren, geeft de http-actie deze eigenschappen op:

   | Eigenschap | Verplicht | Voorbeeldwaarde | Beschrijving |
   |----------|----------|---------------|-------------|
   | **Methode** | Ja | `PUT`| De HTTP-methode die wordt gebruikt door de BLOB-bewerking van de moment opname |
   | **URI** | Ja | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | De resource-ID voor een Azure Blob Storage-bestand in de Azure Global (open bare) omgeving, die gebruikmaakt van deze syntaxis |
   | **Headers** | Ja, voor Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | De waarden voor de `x-ms-blob-type`-en `x-ms-version`-header die vereist zijn voor Azure Storage bewerkingen. <p><p>**Belang rijk**: in uitgaande HTTP-trigger-en actie aanvragen voor Azure Storage vereist de header de `x-ms-version`-eigenschap en de API-versie voor de bewerking die u wilt uitvoeren. <p>Zie de volgende onderwerpen voor meer informatie: <p><p>Aanvraag headers voor de - [-moment opname-BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [versie beheer voor Azure Storage services](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query's** | Ja, voor deze bewerking | `comp` = `snapshot` | De naam en waarde van de query parameter voor de BLOB-bewerking van de moment opname. |
   | **Verificatie** | Ja | `Managed Identity` | Het verificatie type dat moet worden gebruikt voor het verifiëren van toegang tot de Azure-Blob |
   |||||

   Hier volgt een voor beeld van een HTTP-actie waarin al deze eigenschaps waarden worden weer gegeven:

   ![Een HTTP-actie toevoegen voor toegang tot een Azure-resource](./media/create-managed-service-identity/http-action-example.png)

   Zie voor meer informatie over alle beschik bare Azure-REST API bewerkingen de [referentie voor azure rest API](https://docs.microsoft.com/rest/api/azure/).

1. Selecteer in de lijst **verificatie** de optie **beheerde identiteit**. Als de [eigenschap **authenticatie** wordt ondersteund](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , maar verborgen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u **verificatie**.

   > [!NOTE]
   > Niet alle triggers en acties bieden u de mogelijkheid om een verificatie type te selecteren. Zie [verificatie toevoegen aan uitgaande oproepen](logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie.

   ![Selecteer in de eigenschap Authentication de optie beheerde identiteit](./media/create-managed-service-identity/select-managed-identity.png)

1. Nadat u de **beheerde identiteit**hebt geselecteerd, wordt de eigenschap **doel groep** weer gegeven voor sommige triggers en acties. Als de eigenschap **doel groep** wordt ondersteund, maar verborgen, opent u de lijst **nieuwe para meter toevoegen** en selecteert u **doel groep**.

1. Zorg ervoor dat u de waarde van de **doel groep** instelt op de resource-id voor de doel resource of-service. Anders wordt standaard de eigenschap **doel groep** gebruikt voor de `https://management.azure.com/` resource-id. Dit is de resource-id voor Azure Resource Manager.

   > [!IMPORTANT]
   > Zorg ervoor dat de doel Resource-ID *precies overeenkomt* met de waarde die Azure Active Directory (AD) verwacht, inclusief alle vereiste afsluitende slashes. De resource-ID voor alle Azure Blob Storage-accounts vereist bijvoorbeeld een afsluitende slash. De resource-ID voor een specifiek opslag account vereist echter geen afsluitende slash. Controleer de [resource-id's voor de Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   In dit voor beeld wordt de eigenschap **doel groep** ingesteld op `https://storage.azure.com/` zodat de toegangs tokens die worden gebruikt voor verificatie geldig zijn voor alle opslag accounts. U kunt echter ook de URL van de basis service, `https://fabrikamstorageaccount.blob.core.windows.net`, voor een specifiek opslag account opgeven.

   ![Doel Resource-ID opgeven in eigenschap doel groep](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Zie de volgende onderwerpen voor meer informatie over het verlenen van toegang met Azure AD voor Azure Storage:

   * [Toegang tot Azure-blobs en-wacht rijen toestaan met behulp van Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Toegang tot Azure Storage autoriseren met Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Door het systeem toegewezen identiteit verwijderen

Als u de door het systeem toegewezen identiteit voor uw logische app niet meer wilt gebruiken, hebt u de volgende opties:

* [Azure-portal](#azure-portal-disable)
* [Azure Resource Manager sjablonen](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Als u uw logische app verwijdert, wordt de beheerde identiteit door Azure automatisch uit Azure AD verwijderd.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Door het systeem toegewezen identiteit in de Azure Portal verwijderen

Verwijder in de Azure Portal de door het systeem toegewezen identiteit [uit uw logische app](#disable-identity-logic-app) en de toegang van de identiteit [van uw doel resource](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Door het systeem toegewezen identiteit uit de logische app verwijderen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer onder **instellingen**in het menu van de logische app de optie **identiteit** > **systeem is toegewezen**. Selecteer onder **status**de optie **uit** > **Opslaan** > **Ja**.

   ![Stoppen met het gebruik van door het systeem toegewezen identiteit](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identiteits toegang verwijderen uit resources

1. Ga in het [Azure Portal](https://portal.azure.com)naar de doel-Azure-resource waar u de toegang wilt verwijderen voor een beheerde identiteit.

1. Selecteer **toegangs beheer (IAM)** in het menu van de doel resource. Selecteer op de werk balk **roltoewijzingen**.

1. Selecteer in de lijst rollen de beheerde identiteiten die u wilt verwijderen. Selecteer **verwijderen**op de werk balk.

   > [!TIP]
   > Als de optie **verwijderen** is uitgeschakeld, hebt u waarschijnlijk niet de juiste machtigingen. Zie [Administrator role permissions](../active-directory/users-groups-roles/directory-assign-admin-roles.md)(Engelstalig) in azure Active Directory voor meer informatie over de machtigingen waarmee u rollen voor resources kunt beheren.

De beheerde identiteit wordt nu verwijderd en heeft geen toegang meer tot de doel resource.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Beheerde identiteit in Azure Resource Manager sjabloon uitschakelen

Als u de door het systeem beheerde identiteit van de logische app met behulp van een Azure Resource Manager sjabloon hebt ingeschakeld, stelt u de eigenschap `type` kind van het `identity` object in op `None`. Met deze actie wordt ook de principal-ID voor de door het systeem beheerde identiteit verwijderd uit Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
