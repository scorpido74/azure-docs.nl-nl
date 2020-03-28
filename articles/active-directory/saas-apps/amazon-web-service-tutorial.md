---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Amazon Web Services (AWS) | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40fd8217285643aa7d706d194d7f78ba0634dd32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048969"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Amazon Web Services (AWS)

In deze zelfstudie leert u hoe u Amazon Web Services (AWS) integreren met Azure Active Directory (Azure AD). Wanneer u Amazon Web Services (AWS) integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Amazon Web Services (AWS).
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Amazon Web Services (AWS) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

![Diagram met azure-ad- en AWS-relatie](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

U meerdere id's configureren voor meerdere exemplaren. Bijvoorbeeld:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Met deze waarden verwijdert Azure **#** AD de waarde `https://signin.aws.amazon.com/saml` van , en verzendt de juiste waarde als de doelgroep-URL in het SAML-token.

Wij raden deze aanpak aan om de volgende redenen:

- Elke applicatie biedt u een uniek X509-certificaat. Elk exemplaar van een AWS-app-exemplaar kan dan een andere vervaldatum van het certificaat hebben, die kan worden beheerd op basis van een individueel AWS-account. Algemene rollover van certificaten is in dit geval eenvoudiger.

- U gebruikersvoorzieningen inschakelen met een AWS-app in Azure AD en vervolgens haalt onze service alle rollen uit dat AWS-account. U hoeft de AWS-rollen in de app niet handmatig toe te voegen of bij te werken.

- U de app-eigenaar afzonderlijk toewijzen voor de app. Deze persoon kan de app rechtstreeks beheren in Azure AD.

> [!Note]
> Zorg ervoor dat u alleen een galerietoepassing gebruikt.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een AWS single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Amazon Web Services (AWS) ondersteunt door **SP en IDP** gestarte SSO
* Zodra u Amazon Web Services (AWS) hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) toevoegen uit de galerie

