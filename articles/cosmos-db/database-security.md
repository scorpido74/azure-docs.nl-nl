---
title: Databasebeveiliging - Azure Cosmos DB
description: Ontdek hoe Azure Cosmos DB databasebeveiliging en gegevensbeveiliging biedt voor uw gegevens.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: c92f045f2c8d4443d596697596363bda3d0df975
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985283"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Beveiliging in Azure Cosmos DB - overzicht

In dit artikel worden de aanbevolen procedures voor databasebeveiliging en de belangrijkste functies van Azure Cosmos DB beschreven, om u te helpen bij het voorkomen, detecteren en reageren op databaseschendingen.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Nieuwe informatie in Azure Cosmos DB-beveiliging

Versleuteling in rust is nu beschikbaar voor documenten en back-ups die zijn opgeslagen in Azure Cosmos DB in alle Azure-regio's. Versleuteling in rust wordt automatisch toegepast voor zowel nieuwe als bestaande klanten in deze regio's. Het is niet nodig om iets te configureren; en u krijgt dezelfde grote latentie, doorvoer, beschikbaarheid en functionaliteit als voorheen met het voordeel dat uw gegevens veilig en veilig zijn met versleuteling in rust.

## <a name="how-do-i-secure-my-database"></a>Hoe beveilig ik mijn database

Gegevensbeveiliging is een gedeelde verantwoordelijkheid tussen u, de klant en uw databaseprovider. Afhankelijk van de databaseprovider die u kiest, kan de mate van verantwoordelijkheid die u draagt variëren. Als u kiest voor een on-premises oplossing, moet u alles bieden, van end-point bescherming tot fysieke beveiliging van uw hardware - wat geen gemakkelijke taak is. Als u kiest voor een PaaS-clouddatabaseprovider zoals Azure Cosmos DB, krimpt uw zorggebied aanzienlijk. De volgende afbeelding, ontleend aan de [gedeelde verantwoordelijkheden](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) van Microsoft voor cloud computing-whitepaper, laat zien hoe uw verantwoordelijkheid afneemt bij een PaaS-provider zoals Azure Cosmos DB.

![Verantwoordelijkheden voor klant- en databaseaanbieders](./media/database-security/nosql-database-security-responsibilities.png)

Het vorige diagram toont cloudbeveiligingscomponenten op hoog niveau, maar over welke items moet u zich specifiek zorgen maken voor uw databaseoplossing? En hoe kun je oplossingen met elkaar vergelijken?

Wij raden de volgende checklist van eisen aan waarop databasesystemen te vergelijken:

- Netwerkbeveiliging en firewall-instellingen
- Gebruikersverificatie en fijnkorrelige gebruikersbesturingselementen
- Mogelijkheid om gegevens wereldwijd te repliceren voor regionale storingen
- Mogelijkheid om van het ene datacenter naar het andere te mislukken
- Lokale gegevensreplicatie binnen een datacenter
- Automatische back-ups van gegevens
- Herstel van verwijderde gegevens uit back-ups
- Gevoelige gegevens beschermen en isoleren
- Controle op aanvallen
- Reageren op aanvallen
- Mogelijkheid om gegevens te geo-fenceen om te voldoen aan beperkingen voor gegevensbeheer
- Fysieke bescherming van servers in beveiligde datacenters
- Certificeringen

