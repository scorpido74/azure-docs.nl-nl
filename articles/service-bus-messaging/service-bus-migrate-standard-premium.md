---
title: Azure Service Bus-naamruimten migreren - standaard naar premium
description: Handleiding om de migratie van bestaande standaardnaamruimten voor Azure Service Bus naar premium toe te staan
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385024"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Bestaande Azure Service Bus-standaardnaamruimten migreren naar de premiumlaag

Voorheen bood Azure Service Bus alleen naamruimten aan op de standaardlaag. Naamruimten zijn multi-tenant opstellingen die zijn geoptimaliseerd voor omgevingen met lage doorvoer en ontwikkelaars. De premium laag biedt speciale resources per naamruimte voor voorspelbare latentie en verhoogde doorvoer tegen een vaste prijs. De premium-laag is geoptimaliseerd voor hoge doorvoer- en productieomgevingen waarvoor extra bedrijfsfuncties nodig zijn.

In dit artikel wordt beschreven hoe u bestaande standaardnaamruimten voor standaardlagen migreert naar de premiumlaag.  

>[!WARNING]
> Migratie is bedoeld om standaardnaamruimten voor servicebus te upgraden naar de premiumlaag. De migratietool ondersteunt geen downgraden.

Enkele van de punten op te merken:

- Deze migratie is bedoeld om te gebeuren op zijn plaats, wat betekent dat bestaande afzender- en ontvangertoepassingen **geen wijzigingen in code of configuratie vereisen.** De bestaande verbindingstekenreeks wijst automatisch naar de nieuwe premium naamruimte.
- De **premium** naamruimte mag **geen entiteiten** in zich hebben om de migratie te laten slagen.
- Alle **entiteiten** in de standaardnaamruimte **worden tijdens** het migratieproces gekopieerd naar de premium naamruimte.
- Migratie ondersteunt **1.000 entiteiten per berichteneenheid** op de premiumlaag. Als u wilt bepalen hoeveel berichteneenheden u nodig hebt, begint u met het aantal entiteiten dat u hebt op uw huidige standaardnaamruimte.
- U niet direct migreren van **basislaag** naar **premium- laag,** maar u dit indirect doen door eerst van basis naar standaard te migreren en vervolgens in de volgende stap van de standaard naar de premium te migreren.

## <a name="migration-steps"></a>Migratiestappen

Sommige voorwaarden zijn gekoppeld aan het migratieproces. Maak kennis met de volgende stappen om de kans op fouten te verminderen. In deze stappen wordt het migratieproces beschreven en worden de stapsgewijze details weergegeven in de volgende secties.

1. Maak een nieuwe premium naamruimte.
1. Koppel de standaard- en premium naamruimten aan elkaar.
1. Synchronisatie (copy-over) entiteiten van de standaard naar de premium naamruimte.
1. Bega de migratie.
1. Laat entiteiten in de standaardnaamruimte leeglopen met de naam post-migratie van de naamruimte.
1. Verwijder de standaardnaamruimte.

>[!IMPORTANT]
> Nadat de migratie is vastgelegd, u toegang krijgen tot de oude standaardnaamruimte en de wachtrijen en abonnementen leeglopen. Nadat de berichten zijn uitgelekt, kunnen ze worden verzonden naar de nieuwe premium naamruimte die door de ontvangertoepassingen moet worden verwerkt. Nadat de wachtrijen en abonnementen zijn afgetapt, raden we u aan de oude standaardnaamruimte te verwijderen.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migreren met azure CLI of PowerShell

Voer de volgende stappen uit om de standaardnaamruimte van Service Bus te migreren naar premium met het azure CLI- of PowerShell-gereedschap.

1. Maak een nieuwe servicebus premium naamruimte. U verwijzen naar de [Azure Resource Manager-sjablonen](service-bus-resource-manager-namespace.md) of [de Azure-portal gebruiken.](service-bus-create-namespace-portal.md) Zorg ervoor dat u **premium** selecteert voor de parameter **serviceBusSku.**

