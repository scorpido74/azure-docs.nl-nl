---
title: Quickstart - Toegang verkrijgen en een nieuwe tenant maken - Azure AD
description: Instructies over het zoeken van Azure Active Directory en het maken van een nieuwe tenant voor uw organisatie.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d6341aeb6db89d43ef887a3ae50c4439e3867e6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318604"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Quickstart: Een nieuwe tenant maken in Azure Active Directory
U kunt al uw administratieve taken uitvoeren met behulp van de portal van Azure Active Directory (Azure AD), met inbegrip van het maken van een nieuwe tenant voor uw organisatie. 

In deze Snelstartgids leert u hoe u bij de Azure Portal en Azure Active Directory komt en hoe u een eenvoudige tenant voor uw organisatie maakt.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-new-tenant-for-your-organization"></a>Een nieuwe tenant maken voor uw organisatie
Nadat u zich bij Azure Portal aanmeldt, kunt u een nieuwe tenant maken voor uw organisatie. De nieuwe tenant vertegenwoordigt uw organisatie en helpt u bij het beheren van een specifiek exemplaar van Microsoft cloudservices voor uw interne en externe gebruikers.

### <a name="to-create-a-new-tenant"></a>Een nieuwe tenant maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) van uw organisatie.

1. Selecteer **Een resource maken** in het menu van de Azure-portal.  

    ![De pagina Resource maken van Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Selecteer **Identiteit** en vervolgens **Azure Active Directory**.

    De pagina **Map maken** wordt weergegeven.

    ![De pagina Maken voor Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Op de pagina **Map maken**, voer de volgende informatie in:
    
    - Type _Contoso_ in het vak **Organisatienaam**.

    - Type _Contoso_ in het vak **Initiële domeinnaam**.

    - Laat de optie _Verenigde Staten_ in het vak **Land of regio**.

1. Selecteer **Maken**.

De nieuwe tenant wordt gemaakt met het domein contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Resources opschonen
Als u deze app niet meer gebruikt, kunt u de tenant met de volgende stappen verwijderen:

- Zorg ervoor dat u bent aangemeld bij de map die u wilt verwijderen via het filter **Map en abonnement** in Azure Portal en schakel zo nodig over naar de doelmap.
- Selecteer **Azure Active Directory**, en klik vervolgens op de pagina **Contoso - Overzicht** en selecteer **Map verwijderen**.

    De tenant en de bijbehorende informatie wordt verwijderd.

    ![Overzichtspagina, waarop de knop Map verwijderen is gemarkeerd](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Volgende stappen
- Zie [Een aangepaste domeinnaam toevoegen aan Azure Active Directory](add-custom-domain.md) voor het wijzigen of toevoegen van meer informatie over extra domeinnamen

- Gebruikers toevoegen, zie [Een nieuwe gebruiker toevoegen of verwijderen](add-users-azure-active-directory.md)

- Groepen en leden toevoegen, zie [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

- Meer informatie over [op rollen gebaseerde toegang met behulp van Privileged Identity Management](../../role-based-access-control/best-practices.md) en [voorwaardelijke toegang](../../role-based-access-control/conditional-access-azure-management.md) voor het beheren van de app en de toegang tot resources van uw organisatie.

- Informatie over Azure Active Directory, met inbegrip van [basisinformatie over licenties, terminologie en bijbehorende functies](active-directory-whatis.md).