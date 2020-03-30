---
title: De Azure-portal gebruiken om een RBAC-rol toe te wijzen voor gegevenstoegang
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van de Azure-portal om machtigingen toe te wijzen aan een Azure Active Directory-beveiligingsprincipal met rbac (role-based access control). Azure Storage ondersteunt ingebouwde en aangepaste RBAC-rollen voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867507"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>De Azure-portal gebruiken om een RBAC-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens

Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../../role-based-access-control/overview.md) Azure Storage definieert een set ingebouwde RBAC-rollen die veelvoorkomende sets machtigingen omvatten die worden gebruikt om toegang te krijgen tot blob- of wachtrijgegevens.

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. Toegang kan worden beperkt tot het niveau van het abonnement, de resourcegroep, het opslagaccount of een afzonderlijke container of wachtrij. Een Azure AD-beveiligingsprincipal kan een gebruiker, een groep, een hoofd van de toepassingsservice of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om RBAC-rollen toe te wijzen. De Azure-portal biedt een eenvoudige interface voor het toewijzen van RBAC-rollen en het beheren van toegang tot uw opslagbronnen. U ook RBAC-rollen toewijzen voor blob- en wachtrijresources met Azure-opdrachtregelhulpprogramma's of de API's voor Azure Storage Management. Zie [Toegang tot Azure blobs en wachtrijen verifiëren met Azure Active Directory](storage-auth-aad.md)voor meer informatie over RBAC-rollen voor opslagresources. 

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-rollen voor blobs en wachtrijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Resourcebereik bepalen 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toewijzen via de Azure-portal

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die bron in de Azure-portal. Geef de **Instellingen voor Toegangsbeheer (IAM)** voor de resource weer en volg deze instructies om roltoewijzingen te beheren:

1. Wijs de juiste Azure Storage RBAC-rol toe om toegang te verlenen tot een Azure AD-beveiligingsprincipal.

