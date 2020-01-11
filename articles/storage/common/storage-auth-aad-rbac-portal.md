---
title: De Azure Portal gebruiken om een RBAC-rol toe te wijzen voor gegevens toegang
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van de Azure Portal om machtigingen toe te wijzen aan een Azure Active Directory-beveiligingsprincipal met op rollen gebaseerd toegangs beheer (RBAC). Azure Storage ondersteunt ingebouwde en aangepaste RBAC-rollen voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867507"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>De Azure Portal gebruiken om een RBAC-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens

Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../../role-based-access-control/overview.md). Azure Storage definieert een set ingebouwde RBAC-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot BLOB-of wachtrij gegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, het opslag account of een afzonderlijke container of wachtrij. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om RBAC-rollen toe te wijzen. De Azure Portal biedt een eenvoudige interface voor het toewijzen van RBAC-rollen en het beheren van toegang tot uw opslag resources. U kunt ook RBAC-rollen toewijzen voor Blob-en wachtrij bronnen met behulp van Azure-opdracht regel Programma's of de Azure Storage-beheer-Api's. Zie [toegang tot Azure-blobs en-wacht rijen verifiëren met Azure Active Directory](storage-auth-aad.md)voor meer informatie over RBAC-rollen voor opslag resources. 

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-rollen voor blobs en wacht rijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Resource bereik bepalen 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen met behulp van de Azure Portal

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die resource in de Azure Portal. Geef de **Access Control (IAM)-** instellingen voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

1. Wijs de juiste Azure Storage RBAC-rol toe om toegang te verlenen aan een Azure AD-beveiligings-principal.

