---
title: Overzicht van beveiliging in Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Begrijpen hoe Azure Data Lake Storage Gen1 een veiliger big data-archief is
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 7e987c56c3a125a03e3a90540313ace1f8adf47a
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086569"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Beveiliging in Azure Data Lake Storage Gen1

Veel bedrijven maken gebruik van big data analytics voor bedrijfsinzichten om hen te helpen slimme beslissingen te nemen. Een organisatie kan een complexe en gereguleerde omgeving hebben, met een toenemend aantal verschillende gebruikers. Het is van vitaal belang voor een onderneming om ervoor te zorgen dat kritieke bedrijfsgegevens veiliger worden opgeslagen, met het juiste toegangsniveau dat aan individuele gebruikers wordt verleend. Azure Data Lake Storage Gen1 is ontworpen om aan deze beveiligingsvereisten te voldoen. Lees in dit artikel meer over de beveiligingsmogelijkheden van Data Lake Storage Gen1, waaronder:

* Verificatie
* Autorisatie
* Netwerkisolatie
* Gegevensbeveiliging
* Controleren

## <a name="authentication-and-identity-management"></a>Verificatie en identiteitsbeheer

Verificatie is het proces waarbij de identiteit van een gebruiker wordt geverifieerd wanneer de gebruiker samenwerkt met Data Lake Storage Gen1 of met een service die verbinding maakt met Data Lake Storage Gen1. Voor identiteitsbeheer en verificatie maakt Data Lake Storage Gen1 gebruik van [Azure Active Directory,](../active-directory/fundamentals/active-directory-whatis.md)een uitgebreide cloudoplossing voor identiteits- en toegangsbeheer die het beheer van gebruikers en groepen vereenvoudigt.

Elk Azure-abonnement kan worden gekoppeld aan een exemplaar van Azure Active Directory. Alleen gebruikers en serviceidentiteiten die zijn gedefinieerd in uw Azure Active Directory-service hebben toegang tot uw Data Lake Storage Gen1-account, met behulp van de Azure-portal, opdrachtregelhulpprogramma's of via clienttoepassingen die uw organisatie bouwt met behulp van de Data Lake Storage Gen1 SDK. De belangrijkste voordelen van het gebruik van Azure Active Directory als gecentraliseerd toegangscontrolemechanisme zijn:

* Vereenvoudigd beheer van de identiteitslevenscyclus. De identiteit van een gebruiker of een service (een serviceprincipal identity) kan snel worden gemaakt en snel worden ingetrokken door simpelweg het account in de directory te verwijderen of uit te schakelen.
* Multi-factor authenticatie. [Meervoudige verificatie](../active-directory/authentication/multi-factor-authentication.md) biedt een extra beveiligingslaag voor aanmeldingen en transacties van gebruikers.
* Verificatie van elke client via een standaard open protocol, zoals OAuth of OpenID.
* Federatie met enterprise directory services en cloud identity providers.

## <a name="authorization-and-access-control"></a>Autorisatie- en toegangscontrole

Nadat Azure Active Directory een gebruiker verifieert zodat de gebruiker toegang heeft tot Data Lake Storage Gen1, regelt autorisatie toegangsmachtigingen voor Data Lake Storage Gen1. Data Lake Storage Gen1 scheidt de autorisatie voor accountgerelateerde en gegevensgerelateerde activiteiten op de volgende manier:

* [RBAC (Role-based access control)](../role-based-access-control/overview.md) van Azure voor accountbeheer
* POSIX ACL voor toegang tot gegevens in de winkel

### <a name="rbac-for-account-management"></a>RBAC voor accountmanagement

Er zijn standaard vier basisrollen gedefinieerd voor Data Lake Storage Gen1. De rollen maken verschillende bewerkingen mogelijk op een Data Lake Storage Gen1-account via de Azure-portal, PowerShell-cmdlets en REST-API's. De rollen Eigenaar en inzender kunnen verschillende beheerfuncties op het account uitvoeren. U de leesrol toewijzen aan gebruikers die alleen accountbeheergegevens bekijken.

![RBAC-rollen](./media/data-lake-store-security-overview/rbac-roles.png "RBAC-rollen")

