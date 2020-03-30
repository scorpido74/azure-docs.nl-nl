---
title: Voorwaardelijke toegang
description: Meer informatie over het configureren van voorwaardelijke toegang voor Azure SQL Database en Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124897"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Voorwaardelijke toegang (MFA) met Azure SQL Database en Azure Synapse Analytics

Azure [SQL Database](sql-database-technical-overview.md), Managed [Instance](sql-database-managed-instance.md)en [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ondersteunen Microsoft Conditional Access. 

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL-server en op zowel SQL Database als Azure Synapse die zijn gemaakt op de Azure SQL-server. Voor de eenvoud wordt SQL Database gebruikt bij het verwijzen naar zowel SQL Database als Azure Synapse.

In de volgende stappen wordt uitgelegd hoe u SQL-database configureert om een beleid voor voorwaardelijke toegang af te dwingen.  

## <a name="prerequisites"></a>Vereisten  
- U moet uw SQL Database of SQL-groep configureren in Azure Synapse om Azure Active Directory-verificatie te ondersteunen. Zie [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse](sql-database-aad-authentication-configure.md)voor specifieke stappen.  
- Wanneer multi-factor authenticatie is ingeschakeld, moet u verbinding maken met op ondersteund gereedschap, zoals de nieuwste SSMS. Zie [Multifactorverificatie van Azure SQL Database configureren voor SQL Server Management Studio voor](sql-database-ssms-mfa-authentication-configure.md)meer informatie.  

## <a name="configure-ca-for-azure-sql-dbdw"></a>CA configureren voor Azure SQL DB/DW  
1. Meld u aan bij de portal, selecteer **Azure Active Directory**en selecteer Voorwaardelijke **toegang**. Zie technische [naslaginformatie](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)over azure Active Directory Conditional Access voor meer informatie .  
   ![Het blad voorwaardelijke toegang](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Klik in het blad **Conditional Access-policies** op **Nieuw beleid,** geef een naam op en klik vervolgens op **Regels configureren**.  
3. Selecteer **onder Toewijzingen** **Gebruikers en groepen,** schakel **Gebruikers en groepen**selecteren en selecteer vervolgens de gebruiker of groep voor Voorwaardelijke toegang. Klik **op Selecteren**en klik vervolgens op **Gereed** om uw selectie te accepteren.  
   ![gebruikers en groepen selecteren](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecteer **Cloud-apps**en klik op **Apps selecteren**. U ziet alle apps die beschikbaar zijn voor voorwaardelijke toegang. Selecteer **Azure SQL Database**, klik onderin op **Selecteren**en klik vervolgens op **Gereed**.  
   ![SQL-database selecteren](./media/sql-database-conditional-access/select-sql-database.png)  
   Als azure **SQL-database** in de volgende derde schermafbeelding niet wordt weergegeven, voert u de volgende stappen uit:   
   - Meld u aan bij uw Azure SQL DB/DW-exemplaar met SSMS met een AAD-beheerdersaccount.  
   - Uitvoeren `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Meld u aan bij AAD en controleer of Azure SQL Database en Azure Synapse worden vermeld in de toepassingen in uw AAD.  

5. Selecteer **Toegangsbesturingselementen**, selecteer **Verlenen**en controleer vervolgens het beleid dat u wilt toepassen. In dit voorbeeld selecteren we **Multi-factor authenticatie vereisen**.  
   ![subsidietoegang selecteren](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Samenvatting  
De geselecteerde toepassing (Azure SQL Database) waarmee verbinding kan worden gemaakt met Azure SQL DB/DW met Azure AD Premium, dwingt nu het geselecteerde beleid voor voorwaardelijke toegang, **Vereist multi-factor authenticatie.**  
Neem contact op met MFAforSQLDB@microsoft.comvoor vragen over Azure SQL Database en Azure Synapse met betrekking tot multi-factor authenticatie.  

## <a name="next-steps"></a>Volgende stappen  

Zie [Uw Azure SQL Database beveiligen](sql-database-security-tutorial.md)voor een zelfstudie.
