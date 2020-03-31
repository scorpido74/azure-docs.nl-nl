---
title: Voorbeeld van de multi-tenant-app - Wingtip SaaS
description: Biedt stappen en richtlijnen voor het installeren en uitvoeren van de voorbeeldtoepassing met meerdere tenants die Azure SQL Database gebruikt, het voorbeeld van Wingtip Tickets SaaS.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132290"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Algemene richtlijnen voor het werken met Wingtip Tickets voorbeeld SaaS apps

Dit artikel bevat algemene richtlijnen voor het uitvoeren van het voorbeeld Van SaaS-toepassingen voor het voorbeeld van Wingtip Tickets die Azure SQL Database gebruiken.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Download en deblokkering van de Wingtip Tickets SaaS scripts

Uitvoerbare inhoud (scripts, dlls) kan door Windows worden geblokkeerd wanneer zip-bestanden worden gedownload van een externe bron en worden geëxtraheerd. Wanneer u de scripts uit een zip-bestand haalt, **voert u de onderstaande stappen uit om het .zip-bestand te deblokkeren voordat u het uitpakken van .** Dit zorgt ervoor dat de scripts mogen worden uitgevoerd.

1. Blader naar de Wingtip Tickets SaaS GitHub repo voor het databasehuurpatroon dat u wilt verkennen:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klik op **Klonen of download**.
3. Klik **op Zip downloaden** en sla het bestand op.
4. Klik met de rechtermuisknop op het zip-bestand en selecteer **Eigenschappen**. De naam van het zip-bestand komt overeen met de naam repo. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Selecteer op het tabblad **Algemeen** de optie **De blokkering opheffen**.
6. Klik op **OK**.
7. Pak de bestanden uit.

Scripts bevinden zich in de *.. Map \\Leermodules.*


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Werken met de Wingtip Tickets PowerShell-scripts

Om het meeste uit het monster te halen, moet je in de meegeleverde scripts duiken. Gebruik breekpunten en stap door de scripts terwijl ze worden uitgevoerd en onderzoek hoe de verschillende SaaS-patronen worden geïmplementeerd. Om gemakkelijk door de meegeleverde scripts en modules te stappen voor het beste begrip, raden we aan om de [PowerShell ISE te](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)gebruiken.

### <a name="update-the-configuration-file-for-your-deployment"></a>Het configuratiebestand voor uw implementatie bijwerken

Bewerk het **UserConfig.psm1-bestand** met de brongroep en gebruikerswaarde die u tijdens de implementatie instelt:

1. Open de *PowerShell ISE* en laad ... \\Leermodules\\*UserConfig.psm1*
2. *ResourceGroupName* en *Naam bijwerken* met de specifieke waarden voor uw implementatie (alleen op de lijnen 10 en 11).
3. Sla de wijzigingen op!

Als u deze waarden hier instelt, hoeft u deze implementatiespecifieke waarden in elk script niet bij te werken.

### <a name="execute-the-scripts-by-pressing-f5"></a>Voer de scripts uit door op F5 te drukken

Verschillende scripts gebruiken *$PSScriptRoot* om door mappen te navigeren en *$PSScriptRoot* wordt alleen geëvalueerd wanneer scripts worden uitgevoerd door op **F5**te drukken.Het markeren en uitvoeren van een selectie **(F8)** kan leiden tot fouten, dus druk op **F5** bij het uitvoeren van scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Doorloop de scripts stapsgewijs om de implementatie te kunnen bekijken

De beste manier om de scripts te begrijpen is door er doorheen te stappen om te zien wat ze doen. Bekijk de meegeleverde **Demo-scripts** die een eenvoudig te volgen workflow op hoog niveau presenteren. De **Demo-scripts** tonen de stappen die nodig zijn om elke taak te volbrengen, dus stel breekpunten in en boor dieper in de afzonderlijke aanroepen om implementatiedetails voor de verschillende SaaS-patronen te zien.