Hoewel rollen zijn toegewezen voor accountbeheer, zijn sommige rollen van invloed op de toegang tot gegevens. U moet ACL's gebruiken om de toegang tot bewerkingen te beheren die een gebruiker op het bestandssysteem kan uitvoeren. In de volgende tabel ziet u een overzicht van beheerrechten en rechten voor gegevenstoegang voor de standaardrollen.

| Rollen | Beheersrechten | Rechten voor toegang tot gegevens | Uitleg |
| --- | --- | --- | --- |
| Geen toegewezen rol |Geen |Onder de hoede van ACL |De gebruiker kan de Azure-portal of Azure PowerShell-cmdlets niet gebruiken om door Data Lake Storage Gen1 te bladeren. De gebruiker kan alleen opdrachtregelgereedschappen gebruiken. |
| Eigenaar |Alle |Alle |De rol Eigenaar is een supergebruiker. Deze rol kan alles beheren en heeft volledige toegang tot gegevens. |
| Lezer |Alleen-lezen |Onder de hoede van ACL |De rol Reader kan alles bekijken met betrekking tot accountbeheer, zoals aan welke gebruiker is toegewezen. De rol Reader kan geen wijzigingen aanbrengen. |
| Inzender |Alle behalve rollen toevoegen en verwijderen |Onder de hoede van ACL |De rol Inzender kan bepaalde aspecten van een account beheren, zoals implementaties en het maken en beheren van waarschuwingen. De rol Inzender kan geen rollen toevoegen of verwijderen. |
| Beheerder van gebruikerstoegang |Rollen toevoegen en verwijderen |Onder de hoede van ACL |De functie Beheerders van gebruikerstoegang kan gebruikerstoegang tot accounts beheren. |

Zie Gebruikers [of beveiligingsgroepen toewijzen aan Data Lake Storage Gen1-accounts voor](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts)instructies .

### <a name="using-acls-for-operations-on-file-systems"></a>ACL's gebruiken voor bewerkingen op bestandssystemen

