---
title: Eenmalige aanmelding via SAML voor on-premises apps met Azure AD-app proxy
description: Meer informatie over het bieden van eenmalige aanmelding voor on-premises toepassingen die zijn beveiligd met SAML-verificatie. Externe toegang bieden tot on-premises apps met toepassings proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59e4e527f25a32c8a5274db5eab5d33a7843bc3c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275589"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Eenmalige aanmelding via SAML voor on-premises toepassingen met toepassings proxy

U kunt eenmalige aanmelding (SSO) bieden voor on-premises toepassingen die zijn beveiligd met SAML-verificatie en externe toegang bieden tot deze toepassingen via toepassings proxy. Met eenmalige aanmelding via SAML wordt Azure Active Directory (Azure AD) geverifieerd bij de toepassing met behulp van het Azure AD-account van de gebruiker. Azure AD communiceert de informatie aanmelding voor de toepassing via een verbindingsprotocol. U kunt ook gebruikers aan specifieke toepassings rollen toewijzen op basis van de regels die u in uw SAML-claims definieert. Door toepassings proxy naast SAML SSO in te scha kelen, hebben uw gebruikers externe toegang tot de toepassing en een naadloze SSO-ervaring.

De toepassingen moeten SAML-tokens kunnen gebruiken die zijn uitgegeven door **Azure Active Directory**. Deze configuratie is niet van toepassing op toepassingen die gebruikmaken van een on-premises ID-provider. Voor deze scenario's wordt u aangeraden [resources te bekijken voor het migreren van toepassingen naar Azure AD](migration-resources.md).

SAML SSO met Application proxy werkt ook met de SAML-token Encryption-functie. Zie [Configure Azure AD SAML token Encryption](howto-saml-token-encryption.md)(Engelstalig) voor meer informatie.

De volgende protocol diagrammen beschrijven de volg orde van eenmalige aanmelding voor zowel een door een service provider geïnitieerde stroom als een door de provider geïnitieerde (door de IdP geïnitieerde) stroom. Toepassings proxy werkt met SAML SSO door de SAML-aanvraag en het antwoord van en naar de on-premises toepassing in de cache te plaatsen.

  ![SAML-stroom](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML-stroom](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Een toepassing maken en SAML SSO instellen

1. Selecteer in de Azure Portal **Azure Active Directory > bedrijfs toepassingen** en selecteer **nieuwe toepassing**.

2. Selecteer in de sectie **on-premises toepassingen** de optie **een on-premises toepassing toevoegen**.

3. Voer de weergave naam voor de nieuwe toepassing in en selecteer vervolgens **toevoegen**.

4. Op de **overzichts** pagina van de app selecteert u **eenmalige aanmelding**.

5. Selecteer **SAML** als de methode voor eenmalige aanmelding.

6. Stel eerst SAML SSO in om te werken in het bedrijfs netwerk. Ga in de pagina **eenmalige aanmelding met SAML instellen** naar de kop basis- **SAML-configuratie** en selecteer het **bewerkings** pictogram (een potlood). Volg de stappen in [Enter Basic SAML Configuration](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) voor het configureren van op SAML gebaseerde verificatie voor de toepassing.

7. Voeg ten minste één gebruiker toe aan de toepassing en controleer of het test account toegang heeft tot de toepassing. Gebruik tijdens de verbinding met het bedrijfs netwerk het test account om te zien of u eenmalige aanmelding bij de toepassing hebt. 

   > [!NOTE]
   > Nadat u de toepassings proxy hebt ingesteld, gaat u terug en werkt u de **URL**voor SAML-antwoorden bij.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>De on-premises toepassing publiceren met toepassings proxy

Voordat u SSO voor on-premises toepassingen kunt bieden, moet u toepassings proxy inschakelen en een connector installeren. Raadpleeg de zelf studie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy in azure AD](application-proxy-add-on-premises-application.md) voor meer informatie over het voorbereiden van uw on-premises omgeving, het installeren en registreren van een connector en het testen van de connector. Voer vervolgens de volgende stappen uit om uw nieuwe toepassing te publiceren met toepassings proxy. Raadpleeg de sectie [een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) in de zelf studie voor andere instellingen die hieronder niet worden genoemd.

