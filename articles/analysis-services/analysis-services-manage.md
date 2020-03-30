---
title: Azure Analysis Services beheren | Microsoft Documenten
description: In dit artikel worden de hulpprogramma's beschreven die worden gebruikt voor het beheer van beheer- en beheertaken voor een Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572795"
---
# <a name="manage-analysis-services"></a>Analysis Services beheren
Zodra u een Analysis Services-server in Azure hebt gemaakt, kunnen er een aantal beheer- en beheertaken zijn die u meteen of ergens op de weg moet uitvoeren. Voer bijvoorbeeld de verwerking uit naar de vernieuwingsgegevens, bepaal wie toegang heeft tot de modellen op uw server of controleer de status van uw server. Sommige beheertaken kunnen alleen worden uitgevoerd in azure portal, andere in SQL Server Management Studio (SSMS), en sommige taken kunnen worden uitgevoerd in een van beide.

## <a name="azure-portal"></a>Azure Portal
[Azure-portal](https://portal.azure.com/) is de plek waar u servers maken en verwijderen, serverbronnen bewaken, de grootte wijzigen en beheren wie toegang heeft tot uw servers.  Als u problemen ondervindt, u ook een ondersteuningsverzoek indienen.

![Servernaam bepalen in Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Verbinding maken met uw server in Azure is net als verbinding maken met een serverinstantie in uw eigen organisatie. Vanuit SSMS u veel van dezelfde taken uitvoeren, zoals procesgegevens of een verwerkingsscript maken, rollen beheren en PowerShell gebruiken.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>SSMS downloaden en installeren
Als u de nieuwste functies en de soepelste ervaring wilt opdoen wanneer u verbinding maakt met uw Azure Analysis Services-server, moet u ervoor zorgen dat u de nieuwste versie van SSMS gebruikt. 

[SQL Server Management Studio downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Verbinding maken met SSMS
 Controleer bij het gebruik van SSMS voordat u de eerste keer verbinding maakt met uw server, of uw gebruikersnaam is opgenomen in de groep Analysis Services Admins. Zie [Serverbeheerders en databasegebruikers](#server-administrators-and-database-users) later in dit artikel voor meer informatie.

1. Voordat u verbinding maakt, moet u de naam van de server krijgen. In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
    ![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Klik in SSMS > **Objectverkenner** op **Verbinding maken** > **Analysis Services**.
3. Kies in het dialoogvenster **Verbinding maken met server** de naam van de server in en kies vervolgens in **Verificatie**een van de volgende verificatietypen:   
    > [!NOTE]
    > Verificatietype, **Active Directory - Universal met MFA-ondersteuning,** wordt aanbevolen.

    > [!NOTE]
    > Als u zich aanmeldt met een Microsoft-account, laat Live ID, Yahoo, Gmail, enz., het wachtwoordveld leeg. U wordt gevraagd om een wachtwoord nadat u op Verbinding hebt geklikt.

    **Windows-verificatie** voor het gebruik van uw Windows-domein\gebruikersnaam- en wachtwoordreferenties.

    **Active Directory Password Authentication** om een organisatieaccount te gebruiken. Bijvoorbeeld wanneer u verbinding maakt vanaf een niet-domein aangesloten computer.

    **Active Directory - Universeel met MFA-ondersteuning** voor het gebruik [van niet-interactieve of multi-factor authenticatie](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Verbinding maken in SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Serverbeheerders en databasegebruikers
In Azure Analysis Services zijn er twee typen gebruikers, serverbeheerders en databasegebruikers. Beide typen gebruikers moeten zich in uw Azure Active Directory bevinden en moeten worden opgegeven op een e-mailadres van de organisatie of UPN. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Verbindingsproblemen oplossen
Wanneer u verbinding maakt met SSMS, moet u mogelijk de inlogcache wissen als u problemen ondervindt. Niets is in de cache op schijf. Als u de cache wilt wissen, sluit en start u het verbindingsproces opnieuw. 

## <a name="next-steps"></a>Volgende stappen
Als u nog geen tabelmodel hebt geïmplementeerd op uw nieuwe server, is dit een goed moment. Zie [Implementeren naar Azure Analysis Services](analysis-services-deploy.md) voor meer informatie.

Als u een model op uw server hebt geïmplementeerd, u er verbinding mee maken via een client of browser. Zie [Gegevens ophalen van azure Analysis Services-server](analysis-services-connect.md)voor meer informatie.

