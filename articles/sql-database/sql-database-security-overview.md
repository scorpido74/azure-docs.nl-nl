---
title: Beveiligingsoverzicht
description: Meer informatie over Azure SQL Database en SQL Server beveiliging, met inbegrip van de verschillen tussen de Cloud en SQL Server on-premises.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: c9f59eb8c299eb9319694d392c2b5d1d814ed9b8
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997323"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Een overzicht van Azure SQL Database beveiligings mogelijkheden

In dit artikel vindt u een overzicht van de basis principes van het beveiligen van de gegevenslaag van een toepassing met behulp van Azure SQL Database. De beschreven beveiligings strategie volgt de gelaagde ingrijpende aanpak zoals wordt weer gegeven in de onderstaande afbeelding, en verplaatst van de buiten kant naar:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Netwerkbeveiliging

Microsoft Azure SQL Database biedt een relationele database service voor Cloud-en bedrijfs toepassingen. Om klant gegevens te helpen beschermen, worden netwerk toegang tot de database server voor komen wanneer de toegang expliciet wordt verleend op basis van het IP-adres of het virtuele netwerk verkeer van Azure.

### <a name="ip-firewall-rules"></a>IP-firewall regels

IP-firewall regels verlenen toegang tot data bases op basis van het oorspronkelijke IP-adres van elke aanvraag. Zie [overzicht van Azure SQL database en SQL Data Warehouse firewall regels](sql-database-firewall-configure.md)voor meer informatie.

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk

Met [virtuele netwerk service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) wordt de verbinding van het virtuele netwerk via de Azure-backbone uitgebreid en kunnen Azure SQL database het subnet van het virtuele netwerk identificeren waaruit het verkeer afkomstig is. Als u wilt toestaan dat verkeer wordt bereikt Azure SQL Database, gebruikt u de SQL [service-Tags](../virtual-network/security-overview.md) om uitgaand verkeer via netwerk beveiligings groepen toe te staan.

Met [regels voor virtuele netwerken](sql-database-vnet-service-endpoint-rule-overview.md) kunnen Azure SQL database alleen communicatie accepteren die worden verzonden vanuit geselecteerde subnetten binnen een virtueel netwerk.

> [!NOTE]
> Het beheren van toegang met firewall regels is *niet* van toepassing op **een beheerd exemplaar**. Zie [verbinding maken met een beheerd exemplaar](sql-database-managed-instance-connect-app.md) voor meer informatie over de benodigde netwerk configuratie

## <a name="access-management"></a>Toegangsbeheer

> [!IMPORTANT]
> Het beheren van data bases en database servers binnen Azure wordt bepaald door de roltoewijzingen van uw portal-gebruikers account. Zie op [rollen gebaseerd toegangs beheer in azure Portal](../role-based-access-control/overview.md)voor meer informatie over dit artikel.

### <a name="authentication"></a>Verificatie

Verificatie is het proces waarbij de gebruiker wordt geclaimd. Azure SQL Database ondersteunt twee typen verificatie:

- **SQL-verificatie**:

    SQL database-verificatie verwijst naar de verificatie van een gebruiker bij het maken van verbinding met [Azure SQL database](sql-database-technical-overview.md) met behulp van de gebruikers naam en het wacht woord. Tijdens het maken van de database server voor de data base moet een ' Server beheerder ' een aanmelding met een gebruikers naam en wacht woord opgeven. Als u deze referenties gebruikt, kan een ' Server-beheerder ' zich verifiëren bij elke Data Base op die database server als de eigenaar van de data base. Daarna kunnen extra SQL-aanmeldingen en-gebruikers worden gemaakt door de server beheerder, waarmee gebruikers verbinding maken met behulp van de gebruikers naam en het wacht woord.