1. Stel de volgende omgevingsvariabelen in om de migratieopdrachten te vereenvoudigen.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > De post-migratiealias/-naam (post_migration_dns_name) wordt gebruikt om toegang te krijgen tot de oude standaardnaamruimte na migratie. Gebruik dit om de wachtrijen en de abonnementen af te voeren en vervolgens de naamruimte te verwijderen.

1. Koppel de standaard- en premium naamruimten en start de synchronisatie met de volgende opdracht:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Controleer de status van de migratie met de volgende opdracht:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    De migratie wordt als voltooid beschouwd wanneer u de volgende waarden ziet:

    * MigrationState = "Actief"
    * in behandeling in behandeling ReplicatiesOperationsCount = 0
    * provisioningState = "Geslaagd"

    Met deze opdracht wordt ook de migratieconfiguratie weergegeven. Controleer of de waarden correct zijn ingesteld. Controleer ook de premium naamruimte in de portal om ervoor te zorgen dat alle wachtrijen en onderwerpen zijn gemaakt, en dat ze overeenkomen met wat er bestond in de standaard naamruimte.

1. De migratie vastleggen door de volgende volledige opdracht uit te voeren:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migreren met de Azure-portal

Migratie met behulp van de Azure-portal heeft dezelfde logische stroom als migreren met behulp van de opdrachten. Volg deze stappen om te migreren met behulp van de Azure-portal.

1. Selecteer **Migreren naar premium**in het navigatiemenu in het linkerdeelvenster . **Navigation** Klik **op De** knop Aan de slag om door te gaan naar de volgende pagina.
    ![Migratiebestemmingspagina][]

1. Volledige **installatie**.
   ![Naamruimte instellen][]
   1. Maak en wijs de premium naamruimte toe om de bestaande standaardnaamruimte naar te migreren.
        ![Naamruimte instellen - premium naamruimte maken][]
   1. Kies een **naam postmigratie**. U gebruikt deze naam om toegang te krijgen tot de standaardnaamruimte nadat de migratie is voltooid.
        ![Naamruimte instellen - naam na migratie kiezen][]
   1. Selecteer **'Volgende'** om door te gaan.
1. Entiteiten synchroniseren tussen de standaard- en premiumnaamruimten.
    ![Naamruimte instellen - entiteiten synchroniseren - starten][]

   1. Selecteer **Synchronisatie starten** om te beginnen met het synchroniseren van de entiteiten.
   1. Selecteer **Ja** in het dialoogvenster om de synchronisatie te bevestigen en te starten.
   1. Wacht tot de synchronisatie is voltooid. De status is beschikbaar op de statusbalk.
        ![Naamruimte instellen - entiteiten synchroniseren - voortgang][]
        >[!IMPORTANT]
        > Als u de migratie om welke reden dan ook wilt afbreken, raadpleegt u de afbouwstroom in het gedeelte Veelgestelde vragen van dit document.
   1. Nadat de synchronisatie is voltooid, selecteert u **Volgende** onder aan de pagina.

1. Wijzigingen bekijken op de overzichtspagina. Selecteer **Migratie voltooien** om van naamruimten te wisselen en de migratie te voltooien.
    ![Naamruimte wijzigen - switchmenu][]  
    De bevestigingspagina wordt weergegeven wanneer de migratie is voltooid.
    ![Switch naamruimte - succes][]

## <a name="caveats"></a>Waarschuwingen

Sommige functies van azure servicebusstandaard worden niet ondersteund door de Azure Service Bus Premium-laag. Deze zijn door het ontwerp, omdat de premium tier biedt speciale middelen voor voorspelbare doorvoer en latentie.

Hier is een lijst van functies die niet worden ondersteund door Premium en hun mitigatie -

