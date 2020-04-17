---
title: Overzicht van beveiliging
description: Meer informatie over Azure SQL Database- en SQL Server-beveiliging, inclusief de verschillen tussen de cloud en sql server on-premises.
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
ms.openlocfilehash: 1d08770d81ade2d976142b2ce1fd94dae044cf32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461392"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Een overzicht van azure SQL Database-beveiligingsmogelijkheden

In dit artikel worden de basisprincipes beschreven van het beveiligen van de gegevenslaag van een toepassing met Azure SQL Database. De beschreven beveiligingsstrategie volgt de gelaagde verdediging-in-diepte aanpak zoals weergegeven in de afbeelding hieronder, en beweegt zich van buitenaf in:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Netwerkbeveiliging

Microsoft Azure SQL Database biedt een relationele databaseservice voor cloud- en bedrijfstoepassingen. Om klantgegevens te beschermen, verhinderen firewalls netwerktoegang tot de databaseserver totdat de toegang expliciet is verleend op basis van IP-adres of azure virtual-netwerkverkeer.

### <a name="ip-firewall-rules"></a>IP-firewallregels

IP-firewallregels verlenen toegang tot databases op basis van het ip-adres van elke aanvraag. Zie [Overzicht van Azure SQL Database- en SQL Data Warehouse-firewallregels](sql-database-firewall-configure.md)voor meer informatie.

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk

[Eindpunten voor virtuele netwerkservices](../virtual-network/virtual-network-service-endpoints-overview.md) breiden uw virtuele netwerkconnectiviteit uit via de Azure-backbone en stellen Azure SQL Database in staat om het virtuele netwerksubnet te identificeren waarvan het verkeer afkomstig is. Als u verkeer azure SQL Database wilt laten bereiken, gebruikt u de [SQL-servicetags](../virtual-network/security-overview.md) om uitgaand verkeer via netwerkbeveiligingsgroepen toe te staan.

[Met virtuele netwerkregels](sql-database-vnet-service-endpoint-rule-overview.md) kan Azure SQL Database alleen communicatie accepteren die wordt verzonden vanuit geselecteerde subnetten binnen een virtueel netwerk.

> [!NOTE]
> Het beheren van toegang met firewallregels is *niet* van toepassing op **een beheerde instantie**. Zie Verbinding maken met een [beheerde instantie voor](sql-database-managed-instance-connect-app.md) meer informatie over de benodigde netwerkconfiguratie

## <a name="access-management"></a>Toegangsbeheer

> [!IMPORTANT]
> Het beheren van databases en databaseservers binnen Azure wordt beheerd door de roltoewijzingen van uw portalgebruikersaccount. Zie Op rollen gebaseerd [toegangsbeheer in Azure-portal](../role-based-access-control/overview.md)voor meer informatie over dit artikel.

### <a name="authentication"></a>Verificatie

Authenticatie is het proces om te bewijzen dat de gebruiker is wie hij beweert te zijn. Azure SQL Database ondersteunt twee soorten verificatie:

- **SQL-verificatie:**

    SQL-databaseverificatie verwijst naar de verificatie van een gebruiker wanneer hij verbinding maakt met [Azure SQL Database](sql-database-technical-overview.md) met gebruikersnaam en wachtwoord. Tijdens het aanmaken van de databaseserver voor de database moet een aanmelding voor 'Serveradmin' met een gebruikersnaam en wachtwoord worden opgegeven. Met behulp van deze referenties kan een 'serverbeheerder' zich als eigenaar van de database verifiëren voor elke database op die databaseserver. Daarna kunnen extra SQL-aanmeldingen en gebruikers worden gemaakt door de serverbeheerder, waarmee gebruikers verbinding kunnen maken met gebruikersnaam en wachtwoord.

