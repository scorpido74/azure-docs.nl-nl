---
title: Een Service-Principal toevoegen aan Azure Analysis Services rol Server beheerder | Microsoft Docs
description: Meer informatie over het toevoegen van een Automation Service-Principal aan de server beheerdersrol
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c141bcea030f5afcc3cb33adba32f4a96c335eec
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147397"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Een Service-Principal toevoegen aan de rol Server beheerder 

 Voor het automatiseren van Power shell-taken zonder toezicht moet een service-principal over **Server beheerders** rechten beschikken voor de Analysis Services server die wordt beheerd. In dit artikel wordt beschreven hoe u een Service-Principal kunt toevoegen aan de rol Server Administrators op een Azure AS-server.

## <a name="before-you-begin"></a>Voordat u begint
Voordat u deze taak voltooit, moet u een Service-Principal hebben geregistreerd in Azure Active Directory.

Een [Service-Principal maken-Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Service-principal maken - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Vereiste machtigingen
Als u deze taak wilt volt ooien, moet u beschikken over [Server beheerders](analysis-services-server-admins.md) machtigingen voor de Azure-server. 

## <a name="add-service-principal-to-server-administrators-role"></a>Service-Principal toevoegen aan de rol Server Administrators

1. Maak in SSMS verbinding met uw Azure als server.
2. Klik in **Server eigenschappen** > -**beveiliging**op **toevoegen**.
3. In **een gebruiker of groep selecteren**zoekt u de geregistreerde app op naam, selecteert u en klikt u vervolgens op **toevoegen**.

    ![Zoeken naar Service-Principal-account](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Controleer de account-ID van de Service-Principal en klik vervolgens op **OK**.
    
    ![Zoeken naar Service-Principal-account](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Voor Server bewerkingen die gebruikmaken van Azure PowerShell-cmdlets, moet de service principal die scheduler uitvoert ook behoren tot de rol van **eigenaar** van de resource in [Azure op rollen gebaseerde Access Control (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Gerelateerde informatie

* [SQL Server Power shell-module downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


