---
title: Database beveiliging-Azure Cosmos DB
description: Lees hoe Azure Cosmos DB database beveiliging en gegevens beveiliging biedt voor uw gegevens.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: 8a0ef4c5abcd4c4137a878adf6a7fff9e80c2764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273508"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Beveiliging in Azure Cosmos DB - overzicht

In dit artikel worden de aanbevolen procedures voor databasebeveiliging en de belangrijkste functies van Azure Cosmos DB beschreven, om u te helpen bij het voorkomen, detecteren en reageren op databaseschendingen.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Wat is er nieuw in Azure Cosmos DB beveiliging

Versleuteling op rest is nu beschikbaar voor documenten en back-ups die zijn opgeslagen in Azure Cosmos DB in alle Azure-regio's. Versleuteling op rest wordt automatisch toegepast voor zowel nieuwe als bestaande klanten in deze regio's. U hoeft niets te configureren. en u krijgt dezelfde geweldige latentie, door Voer, Beschik baarheid en functionaliteit, net als voorheen, met het voor deel dat u weet dat uw gegevens veilig en beveiligd zijn met versleuteling in rust.

## <a name="how-do-i-secure-my-database"></a>Mijn data base Hoe kan ik beveiligen

Gegevens beveiliging is een gedeelde verantwoordelijkheid tussen u, de klant en uw database provider. Afhankelijk van de database provider die u kiest, kan de hoeveelheid verantwoordelijkheid die u hebt, verschillen. Als u een on-premises oplossing kiest, moet u alles van de beveiliging van het eind punt op de fysieke beveiliging van uw hardware bieden. Dit is geen eenvoudige taak. Als u een PaaS hebt gekozen, zoals Azure Cosmos DB, is uw gebied van bezorgdheid aanzienlijk kleiner. De volgende afbeelding, uitgeleend van de gedeelde verantwoordelijkheden van micro soft voor het technisch document van [Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) , laat zien hoe uw verantwoordelijkheid afneemt met een Paas-provider zoals Azure Cosmos db.

![Verantwoordelijkheden van klanten en database providers](./media/database-security/nosql-database-security-responsibilities.png)

In het voor gaande diagram worden Cloud beveiligings onderdelen van hoog niveau weer gegeven, maar welke items hebt u nodig om specifiek voor uw database oplossing te zorgen? En hoe kunt u oplossingen met elkaar vergelijken?

We raden u aan de volgende controle lijst te volgen met vereisten voor het vergelijken van database systemen:

- Instellingen voor netwerk beveiliging en firewall
- Gebruikers verificatie en verfijnde gebruikers besturings elementen
- De mogelijkheid om gegevens wereld wijd te repliceren voor regionale storingen
- Mogelijkheid tot failover van het ene Data Center naar het andere
- Lokale gegevens replicatie binnen een Data Center
- Automatische gegevens back-ups
- Herstellen van verwijderde gegevens uit back-ups
- Gevoelige gegevens beveiligen en isoleren
- Bewaking voor aanvallen
- Reageren op aanvallen
- De mogelijkheid om geo-Fence-gegevens te voldoen aan de beperkingen voor gegevens beheer
- Fysieke beveiliging van servers in beveiligde data centers
- Certificeringen