Om de integratie van Amazon Web Services (AWS) met Azure AD te configureren, moet u Amazon Web Services (AWS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werkaccount, schoolaccount of persoonlijk Microsoft-account.
1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
1. Kies **Enterprise Applications** > **All applications**in het overzichtsmenu van Azure Active Directory .
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Typ **Amazon Web Services (AWS)** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **Amazon Web Services (AWS)** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Azure AD-singlesign-on configureren en testen voor Amazon Web Services (AWS)

Azure AD SSO configureren en testen met Amazon Web Services (AWS) met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Amazon Web Services (AWS).

Als u Azure AD SSO wilt configureren en testen met Amazon Web Services (AWS), voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Amazon Web Services (AWS) SSO](#configure-amazon-web-services-aws-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Amazon Web Services (AWS) testgebruiker](#create-amazon-web-services-aws-test-user)** - om een tegenhanger van B.Simon in Amazon Web Services (AWS) te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
    1. **[Rolinrichting configureren in Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Amazon Web Services (AWS)** applicatie-integratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door **Opslaan te selecteren.**

1. Wanneer u meer dan één instantie configureert, geeft u een id-waarde op. Gebruik vanaf de tweede instantie de **#** volgende indeling, inclusief een teken, om een unieke SPN-waarde op te geven.

    `https://signin.aws.amazon.com/saml#2`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Amazon Web Services (AWS)** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
1. Kies Gebruikers**alle gebruikers**in het overzichtsmenu **van** > Azure Active Directory .
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Amazon Web Services (AWS).

1. Zoek en selecteer **Azure Active Directory**in de Azure-portal.
1. Kies **Enterprise Applications** > **All applications**in het overzichtsmenu van Azure Active Directory .
1. Selecteer Amazon Web **Services (AWS)** in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-amazon-web-services-aws-sso"></a>Amazon Web Services (AWS) SSO configureren

1. Meld je in een ander browservenster aan op uw AWS-bedrijfssite als beheerder.

2. Selecteer **AWS Home**.

    ![Schermafbeelding van de bedrijfssite van AWS, met het pictogram AWS-startpagina gemarkeerd][11]

3. Selecteer **Identiteits- en toegangsbeheer**.

    ![Schermafbeelding van de pagina AWS-services, met IAM gemarkeerd][12]

4. Selecteer **Identiteitsproviders** > **Provider maken**.

    ![Schermafbeelding van de IAM-pagina, met identiteitsproviders en provider maken gemarkeerd][13]

5. Voer **op** de pagina Provider configureren de volgende stappen uit:

    ![Schermafbeelding van Provider configureren][14]

    a. Selecteer **SAML**voor **providertype**.

    b. Typ **bij Providernaam**een providernaam *(bijvoorbeeld: WAAD*).

    c. Als u uw gedownloade **metagegevensbestand** wilt uploaden vanuit de Azure-portal, selecteert u **Bestand kiezen**.

    d. Selecteer **Volgende stap**.

6. Selecteer op de pagina **Providergegevens verifiëren** de optie **Maken**.

    ![Schermafbeelding van Providergegevens verifiëren, met Markeren gemarkeerd][15]

7. Selecteer **Rollen** > **Maken rol**.

    ![Schermafbeelding van de pagina Rollen][16]

8. Voer op de pagina **Create role** de volgende stappen uit:  

    ![Schermafbeelding van rolpagina maken][19]

    a. Selecteer **SAML 2.0-federatie**onder **Type vertrouwde entiteit selecteren**.

    b. Selecteer **onder Een SAML 2.0-provider kiezen**de **SAML-provider** die u eerder hebt gemaakt (bijvoorbeeld *WAAD).*

    c. Selecteer **Allow programmatic and AWS Management Console access**.
  
    d. Selecteer **Volgende: Machtigingen**.

9. Voeg in het dialoogvenster **Machtigingen koppelen** het juiste beleid per organisatie toe. Selecteer **vervolgens Volgende: Controleren**.  

    ![Schermafbeelding van het dialoogvenster Machtigingen bijvoegen][33]

10. Voer **in het** dialoogvenster Controleren de volgende stappen uit:

    ![Schermafbeelding van het dialoogvenster Controleren][34]

    a. Voer **in Rolnaam**uw rolnaam in.

    b. Voer **in Rolbeschrijving**de beschrijving in.

    c. Selecteer **Rol maken**.

    d. Maak zoveel rollen als nodig is en breng ze in kaart aan de identiteitsprovider.

11. Gebruik AWS-serviceaccountreferenties voor het ophalen van de rollen uit het AWS-account in Azure AD-gebruikersinrichting. Open hiervoor de startpagina van de AWS-console.

12. Selecteer **Services**. Selecteer **IAM** **onder Beveiliging, Identiteit & Compliance**.

    ![Schermafbeelding van het huis van de AWS-console, met Services en IAM gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecteer In de sectie IAM de optie **Beleid**.

    ![Schermafbeelding van de sectie IAM, met gemarkeerd beleid](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Maak een nieuw beleid door **beleid maken** te selecteren voor het ophalen van de rollen uit het AWS-account in Azure AD-gebruikersinrichting.

    ![Schermafbeelding van de rolpagina maken, met beleid maken gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Maak uw eigen beleid om alle rollen uit AWS-accounts te halen.

    ![Schermafbeelding van de beleidspagina maken, met JSON gemarkeerd](./media/amazon-web-service-tutorial/policy1.png)

    a. Selecteer **in Beleid maken**het tabblad **JSON.**

    b. Voeg in het beleidsdocument de volgende JSON toe:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Selecteer **Beleid controleren** om het beleid te valideren.

    ![Schermafbeelding van de beleidspagina maken](./media/amazon-web-service-tutorial/policy5.png)

16. Definieer het nieuwe beleid.

    ![Schermafbeelding van de beleidspagina maken, met de velden Naam en beschrijving gemarkeerd](./media/amazon-web-service-tutorial/policy2.png)

    a. Voer voor **Naam** **AzureAD_SSOUserRole_Policy**in .

    b. Voer voor **Beschrijving** **Dit beleid in om de rollen uit AWS-accounts op te halen**.

    c. Selecteer **Beleid maken**.

17. Maak een nieuw gebruikersaccount in de AWS IAM-service.

    a. Selecteer **Gebruikers**in de AWS IAM-console .

    ![Schermafbeelding van de AWS IAM-console, met gebruikers gemarkeerd](./media/amazon-web-service-tutorial/policy3.png)

    b. Als u een nieuwe gebruiker wilt maken, selecteert u **Gebruiker toevoegen**.

    ![Schermafbeelding van de knop Gebruiker toevoegen](./media/amazon-web-service-tutorial/policy4.png)

    c. Ga als gebruiker **naar de** volgende

    ![Schermafbeelding van Gebruikerspagina toevoegen, met gebruikersnaam en toegangstype gemarkeerd](./media/amazon-web-service-tutorial/adduser1.png)

    * Voer de naam van de gebruiker in als **AzureADRoleManager**.

    * Selecteer **Programmatische toegang**voor het toegangstype . Op deze manier kan de gebruiker de API's aanroepen en de rollen ophalen uit het AWS-account.

    * Selecteer **Volgende machtigingen**.

18. Maak een nieuw beleid voor deze gebruiker.

    ![Schermafbeelding van Gebruiker toevoegen](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selecteer **Bestaand beleid koppelen rechtstreeks**.

    b. Zoek het zojuist gemaakte beleid op in de filtersectie **AzureAD_SSOUserRole_Policy**.

    c. Selecteer het beleid en selecteer **Volgende: Controleren**.

19. Bekijk het beleid voor de bijgevoegde gebruiker.

    ![Schermafbeelding van Gebruikerspagina toevoegen, met Gebruiker maken gemarkeerd](./media/amazon-web-service-tutorial/adduser3.png)

    a. Controleer de gebruikersnaam, het toegangstype en het beleid dat is toegewezen aan de gebruiker.

    b. Selecteer **Gebruiker maken**.

20. Download de gebruikersreferenties van een gebruiker.

    ![Schermafbeelding van Gebruiker toevoegen](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopieer de **Access key ID** en **Secret access key** van de gebruiker.

    b. Voer deze referenties in de sectie Azure AD-gebruikersinrichting in om de rollen uit de AWS-console op te halen.

    c. Selecteer **Sluiten**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Rolinrichting configureren in Amazon Web Services (AWS)

1. Ga in de Azure AD-beheerportal in de AWS-app naar **Provisioning**.

    ![Schermafbeelding van de AWS-app, waarbij provisioning is gemarkeerd](./media/amazon-web-service-tutorial/provisioning.png)

2. Voer respectievelijk de toegangssleutel en het geheim in de velden **clientsecret** en **Secret Token** in.

    ![Schermafbeelding van het dialoogvenster Beheerdersreferenties](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Voer de gebruikerstoegangssleutel van AWS in het veld **Clientgeheim** in.

    b. Voer het gebruikersgeheim van AWS in het veld **Token voor geheim** in.

    c. Selecteer **Testverbinding**.

    d. Sla de instelling op door **Opslaan te selecteren.**

3. Selecteer in de sectie **Instellingen** voor **Inrichtenstatus**de optie **Aan**. Selecteer vervolgens **Opslaan**.

    ![Schermafbeelding van de sectie Instellingen, met Aan gemarkeerd](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> De inrichtingsservice importeert alleen rollen van AWS naar Azure AD. De service biedt geen gebruikers en groepen van Azure AD tot AWS.

> [!NOTE]
> Nadat u de inloggegevens hebt opgeslagen, moet u wachten tot de eerste synchronisatiecyclus is uitgevoerd. Synchronisatie duurt meestal ongeveer 40 minuten om te voltooien. U de status onder aan de pagina **Inrichten** onder **Huidige status**bekijken.

### <a name="create-amazon-web-services-aws-test-user"></a>Testgebruiker voor Amazon Web Services (AWS) maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in Amazon Web Services (AWS). Amazon Web Services (AWS) vereist niet dat een gebruiker is aangemaakt in hun systeem voor eenmalige aanmelding, dus u hoeft hier niets te doen.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Amazon Web Services (AWS) klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Amazon Web Services (AWS) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="known-issues"></a>Bekende problemen

 * In de sectie **Inrichten** wordt in de sectie **Toewijzingen** een "Laden..." en geeft nooit de kenmerktoewijzingen weer. De enige inrichtingswerkstroom die vandaag wordt ondersteund, is het importeren van rollen van AWS in Azure AD voor selectie tijdens een gebruikers- of groepstoewijzing. De kenmerktoewijzingen hiervoor zijn vooraf bepaald en kunnen niet worden geconfigureerd.

 * De sectie **Inrichten** ondersteunt alleen de invoer van één set referenties per AWS-tenant tegelijk. Alle geïmporteerde rollen worden `appRoles` geschreven naar de eigenschap van het Azure AD-object [ `servicePrincipal` ](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) voor de AWS-tenant.

   Meerdere AWS-tenants (vertegenwoordigd door `servicePrincipals`) kunnen vanuit de galerie aan Azure AD worden toegevoegd voor inrichten. Er is echter een bekend probleem, waarbij niet alle geïmporteerde rollen van de `servicePrincipals` meerdere AWS die `servicePrincipal` worden gebruikt voor het inrichten in de single die voor SSO wordt gebruikt, automatisch kunnen worden geschreven.

   Als tijdelijke oplossing u de [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) API `appRoles` gebruiken om `servicePrincipal` alle geïmporteerde in elke AWS te extraheren waar de inrichting is geconfigureerd. Vervolgens u deze roltekenreeksen toevoegen aan de AWS `servicePrincipal` waar SSO is geconfigureerd.

* Rollen moeten voldoen aan de volgende vereisten om in aanmerking te komen voor invoer van AWS in Azure AD:

  * Rollen moeten precies één saml-provider hebben gedefinieerd in AWS

  * De gecombineerde duur van de rol ARN en de saml-provider ARN voor een ingevoerde rol moet 119 tekens of minder zijn

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Amazon Web Services (AWS) met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Amazon Web Services (AWS) beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-aws)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
