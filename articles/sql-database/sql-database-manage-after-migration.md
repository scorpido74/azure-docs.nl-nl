---
title: Afzonderlijke en gepoolde databases beheren na migratie
description: Meer informatie over het beheren van uw database na migratie naar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: ebb512fee0186bed3cc7f49f0525dac43e57da3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256183"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Nieuwe DBA in de cloud – Beheer van uw enkele en gepoolde databases in Azure SQL Database

De overgang van de traditionele zelfbeheerde, zelfbeheerde omgeving naar een PaaS-omgeving kan in het begin een beetje overweldigend lijken. Als app-ontwikkelaar of Een DBA wilt u de kernmogelijkheden van het platform kennen die u zouden helpen uw toepassing beschikbaar, performant, veilig en veerkrachtig te houden - altijd. Dit artikel is bedoeld om precies dat te doen. Het artikel organiseert beknopt resources en geeft u enkele richtlijnen over hoe u de belangrijkste mogelijkheden van SQL Database het beste gebruiken met afzonderlijke en gepoolde databases om uw toepassing efficiënt te beheren en te laten werken en optimale resultaten in de cloud te bereiken. Typisch publiek voor dit artikel zou zijn degenen die:

- Zijn de migratie van hun toepassing(en) naar Azure SQL Database evalueren : uw toepassing(en) moderniseren.
- Zijn in het proces van het migreren van hun toepassing (en) - Lopende migratie scenario.
- Hebben onlangs de migratie naar Azure SQL DB – Nieuwe DBA in de cloud voltooid.

In dit artikel worden enkele van de kernkenmerken van Azure SQL Database besproken als een platform dat u gemakkelijk gebruiken bij het werken met afzonderlijke databases en gepoolde databases in elastische pools. Zij zijn de volgende:

- Database controleren met de Azure-portal
- Bedrijfscontinuïteit en herstel na noodgeval (BCDR)
- Beveiliging en naleving
- Intelligente databasebewaking en -onderhoud
- Gegevensverplaatsing

> [!NOTE]
> Dit artikel is van toepassing op de volgende implementatieopties in Azure SQL Database: afzonderlijke databases en elastische pools. Het is niet van toepassing op de beheerde instantieimplementatieoptie in SQL Database.

## <a name="monitor-databases-using-the-azure-portal"></a>Databases bewaken via de Azure-portal

