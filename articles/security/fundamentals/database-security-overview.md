---
title: Overzicht van azure-databasebeveiliging| Microsoft Documenten
description: In dit artikel vindt u een overzicht van de beveiligingsfuncties van de Azure-database.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906062"
---
# <a name="azure-database-security-overview"></a>Overzicht van azure-databasebeveiliging

Beveiliging is een topzorg voor het beheren van databases en is altijd een prioriteit geweest voor Azure SQL Database. Azure SQL Database ondersteunt verbindingsbeveiliging met firewallregels en verbindingsversleuteling. Het ondersteunt verificatie met gebruikersnaam en wachtwoord en Azure Active Directory (Azure AD)-verificatie, waarbij gebruik wordt gemaakt van identiteiten die worden beheerd door Azure Active Directory. Autorisatie maakt gebruik van op rollen gebaseerd toegangscontrole.

Azure SQL Database ondersteunt versleuteling door realtime versleuteling en decryptie van databases, bijbehorende back-ups en transactielogboekbestanden in rust uit te voeren zonder dat de toepassing moet worden gewijzigd.

Microsoft biedt aanvullende manieren om bedrijfsgegevens te versleutelen:

-   Versleuteling op celniveau is beschikbaar om specifieke kolommen of zelfs cellen van gegevens met verschillende versleutelingssleutels te versleutelen.
-   Als u een hardwarebeveiligingsmodule of centraal beheer van uw sleutelhiërarchie voor versleuteling nodig hebt, u overwegen Azure Key Vault te gebruiken met SQL Server in een virtuele Azure-machine (VM).
-   Always Encrypted (momenteel in preview) maakt versleuteling transparant voor toepassingen. Het stelt clients ook in staat om gevoelige gegevens in clienttoepassingen te versleutelen zonder de versleutelingssleutels te delen met SQL Database.

Azure SQL Database Auditing stelt ondernemingen in staat om gebeurtenissen op te nemen in een controlelogboek in Azure Storage. SQL Database Auditing kan ook worden geïntegreerd met Microsoft Power BI om het inzoomen op rapporten en analyses mogelijk te maken.