- **Azure Active Directory-verificatie:**

    Azure Active Directory-verificatie is een mechanisme om verbinding te maken met [Azure SQL Database](sql-database-technical-overview.md) en SQL Data [Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in Azure Active Directory (Azure AD). Azure AD-verificatie stelt beheerders in staat om de identiteiten en machtigingen van databasegebruikers centraal te beheren, samen met andere Microsoft-services op één centrale locatie. Dit omvat het minimaliseren van wachtwoordopslag en maakt gecentraliseerd wachtwoordrotatiebeleid mogelijk.

     Er moet een serverbeheerder met de **Active Directory-beheerder** worden gemaakt om Azure AD-verificatie met SQL Database te kunnen gebruiken. Zie [Verbinding maken met SQL-database met Azure Active Directory Authentication voor](sql-database-aad-authentication.md)meer informatie. Azure AD-verificatie ondersteunt zowel beheerde als federatieve accounts. De federatieve accounts ondersteunen Windows-gebruikers en -groepen voor een klantdomein dat wordt gefedereerd met Azure AD.

    Aanvullende Azure AD-verificatieopties beschikbaar zijn [Active Directory Universal Authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) connections including [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) and Conditional [Access](sql-database-conditional-access.md).

> [!IMPORTANT]
> Het beheren van databases en servers binnen Azure wordt beheerd door de roltoewijzingen van uw portalgebruikersaccount. Zie Op rollen gebaseerd [toegangsbeheer in Azure-portal](../role-based-access-control/overview.md)voor meer informatie over dit artikel. Het beheren van toegang met firewallregels is *niet* van toepassing op **een beheerde instantie**. Zie het volgende artikel over [het verbinden met een beheerde instantie](sql-database-managed-instance-connect-app.md) voor meer informatie over de benodigde netwerkconfiguratie.

## <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar de machtigingen die zijn toegewezen aan een gebruiker in een Azure SQL-database en bepaalt wat de gebruiker mag doen. Machtigingen worden beheerd door gebruikersaccounts toe te voegen aan [databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles) en machtigingen op databaseniveau toe te wijzen aan die rollen of door de gebruiker bepaalde [machtigingen op objectniveau](/sql/relational-databases/security/permissions-database-engine)toe te kennen. Zie [Aanmeldingen en gebruikers](sql-database-manage-logins.md) voor meer informatie

Maak als best practice aangepaste rollen wanneer dat nodig is. Voeg gebruikers toe aan de rol met de minste bevoegdheden die nodig zijn om hun taakfunctie uit te kunnen doen. Wijs geen machtigingen toe aan gebruikers. Het serverbeheeraccount is lid van de ingebouwde db_owner-rol, die uitgebreide machtigingen heeft en slechts aan weinig gebruikers met administratieve taken moet worden verleend. Voor Azure SQL Database-toepassingen gebruikt u de [uitvoeras](/sql/t-sql/statements/execute-as-clause-transact-sql) om de uitvoeringscontext van de aangeroepen module op te geven of [Toepassingsrollen](/sql/relational-databases/security/authentication-access/application-roles) met beperkte machtigingen te gebruiken. Deze praktijk zorgt ervoor dat de toepassing die verbinding maakt met de database de minste bevoegdheden heeft die de toepassing nodig heeft. Het volgen van deze best practices bevordert ook de scheiding van taken.

### <a name="row-level-security"></a>Beveiliging op rijniveau

Row-Level Security stelt klanten in staat om de toegang tot rijen in een databasetabel te beheren op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld groepslidmaatschap of uitvoeringscontext). Row-Level Security kan ook worden gebruikt om aangepaste labelgebaseerde beveiligingsconcepten te implementeren. Zie [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) voor meer informatie.

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Bescherming tegen bedreigingen

SQL Database beveiligt klantgegevens door controle- en bedreigingsdetectiemogelijkheden te bieden.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-controle in Azure Monitor-logboeken en gebeurtenishubs

