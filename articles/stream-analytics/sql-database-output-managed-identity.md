---
title: Beheerde identiteiten gebruiken om toegang te krijgen tot Azure SQL Database-Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Azure SQL data base-uitvoer.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598239"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Beheerde identiteiten gebruiken om toegang te krijgen tot Azure SQL Database vanuit een Azure Stream Analytics-taak (preview-versie)

Azure Stream Analytics ondersteunt [beheerde identiteits verificatie](../active-directory/managed-identities-azure-resources/overview.md) voor Azure SQL database uitvoer-Sinks. Beheerde identiteiten elimineren de beperkingen van verificatie methoden op basis van gebruikers, zoals de nood zaak om opnieuw te worden geverifieerd als gevolg van wachtwoord wijzigingen of verlopen van gebruikers tokens die elke 90 dagen worden uitgevoerd. Wanneer u de nood zaak om hand matig te verifiëren verwijdert, kunnen uw Stream Analytics-implementaties volledig worden geautomatiseerd.

Een beheerde identiteit is een beheerde toepassing die is geregistreerd in Azure Active Directory die een bepaalde Stream Analytics taak vertegenwoordigt. De beheerde toepassing wordt gebruikt om te verifiëren bij een doel resource. Dit artikel laat u zien hoe u beheerde identiteit kunt inschakelen voor een Azure SQL Database uitvoer (en) van een Stream Analytics taak via de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Het volgende is vereist voor deze functie:

- Een Azure Stream Analytics taak.

- Een Azure SQL Database resource.

## <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Eerst maakt u een beheerde identiteit voor uw Azure Stream Analytics-taak.