1. Wijs de azure resource [manager-lezerrol](../../role-based-access-control/built-in-roles.md#reader) toe aan gebruikers die via de Azure-portal toegang moeten krijgen tot containers of wachtrijen met behulp van hun Azure AD-referenties. 

In de volgende secties worden elk van deze stappen nader beschreven.

> [!NOTE]
> Als eigenaar van uw Azure Storage-account krijgt u niet automatisch machtigingen toegewezen voor toegang tot gegevens. U moet uzelf expliciet een RBAC-rol toewijzen voor Azure Storage. U het toewijzen op het niveau van uw abonnement, resourcegroep, opslagaccount of een container of wachtrij.
>
> U een rol die aan een container of wachtrij is toegewezen, niet toewijzen als in uw opslagaccount een hiërarchische naamruimte is ingeschakeld.

### <a name="assign-a-built-in-rbac-role"></a>Een ingebouwde RBAC-rol toewijzen

Voordat u een rol toewijst aan een beveiligingsprincipal, moet u rekening houden met het bereik van de machtigingen die u verleent. Controleer de sectie [Resourcebereik bepalen](#determine-resource-scope) om het juiste bereik te bepalen.

De hier weergegeven procedure wijst een rol toe die aan een container is toegewezen, maar u dezelfde stappen volgen om een rol toegewezen aan een wachtrij toe te wijzen: 

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount en geef het **overzicht** voor het account weer.
1. Selecteer **Blobs**onder Services . 
1. Zoek de container waarvoor u een rol wilt toewijzen en geef de instellingen van de container weer. 
1. Selecteer **Toegangsbesturingselement (IAM)** om toegangsbeheerinstellingen voor de container weer te geven. Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven.

    ![Schermafbeelding van instellingen voor toegangsbeheer voor containers](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Klik op de knop **Roltoewijzing toevoegen** om een nieuwe rol toe te voegen.
1. Selecteer in het venster **Roltoewijzing toevoegen** de Azure Storage-rol die u wilt toewijzen. Zoek vervolgens naar de beveiligingsprincipal waaraan u die rol wilt toewijzen.

    ![Schermafbeelding van het toewijzen van een RBAC-rol](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Klik op **Opslaan**. De identiteit aan wie u de rol hebt toegewezen, wordt weergegeven onder die rol. De volgende afbeelding laat bijvoorbeeld zien dat de gebruiker die nu is toegevoegd, leesmachtigingen heeft voor gegevens in de container met de naam *sample-container.*

    ![Schermafbeelding van een lijst met gebruikers die aan een rol zijn toegewezen](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

U vergelijkbare stappen uitvoeren om een rol toe te wijzen die is toegewezen aan het opslagaccount, de resourcegroep of het abonnement.

### <a name="assign-the-reader-role-for-portal-access"></a>De readerrol toewijzen voor portaltoegang

Wanneer u een ingebouwde of aangepaste rol voor Azure Storage aan een beveiligingsprincipal toewijst, verleent u machtigingen aan die beveiligingsprincipal om bewerkingen uit te voeren op gegevens in uw opslagaccount. De ingebouwde **rollen Gegevenslezer** bieden leesmachtigingen voor de gegevens in een container of wachtrij, terwijl de ingebouwde rollen **voor gegevensinzendermachtigingen** machtigingen voor een container of wachtrij bieden. Machtigingen zijn beperkt tot de opgegeven resource.  
Als u bijvoorbeeld de rol **Opslagblobgegevensbijdrager toewijst** aan gebruiker Mary op het niveau van een container met de naam **sample-container,** krijgt Mary lees- en schrijf- en verwijdertoegang tot alle blobs in die container.

Als Mary echter een blob in de Azure-portal wil weergeven, biedt de rol **opslagblobgegevensbijdrager** zelf niet voldoende machtigingen om door de portal naar de blob te navigeren om deze te bekijken. Er zijn extra Azure AD-machtigingen vereist om door de portal te navigeren en de andere bronnen te bekijken die daar zichtbaar zijn.

Als uw gebruikers toegang moeten hebben tot blobs in de Azure-portal, wijst u ze vervolgens een extra RBAC-rol toe, de [Reader-rol,](../../role-based-access-control/built-in-roles.md#reader) aan die gebruikers, op het niveau van het opslagaccount of hoger. De rol **Reader** is een Azure Resource Manager-rol waarmee gebruikers opslagaccountbronnen kunnen bekijken, maar deze niet kunnen wijzigen. Het biedt geen leesmachtigingen voor gegevens in Azure Storage, maar alleen aan accountbeheerbronnen.

Volg deze stappen om de **leesrol** toe te wijzen, zodat een gebruiker toegang heeft tot blobs vanuit de Azure-portal. In dit voorbeeld wordt de toewijzing toegewezen aan het opslagaccount:

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.
1. Selecteer **Toegangsbeheer (IAM)** om de toegangscontrole-instellingen voor het opslagaccount weer te geven. Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven.
1. Selecteer in het venster **Roltoewijzing toevoegen** de rol **Reader.** 
1. Selecteer **azure** **AD-gebruiker, -groep of serviceprincipal**in het veld Toegang toewijzen tot veld .
1. Zoek naar de beveiligingsprincipal waaraan u de rol wilt toewijzen.
1. Sla de roltoewijzing op.

Het toewijzen van de **Reader-rol** is alleen nodig voor gebruikers die toegang moeten krijgen tot blobs of wachtrijen via de Azure-portal.

> [!IMPORTANT]
> De preview-versie van Storage Explorer in de Azure-portal biedt geen ondersteuning voor het gebruik van Azure AD-referenties om blob- of wachtrijgegevens weer te geven en te wijzigen. Storage Explorer in de Azure-portal gebruikt altijd de accountsleutels om toegang te krijgen tot gegevens. Als u Storage Explorer wilt gebruiken in de Azure-portal, moet u een rol toegewezen krijgen die **Microsoft.Storage/storageAccounts/listkeys/action**bevat.

## <a name="next-steps"></a>Volgende stappen

- Zie [Toegang tot Azure blobs en wachtrijen verifiëren met Azure Active Directory](storage-auth-aad.md)voor meer informatie over RBAC-rollen voor opslagresources. 
- Zie Wat is op [rolgebaseerde toegangscontrole (RBAC)?](../../role-based-access-control/overview.md).
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van RBAC-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Beheer op rollen gebaseerde toegangscontrole (RBAC) met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Functiegebaseerde toegangscontrole (RBAC) beheren met de REST API](../../role-based-access-control/role-assignments-rest.md)
- Zie [Azure AD gebruiken met Azure Storage-toepassingen](storage-auth-aad-app.md)voor meer informatie over het autoriseren van toegang tot containers en wachtrijen vanuit uw opslagtoepassingen.
