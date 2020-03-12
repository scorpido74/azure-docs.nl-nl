---
title: Beveiliging - Azure Cosmos DB-database
description: Meer informatie over hoe Azure Cosmos DB biedt beveiliging en gegevens Databasebeveiliging voor uw gegevens.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0c97d70ea3e5c7fdd14b0f97c5e393359f2b948e
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087231"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Beveiliging in Azure Cosmos DB-overzicht

In dit artikel worden de aanbevolen procedures voor databasebeveiliging en de belangrijkste functies van Azure Cosmos DB beschreven, om u te helpen bij het voorkomen, detecteren en reageren op databaseschendingen.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Wat is er nieuw in Azure Cosmos DB beveiliging

Versleuteling-at-rest is nu beschikbaar voor documenten en back-ups die zijn opgeslagen in Azure Cosmos DB in alle Azure-regio's. Versleuteling-at-rest wordt automatisch toegepast voor zowel nieuwe als bestaande klanten in deze regio's. Er is niet nodig voor het configureren van alles zijn: en u krijgt de dezelfde geweldige latentie, doorvoer, beschikbaarheid en functionaliteit als voordat u met het voordeel van de wetenschap dat uw gegevens is veilig en beveiligen met versleuteling-at-rest.

## <a name="how-do-i-secure-my-database"></a>Mijn data base Hoe kan ik beveiligen

