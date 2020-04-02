---
title: Azure-beveiligingsservices en -technologieën | Microsoft Documenten
description: Het artikel bevat een samengestelde lijst met Azure Security-services en -technologieën.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 83548063a796401792bd19e7ec27be36a0efa4e4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549212"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Beveiligingsservices en -technologieën die beschikbaar zijn in Azure

In onze gesprekken met huidige en toekomstige Azure-klanten wordt ons vaak gevraagd "heeft u een lijst met alle beveiligingsgerelateerde services en technologieën die Azure te bieden heeft?"

Wanneer u de opties van cloudserviceproviders evalueert, is het handig om over deze informatie te beschikken. Daarom hebben we deze lijst op weg om u op weg te helpen.

Na verloop van tijd zal deze lijst veranderen en groeien, net als Azure. Zorg ervoor dat u deze pagina regelmatig controleert om op de hoogte te blijven van onze beveiligingsgerelateerde diensten en technologieën.

## <a name="general-azure-security"></a>Algemene Azure-beveiliging
|Service|Beschrijving|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](/azure/security-center/security-center-intro)| Een cloudworkloadbeveiligingsoplossing die beveiligingsbeheer en geavanceerde bescherming tegen bedreigingen biedt voor hybride cloudworkloads.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Een beveiligde geheimen winkel voor de wachtwoorden, verbinding strings, en andere informatie die u nodig hebt om uw apps werken. |
|[Azure Monitor-logboeken](/azure/log-analytics/log-analytics-overview)|Een bewakingsservice die telemetrie en andere gegevens verzamelt en een querytaal- en analyse-engine biedt om operationele inzichten te bieden voor uw apps en resources. Kan alleen of met andere services zoals Security Center worden gebruikt. |
|[Azure Dev/Test Labs](/azure/lab-services/devtest-lab-overview)|Een service waarmee ontwikkelaars en testers snel omgevingen in Azure kunnen maken, terwijl verspilling wordt geminimaliseerd en de kosten worden beperkt.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Opslagbeveiliging
|Service|Beschrijving|
|------|--------|
| [Azure&nbsp;&nbsp;Storage&nbsp;Service-versleuteling](/azure/storage/common/storage-service-encryption)|Een beveiligingsfunctie die uw gegevens automatisch versleutelt in Azure-opslag.   |
|[StorSimple versleutelde hybride opslag](/azure/storsimple/storsimple-ova-overview)| Een geïntegreerde opslagoplossing die opslagtaken tussen on-premises apparaten en Azure-cloudopslag beheert.|
|[Azure-client-side versleuteling](/azure/storage/common/storage-client-side-encryption)| Een versleutelingsoplossing aan de clientzijde die gegevens in clienttoepassingen versleutelt voordat deze wordt geüpload naar Azure Storage. decodeert ook de gegevens tijdens het downloaden. |
| [Gedeelde toegangshandtekeningen voor Azure Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Een handtekening voor gedeelde toegang biedt gedelegeerde toegang tot bronnen in uw opslagaccount.  |
|[Azure Storage-accountsleutels](/azure/storage/common/storage-create-storage-account)| Een toegangscontrolemethode voor Azure-opslag die wordt gebruikt voor verificatie wanneer het opslagaccount wordt geopend. |
|[Azure File deelt met SMB 3.0-versleuteling](/azure/storage/files/storage-files-introduction)|Een netwerkbeveiligingstechnologie die automatische netwerkversleuteling mogelijk maakt voor het SMB-protocol (Server Message Block). |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Een registratie- en metrische technologie voor gegevens in uw opslagaccount. |

<!------>

## <a name="database-security"></a>Databasebeveiliging
|Service|Beschrijving|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](/azure/sql-database/sql-database-firewall-configure)|Een functie voor netwerktoegangscontrole die beschermt tegen netwerkaanvallen in de database. |
|[Azure&nbsp;&nbsp;SQL-celversleuteling&nbsp;](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Een databasebeveiligingstechnologie die versleuteling biedt op een gedetailleerd niveau.  |
| [Azure&nbsp;&nbsp;SQL-verbindingsversleuteling](/azure/sql-database/sql-database-control-access)|Voor de beveiliging regelt SQL Database toegang met firewallregels die de connectiviteit beperken op basis van IP-adres. Met behulp van verificatiemechanismen moeten gebruikers hun identiteit kunnen aantonen en met autorisatiemechanismen worden gebruikers beperkt tot bepaalde gegevens en acties. |
| [Azure SQL-versleuteling](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Beschermt gevoelige gegevens, zoals creditcardnummers of nationale identificatienummers (bijvoorbeeld Amerikaanse burgerservicenummers), opgeslagen in Azure SQL Database- of SQL Server-databases.  |
| [Azure&nbsp;&nbsp;SQL-transparante gegevensversleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Een databasebeveiligingsfunctie die de opslag van een hele database versleutelt. |
| [Azure SQL-databasecontrole](/azure/sql-database/sql-database-auditing)|Een databasecontrolefunctie die databasegebeurtenissen bijhoudt en naar een controlelogboek in uw Azure-opslagaccount schrijft.  |


## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer
|Service|Beschrijving|
|------|--------|
| [Azure-rolegebaseerd&nbsp;&nbsp;&nbsp;toegangsbeheer](/azure/active-directory/role-based-access-control-configure)|Een toegangscontrolefunctie die is ontworpen om gebruikers toegang te geven tot alleen de resources die ze nodig hebben om toegang te krijgen op basis van hun rollen binnen de organisatie.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Een cloudgebaseerde verificatierepository die een multi-tenant, cloudgebaseerde directory en meerdere identiteitsbeheerservices binnen Azure ondersteunt.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Een service voor identiteitsbeheer waarmee u bepalen hoe klanten zich aanmelden, aanmelden en hun profielen beheren wanneer ze azure-gebaseerde toepassingen gebruiken.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Een cloudgebaseerde en beheerde versie van Active Directory Domain Services. |
| [Azure-verificatie met meerdere factoren](/azure/active-directory/authentication/multi-factor-authentication)| Een beveiligingsvoorziening die verschillende vormen van verificatie en verificatie gebruikt voordat toegang tot beveiligde informatie wordt toegestaan. |

## <a name="backup-and-disaster-recovery"></a>Back-up en herstel na noodgevallen
|Service|Beschrijving|
|------|--------|
| [Azure-back-up&nbsp;](/azure/backup/backup-introduction-to-azure-backup)| Een op Azure gebaseerde service die wordt gebruikt om back-ups te maken en gegevens te herstellen in de Azure-cloud. |
| [Azure-siteherstel&nbsp;&nbsp;](/azure/site-recovery/site-recovery-overview)|Een onlineservice die workloads nabootst die worden uitgevoerd op fysieke en virtuele machines (VM's) van een primaire site naar een secundaire locatie om herstel van services na een storing mogelijk te maken. |

## <a name="networking"></a>Netwerken
|Service|Beschrijving|
|------|--------|
| [Netwerkbeveiligingsgroepen&nbsp;&nbsp;](/azure/virtual-network/virtual-networks-nsg)| Een op het netwerk gebaseerde toegangscontrolefunctie die een 5-tuple gebruikt om beslissingen toe te staan of te weigeren.  |
| [Azure VPN-gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Een netwerkapparaat dat wordt gebruikt als VPN-eindpunt om cross-premises toegang tot Azure Virtual Networks mogelijk te maken.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|Een geavanceerde webapplicatieloadbalancer die kan routeren op basis van URL en SSL-offloading kan uitvoeren. |
|[Firewall voor webtoepassingen](/azure/frontdoor/waf-overview) (WAF)|Een functie van Application Gateway die gecentraliseerde bescherming van uw webapplicaties biedt tegen veelvoorkomende exploits en kwetsbaarheden|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|Een TCP/UDP-netwerklastbalans. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Een speciale WAN-koppeling tussen on-premises netwerken en Azure Virtual Networks. |
| [Azure-beheer](/azure/traffic-manager/traffic-manager-overview)| Een globale DNS load balancer.|
| [Azure-toepassingsproxy](/azure/active-directory/active-directory-application-proxy-get-started)| Een authenticerende front-end die wordt gebruikt om externe toegang te beveiligen voor on-premises webtoepassingen. |
|[Azure Firewall](/azure/firewall/overview)|Een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-bronnen beschermt.|
|[Azure DDoS-beveiliging](/azure/virtual-network/ddos-protection-overview)|In combinatie met best practices voor applicatieontwerp biedt het verdediging tegen DDoS-aanvallen.|
|[Eindpunten van virtuele netwerkservice](/azure/virtual-network/virtual-network-service-endpoints-overview)|Hiermee breidt u uw privéadresruimte voor virtuele netwerken en de identiteit van uw VNet uit naar de Azure-services via een directe verbinding.|