SQL Database auditing houdt databaseactiviteiten bij en helpt om de naleving van beveiligingsstandaarden te handhaven door databasegebeurtenissen vast te leggen in een controlelogboek in een Azure-opslagaccount dat eigendom is van een klant. Auditing stelt gebruikers in staat om lopende databaseactiviteiten te controleren en historische activiteiten te analyseren en te onderzoeken om potentiële bedreigingen of vermoedelijke schendingen van misbruik en beveiliging te identificeren. Zie Aan de slag met [SQL Database Auditing](sql-database-auditing.md)voor meer informatie.  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection analyseert uw SQL Server-logboeken om ongebruikelijk gedrag en mogelijk schadelijke pogingen om databases te openen of te exploiteren te detecteren. Waarschuwingen worden gemaakt voor verdachte activiteiten zoals SQL-injectie, potentiële gegevensinfiltratie en brute force-aanvallen of voor afwijkingen in toegangspatronen om privilegeescalaties en geschonden referenties te gebruiken. Waarschuwingen worden bekeken vanuit het [Azure Security Center,](https://azure.microsoft.com/services/security-center/)waar de details van de verdachte activiteiten worden verstrekt en aanbevelingen voor verder onderzoek worden gegeven, samen met acties om de dreiging te beperken. Advanced Threat Protection kan tegen een toeslag per server worden ingeschakeld. Zie [Aan de slag met SQL Database Advanced Threat Protection](sql-database-threat-detection.md)voor meer informatie.

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Informatiebescherming en versleuteling

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (Encryption-in-transit)

SQL Database beveiligt klantgegevens door gegevens in beweging te versleutelen met [Transport Layer Security.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

Sql Server dwingt te allen tijde encryptie (SSL/TLS) af voor alle verbindingen. Dit zorgt ervoor dat alle gegevens worden versleuteld "onderweg" tussen de client en de server, ongeacht de instelling van **Encrypt of** **TrustServerCertificate** in de verbindingstekenreeks.

Als aanbevolen aanbevolen aanbevolen dat u in de verbindingstekenreeks van uw toepassing een versleutelde verbinding opgeeft en het servercertificaat _**niet**_ vertrouwt. Dit dwingt uw toepassing om het servercertificaat te verifiëren en voorkomt dus dat uw toepassing kwetsbaar is voor aanvallen van het middelste type.

Bijvoorbeeld bij het gebruik van het ADO.NET stuurprogramma wordt dit bereikt via **Encrypt=True** en **TrustServerCertificate=False**. Als u uw verbindingstekenreeks van de Azure-portal haalt, heeft deze de juiste instellingen.

> [!IMPORTANT]
> Houd er rekening mee dat sommige niet-Microsoft-stuurprogramma's TLS mogelijk niet standaard gebruiken of vertrouwen op een oudere versie van TLS (<1.2) om te kunnen functioneren. In dit geval u met SQL Server nog steeds verbinding maken met uw database. We raden u echter aan de beveiligingsrisico's te evalueren waarmee dergelijke stuurprogramma's en toepassingen verbinding kunnen maken met SQL Database, vooral als u gevoelige gegevens opslaat.
>
> Zie [TLS-overwegingen](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity) voor meer informatie over TLS en connectiviteit

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparante gegevensversleuteling (versleuteling in rust)

[Transparent Data Encryption (TDE) voor Azure SQL Database](transparent-data-encryption-azure-sql.md) voegt een beveiligingslaag toe om gegevens in rust te beschermen tegen ongeautoriseerde of offline toegang tot raw-bestanden of back-ups. Veelvoorkomende scenario's zijn diefstal van datacenters of onbeveiligde verwijdering van hardware of media, zoals schijven en back-uptapes.TDE versleutelt de volledige database met behulp van een AES-versleutelingsalgoritme, waarvoor toepassingsontwikkelaars geen wijzigingen hoeven aan te brengen in bestaande toepassingen.

In Azure worden alle nieuw gemaakte SQL-databases standaard versleuteld en wordt de databaseversleutelingssleutel beschermd door een ingebouwd servercertificaat.  Certificaatonderhoud en -rotatie worden beheerd door de service en vereisen geen invoer van de gebruiker. Klanten die liever de controle over de versleutelingssleutels overnemen, kunnen de sleutels beheren in [Azure Key Vault.](../key-vault/general/secure-your-key-vault.md)

### <a name="key-management-with-azure-key-vault"></a>Sleutelbeheer met Azure Key Vault

[Met ByOK-ondersteuning (Bring Your Own Key)](transparent-data-encryption-byok-azure-sql.md) voor [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) kunnen klanten het eigendom nemen van sleutelbeheer en -rotatie met Azure Key [Vault,](../key-vault/general/secure-your-key-vault.md)het cloudgebaseerde externe sleutelbeheersysteem van Azure. Als de toegang van de database tot de sleutelkluis wordt ingetrokken, kan een database niet worden gedecodeerd en in het geheugen worden gelezen. Azure Key Vault biedt een centraal sleutelbeheerplatform, maakt gebruik van streng bewaakte hardwarebeveiligingsmodules (HSM's) en maakt scheiding van taken tussen beheer van sleutels en gegevens mogelijk om te voldoen aan de vereisten voor beveiligingscompliance.

### <a name="always-encrypted-encryption-in-use"></a>Altijd versleuteld (versleuteling in gebruik)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) is een functie die is ontworpen om gevoelige gegevens die zijn opgeslagen in specifieke databasekolommen te beschermen tegen toegang (bijvoorbeeld creditcardnummers, nationale identificatienummers of gegevens die moeten _worden gebruikt)._ Dit omvat databasebeheerders of andere bevoorrechte gebruikers die gemachtigd zijn om toegang te krijgen tot de database om beheertaken uit te voeren, maar geen zakelijke noodzaak hebben om toegang te krijgen tot de specifieke gegevens in de versleutelde kolommen. De gegevens worden altijd versleuteld, wat betekent dat de versleutelde gegevens alleen worden gedecodeerd voor verwerking door clienttoepassingen met toegang tot de versleutelingssleutel.  De versleutelingssleutel wordt nooit blootgesteld aan SQL en kan worden opgeslagen in de [Windows Certificate Store](sql-database-always-encrypted.md) of in Azure Key [Vault.](sql-database-always-encrypted-azure-key-vault.md)

### <a name="dynamic-data-masking"></a>Dynamische gegevensmaskering

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Met dynamische gegevensmaskering in SQL Database wordt de blootstelling van gevoelige gegevens beperkt door deze gegevens te maskeren voor niet-gemachtigde gebruikers. Dynamische gegevensmaskering detecteert automatisch potentieel gevoelige gegevens in Azure SQL Database en biedt bruikbare aanbevelingen om deze velden te maskeren, met minimale impact op de toepassingslaag. Dit werkt als volgt: de gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden, worden bedekt, terwijl de gegevens in de database niet worden gewijzigd. Zie [Aan de slag met sql database dynamische gegevensmaskering](sql-database-dynamic-data-masking-get-started.md)voor meer informatie.

## <a name="security-management"></a>Beveiligingsbeheer

### <a name="vulnerability-assessment"></a>Evaluatie van beveiligingsproblemen

[Kwetsbaarheidsbeoordeling](sql-vulnerability-assessment.md) is een eenvoudig te configureren service die potentiële databasekwetsbaarheden kan ontdekken, bijhouden en helpen oplossen met als doel de algehele databasebeveiliging proactief te verbeteren. Vulnerability assessment (VA) maakt deel uit van het advanced data security (ADS) aanbod, dat is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Kwetsbaarheidsbeoordeling kan worden geopend en beheerd via de centrale SQL ADS-portal.

### <a name="data-discovery--classification"></a>Gegevensdetectie en -classificatie

Gegevensdetectie & classificatie (momenteel in preview) biedt geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor het ontdekken, classificeren, labelen en beveiligen van de gevoelige gegevens in uw databases. Het ontdekken en classificeren van uw uiterst gevoelige gegevens (zakelijk/financieel, gezondheidszorg, persoonsgegevens, enz.) kan een centrale rol spelen in uw organisatie Informatiebescherming. Dit kan dienen als infrastructuur om:

- Verschillende beveiligingsscenario's, zoals monitoring (auditing) en waarschuwingen over afwijkende toegang tot gevoelige gegevens.
- Controle van de toegang tot en verharding van de beveiliging van databases met zeer gevoelige gegevens.
- Te helpen voldoen aan standaarden op het gebied van gegevensbescherming en aan vereisten voor naleving van regelgeving.

Zie [Aan de slag met gegevensdetectie & classificatie](sql-database-data-discovery-and-classification.md)voor meer informatie.

### <a name="compliance"></a>Naleving

Naast de bovenstaande functies en functionaliteit die uw toepassing kunnen helpen om aan verschillende beveiligingsvereisten te voldoen, neemt Azure SQL Database ook deel aan regelmatige audits en is het gecertificeerd volgens een aantal nalevingsnormen. Zie het Microsoft [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Aanmeldingen en gebruikersaccounts beheren](sql-database-manage-logins.md)voor een discussie over het gebruik van aanmeldingen, gebruikersaccounts, databaserollen en machtigingen in SQL Database.
- Zie [SQL Database auditing](sql-database-auditing.md)voor een bespreking van databaseauditing.
- Zie [SQL Database-dreigingsdetectie](sql-database-threat-detection.md)voor een discussie over bedreigingsdetectie.