Het lijkt erop dat de recente [grootschalige data base ongevoelig](https://thehackernews.com/2017/01/mongodb-database-security.html) is, maar dat het een eenvoudige, maar essentiële belang rijk is voor de volgende vereisten:

- Patched servers die up-to-date blijven
- HTTPS standaard/TLS-versleuteling
- Beheerders accounts met sterke wacht woorden

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hoe Azure Cosmos DB mijn data base beveiligen?

Laten we eens kijken naar de voor gaande lijst: hoeveel van deze beveiligings vereisten Azure Cosmos DB bieden? Elk één.

Laten we een voor een gedetailleerde beschrijving zien.

|Beveiligings vereiste|Beveiligings aanpak van Azure Cosmos DB|
|---|---|
|Netwerkbeveiliging|Het gebruik van een IP-firewall is de eerste beveiligingslaag om uw data base te beveiligen. Azure Cosmos DB ondersteunt op beleid gestuurde op IP gebaseerde toegangs controles voor binnenkomende firewall ondersteuning. De op IP gebaseerde toegangs elementen zijn vergelijkbaar met de firewall regels die worden gebruikt door traditionele database systemen, maar ze worden uitgebreid zodat een Azure Cosmos-database account alleen toegankelijk is vanaf een goedgekeurde set machines of Cloud Services. Meer informatie vindt u in Azure Cosmos DB artikel over [firewall ondersteuning](firewall-support.md) .<br><br>Azure Cosmos DB biedt u de mogelijkheid om een specifiek IP-adres (168.61.48.0), een IP-bereik (168.61.48.0/8) en combi Naties van Ip's en bereiken in te scha kelen. <br><br>Alle aanvragen die afkomstig zijn van computers die zich buiten deze toegestane lijst bevinden, worden geblokkeerd door Azure Cosmos DB. Aanvragen van goedgekeurde computers en Cloud Services moeten vervolgens het verificatie proces volt ooien om toegang te krijgen tot de resources.<br><br> U kunt [service tags van het virtuele netwerk](../virtual-network/service-tags-overview.md) gebruiken om netwerk isolatie te bezorgen en uw Azure Cosmos DB-bronnen te beveiligen via het internet. Gebruik service tags in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de service label (bijvoorbeeld AzureCosmosDB) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.|
|Autorisatie|Azure Cosmos DB gebruikt HMAC (Hash-based Message Authentication Code) voor autorisatie. <br><br>Elke aanvraag wordt gehasht met behulp van de sleutel van het geheime account en de volgende met base 64 gecodeerde hash wordt verzonden met elke aanroep van Azure Cosmos DB. Voor het valideren van de aanvraag gebruikt de Azure Cosmos DB-service de juiste geheime sleutel en eigenschappen om een hash te genereren. vervolgens wordt de waarde vergeleken met de naam in de aanvraag. Als de twee waarden overeenkomen, wordt de bewerking geautoriseerd en de aanvraag wordt verwerkt, anders is er een autorisatie fout en wordt de aanvraag geweigerd.<br><br>U kunt een [hoofd sleutel](secure-access-to-data.md#master-keys)of een [bron token](secure-access-to-data.md#resource-tokens) gebruiken om nauw keurige toegang tot een bron, zoals een document, toe te staan.<br><br>Meer informatie over [het beveiligen van de toegang tot Azure Cosmos DB-resources](secure-access-to-data.md).|
|Gebruikers en machtigingen|U kunt met behulp van de hoofd sleutel voor het account gebruikers resources en machtigings bronnen maken per data base. Een bron token is gekoppeld aan een machtiging in een Data Base en bepaalt of de gebruiker toegang heeft (lezen/schrijven, alleen-lezen of geen toegang) tot een toepassings bron in de data base. Toepassings bronnen zijn onder andere container, documenten, bijlagen, opgeslagen procedures, triggers en Udf's. Het bron token wordt vervolgens gebruikt tijdens de verificatie om toegang tot de bron te verlenen of te weigeren.<br><br>Meer informatie over [het beveiligen van de toegang tot Azure Cosmos DB-resources](secure-access-to-data.md).|
|Active Directory-integratie (RBAC)| U kunt de toegang tot het Cosmos-account, de data base, de container en de aanbiedingen (door Voer) ook opgeven of beperken met behulp van toegangs beheer (IAM) in de Azure Portal. IAM biedt op rollen gebaseerd toegangs beheer en integreert met Active Directory. U kunt ingebouwde rollen of aangepaste rollen gebruiken voor individuen en groepen. Zie [Active Directory Integration](role-based-access-control.md) -artikel voor meer informatie.|
|Globale replicatie|Azure Cosmos DB biedt kant-en-klare wereld wijde distributie, waarmee u uw gegevens kunt repliceren naar een van de wereld wijde data centers van Azure met één klik op een knop. Wereld wijde replicatie biedt u de mogelijkheid om wereld wijd te schalen en toegang te bieden tot uw gegevens over de hele wereld.<br><br>In de context van beveiliging zorgt globale replicatie voor gegevens bescherming tegen regionale storingen.<br><br>Zie [Gegevens wereldwijd distribueren](distribute-data-globally.md) voor meer informatie.|
|Regionale failovers|Als u uw gegevens in meer dan één Data Center hebt gerepliceerd, moet Azure Cosmos DB automatisch worden uitgevoerd voor uw bewerkingen. het regionale Data Center is nu offline. U kunt een overzicht van de failover-regio's maken op basis van de regio's waarin uw gegevens worden gerepliceerd. <br><br>Meer informatie vindt u in [regionale failovers in azure Cosmos DB](high-availability.md).|
|Lokale replicatie|Zelfs binnen één Data Center, Azure Cosmos DB automatisch gegevens repliceren voor hoge Beschik baarheid, zodat u de gewenste [consistentie niveaus](consistency-levels.md)krijgt. Deze replicatie garandeert een [Sla](https://azure.microsoft.com/support/legal/sla/cosmos-db) van 99,99% voor alle accounts voor één regio en alle accounts voor meerdere regio's met beperkte consistentie en 99,999% Lees Beschik baarheid voor alle database accounts voor meerdere regio's.|
|Automatische online back-ups|Voor Azure Cosmos-data bases wordt regel matig een back-up gemaakt en opgeslagen in een geografisch redundante opslag. <br><br>Meer informatie vindt u in [Automatische online back-ups en herstellen met Azure Cosmos DB](online-backup-and-restore.md).|
|Verwijderde gegevens herstellen|De geautomatiseerde online back-ups kunnen worden gebruikt om gegevens te herstellen die u per ongeluk hebt verwijderd tot meer dan 30 dagen na de gebeurtenis. <br><br>Meer informatie vindt u in [Automatische online back-ups en herstellen met Azure Cosmos DB](online-backup-and-restore.md)|
|Gevoelige gegevens beveiligen en isoleren|Alle gegevens in de regio's die worden vermeld in wat is er nieuw? is nu op rest versleuteld.<br><br>Persoons gegevens en andere vertrouwelijke gegevens kunnen worden geïsoleerd voor specifieke containers en alleen-lezen toegang, of alleen lees bewerkingen kunnen worden beperkt tot specifieke gebruikers.|
|Controleren op aanvallen|Met [controle logboek registratie en activiteiten logboeken](logging.md)kunt u uw account controleren op normale en abnormale activiteiten. U kunt weer geven welke bewerkingen zijn uitgevoerd op uw resources, wie de bewerking heeft gestart, wanneer de bewerking plaatsvond, de status van de bewerking en nog veel meer, zoals wordt weer gegeven in de scherm opname die volgt op deze tabel.|
|Reageren op aanvallen|Wanneer u contact hebt opgenomen met de ondersteuning van Azure om een mogelijke aanval te melden, wordt een respons proces van 5 stappen gestart. Het doel van het proces van vijf stappen is om zo snel mogelijk de normale service beveiliging en-bewerkingen te herstellen nadat een probleem is gedetecteerd en een onderzoek wordt gestart.<br><br>Meer informatie vindt u in [Microsoft Azure Security Response in de Cloud](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geoomheining|Azure Cosmos DB garandeert dat data governance voor soevereine regio's (bijvoorbeeld Duitsland, China, US Gov).|
|Beveiligde faciliteiten|Gegevens in Azure Cosmos DB worden opgeslagen op Ssd's in de beveiligde data centers van Azure.<br><br>Meer informatie in [micro soft Global Data Centers](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-versleuteling|Alle verbindingen met Azure Cosmos DB ondersteuning voor HTTPS. Azure Cosmos DB ondersteunt ook TLS 1,2.<br>Het is mogelijk om een minimale TLS-versie aan de server zijde af te dwingen. Als u dit wilt doen, [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)neemt u contact op met.|
|Versleuteling 'at rest'|Alle gegevens die zijn opgeslagen in Azure Cosmos DB, worden op rest versleuteld. Meer informatie over [Azure Cosmos DB versleuteling in rust](./database-encryption-at-rest.md)|
|Patched servers|Als beheerde data base, Azure Cosmos DB u geen servers meer hoeft te beheren en te patchen, die automatisch voor u worden uitgevoerd.|
|Beheerders accounts met sterke wacht woorden|Het is moeilijk om te geloven dat deze vereiste wordt vermeld, maar in tegens telling tot enkele van onze concurrenten is het niet mogelijk om een beheerders account zonder wacht woord te hebben in Azure Cosmos DB.<br><br> Beveiliging via TLS en op HMAC-geheim gebaseerde verificatie is standaard geïntegreerde.|
|Certificeringen voor beveiliging en gegevens bescherming| Voor de meest recente lijst met certificeringen raadpleegt u de algemene Azure- [nalevings site](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , evenals het meest recente [Azure-nalevings document](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) met alle certificeringen (zoek naar Cosmos). Lees voor meer informatie lezen van de 25 april 2018 post [Azure #CosmosDB: Secure, private, conform SOCS 1/2 type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High en veel andere.

De volgende scherm afbeelding laat zien hoe u controle logboeken en activiteiten Logboeken kunt gebruiken om ![uw account te bewaken: activiteiten logboeken voor Azure Cosmos db](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Volgende stappen

Zie [toegang tot Azure Cosmos DB gegevens beveiligen](secure-access-to-data.md)voor meer informatie over hoofd sleutels en bron tokens.

Zie [Azure Cosmos DB Diagnostic logging](logging.md)(Engelstalig) voor meer informatie over controle logboek registratie.

Zie [Vertrouwenscentrum van Azure](https://azure.microsoft.com/support/trust-center/)voor meer informatie over micro soft-certificeringen.
