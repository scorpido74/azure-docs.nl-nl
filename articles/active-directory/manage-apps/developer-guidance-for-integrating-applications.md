---
title: Uw toepassing registreren voor gebruik van Azure Active Directory | Microsoft Docs
description: Dit artikel bevat richt lijnen voor de integratie van Azure-toepassingen met Active Directory voor IT-professionals.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b24bd190eac9cb6c29ceab6eb597d8d1c48f2ff
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605087"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Line-of-Business-Apps ontwikkelen voor Azure Active Directory
Deze hand leiding bevat een overzicht van het ontwikkelen van LoB-toepassingen (line-of-Business) voor Azure Active Directory (AD). De beoogde doel groep is Active Directory/Microsoft 365 globale beheerders.

## <a name="overview"></a>Overzicht
Het bouwen van toepassingen die zijn geïntegreerd met Azure AD biedt gebruikers in uw organisatie eenmalige aanmelding met Microsoft 365. Met de toepassing in azure AD kunt u het verificatie beleid voor de toepassing beheren. Zie [toegangs regels configureren](../conditional-access/app-based-mfa.md)voor meer informatie over voorwaardelijke toegang en het beveiligen van apps met multi-factor Authentication (MFA).

Registreer uw toepassing voor gebruik van Azure Active Directory. Het registreren van de toepassing betekent dat uw ontwikkel aars Azure AD kunnen gebruiken om gebruikers te verifiëren en om toegang aan te vragen voor gebruikers bronnen zoals e-mail, agenda en documenten.

Elk lid van uw directory (geen gasten) kan een toepassing registreren, ook wel bekend als *het maken van een toepassings object*.

Als u een toepassing registreert, kan elke gebruiker het volgende doen:

* Een identiteit ophalen voor de toepassing die door Azure AD wordt herkend
* Een of meer geheimen/sleutels ophalen die de toepassing kan gebruiken om zichzelf te verifiëren bij AD
* Brand de toepassing in de Azure Portal met een aangepaste naam, logo, enzovoort.
* Pas Azure AD-autorisatie functies toe op de app, waaronder:

  * RBAC (op rollen gebaseerd toegangsbeheer)
  * Azure Active Directory als oAuth-autorisatie server (een API beveiligen die wordt weer gegeven door de toepassing)
* Declareer de vereiste machtigingen die nodig zijn om de toepassing te laten werken zoals verwacht, waaronder:

     - App-machtigingen (alleen voor globale beheerders). Bijvoorbeeld: rollidmaatschap in een andere Azure AD-toepassing of rollidmaatschap ten opzichte van een Azure-resource, resource groep of abonnement
     - Gedelegeerde machtigingen (een wille keurige gebruiker). Bijvoorbeeld: Azure AD, aanmelden en Profiel lezen

> [!NOTE]
> Standaard kan elk lid een toepassing registreren. Zie [hoe toepassingen worden toegevoegd aan Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)voor meer informatie over het beperken van machtigingen voor het registreren van toepassingen voor specifieke leden.
>
>

Dit is wat u, de globale beheerder, nodig hebt om ontwikkel aars te helpen hun toepassing gereed te maken voor productie:

* Toegangs regels (toegangs beleid/MFA) configureren
* De app configureren om gebruikers toewijzing te vereisen en gebruikers toe te wijzen
* De standaard gebruikers interface voor toestemming onderdrukken

## <a name="configure-access-rules"></a>Toegangs regels configureren
Toegangs regels per toepassing configureren voor uw SaaS-apps. U kunt bijvoorbeeld MFA vereisen of alleen toegang verlenen aan gebruikers op vertrouwde netwerken. De Details voor deze informatie zijn beschikbaar in het document [toegangs regels configureren](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>De app configureren om gebruikers toewijzing te vereisen en gebruikers toe te wijzen
Standaard hebben gebruikers toegang tot toepassingen zonder te worden toegewezen. Als de toepassing echter rollen beschikbaar maakt of als u wilt dat de toepassing wordt weer gegeven in mijn apps van een gebruiker, moet u de gebruikers toewijzing vereisen.

Als u een Azure AD Premium of een Enter prise Mobility Suite (EMS)-abonnee bent, raden we u ten zeerste aan groepen te gebruiken. Door groepen toe te wijzen aan de toepassing, kunt u doorlopend toegangs beheer delegeren aan de eigenaar van de groep. U kunt de groep maken of de verantwoordelijke partij in uw organisatie vragen de groep te maken met behulp van de groeps beheer faciliteit.

[Gebruikers en groepen toewijzen aan een toepassing](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Toestemming van de gebruiker onderdrukken
Elke gebruiker krijgt standaard een toestemmings ervaring om zich aan te melden. De toestemming om gebruikers te vragen om machtigingen te verlenen aan een toepassing, kan worden afgestemd op gebruikers die geen ervaring hebben met het nemen van dergelijke beslissingen.

Voor toepassingen die u vertrouwt, kunt u de gebruikers ervaring vereenvoudigen door namens uw organisatie toestemming te bieden voor de toepassing.

Zie [toepassingen integreren met Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie over de toestemming van de gebruiker en de toestemming van Azure.

## <a name="related-articles"></a>Gerelateerde artikelen
* [Veilige externe toegang tot on-premises toepassingen met Azure AD-toepassingsproxy inschakelen](application-proxy.md)
* [Toegang tot apps beheren met Azure AD](what-is-access-management.md)

