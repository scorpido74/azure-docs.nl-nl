---
title: Toegang tot blobs en wachtrijen autoriseren met Active Directory
titleSuffix: Azure Storage
description: De toegang tot Azure-blobs en wachtrijen autoriseren met Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255364"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Toegang tot blobs en wachtrijen autoriseren met Azure Active Directory

Azure Storage ondersteunt het gebruik van Azure Active Directory (Azure AD) om aanvragen voor Blob- en Queue-opslag te autoriseren. Met Azure AD u RBAC (Role-based access control) gebruiken om machtigingen toe te kennen aan een beveiligingsprincipal, die mogelijk een hoofdvan de gebruikers-, groep- of toepassingsserviceiser. De beveiligingsprincipal is geverifieerd door Azure AD om een OAuth 2.0-token terug te sturen. Het token kan vervolgens worden gebruikt om een aanvraag te autoriseren tegen blob- of wachtrijopslag.

Het toestaan van aanvragen tegen Azure Storage met Azure AD biedt superieure beveiliging en gebruiksgemak via de machtiging Voor gedeelde sleutels. Microsoft raadt aan om Azure AD-autorisatie waar mogelijk te gebruiken met uw blob- en wachtrijtoepassingen om potentiële beveiligingsproblemen die inherent zijn aan Shared Key tot een minimum te beperken.

Autorisatie met Azure AD is beschikbaar voor alle opslagaccounts voor algemene doeleinden en Blob in alle openbare regio's en nationale clouds. Alleen opslagaccounts die zijn gemaakt met het Azure Resource Manager-implementatiemodel ondersteunen Azure AD-autorisatie.

Blob-opslag ondersteunt bovendien het maken van sas-handtekeningen (Shared Access Signatures) die zijn ondertekend met Azure AD-referenties. Zie [Beperkte toegang tot gegevens met handtekeningen voor gedeelde toegang verlenen](storage-sas-overview.md)voor meer informatie .

Azure Files ondersteunt autorisatie met ALLEEN AD (preview) of Azure AD DS (GA) via SMB voor vm's die zijn verbonden met domeinen. Zie Overzicht van verificatieondersteuning voor azure [files op basis van azure-bestanden voor SMB-toegang](../files/storage-files-active-directory-overview.md)voor meer informatie over het gebruik van AD (preview) of Azure AD DS (GA) via SMB voor Azure-bestanden.

Autorisatie met Azure AD wordt niet ondersteund voor Azure Table-opslag. Gedeelde sleutel gebruiken om aanvragen voor tabelopslag te autoriseren.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Overzicht van Azure AD voor blobs en wachtrijen

Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een blob of wachtrijbron, moet de aanvraag worden geautoriseerd, tenzij het een blob is die beschikbaar is voor anonieme toegang. Met Azure AD is toegang tot een bron een proces in twee stappen. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2.0-token geretourneerd. Vervolgens wordt het token doorgegeven als onderdeel van een aanvraag voor de Blob- of Queue-service en wordt het door de service gebruikt om de toegang tot de opgegeven bron te autoriseren.

De verificatiestap vereist dat een toepassing een OAuth 2.0-toegangstoken aanvraagt tijdens runtime. Als een toepassing wordt uitgevoerd vanuit een Azure-entiteit, zoals een Azure VM, een virtuele machineschaalset of een Azure Functions-app, kan deze een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) gebruiken om toegang te krijgen tot blobs of wachtrijen. Zie Toegang tot blobs en wachtrijen met Azure Active [Directory en beheerde identiteiten voor Azure Resources voor](storage-auth-aad-msi.md)meer informatie over het autoriseren van aanvragen die door een beheerde identiteit zijn gedaan aan de Azure Blob- of Queue-service.

De autorisatiestap vereist dat een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Storage biedt RBAC-rollen die algemene sets machtigingen voor blob- en wachtrijgegevens omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die de opdrachtgever zal hebben. Zie [Toegangsrechten tot opslaggegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over het toewijzen van RBAC-rollen voor Azure Storage.

Native toepassingen en webtoepassingen die aanvragen indienen voor de Azure Blob- of Queue-service kunnen ook toegang met Azure AD autoriseren. Zie Toegang tot Azure Storage met Azure AD beheren met Azure AD vanuit een Azure Storage-toepassing voor meer informatie over het aanvragen van een toegangstoken en het gebruiken om aanvragen voor blob- of wachtrijgegevens [te autoriseren.](storage-auth-aad-app.md)

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangsrechten

Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../../role-based-access-control/overview.md) Azure Storage definieert een set ingebouwde RBAC-rollen die veelvoorkomende sets machtigingen omvatten die worden gebruikt om toegang te krijgen tot blob- en wachtrijgegevens. U ook aangepaste rollen definiëren voor toegang tot blob- en wachtrijgegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het niveau van het abonnement, de resourcegroep, het opslagaccount of een afzonderlijke container of wachtrij. Een Azure AD-beveiligingsprincipal kan een gebruiker, een groep, een hoofd van de toepassingsservice of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Ingebouwde RBAC-rollen voor blobs en wachtrijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Zie een van de volgende artikelen voor meer informatie over het toewijzen van een ingebouwde RBAC-rol aan een beveiligingsprincipal:

- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van Azure CLI](storage-auth-aad-rbac-cli.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van PowerShell](storage-auth-aad-rbac-powershell.md)

Zie [Functiedefinities begrijpen](../../role-based-access-control/role-definitions.md#management-and-data-operations)voor meer informatie over hoe ingebouwde rollen zijn gedefinieerd voor Azure Storage. Zie Aangepaste rollen maken voor [Azure Role-Based Access Control](../../role-based-access-control/custom-roles.md)voor informatie over het maken van aangepaste RBAC-rollen.

### <a name="access-permissions-for-data-operations"></a>Toegangsmachtigingen voor gegevensbewerkingen

Zie [Machtigingen voor het aanroepen van blob- en queuegegevensbewerkingen voor](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)meer informatie over de machtigingen die nodig zijn om specifieke blob- of wachtrijservicebewerkingen aan te roepen.

## <a name="resource-scope"></a>Resourcebereik

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Toegang tot gegevens met een Azure AD-account

Toegang tot blob- of wachtrijgegevens via de Azure-portal, PowerShell of Azure CLI kan worden geautoriseerd met behulp van het Azure AD-account van de gebruiker of met behulp van de accounttoegangssleutels (Machtiging voor gedeelde sleutel).

### <a name="data-access-from-the-azure-portal"></a>Toegang tot gegevens vanuit de Azure-portal

De Azure-portal kan uw Azure AD-account of de accounttoegangssleutels gebruiken om toegang te krijgen tot blob- en wachtrijgegevens in een Azure-opslagaccount. Welk autorisatieschema de Azure-portal gebruikt, is afhankelijk van de RBAC-rollen die aan u zijn toegewezen.

Wanneer u blob- of wachtrijgegevens probeert te openen, controleert de Azure-portal eerst of u een RBAC-rol hebt toegewezen met **Microsoft.Storage/storageAccounts/listkeys/action**. Als u een rol met deze actie hebt toegewezen, gebruikt de Azure-portal de accountsleutel voor toegang tot blob- en wachtrijgegevens via de machtiging Gedeelde sleutel. Als u geen rol met deze actie hebt toegewezen, probeert de Azure-portal toegang te krijgen tot gegevens met uw Azure AD-account.

Als u blob- of wachtrijgegevens wilt openen vanuit de Azure-portal met uw Azure AD-account, hebt u machtigingen nodig om toegang te krijgen tot blob- en wachtrijgegevens en hebt u ook machtigingen nodig om door de opslagaccountbronnen in de Azure-portal te navigeren. De ingebouwde rollen die door Azure Storage worden geleverd, verlenen toegang tot blob- en wachtrijbronnen, maar ze verlenen geen machtigingen voor opslagaccountbronnen. Om deze reden vereist toegang tot de portal ook de toewijzing van een Azure Resource Manager-rol, zoals [de](../../role-based-access-control/built-in-roles.md#reader) leesrol, die is beperkt tot het niveau van het opslagaccount of hoger. De rol **Reader** verleent de meest beperkte machtigingen, maar een andere Azure Resource Manager-rol die toegang verleent tot opslagaccountbeheerbronnen, is ook aanvaardbaar. Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)voor meer informatie over het toewijzen van machtigingen aan gebruikers voor gegevenstoegang in de Azure-portal.

De Azure-portal geeft aan welk autorisatieschema wordt gebruikt wanneer u naar een container of wachtrij navigeert. Zie [De Azure-portal gebruiken om toegang te krijgen tot blob- of wachtrijgegevens voor](storage-access-blobs-queues-portal.md)meer informatie over gegevenstoegang in de portal.

### <a name="data-access-from-powershell-or-azure-cli"></a>Toegang tot gegevens vanuit PowerShell of Azure CLI

Azure CLI- en PowerShell-ondersteuning bij het aanmelden met Azure AD-referenties. Nadat u zich hebt aangemeld, wordt uw sessie uitgevoerd onder deze referenties. Zie [Azure CLI- of PowerShell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot blob- of queuegegevens](authorize-active-directory-powershell.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot blobs en wachtrijen autoriseren met Azure Active Directory en beheerde identiteiten voor Azure Resources](storage-auth-aad-msi.md)
- [Autoriseren met Azure Active Directory vanuit een toepassing voor toegang tot blobs en wachtrijen](storage-auth-aad-app.md)
- [Azure Storage-ondersteuning voor Azure Active Directory-gebaseerdtoegangsbeheer is algemeen beschikbaar](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
