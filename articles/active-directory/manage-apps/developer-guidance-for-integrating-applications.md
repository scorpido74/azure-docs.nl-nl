---
title: Uw toepassing registreren om Azure Active Directory te gebruiken | Microsoft Documenten
description: Dit artikel is geschreven voor de IT Pro en bevat richtlijnen voor het integreren van Azure-toepassingen met Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108289"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Line-of-business-apps ontwikkelen voor Azure Active Directory
Deze handleiding biedt een overzicht van het ontwikkelen van LoB-toepassingen (Line-of-Business) voor Azure Active Directory (AD). De beoogde doelgroep is globale beheerders van Active Directory/Office 365.

## <a name="overview"></a>Overzicht
Door toepassingen te bouwen die zijn geïntegreerd met Azure AD, kunnen gebruikers in uw organisatie één aanmelding doen met Office 365. Als u de toepassing in Azure AD hebt, hebt u controle over het verificatiebeleid voor de toepassing. Zie [Toegangsregels configureren](../conditional-access/app-based-mfa.md)voor meer informatie over voorwaardelijke toegang en hoe u apps beveiligen met multi-factor authenticatie (MFA).

Registreer uw toepassing om Azure Active Directory te gebruiken. Als u de toepassing registreert, kunnen uw ontwikkelaars Azure AD gebruiken om gebruikers te verifiëren en toegang te vragen tot gebruikersbronnen zoals e-mail, agenda en documenten.

Elk lid van uw directory (niet gasten) kan een toepassing registreren, ook wel bekend als *het maken van een toepassingsobject.*

Als u een toepassing registreert, kan elke gebruiker het volgende doen:

* Een identiteit voor hun toepassing krijgen die Azure AD herkent
* Een of meer geheimen/sleutels ophalen die de toepassing kan gebruiken om zichzelf te authenticeren bij AD
* Merk de toepassing in de Azure-portal met een aangepaste naam, logo, enz.
* Pas Azure AD-autorisatiefuncties toe op hun app, waaronder:

  * RBAC (op rollen gebaseerd toegangsbeheer)
  * Azure Active Directory als oAuth-autorisatieserver (een API beveiligen die door de toepassing wordt weergegeven)
* De vereiste machtigingen declareren die nodig zijn om de toepassing te laten functioneren zoals verwacht, waaronder:

     - App-machtigingen (alleen globale beheerders). Bijvoorbeeld: Rollidmaatschap in een andere Azure AD-toepassing of rollidmaatschap ten opzichte van een Azure Resource, Resource Group of Abonnement
     - Gedelegeerde machtigingen (elke gebruiker). Bijvoorbeeld: Azure AD, Aanmelding en Leesprofiel

> [!NOTE]
> Standaard kan elk lid een aanvraag registreren. Zie [Hoe toepassingen worden toegevoegd aan Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)voor meer informatie over het beperken van machtigingen voor het registreren van toepassingen aan specifieke leden.
>
>

Dit is wat u, de globale beheerder, moet doen om ontwikkelaars te helpen hun toepassing klaar te maken voor productie:

* Toegangsregels configureren (toegangsbeleid/MFA)
* De app configureren om gebruikerstoewijzing vereisen en gebruikers toewijzen
* De standaardgebruikerstoestemmingservaring onderdrukken

## <a name="configure-access-rules"></a>Toegangsregels configureren
Configureer toegangsregels per toepassing voor uw SaaS-apps. U bijvoorbeeld MFA vereisen of alleen toegang geven aan gebruikers op vertrouwde netwerken. De details hiervoor zijn beschikbaar in het document [Toegangsregels configureren](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>De app configureren om gebruikerstoewijzing vereisen en gebruikers toewijzen
Gebruikers hebben standaard toegang tot toepassingen zonder dat ze zijn toegewezen. Als de toepassing echter rollen blootlegt of als u wilt dat de toepassing wordt weergegeven in het toegangspaneel van een gebruiker, moet u gebruikerstoewijzing vereisen.

Als u een Azure AD Premium- of Enterprise Mobility Suite-abonnee (EMS) bent, raden we u ten zeerste aan groepen te gebruiken. Als u groepen aan de toepassing toewijs, u doorlopend toegangsbeheer delegeren aan de eigenaar van de groep. U de groep maken of de verantwoordelijke partij in uw organisatie vragen om de groep te maken met behulp van uw groepsbeheerfaciliteit.

[Gebruikers en groepen toewijzen aan een toepassing](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Toestemming van de gebruiker onderdrukken
Standaard gaat elke gebruiker door een toestemmingservaring om zich aan te melden. De toestemmingservaring, waarbij gebruikers worden gevraagd om machtigingen voor een toepassing te verlenen, kan verontrustend zijn voor gebruikers die niet bekend zijn met het nemen van dergelijke beslissingen.

Voor toepassingen die u vertrouwt, u de gebruikerservaring vereenvoudigen door namens uw organisatie in te stemmen met de toepassing.

Zie Toepassingen integreren met Azure Active Directory voor meer informatie over toestemming van gebruikers en de [toestemmingservaring](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)in Azure.

## <a name="related-articles"></a>Verwante artikelen
* [Veilige externe toegang tot on-premises toepassingen inschakelen met Azure AD-toepassingsproxy](application-proxy.md)
* [Toegang tot apps beheren met Azure AD](what-is-access-management.md)

