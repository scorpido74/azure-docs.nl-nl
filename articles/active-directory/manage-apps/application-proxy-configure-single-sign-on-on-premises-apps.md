---
title: SAML-aanmelding voor on-premises apps met Azure AD App Proxy
description: Meer informatie over het bieden van eenmalige aanmelding voor on-premises toepassingen die zijn beveiligd met SAML-verificatie. Bied externe toegang tot on-premises apps met Application Proxy.
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
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481344"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML-aanmelding voor on-premises toepassingen met application proxy

U eenmalige aanmelding (SSO) bieden aan on-premises toepassingen die zijn beveiligd met SAML-verificatie en externe toegang tot deze toepassingen bieden via Application Proxy. Met SAML single sign-on verifieert Azure Active Directory (Azure AD) de toepassing met behulp van het Azure AD-account van de gebruiker. Azure AD communiceert de aanmeldingsgegevens naar de toepassing via een verbindingsprotocol. U gebruikers ook toewijzen aan specifieke toepassingsrollen op basis van regels die u definieert in uw SAML-claims. Door Application Proxy in te schakelen naast SAML SSO, hebben uw gebruikers externe toegang tot de applicatie en een naadloze SSO-ervaring.

De toepassingen moeten SAML-tokens kunnen gebruiken die zijn uitgegeven door **Azure Active Directory.** Deze configuratie is niet van toepassing op toepassingen die een on-premises identiteitsprovider gebruiken. Voor deze scenario's raden we u aan resources te controleren [voor het migreren van toepassingen naar Azure AD.](migration-resources.md)

SAML SSO met Application Proxy werkt ook met de SAML-tokenversleutelingsfunctie. Zie [Azure AD SAML-tokenversleuteling configureren](howto-saml-token-encryption.md)voor meer informatie .