- **Azure Active Directory-verificatie**:

    Azure Active Directory-verificatie is een mechanisme om verbinding te maken met [Azure SQL database](sql-database-technical-overview.md) en [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in azure Active Directory (Azure AD). Met Azure AD-verificatie kunnen beheerders de identiteiten en machtigingen van database gebruikers centraal beheren, samen met andere micro soft-Services op één centrale locatie. Dit omvat de minimale wacht woord opslag en maakt beleid voor gecentraliseerde wachtwoord rotatie mogelijk.

     Een server beheerder met de naam **Active Directory beheerder** moet worden gemaakt voor het gebruik van Azure AD-verificatie met SQL database. Zie [verbinding maken met SQL database met behulp van Azure Active Directory-verificatie](sql-database-aad-authentication.md)voor meer informatie. Azure AD-verificatie ondersteunt zowel beheerde als federatieve accounts. De federatieve accounts ondersteunen Windows-gebruikers en-groepen voor een klant domein Federated met Azure AD.

    Er zijn extra Azure AD-verificatie opties beschikbaar [Active Directory universele verificatie voor SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) verbindingen, waaronder [multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) en [voorwaardelijke toegang](sql-database-conditional-access.md).

> [!IMPORTANT]
> Het beheren van data bases en servers binnen Azure wordt bepaald door de roltoewijzingen van uw portal-gebruikers account. Zie op [rollen gebaseerd toegangs beheer in azure Portal](../role-based-access-control/overview.md)voor meer informatie over dit artikel. Het beheren van toegang met firewall regels is *niet* van toepassing op **een beheerd exemplaar**. Raadpleeg het volgende artikel over het [maken van een verbinding met een beheerd exemplaar](sql-database-managed-instance-connect-app.md) voor meer informatie over de benodigde netwerk configuratie.

## <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar de machtigingen die zijn toegewezen aan een gebruiker binnen een Azure SQL Database en bepaalt wat de gebruiker mag doen. Machtigingen worden beheerd door gebruikers accounts toe te voegen aan [database rollen](/sql/relational-databases/security/authentication-access/database-level-roles) en machtigingen op database niveau toe te wijzen aan deze rollen of door de gebruiker bepaalde [machtigingen op object niveau](/sql/relational-databases/security/permissions-database-engine)toe te kennen. Zie [aanmeldingen en gebruikers](sql-database-manage-logins.md) voor meer informatie

Maak als best practice aangepaste rollen wanneer dat nodig is. Voeg gebruikers toe aan de rol met de mini maal benodigde bevoegdheden voor het uitvoeren van hun functie. Wijs machtigingen niet rechtstreeks toe aan gebruikers. Het account van de server beheerder is lid van de ingebouwde db_owner rol, met uitgebreide machtigingen en mag alleen worden toegekend aan een beperkt aantal gebruikers met beheerders rechten. Gebruik voor Azure SQL Database toepassingen de [uitvoeren als](/sql/t-sql/statements/execute-as-clause-transact-sql) om de uitvoerings context van de aangeroepen module op te geven of gebruik [toepassings rollen](/sql/relational-databases/security/authentication-access/application-roles) met beperkte machtigingen. Op deze manier zorgt u ervoor dat de toepassing die verbinding maakt met de data base, over de minste bevoegdheden beschikt die nodig zijn voor de toepassing. Het volgen van deze aanbevolen procedures bevordert ook de schei ding van taken.

### <a name="row-level-security"></a>Beveiliging op rijniveau

Met beveiliging op rijniveau kunnen klanten de toegang tot rijen in een database tabel beheren op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld groepslid maatschap of uitvoerings context). Beveiliging op rijniveau kan ook worden gebruikt voor het implementeren van op aangepaste labels gebaseerde beveiligings concepten. Zie [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) voor meer informatie.

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Bescherming tegen bedreigingen

SQL Database klant gegevens beveiligen door de mogelijkheden voor controle en detectie van bedreigingen op te geven.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-controle in Azure Monitor logboeken en Event Hubs

