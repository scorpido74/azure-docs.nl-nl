---
title: Aanbevolen procedures voor databasebeveiliging - Microsoft Azure
description: In dit artikel vindt u een reeks aanbevolen procedures voor Azure-databasebeveiliging.
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
ms.openlocfilehash: 0f2e0257c5bf855b0d9be61c43b68b4e30b3d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125107"
---
# <a name="azure-database-security-best-practices"></a>Best practices voor Azure-databasebeveiliging
In dit artikel worden aanbevolen procedures voor databasebeveiliging beschreven.

De aanbevolen procedures zijn gebaseerd op een consensus van mening en werken met de huidige Azure-platformmogelijkheden en functiesets. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regelmatig bijgewerkt om deze veranderingen weer te geven.

## <a name="secure-databases"></a>Beveiligde databases
Beveiliging is een topzorg voor het beheren van databases en is altijd een prioriteit geweest voor [Azure SQL Database.](../../sql-database/index.yml) Uw databases kunnen goed beveiligd zijn om te voldoen aan de meeste wettelijke of beveiligingsvereisten, waaronder HIPAA, ISO 27001/27002 en PCI DSS Level 1. Een huidige lijst met certificeringen voor beveiligingscompliance is beschikbaar op de site van [het Microsoft Trust Center.](https://azure.microsoft.com/support/trust-center/services/) U er ook voor kiezen om uw databases in specifieke Azure-datacenters te plaatsen op basis van wettelijke vereisten.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Firewallregels gebruiken om databasetoegang te beperken
Microsoft Azure SQL Database biedt een relationele databaseservice voor Azure en andere internettoepassingen. Om toegangsbeveiliging te bieden, beheert SQL Database de toegang met:

- Firewallregels die de connectiviteit per IP-adres beperken.
- Verificatiemechanismen waarvoor gebruikers hun identiteit moeten bewijzen.
- Autorisatiemechanismen die gebruikers beperken tot specifieke acties en gegevens.

Firewalls voorkomen dat alle toegang tot uw databaseserver totdat u opgeeft welke computers toestemming hebben. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.

In de volgende afbeelding ziet u waar u een serverfirewall inSQL-database instelt:

![Firewall-regels](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

De Azure SQL Database-service is alleen beschikbaar via TCP-poort 1433. Als u vanaf uw computer toegang wilt krijgen tot een SQL-database, moet u ervoor zorgen dat uw clientcomputerfirewall uitgaande TCP-communicatie op TCP-poort 1433 toestaat. Blokkeer binnenkomende verbindingen op TCP-poort 1433 met behulp van firewallregels als u deze verbindingen niet nodig hebt voor andere toepassingen.

Als onderdeel van het verbindingsproces worden verbindingen van virtuele Azure-machines doorgestuurd naar een IP-adres en poort die uniek zijn voor elke werkrol. Het poortnummer ligt in het bereik van 11000 tot 11999. Zie Poorten van meer [dan 1433 voor ADO.NET 4.5 voor](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)meer informatie over TCP-poorten.

Zie [SQL Database-firewallregels](../../sql-database/sql-database-firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.

> [!Note]
> Naast IP-regels beheert de firewall virtuele netwerkregels. Virtuele netwerkregels zijn gebaseerd op eindpunten voor virtuele netwerkservice. Virtuele netwerkregels kunnen in sommige gevallen de voorkeur krijgen boven IP-regels. Zie [Eindpunten en regels voor virtual network service voor Azure SQL Database voor](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)meer informatie.

## <a name="enable-database-authentication"></a>Databaseverificatie inschakelen
SQL Database ondersteunt twee typen verificatie, SQL Server-verificatie en Azure AD-verificatie.

### <a name="sql-server-authentication"></a>*SQL Server-verificatie*

Dit biedt verschillende voordelen, zoals:

- Hiermee kan SQL Database omgevingen ondersteunen met gemengde besturingssystemen, waarbij niet alle gebruikers worden geverifieerd door een Windows-domein.
- Hiermee kan SQL Database oudere toepassingen en door partners geleverde toepassingen ondersteunen waarvoor SQL Server-verificatie vereist is.
- Hiermee kunnen gebruikers verbinding maken vanuit onbekende of niet-vertrouwde domeinen. Een voorbeeld is een toepassing waarbij gevestigde klanten verbinding maken met toegewezen SQL Server-aanmeldingen om de status van hun bestellingen te ontvangen.
- Hiermee kan SQL Database webgebaseerde toepassingen ondersteunen waarbij gebruikers hun eigen identiteit maken.
- Hiermee kunnen softwareontwikkelaars hun toepassingen distribueren met behulp van een complexe machtigingshiërarchie op basis van bekende, vooraf ingestelde SQL Server-aanmeldingen.

> [!NOTE]
> SQL Server-verificatie kan het Kerberos-beveiligingsprotocol niet gebruiken.

Als u SQL Server-verificatie gebruikt, moet u het:

- Beheer de sterke referenties zelf.
- Bescherm de referenties in de verbindingstekenreeks.
- (Mogelijk) de referenties die via het netwerk worden doorgegeven, te beschermen van de webserver naar de database. Zie [Hoe: Verbinding maken met SQL Server met SQL-verificatie in ASP.NET 2.0 voor](/previous-versions/msp-n-p/ff648340(v=pandp.10))meer informatie.

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD) verificatie*
Azure AD-verificatie is een mechanisme om verbinding te maken met Azure SQL Database en [SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in Azure AD. Met Azure AD-verificatie u de identiteiten van databasegebruikers en andere Microsoft-services op één centrale locatie beheren. Centraal identiteitsbeheer biedt één plek voor het beheren van databasegebruikers en vereenvoudigt het machtigingenbeheer.

> [!NOTE]
> We raden het gebruik van Azure AD-verificatie aan voor het gebruik van SQL Server-verificatie.

Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor SQL Server-verificatie.
- Het helpt de verspreiding van gebruikersidentiteiten over databaseservers te stoppen.
- Het maakt wachtwoordrotatie op één plaats mogelijk.
- Klanten kunnen databasemachtigingen beheren met externe (Azure AD)-groepen.
- Het kan het opslaan van wachtwoorden elimineren door geïntegreerde Windows-verificatie en andere vormen van verificatie in te schakelen die worden ondersteund door Azure Active Directory.
- Het gebruikt opgenomen databasegebruikers om identiteiten op databaseniveau te verifiëren.
- Het ondersteunt tokengebaseerde verificatie voor toepassingen die verbinding maken met SQL Database.
- Het ondersteunt AD FS (domeinfederatie) of native user/password authentication voor een lokaal Azure Active Directory-exemplaar zonder domeinsynchronisatie.
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal Authentication, waaronder Multi-Factor Authentication. Multi-Factor Authentication biedt sterke verificatie met een scala aan verificatieopties: telefoongesprek, sms, smartcards met pincode of melding van mobiele apps. Zie [SSMS-ondersteuning voor Azure AD Multi-Factor Authentication met SQL Database en SQL Data Warehouse voor](../../sql-database/sql-database-ssms-mfa-authentication.md)meer informatie.

De configuratiestappen omvatten de volgende procedures voor het configureren en gebruiken van Azure AD-verificatie:

- Azure AD maken en invullen.
- Optioneel: het Active Directory-exemplaar koppelen of wijzigen dat momenteel aan uw Azure-abonnement is gekoppeld.
- Maak een Azure Active Directory-beheerder voor Azure SQL Database of [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configureer uw clientcomputers.
- Contactpersonen maken voor databasegebruikers in uw database die zijn toegewezen aan Azure AD-identiteiten.
- Maak verbinding met uw database met Azure AD-identiteiten.

U gedetailleerde informatie vinden in [Azure Active Directory-verificatie gebruiken voor verificatie met SQL Database, Managed Instance of SQL Data Warehouse.](../../sql-database/sql-database-aad-authentication.md)

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Bescherm uw gegevens met behulp van versleuteling en beveiliging op rijniveau
[Azure SQL Database transparante gegevensversleuteling](../../sql-database/transparent-data-encryption-azure-sql.md) helpt gegevens op schijf te beschermen en beschermt tegen ongeautoriseerde toegang tot hardware. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. Transparante gegevensversleuteling versleutelt de opslag van een hele database met behulp van een symmetrische sleutel genaamd de databaseversleutelingssleutel.

Zelfs wanneer de volledige opslag is versleuteld, is het belangrijk om ook de database zelf te versleutelen. Dit is een implementatie van de defensie-diepgaande aanpak voor gegevensbescherming. Als u Azure SQL Database gebruikt en gevoelige gegevens wilt beschermen (zoals creditcard- of burgerservicenummers), u databases versleutelen met FIPS 140-2 gevalideerde 256-bits AES-versleuteling. Deze versleuteling voldoet aan de eisen van veel industriestandaarden (bijvoorbeeld HIPAA en PCI).

Bestanden met betrekking tot [bufferpoolextensie (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) worden niet versleuteld wanneer u een database versleutelt met behulp van transparante gegevensversleuteling. U moet versleutelingstools op bestandssysteemniveau zoals [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) of het [Encrypting File System (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) gebruiken voor BPE-gerelateerde bestanden.

Omdat een geautoriseerde gebruiker zoals een beveiligingsbeheerder of een databasebeheerder toegang heeft tot de gegevens, zelfs als de database is versleuteld met transparante gegevensversleuteling, moet u ook de volgende aanbevelingen opvolgen:

- SQL Server-verificatie inschakelen op databaseniveau.
- Gebruik Azure AD-verificatie met [RBAC-rollen](/azure/role-based-access-control/overview).
- Zorg ervoor dat gebruikers en toepassingen afzonderlijke accounts gebruiken om te verifiëren. Op deze manier u de machtigingen die aan gebruikers en toepassingen worden verleend, beperken en het risico op schadelijke activiteiten verminderen.
- Implementeren databasebeveiliging met behulp van vaste databaserollen (zoals db_datareader of db_datawriter). U ook aangepaste rollen voor uw toepassing maken om expliciete machtigingen toe te kennen aan geselecteerde databaseobjecten.

Voor andere manieren om uw gegevens te beveiligen, u rekening houden met:

- [Versleuteling op celniveau](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) om specifieke kolommen of zelfs cellen met gegevens met verschillende versleutelingssleutels te versleutelen.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), waarmee clients gevoelige gegevens in clienttoepassingen kunnen versleutelen en nooit de versleutelingssleutels aan de Database Engine (SQL Database of SQL Server) kunnen onthullen. Als gevolg hiervan biedt Always Encrypted een scheiding tussen degenen die eigenaar zijn van de gegevens (en deze kunnen bekijken) en degenen die de gegevens beheren (maar geen toegang moeten hebben).
- [Row-Level Security](/sql/relational-databases/security/row-level-security), waarmee klanten de toegang tot rijen in een databasetabel kunnen beheren op basis van de kenmerken van de gebruiker die een query uitvoert. (Voorbeeldkenmerken zijn groepslidmaatschap en uitvoeringscontext.)

Organisaties die geen versleuteling op databaseniveau gebruiken, zijn mogelijk gevoeliger voor aanvallen die gegevens in SQL-databases compromitteren.

U meer informatie over sql-database transparante gegevensversleuteling lezen door het artikel [Transparante gegevensversleuteling met Azure SQL Database te](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)lezen.

## <a name="enable-database-auditing"></a>Databasecontrole inschakelen
Als u een instantie van de SQL Server Database Engine of een afzonderlijke database controleert, worden gebeurtenissen bijhouden en registreren. Voor SQL Server u audits maken die specificaties bevatten voor gebeurtenissen op serverniveau en specificaties voor gebeurtenissen op databaseniveau. Gecontroleerde gebeurtenissen kunnen worden geschreven naar de gebeurtenislogboeken of naar controlebestanden.

Er zijn verschillende niveaus van controle voor SQL Server, afhankelijk van de vereisten van de overheid of standaarden voor uw installatie. SQL Server auditing biedt tools en processen voor het inschakelen, opslaan en bekijken van audits op verschillende server- en databaseobjecten.

[Azure SQL Database auditing](/azure/sql-database/sql-database-auditing) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in uw Azure-opslagaccount.

Controle kan u helpen de naleving van de regelgeving te handhaven, inzicht te krijgen in de databaseactiviteit en afwijkingen en afwijkingen te vinden die kunnen wijzen op zakelijke problemen of beveiligingsschendingen. Auditing vergemakkelijkt naleving van nalevingsnormen, maar garandeert geen naleving.

Zie [Aan de slag met SQL-databasecontrole](/azure/sql-database/sql-database-auditing)voor meer informatie over databasecontrole en hoe u deze inschakelen.

## <a name="enable-database-threat-detection"></a>Databasedetectie van bedreigingen inschakelen
Bescherming tegen bedreigingen gaat verder dan detectie. Bescherming tegen bedreigingen in de database omvat:

- Het ontdekken en classificeren van uw meest gevoelige gegevens, zodat u uw gegevens beschermen.
- Het implementeren van veilige configuraties in uw database, zodat u uw database beschermen.
- Detecteren en reageren op potentiële bedreigingen wanneer deze zich voordoen, zodat u snel reageren en herstellen.

**Aanbevolen procedures**: Ontdek, classificeer en label de gevoelige gegevens in uw databases.   
**Detail:** Classificeer de gegevens in uw SQL-database door [Gegevensdetectie en -classificatie](/azure/sql-database/sql-database-data-discovery-and-classification) in Azure SQL Database in te schakelen. U de toegang tot uw gevoelige gegevens in het Azure-dashboard controleren of rapporten downloaden.

**Aanbevolen procedures**: Houd kwetsbaarheden in database's bij, zodat u uw databasebeveiliging proactief verbeteren.   
**Detail:** Gebruik de Azure SQL Database [Vulnerability Assessment-service,](/azure/sql-database/sql-vulnerability-assessment) die scant op mogelijke databasekwetsbaarheden. De service maakt gebruik van een kennisbasis van regels die beveiligingsproblemen markeren en afwijkingen van aanbevolen procedures weergeven, zoals verkeerde configuraties, overmatige machtigingen en onbeveiligde gevoelige gegevens.

De regels zijn gebaseerd op de best practices van Microsoft en richten zich op de beveiligingsproblemen die de grootste risico's voor uw database en de waardevolle gegevens vormen. Ze hebben betrekking op zowel problemen op databaseniveau als beveiligingsproblemen op serverniveau, zoals serverfirewall-instellingen en machtigingen op serverniveau. Deze regels vertegenwoordigen ook veel van de vereisten van regelgevende instanties om aan hun nalevingsnormen te voldoen.

**Aanbevolen procedures**: Schakel bedreigingsdetectie in.  
**Detail:** Schakel Azure SQL Database [Threat Detection](/azure/sql-database/sql-database-threat-detection) in om beveiligingswaarschuwingen en aanbevelingen te ontvangen over het onderzoeken en beperken van bedreigingen. U krijgt meldingen over verdachte databaseactiviteiten, potentiële kwetsbaarheden en SQL-injectieaanvallen, evenals afwijkende databasetoegang en querypatronen.

[Advanced Threat Protection](/azure/sql-database/sql-advanced-threat-protection) is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Het omvat de eerder genoemde services: Data Discovery and Classification, Vulnerability Assessment en Threat Detection. Het biedt één locatie voor het inschakelen en beheren van deze mogelijkheden.

Het inschakelen van deze mogelijkheden helpt u:

- Voldoe aan de privacynormen en nalevingsvereisten voor regelgeving.
- Beheer de toegang tot uw databases en verhard hun beveiliging.
- Monitor een dynamische databaseomgeving waar wijzigingen moeilijk te volgen zijn.
- Detecteren en reageren op potentiële bedreigingen.

Bovendien integreert Bedreigingsdetectie waarschuwingen met Azure Security Center voor een centrale weergave van de beveiligingsstatus van al uw Azure-bronnen.

## <a name="next-steps"></a>Volgende stappen
Zie [aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging die u gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met Azure.

De volgende bronnen zijn beschikbaar om meer algemene informatie te verstrekken over Azure-beveiliging en gerelateerde Microsoft-services:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - voor up-to-date informatie over de nieuwste azure security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - waar beveiligingsproblemen van Microsoft, waaronder problemen met Azure, kunnen worden gemeld of via e-mail naarsecure@microsoft.com