Azure SQL-databases kunnen goed worden beveiligd om te voldoen aan de meeste wettelijke of beveiligingsvereisten, waaronder HIPAA, ISO 27001/27002 en PCI DSS Level 1. Een huidige lijst met certificeringen voor beveiligingscompliance is beschikbaar op de [microsoft Azure Trust Center-site.](https://azure.microsoft.com/support/trust-center/services/)

In dit artikel worden de basisprincipes van het beveiligen van Microsoft Azure SQL-databases voor gestructureerde, tabelvormige en relationele gegevens doorgenomen. Dit artikel helpt u in het bijzonder om aan de slag te gaan met resources voor gegevensbeveiliging, toegangsbeheer en proactieve controle.

## <a name="protection-of-data"></a>Bescherming van gegevens

SQL Database helpt uw gegevens te beveiligen door versleuteling te bieden:

- Voor gegevens in beweging via [Transport Layer Security (TLS).](https://support.microsoft.com/kb/3135244)
- Voor gegevens in rust door middel [van transparante gegevensversleuteling](https://go.microsoft.com/fwlink/?LinkId=526242).
- Voor gegevens die worden gebruikt via [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Andere manieren om uw gegevens te versleutelen die u kunt overwegen:

-   [Versleuteling op celniveau](https://msdn.microsoft.com/library/ms179331.aspx) om specifieke kolommen of zelfs cellen met gegevens met verschillende versleutelingssleutels te versleutelen.
-   [Azure Key Vault met SQL Server in een Azure VM,](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)als u een hardwarebeveiligingsmodule of centraal beheer van uw sleutelhiërarchie nodig hebt.

### <a name="encryption-in-motion"></a>Versleuteling in beweging

Een veelvoorkomend probleem voor alle client/servertoepassingen is de behoefte aan privacy als gegevens zich over openbare en particuliere netwerken verplaatst. Als gegevens die via een netwerk worden verzonden niet worden versleuteld, bestaat de kans dat deze worden vastgelegd en gestolen door onbevoegde gebruikers. Wanneer u te maken hebt met databaseservices, moet u ervoor zorgen dat gegevens worden versleuteld tussen de databaseclient en de server. Zorg er ook voor dat gegevens worden versleuteld tussen databaseservers die met elkaar en met toepassingen op het middenniveau communiceren.

Een probleem wanneer u een netwerk beheert, is het beveiligen van gegevens die worden verzonden tussen toepassingen in een niet-vertrouwd netwerk. U [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) gebruiken om servers en clients te verifiëren en deze vervolgens te gebruiken om berichten tussen de geverifieerde partijen te versleutelen.

In het verificatieproces stuurt een TLS/SSL-client een bericht naar een TLS/SSL-server. De server reageert met de informatie die de server nodig heeft om zichzelf te verifiëren. De client en server wisselen daarna sessiesleutels uit en de verificatiedialoog wordt beëindigd. Wanneer de verificatie is voltooid, kan ssl-beveiligde communicatie beginnen tussen de server en de client via de symmetrische versleutelingssleutels die tijdens het verificatieproces worden vastgesteld.

Alle verbindingen met Azure SQL Database vereisen te allen tijde versleuteling (TLS/SSL), terwijl gegevens "onderweg" zijn van en naar de database. SQL Database gebruikt TLS/SSL om servers en clients te verifiëren en vervolgens te gebruiken om berichten tussen de geverifieerde partijen te versleutelen. 

In de verbindingstekenreeks van uw toepassing moet u parameters opgeven om de verbinding te versleutelen en het servercertificaat niet te vertrouwen. (Dit wordt voor u gedaan als u uw verbindingstekenreeks uit de Azure-portal kopieert.) Anders zal de verbinding de identiteit van de server niet verifiëren en is het gevoelig voor "man-in-the-middle"-aanvallen. Voor het ADO.NET-stuurprogramma zijn deze verbindingstekenreeksparameters bijvoorbeeld `Encrypt=True` en `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

U verschillende voorzorgsmaatregelen nemen om de database te beveiligen. Ontwerp bijvoorbeeld een beveiligd systeem, versleutel vertrouwelijke assets en bouw een firewall rond de databaseservers. Maar in een scenario waarin de fysieke media (zoals schijven of back-up tapes) worden gestolen, een kwaadwillende partij kan gewoon herstellen of hechten van de database en blader door de gegevens.

Een oplossing is om de gevoelige gegevens in de database te versleutelen en de sleutels te beschermen die worden gebruikt om de gegevens te versleutelen met een certificaat. Deze oplossing voorkomt dat iemand zonder de sleutels de gegevens gebruikt, maar dit soort bescherming moet worden gepland.

Om dit probleem op te lossen, ondersteunen SQL Server en SQL Database [transparante gegevensversleuteling.](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017) Transparante gegevensversleuteling versleutelt SQL Server- en SQL Database-gegevensbestanden, in rust bekend als versleutelingsgegevens.

Transparante gegevensversleuteling helpt beschermen tegen de dreiging van schadelijke activiteiten. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht.  

Transparante gegevensversleuteling versleutelt de opslag van een hele database met behulp van een symmetrische sleutel genaamd de databaseversleutelingssleutel. In SQL Database wordt de databaseversleutelingssleutel beschermd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke SQL Database-server.

Als een database zich in een Geo-DR-relatie bevindt, wordt deze beschermd door een andere sleutel op elke server. Als twee databases met dezelfde server zijn verbonden, delen ze hetzelfde ingebouwde certificaat. Microsoft draait deze certificaten automatisch ten minste om de 90 dagen. 

Zie [Transparante gegevensversleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption-tde)voor meer informatie .

### <a name="encryption-in-use-client"></a>Versleuteling in gebruik (client)

De meeste datalekken omvatten de diefstal van kritieke gegevens, zoals creditcardnummers of persoonlijk identificeerbare informatie. Databases kunnen schatkamers van gevoelige informatie zijn. Ze kunnen persoonlijke gegevens van klanten (zoals nationale identificatienummers), vertrouwelijke concurrerende informatie en intellectuele eigendom bevatten. Verloren of gestolen gegevens, met name klantgegevens, kunnen leiden tot merkschade, concurrentienadeel en ernstige boetes - zelfs rechtszaken.

![De always versleutelde functie geïllustreerd als een slot en sleutel](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) is een functie die is ontworpen om gevoelige gegevens te beschermen die zijn opgeslagen in Azure SQL Database- of SQL Server-databases. Always Encrypted stelt clients in staat om gevoelige gegevens in clienttoepassingen te versleutelen en nooit de versleutelingssleutels aan de databaseengine (SQL Database of SQL Server) te onthullen.

Always Encrypted biedt een scheiding tussen mensen die eigenaar zijn van de gegevens (en deze kunnen bekijken) en mensen die de gegevens beheren (maar geen toegang zouden moeten hebben). Het helpt ervoor te zorgen dat on-premises databasebeheerders, clouddatabasebeheerders of andere gebruikers met hoge bevoegdheden, maar onbevoegde gebruikers geen toegang hebben tot de versleutelde gegevens.

Daarnaast maakt Always Encrypted encryptie transparant voor applicaties. Er is een stuurprogramma met always encrypted-enabled geïnstalleerd op de clientcomputer, zodat het automatisch gevoelige gegevens in de clienttoepassing kan versleutelen en decoderen. De bestuurder versleutelt de gegevens in gevoelige kolommen voordat de gegevens worden doorgesleuteld aan de database-engine. Het stuurprogramma herschrijft query's automatisch, zodat de semantiek van de toepassing behouden blijft. Op dezelfde manier decodeert het stuurprogramma op transparante wijze gegevens, opgeslagen in versleutelde databasekolommen, in queryresultaten.

## <a name="access-control"></a>Toegangsbeheer

Om beveiliging te bieden, beheert SQL Database de toegang met behulp van:

- Firewallregels die de connectiviteit per IP-adres beperken.
- Verificatiemechanismen waarvoor gebruikers hun identiteit moeten bewijzen.
- Autorisatiemechanismen die gebruikers beperken tot specifieke acties en gegevens.

### <a name="database-access"></a>Databasetoegang

Gegevensbescherming begint met het beheren van de toegang tot uw gegevens. Het datacenter dat uw gegevens host, beheert fysieke toegang. U een firewall configureren om de beveiliging op de netwerklaag te beheren. U beheert ook de toegang door aanmeldingen voor verificatie te configureren en machtigingen voor server- en databaserollen te definiëren.

#### <a name="firewall-and-firewall-rules"></a>Firewall en firewallregels

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) biedt een relationele databaseservice voor Azure en andere internettoepassingen. Om uw gegevens te beschermen, verhinderen firewalls alle toegang tot de databaseserver totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt. Zie [Overzicht van de firewallregels voor SQL Database](/azure/sql-database/sql-database-firewall-configure) voor meer informatie.

De Azure SQL Database-service is alleen beschikbaar via TCP-poort 1433. Als u vanaf uw computer toegang wilt krijgen tot een SQL-database, moet u ervoor zorgen dat uw clientcomputerfirewall uitgaande TCP-communicatie op TCP-poort 1433 toestaat. Als binnenkomende verbindingen niet nodig zijn voor andere toepassingen, blokkeert u deze op TCP-poort 1433.

#### <a name="authentication"></a>Authentication

Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL Database ondersteunt twee typen verificatie:

-   **SQL Server-verificatie**: er wordt één aanmeldingsaccount gemaakt wanneer een logische SQL-instantie wordt gemaakt, het SQL Database-abonneeaccount. Dit account maakt verbinding met [SQL Server-verificatie](/azure/sql-database/sql-database-security-overview) (gebruikersnaam en wachtwoord). Het account fungeert als beheerder voor de logische serverinstantie en voor alle gebruikersdatabases die hieraan zijn gekoppeld. De machtigingen van het abonneeaccount kunnen niet worden beperkt. Er kan slechts één van deze accounts bestaan.
-   **Azure Active Directory-verificatie**: [Azure AD-verificatie](/azure/sql-database/sql-database-aad-authentication) is een mechanisme om verbinding te maken met Azure SQL Database en Azure SQL Data Warehouse met behulp van identiteiten in Azure AD. U het gebruiken om de identiteit van databasegebruikers centraal te beheren.

![Azure AD-verificatie met SQL-database](./media/database-security-overview/azure-database-fig2.png)

 Voordelen van Azure AD-verificatie zijn onder andere:
  - Het biedt een alternatief voor SQL Server-verificatie.
  - Het helpt de verspreiding van gebruikersidentiteiten over databaseservers te stoppen en maakt wachtwoordrotatie op één plaats mogelijk.
  - U databasemachtigingen beheren met externe (Azure AD)-groepen.
  - Het kan het opslaan van wachtwoorden elimineren door geïntegreerde Windows-verificatie en andere vormen van verificatie in te schakelen die Azure AD ondersteunt.

#### <a name="authorization"></a>Autorisatie

[Autorisatie](/azure/sql-database/sql-database-manage-logins) verwijst naar wat een gebruiker kan doen in een Azure SQL-database. Het wordt beheerd door de [databaserollidmaatschappen](https://msdn.microsoft.com/library/ms189121) van uw gebruikersaccount en [machtigingen op objectniveau.](https://msdn.microsoft.com/library/ms191291.aspx) Autorisatie is het proces om te bepalen tot welke securable resources een opdrachtgever toegang heeft en welke bewerkingen voor deze resources zijn toegestaan.

### <a name="application-access"></a>Toegang tot toepassingen

#### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering

Een servicemedewerker van een callcenter kan bellers identificeren aan de kaart met meerdere cijfers van hun sofi-nummer of creditcardnummer. Maar deze gegevens items mogen niet volledig worden blootgesteld aan de service vertegenwoordiger.

U een maskerregel definiëren die alle, behalve de laatste vier cijfers van een sofi-nummer of creditcardnummer, maskeert in de resultatenset van een query.

![Maskeren op een nummer dat wordt verzonden tussen een SQL-database en zakelijke apps](./media/database-security-overview/azure-database-fig3.png)

Als ander voorbeeld kan een geschikt gegevensmasker worden gedefinieerd om persoonlijk identificeerbare informatie te beschermen. Een ontwikkelaar kan vervolgens productieomgevingen opvragen voor probleemoplossingsdoeleinden zonder de nalevingsvoorschriften te schenden.

Met [dynamische gegevensmaskering in SQL Database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. Dynamische gegevensmaskering wordt ondersteund voor de V12-versie van Azure SQL Database.

[Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking) helpt ongeautoriseerde toegang tot gevoelige gegevens te voorkomen door u in staat te stellen aan te geven hoeveel van de gevoelige gegevens moeten worden weergegeven met minimale impact op de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.

> [!Note]
> Dynamische gegevensmaskering kan worden geconfigureerd door de rollen Azure Database-beheerder, serverbeheerder of beveiligingsfunctionaris.

#### <a name="row-level-security"></a>Beveiliging op rijniveau

Een andere veelvoorkomende beveiligingsvereiste voor multitenant-databases is [Row-Level Security](https://msdn.microsoft.com/library/dn765131.aspx). U deze functie gebruiken om de toegang tot rijen in een databasetabel te beheren op basis van de kenmerken van de gebruiker die een query uitvoert. (Voorbeeldkenmerken zijn groepslidmaatschap en uitvoeringscontext.)

![Rijniveaubeveiliging waarmee een gebruiker toegang heeft tot rijen in een tabel via een client-app](./media/database-security-overview/azure-database-fig4.png)

De logica voor toegangsbeperking bevindt zich in de databaselaag in plaats van in plaats van in de buurt van de gegevens in een andere toepassingslaag. Het databasesysteem past de toegangsbeperkingen toe telkens wanneer gegevenstoegang vanaf een laag wordt geprobeerd. Dit maakt uw beveiligingssysteem betrouwbaarder en robuuster door het verkleinen van de oppervlakte van uw beveiligingssysteem.

Row-Level Security introduceert op basis van predicaat toegangscontrole. Het beschikt over een flexibele, gecentraliseerde evaluatie die rekening kan houden met metadata of andere criteria die de beheerder waar nodig bepaalt. Het predicaat wordt gebruikt als criterium om te bepalen of de gebruiker de juiste toegang tot de gegevens heeft op basis van gebruikerskenmerken. U labelgebaseerde toegangscontrole implementeren met behulp van op predicaat gebaseerdtoegangsbeheer.

## <a name="proactive-monitoring"></a>Proactieve controle

SQL Database helpt uw gegevens te beveiligen door *controle-* en *bedreigingsdetectiemogelijkheden* te bieden.

### <a name="auditing"></a>Controleren

[Azure SQL Database auditing](/azure/sql-database/sql-database-auditing-get-started) vergroot uw mogelijkheid om inzicht te krijgen in gebeurtenissen en wijzigingen die zich voordoen in de database. Voorbeelden zijn updates en query's ten opzichte van de gegevens.

SQL Database auditing houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in uw Azure-opslagaccount. Controle kan u helpen de naleving van de regelgeving te handhaven, de databaseactiviteit te begrijpen en inzicht te krijgen in verschillen en afwijkingen die kunnen duiden op zakelijke problemen of vermoedelijke beveiligingsschendingen. Auditing maakt naleving van nalevingsnormen mogelijk en vergemakkelijkt dit, maar garandeert geen naleving.

U SQL Database-controle gebruiken om:

- **Houd** een controlespoor van geselecteerde gebeurtenissen. U kunt categorieën van database-acties definiëren die moeten worden gecontroleerd.
- **Rapport over** databaseactiviteit. U kunt vooraf geconfigureerde rapporten en een dashboard gebruiken om snel aan de slag te gaan met de rapportage bvavan activiteiten en gebeurtenissen.
- **Rapporten analyseren.** U kunt verdachte gebeurtenissen, ongebruikelijke activiteiten en trends vinden.

Er zijn twee controlemethoden:

-   **Blob-controle:** logboeken worden naar Azure Blob-opslag geschreven. Dit is een nieuwere controlemethode. Het biedt hogere prestaties, ondersteunt controle op objectniveau met hogere granulariteit en is kosteneffectiever.
-   **Tabelcontrole:** logboeken worden naar Azure Table-opslag geschreven.

### <a name="threat-detection"></a>Detectie van bedreigingen

[Geavanceerde bedreigingsbeveiliging voor Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) detecteert verdachte activiteiten die potentiële beveiligingsbedreigingen aangeven. U bedreigingsdetectie gebruiken om te reageren op verdachte gebeurtenissen in de database, zoals SQL-injecties, terwijl deze zich voordoen. Het biedt waarschuwingen en maakt het gebruik van Azure SQL Database auditing mogelijk om de verdachte gebeurtenissen te verkennen.

![Detectie van bedreigingen voor SQL Database en een web-app, met een externe aanvaller en een kwaadaardige insider](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) biedt een reeks geavanceerde SQL-beveiligingsmogelijkheden, waaronder Gegevensdetectie & classificatie, kwetsbaarheidsbeoordeling en bedreigingsdetectie. 

- [Gegevensdetectie & classificatie](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Kwetsbaarheidsbeoordeling](/azure/sql-database/sql-vulnerability-assessment)  
- [Detectie van bedreigingen](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL Advanced Threat Protection](/azure/postgresql/concepts-data-access-and-security-threat-protection) biedt een nieuwe beveiligingslaag, waarmee u potentiële bedreigingen detecteren en erop reageren wanneer deze zich voordoen door beveiligingswaarschuwingen te verstrekken voor afwijkende activiteiten. Gebruikers ontvangen een waarschuwing over verdachte databaseactiviteiten en mogelijke kwetsbaarheden, evenals afwijkende databasetoegang en querypatronen. Geavanceerde bedreigingsbeveiliging voor Azure Database voor PostgreSQL integreert waarschuwingen met Azure Security Center. Type waarschuwingen zijn:

- Toegang vanaf ongebruikelijke locatie
- Toegang vanuit een ongebruikelijk Azure-datacenter 
- Toegang van onbekende opdrachtgever 
- Toegang vanuit een mogelijk schadelijke toepassing 
- Azure-database met Brute force voor PostgreSQL-referenties 

[Azure Database for MySQL Advanced Threat Protection](/azure/mysql/concepts-data-access-and-security-threat-protection) biedt bescherming die vergelijkbaar is met PostgreSQL Advanced Protection.  

## <a name="centralized-security-management"></a>Gecentraliseerd beveiligingsbeheer

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen. Het helpt bij het detecteren van bedreigingen die anders onopgemerkt zouden kunnen blijven, en het werkt met een breed ecosysteem van beveiligingsoplossingen.

[Security Center](../../security-center/security-center-alerts-data-services.md) helpt u gegevens in SQL Database te beveiligen door inzicht te bieden in de beveiliging van al uw servers en databases. Met Security Center u:

- Beleid definiëren voor SQL Database-versleuteling en -controle.
- Controleer de beveiliging van SQL Database-bronnen voor al uw abonnementen.
- Snel beveiligingsproblemen identificeren en oplossen.
- Integreer waarschuwingen van [Azure SQL Database-bedreigingsdetectie](/azure/sql-database/sql-database-threat-detection).

Security Center ondersteunt op rollen gebaseerde toegang.

## <a name="sql-information-protection"></a>SQL-informatiebeveiliging

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) detecteert en classificeert automatisch potentieel gevoelige gegevens, biedt een etiketteringsmechanisme voor het aanhoudend taggen van gevoelige gegevens met classificatiekenmerken en biedt een gedetailleerd dashboard met de classificatiestatus van de database.  

Bovendien berekent het de gevoeligheid van SQL-query's met resultaatset, zodat query's die gevoelige gegevens extraheren expliciet kunnen worden gecontroleerd en de gegevens kunnen worden beschermd. Zie Azure SQL Database Data Discovery and Classification voor meer informatie over SQL Information Protection.

U [SQL Information Protection-beleidsregels](/azure/security-center/security-center-info-protection-policy) configureren in Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

De Azure Marketplace is een online marktplaats voor toepassingen en services waarmee start-ups en onafhankelijke softwareleveranciers (ISV's) hun oplossingen kunnen aanbieden aan Azure-klanten over de hele wereld.
De Azure Marketplace combineert Microsoft Azure-partnerecosystemen in een uniform platform om klanten en partners beter van dienst te zijn. U [een zoekopdracht uitvoeren](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) om databasebeveiligingsproducten weer te geven die beschikbaar zijn in de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure SQL-database beveiligen](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center en Azure SQL Database-service](/azure/security-center/security-center-sql-database)
- [DETECTIE van SQL-database-bedreigingen](/azure/sql-database/sql-database-threat-detection)
- [Prestaties van sql-database verbeteren](/azure/sql-database/sql-database-performance-tutorial)
