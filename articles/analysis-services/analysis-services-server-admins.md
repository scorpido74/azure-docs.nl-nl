---
title: Server beheerders beheren in Azure Analysis Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Server beheerders beheert voor een Azure Analysis Services-server met behulp van de Azure Portal, Power shell of REST-Api's.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c876e27165eba30b17874eca600ba81be2e9354
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019428"
---
# <a name="manage-server-administrators"></a>Serverbeheerders beheren

Server beheerders moeten een geldige gebruiker, Service-Principal of beveiligings groep in de Azure Active Directory (Azure AD) zijn voor de Tenant waarin de server zich bevindt. U kunt **Analysis Services beheerders** voor uw server gebruiken in azure Portal, Server eigenschappen in SSMS, Power shell of rest API voor het beheren van Server beheerders. 

Gebruik wanneer u een **beveiligings groep**toevoegt `obj:groupid@tenantid` . Service-principals worden niet ondersteund in beveiligings groepen die zijn toegevoegd aan de rol Server beheerder.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Server beheerders toevoegen met behulp van Azure Portal

1. Klik in de portal, voor uw server, op **Analysis Services beheerders**.
2. Klik in ** \<servername> -Analysis Services-Administrators**op **toevoegen**.
3. Selecteer in **Server beheerders toevoegen**de optie gebruikers accounts uit uw Azure AD of externe gebruikers uitnodigen op e-mail adres.

    ![Server beheerders in Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Server beheerders toevoegen met behulp van SSMS

1. Klik met de rechter muisknop op de > **Eigenschappen**van de server.
2. Klik in **Analyseserver eigenschappen**op **beveiliging**.
3. Klik op **toevoegen**en voer het e-mail adres voor een gebruiker of groep in uw Azure AD in.
   
    ![Server beheerders toevoegen in SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik de cmdlet [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) om de Administrator-para meter op te geven bij het maken van een nieuwe server. <br>
Gebruik de cmdlet [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) om de Administrator-para meter voor een bestaande server te wijzigen.

## <a name="rest-api"></a>REST-API

Gebruik [Create](/rest/api/analysisservices/servers/create) om de eigenschap asAdministrator op te geven bij het maken van een nieuwe server. <br>
Gebruik [Update](/rest/api/analysisservices/servers/update) om de eigenschap asAdministrator op te geven bij het wijzigen van een bestaande server. <br>



## <a name="next-steps"></a>Volgende stappen 

[Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md)  
[Database rollen en-gebruikers beheren](analysis-services-database-users.md)  
[Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../role-based-access-control/overview.md)