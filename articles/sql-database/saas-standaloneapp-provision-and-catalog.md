---
title: Multi tenant SaaS-zelf studie-Azure SQL Database
description: Nieuwe tenants inrichten en catalogiseren met behulp van het patroon van de zelfstandige toepassing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: de1007aac3988f2ea78b9d1b7b1de19b862f196a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691953"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Nieuwe tenants inrichten en catalogiseren met behulp van de toepassing per Tenant SaaS-patroon

Dit artikel behandelt het inrichten en catalogiseren van nieuwe tenants met behulp van de zelfstandige app per Tenant SaaS-patroon.
Dit artikel heeft twee belang rijke onderdelen:
* Conceptuele bespreking van het inrichten en catalogiseren van nieuwe tenants
* Een zelf studie voor het markeren van de Power shell-voorbeeld code die het inrichten en catalogiseren uitvoert
    * In de zelf studie wordt gebruikgemaakt van de SaaS-voorbeeld toepassing Wingtip tickets, aangepast aan de zelfstandige app per Tenant patroon.

## <a name="standalone-application-per-tenant-pattern"></a>Zelfstandige toepassing per Tenant patroon

De zelfstandige app per Tenant patroon is een van de verschillende patronen voor SaaS-toepassingen met meerdere tenants.  In dit patroon wordt een zelfstandige app ingericht voor elke Tenant. De toepassing omvat onderdelen op toepassings niveau en een SQL database.  Elke Tenant-app kan worden geïmplementeerd in het abonnement van de leverancier.  Azure biedt ook een programma voor [beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/overview) waarin een app kan worden geïmplementeerd in het abonnement van een Tenant en door de leverancier kan worden beheerd voor de naam van de Tenant. 

   ![app-per-Tenant patroon](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Wanneer u een toepassing implementeert voor een Tenant, worden de app en de data base ingericht in een nieuwe resource groep die voor de Tenant is gemaakt.  Door afzonderlijke resource groepen te gebruiken, worden de toepassings resources van elke Tenant geïsoleerd en kunnen ze onafhankelijk worden beheerd. Binnen elke resource groep is elk toepassings exemplaar geconfigureerd voor toegang tot de bijbehorende data base.  Dit verbindings model contrasteert met andere patronen die een catalogus gebruiken om verbindingen tussen de app en de data base te brokern.  En omdat er geen resources worden gedeeld, moet elke Tenant database worden ingericht met voldoende bronnen om de piek belasting te kunnen afhandelen. Dit patroon wordt meestal gebruikt voor SaaS-toepassingen met minder tenants, waarbij de nadruk ligt op de isolatie van tenants en minder nadruk op resource kosten.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Een Tenant catalogus met de toepassing per Tenant patroon gebruiken

Hoewel de app en de data base van elke Tenant volledig zijn geïsoleerd, kunnen verschillende beheer-en analyse scenario's worden toegepast op alle tenants.  Voor het Toep assen van een schema wijziging voor een nieuwe release van de toepassing, moet het schema van elke Tenant database worden gewijzigd. Voor rapportage-en analyse scenario's is mogelijk ook toegang nodig tot alle Tenant databases, ongeacht waar ze worden geïmplementeerd.

   ![app-per-Tenant patroon](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

De Tenant catalogus bevat een toewijzing tussen een Tenant-id en een Tenant database, zodat een id kan worden omgezet naar een server-en database naam.  In de Wingtip SaaS-app wordt de Tenant-id berekend als een hash van de Tenant naam, hoewel andere schema's kunnen worden gebruikt.  Hoewel voor zelfstandige toepassingen de catalogus niet nodig is voor het beheren van verbindingen, kan de catalogus worden gebruikt om andere acties te beperken tot een set Tenant databases. Elastische Query's kunnen bijvoorbeeld de catalogus gebruiken om de set data bases te bepalen waarin query's worden gedistribueerd voor rapportage over meerdere tenants.

## <a name="elastic-database-client-library"></a>Clientbibliotheek voor Elastic Database

In de voorbeeld toepassing Wingtip wordt de catalogus geïmplementeerd door de Shard-beheer functies van de [Elastic database-client bibliotheek](sql-database-elastic-database-client-library.md) (EDCL).  Met de bibliotheek kan een toepassing een Shard-toewijzing maken, beheren en gebruiken die is opgeslagen in een Data Base. In het Wingtip tickets-voor beeld wordt de catalogus opgeslagen in de catalogus database van de *Tenant* .  De Shard wijst een Tenant sleutel toe aan de Shard (data base) waarin de gegevens van de Tenant zijn opgeslagen.  Met EDCL-functies beheert u een *globale Shard-map* die is opgeslagen in tabellen in de *Tenant catalogus* database en een *lokale Shard-toewijzing* die is opgeslagen in elke Shard.

EDCL-functies kunnen worden aangeroepen vanuit toepassingen of Power shell-scripts voor het maken en beheren van de vermeldingen in de Shard-toewijzing. Andere EDCL-functies kunnen worden gebruikt om de set Shards op te halen of om verbinding te maken met de juiste Data Base voor de opgegeven Tenant sleutel. 

> [!IMPORTANT]
> Bewerk de gegevens in de catalogus database of de lokale Shard-toewijzing in de Tenant databases niet rechtstreeks. Directe updates worden niet ondersteund vanwege het hoge risico op beschadiging van gegevens. Bewerk in plaats daarvan de toewijzings gegevens met behulp van EDCL-Api's.

## <a name="tenant-provisioning"></a>Tenant inrichting 

Elke Tenant vereist een nieuwe Azure-resource groep, die moet worden gemaakt voordat er resources in kunnen worden ingericht. Zodra de resource groep bestaat, kunt u een Azure resource management-sjabloon gebruiken om de toepassings onderdelen en de data base te implementeren en vervolgens de database verbinding te configureren. Voor het initialiseren van het database schema, kan de sjabloon een Bacpac-bestand importeren.  De data base kan ook worden gemaakt als een kopie van een ' sjabloon '-data base.  De data base wordt vervolgens bijgewerkt met de eerste locatie gegevens en wordt geregistreerd in de catalogus.

## <a name="tutorial"></a>Zelfstudie

In deze zelfstudie leert u het volgende:

* Een catalogus inrichten
* De voor beeld-Tenant databases registreren die u eerder in de catalogus hebt geïmplementeerd
* Een extra Tenant inrichten en registreren in de catalogus

Er wordt een Azure Resource Manager sjabloon gebruikt om de toepassing te implementeren en te configureren, de Tenant database te maken en vervolgens een Bacpac-bestand te importeren om het te initialiseren. De aanvraag voor importeren kan enkele minuten duren voordat deze wordt uitgevoerd.

Aan het einde van deze zelf studie hebt u een set zelfstandige Tenant toepassingen, waarbij elke data base in de catalogus wordt geregistreerd.

## <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan: 

* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* De drie voor beelden van Tenant-apps worden geïmplementeerd. Als u deze apps in minder dan vijf minuten wilt implementeren, raadpleegt u [het patroon van de Wingtip tickets SaaS-zelfstandig toepassing implementeren en verkennen](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>De catalogus inrichten

In deze taak leert u hoe u de catalogus inricht die wordt gebruikt voor het registreren van alle Tenant databases. U gaat het volgende doen: 

* **Richt de catalogus database** in met behulp van een Azure resource management-sjabloon. De data base wordt geïnitialiseerd door het importeren van een Bacpac-bestand.  
* **Registreer de voor beelden van Tenant-apps** die u eerder hebt geïmplementeerd.  Elke Tenant wordt geregistreerd met behulp van een sleutel die is gemaakt op basis van een hash van de Tenant naam.  De naam van de Tenant wordt ook opgeslagen in een uitbreidings tabel in de catalogus.

1. Open in Power shell ISE *. ..\Learning Modules\UserConfig.psm* en werk de waarde van de **\<gebruiker\>** bij naar de waarde die u hebt gebruikt bij het implementeren van de drie voorbeeld toepassingen.  **Sla het bestand**op.  
1. Open *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* in Power shell ISE en stel **$scenario = 1**in. Implementeer de Tenant catalogus en registreer de vooraf gedefinieerde tenants.

1. Voeg een onderbrekings punt toe door de cursor ergens op de regel te plaatsen met de tekst, `& $PSScriptRoot\New-Catalog.ps1`en druk vervolgens op **F9**.

    ![een onderbrekings punt instellen voor tracering](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Voer het script uit door op **F5**te drukken. 
1.  Wanneer het uitvoeren van het script stopt bij het onderbrekings punt, drukt u op **F11** om het script New-Catalog. ps1 te Step into.
1.  De uitvoering van het script traceren met behulp van de opties voor het menu fout opsporing, F10 en F11, om over te stappen of functies te noemen.
    *   Zie [Tips voor het werken met en het opsporen van fouten in Power shell-scripts](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)voor meer informatie over fout opsporing in Power shell-scripts.

Zodra het script is voltooid, bestaat de catalogus en worden alle voor beelden van tenants geregistreerd. 

Bekijk nu de resources die u hebt gemaakt.

1. Open de [Azure Portal](https://portal.azure.com/) en blader door de resource groepen.  Open de resource groep **Wingtip-sa-Catalog-\<gebruiker\>** en noteer de catalogus server en de data base.
1. Open de data base in de portal en selecteer *Data Explorer* in het menu aan de linkerkant.  Klik op de opdracht login en voer het wacht woord in = **P\@ssword1**.


1. Verken het schema van de *tenantcatalog* -data base.  
   * De objecten in het `__ShardManagement` schema worden allemaal door de Elastic Database-client bibliotheek verschaft.
   * De `Tenants` tabel en `TenantsExtended` weer gave zijn uitbrei dingen die in het voor beeld worden toegevoegd, en laten zien hoe u de catalogus kunt uitbreiden voor extra waarde.
1. Voer de query uit `SELECT * FROM dbo.TenantsExtended`.          

   ![Data Explorer](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Als alternatief voor het gebruik van de Data Explorer kunt u vanuit SQL Server Management Studio verbinding maken met de data base. Als u dit wilt doen, maakt u verbinding met de server Wingtip- 

    
    Houd er rekening mee dat u gegevens niet rechtstreeks in de catalogus moet bewerken: gebruik altijd de Shard-beheer-Api's. 

## <a name="provision-a-new-tenant-application"></a>Een nieuwe Tenant toepassing inrichten

In deze taak leert u hoe u één Tenant toepassing inricht. U gaat het volgende doen:  

* **Maak een nieuwe resource groep** voor de Tenant. 
* **Richt de toepassing en data base** in de nieuwe resource groep in met behulp van een Azure resource management-sjabloon.  Deze actie omvat het initialiseren van de data base met algemene schema-en referentie gegevens door het importeren van een Bacpac-bestand. 
* **Initialiseer de data base met de basis gegevens van de Tenant**. Deze actie omvat het opgeven van het type locatie, waarmee de foto wordt bepaald die wordt gebruikt als achtergrond op de website van de events. 
* **Registreer de data base in de catalogus database**. 

1. Open *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* in Power shell ISE en stel **$scenario = 2**in. De Tenant catalogus implementeren en de vooraf gedefinieerde tenants registreren

1. Voeg een onderbrekings punt in het script toe door de cursor ergens op regel 49 te plaatsen met de tekst `& $PSScriptRoot\New-TenantApp.ps1`en druk vervolgens op **F9**.
1. Voer het script uit door op **F5**te drukken. 
1.  Wanneer het uitvoeren van het script stopt bij het onderbrekings punt, drukt u op **F11** om het script New-Catalog. ps1 te Step into.
1.  De uitvoering van het script traceren met behulp van de opties voor het menu fout opsporing, F10 en F11, om over te stappen of functies te noemen.

Nadat de Tenant is ingericht, wordt de website van de nieuwe Tenant geopend.

   ![rode esdoorn race](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Vervolgens kunt u de nieuwe resources die zijn gemaakt in de Azure Portal inspecteren. 

   ![race bronnen voor Red esdoorn](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Voor het stoppen van de facturering, het verwijderen van resource groepen

Wanneer u klaar bent met het verkennen van het voor beeld, verwijdert u alle resource groepen die u hebt gemaakt om de gekoppelde facturering te stoppen.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [ontwerp patronen voor SaaS-toepassingen met meerdere](saas-tenancy-app-design-patterns.md)tenants voor meer informatie over de SaaS-database toepassingen met meerdere tenants.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Hoe u de zelfstandige Wingtip tickets SaaS-toepassing implementeert.
> * Over de servers en data bases die de app vormen.
> * Voorbeeld resources verwijderen om gerelateerde facturering te stoppen.

U kunt verkennen hoe de catalogus wordt gebruikt ter ondersteuning van verschillende scenario's voor meerdere tenants met behulp van de data base-per-Tenant-versie van de [SaaS-toepassing Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md).  