### <a name="express-entities"></a>Express-entiteiten

   Express-entiteiten die geen berichtgegevens aan opslag verbinden, worden niet ondersteund in Premium. Speciale resources zorgden voor een aanzienlijke verbetering van de doorvoer en zorgden ervoor dat de gegevens blijven bestaan, zoals wordt verwacht van elk bedrijfsberichtensysteem.

   Tijdens de migratie worden al uw expresentiteiten in uw standaardnaamruimte gemaakt op de Premium-naamruimte als een niet-uitdrukkelijke entiteit.

   Als u ARM-sjablonen (Azure Resource Manager) gebruikt, moet u ervoor zorgen dat u de vlag 'enableExpress' uit de implementatieconfiguratie verwijdert, zodat uw geautomatiseerde werkstromen zonder fouten worden uitgevoerd.

### <a name="partitioned-entities"></a>Gepartitioneerde entiteiten

   Gepartitioneerde entiteiten werden ondersteund in de standaardlaag om een betere beschikbaarheid te bieden in een instelling met meerdere tenants. Met het verstrekken van speciale resources die beschikbaar zijn per naamruimte in de Premium-laag, is dit niet langer nodig.

   Tijdens de migratie wordt elke partitieentiteit in de standaardnaamruimte gemaakt op de Premium-naamruimte als een niet-partitieentiteit.

   Als uw ARM-sjabloon 'partitionering' instelt op 'true' voor een specifieke wachtrij of onderwerp, wordt deze genegeerd door de makelaar.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="what-happens-when-the-migration-is-committed"></a>Wat gebeurt er als de migratie wordt gepleegd?

Nadat de migratie is vastgelegd, wijst de verbindingstekenreeks die naar de standaardnaamruimte wees, naar de premium naamruimte.

De verzender- en ontvangertoepassingen verbreken de verbinding met de standaard naamruimte en maken automatisch opnieuw verbinding met de premium naamruimte.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Wat moet ik doen nadat de standaard voor premium migratie is voltooid?

De standaard naar premium migratie zorgt ervoor dat de entiteit metagegevens zoals onderwerpen, abonnementen en filters worden gekopieerd van de standaardnaamruimte naar de premium naamruimte. De berichtgegevens die zijn vastgelegd in de standaardnaamruimte, worden niet gekopieerd van de standaardnaamruimte naar de premium naamruimte.

De standaardnaamruimte kan een aantal berichten hebben die zijn verzonden en vastgelegd terwijl de migratie aan de gang was. Giet deze berichten handmatig af van de standaard Naamruimte en stuur ze handmatig naar de premium Naamruimte. Als u de berichten handmatig wilt afvoeren, gebruikt u een console-app of een script dat de standaardnaamruimteentiteiten afvoert met behulp van de DNS-naam na migratie die u in de migratieopdrachten hebt opgegeven. Stuur deze berichten naar de premium naamruimte, zodat ze kunnen worden verwerkt door de ontvangers.

Nadat de berichten zijn verwijderd, verwijdert u de standaardnaamruimte.

>[!IMPORTANT]
> Nadat de berichten uit de standaardnaamruimte zijn verwijderd, verwijdert u de standaardnaamruimte. Dit is belangrijk omdat de verbindingstekenreeks die in eerste instantie naar de standaardnaamruimte verwees, nu verwijst naar de premium naamruimte. Je hebt de standaard Naamruimte niet meer nodig. Als u de standaardnaamruimte die u hebt gemigreerd, wordt de verwarring in de ruimte voor later verminderd.

### <a name="how-much-downtime-do-i-expect"></a>Hoeveel downtime verwacht ik?

Het migratieproces is bedoeld om de verwachte downtime voor de toepassingen te verminderen. Downtime wordt verminderd door de verbindingstekenreeks te gebruiken die de verzender- en ontvangertoepassingen gebruiken om naar de nieuwe premium naamruimte te wijzen.

De downtime die wordt ervaren door de toepassing is beperkt tot de tijd die nodig is om de DNS-vermelding bij te werken om te wijzen op de premium naamruimte. Downtime is ongeveer 5 minuten.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Moet ik tijdens de migratie configuratiewijzigingen aanbrengen?

Nee, er zijn geen code- of configuratiewijzigingen nodig om de migratie uit te voeren. De verbindingstekenreeks die verzend- en ontvangertoepassingen gebruiken om toegang te krijgen tot de standaardnaamruimte, wordt automatisch toegewezen om als alias voor de premium naamruimte te fungeren.

