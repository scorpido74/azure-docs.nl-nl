---
title: Voorwaardelijke toegang
description: Meer informatie over het configureren van voorwaardelijke toegang voor Azure SQL Database en het Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827165"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Voorwaardelijke toegang (MFA) met Azure SQL Database en Data Warehouse  

Azure [SQL database](sql-database-technical-overview.md), een [beheerd exemplaar](sql-database-managed-instance.md)en [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ondersteunen micro soft voorwaardelijke toegang. 

> [!NOTE]
> Dit onderwerp is van toepassing op Azure SQL-servers en op SQL Database- en SQL Data Warehouse-databases die op deze Azure SQL-servers worden gemaakt. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

De volgende stappen laten zien hoe u SQL Database kunt configureren voor het afdwingen van beleid voor voorwaardelijke toegang.  

## <a name="prerequisites"></a>Vereisten  
- U moet uw SQL Database of SQL Data Warehouse configureren voor de ondersteuning van Azure Active Directory-verificatie. Zie [Azure Active Directory authenticatie configureren en beheren met SQL database of SQL Data Warehouse](sql-database-aad-authentication-configure.md)voor specifieke stappen.  
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
   - Meld u aan bij AAD en controleer of Azure SQL Database en Data Warehouse worden weer gegeven in de toepassingen in uw AAD.  

5. Selecteer **toegangs beheer**, selecteer **toekennen**en controleer vervolgens het beleid dat u wilt Toep assen. Voor dit voor beeld selecteren we **multi-factor Authentication vereisen**.  
   ![Selecteer Grant Access](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Samenvatting  
De geselecteerde toepassing (Azure SQL Database) waarmee verbinding kan worden gemaakt met Azure SQL data base/DW via Azure AD Premium, nu het geselecteerde beleid voor voorwaardelijke toegang afdwingt, **vereiste multi-factor Authentication.**  
Neem contact op met MFAforSQLDB@microsoft.comvoor vragen over Azure SQL Database en data warehouse met betrekking tot multi-factor Authentication.  

## <a name="next-steps"></a>Volgende stappen  

Zie [uw Azure SQL database beveiligen](sql-database-security-tutorial.md)voor een zelf studie.
