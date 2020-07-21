---
title: Beheerbewerkingen
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over Azure SQL Managed instance Management-bewerkingen voor de duur en aanbevolen procedures.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531489"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Overzicht van Azure SQL Managed instance Management-bewerkingen
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>Wat zijn beheer bewerkingen?
Azure SQL Managed instance biedt beheer bewerkingen die u kunt gebruiken om automatisch nieuwe beheerde instanties te implementeren, exemplaar-eigenschappen bij te werken en instanties te verwijderen wanneer ze niet meer nodig zijn.

Voor de ondersteuning van [implementaties in azure Virtual Networks](../../virtual-network/virtual-network-for-azure-services.md) en het bieden van isolatie en beveiliging voor klanten, is SQL Managed instance afhankelijk van [virtuele clusters](connectivity-architecture-overview.md#high-level-connectivity-architecture). Een virtueel cluster vertegenwoordigt een speciale set geïsoleerde virtuele machines die zijn geïmplementeerd in het subnet van het virtuele netwerk van de klant. In wezen resulteert elke implementatie van een beheerd exemplaar in een leeg subnet in een nieuw virtueel cluster buildout.

Volgende bewerkingen in geïmplementeerde beheerde instanties kunnen ook gevolgen hebben voor het onderliggende virtuele cluster. Deze bewerkingen zijn van invloed op de duur van beheer bewerkingen, zoals het implementeren van aanvullende virtuele machines wordt geleverd met een overhead die moet worden overwogen wanneer u nieuwe implementaties of updates voor bestaande beheerde exemplaren plant.

Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Implementatie van instanties (nieuwe instantie maken).
- Update van instantie (instantie-eigenschappen wijzigen, zoals vCores of gereserveerde opslag).
- Instantie verwijderen.

## <a name="management-operations-duration"></a>Beheer bewerkingen duur
Normaal gesp roken hebben bewerkingen op virtuele clusters het langst. De duur van de bewerkingen op virtuele clusters is afhankelijk van de waarden die u doorgaans kunt verwachten, op basis van de bestaande telemetrie-gegevens van de service:

- **Virtueel cluster maken**: maken is een synchrone stap in het beheer van exemplaren. **90% van de bewerkingen zijn voltooid over vier uur**.
- **Verg Roten/verkleinen van een virtueel cluster (uitbrei ding of krimpen)**: uitbrei ding is een synchrone stap, terwijl het comprimeren wordt asynchroon uitgevoerd (zonder invloed op de duur van de beheer bewerkingen voor exemplaren). **90% van de cluster uitbreidingen eindigen in minder dan 2,5 uur**.
- **Virtueel cluster verwijderen**: de verwijdering is een asynchrone stap, maar kan ook [hand matig worden gestart](virtual-cluster-delete.md) op een leeg virtueel cluster. in dat geval wordt het synchroon uitgevoerd. **90% van verwijderde virtuele clusters is in 1,5 uur voltooid**.

Daarnaast kan het beheer van instanties ook een van de bewerkingen op gehoste data bases bevatten, wat resulteert in langere duur:

- **Database bestanden van Azure Storage koppelen**: een synchrone stap, zoals Compute (vCores), of het omhoog of omlaag schalen van opslag in de servicelaag algemeen. **90% van deze bewerkingen zijn voltooid in 5 minuten**.
- AlwaysOn- **beschikbaarheids groep moet worden geseedd**: een synchrone stap, zoals Compute (vCores), of opslag schalen in de laag bedrijfskritiek en in het wijzigen van de servicelaag van Algemeen in bedrijfskritiek (of andersom). De duur van deze bewerking is evenredig met de totale database grootte en de huidige database activiteit (aantal actieve trans acties). Data base-activiteit bij het bijwerken van een exemplaar kan aanzienlijk verschillen veroorzaken in de totale duur. **90% van deze bewerkingen worden uitgevoerd om 220 GB/uur of hoger**.

De volgende tabel bevat een overzicht van de bewerkingen en typische totale duur:

|Categorie  |Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|---------|
|**Implementatie** |Eerste instantie in een leeg subnet|Virtueel cluster maken|90% van de bewerkingen zijn voltooid over vier uur.|
|Implementatie |Eerste exemplaar van een andere hardware-generatie in een niet-leeg subnet (bijvoorbeeld eerste generatie 5-exemplaar in een subnet met exemplaren van de generatie 4)|Virtueel cluster maken *|90% van de bewerkingen zijn voltooid over vier uur.|
|Implementatie |Eerste instantie maken van vier vCores in een leeg of niet-leeg subnet|Virtueel cluster maken * *|90% van de bewerkingen zijn voltooid over vier uur.|
|Implementatie |Het maken van de volgende instantie binnen het niet-lege subnet (2e, 3e, enz.)|Verg Roten/verkleinen van virtueel cluster|90% van de bewerkingen zijn voltooid over 2,5 uur.|
|**Bijwerken** |Wijziging van instantie-eigenschap (beheerders wachtwoord, Azure AD-aanmelding, Azure Hybrid Benefit vlag)|N.v.t.|Maxi maal 1 minuut.|
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Algemeen servicelaag)|Database bestanden koppelen|90% van de bewerkingen zijn voltooid over vijf minuten.|
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Bedrijfskritiek servicelaag)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid om 2,5 uur en tijd om alle data bases te seeden (220 GB/uur).|
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Algemeen)|-Verg Roten/verkleinen van virtueel cluster<br>-Database bestanden koppelen|90% van de bewerkingen zijn voltooid over 2,5 uur.|
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Bedrijfskritiek)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid om 2,5 uur en tijd om alle data bases te seeden (220 GB/uur).|
|Bijwerken |Wijziging van de instantie-servicelaag (Algemeen Bedrijfskritiek en omgekeerd)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid om 2,5 uur en tijd om alle data bases te seeden (220 GB/uur).|
|**Verwijdering**|Verwijdering van exemplaar|Back-ups van staart vastleggen voor alle data bases|90% bewerkingen zijn Maxi maal 1 minuut voltooid.<br>Opmerking: als het laatste exemplaar van het subnet wordt verwijderd, wordt de verwijdering van het virtuele cluster na 12 uur door deze bewerking gepland. * *|
|Verwijdering|Virtueel cluster verwijderen (als door de gebruiker geïnitieerde bewerking)|Virtueel cluster verwijderen|90% van de bewerkingen zijn Maxi maal 1,5 uur voltooid.|