1. Open de Azure Stream Analytics-taak in de [Azure Portal](https://portal.azure.com).

1. Selecteer in het navigatie menu links **beheerde identiteit** onder **configureren**. Schakel vervolgens het selectie vakje naast door **systeem toegewezen beheerde identiteit gebruiken** in en selecteer **Opslaan**.

   ![Door het systeem toegewezen beheerde identiteit selecteren](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Een service-principal voor de identiteit van de Stream Analytics-taak wordt gemaakt in Azure Active Directory. De levens cyclus van de zojuist gemaakte identiteit wordt beheerd door Azure. Wanneer de Stream Analytics taak wordt verwijderd, wordt de bijbehorende identiteit (de service-principal) automatisch verwijderd door Azure. 

1. Wanneer u de configuratie opslaat, wordt de object-ID (OID) van de Service-Principal vermeld als de principal-ID zoals hieronder wordt weer gegeven: 

   ![Object-ID weer gegeven als Principal-ID](./media/sql-db-output-managed-identity/principal-id.png)

   De service-principal heeft dezelfde naam als de Stream Analytics taak. Als de naam van uw taak bijvoorbeeld *MyASAJob*is, is de naam van de Service-Principal ook *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Een Active Directory beheerder selecteren

Nadat u een beheerde identiteit hebt gemaakt, selecteert u een Active Directory beheerder.

1. Navigeer naar uw Azure SQL Database-resource en selecteer de SQL Server waarvan de data base zich bevindt. U vindt de SQL Server naam naast *Server naam* op de pagina overzicht van resources. 

1. Selecteer **Active Directory beheerder** onder **instellingen**. Selecteer vervolgens **beheerder instellen**. 

   ![Active Directory-beheer pagina](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. Zoek op de pagina Active Directory-beheer naar een gebruiker of groep om een beheerder voor de SQL Server te zijn en klik op **selecteren**.  

   ![Active Directory beheerder toevoegen](./media/sql-db-output-managed-identity/add-admin.png)

1. Selecteer **Opslaan** op de pagina **beheer van Active Directory** . Het proces voor het wijzigen van de beheerder duurt enkele minuten.  

## <a name="create-a-database-user"></a>Een databasegebruiker maken

Vervolgens maakt u een Inge sloten database gebruiker in uw SQL Database die is toegewezen aan de Azure Active Directory identiteit. De Inge sloten database gebruiker heeft geen aanmelding voor de hoofd database, maar wordt toegewezen aan een identiteit in de map die is gekoppeld aan de data base. De identiteit van de Azure Active Directory kan een afzonderlijk gebruikers account of een groep zijn. In dit geval wilt u een Inge sloten database gebruiker voor uw Stream Analytics-taak maken. 

1. Maak verbinding met de SQL database met behulp van SQL Server Management Studio. De **gebruikers naam** is een Azure Active Directory gebruiker met de machtiging voor het **wijzigen van een gebruiker** . De beheerder die u instelt op de SQL Server is een voor beeld. Gebruik **Azure Active Directory-Universal met MFA-** verificatie. 

   ![Verbinding maken met SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   De naam van de server `<SQL Server name>.database.windows.net` kan verschillen in verschillende regio's. Bijvoorbeeld, de regio China moet gebruiken `<SQL Server name>.database.chinacloudapi.cn` .
 
   U kunt een specifieke SQL Database opgeven door te gaan naar **opties > verbindings eigenschappen > verbinding maken met data base**.  

   ![Eigenschappen van SQL Server-verbinding](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Wanneer u voor het eerst verbinding maakt, kunt u het volgende venster tegen komen:

   ![Venster nieuwe firewall regel](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Als dit het geval is, gaat u naar uw SQL Server-Resource op de Azure Portal. Open de pagina **firewalls en virtuele netwerken** onder het gedeelte **beveiliging** . 
   1. Voeg een nieuwe regel toe met een regel naam.
   1. Gebruik het IP-adres *uit* het venster **nieuwe firewall regel** voor het *begin-IP*.
   1. Gebruik het *IP-adres in het* venster **nieuwe firewall regel** voor het *eind-IP*. 
   1. Selecteer **Opslaan** en probeer opnieuw verbinding te maken vanuit SQL Server Management Studio. 

1. Nadat u verbinding hebt gemaakt, maakt u de Inge sloten database gebruiker. Met de volgende SQL-opdracht maakt u een Inge sloten database gebruiker die dezelfde naam heeft als uw Stream Analytics-taak. Zorg ervoor dat u de haken rond de *ASA_JOB_NAME*plaatst. Gebruik de volgende T-SQL-syntaxis en voer de query uit. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analytics taak machtigingen verlenen

De Stream Analytics taak heeft machtigingen van een beheerde identiteit om **verbinding te maken** met uw SQL database resource. Het is waarschijnlijk de beste manier om de Stream Analytics-taak opdrachten uit te voeren, zoals **selecteren**. U kunt deze machtigingen toewijzen aan de Stream Analytics-taak met behulp van SQL Server Management Studio. Zie de referentie [Grant (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) voor meer informatie.

U kunt ook met de rechter muisknop op uw SQL database in SQL Server Management Studio klikken en **eigenschappen > machtigingen**selecteren. In het menu machtigingen ziet u de Stream Analytics-taak die u eerder hebt toegevoegd. u kunt ook hand matig machtigingen verlenen of weigeren zoals u dat wilt.

## <a name="create-an-azure-sql-database-output"></a>Een Azure SQL Database uitvoer maken

Nu uw beheerde identiteit is geconfigureerd, kunt u de Azure SQL Database als uitvoer aan uw Stream Analytics-taak toevoegen.

1. Ga terug naar uw Stream Analytics-taak en navigeer naar de pagina **uitvoer** onder **taak topologie**. 

1. Selecteer **> SQL database toevoegen**. Selecteer in het venster uitvoer eigenschappen van de SQL Database uitvoer Sink **beheerde identiteit** in de vervolg keuzelijst verificatie modus.

1. Vul de rest van de eigenschappen in. Zie [Create a SQL database output with stream Analytics](stream-analytics-define-outputs.md#sql-database)voor meer informatie over het maken van een SQL database-uitvoer. Wanneer u klaar bent, selecteert u **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de uitvoer van Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics uitvoer naar Azure SQL Database](stream-analytics-sql-output-perf.md)
