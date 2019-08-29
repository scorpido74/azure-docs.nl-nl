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
ms.date: 08/27/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: fb1007929a26384da60e542865c750fd1d642440
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114661"
---
# <a name="built-in-roles-for-azure-resources"></a>Ingebouwde rollen voor Azure-resources

[Op rollen gebaseerd toegangs beheer (RBAC)](overview.md) heeft verschillende ingebouwde rollen voor Azure-resources die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Roltoewijzingen zijn de manier waarop u de toegang tot Azure-resources beheert. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste rollen maken voor Azure-resources](custom-roles.md).

Dit artikel bevat een overzicht van de ingebouwde rollen voor Azure-resources, die altijd in ontwikkeling zijn. Gebruik de lijst met [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) of [AZ Role definition](/cli/azure/role/definition#az-role-definition-list)om de nieuwste rollen op te halen. Als u op zoek bent naar beheerders rollen voor Azure Active Directory, raadpleegt u [machtigingen voor beheerdersrol in azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Beschrijvingen van ingebouwde rollen

De volgende tabel bevat een korte beschrijving van elke ingebouwde rol. Klik op de naam van de rol om de `Actions`lijst `NotActions`met `DataActions`,, `NotDataActions` en voor elke rol weer te geven. Zie voor meer informatie over de werking van deze acties en over de manier waarop deze worden toegepast op het beheer-en gegevens abonnement [begrijpen functie definities voor Azure-resources](role-definitions.md).


| Ingebouwde rol | Description |
| --- | --- |
| [Eigenaar](#owner) | Hiermee kunt u alles beheren, inclusief toegang tot resources. |
| [Inzender](#contributor) | Hiermee beheert u alles behalve toegang tot resources. |
| [Lezer](#reader) | Hiermee kunt u alles weer geven, maar geen wijzigingen aanbrengen. |
| [AcrDelete](#acrdelete) | ACR verwijderen |
| [AcrImageSigner](#acrimagesigner) | acr-afbeeldingsondertekenaar |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | acr-quarantainegegevenslezer |
| [AcrQuarantineWriter](#acrquarantinewriter) | acr-quarantainegegevensschrijver |
| [Inzender van API Management-service](#api-management-service-contributor) | Kan de service en de Api's beheren |
| [Functie API Management service operator](#api-management-service-operator-role) | Kan de service beheren, maar niet de Api's |
| [API Management functie Service lezer](#api-management-service-reader-role) | Alleen-lezen toegang tot de service en Api's |
| [Inzender voor Application Insights onderdelen](#application-insights-component-contributor) | Kan onderdelen van Application Insights beheren |
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
| [Gegevens opschoner](#data-purger) | Kan analytische gegevens verwijderen |
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
| [Inzender beheer groep](#management-group-contributor) | Rol Inzender beheergroep |
| [Lezer beheer groep](#management-group-reader) | Rol Lezer beheergroep |
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
| [Beveiligings beheerder](#security-admin) | Alleen in Security Center: Kan beveiligings beleid weer geven, beveiligings statussen bekijken, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren |
| [Beveiligings beheer (verouderd)](#security-manager-legacy) | Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder |
| [Beveiligings lezer](#security-reader) | Alleen in Security Center: Kan aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar kan geen wijzigingen aanbrengen |
| [Inzender Site Recovery](#site-recovery-contributor) | Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing |
| [Site Recovery-operator](#site-recovery-operator) | Een failover en failback, maar geen andere Site Recovery beheer bewerkingen uitvoeren |
| [Site Recovery lezer](#site-recovery-reader) | Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren |
| [Inzender voor ruimtelijke ankers](#spatial-anchors-account-contributor) | Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen |
| [Eigenaar van ruimtelijk ankers](#spatial-anchors-account-owner) | Hiermee kunt u ruimtelijke ankers in uw account beheren, met inbegrip van het verwijderen ervan |
| [Lezer van ruimtelijk ankers-account](#spatial-anchors-account-reader) | Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen |
| [Inzender voor SQL-data base](#sql-db-contributor) | Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. |
| [Inzender voor SQL Managed instance](#sql-managed-instance-contributor) | Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen. |
| [SQL Security Manager](#sql-security-manager) | Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. |
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
> | **Beschrijving** | Hiermee beheert u alles behalve toegang tot resources. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Acties** |  |
> | * | Resources van alle typen maken en beheren |
> | **Intact** |  |
> | Microsoft.Authorization/*/Delete | Rollen en roltoewijzingen verwijderen |
> | Microsoft.Authorization/*/Write | Rollen en roltoewijzingen maken |
> | Microsoft.Authorization/elevateAccess/Action | Hiermee wordt de gebruiker toegang tot beheerders verleend in het Tenant bereik |
> | Microsoft.Blueprint/blueprintAssignments/write | Blauw drukken artefacten maken of bijwerken |
> | Microsoft.Blueprint/blueprintAssignments/delete | Alle blauwdrukartefacten verwijderen |
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
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Artefact verwijderen in container register. |
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
> | **Beschrijving** | acr-afbeeldingsondertekenaar |
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
> | **Beschrijving** | acr pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pull of ophalen van installatie kopieën uit een container register. |
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
> | **Beschrijving** | acr push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pull of ophalen van installatie kopieën uit een container register. |
> | Microsoft.ContainerRegistry/registries/push/write | Push of schrijf installatie kopieën naar een container register. |
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
> | **Beschrijving** | acr-quarantainegegevenslezer |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
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
> | **Beschrijving** | acr-quarantainegegevensschrijver |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | Micro soft. ContainerRegistry/registers/quarantineWrite/schrijven | De quarantaine status van in quarantaine geplaatste installatie kopieën schrijven/wijzigen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="api-management-service-contributor"></a>Inzender voor API Management-services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan de service en de Api's beheren |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/* | API Management-service maken en beheren |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="api-management-service-operator-role"></a>Operator-rol voor de API Management-service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan de service beheren, maar niet de Api's |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/*/read | API Management service-exemplaren lezen |
> | Microsoft.ApiManagement/service/backup/action | Backup API Management-service naar de opgegeven container in een door de gebruiker opgegeven opslag account |
> | Microsoft.ApiManagement/service/delete | API Management service-exemplaar verwijderen |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/eenheden wijzigen, regionale implementaties van API Management service toevoegen/verwijderen |
> | Microsoft.ApiManagement/service/read | Meta gegevens voor een API Management service-exemplaar lezen |
> | Microsoft.ApiManagement/service/restore/action | De API Management-service herstellen vanuit de opgegeven container in een opslag account dat door de gebruiker is opgegeven |
> | Microsoft.ApiManagement/service/updatecertificate/action | SSL-certificaat voor een API Management-service uploaden |
> | Microsoft.ApiManagement/service/updatehostname/action | Aangepaste domein namen instellen, bijwerken of verwijderen voor een API Management-service |
> | Microsoft.ApiManagement/service/write | Een nieuw exemplaar van API Management service maken |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="api-management-service-reader-role"></a>Lezerrol voor de API Management-service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Alleen-lezen toegang tot de service en Api's |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/*/read | API Management service-exemplaren lezen |
> | Microsoft.ApiManagement/service/read | Meta gegevens voor een API Management service-exemplaar lezen |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="application-insights-component-contributor"></a>Inzender voor Application Insights-onderdelen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan onderdelen van Application Insights beheren |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.Insights/components/* | Insights-onderdelen maken en beheren |
> | Microsoft.Insights/webtests/* | Webtests maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="automation-job-operator"></a>Operator voor Automation-taak
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Maak en beheer taken met behulp van Automation-Runbooks. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker resources lezen |
> | Microsoft.Automation/automationAccounts/jobs/read | Hiermee wordt een Azure Automation-taak opgehaald |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Hiermee wordt een Azure Automation taak hervat |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Hiermee wordt een Azure Automation taak gestopt |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Hiermee wordt een Azure Automation taak onderbroken |
> | Microsoft.Automation/automationAccounts/jobs/write | Hiermee maakt u een Azure Automation taak |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Hiermee wordt de uitvoer van een taak opgehaald |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="automation-operator"></a>Operator voor Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker resources lezen |
> | Microsoft.Automation/automationAccounts/jobs/read | Hiermee wordt een Azure Automation-taak opgehaald |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Hiermee wordt een Azure Automation taak hervat |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Hiermee wordt een Azure Automation taak gestopt |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Hiermee wordt een Azure Automation taak onderbroken |
> | Microsoft.Automation/automationAccounts/jobs/write | Hiermee maakt u een Azure Automation taak |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Hiermee wordt een Azure Automation taak planning opgehaald |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Hiermee maakt u een Azure Automation taak schema |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hiermee wordt de werk ruimte opgehaald die is gekoppeld aan het Automation-account |
> | Microsoft.Automation/automationAccounts/read | Hiermee wordt een Azure Automation-account opgehaald |
> | Microsoft.Automation/automationAccounts/runbooks/read | Hiermee wordt een Azure Automation runbook opgehaald |
> | Microsoft.Automation/automationAccounts/schedules/read | Hiermee wordt een Azure Automation schema-element opgehaald |
> | Microsoft.Automation/automationAccounts/schedules/write | Hiermee wordt een Azure Automation Schedule-Asset gemaakt of bijgewerkt |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Hiermee wordt de uitvoer van een taak opgehaald |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="automation-runbook-operator"></a>Operator voor Automation-runbook
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Automation/automationAccounts/runbooks/read | Hiermee wordt een Azure Automation runbook opgehaald |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Compute/*/read |  |
> | Micro soft. Compute/Availability sets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.Network/virtualNetworks/subnets/read | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Hiermee haalt u de resources voor de resource groep op. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourneert een BLOB of een lijst met blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="avere-operator"></a>AVERE-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Acties** |  |
> | Microsoft.Compute/virtualMachines/read | De eigenschappen van een virtuele machine ophalen |
> | Microsoft.Network/networkInterfaces/read | Hiermee haalt u een definitie van een netwerk interface.  |
> | Microsoft.Network/networkInterfaces/write | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.Network/virtualNetworks/subnets/read | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retourneert het resultaat van het verwijderen van een container |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Hiermee wordt een lijst met containers geretourneerd |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourneert het resultaat van de put-BLOB-container |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourneert een BLOB of een lijst met blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-event-hubs-data-owner"></a>Eigenaar van Azure Event Hubs-gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Acties** |  |
> | Microsoft.EventHub/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-event-hubs-data-receiver"></a>Gegevens ontvanger van Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u toegang tot Azure Event Hubs-resources. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Acties** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-event-hubs-data-sender"></a>Afzender van Azure Event Hubs gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee wordt toegang tot Azure Event Hubs-resources verzonden. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Acties** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Rol van Cluster beheerder voor Azure Kubernetes-service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Lijst met actie voor cluster beheer referenties. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Acties** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | De clusterAdmin-referentie van een beheerd cluster weer geven |
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
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | De clusterUser-referentie van een beheerd cluster weer geven |
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
> | Microsoft.Maps/accounts/data/read | Hiermee wordt het lezen van gegevens toegang verleend aan een Maps-account. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-service-bus-data-owner"></a>Gegevens eigenaar Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u volledige toegang tot Azure Service Bus-resources. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Acties** |  |
> | Microsoft.ServiceBus/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-service-bus-data-receiver"></a>Gegevens ontvanger Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee krijgt u toegang tot Azure Service Bus-resources. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Acties** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-service-bus-data-sender"></a>Afzender van Azure Service Bus gegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u toegang tot Azure Service Bus resources verzenden. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Acties** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="azure-stack-registration-owner"></a>Eigenaar Azure Stack-registratie
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u Azure Stack registraties beheren. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Acties** |  |
> | Micro soft. AzureStack/registraties/producten/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Hiermee worden de eigenschappen van een Azure Stack Marketplace-product opgehaald |
> | Microsoft.AzureStack/registrations/read | Hiermee worden de eigenschappen van een Azure Stack registratie opgehaald |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Resultaten van de werking van het back-upbeheer beheren |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Back-upcontainers maken en beheren in back-upinfrastructuur van Recovery Services kluis |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Hiermee vernieuwt u de container lijst |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Meta gegevens met betrekking tot back-upbeheer maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Back-upbeleid maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Back-upitems maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Containers maken en beheren met back-upitems |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Certificaten maken en beheren die zijn gerelateerd aan back-ups in Recovery Services kluis |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | Microsoft.RecoveryServices/Vaults/usages/* | Gebruik van Recovery Services kluis maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Bewerking voor beveiligd item valideren |
> | Microsoft.RecoveryServices/Vaults/write | Met de bewerking Kluis maken wordt een Azure-resource van het type vault gemaakt. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Hiermee worden alle servers voor back-upbeheer geretourneerd die in de kluis zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Alle Beveilig bare containers ophalen |
> | Microsoft.RecoveryServices/locations/backupStatus/action | De back-upstatus voor Recovery Services kluizen controleren |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Functies valideren |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties van de back-upbeveiliging weer geven |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Hiermee wordt de status van de bewerking geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hiermee wordt het resultaat opgehaald van de bewerking die is uitgevoerd op de beveiligde container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Hiermee wordt een back-up van het beveiligde item gemaakt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hiermee wordt het resultaat opgehaald van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Direct-item herstel inrichten voor beveiligd item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstelpunten voor beveiligde items ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Herstelpunten voor beveiligde items herstellen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Herstel van onmiddellijke items intrekken voor beveiligd item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Hiermee worden alle geregistreerde containers geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Hiermee vernieuwt u de container lijst |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hiermee worden de resultaten van de beleidsbewerking opgehaald. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Hiermee worden alle containers van het abonnement geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type ?vault? vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Met de bewerking Uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type ?vault? vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | De bewerking service container registreren kan worden gebruikt om een container te registreren bij de Recovery-service. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden de gebruiksgegevens voor een Recovery Services-kluis geretourneerd. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Bewerking voor beveiligd item valideren |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleids bewerking ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Hiermee maakt u een geregistreerde container |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Een query uitvoeren voor werk belastingen binnen een container |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Hiermee worden alle servers voor back-upbeheer geretourneerd die in de kluis zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Een doel voor back-upbeveiliging maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Een doel voor back-upbeveiliging verkrijgen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Alle Beveilig bare containers ophalen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Alle items in een container ophalen |
> | Microsoft.RecoveryServices/locations/backupStatus/action | De back-upstatus voor Recovery Services kluizen controleren |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Functies valideren |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties van de back-upbeveiliging weer geven |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Hiermee wordt de status van de bewerking geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hiermee wordt het resultaat opgehaald van de bewerking die is uitgevoerd op de beveiligde container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hiermee wordt het resultaat opgehaald van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstelpunten voor beveiligde items ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Hiermee worden alle geregistreerde containers geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Hiermee wordt het resultaat van de taakbewerking geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Hiermee worden alle taak objecten geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Hiermee wordt het resultaat van de back-upbewerking voor een Recovery Services-kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hiermee worden de resultaten van de beleidsbewerking opgehaald. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Hiermee worden alle containers van het abonnement geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type ?vault? vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Hiermee wordt de opslag configuratie voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Hiermee wordt de configuratie voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleids bewerking ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Hiermee worden alle servers voor back-upbeheer geretourneerd die in de kluis zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Een doel voor back-upbeveiliging verkrijgen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Alle items in een container ophalen |
> | Microsoft.RecoveryServices/locations/backupStatus/action | De back-upstatus voor Recovery Services kluizen controleren |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties van de back-upbeveiliging weer geven |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden de gebruiksgegevens voor een Recovery Services-kluis geretourneerd. |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Billing/*/read | Facturerings gegevens lezen |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="biztalk-contributor"></a>Inzender voor BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services maken en beheren |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hiermee wordt een lijst met bestaande Block Chain-leden transactie knooppunten opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Maakt verbinding met een trans actie-knoop punt van het block Chain-lid. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="blueprint-contributor"></a>Blauw druk bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan blauw drukken-definities beheren, maar niet toewijzen. |
> | **Id** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Micro soft. blauw druk/blauw drukken/* | Maak en beheer blauw drukken-definities of blauw drukken-artefacten. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Micro soft. blauw druk/blueprintAssignments/* | Maak maken en beheren van blauw druk-toewijzingen. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-endpoint-contributor"></a>Inzender voor CDN-eindpunt
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-endpoint-reader"></a>Lezer voor CDN-eindpunt
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cdn-profile-reader"></a>Lezer voor CDN-profiel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.ClassicNetwork/* | Klassieke netwerken maken en beheren |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="classic-storage-account-contributor"></a>Inzender voor klassieke opslagaccounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.ClassicStorage/storageAccounts/* | Opslagaccounts maken en beheren |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Hiermee worden de bestaande toegangs sleutels voor het opslag account opnieuw gegenereerd. |
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
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Micro soft. ClassicCompute/domainName/* | Klassieke Compute-domein namen maken en beheren |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuele machines maken en beheren |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Een gereserveerde IP koppelen |
> | Microsoft.ClassicNetwork/reservedIps/read | Hiermee worden de gereserveerde IP's opgehaald. |
> | Micro soft. ClassicNetwork/virtualNetworks/deelname/actie | Voegt het virtuele netwerk samen. |
> | Micro soft. ClassicNetwork/virtualNetworks/lezen | Het virtuele netwerk ophalen. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Retourneert de schijf van het opslag account. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Hiermee wordt de installatie kopie van het opslag account geretourneerd. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retour neer het opslag account met het opgegeven account. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cognitive-services-contributor"></a>Inzender voor Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.CognitiveServices/* |  |
> | Micro soft. features/onderdelen/lezen | Hiermee haalt u de functies van een abonnement op. |
> | Microsoft.Features/providers/features/read | Hiermee wordt de functie van een abonnement in een bepaalde resource provider opgehaald. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Microsoft.Insights/logDefinitions/read | Logboekdefinities lezen |
> | Micro soft. Insights/metricdefinitions/lezen | Metrische definities lezen |
> | Microsoft.Insights/metrics/read | De metrische gegevens lezen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Microsoft.Resources/subscriptions/operationresults/read | De resultaten van de abonnements bewerking ophalen. |
> | Microsoft.Resources/subscriptions/read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cognitive-services-user"></a>Cognitive Services-gebruiker
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Acties** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Een lijst met sleutels maken |
> | Microsoft.Insights/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | Micro soft. Insights/diagnosticSettings/lezen | Een diagnostische instelling voor de resource lezen |
> | Microsoft.Insights/logDefinitions/read | Logboekdefinities lezen |
> | Micro soft. Insights/metricdefinitions/lezen | Metrische definities lezen |
> | Microsoft.Insights/metrics/read | De metrische gegevens lezen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Microsoft.Resources/subscriptions/operationresults/read | De resultaten van de abonnements bewerking ophalen. |
> | Microsoft.Resources/subscriptions/read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cosmos-db-account-reader-role"></a>Rol van lezer voor Cosmos DB-account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Bij het lezen van functies en roltoewijzingen kunnen machtigingen worden gelezen die aan elke gebruiker worden verleend |
> | Microsoft.DocumentDB/*/read | Een verzameling lezen |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Microsoft.Insights/MetricDefinitions/read | Metrische definities lezen |
> | Microsoft.Insights/Metrics/read | De metrische gegevens lezen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
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
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Een aanvraag indienen voor het configureren van de back-up |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Een herstel aanvraag verzenden |
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
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Advisor/configuraties/lezen | Configuraties ophalen |
> | Micro soft. Advisor/aanbevelingen/lezen | Aanbevelingen voor lezen |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="cost-management-reader"></a>Reader Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exports) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Acties** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. Advisor/configuraties/lezen | Configuraties ophalen |
> | Micro soft. Advisor/aanbevelingen/lezen | Aanbevelingen voor lezen |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Databox/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-box-reader"></a>Data Box-reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Geeft een lijst van de niet-versleutelde referenties die zijn gerelateerd aan de order. |
> | Microsoft.Databox/locations/availableSkus/action | Met deze methode wordt de lijst met beschik bare sku's geretourneerd. |
> | Microsoft.Databox/locations/validateAddress/action | Valideert het verzend adres en levert eventueel alternatieve adressen. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.DataFactory/dataFactories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | Microsoft.DataFactory/factories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-lake-analytics-developer"></a>Ontwikkelaar voor Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Een DataLakeAnalytics-account verwijderen. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Machtigingen verlenen om taken te annuleren die door andere gebruikers zijn verzonden. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Een DataLakeAnalytics-account maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Een gekoppeld data Lake Store-account maken of bijwerken van een DataLakeAnalytics-account. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Een Data Lake Store-account ontkoppelen van een DataLakeAnalytics-account. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Een gekoppeld opslag account maken of bijwerken van een DataLakeAnalytics-account. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Een opslag account ontkoppelen van een DataLakeAnalytics-account. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Een firewall regel maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Een firewall regel verwijderen. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Een reken beleid maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Een reken beleid verwijderen. |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="data-purger"></a>Gegevensverwijderaar
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan analytische gegevens verwijderen |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Acties** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Gegevens uit Application Insights leegmaken |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Opgegeven gegevens uit de werk ruimte verwijderen |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Compute/availabilitySets/read | De eigenschappen van een beschikbaarheidsset ophalen |
> | Microsoft.Compute/virtualMachines/*/read | De eigenschappen van een virtuele machine lezen (VM-grootte, runtime status, VM-extensies, enzovoort) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Hiermee wordt de virtuele machine uitgeschakeld en worden de reken resources vrijgegeven |
> | Microsoft.Compute/virtualMachines/read | De eigenschappen van een virtuele machine ophalen |
> | Microsoft.Compute/virtualMachines/restart/action | Hiermee wordt de virtuele machine opnieuw gestart |
> | Microsoft.Compute/virtualMachines/start/action | Hiermee wordt de virtuele machine gestart |
> | Microsoft.DevTestLab/*/read | De eigenschappen van een Lab lezen |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Claim een wille keurig claim bare virtuele machine in het lab. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Maak virtuele machines in een lab. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Zorg ervoor dat de huidige gebruiker een geldig profiel in het lab heeft. |
> | Microsoft.DevTestLab/labs/formulas/delete | Formules verwijderen. |
> | Microsoft.DevTestLab/labs/formulas/read | Formules lezen. |
> | Microsoft.DevTestLab/labs/formulas/write | Formules toevoegen of wijzigen. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Evalueert het lab-beleid. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Eigenaar worden van een bestaande virtuele machine |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Hiermee wordt een teken reeks opgehaald waarmee de inhoud van het RDP-bestand voor de virtuele machine wordt aangeduid |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | Microsoft.Network/networkInterfaces/*/read | De eigenschappen van een netwerk interface lezen (bijvoorbeeld alle load balancers waarvan de netwerk interface deel uitmaakt) |
> | Microsoft.Network/networkInterfaces/join/action | Voegt een virtuele machine toe aan een netwerk interface. Niet Alertable. |
> | Microsoft.Network/networkInterfaces/read | Hiermee haalt u een definitie van een netwerk interface.  |
> | Microsoft.Network/networkInterfaces/write | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | Microsoft.Network/publicIPAddresses/*/read | De eigenschappen van een openbaar IP-adres lezen |
> | Microsoft.Network/publicIPAddresses/join/action | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Microsoft.Resources/deployments/read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | **Intact** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Een lijst met beschik bare grootten waarmee de virtuele machine kan worden bijgewerkt |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="dns-zone-contributor"></a>Inzender voor DNS-zone
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.Network/dnsZones/* | DNS-zones en-records maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="documentdb-account-contributor"></a>Inzender voor het DocumentDB-account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB accounts maken en beheren |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Inzender van abonnement voor EventGrid-gebeurtenissen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionale gebeurtenis abonnementen weer geven |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionale gebeurtenis abonnementen weer geven op topictype |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Lezer van abonnement voor EventGrid-gebeurtenissen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.EventGrid/eventSubscriptions/read | Een eventSubscription lezen |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionale gebeurtenis abonnementen weer geven |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionale gebeurtenis abonnementen weer geven op topictype |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
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
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Gateway-instellingen voor HDInsight-cluster ophalen |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="hdinsight-domain-services-contributor"></a>Inzender HDInsight Domain Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan gerelateerde bewerkingen die betrekking hebben op domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Acties** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="intelligent-systems-account-contributor"></a>Inzender voor ISS-accounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.IntelligentSystems/accounts/* | Intelligente systeem accounts maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="key-vault-contributor"></a>Inzender voor Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u sleutel kluizen beheren, maar niet de toegang tot de kluis. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Een voorlopig verwijderde sleutel kluis leegmaken |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="lab-creator"></a>Labmaker
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u uw beheerde Labs maken, beheren en verwijderen in uw Azure Lab-accounts. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Een lab maken in een Lab-account. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Informatie over regionale Beschik baarheid ophalen voor elke grootte categorie die is geconfigureerd met een Lab-account |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met een nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/search/action | Hiermee wordt een zoek query uitgevoerd |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="logic-app-contributor"></a>Logische app-bijdrager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u logische apps beheren, maar niet wijzigen. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retour neer het opslag account met het opgegeven account. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Microsoft.Insights/logdefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | Microsoft.Insights/metricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | Microsoft.Logic/* | Beheert Logic Apps-resources. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/operationresults/read | De resultaten van de abonnements bewerking ophalen. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Web/connectionGateways/* | Een verbindings gateway maken en beheren. |
> | Microsoft.Web/connections/* | Een verbinding maken en beheren. |
> | Microsoft.Web/customApis/* | Maakt en beheert een aangepaste API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | De eigenschappen van een App Service plan ophalen |
> | Microsoft.Web/sites/functions/listSecrets/action | Geef geheimen Web Apps functies weer. |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/*/read | Inzichten-waarschuwings regels lezen |
> | Micro soft. Insights/diagnosticSettings/*/Read | Hiermee worden Diagnostische instellingen voor Logic Apps opgehaald |
> | Micro soft. Insights/metricDefinitions/*/Read | Hiermee worden de beschik bare metrische gegevens opgehaald voor Logic Apps. |
> | Microsoft.Logic/*/read | Hiermee worden Logic Apps resources gelezen. |
> | Microsoft.Logic/workflows/disable/action | Hiermee schakelt u de werk stroom uit. |
> | Microsoft.Logic/workflows/enable/action | Hiermee wordt de werk stroom ingeschakeld. |
> | Microsoft.Logic/workflows/validate/action | Valideert de werk stroom. |
> | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Microsoft.Resources/subscriptions/operationresults/read | De resultaten van de abonnements bewerking ophalen. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Web/connectionGateways/*/read | Lees de verbindings gateways. |
> | Microsoft.Web/connections/*/read | Lees verbindingen. |
> | Microsoft.Web/customApis/*/read | Lees de aangepaste API. |
> | Microsoft.Web/serverFarms/read | De eigenschappen van een App Service plan ophalen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-application-operator-role"></a>Operatorrol voor beheerde toepassingen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Microsoft.Solutions/applications/read | Hiermee haalt u een lijst met toepassingen op. |
> | Microsoft.Solutions/*/action |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-applications-reader"></a>Reader Beheerde toepassingen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
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
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit opgehaald |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Hiermee wordt een nieuwe door de gebruiker toegewezen identiteit gemaakt of worden de labels bijgewerkt die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit verwijderd |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="managed-identity-operator"></a>Operator beheerde identiteit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Door gebruiker toegewezen identiteit lezen en toewijzen |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Acties** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="management-group-contributor"></a>Inzender beheergroep
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Rol Inzender beheergroep |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Acties** |  |
> | Microsoft.Management/managementGroups/delete | Beheer groep verwijderen. |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Het abonnement van de beheer groep wordt ontkoppeld. |
> | Microsoft.Management/managementGroups/subscriptions/write | Het bestaande abonnement wordt gekoppeld aan de beheer groep. |
> | Microsoft.Management/managementGroups/write | Een beheer groep maken of bijwerken. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="management-group-reader"></a>Lezer beheergroep
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Rol Lezer beheergroep |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Acties** |  |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="monitoring-contributor"></a>Bijdrager voor bewaking
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Waarschuwings regels lezen/schrijven/verwijderen. |
> | Microsoft.Insights/components/* | Application Insights onderdelen lezen/schrijven/verwijderen. |
> | Microsoft.Insights/DiagnosticSettings/* | Diagnostische instellingen lezen/schrijven/verwijderen. |
> | Microsoft.Insights/eventtypes/* | Lijst activiteiten logboek gebeurtenissen (beheer gebeurtenissen) in een abonnement. Deze machtiging is van toepassing op zowel toegang via het programma als de portal tot het activiteiten logboek. |
> | Microsoft.Insights/LogDefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | Micro soft. Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | Microsoft.Insights/Metrics/* | Meet gegevens voor een resource lezen. |
> | Microsoft.Insights/Register/Action | De Microsoft Insights-provider registeren |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Application Insights webtests lezen/schrijven/verwijderen. |
> | Micro soft. Insights/werkmappen/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log Analytics-oplossings pakketten lezen/schrijven/verwijderen. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | In log Analytics opgeslagen Zoek opdrachten lezen/schrijven/verwijderen. |
> | Microsoft.OperationalInsights/workspaces/search/action | Hiermee wordt een zoek query uitgevoerd |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Opslag inzicht configuraties voor log Analytics lezen/schrijven/verwijderen. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Micro soft. WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Micro soft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="monitoring-metrics-publisher"></a>Uitgever van metrische gegevens voor bewaking
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Acties** |  |
> | Microsoft.Insights/Register/Action | De Microsoft Insights-provider registeren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Metrische gegevens schrijven |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="monitoring-reader"></a>Lezer voor bewaking
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Microsoft.OperationalInsights/workspaces/search/action | Hiermee wordt een zoek query uitgevoerd |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.Network/* | Netwerken maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="new-relic-apm-account-contributor"></a>Inzender voor het New Relic APM-account
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | NewRelic.APM/accounts/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="reader-and-data-access"></a>Lezer- en gegevenstoegang
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Hiermee wordt het SAS-token van het account voor het opgegeven opslag account geretourneerd. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="redis-cache-contributor"></a>Inzender voor Redis-caches
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Cache/redis/* | Redis-caches maken en beheren |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/policyassignments/* | Beleids toewijzingen maken en beheren |
> | Microsoft.Authorization/policydefinitions/* | Beleids definities maken en beheren |
> | Micro soft. Authorization/policysetdefinitions/* | Beleids sets maken en beheren |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="scheduler-job-collections-contributor"></a>Inzender voor Scheduler-taakverzamelingen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Scheduler/jobcollections/* | Taak verzamelingen maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="search-service-contributor"></a>Inzender voor Search-services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u zoek services beheren, maar niet de toegang tot ze. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Search/searchServices/* | Zoek Services maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | **Beschrijving** | Alleen in Security Center: Kan beveiligings beleid weer geven, beveiligings statussen bekijken, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Authorization/policyAssignments/* | Beleids toewijzingen maken en beheren |
> | Micro soft. Authorization/policyDefinitions/* | Beleids definities maken en beheren |
> | Microsoft.Authorization/policySetDefinitions/* | Beleids sets maken en beheren |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-gegevens weer geven |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicCompute/*/read | Configuratie-informatie voor klassieke virtuele machines lezen |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Configuratie voor klassieke virtuele machines schrijven |
> | Microsoft.ClassicNetwork/*/read | Configuratie-informatie over klassiek netwerk lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Security/* | Beveiligings onderdelen en-beleid maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="security-reader"></a>Beveiligingslezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Alleen in Security Center: Kan aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar kan geen wijzigingen aanbrengen |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-gegevens weer geven |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Security/*/read | Beveiligings onderdelen en-beleid lezen |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="site-recovery-contributor"></a>Site Recovery-inzender
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Waarschuwings instellingen voor replicatie maken of bijwerken |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Gebeurtenissen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Replicatie-fabrics maken en beheren |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Replicatie taken maken en beheren |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Replicatie beleid maken en beheren |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Herstel plannen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Opslag configuratie van Recovery Services kluis maken en beheren |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden de gebruiksgegevens voor een Recovery Services-kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type ?vault? vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Waarschuwings instellingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Gebeurtenissen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Hiermee wordt de consistentie van de infrastructuur gecontroleerd |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/lezen | Alle infra structuren lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Gateway opnieuw koppelen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Certificaat voor Fabric vernieuwen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Alle netwerken lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Netwerk toewijzingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Beveiligings containers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/lezen | Beveilig bare items lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Herstel punt Toep assen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover door voeren |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplande failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Alle beveiligde items lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Alle replicatie herstel punten lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replicatie herstellen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Beveiligd item opnieuw beveiligen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover testen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Failovertest opschonen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobility-service bijwerken |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Alle beveiligings container toewijzingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services Providers lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Provider vernieuwen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Alle vCenter lezen |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Replicatie taken maken en beheren |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Alle beleids regels lezen |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Herstel plan voor failover door voeren |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Herstel plan voor geplande failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Herstel plannen lezen |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Herstel plan opnieuw beveiligen |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Herstel plan voor failover testen |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Herstel plan voor het opschonen van de Failovertest |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Herstel plan voor failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden de gebruiksgegevens voor een Recovery Services-kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="site-recovery-reader"></a>Site Recovery-lezer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type ?vault? vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Waarschuwings instellingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Gebeurtenissen lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/lezen | Alle infra structuren lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Alle netwerken lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Netwerk toewijzingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Beveiligings containers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/lezen | Beveilig bare items lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Alle beveiligde items lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Alle replicatie herstel punten lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Alle beveiligings container toewijzingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services Providers lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Alle vCenter lezen |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Alle taken lezen |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Alle beleids regels lezen |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Herstel plannen lezen |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden de gebruiksgegevens voor een Recovery Services-kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="spatial-anchors-account-contributor"></a>Inzender voor Spatial Anchors-account
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
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Ruimtelijke ankers maken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ruimtelijke beankeringen in de buurt detecteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="spatial-anchors-account-owner"></a>Eigenaar van Spatial Anchors-account
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
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Ruimtelijke ankers maken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Ruimtelijke ankers verwijderen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ruimtelijke beankeringen in de buurt detecteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="spatial-anchors-account-reader"></a>Lezer voor Spatial Anchors-account
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
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ruimtelijke beankeringen in de buurt detecteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-db-contributor"></a>Inzender voor SQL-databases
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/locaties/*/Read |  |
> | Microsoft.Sql/servers/databases/* | SQL-data bases maken en beheren |
> | Microsoft.Sql/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Insights/metrics/read | De metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Controle beleid bewerken |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Controle-instellingen bewerken |
> | Microsoft.Sql/servers/databases/auditRecords/read | De data base-BLOB-controle records ophalen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Verbindings beleid bewerken |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Beleids regels voor gegevens maskering bewerken |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Beveiligings waarschuwingen beleid bewerken |
> | Micro soft. SQL/servers/data bases/securityMetrics/* | Metrische beveiligings gegevens bewerken |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
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
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Micro soft. SQL/locaties/*/Read |  |
> | Micro soft. SQL/managedInstances/* |  |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/metrics/read | De metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-security-manager"></a>SQL-beveiligingsbeheer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Micro soft-autorisatie lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Micro soft. SQL/managedInstances/data bases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL Server-controle beleid maken en beheren |
> | Microsoft.Sql/servers/auditingSettings/* | SQL Server-controle-instelling maken en beheren |
> | Micro soft. SQL/servers/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide-server-BLOB-controle beleid dat op een bepaalde server is geconfigureerd |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Controle beleid voor SQL server-data bases maken en beheren |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Controle-instellingen voor SQL Server-Data Base maken en beheren |
> | Microsoft.Sql/servers/databases/auditRecords/read | Controle records lezen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL Server-database verbindings beleid maken en beheren |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Gegevens maskerings beleid voor SQL server-data bases maken en beheren |
> | Micro soft. SQL/servers/data bases/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | Micro soft. SQL/servers/data bases/lezen | De lijst met data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/schema's/lezen | Een database schema ophalen. |
> | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/lezen | Een database kolom ophalen. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/schema's/tabellen/lezen | Een database tabel ophalen. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Beveiligings waarschuwingen beleid voor SQL Server-Data Base maken en beheren |
> | Micro soft. SQL/servers/data bases/securityMetrics/* | Metrische gegevens over beveiliging voor SQL Server-Data Base maken en beheren |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Micro soft. SQL/servers/data bases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Micro soft. SQL/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Beveiligings waarschuwingen voor SQL Server maken en beheren |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="sql-server-contributor"></a>Inzender voor SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/locaties/*/Read |  |
> | Microsoft.Sql/servers/* | SQL-servers maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Insights/metrics/read | De metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Micro soft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Controle beleid voor SQL server bewerken |
> | Microsoft.Sql/servers/auditingSettings/* | SQL Server-controle-instellingen bewerken |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Controle beleid voor SQL Server-Data Base bewerken |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Controle-instellingen voor SQL Server-Data Base bewerken |
> | Microsoft.Sql/servers/databases/auditRecords/read | Controle records lezen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL Server-database verbindings beleid bewerken |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Beleids regels voor gegevens maskering van SQL Server-Data Base bewerken |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Beleid voor beveiligings waarschuwingen van SQL Server-Data Base bewerken |
> | Micro soft. SQL/servers/data bases/securityMetrics/* | Metrische gegevens van beveiliging voor SQL Server-Data Base bewerken |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Beveiligings waarschuwingen voor SQL server bewerken |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-account-contributor"></a>Inzender voor opslagaccounts
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Alle autorisatie lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Diagnostische instellingen beheren |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/* | Opslagaccounts maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
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
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Hiermee worden de toegangs sleutels voor het opgegeven opslag account opnieuw gegenereerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-data-contributor"></a>Bijdrager voor opslagblobgegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Een container verwijderen. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Een container of een lijst met containers retour neren. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Meta gegevens of eigenschappen van een container wijzigen. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Een BLOB verwijderen. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Een BLOB of een lijst met blobs retour neren. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Schrijven naar een blob. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-data-owner"></a>Eigenaar van opslagblobgegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Volledige machtigingen voor containers. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Volledige machtigingen op blobs. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-data-reader"></a>Lezer voor opslagblobgegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Een container of een lijst met containers retour neren. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Een BLOB of een lijst met blobs retour neren. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-blob-delegator"></a>Delegering van opslag-BLOB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
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
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
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
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
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
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-contributor"></a>Bijdrager voor opslagwachtrijgegevens
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Een wachtrij verwijderen. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Een wachtrij of een lijst met wacht rijen retour neren. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Meta gegevens of eigenschappen van de wachtrij wijzigen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Een of meer berichten verwijderen uit een wachtrij. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Een bericht toevoegen aan een wachtrij. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-message-processor"></a>Berichtenprocessor voor opslagwachtrijgegevens
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
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een bericht bekijken. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Een bericht ophalen en verwijderen. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-message-sender"></a>Berichtenverzender voor opslagwachtrijgegevens
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
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Een bericht toevoegen aan een wachtrij. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="storage-queue-data-reader"></a>Gegevenslezer voor opslagwachtrij
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Hiermee wordt een wachtrij of een lijst met wacht rijen geretourneerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="support-request-contributor"></a>Inzender voor ondersteuningsaanvragen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u ondersteunings aanvragen maken en beheren |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="traffic-manager-contributor"></a>Inzender voor Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="user-access-administrator"></a>Administrator voor gebruikerstoegang
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee beheert u de gebruikers toegang tot Azure-resources. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Microsoft.Authorization/* | Autorisatie beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="virtual-machine-administrator-login"></a>Beheerdersaanmelding bij virtuele Machine
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Virtual Machines in de portal weer geven en u aanmelden als beheerder |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Acties** |  |
> | Microsoft.Network/publicIPAddresses/read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.Network/loadBalancers/read | Hiermee wordt een load balancer definitie opgehaald |
> | Microsoft.Network/networkInterfaces/read | Hiermee haalt u een definitie van een netwerk interface.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Aanmelden bij een virtuele machine met Windows-beheerders-of Linux-root gebruikers bevoegdheden |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="virtual-machine-contributor"></a>Inzender voor virtuele machines
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Micro soft. Compute/Availability sets/* | Berekenings beschikbaarheids sets maken en beheren |
> | Microsoft.Compute/locations/* | Reken locaties maken en beheren |
> | Microsoft.Compute/virtualMachines/* | Virtuele machines maken en beheren |
> | Microsoft.Compute/virtualMachineScaleSets/* | Schaal sets voor virtuele machines maken en beheren |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Voegt een back-end-adres groep van een toepassings gateway samen. Niet Alertable. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Voegt een load balancer binnenkomende NAT-pool samen. Niet alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | Microsoft.Network/loadBalancers/probes/join/action | Staat het gebruik van tests van een load balancer toe. Met deze machtiging healthProbe eigenschap van de VM-schaalset kan bijvoorbeeld naar de test worden verwezen. Niet alertable. |
> | Microsoft.Network/loadBalancers/read | Hiermee wordt een load balancer definitie opgehaald |
> | Microsoft.Network/locations/* | Netwerk locaties maken en beheren |
> | Microsoft.Network/networkInterfaces/* | Netwerk interfaces maken en beheren |
> | Microsoft.Network/networkSecurityGroups/join/action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Microsoft.Network/networkSecurityGroups/read | Hiermee wordt een definitie van een netwerk beveiligings groep opgehaald |
> | Microsoft.Network/publicIPAddresses/join/action | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | Microsoft.Network/publicIPAddresses/read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Een doel voor back-upbeveiliging maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Hiermee wordt een beveiligings beleid gemaakt |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden de gebruiksgegevens voor een Recovery Services-kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/write | Met de bewerking Kluis maken wordt een Azure-resource van het type vault gemaakt. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="virtual-machine-user-login"></a>Gebruikersaanmelding bij virtuele machine
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Acties** |  |
> | Microsoft.Network/publicIPAddresses/read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.Network/loadBalancers/read | Hiermee wordt een load balancer definitie opgehaald |
> | Microsoft.Network/networkInterfaces/read | Hiermee haalt u een definitie van een netwerk interface.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="web-plan-contributor"></a>Inzender voor webabonnementen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Web/serverFarms/* | Server farms maken en beheren |
> | Micro soft. Web/hostingEnvironments/samen voegen/actie | Voegt een App Service Environment |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

## <a name="website-contributor"></a>Inzender voor websites
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beschrijving** | Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Lees autorisatie |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/components/* | Insights-onderdelen maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Web/certificates/* | Website certificaten maken en beheren |
> | Microsoft.Web/listSitesAssignedToHostName/read | Namen ophalen van sites die zijn toegewezen aan de hostnaam. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | De eigenschappen van een App Service plan ophalen |
> | Microsoft.Web/sites/* | Websites maken en beheren (voor het maken van sites is ook schrijf machtigingen vereist voor het bijbehorende App Service plan) |
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
