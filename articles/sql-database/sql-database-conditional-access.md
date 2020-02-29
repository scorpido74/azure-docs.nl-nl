---
title: Voorwaardelijke toegang
description: Meer informatie over het configureren van voorwaardelijke toegang voor Azure SQL Database en Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: f2431ee7c62079a3691a5ea99e562460df8f9309
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197569"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Voorwaardelijke toegang (MFA) met Azure SQL Database en Azure Synapse Analytics

Azure [SQL database](sql-database-technical-overview.md), [Managed instance](sql-database-managed-instance.md)en [Azure Synapse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ondersteunen micro soft voorwaardelijke toegang. 

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL Server en op zowel SQL Database als Azure Synapse die zijn gemaakt op de Azure SQL-Server. SQL Database wordt gebruikt bij het verwijzen naar SQL Database en Azure Synapse voor eenvoud.

De volgende stappen laten zien hoe u SQL Database kunt configureren voor het afdwingen van beleid voor voorwaardelijke toegang.  

## <a name="prerequisites"></a>Vereisten  
- U moet uw SQL Database of SQL-groep in azure Synapse configureren ter ondersteuning van Azure Active Directory-verificatie. Zie voor specifieke stappen [Azure Active Directory verificatie configureren en beheren met SQL database of Azure Synapse](sql-database-aad-authentication-configure.md).  
- Als multi-factor Authentication is ingeschakeld, moet u verbinding maken met met een ondersteund hulp programma, zoals de laatste SSMS. Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md)voor meer informatie.  

## <a name="configure-ca-for-azure-sql-dbdw"></a>CA configureren voor Azure SQL DB/DW  
1. Meld u aan bij de portal, selecteer **Azure Active Directory**en selecteer vervolgens **voorwaardelijke toegang**. Zie [Azure Active Directory technische Naslag informatie voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Blade voorwaardelijke toegang](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Klik op de Blade **voorwaardelijke toegang-beleids regels** op **Nieuw beleid**, geef een naam op en klik vervolgens op **regels configureren**.  
3. Onder **toewijzingen**selecteert u **gebruikers en groepen**, **selecteert u gebruikers en groepen selecteren**en selecteert u vervolgens de gebruiker of groep voor voorwaardelijke toegang. Klik op **selecteren**en klik vervolgens op **gereed** om uw selectie te accepteren.  
   ![gebruikers en groepen selecteren](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Selecteer **Cloud-apps**, klik op **apps selecteren**. U ziet alle apps die beschikbaar zijn voor voorwaardelijke toegang. Selecteer **Azure SQL database**, klik onderaan op **selecteren**en klik vervolgens op **gereed**.  
   ![SQL Database selecteren](./media/sql-database-conditional-access/select-sql-database.png)  
   Als **Azure SQL database** niet wordt weer gegeven in de volgende derde scherm afbeelding, voert u de volgende stappen uit:   
   - Meld u aan bij uw Azure SQL DB/DW-exemplaar met behulp van SSMS met een AAD-beheerders account.  
   - Voer `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`uit.  
   - Meld u aan bij AAD en controleer of Azure SQL Database en Azure Synapse zijn opgenomen in de toepassingen in uw AAD.  

5. Selecteer **toegangs beheer**, selecteer **toekennen**en controleer vervolgens het beleid dat u wilt Toep assen. Voor dit voor beeld selecteren we **multi-factor Authentication vereisen**.  
   ![Selecteer Grant Access](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Samenvatting  
De geselecteerde toepassing (Azure SQL Database) waarmee verbinding kan worden gemaakt met Azure SQL data base/DW via Azure AD Premium, nu het geselecteerde beleid voor voorwaardelijke toegang afdwingt, **vereiste multi-factor Authentication.**  
Voor vragen over Azure SQL Database en Azure Synapse met betrekking tot multi-factor Authentication, neemt u contact op met MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Volgende stappen  

Zie [uw Azure SQL database beveiligen](sql-database-security-tutorial.md)voor een zelf studie.
