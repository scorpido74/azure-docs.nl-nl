---
title: Patroon van webtoepassingen met meerdere tenantn | Microsoft Documenten
description: Zoek architectuuroverzichten en ontwerppatronen waarin wordt beschreven hoe u een multi-tenant webtoepassing op Azure implementeert.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: d1441ede9f448b3e6ffb0726c2ee92f192369e9a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481835"
---
# <a name="multitenant-applications-in-azure"></a>Multitenant-toepassingen in Azure
Een multitenant-toepassing is een gedeelde bron waarmee "gebruikers in afzonderlijke tenants" de toepassing kunnen bekijken alsof deze hun eigen toepassing is. Een typisch scenario dat zich leent voor een multitenant-toepassing is er een waarin alle gebruikers van de toepassing van verschillende tenants de gebruikerservaring willen aanpassen, maar anders dezelfde basisvereisten voor het bedrijf hebben. Voorbeelden van grote multitenant-toepassingen zijn Office 365, Outlook.com en visualstudio.com.

Vanuit het perspectief van een applicatieleverancier hebben de voordelen van multitenancy vooral betrekking op operationele en kostenefficiëntie. Eén versie van uw toepassing kan voldoen aan de behoeften van veel tenants/klanten, waardoor consolidatie van systeembeheertaken mogelijk is, zoals monitoring, prestatieafstemming, softwareonderhoud en back-ups van gegevens.

Het volgende bevat een lijst van de belangrijkste doelen en vereisten vanuit het perspectief van een provider.

* **Inrichting**: U moet in staat zijn om nieuwe huurders voor de aanvraag.  Voor multitenant-toepassingen met een groot aantal tenants is het meestal noodzakelijk om dit proces te automatiseren door selfservice-inlevering mogelijk te maken.
* **Onderhoudbaarheid:** U moet de toepassing kunnen upgraden en andere onderhoudstaken kunnen uitvoeren terwijl meerdere tenants deze gebruiken.
* **Monitoring**: U moet te allen tijde de toepassing kunnen controleren om eventuele problemen te identificeren en op te lossen. Dit omvat het controleren hoe elke tenant de toepassing gebruikt.

Een goed geïmplementeerde multitenant-toepassing biedt de volgende voordelen voor gebruikers.

* **Isolatie**: De activiteiten van individuele huurders hebben geen invloed op het gebruik van de toepassing door andere huurders. Huurders hebben geen toegang tot elkaars gegevens. Het lijkt de huurder alsof ze exclusief gebruik van de applicatie.
* **Beschikbaarheid**: Individuele huurders willen dat de toepassing voortdurend beschikbaar is, misschien met garanties gedefinieerd in een SLA. Nogmaals, de activiteiten van andere huurders mogen geen invloed hebben op de beschikbaarheid van de toepassing.
* **Schaalbaarheid**: De applicatie schaalt om te voldoen aan de vraag van individuele tenants. De aanwezigheid en acties van andere huurders mogen geen invloed hebben op de uitvoering van de toepassing.
* **Kosten:** Kosten zijn lager dan het uitvoeren van een speciale toepassing met één tenant, omdat multi-tenancy het delen van resources mogelijk maakt.
* **Aanpasbaarheid**. De mogelijkheid om de toepassing voor een individuele tenant op verschillende manieren aan te passen, zoals het toevoegen of verwijderen van functies, het wijzigen van kleuren en logo's of zelfs het toevoegen van hun eigen code of script.

Kortom, hoewel er veel overwegingen zijn waarmee u rekening moet houden om een zeer schaalbare service te bieden, zijn er ook een aantal doelen en vereisten die veel voorkomende zijn voor veel multitenant-toepassingen. Sommige zijn mogelijk niet relevant in specifieke scenario's en het belang van individuele doelen en vereisten zal in elk scenario verschillen. Als aanbieder van de multitenant-toepassing heeft u ook doelen en vereisten, zoals het voldoen aan de doelstellingen en vereisten van de tenant, winstgevendheid, facturering, meerdere serviceniveaus, inrichting, onderhoudsbewaking en automatisering.

