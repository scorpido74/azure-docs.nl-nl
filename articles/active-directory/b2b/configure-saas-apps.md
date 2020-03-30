---
title: SaaS-apps configureren voor B2B-samenwerking - Azure AD
description: Code- en PowerShell-voorbeelden voor Azure Active Directory B2B-samenwerking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272953"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>SaaS-toepassingen configureren voor B2B-samenwerking

Azure Active Directory (Azure AD) B2B-samenwerking werkt met de meeste apps die zijn geïntegreerd met Azure AD. In deze sectie doorlopen we instructies voor het configureren van een aantal populaire SaaS-apps voor gebruik met Azure AD B2B.

Voordat u naar app-specifieke instructies kijkt, volgen enkele vuistregels:

* Voor de meeste apps moet de installatie van de gebruiker handmatig gebeuren. Dat wil zeggen, gebruikers moeten handmatig worden gemaakt in de app ook.

* Voor apps die automatische instellingen ondersteunen, zoals Dropbox, worden afzonderlijke uitnodigingen gemaakt van de apps. Gebruikers moeten er zeker van zijn dat ze elke uitnodiging accepteren.

* Stel in de gebruikerskenmerken altijd **gebruikers-id** in om eventuele problemen met verminkte gebruikersprofielschijf (UPD) bij gastgebruikers **te**beperken.


## <a name="dropbox-business"></a>Dropbox Business

Als u gebruikers in staat wilt stellen zich aan te melden met hun organisatieaccount, moet u Dropbox Business handmatig configureren om Azure AD te gebruiken als saml-identiteitsprovider (Security Assertion Markup Language). Als Dropbox Business hiervoor niet is geconfigureerd, kunnen gebruikers zich niet op de hoogte stellen van of anderszins toestaan dat ze zich aanmelden met Azure AD.

1. Als u de Dropbox Business-app wilt toevoegen aan Azure AD, selecteert u **Enterprise-toepassingen** in het linkerdeelvenster en klikt u op **Toevoegen**.

   ![De knop 'Toevoegen' op de pagina Enterprise-toepassingen](media/configure-saas-apps/add-dropbox.png)

2. Typ **dropbox** in het zoekvak en selecteer Dropbox **voor Bedrijven** in het venster Met een **aanvraag** toevoegen in de lijst met resultaten.

   ![Zoeken naar 'dropbox' op de pagina Een toepassingspagina toevoegen](media/configure-saas-apps/add-app-dialog.png)

3. Selecteer op de pagina **Eén aanmelding** **in** het linkerdeelvenster en voer **vervolgens user.mail** in het vak **Gebruikers-id** in. (Het is standaard ingesteld als UPN.)

   ![Eenmalige aanmelding voor de app configureren](media/configure-saas-apps/configure-app-sso.png)

4. Als u het certificaat wilt downloaden dat u wilt gebruiken voor Dropbox-configuratie, selecteert u **DropBox configureren**en selecteert u **vervolgens DE URL van SAML-geadresseerde aan-papier** service in de lijst.

   ![Het certificaat voor Dropbox-configuratie downloaden](media/configure-saas-apps/download-certificate.png)

5. Meld u aan bij Dropbox met de aanmeldings-URL van de pagina **Eén aanmelding.**

   ![Schermafbeelding van de aanmeldingspagina van Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Selecteer in het menu de optie **Beheerconsole**.

   ![De link 'Beheerconsole' in het Dropbox-menu](media/configure-saas-apps/dropbox-menu.png)

7. Selecteer **in** het dialoogvenster Verificatie de optie **Meer,** upload het certificaat en voer vervolgens in het vak **AanmeldingsURL** de URL voor eenmalig aanmelden in.

   ![De koppeling 'Meer' in het dialoogvenster Samengevouwen verificatie](media/configure-saas-apps/dropbox-auth-01.png)

   ![De URL 'Aanmelden' in het dialoogvenster Uitgebreide verificatie](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Als u de automatische gebruikersinstelling in de Azure-portal wilt configureren, selecteert u **Voorziening** in het linkerdeelvenster, selecteert u **Automatisch** in het vak **Inrichtingsmodus** en selecteert u **Vervolgens Toestaan**.

   ![Automatische gebruikersvoorziening configureren in de Azure-portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Nadat gast- of ledengebruikers zijn ingesteld in de Dropbox-app, ontvangen ze een aparte uitnodiging van Dropbox. Als u Dropbox-enkele aanmelding wilt gebruiken, moeten genodigden de uitnodiging accepteren door op een koppeling in de uitnodiging te klikken.

## <a name="box"></a>Box
U gebruikers in staat stellen gastgebruikers van Box te verifiëren met hun Azure AD-account met behulp van federatie die is gebaseerd op het SAML-protocol. In deze procedure upload je metadata naar Box.com.

1. Voeg de Box-app toe vanuit de bedrijfsapps.

2. Eenmalige aanmelding configureren in de volgende volgorde:

   ![Schermafbeelding van de configuratie-instellingen voor één aanmelding](media/configure-saas-apps/configure-box-sso.png)

   a. Controleer **in** het vak Aanmelden op URL of de aanmeldings-URL op de juiste manier is ingesteld voor Box in de Azure-portal. Deze URL is de URL van uw Box.com tenant. Het moet de *https://.box.com*naamgevingsconventie volgen.  
   De **id** is niet van toepassing op deze app, maar wordt nog steeds weergegeven als een verplicht veld.

   b. Voer in het vak **Gebruikers-id** **user.mail** (voor SSO voor gastaccounts) in.

   c. Klik onder **SAML-ondertekeningscertificaat**op **Nieuw certificaat maken**.

   d. Als u wilt beginnen met het configureren van uw Box.com tenant om Azure AD als identiteitsprovider te gebruiken, downloadt u het metagegevensbestand en slaat u het op uw lokale station op.

   e. Stuur het metagegevensbestand door naar het ondersteuningsteam van Box, dat eenmalige aanmelding voor u configureert.

3. Selecteer voor automatische gebruikersinstelling van Azure AD in het linkerdeelvenster De optie **Inrichten**en selecteer **Vervolgens Autoriseren**.

   ![Azure AD autoriseren om verbinding te maken met Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Net als Dropbox-genodigden moeten genodigden hun uitnodiging inwisselen in de Box-app.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over Azure AD B2B-samenwerking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Dynamische groepen en B2B-samenwerking](use-dynamic-groups.md)
- [B2B-samenwerking gebruiker claims mapping](claims-mapping.md)
- [Office 365 extern delen](o365-external-user.md)

