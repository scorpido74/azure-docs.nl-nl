---
title: Ingebouwde rollen van Azure voor Azure RBAC
description: Hierin worden de ingebouwde rollen van Azure beschreven voor toegangs beheer op basis van rollen (Azure RBAC). Een lijst met de acties, intact, DataActions en NotDataActions.
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
ms.date: 02/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b7b944880074b64f6a9e66e177082e52632e9c9d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205888"
---
# <a name="azure-built-in-roles"></a>Ingebouwde rollen van Azure

[Toegangs beheer op basis van rollen (Azure RBAC) van](overview.md) Azure heeft verschillende ingebouwde rollen van Azure die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Roltoewijzingen zijn de manier waarop u de toegang tot Azure-resources beheert. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste Azure-rollen](custom-roles.md)maken.

Dit artikel bevat een overzicht van de ingebouwde rollen voor Azure-resources, die altijd in ontwikkeling zijn. Gebruik de lijst met [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) of [AZ Role definition](/cli/azure/role/definition#az-role-definition-list)om de nieuwste rollen op te halen. Als u op zoek bent naar beheerders rollen voor Azure Active Directory, raadpleegt u [machtigingen voor beheerdersrol in azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="descriptions-and-ids"></a>Beschrijvingen en Id's

De volgende tabel bevat een korte beschrijving en de unieke ID van elke ingebouwde rol. Klik op de naam van de rol om de lijst met `Actions`, `NotActions`, `DataActions`en `NotDataActions` voor elke rol weer te geven. Zie voor meer informatie over de werking van deze acties en over de manier waarop deze worden toegepast op het beheer-en gegevens abonnement [begrijpen functie definities voor Azure-resources](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Ingebouwde rol | Beschrijving | Id |
> | --- | --- | --- |
> | [AcrDelete](#acrdelete) | ACR verwijderen | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-installatie kopie ondertekenaar | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR-push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR quarantaine gegevens lezer | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR quarantaine gegevens schrijver | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Inzender van API Management-service](#api-management-service-contributor) | Kan de service en de Api's beheren | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Functie API Management service operator](#api-management-service-operator-role) | Kan de service beheren, maar niet de Api's | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management functie Service lezer](#api-management-service-reader-role) | Alleen-lezen toegang tot de service en Api's | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Eigenaar van app-configuratie gegevens](#app-configuration-data-owner) | Hiermee krijgt u volledige toegang tot de app-configuratie gegevens. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Gegevens lezer app-configuratie](#app-configuration-data-reader) | Hiermee staat u lees toegang toe voor app-configuratie gegevens. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Inzender voor Application Insights onderdelen](#application-insights-component-contributor) | Kan Application Insights onderdelen beheren | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Geeft gebruikers machtigingen voor het weer geven en downloaden van moment opnamen van fout opsporing die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [eigenaar](#owner) of [Inzender](#contributor) . | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Automation-taak operator](#automation-job-operator) | Maak en beheer taken met behulp van Automation-Runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operator](#automation-operator) | Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation-Runbook-operator](#automation-runbook-operator) | Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [AVERE-bijdrager](#avere-contributor) | Kan een avere vFXT-cluster maken en beheren. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [AVERE-operator](#avere-operator) | Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Onboarding van met Azure verbonden computer](#azure-connected-machine-onboarding) | Kan onboarding van met Azure verbonden computers uitvoeren. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Resource beheerder van Azure Connected machine](#azure-connected-machine-resource-administrator) | Kan met Azure verbonden computers lezen, schrijven, verwijderen en onboarden. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Eigenaar van Azure Event Hubs-gegevens](#azure-event-hubs-data-owner) | Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Gegevens ontvanger van Azure Event Hubs](#azure-event-hubs-data-receiver) | Hiermee krijgt u toegang tot Azure Event Hubs-resources. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Afzender van Azure Event Hubs gegevens](#azure-event-hubs-data-sender) | Hiermee wordt toegang tot Azure Event Hubs-resources verzonden. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Rol van Cluster beheerder voor Azure Kubernetes-service](#azure-kubernetes-service-cluster-admin-role) | Lijst met actie voor cluster beheer referenties. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Gebruikersrol Azure Kubernetes service-cluster](#azure-kubernetes-service-cluster-user-role) | Geef een lijst actie voor de gebruikers referenties van het cluster op. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Maps gegevens lezer (preview-versie)](#azure-maps-data-reader-preview) | Hiermee wordt toegang verleend om gerelateerde gegevens te lezen vanuit een Azure Maps-account. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Sentinel-bijdrager](#azure-sentinel-contributor) | Azure Sentinel-bijdrager | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel responder](#azure-sentinel-responder) | Azure Sentinel responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Gegevens eigenaar Azure Service Bus](#azure-service-bus-data-owner) | Hiermee krijgt u volledige toegang tot Azure Service Bus-resources. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Gegevens ontvanger Azure Service Bus](#azure-service-bus-data-receiver) | Hiermee krijgt u toegang tot Azure Service Bus-resources. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Afzender van Azure Service Bus gegevens](#azure-service-bus-data-sender) | Hiermee kunt u toegang tot Azure Service Bus resources verzenden. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Registratie-eigenaar Azure Stack](#azure-stack-registration-owner) | Hiermee kunt u Azure Stack registraties beheren. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Back-upinzender](#backup-contributor) | Hiermee kunt u de back-upservice beheren, maar geen kluizen maken en anderen toegang verlenen | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Back-upoperator](#backup-operator) | Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Back-uplezer](#backup-reader) | Kan back-upservices weer geven, maar kan geen wijzigingen aanbrengen | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Facturerings lezer](#billing-reader) | Hiermee staat u lees toegang tot facturerings gegevens toe | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [BizTalk-bijdrager](#biztalk-contributor) | Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Toegang tot Block Chain-leden knooppunt (preview-versie)](#blockchain-member-node-access-preview) | Hiermee wordt toegang tot Block Chain-leden knooppunten toegestaan | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | [Blauw druk bijdrager](#blueprint-contributor) | Kan blauw drukken-definities beheren, maar niet toewijzen. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blauw druk-operator](#blueprint-operator) | Kan bestaande gepubliceerde blauw drukken toewijzen, maar kan geen nieuwe blauw drukken maken. Opmerking: dit werkt alleen als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Inzender voor CDN-eind punten](#cdn-endpoint-contributor) | Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-eindpunt lezer](#cdn-endpoint-reader) | Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Inzender voor CDN-profiel](#cdn-profile-contributor) | Kan CDN-profielen en de bijbehorende eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profiel lezer](#cdn-profile-reader) | Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Inzender voor klassieke netwerken](#classic-network-contributor) | Hiermee beheert u klassieke netwerken, maar hebt u geen toegang tot de netwerk bestanden. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Inzender voor klassieke opslag accounts](#classic-storage-account-contributor) | Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Service functie voor de sleutel operator voor klassieke opslag accounts](#classic-storage-account-key-operator-service-role) | De sleutel operators voor klassieke opslag accounts zijn toegestaan om sleutels weer te geven en opnieuw te genereren voor klassieke opslag. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Inzender voor klassieke virtuele machines](#classic-virtual-machine-contributor) | Hiermee beheert u klassieke virtuele machines, maar kunt u niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Inzender Cognitive Services](#cognitive-services-contributor) | Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services gegevens lezer (preview-versie)](#cognitive-services-data-reader-preview) | Hiermee kunt u Cognitive Services gegevens lezen. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services gebruiker](#cognitive-services-user) | Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | [Inzender](#contributor) | Hiermee beheert u alles behalve het verlenen van toegang tot resources. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Rol van Cosmos DB-account lezer](#cosmos-db-account-reader-role) | Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB-operator](#cosmos-db-operator) | Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang tot gegevens. Hiermee voor komt u toegang tot account sleutels en verbindings reeksen. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan een terugzet aanvraag indienen voor een Cosmos DB-Data Base of een container voor een account | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Inzender Cost Management](#cost-management-contributor) | Kan kosten bekijken en kosten configuratie beheren (bijvoorbeeld budgetten, exports) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management lezer](#cost-management-reader) | Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exports) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Inzender Data Box](#data-box-contributor) | Hiermee kunt u alles beheren onder Data Box Service, behalve dat anderen toegang verlenen. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box lezer](#data-box-reader) | Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Inzender Data Factory](#data-factory-contributor) | Het maken en beheren van gegevens fabrieken, evenals onderliggende resources. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data Lake Analytics-ontwikkelaar](#data-lake-analytics-developer) | Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Gegevens opschoner](#data-purger) | Kan Analytics-gegevens verwijderen | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [DevTest Labs-gebruiker](#devtest-labs-user) | Met kunt u verbinding maken met uw virtuele machines in uw Azure DevTest Labs, deze starten, opnieuw opstarten en afsluiten. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Inzender DNS-zone](#dns-zone-contributor) | Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Inzender voor DocumentDB-accounts](#documentdb-account-contributor) | Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [EventGrid EventSubscription-bijdrager](#eventgrid-eventsubscription-contributor) | Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-lezer](#eventgrid-eventsubscription-reader) | Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [HDInsight-cluster operator](#hdinsight-cluster-operator) | Hiermee kunt u HDInsight-cluster configuraties lezen en wijzigen. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Inzender voor HDInsight Domain Services](#hdinsight-domain-services-contributor) | Kan gerelateerde bewerkingen die betrekking hebben op domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Inzender voor Intelligent Systems-account](#intelligent-systems-account-contributor) | Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Inzender Key Vault](#key-vault-contributor) | Hiermee kunt u sleutel kluizen beheren, maar niet de toegang tot de kluis. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Lab-Maker](#lab-creator) | Met kunt u uw beheerde Labs maken, beheren en verwijderen in uw Azure Lab-accounts. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | [Inzender Log Analytics](#log-analytics-contributor) | Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics lezer](#log-analytics-reader) | Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Inzender voor logische apps](#logic-app-contributor) | Hiermee kunt u logische apps beheren, maar niet wijzigen. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logische app-operator](#logic-app-operator) | Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | [Rol Inzender beheerde toepassing](#managed-application-contributor-role) | Maakt het mogelijk om beheerde toepassings resources te maken. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rol beheerde toepassings operator](#managed-application-operator-role) | Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lezer voor beheerde toepassingen](#managed-applications-reader) | Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Inzender beheerde identiteit](#managed-identity-contributor) | Aan de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Beheerde identiteits operator](#managed-identity-operator) | Door gebruiker toegewezen identiteit lezen en toewijzen | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Rol voor registratie toewijzing beheerde services verwijderen](#managed-services-registration-assignment-delete-role) | Met de functie voor registratie toewijzing van beheerde services verwijderen kan de Tenant gebruikers beheren de registratie toewijzing verwijderen die aan de Tenant is toegewezen. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Inzender beheer groep](#management-group-contributor) | Rol Inzender beheer groep | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lezer beheer groep](#management-group-reader) | Rol van lezer van beheer groep | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Inzender bewaken](#monitoring-contributor) | Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [De uitgever van metrische gegevens controleren](#monitoring-metrics-publisher) | Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Bewakings lezer](#monitoring-reader) | Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Inzender voor netwerken](#network-contributor) | Hiermee kunt u netwerken beheren, maar niet de toegang tot de netwerk bestanden. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Nieuwe Inzender voor Relic APM-account](#new-relic-apm-account-contributor) | Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Eigenaar](#owner) | Hiermee kunt u alles beheren, inclusief toegang tot resources. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Policy Insights Data Writer (preview-versie)](#policy-insights-data-writer-preview) | Hiermee wordt lees toegang tot bron beleid en schrijf toegang tot bron onderdeel beleids gebeurtenissen toegestaan. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Lezer](#reader) | Hiermee kunt u alles weer geven, maar geen wijzigingen aanbrengen. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Lezer en gegevens toegang](#reader-and-data-access) | Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Inzender Redis Cache](#redis-cache-contributor) | Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Inzender voor resource beleid](#resource-policy-contributor) | Gebruikers met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Inzender voor scheduler-taak verzamelingen](#scheduler-job-collections-contributor) | Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Inzender Search Service](#search-service-contributor) | Hiermee kunt u zoek services beheren, maar niet de toegang tot ze. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Beveiligings beheerder](#security-admin) | Kan beveiligings beleid weer geven, beveiligings statussen bekijken, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Inzender voor beveiligings beoordeling](#security-assessment-contributor) | Hiermee kunt u evaluaties naar Security Center pushen | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Beveiligings beheer (verouderd)](#security-manager-legacy) | Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Beveiligings lezer](#security-reader) | Kan aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar kan geen wijzigingen aanbrengen. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Inzender Site Recovery](#site-recovery-contributor) | Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-operator](#site-recovery-operator) | Een failover en failback, maar geen andere Site Recovery beheer bewerkingen uitvoeren | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery lezer](#site-recovery-reader) | Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Inzender voor ruimtelijke ankers](#spatial-anchors-account-contributor) | Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Eigenaar van ruimtelijk ankers](#spatial-anchors-account-owner) | Hiermee kunt u ruimtelijke ankers in uw account beheren, met inbegrip van het verwijderen ervan | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Lezer van ruimtelijk ankers-account](#spatial-anchors-account-reader) | Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [Inzender voor SQL-data base](#sql-db-contributor) | Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Inzender voor SQL Managed instance](#sql-managed-instance-contributor) | Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-beveiligings beheer](#sql-security-manager) | Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Inzender SQL Server](#sql-server-contributor) | Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Inzender voor opslagaccounts](#storage-account-contributor) | Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Functie Service-sleutel operator van opslag account](#storage-account-key-operator-service-role) | Kan de toegangs sleutels voor het opslag account weer geven en opnieuw genereren. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Inzender voor Storage BLOB-gegevens](#storage-blob-data-contributor) | Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Eigenaar van gegevens van opslag-BLOB](#storage-blob-data-owner) | Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Gegevens lezer van BLOB voor opslag](#storage-blob-data-reader) | Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegering van opslag-BLOB](#storage-blob-delegator) | Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Inzender voor opslag bestands gegevens SMB delen](#storage-file-data-smb-share-contributor) | Hiermee wordt lees-, schrijf-en verwijder toegang voor bestanden/mappen in azure-bestands shares toegestaan. Deze rol heeft geen ingebouwd equivalent op Windows-bestands servers. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Opslag bestands gegevens SMB-share verhoogde Inzender](#storage-file-data-smb-share-elevated-contributor) | Hiermee kunt u Acl's voor lezen, schrijven, verwijderen en wijzigen van bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL van de bestands share die moet worden gewijzigd op Windows-bestands servers. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Reader voor gegevens van SMB-share voor opslag bestand](#storage-file-data-smb-share-reader) | Hiermee staat u lees toegang toe voor bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL voor bestands shares van lezen op Windows-bestands servers. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Inzender voor opslag wachtrij gegevens](#storage-queue-data-contributor) | Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processor voor gegevens berichten van de opslag wachtrij](#storage-queue-data-message-processor) | Een bericht uit een Azure Storage wachtrij bekijken, ophalen en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Afzender gegevens bericht van opslag wachtrij](#storage-queue-data-message-sender) | Berichten toevoegen aan een Azure Storage wachtrij. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Gegevens lezer van de opslag wachtrij](#storage-queue-data-reader) | Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Inzender voor ondersteunings aanvragen](#support-request-contributor) | Hiermee kunt u ondersteunings aanvragen maken en beheren | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Inzender Traffic Manager](#traffic-manager-contributor) | Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | [Beheerder van gebruikerstoegang](#user-access-administrator) | Hiermee beheert u de gebruikers toegang tot Azure-resources. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | [Aanmelding voor de beheerder van de virtuele machine](#virtual-machine-administrator-login) | Virtual Machines in de portal weer geven en u aanmelden als beheerder | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Inzender voor virtuele machines](#virtual-machine-contributor) | Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Gebruikers aanmelding voor de virtuele machine](#virtual-machine-user-login) | Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | [Inzender voor webabonnementen](#web-plan-contributor) | Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Website bijdrager](#website-contributor) | Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | [Inzender voor werkmappen](#workbook-contributor) | Kan gedeelde werkmappen opslaan. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Werkmap lezer](#workbook-reader) | Kan werkmappen lezen. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |


## <a name="general"></a>Algemeen


### <a name="contributor"></a>Inzender

Hiermee beheert u alles behalve het verlenen van toegang tot resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | * | Resources van alle typen maken en beheren |
> | **Intact** |  |
> | Microsoft.Authorization/*/Delete | Rollen, beleids toewijzingen, beleids definities en definities van beleids sets verwijderen |
> | Microsoft.Authorization/*/Write | Rollen, roltoewijzingen, beleids toewijzingen, beleids definities en definities van beleids sets maken |
> | Microsoft.Authorization/elevateAccess/Action | Hiermee wordt de gebruiker toegang tot beheerders verleend in het Tenant bereik |
> | Microsoft.Blueprint/blueprintAssignments/write | Een blauw druk-toewijzing maken of bijwerken |
> | Microsoft.Blueprint/blueprintAssignments/delete | Eventuele blauw drukken-toewijzingen verwijderen |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Eigenaar

Hiermee kunt u alles beheren, inclusief toegang tot resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | * | Resources van alle typen maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Lezer

Hiermee kunt u alles weer geven, maar geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Beheerder van gebruikerstoegang

Hiermee beheert u de gebruikers toegang tot Azure-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + machine learning


### <a name="cognitive-services-contributor"></a>Inzender Cognitive Services

Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.CognitiveServices/* |  |
> | Micro soft. features/onderdelen/lezen | Hiermee haalt u de functies van een abonnement op. |
> | Microsoft.Features/providers/features/read | Hiermee wordt de functie van een abonnement in een bepaalde resource provider opgehaald. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Microsoft.Insights/logDefinitions/read | Logboek definities lezen |
> | Micro soft. Insights/metricdefinitions/lezen | Metrische definities lezen |
> | Microsoft.Insights/metrics/read | Metrische gegevens lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services gegevens lezer (preview-versie)

Hiermee kunt u Cognitive Services gegevens lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services gebruiker

Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Lijst met sleutels |
> | Microsoft.Insights/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | Micro soft. Insights/diagnosticSettings/lezen | Een diagnostische instelling voor bronnen lezen |
> | Microsoft.Insights/logDefinitions/read | Logboek definities lezen |
> | Micro soft. Insights/metricdefinitions/lezen | Metrische definities lezen |
> | Microsoft.Insights/metrics/read | Metrische gegevens lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytische gegevens


### <a name="azure-event-hubs-data-owner"></a>Eigenaar van Azure Event Hubs-gegevens

Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.EventHub/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Gegevens ontvanger van Azure Event Hubs

Hiermee krijgt u toegang tot Azure Event Hubs-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Afzender van Azure Event Hubs gegevens

Hiermee wordt toegang tot Azure Event Hubs-resources verzonden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Inzender Data Factory

Het maken en beheren van gegevens fabrieken, evenals onderliggende resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.DataFactory/dataFactories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | Microsoft.DataFactory/factories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.EventGrid/eventSubscriptions/write | Een eventSubscription maken of bijwerken |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Gegevens opschoner

Kan Analytics-gegevens verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Gegevens uit Application Insights verwijderen |
> | Microsoft.OperationalInsights/workspaces/*/read | Log Analytics-gegevens weer geven |
> | Microsoft.OperationalInsights/workspaces/purge/action | Opgegeven gegevens uit de werk ruimte verwijderen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight-cluster operator

Hiermee kunt u HDInsight-cluster configuraties lezen en wijzigen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Inzender voor HDInsight Domain Services

Kan gerelateerde bewerkingen die betrekking hebben op domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Inzender van Log Analytics

Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Lezer van Log Analytics

Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Toegang tot Block Chain-leden knooppunt (preview-versie)

Hiermee wordt toegang tot Block Chain-leden knooppunten toegestaan

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hiermee wordt een lijst met bestaande Block Chain-leden transactie knooppunten opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Maakt verbinding met een trans actie-knoop punt van het block Chain-lid. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Inzender voor klassieke virtuele machines

Hiermee beheert u klassieke virtuele machines, maar kunt u niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Micro soft. ClassicCompute/domainName/* | Klassieke Compute-domein namen maken en beheren |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuele machines maken en beheren |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Een gereserveerd IP-adres koppelen |
> | Microsoft.ClassicNetwork/reservedIps/read | Hiermee worden de gereserveerde Ip's opgehaald |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they’re connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Aanmelding voor de beheerder van de virtuele machine

Virtual Machines in de portal weer geven en u aanmelden als beheerder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Inzender voor virtuele machines

Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Micro soft. Compute/Availability sets/* | Berekenings beschikbaarheids sets maken en beheren |
> | Microsoft.Compute/locations/* | Reken locaties maken en beheren |
> | Microsoft.Compute/virtualMachines/* | Virtuele machines maken en beheren |
> | Microsoft.Compute/virtualMachineScaleSets/* | Schaal sets voor virtuele machines maken en beheren |
> | Microsoft.Compute/disks/write | Hiermee wordt een nieuwe schijf gemaakt of een bestaande bijgewerkt |
> | Microsoft.Compute/disks/read | De eigenschappen van een schijf ophalen |
> | Microsoft.Compute/disks/delete | Hiermee wordt de schijf verwijderd |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/write | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Gebruikers aanmelding voor de virtuele machine

Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

ACR verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Artefact verwijderen in container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR-installatie kopie ondertekenaar

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/ondertekenen/schrijven | Meta gegevens van de vertrouwens relatie push/pull voor een container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

ACR pull

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pull of ophalen van installatie kopieën uit een container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

ACR-push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pull of ophalen van installatie kopieën uit een container register. |
> | Microsoft.ContainerRegistry/registries/push/write | Push of schrijf installatie kopieën naar een container register. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR quarantaine gegevens lezer

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/quarantaine/lezen | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR quarantaine gegevens schrijver

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. ContainerRegistry/registers/quarantaine/lezen | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | Micro soft. ContainerRegistry/registers/quarantaine/schrijven | De quarantaine status van in quarantaine geplaatste installatie kopieën schrijven/wijzigen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Rol van Cluster beheerder voor Azure Kubernetes-service

Lijst met actie voor cluster beheer referenties.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | De clusterAdmin-referentie van een beheerd cluster weer geven |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Een beheerd cluster toegangs profiel verkrijgen met een rolnaam met behulp van de lijst referentie |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Gebruikersrol Azure Kubernetes service-cluster

Geef een lijst actie voor de gebruikers referenties van het cluster op.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | De clusterUser-referentie van een beheerd cluster weer geven |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Databases


### <a name="cosmos-db-account-reader-role"></a>Rol van Cosmos DB-account lezer

Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.DocumentDB/*/read | Een verzameling lezen |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Microsoft.Insights/MetricDefinitions/read | Metrische definities lezen |
> | Microsoft.Insights/Metrics/read | Metrische gegevens lezen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB-operator

Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang tot gegevens. Hiermee voor komt u toegang tot account sleutels en verbindings reeksen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | **Intact** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Kan een terugzet aanvraag indienen voor een Cosmos DB-Data Base of een container voor een account

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Een aanvraag indienen voor het configureren van de back-up |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Een herstel aanvraag verzenden |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Inzender voor DocumentDB-accounts

Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB accounts maken en beheren |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Inzender Redis Cache

Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Cache/redis/* | Redis-caches maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Inzender voor SQL-data base

Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/locaties/*/Read |  |
> | Microsoft.Sql/servers/databases/* | SQL-data bases maken en beheren |
> | Microsoft.Sql/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Insights/metrics/read | Metrische gegevens lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Inzender voor SQL Managed instance

Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.Insights/metrics/read | Metrische gegevens lezen |
> | Micro soft. Insights/metricDefinitions/lezen | Metrische definities lezen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can’t give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL-beveiligings beheer

Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | De data base-BLOB-controle records ophalen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Inzender SQL Server

Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Micro soft. SQL/locaties/*/Read |  |
> | Microsoft.Sql/servers/* | SQL-servers maken en beheren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Insights/metrics/read | Metrische gegevens lezen |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | De data base-BLOB-controle records ophalen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="developer-tools"></a>Hulpprogramma's voor ontwikkelaars


### <a name="app-configuration-data-owner"></a>Eigenaar van app-configuratie gegevens

Hiermee krijgt u volledige toegang tot de app-configuratie gegevens.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. AppConfiguration/configurationStores/*/Read |  |
> | Micro soft. AppConfiguration/configurationStores/*/write |  |
> | Micro soft. AppConfiguration/configurationStores/*/Delete |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Gegevens lezer app-configuratie

Hiermee staat u lees toegang toe voor app-configuratie gegevens.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. AppConfiguration/configurationStores/*/Read |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab-Maker

Met kunt u uw beheerde Labs maken, beheren en verwijderen in uw Azure Lab-accounts.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Een lab maken in een Lab-account. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Informatie over regionale Beschik baarheid ophalen voor elke grootte categorie die is geconfigureerd met een Lab-account |
> | Micro soft. LabServices/labAccounts/getPricingAndAvailability/Action | Profiteer van de prijzen en beschik baarheid van combi Naties van groottes, geografi en besturings systemen voor het lab-account. |
> | Micro soft. LabServices/labAccounts/getRestrictionsAndUsage/Action | Kern beperkingen en gebruik voor dit abonnement ophalen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs-gebruiker

Met kunt u verbinding maken met uw virtuele machines in uw Azure DevTest Labs, deze starten, opnieuw opstarten en afsluiten.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Compute/availabilitySets/read | De eigenschappen van een beschikbaarheidsset ophalen |
> | Microsoft.Compute/virtualMachines/*/read | De eigenschappen van een virtuele machine lezen (VM-grootte, runtime status, VM-extensies, enzovoort) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Hiermee wordt de virtuele machine uitgeschakeld en worden de reken resources vrijgegeven |
> | Microsoft.Compute/virtualMachines/read | De eigenschappen van een virtuele machine ophalen |
> | Microsoft.Compute/virtualMachines/restart/action | Hiermee wordt de virtuele machine opnieuw opgestart |
> | Microsoft.Compute/virtualMachines/start/action | De virtuele machine wordt gestart |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="hybrid"></a>Hybride


### <a name="azure-connected-machine-onboarding"></a>Onboarding van met Azure verbonden computer

Kan onboarding van met Azure verbonden computers uitvoeren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. HybridCompute/machines/lezen | Alle Azure-Arc-machines lezen |
> | Micro soft. HybridCompute/machines/schrijven | Schrijft een Azure-Arc-machine |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Toewijzing van gast configuratie ophalen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Resource beheerder van Azure Connected machine

Kan met Azure verbonden computers lezen, schrijven, verwijderen en onboarden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. HybridCompute/machines/lezen | Alle Azure-Arc-machines lezen |
> | Micro soft. HybridCompute/machines/schrijven | Schrijft een Azure-Arc-machine |
> | Micro soft. HybridCompute/machines/verwijderen | Hiermee wordt een Azure-Arc-machine verwijderd |
> | Micro soft. HybridCompute/machines/opnieuw verbinden/actie | Opnieuw verbinding maken met een Azure-Arc-computer |
> | Micro soft. HybridCompute/*/Read |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Registratie-eigenaar Azure Stack

Hiermee kunt u Azure Stack registraties beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identiteit


### <a name="managed-identity-contributor"></a>Inzender beheerde identiteit

Aan de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Beheerde identiteits operator

Door gebruiker toegewezen identiteit lezen en toewijzen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integratie


### <a name="api-management-service-contributor"></a>Inzender van API Management-service

Kan de service en de Api's beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/* | API Management-service maken en beheren |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Functie API Management service operator

Kan de service beheren, maar niet de Api's

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/*/read | API Management service-exemplaren lezen |
> | Microsoft.ApiManagement/service/backup/action | Backup API Management-service naar de opgegeven container in een door de gebruiker opgegeven opslag account |
> | Microsoft.ApiManagement/service/delete | API Management service-exemplaar verwijderen |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/eenheden wijzigen, regionale implementaties van API Management service toevoegen/verwijderen |
> | Microsoft.ApiManagement/service/read | Meta gegevens voor een API Management service-exemplaar lezen |
> | Microsoft.ApiManagement/service/restore/action | De API Management-service herstellen vanuit de opgegeven container in een opslag account dat door de gebruiker is opgegeven |
> | Microsoft.ApiManagement/service/updatecertificate/action | SSL-certificaat voor een API Management-service uploaden |
> | Microsoft.ApiManagement/service/updatehostname/action | Aangepaste domein namen instellen, bijwerken of verwijderen voor een API Management-service |
> | Microsoft.ApiManagement/service/write | API Management service-exemplaar maken of bijwerken |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management functie Service lezer

Alleen-lezen toegang tot de service en Api's

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/*/read | API Management service-exemplaren lezen |
> | Microsoft.ApiManagement/service/read | Meta gegevens voor een API Management service-exemplaar lezen |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Gegevens eigenaar Azure Service Bus

Hiermee krijgt u volledige toegang tot Azure Service Bus-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ServiceBus/* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Gegevens ontvanger Azure Service Bus

Hiermee krijgt u toegang tot Azure Service Bus-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Afzender van Azure Service Bus gegevens

Hiermee kunt u toegang tot Azure Service Bus resources verzenden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Inzender voor Intelligent Systems-account

Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Inzender voor logische apps

Hiermee kunt u logische apps beheren, maar niet wijzigen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retour neer het opslag account met het opgegeven account. |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Micro soft. Insights/metricAlerts/* |  |
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
> | Microsoft.Web/sites/functions/listSecrets/action | Lijst met functie geheimen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logische app-operator

Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/*/read | Inzichten-waarschuwings regels lezen |
> | Micro soft. Insights/metricAlerts/*/Read |  |
> | Micro soft. Insights/diagnosticSettings/*/Read | Hiermee worden Diagnostische instellingen voor Logic Apps opgehaald |
> | Micro soft. Insights/metricDefinitions/*/Read | Hiermee worden de beschik bare metrische gegevens opgehaald voor Logic Apps. |
> | Microsoft.Logic/*/read | Hiermee worden Logic Apps resources gelezen. |
> | Microsoft.Logic/workflows/disable/action | Hiermee schakelt u de werk stroom uit. |
> | Microsoft.Logic/workflows/enable/action | Hiermee wordt de werkstroom ingeschakeld. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Internet der dingen


### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-bijdrager

Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-lezer

Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Beheer + governance


### <a name="application-insights-component-contributor"></a>Inzender voor Application Insights onderdelen

Kan Application Insights onderdelen beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Klassieke waarschuwings regels maken en beheren |
> | Micro soft. Insights/metricAlerts/* | Nieuwe waarschuwings regels maken en beheren |
> | Microsoft.Insights/components/* | Insights-onderdelen maken en beheren |
> | Microsoft.Insights/webtests/* | Inzichten-webtests maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Geeft gebruikers machtigingen voor het weer geven en downloaden van moment opnamen van fout opsporing die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [eigenaar](#owner) of [Inzender](#contributor) .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-job-operator"></a>Automation-taak operator

Maak en beheer taken met behulp van Automation-Runbooks.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation-operator

Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation-Runbook-operator

Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Lezer voor facturering

Hiermee staat u lees toegang tot facturerings gegevens toe

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Blauw druk bijdrager

Kan blauw drukken-definities beheren, maar niet toewijzen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Blauw druk-operator

Kan bestaande gepubliceerde blauw drukken toewijzen, maar kan geen nieuwe blauw drukken maken. 

> [!NOTE] 
> Dit werkt alleen als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management-inzender

Kan kosten bekijken en kosten configuratie beheren (bijvoorbeeld budgetten, exports)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management lezer

Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exports)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rol Inzender beheerde toepassing

Maakt het mogelijk om beheerde toepassings resources te maken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Micro soft. Solutions/Applications/* |  |
> | Micro soft. Solutions/REGI ster/actie | Registreren bij oplossingen. |
> | Micro soft. resources/abonnementen/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Rol beheerde toepassings operator

Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Lezer voor beheerde toepassingen

Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Rol voor registratie toewijzing beheerde services verwijderen

Met de functie voor registratie toewijzing van beheerde services verwijderen kan de Tenant gebruikers beheren de registratie toewijzing verwijderen die aan de Tenant is toegewezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Inzender beheer groep

Rol Inzender beheer groep

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Lezer beheer groep

Rol van lezer van beheer groep

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Inzender bewaken

Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/components/* | Insights-onderdelen maken en beheren |
> | Microsoft.Insights/DiagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | Microsoft.Insights/eventtypes/* | Lijst activiteiten logboek gebeurtenissen (beheer gebeurtenissen) in een abonnement. Deze machtiging is van toepassing op zowel toegang via het programma als de portal tot het activiteiten logboek. |
> | Microsoft.Insights/LogDefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | Micro soft. Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | Microsoft.Insights/Metrics/* | Meet gegevens voor een resource lezen. |
> | Microsoft.Insights/Register/Action | De micro soft Insights-provider registreren |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Inzichten-webtests maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>De uitgever van metrische gegevens controleren

Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Insights/Register/Action | De micro soft Insights-provider registreren |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Metrische gegevens schrijven |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Bewakings lezer

Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Nieuwe Inzender voor Relic APM-account

Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (preview-versie)

Hiermee wordt lees toegang tot bron beleid en schrijf toegang tot bron onderdeel beleids gebeurtenissen toegestaan.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. Authorization/policyassignments/lezen | Informatie over een beleids toewijzing ophalen. |
> | Micro soft. Authorization/policydefinitions/lezen | Informatie over een beleids definitie ophalen. |
> | Micro soft. Authorization/policysetdefinitions/lezen | Informatie over een definitie van een beleidsset ophalen. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. PolicyInsights/checkDataPolicyCompliance/actie | Controleer de nalevings status van een bepaald onderdeel met gegevens beleid. |
> | Micro soft. PolicyInsights/policyEvents/logDataEvents/Action | De gebeurtenissen van het bron onderdeel beleid registreren. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Inzender voor resourcebeleid

Gebruikers met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Inzender voor scheduler-taak verzamelingen

Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Inzender Site Recovery

Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Micro soft. Recovery Services/kluizen/replicationOperationStatus/lezen | De status van de kluis replicatie bewerking lezen |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery-operator

Een failover en failback, maar geen andere Site Recovery beheer bewerkingen uitvoeren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Waarschuwings instellingen lezen |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Gebeurtenissen lezen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Hiermee wordt de consistentie van de infra structuur gecontroleerd |
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
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Beveiligings container overschakelen |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery lezer

Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Inzender voor ondersteunings aanvragen

Hiermee kunt u ondersteunings aanvragen maken en beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Inzender voor werkmappen

Kan gedeelde werkmappen opslaan.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. Insights/werkmappen/schrijven | Een werkmap maken of bijwerken |
> | Micro soft. Insights/werkmappen/verwijderen | Een werkmap verwijderen |
> | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Werkmap lezer

Kan werkmappen lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Mixed reality


### <a name="spatial-anchors-account-contributor"></a>Inzender voor ruimtelijke ankers

Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Eigenaar van ruimtelijk ankers

Hiermee kunt u ruimtelijke ankers in uw account beheren, met inbegrip van het verwijderen ervan

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Lezer van ruimtelijk ankers-account

Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Netwerken


### <a name="cdn-endpoint-contributor"></a>Inzender voor CDN-eind punten

Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can’t grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-eindpunt lezer

Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can’t make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Inzender voor CDN-profiel

Kan CDN-profielen en de bijbehorende eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can’t grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN-profiel lezer

Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can’t make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Inzender voor klassieke netwerken

Hiermee beheert u klassieke netwerken, maar hebt u geen toegang tot de netwerk bestanden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Inzender DNS-zone

Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Inzender voor netwerken

Hiermee kunt u netwerken beheren, maar niet de toegang tot de netwerk bestanden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Inzender Traffic Manager

Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Beveiliging


### <a name="azure-sentinel-contributor"></a>Azure Sentinel-bijdrager

Azure Sentinel-bijdrager

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met een nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/read | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | De OMS-oplossing ophalen |
> | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Micro soft. OperationalInsights/werk ruimten/query/*/Read |  |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. Insights/werkmappen/* |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel Reader

Azure Sentinel Reader

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. SecurityInsights/*/Read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met een nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/read | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | Microsoft.OperationsManagement/solutions/read | De OMS-oplossing ophalen |
> | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Micro soft. OperationalInsights/werk ruimten/query/*/Read |  |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel responder

Azure Sentinel responder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. SecurityInsights/*/Read |  |
> | Micro soft. SecurityInsights/cases/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met een nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/read | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | Microsoft.OperationsManagement/solutions/read | De OMS-oplossing ophalen |
> | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Micro soft. OperationalInsights/werk ruimten/query/*/Read |  |
> | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Inzender Key Vault

Hiermee kunt u sleutel kluizen beheren, maar niet de toegang tot de kluis.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Beveiligingsbeheerder

Kan beveiligings beleid weer geven, beveiligings statussen bekijken, beveiligings beleid bewerken, waarschuwingen en aanbevelingen weer geven, waarschuwingen en aanbevelingen negeren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Authorization/policyAssignments/* | Beleids toewijzingen maken en beheren |
> | Micro soft. Authorization/policyDefinitions/* | Beleids definities maken en beheren |
> | Microsoft.Authorization/policySetDefinitions/* | Beleids sets maken en beheren |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-gegevens weer geven |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Inzender voor beveiligings beoordeling

Hiermee kunt u evaluaties naar Security Center pushen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Micro soft. Security/beoordelingen/schrijven | Beveiligings beoordelingen maken of bijwerken voor uw abonnement |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Beveiligings beheer (verouderd)

Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicCompute/*/read | Configuratie-informatie voor klassieke virtuele machines lezen |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Configuratie voor klassieke virtuele machines schrijven |
> | Microsoft.ClassicNetwork/*/read | Configuratie-informatie over klassiek netwerk lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Beveiligings lezer

Kan aanbevelingen en waarschuwingen weer geven, beveiligings beleid weer geven, beveiligings status weer geven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Opslag


### <a name="avere-contributor"></a>AVERE-bijdrager

Kan een avere vFXT-cluster maken en beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.Storage/storageAccounts/* | Opslagaccounts maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>AVERE-operator

Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Hiermee wordt een lijst met containers opgehaald |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourneert het resultaat van de put-BLOB-container |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourneert een BLOB of een lijst met blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Back-upinzender

Hiermee kunt u de back-upservice beheren, maar geen kluizen maken en anderen toegang verlenen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Resultaten van de werking van het back-upbeheer beheren |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Back-upcontainers maken en beheren in back-upinfrastructuur van Recovery Services kluis |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Hiermee vernieuwt u de container lijst |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
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
> | Microsoft.RecoveryServices/Vaults/write | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Back-upoperator

Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Hiermee wordt de status van de bewerking geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Maakt een back-up voor het beveiligde item. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Direct-item herstel inrichten voor beveiligd item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstel punten voor beveiligde items ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Herstel punten voor beveiligde items herstellen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Herstel van onmiddellijke items intrekken voor beveiligd item |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Hiermee worden alle geregistreerde containers geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Hiermee vernieuwt u de container lijst |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Resultaten van beleids bewerking ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Hiermee worden alle containers van het abonnement geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | De bewerking service container registreren kan worden gebruikt om een container te registreren bij de Recovery-service. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Microsoft.Resources/deployments/* | Implementaties van resource groepen maken en beheren |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Bewerking voor beveiligd item valideren |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleids bewerking ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Hiermee maakt u een geregistreerde container |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Een query uitvoeren voor werk belastingen binnen een container |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Back-uplezer

Kan back-upservices weer geven, maar kan geen wijzigingen aanbrengen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Hiermee wordt de status van de bewerking geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstel punten voor beveiligde items ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Hiermee worden alle geregistreerde containers geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Hiermee wordt het resultaat van de taak bewerking geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Hiermee worden alle taak objecten geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retourneert een back-upbewerkings resultaat voor Recovery Services kluis. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Resultaten van beleids bewerking ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Hiermee worden alle containers van het abonnement geretourneerd |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Hiermee wordt de opslag configuratie voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Hiermee wordt de configuratie voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleids bewerking ophalen. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Een doel voor back-upbeveiliging verkrijgen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Alle items in een container ophalen |
> | Microsoft.RecoveryServices/locations/backupStatus/action | De back-upstatus voor Recovery Services kluizen controleren |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties van de back-upbeveiliging weer geven |
> | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Inzender voor klassieke opslag accounts

Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Service functie voor de sleutel operator voor klassieke opslag accounts

De sleutel operators voor klassieke opslag accounts zijn toegestaan om sleutels weer te geven en opnieuw te genereren voor klassieke opslag.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Hiermee worden de bestaande toegangs sleutels voor het opslag account opnieuw gegenereerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Inzender Data Box

Hiermee kunt u alles beheren onder Data Box Service, behalve dat anderen toegang verlenen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box lezer

Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Geeft een lijst van de niet-versleutelde referenties die zijn gerelateerd aan de order. |
> | Microsoft.Databox/locations/availableSkus/action | Met deze methode wordt de lijst met beschik bare sku's geretourneerd. |
> | Micro soft. Databox/locaties/validateInputs/actie | Met deze methode worden alle typen validaties ondersteund. |
> | Micro soft. Databox/locaties/regionConfiguration/actie | Deze methode retourneert de configuraties voor de regio. |
> | Microsoft.Databox/locations/validateAddress/action | Valideert het verzend adres en levert eventueel alternatieve adressen. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Microsoft.Support/* | Ondersteunings tickets maken en beheren |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics-ontwikkelaar

Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Lezer en gegevens toegang

Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Inzender voor opslag accounts

Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Functie Service-sleutel operator van opslag account

Kan de toegangs sleutels voor het opslag account weer geven en opnieuw genereren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Hiermee worden de toegangs sleutels voor het opgegeven opslag account opnieuw gegenereerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Inzender voor Storage BLOB-gegevens

Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Eigenaar van gegevens van opslag-BLOB

Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Volledige machtigingen voor containers. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Volledige machtigingen op blobs. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Gegevens lezer van BLOB voor opslag

Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Een container of een lijst met containers retour neren. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Een BLOB of een lijst met blobs retour neren. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegering van opslag-BLOB

Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Inzender voor opslag bestands gegevens SMB delen

Hiermee wordt lees-, schrijf-en verwijder toegang voor bestanden/mappen in azure-bestands shares toegestaan. Deze rol heeft geen ingebouwd equivalent op Windows-bestands servers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Opslag bestands gegevens SMB-share verhoogde Inzender

Hiermee kunt u Acl's voor lezen, schrijven, verwijderen en wijzigen van bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL van de bestands share die moet worden gewijzigd op Windows-bestands servers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Reader voor gegevens van SMB-share voor opslag bestand

Hiermee staat u lees toegang toe voor bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL voor bestands shares van lezen op Windows-bestands servers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Inzender voor opslag wachtrij gegevens

Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processor voor gegevens berichten van de opslag wachtrij

Een bericht uit een Azure Storage wachtrij bekijken, ophalen en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een bericht bekijken. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Een bericht ophalen en verwijderen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Afzender gegevens bericht van opslag wachtrij

Berichten toevoegen aan een Azure Storage wachtrij. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Een bericht toevoegen aan een wachtrij. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Gegevens lezer van de opslag wachtrij

Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Hiermee wordt een wachtrij of een lijst met wacht rijen geretourneerd. |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader-preview"></a>Azure Maps gegevens lezer (preview-versie)

Hiermee wordt toegang verleend om gerelateerde gegevens te lezen vanuit een Azure Maps-account.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **Intact** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Hiermee wordt het lezen van gegevens toegang verleend aan een Maps-account. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Inzender Search Service

Hiermee kunt u zoek services beheren, maar niet de toegang tot ze.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRules/* | Insights-waarschuwings regels maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Inzender voor webabonnementen

Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Website bijdrager

Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Overige


### <a name="biztalk-contributor"></a>BizTalk-bijdrager

Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services maken en beheren |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Volgende stappen

- [Overeenkomende resource provider voor service](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Machtigingen in Azure Security Center](../security-center/security-center-permissions.md)