Zie Hosting van [een multitenant-toepassing op Azure][Hosting a Multi-Tenant Application on Azure]voor meer informatie over aanvullende ontwerpoverwegingen van een multitenant-toepassing. Zie voor informatie over algemene gegevensarchitectuurpatronen van multitenant software as a service (SaaS)-databasetoepassingen, [Ontwerppatronen voor multitenant SaaS-toepassingen met Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure biedt veel functies waarmee u de belangrijkste problemen aanpakken die zich voordoen bij het ontwerpen van een multitenant-systeem.

**Isolatie**

* Segmenteer websitetenants op hostheaders met of zonder TLS-communicatie
* Gebruikers van de website segmenteren op queryparameters
* Webservices in werknemersrollen
  * Worker Rollen die doorgaans gegevens verwerken op de backend van een toepassing.
  * Webrollen die doorgaans fungeren als de frontend voor toepassingen.

**Storage**

Gegevensbeheer zoals Azure SQL Database- of Azure Storage-services, zoals de Tabel-service, die services biedt voor de opslag van grote hoeveelheden ongestructureerde gegevens en de Blob-service, die services biedt voor het opslaan van grote hoeveelheden ongestructureerde tekst of binaire gegevens zoals video, audio en afbeeldingen.

* Multitenant-gegevens beveiligen in SQL Database per tenant SQL Server-aanmeldingen.
* Als u Azure Tables for Application Resources gebruikt door een toegangsbeleid op containerniveau op te geven, u machtigingen aanpassen zonder dat u nieuwe URL's hoeft uit te geven voor de resources die zijn beveiligd met handtekeningen voor gedeelde toegang.
* Azure-wachtrijen voor Azure-wachtrijen worden vaak gebruikt om de verwerking namens tenants te stimuleren, maar kunnen ook worden gebruikt om werk te distribueren dat nodig is voor inrichting of beheer.
* Servicebuswachtrijen voor toepassingsbronnen die werk naar een gedeelde service pusht, u één wachtrij gebruiken waarbij elke tenantafzender alleen machtigingen heeft (zoals afgeleid van claims die zijn uitgegeven door ACS) om naar die wachtrij te duwen, terwijl alleen de ontvangers van de service toestemming hebben om de gegevens van meerdere tenants uit de wachtrij te halen.

**Verbindings- en beveiligingsdiensten**

* Azure Service Bus, een berichteninfrastructuur die zich tussen toepassingen bevindt waarmee ze berichten op een losjes gekoppelde manier kunnen uitwisselen voor verbeterde schaal en tolerantie.

**Netwerkservices**

Azure biedt verschillende netwerkservices die verificatie ondersteunen en de beheerbaarheid van uw gehoste toepassingen verbeteren. Deze diensten omvatten de volgende gegevens:

* Met Azure Virtual Network u virtual private networks (VPN's) in Azure inrichten en beheren en deze veilig koppelen aan on-premises IT-infrastructuur.
* Met Virtual Network Traffic Manager u binnenkomend verkeer in balans laden in meerdere gehoste Azure-services, ongeacht of ze in hetzelfde datacenter of in verschillende datacenters over de hele wereld worden uitgevoerd.
* Azure Active Directory (Azure AD) is een moderne, op RUST gebaseerde service die mogelijkheden voor identiteitsbeheer en toegangscontrole biedt voor uw cloudtoepassingen. Het gebruik van Azure AD for Application Resources biedt een eenvoudige manier om gebruikers te authenticeren en toestemming te geven om toegang te krijgen tot uw webtoepassingen en -services, terwijl de functies van verificatie en autorisatie kunnen worden meegenomen uit uw code.
* Azure Service Bus biedt een veilige messaging- en gegevensstroommogelijkheid voor gedistribueerde en hybride toepassingen, zoals communicatie tussen azure-gehoste toepassingen en on-premises toepassingen en services, zonder complexe firewall- en beveiligingsinfrastructuren te vereisen. Het gebruik van Service Bus Relay voor toepassingsbronnen om toegang te krijgen tot de services die worden blootgesteld als eindpunten kan van de tenant zijn (bijvoorbeeld buiten het systeem worden gehost, zoals on-premises), of het kan zijn dat services specifiek voor de tenant worden ingericht (omdat gevoelige, tenantspecifieke gegevens over hen heen worden verplaatst).

**Inrichten van middelen**

Azure biedt een aantal manieren om nieuwe tenants voor de toepassing in te richten. Voor multitenant-toepassingen met een groot aantal tenants is het meestal noodzakelijk om dit proces te automatiseren door selfservice-inlevering mogelijk te maken.

* Met werknemersrollen u per tenantresources inrichten en deprovisionenderen (zoals wanneer een nieuwe tenant zich aanmeldt of annuleert), statistieken verzamelen voor metinggebruik en schaal beheren volgens een bepaald schema of in reactie op het overschrijden van drempelwaarden voor belangrijke prestatie-indicatoren. Dezelfde rol kan ook worden gebruikt om updates en upgrades naar de oplossing uit te duwen.
* Azure Blobs kunnen worden gebruikt voor het inrichten van compute- of vooraf geïnitialiseerde opslagbronnen voor nieuwe tenants, terwijl het toegangsbeleid op containerniveau wordt geboden om de compute service-pakketten, VHD-afbeeldingen en andere bronnen te beschermen.
* Opties voor het inrichten van SQL Database-resources voor een tenant zijn:
  
  * DDL in scripts of ingesloten als resources binnen assemblages.
  * SQL Server 2008 R2 DAC-pakketten programmatisch geïmplementeerd.
  * Kopiëren vanuit een hoofdreferentiedatabase.
  * Met behulp van database Importeren en exporteren om nieuwe databases uit een bestand te voorzien.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
