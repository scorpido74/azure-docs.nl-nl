---
title: Instellingen voor opslag accounts beheren in de Azure Portal-Azure Storage | Microsoft Docs
description: Meer informatie over het beheren van instellingen voor opslag accounts in de Azure Portal, waaronder het configureren van instellingen voor toegangs beheer, het opnieuw genereren van de toegangs sleutels voor accounts, het wijzigen van de toegangs laag of het wijzigen van het type replicatie dat door het account wordt gebruikt. Meer informatie over het verwijderen van een opslag account in de portal.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 3c01cc870b20c8256b215eb700548e6cd69ad0d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749010"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Instellingen voor opslag accounts beheren in de Azure Portal

Er zijn verschillende instellingen voor uw opslag account beschikbaar in de [Azure Portal](https://portal.azure.com). In dit artikel worden enkele van deze instellingen beschreven en wordt uitgelegd hoe u deze kunt gebruiken.

## <a name="access-control"></a>Toegangsbeheer

Azure Storage ondersteunt autorisatie met Azure Active Directory voor Blob Storage en Queue Storage via op rollen gebaseerd toegangs beheer (RBAC). Zie [toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie over autorisatie met Azure AD.

De instellingen voor **toegangs beheer** in de Azure Portal bieden een eenvoudige manier om RBAC-rollen toe te wijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Zie [toegangs rechten voor Blob-en wachtrij gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over het toewijzen van RBAC-rollen.

## <a name="tags"></a>Tags

Azure Storage ondersteunt Azure Resource Manager-Tags voor het organiseren van uw Azure-resources met een aangepaste taxonomie. U kunt Tags Toep assen op uw opslag accounts, zodat u ze binnen uw abonnement op een logische manier kunt groeperen.

Voor opslag accounts is een label naam beperkt tot 128 tekens en een label waarde is beperkt tot 256 tekens.

Zie [Tags gebruiken om uw Azure-resources te organiseren](../../azure-resource-manager/resource-group-using-tags.md)voor meer informatie.

## <a name="access-keys"></a>Toegangssleutels

Wanneer u een opslag account maakt, genereert Azure 2 512-bits toegangs sleutels voor opslag accounts. Deze sleutels kunnen worden gebruikt om toegang te verlenen tot uw opslag account via een gedeelde sleutel. U kunt de sleutels zonder onderbreking voor uw toepassingen draaien en opnieuw genereren. micro soft raadt u daarom regel matig aan.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Account sleutels en connection string weer geven

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Toegangssleutels regenereren

Micro soft raadt u aan uw toegangs sleutels periodiek opnieuw te genereren om uw opslag account veilig te houden. Er zijn twee toegangs sleutels toegewezen zodat u uw sleutels kunt draaien. Wanneer u uw sleutels roteert, zorgt u ervoor dat uw toepassing gedurende het proces toegang tot Azure Storage houdt. 

> [!WARNING]
> Het opnieuw genereren van de toegangs sleutels kan invloed hebben op alle toepassingen of Azure-Services die afhankelijk zijn van de sleutel van het opslag account. Clients die gebruikmaken van de account sleutel voor toegang tot het opslag account moeten worden bijgewerkt om de nieuwe sleutel te gebruiken, inclusief Media Services, Cloud, desktop-en mobiele toepassingen en Graphical User Interface toepassingen voor Azure Storage, zoals [Azure Storage Explorer ](https://azure.microsoft.com/features/storage-explorer/).

Volg deze procedure om de sleutels van uw opslag account te roteren:

1. Werk de verbindings reeksen in de toepassings code bij om de secundaire sleutel te gebruiken.
2. Genereer de primaire toegangssleutel voor uw opslagaccount opnieuw. Klik op de Blade **toegangs sleutels** in de Azure Portal op **key1 opnieuw genereren**en klik vervolgens op **Ja** om te bevestigen dat u een nieuwe sleutel wilt genereren.
3. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
4. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw.

## <a name="account-configuration"></a>Account configuratie

Nadat u een opslag account hebt gemaakt, kunt u de configuratie wijzigen. U kunt bijvoorbeeld wijzigen hoe uw gegevens worden gerepliceerd, of de toegangs laag van het account wijzigen van hot naar cool. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw opslag account en zoek en klik vervolgens op **configuratie** onder **instellingen** om de account configuratie te bekijken en/of te wijzigen.

Het wijzigen van de configuratie van het opslag account kan leiden tot extra kosten. Zie de pagina met prijzen voor [Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie.

## <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

Als u een opslagaccount wilt verwijderen dat u niet langer gebruikt, gaat u in [Azure Portal](https://portal.azure.com) naar het opslagaccount en klikt u op **Verwijderen**. Als u een opslagaccount verwijdert, wordt het hele account verwijderd, inclusief alle gegevens in het account.

> [!WARNING]
> Het is niet mogelijk om een verwijderd opslagaccount te herstellen of om inhoud terug te halen die het account vóór verwijdering bevatte. Zorg ervoor dat u een back-up maakt van alles dat u wilt opslaan, voordat u het account verwijdert. Dit geldt ook voor alle resources in het account: als u blobs, tabellen, wachtrijen of bestanden verwijdert, worden deze permanent verwijderd.
> 

Als u probeert een opslagaccount te verwijderen dat is gekoppeld aan een virtuele machine van Azure, ziet u mogelijk een foutbericht dat het account nog in gebruik is. Zie [Troubleshoot errors when you delete Azure storage accounts, containers, or VHDs](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md) (Fouten oplossen met het verwijderen van Azure-opslagaccounts, containers of VHD's) voor informatie over het oplossen van deze fout.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Storage-account](storage-account-overview.md)
- [Een opslagaccount maken](storage-quickstart-create-account.md)