De onderstaande protocoldiagrammen beschrijven de single sign-on sequentie voor zowel een door de serviceprovider geïnitieerde (SP-geïnitieerde) stroom als een door idp geïnitieerde stroom van identiteitsprovider. Application Proxy werkt met SAML SSO door de SAML-aanvraag en het antwoord op en vanuit de on-premises toepassing in te dienen.

  ![SAML SP-stroom](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP-stroom](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Een toepassing maken en SAML SSO instellen

1. Selecteer azure active **directory > Enterprise-toepassingen** in de Azure-portal en selecteer **Nieuwe toepassing**.

2. Voer de weergavenaam in voor uw nieuwe toepassing, selecteer **Elke andere toepassing integreren die u niet in de galerie vindt**en selecteer Vervolgens **Maken**.

3. Selecteer op de **pagina Overzicht** van de app de optie Eén **aanmelding**.

4. Selecteer **SAML** als de methode met één aanmelding.

5. Stel eerst SAML SSO in om te werken terwijl het op het bedrijfsnetwerk werkt. Ga op de pagina **Eén aanmelding instellen met SAML** naar de kop **BasisSAML-configuratie** en selecteer het pictogram **Bewerken** (een potlood). Volg de stappen in [De basisSAML-configuratie invoeren](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) om SAML-verificatie voor de toepassing te configureren.

6. Voeg ten minste één gebruiker toe aan de toepassing en zorg ervoor dat het testaccount toegang heeft tot de toepassing. Als u bent verbonden met het bedrijfsnetwerk, gebruikt u het testaccount om te zien of u zich eenmalig hebt aangemeld bij de toepassing. 

   > [!NOTE]
   > Nadat u Application Proxy hebt ingesteld, komt u terug en werkt u de **URL van saml-antwoord bij.**

## <a name="publish-the-on-premises-application-with-application-proxy"></a>De on-premises toepassing publiceren met Application Proxy

Voordat u SSO leveren voor on-premises toepassingen, moet u Application Proxy inschakelen en een connector installeren. Zie de zelfstudie [Voeg een on-premises toepassing toe voor externe toegang via Application Proxy in Azure AD](application-proxy-add-on-premises-application.md) voor meer informatie over het voorbereiden van uw on-premises omgeving, het installeren en registreren van een connector en het testen van de connector. Volg vervolgens deze stappen om uw nieuwe toepassing te publiceren met Application Proxy. Raadpleeg de sectie [Een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) in de zelfstudie voor andere instellingen die hieronder niet worden vermeld.

1. Als de toepassing nog steeds is geopend in de Azure-portal, selecteert u **Toepassingsproxy**. Geef de **interne URL** voor de toepassing op. Als u een aangepast domein gebruikt, moet u ook het TLS/SSL-certificaat voor uw toepassing uploaden. 
   > [!NOTE]
   > Gebruik als best practice aangepaste domeinen waar mogelijk voor een geoptimaliseerde gebruikerservaring. Meer informatie over [Werken met aangepaste domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

2. Selecteer **Azure Active Directory** als de methode **Voorverificatie** voor uw toepassing.

3. Kopieer de **externe URL** voor de toepassing. U hebt deze URL nodig om de SAML-configuratie te voltooien.

4. Probeer met het testaccount de toepassing te openen met de **externe URL** om te valideren dat toepassingsproxy correct is ingesteld. Zie Problemen met [toepassingsproxy oplossen en foutberichten](application-proxy-troubleshoot.md)oplossen als er problemen zijn.

## <a name="update-the-saml-configuration"></a>De SAML-configuratie bijwerken

1. Als de toepassing nog steeds is geopend in de Azure-portal, selecteert u **Eenmalige aanmelding**. 

2. Ga op de pagina **Eén aanmelding instellen met SAML** naar de kop **BasisSAML-configuratie** en selecteer het pictogram **Bewerken** (een potlood). Controleer of de **externe URL** die u hebt geconfigureerd in toepassingsproxy wordt ingevuld in de velden **Id,** **URL voor beantwoorden**en **Afmelden.** Deze URL's zijn vereist om application proxy correct te laten werken. 

3. Bewerk de eerder geconfigureerde **URL van het antwoord,** zodat het domein bereikbaar is via Application Proxy. Als uw **externe URL** `https://contosotravel-f128.msappproxy.net` dat is en `https://contosotravel.com/acs`de oorspronkelijke URL van het **antwoord** `https://contosotravel-f128.msappproxy.net/acs`is, moet u bijvoorbeeld de oorspronkelijke URL van **antwoord** bijwerken naar . 

    ![BasisSAML-configuratiegegevens invoeren](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Schakel het selectievakje naast de bijgewerkte **URL van antwoord** in om deze als standaard te markeren.

   * Als de vereiste **url van antwoord** al wordt vermeld, markeert u deze URL van **antwoord** als standaard en verwijdert u de eerder geconfigureerde URL **van antwoord**.

   * Controleer voor een door SP gestarte stroom of de back-endtoepassing de juiste **URL van de antwoord-URL** of de URL van de Bevestigingsconsumentenservice opgeeft voor het ontvangen van het verificatietoken.

    > [!NOTE]
    > Als de back-end-toepassing verwacht dat de **URL voor het beantwoorden** de interne URL is, moet u aangepaste [domeinen](application-proxy-configure-custom-domain.md) gebruiken om overeenkomende interne en externe URL's te hebben of de aanmeldingsextensie Mijn apps veilig installeren op de apparaten van gebruikers. Deze extensie wordt automatisch doorgestuurd naar de juiste Application Proxy Service. Zie De beveiligde [aanmeldingsextensie Mijn apps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)beveiligen als u de extensie wilt installeren.
    
## <a name="test-your-app"></a>Uw app testen

Wanneer u al deze stappen hebt voltooid, moet uw app operationeel zijn. Ga als nodig de app testen:

1. Open een browser en navigeer naar de **externe URL** die u hebt gemaakt toen u de app publiceerde. 
1. Meld u aan met het testaccount dat u aan de app hebt toegewezen. U moet in staat zijn om de toepassing te laden en Hebben SSO in de toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Hoe biedt Azure AD Application Proxy eenmalige aanmelding?](application-proxy-single-sign-on.md)
- [Problemen met toepassingsproxy oplossen](application-proxy-troubleshoot.md)
