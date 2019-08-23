---
title: Overzicht van Azure data base Security | Microsoft Docs
description: Dit artikel bevat een overzicht van de beveiligings functies van Azure data base.
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
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906062"
---
# <a name="azure-database-security-overview"></a>Overzicht van Azure data base-beveiliging

Beveiliging is een belangrijkste reden voor het beheren van data bases en is altijd een prioriteit voor Azure SQL Database. Azure SQL Database ondersteunt verbindings beveiliging met firewall regels en verbindings versleuteling. Het ondersteunt verificatie met de gebruikers naam en het wacht woord en de verificatie van Azure Active Directory (Azure AD), die gebruikmaken van identiteiten die worden beheerd door Azure Active Directory. Autorisatie maakt gebruik van op rollen gebaseerd toegangs beheer.

Azure SQL Database ondersteunt versleuteling door real-time versleuteling en ontsleuteling van data bases, gekoppelde back-ups en transactie logboek bestanden in rust te brengen zonder dat de toepassing hoeft te worden gewijzigd.

Micro soft biedt extra manieren om bedrijfs gegevens te versleutelen:

-   Versleuteling op celniveau is beschikbaar voor het versleutelen van specifieke kolommen of zelfs gegevens cellen met verschillende versleutelings sleutels.
-   Als u een Hardware Security module of Centraal beheer van uw versleutelings sleutel hiërarchie nodig hebt, kunt u overwegen Azure Key Vault te gebruiken met SQL Server in een Azure virtual machine (VM).
-   Always Encrypted (momenteel in Preview) is versleuteling transparant voor toepassingen. Ook kunnen clients gevoelige gegevens binnen client toepassingen versleutelen zonder de versleutelings sleutels met SQL Database te delen.

Met Azure SQL Database controle kunnen ondernemingen gebeurtenissen vastleggen in een audit logboek in Azure Storage. SQL Database Auditing kan ook worden geïntegreerd met Microsoft Power BI om het inzoomen op rapporten en analyses mogelijk te maken.

