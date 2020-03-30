---
title: Zelfstudie met meerdere tenant SaaS
description: Nieuwe tenants inrichten en catalogiseren met behulp van het zelfstandige toepassingspatroon
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
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133125"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Nieuwe tenants inrichten en catalogiseren met behulp van het saas-patroon van de tenant

Dit artikel heeft betrekking op het inrichten en catalogiseren van nieuwe tenants met behulp van de zelfstandige app per tenant SaaS-patroon.
Dit artikel bestaat uit twee belangrijke delen:
* Conceptuele bespreking van het inrichten en catalogiseren van nieuwe huurders
* Een zelfstudie die voorbeeldpowershell-code markeert die de inrichting en catalogisering bereikt
    * De zelfstudie maakt gebruik van de Wingtip Tickets voorbeeld SaaS applicatie, aangepast aan de standalone app per tenant patroon.

## <a name="standalone-application-per-tenant-pattern"></a>Zelfstandige toepassing per tenantpatroon

De standalone app per tenant patroon is een van de verschillende patronen voor multi-tenant SaaS-toepassingen.  In dit patroon is voor elke tenant een zelfstandige app ingericht. De toepassing bestaat uit componenten op toepassingsniveau en een SQL-database.  Elke tenant-app kan worden geïmplementeerd in het abonnement van de leverancier.  Azure biedt ook een [beheerd programma voor toepassingen](https://docs.microsoft.com/azure/managed-applications/overview) waarin een app kan worden geïmplementeerd in het abonnement van een tenant en wordt beheerd door de leverancier namens de tenant.

   ![app-per-tenant-patroon](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Bij het implementeren van een toepassing voor een tenant worden de app en database ingericht in een nieuwe brongroep die voor de tenant is gemaakt.  Met behulp van afzonderlijke brongroepen isoleert elke tenant de toepassingsbronnen en kunnen ze onafhankelijk worden beheerd. Binnen elke brongroep is elke toepassingsinstantie geconfigureerd om rechtstreeks toegang te krijgen tot de bijbehorende database.  Dit verbindingsmodel contrasteert met andere patronen die een catalogus gebruiken om verbindingen tussen de app en de database te bemiddelen.  En omdat er geen resourcesharing is, moet elke tenantdatabase worden ingericht met voldoende resources om de piekbelasting te verwerken. Dit patroon wordt meestal gebruikt voor SaaS-toepassingen met minder huurders, waarbij er een sterke nadruk ligt op het isoleren van huurders en minder nadruk op resourcekosten.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Een tenantcatalogus gebruiken met de toepassing per tenantpatroon

Hoewel de app en database van elke tenant volledig geïsoleerd zijn, kunnen verschillende beheer- en analysescenario's werken tussen tenants.  Als u bijvoorbeeld een schemawijziging toepast voor een nieuwe release van de toepassing, moet het schema van elke tenantdatabase worden gewijzigd. Rapportage- en analysescenario's vereisen mogelijk ook toegang tot alle tenantdatabases, ongeacht waar ze worden geïmplementeerd.

   ![app-per-tenant-patroon](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

De tenantcatalogus bevat een toewijzing tussen een tenant-id en een tenantdatabase, waardoor een id kan worden opgelost naar een server en databasenaam.  In de Wingtip SaaS-app wordt de tenant-id berekend als een hash van de tenantnaam, hoewel andere schema's kunnen worden gebruikt.  Hoewel zelfstandige toepassingen de catalogus niet nodig hebben om verbindingen te beheren, kan de catalogus worden gebruikt om andere acties naar een set tenantdatabases te koppelen. Elastic Query kan bijvoorbeeld de catalogus gebruiken om de set databases te bepalen waarover query's worden gedistribueerd voor rapportage met meerdere tenant's.

## <a name="elastic-database-client-library"></a>Elastische databaseclientbibliotheek

In de wingtip-voorbeeldtoepassing wordt de catalogus geïmplementeerd door de shardbeheerfuncties van de [Elastic Database Client Library](sql-database-elastic-database-client-library.md) (EDCL).  De bibliotheek stelt een toepassing in staat om een shardkaart te maken, te beheren en te gebruiken die in een database is opgeslagen. In het voorbeeld van Wingtip Tickets wordt de catalogus opgeslagen in de database van de *tenantcatalogus.*  De shard brengt een tenantsleutel in kaart voor de shard (database) waarin de gegevens van die tenant worden opgeslagen.  EDCL-functies beheren een *globale shardkaart* die is opgeslagen in tabellen in de *database met de tenantcatalogus* en een lokale *shardkaart* die in elke shard is opgeslagen.

EDCL-functies kunnen worden aangeroepen vanuit toepassingen of PowerShell-scripts om de vermeldingen in de shardkaart te maken en te beheren. Andere EDCL-functies kunnen worden gebruikt om de set shards op te halen of verbinding te maken met de juiste database voor een bepaalde tenantsleutel.

> [!IMPORTANT]
> Bewerk de gegevens in de catalogusdatabase of de lokale shardkaart niet rechtstreeks in de tenantdatabases. Directe updates worden niet ondersteund vanwege het hoge risico op gegevensbeschadiging. Bewerk in plaats daarvan de kaartgegevens met alleen EDCL API's.

## <a name="tenant-provisioning"></a>Huurdervoorziening

Elke tenant vereist een nieuwe Azure-brongroep, die moet worden gemaakt voordat resources erin kunnen worden ingericht. Zodra de brongroep bestaat, kan een Azure Resource Management-sjabloon worden gebruikt om de toepassingsonderdelen en de database te implementeren en vervolgens de databaseverbinding te configureren. Als u het databaseschema wilt initialiseren, kan de sjabloon een bacpac-bestand importeren.  Als alternatief kan de database worden gemaakt als een kopie van een 'template' database.  De database wordt vervolgens verder bijgewerkt met de eerste locatiegegevens en geregistreerd in de catalogus.

## <a name="tutorial"></a>Zelfstudie

In deze zelfstudie leert u het volgende:

* Een catalogus inrichten
* De voorbeeldtenantdatabases registreren die u eerder in de catalogus hebt geïmplementeerd
* Een extra tenant inrichten en registreren in de catalogus

Een Azure Resource Manager-sjabloon wordt gebruikt om de toepassing te implementeren en te configureren, de tenantdatabase te maken en vervolgens een bacpac-bestand te importeren om deze te initialiseren. De importaanvraag kan enkele minuten in de wachtrij staan voordat deze wordt uitgevoerd.

Aan het einde van deze zelfstudie hebt u een set zelfstandige tenanttoepassingen, waarbij elke database in de catalogus is geregistreerd.

## <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* De drie voorbeeldtenant-apps worden geïmplementeerd. Zie [Het saas-toepassingspatroon van Wingtip Tickets implementeren en verkennen](saas-standaloneapp-get-started-deploy.md)om deze apps in minder dan vijf minuten te implementeren.

## <a name="provision-the-catalog"></a>De catalogus inrichten

In deze taak leert u hoe u de catalogus indient die wordt gebruikt om alle tenantdatabases te registreren. U gaat het volgende doen:

* **Indik de catalogusdatabase** met behulp van een Azure-resourcebeheersjabloon. De database wordt geïnitialiseerd door het importeren van een bacpac-bestand.
* **Registreer de voorbeeldtenant-apps** die u eerder hebt geïmplementeerd.  Elke tenant wordt geregistreerd met behulp van een sleutel die is opgebouwd uit een hash van de tenantnaam.  De tenantnaam wordt ook opgeslagen in een extensietabel in de catalogus.

1. Open in PowerShell ISE *...\Learning Modules\UserConfig.psm* en werk de ** \<gebruikerswaarde\> ** bij naar de waarde die u hebt gebruikt bij het implementeren van de drie voorbeeldtoepassingen.  **Sla het bestand op.**
1. Open in PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* en stel **$Scenario in = 1**. Implementeer de tenantcatalogus en registreer de vooraf gedefinieerde tenants.

1. Voeg een breekpunt toe door de cursor `& $PSScriptRoot\New-Catalog.ps1`ergens op de lijn te plaatsen waarop staat, en druk vervolgens op **F9**.

    ![een breekpunt instellen voor tracering](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Voer het script uit door op **F5**te drukken.
1.  Nadat de uitvoering van het script is gestopt bij het breekpunt, drukt u op **F11** om in het script Nieuw-Catalogus.ps1 te stappen.
1.  Traceer de uitvoering van het script met de menuopties Foutopsporing, F10 en F11, om over of in aangeroepen functies te stappen.
    *   Zie Tips voor het werken met [PowerShell-scripts voor](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)meer informatie over het debuggen van PowerShell-scripts.

Zodra het script is voltooid, bestaat de catalogus en worden alle voorbeeldtenants geregistreerd.

Kijk nu eens naar de resources die u hebt gemaakt.

1. Open de [Azure-portal](https://portal.azure.com/) en blader door de brongroepen.  Open de **wingtip-sa-catalog-\<user\> ** resource group en noteer de catalogusserver en -database.
1. Open de database in de portal en selecteer *Gegevensverkenner* in het linkermenu.  Klik op de opdracht Aanmelding en voer het wachtwoord = **P\@zwaard1 in.**


1. Bekijk het schema van de *tenantcatalogusdatabase.*
   * De objecten `__ShardManagement` in het schema worden allemaal geleverd door de Elastic Database Client Library.
   * De `Tenants` tabel `TenantsExtended` en weergave zijn uitbreidingen toegevoegd in het voorbeeld die laten zien hoe u de catalogus uitbreiden om extra waarde te bieden.
1. Voer de `SELECT * FROM dbo.TenantsExtended`query uit, .

   ![gegevensverkenner](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Als alternatief voor het gebruik van de Data Explorer u verbinding maken met de database van SQL Server Management Studio. Om dit te doen, verbinding maken met de server wingtip-


    Houd er rekening mee dat u gegevens niet rechtstreeks in de catalogus moet bewerken - gebruik altijd de shardbeheer-API's.

## <a name="provision-a-new-tenant-application"></a>Een nieuwe tenanttoepassing inrichten

In deze taak leert u hoe u één tenanttoepassing indient. U gaat het volgende doen:

* **Maak een nieuwe resourcegroep** voor de tenant.
* **Indik de toepassing en database** in de nieuwe brongroep met behulp van een Azure-resourcebeheersjabloon.  Deze actie omvat het initialiseren van de database met algemene schema- en referentiegegevens door een bacpac-bestand te importeren.
* **Initialiseer de database met basistenantgegevens.** Deze actie omvat het opgeven van het locatietype, dat de foto bepaalt die wordt gebruikt als achtergrond op de website van de gebeurtenissen.
* **Registreer de database in de catalogusdatabase**.

1. Open in PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* en stel **$Scenario in = 2**. De tenantcatalogus implementeren en de vooraf gedefinieerde tenants registreren

1. Voeg een breekpunt toe in het script door de `& $PSScriptRoot\New-TenantApp.ps1`cursor overal op regel 49 te plaatsen met de tekst , en druk vervolgens op **F9**.
1. Voer het script uit door op **F5**te drukken.
1.  Nadat de uitvoering van het script is gestopt bij het breekpunt, drukt u op **F11** om in het script Nieuw-Catalogus.ps1 te stappen.
1.  Traceer de uitvoering van het script met de menuopties Foutopsporing, F10 en F11, om over of in aangeroepen functies te stappen.

Nadat de huurder is ingericht, wordt de evenementenwebsite van de nieuwe huurder geopend.

   ![rood esdoorn het rennen](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Vervolgens u de nieuwe resources inspecteren die zijn gemaakt in de Azure-portal.

   ![rode esdoorn racing middelen](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Als u de facturering wilt stoppen, verwijdert u resourcegroepen

Wanneer u klaar bent met het verkennen van het voorbeeld, verwijdert u alle resourcegroepen die u hebt gemaakt om de bijbehorende facturering te stoppen.

## <a name="additional-resources"></a>Aanvullende bronnen

- Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenant's voor](saas-tenancy-app-design-patterns.md)meer informatie over SaaS-databasetoepassingen met meerdere tenants.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Hoe de Wingtip Tickets SaaS Standalone Applicatie te implementeren.
> * Over de servers en databases die deel uitmaken van de app.
> * Voorbeeldbronnen verwijderen om gerelateerde facturering te stoppen.

U nagaan hoe de catalogus wordt gebruikt om verschillende cross-tenantscenario's te ondersteunen met behulp van de database-per-tenant-versie van de [Wingtip Tickets SaaS-toepassing.](saas-dbpertenant-wingtip-app-overview.md)
