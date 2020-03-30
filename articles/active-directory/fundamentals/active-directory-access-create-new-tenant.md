---
title: Snelstart - Toegang tot & nieuwe tenant maken - Azure AD
description: Instructies over het zoeken van Azure Active Directory en het maken van een nieuwe tenant voor uw organisatie.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049995"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Snelstart: een nieuwe tenant maken in Azure Active Directory
U kunt al uw administratieve taken uitvoeren met behulp van de portal van Azure Active Directory (Azure AD), met inbegrip van het maken van een nieuwe tenant voor uw organisatie. 

In deze Snelstartgids leert u hoe u bij de Azure Portal en Azure Active Directory komt en hoe u een eenvoudige tenant voor uw organisatie maakt.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-new-tenant-for-your-organization"></a>Een nieuwe tenant maken voor uw organisatie
Nadat u zich bij Azure Portal aanmeldt, kunt u een nieuwe tenant maken voor uw organisatie. De nieuwe tenant vertegenwoordigt uw organisatie en helpt u bij het beheren van een specifiek exemplaar van Microsoft cloudservices voor uw interne en externe gebruikers.

### <a name="to-create-a-new-tenant"></a>Een nieuwe tenant maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/)van uw organisatie.

1. Selecteer in het menu azure portal de optie **Een resource maken**.  

    ![Pagina Resoure van Azure Active Directory Maken](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Selecteer **Identiteit**en selecteer **vervolgens Azure Active Directory**.

    De pagina **Map maken** wordt weergegeven.

    ![De pagina Maken voor Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Op de pagina **Map maken**, voer de volgende informatie in:
    
    - Type _Contoso_ in het vak **Organisatienaam**.

    - Type _Contoso_ in het vak **Initiële domeinnaam**.

    - Laat de optie _Verenigde Staten_ in het vak **Land of regio**.

1. Selecteer **Maken**.

De nieuwe tenant wordt gemaakt met het domein contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze toepassing niet meer wilt gebruiken, u de tenant verwijderen met de volgende stappen:

- Controleer of u bent aangemeld bij de map die u wilt verwijderen via het **directory + abonnementsfilter** in de Azure Portal en schakel indien nodig over naar de doelmap.
- Selecteer **Azure Active Directory**, en klik vervolgens op de pagina **Contoso - Overzicht** en selecteer **Map verwijderen**.

    De tenant en de bijbehorende informatie wordt verwijderd.

    ![Overzichtspagina, met gemarkeerde knop Map verwijderen](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Volgende stappen
- Zie [Een aangepaste domeinnaam toevoegen aan Azure Active Directory](add-custom-domain.md) voor het wijzigen of toevoegen van meer informatie over extra domeinnamen

- Gebruikers toevoegen, zie [Een nieuwe gebruiker toevoegen of verwijderen](add-users-azure-active-directory.md)

- Groepen en leden toevoegen, zie [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- Meer informatie over [op rollen gebaseerde toegang met privileged identity management](../../role-based-access-control/pim-azure-resource.md) en voorwaardelijke [toegang](../../role-based-access-control/conditional-access-azure-management.md) om de toepassing- en brontoegang van uw organisatie te beheren.

- Informatie over Azure Active Directory, met inbegrip van [basisinformatie over licenties, terminologie en bijbehorende functies](active-directory-whatis.md).
