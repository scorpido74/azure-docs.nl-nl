---
title: Uw aangepaste domein toevoegen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toevoegen van een aangepast domein met Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262150"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Uw aangepaste domeinnaam toevoegen met de Azure Active Directory-portal

Elke nieuwe Azure AD-tenant wordt geleverd met een oorspronkelijke domeinnaam, * \<domeinnaam>.onmicrosoft.com*. U de oorspronkelijke domeinnaam niet wijzigen of verwijderen, maar u de namen van uw organisatie wel toevoegen. Als u aangepaste domeinnamen toevoegt, u gebruikersnamen maken die bekend zijn bij uw gebruikers, zoals *\@alain contoso.com.*

## <a name="before-you-begin"></a>Voordat u begint

Voordat u een aangepaste domeinnaam toevoegen, maakt u uw domeinnaam met een domeinregistrar. Zie [ICANN-geaccrediteerde registrars](https://www.icann.org/registrar-reports/accredited-list.html)voor een geaccrediteerde domeinregistrar.

## <a name="create-your-directory-in-azure-ad"></a>Uw map maken in Azure AD

Nadat u uw domeinnaam hebt opgemaakt, u uw eerste Azure AD-map maken. Meld u aan bij de Azure-portal voor uw directory met behulp van een account met de rol **Eigenaar** voor het abonnement.

Maak uw nieuwe map door de stappen te volgen in [Een nieuwe tenant maken voor uw organisatie.](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)

>[!IMPORTANT]
>De persoon die de tenant maakt, is automatisch de globale beheerder voor die tenant. De globale beheerder kan extra beheerders toevoegen aan de tenant.

Zie [Azure RBAC-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)voor meer informatie over abonnementsrollen.

>[!TIP]
> Als u van plan bent uw on-premises Windows Server AD te beoordelen met Azure AD, moet u kiezen dat **ik van plan ben dit domein te configureren voor eenmalige aanmelding met mijn lokale Active Directory** wanneer u het Azure AD Connect-hulpprogramma uitvoert om uw mappen te synchroniseren.
>
> Daarnaast moet u dezelfde domeinnaam registreren die u selecteert voor het federeren met uw on-premises directory in de stap **Azure AD-domein** in de wizard. Zie [Het Azure AD-domein verifiëren dat is geselecteerd voor federatie](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)als u wilt zien hoe die instelling eruit ziet. Als u niet beschikt over het Azure AD Connect-hulpprogramma, u [deze hier downloaden.](https://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Uw aangepaste domeinnaam toevoegen aan Azure AD

Nadat u uw map hebt gemaakt, u uw aangepaste domeinnaam toevoegen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

1. Zoeken naar en selecteer *Azure Active Directory* op elke pagina. Selecteer vervolgens **Aangepaste domeinnamen** > **Voeg aangepast domein toe.**

    ![Aangepaste pagina domeinnamen, met Aangepaste domein toevoegen weergegeven](media/add-custom-domain/add-custom-domain.png)

1. Voer in **Aangepaste domeinnaam**de nieuwe naam van uw organisatie in, in dit voorbeeld *contoso.com*. Selecteer **Add domain**.

    ![Aangepaste pagina domeinnamen, met aangepaste domeinpagina toevoegen](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >U moet *.com,* *.net*of een andere extensie op het hoogste niveau opnemen om dit goed te laten werken.

    Het niet-geverifieerde domein wordt toegevoegd. De **contoso.com** pagina wordt weergegeven met uw DNS-gegevens. Sla deze informatie op. U hebt het later nodig om een TXT-record te maken om DNS te configureren.

    ![Contoso-pagina met DNS-invoergegevens](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Uw DNS-gegevens toevoegen aan de domeinregistrar

Nadat u uw aangepaste domeinnaam aan Azure AD hebt toegevoegd, moet u terugkeren naar uw domeinregistrar en de DNS-gegevens van Azure AD toevoegen uit uw gekopieerde TXT-bestand. Als u deze TXT-record voor uw domein maakt, wordt het eigendom van uw domeinnaam geverifieerd.

Ga terug naar uw domeinregistrar en maak een nieuwe TXT-record voor uw domein op basis van uw gekopieerde DNS-gegevens. Stel de tijd in om te leven (TTL) op 3600 seconden (60 minuten) en sla vervolgens het record op.

>[!IMPORTANT]
>U zoveel domeinnamen registreren als u wilt. Elk domein krijgt echter zijn eigen TXT-record van Azure AD. Wees voorzichtig wanneer u de TXT-bestandsinformatie invoert bij de domeinregistrar. Als u per ongeluk de verkeerde of dubbele informatie invoert, moet u wachten tot de TTL een keer is (60 minuten) voordat u het opnieuw proberen.

## <a name="verify-your-custom-domain-name"></a>Uw aangepaste domeinnaam verifiëren

Nadat u uw aangepaste domeinnaam hebt geregistreerd, controleert u of deze geldig is in Azure AD. De verspreiding van uw domeinregistrar naar Azure AD kan onmiddellijk zijn of het kan enkele dagen duren, afhankelijk van uw domeinregistrar.

Voer de volgende stappen uit om uw aangepaste domeinnaam te verifiëren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

1. Zoek naar en selecteer *Azure Active Directory* op een pagina en selecteer vervolgens Aangepaste **domeinnamen**.

1. Selecteer **in Aangepaste domeinnamen**de aangepaste domeinnaam. Selecteer in dit voorbeeld **contoso.com**.

    ![Fabrikam - Aangepaste domeinnaamnamen pagina, met contoso gemarkeerd](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Selecteer **op** de pagina contoso.com **controleren** om te controleren of uw aangepaste domein correct is geregistreerd en geldig is voor Azure AD.

    ![Contoso-pagina met DNS-invoergegevens en de knop Verifiëren](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Nadat u uw aangepaste domeinnaam hebt geverifieerd, u uw verificatie-TXT- of MX-bestand verwijderen.

## <a name="common-verification-issues"></a>Veelvoorkomende verificatieproblemen

Als Azure AD een aangepaste domeinnaam niet kan verifiëren, probeert u de volgende suggesties:

- **Wacht minstens een uur en probeer het opnieuw.** DNS-records moeten zijn doorgegeven voordat Azure AD het domein kan verifiëren. Dit proces kan een uur of langer duren.

- **Controleer of de DNS-record correct is.** Ga terug naar de site van de domeinnaamregistrar. Zorg ervoor dat de vermelding er is en dat deze overeenkomt met de DNS-invoergegevens van Azure AD.

  Als u de record op de registrarsite niet bijwerken, deelt u de vermelding met iemand die machtigingen heeft om de vermelding toe te voegen en te controleren of deze correct is.

- **Zorg ervoor dat de domeinnaam nog niet in een andere map wordt gebruikt.** Een domeinnaam kan slechts in één map worden geverifieerd. Als uw domeinnaam momenteel is geverifieerd in een andere map, kan deze ook niet worden geverifieerd in de nieuwe map. Als u dit duplicatieprobleem wilt oplossen, moet u de domeinnaam uit de oude map verwijderen. Zie [Aangepaste domeinnamen beheren](../users-groups-roles/domains-manage.md)voor meer informatie over het verwijderen van domeinnamen.

- **Zorg ervoor dat u geen onbeheerde Power BI-tenants hebt.** Als uw gebruikers Power BI hebben geactiveerd via selfservice-aanmelding en een onbeheerde tenant voor uw organisatie hebben gemaakt, moet u het beheer overnemen als interne of externe beheerder met PowerShell. Zie [Een niet-beheerde directory overnemen als beheerder in Azure Active Directory](../users-groups-roles/domains-admin-takeover.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voeg een andere globale beheerder toe aan uw directory. Zie [Rollen en beheerders toewijzen](active-directory-users-assign-role-azure-portal.md)voor meer informatie.

- Gebruikers toevoegen aan uw domein. Zie [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)voor meer informatie.

- Uw domeinnaamgegevens beheren in Azure AD. Zie [Aangepaste domeinnamen beheren](../users-groups-roles/domains-manage.md)voor meer informatie .

- Zie [Uw on-premises mappen integreren met Azure Active Directory](../connect/active-directory-aadconnect.md)als u on-premises versies van Windows Server hebt die u naast Azure Active Directory wilt gebruiken.