Data Lake Storage Gen1 is een hiërarchisch bestandssysteem zoals Hadoop Distributed File System (HDFS) en ondersteunt [POSIX ACL's.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) Het besturingselementen lezen (r), schrijven (w) en uitvoeren (x) machtigingen voor resources voor de rol Eigenaar, voor de groep Eigenaren, en voor andere gebruikers en groepen. In Data Lake Storage Gen1 kunnen ACL's worden ingeschakeld in de hoofdmap, op submappen en op afzonderlijke bestanden. Zie Toegangscontrole in Data Lake Storage Gen1 voor meer informatie over hoe ACL's werken in het kader van Data Lake Storage [Gen1.](data-lake-store-access-control.md)

We raden u aan ACL's voor meerdere gebruikers te definiëren met behulp van [beveiligingsgroepen.](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) Voeg gebruikers toe aan een beveiligingsgroep en wijs de ACL's voor een bestand of map toe aan die beveiligingsgroep. Dit is handig wanneer u toegewezen machtigingen wilt geven, omdat u beperkt bent tot maximaal 28 vermeldingen voor toegewezen machtigingen. Zie [Gebruikers of beveiligingsgroepen toewijzen als ACL's aan het Data Lake Storage Gen1-bestandssysteem voor](data-lake-store-secure-data.md#filepermissions)meer informatie over het beter beveiligen van gegevens die zijn opgeslagen in Data Lake Storage Gen1 door Azure Active Directory-beveiligingsgroepen te gebruiken.

![Machtigingen voor lijsttoegang](./media/data-lake-store-security-overview/adl.acl.2.png "Machtigingen voor lijsttoegang")

## <a name="network-isolation"></a>Netwerkisolatie

Gebruik Data Lake Storage Gen1 om de toegang tot uw gegevensarchief op netwerkniveau te beheren. U firewalls instellen en een IP-adresbereik definiëren voor uw vertrouwde clients. Met een IP-adresbereik kunnen alleen clients met een IP-adres binnen het gedefinieerde bereik verbinding maken met Data Lake Storage Gen1.

![Firewall-instellingen en IP-toegang](./media/data-lake-store-security-overview/firewall-ip-access.png "Firewall-instellingen en IP-adres")

VNet (Virtual Networks) ondersteunt servicetags voor Data Lake Gen 1. Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels van een bepaalde Azure-service. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd. Zie [overzicht van Azure-servicetags](../virtual-network/service-tags-overview.md)voor meer informatie.

## <a name="data-protection"></a>Gegevensbeveiliging

Data Lake Storage Gen1 beschermt uw gegevens gedurende de gehele levenscyclus. Voor onderweg gegevens gebruikt Data Lake Storage Gen1 het standaard Transport Layer Security (TLS 1.2)-protocol om gegevens via het netwerk te beveiligen.

![Versleuteling in Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Versleuteling in Data Lake Storage Gen1")

Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U kunt ervoor kiezen de gegevens te versleutelen of niet te versleutelen. Als u zich voorversleuteling kiest, worden gegevens die zijn opgeslagen in Data Lake Storage Gen1 versleuteld voordat u deze opslaat op persistente media. In een dergelijk geval versleutelt Data Lake Storage Gen1 automatisch gegevens voordat gegevens worden volgehouden en decodeert deze gegevens voordat deze worden opgehaald, zodat deze volledig transparant is voor de client die toegang heeft tot de gegevens. Er is geen codewijziging vereist aan de clientzijde om gegevens te versleutelen/decoderen.

Voor sleutelbeheer biedt Data Lake Storage Gen1 twee modi voor het beheren van uw hoofdversleutelingssleutels (MEK's), die nodig zijn voor het decoderen van gegevens die zijn opgeslagen in Data Lake Storage Gen1. U Data Lake Storage Gen1 de MEK's voor u laten beheren of ervoor kiezen om eigenaar te blijven van de MEKs met uw Azure Key Vault-account. U geeft de wijze van sleutelbeheer op tijdens het maken van een Data Lake Storage Gen1-account. Zie [Aan de slag met Azure Data Lake Storage Gen1 met de Azure Portal](data-lake-store-get-started-portal.md)voor meer informatie over het bieden van versleutelingsgerelateerde configuratie.

## <a name="activity-and-diagnostic-logs"></a>Diagnostische en activiteitslogboeken

U activiteits- of diagnostische logboeken gebruiken, afhankelijk van of u op zoek bent naar logboeken voor accountbeheergerelateerde activiteiten of gegevensgerelateerde activiteiten.

* Accountbeheergerelateerde activiteiten maken gebruik van Azure Resource Manager API's en worden via activiteitslogboeken in de Azure-portal opgedoken.
* Gegevensgerelateerde activiteiten gebruiken WebHDFS REST API's en worden via diagnostische logboeken in de Azure-portal opgedoken.

### <a name="activity-log"></a>Activiteitenlogboek

Om te voldoen aan de regelgeving, kan een organisatie vereisen adequate audit trails van account management activiteiten als het nodig heeft om te graven in specifieke incidenten. Data Lake Storage Gen1 heeft ingebouwde monitoring en registreert alle accountmanagementactiviteiten.

Bekijk en kies de kolommen die u wilt registreren voor controlepaden voor accountbeheer. U ook activiteitslogboeken exporteren naar Azure Storage.

![Activiteitenlogboek](./media/data-lake-store-security-overview/activity-logs.png "Activiteitenlogboek")

Zie [Activiteitenlogboeken weergeven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over het werken met activiteitenlogboeken.

### <a name="diagnostics-logs"></a>Logboeken met diagnostische gegevens

U controle en diagnostische logboekregistratie voor gegevenstoegang inschakelen in de Azure-portal en de logboeken verzenden naar een Azure Blob-opslagaccount, een gebeurtenishub of Azure Monitor-logboeken.

![Diagnostische logboeken](./media/data-lake-store-security-overview/diagnostic-logs.png "Logboeken met diagnostische gegevens")

Zie [Diagnostische logboeken voor Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)voor meer informatie over het werken met diagnostische logboeken met Data Lake Storage Gen1.

## <a name="summary"></a>Samenvatting

Enterprise-klanten eisen een data analytics cloudplatform dat veilig en gebruiksvriendelijk is. Data Lake Storage Gen1 is ontworpen om deze vereisten aan te pakken via identiteitsbeheer en verificatie via Azure Active Directory-integratie, ACL-gebaseerde autorisatie, netwerkisolatie, gegevensversleuteling tijdens het transport en in rust, en auditing.

Als u nieuwe functies wilt zien in Data Lake Storage Gen1, stuurt u ons uw feedback op het [Data Lake Storage Gen1 UserVoice-forum.](https://feedback.azure.com/forums/327234-data-lake)

## <a name="see-also"></a>Zie ook

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Aan de slag met Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