Tips voor het verkennen en doorlopen van PowerShell-scripts:

- Open **Demo-scripts** in de PowerShell ISE.
- F5 uitvoeren **F5** of voortzetten (het gebruik van **F8** wordt afgeraden omdat *$PSScriptRoot* niet wordt geëvalueerd bij het uitvoeren van selecties van een script).
- Plaats onderbrekingspunten door op een regel te klikken of een regel te selecteren en op **F9** te drukken.
- Stap over een functie of scriptaanroep heen met **F10**.
- Stap in een functie of scriptaanroep met **F11**.
- Stap uit de huidige functie of scriptaanroep met **Shift + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Bekijk het databaseschema en voer SQL-query’s uit met SSMS

Gebruik [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) om verbinding te maken en door de toepassingsservers en databases te bladeren.

De implementatie heeft in eerste instantie tenants en catalogus SQL Database-servers om verbinding mee te maken. De naamgeving van de servers is afhankelijk van het databasehuurpatroon (zie hieronder voor details).

   - **Zelfstandige toepassing:** servers voor elke tenant (bijvoorbeeld. *contosoconcerthall-&lt;&gt; Gebruikersserver)* en *catalogus-sa-&lt;Gebruiker&gt; *
   - **Database per tenant:** *tenants1-dpt-&lt;Gebruiker&gt; * en *catalogus-dpt-&lt;Gebruikersservers&gt; *
   - **Multi-tenant database:** *tenants1-mt-&lt;Gebruiker&gt; * en *catalogus-mt-&lt;Gebruikersservers&gt; *

Om een succesvolle demoverbinding te garanderen, hebben alle servers een [firewallregel](sql-database-firewall-configure.md) die alle IP's doorlaat.


1. Open *SSMS* en maak verbinding met de huurders. De servernaam is afhankelijk van het databasehuurpatroon dat u hebt geselecteerd (zie hieronder voor details):
    - **Zelfstandige toepassing:** servers van individuele tenants (bijvoorbeeld. *contosoconcerthall-&lt;&gt;Gebruiker .database.windows.net*)
    - **Database per tenant:** *&lt;tenants1-dpt- Gebruiker&gt;.database.windows.net*
    - **Multi-tenant database:** *tenants1-mt-&lt;Gebruiker&gt;.database.windows.net*
2. Klik op Database engine **verbinden...** > **Database Engine...**:

   ![catalogusserver](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Demo referenties zijn: Login = *ontwikkelaar,* Wachtwoord = *\@P zwaard1*

    De onderstaande afbeelding toont de login voor de *database per tenantpatroon.*
    ![verbinding](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Herhaal stap 2-3 en maak verbinding met de catalogusserver (zie hieronder voor specifieke servernamen op basis van het geselecteerde databasehuurpatroon)
    - **Zelfstandige toepassing:** *&lt;catalogus-sa- Gebruiker&gt;.database.windows.net*
    - **Database per tenant:** *&lt;catalogus-dpt- Gebruiker&gt;.database.windows.net*
    - **Multi-tenant database:** *catalogus-mt-&lt;Gebruiker&gt;.database.windows.net*


Na het succesvol aansluiten van u moet u alle servers te zien. Uw lijst met databases kan verschillen, afhankelijk van de tenants die u hebt ingericht.

De onderstaande afbeelding toont de aanmelding voor het *databasepatroon per tenant.*

![objectverkenner](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Volgende stappen
- [Implementeer de Wingtip Tickets SaaS Standalone Applicatie](saas-standaloneapp-get-started-deploy.md)
- [De Wingtip Tickets SaaS-database per tenanttoepassing implementeren](saas-dbpertenant-get-started-deploy.md)
- [De Toepassing Van De Vleugeltip Tickets SaaS Multi-tenant Database implementeren](saas-multitenantdb-get-started-deploy.md)

