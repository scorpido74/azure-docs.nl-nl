---
title: B2B-samenwerking voor hybride organisaties - Azure AD
description: Geef partners toegang tot zowel on-premises als cloudresources met Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272474"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samenwerking voor hybride organisaties

Azure Active Directory (Azure AD) B2B-samenwerking maakt het voor u gemakkelijk om uw externe partners toegang te geven tot apps en bronnen in uw organisatie. Dit geldt zelfs in een hybride configuratie waar u zowel on-premises als cloudgebaseerde bronnen hebt. Het maakt niet uit of u momenteel externe partneraccounts lokaal beheert in uw on-premises identiteitssysteem of dat u de externe accounts in de cloud beheert als Azure AD B2B-gebruikers. U deze gebruikers nu toegang verlenen tot bronnen op beide locaties, met dezelfde aanmeldingsreferenties voor beide omgevingen.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises apps

Als uw organisatie Azure AD B2B-samenwerkingsmogelijkheden gebruikt om gastgebruikers van partnerorganisaties uit te nodigen voor uw Azure AD, u deze B2B-gebruikers nu toegang bieden tot on-premises apps.

Voor apps die SAML-gebaseerde verificatie gebruiken, u deze apps beschikbaar maken voor B2B-gebruikers via de Azure-portal, met Azure AD-toepassingsproxy voor verificatie.

Voor apps die Geïntegreerde Windows-verificatie (IWA) met Kerberos constrained delegation (KCD) gebruiken, gebruikt u ook Azure AD Proxy voor verificatie. Voor een autorisatie om te werken is een gebruikersobject echter vereist in de on-premises Windows Server Active Directory. Er zijn twee methoden die u gebruiken om lokale gebruikersobjecten te maken die uw B2B-gastgebruikers vertegenwoordigen.

- U Microsoft Identity Manager (MIM) 2016 SP1 en de MIM-beheeragent voor Microsoft Graph gebruiken.
- U een PowerShell-script gebruiken. (Deze oplossing vereist geen MIM.)

Zie [B2B-gebruikers in Azure AD-toegang verlenen tot uw on-premises toepassingen](hybrid-cloud-to-on-premises.md)voor meer informatie over het implementeren van deze oplossingen.

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Lokaal beheerde partneraccounts toegang verlenen tot cloudbronnen

Vóór Azure AD hebben organisaties met on-premises identiteitssystemen traditioneel partneraccounts beheerd in hun on-premises directory. Als u zo'n organisatie bent, wilt u ervoor zorgen dat uw partners toegang blijven houden terwijl u uw apps en andere bronnen naar de cloud verplaatst. Idealiter wilt u dat deze gebruikers dezelfde set referenties gebruiken om toegang te krijgen tot zowel cloud- als on-premises resources. 

We bieden nu methoden aan waarmee u Azure AD Connect gebruiken om deze lokale accounts te synchroniseren met de cloud als 'gastgebruikers', waarbij de accounts zich gedragen net als Azure AD B2B-gebruikers.

Om uw bedrijfsgegevens te beschermen, u de toegang tot de juiste bronnen beheren en autorisatiebeleid configureren dat deze gastgebruikers anders behandelt dan uw werknemers.

Zie [Lokaal beheerde partneraccounts toegang verlenen tot cloudbronnen met Azure AD B2B-samenwerking](hybrid-on-premises-to-cloud.md)voor implementatiedetails.
 
## <a name="next-steps"></a>Volgende stappen

- [B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen](hybrid-cloud-to-on-premises.md)
- [Lokaal beheerde partneraccounts toegang verlenen tot cloudbronnen met Azure AD B2B-samenwerking](hybrid-on-premises-to-cloud.md)