\*Virtueel cluster is gebouwd per generatie van hardware.

\*\*12 uur is de huidige configuratie, maar dit kan in de toekomst veranderen, dus neem geen vaste afhankelijkheid op. Als u eerder een virtueel cluster moet verwijderen (bijvoorbeeld voor het vrijgeven van het subnet), raadpleegt u [een subnet verwijderen na het verwijderen van een beheerd exemplaar](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Beschik baarheid van exemplaren tijdens beheer bewerkingen

SQL Managed instance **is beschikbaar tijdens update bewerkingen**, met uitzonde ring van een korte downtime die wordt veroorzaakt door de failover die aan het einde van de update plaatsvindt. Het duurt meestal Maxi maal 10 seconden, zelfs in het geval van langdurige langlopende trans acties, dankzij het [versnelde herstel van de data base](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Het is niet raadzaam om Compute of opslag van een door Azure SQL beheerd exemplaar te schalen of de servicelaag tegelijkertijd te wijzigen met de langlopende trans acties (gegevens importeren, gegevens verwerkings taken, index Rebuild, enzovoort). Voor de data base-failover die aan het einde van de bewerking wordt uitgevoerd, worden alle lopende trans acties geannuleerd.

SQL Managed instance is niet beschikbaar voor client toepassingen tijdens implementatie-en verwijderings bewerkingen.

## <a name="management-operations-cross-impact"></a>Beheer bewerkingen, cross-effect

Beheer bewerkingen op een beheerd exemplaar kunnen invloed hebben op andere beheer bewerkingen van de instanties die binnen hetzelfde virtuele cluster zijn geplaatst:

- Voor **langlopende herstel bewerkingen** in een virtueel cluster worden andere bewerkingen voor het maken of schalen van instanties in hetzelfde subnet opgeslagen.<br/>**Voor beeld:** Als er een langlopende herstel bewerking is en er in hetzelfde subnet een aanvraag voor maken of schalen wordt uitgevoerd, duurt het langer voordat deze aanvraag is voltooid, omdat er wordt gewacht tot de herstel bewerking is voltooid voordat deze wordt voortgezet.
    
- **Een volgende bewerking voor het maken of schalen van een exemplaar** wordt in de wacht geplaatst door een eerder geïnitieerde instantie voor het maken of schalen van een exemplaar waarbij het formaat van het virtuele cluster wordt gestart.<br/>**Voor beeld:** Als er meerdere aanvragen voor maken en/of schalen worden uitgevoerd in hetzelfde subnet onder hetzelfde virtuele cluster, en een ervan het formaat van een virtueel cluster initieert, worden alle aanvragen die 5 + minuten na de wijziging van het virtuele cluster zijn ingediend, langer dan verwacht, omdat deze aanvragen moeten wachten tot de grootte is voltooid voordat het proces wordt hervat.

- **Create/Scale-bewerkingen die zijn verzonden in een venster van vijf minuten** , worden parallel in batch verwerkt en uitgevoerd.<br/>**Voor beeld:** Er wordt slechts één grootte van het virtuele cluster uitgevoerd voor alle bewerkingen die zijn ingediend in een periode van vijf minuten (gemeten vanaf het moment waarop de eerste bewerkings aanvraag wordt uitgevoerd). Als een andere aanvraag meer dan vijf minuten na het verzenden van de eerste wordt verzonden, wordt gewacht tot de grootte van het virtuele cluster is voltooid voordat de uitvoering wordt gestart.

> [!IMPORTANT]
> Beheer bewerkingen die in de wacht worden gezet omdat een andere bewerking wordt uitgevoerd, worden automatisch hervat wanneer aan de voor waarden wordt voldaan. Er is geen actie van de gebruiker nodig om tijdelijk onderbroken beheer bewerkingen te hervatten.

## <a name="canceling-management-operations"></a>Beheer bewerkingen annuleren

De volgende tabel bevat een overzicht van de mogelijkheid om specifieke beheer bewerkingen en typische totale duur te annuleren:

Categorie  |Bewerking  |Geannuleerd  |Geschatte annulerings duur  |
|---------|---------|---------|---------|
|Implementatie |Instantie maken |Nee |  |
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Algemeen) |Nee |  |
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Bedrijfskritiek) |Ja |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Algemeen) |Ja |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Bedrijfskritiek) |Ja |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Bijwerken |Wijziging van de instantie-servicelaag (Algemeen Bedrijfskritiek en omgekeerd) |Ja |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Verwijderen |Verwijdering van exemplaar |Nee |  |
|Verwijderen |Virtueel cluster verwijderen (als door de gebruiker geïnitieerde bewerking) |Nee |  |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u de beheer bewerking wilt annuleren, gaat u naar de Blade overzicht en klikt u op meldingen venster van de actieve bewerking. Aan de rechter kant verschijnt een scherm met de doorlopende bewerking en er wordt een knop weer gegeven voor het annuleren van de bewerking. Nadat u de eerste keer hebt geklikt, wordt u gevraagd opnieuw te klikken en bevestigen dat u de bewerking wilt annuleren.