1. Wijs de rol van Azure Resource Manager [lezer](../../role-based-access-control/built-in-roles.md#reader) toe aan gebruikers die toegang moeten hebben tot containers of wacht rijen via de Azure Portal met hun Azure AD-referenties. 

In de volgende secties worden deze stappen uitvoeriger beschreven.

> [!NOTE]
> Als een eigenaar van uw Azure Storage-account, zijn u machtigingen voor toegang tot gegevens niet automatisch toegewezen. U moet zelf expliciet een RBAC-rol toewijzen voor Azure Storage. U kunt deze toewijzen op het niveau van uw abonnement, resource groep, opslag account of een container of wachtrij.
>
> U kunt geen rollen bereik toewijzen aan een container of wachtrij als uw opslag account een hiërarchische naam ruimte heeft ingeschakeld.

### <a name="assign-a-built-in-rbac-role"></a>Een ingebouwde RBAC-rol toewijzen

Voordat u een rol aan een beveiligingsprincipal toewijst, moet u rekening houden met het bereik van de machtigingen die u wilt verlenen. Raadpleeg de sectie [resource bereik bepalen](#determine-resource-scope) om het juiste bereik te kiezen.

Met de procedure die hier wordt weer gegeven, wordt een rol binnen een container toegewezen, maar u kunt dezelfde stappen volgen om een rollen bereik toe te wijzen aan een wachtrij: 

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account en geef het **overzicht** voor het account weer.
1. Onder Services selecteert u **blobs**. 
1. Zoek de container waarvoor u een rol wilt toewijzen en geef de instellingen van de container weer. 
1. Selecteer **toegangs beheer (IAM)** om instellingen voor toegangs beheer voor de container weer te geven. Selecteer het **tabblad roltoewijzingen om de lijst** met roltoewijzingen weer te geven.

    ![Scherm opname van instellingen voor toegangs beheer voor container](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Klik op de knop roltoewijzing **toevoegen** om een nieuwe rol toe te voegen.
1. Selecteer in het venster **roltoewijzing toevoegen** de Azure Storage rol die u wilt toewijzen. Zoek vervolgens naar de beveiligingsprincipal waaraan u de rol wilt toewijzen.

    ![Scherm afbeelding die laat zien hoe u een RBAC-rol toewijst](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Klik op **Opslaan**. De identiteit waaraan u de rol hebt toegewezen, wordt weer gegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat de gebruiker die nu heeft toegevoegd, lees machtigingen heeft voor de gegevens in de container *sample-container*.

    ![Scherm afbeelding met een lijst met gebruikers die zijn toegewezen aan een rol](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

U kunt vergelijk bare stappen volgen om een rollen bereik toe te wijzen aan het opslag account, de resource groep of het abonnement.

### <a name="assign-the-reader-role-for-portal-access"></a>De rol van lezer toewijzen voor toegang tot de portal

Wanneer u een ingebouwde of aangepaste rol toewijst aan Azure Storage aan een beveiligingsprincipal, verleent u machtigingen aan die beveiligingsprincipal om bewerkingen uit te voeren op gegevens in uw opslag account. De ingebouwde rollen van **gegevens lezers** bieden Lees machtigingen voor de gegevens in een container of wachtrij, terwijl de ingebouwde **gegevensinzender** rollen Lees-, schrijf-en verwijderings rechten bieden voor een container of wachtrij. De machtigingen bevinden zich in het bereik van de opgegeven resource.  
Bijvoorbeeld, als u de rol van **BLOB-gegevens** van de opslag gebruiker aan Mary toewijst op het niveau van een container met de naam **sample-container**, wordt met Mary Lees-, schrijf-en verwijder toegang verleend voor alle blobs in die container.

Als Mary echter een BLOB wil weer geven in de Azure Portal, biedt de rol van de BLOB voor de **gegevens verzamelaar** van de opslag op zichzelf niet voldoende machtigingen om door de portal naar de BLOB te navigeren om deze te kunnen bekijken. Aanvullende Azure AD-machtigingen zijn vereist om door de portal te navigeren en de andere bronnen weer te geven die daar zichtbaar zijn.

Als uw gebruikers toegang moeten hebben tot blobs in de Azure Portal, wijst u hen vervolgens een extra RBAC-rol, de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) , toe aan deze gebruikers, op het niveau van het opslag account of hoger. De rol van **lezer** is een Azure Resource Manager rol waarmee gebruikers bronnen van het opslag account kunnen weer geven, maar niet kunnen wijzigen. Het biedt geen lees machtigingen voor gegevens in Azure Storage, maar alleen voor account beheer resources.

Volg deze stappen om de rol van **lezer** toe te wijzen, zodat een gebruiker toegang heeft tot de blobs van de Azure Portal. In dit voor beeld is de toewijzing het bereik van het opslag account:

1. Ga in [Azure Portal](https://portal.azure.com) naar uw opslagaccount.
1. Selecteer **toegangs beheer (IAM)** om de instellingen voor toegangs beheer voor het opslag account weer te geven. Selecteer het **tabblad roltoewijzingen om de lijst** met roltoewijzingen weer te geven.
1. Selecteer de rol **lezer** in het venster **roltoewijzing toevoegen** . 
1. Selecteer in het veld **toegang toewijzen aan** de optie **Azure AD-gebruiker,-groep of-Service-Principal**.
1. Zoek naar de beveiligingsprincipal waaraan u de rol wilt toewijzen.
1. Sla de roltoewijzing op.

Het toewijzen van de rol **lezer** is alleen nodig voor gebruikers die toegang moeten hebben tot blobs of wacht rijen met behulp van de Azure Portal.

> [!IMPORTANT]
> De preview-versie van Storage Explorer in het Azure Portal biedt geen ondersteuning voor het gebruik van Azure AD-referenties om BLOB-of wachtrij gegevens te bekijken en te wijzigen. Storage Explorer in de Azure Portal maakt altijd gebruik van de account sleutels om toegang te krijgen tot gegevens. Als u Storage Explorer in het Azure Portal wilt gebruiken, moet u een rol toewijzen die **micro soft. Storage/Storage accounts/listkeys ophalen/Action**bevat.

## <a name="next-steps"></a>Volgende stappen

- Zie [toegang tot Azure-blobs en-wacht rijen verifiëren met Azure Active Directory](storage-auth-aad.md)voor meer informatie over RBAC-rollen voor opslag resources. 
- Zie [Wat is op rollen gebaseerd toegangs beheer (RBAC)?](../../role-based-access-control/overview.md)voor meer informatie over RBAC.
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van RBAC-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met de REST API](../../role-based-access-control/role-assignments-rest.md)
- Zie [Azure AD gebruiken met Azure Storage-toepassingen](storage-auth-aad-app.md)voor meer informatie over het machtigen van toegang tot containers en wacht rijen in uw opslag toepassingen.