Azure SQL-data bases kunnen nauw keurig worden beveiligd om te voldoen aan de meeste regelgevende of beveiligings vereisten, waaronder HIPAA, ISO 27001/27002 en PCI DSS niveau 1. Een actuele lijst met beveiligings nalevings certificeringen is beschikbaar op de [Microsoft Azure Trust Center-site](https://azure.microsoft.com/support/trust-center/services/).

In dit artikel worden de basis beginselen besproken van het beveiligen van Microsoft Azure SQL-data bases voor gestructureerde, tabellaire en relationele gegevens. Dit artikel helpt u in het bijzonder om aan de slag te gaan met resources voor gegevensbeveiliging, toegangsbeheer en proactieve controle.

## <a name="protection-of-data"></a>Beveiliging van gegevens

SQL Database helpt uw gegevens te beveiligen door versleuteling te bieden:

- Voor gegevens in beweging via [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- Voor gegevens in rust door [transparante gegevens versleuteling](https://go.microsoft.com/fwlink/?LinkId=526242).
- Voor gegevens die via [Always encrypted](https://msdn.microsoft.com/library/mt163865.aspx)worden gebruikt.

Andere manieren om uw gegevens te versleutelen die u kunt overwegen:

-   [Versleuteling op celniveau](https://msdn.microsoft.com/library/ms179331.aspx) om specifieke kolommen of zelfs cellen met gegevens met verschillende versleutelingssleutels te versleutelen.
-   [Azure Key Vault met SQL Server in een Azure-VM](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), als u een Hardware Security module of Centraal beheer van uw versleutelings sleutel hiërarchie nodig hebt.

### <a name="encryption-in-motion"></a>Versleuteling in beweging

Een veelvoorkomend probleem voor alle client-en server toepassingen is de nood zaak van privacy naarmate gegevens worden verplaatst via open bare en particuliere netwerken. Als gegevens die via een netwerk worden verplaatst, niet zijn versleuteld, is er een kans dat deze kan worden vastgelegd en gestolen door onbevoegde gebruikers. Wanneer u met database Services werkt, moet u ervoor zorgen dat de gegevens worden versleuteld tussen de data base-client en-server. Zorg er ook voor dat de gegevens worden versleuteld tussen database servers die communiceren met elkaar en met toepassingen in de middelste laag.

Een probleem bij het beheren van een netwerk is het beveiligen van gegevens die worden verzonden tussen toepassingen via een niet-vertrouwd netwerk. U kunt [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) gebruiken om servers en clients te verifiëren en vervolgens gebruiken voor het versleutelen van berichten tussen de geverifieerde partijen.

In het verificatie proces verzendt een TLS/SSL-client een bericht naar een TLS/SSL-server. De server reageert met de informatie die de server moet verifiëren. De client en server voeren een extra uitwisseling van sessie sleutels uit en het dialoog venster voor verificatie wordt beëindigd. Wanneer de verificatie is voltooid, kan de met SSL beveiligde communicatie tussen de server en de client worden gestart via de symmetrische versleutelings sleutels die tijdens het verificatie proces zijn ingesteld.

Voor alle verbindingen met Azure SQL Database is te allen tijde versleuteling (TLS/SSL) vereist terwijl gegevens ' onderweg ' zijn naar en van de data base. SQL Database gebruikt TLS/SSL om servers en clients te verifiëren en vervolgens te gebruiken voor het versleutelen van berichten tussen de geverifieerde partijen. 

In de connection string van uw toepassing moet u para meters opgeven om de verbinding te versleutelen en niet om het server certificaat te vertrouwen. (Dit gebeurt als u uw connection string uit de Azure Portal kopieert.) Als dat niet het geval is, wordt de identiteit van de server niet gecontroleerd en is deze vatbaar voor man-in-the-middle-aanvallen. Voor het ADO.net-stuur programma zijn `Encrypt=True` deze Connection String-para meters en. `TrustServerCertificate=False`

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

U kunt verschillende voorzorgsmaatregelen nemen om de data base te beveiligen. Zo kunt u bijvoorbeeld een beveiligd systeem ontwerpen, vertrouwelijke assets versleutelen en een firewall bouwen rond de database servers. Maar in een scenario waarin de fysieke media (zoals stations of back-uptapes) worden gestolen, kan een kwaadwillende partij de data base alleen herstellen of koppelen en door de gegevens bladeren.

Eén oplossing is het versleutelen van de gevoelige gegevens in de-data base en het beveiligen van de sleutels die worden gebruikt om de gegevens te versleutelen met een certificaat. Met deze oplossing wordt voor komen dat niemand zonder de sleutel de gegevens gebruikt, maar dit soort beveiliging moet worden gepland.

Om dit probleem op te lossen, SQL Server en SQL Database ondersteuning voor [transparante gegevens versleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparante gegevens versleuteling versleutelt SQL Server en SQL Database gegevens bestanden, ook wel versleutelings gegevens in rust.

Transparante gegevens versleuteling helpt bij het beschermen van de dreiging van schadelijke activiteiten. Er wordt in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden in rust uitgevoerd zonder dat de toepassing hoeft te worden gewijzigd.  

Transparante gegevens versleuteling versleutelt de opslag van een volledige data base met behulp van een symmetrische sleutel die de database versleutelings sleutel wordt genoemd. In SQL Database wordt de database versleutelings sleutel beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke SQL Database-Server.

Als een Data Base zich in een geo-DR-relatie bevindt, wordt deze beveiligd door een andere sleutel op elke server. Als twee data bases zijn verbonden met dezelfde server, delen ze hetzelfde ingebouwde certificaat. Micro soft roteert deze certificaten ten minste elke 90 dagen automatisch. 

Zie [transparent Data Encryption (transparante gegevens versleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption-tde)) voor meer informatie.

### <a name="encryption-in-use-client"></a>Versleuteling in gebruik (client)

Bij de meeste inbreuken op gegevens is het mogelijk om essentiële gegevens te stelen, zoals creditcard nummers of persoons gegevens. Data bases kunnen een financieel Troves van gevoelige informatie zijn. Ze kunnen persoonlijke gegevens van klanten (zoals nationale identificatie nummers), vertrouwelijke concurrentie gegevens en intellectueel eigendom bevatten. Verloren of gestolen gegevens, met name klant gegevens, kunnen leiden tot brand schade, een competitieve nadeel en serieuze geld, zelfs rechts zaken.

![De functie Always Encrypted wordt geïllustreerd als een vergren deling en een sleutel](./media/database-security-overview/azure-database-fig1.png)

[Always encrypted](https://msdn.microsoft.com/library/mt163865.aspx) is een functie die is ontworpen om gevoelige gegevens te beschermen die zijn opgeslagen in Azure SQL database-of SQL server-data bases. Met Always Encrypted kunnen clients gevoelige gegevens binnen client toepassingen versleutelen en de versleutelings sleutels niet aan de data base-engine (SQL Database of SQL Server) tonen.

Always Encrypted biedt een schei ding tussen personen die eigenaar zijn van de gegevens (en kunnen deze weer geven) en personen die de gegevens beheren (maar geen toegang mogen hebben). Het helpt ervoor te zorgen dat beheerders van lokale data bases, Cloud database operators of andere zeer privilegede, maar niet-gemachtigde gebruikers geen toegang hebben tot de versleutelde gegevens.

Daarnaast maakt Always Encrypted versleuteling transparant voor toepassingen. Er wordt een Always Encrypted-stuur programma geïnstalleerd op de client computer zodat gevoelige gegevens in de client toepassing automatisch kunnen worden versleuteld en ontsleuteld. Het stuur programma versleutelt de gegevens in gevoelige kolommen voordat de gegevens worden door gegeven aan de data base-engine. Het stuur programma herschrijft query's automatisch zodat de semantiek van de toepassing behouden blijft. Op dezelfde manier kan het stuur programma gegevens decoderen die zijn opgeslagen in kolommen met versleutelde data bases in query resultaten.

## <a name="access-control"></a>Toegangsbeheer

SQL Database beheert de toegang met behulp van:

- Firewall regels die de connectiviteit beperken op basis van het IP-adres.
- Verificatie mechanismen waarvoor gebruikers hun identiteit moeten bewijzen.
- Autorisatie mechanismen waarmee gebruikers worden beperkt tot specifieke acties en gegevens.

### <a name="database-access"></a>Databasetoegang

Gegevens beveiliging begint met het beheren van de toegang tot uw gegevens. Het Data Center dat uw gegevens host, beheert fysieke toegang. U kunt een firewall configureren voor het beheren van beveiliging op de netwerklaag. U beheert ook de toegang door aanmeldingen te configureren voor verificatie en machtigingen te definiëren voor Server-en database rollen.

#### <a name="firewall-and-firewall-rules"></a>Firewall en firewallregels

[Azure SQL database](https://azure.microsoft.com/services/sql-database/) biedt een relationele database service voor Azure en andere toepassingen op internet. Om uw gegevens te beschermen, verhinderen firewalls alle toegang tot de databaseserver totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt. Zie [Overzicht van de firewallregels voor SQL Database](/azure/sql-database/sql-database-firewall-configure) voor meer informatie.

De Azure SQL Database-Service is alleen beschikbaar via TCP-poort 1433. Als u toegang wilt krijgen tot een SQL database van uw computer, moet u ervoor zorgen dat de firewall van de client computer uitgaande TCP-communicatie toestaat op TCP-poort 1433. Als inkomende verbindingen niet nodig zijn voor andere toepassingen, blokkeert u ze op TCP-poort 1433.

#### <a name="authentication"></a>Authentication

Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL Database ondersteunt twee typen verificatie:

-   **SQL Server-verificatie**: Bij het maken van een logische SQL-instantie wordt er een account voor enkelvoudige aanmelding gemaakt, 'account van de SQL Database-abonnee' genoemd. Dit account maakt verbinding met behulp van [SQL Server-verificatie](/azure/sql-database/sql-database-security-overview) (gebruikers naam en wacht woord). Het account fungeert als beheerder voor de logische serverinstantie en voor alle gebruikersdatabases die hieraan zijn gekoppeld. De machtigingen van het abonnee account kunnen niet worden beperkt. Er kan slechts één van deze accounts bestaan.
-   **Azure Active Directory-verificatie**: [Azure AD-verificatie](/azure/sql-database/sql-database-aad-authentication) is een mechanisme om verbinding te maken met Azure SQL Database en Azure SQL data warehouse met behulp van identiteiten in azure AD. U kunt deze gebruiken om de identiteit van database gebruikers centraal te beheren.

![Azure AD-verificatie met SQL Database](./media/database-security-overview/azure-database-fig2.png)

 Voor delen van Azure AD-verificatie zijn onder andere:
  - Het biedt een alternatief voor SQL Server-verificatie.
  - Het helpt bij het stoppen van de verspreiding van gebruikers identiteiten over database servers en staat het draaien van wacht woorden op één plek toe.
  - U kunt database machtigingen beheren met behulp van externe groepen (Azure AD).
  - Het kan de opslag van wacht woorden elimineren door geïntegreerde Windows-verificatie en andere vormen van verificatie in te scha kelen die door Azure AD worden ondersteund.

#### <a name="authorization"></a>Authorization

[Autorisatie](/azure/sql-database/sql-database-manage-logins) verwijst naar wat een gebruiker kan doen binnen een Azure-SQL database. Het wordt beheerd door de databaserol lidmaatschappen en [](https://msdn.microsoft.com/library/ms189121) [object machtigingen](https://msdn.microsoft.com/library/ms191291.aspx)van uw gebruikers account. Autorisatie is het proces van het bepalen van de Beveilig bare bronnen waartoe een principal toegang heeft en welke bewerkingen zijn toegestaan voor deze resources.

### <a name="application-access"></a>Toegang tot toepassingen

#### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering

Een service medewerker bij een Call Center kan bellers identificeren met verschillende cijfers of creditcard nummers. Deze gegevens items mogen echter niet volledig worden blootgesteld aan de mede werker van de service.

U kunt een maskerings regel definiëren waarmee alle, behalve de laatste vier cijfers van een sociaal-fiscaal nummer of creditcard nummer in de resultatenset van een wille keurige query worden gemaskeerd.

![Maskeren voor een getal dat is verzonden tussen een SQL database en zakelijke apps](./media/database-security-overview/azure-database-fig3.png)

Een ander voor beeld is dat er een geschikt gegevens masker kan worden gedefinieerd voor het beveiligen van persoons gegevens. Een ontwikkelaar kan vervolgens een query uitvoeren op productie omgevingen om problemen op te lossen, zonder dat nalevings voorschriften worden geschonden.

Met [dynamische gegevensmaskering in SQL Database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. Dynamische gegevens maskering wordt ondersteund voor de V12-versie van Azure SQL Database.

[Dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking) helpt onbevoegde toegang tot gevoelige gegevens te voor komen, doordat u kunt opgeven hoeveel gevoelige gegevens moeten worden weer gegeven met minimale gevolgen voor de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.

> [!Note]
> Dynamische gegevens maskering kan worden geconfigureerd door de Azure data base-beheerder, de server beheerder of de security officer rollen.

#### <a name="row-level-security"></a>Beveiliging op rijniveau

Een andere algemene beveiligings vereiste voor multi tenant-data bases is [beveiliging op rijniveau](https://msdn.microsoft.com/library/dn765131.aspx). U kunt deze functie gebruiken om de toegang tot rijen in een database tabel te beheren op basis van de kenmerken van de gebruiker die een query uitvoert. (Voor beelden van kenmerken zijn groepslid maatschappen en uitvoerings context.)

![Beveiliging op rijniveau waarmee een gebruiker toegang krijgt tot rijen in een tabel via een client-app](./media/database-security-overview/azure-database-fig4.png)

De logica van de toegangs beperking bevindt zich in de database laag, in plaats van dat de gegevens in een andere toepassingslaag worden verwijderd. Het database systeem past de toegangs beperkingen telkens toe wanneer de toegang tot gegevens vanuit een wille keurige laag wordt gestart. Hierdoor is uw beveiligings systeem betrouwbaarder en betrouwbaarder door de surface area van uw beveiligings systeem te verminderen.

Beveiliging op rijniveau introduceert toegangs beheer op basis van een predikaat. Het bevat een flexibele, gecentraliseerde evaluatie die in aanmerking komt voor meta gegevens of andere criteria die de beheerder indien nodig bepaalt. Het predicaat wordt gebruikt als criterium om te bepalen of de gebruiker de juiste toegang heeft tot de gegevens op basis van gebruikers kenmerken. U kunt op labels gebaseerd toegangs beheer implementeren door gebruik te maken van toegangs beheer op basis van predikaten.

## <a name="proactive-monitoring"></a>Proactieve controle

SQL Database helpt uw gegevens te beveiligen door de mogelijkheden voor *controle* en *detectie van bedreigingen* te bieden.

### <a name="auditing"></a>Controleren

[Azure SQL database controle](/azure/sql-database/sql-database-auditing-get-started) kunt u de mogelijkheid bieden om inzicht te krijgen in gebeurtenissen en wijzigingen die in de data base optreden. Voor beelden zijn updates en query's voor de gegevens.

Met SQL Database controle worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven. Controle kan u helpen de naleving van de regelgeving te hand haven, database activiteiten te begrijpen en inzicht te krijgen in verschillen en afwijkingen die kunnen wijzen op problemen met het bedrijf of vermoedelijke beveiligings schendingen. Controle maakt en vergemakkelijkt de naleving van compliantie standaarden, maar garandeert geen naleving.

U kunt SQL Database controle gebruiken voor het volgende:

- Een audittrail van geselecteerde gebeurtenissen **bewaren** . U kunt categorieën van database acties definiëren die moeten worden gecontroleerd.
- **Rapport** over de database activiteit. U kunt vooraf geconfigureerde rapporten en een dash board gebruiken om snel aan de slag te gaan met activiteiten en gebeurtenis rapportage.
- Rapporten **analyseren** . U vindt verdachte gebeurtenissen, ongebruikelijke activiteiten en trends.

Er zijn twee controle methoden:

-   **BLOB-controle**: Logboeken worden geschreven naar Azure Blob Storage. Dit is een nieuwere controle methode. Het biedt betere prestaties, ondersteunt een hogere nauw keurigheid op object niveau, en is rendabeler.
-   **Tabel controle**: Logboeken worden geschreven naar Azure-tabel opslag.

### <a name="threat-detection"></a>Detectie van bedreigingen

[Advanced Threat Protection voor Azure SQL database](/azure/sql-database/sql-advanced-threat-protection) detecteert verdachte activiteiten die duiden op potentiële beveiligings Risico's. U kunt bedreigingen detectie gebruiken om te reageren op verdachte gebeurtenissen in de data base, zoals SQL-injecties, wanneer deze zich voordoen. Het biedt waarschuwingen en staat het gebruik van Azure SQL Database controle toe om de verdachte gebeurtenissen te verkennen.

![Detectie van bedreigingen voor SQL Database en een web-app, met een externe aanvaller en een schadelijke Insider](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, waaronder gegevens detectie & classificatie, evaluatie van beveiligings problemen en detectie van bedreigingen. 

- [Classificatie van gegevens detectie &](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Beoordeling van beveiligingsproblemen](/azure/sql-database/sql-vulnerability-assessment)  
- [Detectie van bedreigingen](/azure/sql-database/sql-database-threat-detection)

[Azure database for PostgreSQL Advanced Threat Protection](/azure/postgresql/concepts-data-access-and-security-threat-protection) biedt een nieuwe beveiligingslaag, waarmee u mogelijke bedreigingen kunt detecteren en hierop kunt reageren wanneer deze optreden door beveiligings waarschuwingen te bieden over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten en mogelijke beveiligings problemen, evenals afwijkende database toegang en query patronen. Advanced Threat Protection voor Azure Database for PostgreSQL integreert waarschuwingen met Azure Security Center. Type waarschuwingen zijn:

- Toegang vanaf ongebruikelijke locatie
- Toegang vanaf ongebruikelijk Azure Data Center 
- Toegang vanaf onbekende Principal 
- Toegang via een mogelijk schadelijke toepassing 
- PostgreSQL-referenties voor brute kracht van Azure data base 

[Azure database for MySQL geavanceerde beveiliging tegen bedreigingen](/azure/mysql/concepts-data-access-and-security-threat-protection) biedt beveiliging vergelijkbaar met postgresql geavanceerde beveiliging.  

## <a name="centralized-security-management"></a>Gecentraliseerd beveiligings beheer

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) helpt u bedreigingen te voorkomen, te detecteren en erop te reageren. Het biedt geïntegreerde beveiligings bewaking en beleids beheer in uw Azure-abonnementen. Het helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en werkt met een uitgebreid ecosysteem van beveiligings oplossingen.

[Security Center](../../security-center/security-center-alerts-data-services.md) helpt u bij het beveiligen van gegevens in SQL database door inzicht te krijgen in de beveiliging van al uw servers en data bases. Met Security Center kunt u het volgende doen:

- Beleids regels definiëren voor SQL Database versleuteling en controle.
- Bewaak de beveiliging van SQL Database resources in al uw abonnementen.
- U kunt beveiligings problemen snel identificeren en oplossen.
- Integreer waarschuwingen van [Azure SQL database-detectie van bedreigingen](/azure/sql-database/sql-database-threat-detection).

Security Center ondersteunt op rollen gebaseerde toegang.

## <a name="sql-information-protection"></a>SQL Information Protection

Met [SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) worden mogelijk gevoelige gegevens automatisch gedetecteerd en geclassificeerd. Dit biedt een label mechanisme voor het permanent coderen van gevoelige gegevens met classificatie kenmerken en biedt een gedetailleerd dash board met de de classificatie status van de data base.  

Daarnaast berekent de IT de gevoeligheid van de resultatenset van SQL-query's, zodat query's die gevoelige gegevens ophalen, expliciet kunnen worden gecontroleerd en de gegevens kunnen worden beveiligd. Zie Azure SQL Database gegevens detectie en-classificatie voor meer informatie over SQL Information Protection.

U kunt [SQL Information Protection-beleid](/azure/security-center/security-center-info-protection-policy) configureren in azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace is een onlinemarktplaats voor toepassingen en services waar beginnende en onafhankelijke softwareleveranciers hun oplossingen kunnen aanbieden aan Azure-klanten over de hele wereld.
Azure Marketplace combineert Microsoft Azure-partner ecosystemen in een uniform platform om klanten en partners beter te bezorgen. U kunt [een zoek opdracht uitvoeren](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) om database beveiligings producten weer te geven die beschikbaar zijn op de Azure Marketplace.

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure-SQL database beveiligen](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center-en Azure SQL Database-Service](/azure/security-center/security-center-sql-database)
- [Detectie van SQL Database-dreigingen](/azure/sql-database/sql-database-threat-detection)
- [Prestaties van SQL database verbeteren](/azure/sql-database/sql-database-performance-tutorial)
