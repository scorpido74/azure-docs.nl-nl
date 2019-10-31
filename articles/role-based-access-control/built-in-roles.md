---
title: Ingebouwde rollen voor Azure-resources | Microsoft Docs
description: Beschrijft de ingebouwde rollen voor op rollen gebaseerd toegangs beheer (RBAC) en Azure-resources. Een lijst met de acties, intact, DataActions en NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/28/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: dff222788480eea882614ad29478df1dce359199
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101282"
---
# <a name="built-in-roles-for-azure-resources"></a>Ingebouwde rollen voor Azure-resources

[Op rollen gebaseerd toegangs beheer (RBAC)](overview.md) heeft verschillende ingebouwde rollen voor Azure-resources die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Roltoewijzingen zijn de manier waarop u de toegang tot Azure-resources beheert. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste rollen maken voor Azure-resources](custom-roles.md).

Dit artikel bevat een overzicht van de ingebouwde rollen voor Azure-resources, die altijd in ontwikkeling zijn. Gebruik de lijst met [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) of [AZ Role definition](/cli/azure/role/definition#az-role-definition-list)om de nieuwste rollen op te halen. Als u op zoek bent naar beheerders rollen voor Azure Active Directory, raadpleegt u [machtigingen voor beheerdersrol in azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Beschrijvingen van ingebouwde rollen

De volgende tabel bevat een korte beschrijving van elke ingebouwde rol. Klik op de naam van de rol om de lijst met `Actions`, `NotActions`, `DataActions`en `NotDataActions` voor elke rol weer te geven. Zie voor meer informatie over de werking van deze acties en over de manier waarop deze worden toegepast op het beheer-en gegevens abonnement [begrijpen functie definities voor Azure-resources](role-definitions.md).


| Ingebouwde rol | Beschrijving |
| --- | --- |
| [Eigenaar](#owner) | Hiermee kunt u alles beheren, inclusief toegang tot resources. |
| [Inzender](#contributor) | Hiermee beheert u alles behalve het verlenen van toegang tot resources. |
| [Lezer](#reader) | Hiermee kunt u alles weer geven, maar geen wijzigingen aanbrengen. |
| [AcrDelete](#acrdelete) | ACR verwijderen |
| [AcrImageSigner](#acrimagesigner) | ACR-installatie kopie ondertekenaar |
| [AcrPull](#acrpull) | ACR pull |
| [AcrPush](#acrpush) | ACR-push |
| [AcrQuarantineReader](#acrquarantinereader) | ACR quarantaine gegevens lezer |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR quarantaine gegevens schrijver |
| [Inzender van API Management-service](#api-management-service-contributor) | Kan de service en de Api's beheren |
| [Functie API Management service operator](#api-management-service-operator-role) | Kan de service beheren, maar niet de Api's |
| [API Management functie Service lezer](#api-management-service-reader-role) | Alleen-lezen toegang tot de service en Api's |
| [Inzender voor Application Insights onderdelen](#application-insights-component-contributor) | Kan Application Insights onderdelen beheren |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Geeft gebruikers machtigingen voor het weer geven en downloaden van moment opnamen van fout opsporing die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [eigenaar](#owner) of [Inzender](#contributor) . |
| [Automation-taak operator](#automation-job-operator) | Maak en beheer taken met behulp van Automation-Runbooks. |
| [Automation-operator](#automation-operator) | Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten |
| [Automation-Runbook-operator](#automation-runbook-operator) | Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken. |
| [AVERE-bijdrager](#avere-contributor) | Kan een avere vFXT-cluster maken en beheren. |
| [AVERE-operator](#avere-operator) | Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster |
| [Eigenaar van Azure Event Hubs-gegevens](#azure-event-hubs-data-owner) | Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources. |
| [Gegevens ontvanger van Azure Event Hubs](#azure-event-hubs-data-receiver) | Hiermee krijgt u toegang tot Azure Event Hubs-resources. |
| [Afzender van Azure Event Hubs gegevens](#azure-event-hubs-data-sender) | Hiermee wordt toegang tot Azure Event Hubs-resources verzonden. |
| [Rol van Cluster beheerder voor Azure Kubernetes-service](#azure-kubernetes-service-cluster-admin-role) | Lijst met actie voor cluster beheer referenties. |
| [Gebruikersrol Azure Kubernetes service-cluster](#azure-kubernetes-service-cluster-user-role) | Geef een lijst actie voor de gebruikers referenties van het cluster op. |
| [Azure Maps gegevens lezer (preview-versie)](#azure-maps-data-reader-preview) | Hiermee wordt toegang verleend om gerelateerde gegevens te lezen vanuit een Azure Maps-account. |
| [Azure Sentinel-bijdrager](#azure-sentinel-contributor) | Azure Sentinel-bijdrager |
| [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader |
| [Azure Sentinel responder](#azure-sentinel-responder) | Azure Sentinel responder |
| [Gegevens eigenaar Azure Service Bus](#azure-service-bus-data-owner) | Hiermee krijgt u volledige toegang tot Azure Service Bus-resources. |
| [Gegevens ontvanger Azure Service Bus](#azure-service-bus-data-receiver) | Hiermee krijgt u toegang tot Azure Service Bus-resources. |
| [Afzender van Azure Service Bus gegevens](#azure-service-bus-data-sender) | Hiermee kunt u toegang tot Azure Service Bus resources verzenden. |
| [Registratie-eigenaar Azure Stack](#azure-stack-registration-owner) | Hiermee kunt u Azure Stack registraties beheren. |
| [Back-upinzender](#backup-contributor) | Hiermee kunt u de back-upservice beheren, maar geen kluizen maken en anderen toegang verlenen |
| [Back-upoperator](#backup-operator) | Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang |
| [Back-uplezer](#backup-reader) | Kan back-upservices weer geven, maar kan geen wijzigingen aanbrengen |
| [Facturerings lezer](#billing-reader) | Hiermee staat u lees toegang tot facturerings gegevens toe |
| [BizTalk-bijdrager](#biztalk-contributor) | Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service. |
| [Toegang tot Block Chain-leden knooppunt (preview-versie)](#blockchain-member-node-access-preview) | Hiermee wordt toegang tot Block Chain-leden knooppunten toegestaan |
| [Blauw druk bijdrager](#blueprint-contributor) | Kan blauw drukken-definities beheren, maar niet toewijzen. |
| [Blauw druk-operator](#blueprint-operator) | Kan bestaande gepubliceerde blauw drukken toewijzen, maar kan geen nieuwe blauw drukken maken. Opmerking: dit werkt alleen als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. |
| [Inzender voor CDN-eind punten](#cdn-endpoint-contributor) | Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. |
| [CDN-eindpunt lezer](#cdn-endpoint-reader) | Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen. |
| [Inzender voor CDN-profiel](#cdn-profile-contributor) | Kan CDN-profielen en de bijbehorende eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. |
| [CDN-profiel lezer](#cdn-profile-reader) | Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen. |
| [Inzender voor klassieke netwerken](#classic-network-contributor) | Hiermee beheert u klassieke netwerken, maar hebt u geen toegang tot de netwerk bestanden. |
| [Inzender voor klassieke opslag accounts](#classic-storage-account-contributor) | Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account. |
| [Service functie voor de sleutel operator voor klassieke opslag accounts](#classic-storage-account-key-operator-service-role) | De sleutel operators voor klassieke opslag accounts zijn toegestaan om sleutels weer te geven en opnieuw te genereren voor klassieke opslag. |
| [Inzender voor klassieke virtuele machines](#classic-virtual-machine-contributor) | Hiermee beheert u klassieke virtuele machines, maar kunt u niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. |
| [Inzender Cognitive Services](#cognitive-services-contributor) | Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. |
| [Cognitive Services gegevens lezer (preview-versie)](#cognitive-services-data-reader-preview) | Hiermee kunt u Cognitive Services gegevens lezen. |
| [Cognitive Services gebruiker](#cognitive-services-user) | Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven. |
| [Rol van Cosmos DB-account lezer](#cosmos-db-account-reader-role) | Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts. |
| [Cosmos DB-operator](#cosmos-db-operator) | Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang tot gegevens. Hiermee voor komt u toegang tot account sleutels en verbindings reeksen. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Kan een terugzet aanvraag indienen voor een Cosmos DB-Data Base of een container voor een account |
| [Inzender Cost Management](#cost-management-contributor) | Kan kosten bekijken en kosten configuratie beheren (bijvoorbeeld budgetten, exports) |
| [Cost Management lezer](#cost-management-reader) | Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exports) |
| [Inzender Data Box](#data-box-contributor) | Hiermee kunt u alles beheren onder Data Box Service, behalve dat anderen toegang verlenen. |
| [Data Box lezer](#data-box-reader) | Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen. |
| [Inzender Data Factory](#data-factory-contributor) | Het maken en beheren van gegevens fabrieken, evenals onderliggende resources. |
| [Data Lake Analytics-ontwikkelaar](#data-lake-analytics-developer) | Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen. |
| [Gegevens opschoner](#data-purger) | Kan Analytics-gegevens verwijderen |
| [DevTest Labs-gebruiker](#devtest-labs-user) | Met kunt u verbinding maken met uw virtuele machines in uw Azure DevTest Labs, deze starten, opnieuw opstarten en afsluiten. |
| [Inzender DNS-zone](#dns-zone-contributor) | Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen. |
| [Inzender voor DocumentDB-accounts](#documentdb-account-contributor) | Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. |
| [EventGrid EventSubscription-bijdrager](#eventgrid-eventsubscription-contributor) | Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren. |
| [EventGrid EventSubscription-lezer](#eventgrid-eventsubscription-reader) | Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen. |
| [HDInsight-cluster operator](#hdinsight-cluster-operator) | Hiermee kunt u HDInsight-cluster configuraties lezen en wijzigen. |
| [Inzender voor HDInsight Domain Services](#hdinsight-domain-services-contributor) | Kan gerelateerde bewerkingen die betrekking hebben op domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package |
| [Inzender voor Intelligent Systems-account](#intelligent-systems-account-contributor) | Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze. |
| [Inzender Key Vault](#key-vault-contributor) | Hiermee kunt u sleutel kluizen beheren, maar niet de toegang tot de kluis. |
| [Lab-Maker](#lab-creator) | Met kunt u uw beheerde Labs maken, beheren en verwijderen in uw Azure Lab-accounts. |
| [Inzender Log Analytics](#log-analytics-contributor) | Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources. |
| [Log Analytics lezer](#log-analytics-reader) | Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. |
| [Inzender voor logische apps](#logic-app-contributor) | Hiermee kunt u logische apps beheren, maar niet wijzigen. |
| [Logische app-operator](#logic-app-operator) | Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken. |
| [Rol beheerde toepassings operator](#managed-application-operator-role) | Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren |
| [Lezer voor beheerde toepassingen](#managed-applications-reader) | Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen. |
| [Inzender beheerde identiteit](#managed-identity-contributor) | Aan de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen |
| [Beheerde identiteits operator](#managed-identity-operator) | Door gebruiker toegewezen identiteit lezen en toewijzen |
| [Rol voor registratie toewijzing beheerde services verwijderen](#managed-services-registration-assignment-delete-role) | Met de functie voor registratie toewijzing van beheerde services verwijderen kan de Tenant gebruikers beheren de registratie toewijzing verwijderen die aan de Tenant is toegewezen. |
| [Inzender beheer groep](#management-group-contributor) | Rol Inzender beheer groep |
| [Lezer beheer groep](#management-group-reader) | Rol van lezer van beheer groep |
| [Inzender bewaken](#monitoring-contributor) | Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [De uitgever van metrische gegevens controleren](#monitoring-metrics-publisher) | Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources |
| [Bewakings lezer](#monitoring-reader) | Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Inzender voor netwerken](#network-contributor) | Hiermee kunt u netwerken beheren, maar niet de toegang tot de netwerk bestanden. |
| [Nieuwe Inzender voor Relic APM-account](#new-relic-apm-account-contributor) | Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze. |
| [Lezer en gegevens toegang](#reader-and-data-access) | Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account. |
| [Inzender Redis Cache](#redis-cache-contributor) | Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden. |
| [Inzender voor resource beleid (preview-versie)](#resource-policy-contributor-preview) | Evaluatie Alsnog gebruikers van EA, met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie. |
| [Inzender voor scheduler-taak verzamelingen](#scheduler-job-collections-contributor) | Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken. |
| [Inzender Search Service](#search-service-contributor) | Hiermee kunt u zoek services beheren, maar niet de toegang tot ze. |
| [Beveiligings beheerder](#security-admin) | In Security Center: kan beveiligings beleid weer geven, beveiligings status weer geven, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren |
| [Beveiligings beheer (verouderd)](#security-manager-legacy) | Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder |
| [Beveiligings lezer](#security-reader) | In Security Center: kunt u aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar geen wijzigingen aanbrengen |
| [Inzender Site Recovery](#site-recovery-contributor) | Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing |
| [Site Recovery-operator](#site-recovery-operator) | Een failover en failback, maar geen andere Site Recovery beheer bewerkingen uitvoeren |
| [Site Recovery lezer](#site-recovery-reader) | Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren |
| [Inzender voor ruimtelijke ankers](#spatial-anchors-account-contributor) | Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen |
| [Eigenaar van ruimtelijk ankers](#spatial-anchors-account-owner) | Hiermee kunt u ruimtelijke ankers in uw account beheren, met inbegrip van het verwijderen ervan |
| [Lezer van ruimtelijk ankers-account](#spatial-anchors-account-reader) | Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen |
| [Inzender voor SQL-data base](#sql-db-contributor) | Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. |
| [Inzender voor SQL Managed instance](#sql-managed-instance-contributor) | Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen. |
| [SQL-beveiligings beheer](#sql-security-manager) | Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. |
| [Inzender SQL Server](#sql-server-contributor) | Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid. |
| [Inzender voor opslagaccounts](#storage-account-contributor) | Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie. |
| [Functie Service-sleutel operator van opslag account](#storage-account-key-operator-service-role) | Kan de toegangs sleutels voor het opslag account weer geven en opnieuw genereren. |
| [Inzender voor Storage BLOB-gegevens](#storage-blob-data-contributor) | Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Eigenaar van gegevens van opslag-BLOB](#storage-blob-data-owner) | Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Gegevens lezer van BLOB voor opslag](#storage-blob-data-reader) | Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Delegering van opslag-BLOB](#storage-blob-delegator) | Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. |
| [Inzender voor opslag bestands gegevens SMB delen](#storage-file-data-smb-share-contributor) | Hiermee wordt lees-, schrijf-en verwijder toegang in Azure Storage bestands shares via SMB toegestaan |
| [Opslag bestands gegevens SMB-share verhoogde Inzender](#storage-file-data-smb-share-elevated-contributor) | Hiermee wordt toegang tot NTFS-machtigingen voor lezen, schrijven, verwijderen en wijzigen in Azure Storage bestands shares via SMB toegestaan |
| [Reader voor gegevens van SMB-share voor opslag bestand](#storage-file-data-smb-share-reader) | Hiermee wordt lees toegang tot Azure file share via SMB toegestaan |
| [Inzender voor opslag wachtrij gegevens](#storage-queue-data-contributor) | Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Processor voor gegevens berichten van de opslag wachtrij](#storage-queue-data-message-processor) | Een bericht uit een Azure Storage wachtrij bekijken, ophalen en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Afzender gegevens bericht van opslag wachtrij](#storage-queue-data-message-sender) | Berichten toevoegen aan een Azure Storage wachtrij. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Gegevens lezer van de opslag wachtrij](#storage-queue-data-reader) | Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
| [Inzender voor ondersteunings aanvragen](#support-request-contributor) | Hiermee kunt u ondersteunings aanvragen maken en beheren |
| [Inzender Traffic Manager](#traffic-manager-contributor) | Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft. |
| [Beheerder van gebruikerstoegang](#user-access-administrator) | Hiermee beheert u de gebruikers toegang tot Azure-resources. |
| [Aanmelding voor de beheerder van de virtuele machine](#virtual-machine-administrator-login) | Virtual Machines in de portal weer geven en u aanmelden als beheerder |
| [Inzender voor virtuele machines](#virtual-machine-contributor) | Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. |
| [Gebruikers aanmelding voor de virtuele machine](#virtual-machine-user-login) | Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. |
| [Inzender voor webabonnementen](#web-plan-contributor) | Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen. |
| [Website bijdrager](#website-contributor) | Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites. |


## <a name="owner"></a>Eigenaar
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u alles beheren, inclusief toegang tot resources. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Acties** |  |
> | * | Resources van alle typen maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="contributor"></a>Inzender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u alles behalve het verlenen van toegang tot resources. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Acties** |  |
> | * | Resources van alle typen maken en beheren |
> | **Intact** |  |
> | Micro soft. Authorization/*/Delete | Rollen, beleids toewijzingen, beleids definities en definities van beleids sets verwijderen |
> | Micro soft. Authorization/*/write | Rollen, roltoewijzingen, beleids toewijzingen, beleids definities en definities van beleids sets maken |
> | Micro soft. Authorization/elevateAccess/Action | Hiermee wordt de gebruiker toegang tot beheerders verleend in het Tenant bereik |
> | Micro soft. blauw druk/blueprintAssignments/schrijven | Een blauw druk-toewijzing maken of bijwerken |
> | Micro soft. blauw druk/blueprintAssignments/verwijderen | Eventuele blauw drukken-toewijzingen verwijderen |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="reader"></a>Lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u alles weer geven, maar geen wijzigingen aanbrengen. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | ACR verwijderen |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/artefacten/verwijderen | Artefact verwijderen in container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | ACR-installatie kopie ondertekenaar |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/ondertekenen/schrijven | Meta gegevens van de vertrouwens relatie push/pull voor een container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | ACR pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/pull/lezen | Pull of ophalen van installatie kopieën uit een container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | ACR-push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/pull/lezen | Pull of ophalen van installatie kopieën uit een container register. |
> | Micro soft. ContainerRegistry/registers/push/schrijven | Push of schrijf installatie kopieën naar een container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | ACR quarantaine gegevens lezer |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/quarantaine/lezen | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | ACR quarantaine gegevens schrijver |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/quarantaine/lezen | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | Micro soft. ContainerRegistry/registers/quarantaine/schrijven | De quarantaine status van in quarantaine geplaatste installatie kopieën schrijven/wijzigen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="api-management-service-contributor"></a>Inzender van API Management-service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan de service en de Api's beheren |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Acties** |  |
> | Micro soft. ApiManagement/service/* | API Management-service maken en beheren |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="api-management-service-operator-role"></a>Functie API Management service operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan de service beheren, maar niet de Api's |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Acties** |  |
> | Micro soft. ApiManagement/service/*/Read | API Management service-exemplaren lezen |
> | Micro soft. ApiManagement/service/Backup/Action | Backup API Management-service naar de opgegeven container in een door de gebruiker opgegeven opslag account |
> | Micro soft. ApiManagement/service/verwijderen | API Management service-exemplaar verwijderen |
> | Micro soft. ApiManagement/service/managedeployments/actie | SKU/eenheden wijzigen, regionale implementaties van API Management service toevoegen/verwijderen |
> | Micro soft. ApiManagement/service/lezen | Meta gegevens voor een API Management service-exemplaar lezen |
> | Micro soft. ApiManagement/service/Restore/Action | De API Management-service herstellen vanuit de opgegeven container in een opslag account dat door de gebruiker is opgegeven |
> | Micro soft. ApiManagement/service/updatecertificate/actie | SSL-certificaat voor een API Management-service uploaden |
> | Micro soft. ApiManagement/service/updatehostname/actie | Aangepaste domein namen instellen, bijwerken of verwijderen voor een API Management-service |
> | Micro soft. ApiManagement/service/schrijven | Een nieuw exemplaar van API Management service maken |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Micro soft. ApiManagement/Service/gebruikers/sleutels/lezen | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="api-management-service-reader-role"></a>API Management functie Service lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Alleen-lezen toegang tot de service en Api's |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Acties** |  |
> | Micro soft. ApiManagement/service/*/Read | API Management service-exemplaren lezen |
> | Micro soft. ApiManagement/service/lezen | Meta gegevens voor een API Management service-exemplaar lezen |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Micro soft. ApiManagement/Service/gebruikers/sleutels/lezen | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="application-insights-component-contributor"></a>Inzender voor Application Insights onderdelen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan Application Insights onderdelen beheren |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. Insights/onderdelen/* | Insights-onderdelen maken en beheren |
> | Micro soft. Insights/webtests/* | Webtests maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Geeft gebruikers machtigingen voor het weer geven en downloaden van moment opnamen van fout opsporing die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [eigenaar](#owner) of [Inzender](#contributor) . |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/onderdelen/*/Read |  |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="automation-job-operator"></a>Automation-taak operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Maak en beheer taken met behulp van Automation-Runbooks. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Automation/automationAccounts/hybridRunbookWorkerGroups/lezen | Hybrid Runbook Worker resources lezen |
> | Micro soft. Automation/automationAccounts/Jobs/lezen | Hiermee wordt een Azure Automation-taak opgehaald |
> | Micro soft. Automation/automationAccounts/Jobs/resume/Action | Hiermee wordt een Azure Automation taak hervat |
> | Micro soft. Automation/automationAccounts/Jobs/stop/actie | Hiermee wordt een Azure Automation taak gestopt |
> | Micro soft. Automation/automationAccounts/Jobs/stromen/lezen | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Micro soft. Automation/automationAccounts/Jobs/onderbreken/actie | Hiermee wordt een Azure Automation taak onderbroken |
> | Micro soft. Automation/automationAccounts/Jobs/schrijven | Hiermee maakt u een Azure Automation taak |
> | Micro soft. Automation/automationAccounts/Jobs/uitvoer/lezen | Hiermee wordt de uitvoer van een taak opgehaald |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="automation-operator"></a>Automation-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Automation/automationAccounts/hybridRunbookWorkerGroups/lezen | Hybrid Runbook Worker resources lezen |
> | Micro soft. Automation/automationAccounts/Jobs/lezen | Hiermee wordt een Azure Automation-taak opgehaald |
> | Micro soft. Automation/automationAccounts/Jobs/resume/Action | Hiermee wordt een Azure Automation taak hervat |
> | Micro soft. Automation/automationAccounts/Jobs/stop/actie | Hiermee wordt een Azure Automation taak gestopt |
> | Micro soft. Automation/automationAccounts/Jobs/stromen/lezen | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Micro soft. Automation/automationAccounts/Jobs/onderbreken/actie | Hiermee wordt een Azure Automation taak onderbroken |
> | Micro soft. Automation/automationAccounts/Jobs/schrijven | Hiermee maakt u een Azure Automation taak |
> | Micro soft. Automation/automationAccounts/jobSchedules/lezen | Hiermee wordt een Azure Automation taak planning opgehaald |
> | Micro soft. Automation/automationAccounts/jobSchedules/write | Hiermee maakt u een Azure Automation taak schema |
> | Micro soft. Automation/automationAccounts/linkedWorkspace/lezen | Hiermee wordt de werk ruimte opgehaald die is gekoppeld aan het Automation-account |
> | Micro soft. Automation/automationAccounts/lezen | Hiermee wordt een Azure Automation-account opgehaald |
> | Micro soft. Automation/automationAccounts/runbooks/lezen | Hiermee wordt een Azure Automation runbook opgehaald |
> | Micro soft. Automation/automationAccounts/schedules/lezen | Hiermee wordt een Azure Automation schema-element opgehaald |
> | Micro soft. Automation/automationAccounts/schedules/write | Hiermee wordt een Azure Automation Schedule-Asset gemaakt of bijgewerkt |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. Automation/automationAccounts/Jobs/uitvoer/lezen | Hiermee wordt de uitvoer van een taak opgehaald |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="automation-runbook-operator"></a>Automation-Runbook-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Automation/automationAccounts/runbooks/lezen | Hiermee wordt een Azure Automation runbook opgehaald |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="avere-contributor"></a>AVERE-bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan een avere vFXT-cluster maken en beheren. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Compute/*/Read |  |
> | Micro soft. Compute/Availability sets/* |  |
> | Micro soft. Compute/informatie/* |  |
> | Micro soft. Compute/schijven/* |  |
> | Micro soft. Network/*/Read |  |
> | Micro soft. Network/networkInterfaces/* |  |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Network/virtualNetworks/subnetten/lezen | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | Micro soft. Network/virtualNetworks/subnetten/toevoegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Micro soft. netwerk/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Micro soft. Network/networkSecurityGroups/samen voegen/actie | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/*/Read |  |
> | Micro soft. Storage/Storage accounts/* |  |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/resources/lezen | Hiermee haalt u de resources voor de resource groep op. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/verwijderen | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/lezen | Retourneert een BLOB of een lijst met blobs |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/schrijven | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="avere-operator"></a>AVERE-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Acties** |  |
> | Micro soft. Compute/informatie/lezen | De eigenschappen van een virtuele machine ophalen |
> | Micro soft. Network/networkInterfaces/lezen | Hiermee haalt u een definitie van een netwerk interface.  |
> | Micro soft. Network/networkInterfaces/schrijven | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Network/virtualNetworks/subnetten/lezen | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | Micro soft. Network/virtualNetworks/subnetten/toevoegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Micro soft. Network/networkSecurityGroups/samen voegen/actie | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/blobServices/containers/verwijderen | Retourneert het resultaat van het verwijderen van een container |
> | Micro soft. Storage/Storage accounts/blobServices/containers/lezen | Hiermee wordt een lijst met containers opgehaald |
> | Micro soft. Storage/Storage accounts/blobServices/containers/schrijven | Retourneert het resultaat van de put-BLOB-container |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/verwijderen | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/lezen | Retourneert een BLOB of een lijst met blobs |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/schrijven | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-event-hubs-data-owner"></a>Eigenaar van Azure Event Hubs-gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Acties** |  |
> | Micro soft. EventHub/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. EventHub/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-event-hubs-data-receiver"></a>Gegevens ontvanger van Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u toegang tot Azure Event Hubs-resources. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Acties** |  |
> | Micro soft. EventHub/*/eventhubs/consumergroups/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. EventHub/*/receive/Action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-event-hubs-data-sender"></a>Afzender van Azure Event Hubs gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt toegang tot Azure Event Hubs-resources verzonden. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Acties** |  |
> | Micro soft. EventHub/*/eventhubs/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Rol van Cluster beheerder voor Azure Kubernetes-service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Lijst met actie voor cluster beheer referenties. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Acties** |  |
> | Micro soft. container service/managedClusters/listClusterAdminCredential/Action | De clusterAdmin-referentie van een beheerd cluster weer geven |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Gebruikersrol Azure Kubernetes service-cluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Geef een lijst actie voor de gebruikers referenties van het cluster op. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Acties** |  |
> | Micro soft. container service/managedClusters/listClusterUserCredential/Action | De clusterUser-referentie van een beheerd cluster weer geven |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure Maps gegevens lezer (preview-versie)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt toegang verleend om gerelateerde gegevens te lezen vanuit een Azure Maps-account. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Maps/accounts/gegevens/lezen | Hiermee wordt het lezen van gegevens toegang verleend aan een Maps-account. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-sentinel-contributor"></a>Azure Sentinel-bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Sentinel-bijdrager |
> | **Id** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Acties** |  |
> | Micro soft. SecurityInsights/* |  |
> | Micro soft. OperationalInsights/werk ruimten/Analytics/query/actie | Zoek met een nieuwe engine. |
> | Micro soft. OperationalInsights/werk ruimten/lezen | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Micro soft. OperationalInsights/werk ruimten/savedSearches/* |  |
> | Micro soft. OperationsManagement/Solutions/lezen | De OMS-oplossing ophalen |
> | Micro soft. OperationalInsights/werk ruimten/query/lezen | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. Insights/werkmappen/* |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-sentinel-reader"></a>Azure Sentinel Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Sentinel Reader |
> | **Id** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Acties** |  |
> | Micro soft. SecurityInsights/*/Read |  |
> | Micro soft. OperationalInsights/werk ruimten/Analytics/query/actie | Zoek met een nieuwe engine. |
> | Micro soft. OperationalInsights/werk ruimten/lezen | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Micro soft. OperationalInsights/werk ruimten/savedSearches/lezen | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | Micro soft. OperationsManagement/Solutions/lezen | De OMS-oplossing ophalen |
> | Micro soft. OperationalInsights/werk ruimten/query/lezen | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-sentinel-responder"></a>Azure Sentinel responder
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Sentinel responder |
> | **Id** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Acties** |  |
> | Micro soft. SecurityInsights/*/Read |  |
> | Micro soft. SecurityInsights/cases/* |  |
> | Micro soft. OperationalInsights/werk ruimten/Analytics/query/actie | Zoek met een nieuwe engine. |
> | Micro soft. OperationalInsights/werk ruimten/lezen | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. OperationalInsights/werk ruimten/savedSearches/lezen | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | Micro soft. OperationsManagement/Solutions/lezen | De OMS-oplossing ophalen |
> | Micro soft. OperationalInsights/werk ruimten/query/lezen | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-service-bus-data-owner"></a>Gegevens eigenaar Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u volledige toegang tot Azure Service Bus-resources. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Acties** |  |
> | Micro soft. ServiceBus/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-service-bus-data-receiver"></a>Gegevens ontvanger Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u toegang tot Azure Service Bus-resources. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Acties** |  |
> | Micro soft. ServiceBus/*/queues/Read |  |
> | Micro soft. ServiceBus/*/topics/Read |  |
> | Micro soft. ServiceBus/*/topics/Subscriptions/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. ServiceBus/*/receive/Action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-service-bus-data-sender"></a>Afzender van Azure Service Bus gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u toegang tot Azure Service Bus resources verzenden. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Acties** |  |
> | Micro soft. ServiceBus/*/queues/Read |  |
> | Micro soft. ServiceBus/*/topics/Read |  |
> | Micro soft. ServiceBus/*/topics/Subscriptions/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-stack-registration-owner"></a>Registratie-eigenaar Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u Azure Stack registraties beheren. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Acties** |  |
> | Micro soft. AzureStack/registraties/producten/*/Action |  |
> | Micro soft. AzureStack/registraties/producten/lezen | Hiermee worden de eigenschappen van een Azure Stack Marketplace-product opgehaald |
> | Micro soft. AzureStack/registraties/lezen | Hiermee worden de eigenschappen van een Azure Stack registratie opgehaald |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="backup-contributor"></a>Back-upinzender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de back-upservice beheren, maar geen kluizen maken en anderen toegang verlenen |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Recovery Services/locaties/* |  |
> | Micro soft. Recovery Services/kluizen/backupFabrics/operationResults/* | Resultaten van de werking van het back-upbeheer beheren |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/* | Back-upcontainers maken en beheren in back-upinfrastructuur van Recovery Services kluis |
> | Micro soft. Recovery Services/kluizen/backupFabrics/refreshContainers/actie | Hiermee vernieuwt u de container lijst |
> | Micro soft. Recovery Services/kluizen/backupJobs/* | Back-uptaken maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupJobsExport/actie | Taken exporteren |
> | Micro soft. Recovery Services/kluizen/backupManagementMetaData/* | Meta gegevens met betrekking tot back-upbeheer maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupPolicies/* | Back-upbeleid maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Micro soft. Recovery Services/kluizen/backupProtectedItems/* | Back-upitems maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupProtectionContainers/* | Containers maken en beheren met back-upitems |
> | Micro soft. Recovery Services/kluizen/backupSecurityPIN/* |  |
> | Micro soft. Recovery Services/kluizen/backupUsageSummaries/lezen | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Micro soft. Recovery Services/kluizen/certificaten/* | Certificaten maken en beheren die zijn gerelateerd aan back-ups in Recovery Services kluis |
> | Micro soft. Recovery Services/kluizen/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/lezen | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Micro soft. Recovery Services/kluizen/monitoringConfigurations/* |  |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | Micro soft. Recovery Services/kluizen/gebruik/* | Gebruik van Recovery Services kluis maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. Recovery Services/kluizen/backupstorageconfig/* |  |
> | Micro soft. Recovery Services/kluizen/backupconfig/* |  |
> | Micro soft. Recovery Services/kluizen/backupValidateOperation/actie | Bewerking voor beveiligd item valideren |
> | Micro soft. Recovery Services/kluizen/schrijven | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | Micro soft. Recovery Services/kluizen/backupOperations/lezen | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupEngines/lezen | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/* |  |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectableContainers/lezen | Alle Beveilig bare containers ophalen |
> | Micro soft. Recovery Services/locaties/backupStatus/actie | De back-upstatus voor Recovery Services kluizen controleren |
> | Micro soft. Recovery Services/locaties/backupPreValidateProtection/actie |  |
> | Micro soft. Recovery Services/locaties/backupValidateFeatures/actie | Functies valideren |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Micro soft. Recovery Services/Operations/lezen | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Micro soft. Recovery Services/locaties/operationStatus/lezen | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Micro soft. Recovery Services/kluizen/backupProtectionIntents/lezen | Alle intenties van de back-upbeveiliging weer geven |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="backup-operator"></a>Back-upoperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Recovery Services/kluizen/backupFabrics/operationResults/lezen | Hiermee wordt de status van de bewerking geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/operationResults/lezen | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/backup/Action | Maakt een back-up voor het beveiligde item. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/operationResults/lezen | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/operationsStatus/lezen | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/lezen | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Direct-item herstel inrichten voor beveiligd item |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/lezen | Herstel punten voor beveiligde items ophalen. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Herstel punten voor beveiligde items herstellen. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Herstel van onmiddellijke items intrekken voor beveiligd item |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/lezen | Hiermee worden alle geregistreerde containers geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupFabrics/refreshContainers/actie | Hiermee vernieuwt u de container lijst |
> | Micro soft. Recovery Services/kluizen/backupJobs/* | Back-uptaken maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupJobsExport/actie | Taken exporteren |
> | Micro soft. Recovery Services/kluizen/backupManagementMetaData/lezen |  |
> | Micro soft. Recovery Services/kluizen/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | Micro soft. Recovery Services/kluizen/backupPolicies/operationResults/lezen | Resultaten van beleids bewerking ophalen. |
> | Micro soft. Recovery Services/kluizen/backupPolicies/lezen | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Micro soft. Recovery Services/kluizen/backupProtectedItems/lezen | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupProtectionContainers/lezen | Hiermee worden alle containers van het abonnement geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupUsageSummaries/lezen | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Micro soft. Recovery Services/kluizen/certificaten/schrijven | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Micro soft. Recovery Services/kluizen/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Micro soft. Recovery Services/kluizen/extendedInformation/schrijven | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/lezen | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Micro soft. Recovery Services/kluizen/monitoringConfigurations/* |  |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/operationResults/lezen | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/lezen | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/schrijven | De bewerking service container registreren kan worden gebruikt om een container te registreren bij de Recovery-service. |
> | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. Recovery Services/kluizen/backupstorageconfig/* |  |
> | Micro soft. Recovery Services/kluizen/backupValidateOperation/actie | Bewerking voor beveiligd item valideren |
> | Micro soft. Recovery Services/kluizen/backupOperations/lezen | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupPolicies/Operations/lezen | Status van beleids bewerking ophalen. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/schrijven | Hiermee maakt u een geregistreerde container |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/query/Action | Een query uitvoeren voor werk belastingen binnen een container |
> | Micro soft. Recovery Services/kluizen/backupEngines/lezen | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/schrijven | Een doel voor back-upbeveiliging maken |
> | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/lezen | Een doel voor back-upbeveiliging verkrijgen |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectableContainers/lezen | Alle Beveilig bare containers ophalen |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/items/lezen | Alle items in een container ophalen |
> | Micro soft. Recovery Services/locaties/backupStatus/actie | De back-upstatus voor Recovery Services kluizen controleren |
> | Micro soft. Recovery Services/locaties/backupPreValidateProtection/actie |  |
> | Micro soft. Recovery Services/locaties/backupValidateFeatures/actie | Functies valideren |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Micro soft. Recovery Services/Operations/lezen | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Micro soft. Recovery Services/locaties/operationStatus/lezen | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Micro soft. Recovery Services/kluizen/backupProtectionIntents/lezen | Alle intenties van de back-upbeveiliging weer geven |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="backup-reader"></a>Back-uplezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan back-upservices weer geven, maar kan geen wijzigingen aanbrengen |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Recovery Services/locaties/allocatedStamp/lezen | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Micro soft. Recovery Services/kluizen/backupFabrics/operationResults/lezen | Hiermee wordt de status van de bewerking geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/operationResults/lezen | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/operationResults/lezen | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/operationsStatus/lezen | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/lezen | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/lezen | Herstel punten voor beveiligde items ophalen. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/lezen | Hiermee worden alle geregistreerde containers geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupJobs/operationResults/lezen | Hiermee wordt het resultaat van de taak bewerking geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupJobs/lezen | Hiermee worden alle taak objecten geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupJobsExport/actie | Taken exporteren |
> | Micro soft. Recovery Services/kluizen/backupManagementMetaData/lezen |  |
> | Micro soft. Recovery Services/kluizen/backupOperationResults/lezen | Retourneert een back-upbewerkings resultaat voor Recovery Services kluis. |
> | Micro soft. Recovery Services/kluizen/backupPolicies/operationResults/lezen | Resultaten van beleids bewerking ophalen. |
> | Micro soft. Recovery Services/kluizen/backupPolicies/lezen | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupProtectedItems/lezen | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupProtectionContainers/lezen | Hiermee worden alle containers van het abonnement geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupUsageSummaries/lezen | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Micro soft. Recovery Services/kluizen/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/lezen | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/operationResults/lezen | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/lezen | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Micro soft. Recovery Services/kluizen/backupstorageconfig/lezen | Hiermee wordt de opslag configuratie voor Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupconfig/lezen | Hiermee wordt de configuratie voor Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupOperations/lezen | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/backupPolicies/Operations/lezen | Status van beleids bewerking ophalen. |
> | Micro soft. Recovery Services/kluizen/backupEngines/lezen | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/lezen | Een doel voor back-upbeveiliging verkrijgen |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/items/lezen | Alle items in een container ophalen |
> | Micro soft. Recovery Services/locaties/backupStatus/actie | De back-upstatus voor Recovery Services kluizen controleren |
> | Micro soft. Recovery Services/kluizen/monitoringConfigurations/* |  |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Micro soft. Recovery Services/Operations/lezen | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Micro soft. Recovery Services/locaties/operationStatus/lezen | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Micro soft. Recovery Services/kluizen/backupProtectionIntents/lezen | Alle intenties van de back-upbeveiliging weer geven |
> | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="billing-reader"></a>Lezer voor facturering
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee staat u lees toegang tot facturerings gegevens toe |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. billing/*/Read | Facturerings gegevens lezen |
> | Micro soft. commerce/*/Read |  |
> | Micro soft. verbruik/*/Read |  |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Micro soft. CostManagement/*/Read |  |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="biztalk-contributor"></a>BizTalk-bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. BizTalkServices/BizTalk/* | BizTalk Services maken en beheren |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="blockchain-member-node-access-preview"></a>Toegang tot Block Chain-leden knooppunt (preview-versie)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt toegang tot Block Chain-leden knooppunten toegestaan |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Acties** |  |
> | Micro soft. Block Chain/blockchainMembers/transactionNodes/lezen | Hiermee wordt een lijst met bestaande Block Chain-leden transactie knooppunten opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Block Chain/blockchainMembers/transactionNodes/Connect/Action | Maakt verbinding met een trans actie-knoop punt van het block Chain-lid. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="blueprint-contributor"></a>Blauw druk bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan blauw drukken-definities beheren, maar niet toewijzen. |
> | **Id** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. blauw druk/blauw drukken/* | Maak en beheer blauw drukken-definities of blauw drukken-artefacten. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="blueprint-operator"></a>Blauw druk-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan bestaande gepubliceerde blauw drukken toewijzen, maar kan geen nieuwe blauw drukken maken. Opmerking: dit werkt alleen als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. |
> | **Id** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. blauw druk/blueprintAssignments/* | Maak maken en beheren van blauw druk-toewijzingen. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-endpoint-contributor"></a>Inzender voor CDN-eind punten
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. CDN/edgenodes/lezen |  |
> | Micro soft. CDN/operationresults/* |  |
> | Micro soft. CDN/profielen/eind punten/* |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-endpoint-reader"></a>CDN-eindpunt lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. CDN/edgenodes/lezen |  |
> | Micro soft. CDN/operationresults/* |  |
> | Micro soft. CDN/profielen/eind punten/*/Read |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-profile-contributor"></a>Inzender voor CDN-profiel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-profielen en de bijbehorende eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. CDN/edgenodes/lezen |  |
> | Micro soft. CDN/operationresults/* |  |
> | Micro soft. CDN/profielen/* |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-profile-reader"></a>CDN-profiel lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. CDN/edgenodes/lezen |  |
> | Micro soft. CDN/operationresults/* |  |
> | Micro soft. CDN/profielen/*/Read |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="classic-network-contributor"></a>Inzender voor klassieke netwerken
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u klassieke netwerken, maar hebt u geen toegang tot de netwerk bestanden. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. ClassicNetwork/* | Klassieke netwerken maken en beheren |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="classic-storage-account-contributor"></a>Inzender voor klassieke opslag accounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. ClassicStorage/Storage accounts/* | Opslagaccounts maken en beheren |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Service functie voor de sleutel operator voor klassieke opslag accounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | De sleutel operators voor klassieke opslag accounts zijn toegestaan om sleutels weer te geven en opnieuw te genereren voor klassieke opslag. |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Acties** |  |
> | Micro soft. ClassicStorage/Storage accounts/listkeys ophalen/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Micro soft. ClassicStorage/Storage accounts/regeneratekey/Action | Hiermee worden de bestaande toegangs sleutels voor het opslag account opnieuw gegenereerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="classic-virtual-machine-contributor"></a>Inzender voor klassieke virtuele machines
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u klassieke virtuele machines, maar kunt u niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. ClassicCompute/domainName/* | Klassieke Compute-domein namen maken en beheren |
> | Micro soft. ClassicCompute/informatie/* | Virtuele machines maken en beheren |
> | Micro soft. ClassicNetwork/networkSecurityGroups/deelname/actie |  |
> | Micro soft. ClassicNetwork/reservedIps/link/actie | Een gereserveerd IP-adres koppelen |
> | Micro soft. ClassicNetwork/reservedIps/lezen | Hiermee worden de gereserveerde Ip's opgehaald |
> | Micro soft. ClassicNetwork/virtualNetworks/deelname/actie | Voegt het virtuele netwerk samen. |
> | Micro soft. ClassicNetwork/virtualNetworks/lezen | Het virtuele netwerk ophalen. |
> | Micro soft. ClassicStorage/Storage accounts/schijven/lezen | Retourneert de schijf van het opslag account. |
> | Micro soft. ClassicStorage/Storage accounts/images/lezen | Hiermee wordt de installatie kopie van het opslag account geretourneerd. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | Micro soft. ClassicStorage/Storage accounts/Listkeys ophalen/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Micro soft. ClassicStorage/Storage accounts/lezen | Retour neer het opslag account met het opgegeven account. |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cognitive-services-contributor"></a>Inzender Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. CognitiveServices/* |  |
> | Micro soft. features/onderdelen/lezen | Hiermee haalt u de functies van een abonnement op. |
> | Micro soft. features/providers/onderdelen/lezen | Hiermee wordt de functie van een abonnement in een bepaalde resource provider opgehaald. |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Micro soft. Insights/logDefinitions/lezen | Logboek definities lezen |
> | Micro soft. Insights/metricdefinitions/lezen | Metrische definities lezen |
> | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Micro soft. resources/abonnementen/operationresults/lezen | De resultaten van de abonnements bewerking ophalen. |
> | Micro soft. resources/abonnementen/lezen | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Micro soft. resources/abonnementen/ResourceGroups/implementaties/* |  |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services gegevens lezer (preview-versie)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u Cognitive Services gegevens lezen. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. CognitiveServices/*/Read |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cognitive-services-user"></a>Cognitive Services gebruiker
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Acties** |  |
> | Micro soft. CognitiveServices/*/Read |  |
> | Micro soft. CognitiveServices/accounts/listkeys ophalen/actie | Lijst met sleutels |
> | Micro soft. Insights/alertRules/lezen | Een klassieke waarschuwing voor metrische gegevens lezen |
> | Micro soft. Insights/diagnosticSettings/lezen | Een diagnostische instelling voor bronnen lezen |
> | Micro soft. Insights/logDefinitions/lezen | Logboek definities lezen |
> | Micro soft. Insights/metricdefinitions/lezen | Metrische definities lezen |
> | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Micro soft. resources/abonnementen/operationresults/lezen | De resultaten van de abonnements bewerking ophalen. |
> | Micro soft. resources/abonnementen/lezen | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cosmos-db-account-reader-role"></a>Rol van Cosmos DB-account lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Bij het lezen van functies en roltoewijzingen kunnen machtigingen worden gelezen die aan elke gebruiker worden verleend |
> | Micro soft. DocumentDB/*/Read | Een verzameling lezen |
> | Micro soft. DocumentDB/databaseAccounts/readonlykeys/Action | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Micro soft. Insights/MetricDefinitions/lezen | Metrische definities lezen |
> | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang tot gegevens. Hiermee voor komt u toegang tot account sleutels en verbindings reeksen. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Acties** |  |
> | Micro soft. DocumentDb/databaseAccounts/* |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Micro soft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Micro soft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Micro soft. DocumentDB/databaseAccounts/Listkeys ophalen/* |  |
> | Micro soft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan een terugzet aanvraag indienen voor een Cosmos DB-Data Base of een container voor een account |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Acties** |  |
> | Micro soft. DocumentDB/databaseAccounts/backup/Action | Een aanvraag indienen voor het configureren van de back-up |
> | Micro soft. DocumentDB/databaseAccounts/Restore/Action | Een herstel aanvraag verzenden |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cost-management-contributor"></a>Inzender Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan kosten bekijken en kosten configuratie beheren (bijvoorbeeld budgetten, exports) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Acties** |  |
> | Micro soft. verbruik/* |  |
> | Micro soft. CostManagement/* |  |
> | Micro soft. facturering/billingPeriods/lezen |  |
> | Micro soft. resources/abonnementen/lezen | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Advisor/configuraties/lezen | Configuraties ophalen |
> | Micro soft. Advisor/aanbevelingen/lezen | Aanbevelingen voor lezen |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cost-management-reader"></a>Cost Management lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exports) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Acties** |  |
> | Micro soft. verbruik/*/Read |  |
> | Micro soft. CostManagement/*/Read |  |
> | Micro soft. facturering/billingPeriods/lezen |  |
> | Micro soft. resources/abonnementen/lezen | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Advisor/configuraties/lezen | Configuraties ophalen |
> | Micro soft. Advisor/aanbevelingen/lezen | Aanbevelingen voor lezen |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-box-contributor"></a>Inzender Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u alles beheren onder Data Box Service, behalve dat anderen toegang verlenen. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Databox/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-box-reader"></a>Data Box lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Databox/*/Read |  |
> | Micro soft. Databox/Jobs/listsecrets/actie |  |
> | Micro soft. Databox/Jobs/listcredentials/actie | Geeft een lijst van de niet-versleutelde referenties die zijn gerelateerd aan de order. |
> | Micro soft. Databox/locaties/availableSkus/actie | Met deze methode wordt de lijst met beschik bare sku's geretourneerd. |
> | Micro soft. Databox/locaties/validateAddress/actie | Valideert het verzend adres en levert eventueel alternatieve adressen. |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-factory-contributor"></a>Inzender Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Het maken en beheren van gegevens fabrieken, evenals onderliggende resources. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. DataFactory/dataFactories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | Micro soft. DataFactory/fabrieken/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-ontwikkelaar
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen. |
> | **Id** | 47b7735b-770E-4598-A7DA-8b91488b4c88 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. BigAnalytics/accounts/* |  |
> | Micro soft. DataLakeAnalytics/accounts/* |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Micro soft. BigAnalytics/accounts/verwijderen |  |
> | Micro soft. BigAnalytics/accounts/TakeOwnership/actie |  |
> | Micro soft. BigAnalytics/accounts/schrijven |  |
> | Micro soft. DataLakeAnalytics/accounts/verwijderen | Een DataLakeAnalytics-account verwijderen. |
> | Micro soft. DataLakeAnalytics/accounts/TakeOwnership/actie | Machtigingen verlenen om taken te annuleren die door andere gebruikers zijn verzonden. |
> | Micro soft. DataLakeAnalytics/accounts/schrijven | Een DataLakeAnalytics-account maken of bijwerken. |
> | Micro soft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/schrijven | Een gekoppeld data Lake Store-account maken of bijwerken van een DataLakeAnalytics-account. |
> | Micro soft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/verwijderen | Een Data Lake Store-account ontkoppelen van een DataLakeAnalytics-account. |
> | Micro soft. DataLakeAnalytics/accounts/Storage accounts/schrijven | Een gekoppeld opslag account maken of bijwerken van een DataLakeAnalytics-account. |
> | Micro soft. DataLakeAnalytics/accounts/Storage accounts/verwijderen | Een opslag account ontkoppelen van een DataLakeAnalytics-account. |
> | Micro soft. DataLakeAnalytics/accounts/firewallRules/schrijven | Een firewall regel maken of bijwerken. |
> | Micro soft. DataLakeAnalytics/accounts/firewallRules/verwijderen | Een firewall regel verwijderen. |
> | Micro soft. DataLakeAnalytics/accounts/computePolicies/schrijven | Een reken beleid maken of bijwerken. |
> | Micro soft. DataLakeAnalytics/accounts/computePolicies/verwijderen | Een reken beleid verwijderen. |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-purger"></a>Gegevens opschoner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan Analytics-gegevens verwijderen |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Acties** |  |
> | Micro soft. Insights/onderdelen/*/Read |  |
> | Micro soft. Insights/onderdelen/opschonen/actie | Gegevens uit Application Insights verwijderen |
> | Micro soft. OperationalInsights/werk ruimten/*/Read |  |
> | Micro soft. OperationalInsights/werk ruimten/leegmaken/actie | Opgegeven gegevens uit de werk ruimte verwijderen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="devtest-labs-user"></a>DevTest Labs-gebruiker
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u verbinding maken met uw virtuele machines in uw Azure DevTest Labs, deze starten, opnieuw opstarten en afsluiten. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Compute/Availability sets/lezen | De eigenschappen van een beschikbaarheidsset ophalen |
> | Micro soft. Compute/informatie/*/Read | De eigenschappen van een virtuele machine lezen (VM-grootte, runtime status, VM-extensies, enzovoort) |
> | Micro soft. Compute/informatie/disallocate/Action | Hiermee wordt de virtuele machine uitgeschakeld en worden de reken resources vrijgegeven |
> | Micro soft. Compute/informatie/lezen | De eigenschappen van een virtuele machine ophalen |
> | Micro soft. Compute/informatie/restart/Action | Hiermee wordt de virtuele machine opnieuw opgestart |
> | Micro soft. Compute/informatie/start/Action | De virtuele machine wordt gestart |
> | Micro soft. DevTestLab/*/Read | De eigenschappen van een Lab lezen |
> | Micro soft. DevTestLab/Labs/claimAnyVm/actie | Claim een wille keurig claim bare virtuele machine in het lab. |
> | Micro soft. DevTestLab/Labs/createEnvironment/actie | Maak virtuele machines in een lab. |
> | Micro soft. DevTestLab/Labs/ensureCurrentUserProfile/actie | Zorg ervoor dat de huidige gebruiker een geldig profiel in het lab heeft. |
> | Micro soft. DevTestLab/Labs/formules/verwijderen | Formules verwijderen. |
> | Micro soft. DevTestLab/Labs/formules/lezen | Formules lezen. |
> | Micro soft. DevTestLab/Labs/formules/schrijven | Formules toevoegen of wijzigen. |
> | Micro soft. DevTestLab/Labs/policySets/evaluatePolicies/actie | Evalueert het lab-beleid. |
> | Micro soft. DevTestLab/Labs/informatie/claim/actie | Eigenaar worden van een bestaande virtuele machine |
> | Micro soft. DevTestLab/Labs/informatie/listApplicableSchedules/actie | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | Micro soft. DevTestLab/Labs/informatie/getRdpFileContents/actie | Hiermee wordt een teken reeks opgehaald waarmee de inhoud van het RDP-bestand voor de virtuele machine wordt aangeduid |
> | Micro soft. Network/loadBalancers/backendAddressPools/samen voegen/actie | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | Micro soft. Network/loadBalancers/inboundNatRules/samen voegen/actie | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | Micro soft. Network/networkInterfaces/*/Read | De eigenschappen van een netwerk interface lezen (bijvoorbeeld alle load balancers waarvan de netwerk interface deel uitmaakt) |
> | Micro soft. Network/networkInterfaces/samen voegen/actie | Voegt een virtuele machine toe aan een netwerk interface. Niet Alertable. |
> | Micro soft. Network/networkInterfaces/lezen | Hiermee haalt u een definitie van een netwerk interface.  |
> | Micro soft. Network/networkInterfaces/schrijven | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | Micro soft. Network/publicIPAddresses/*/Read | De eigenschappen van een openbaar IP-adres lezen |
> | Micro soft. Network/publicIPAddresses/samen voegen/actie | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | Micro soft. Network/publicIPAddresses/lezen | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Micro soft. Network/virtualNetworks/subnetten/toevoegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/lezen | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/Listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | **Intact** |  |
> | Micro soft. Compute/informatie/toegestane VM/lezen | Een lijst met beschik bare grootten waarmee de virtuele machine kan worden bijgewerkt |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="dns-zone-contributor"></a>Inzender DNS-zone
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. Network/dnsZones/* | DNS-zones en-records maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="documentdb-account-contributor"></a>Inzender voor DocumentDB-accounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. DocumentDb/databaseAccounts/* | Azure Cosmos DB accounts maken en beheren |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. EventGrid/eventSubscriptions/* |  |
> | Micro soft. EventGrid/topicTypes/eventSubscriptions/lezen | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | Micro soft. EventGrid/locaties/eventSubscriptions/lezen | Regionale gebeurtenis abonnementen weer geven |
> | Micro soft. EventGrid/locaties/topicTypes/eventSubscriptions/lezen | Regionale gebeurtenis abonnementen weer geven op topictype |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. EventGrid/eventSubscriptions/lezen | Een eventSubscription lezen |
> | Micro soft. EventGrid/topicTypes/eventSubscriptions/lezen | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | Micro soft. EventGrid/locaties/eventSubscriptions/lezen | Regionale gebeurtenis abonnementen weer geven |
> | Micro soft. EventGrid/locaties/topicTypes/eventSubscriptions/lezen | Regionale gebeurtenis abonnementen weer geven op topictype |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight-cluster operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u HDInsight-cluster configuraties lezen en wijzigen. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Acties** |  |
> | Micro soft. HDInsight/*/Read |  |
> | Micro soft. HDInsight/clusters/getGatewaySettings/actie | Gateway-instellingen voor HDInsight-cluster ophalen |
> | Micro soft. HDInsight/clusters/updateGatewaySettings/actie | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | Micro soft. HDInsight/clusters/configuraties/* |  |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="hdinsight-domain-services-contributor"></a>Inzender voor HDInsight Domain Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan gerelateerde bewerkingen die betrekking hebben op domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Acties** |  |
> | Micro soft. AAD/*/Read |  |
> | Micro soft. AAD/domainServices/*/Read |  |
> | Micro soft. AAD/domainServices/oucontainer/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="intelligent-systems-account-contributor"></a>Inzender voor Intelligent Systems-account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. IntelligentSystems/accounts/* | Intelligente systeem accounts maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="key-vault-contributor"></a>Inzender Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u sleutel kluizen beheren, maar niet de toegang tot de kluis. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. de sleutel kluis/* |  |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Micro soft. sleutel kluis/locaties/deletedVaults/opschonen/actie | Een voorlopig verwijderde sleutel kluis leegmaken |
> | Micro soft. hsmPools/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="lab-creator"></a>Lab-Maker
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u uw beheerde Labs maken, beheren en verwijderen in uw Azure Lab-accounts. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. LabServices/labAccounts/*/Read |  |
> | Micro soft. LabServices/labAccounts/createLab/Action | Een lab maken in een Lab-account. |
> | Micro soft. LabServices/labAccounts/sizes/getRegionalAvailability/Action |  |
> | Micro soft. LabServices/labAccounts/getRegionalAvailability/Action | Informatie over regionale Beschik baarheid ophalen voor elke grootte categorie die is geconfigureerd met een Lab-account |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="log-analytics-contributor"></a>Inzender van Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. Automation/automationAccounts/* |  |
> | Micro soft. ClassicCompute/informatie/Extensions/* |  |
> | Micro soft. ClassicStorage/Storage accounts/Listkeys ophalen/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Micro soft. Compute/informatie/Extensions/* |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Micro soft. OperationalInsights/* |  |
> | Micro soft. OperationsManagement/* |  |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/ResourceGroups/implementaties/* |  |
> | Micro soft. Storage/Storage accounts/Listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="log-analytics-reader"></a>Lezer van Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. OperationalInsights/werk ruimten/Analytics/query/actie | Zoek met een nieuwe engine. |
> | Micro soft. OperationalInsights/werk ruimten/zoeken/actie | Hiermee wordt een zoek query uitgevoerd |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Micro soft. OperationalInsights/werk ruimten/sharedKeys/lezen | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="logic-app-contributor"></a>Inzender voor logische apps
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u logische apps beheren, maar niet wijzigen. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. ClassicStorage/Storage accounts/Listkeys ophalen/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Micro soft. ClassicStorage/Storage accounts/lezen | Retour neer het opslag account met het opgegeven account. |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/metricAlerts/* |  |
> | Micro soft. Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Micro soft. Insights/logdefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | Micro soft. Insights/metricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | Micro soft. Logic/* | Beheert Logic Apps-resources. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/operationresults/lezen | De resultaten van de abonnements bewerking ophalen. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Web/connectionGateways/* | Een verbindings gateway maken en beheren. |
> | Micro soft. Web/verbindingen/* | Een verbinding maken en beheren. |
> | Micro soft. Web/customApis/* | Maakt en beheert een aangepaste API. |
> | Micro soft. web/server farms/samen voegen/actie |  |
> | Micro soft. web/server farms/lezen | De eigenschappen van een App Service plan ophalen |
> | Micro soft. web/sites/functies/listSecrets/actie | Lijst met functie geheimen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="logic-app-operator"></a>Logische app-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/*/Read | Inzichten-waarschuwings regels lezen |
> | Micro soft. Insights/metricAlerts/*/Read |  |
> | Micro soft. Insights/diagnosticSettings/*/Read | Hiermee worden Diagnostische instellingen voor Logic Apps opgehaald |
> | Micro soft. Insights/metricDefinitions/*/Read | Hiermee worden de beschik bare metrische gegevens opgehaald voor Logic Apps. |
> | Micro soft. Logic/*/Read | Hiermee worden Logic Apps resources gelezen. |
> | Micro soft. Logic/werk stromen/uitschakelen/actie | Hiermee schakelt u de werk stroom uit. |
> | Micro soft. Logic/werk stromen/inschakelen/actie | Hiermee wordt de werk stroom ingeschakeld. |
> | Micro soft. Logic/werk stromen/valideren/actie | Valideert de werk stroom. |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Micro soft. resources/abonnementen/operationresults/lezen | De resultaten van de abonnements bewerking ophalen. |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Web/connectionGateways/*/Read | Lees de verbindings gateways. |
> | Micro soft. Web/Connections/*/Read | Lees verbindingen. |
> | Micro soft. Web/customApis/*/Read | Lees de aangepaste API. |
> | Micro soft. web/server farms/lezen | De eigenschappen van een App Service plan ophalen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-application-operator-role"></a>Rol beheerde toepassings operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. Solutions/toepassingen/lezen | Hiermee haalt u een lijst met toepassingen op. |
> | Micro soft. Solutions/*/Action |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-applications-reader"></a>Lezer voor beheerde toepassingen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. Solutions/jitRequests/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-identity-contributor"></a>Inzender beheerde identiteit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Aan de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Acties** |  |
> | Micro soft. ManagedIdentity/userAssignedIdentities/lezen | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit opgehaald |
> | Micro soft. ManagedIdentity/userAssignedIdentities/schrijven | Hiermee wordt een nieuwe door de gebruiker toegewezen identiteit gemaakt of worden de labels bijgewerkt die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |
> | Micro soft. ManagedIdentity/userAssignedIdentities/verwijderen | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit verwijderd |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-identity-operator"></a>Beheerde identiteits operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Door gebruiker toegewezen identiteit lezen en toewijzen |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Acties** |  |
> | Micro soft. ManagedIdentity/userAssignedIdentities/*/Read |  |
> | Micro soft. ManagedIdentity/userAssignedIdentities/*/Assign/Action |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-services-registration-assignment-delete-role"></a>Rol voor registratie toewijzing beheerde services verwijderen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met de functie voor registratie toewijzing van beheerde services verwijderen kan de Tenant gebruikers beheren de registratie toewijzing verwijderen die aan de Tenant is toegewezen. |
> | **Id** | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | **Acties** |  |
> | Micro soft. ManagedServices/registrationAssignments/lezen | Hiermee wordt een lijst met beheerde services registratie toewijzingen opgehaald. |
> | Micro soft. ManagedServices/registrationAssignments/verwijderen | Hiermee wordt de registratie toewijzing van beheerde services verwijderd. |
> | Micro soft. ManagedServices/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="management-group-contributor"></a>Inzender beheer groep
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Rol Inzender beheer groep |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Acties** |  |
> | Micro soft. Management/managementGroups/verwijderen | Beheer groep verwijderen. |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Micro soft. Management/managementGroups/abonnementen/verwijderen | Het abonnement van de beheer groep wordt ontkoppeld. |
> | Micro soft. Management/managementGroups/abonnementen/schrijven | Het bestaande abonnement wordt gekoppeld aan de beheer groep. |
> | Micro soft. Management/managementGroups/schrijven | Een beheer groep maken of bijwerken. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="management-group-reader"></a>Lezer beheer groep
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Rol van lezer van beheer groep |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Acties** |  |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="monitoring-contributor"></a>Inzender bewaken
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. AlertsManagement/Alerts/* |  |
> | Micro soft. AlertsManagement/alertsSummary/* |  |
> | Micro soft. Insights/actiongroups/* |  |
> | Micro soft. Insights/activityLogAlerts/* |  |
> | Micro soft. Insights/AlertRules/* | Waarschuwings regels lezen/schrijven/verwijderen. |
> | Micro soft. Insights/onderdelen/* | Application Insights onderdelen lezen/schrijven/verwijderen. |
> | Micro soft. Insights/DiagnosticSettings/* | Diagnostische instellingen lezen/schrijven/verwijderen. |
> | Micro soft. Insights/eventtypes/* | Lijst activiteiten logboek gebeurtenissen (beheer gebeurtenissen) in een abonnement. Deze machtiging is van toepassing op zowel toegang via het programma als de portal tot het activiteiten logboek. |
> | Micro soft. Insights/LogDefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | Micro soft. Insights/metricalerts/* |  |
> | Micro soft. Insights/MetricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | Micro soft. Insights/metrische gegevens/* | Meet gegevens voor een resource lezen. |
> | Micro soft. Insights/registreren/actie | De micro soft Insights-provider registreren |
> | Micro soft. Insights/scheduledqueryrules/* |  |
> | Micro soft. Insights/webtests/* | Application Insights webtests lezen/schrijven/verwijderen. |
> | Micro soft. Insights/werkmappen/* |  |
> | Micro soft. OperationalInsights/werk ruimten/Intelligence packs/* | Log Analytics-oplossings pakketten lezen/schrijven/verwijderen. |
> | Micro soft. OperationalInsights/werk ruimten/savedSearches/* | In log Analytics opgeslagen Zoek opdrachten lezen/schrijven/verwijderen. |
> | Micro soft. OperationalInsights/werk ruimten/zoeken/actie | Hiermee wordt een zoek query uitgevoerd |
> | Micro soft. OperationalInsights/werk ruimten/sharedKeys/actie | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Micro soft. OperationalInsights/werk ruimten/storageinsightconfigs/* | Opslag inzicht configuraties voor log Analytics lezen/schrijven/verwijderen. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. WorkloadMonitor/monitors/* |  |
> | Micro soft. WorkloadMonitor/notificationSettings/* |  |
> | Micro soft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="monitoring-metrics-publisher"></a>De uitgever van metrische gegevens controleren
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Acties** |  |
> | Micro soft. Insights/registreren/actie | De micro soft Insights-provider registreren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Insights/metrische gegevens/schrijven | Metrische gegevens schrijven |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="monitoring-reader"></a>Bewakings lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. OperationalInsights/werk ruimten/zoeken/actie | Hiermee wordt een zoek query uitgevoerd |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="network-contributor"></a>Inzender voor netwerken
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u netwerken beheren, maar niet de toegang tot de netwerk bestanden. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. Network/* | Netwerken maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="new-relic-apm-account-contributor"></a>Nieuwe Inzender voor Relic APM-account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | NewRelic. APM/accounts/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="reader-and-data-access"></a>Lezer en gegevens toegang
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/Listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Micro soft. Storage/Storage accounts/ListAccountSas/Action | Hiermee wordt het SAS-token van het account voor het opgegeven opslag account geretourneerd. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="redis-cache-contributor"></a>Inzender Redis Cache
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. cache/redis/* | Redis-caches maken en beheren |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="resource-policy-contributor-preview"></a>Inzender voor resource beleid (preview-versie)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Evaluatie Alsnog gebruikers van EA, met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. Authorization/policyassignments/* | Beleids toewijzingen maken en beheren |
> | Micro soft. Authorization/policydefinitions/* | Beleids definities maken en beheren |
> | Micro soft. Authorization/policysetdefinitions/* | Beleids sets maken en beheren |
> | Micro soft. PolicyInsights/* |  |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="scheduler-job-collections-contributor"></a>Inzender voor scheduler-taak verzamelingen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. scheduler/jobcollections/* | Taak verzamelingen maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="search-service-contributor"></a>Inzender Search Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u zoek services beheren, maar niet de toegang tot ze. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Search/searchServices/* | Zoek Services maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="security-admin"></a>Beveiligingsbeheerder
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | In Security Center: kan beveiligings beleid weer geven, beveiligings status weer geven, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Authorization/policyAssignments/* | Beleids toewijzingen maken en beheren |
> | Micro soft. Authorization/policyDefinitions/* | Beleids definities maken en beheren |
> | Micro soft. Authorization/policySetDefinitions/* | Beleids sets maken en beheren |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Micro soft. operationalInsights/werk ruimten/*/Read | Log Analytics-gegevens weer geven |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Security/* |  |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="security-manager-legacy"></a>Beveiligings beheer (verouderd)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. ClassicCompute/*/Read | Configuratie-informatie voor klassieke virtuele machines lezen |
> | Micro soft. ClassicCompute/informatie/*/write | Configuratie voor klassieke virtuele machines schrijven |
> | Micro soft. ClassicNetwork/*/Read | Configuratie-informatie over klassiek netwerk lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Security/* | Beveiligings onderdelen en-beleid maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="security-reader"></a>Beveiligings lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | In Security Center: kunt u aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar geen wijzigingen aanbrengen |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. operationalInsights/werk ruimten/*/Read | Log Analytics-gegevens weer geven |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Security/*/Read | Beveiligings onderdelen en-beleid lezen |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="site-recovery-contributor"></a>Inzender Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Recovery Services/locaties/allocatedStamp/lezen | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Micro soft. Recovery Services/locaties/Allo Cate Stamp/actie | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Micro soft. Recovery Services/kluizen/certificaten/schrijven | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Micro soft. Recovery Services/kluizen/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/refreshContainers/lezen |  |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | Micro soft. Recovery Services/kluizen/replicationAlertSettings/* | Waarschuwings instellingen voor replicatie maken of bijwerken |
> | Micro soft. Recovery Services/kluizen/replicationEvents/lezen | Gebeurtenissen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/* | Replicatie-fabrics maken en beheren |
> | Micro soft. Recovery Services/kluizen/replicationJobs/* | Replicatie taken maken en beheren |
> | Micro soft. Recovery Services/kluizen/replicationPolicies/* | Replicatie beleid maken en beheren |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/* | Herstel plannen maken en beheren |
> | Micro soft. Recovery Services/kluizen/storageConfig/* | Opslag configuratie van Recovery Services kluis maken en beheren |
> | Micro soft. Recovery Services/kluizen/tokenInfo/lezen |  |
> | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/vaultTokens/lezen | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | Micro soft. Recovery Services/kluizen/monitoringConfigurations/notificationConfiguration/lezen |  |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="site-recovery-operator"></a>Site Recovery-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Een failover en failback, maar geen andere Site Recovery beheer bewerkingen uitvoeren |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Recovery Services/locaties/allocatedStamp/lezen | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Micro soft. Recovery Services/locaties/Allo Cate Stamp/actie | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Micro soft. Recovery Services/kluizen/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/refreshContainers/lezen |  |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/operationResults/lezen | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/lezen | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Micro soft. Recovery Services/kluizen/replicationAlertSettings/lezen | Waarschuwings instellingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationEvents/lezen | Gebeurtenissen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/checkConsistency/actie | Hiermee wordt de consistentie van de infra structuur gecontroleerd |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/lezen | Alle infra structuren lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/reassociateGateway/actie | Gateway opnieuw koppelen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/renewcertificate/actie | Certificaat voor Fabric vernieuwen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/lezen | Alle netwerken lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/replicationNetworkMappings/lezen | Netwerk toewijzingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/lezen | Beveiligings containers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/lezen | Beveilig bare items lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Herstel punt Toep assen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Failover door voeren |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Geplande failover |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/lezen | Alle beveiligde items lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/lezen | Alle replicatie herstel punten lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Replicatie herstellen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/opnieuw beveiligen/actie | Beveiligd item opnieuw beveiligen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/switchprotection/actie | Beveiligings container overschakelen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Failover testen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Failovertest opschonen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Mobility-service bijwerken |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/lezen | Alle beveiligings container toewijzingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/lezen | Recovery Services Providers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/actie | Provider vernieuwen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/lezen | Alle vCenter lezen |
> | Micro soft. Recovery Services/kluizen/replicationJobs/* | Replicatie taken maken en beheren |
> | Micro soft. Recovery Services/kluizen/replicationPolicies/lezen | Alle beleids regels lezen |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/failoverCommit/actie | Herstel plan voor failover door voeren |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/plannedFailover/actie | Herstel plan voor geplande failover |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/lezen | Herstel plannen lezen |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/opnieuw beveiligen/actie | Herstel plan opnieuw beveiligen |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/testFailover/actie | Herstel plan voor failover testen |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/testFailoverCleanup/actie | Herstel plan voor het opschonen van de Failovertest |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/unplannedFailover/actie | Herstel plan voor failover |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | Micro soft. Recovery Services/kluizen/monitoringConfigurations/notificationConfiguration/lezen |  |
> | Micro soft. Recovery Services/kluizen/storageConfig/lezen |  |
> | Micro soft. Recovery Services/kluizen/tokenInfo/lezen |  |
> | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/vaultTokens/lezen | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="site-recovery-reader"></a>Site Recovery lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Recovery Services/locaties/allocatedStamp/lezen | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Micro soft. Recovery Services/kluizen/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Micro soft. Recovery Services/kluizen/monitoringAlerts/lezen | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Micro soft. Recovery Services/kluizen/monitoringConfigurations/notificationConfiguration/lezen |  |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/refreshContainers/lezen |  |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/operationResults/lezen | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Micro soft. Recovery Services/kluizen/registeredIdentities/lezen | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Micro soft. Recovery Services/kluizen/replicationAlertSettings/lezen | Waarschuwings instellingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationEvents/lezen | Gebeurtenissen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/lezen | Alle infra structuren lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/lezen | Alle netwerken lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/replicationNetworkMappings/lezen | Netwerk toewijzingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/lezen | Beveiligings containers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/lezen | Beveilig bare items lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/lezen | Alle beveiligde items lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/lezen | Alle replicatie herstel punten lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/lezen | Alle beveiligings container toewijzingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/lezen | Recovery Services Providers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/lezen | Alle vCenter lezen |
> | Micro soft. Recovery Services/kluizen/replicationJobs/lezen | Alle taken lezen |
> | Micro soft. Recovery Services/kluizen/replicationPolicies/lezen | Alle beleids regels lezen |
> | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/lezen | Herstel plannen lezen |
> | Micro soft. Recovery Services/kluizen/storageConfig/lezen |  |
> | Micro soft. Recovery Services/kluizen/tokenInfo/lezen |  |
> | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/vaultTokens/lezen | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="spatial-anchors-account-contributor"></a>Inzender voor ruimtelijke ankers
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/Create/Action | Ruimtelijke ankers maken |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/detectie/lezen | Ruimtelijke beankeringen in de buurt detecteren |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/eigenschappen/lezen | Eigenschappen van ruimtelijke ankers ophalen |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/query/lezen | Ruimtelijke ankers zoeken |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/submitdiag/lezen | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/schrijven | Eigenschappen van ruimtelijke ankers bijwerken |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="spatial-anchors-account-owner"></a>Eigenaar van ruimtelijk ankers
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u ruimtelijke ankers in uw account beheren, met inbegrip van het verwijderen ervan |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/Create/Action | Ruimtelijke ankers maken |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/verwijderen | Ruimtelijke ankers verwijderen |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/detectie/lezen | Ruimtelijke beankeringen in de buurt detecteren |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/eigenschappen/lezen | Eigenschappen van ruimtelijke ankers ophalen |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/query/lezen | Ruimtelijke ankers zoeken |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/submitdiag/lezen | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/schrijven | Eigenschappen van ruimtelijke ankers bijwerken |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="spatial-anchors-account-reader"></a>Lezer van ruimtelijk ankers-account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/detectie/lezen | Ruimtelijke beankeringen in de buurt detecteren |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/eigenschappen/lezen | Eigenschappen van ruimtelijke ankers ophalen |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/query/lezen | Ruimtelijke ankers zoeken |
> | Micro soft. MixedReality/SpatialAnchorsAccounts/submitdiag/lezen | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-db-contributor"></a>Inzender voor SQL-data base
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/locaties/*/Read |  |
> | Micro soft. SQL/servers/data bases/* | SQL-data bases maken en beheren |
> | Micro soft. SQL/servers/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | Micro soft. SQL/managedInstances/data bases/currentSensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/schema's/Tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/data bases/sensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/servers/data bases/auditingPolicies/* | Controle beleid bewerken |
> | Micro soft. SQL/servers/data bases/auditingSettings/* | Controle-instellingen bewerken |
> | Micro soft. SQL/servers/data bases/auditRecords/lezen | De data base-BLOB-controle records ophalen |
> | Micro soft. SQL/servers/data bases/connectionPolicies/* | Verbindings beleid bewerken |
> | Micro soft. SQL/servers/data bases/currentSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/dataMaskingPolicies/* | Beleids regels voor gegevens maskering bewerken |
> | Micro soft. SQL/servers/data bases/extendedAuditingSettings/* |  |
> | Micro soft. SQL/servers/data bases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/schema's/Tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/securityAlertPolicies/* | Beveiligings waarschuwingen beleid bewerken |
> | Micro soft. SQL/servers/data bases/securityMetrics/* | Metrische beveiligings gegevens bewerken |
> | Micro soft. SQL/servers/data bases/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentScans/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentSettings/* |  |
> | Micro soft. SQL/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-managed-instance-contributor"></a>Inzender voor SQL Managed instance
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Acties** |  |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Network/networkSecurityGroups/* |  |
> | Micro soft. Network/routeTables/* |  |
> | Micro soft. SQL/locaties/*/Read |  |
> | Micro soft. SQL/managedInstances/* |  |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Network/virtualNetworks/subnets/* |  |
> | Micro soft. Network/virtualNetworks/* |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-security-manager"></a>SQL-beveiligings beheer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Micro soft-autorisatie lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. netwerk/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/managedInstances/data bases/currentSensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/schema's/Tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/data bases/sensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/data bases/transparentDataEncryption/* |  |
> | Micro soft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/servers/auditingPolicies/* | SQL Server-controle beleid maken en beheren |
> | Micro soft. SQL/servers/auditingSettings/* | SQL Server-controle-instelling maken en beheren |
> | Micro soft. SQL/servers/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide-server-BLOB-controle beleid dat op een bepaalde server is geconfigureerd |
> | Micro soft. SQL/servers/data bases/auditingPolicies/* | Controle beleid voor SQL server-data bases maken en beheren |
> | Micro soft. SQL/servers/data bases/auditingSettings/* | Controle-instellingen voor SQL Server-Data Base maken en beheren |
> | Micro soft. SQL/servers/data bases/auditRecords/lezen | Controle records lezen |
> | Micro soft. SQL/servers/data bases/connectionPolicies/* | SQL Server-database verbindings beleid maken en beheren |
> | Micro soft. SQL/servers/data bases/currentSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/dataMaskingPolicies/* | Gegevens maskerings beleid voor SQL server-data bases maken en beheren |
> | Micro soft. SQL/servers/data bases/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | Micro soft. SQL/servers/data bases/lezen | De lijst met data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Micro soft. SQL/servers/data bases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/schema's/lezen | Een database schema ophalen. |
> | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/lezen | Een database kolom ophalen. |
> | Micro soft. SQL/servers/data bases/schema's/Tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/schema's/tabellen/lezen | Een database tabel ophalen. |
> | Micro soft. SQL/servers/data bases/securityAlertPolicies/* | Beveiligings waarschuwingen beleid voor SQL Server-Data Base maken en beheren |
> | Micro soft. SQL/servers/data bases/securityMetrics/* | Metrische gegevens over beveiliging voor SQL Server-Data Base maken en beheren |
> | Micro soft. SQL/servers/data bases/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/transparentDataEncryption/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentScans/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentSettings/* |  |
> | Micro soft. SQL/servers/firewallRules/* |  |
> | Micro soft. SQL/servers/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Micro soft. SQL/servers/securityAlertPolicies/* | Beveiligings waarschuwingen voor SQL Server maken en beheren |
> | Micro soft. SQL/servers/vulnerabilityAssessments/* |  |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-server-contributor"></a>Inzender SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/locaties/*/Read |  |
> | Micro soft. SQL/servers/* | SQL-servers maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | Micro soft. SQL/managedInstances/data bases/currentSensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/schema's/Tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/data bases/sensitivityLabels/* |  |
> | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/servers/auditingPolicies/* | Controle beleid voor SQL server bewerken |
> | Micro soft. SQL/servers/auditingSettings/* | SQL Server-controle-instellingen bewerken |
> | Micro soft. SQL/servers/data bases/auditingPolicies/* | Controle beleid voor SQL Server-Data Base bewerken |
> | Micro soft. SQL/servers/data bases/auditingSettings/* | Controle-instellingen voor SQL Server-Data Base bewerken |
> | Micro soft. SQL/servers/data bases/auditRecords/lezen | Controle records lezen |
> | Micro soft. SQL/servers/data bases/connectionPolicies/* | SQL Server-database verbindings beleid bewerken |
> | Micro soft. SQL/servers/data bases/currentSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/dataMaskingPolicies/* | Beleids regels voor gegevens maskering van SQL Server-Data Base bewerken |
> | Micro soft. SQL/servers/data bases/extendedAuditingSettings/* |  |
> | Micro soft. SQL/servers/data bases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/schema's/Tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/securityAlertPolicies/* | Beleid voor beveiligings waarschuwingen van SQL Server-Data Base bewerken |
> | Micro soft. SQL/servers/data bases/securityMetrics/* | Metrische gegevens van beveiliging voor SQL Server-Data Base bewerken |
> | Micro soft. SQL/servers/data bases/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentScans/* |  |
> | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentSettings/* |  |
> | Micro soft. SQL/servers/extendedAuditingSettings/* |  |
> | Micro soft. SQL/servers/securityAlertPolicies/* | Beveiligings waarschuwingen voor SQL server bewerken |
> | Micro soft. SQL/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-account-contributor"></a>Inzender voor opslag accounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Alle autorisatie lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/diagnosticSettings/* | Diagnostische instellingen beheren |
> | Micro soft. netwerk/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. Storage/Storage accounts/* | Opslagaccounts maken en beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-account-key-operator-service-role"></a>Functie Service-sleutel operator van opslag account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan de toegangs sleutels voor het opslag account weer geven en opnieuw genereren. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Micro soft. Storage/Storage accounts/regeneratekey/Action | Hiermee worden de toegangs sleutels voor het opgegeven opslag account opnieuw gegenereerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-data-contributor"></a>Inzender voor Storage BLOB-gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/verwijderen | Een container verwijderen. |
> | Micro soft. Storage/Storage accounts/blobServices/containers/lezen | Een container of een lijst met containers retour neren. |
> | Micro soft. Storage/Storage accounts/blobServices/containers/schrijven | Meta gegevens of eigenschappen van een container wijzigen. |
> | Micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey/actie | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/verwijderen | Een BLOB verwijderen. |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/lezen | Een BLOB of een lijst met blobs retour neren. |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/schrijven | Schrijven naar een blob. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-data-owner"></a>Eigenaar van gegevens van opslag-BLOB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/* | Volledige machtigingen voor containers. |
> | Micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey/actie | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/* | Volledige machtigingen op blobs. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-data-reader"></a>Gegevens lezer van BLOB voor opslag
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/lezen | Een container of een lijst met containers retour neren. |
> | Micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey/actie | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/lezen | Een BLOB of een lijst met blobs retour neren. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-delegator"></a>Delegering van opslag-BLOB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey/actie | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Inzender voor opslag bestands gegevens SMB delen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt lees-, schrijf-en verwijder toegang in Azure Storage bestands shares via SMB toegestaan |
> | **Id** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/lezen | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/schrijven | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/verwijderen | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Opslag bestands gegevens SMB-share verhoogde Inzender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt toegang tot NTFS-machtigingen voor lezen, schrijven, verwijderen en wijzigen in Azure Storage bestands shares via SMB toegestaan |
> | **Id** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/lezen | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/schrijven | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/verwijderen | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/files/modifypermissions/Action | Retourneert het resultaat van het wijzigen van de machtiging voor een bestand/map. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-file-data-smb-share-reader"></a>Reader voor gegevens van SMB-share voor opslag bestand
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt lees toegang tot Azure file share via SMB toegestaan |
> | **Id** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/lezen | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-contributor"></a>Inzender voor opslag wachtrij gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/queueServices/queues/verwijderen | Een wachtrij verwijderen. |
> | Micro soft. Storage/Storage accounts/queueServices/queues/Read | Een wachtrij of een lijst met wacht rijen retour neren. |
> | Micro soft. Storage/Storage accounts/queueServices/queues/write | Meta gegevens of eigenschappen van de wachtrij wijzigen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/verwijderen | Een of meer berichten verwijderen uit een wachtrij. |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/lezen | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/schrijven | Een bericht toevoegen aan een wachtrij. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-message-processor"></a>Processor voor gegevens berichten van de opslag wachtrij
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Een bericht uit een Azure Storage wachtrij bekijken, ophalen en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/lezen | Een bericht bekijken. |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/proces/actie | Een bericht ophalen en verwijderen. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-message-sender"></a>Afzender gegevens bericht van opslag wachtrij
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Berichten toevoegen aan een Azure Storage wachtrij. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/toevoegen/actie | Een bericht toevoegen aan een wachtrij. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-reader"></a>Gegevens lezer van de opslag wachtrij
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Acties** |  |
> | Micro soft. Storage/Storage accounts/queueServices/queues/Read | Hiermee wordt een wachtrij of een lijst met wacht rijen geretourneerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/lezen | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="support-request-contributor"></a>Inzender voor ondersteunings aanvragen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u ondersteunings aanvragen maken en beheren |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="traffic-manager-contributor"></a>Inzender Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Network/trafficManagerProfiles/* |  |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="user-access-administrator"></a>Beheerder van gebruikerstoegang
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u de gebruikers toegang tot Azure-resources. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. Authorization/* | Autorisatie beheren |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="virtual-machine-administrator-login"></a>Aanmelding voor de beheerder van de virtuele machine
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Virtual Machines in de portal weer geven en u aanmelden als beheerder |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Acties** |  |
> | Micro soft. Network/publicIPAddresses/lezen | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Network/loadBalancers/lezen | Hiermee wordt een load balancer definitie opgehaald |
> | Micro soft. Network/networkInterfaces/lezen | Hiermee haalt u een definitie van een netwerk interface.  |
> | Micro soft. Compute/informatie/*/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Compute/informatie/login/Action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | Micro soft. Compute/informatie/loginAsAdmin/Action | Aanmelden bij een virtuele machine met Windows-beheerders-of Linux-root gebruikers bevoegdheden |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="virtual-machine-contributor"></a>Inzender voor virtuele machines
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. Compute/Availability sets/* | Berekenings beschikbaarheids sets maken en beheren |
> | Micro soft. Compute/locaties/* | Reken locaties maken en beheren |
> | Micro soft. Compute/informatie/* | Virtuele machines maken en beheren |
> | Micro soft. Compute/virtualMachineScaleSets/* | Schaal sets voor virtuele machines maken en beheren |
> | Micro soft. DevTestLab/planningen/* |  |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Network/applicationGateways/backendAddressPools/samen voegen/actie | Voegt een back-end-adres groep van een toepassings gateway samen. Niet Alertable. |
> | Micro soft. Network/loadBalancers/backendAddressPools/samen voegen/actie | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | Micro soft. Network/loadBalancers/inboundNatPools/samen voegen/actie | Voegt een load balancer binnenkomende NAT-pool samen. Niet alertable. |
> | Micro soft. Network/loadBalancers/inboundNatRules/samen voegen/actie | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | Micro soft. Network/loadBalancers/tests/samen voegen/actie | Staat het gebruik van tests van een load balancer toe. Met deze machtiging healthProbe eigenschap van de VM-schaalset kan bijvoorbeeld naar de test worden verwezen. Niet alertable. |
> | Micro soft. Network/loadBalancers/lezen | Hiermee wordt een load balancer definitie opgehaald |
> | Micro soft. netwerk/locaties/* | Netwerk locaties maken en beheren |
> | Micro soft. Network/networkInterfaces/* | Netwerk interfaces maken en beheren |
> | Micro soft. Network/networkSecurityGroups/samen voegen/actie | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Micro soft. Network/networkSecurityGroups/lezen | Hiermee wordt een definitie van een netwerk beveiligings groep opgehaald |
> | Micro soft. Network/publicIPAddresses/samen voegen/actie | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | Micro soft. Network/publicIPAddresses/lezen | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Network/virtualNetworks/subnetten/toevoegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Micro soft. Recovery Services/locaties/* |  |
> | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/schrijven | Een doel voor back-upbeveiliging maken |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/lezen | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Micro soft. Recovery Services/kluizen/backupPolicies/lezen | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Micro soft. Recovery Services/kluizen/backupPolicies/schrijven | Hiermee wordt een beveiligings beleid gemaakt |
> | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Micro soft. Recovery Services/kluizen/schrijven | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SqlVirtualMachine/* |  |
> | Micro soft. Storage/Storage accounts/Listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="virtual-machine-user-login"></a>Gebruikers aanmelding voor de virtuele machine
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Acties** |  |
> | Micro soft. Network/publicIPAddresses/lezen | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Micro soft. Network/loadBalancers/lezen | Hiermee wordt een load balancer definitie opgehaald |
> | Micro soft. Network/networkInterfaces/lezen | Hiermee haalt u een definitie van een netwerk interface.  |
> | Micro soft. Compute/informatie/*/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. Compute/informatie/login/Action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="web-plan-contributor"></a>Inzender voor webabonnementen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. web/server farms/* | Server farms maken en beheren |
> | Micro soft. Web/hostingEnvironments/samen voegen/actie | Voegt een App Service Environment |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="website-contributor"></a>Website bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Acties** |  |
> | Micro soft. Authorization/*/Read | Lees autorisatie |
> | Micro soft. Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/onderdelen/* | Insights-onderdelen maken en beheren |
> | Micro soft. ResourceHealth/availabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. web/certificaten/* | Website certificaten maken en beheren |
> | Micro soft. Web/listSitesAssignedToHostName/lezen | Namen ophalen van sites die zijn toegewezen aan de hostnaam. |
> | Micro soft. web/server farms/samen voegen/actie |  |
> | Micro soft. web/server farms/lezen | De eigenschappen van een App Service plan ophalen |
> | Micro soft. web/sites/* | Websites maken en beheren (voor het maken van sites is ook schrijf machtigingen vereist voor het bijbehorende App Service plan) |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Toegang tot Azure-resources beheren met op rollen gebaseerd toegangsbeheer en de Azure-portal](role-assignments-portal.md)
- [Machtigingen in Azure Security Center](../security-center/security-center-permissions.md)
