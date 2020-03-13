---
title: Uw aangepaste domein - Azure Active Directory toevoegen | Microsoft Docs
description: Instructies over het toevoegen van een aangepast domein met behulp van Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262150"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>De naam van uw aangepaste domein met behulp van de Azure Active Directory-portal toevoegen

Elke nieuwe Azure AD-Tenant wordt geleverd met een initiële domein naam, *\<domainname >. onmicrosoft. com*. U kunt de initiële domein naam niet wijzigen of verwijderen, maar de namen van uw organisatie toevoegen. Door aangepaste domein namen toe te voegen, kunt u gebruikers namen maken die bekend zijn bij uw gebruikers, zoals *alain\@contoso.com*.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u een aangepaste domein naam kunt toevoegen, maakt u uw domein naam met een domein registratie service. Zie voor een geaccrediteerd domein registratie [ICANN-accreditatie registraties](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Maken van uw directory in Azure AD

Nadat u uw domeinnaam hebt ontvangen, kunt u uw eerste Azure AD-directory maken. Meld u aan bij de Azure Portal voor uw directory met een account met de rol **eigenaar** voor het abonnement.

Maak een nieuwe map door de stappen in [een nieuwe Tenant voor uw organisatie maken](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)te volgen.

>[!IMPORTANT]
>De persoon die de tenant maakt, wordt automatisch de globale beheerder voor de tenant. De globale beheerder kunt u extra beheerders toevoegen aan de tenant.

Zie [Azure RBAC-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)voor meer informatie over abonnements rollen.

>[!TIP]
> Als u van plan bent om uw on-premises Windows Server AD te hosten met Azure AD, moet u de optie **Ik wil dit domein configureren voor eenmalige aanmelding met mijn lokale Active Directory** wanneer u het Azure AD Connect-hulp programma uitvoert om uw mappen te synchroniseren.
>
> Daarnaast moet u dezelfde domeinnaam registreren die u selecteert voor het federeren met uw on-premises directory in de stap **Azure AD-domein** in de wizard. Zie [controleren of het Azure AD-domein is geselecteerd voor Federatie](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)om te zien wat de installatie lijkt te zijn. Als u het hulp programma Azure AD Connect niet hebt, kunt u [het hier downloaden](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Uw aangepaste domeinnaam toevoegen aan Azure AD

Nadat u uw directory hebt gemaakt, kunt u uw aangepaste domeinnaam toevoegen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

1. Zoek en selecteer *Azure Active Directory* op elke pagina. Selecteer vervolgens **aangepaste domein namen** > **aangepast domein toe te voegen**.

    ![Pagina aangepaste domein namen, met weer gegeven aangepast domein toevoegen](media/add-custom-domain/add-custom-domain.png)

1. Voer in **aangepaste domein naam**de nieuwe naam van uw organisatie in, in dit voor beeld *contoso.com*. Selecteer **Add domain**.

    ![Pagina aangepaste domein namen, met de pagina aangepaste domein toevoegen](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >U moet *. com*, *.net*of een andere extensie op het hoogste niveau gebruiken om dit goed te laten werken.

    Het niet-geverifieerde domein is toegevoegd. De pagina **contoso.com** wordt weer gegeven met uw DNS-gegevens. Sla deze informatie op. U hebt deze later nodig om een TXT-record te maken om DNS te configureren.

    ![Contoso-pagina met DNS-vermelding informatie](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Uw DNS-gegevens toevoegen aan de domeinregistrar

Nadat u uw aangepaste domeinnaam aan Azure AD toevoegen, moet u terug naar uw domeinregistrar en de Azure AD DNS-gegevens uit het gekopieerde TXT-bestand toevoegen. Als u deze TXT-record voor uw domein maakt, wordt het eigendom van uw domein naam gecontroleerd.

Ga terug naar uw domein registratie service en maak een nieuwe TXT-record voor uw domein op basis van de gekopieerde DNS-gegevens. Stel de TTL (time to Live) in op 3600 seconden (60 minuten) en sla de record op.

>[!IMPORTANT]
>U kunt zoveel domeinnamen als u wilt registreren. Elk domein wordt echter een eigen TXT-record opgehaald uit Azure AD. Wees voorzichtig wanneer u de gegevens van het TXT-bestand invoert bij het domein registratie service. Als u de verkeerde of dubbele gegevens per ongeluk opgeeft, moet u wachten tot de TTL-tijd (60 minuten) voordat u het opnieuw kunt proberen.

## <a name="verify-your-custom-domain-name"></a>Controleer of de naam van uw aangepaste domein

Nadat u uw aangepaste domein naam hebt geregistreerd, moet u ervoor zorgen dat deze geldig is in azure AD. De doorgifte van uw domein registratie voor Azure AD kan onmiddellijk worden uitgevoerd, maar het kan enkele dagen duren, afhankelijk van uw domein registratie.

Voer de volgende stappen uit om uw aangepaste domein naam te verifiëren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

1. Zoek en selecteer *Azure Active Directory* op een wille keurige pagina en selecteer vervolgens **aangepaste domein namen**.

1. In **aangepaste domein namen**selecteert u de naam van het aangepaste domein. In dit voor beeld selecteert u **contoso.com**.

    ![Fabrikam - aangepast domein namen pagina, met contoso gemarkeerd](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Op de pagina **contoso.com** selecteert u **verifiëren** om er zeker van te zijn dat uw aangepaste domein juist is geregistreerd en geldig is voor Azure AD.

    ![Contoso-pagina met DNS-vermeldingsgegevens en de knop controleren](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Nadat u uw aangepaste domein naam hebt geverifieerd, kunt u uw TXT-of MX-verificatie bestand verwijderen.

## <a name="common-verification-issues"></a>Veelvoorkomende problemen met verificatie

Als Azure AD een aangepaste domeinnaam niet verifiëren kan, probeert u de volgende suggesties:

- **Wacht minstens een uur en probeer het opnieuw**. DNS-records moeten zijn doorgegeven voordat Azure AD het domein kan verifiëren. Dit proces kan een uur of langer duren.

- **Controleer of de DNS-record juist is.** Ga terug naar de registratie site van de domein naam. Zorg ervoor dat de vermelding daar staat en dat deze overeenkomt met de DNS-invoer gegevens die door Azure AD worden verstrekt.

  Als u de record op de registratie site niet kunt bijwerken, deelt u de vermelding met iemand die gemachtigd is om de vermelding toe te voegen en controleert u of deze juist is.

- **Zorg ervoor dat de domein naam niet al in gebruik is in een andere map.** Een domein naam kan alleen in één Directory worden geverifieerd. Als uw domein naam momenteel in een andere Directory is geverifieerd, kan deze ook niet worden geverifieerd in de nieuwe map. U lost dit probleem duplicatie, moet u de domeinnaam verwijderen uit de oude map. Zie [aangepaste domein namen beheren](../users-groups-roles/domains-manage.md)voor meer informatie over het verwijderen van domein namen.

- **Zorg ervoor dat u geen niet-beheerde Power BI tenants hebt.** Als uw gebruikers hebt geactiveerd Power BI via self-service-aanmelding en die een niet-beheerde tenant voor uw organisatie zijn gemaakt, moet u via management als een beheerder intern of extern uitvoeren met behulp van PowerShell. Zie [Een niet-beheerde directory overnemen als beheerder in Azure Active Directory](../users-groups-roles/domains-admin-takeover.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Een andere globale beheerder toevoegen aan uw directory. Zie [rollen en beheerders toewijzen](active-directory-users-assign-role-azure-portal.md)voor meer informatie.

- Gebruikers toevoegen aan uw domein. Zie [hoe gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)voor meer informatie.

- De gegevens van uw domein naam in Azure AD beheren. Zie [aangepaste domein namen beheren](../users-groups-roles/domains-manage.md)voor meer informatie.

- Zie [uw on-premises Directory's integreren met Azure Active Directory](../connect/active-directory-aadconnect.md)als u on-premises versies van Windows Server hebt die u naast Azure Active Directory wilt gebruiken.
