---
title: Een app verplaatsen naar een andere regio
description: Meer informatie over het verplaatsen van app-servicebronnen van de ene regio naar de andere.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925707"
---
# <a name="move-an-app-service-app-to-another-region"></a>Een App-service-app verplaatsen naar een andere regio

In dit artikel wordt beschreven hoe u app-servicebronnen verplaatst naar een andere Azure-regio. U uw resources om een aantal redenen naar een andere regio verplaatsen. Bijvoorbeeld om te profiteren van een nieuwe Azure-regio, om functies of services te implementeren die alleen beschikbaar zijn in specifieke regio's, om te voldoen aan interne beleids- en governancevereisten of om te voldoen aan de vereisten voor capaciteitsplanning.

App Service-bronnen zijn regiospecifiek en kunnen niet worden verplaatst naar verschillende regio's. U moet een kopie maken van uw bestaande App-servicebronnen in het doelgebied en uw inhoud verplaatsen naar de nieuwe app. Als uw bron-app een aangepast domein gebruikt, u [deze migreren naar de nieuwe app in het doelgebied](manage-custom-dns-migrate-domain.md) wanneer u klaar bent.

Om het kopiëren van uw app gemakkelijker te maken, u [een afzonderlijke App Service-app klonen](app-service-web-app-cloning.md) in een App Service-abonnement in een andere regio, maar het heeft wel [beperkingen](app-service-web-app-cloning.md#current-restrictions), vooral dat het geen Linux-apps ondersteunt.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de App Service-app zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.
- Zorg ervoor dat de doelregio app-service en elke gerelateerde service ondersteunt, waarvan u de resources wilt verplaatsen.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Voorbereiden

Identificeer alle App Service-bronnen die u momenteel gebruikt. Bijvoorbeeld:

- App Service-apps
- [App Service-abonnementen](overview-hosting-plans.md)
- [Implementatiesleuven](deploy-staging-slots.md)
- [Aangepaste domeinen die zijn aangeschaft in Azure](manage-custom-dns-buy-domain.md)
- [SSL-certificaten](configure-ssl-certificate.md)
- [Azure Virtual Network-integratie](web-sites-integrate-with-vnet.md)
- [Hybride verbindingen](app-service-hybrid-connections.md).
- [Beheerde identiteiten](overview-managed-identity.md)
- [Back-upinstellingen](manage-backup.md)

Bepaalde bronnen, zoals geïmporteerde certificaten of hybride verbindingen, bevatten integratie met andere Azure-services. Zie de documentatie voor de betreffende services voor informatie over het verplaatsen van deze bronnen door regio's.

## <a name="move"></a>Verplaatsen

1. [Maak een back-up van de bron-app](manage-backup.md).
1. [Maak een app in een nieuw App Service-abonnement in het doelgebied.](app-service-plan-manage.md#create-an-app-service-plan)
2. [De back-up herstellen in de doel-app](web-sites-restore.md)
2. Als u een aangepast domein gebruikt, [bindt u het preventief aan de doel-app](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) met `awverify.` en schakelt u het domein in de [doel-app in.](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)
3. Configureer al het andere in uw doel-app als hetzelfde als de bron-app en controleer uw configuratie.
4. Wanneer u klaar bent om het aangepaste domein naar de doel-app te wijzen, [brengt u de domeinnaam opnieuw in kaart.](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Bronbronnen opschonen

Verwijder het bron-app- en App-serviceplan. [Een App Service-abonnement in de niet-vrije laag brengt kosten met zich mee, zelfs als er geen app in wordt uitgevoerd.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Volgende stappen

[Azure App Service App klonen met PowerShell](app-service-web-app-cloning.md)