En hoewel het misschien voor de hand liggend lijkt, herinneren recente [grootschalige databaselekken](https://thehackernews.com/2017/01/mongodb-database-security.html) ons aan het eenvoudige maar kritieke belang van de volgende vereisten:

- Gepatchte servers die up-to-date worden gehouden
- HTTPS by default/TLS-versleuteling
- Beheerdersaccounts met sterke wachtwoorden

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hoe beveiligt Azure Cosmos DB mijn database

Laten we eens terugkijken naar de voorgaande lijst - hoeveel van die beveiligingsvereisten biedt Azure Cosmos DB? Allemaal.

Laten we in detail ingaan op elk.

|Veiligheidseis|De beveiligingsaanpak van Azure Cosmos DB|
|---|---|
|Netwerkbeveiliging|Het gebruik van een IP-firewall is de eerste beveiligingslaag om uw database te beveiligen. Azure Cosmos DB ondersteunt beleidsgestuurde IP-gebaseerde toegangscontroles voor inkomende firewallondersteuning. De IP-gebaseerde toegangscontroles zijn vergelijkbaar met de firewallregels die worden gebruikt door traditionele databasesystemen, maar ze worden uitgebreid zodat een Azure Cosmos-databaseaccount alleen toegankelijk is vanaf een goedgekeurde set machines of cloudservices. Meer informatie in het ondersteuningsartikel [azure cosmos DB-firewall.](firewall-support.md)<br><br>Azure Cosmos DB stelt u in staat om een specifiek IP-adres (168.61.48.0), een IP-bereik (168.61.48.0/8) en combinaties van IP-waarden in te schakelen. <br><br>Alle aanvragen afkomstig van machines buiten deze toegestane lijst worden geblokkeerd door Azure Cosmos DB. Aanvragen van goedgekeurde machines en cloudservices moeten vervolgens het verificatieproces voltooien om toegangsbeheer tot de resources te krijgen.<br><br> U [virtuele netwerkservicetags](../virtual-network/service-tags-overview.md) gebruiken om netwerkisolatie te bereiken en uw Azure Cosmos DB-bronnen te beschermen tegen het algemene internet. Gebruik servicetags in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld AzureCosmosDB) op te geven in het juiste bron- of doelveld van een regel, u het verkeer voor de bijbehorende service toestaan of weigeren.|
|Autorisatie|Azure Cosmos DB gebruikt hmac (hash-based message authentication code) voor autorisatie. <br><br>Elk verzoek wordt gehasht met behulp van de geheime accountsleutel en de daaropvolgende basis-64 gecodeerde hash wordt verzonden met elke oproep naar Azure Cosmos DB. Om de aanvraag te valideren, gebruikt de Azure Cosmos DB-service de juiste geheime sleutel en eigenschappen om een hash te genereren en vergelijkt deze de waarde met de toepassing in de aanvraag. Als de twee waarden overeenkomen, is de bewerking geautoriseerd en wordt de aanvraag verwerkt, anders is er een autorisatiefout en wordt de aanvraag afgewezen.<br><br>U een [hoofdsleutel](secure-access-to-data.md#master-keys)of een [resourcetoken](secure-access-to-data.md#resource-tokens) gebruiken waarmee fijnmazige toegang tot een bron zoals een document kan worden gebruikt.<br><br>Meer informatie over [Het beveiligen van toegang tot Azure Cosmos DB-bronnen](secure-access-to-data.md).|
|Gebruikers en machtigingen|Met behulp van de hoofdsleutel voor het account u gebruikersbronnen en machtigingsbronnen per database maken. Een resourcetoken is gekoppeld aan een machtiging in een database en bepaalt of de gebruiker toegang heeft (lezen-schrijven, alleen-lezen of geen toegang) tot een toepassingsbron in de database. Toepassingsbronnen omvatten container, documenten, bijlagen, opgeslagen procedures, triggers en UDF's. Het resourcetoken wordt vervolgens gebruikt tijdens verificatie om toegang tot de bron te bieden of te weigeren.<br><br>Meer informatie over [Het beveiligen van toegang tot Azure Cosmos DB-bronnen](secure-access-to-data.md).|
|RBAC (Active Directory-integratie)| U ook toegang tot het Cosmos-account, de database, de container en de aanbiedingen (doorvoer) bieden of beperken met behulp van Toegangsbeheer (IAM) in de Azure-portal. IAM biedt op rollen gebaseerd toegangscontrole en integreert met Active Directory. U ingebouwde rollen of aangepaste rollen voor personen en groepen gebruiken. Zie [artikel over Active Directory-integratie](role-based-access-control.md) voor meer informatie.|
|Globale replicatie|Azure Cosmos DB biedt kant-en-klare wereldwijde distributie, waarmee u uw gegevens met één klik op de knop repliceren naar een van de wereldwijde datacenters van Azure. Met globale replicatie u wereldwijd schalen en toegang tot uw gegevens met een lage latentie over de hele wereld bieden.<br><br>In het kader van beveiliging zorgt wereldwijde replicatie voor gegevensbescherming tegen regionale storingen.<br><br>Zie [Gegevens wereldwijd distribueren](distribute-data-globally.md) voor meer informatie.|
|Regionale failovers|Als u uw gegevens in meer dan één datacenter hebt gerepliceerd, rolt Azure Cosmos DB automatisch over uw bewerkingen als een regionaal datacenter offline gaat. U een geprioriteerd overzicht maken van failoverregio's met behulp van de regio's waarin uw gegevens worden gerepliceerd. <br><br>Meer informatie in [Regionale failovers in Azure Cosmos DB](high-availability.md).|
|Lokale replicatie|Zelfs binnen één datacenter repliceert Azure Cosmos DB automatisch gegevens voor hoge beschikbaarheid, zodat u de keuze hebt uit [consistentieniveaus.](consistency-levels.md) Deze replicatie garandeert een SLA [van](https://azure.microsoft.com/support/legal/sla/cosmos-db) 99,99% beschikbaarheid voor alle accounts met één regio en alle accounts met meerdere regio's met ontspannen consistentie en 99,999% leesbeschikbaarheid op alle databaseaccounts met meerdere regio's.|
|Geautomatiseerde online back-ups|Azure Cosmos-databases worden regelmatig geback-upt en opgeslagen in een georedundante winkel. <br><br>Meer informatie over [automatische online back-up en herstel met Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).|
|Verwijderde gegevens herstellen|De geautomatiseerde online back-ups kunnen worden gebruikt om gegevens te herstellen die u mogelijk per ongeluk tot ~ 30 dagen na de gebeurtenis hebt verwijderd. <br><br>Meer informatie over [automatische online back-up en herstel met Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)|
|Gevoelige gegevens beschermen en isoleren|Alle gegevens in de regio's die worden vermeld in Wat is nieuw? is nu versleuteld in rust.<br><br>Persoonlijke gegevens en andere vertrouwelijke gegevens kunnen worden geïsoleerd tot specifieke containers en read-write, of alleen-lezen toegang kan worden beperkt tot specifieke gebruikers.|
|Monitor voor aanvallen|Door het gebruik van [audit logging en activiteit logs](logging.md), u uw account te controleren op normale en abnormale activiteit. U bekijken welke bewerkingen zijn uitgevoerd op uw resources, wie de bewerking heeft geïnitieerd, wanneer de bewerking heeft plaatsgevonden, de status van de bewerking en nog veel meer, zoals weergegeven in de schermafbeelding na deze tabel.|
|Reageren op aanvallen|Zodra u contact hebt opgenomen met Azure-ondersteuning om een potentiële aanval te melden, wordt een 5-stappenincidentresponsproces gestart. Het doel van het 5-stappenproces is om de normale servicebeveiliging en -bewerkingen zo snel mogelijk te herstellen nadat een probleem is gedetecteerd en een onderzoek wordt gestart.<br><br>Meer informatie in [Microsoft Azure Security Response in de cloud](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geo-fencing|Azure Cosmos DB zorgt voor data governance voor soevereine regio's (bijvoorbeeld Duitsland, China, US Gov).|
|Beschermde voorzieningen|Gegevens in Azure Cosmos DB worden opgeslagen op SSD's in de beveiligde datacenters van Azure.<br><br>Meer informatie in [microsoft-datacenters](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-versleuteling|Alle verbindingen met Azure Cosmos DB ondersteunen HTTPS. Azure Cosmos DB ondersteunt ook TLS 1.2.<br>Het is mogelijk om een minimale TLS-versie server-kant af te dwingen. Neem hiervoor contact [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)op met .|
|Versleuteling 'at rest'|Alle gegevens die zijn opgeslagen in Azure Cosmos DB worden in rust versleuteld. Meer informatie over [Azure Cosmos DB-versleuteling in rust](./database-encryption-at-rest.md)|
|Gepatchte servers|Als beheerde database elimineert Azure Cosmos DB de noodzaak om servers, die voor u worden gedaan, automatisch te beheren en te patchen.|
|Beheerdersaccounts met sterke wachtwoorden|Het is moeilijk te geloven dat we deze vereiste zelfs moeten vermelden, maar in tegenstelling tot sommige van onze concurrenten is het onmogelijk om een administratief account te hebben zonder wachtwoord in Azure Cosmos DB.<br><br> Beveiliging via TLS en HMAC secret based authentication wordt standaard ingebakken.|
|Certificeringen voor beveiliging en gegevensbescherming| Voor de meest recente lijst met certificeringen ziet u de algemene [Azure Compliance-site](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) en het nieuwste [Azure Compliance Document](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) met alle certificeringen (zoeken naar Cosmos). Voor een meer gerichte lees check out de 25 april 2018 post [Azure #CosmosDB: Secure, private, compliant that includes SOCS 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High, and vele anderen.

In de volgende schermafbeelding ziet u hoe u controlelogboekregistratie en activiteitslogboeken gebruiken om uw account te controleren: ![Activiteitslogboeken voor Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Toegang tot Azure Cosmos DB-gegevens beveiligen](secure-access-to-data.md)voor meer informatie over hoofdsleutels en brontokens.

Zie [Diagnostische logboekregistratie](logging.md)van Azure Cosmos DB voor meer informatie over controlelogboekregistratie.

Zie [Azure Trust Center](https://azure.microsoft.com/support/trust-center/)voor meer informatie over Microsoft-certificeringen.