Beveiliging van gegevens is een gedeelde verantwoordelijkheid tussen u, de klant en uw databaseprovider. Afhankelijk van de database-provider die u kiest, kan variëren van de hoeveelheid verantwoordelijkheid die u uitvoeren. Als u een on-premises oplossing kiest, moet u alles van eindpuntbescherming fysieke beveiliging van uw hardware - dit geen eenvoudige taak is opgeven. Als u ervoor geen databaseprovider van PaaS cloud zoals Azure Cosmos DB kiest, wordt het gebied van belang aanzienlijk kleiner. De volgende afbeelding, uitgeleend van de gedeelde verantwoordelijkheden van micro soft voor het technisch document van [Cloud Computing](https://aka.ms/sharedresponsibility) , laat zien hoe uw verantwoordelijkheid afneemt met een Paas-provider zoals Azure Cosmos db.

![Klant- en database-provider verantwoordelijkheden](./media/database-security/nosql-database-security-responsibilities.png)

Het vorige diagram toont op hoog niveau cloud security-onderdelen, maar welke items moet u zorgen te maken over specifiek voor uw databaseoplossing? En hoe kunt u oplossingen met elkaar vergelijken?

U wordt aangeraden de volgende controlelijst voor vereisten waarop u wilt vergelijken databasesystemen:

- Beveiliging van het netwerk en firewall-instellingen
- Verificatie via gebruikersnaam en goed korrelig gebruikersbesturingselementen
- Mogelijkheid om gegevens wereldwijd voor regionale storingen te repliceren
- Mogelijkheid tot failover van het ene Data Center naar het andere
- Replicatie van lokale gegevens binnen een datacenter
- Automatische back-ups
- Het herstellen van verwijderde gegevens van back-ups
- Beveiligen en isoleren van de gevoelige gegevens
- Bewaking voor aanvallen
- Reageren op aanvallen
- Mogelijkheid tot geo-omheining gegevens om te voldoen aan de beperkingen voor het beheer van gegevens
- Fysieke beveiliging van servers in beveiligde datacenters
- Certificeringen

Het lijkt erop dat de recente [grootschalige data base ongevoelig](https://thehackernews.com/2017/01/mongodb-database-security.html) is, maar dat het een eenvoudige, maar essentiële belang rijk is voor de volgende vereisten:

- Patched servers die up-to-date blijven
- HTTPS door standaard/SSL-versleuteling
- Beheerdersaccounts met sterke wachtwoorden

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hoe Azure Cosmos DB mijn data base beveiligen?

Laten we kijken weer de voorgaande lijst - hoeveel van de beveiligingsvereisten van de Azure Cosmos DB biedt? Elke enkel een.

Laten we even stilstaan bij elkaar in detail.

|Beveiligingsvereiste|Aanpak van Azure Cosmos-DB-beveiliging|
|---|---|
|Netwerkbeveiliging|Met behulp van een IP-firewall, is de eerste laag van beveiliging voor het beveiligen van uw database. Azure Cosmos DB ondersteunt basis van IP-gebaseerd toegangsbeheer voor firewallondersteuning van de inkomende-beleid. De op IP gebaseerde toegangs elementen zijn vergelijkbaar met de firewall regels die worden gebruikt door traditionele database systemen, maar ze worden uitgebreid zodat een Azure Cosmos-database account alleen toegankelijk is vanaf een goedgekeurde set machines of Cloud Services. Meer informatie vindt u in Azure Cosmos DB artikel over [firewall ondersteuning](firewall-support.md) .<br><br>Azure Cosmos DB kunt u mogelijk om een specifiek IP-adres (168.61.48.0), een IP-adresbereik (168.61.48.0/8) en combinaties van IP-adressen en -bereiken. <br><br>Alle aanvragen die afkomstig zijn van computers buiten deze toegestane lijst geblokkeerd door Azure Cosmos DB. Aanvragen van goedgekeurde machines en cloudservices vervolgens vult het verificatieproces uit als u wilt toegang krijgen tot de resources.<br><br> U kunt [service tags van het virtuele netwerk](../virtual-network/service-tags-overview.md) gebruiken om netwerk isolatie te bezorgen en uw Azure Cosmos DB-bronnen te beveiligen via het internet. Gebruik service tags in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de service label (bijvoorbeeld AzureCosmosDB) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.|
|Autorisatie|Azure Cosmos DB maakt gebruik van hash-gebaseerde bericht verificatiecode op te geven (HMAC) voor autorisatie. <br><br>Elke aanvraag wordt opgedeeld met behulp van de geheime sleutel en de volgende base-64 gecodeerde hash wordt verzonden met elke aanroep naar Azure Cosmos DB. Voor het valideren van de aanvraag, de Azure Cosmos DB-service maakt gebruik van de juiste geheime sleutel en de eigenschappen voor het genereren van een hash en vervolgens de waarde met de in de aanvraag worden vergeleken. Als de twee waarden overeenkomen met de bewerking is geautoriseerd en de aanvraag wordt verwerkt, anders wordt er is een Autorisatiefout en de aanvraag wordt afgewezen.<br><br>U kunt een [hoofd sleutel](secure-access-to-data.md#master-keys)of een [bron token](secure-access-to-data.md#resource-tokens) gebruiken om nauw keurige toegang tot een bron, zoals een document, toe te staan.<br><br>Meer informatie over [het beveiligen van de toegang tot Azure Cosmos DB-resources](secure-access-to-data.md).|
|Gebruikers en machtigingen|U kunt met behulp van de hoofd sleutel voor het account gebruikers resources en machtigings bronnen maken per data base. Een bron token is gekoppeld aan een machtiging in een Data Base en bepaalt of de gebruiker toegang heeft (lezen/schrijven, alleen-lezen of geen toegang) tot een toepassings bron in de data base. Toepassingsresources omvatten container, documenten, bijlagen, opgeslagen procedures, triggers en UDF's. De resourcetoken wordt vervolgens gebruikt tijdens de verificatie om te geven of weigeren van toegang tot de resource.<br><br>Meer informatie over [het beveiligen van de toegang tot Azure Cosmos DB-resources](secure-access-to-data.md).|
|Active directory-integratie (RBAC)| U kunt de toegang tot het Cosmos-account, de data base, de container en de aanbiedingen (door Voer) ook opgeven of beperken met behulp van toegangs beheer (IAM) in de Azure Portal. IAM biedt op rollen gebaseerd toegangsbeheer en kan worden geïntegreerd met Active Directory. U kunt ingebouwde rollen of aangepaste rollen gebruiken voor individuen en groepen. Zie [Active Directory Integration](role-based-access-control.md) -artikel voor meer informatie.|
|Globale replicatie|Azure Cosmos DB biedt kant en klare wereldwijde distributie, waarmee u uw gegevens gerepliceerd naar een van de world wide-datacenters van Azure met één klik op een knop. Globale replicatie kunt u wereldwijd schalen en lage latentie toegang bieden tot uw gegevens over de hele wereld.<br><br>In de context van beveiliging gegevensbeveiliging globale replicatie tegen regionale fouten.<br><br>Zie [Gegevens wereldwijd distribueren](distribute-data-globally.md) voor meer informatie.|
|Regionale failovers|Als u uw gegevens in meer dan één datacenter zijn gerepliceerd, Azure Cosmos DB automatisch wordt getotaliseerd via uw bewerkingen moet een regionaal datacenter offline gaan. U kunt een geprioriteerde lijst met failover-regio's met behulp van de regio's waarin uw gegevens worden gerepliceerd. <br><br>Meer informatie vindt u in [regionale failovers in azure Cosmos DB](high-availability.md).|
|Lokale replicatie|Zelfs binnen één Data Center, Azure Cosmos DB automatisch gegevens repliceren voor hoge Beschik baarheid, zodat u de gewenste [consistentie niveaus](consistency-levels.md)krijgt. Deze replicatie garandeert een [Sla](https://azure.microsoft.com/support/legal/sla/cosmos-db) van 99,99% voor alle accounts voor één regio en alle accounts voor meerdere regio's met beperkte consistentie en 99,999% Lees Beschik baarheid voor alle database accounts voor meerdere regio's.|
|Automatische online back-ups|Voor Azure Cosmos-data bases wordt regel matig een back-up gemaakt en opgeslagen in een geografisch redundante opslag. <br><br>Meer informatie vindt u in [Automatische online back-ups en herstellen met Azure Cosmos DB](online-backup-and-restore.md).|
|Terugzetten van verwijderde gegevens|De geautomatiseerde online back-ups kunnen worden gebruikt om gegevens die u hebt mogelijk per ongeluk verwijderd tot ongeveer 30 dagen na de gebeurtenis te herstellen. <br><br>Meer informatie vindt u in [Automatische online back-ups en herstellen met Azure Cosmos DB](online-backup-and-restore.md)|
|Beveiligen en isoleren van de gevoelige gegevens|Alle gegevens in de regio's die worden vermeld in wat is er nieuw? is nu op rest versleuteld.<br><br>Persoonlijke gegevens en andere vertrouwelijke gegevens kunnen worden geïsoleerd in een specifieke container en lezen / schrijven, of alleen-lezen toegang kan worden beperkt tot specifieke gebruikers.|
|Monitor voor aanvallen|Met [controle logboek registratie en activiteiten logboeken](logging.md)kunt u uw account controleren op normale en abnormale activiteiten. U kunt weergeven welke bewerkingen zijn uitgevoerd op uw resources, die de bewerking heeft gestart wanneer de bewerking is opgetreden, de status van de bewerking, en nog veel meer zoals weergegeven in de schermopname onder deze tabel.|
|Reageren op aanvallen|Nadat u hebt verbinding gemaakt met de ondersteuning van Azure om aan te melden bij een aanval, wordt een reactie op incidenten 5-stap-proces gestart. Het doel van de 5-proces is normaal servicebeveiliging en bewerkingen zo snel mogelijk herstellen nadat er een probleem is gedetecteerd en een onderzoek is gestart.<br><br>Meer informatie vindt u in [Microsoft Azure Security Response in de Cloud](https://aka.ms/securityresponsepaper).|
|Geofencing|Azure Cosmos DB garandeert gegevensbeheer voor soevereine regio's (bijvoorbeeld Duitsland, China, VS (overheid)).|
|Beveiligde faciliteiten|Gegevens in Azure Cosmos DB worden opgeslagen op SSD's in de beveiligde datacenters van Azure.<br><br>Meer informatie in [micro soft Global Data Centers](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-versleuteling|Alle verbindingen met Azure Cosmos DB ondersteuning voor HTTPS. Azure Cosmos DB ondersteunt ook TLS 1,2.<br>Het is mogelijk om een minimale TLS-versie aan de server zijde af te dwingen. Als u dit wilt doen, neemt u contact op met [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Versleuteling 'at rest'|Alle gegevens die zijn opgeslagen in Azure Cosmos DB is versleuteld in rust. Meer informatie over [Azure Cosmos DB versleuteling in rust](./database-encryption-at-rest.md)|
|Gecorrigeerde servers|Als een beheerde database hoeft Azure Cosmos DB te beheren en vullen van de servers, die automatisch voor u heeft gedaan.|
|Beheerdersaccounts met sterke wachtwoorden|Het is moeilijk te geloven moeten we ook nog deze vereiste, maar in tegenstelling tot sommige van onze concurrenten, is het niet mogelijk om een Administrator-account zonder een wachtwoord in Azure Cosmos DB.<br><br> Beveiliging via SSL en HMAC geheime gebaseerde verificatie is standaard sparen.|
|Beveiliging en bescherming-certificeringen| Voor de meest recente lijst met certificeringen raadpleegt u de algemene Azure- [nalevings site](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , evenals het meest recente [Azure-nalevings document](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) met alle certificeringen (zoek naar Cosmos). Lees voor meer informatie lezen van de 25 april 2018 post [Azure #CosmosDB: Secure, private, conform SOCS 1/2 type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High en veel andere.

De volgende scherm afbeelding laat zien hoe u controle logboeken en activiteiten Logboeken kunt gebruiken om uw account te controleren: ![activiteiten logboeken voor Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Volgende stappen

Zie [toegang tot Azure Cosmos DB gegevens beveiligen](secure-access-to-data.md)voor meer informatie over hoofd sleutels en bron tokens.

Zie [Azure Cosmos DB Diagnostic logging](logging.md)(Engelstalig) voor meer informatie over controle logboek registratie.

Zie [Vertrouwenscentrum van Azure](https://azure.microsoft.com/support/trust-center/)voor meer informatie over micro soft-certificeringen.