SQL Database controle houdt database activiteiten bij en helpt bij het onderhouden van de naleving van beveiligings standaarden door database gebeurtenissen te registreren in een audit logboek in een Azure-opslag account van een klant. Met controle kunnen gebruikers doorlopende database activiteiten bewaken en historische activiteiten analyseren en onderzoeken om mogelijke dreigingen of verdachte misbruik en beveiligings schendingen te identificeren. Zie aan de slag met [SQL database auditing](sql-database-auditing.md)voor meer informatie.  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Geavanceerde beveiliging tegen bedreigingen is het analyseren van uw SQL Server logboeken voor het detecteren van ongebruikelijk gedrag en mogelijk schadelijke pogingen om data bases te openen of te exploiteren. Er worden waarschuwingen gemaakt voor verdachte activiteiten, zoals SQL-injectie, mogelijke gegevens infiltratie en beveiligings aanvallen, of voor afwijkingen in toegangs patronen om escalaties van bevoegdheden te ondervangen en gekraakte referenties te gebruiken. Er worden waarschuwingen weer gegeven uit de [Azure Security Center](https://azure.microsoft.com/services/security-center/), waar de details van de verdachte activiteiten worden verstrekt en aanbevelingen voor verder onderzoek worden gegeven, samen met acties voor het beperken van de dreiging. Geavanceerde beveiliging tegen bedreigingen kan per server worden ingeschakeld voor extra kosten. Zie [aan de slag met SQL database Advanced Threat Protection](sql-database-threat-detection.md)voor meer informatie.

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Gegevens beveiliging en-versleuteling

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (versleuteling-in-transit)

SQL Database klant gegevens beveiligen door gegevens in beweging te versleutelen met [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server dwingt te allen tijde versleuteling (SSL/TLS) af voor alle verbindingen. Dit zorgt ervoor dat alle gegevens in transit tussen de client en server worden versleuteld, ongeacht de instelling van **versleutelen** of **TrustServerCertificate** in de Connection String.

Als best practice kunt u het beste een versleutelde verbinding opgeven in de connection string van uw toepassing en het server certificaat _**niet**_ vertrouwen. Dit dwingt uw toepassing af om het server certificaat te verifiëren en zorgt er daarom voor dat uw toepassing kwetsbaar is voor man in het middelste type aanvallen.

Als u bijvoorbeeld het ADO.NET-stuur programma gebruikt, wordt dit bereikt via **versleutelen = True** en **TrustServerCertificate = False**. Als u uw connection string van de Azure Portal opvraagt, worden de juiste instellingen weer geven.

> [!IMPORTANT]
> Houd er rekening mee dat sommige niet-micro soft-Stuur Programma's standaard geen gebruikmaken van TLS of gebruikmaken van een oudere versie van TLS (< 1.2) om te kunnen functioneren. In dit geval kunt SQL Server nog steeds verbinding maken met uw data base. We raden u echter aan de beveiligings Risico's te evalueren waarmee dergelijke Stuur Programma's en toepassingen verbinding kunnen maken met SQL Database, met name als u gevoelige gegevens opslaat. 
>
> Zie voor meer informatie over TLS en connectiviteit [TLS-overwegingen](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (versleuteling-at-rest)

[Transparent Data Encryption (TDE) voor Azure SQL database](transparent-data-encryption-azure-sql.md) voegt een beveiligingslaag toe om gegevens op rest te beschermen tegen onbevoegde of offline toegang tot onbewerkte bestanden of back-ups. Veelvoorkomende scenario's zijn onder meer Data Center-dief stal of onbeveiligde verwijdering van hardware of media, zoals schijf stations en back-uptapes. TDE versleutelt de gehele data base met behulp van een AES-versleutelings algoritme, waarvoor ontwikkel aars van toepassingen geen wijzigingen in bestaande toepassingen hoeven aan te brengen.

In Azure worden alle nieuw gemaakte SQL-data bases standaard versleuteld en wordt de database versleutelings sleutel beveiligd door een ingebouwd server certificaat.  Het onderhoud en de rotatie van het certificaat worden beheerd door de service en vereist geen invoer van de gebruiker. Klanten die de controle over de versleutelings sleutels liever overnemen, kunnen de sleutels in [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)beheren.

### <a name="key-management-with-azure-key-vault"></a>Sleutel beheer met Azure Key Vault

Met [Bring your own Key](transparent-data-encryption-byok-azure-sql.md) -ondersteuning (BYOK) voor [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) kunnen klanten eigenaar worden van sleutel beheer en draaiing met behulp van [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), het op de cloud gebaseerde externe sleutel beheer systeem van Azure. Als de data base toegang tot de sleutel kluis wordt ingetrokken, kan een Data Base niet worden ontsleuteld en in het geheugen worden gelezen. Azure Key Vault biedt een centraal platform voor sleutel beheer, maakt gebruik van nauw bewaakte Hardware Security modules (Hsm's) en stelt schei ding van taken mogelijk te maken tussen het beheer van sleutels en gegevens om te voldoen aan de vereisten voor naleving van de beveiliging.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (versleuteling-in-gebruik)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) is een functie die is ontworpen om gevoelige gegevens die zijn opgeslagen in specifieke database kolommen, te beveiligen tegen toegang (bijvoorbeeld creditcard nummers, nationale identificatie nummers of gegevens die op basis van _kennis moeten worden_ genoteerd). Dit zijn onder andere database beheerders of andere bevoegde gebruikers die gemachtigd zijn om toegang te krijgen tot de data base om beheer taken uit te voeren, maar geen toegang hebben tot de specifieke gegevens in de versleutelde kolommen. De gegevens worden altijd versleuteld, wat betekent dat de versleutelde gegevens alleen worden ontsleuteld voor verwerking door client toepassingen met toegang tot de versleutelings sleutel.  De versleutelings sleutel wordt nooit blootgesteld aan SQL en kan worden opgeslagen in het [Windows-certificaat archief](sql-database-always-encrypted.md) of in [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

SQL Database dynamische gegevens maskering beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-bevoegde gebruikers. Met dynamische gegevens maskering worden mogelijk gevoelige gegevens in Azure SQL Database automatisch gedetecteerd en kunnen actie bare aanbevelingen worden gemaskeerd om deze velden te maskeren, met minimale gevolgen voor de toepassingslaag. Dit werkt als volgt: de gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden, worden bedekt, terwijl de gegevens in de database niet worden gewijzigd. Zie [aan de slag met SQL database dynamische gegevens maskering](sql-database-dynamic-data-masking-get-started.md)voor meer informatie.

## <a name="security-management"></a>Beveiligingsbeheer

### <a name="vulnerability-assessment"></a>Evaluatie van beveiligingsproblemen

[Evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service waarmee mogelijke beveiligings problemen met een Data Base kunnen worden gedetecteerd, gevolgd en opgelost, waardoor de algehele beveiliging van de data base proactief wordt verbeterd. De evaluatie van beveiligings problemen (VA) maakt deel uit van de aanbieding voor geavanceerde gegevens beveiliging (ADS), een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. De evaluatie van beveiligings problemen kan worden geopend en beheerd via de centrale SQL ADS-Portal.

### <a name="data-discovery--classification"></a>Gegevensdetectie en -classificatie

Gegevens detectie & classificatie (momenteel in Preview) biedt geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen en beveiligen van gevoelige gegevens in uw data bases. Het detecteren en classificeren van uw meest gevoelige gegevens (bedrijfs-en financiële, gezondheids zorg, persoonlijke gegevens, enz.) kunnen een rol draaien in uw organisatie voor Information Protection stature. Dit kan dienen als infrastructuur om:

- Diverse beveiligings scenario's, zoals controle (controle) en waarschuwingen over afwijkende toegang tot gevoelige gegevens.
- Het beheren van toegang tot en het beveiligen van de beveiliging van data bases met zeer gevoelige gegevens.
- Te helpen voldoen aan standaarden op het gebied van gegevensbescherming en aan vereisten voor naleving van regelgeving.

Zie [aan de slag met gegevens detectie & classificatie](sql-database-data-discovery-and-classification.md)voor meer informatie.

### <a name="compliance"></a>Naleving

Naast de bovenstaande functies en functionaliteit die uw toepassing kunnen helpen voldoen aan verschillende beveiligings vereisten, is Azure SQL Database ook betrokken bij regel matige controles en is gecertificeerd voor een aantal nalevings standaarden. Zie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie over de meest recente lijst met SQL database nalevings certificeringen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Toegang beheren](sql-database-control-access.md) voor een discussie over het gebruik van functies voor toegangsbeheer in SQL Database.
- Zie [SQL database controle](sql-database-auditing.md)voor een bespreking van database controle.
- Zie [SQL database Threat Detection](sql-database-threat-detection.md)(Engelstalig) voor een bespreking van detectie van bedreigingen.
