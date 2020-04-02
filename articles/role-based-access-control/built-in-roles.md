---
title: Azure ingebouwde rollen voor Azure RBAC
description: In dit artikel worden de ingebouwde Azure-rollen voor RBAC (Azure role-based access control) beschreven. Het bevat acties, notactions, dataactions en NotDataActions.
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
ms.date: 03/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 2fe3b94463da07304f2c853910ac5d2a6771d070
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545646"
---
# <a name="azure-built-in-roles"></a>Ingebouwde azure-rollen

[RBAC (Azure role-based access control)](overview.md) heeft verschillende Azure-ingebouwde rollen die u toewijzen aan gebruikers, groepen, serviceprincipals en beheerde identiteiten. Roltoewijzingen zijn de manier waarop u de toegang tot Azure-bronnen beheert. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, u uw eigen [aangepaste Azure-rollen maken.](custom-roles.md)

In dit artikel worden de ingebouwde rollen voor Azure-resources weergegeven, die altijd in ontwikkeling zijn. Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) of [de AZ-roldefinitielijst](/cli/azure/role/definition#az-role-definition-list)om de nieuwste rollen te krijgen. Zie [Beheerdersrolmachtigingen in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)als u op zoek bent naar beheerdersrollen voor Azure Active Directory (Azure AD).

## <a name="all"></a>Alle

De volgende tabel bevat een korte beschrijving en de unieke ID van elke ingebouwde rol. Selecteer de rolnaam die `Actions`de `NotActions` `DataActions`lijst `NotDataActions` van , , en voor elke rol moet worden weergegeven. Zie [Functiedefinities voor Azure-resources begrijpen voor](role-definitions.md)informatie over wat deze acties betekenen en hoe deze van toepassing zijn op de beheer- en gegevensvlakken.


> [!div class="mx-tableFixed"]
> | Ingebouwde rol | Beschrijving | Id |
> | --- | --- | --- |
> | **Algemeen** |  |  |
> | [Inzender](#contributor) | Hiermee u alles beheren, behalve toegang verlenen tot bronnen. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Eigenaar](#owner) | Hiermee u alles beheren, inclusief toegang tot bronnen. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Lezer](#reader) | Hiermee u alles bekijken, maar geen wijzigingen aanbrengen. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Beheerder voor gebruikerstoegang](#user-access-administrator) | Hiermee u gebruikerstoegang tot Azure-bronnen beheren. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Klassieke virtuele machine bijdrager](#classic-virtual-machine-contributor) | Hiermee u klassieke virtuele machines beheren, maar geen toegang tot deze machines en niet het virtuele netwerk- of opslagaccount waarmee ze zijn verbonden. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Inloggen voor virtuele machinebeheerders](#virtual-machine-administrator-login) | Virtuele machines in de portal bekijken en inloggen als beheerder | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Inzender voor virtuele machines](#virtual-machine-contributor) | Hiermee u virtuele machines beheren, maar geen toegang tot deze machines en niet het virtuele netwerk- of opslagaccount waarmee ze zijn verbonden. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Inloggen voor virtuele machine-gebruikers](#virtual-machine-user-login) | Bekijk virtuele machines in de portal en log in als een gewone gebruiker. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Netwerken** |  |  |
> | [CDN-eindpuntbijdrager](#cdn-endpoint-contributor) | Kan CDN-eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-eindpuntlezer](#cdn-endpoint-reader) | Kan CDN-eindpunten weergeven, maar kan geen wijzigingen aanbrengen. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Cdn-profielbijdrager](#cdn-profile-contributor) | Kan CDN-profielen en hun eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profiellezer](#cdn-profile-reader) | Kan CDN-profielen en hun eindpunten bekijken, maar kan geen wijzigingen aanbrengen. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Inzender voor klassieke netwerken](#classic-network-contributor) | Hiermee u klassieke netwerken beheren, maar er geen toegang toe hebben. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS-zonebijdrager](#dns-zone-contributor) | Hiermee u DNS-zones beheren en sets opnemen in Azure DNS, maar u niet bepalen wie er toegang toe heeft. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Inzender voor netwerken](#network-contributor) | Hiermee u netwerken beheren, maar er geen toegang toe hebben. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Medewerker verkeersmanager](#traffic-manager-contributor) | Hiermee u Traffic Manager-profielen beheren, maar u niet bepalen wie er toegang toe heeft. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Opslag** |  |  |
> | [Avere Bijdrager](#avere-contributor) | Kan een Avere vFXT-cluster maken en beheren. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere Operator](#avere-operator) | Gebruikt door het Avere vFXT-cluster om het cluster te beheren | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Back-upbijdrager](#backup-contributor) | Hiermee u een back-upservice beheren, maar geen kluizen maken en geen toegang geven tot anderen | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Back-upoperator](#backup-operator) | Hiermee u back-upservices beheren, behalve het verwijderen van back-up, het maken van kluizen en het geven van toegang tot anderen | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Back-uplezer](#backup-reader) | Kan back-upservices bekijken, maar kan geen wijzigingen aanbrengen | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Bijdrage aan klassieke opslagaccount](#classic-storage-account-contributor) | Hiermee u klassieke opslagaccounts beheren, maar geen toegang tot deze accounts. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Servicerol voor de sleutel van klassieke opslagaccount](#classic-storage-account-key-operator-service-role) | Beheerders van klassieke opslagaccounts mogen sleutels op klassieke opslagaccounts aanbieden en regenereren | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Gegevensvak inzender](#data-box-contributor) | Hiermee u alles beheren onder Data Box Service, behalve het geven van toegang tot anderen. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Gegevensvaklezer](#data-box-reader) | Hiermee u De Gegevensboxservice beheren, behalve het maken van order- of bewerkingsdetails en het geven van toegang aan anderen. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Ontwikkelaar datalake analytics](#data-lake-analytics-developer) | Hiermee u uw eigen taken indienen, controleren en beheren, maar gegevenslake Analytics-accounts niet maken of verwijderen. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Toegang tot lezer en gegevens](#reader-and-data-access) | Hiermee u alles bekijken, maar u geen opslagaccount of bron verwijderen of maken. Het zal ook lezen / schrijven toegang tot alle gegevens in een opslagaccount via toegang tot opslag accountsleutels. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Inzender voor opslagaccounts](#storage-account-contributor) | Staat beheer van opslagrekeningen toe. Biedt toegang tot de accountsleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via de autorisatie van Gedeelde sleutel. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Servicerol opslagaccountsleuteloperator](#storage-account-key-operator-service-role) | Hiermee worden toegangssleutels voor opslagaccount vermeld en geregenereert. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Opslagblob-gegevensbijdrager](#storage-blob-data-contributor) | Azure Storage-containers en blobs lezen, schrijven en verwijderen. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Eigenaar opslagblobgegevens](#storage-blob-data-owner) | Biedt volledige toegang tot Blob-containers en -gegevens van Azure Storage, inclusief het toewijzen van POSIX-toegangscontrole. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Blob-gegevenslezer opslag](#storage-blob-data-reader) | Lees en lijst Azure Storage-containers en blobs. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Opslag Blob Delegator](#storage-blob-delegator) | Ontvang een gebruikersdelegatiesleutel, die vervolgens kan worden gebruikt om een handtekening voor gedeelde toegang te maken voor een container of blob die is ondertekend met Azure AD-referenties. Zie [Een SAS voor gebruikersdelegatie maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Bijdrager voor het delen van opslagbestanden smb-share](#storage-file-data-smb-share-contributor) | Hiermee u toegang lezen, schrijven en verwijderen voor bestanden/mappen in Azure-bestandsshares. Deze rol heeft geen ingebouwd equivalent op Windows-bestandsservers. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Opslagbestandsgegevens SMB-bijdrager voor het delen van verhoogde bijdragen](#storage-file-data-smb-share-elevated-contributor) | Hiermee u ACL's lezen, schrijven, verwijderen en wijzigen voor bestanden/mappen in Azure-bestandsshares. Deze rol is gelijk aan een ACL voor bestandsshare van wijzigingen op Windows-bestandsservers. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [SMB-sharereader voor opslagbestanden](#storage-file-data-smb-share-reader) | Hiermee u leestoegang krijgen voor bestanden/mappen in Azure-bestandsshares. Deze rol is gelijk aan een acl voor het delen van bestanden op Windows-bestandsservers. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Opslagwachtrijgegevens inzender](#storage-queue-data-contributor) | Azure Storage-wachtrijen en wachtrijberichten lezen, schrijven en verwijderen. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Berichtenverwerker opslagwachtrijgegevens](#storage-queue-data-message-processor) | Een bericht bekijken, ophalen en verwijderen uit een Azure Storage-wachtrij. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Afzender van gegevensin de opslagwachtrij](#storage-queue-data-message-sender) | Berichten toevoegen aan een Azure Storage-wachtrij. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Gegevenslezer van de opslagwachtrij](#storage-queue-data-reader) | Lees en lijst Azure Storage-wachtrijen en wachtrijberichten. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking. | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps-gegevenslezer (voorbeeld)](#azure-maps-data-reader-preview) | Verleent toegang tot het lezen van kaartgerelateerde gegevens van een Azure-kaartaccount. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Medewerker zoekservice](#search-service-contributor) | Hiermee u zoekservices beheren, maar geen toegang tot deze services. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Webplan bijdrager](#web-plan-contributor) | Hiermee u de webplannen voor websites beheren, maar er geen toegang toe hebben. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Websitebijdrager](#website-contributor) | Hiermee u websites beheren (geen webplannen), maar geen toegang tot deze websites. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | acr verwijderen | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr-afbeeldingsondertekenaar | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull (AcrPull)](#acrpull) | acr pull acr pull acr pull acr | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr quarantaine gegevenslezer | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr quarantaine gegevens schrijver | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service Cluster Admin-rol](#azure-kubernetes-service-cluster-admin-role) | Lijst cluster admin credential actie. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Gebruikersrol Azure Kubernetes Service Cluster](#azure-kubernetes-service-cluster-user-role) | Actie voor clustergebruikersreferenties. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Databases** |  |  |
> | [Cosmos DB-accountlezerrol](#cosmos-db-account-reader-role) | Kan Azure Cosmos DB-accountgegevens lezen. Zie [DocumentDB-accountbijdrager](#documentdb-account-contributor) voor het beheer van Azure Cosmos DB-accounts. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB-operator](#cosmos-db-operator) | Hiermee u Azure Cosmos DB-accounts beheren, maar geen toegang krijgen tot gegevens in deze accounts. Hiermee voorkomt u toegang tot accountsleutels en verbindingstekenreeksen. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan herstelaanvraag indienen voor een Cosmos DB-database of een container voor een account | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB-accountbijdrager](#documentdb-account-contributor) | Kan Azure Cosmos DB-accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Bijdrager in De cache van Redis](#redis-cache-contributor) | Hiermee u Redis-caches beheren, maar er geen toegang toe hebben. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB-bijdrager](#sql-db-contributor) | Hiermee u SQL-databases beheren, maar geen toegang tot deze databases. U ook hun beveiligingsbeleid of hun bovenliggende SQL-servers niet beheren. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance Contributor](#sql-managed-instance-contributor) | Hiermee u SQL Managed Instances en vereiste netwerkconfiguratie beheren, maar geen toegang geven tot anderen. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-beveiligingsbeheer](#sql-security-manager) | Hiermee u het beveiligingsbeleid van SQL-servers en -databases beheren, maar geen toegang tot deze servers. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server-bijdrager](#sql-server-contributor) | Hiermee u SQL-servers en -databases beheren, maar geen toegang tot deze servers en niet hun beveiligingsbeleid. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analytics** |  |  |
> | [Eigenaar azure-gebeurtenishubs](#azure-event-hubs-data-owner) | Hiermee u volledige toegang krijgen tot Azure Event Hubs-bronnen. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Gegevensontvanger azure-gebeurtenishubs](#azure-event-hubs-data-receiver) | Hiermee u toegang krijgen tot Azure Event Hubs-bronnen. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Afzender van Azure-gebeurtenishubs](#azure-event-hubs-data-sender) | Hiermee u toegang tot Azure Event Hubs-bronnen verzenden. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Gegevensfabriek bijdrager](#data-factory-contributor) | Maak en beheer gegevensfabrieken, evenals onderliggende bronnen binnen hen. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Gegevenszuiverheid](#data-purger) | Kan analysegegevens zuiveren | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-clusteroperator](#hdinsight-cluster-operator) | Hiermee u HDInsight-clusterconfiguraties lezen en wijzigen. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Bijdrager HDInsight Domain Services](#hdinsight-domain-services-contributor) | Kan domeinservices lezen, maken, wijzigen en verwijderen die nodig zijn voor hdinsight enterprise security pakket | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Inzender van Log Analytics](#log-analytics-contributor) | Log Analytics-inzender kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. De bewakingsinstellingen voor bewerken omvatten het toevoegen van de VM-extensie aan VM's; leesopslagaccountsleutels om verzameling logboeken uit Azure Storage te kunnen configureren; Het maken en configureren van Automatiseringsaccounts; het toevoegen van oplossingen; en azure-diagnose configureren op alle Azure-resources. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lezer van Log Analytics](#log-analytics-reader) | Log Analytics Reader kan alle bewakingsgegevens bekijken en doorzoeken en bewakingsinstellingen weergeven, inclusief het bekijken van de configuratie van Azure-diagnoses op alle Azure-bronnen. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blockchain** |  |  |
> | [Toegang tot blockchain-lidknooppunt (voorbeeld)](#blockchain-member-node-access-preview) | Biedt toegang tot blockchain-lidknooppunten | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + machine learning** |  |  |
> | [Medewerker cognitieve diensten](#cognitive-services-contributor) | Hiermee u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497e68 |
> | [Gegevenslezer van cognitive services (voorbeeld)](#cognitive-services-data-reader-preview) | Hiermee u gegevens van Cognitive Services lezen. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Gebruiker van cognitieve services](#cognitive-services-user) | Hiermee u toetsen van cognitieve services lezen en vermelden. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Gemengde werkelijkheid** |  |  |
> | [Rekeningbijdrage voor ruimtelijke ankers](#spatial-anchors-account-contributor) | Hiermee u ruimtelijke ankers in uw account beheren, maar deze niet verwijderen | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Eigenaar van accountruimtelijke ankers](#spatial-anchors-account-owner) | Hiermee u ruimtelijke ankers in uw account beheren, inclusief het verwijderen ervan | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Accountlezer ruimtelijke ankers](#spatial-anchors-account-reader) | Hiermee u eigenschappen van ruimtelijke ankers in uw account vinden en lezen | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integratie** |  |  |
> | [Api-beheerservice bijdrager](#api-management-service-contributor) | Kan service en api's beheren | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Functie API Management Service Operator](#api-management-service-operator-role) | Kan service beheren, maar niet de API's | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Api Management Service Reader Rol](#api-management-service-reader-role) | Alleen-lezen toegang tot service en API's | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Eigenaar van app-configuratiegegevens](#app-configuration-data-owner) | Hiermee u volledige toegang krijgen tot app-configuratiegegevens. | 5ae67d6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Gegevenslezer app-configuratie](#app-configuration-data-reader) | Hiermee u leestoegang krijgen tot app-configuratiegegevens. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus-gegevens-eigenaar](#azure-service-bus-data-owner) | Hiermee u volledige toegang krijgen tot Azure Service Bus-bronnen. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus-gegevensontvanger](#azure-service-bus-data-receiver) | Hiermee u toegang krijgen tot Azure Service Bus-bronnen. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Afzender van Azure-servicebusgegevens](#azure-service-bus-data-sender) | Hiermee u toegang verzenden tot Azure Service Bus-bronnen. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Eigenaar Azure Stack-registratie](#azure-stack-registration-owner) | Hiermee u Azure Stack-registraties beheren. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription Inzender](#eventgrid-eventsubscription-contributor) | Hiermee u eventgrid-abonnementsbewerkingen beheren. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Hiermee u eventgrid-gebeurtenisabonnementen lezen. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Bijdrage aan intelligente systemen-account](#intelligent-systems-account-contributor) | Hiermee u accounts van Intelligent Systems beheren, maar er geen toegang toe hebben. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logica-app-inzender](#logic-app-contributor) | Hiermee u logische apps beheren, maar de toegang er niet toe wijzigen. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logische app-operator](#logic-app-operator) | Hiermee u logische apps lezen, inschakelen en uitschakelen, maar deze niet bewerken of bijwerken. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identiteit** |  |  |
> | [Medewerker beheerde identiteit](#managed-identity-contributor) | Gebruikersnaam maken, lezen, bijwerken en verwijderen | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Beheerde identiteitsoperator](#managed-identity-operator) | Gebruikerstoegewezen identiteit lezen en toewijzen | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Beveiliging** |  |  |
> | [Azure Sentinel-bijdrager](#azure-sentinel-contributor) | Azure Sentinel-bijdrager | ab8e14d6-4a74-4a29-9ba8-5494222addade |
> | [Azure Sentinel-lezer](#azure-sentinel-reader) | Azure Sentinel-lezer | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Belangrijke vault-bijdrager](#key-vault-contributor) | Hiermee u sleutelkluizen beheren, maar geen toegang tot deze kluizen. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Beveiligingsbeheerder](#security-admin) | U beveiligingsbeleid bekijken, beveiligingsstatussen bekijken, beveiligingsbeleid bewerken, waarschuwingen en aanbevelingen bekijken, waarschuwingen en aanbevelingen afwijzen. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Medewerker beveiligingsbeoordeling](#security-assessment-contributor) | Hiermee u beoordelingen pushen naar Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Beveiligingsmanager (Legacy)](#security-manager-legacy) | Dit is een erfenis rol. Gebruik in plaats daarvan beveiligingsbeheerder. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Beveiligingslezer](#security-reader) | Kan aanbevelingen en waarschuwingen bekijken, beveiligingsbeleid bekijken, beveiligingsstatussen bekijken, maar geen wijzigingen aanbrengen. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-gebruiker](#devtest-labs-user) | Hiermee u uw virtuele machines verbinden, starten, opnieuw starten en afsluiten in uw Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Creator](#lab-creator) | Hiermee u uw beheerde labs maken, beheren en verwijderen onder uw Azure Lab-accounts. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitor** |  |  |
> | [Componentbijdrager voor toepassingsinzichten](#application-insights-component-contributor) | Onderdelen van Application Insights beheren | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Momentopnamefoutfout van toepassingsinzichten](#application-insights-snapshot-debugger) | Geeft gebruikers toestemming om foutopsporingsmomentopnamen te bekijken en te downloaden die zijn verzameld met de Foutopsporing van de momentopname van Application Insights. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [Eigenaar](#owner) of [Inzender.](#contributor) | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Monitorbijdrager](#monitoring-contributor) | Kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Uitgever statistieken controleren](#monitoring-metrics-publisher) | Hiermee kunnen publicatiestatistieken worden weergegeven tegen Azure-bronnen | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) | Kan alle monitoringgegevens (metrics, logs, etc.) lezen. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Werkmapinzender](#workbook-contributor) | Kan gedeelde werkmappen opslaan. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Werkmaplezer](#workbook-reader) | Kan werkmappen lezen. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Management + governance** |  |  |
> | [Operator van automatiseringswerktaak](#automation-job-operator) | Taken maken en beheren met behulp van Automatiseringsrunbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operator](#automation-operator) | Automatiseringsoperators kunnen taken starten, stoppen, opschorten en hervatten | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operator voor automatiseringsrunbook](#automation-runbook-operator) | Lees Runbook-eigenschappen - om taken van het runbook te kunnen maken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Connected Machine Onboarding](#azure-connected-machine-onboarding) | Kan aan boord van Azure Connected Machines. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine Resource Administrator](#azure-connected-machine-resource-administrator) | Kan Azure Connected Machines lezen, schrijven, verwijderen en opnieuw ingebouwde. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Factureringslezer](#billing-reader) | Geeft leestoegang tot factureringsgegevens | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Blauwdrukbijdrager](#blueprint-contributor) | Kan blauwdrukdefinities beheren, maar deze niet toewijzen. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blueprint Operator](#blueprint-operator) | Kan bestaande gepubliceerde blauwdrukken toewijzen, maar kan geen nieuwe blauwdrukken maken. Houd er rekening mee dat dit alleen werkt als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Bijdrager kostenbeheer](#cost-management-contributor) | Kan de kosten bekijken en kostenconfiguratie beheren (bijv. budgetten, export) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Kostenbeheerlezer](#cost-management-reader) | Kan kostengegevens en configuratie weergeven (bijv. budgetten, export) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Rol beheerde toepassingsbijdrager](#managed-application-contributor-role) | Hiermee u beheerde toepassingsbronnen maken. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Functie beheerde toepassingsoperator](#managed-application-operator-role) | Hiermee u acties lezen en uitvoeren op resources voor beheerde toepassingen | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lezer van beheerde toepassingen](#managed-applications-reader) | Hiermee u bronnen in een beheerde app lezen en JIT-toegang aanvragen. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Verwijderrol registratietoewijzing managed services](#managed-services-registration-assignment-delete-role) | Met de verwijderrol beheerde servicesregistratietoewijzing kunnen de gebruikers van de beherende tenant de registratietoewijzing verwijderen die aan hun tenant is toegewezen. | 91c177a-f3dc-4fae-b103-61d183457e46 |
> | [Medewerker managementgroep](#management-group-contributor) | Rol van directiegroepbijdrager | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Management Group Reader](#management-group-reader) | Management Group Reader Rol | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Nieuwe Relic APM-accountbijdrager](#new-relic-apm-account-contributor) | Hiermee u nieuwe Reliekenapplicatie Prestatiebeheeraccounts en -toepassingen beheren, maar geen toegang tot deze accounts. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Beleidsinzichten Gegevensschrijver (voorbeeld)](#policy-insights-data-writer-preview) | Hiermee u leestoegang tot resourcebeleid en schrijftoegang tot beleidsgebeurtenissen voor resourcecomponenten. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Inzender voor resourcebeleid](#resource-policy-contributor) | Gebruikers met rechten om resourcebeleid te maken/wijzigen, ondersteuningsticket te maken en bronnen/hiërarchie te lezen. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Bijdrage raan van siteherstel](#site-recovery-contributor) | Hiermee u de service Siteherstel beheren, behalve het maken van kluizen en roltoewijzing | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Beheerder van siteherstel](#site-recovery-operator) | Hiermee u failover en failback, maar niet uitvoeren van andere Site Recovery management operaties | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Siteherstellezer](#site-recovery-reader) | Hiermee u de status Siteherstel bekijken, maar geen andere beheerbewerkingen uitvoeren | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Bijdrage voor ondersteuningsaanvraag](#support-request-contributor) | Hiermee u ondersteuningsaanvragen maken en beheren | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Tagbijdrager](#tag-contributor) | Hiermee u tags op entiteiten beheren, zonder toegang te verlenen tot de entiteiten zelf. | 4a9ae827-6dc8-4573-8ac7-8239d42a03f |
> | **Overige** |  |  |
> | [BizTalk-bijdrager](#biztalk-contributor) | Hiermee u BizTalk-services beheren, maar er geen toegang toe hebben. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Bijdrager voor taakverzamelingen van planner](#scheduler-job-collections-contributor) | Hiermee u de taakverzamelingen van Scheduler beheren, maar geen toegang tot deze verzamelingen. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Algemeen


### <a name="contributor"></a>Inzender

Hiermee u alles beheren, behalve toegang verlenen tot bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | * | Bronnen van alle typen maken en beheren |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Rollen, beleidstoewijzingen, beleidsdefinities en definities van beleidssets verwijderen |
> | Microsoft.Authorization/*/Write | Rollen, roltoewijzingen, beleidstoewijzingen, beleidsdefinities en definities van beleidssets maken |
> | Microsoft.Authorization/elevateAccess/Action | Hiermee krijgt de Gebruikerstoegangbeheerder toegang op tenantniveau |
> | Microsoft.Blueprint/blueprintOpdrachten/schrijven | Blueprint-toewijzingen maken of bijwerken |
> | Microsoft.Blueprint/blueprintAssignments/delete | Alle blauwdruktoewijzingen verwijderen |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u alles beheren, inclusief toegang tot bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | * | Bronnen van alle typen maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u alles bekijken, maar geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u gebruikerstoegang tot Azure-bronnen beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.Authorization/* | Autorisatie beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Klassieke virtuele machine bijdrager

Hiermee u klassieke virtuele machines beheren, maar geen toegang tot deze machines en niet het virtuele netwerk- of opslagaccount waarmee ze zijn verbonden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicCompute/domainNames/* | Klassieke compute-domeinnamen maken en beheren |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuele machines maken en beheren |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Een gereserveerd ip koppelen |
> | Microsoft.ClassicNetwork/reservedIps/read | Krijgt de gereserveerde Ips |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Sluit zich aan bij het virtuele netwerk. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Download het virtuele netwerk. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Geeft als resultaat de schijf van het opslagaccount. |
> | Microsoft.ClassicStorage/storageAccounts/afbeeldingen/gelezen | Geeft als resultaat de afbeelding van het opslagaccount. (Afgeschaft. Gebruik 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangssleutels voor de opslagaccounts weergegeven. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retourneer de opslagrekening met het opgegeven account. |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
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

### <a name="virtual-machine-administrator-login"></a>Inloggen voor virtuele machinebeheerders

Virtuele machines in de portal bekijken en inloggen als beheerder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Network/publicIPAddresses/read | Krijgt een openbare ip-adresdefinitie. |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.Network/loadBalancers/read | Krijgt een definitie van load balancer |
> | Microsoft.Network/networkInterfaces/read | Krijgt een netwerkinterfacedefinitie.  |
> | Microsoft.Compute/virtualMachines/*/lezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Compute/virtualMachines/login/action | Log in bij een virtuele machine als gewone gebruiker |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Inloggen op een virtuele machine met Windows-beheerders- of Linux-rootgebruikersrechten |
> | **NietGegevensacties** |  |
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

Hiermee u virtuele machines beheren, maar geen toegang tot deze machines en niet het virtuele netwerk- of opslagaccount waarmee ze zijn verbonden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Compute/availabilitySets/* | Compute availability sets maken en beheren |
> | Microsoft.Compute/locaties/* | Compute-locaties maken en beheren |
> | Microsoft.Compute/virtualMachines/* | Virtuele machines maken en beheren |
> | Microsoft.Compute/virtualMachineScaleSets/* | Schaalsets voor virtuele machines maken en beheren |
> | Microsoft.Compute/disks/write | Een nieuwe schijf maken of een bestaande schijf bijwerken |
> | Microsoft.Compute/disks/read | De eigenschappen van een schijf oppakken |
> | Microsoft.Compute/disks/delete | De schijf verwijderen |
> | Microsoft.DevTestLab/schema's/* |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Voegt zich bij een backend-adresgroep van de toepassingsgateway. Niet alertbaar. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Voegt zich bij een backend-adresgroep van de load balancer. Niet alertbaar. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Voegt zich bij een inkomende NAT-groep van de load balancer. Niet alert. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Hiermee wordt een inkomende nat-regel voor load balancer verbonden. Niet alertbaar. |
> | Microsoft.Network/loadBalancers/probes/join/action | Hiermee u sondes van een load balancer gebruiken. Met deze eigenschap healthProbe met toestemming van vm-schaalset kan bijvoorbeeld naar de sonde worden verwezen. Niet alert. |
> | Microsoft.Network/loadBalancers/read | Krijgt een definitie van load balancer |
> | Microsoft.Network/locaties/* | Netwerklocaties maken en beheren |
> | Microsoft.Network/networkInterfaces/* | Netwerkinterfaces maken en beheren |
> | Microsoft.Network/networkSecurityGroups/join/action | Sluit zich aan bij een netwerkbeveiligingsgroep. Niet alertbaar. |
> | Microsoft.Network/networkSecurityGroups/read | Krijgt een definitie van netwerkbeveiligingsgroepen |
> | Microsoft.Network/publicIPAddresses/join/action | Voegt zich bij een openbaar ip-adres. Niet alertbaar. |
> | Microsoft.Network/publicIPAddresses/read | Krijgt een openbare ip-adresdefinitie. |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Sluit zich aan bij een virtueel netwerk. Niet alertbaar. |
> | Microsoft.RecoveryServices/locaties/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Een back-upbeveiligingsintentie maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retourneert objectdetails van het beveiligde object |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retourneert alle beveiligingsbeleid |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Beveiligingsbeleid maken |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Kluizen/gebruik/lezen | Retourneert gebruiksgegevens voor een Vault voor Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/write | Vault-bewerking maken een Azure-bron van het type 'vault' |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="virtual-machine-user-login"></a>Inloggen voor virtuele machine-gebruikers

Bekijk virtuele machines in de portal en log in als een gewone gebruiker.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Network/publicIPAddresses/read | Krijgt een openbare ip-adresdefinitie. |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.Network/loadBalancers/read | Krijgt een definitie van load balancer |
> | Microsoft.Network/networkInterfaces/read | Krijgt een netwerkinterfacedefinitie.  |
> | Microsoft.Compute/virtualMachines/*/lezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Compute/virtualMachines/login/action | Log in bij een virtuele machine als gewone gebruiker |
> | **NietGegevensacties** |  |
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

## <a name="networking"></a>Netwerken


### <a name="cdn-endpoint-contributor"></a>CDN-eindpuntbijdrager

Kan CDN-eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profielen/eindpunten/* |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
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

### <a name="cdn-endpoint-reader"></a>CDN-eindpuntlezer

Kan CDN-eindpunten weergeven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
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

### <a name="cdn-profile-contributor"></a>Cdn-profielbijdrager

Kan CDN-profielen en hun eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profielen/* |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
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

### <a name="cdn-profile-reader"></a>CDN-profiellezer

Kan CDN-profielen en hun eindpunten bekijken, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profielen/*/gelezen |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
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

Hiermee u klassieke netwerken beheren, maar er geen toegang toe hebben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicNetwork/* | Klassieke netwerken maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="dns-zone-contributor"></a>DNS-zonebijdrager

Hiermee u DNS-zones beheren en sets opnemen in Azure DNS, maar u niet bepalen wie er toegang toe heeft.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/dnsZones/* | DNS-zones en -records maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u netwerken beheren, maar er geen toegang toe hebben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/* | Netwerken maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="traffic-manager-contributor"></a>Medewerker verkeersmanager

Hiermee u Traffic Manager-profielen beheren, maar u niet bepalen wie er toegang toe heeft.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Avere Bijdrager

Kan een Avere vFXT-cluster maken en beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Compute/*/gelezen |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/lezen |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.Network/virtualNetworks/subnets/read | Krijgt een virtuele netwerk subnet definitie |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Sluit zich aan bij een virtueel netwerk. Niet alertbaar. |
> | Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt bron zoals opslagaccount of SQL-database samen met een subnet. Niet alert. |
> | Microsoft.Network/networkSecurityGroups/join/action | Sluit zich aan bij een netwerkbeveiligingsgroep. Niet alertbaar. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/*/lezen |  |
> | Microsoft.Storage/storageAccounts/* | Opslagaccounts maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/resources/gelezen | Haalt de resources voor de resourcegroep op. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Geeft als resultaat het resultaat van het verwijderen van een blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Geeft als resultaat een blob of een lijst met blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Geeft als resultaat het resultaat van het schrijven van een blob |
> | **NietGegevensacties** |  |
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

### <a name="avere-operator"></a>Avere Operator

Gebruikt door het Avere vFXT-cluster om het cluster te beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Compute/virtualMachines/lezen | De eigenschappen van een virtuele machine krijgen |
> | Microsoft.Network/networkInterfaces/read | Krijgt een netwerkinterfacedefinitie.  |
> | Microsoft.Network/networkInterfaces/write | Hiermee maakt u een netwerkinterface of werkt u een bestaande netwerkinterface bij.  |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.Network/virtualNetworks/subnets/read | Krijgt een virtuele netwerk subnet definitie |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Sluit zich aan bij een virtueel netwerk. Niet alertbaar. |
> | Microsoft.Network/networkSecurityGroups/join/action | Sluit zich aan bij een netwerkbeveiligingsgroep. Niet alertbaar. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Geeft als resultaat als resultaat van het verwijderen van een container |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourneert lijst met containers |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Geeft als resultaat van putblobcontainer als resultaat |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Geeft als resultaat het resultaat van het verwijderen van een blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Geeft als resultaat een blob of een lijst met blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Geeft als resultaat het resultaat van het schrijven van een blob |
> | **NietGegevensacties** |  |
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

### <a name="backup-contributor"></a>Back-upbijdrager

Hiermee u een back-upservice beheren, maar geen kluizen maken en geen toegang geven tot anderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.RecoveryServices/locaties/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Bedrijfsresultaten beheren op het gebied van back-upbeheer |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Back-upcontainers maken en beheren in back-upstoffen van de vault van Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | De containerlijst vernieuwen |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Resultaten van back-upbeheerbewerkingen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Back-upbeleid maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Back-upitems maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Containers maken en beheren met back-upitems |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samenvattingen voor beveiligde items en beveiligde servers voor een herstelservices . |
> | Microsoft.RecoveryServices/Vaults/certificaten/* | Certificaten maken en beheren met betrekking tot back-up in de vault van Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot kluis maken en beheren |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/gelezen | Krijgt de waarschuwingen voor de kluis van de herstelservices. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfiguraties/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/* | Geregistreerde identiteiten maken en beheren |
> | Microsoft.RecoveryServices/Kluizen/gebruik/* | Het gebruik van de kluis Recovery Services maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Bewerking op beveiligd object valideren |
> | Microsoft.RecoveryServices/Vaults/write | Vault-bewerking maken een Azure-bron van het type 'vault' |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retourneert de status van back-upbewerking voor de vault van Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerservers die zijn geregistreerd bij vault. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Haal alle beschermbare containers |
> | Microsoft.RecoveryServices/locaties/back-upStatus/actie | Back-upstatus controleren voor kluizen van Recovery Services |
> | Microsoft.RecoveryServices/locaties/back-upPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locaties/back-upsvaliderenFuncties/actie | Functies valideren |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Bewerking retourneert de lijst met bewerkingen voor een resourceprovider |
> | Microsoft.RecoveryServices/locaties/operationStatus/read | Krijgt de bedrijfsstatus voor een bepaalde bewerking |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties voor back-upbeveiliging weergeven |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u back-upservices beheren, behalve het verwijderen van back-up, het maken van kluizen en het geven van toegang tot anderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Geeft als resultaat de status van de bewerking |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Wordt het resultaat van de operatie uitgevoerd op Protection Container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Hiermee wordt back-up uitgevoerd voor beveiligd item. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Wordt het resultaat van de bewerking uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Geeft als resultaat de status van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retourneert objectdetails van het beveiligde object |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Invordering van expresobject voor beveiligd object |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstelpunten voor beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Herstelpunten voor beveiligde items herstellen. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Herstel van expresobject intrekken voor beveiligd object |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retourneert alle geregistreerde containers |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | De containerlijst vernieuwen |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Resultaten van back-upbeheerbewerkingen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Resultaten van beleidsbewerking. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retourneert alle beveiligingsbeleid |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retourneert de lijst met alle beveiligde objecten. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retourneert alle containers die bij het abonnement horen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samenvattingen voor beveiligde items en beveiligde servers voor een herstelservices . |
> | Microsoft.RecoveryServices/Vaults/certificaten/schrijven | Met de bewerking Resourcecertificaat bijwerken wordt het bron-/kluisreferentiecertificaat bijgewerkt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | De bewerking Uitgebreide informatie van een object krijgen, wordt de uitgebreide informatie van een object weergegeven die de Azure-bron van het type ?vault vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | De bewerking Uitgebreide informatie van een object krijgen, wordt de uitgebreide informatie van een object weergegeven die de Azure-bron van het type ?vault vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/gelezen | Krijgt de waarschuwingen voor de kluis van de herstelservices. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfiguraties/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten opvragen kan worden gebruikt, waarbij de bedrijfsstatus en het resultaat voor de asynchrone ingediende bewerking worden uitgevoerd |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/gelezen | De bewerking Containers worden gebruikt, zorg ervoor dat de containers zijn geregistreerd voor een resource. |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/schrijven | De registerservicecontainerbewerking kan worden gebruikt om een container te registreren bij Recovery Service. |
> | Microsoft.RecoveryServices/Kluizen/gebruik/lezen | Retourneert gebruiksgegevens voor een Vault voor Herstelservices. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Bewerking op beveiligd object valideren |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retourneert de status van back-upbewerking voor de vault van Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleidsbewerking. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Een geregistreerde container maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Onderzoek doen naar workloads in een container |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerservers die zijn geregistreerd bij vault. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Een back-upbeveiligingsintentie maken |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Een back-upbeveiligingsintentie |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Haal alle beschermbare containers |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Alle artikelen in een container opbrengen |
> | Microsoft.RecoveryServices/locaties/back-upStatus/actie | Back-upstatus controleren voor kluizen van Recovery Services |
> | Microsoft.RecoveryServices/locaties/back-upPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locaties/back-upsvaliderenFuncties/actie | Functies valideren |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Bewerking retourneert de lijst met bewerkingen voor een resourceprovider |
> | Microsoft.RecoveryServices/locaties/operationStatus/read | Krijgt de bedrijfsstatus voor een bepaalde bewerking |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties voor back-upbeveiliging weergeven |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Kan back-upservices bekijken, maar kan geen wijzigingen aanbrengen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.RecoveryServices/locaties/toegewezenStamp/gelezen | GetAllocatedStamp is interne bewerking die door service wordt gebruikt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Geeft als resultaat de status van de bewerking |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Wordt het resultaat van de operatie uitgevoerd op Protection Container. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Wordt het resultaat van de bewerking uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Geeft als resultaat de status van de bewerking die is uitgevoerd op beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retourneert objectdetails van het beveiligde object |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstelpunten voor beveiligde items. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retourneert alle geregistreerde containers |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Geeft als resultaat het resultaat van taakbewerking. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Als u alle taakobjecten retourneert |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retourneert back-upoperationresultaat voor vault herstelservices. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Resultaten van beleidsbewerking. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retourneert alle beveiligingsbeleid |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retourneert de lijst met alle beveiligde objecten. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retourneert alle containers die bij het abonnement horen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samenvattingen voor beveiligde items en beveiligde servers voor een herstelservices . |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | De bewerking Uitgebreide informatie van een object krijgen, wordt de uitgebreide informatie van een object weergegeven die de Azure-bron van het type ?vault vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/gelezen | Krijgt de waarschuwingen voor de kluis van de herstelservices. |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten opvragen kan worden gebruikt, waarbij de bedrijfsstatus en het resultaat voor de asynchrone ingediende bewerking worden uitgevoerd |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/gelezen | De bewerking Containers worden gebruikt, zorg ervoor dat de containers zijn geregistreerd voor een resource. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retourneert opslagconfiguratie voor vault recovery services. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retourneert Configuratie voor Vault Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retourneert de status van back-upbewerking voor de vault van Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleidsbewerking. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerservers die zijn geregistreerd bij vault. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Een back-upbeveiligingsintentie |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Alle artikelen in een container opbrengen |
> | Microsoft.RecoveryServices/locaties/back-upStatus/actie | Back-upstatus controleren voor kluizen van Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfiguraties/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Microsoft.RecoveryServices/operations/read | Bewerking retourneert de lijst met bewerkingen voor een resourceprovider |
> | Microsoft.RecoveryServices/locaties/operationStatus/read | Krijgt de bedrijfsstatus voor een bepaalde bewerking |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties voor back-upbeveiliging weergeven |
> | Microsoft.RecoveryServices/Kluizen/gebruik/lezen | Retourneert gebruiksgegevens voor een Vault voor Herstelservices. |
> | Microsoft.RecoveryServices/locaties/back-upsvaliderenFuncties/actie | Functies valideren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
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

### <a name="classic-storage-account-contributor"></a>Bijdrage aan klassieke opslagaccount

Hiermee u klassieke opslagaccounts beheren, maar geen toegang tot deze accounts.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicStorage/storageAccounts/* | Opslagaccounts maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Servicerol voor de sleutel van klassieke opslagaccount

Beheerders van klassieke opslagaccounts mogen sleutels op klassieke opslagaccounts aanbieden en regenereren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/actie | Hier worden de toegangssleutels voor de opslagaccounts weergegeven. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Hiermee worden de bestaande toegangssleutels voor het opslagaccount geregenereert. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="data-box-contributor"></a>Gegevensvak inzender

Hiermee u alles beheren onder Data Box Service, behalve het geven van toegang tot anderen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="data-box-reader"></a>Gegevensvaklezer

Hiermee u De Gegevensboxservice beheren, behalve het maken van order- of bewerkingsdetails en het geven van toegang aan anderen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Databox/*/lezen |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Hiermee worden de niet-versleutelde referenties weergegeven die betrekking hebben op de bestelling. |
> | Microsoft.Databox/locaties/availableSkus/action | Met deze methode wordt de lijst met beschikbare skus geretourneerd. |
> | Microsoft.Databox/locaties/validateInputs/action | Deze methode doet alle soorten validaties. |
> | Microsoft.Databox/locaties/regioConfiguratie/actie | Met deze methode worden de configuraties voor de regio geretourneerd. |
> | Microsoft.Databox/locaties/validateAddress/action | Valideert het verzendadres en geeft eventuele alternatieve adressen aan. |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="data-lake-analytics-developer"></a>Ontwikkelaar datalake analytics

Hiermee u uw eigen taken indienen, controleren en beheren, maar gegevenslake Analytics-accounts niet maken of verwijderen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Verwijderen |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Schrijven |  |
> | Microsoft.DataLakeAnalytics/accounts/Verwijderen | Een DataLakeAnalytics-account verwijderen. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Machtigingen verlenen om taken te annuleren die door andere gebruikers zijn ingediend. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Een DataLakeAnalytics-account maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Een gekoppeld DataLakeStore-account van een DataLakeAnalytics-account maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Koppel een DataLakeStore-account los van een DataLakeAnalytics-account. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Een gekoppeld opslagaccount van een DataLakeAnalytics-account maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Koppel een opslagaccount los van een DataLakeAnalytics-account. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRegels/Schrijven | Een firewallregel maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRegels/Delete | Een firewallregel verwijderen. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Een rekenbeleid maken of bijwerken. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Een rekenbeleid verwijderen. |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="reader-and-data-access"></a>Toegang tot lezer en gegevens

Hiermee u alles bekijken, maar u geen opslagaccount of bron verwijderen of maken. Het zal ook lezen / schrijven toegang tot alle gegevens in een opslagaccount via toegang tot opslag accountsleutels.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Retourneert het SAS-token account voor het opgegeven opslagaccount. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="storage-account-contributor"></a>Inzender voor opslagaccounts

Staat beheer van opslagrekeningen toe. Biedt toegang tot de accountsleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via de autorisatie van Gedeelde sleutel.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee maakt, werkt u de diagnostische instelling voor Analysis Server |
> | Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt bron zoals opslagaccount of SQL-database samen met een subnet. Niet alert. |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/* | Opslagaccounts maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="storage-account-key-operator-service-role"></a>Servicerol opslagaccountsleuteloperator

Hiermee worden toegangssleutels voor opslagaccount vermeld en geregenereert.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Hiermee worden de toegangssleutels voor het opgegeven opslagaccount opnieuw gegenereerd. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="storage-blob-data-contributor"></a>Opslagblob-gegevensbijdrager

Azure Storage-containers en blobs lezen, schrijven en verwijderen. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Een container verwijderen. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Breng een container of een lijst met containers terug. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Wijzig de metagegevens of eigenschappen van een container. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikersdelegatiesleutel voor de Blob-service. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Een blob verwijderen. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Een blob of een lijst met blobs retourneren. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Schrijf naar een blob. |
> | **NietGegevensacties** |  |
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

### <a name="storage-blob-data-owner"></a>Eigenaar opslagblobgegevens

Biedt volledige toegang tot Blob-containers en -gegevens van Azure Storage, inclusief het toewijzen van POSIX-toegangscontrole. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Volledige machtigingen op containers. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikersdelegatiesleutel voor de Blob-service. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Volledige machtigingen op blobs. |
> | **NietGegevensacties** |  |
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

### <a name="storage-blob-data-reader"></a>Blob-gegevenslezer opslag

Lees en lijst Azure Storage-containers en blobs. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Breng een container of een lijst met containers terug. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikersdelegatiesleutel voor de Blob-service. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Een blob of een lijst met blobs retourneren. |
> | **NietGegevensacties** |  |
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

### <a name="storage-blob-delegator"></a>Opslag Blob Delegator

Ontvang een gebruikersdelegatiesleutel, die vervolgens kan worden gebruikt om een handtekening voor gedeelde toegang te maken voor een container of blob die is ondertekend met Azure AD-referenties. Zie [Een SAS voor gebruikersdelegatie maken](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)voor meer informatie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikersdelegatiesleutel voor de Blob-service. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="storage-file-data-smb-share-contributor"></a>Bijdrager voor het delen van opslagbestanden smb-share

Hiermee u toegang lezen, schrijven en verwijderen voor bestanden/mappen in Azure-bestandsshares. Deze rol heeft geen ingebouwd equivalent op Windows-bestandsservers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Geeft als resultaat het resultaat van het schrijven van een bestand of het maken van een map. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | **NietGegevensacties** |  |
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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Opslagbestandsgegevens SMB-bijdrager voor het delen van verhoogde bijdragen

Hiermee u ACL's lezen, schrijven, verwijderen en wijzigen voor bestanden/mappen in Azure-bestandsshares. Deze rol is gelijk aan een ACL voor bestandsshare van wijzigingen op Windows-bestandsservers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Geeft als resultaat het resultaat van het schrijven van een bestand of het maken van een map. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retourneert het resultaat van het wijzigen van de machtiging voor een bestand/map. |
> | **NietGegevensacties** |  |
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

### <a name="storage-file-data-smb-share-reader"></a>SMB-sharereader voor opslagbestanden

Hiermee u leestoegang krijgen voor bestanden/mappen in Azure-bestandsshares. Deze rol is gelijk aan een acl voor het delen van bestanden op Windows-bestandsservers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | **NietGegevensacties** |  |
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

### <a name="storage-queue-data-contributor"></a>Opslagwachtrijgegevens inzender

Azure Storage-wachtrijen en wachtrijberichten lezen, schrijven en verwijderen. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Een wachtrij verwijderen. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Een wachtrij of een lijst met wachtrijen retourneren. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Metagegevens of eigenschappen van wachtrijen wijzigen. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Verwijder een of meer berichten uit een wachtrij. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Een bericht toevoegen aan een wachtrij. |
> | **NietGegevensacties** |  |
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

### <a name="storage-queue-data-message-processor"></a>Berichtenverwerker opslagwachtrijgegevens

Een bericht bekijken, ophalen en verwijderen uit een Azure Storage-wachtrij. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Bekijk een bericht. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Een bericht ophalen en verwijderen. |
> | **NietGegevensacties** |  |
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

### <a name="storage-queue-data-message-sender"></a>Afzender van gegevensin de opslagwachtrij

Berichten toevoegen aan een Azure Storage-wachtrij. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Een bericht toevoegen aan een wachtrij. |
> | **NietGegevensacties** |  |
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

### <a name="storage-queue-data-reader"></a>Gegevenslezer van de opslagwachtrij

Lees en lijst Azure Storage-wachtrijen en wachtrijberichten. Zie [Machtigingen voor het aanroepen van blob- en queuedatabewerkingen](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties nodig zijn voor een bepaalde gegevensbewerking.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retourneert een wachtrij of een lijst met wachtrijen. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | **NietGegevensacties** |  |
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


### <a name="azure-maps-data-reader-preview"></a>Azure Maps-gegevenslezer (voorbeeld)

Verleent toegang tot het lezen van kaartgerelateerde gegevens van een Azure-kaartaccount.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Maps/accounts/gegevens/lezen | Verleent gegevens leestoegang tot een kaartenaccount. |
> | **NietGegevensacties** |  |
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

### <a name="search-service-contributor"></a>Medewerker zoekservice

Hiermee u zoekservices beheren, maar geen toegang tot deze services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Search/searchServices/* | Zoekservices maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="web-plan-contributor"></a>Webplan bijdrager

Hiermee u de webplannen voor websites beheren, maar er geen toegang toe hebben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Web/serverFarms/* | Serverfarms maken en beheren |
> | Microsoft.Web/hostingOmgevingen/Join/Action | Lid wordt van een app-serviceomgeving |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="website-contributor"></a>Websitebijdrager

Hiermee u websites beheren (geen webplannen), maar geen toegang tot deze websites.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/componenten/* | Insights-componenten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Web/certificaten/* | Websitecertificaten maken en beheren |
> | Microsoft.Web/listSitesAssignedToHostName/read | Namen van sites die zijn toegewezen aan hostname. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/gelezen | De eigenschappen van een App-serviceplan downloaden |
> | Microsoft.Web/sites/* | Websites maken en beheren (voor het maken van sites zijn ook schrijfmachtigingen voor het bijbehorende App Service Plan vereist) |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

acr verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registers/artefacten/verwijderen | Artefact verwijderen in een containerregister. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

acr-afbeeldingsondertekenaar

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Push/Pull content trust metadata voor een containerregister. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="acrpull"></a>AcrPull (AcrPull)

acr pull acr pull acr pull acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Afbeeldingen ophalen of ophalen uit een containerregister. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="acrpush"></a>AcrPush AcrPush

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Afbeeldingen ophalen of ophalen uit een containerregister. |
> | Microsoft.ContainerRegistry/registers/push/write | Afbeeldingen pushen of schrijven naar een containerregister. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

acr quarantaine gegevenslezer

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registers/quarantaine/lezen | Afbeeldingen uit de containerregistratie ophalen of in quarantaine plaatsen ophalen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

acr quarantaine gegevens schrijver

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerRegistry/registers/quarantaine/lezen | Afbeeldingen uit de containerregistratie ophalen of in quarantaine plaatsen ophalen |
> | Microsoft.ContainerRegistry/registers/quarantaine/schrijven | Quarantainestatus van in quarantaine geplaatste afbeeldingen schrijven/wijzigen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service Cluster Admin-rol

Lijst cluster admin credential actie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | De cluster-administrateurreferenties van een beheerd cluster weergeven |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Een beheerd clustertoegangsprofiel op basis van de naam van de rol opmaken met lijstreferenties |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Gebruikersrol Azure Kubernetes Service Cluster

Actie voor clustergebruikersreferenties.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | De clustergebruikersreferenties van een beheerd cluster weergeven |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB-accountlezerrol

Kan Azure Cosmos DB-accountgegevens lezen. Zie [DocumentDB-accountbijdrager](#documentdb-account-contributor) voor het beheer van Azure Cosmos DB-accounts.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.DocumentDB/*/lezen | Lees elke collectie |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Leest de database account readonly sleutels. |
> | Microsoft.Insights/MetricDefinities/gelezen | Metrische definities lezen |
> | Microsoft.Insights/Metrics/read | Statistieken lezen |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u Azure Cosmos DB-accounts beheren, maar geen toegang krijgen tot gegevens in deze accounts. Hiermee voorkomt u toegang tot accountsleutels en verbindingstekenreeksen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt bron zoals opslagaccount of SQL-database samen met een subnet. Niet alert. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Kan herstelaanvraag indienen voor een Cosmos DB-database of een container voor een account

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.DocumentDB/databaseAccounts/back-up/actie | Een verzoek indienen om een back-up te configureren |
> | Microsoft.DocumentDB/databaseAccounts/herstellen/actie | Een herstelaanvraag indienen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="documentdb-account-contributor"></a>DocumentDB-accountbijdrager

Kan Azure Cosmos DB-accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB-accounts maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt bron zoals opslagaccount of SQL-database samen met een subnet. Niet alert. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="redis-cache-contributor"></a>Bijdrager in De cache van Redis

Hiermee u Redis-caches beheren, maar er geen toegang toe hebben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Cache/redis/* | Redis-caches maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="sql-db-contributor"></a>SQL DB-bijdrager

Hiermee u SQL-databases beheren, maar geen toegang tot deze databases. U ook hun beveiligingsbeleid of hun bovenliggende SQL-servers niet beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Sql/locaties/*/gelezen |  |
> | Microsoft.Sql/servers/databases/* | SQL-databases maken en beheren |
> | Microsoft.Sql/servers/read | Retourneer de lijst met servers of de eigenschappen voor de opgegeven server. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Insights/metrics/read | Statistieken lezen |
> | Microsoft.Insights/metricDefinities/gelezen | Metrische definities lezen |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schema's/tabellen/kolommen/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingBeleid/* | Controlebeleid bewerken |
> | Microsoft.Sql/servers/databases/auditingInstellingen/* | Controle-instellingen bewerken |
> | Microsoft.Sql/servers/databases/auditRecords/gelezen | De controlerecords van de databaseblob ophalen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Verbindingsbeleid bewerken |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Beleid voor gegevensmaskering bewerken |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schema's/tabellen/kolommen/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid bewerken |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Beveiligingsstatistieken bewerken |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance Contributor

Hiermee u SQL Managed Instances en vereiste netwerkconfiguratie beheren, maar geen toegang geven tot anderen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTabellen/* |  |
> | Microsoft.Sql/locaties/*/gelezen |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Network/virtualNetworks/subnetten/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/metrics/read | Statistieken lezen |
> | Microsoft.Insights/metricDefinities/gelezen | Metrische definities lezen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
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

### <a name="sql-security-manager"></a>SQL-beveiligingsbeheer

Hiermee u het beveiligingsbeleid van SQL-servers en -databases beheren, maar geen toegang tot deze servers.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt bron zoals opslagaccount of SQL-database samen met een subnet. Niet alert. |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schema's/tabellen/kolommen/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingBeleid/* | Sql-servercontrolebeleid maken en beheren |
> | Microsoft.Sql/servers/auditingInstellingen/* | Controle-instelling voor SQL-servers maken en beheren |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Details ophalen van het uitgebreide controlebeleid voor serverblobs dat is geconfigureerd op een bepaalde server |
> | Microsoft.Sql/servers/databases/auditingBeleid/* | Controlebeleid voor SQL-server-database |
> | Microsoft.Sql/servers/databases/auditingInstellingen/* | Controle-instellingen voor SQL-server-database maken en beheren |
> | Microsoft.Sql/servers/databases/auditRecords/gelezen | De controlerecords van de databaseblob ophalen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Verbindingsbeleid voor SQL-server-database maken en beheren |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Beleid voor het maskeren van SQL-serverdatabasegegevens maken en beheren |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Details ophalen van het uitgebreide blob-controlebeleid dat is geconfigureerd voor een bepaalde database |
> | Microsoft.Sql/servers/databases/lezen | Retourneer de lijst met databases of de eigenschappen voor de opgegeven database. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schema's/lezen | Zorg voor een databaseschema. |
> | Microsoft.Sql/servers/databases/schema's/tabellen/kolommen/lezen | Een databasekolom. |
> | Microsoft.Sql/servers/databases/schema's/tabellen/kolommen/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schema's/tabellen/gelezen | Neem een databasetabel. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Waarschuwingsbeleid voor SQL-serverdatabasebeveiliging maken en beheren |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Beveiligingsstatistieken voor SQL-server-database maken en beheren |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRegels/* |  |
> | Microsoft.Sql/servers/read | Retourneer de lijst met servers of de eigenschappen voor de opgegeven server. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Waarschuwingsbeleid voor SQL-servers beveiliging maken en beheren |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="sql-server-contributor"></a>SQL Server-bijdrager

Hiermee u SQL-servers en -databases beheren, maar geen toegang tot deze servers en niet hun beveiligingsbeleid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Sql/locaties/*/gelezen |  |
> | Microsoft.Sql/servers/* | SQL-servers maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Insights/metrics/read | Statistieken lezen |
> | Microsoft.Insights/metricDefinities/gelezen | Metrische definities lezen |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schema's/tabellen/kolommen/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingBeleid/* | Controlebeleid voor SQL-servers bewerken |
> | Microsoft.Sql/servers/auditingInstellingen/* | Controle-instellingen voor SQL-servers bewerken |
> | Microsoft.Sql/servers/databases/auditingBeleid/* | Controlebeleid voor SQL-server-database |
> | Microsoft.Sql/servers/databases/auditingInstellingen/* | Controle-instellingen voor SQL-server-database bewerken |
> | Microsoft.Sql/servers/databases/auditRecords/gelezen | De controlerecords van de databaseblob ophalen |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Verbindingsbeleid voor SQL-server-database bewerken |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Beleid voor het beheren van SQL-serverdatabasegegevens bewerken |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schema's/tabellen/kolommen/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Beveiligingsbeleid voor SQL-serversdatabase bewerken |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Beveiligingsstatistieken van SQL-serverdatabase bewerken |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid voor SQL-servers bewerken |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="analytics"></a>Analyse


### <a name="azure-event-hubs-data-owner"></a>Eigenaar azure-gebeurtenishubs

Hiermee u volledige toegang krijgen tot Azure Event Hubs-bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.EventHub/* |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Gegevensontvanger azure-gebeurtenishubs

Hiermee u toegang krijgen tot Azure Event Hubs-bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.EventHub/*/eventhubs/consumentengroepen/gelezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.EventHub/*/ontvangen/actie |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-event-hubs-data-sender"></a>Afzender van Azure-gebeurtenishubs

Hiermee u toegang tot Azure Event Hubs-bronnen verzenden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.EventHub/*/eventhubs/gelezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.EventHub/*/verzenden/actie |  |
> | **NietGegevensacties** |  |
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

### <a name="data-factory-contributor"></a>Gegevensfabriek bijdrager

Maak en beheer gegevensfabrieken, evenals onderliggende bronnen binnen hen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.DataFactory/dataFabrieken/* | Maak en beheer gegevensfabrieken en onderliggende bronnen binnen hen. |
> | Microsoft.DataFactory/fabrieken/* | Maak en beheer gegevensfabrieken en onderliggende bronnen binnen hen. |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.EventGrid/eventAbonnementen/schrijven | Een gebeurtenis maken of bijwerkenAbonnement |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="data-purger"></a>Gegevenszuiverheid

Kan analysegegevens zuiveren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Insights/componenten/*/gelezen |  |
> | Microsoft.Insights/componenten/zuivering/actie | Gegevens uit Application Insights zuiveren |
> | Microsoft.OperationalInsights/workspaces/*/lezen | Gegevens over logboekanalyse weergeven |
> | Microsoft.OperationalInsights/workspaces/purge/action | Opgegeven gegevens uit werkruimte verwijderen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="hdinsight-cluster-operator"></a>HDInsight-clusteroperator

Hiermee u HDInsight-clusterconfiguraties lezen en wijzigen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewayInstellingen/actie | Gateway-instellingen voor HDInsight-cluster bekijken |
> | Microsoft.HDInsight/clusters/updateGatewayInstellingen/actie | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | Microsoft.HDInsight/clusters/configuraties/* |  |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Resources/implementaties/bewerkingen/lezen | Hiermee worden implementatiebewerkingen op- of lijsten weergegeven. |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="hdinsight-domain-services-contributor"></a>Bijdrager HDInsight Domain Services

Kan domeinservices lezen, maken, wijzigen en verwijderen die nodig zijn voor hdinsight enterprise security pakket

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.AAD/*/gelezen |  |
> | Microsoft.AAD/domainServices/*/gelezen |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Log Analytics-inzender kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. De bewakingsinstellingen voor bewerken omvatten het toevoegen van de VM-extensie aan VM's; leesopslagaccountsleutels om verzameling logboeken uit Azure Storage te kunnen configureren; Het maken en configureren van Automatiseringsaccounts; het toevoegen van oplossingen; en azure-diagnose configureren op alle Azure-resources.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensies/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangssleutels voor de opslagaccounts weergegeven. |
> | Microsoft.Compute/virtualMachines/extensies/* |  |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee maakt, werkt u de diagnostische instelling voor Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourcegroepen/implementaties/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Log Analytics Reader kan alle bewakingsgegevens bekijken en doorzoeken en bewakingsinstellingen weergeven, inclusief het bekijken van de configuratie van Azure-diagnoses op alle Azure-bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met behulp van nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/zoeken/actie | Een zoekopdracht uitvoeren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hiermee worden de gedeelde sleutels voor de werkruimte opgehaald. Deze sleutels worden gebruikt om Microsoft Operational Insights-agents met de werkruimte te verbinden. |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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


### <a name="blockchain-member-node-access-preview"></a>Toegang tot blockchain-lidknooppunt (voorbeeld)

Biedt toegang tot blockchain-lidknooppunten

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Blockchain/blockchainLeden/transactionNodes/read | Hiermee worden bestaande Blockchain-lidtransactieknooppunt(s) of lijsten weergegeven. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Blockchain/blockchainLeden/transactionNodes/connect/action | Maakt verbinding met een Blockchain-transactieknooppunt voor leden. |
> | **NietGegevensacties** |  |
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

## <a name="ai--machine-learning"></a>AI + machine learning


### <a name="cognitive-services-contributor"></a>Medewerker cognitieve diensten

Hiermee u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Krijgt de functies van een abonnement. |
> | Microsoft.Features/providers/functies/lezen | Krijgt de functie van een abonnement in een bepaalde resourceprovider. |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee maakt, werkt u de diagnostische instelling voor Analysis Server |
> | Microsoft.Insights/logDefinities/lezen | Logdefinities lezen |
> | Microsoft.Insights/metricdefinities/gelezen | Metrische definities lezen |
> | Microsoft.Insights/metrics/read | Statistieken lezen |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/implementaties/bewerkingen/lezen | Hiermee worden implementatiebewerkingen op- of lijsten weergegeven. |
> | Microsoft.Resources/abonnementen/operationeleresultaten/gelezen | Profiteer van de resultaten van de abonnementsbewerking. |
> | Microsoft.Resources/abonnementen/lezen | Krijgt de lijst met abonnementen. |
> | Microsoft.Resources/abonnementen/resourcegroepen/implementaties/* |  |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="cognitive-services-data-reader-preview"></a>Gegevenslezer van cognitive services (voorbeeld)

Hiermee u gegevens van Cognitive Services lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NietGegevensacties** |  |
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

### <a name="cognitive-services-user"></a>Gebruiker van cognitieve services

Hiermee u toetsen van cognitieve services lezen en vermelden.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/actie | Lijsttoetsen |
> | Microsoft.Insights/alertRegels/lezen | Een klassieke metrische waarschuwing lezen |
> | Microsoft.Insights/diagnosticSettings/read | Een diagnostische instelling voor resources lezen |
> | Microsoft.Insights/logDefinities/lezen | Logdefinities lezen |
> | Microsoft.Insights/metricdefinities/gelezen | Metrische definities lezen |
> | Microsoft.Insights/metrics/read | Statistieken lezen |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/bewerkingen/lezen | Hiermee worden implementatiebewerkingen op- of lijsten weergegeven. |
> | Microsoft.Resources/abonnementen/operationeleresultaten/gelezen | Profiteer van de resultaten van de abonnementsbewerking. |
> | Microsoft.Resources/abonnementen/lezen | Krijgt de lijst met abonnementen. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NietGegevensacties** |  |
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

## <a name="mixed-reality"></a>Mixed reality


### <a name="spatial-anchors-account-contributor"></a>Rekeningbijdrage voor ruimtelijke ankers

Hiermee u ruimtelijke ankers in uw account beheren, maar deze niet verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/maken/actie | Ruimtelijke ankers maken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ontdek nabijgelegen ruimtelijke ankers |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/gelezen | Ruimtelijke ankers lokaliseren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens indienen om de kwaliteit van de Azure Spatial Anchors-service te verbeteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Eigenschappen ruimtelijke ankers bijwerken |
> | **NietGegevensacties** |  |
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

### <a name="spatial-anchors-account-owner"></a>Eigenaar van accountruimtelijke ankers

Hiermee u ruimtelijke ankers in uw account beheren, inclusief het verwijderen ervan

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/maken/actie | Ruimtelijke ankers maken |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/verwijderen | Ruimtelijke ankers verwijderen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ontdek nabijgelegen ruimtelijke ankers |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/gelezen | Ruimtelijke ankers lokaliseren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens indienen om de kwaliteit van de Azure Spatial Anchors-service te verbeteren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Eigenschappen ruimtelijke ankers bijwerken |
> | **NietGegevensacties** |  |
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

### <a name="spatial-anchors-account-reader"></a>Accountlezer ruimtelijke ankers

Hiermee u eigenschappen van ruimtelijke ankers in uw account vinden en lezen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ontdek nabijgelegen ruimtelijke ankers |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/gelezen | Ruimtelijke ankers lokaliseren |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens indienen om de kwaliteit van de Azure Spatial Anchors-service te verbeteren |
> | **NietGegevensacties** |  |
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

## <a name="integration"></a>Integratie


### <a name="api-management-service-contributor"></a>Api-beheerservice bijdrager

Kan service en api's beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/* | API-beheerservice maken en beheren |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="api-management-service-operator-role"></a>Functie API Management Service Operator

Kan service beheren, maar niet de API's

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/*/lezen | API-beheerservice-exemplaren lezen |
> | Microsoft.ApiManagement/service/back-up/actie | Back-up van API-beheerservice voor de opgegeven container in een door de gebruiker opgegeven opslagaccount |
> | Microsoft.ApiManagement/service/delete | Api-beheerservice-instantie verwijderen |
> | Microsoft.ApiManagement/service/beheerimplementaties/actie | SKU/eenheden wijzigen, regionale implementaties van API Management Service toevoegen/verwijderen |
> | Microsoft.ApiManagement/service/lezen | Metagegevens voor een API-beheerservice-instantie lezen |
> | Microsoft.ApiManagement/service/restore/action | API-beheerservice herstellen vanuit de opgegeven container in een door de gebruiker opgegeven opslagaccount |
> | Microsoft.ApiManagement/service/updatecertificaat/actie | TLS/SSL-certificaat uploaden voor een API Management Service |
> | Microsoft.ApiManagement/service/updatehostname/actie | Aangepaste domeinnamen voor een API-beheerservice instellen, bijwerken of verwijderen |
> | Microsoft.ApiManagement/service/write | Instantie API Management Service maken of bijwerken |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Sleutels ophalen die zijn gekoppeld aan de gebruiker |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="api-management-service-reader-role"></a>Api Management Service Reader Rol

Alleen-lezen toegang tot service en API's

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ApiManagement/service/*/lezen | API-beheerservice-exemplaren lezen |
> | Microsoft.ApiManagement/service/lezen | Metagegevens voor een API-beheerservice-instantie lezen |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Sleutels ophalen die zijn gekoppeld aan de gebruiker |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="app-configuration-data-owner"></a>Eigenaar van app-configuratiegegevens

Hiermee u volledige toegang krijgen tot app-configuratiegegevens.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | Microsoft.AppConfiguration/configurationStores/*/write |  |
> | Microsoft.AppConfiguration/configurationStores/*/delete |  |
> | **NietGegevensacties** |  |
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

### <a name="app-configuration-data-reader"></a>Gegevenslezer app-configuratie

Hiermee u leestoegang krijgen tot app-configuratiegegevens.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus-gegevens-eigenaar

Hiermee u volledige toegang krijgen tot Azure Service Bus-bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.ServiceBus/* |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus-gegevensontvanger

Hiermee u toegang krijgen tot Azure Service Bus-bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ServiceBus/*/wachtrijen/lezen |  |
> | Microsoft.ServiceBus/*/onderwerpen/gelezen |  |
> | Microsoft.ServiceBus/*/topics/abonnementen/gelezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.ServiceBus/*/ontvangen/actie |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-service-bus-data-sender"></a>Afzender van Azure-servicebusgegevens

Hiermee u toegang verzenden tot Azure Service Bus-bronnen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ServiceBus/*/wachtrijen/lezen |  |
> | Microsoft.ServiceBus/*/onderwerpen/gelezen |  |
> | Microsoft.ServiceBus/*/topics/abonnementen/gelezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.ServiceBus/*/verzenden/actie |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-stack-registration-owner"></a>Eigenaar Azure Stack-registratie

Hiermee u Azure Stack-registraties beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.AzureStack/registraties/producten/*/actie |  |
> | Microsoft.AzureStack/registraties/producten/gelezen | Haalt de eigenschappen van een Azure Stack Marketplace-product |
> | Microsoft.AzureStack/registraties/gelezen | Haalt de eigenschappen van een Azure Stack-registratie |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Inzender

Hiermee u eventgrid-abonnementsbewerkingen beheren.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.EventGrid/eventAbonnementen/* |  |
> | Microsoft.EventGrid/topicTypes/eventAbonnementen/gelezen | Globale gebeurtenisabonnementen aanbieden op onderwerptype |
> | Microsoft.EventGrid/locaties/gebeurtenisAbonnementen/gelezen | Regionale evenementenabonnementen weergeven |
> | Microsoft.EventGrid/locaties/topicTypes/gebeurtenisAbonnementen/gelezen | Regionale gebeurtenisabonnementen aanbieden op onderwerptype |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription Reader

Hiermee u eventgrid-gebeurtenisabonnementen lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.EventGrid/eventAbonnementen/gelezen | Een gebeurtenis lezenAbonnement |
> | Microsoft.EventGrid/topicTypes/eventAbonnementen/gelezen | Globale gebeurtenisabonnementen aanbieden op onderwerptype |
> | Microsoft.EventGrid/locaties/gebeurtenisAbonnementen/gelezen | Regionale evenementenabonnementen weergeven |
> | Microsoft.EventGrid/locaties/topicTypes/gebeurtenisAbonnementen/gelezen | Regionale gebeurtenisabonnementen aanbieden op onderwerptype |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="intelligent-systems-account-contributor"></a>Bijdrage aan intelligente systemen-account

Hiermee u accounts van Intelligent Systems beheren, maar er geen toegang toe hebben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.IntelligentSystems/accounts/* | Intelligente systeemaccounts maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="logic-app-contributor"></a>Logica-app-inzender

Hiermee u logische apps beheren, maar de toegang er niet toe wijzigen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangssleutels voor de opslagaccounts weergegeven. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retourneer de opslagrekening met het opgegeven account. |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Hiermee maakt, werkt u de diagnostische instelling voor Analysis Server |
> | Microsoft.Insights/logdefinities/* | Deze toestemming is nodig voor gebruikers die via de portal toegang nodig hebben tot activiteitslogboeken. Lijstlogboekcategorieën in activiteitenlogboek. |
> | Microsoft.Insights/metricDefinities/* | Lees metrische definities (lijst met beschikbare metrische typen voor een resource). |
> | Microsoft.Logic/* | Beheert de bronnen van Logic Apps. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/operationeleresultaten/gelezen | Profiteer van de resultaten van de abonnementsbewerking. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Web/connectionGateways/* | Een verbindingsgateway maken en beheren. |
> | Microsoft.Web/verbindingen/* | Een verbinding maken en beheren. |
> | Microsoft.Web/customApis/* | Hiermee maakt en beheert u een aangepaste API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/gelezen | De eigenschappen van een App-serviceplan downloaden |
> | Microsoft.Web/sites/functies/listSecrets/action | Lijst Functie geheimen. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Hiermee u logische apps lezen, inschakelen en uitschakelen, maar deze niet bewerken of bijwerken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/*/gelezen | Waarschuwingsregels voor Inzichten lezen |
> | Microsoft.Insights/metricAlerts/*/gelezen |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Krijgt diagnostische instellingen voor Logic Apps |
> | Microsoft.Insights/metricDefinities/*/gelezen | Hier worden de beschikbare statistieken voor Logic Apps weergegeven. |
> | Microsoft.Logic/*/lezen | Hiermee leest u de bronnen van Logic Apps. |
> | Microsoft.Logic/workflows/uitschakelen/actie | Hiermee schakelt u de werkstroom uit. |
> | Microsoft.Logic/workflows/inschakelen/actie | Hiermee schakelt u de werkstroom in. |
> | Microsoft.Logic/workflows/valideren/uitvoeren/uitvoeren | Valideert de werkstroom. |
> | Microsoft.Resources/implementaties/bewerkingen/lezen | Hiermee worden implementatiebewerkingen op- of lijsten weergegeven. |
> | Microsoft.Resources/abonnementen/operationeleresultaten/gelezen | Profiteer van de resultaten van de abonnementsbewerking. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Web/connectionGateways/*/lezen | Lees Verbindingsgateways. |
> | Microsoft.Web/verbindingen/*/lezen | Lees Verbindingen. |
> | Microsoft.Web/customApis/*/lezen | Lees Aangepaste API. |
> | Microsoft.Web/serverFarms/gelezen | De eigenschappen van een App-serviceplan downloaden |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="identity"></a>Identiteit


### <a name="managed-identity-contributor"></a>Medewerker beheerde identiteit

Gebruikersnaam maken, lezen, bijwerken en verwijderen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Krijgt een bestaande door de gebruiker toegewezen identiteit |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Hiermee maakt u een nieuwe door de gebruiker toegewezen identiteit of worden de tags bijgewerkt die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Een bestaande door de gebruiker toegewezen identiteit verwijderen |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="managed-identity-operator"></a>Beheerde identiteitsoperator

Gebruikerstoegewezen identiteit lezen en toewijzen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="security"></a>Beveiliging


### <a name="azure-sentinel-contributor"></a>Azure Sentinel-bijdrager

Azure Sentinel-bijdrager

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met behulp van nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/*/lezen | Gegevens over logboekanalyse weergeven |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/oplossingen/lezen | Download spannende OMS-oplossing |
> | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren op de gegevens in de werkruimte |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataBronnen/lezen | Haal gegevensbronnen onder een werkruimte. |
> | Microsoft.Insights/werkmappen/* |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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
        "Microsoft.OperationalInsights/workspaces/*/read",
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

### <a name="azure-sentinel-reader"></a>Azure Sentinel-lezer

Azure Sentinel-lezer

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met behulp van nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/*/lezen | Gegevens over logboekanalyse weergeven |
> | Microsoft.OperationalInsights/workspaces/LinkedServices/read | Krijg gekoppelde services onder bepaalde werkruimte. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Krijgt een opgeslagen zoekopdracht |
> | Microsoft.OperationsManagement/oplossingen/lezen | Download spannende OMS-oplossing |
> | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren op de gegevens in de werkruimte |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataBronnen/lezen | Haal gegevensbronnen onder een werkruimte. |
> | Microsoft.Insights/werkmappen/lezen | Een werkmap lezen |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
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

### <a name="azure-sentinel-responder"></a>Azure Sentinel Responder

Azure Sentinel Responder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.SecurityInsights/incidenten/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met behulp van nieuwe engine. |
> | Microsoft.OperationalInsights/workspaces/*/lezen | Gegevens over logboekanalyse weergeven |
> | Microsoft.OperationalInsights/workspaces/dataBronnen/lezen | Haal gegevensbronnen onder een werkruimte. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Krijgt een opgeslagen zoekopdracht |
> | Microsoft.OperationsManagement/oplossingen/lezen | Download spannende OMS-oplossing |
> | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren op de gegevens in de werkruimte |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataBronnen/lezen | Haal gegevensbronnen onder een werkruimte. |
> | Microsoft.Insights/werkmappen/lezen | Een werkmap lezen |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
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

### <a name="key-vault-contributor"></a>Belangrijke vault-bijdrager

Hiermee u sleutelkluizen beheren, maar geen toegang tot deze kluizen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | Microsoft.KeyVault/locaties/verwijderdeVaults/purge/action | Een zachte verwijderde sleutelkluis wissen |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

U beveiligingsbeleid bekijken, beveiligingsstatussen bekijken, beveiligingsbeleid bewerken, waarschuwingen en aanbevelingen bekijken, waarschuwingen en aanbevelingen afwijzen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Authorization/policyAssignments/* | Beleidstoewijzingen maken en beheren |
> | Microsoft.Authorization/policyDefinities/* | Beleidsdefinities maken en beheren |
> | Microsoft.Authorization/policySetDefinities/* | Beleidssets maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | Microsoft.operationalInsights/workspaces/*/read | Gegevens over logboekanalyse weergeven |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Security/* | Beveiligingscomponenten en -beleid maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="security-assessment-contributor"></a>Medewerker beveiligingsbeoordeling

Hiermee u beoordelingen pushen naar Security Center

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Security/assessments/write | Beveiligingsupdaten voor uw abonnement maken of bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="security-manager-legacy"></a>Beveiligingsmanager (Legacy)

Dit is een erfenis rol. Gebruik in plaats daarvan beveiligingsbeheerder.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.ClassicCompute/*/lezen | Lees configuratie-informatie klassieke virtuele machines |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Schrijfconfiguratie voor klassieke virtuele machines |
> | Microsoft.ClassicNetwork/*/lezen | Lees configuratie-informatie over klassiek netwerk |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Security/* | Beveiligingscomponenten en -beleid maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="security-reader"></a>Beveiligingslezer

Kan aanbevelingen en waarschuwingen bekijken, beveiligingsbeleid bekijken, beveiligingsstatussen bekijken, maar geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.operationalInsights/workspaces/*/read | Gegevens over logboekanalyse weergeven |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Security/*/lezen | Beveiligingscomponenten en -beleid lezen |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs-gebruiker

Hiermee u uw virtuele machines verbinden, starten, opnieuw starten en afsluiten in uw Azure DevTest Labs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Compute/availabilitySets/lezen | De eigenschappen van een beschikbaarheidsset ophalen |
> | Microsoft.Compute/virtualMachines/*/lezen | Lees de eigenschappen van een virtuele machine (VM-formaten, runtime-status, VM-extensies, enz.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Schakelt de virtuele machine uit en geeft de compute resources vrij |
> | Microsoft.Compute/virtualMachines/lezen | De eigenschappen van een virtuele machine krijgen |
> | Microsoft.Compute/virtualMachines/opnieuw opstarten/actie | Start de virtuele machine opnieuw op |
> | Microsoft.Compute/virtualMachines/start/actie | Start de virtuele machine |
> | Microsoft.DevTestLab/*/read | Lees de eigenschappen van een lab |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Claim een willekeurige claimbare virtuele machine in het lab. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Maak virtuele machines in een lab. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/actie | Zorg ervoor dat de huidige gebruiker een geldig profiel in het lab heeft. |
> | Microsoft.DevTestLab/labs/formules/verwijderen | Formules verwijderen. |
> | Microsoft.DevTestLab/labs/formules/lezen | Lees formules. |
> | Microsoft.DevTestLab/labs/formules/schrijven | Formules toevoegen of wijzigen. |
> | Microsoft.DevTestLab/labs/policySets/evaluerenBeleid/actie | Evalueert het labbeleid. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Neem de eigendom van een bestaande virtuele machine |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Geeft de toepasselijke start-/stopschema's aan, indien van toepassing. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Hiermee krijgt u een tekenreeks die de inhoud van het RDP-bestand voor de virtuele machine vertegenwoordigt |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Voegt zich bij een backend-adresgroep van de load balancer. Niet alertbaar. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Hiermee wordt een inkomende nat-regel voor load balancer verbonden. Niet alertbaar. |
> | Microsoft.Network/networkInterfaces/*/gelezen | Lees de eigenschappen van een netwerkinterface (bijvoorbeeld alle load balancers waar de netwerkinterface deel van uitmaakt) |
> | Microsoft.Network/networkInterfaces/join/action | Hiermee wordt een virtuele machine verbonden aan een netwerkinterface. Niet alertbaar. |
> | Microsoft.Network/networkInterfaces/read | Krijgt een netwerkinterfacedefinitie.  |
> | Microsoft.Network/networkInterfaces/write | Hiermee maakt u een netwerkinterface of werkt u een bestaande netwerkinterface bij.  |
> | Microsoft.Network/publicIPAddresses/*/read | De eigenschappen van een openbaar IP-adres lezen |
> | Microsoft.Network/publicIPAddresses/join/action | Voegt zich bij een openbaar ip-adres. Niet alertbaar. |
> | Microsoft.Network/publicIPAddresses/read | Krijgt een openbare ip-adresdefinitie. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Sluit zich aan bij een virtueel netwerk. Niet alertbaar. |
> | Microsoft.Resources/implementaties/bewerkingen/lezen | Hiermee worden implementatiebewerkingen op- of lijsten weergegeven. |
> | Microsoft.Resources/implementaties/lezen | Hiermee worden implementaties in- of opsomt. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Lijsten met beschikbare formaten waarin de virtuele machine kan worden bijgewerkt |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="lab-creator"></a>Lab Creator

Hiermee u uw beheerde labs maken, beheren en verwijderen onder uw Azure Lab-accounts.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.LabServices/labAccounts/*/gelezen |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Maak een lab in een lab account. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Regionale beschikbaarheidsgegevens opvragen voor elke groottecategorie die is geconfigureerd onder een labaccount |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | Profiteer van de prijzen en beschikbaarheid van combinaties van groottes, geografische gebieden en besturingssystemen voor het labaccount. |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action | Krijg kernbeperkingen en gebruik voor dit abonnement |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="monitor"></a>Controleren


### <a name="application-insights-component-contributor"></a>Componentbijdrager voor toepassingsinzichten

Onderdelen van Application Insights beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Klassieke waarschuwingsregels maken en beheren |
> | Microsoft.Insights/metricAlerts/* | Nieuwe waarschuwingsregels maken en beheren |
> | Microsoft.Insights/componenten/* | Insights-componenten maken en beheren |
> | Microsoft.Insights/webtests/* | Insights-webtests maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="application-insights-snapshot-debugger"></a>Momentopnamefoutfout van toepassingsinzichten

Geeft gebruikers toestemming om foutopsporingsmomentopnamen te bekijken en te downloaden die zijn verzameld met de Foutopsporing van de momentopname van Application Insights. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [Eigenaar](#owner) of [Inzender.](#contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/componenten/*/gelezen |  |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="monitoring-contributor"></a>Monitorbijdrager

Kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.AlertsBeheer/waarschuwingen/* |  |
> | Microsoft.AlertsManagement/alertsSamenvatting/* |  |
> | Microsoft.Insights/actiegroepen/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/Waarschuwingsregels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Insights/componenten/* | Insights-componenten maken en beheren |
> | Microsoft.Insights/DiagnosticSettings/* | Hiermee maakt, werkt u de diagnostische instelling voor Analysis Server |
> | Microsoft.Insights/eventtypes/* | Activiteitslogboekgebeurtenissen (beheergebeurtenissen) in een abonnement weergeven. Deze toestemming is van toepassing op zowel programmatische als portaltoegang tot het activiteitenlogboek. |
> | Microsoft.Insights/LogDefinities/* | Deze toestemming is nodig voor gebruikers die via de portal toegang nodig hebben tot activiteitslogboeken. Lijstlogboekcategorieën in activiteitenlogboek. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Lees metrische definities (lijst met beschikbare metrische typen voor een resource). |
> | Microsoft.Insights/Metrics/* | Lees statistieken voor een resource. |
> | Microsoft.Insights/Register/Actie | De Microsoft Insights-provider registreren |
> | Microsoft.Insights/geplande queryregels/* |  |
> | Microsoft.Insights/webtests/* | Insights-webtests maken en beheren |
> | Microsoft.Insights/werkmappen/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log analytics-oplossingspakketten lezen/schrijven/verwijderen. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Opslaande zoekopdrachten voor logboekanalyses lezen/schrijven/verwijderen. |
> | Microsoft.OperationalInsights/workspaces/zoeken/actie | Een zoekopdracht uitvoeren |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hiermee worden de gedeelde sleutels voor de werkruimte opgehaald. Deze sleutels worden gebruikt om Microsoft Operational Insights-agents met de werkruimte te verbinden. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Lees/write/delete log analytics storage insight configuraties. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationInstellingen/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsManagement/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
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

### <a name="monitoring-metrics-publisher"></a>Uitgever statistieken controleren

Hiermee kunnen publicatiestatistieken worden weergegeven tegen Azure-bronnen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Insights/Register/Actie | De Microsoft Insights-provider registreren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.Insights/Metrics/Write | Statistieken schrijven |
> | **NietGegevensacties** |  |
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

### <a name="monitoring-reader"></a>Monitoring Reader

Kan alle monitoringgegevens (metrics, logs, etc.) lezen. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.OperationalInsights/workspaces/zoeken/actie | Een zoekopdracht uitvoeren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="workbook-contributor"></a>Werkmapinzender

Kan gedeelde werkmappen opslaan.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Insights/werkmappen/schrijven | Een werkmap maken of bijwerken |
> | Microsoft.Insights/werkmappen/verwijderen | Een werkmap verwijderen |
> | Microsoft.Insights/werkmappen/lezen | Een werkmap lezen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="workbook-reader"></a>Werkmaplezer

Kan werkmappen lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | microsoft.insights/werkmappen/lezen | Een werkmap lezen |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="management--governance"></a>Management + governance


### <a name="automation-job-operator"></a>Operator van automatiseringswerktaak

Taken maken en beheren met behulp van Automatiseringsrunbooks.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Leest hybride runbook worker resources |
> | Microsoft.Automation/automationAccounts/jobs/read | Krijgt een Azure Automation-taak |
> | Microsoft.Automation/automationAccounts/taken/hervatten/actie | Hervat een azure-automatiseringstaak |
> | Microsoft.Automation/automationAccounts/taken/stop/action | Hiermee stopt u een azure-automatiseringstaak |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Krijgt een Azure Automation-taakstroom |
> | Microsoft.Automation/automationAccounts/taken/suspend/action | Hiermee wordt een azure-automatiseringstaak opschorten |
> | Microsoft.Automation/automationAccounts/jobs/write | Hiermee maakt u een azure-automatiseringstaak |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Krijgt de output van een taak |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

Automatiseringsoperators kunnen taken starten, stoppen, opschorten en hervatten

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Leest hybride runbook worker resources |
> | Microsoft.Automation/automationAccounts/jobs/read | Krijgt een Azure Automation-taak |
> | Microsoft.Automation/automationAccounts/taken/hervatten/actie | Hervat een azure-automatiseringstaak |
> | Microsoft.Automation/automationAccounts/taken/stop/action | Hiermee stopt u een azure-automatiseringstaak |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Krijgt een Azure Automation-taakstroom |
> | Microsoft.Automation/automationAccounts/taken/suspend/action | Hiermee wordt een azure-automatiseringstaak opschorten |
> | Microsoft.Automation/automationAccounts/jobs/write | Hiermee maakt u een azure-automatiseringstaak |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Krijgt een Azure Automation-taakschema |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Hiermee maakt u een azure automation-taakschema |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Krijgt de werkruimte gekoppeld aan het automatiseringsaccount |
> | Microsoft.Automation/automationAccounts/gelezen | Krijgt een Azure Automation-account |
> | Microsoft.Automation/automationAccounts/runbooks/read | Krijgt een Azure Automation-runbook |
> | Microsoft.Automation/automationAccounts/schema's/gelezen | Krijgt een Azure Automation-planningsplanningsitem |
> | Microsoft.Automation/automationAccounts/schema's/schrijven | Een Azure Automation-planningsplanningsitem maken of bijwerken |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Krijgt de output van een taak |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="automation-runbook-operator"></a>Operator voor automatiseringsrunbook

Lees Runbook-eigenschappen - om taken van het runbook te kunnen maken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Automation/automationAccounts/runbooks/read | Krijgt een Azure Automation-runbook |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine Onboarding

Kan aan boord van Azure Connected Machines.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.HybridCompute/machines/lezen | Lees alle Azure Arc-machines |
> | Microsoft.HybridCompute/machines/schrijven | Schrijft een Azure Arc-machines |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Ontvang een toewijzing voor gastconfiguratie. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine Resource Administrator

Kan Azure Connected Machines lezen, schrijven, verwijderen en opnieuw ingebouwde.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.HybridCompute/machines/lezen | Lees alle Azure Arc-machines |
> | Microsoft.HybridCompute/machines/schrijven | Schrijft een Azure Arc-machines |
> | Microsoft.HybridCompute/machines/verwijderen | Een Azure Arc-machines verwijderen |
> | Microsoft.HybridCompute/machines/reconnect/action | Verbindt een Azure Arc-machine opnieuw |
> | Microsoft.HybridCompute/machines/extensies/schrijven | Installeert of werkt een Azure Arc-extensie |
> | Microsoft.HybridCompute/*/lezen |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
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

### <a name="billing-reader"></a>Lezer voor facturering

Geeft leestoegang tot factureringsgegevens

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Billing/*/gelezen | Factureringsgegevens lezen |
> | Microsoft.Commerce/*/gelezen |  |
> | Microsoft.Verbruik/*/gelezen |  |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | Microsoft.CostManagement/*/lezen |  |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="blueprint-contributor"></a>Blauwdrukbijdrager

Kan blauwdrukdefinities beheren, maar deze niet toewijzen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Blueprint/blauwdrukken/* | Maak en beheer blauwdrukdefinities of blauwdrukartefacten. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="blueprint-operator"></a>Blueprint Operator

Kan bestaande gepubliceerde blauwdrukken toewijzen, maar kan geen nieuwe blauwdrukken maken. Houd er rekening mee dat dit alleen werkt als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Blueprint/blueprintOpdrachten/* | Blauwdruktoewijzingen maken en beheren. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="cost-management-contributor"></a>Bijdrager kostenbeheer

Kan de kosten bekijken en kostenconfiguratie beheren (bijv. budgetten, export)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Verbruik/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Facturering/factureringSperioden/gelezen |  |
> | Microsoft.Resources/abonnementen/lezen | Krijgt de lijst met abonnementen. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Advisor/configuraties/lezen | Configuraties oppakken |
> | Microsoft.Advisor/aanbevelingen/gelezen | Leest aanbevelingen |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="cost-management-reader"></a>Kostenbeheerlezer

Kan kostengegevens en configuratie weergeven (bijv. budgetten, export)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Verbruik/*/gelezen |  |
> | Microsoft.CostManagement/*/lezen |  |
> | Microsoft.Facturering/factureringSperioden/gelezen |  |
> | Microsoft.Resources/abonnementen/lezen | Krijgt de lijst met abonnementen. |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Advisor/configuraties/lezen | Configuraties oppakken |
> | Microsoft.Advisor/aanbevelingen/gelezen | Leest aanbevelingen |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="managed-application-contributor-role"></a>Rol beheerde toepassingsbijdrager

Hiermee u beheerde toepassingsbronnen maken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.Solutions/applicaties/* |  |
> | Microsoft.Solutions/register/actie | Registreer bij Solutions. |
> | Microsoft.Resources/abonnementen/resourceGroepen/* |  |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="managed-application-operator-role"></a>Functie beheerde toepassingsoperator

Hiermee u acties lezen en uitvoeren op resources voor beheerde toepassingen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.Solutions/toepassingen/lezen | Hiermee haalt u een lijst met toepassingen op. |
> | Microsoft.Solutions/*/actie |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="managed-applications-reader"></a>Lezer van beheerde toepassingen

Hiermee u bronnen in een beheerde app lezen en JIT-toegang aanvragen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Verwijderrol registratietoewijzing managed services

Met de verwijderrol beheerde servicesregistratietoewijzing kunnen de gebruikers van de beherende tenant de registratietoewijzing verwijderen die aan hun tenant is toegewezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.ManagedServices/registratieOpdrachten/lezen | Hiermee haalt u een lijst met registratietoewijzingen voor Managed Services op. |
> | Microsoft.ManagedServices/registrationAssignments/delete | Hiermee verwijdert u de registratietoewijzing voor Managed Services. |
> | Microsoft.ManagedServices/operationStatuss/read | Hiermee leest u de bedrijfsstatus voor de resource. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="management-group-contributor"></a>Medewerker managementgroep

Rol van directiegroepbijdrager

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Management/beheerGroepen/verwijderen | Beheergroep verwijderen. |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | Microsoft.Management/beheerGroepen/abonnementen/verwijderen | De-associates abonnement van de managementgroep. |
> | Microsoft.Management/beheerGroepen/abonnementen/schrijven | Koppelt bestaand abonnement aan de beheergroep. |
> | Microsoft.Management/managementGroepen/schrijven | Een beheergroep maken of bijwerken. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="management-group-reader"></a>Management Group Reader

Management Group Reader Rol

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Management/managementGroepen/lezen | Lijstbeheergroepen voor de geverifieerde gebruiker. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="new-relic-apm-account-contributor"></a>Nieuwe Relic APM-accountbijdrager

Hiermee u nieuwe Reliekenapplicatie Prestatiebeheeraccounts en -toepassingen beheren, maar geen toegang tot deze accounts.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="policy-insights-data-writer-preview"></a>Beleidsinzichten Gegevensschrijver (voorbeeld)

Hiermee u leestoegang tot resourcebeleid en schrijftoegang tot beleidsgebeurtenissen voor resourcecomponenten.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/policyassignments/read | Informatie over een beleidsopdracht. |
> | Microsoft.Authorization/policydefinitions/read | Informatie over een beleidsdefinitie. |
> | Microsoft.Authorization/policysetdefinities/gelezen | Informatie over een beleidssetdefinitie. |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/actie | Controleer de nalevingsstatus van een bepaald onderdeel aan de hand van het gegevensbeleid. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Log de beleidsgebeurtenissen voor resourcecomponenten in. |
> | **NietGegevensacties** |  |
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

Gebruikers met rechten om resourcebeleid te maken/wijzigen, ondersteuningsticket te maken en bronnen/hiërarchie te lezen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | */lezen | Lees bronnen van alle soorten, behalve geheimen. |
> | Microsoft.Authorization/policyassignments/* | Beleidstoewijzingen maken en beheren |
> | Microsoft.Autorisatie/beleidsdefinities/* | Beleidsdefinities maken en beheren |
> | Microsoft.Authorization/policysetdefinities/* | Beleidssets maken en beheren |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="site-recovery-contributor"></a>Bijdrage raan van siteherstel

Hiermee u de service Siteherstel beheren, behalve het maken van kluizen en roltoewijzing

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.RecoveryServices/locaties/toegewezenStamp/gelezen | GetAllocatedStamp is interne bewerking die door service wordt gebruikt |
> | Microsoft.RecoveryServices/locaties/toewijzingStamp/actie | AssignStamp is interne bewerking die wordt gebruikt door service |
> | Microsoft.RecoveryServices/Vaults/certificaten/schrijven | Met de bewerking Resourcecertificaat bijwerken wordt het bron-/kluisreferentiecertificaat bijgewerkt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot kluis maken en beheren |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/* | Geregistreerde identiteiten maken en beheren |
> | Microsoft.RecoveryServices/kluizen/replicatieWaarschuwingsInstellingen/* | Instellingen voor replicatiewaarschuwingen maken of bijwerken |
> | Microsoft.RecoveryServices/kluizen/replicatieGebeurtenissen/gelezen | Alle gebeurtenissen lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/* | Replicatiestoffen maken en beheren |
> | Microsoft.RecoveryServices/kluizen/replicatieTaken/* | Replicatietaken maken en beheren |
> | Microsoft.RecoveryServices/kluizen/replicatiebeleid/* | Replicatiebeleid maken en beheren |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/* | Herstelplannen maken en beheren |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Opslagconfiguratie van de vault van Recovery Services maken en beheren |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Kluizen/gebruik/lezen | Retourneert gebruiksgegevens voor een Vault voor Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De Vault Token-bewerking kan worden gebruikt om Vault Token te krijgen voor backend-bewerkingen op vaultlevel. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Waarschuwingen voor de kluis van de herstelservices lezen |
> | Microsoft.RecoveryServices/Vaults/monitoringConfiguraties/meldingConfiguratie/gelezen |  |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | Microsoft.RecoveryServices/kluizen/replicatieOperationStatus/gelezen | Lees een vault replication-status |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="site-recovery-operator"></a>Beheerder van siteherstel

Hiermee u failover en failback, maar niet uitvoeren van andere Site Recovery management operaties

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Network/virtualNetworks/lezen | De definitie van het virtuele netwerk |
> | Microsoft.RecoveryServices/locaties/toegewezenStamp/gelezen | GetAllocatedStamp is interne bewerking die door service wordt gebruikt |
> | Microsoft.RecoveryServices/locaties/toewijzingStamp/actie | AssignStamp is interne bewerking die wordt gebruikt door service |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | De bewerking Uitgebreide informatie van een object krijgen, wordt de uitgebreide informatie van een object weergegeven die de Azure-bron van het type ?vault vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten opvragen kan worden gebruikt, waarbij de bedrijfsstatus en het resultaat voor de asynchrone ingediende bewerking worden uitgevoerd |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/gelezen | De bewerking Containers worden gebruikt, zorg ervoor dat de containers zijn geregistreerd voor een resource. |
> | Microsoft.RecoveryServices/kluizen/replicatieAlertInstellingen/gelezen | Alle waarschuwingsinstellingen lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieGebeurtenissen/gelezen | Alle gebeurtenissen lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/checkConsistentie/actie | Controleert de consistentie van de stof |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/gelezen | Lees alle stoffen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/reassociateGateway/actie | Gateway opnieuw koppelen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/vernieuwencertificaat/actie | Certificaat voor stof verlengen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieNetwerken/gelezen | Alle netwerken lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieNetwerken/replicatieNetworkMappings/gelezen | Lees alle netwerktoewijzingen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/gelezen | Lees alle beveiligingscontainers |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectableItems/read | Lees alle beschermbare objecten |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/applyRecoveryPoint/actie | Herstelpunt toepassen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplande failover |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/gelezen | Beschermde objecten lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/recoveryPoints/read | Lees alle replicatieherstelpunten |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/repairReplicatie/actie | Replicatie herstellen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/reProtect/action | Beveiligd beveiligd object opnieuw beveiligen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/switchprotection/action | Schakelbeveiligingscontainer |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/testFailover/actie | Failover testen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/testFailoverCleanup/actie | Failover opruimen testen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/updateMobilityService/actie | Mobiliteitsservice bijwerken |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectionContainerMappings/gelezen | Lees alle Beveiligingscontainertoewijzingen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieRecoveryServicesProviders/gelezen | Lees alle Recovery Services Providers |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Provider vernieuwen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieStorageClassifications/gelezen | Lees alle opslagclassificaties |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieStorageClassifications/replicatieStorageClassificationMappings/gelezen | Lees alle opslagclassificatietoewijzingen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatievCenters/gelezen | Lees alle vCenters |
> | Microsoft.RecoveryServices/kluizen/replicatieTaken/* | Replicatietaken maken en beheren |
> | Microsoft.RecoveryServices/kluizen/replicatiebeleid/gelezen | Elk beleid lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/failoverCommit/actie | Failover Commit-herstelplan |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/geplandeFailover/actie | Gepland herstelplan voor mislukte herstel |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/gelezen | Lees alle herstelplannen |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/reProtect/actie | Herstelplan opnieuw beveiligen |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/testFailover/actie | Herstelplan voor failovertesten |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/testFailoverCleanup/actie | Herstelplan voor failoveropruiming testen |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/ongeplandeFailover/actie | Herstelplan failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Waarschuwingen voor de kluis van de herstelservices lezen |
> | Microsoft.RecoveryServices/Vaults/monitoringConfiguraties/meldingConfiguratie/gelezen |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Kluizen/gebruik/lezen | Retourneert gebruiksgegevens voor een Vault voor Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De Vault Token-bewerking kan worden gebruikt om Vault Token te krijgen voor backend-bewerkingen op vaultlevel. |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Storage/storageAccounts/read | Geeft als resultaat de lijst met opslagaccounts of krijgt de eigenschappen voor het opgegeven opslagaccount. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="site-recovery-reader"></a>Siteherstellezer

Hiermee u de status Siteherstel bekijken, maar geen andere beheerbewerkingen uitvoeren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.RecoveryServices/locaties/toegewezenStamp/gelezen | GetAllocatedStamp is interne bewerking die door service wordt gebruikt |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | De bewerking Uitgebreide informatie van een object krijgen, wordt de uitgebreide informatie van een object weergegeven die de Azure-bron van het type ?vault vertegenwoordigt? |
> | Microsoft.RecoveryServices/Vaults/monitoringWaarschuwingen/gelezen | Krijgt de waarschuwingen voor de kluis van de herstelservices. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfiguraties/meldingConfiguratie/gelezen |  |
> | Microsoft.RecoveryServices/Vaults/read | Met de bewerking Kluis worden een object uitgevoerd dat de Azure-bron van het type 'vault' vertegenwoordigt |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten opvragen kan worden gebruikt, waarbij de bedrijfsstatus en het resultaat voor de asynchrone ingediende bewerking worden uitgevoerd |
> | Microsoft.RecoveryServices/Kluizen/geregistreerdeIdentiteiten/gelezen | De bewerking Containers worden gebruikt, zorg ervoor dat de containers zijn geregistreerd voor een resource. |
> | Microsoft.RecoveryServices/kluizen/replicatieAlertInstellingen/gelezen | Alle waarschuwingsinstellingen lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieGebeurtenissen/gelezen | Alle gebeurtenissen lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/gelezen | Lees alle stoffen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieNetwerken/gelezen | Alle netwerken lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieNetwerken/replicatieNetworkMappings/gelezen | Lees alle netwerktoewijzingen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/gelezen | Lees alle beveiligingscontainers |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectableItems/read | Lees alle beschermbare objecten |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/gelezen | Beschermde objecten lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectedItems/recoveryPoints/read | Lees alle replicatieherstelpunten |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieProtectionContainers/replicatieProtectionContainerMappings/gelezen | Lees alle Beveiligingscontainertoewijzingen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieRecoveryServicesProviders/gelezen | Lees alle Recovery Services Providers |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieStorageClassifications/gelezen | Lees alle opslagclassificaties |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatieStorageClassifications/replicatieStorageClassificationMappings/gelezen | Lees alle opslagclassificatietoewijzingen |
> | Microsoft.RecoveryServices/kluizen/replicatieStoffen/replicatievCenters/gelezen | Lees alle vCenters |
> | Microsoft.RecoveryServices/kluizen/replicatieTaken/gelezen | Lees alle vacatures |
> | Microsoft.RecoveryServices/kluizen/replicatiebeleid/gelezen | Elk beleid lezen |
> | Microsoft.RecoveryServices/kluizen/replicatieHerstelplannen/gelezen | Lees alle herstelplannen |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Kluizen/gebruik/lezen | Retourneert gebruiksgegevens voor een Vault voor Herstelservices. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De Vault Token-bewerking kan worden gebruikt om Vault Token te krijgen voor backend-bewerkingen op vaultlevel. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="support-request-contributor"></a>Bijdrage voor ondersteuningsaanvraag

Hiermee u ondersteuningsaanvragen maken en beheren

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="tag-contributor"></a>Tagbijdrager

Hiermee u tags op entiteiten beheren, zonder toegang te verlenen tot de entiteiten zelf.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Resources/abonnementen/resourceGroepen/resources/gelezen | Haalt de resources voor de resourcegroep op. |
> | Microsoft.Resources/abonnementen/bronnen/gelezen | Krijgt middelen van een abonnement. |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | Microsoft.Resources/tags/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Overige


### <a name="biztalk-contributor"></a>BizTalk-bijdrager

Hiermee u BizTalk-services beheren, maar er geen toegang toe hebben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk-services maken en beheren |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

### <a name="scheduler-job-collections-contributor"></a>Bijdrager voor taakverzamelingen van planner

Hiermee u de taakverzamelingen van Scheduler beheren, maar geen toegang tot deze verzamelingen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Acties** |  |
> | Microsoft.Authorization/*/gelezen | Rollen en roltoewijzingen lezen |
> | Microsoft.Insights/alertRegels/* | Waarschuwingsregels voor Inzichten maken en beheren |
> | Microsoft.ResourceHealth/availabilityStatuss/read | Krijgt de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik |
> | Microsoft.Resources/implementaties/* | Implementaties van resourcegroepen maken en beheren |
> | Microsoft.Resources/abonnementen/resourceGroepen/gelezen | Hiermee worden resourcegroepen in- of weergegeven. |
> | Microsoft.Scheduler/jobcollections/* | Taakverzamelingen maken en beheren |
> | Microsoft.Support/* | Ondersteuningstickets maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **Gegevensacties** |  |
> | *geen* |  |
> | **NietGegevensacties** |  |
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

## <a name="next-steps"></a>Volgende stappen

- [Resourceprovider koppelen aan service](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Machtigingen in Azure Security Center](../security-center/security-center-permissions.md)
