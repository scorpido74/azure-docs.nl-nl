---
title: Toegang verlenen tot Azure-blobs en-wacht rijen met Azure Active Directory | Microsoft Docs
description: De toegang tot Azure-blobs en-wacht rijen met Azure Active Directory autoriseren.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: caf72d4af390956391fdab133cf0897abbee4633
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673156"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory

Azure Storage ondersteunt het gebruik van Azure Active Directory (AD) om aanvragen voor Blob-en wachtrij opslag te autoriseren. Met Azure AD kunt u gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) om machtigingen toe te kennen aan een beveiligingsprincipal. Dit kan een gebruiker, groep of toepassings Service-Principal zijn. De beveiligingsprincipal wordt door Azure AD geverifieerd om een OAuth 2,0-token te retour neren. Het token kan worden gebruikt voor het autoriseren van een aanvraag voor toegang tot een bron in BLOB-of queue-opslag.

Het autoriseren van gebruikers of toepassingen met een OAuth 2,0-token dat wordt geretourneerd door Azure AD biedt een superieure beveiliging en gebruiks vriendelijk gebruik van gedeelde sleutel autorisatie en Shared Access signatures (SAS). Met Azure AD hoeft u de toegangs sleutel voor het account niet op te slaan met uw code en mogelijke beveiligings problemen. U kunt de verificatie van de gedeelde sleutel blijven gebruiken met uw toepassingen, maar met Azure AD wordt de nood zaak om uw account toegangs sleutel op te slaan met uw code. U kunt ook door gaan met het gebruik van Shared Access signatures (SAS) om nauw keurige toegang tot resources in uw opslag account te verlenen, maar Azure AD biedt soort gelijke mogelijkheden zonder de behoefte aan het beheer van SAS-tokens of een probleem bij het intrekken van een aangetaste SAS. Micro soft raadt u aan gebruik te maken van Azure AD-autorisatie met uw Azure Storage-toepassingen wanneer dat mogelijk is.

Verificatie met Azure AD is beschikbaar voor alle accounts voor algemeen gebruik en Blob Storage in alle open bare regio's en nationale Clouds. Alleen opslag accounts die zijn gemaakt met het Azure Resource Manager implementatie model ondersteunen Azure AD-autorisatie. Autorisatie met Azure AD wordt niet ondersteund voor Azure-tabel opslag.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Overzicht van Azure AD voor blobs en wacht rijen

Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een BLOB of wachtrij resource, moet de aanvraag worden geautoriseerd, tenzij het een blob is die beschikbaar is voor anonieme toegang. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de BLOB of Queue-service en door de service wordt gebruikt om toegang te verlenen tot de opgegeven resource.

De verificatie stap vereist dat een toepassing tijdens runtime een OAuth 2,0-toegangs token opvraagt. Als een toepassing wordt uitgevoerd vanuit een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure Functions-app, kan deze een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) gebruiken om toegang te krijgen tot blobs of wacht rijen. Zie [toegang verlenen aan blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)voor meer informatie over het autoriseren van aanvragen die door een beheerde identiteit worden door gegeven aan de Azure-Blob of de Queue-service.

Voor de autorisatie stap moeten een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure Storage biedt RBAC-rollen die algemene sets machtigingen voor Blob-en wachtrij gegevens omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Zie [toegangs rechten voor opslag gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over het toewijzen van RBAC-rollen voor Azure Storage.

Systeem eigen toepassingen en webtoepassingen die aanvragen indienen bij de Azure Blob of Queue-service, kunnen ook toegang verlenen met Azure AD. Zie [toegang tot Azure Storage met Azure AD toestaan vanuit een Azure Storage toepassing](storage-auth-aad-app.md)voor meer informatie over het aanvragen van een toegangs token en het gebruik ervan om aanvragen voor BLOB-of wachtrij gegevens te autoriseren.

## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangs rechten

Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/overview.md). Azure Storage definieert een set ingebouwde RBAC-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot Blob-en wachtrij gegevens. U kunt ook aangepaste rollen definiëren voor toegang tot Blob-en wachtrij gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, het opslag account of een afzonderlijke container of wachtrij. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Ingebouwde RBAC-rollen voor blobs en wacht rijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Zie een van de volgende artikelen voor meer informatie over het toewijzen van een ingebouwde RBAC-rol aan een beveiligingsprincipal:

- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van Azure CLI](storage-auth-aad-rbac-cli.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van PowerShell](storage-auth-aad-rbac-powershell.md)

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd voor Azure Storage [begrijpen functie definities](../../role-based-access-control/role-definitions.md#management-and-data-operations). Zie voor meer informatie over het maken van aangepaste RBAC-rollen [aangepaste rollen maken voor op rollen gebaseerd Azure-Access Control](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Toegangs machtigingen voor gegevens bewerkingen

Zie voor meer informatie over de vereiste machtigingen voor het aanroepen van specifieke BLOB-of Queue-service bewerkingen [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Bron bereik

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Toegang tot gegevens met een Azure AD-account

Toegang tot BLOB-of wachtrij gegevens via de Azure Portal, Power shell of Azure CLI kan worden geautoriseerd door gebruik te maken van het Azure AD-account van de gebruiker of door gebruik te maken van de toegangs sleutels voor het account (gedeelde sleutel autorisatie).

### <a name="data-access-from-the-azure-portal"></a>Toegang tot gegevens vanuit de Azure Portal

De Azure Portal kunnen uw Azure AD-account of de toegangs sleutels voor het account gebruiken om toegang te krijgen tot Blob-en wachtrij gegevens in een Azure-opslag account. Welk verificatie schema het Azure Portal gebruikt, is afhankelijk van de RBAC-rollen die aan u zijn toegewezen.

Wanneer u probeert toegang te krijgen tot BLOB-of wachtrij gegevens, controleert de Azure Portal eerst of aan u een RBAC-rol is toegewezen met **micro soft. Storage/Storage accounts/listkeys ophalen/Action**. Als aan u een rol is toegewezen met deze actie, gebruikt de Azure Portal de account sleutel voor toegang tot Blob-en wachtrij gegevens via gedeelde sleutel autorisatie. Als u geen rol aan deze actie hebt toegewezen, probeert de Azure Portal toegang te krijgen tot gegevens met uw Azure AD-account.

Als u toegang wilt krijgen tot BLOB-of wachtrij gegevens van de Azure Portal met uw Azure AD-account, hebt u machtigingen nodig voor toegang tot de BLOB-en wachtrij gegevens en hebt u ook machtigingen nodig om te navigeren door de resources van het opslag account in de Azure Portal. De ingebouwde rollen die worden verschaft door Azure Storage toegang verlenen tot Blob-en wachtrij resources, maar ze verlenen geen machtigingen voor de resources van het opslag account. Daarom moet de toegang tot de portal ook de toewijzing van een Azure Resource Manager rol, zoals de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) , bereiken van het niveau van het opslag account of hoger. De rol van **lezer** verleent de meeste beperkte machtigingen, maar een andere Azure Resource Manager rol die toegang verleent tot bronnen voor het beheer van opslag accounts is ook aanvaardbaar. Zie voor meer informatie over het toewijzen van machtigingen aan gebruikers voor toegang tot gegevens in de Azure Portal met een Azure AD-account [toegang verlenen tot Azure Blob en gegevens wachtrij met RBAC in de Azure Portal](storage-auth-aad-rbac-portal.md).

De Azure Portal geeft aan welk verificatie schema wordt gebruikt wanneer u naar een container of wachtrij navigeert. Zie [de Azure Portal gebruiken om toegang te krijgen tot BLOB-of wachtrij gegevens](storage-access-blobs-queues-portal.md)voor meer informatie over toegang tot gegevens in de portal.

### <a name="data-access-from-powershell-or-azure-cli"></a>Gegevens toegang vanuit Power shell of Azure CLI

Azure CLI en Power shell ondersteunen het aanmelden met Azure AD-referenties. Nadat u zich hebt aangemeld, wordt uw sessie uitgevoerd onder deze referenties. Zie voor meer informatie [Azure CLI of Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Azure AD-autorisatie via SMB voor Azure Files

Azure Files ondersteunt alleen verificatie met Azure AD via SMB voor Vm's die zijn toegevoegd aan een domein (preview). Zie [overzicht van Azure Active Directory autorisatie via SMB voor Azure files (preview)](../files/storage-files-active-directory-overview.md)voor meer informatie over het gebruik van Azure ad over smb voor Azure files.

## <a name="next-steps"></a>Volgende stappen

- [Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)
- [Azure Active Directory van een toepassing autoriseren voor toegang tot blobs en wacht rijen](storage-auth-aad-app.md)
- [Azure Storage ondersteuning voor toegangs beheer op basis van Azure Active Directory algemeen beschikbaar](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
