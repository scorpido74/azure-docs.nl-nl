---
title: Serverbeheerders beheren in Azure Analysis Services | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u serverbeheerders voor een Azure Analysis Services-server beheert met behulp van de Azure-portal, PowerShell of REST API's.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 944a84405bd7e03b72b2610278f9f0e4d3cfaf38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454234"
---
# <a name="manage-server-administrators"></a>Serverbeheerders beheren

Serverbeheerders moeten een geldige gebruikers- of beveiligingsgroep zijn in de Azure Active Directory (Azure AD) van Azure voor de tenant waarin de server zich bevindt. U **Analysis Services-beheerders** voor uw server gebruiken in Azure-portal, Servereigenschappen in SSMS, PowerShell of REST API om serverbeheerders te beheren. 

Gebruik bij het toevoegen `obj:groupid@tenantid`van een **beveiligingsgroep**.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Serverbeheerders toevoegen met Azure-portal

1. Klik in de portal voor uw server op **Analysis Services Administrators**.
2. Klik in ** \<servernaam> - Analysis Services-beheerders**op **Toevoegen**.
3. Selecteer **in Serverbeheerders toevoegen**gebruikersaccounts uit uw Azure-AD of nodig externe gebruikers uit via e-mailadres.

    ![Serverbeheerders in Azure-portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Serverbeheerders toevoegen met SSMS

1. Klik met de rechtermuisknop op de server > **eigenschappen**.
2. Klik in **eigenschappen van analysisserver**op **Beveiliging**.
3. Klik **op Toevoegen**en voer het e-mailadres in voor een gebruiker of groep in uw Azure AD.
   
    ![Serverbeheerders toevoegen aan SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik de cmdlet [Nieuw-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) om de parameter Administrator op te geven bij het maken van een nieuwe server. <br>
Gebruik de cmdlet [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) om de parameter Administrator voor een bestaande server te wijzigen.

## <a name="rest-api"></a>REST-API

Gebruik [Maken](https://docs.microsoft.com/rest/api/analysisservices/servers/create) om de eigenschap asAdministrator op te geven bij het maken van een nieuwe server. <br>
Gebruik [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) om de eigenschap asAdministrator op te geven wanneer u een bestaande server wijzigt. <br>



## <a name="next-steps"></a>Volgende stappen 

[Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md)  
[Databaserollen en -gebruikers beheren](analysis-services-database-users.md)  
[Op rollen gebaseerd toegangscontrole](../role-based-access-control/overview.md)  