In de [Azure-portal](https://portal.azure.com/)u het gebruik van afzonderlijke databases controleren door uw database te selecteren en op de **grafiek Met controle** te klikken. Nu wordt een venster **Metrische gegevens** geopend, dat u kunt wijzigen door op de knop **Grafiek bewerken** te klikken. Voeg de volgende metrische gegevens toe:

- CPU-percentage
- DTU-percentage
- Gegevens-I/O-percentage
- Databaseomvangpercentage

Nadat u deze statistieken hebt toegevoegd, u ze blijven bekijken in de grafiek **Monitoring** met meer informatie over het **venster Metriek.** De vier metrische gegevens tonen het gemiddelde gebruikspercentage ten opzichte van de **DTU** van uw database. Zie het [Op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) en [vCore-gebaseerde inkoopmodelartikelen](sql-database-service-tiers-vcore.md) voor meer informatie over servicelagen.  

![Servicelaagbewaking van databaseprestaties.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

U kunt ook meldingen configureren voor prestatiewaarden. Klik op de knop **Melding toevoegen** in het venster **Metrische gegevens**. Volg de wizard om de melding te configureren. U hebt de keuze om een melding weer te geven als de metrische gegevens een bepaalde drempelwaarde overschrijden of als het meetpunt onder een bepaalde drempelwaarde komt.

Als u bijvoorbeeld verwacht dat de workload van de database zal toenemen, kunt u configureren dat er een e-mailmelding wordt verstuurd wanneer de database 80% van een van de prestatiewaarden heeft bereikt. U dit gebruiken als een vroege waarschuwing om erachter te komen wanneer u mogelijk moet overschakelen naar de volgende hoogste rekengrootte.

De prestatiestatistieken kunnen u ook helpen bepalen of u downgraden naar een lagere rekengrootte. Stel dat u een Standard S2-database gebruikt en alle prestatiewaarden aangeven dat de database gemiddeld nooit meer dan 10% gebruikt. Het is dan waarschijnlijk dat de database in Standard S1 goed werkt. Houd echter rekening met workloads die pieken of fluctueren voordat u de beslissing neemt om naar een lagere rekengrootte te gaan.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Bedrijfscontinuïteit en herstel na noodgeval (BCDR)

Business continuity en disaster recovery vaardigheden stellen u in staat om uw bedrijf voort te zetten, zoals gebruikelijk, in geval van een ramp. De ramp kan een gebeurtenis op databaseniveau zijn (iemand laat bijvoorbeeld per ongeluk een cruciale tabel vallen) of een gebeurtenis op datacenterniveau (regionale catastrofe, bijvoorbeeld een tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hoe maak en beheer ik back-ups op SQL Database

U maakt geen back-ups op Azure SQL DB en dat is omdat u dat niet hoeft te doen. SQL Database maakt automatisch een back-up van databases voor u, zodat u zich geen zorgen meer hoeft te maken over het plannen, nemen en beheren van back-ups. Het platform neemt elke week een volledige back-up, differentiële back-up om de paar uur en een logboekback-up om de 5 minuten om ervoor te zorgen dat het herstel na noodgevallen efficiënt is en het gegevensverlies minimaal is. De eerste volledige back-up gebeurt zodra u een database maakt. Deze back-ups zijn beschikbaar voor u voor een bepaalde periode genaamd de "Bewaarperiode" en varieert afhankelijk van de servicelaag die u kiest. SQL Database biedt u de mogelijkheid om te herstellen naar elk moment binnen deze bewaarperiode met behulp van [Point in Time Recovery (PITR).](sql-database-recovery-using-backups.md#point-in-time-restore)

|Servicelaag|Bewaartermijn in dagen|
|---|:---:|
|Basic|7|
|Standard|35|
|Premium|35|
|||

Bovendien u met de [LTR-functie (Long-Term Retention)](sql-database-long-term-retention.md) uw back-upbestanden voor een veel langere periode in het bijzonder, tot 10 jaar, vasthouden en gegevens van deze back-ups op elk punt binnen die periode herstellen. Bovendien worden de databaseback-ups bewaard in geo-gerepliceerde opslag om veerkracht tegen regionale catastrofes te garanderen. U deze back-ups ook op elk gewenst moment binnen de bewaarperiode in een Azure-regio herstellen. Zie [Overzicht bedrijfscontinuïteit](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hoe zorg ik voor bedrijfscontinuïteit in het geval van een ramp op datacenterniveau of regionale catastrofe

Omdat uw databaseback-ups worden opgeslagen in geo-gerepliceerde opslag om ervoor te zorgen dat u in geval van een regionale ramp de back-up herstellen naar een andere Azure-regio. Dit wordt geo-restore genoemd. De RPO (Recovery Point Objective) hiervoor is over het algemeen < 1 uur en de ERT (Geschatte hersteltijd – enkele minuten tot uren).

Voor bedrijfskritieke databases biedt Azure SQL DB actieve georeplicatie. Wat dit in wezen doet is dat het een geo-gerepliceerde secundaire kopie van uw oorspronkelijke database in een andere regio creëert. Als uw database bijvoorbeeld in eerste instantie wordt gehost in de regio Azure West US en u regionale weerbaarheid voor rampen wilt. Je zou een actieve geo replica van de database in West VS te zeggen Oost-VS. Wanneer de ramp toeslaat op West US, u mislukken naar het Oosten van de VS regio. Het configureren ervan in een auto-failovergroep is nog beter omdat dit ervoor zorgt dat de database automatisch niet naar de secundaire in Oost-VS in geval van een ramp. De RPO hiervoor is < 5 seconden en de ERT < 30 seconden.

Als een groep voor automatische failover niet is geconfigureerd, moet uw toepassing actief controleren op een ramp en een failover naar het secundaire starten. U maximaal 4 van dergelijke actieve georeplica's maken in verschillende Azure-regio's. Het wordt nog beter. U hebt ook toegang tot deze secundaire actieve georeplica's voor alleen-lezen toegang. Dit is erg handig om latentie voor een geo-gedistribueerd toepassingsscenario te verminderen.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hoe verandert mijn noodherstelplan van on-premises naar SQL Database

Samengevat, de traditionele on-premises SQL Server setup vereist u om actief te beheren uw beschikbaarheid met behulp van functies zoals Failover Clustering, Database Mirroring, Transaction Replication, of Log Shipping en onderhouden en beheren van back-ups om ervoor te zorgen Bedrijfscontinuïteit. Met SQL Database beheert het platform deze voor u, zodat u zich concentreren op het ontwikkelen en optimaliseren van uw databasetoepassing en zich niet zoveel zorgen hoeft te maken over rampenbeheer. U back-up- en noodherstelplannen configureren en werken met slechts een paar klikken op de Azure-portal (of een paar opdrachten met behulp van de PowerShell API's).

Zie voor meer informatie over disaster recovery: [Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Beveiliging en naleving

SQL Database neemt beveiliging en privacy zeer serieus. Beveiliging binnen SQL Database is beschikbaar op databaseniveau en op platformniveau en is het best te begrijpen wanneer deze in verschillende lagen wordt ingedeeld. Bij elke laag krijg je de controle en zorgen voor optimale beveiliging voor uw toepassing. De lagen zijn:

- Identiteits& verificatie[(SQL-verificatie en Azure Active Directory [AAD]-verificatie](sql-database-manage-logins.md)).
- Monitoringactiviteit[(Auditing en](sql-database-auditing.md) [detectie van bedreigingen).](sql-database-threat-detection.md)
- Bescherming van werkelijke gegevens ([Transparante gegevensversleuteling [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) en [Altijd versleuteld [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Toegang tot gevoelige en bevoorrechte gegevens beheren[(row level security](/sql/relational-databases/security/row-level-security) en [Dynamic Data Masking).](/sql/relational-databases/security/dynamic-data-masking)

[Azure Security Center](https://azure.microsoft.com/services/security-center/) biedt gecentraliseerd beveiligingsbeheer voor workloads die worden uitgevoerd in Azure, on-premises en in andere clouds. U zien of essentiële SQL-databasebeveiliging zoals [Auditing](sql-database-auditing.md) en [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) zijn geconfigureerd op alle resources en beleid maken op basis van uw eigen vereisten.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Welke gebruikersverificatiemethoden worden aangeboden in SQL Database

Er zijn twee verificatiemethoden aangeboden in SQL Database:

- [Azure Active Directory-verificatie](sql-database-aad-authentication.md)
- [SQL-verificatie](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

De traditionele windows-verificatie wordt niet ondersteund. Azure Active Directory (AD) is een gecentraliseerde service voor identiteits- en toegangsbeheer. Hiermee u heel gemakkelijk een Single Sign-on Access (SSO) leveren aan al het personeel in uw organisatie. Dit betekent dat de referenties worden gedeeld in alle Azure-services voor eenvoudigere verificatie. AAD ondersteunt [MFA (Multi Factor Authentication)](sql-database-ssms-mfa-authentication.md) en met een [paar klikken](../active-directory/hybrid/how-to-connect-install-express.md) kan AAD worden geïntegreerd met Windows Server Active Directory. SQL-verificatie werkt precies zoals u het in het verleden hebt gebruikt. U geeft een gebruikersnaam/wachtwoord op en u gebruikers verifiëren naar elke database op een bepaalde SQL Database-server. Hierdoor kunnen SQL Database en SQL Data Warehouse ook multi-factor authenticatie en gastgebruikersaccounts aanbieden binnen een Azure AD-domein. Als u al een Active Directory on-premises hebt, u de map met Azure Active Directory federatieren om uw directory uit te breiden naar Azure.

|**Als je...**|**SQL-database / SQL Data Warehouse**|
|---|---|
|Gebruik Azure Active Directory (AD) liever niet in Azure|[SQL-verificatie gebruiken](sql-database-security-overview.md)|
|On-premises gebruikte AD op SQL Server|[Geef AD vast aan Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)en gebruik Azure AD-verificatie. Hiermee u Single Sign-On gebruiken.|
|Noodzaak om multi-factor authenticatie (MFA) af te dwingen|Mfa vereisen als beleid via [Microsoft Conditional Access](sql-database-conditional-access.md)en gebruik Azure AD [Universal-verificatie met MFA-ondersteuning](sql-database-ssms-mfa-authentication.md).|
|Gastaccounts van Microsoft-accounts (live.com, outlook.com) of andere domeinen (gmail.com)|Azure [AD Universal-verificatie gebruiken](sql-database-ssms-mfa-authentication.md) in SQL Database/Data Warehouse, waarmee azure AD [B2B-samenwerking](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)wordt gebruikt.|
|Zijn aangemeld bij Windows met uw Azure AD-referenties van een federatief domein|Geïntegreerde [verificatie van Azure AD gebruiken](sql-database-aad-authentication-configure.md).|
|Zijn aangemeld bij Windows met referenties van een domein dat niet is gefedereerd met Azure|Geïntegreerde [verificatie van Azure AD gebruiken](sql-database-aad-authentication-configure.md).|
|Middle-tier services hebben die verbinding moeten maken met SQL Database of SQL Data Warehouse|Geïntegreerde [verificatie van Azure AD gebruiken](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hoe beperk of beheer ik de toegang tot de connectiviteit tot mijn database

Er zijn meerdere technieken tot uw beschikking die u zou kunnen gebruiken om een optimale connectiviteit organisatie voor uw toepassing te bereiken.

- Firewallregels
- VNet-serviceeindpunten
- Gereserveerde IP-adressen

#### <a name="firewall"></a>Firewall

Een firewall voorkomt toegang tot uw server vanuit een externe entiteit door alleen specifieke entiteiten toegang te geven tot uw SQL Database-server. Standaard zijn alle verbindingen en databases binnen de SQL Database-server niet toegestaan, behalve verbindingen die afkomstig zijn van andere Azure Services. Met een firewallregel u de toegang tot uw server alleen openen voor entiteiten (bijvoorbeeld een ontwikkelaarsmachine) die u goedkeurt, door het IP-adres van die computer via de firewall toe te staan. Het stelt u ook in staat om een reeks IP's op te geven die u toegang tot de SQL Database-server zou willen toestaan. Ip-adressen van ontwikkelaarsmachines in uw organisatie kunnen bijvoorbeeld in één keer worden toegevoegd door een bereik op te geven op de pagina Firewall-instellingen.

U firewallregels maken op serverniveau of op databaseniveau. IP-firewallregels op serverniveau kunnen worden gemaakt met behulp van de Azure-portal of met SSMS. Zie: [IP-firewallregels maken in SQL Database](sql-database-security-tutorial.md#create-firewall-rules)voor meer informatie over het instellen van een firewallregel op serverniveau en op databaseniveau.

#### <a name="service-endpoints"></a>Service-eindpunten

Standaard is uw SQL-database geconfigureerd om 'Azure-services toegang geven tot server' te maken, wat betekent dat elke virtuele machine in Azure kan proberen verbinding te maken met uw database. Deze pogingen moeten nog steeds worden geverifieerd. Als u echter niet wilt dat uw database toegankelijk is voor Azure IP's, u 'Azure-services toestaan toegang tot server' uitschakelen. Bovendien u [VNet Service-eindpunten](sql-database-vnet-service-endpoint-rule-overview.md)configureren.

Met Service-eindpunten (SE) u uw kritieke Azure-resources alleen blootstellen aan uw eigen virtuele privénetwerk in Azure. Door dit te doen, elimineert u in wezen de toegang van het publiek tot uw bronnen. Het verkeer tussen uw virtuele netwerk naar Azure blijft op het Azure-backbonenetwerk. Zonder SE krijg je gedwongen tunneling packet routing. Uw virtuele netwerk dwingt het internetverkeer naar uw organisatie en het Azure Service-verkeer om over dezelfde route te gaan. Met Service Endpoints u dit optimaliseren omdat de pakketten rechtstreeks van uw virtuele netwerk naar de service op Azure-backbonenetwerk stromen.

![VNet-service-eindpunten](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Gereserveerde IP-adressen

Een andere optie is het inrichten van [gereserveerde IP's](../virtual-network/virtual-networks-reserved-public-ip.md) voor uw VM's en deze specifieke VM IP-adressen toevoegen aan de instellingen voor serverfirewall. Door gereserveerde IP's toe te wijs, bespaart u de moeite om de firewallregels bij te werken met het wijzigen van IP-adressen.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Op welke poort maak ik verbinding met SQL Database

Poort 1433. SQL Database communiceert via deze poort. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, moet u een uitgaande regel toevoegen aan de firewall-instellingen van uw organisatie. Als richtlijn moet u voorkomen dat poort 1433 buiten de Azure-grens wordt blootgesteld.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hoe kan ik de activiteit op mijn server en database in SQL Database controleren en reguleren

#### <a name="sql-database-auditing"></a>SQL Database Auditing

Met SQL Database u On Auditing inschakelen om databasegebeurtenissen bij te houden. [SQL Database Auditing](sql-database-auditing.md) registreert databasegebeurtenissen en schrijft deze op in een controlelogboekbestand in uw Azure Storage Account. Auditing is vooral handig als u van plan bent om inzicht te krijgen in mogelijke schendingen van de beveiliging en beleid, naleving van de regelgeving te handhaven, enz. Hiermee u bepaalde categorieën gebeurtenissen definiëren en configureren waarvan u denkt dat deze controle moet controleren en op basis daarvan u vooraf geconfigureerde rapporten en een dashboard krijgen om een overzicht te krijgen van gebeurtenissen die zich in uw database voordoen. U deze controlebeleidsregels toepassen op databaseniveau of op serverniveau. Een handleiding voor het inschakelen van controle voor uw server/database, zie: [SQL Database Auditing inschakelen](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Detectie van bedreigingen

Met [bedreigingsdetectie](sql-database-threat-detection.md)krijgt u de mogelijkheid om te reageren op beveiligings- of beleidsschendingen die door Auditing worden ontdekt, zeer gemakkelijk worden ontdekt. U hoeft geen beveiligingsexpert te zijn om mogelijke bedreigingen of schendingen in uw systeem aan te pakken. Bedreigingsdetectie heeft ook een aantal ingebouwde mogelijkheden, zoals SQL Injection-detectie. SQL Injection is een poging om de gegevens te wijzigen of te compromitteren en een veel voorkomende manier om een databasetoepassing in het algemeen aan te vallen. Bedreigingsdetectie voert meerdere sets algoritmen uit die potentiële kwetsbaarheden en SQL-injectieaanvallen detecteren, evenals afwijkende databasetoegangspatronen (zoals toegang vanaf een ongebruikelijke locatie of door een onbekende opdrachtgever). Beveiligingsfunctionarissen of andere aangewezen beheerders ontvangen een e-mailmelding als er een bedreiging wordt gedetecteerd in de database. Elke melding bevat details over de verdachte activiteit en aanbevelingen over hoe de dreiging verder kan worden onderzocht en beperkt. Zie: [Bedreigingsdetectie inschakelen](sql-database-security-tutorial.md#enable-security-features)voor meer informatie over het inschakelen van bedreigingsdetectie.

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hoe bescherm ik mijn gegevens in het algemeen op SQL Database

Versleuteling biedt een sterk mechanisme om uw gevoelige gegevens te beschermen en te beveiligen tegen indringers. Uw versleutelde gegevens is van geen nut voor de indringer zonder de decryptie sleutel. Zo voegt het een extra beveiligingslaag toe bovenop de bestaande beveiligingslagen die zijn ingebouwd in SQL Database. Er zijn twee aspecten om uw gegevens in SQL Database te beschermen:

- Uw gegevens die in de gegevens en logboekbestanden rusten
- Uw gegevens die tijdens de vlucht zijn

In SQL Database zijn uw gegevens standaard in rust in de gegevens- en logbestanden op het subsysteem voor opslag volledig en altijd versleuteld via [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Uw back-ups zijn ook versleuteld. Met TDE zijn er geen wijzigingen vereist aan uw toepassingszijde die toegang heeft tot deze gegevens. De encryptie en decryptie gebeuren transparant; vandaar de naam.
Voor het beschermen van uw gevoelige gegevens tijdens de vlucht en in rust, SQL Database biedt een functie genaamd [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE is een vorm van client-side encryptie die gevoelige kolommen in uw database versleutelt (dus ze zijn in cijfertekst aan databasebeheerders en onbevoegde gebruikers). De server ontvangt de versleutelde gegevens om te beginnen. De sleutel voor Always Encrypted wordt ook opgeslagen aan de clientzijde, zodat alleen geautoriseerde clients de gevoelige kolommen kunnen decoderen. De server- en gegevensbeheerders kunnen de gevoelige gegevens niet zien omdat de versleutelingssleutels op de client zijn opgeslagen. AE versleutelt gevoelige kolommen in de tabel van begin tot eind, van onbevoegde clients tot de fysieke schijf. AE ondersteunt vandaag de dag gelijkheidsvergelijkingen, zodat DBA's versleutelde kolommen kunnen blijven opvragen als onderdeel van hun SQL-opdrachten. Always Encrypted kan worden gebruikt met verschillende belangrijke opslagopties, zoals [Azure Key Vault,](sql-database-always-encrypted-azure-key-vault.md)Windows Certificate Store en lokale hardwarebeveiligingsmodules.

|**Kenmerken**|**Altijd versleuteld**|**Transparante gegevensversleuteling**|
|---|---|---|
|**Versleutelingsperiode**|End-to-end|Rustgegevens|
|**Databaseserver heeft toegang tot gevoelige gegevens**|Nee|Ja, aangezien versleuteling voor de gegevens in rust is|
|**Toegestane T-SQL-bewerkingen**|Gelijkheidsvergelijking|Alle T-SQL-oppervlakte is beschikbaar|
|**App-wijzigingen die nodig zijn om de functie te gebruiken**|Minimaal|Zeer minimaal|
|**Granulariteit van versleuteling**|Kolomniveau|databaseniveau|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hoe kan ik de toegang tot gevoelige gegevens in mijn database beperken

Elke toepassing heeft een bepaald beetje gevoelige gegevens in de database die moeten worden beschermd tegen zichtbaar zijn voor iedereen. Bepaalde medewerkers binnen de organisatie moeten deze gegevens bekijken, maar anderen zouden deze gegevens niet moeten kunnen bekijken. Een voorbeeld is het loon van werknemers. Een manager zou toegang nodig hebben tot de looninformatie voor hun directe rapporten, maar de individuele teamleden moeten geen toegang hebben tot de looninformatie van hun collega's. Een ander scenario is gegevensontwikkelaars die mogelijk interactie hebben met gevoelige gegevens tijdens ontwikkelingsfasen of het testen van bijvoorbeeld SSN's van klanten. Deze informatie hoeft opnieuw niet te worden blootgesteld aan de ontwikkelaar. In dergelijke gevallen moeten uw gevoelige gegevens worden gemaskeerd of helemaal niet worden blootgesteld. SQL Database biedt twee van dergelijke benaderingen om te voorkomen dat onbevoegde gebruikers gevoelige gegevens kunnen bekijken:

[Dynamic Data Masking](sql-database-dynamic-data-masking-get-started.md) is een functie voor het maskeren van gegevens waarmee u de blootstelling aan gevoelige gegevens beperken door deze te maskeren aan niet-bevoorrechte gebruikers op de toepassingslaag. U definieert een maskerregel die een maskerpatroon kan maken (bijvoorbeeld om alleen de laatste vier cijfers van een nationale ID-SSN weer te geven: XXX-XX-0000 en het meeste als Xs te markeren) en te bepalen welke gebruikers moeten worden uitgesloten van de maskerregel. Het maskeren gebeurt on-the-fly en er zijn verschillende maskeren functies beschikbaar voor verschillende categorieën van gegevens. Met dynamische gegevensmaskering u automatisch gevoelige gegevens in uw database detecteren en maskeren toepassen.

[Met row level-beveiliging](/sql/relational-databases/security/row-level-security) u de toegang op rijniveau beheren. Bepaalde rijen in een databasetabel op basis van de gebruiker die de query uitvoert (groepslidmaatschap of uitvoeringscontext) zijn dus verborgen. De toegangsbeperking wordt uitgevoerd op de databaselaag in plaats van in een toepassingslaag, om de logica van uw app te vereenvoudigen. U begint met het maken van een filterpredicaat, het filteren van rijen die niet worden weergegeven en het beveiligingsbeleid bepaalt vervolgens wie toegang heeft tot deze rijen. Ten slotte voert de eindgebruiker zijn zoekopdracht uit en, afhankelijk van het voorrecht van de gebruiker, bekijkt hij deze beperkte rijen of kunnen ze ze helemaal niet zien.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hoe beheer ik versleutelingssleutels in de cloud

Er zijn belangrijke beheeropties voor zowel Always Encrypted (client-side encryption) als Transparent Data Encryption (encryptie in rust). Het wordt aanbevolen om regelmatig encryptiesleutels te roteren. De rotatiefrequentie moet in overeenstemming zijn met zowel uw interne organisatievoorschriften als nalevingsvereisten.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

Er is een twee-key hiërarchie in TDE - de gegevens in elke gebruikersdatabase wordt versleuteld door een symmetrische AES-256 database-unieke database encryptie sleutel (DEK), die op zijn beurt wordt versleuteld door een server-unieke asymmetrische RSA 2048 master key. De hoofdsleutel kan worden beheerd:

- Automatisch door het platform - SQL Database.
- Of door azure [key vault](sql-database-always-encrypted-azure-key-vault.md) te gebruiken als de sleutelwinkel.

Standaard wordt de hoofdsleutel voor transparante gegevensversleuteling voor het gemak beheerd door de SQL Database-service. Als uw organisatie controle over de hoofdsleutel wil, is er een optie om Azure Key Vault(sql-database-always-encrypted-azure-key-vault.md) als sleutelarchief te gebruiken. Door Azure Key Vault te gebruiken, neemt uw organisatie de controle over de beheer-, rotatie- en machtigingsbesturingselementen voor sleutelvoorzieningen. [Het draaien of schakelen van het type TDE-hoofdsleutel](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) is snel, omdat de DEK alleen opnieuw wordt versleuteld. Voor organisaties met een scheiding van rollen tussen beveiliging en gegevensbeheer kan een beveiligingsbeheerder het belangrijkste materiaal voor de TDE-hoofdsleutel in Azure Key Vault inrichten en een Azure Key Vault-sleutel-id verstrekken aan de databasebeheerder die moet worden gebruikt voor versleuteling in rust op een server. De Key Vault is zo ontworpen dat Microsoft geen encryptiesleutels ziet of eruit haalt. U krijgt ook een gecentraliseerd beheer van sleutels voor uw organisatie.

#### <a name="always-encrypted"></a>Altijd versleuteld

Er is ook een [twee-toets hiërarchie](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) in Always Encrypted - een kolom van gevoelige gegevens wordt versleuteld door een AES 256-kolom encryptiesleutel (CEK), die op zijn beurt wordt versleuteld door een kolom master key (CMK). De clientdrivers die voor Always Encrypted worden geleverd, hebben geen beperkingen op de lengte van CMK's. De versleutelde waarde van de CEK wordt opgeslagen in de database en de CMK wordt opgeslagen in een vertrouwde sleutelwinkel, zoals Windows Certificate Store, Azure Key Vault of een hardwarebeveiligingsmodule.

- Zowel de [CEK als cmk](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kunnen worden gedraaid.
- CEK-rotatie is een grootte van de gegevensbewerking en kan tijdsintensief zijn, afhankelijk van de grootte van de tabellen met de versleutelde kolommen. Daarom is het verstandig om cek-rotaties dienovereenkomstig te plannen.
- CMK-rotatie verstoort echter niet de prestaties van de database en kan worden gedaan met gescheiden rollen.

In het volgende diagram worden de opties voor het belangrijkste archief voor de kolomhoofdsleutels weergegeven in Always Encrypted

![Altijd versleutelde CMK-winkelproviders](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hoe kan ik het verkeer tussen mijn organisatie en SQL Database optimaliseren en beveiligen

Het netwerkverkeer tussen uw organisatie en SQL Database wordt over het algemeen over het openbare netwerk doorgestuurd. Als u er echter voor kiest om dit pad te optimaliseren en het veiliger te maken, u Express Route bekijken. Met de Express-route u uw bedrijfsnetwerk in wezen uitbreiden naar het Azure-platform via een privéverbinding. Door dit te doen, ga je niet over het openbare internet. U krijgt ook een hogere beveiliging, betrouwbaarheid en routing optimalisatie die zich vertaalt naar lagere netwerk latencies en veel hogere snelheden dan je normaal zou ervaren gaan over het openbare internet. Als u van plan bent een aanzienlijk deel van de gegevens over te dragen tussen uw organisatie en Azure, kan het gebruik van Express Route kostenvoordelen opleveren. U kiezen uit drie verschillende connectiviteitsmodellen voor de verbinding van uw organisatie met Azure:

- [Cloud Exchange Co-locatie](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route u ook barsten tot 2x de bandbreedte limiet die u koopt zonder extra kosten. Het is ook mogelijk om cross region connectivity te configureren via express route. Zie: [Routepartners en peeringlocaties](../expressroute/expressroute-locations.md)voor ziet in een lijst met ER-connectiviteitsproviders. In de volgende artikelen wordt Express Route nader beschreven:

- [Introductie op Express Route](../expressroute/expressroute-introduction.md)
- [Vereisten](../expressroute/expressroute-prerequisites.md)
- [Werkstromen](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Voldoet SQL Database aan alle wettelijke vereisten en hoe helpt dat bij de naleving van mijn eigen organisatie

SQL Database voldoet aan een reeks regelgevende compliancies. Als u de nieuwste reeks compliancies wilt bekijken waaraan SQL Database voldoet, gaat u naar het [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) en zoomt u in op de compliancies die belangrijk zijn voor uw organisatie om te zien of SQL Database is opgenomen onder de compatibele Azure-services. Het is belangrijk op te merken dat SQL Database weliswaar is gecertificeerd als een compatibele service, maar dat het helpt bij de naleving van de service van uw organisatie, maar deze niet automatisch garandeert.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligente databasebewaking en -onderhoud na migratie

Zodra u uw database naar SQL Database hebt gemigreerd, wilt u uw database controleren (bijvoorbeeld controleren hoe het gebruik van resources is of DBCC-controles) en regelmatig onderhoud uitvoeren (bijvoorbeeld indexen, statistieken, enz.) opnieuw opbouwen of reorganiseren. Gelukkig is SQL Database intelligent in de zin dat het de historische trends en geregistreerde statistieken en statistieken gebruikt om u proactief te helpen uw database te bewaken en te onderhouden, zodat uw toepassing altijd optimaal wordt uitgevoerd. In sommige gevallen kan Azure SQL DB automatisch onderhoudstaken uitvoeren, afhankelijk van uw configuratie-instelling. Er zijn drie facetten om uw database in SQL Database te bewaken:

- Prestatiebewaking en optimalisatie.
- Beveiligingsoptimalisatie.
- Kostenoptimalisatie.

### <a name="performance-monitoring-and-optimization"></a>Prestatiebewaking en -optimalisatie

Met Query Performance Insights u op maat gemaakte aanbevelingen krijgen voor uw databaseworkload, zodat uw toepassingen altijd op een optimaal niveau kunnen blijven draaien. U het ook zo instellen dat deze aanbevelingen automatisch worden toegepast en u geen moeite hoeft te doen om onderhoudstaken uit te voeren. Met Index Advisor u automatisch indexaanbevelingen implementeren op basis van uw werkbelasting - dit wordt Auto-Tuning genoemd. De aanbevelingen evolueren naarmate de werkbelasting van uw toepassing verandert om u de meest relevante suggesties te geven. U krijgt ook de mogelijkheid om deze aanbevelingen handmatig te bekijken en naar eigen inzicht toe te passen.  

### <a name="security-optimization"></a>Beveiligingsoptimalisatie

SQL Database biedt bruikbare beveiligingsaanbevelingen om u te helpen uw gegevens- en bedreigingsdetectie te beveiligen voor het identificeren en onderzoeken van verdachte databaseactiviteiten die een potentiële thread voor de database kunnen vormen. [Kwetsbaarheidsbeoordeling](sql-vulnerability-assessment.md) is een databasescan- en rapportageservice waarmee u de beveiligingsstatus van uw databases op schaal controleren en beveiligingsrisico's identificeren en afstappen van een door u gedefinieerde beveiligingsbasislijn. Na elke scan wordt een aangepaste lijst met bruikbare stappen en herstelscripts verstrekt, evenals een beoordelingsrapport dat kan worden gebruikt om te voldoen aan de nalevingsvereisten.

Met Azure Security Center identificeert u de beveiligingsaanbevelingen over de hele linie en past u deze met één klik toe.

### <a name="cost-optimization"></a>Kostenoptimalisatie

Azure SQL-platform analyseert de gebruiksgeschiedenis in de databases in een server om kostenoptimalisatieopties voor u te evalueren en aan te bevelen. Deze analyse duurt meestal twee weken om bruikbare aanbevelingen te analyseren en op te bouwen. Elastische pool is een dergelijke optie. De aanbeveling wordt op de portal weergegeven als een banner:

![aanbevelingen voor elastische zwembaden](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

U deze analyse ook bekijken onder de sectie Adviseur:

![elastische zwembad aanbevelingen-adviseur](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hoe controleer ik de prestaties en het gebruik van resources in SQL Database

In SQL Database u gebruikmaken van de intelligente inzichten van het platform om de prestaties te controleren en dienovereenkomstig af te stemmen. U de prestaties en het gebruik van resources in SQL Database controleren met de volgende methoden:

#### <a name="azure-portal"></a>Azure Portal

De Azure-portal toont het gebruik van een database door de database te selecteren en op de grafiek in het deelvenster Overzicht te klikken. U de grafiek wijzigen om meerdere statistieken weer te geven, waaronder het CPU-percentage, het DTU-percentage, het Io-percentage gegevens, het percentage sessies en het percentage databasegrootte.

![Controlegrafiek](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Bewakingsgrafiek2](./media/sql-database-manage-after-migration/chart.png)

In deze grafiek u waarschuwingen ook configureren op resource. Met deze waarschuwingen u reageren op resourcevoorwaarden met een e-mail, naar een HTTPS/HTTP-eindpunt schrijven of een actie uitvoeren. Zie [Waarschuwingen maken voor](sql-database-insights-alerts-portal.md)meer informatie .

#### <a name="dynamic-management-views"></a>Dynamische beheerweergaven

U de dynamische beheerweergave [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) opvragen om de geschiedenis van resourceverbruiksstatistieken van het laatste uur en de systeemcatalogusweergave [van sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) te retourneren om de geschiedenis van de afgelopen 14 dagen terug te geven.

#### <a name="query-performance-insight"></a>Inzicht in queryprestaties

[Met Query Performance Insight](sql-database-query-performance.md) u een geschiedenis zien van de belangrijkste query's die nuttig zijn en langlopende query's voor een specifieke database. U TOP-query's snel identificeren op basis van resourcegebruik, duur en uitvoeringsfrequentie. U query's bijhouden en regressie detecteren. Voor deze functie moet [queryarchief](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) zijn ingeschakeld en actief zijn voor de database.

![Inzicht in queryprestaties](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (Preview) in Azure Monitor-logboeken

[Met Azure Monitor-logboeken](../azure-monitor/insights/azure-sql.md) u belangrijke Azure SQL-databaseprestatiestatistieken verzamelen en visualiseren, met ondersteuning voor maximaal 150.000 SQL-databases en 5.000 SQL Elastic-pools per werkruimte. U het gebruiken om meldingen te controleren en te ontvangen. U SQL Database en elastische poolstatistieken voor meerdere Azure-abonnementen en elastische pools controleren en worden gebruikt om problemen op elke laag van een toepassingsstack te identificeren.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Ik merk prestatieproblemen: hoe verschilt mijn SQL Database-probleemoplossingsmethode van SQL Server

Een groot deel van de probleemoplossingstechnieken die u zou gebruiken voor het diagnosticeren van query- en databaseprestatieproblemen, blijft hetzelfde. Immers dezelfde SQL Server-engine voedt de cloud. Het platform - Azure SQL DB heeft echter 'intelligentie' ingebouwd. Het kan u helpen om prestatieproblemen nog gemakkelijker op te lossen en te diagnosticeren. Het kan ook een aantal van deze corrigerende acties uit te voeren namens u en in sommige gevallen, proactief vast te stellen - automatisch.

Uw benadering van het oplossen van prestatieproblemen kan aanzienlijk profiteren door het gebruik van intelligente functies zoals [Query Performance Insight (QPI)](sql-database-query-performance.md) en [Database Advisor](sql-database-advisor.md) in combinatie en dus verschilt het verschil in methodologie in dat opzicht - u hoeft niet langer het handmatige werk te doen van het slijpen van de essentiële details die u kunnen helpen het probleem op te lossen. Het platform doet het harde werk voor u. Een voorbeeld daarvan is QPI. Met QPI u helemaal naar beneden boren naar het queryniveau en kijken naar de historische trends en uitzoeken wanneer de query precies is teruggevallen. De Database Advisor geeft u aanbevelingen over dingen die u kunnen helpen uw algehele prestaties in het algemeen te verbeteren, zoals - ontbrekende indexen, dropping indexen, parameterisering van uw query's, enz.

Met het oplossen van problemen met de prestaties, is het belangrijk om te bepalen of het alleen de toepassing of de database die het ondersteunt, dat is van invloed op uw prestaties van de toepassing. Vaak ligt het prestatieprobleem in de toepassingslaag. Het kan de architectuur of het patroon voor gegevenstoegang zijn. Overweeg bijvoorbeeld dat u een spraakzame toepassing hebt die gevoelig is voor netwerklatentie. In dit geval lijdt uw toepassing omdat er veel korte verzoeken heen en weer gaan ("spraakzaam") tussen de toepassing en de server en op een overbelast netwerk, deze roundtrips snel optellen. Als u de prestaties in dit geval wilt verbeteren, u [Batchquery's](sql-database-performance-guidance.md#batch-queries)gebruiken. Het gebruik van batches helpt u enorm, want nu worden uw verzoeken verwerkt in een batch; zo, helpt u bezuinigen op de retourlatentie en het verbeteren van uw applicatie prestaties.

Als u bovendien een verslechtering van de algehele prestaties van uw database opmerkt, u de dynamische beheerweergaven [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) controleren om het cpu-, IO- en geheugenverbruik te begrijpen. Uw prestaties misschien beïnvloed omdat uw database is uitgehongerd van middelen. Het kan zijn dat u de rekengrootte en/of servicelaag moet wijzigen op basis van de groeiende en krimpende workloadvereisten.

Zie: [Uw database afstemmen](sql-database-performance-guidance.md#tune-your-database)voor een uitgebreide reeks aanbevelingen voor het afstemmen van prestatieproblemen.

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hoe zorg ik ervoor dat ik de juiste servicelaag en rekengrootte gebruik

SQL Database biedt verschillende servicelagen Basic, Standard en Premium. Elke servicelaag krijgt u een gegarandeerde voorspelbare prestaties die aan die servicelaag zijn gekoppeld. Afhankelijk van uw werkbelasting u barsten van activiteit hebben waarbij uw resourcegebruik het plafond van de huidige rekengrootte waarin u zich bevindt, kan raken. In dergelijke gevallen is het nuttig om eerst te evalueren of een tuning kan helpen (bijvoorbeeld het toevoegen of wijzigen van een index, enz.). Als u nog steeds limietproblemen ondervindt, u overwegen om over te gaan naar een hogere servicelaag of rekengrootte.

|**Servicelaag**|**Veelvoorkomende gebruiksscenario's**|
|---|---|
|**Basic**|Toepassingen met een handvol gebruikers en een database die geen hoge gelijktijdigheids-, schaal- en prestatievereisten heeft. |
|**Standaard**|Toepassingen met een aanzienlijke gelijktijdigheid, schaal en prestatie-eisen in combinatie met lage tot gemiddelde IO-eisen. |
|**Premium**|Toepassingen met veel gelijktijdige gebruikers, hoge CPU / geheugen, en hoge IO-eisen. Hoge gelijktijdigheid, hoge doorvoer en latentiegevoelige apps kunnen gebruikmaken van het Premium-niveau. |
|||

Om ervoor te zorgen dat u op de juiste rekengrootte bent, u het verbruik van uw query- en databasebron controleren via een van de bovengenoemde manieren in Hoe controleer ik de prestaties en het gebruik van resources in SQL Database. Mocht u merken dat uw query's / databases zijn consequent actief warm op CPU / Geheugen etc. u overwegen opschalen naar een hogere rekengrootte. Evenzo, als je merkt dat zelfs tijdens je piekuren, je niet lijkt te gebruiken van de middelen zo veel; overwegen om af te schalen van de huidige rekengrootte.

Als u een SaaS-apppatroon of een databaseconsolidatiescenario hebt, u overwegen een elasticpool te gebruiken voor kostenoptimalisatie. Elastic pool is een geweldige manier om database consolidatie en kosten-optimalisatie te bereiken. Zie [Zwembaden en databases beheren](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)voor meer informatie over het beheren van meerdere databases met behulp van elastic pool.

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Hoe vaak moet ik databaseintegriteitscontroles uitvoeren voor mijn database

SQL Database maakt gebruik van een aantal slimme technieken waarmee het bepaalde klassen van gegevensbeschadiging automatisch en zonder gegevensverlies kan verwerken. Deze technieken zijn ingebouwd in de service en worden benut door de service wanneer dat nodig is. Op regelmatige basis worden uw databaseback-ups in de hele service getest door ze te herstellen en DBCC CHECKDB erop uit te voeren. Als er problemen zijn, pakt SQL Database deze proactief aan. [Automatische paginareparatie](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) wordt gebruikt voor het herstellen van pagina's die beschadigd zijn of problemen met de gegevensintegriteit hebben. De databasepagina's worden altijd geverifieerd met de standaardINSTELLING CHECKSum die de integriteit van de pagina verifieert. SQL Database bewaakt en controleert proactief de gegevensintegriteit van uw database en pakt deze, als er zich problemen voordoen, met de hoogste prioriteit. Naast deze, u ervoor kiezen om optioneel uw eigen integriteitcontroles uit te voeren op uw wil.  Zie [Gegevensintegriteit in SQL-database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) voor meer informatie

## <a name="data-movement-after-migration"></a>Gegevensverplaatsing na migratie

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Hoe kan ik gegevens exporteren en importeren als BACPAC-bestanden uit SQL Database

- **Exporteren:** U uw Azure SQL-database exporteren als een BACPAC-bestand vanuit de Azure-portal

   ![database-export](./media/sql-database-export/database-export1.png)

- **Importeren:** U gegevens als BACPAC-bestand ook importeren in de database met behulp van de Azure-portal.

   ![database importeren](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hoe synchroniseer ik gegevens tussen SQL Database en SQL Server

Je hebt verschillende manieren om dit te bereiken:

- **[Gegevenssynchronisatie](sql-database-sync-data.md)** : met deze functie u gegevens bi-directioneel synchroniseren tussen meerdere on-premises SQL Server-databases en SQL Database. Als u wilt synchroniseren met on-premises SQL Server-databases, moet u de synchronisatieagent op een lokale computer installeren en configureren en de uitgaande TCP-poort 1433 openen.
- **[Transactiereplicatie](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** : met transactiereplicatie u uw gegevens synchroniseren van on-premises naar Azure SQL DB, waarbij de uitgever op locatie wordt uitgevoerd en de Azure SQL DB de abonnee is. Voor nu wordt alleen deze setup ondersteund. Zie Voor meer informatie over het migreren van uw gegevens van on-premises naar Azure SQL met minimale downtime: [Transactiereplicatie gebruiken](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [SQL Database](sql-database-technical-overview.md).