### <a name="what-happens-when-i-abort-the-migration"></a>Wat gebeurt er als ik de migratie afbreek?

De migratie kan worden afgebroken `Abort` met de opdracht of met behulp van de Azure-portal.

#### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Stroom afbreken -][]
![synchronisatie-voltooiingsstroom afbreken - voltooid][]

Wanneer het migratieproces wordt afgebroken, wordt het proces afgebroken van het kopiëren van de entiteiten (onderwerpen, abonnementen en filters) van de standaard naar de premium naamruimte en wordt de koppeling afgebroken.

De verbindingstekenreeks wordt niet bijgewerkt om naar de premium naamruimte te wijzen. Uw bestaande toepassingen blijven werken zoals ze deden voordat u met de migratie begon.

Het verwijdert echter niet de entiteiten op de premium naamruimte of verwijdert de premium naamruimte. Verwijder de entiteiten handmatig als u hebt besloten niet verder te gaan met de migratie.

>[!IMPORTANT]
> Als u besluit de migratie af te breken, verwijdert u de premium naamruimte die u voor de migratie hebt ingericht, zodat er geen kosten in rekening worden gebracht voor de resources.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Ik wil de berichten niet hoeven aftappen. Wat moet ik doen?

Er kunnen berichten zijn die worden verzonden door de afzendertoepassingen en die zijn vastgelegd voor de opslag op de standaard naamruimte terwijl de migratie plaatsvindt en net voordat de migratie wordt vastgelegd.

Tijdens de migratie worden de werkelijke berichtgegevens/payload niet gekopieerd van de standaard naar de premium naamruimte. De berichten moeten handmatig worden afgevoerd en vervolgens naar de premium naamruimte worden verzonden.

Als u echter migreren tijdens een gepland onderhouds-/huishoudvenster en u de berichten niet handmatig wilt aftappen en verzenden, voert u de volgende stappen uit:

1. Stop de afzendertoepassingen. De ontvangertoepassingen verwerken de berichten die zich momenteel in de standaardnaamruimte bevinden en zullen de wachtrij leeglopen.
1. Nadat de wachtrijen en abonnementen in de standaard naamruimte leeg zijn, volgt u de eerder beschreven procedure om de migratie van de standaard naar de premiumnaamruimte uit te voeren.
1. Nadat de migratie is voltooid, u de afzendertoepassingen opnieuw starten.
1. De afzenders en ontvangers maken nu automatisch verbinding met de premium naamruimte.

    >[!NOTE]
    > U hoeft de ontvangertoepassingen voor de migratie niet te stoppen.
    >
    > Nadat de migratie is voltooid, worden de ontvangertoepassingen losgekoppeld van de standaardnaamruimte en worden ze automatisch verbinding gemaakt met de premium naamruimte.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [verschillen tussen standaard- en premium berichten.](./service-bus-premium-messaging.md)
* Meer informatie over de [aspecten hoge beschikbaarheid en geo-disaster recovery voor servicebuspremium.](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Migratiebestemmingspagina]: ./media/service-bus-standard-premium-migration/1.png
[Naamruimte instellen]: ./media/service-bus-standard-premium-migration/2.png
[Naamruimte instellen - premium naamruimte maken]: ./media/service-bus-standard-premium-migration/3.png
[Naamruimte instellen - naam na migratie kiezen]: ./media/service-bus-standard-premium-migration/4.png
[Naamruimte instellen - entiteiten synchroniseren - starten]: ./media/service-bus-standard-premium-migration/5.png
[Naamruimte instellen - entiteiten synchroniseren - voortgang]: ./media/service-bus-standard-premium-migration/8.png
[Naamruimte wijzigen - switchmenu]: ./media/service-bus-standard-premium-migration/9.png
[Switch naamruimte - succes]: ./media/service-bus-standard-premium-migration/12.png

[Stroom afbreken - synchronisatie afbreken]: ./media/service-bus-standard-premium-migration/abort1.png
[Stroom afbreken - voltooid]: ./media/service-bus-standard-premium-migration/abort3.png
