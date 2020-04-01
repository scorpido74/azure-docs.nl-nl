---
title: Serviceprincipal toevoegen aan de beheerrol van Azure Analysis Services | Microsoft Documenten
description: Meer informatie over het toevoegen van een principal van de automatiseringsservice aan de functie Azure Analysis Services-serverbeheerder
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 925fbbb51ac240b96486a2c0aa09c850a8d164bc
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408650"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Een serviceprincipal toevoegen aan de serverbeheerderrol 

 Als u onbeheerde PowerShell-taken wilt automatiseren, moet een **serviceprincipal serverbeheerdersbevoegdheden** hebben op de Analysis Services-server die wordt beheerd. In dit artikel wordt beschreven hoe u een serviceprincipal toevoegt aan de rol serverbeheerders op een Azure AS-server. U dit doen met SQL Server Management Studio of een Resource Manager-sjabloon.

## <a name="before-you-begin"></a>Voordat u begint
Voordat u deze taak voltooit, moet u een serviceprincipal hebben geregistreerd in Azure Active Directory.

[Serviceprincipal maken - Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Service-principal maken - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio gebruiken

U serverbeheerders configureren met SQL Server Management Studio (SSMS). Als u deze taak wilt voltooien, moet u over [serverbeheerders](analysis-services-server-admins.md) machtigingen hebben op de Azure AS-server. 

1. Maak in SSMS verbinding met uw Azure AS-server.
2. Klik in**Beveiliging** **servereigenschappen** > op **Toevoegen**.
3. Zoek **in Een gebruiker of groep**selecteren naar uw geregistreerde app op naam, selecteer en klik op **Toevoegen**.

    ![Zoeken naar serviceprincipal account](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Controleer de hoofdaccount-id van de service en klik op **OK**.
    
    ![Zoeken naar serviceprincipal account](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Een Resource Manager-sjabloon gebruiken

U serverbeheerders ook configureren door de Analysis Services-server te implementeren met behulp van een Azure Resource Manager-sjabloon. De identiteit waarop de implementatie wordt uitgevoerd, moet deel uitmaken van de **rol inzender** voor de bron in [Azure Role-Based Access Control (RBAC).](../role-based-access-control/overview.md)

> [!IMPORTANT]
> De serviceprincipal moet worden `app:{service-principal-client-id}@{azure-ad-tenant-id}`toegevoegd met behulp van de indeling .

Met de volgende sjabloon Resourcebeheer wordt een Analysis Services-server geïmplementeerd met een opgegeven serviceprincipal die is toegevoegd aan de rol Analysis Services Admin:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Beheerde identiteiten gebruiken

Een beheerde identiteit kan ook worden toegevoegd aan de lijst analyseservicesbeheerders. U hebt bijvoorbeeld een Logische App met een door het systeem toegewezen beheerde identiteit en u wilt deze de mogelijkheid bieden om uw Analysis [Services-server](../logic-apps/create-managed-service-identity.md)te beheren.

In de meeste delen van de Azure-portal en API's worden beheerde identiteiten geïdentificeerd met behulp van hun servicehoofdobject-id. Analysis Services vereist echter dat ze worden geïdentificeerd met behulp van hun client-ID. Als u de client-id voor een serviceprincipal wilt verkrijgen, u de Azure CLI gebruiken:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

U powershell ook gebruiken:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

U deze client-id vervolgens gebruiken in combinatie met de tenant-id om de beheerde identiteit toe te voegen aan de lijst met analysisservices, zoals hierboven beschreven.

## <a name="related-information"></a>Gerelateerde informatie

* [SQL Server PowerShell-module downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