1. Selecteer **toepassings proxy**terwijl de toepassing nog in de Azure Portal is geopend. Geef de **interne URL** voor de toepassing op. Als u een aangepast domein gebruikt, moet u ook het SSL-certificaat voor uw toepassing uploaden. 
   > [!NOTE]
   > Gebruik als best practice aangepaste domeinen wanneer dat mogelijk is voor een geoptimaliseerde gebruikers ervaring. Meer informatie over het [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md).

2. Selecteer **Azure Active Directory** als de **Pre-verificatie** methode voor uw toepassing.

3. Kopieer de **externe URL** voor de toepassing. U hebt deze URL nodig om de SAML-configuratie te volt ooien.

4. Probeer met behulp van het test account de toepassing te openen met de **externe URL** om te valideren dat de toepassings proxy juist is ingesteld. Zie problemen [met toepassings proxy en fout berichten oplossen](application-proxy-troubleshoot.md)als er problemen zijn.

## <a name="update-the-saml-configuration"></a>De SAML-configuratie bijwerken

1. Selecteer, terwijl de toepassing nog geopend is in de Azure Portal, **eenmalige aanmelding**. 

2. Ga in de pagina **eenmalige aanmelding met SAML instellen** naar de kop basis- **SAML-configuratie** en selecteer het **bewerkings** pictogram (een potlood). De **externe URL** die u in de toepassings proxy hebt geconfigureerd, vult automatisch de velden **id**, **antwoord-URL**en **afmeldings-URL** in. Bewerk deze Url's niet omdat deze vereist zijn voor een goede werking van de toepassings proxy.

3. Bewerk de **antwoord-URL** die u eerder hebt geconfigureerd zodat het domein bereikbaar is voor de toepassings proxy. Als uw **externe URL** bijvoorbeeld `https://contosotravel-f128.msappproxy.net` is en de oorspronkelijke antwoord- **URL** is `https://contosotravel.com/acs`, moet u de oorspronkelijke **antwoord-URL** bijwerken naar `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Elementaire SAML-configuratie gegevens invoeren](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Schakel het selectie vakje naast de bijgewerkte **antwoord-URL** in om deze als de standaard waarde te markeren.

   * Als de vereiste **antwoord-URL** al wordt vermeld, markeert u deze **antwoord-URL** als standaard en verwijdert u de eerder geconfigureerde **antwoord-URL**.

   * Voor een door SP geïnitieerde stroom moet u ervoor zorgen dat de back-end-toepassing de juiste **antwoord-URL** of de URL van de Assertion Consumer Service opgeeft voor het ontvangen van het verificatie token.

    > [!NOTE]
    > Als de back-endtoepassing verwacht dat de **antwoord-URL** de interne URL is, moet u [aangepaste domeinen](application-proxy-configure-custom-domain.md) gebruiken om overeenkomende interne en externe url's te hebben of de beveiligde aanmeldings extensie voor mijn apps op de apparaten van gebruikers installeren. Deze extensie wordt automatisch omgeleid naar de juiste toepassings proxy service. Zie voor het installeren van de uitbrei ding [mijn apps beveiligde aanmeldings extensie](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Uw app testen

Als u al deze stappen hebt voltooid, moet uw app te starten. De app testen:

1. Open een browser en navigeer naar de **externe URL** die u hebt gemaakt tijdens het publiceren van de app. 
1. Meld u aan met de testaccount dat u hebt toegewezen aan de app. U moet de toepassing kunnen laden en eenmalige aanmelding in de toepassing hebben.

## <a name="next-steps"></a>Volgende stappen

- [Hoe biedt Azure AD-toepassingsproxy eenmalige aanmelding?](application-proxy-single-sign-on.md)
- [Application Proxy oplossen](application-proxy-troubleshoot.md)
