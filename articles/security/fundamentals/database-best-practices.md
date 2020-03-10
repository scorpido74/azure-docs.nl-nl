---
title: Aanbevolen procedures voor database beveiliging-Microsoft Azure
description: Dit artikel bevat een aantal aanbevolen procedures voor Azure data base Security.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 316c3ef3c5bd16b52291029924d04fc159375bc8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943666"
---
# <a name="azure-database-security-best-practices"></a>Best practices voor Azure-databasebeveiliging
In dit artikel worden aanbevolen procedures voor database beveiliging beschreven.

De aanbevolen procedures zijn gebaseerd op een advies van de mening en ze werken met de huidige mogelijkheden en functie sets van het Azure-platform. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regel matig bijgewerkt om deze wijzigingen weer te geven.

## <a name="secure-databases"></a>Beveiligde data bases
Beveiliging is een belangrijkste reden voor het beheren van data bases en is altijd een prioriteit voor [Azure SQL database](../../sql-database/index.yml). Uw data bases kunnen nauw keurig worden beveiligd om te voldoen aan de meeste regelgevende of beveiligings vereisten, waaronder HIPAA, ISO 27001/27002 en PCI DSS niveau 1. Een actuele lijst met beveiligings nalevings certificeringen is beschikbaar op de website van het [micro soft vertrouwens centrum](https://azure.microsoft.com/support/trust-center/services/). U kunt er ook voor kiezen om uw data bases in specifieke Azure-data centers te plaatsen op basis van wettelijke vereisten.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Firewall regels gebruiken om database toegang te beperken
Microsoft Azure SQL Database biedt een relationele database service voor Azure en andere toepassingen op internet. SQL Database beheert de toegang met de volgende opties om toegang te krijgen tot beveiliging:

- Firewall regels die de connectiviteit beperken op basis van het IP-adres.
- Verificatie mechanismen waarvoor gebruikers hun identiteit moeten bewijzen.
- Autorisatie mechanismen waarmee gebruikers worden beperkt tot specifieke acties en gegevens.

Firewalls verhinderen alle toegang tot uw database server totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.

In de volgende afbeelding ziet u waar u een server firewall instelt in SQL Database:

![Firewall-regels](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

De Azure SQL Database-Service is alleen beschikbaar via TCP-poort 1433. Als u toegang wilt krijgen tot een SQL database van uw computer, moet u ervoor zorgen dat de firewall van de client computer uitgaande TCP-communicatie toestaat op TCP-poort 1433. Blok keer binnenkomende verbindingen op TCP-poort 1433 door gebruik te maken van firewall regels, als u deze verbindingen voor andere toepassingen niet nodig hebt.

Als onderdeel van het verbindings proces worden verbindingen van virtuele Azure-machines omgeleid naar een IP-adres en een unieke poort voor elke worker-rol. Het poortnummer ligt in het bereik van 11000 tot 11999. Zie voor meer informatie over TCP-poorten [poorten van meer dan 1433 voor ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Zie [SQL Database-firewallregels](../../sql-database/sql-database-firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.

> [!Note]
> Naast de IP-regels beheert de firewall regels voor het virtuele netwerk. De regels voor virtuele netwerken zijn gebaseerd op service-eind punten van het virtuele netwerk. De regels voor virtuele netwerken kunnen in sommige gevallen de voor keur geven aan IP-regels. Zie [service-eind punten en-regels voor virtuele netwerken voor Azure SQL database voor](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)meer informatie.

## <a name="enable-database-authentication"></a>Database verificatie inschakelen
SQL Database ondersteunt twee soorten verificatie, SQL Server-verificatie en Azure AD-verificatie.

### <a name="sql-server-authentication"></a>*SQL Server-verificatie*

Dit biedt verschillende voordelen, zoals:

- Het biedt SQL Database ondersteuning voor omgevingen met gemengde besturings systemen, waarbij alle gebruikers niet worden geverifieerd door een Windows-domein.
- Hiermee kunnen SQL Database oudere toepassingen en door de partner geleverde toepassingen ondersteunen waarvoor SQL Server-verificatie is vereist.
- Hiermee kunnen gebruikers verbinding maken met onbekende of niet-vertrouwde domeinen. Een voor beeld is een toepassing waarbij bestaande klanten verbinding maken met toegewezen SQL Server aanmeldingen om de status van hun orders te ontvangen.
- Hiermee kan SQL Database webtoepassingen ondersteunen waarbij gebruikers hun eigen identiteiten maken.
- Stelt software ontwikkelaars in staat om hun toepassingen te distribueren met behulp van een complexe machtigings hiërarchie op basis van bekende, vooraf ingestelde SQL Server aanmeldingen.

> [!NOTE]
> SQL Server-verificatie kan het Kerberos-beveiligings protocol niet gebruiken.

Als u SQL Server-verificatie gebruikt, moet u het volgende doen:

- De sterke referenties zelf beheren.
- Beveilig de referenties in de connection string.
- (Mogelijk) de referenties beveiligen die via het netwerk van de webserver worden door gegeven aan de data base. Zie [How to: verbinding maken met SQL Server met behulp van SQL-verificatie in ASP.NET 2,0](/previous-versions/msp-n-p/ff648340(v=pandp.10))voor meer informatie.

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD)-verificatie*
Azure AD-verificatie is een mechanisme om verbinding te maken met Azure SQL Database en [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in azure AD. Met Azure AD-verificatie kunt u de identiteiten van database gebruikers en andere micro soft-Services op één centrale locatie beheren. Centraal-ID-beheer biedt één locatie voor het beheren van database gebruikers en het vereenvoudigt het beheer van machtigingen.

> [!NOTE]
> We raden u aan Azure AD-verificatie te gebruiken voor het gebruik van SQL Server-verificatie.

Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor SQL Server-verificatie.
- Het helpt bij het stoppen van de verspreiding van gebruikers identiteiten over database servers.
- Hiermee kan het draaien van wacht woorden op één plek plaatsvinden.
- Klanten kunnen database machtigingen beheren via externe groepen (Azure AD).
- Hiermee kan worden voor komen dat wacht woorden worden opgeslagen door geïntegreerde Windows-verificatie en andere vormen van verificatie die door Azure Active Directory worden ondersteund, in te scha kelen.
- Hierin worden inge sloten database gebruikers gebruikt voor het verifiëren van identiteiten op database niveau.
- Het ondersteunt verificatie op basis van tokens voor toepassingen die verbinding maken met SQL Database.
- Het ondersteunt AD FS (domein-Federatie) of systeem eigen gebruikers-en wachtwoord verificatie voor een lokale Azure Active Directory-exemplaar zonder domein synchronisatie.
- Azure AD biedt ondersteuning voor verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory universele verificatie, waaronder Multi-Factor Authentication. Multi-Factor Authentication biedt krachtige verificatie met diverse verificatie opties: telefoon oproep, SMS-bericht, Smart Cards met pincode of mobiele app-melding. Zie [SSMS-ondersteuning voor Azure AD-multi-factor Authentication met SQL database en SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md)voor meer informatie.

De configuratie stappen omvatten de volgende procedures voor het configureren en gebruiken van Azure AD-verificatie:

- Azure AD maken en vullen.
- Optioneel: koppel of wijzig het Active Directory exemplaar dat momenteel is gekoppeld aan uw Azure-abonnement.
- Een Azure Active Directory beheerder maken voor Azure SQL Database of [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configureer uw client computers.
- Maak Inge sloten database gebruikers in uw data base die zijn toegewezen aan Azure AD-identiteiten.
- Maak verbinding met uw data base met behulp van Azure AD-identiteiten.

U kunt gedetailleerde informatie vinden over het [gebruik van Azure Active Directory verificatie voor verificatie met SQL database, een beheerd exemplaar of SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Uw gegevens beveiligen met versleuteling en beveiliging op rijniveau
[Azure SQL database transparante gegevens versleuteling](../../sql-database/transparent-data-encryption-azure-sql.md) helpt gegevens op de schijf te beschermen en beschermt tegen onbevoegde toegang tot de hardware. Er wordt in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden in rust uitgevoerd zonder dat de toepassing hoeft te worden gewijzigd. Transparante gegevens versleuteling versleutelt de opslag van een volledige data base met behulp van een symmetrische sleutel die de database versleutelings sleutel wordt genoemd.

Zelfs wanneer de hele opslag is versleuteld, is het belang rijk om de data base zelf te versleutelen. Dit is een implementatie van de ingrijpende aanpak voor gegevens beveiliging. Als u Azure SQL Database gebruikt en gevoelige gegevens wilt beveiligen (zoals credit card-of sofi-nummers), kunt u data bases versleutelen met FIPS 140-2-gevalideerde 256-bits AES-versleuteling. Deze versleuteling voldoet aan de vereisten van veel industrie normen (bijvoorbeeld HIPAA en PCI).

Bestanden met betrekking tot de buffergroepuitbreiding [(BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) worden niet versleuteld wanneer u een Data Base versleutelt met behulp van transparante gegevens versleuteling. U moet de hulpprogram ma's voor versleuteling op bestands systeem niveau, zoals [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) of de [Encrypting File System (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) voor BPE bestanden gebruiken.

Omdat een geautoriseerde gebruiker, zoals een beveiligings beheerder of een database beheerder, toegang heeft tot de gegevens, zelfs als de data base is versleuteld met transparante gegevens versleuteling, moet u ook de volgende aanbevelingen volgen:

- Schakel SQL Server-verificatie in op database niveau.
- Gebruik Azure AD-verificatie met behulp van [RBAC-rollen](/azure/role-based-access-control/overview).
- Zorg ervoor dat gebruikers en toepassingen afzonderlijke accounts gebruiken om te verifiëren. Op deze manier kunt u de machtigingen beperken die worden verleend aan gebruikers en toepassingen en het risico op schadelijke activiteiten verminderen.
- Implementeer beveiliging op database niveau met behulp van vaste database rollen (zoals db_datareader of db_datawriter). U kunt ook aangepaste rollen maken voor uw toepassing om expliciete machtigingen te verlenen aan geselecteerde database objecten.

Voor andere manieren om uw gegevens te beveiligen, kunt u het volgende overwegen:

- [Versleuteling op celniveau](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) om specifieke kolommen of zelfs cellen met gegevens met verschillende versleutelingssleutels te versleutelen.
- [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), waarmee clients gevoelige gegevens binnen client toepassingen kunnen versleutelen en de versleutelings sleutels niet kunnen bekendmaken aan de data base-Engine (SQL Database of SQL Server). Als gevolg hiervan biedt Always Encrypted een schei ding tussen degenen die eigenaar zijn van de gegevens (en deze kunnen bekijken) en degenen die de gegevens beheren (maar geen toegang mogen hebben).
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security), waarmee klanten de toegang tot rijen in een database tabel kunnen beheren op basis van de kenmerken van de gebruiker die een query uitvoert. (Voor beelden van kenmerken zijn groepslid maatschappen en uitvoerings context.)

Organisaties die geen versleuteling op database niveau gebruiken, zijn mogelijk gevoeliger voor aanvallen waarbij gegevens in SQL-data bases worden aangetast.

Meer informatie over SQL Database transparante gegevens versleuteling vindt u in het artikel [transparent Data Encryption met Azure SQL database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Database controle inschakelen
Als u een exemplaar van de SQL Server data base-engine of een afzonderlijke Data Base wilt controleren, moet u gebeurtenissen bijhouden en registreren. Voor SQL Server kunt u controles maken die specificaties bevatten voor gebeurtenissen op server niveau en specificaties voor gebeurtenissen op database niveau. Gecontroleerde gebeurtenissen kunnen worden geschreven naar de gebeurtenis Logboeken of om bestanden te controleren.

Er zijn verschillende controle niveaus voor SQL Server, afhankelijk van de vereisten voor de overheid of standaarden voor uw installatie. SQL Server controle bevat hulpprogram ma's en processen voor het inschakelen, opslaan en weer geven van controles op verschillende server-en database objecten.

Met [Azure SQL database controle](/azure/sql-database/sql-database-auditing) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven.

Controle kan u helpen bij het onderhouden van naleving van regelgeving, het begrijpen van database activiteiten en het vinden van verschillen en afwijkingen die kunnen wijzen op zakelijke problemen of beveiligings schendingen. Auditing vereenvoudigt het voldoen aan nalevings normen, maar garandeert geen naleving.

Zie aan de [slag met SQL database auditing](/azure/sql-database/sql-database-auditing)voor meer informatie over database controle en hoe u deze functie inschakelt.

## <a name="enable-database-threat-detection"></a>Detectie van de database bedreigingen inschakelen
Bedreigings beveiliging gaat verder dan detectie. Data base Threat Protection omvat:

- De meest gevoelige gegevens detecteren en classificeren zodat u uw gegevens kunt beveiligen.
- Implementatie van beveiligde configuraties in uw data base zodat u uw data base kunt beveiligen.
- Mogelijke bedreigingen detecteren en erop reageren wanneer deze zich voordoen, zodat u snel kunt reageren en herstellen.

**Best Practice**: de gevoelige gegevens in uw data bases detecteren, classificeren en labelen.   
**Details**: de gegevens in uw SQL database classificeren door [gegevens detectie en classificatie](/azure/sql-database/sql-database-data-discovery-and-classification) in Azure SQL database in te scha kelen. U kunt de toegang tot uw gevoelige gegevens in het Azure-dash board controleren of rapporten downloaden.

**Best Practice**: beveiligings lekken in de data base volgen zodat u uw database beveiliging proactief kunt verbeteren.   
**Details**: gebruik de Azure SQL database-evaluatie service voor [beveiligings](/azure/sql-database/sql-vulnerability-assessment) problemen, die scant op mogelijke beveiligings lekken voor de data base. De service maakt gebruik van een Knowledge Base met regels voor het markeren van beveiligings problemen en het weer geven van afwijkingen van aanbevolen procedures, zoals onjuiste configuraties, buitensporige machtigingen en onbeveiligde gevoelige gegevens.

De regels zijn gebaseerd op de best practices van micro soft en zijn gericht op de beveiligings problemen die de grootste Risico's voor uw data base en de waardevolle gegevens van deze computer opleveren. Ze omvatten zowel problemen op database niveau als beveiligings problemen op server niveau, zoals Server firewall-instellingen en machtigingen op server niveau. Deze regels vormen ook een groot aantal vereisten van regelgevende instanties om te voldoen aan de nalevings normen.

**Best Practice**: detectie van bedreigingen inschakelen.  
**Details**: Schakel Azure SQL database [detectie van bedreigingen](/azure/sql-database/sql-database-threat-detection) in om beveiligings waarschuwingen en aanbevelingen te ontvangen over het onderzoeken en oplossen van bedreigingen. U krijgt waarschuwingen over verdachte database activiteiten, potentiële kwetsbaar heden en SQL-injectie aanvallen, evenals afwijkende database toegang en query patronen.

[Advanced Threat Protection](/azure/sql-database/sql-advanced-threat-protection) is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Hierin zijn de services opgenomen die eerder zijn vermeld: gegevens detectie en-classificatie, evaluatie van beveiligings problemen en detectie van bedreigingen. Het biedt één locatie om deze mogelijkheden in te scha kelen en te beheren.

Als u deze mogelijkheden inschakelt, kunt u:

- Voldoen aan de normen voor gegevens beveiliging en naleving van regelgeving.
- De toegang tot uw data bases beheren en hun beveiliging beveiligen.
- Een dynamische database omgeving bewaken waarbij wijzigingen moeilijk te volgen zijn.
- Mogelijke dreigingen detecteren en erop reageren.

Daarnaast integreert de detectie van dreigingen waarschuwingen met Azure Security Center voor een centraal overzicht van de beveiligings status van al uw Azure-resources.

## <a name="next-steps"></a>Volgende stappen
Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.

De volgende resources zijn beschikbaar om meer algemene informatie te geven over Azure-beveiliging en gerelateerde micro soft-Services:
* [Blog van het Azure-beveiligings team](https://blogs.msdn.microsoft.com/azuresecurity/) : voor actuele informatie over de nieuwste Azure-beveiliging
* [Micro soft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) : waar micro soft-beveiligings problemen, met inbegrip van problemen met Azure, kunnen worden gerapporteerd of via e-mail naar secure@microsoft.com