[![Bewerking annuleren](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

Nadat een annulerings aanvraag is verzonden en verwerkt, ontvangt u een melding als de annulering is geslaagd of niet.

Als de ingediende annulerings aanvraag is voltooid, wordt de beheer bewerking in een paar minuten geannuleerd, wat resulteert in een fout.

![Het resultaat van de bewerking wordt geannuleerd](./media/management-operations-overview/canceling-operation-result.png)

Als de annulerings aanvraag mislukt of de knop Annuleren niet actief is, betekent dit dat de beheer bewerking een niet-Annuleer bare status heeft en dat deze binnen een paar minuten wordt voltooid. De beheer bewerking wordt voortgezet totdat deze is voltooid.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als Azure PowerShell nog niet is geïnstalleerd, raadpleegt u [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps).

Als u de beheer bewerking wilt annuleren, moet u de naam van de beheer bewerking opgeven. Daarom moet u eerst de opdracht Get gebruiken voor het ophalen van de lijst met bewerkingen en vervolgens een specifieke bewerking annuleren.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Zie [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) en [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)voor gedetailleerde uitleg over opdrachten.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).

Als u de beheer bewerking wilt annuleren, moet u de naam van de beheer bewerking opgeven. Daarom moet u eerst de opdracht Get gebruiken voor het ophalen van de lijst met bewerkingen en vervolgens een specifieke bewerking annuleren.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Zie [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest)voor gedetailleerde opdracht uitleg.

---

## <a name="next-steps"></a>Volgende stappen
- Zie [Quick Start Guide (Engelstalig](instance-create-quickstart.md)) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [algemene SQL-functies](../database/features-comparison.md)voor een lijst met functies en vergelijkingen.
- Zie [vnet-configuratie voor SQL Managed instance](connectivity-architecture-overview.md)voor meer informatie over vnet-configuraties.
- Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor een Snelstartgids die een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-upbestand.
- Voor een zelf studie over het gebruik van Azure Database Migration Service voor migratie raadpleegt u [migratie van SQL-beheerde exemplaren met behulp van database Migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
