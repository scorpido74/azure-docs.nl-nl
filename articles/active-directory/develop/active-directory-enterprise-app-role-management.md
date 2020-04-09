---
title: Rolclaim configureren voor zakelijke Azure AD-apps | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van de rolclaim die is uitgegeven in het SAML-token voor bedrijfstoepassingen in Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8db27819b7eef6cdf05ea3f6645ae930ebc4ef58
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884746"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>How to: De rolclaim configureren die is uitgegeven in het SAML-token voor bedrijfstoepassingen

Door Azure Active Directory (Azure AD) te gebruiken, u het claimtype aanpassen voor de rolclaim in het antwoordtoken dat u ontvangt nadat u een app hebt geautoriseerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure AD-abonnement met directory-instelling.
- Een abonnement met eenmalige aanmelding (SSO) ingeschakeld. U moet SSO configureren met uw toepassing.

## <a name="when-to-use-this-feature"></a>Wanneer deze functie te gebruiken

Als uw toepassing verwacht dat aangepaste rollen worden doorgegeven in een SAML-antwoord, moet u deze functie gebruiken. U zoveel rollen maken als nodig is om van Azure AD naar uw toepassing te worden doorgegeven.

## <a name="create-roles-for-an-application"></a>Rollen voor een toepassing maken

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster het Azure **Active Directory-pictogram.**

    ![Pictogram Azure Active Directory][1]

2. Selecteer **Enterprise-toepassingen**. Selecteer vervolgens **Alle toepassingen**.

    ![Deelvenster Ondernemingstoepassingen][2]

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![Knop 'Nieuwe toepassing'][3]

4. Typ in het zoekvak de naam van uw toepassing en selecteer de toepassing in het resultaatpaneel. Selecteer de knop **Toevoegen** om de toepassing toe te voegen.

    ![Toepassing in de resultatenlijst](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Nadat de toepassing is toegevoegd, gaat u naar de pagina **Eigenschappen** en kopieert u de object-id.

    ![Pagina Eigenschappen](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster en neem de volgende stappen:

    a. Meld u aan bij de Site Graph Explorer met behulp van de algemene beheerders- of medebeheerdersreferenties voor uw tenant.

    b. U hebt voldoende machtigingen nodig om de rollen te maken. Selecteer **machtigingen wijzigen** om de machtigingen te krijgen.

      ![De knop Machtigingen wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecteer de volgende machtigingen in de lijst (als u deze nog niet hebt) en selecteer **Machtigingen wijzigen.**

      ![Lijst met machtigingen en knop Machtigingen wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > De rol Voor beheerder en app-beheerder van cloudapps werkt in dit scenario niet omdat we de machtigingen voor globale beheerders nodig hebben voor directory lezen en schrijven.

    d. Accepteer de toestemming. Je bent weer ingelogd op het systeem.

    e. Wijzig de versie in **bèta**en haal de lijst met serviceprincipals uit uw tenant met de volgende query:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Als u meerdere mappen gebruikt, volgt u dit patroon:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Dialoogvenster Grafiekverkenner, met de query voor het ophalen van serviceprincipals](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > We zijn al bezig met het upgraden van de API's, zodat klanten mogelijk enige verstoring in de service zien.

    f. In de lijst met opgehaalde serviceprincipals krijgt u degene die u moet wijzigen. U Ctrl+F ook gebruiken om de toepassing te doorzoeken op alle vermelde serviceprincipals. Zoek naar de object-id die u hebt gekopieerd vanaf de pagina **Eigenschappen** en gebruik de volgende query om naar de serviceprincipal te gaan:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Query voor het verkrijgen van de serviceprincipal die u moet wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Haal de eigenschap **appRoles** uit het hoofdobject van de service.

      ![Details van de eigenschap appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Als u de aangepaste app gebruikt (niet de Azure Marketplace-app), ziet u twee standaardrollen: gebruiker en msiam_access. Voor de Marketplace-app is msiam_access de enige standaardrol. U hoeft geen wijzigingen aan te brengen in de standaardrollen.

    h. Genereer nieuwe rollen voor uw toepassing.

      De volgende JSON is een voorbeeld van het object **appRoles.** Maak een vergelijkbaar object om de gewenste rollen voor uw toepassing toe te voegen.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > U alleen nieuwe rollen toevoegen na msiam_access voor de patchbewerking. U ook zoveel rollen toevoegen als uw organisatie nodig heeft. Azure AD verzendt de waarde van deze rollen als claimwaarde in het SAML-antwoord. Als u de GUID-waarden voor de ID van nieuwe rollen wilt genereren, gebruikt u de webtools als [deze](https://www.guidgenerator.com/)

    i. Ga terug naar Graph Explorer en verander de methode van **GET** naar **PATCH**. Patch het hoofdobject van de service om de gewenste rollen te hebben door de eigenschap appRoles bij te **werken,** zoals in het voorgaande voorbeeld. Selecteer **Query uitvoeren** om de patchbewerking uit te voeren. Een succesbericht bevestigt de creatie van de rol.

      ![Patchbewerking met succesbericht](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Nadat de serviceprincipal is gepatcht met meer rollen, u gebruikers toewijzen aan de desbetreffende rollen. U de gebruikers toewijzen door naar portal te gaan en naar de toepassing te bladeren. Selecteer het tabblad **Gebruikers en groepen.** Op dit tabblad worden alle gebruikers en groepen weergegeven die al aan de app zijn toegewezen. U nieuwe gebruikers toevoegen aan de nieuwe rollen. U ook een bestaande gebruiker selecteren en **Bewerken** selecteren om de rol te wijzigen.

    ![Tabblad 'Gebruikers en groepen'](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Als u de rol aan een gebruiker wilt toewijzen, selecteert u de nieuwe rol en selecteert u de knop **Toewijzen** onder aan de pagina.

    ![Deelvenster Toewijzing bewerken en deelvenster Rol selecteren](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > U moet uw sessie vernieuwen in de Azure-portal om nieuwe rollen te zien.

8. Werk de tabel **Kenmerken** bij om een aangepaste toewijzing van de rolclaim te definiëren.

9. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Kenmerknaam | Kenmerkwaarde |
    | -------------- | ----------------|
    | Rolnaam  | user.assignedroles |

    >[!NOTE]
    >Als de waarde van de rolclaim niet ig is, wordt deze waarde niet verzonden in het token en is dit standaard per ontwerp.

    a. Klik **op Pictogram Bewerken** om **gebruikerskenmerken & claims** te openen.

      ![Knop 'Attribuut toevoegen'](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Voeg in het dialoogvenster **Gebruikersclaims beheren** het SAML-tokenkenmerk toe door op **Nieuwe claim toevoegen**te klikken.

      ![Knop 'Attribuut toevoegen'](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Deelvenster Kenmerk toevoegen](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Typ de naam van het kenmerk indien nodig in het vak **Naam.** In dit voorbeeld wordt **rolnaam** als claimnaam gebruikt.

    d. Laat het vak **Naamruimte** leeg.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Selecteer **Opslaan**.

10. Als u uw toepassing wilt testen in één aanmelding die is gestart door een identiteitsprovider, meldt u zich aan bij het [toegangspaneel](https://myapps.microsoft.com) en selecteert u de toepassingstegel. In het SAML-token ziet u alle toegewezen rollen voor de gebruiker met de claimnaam die u hebt opgegeven.

## <a name="update-an-existing-role"></a>Een bestaande rol bijwerken

Voer de volgende stappen uit om een bestaande rol bij te werken:

1. [Open Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Meld u aan bij de Site Graph Explorer met behulp van de algemene beheerders- of medebeheerdersreferenties voor uw tenant.

3. Wijzig de versie in **bèta**en haal de lijst met serviceprincipals uit uw tenant met de volgende query:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Als u meerdere mappen gebruikt, volgt u dit patroon:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialoogvenster Grafiekverkenner, met de query voor het ophalen van serviceprincipals](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. In de lijst met opgehaalde serviceprincipals krijgt u degene die u moet wijzigen. U Ctrl+F ook gebruiken om de toepassing te doorzoeken op alle vermelde serviceprincipals. Zoek naar de object-id die u hebt gekopieerd vanaf de pagina **Eigenschappen** en gebruik de volgende query om naar de serviceprincipal te gaan:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query voor het verkrijgen van de serviceprincipal die u moet wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Haal de eigenschap **appRoles** uit het hoofdobject van de service.

    ![Details van de eigenschap appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Als u de bestaande rol wilt bijwerken, gebruikt u de volgende stappen.

    ![Hoofdtekst aanvragen voor 'PATCH', met 'beschrijving' en 'weergavenaam' gemarkeerd](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Wijzig de methode van **GET** naar **PATCH**.

    b. Kopieer de bestaande rollen en plak ze onder **Aanvraagtekst**.

    c. Werk de waarde van een rol bij door de rolbeschrijving, rolwaarde of rolweergavenaam naar behoefte bij te werken.

    d. Nadat u alle vereiste rollen hebt bijgewerkt, selecteert u **Query uitvoeren**.

## <a name="delete-an-existing-role"></a>Een bestaande rol verwijderen

Voer de volgende stappen uit om een bestaande rol te verwijderen:

1. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster.

2. Meld u aan bij de Site Graph Explorer met behulp van de algemene beheerders- of medebeheerdersreferenties voor uw tenant.

3. Wijzig de versie in **bèta**en haal de lijst met serviceprincipals uit uw tenant met de volgende query:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Als u meerdere mappen gebruikt, volgt u dit patroon:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialoogvenster Grafiekverkenner, met de query voor het ophalen van de lijst met serviceprincipals](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. In de lijst met opgehaalde serviceprincipals krijgt u degene die u moet wijzigen. U Ctrl+F ook gebruiken om de toepassing te doorzoeken op alle vermelde serviceprincipals. Zoek naar de object-id die u hebt gekopieerd vanaf de pagina **Eigenschappen** en gebruik de volgende query om naar de serviceprincipal te gaan:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Query voor het verkrijgen van de serviceprincipal die u moet wijzigen](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Haal de eigenschap **appRoles** uit het hoofdobject van de service.

    ![Details van de eigenschap appRoles van het hoofdobject van de service](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Als u de bestaande rol wilt verwijderen, gebruikt u de volgende stappen.

    ![Instantie aanvragen voor 'PATCH', waarbij IsEnabled is ingesteld op false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Wijzig de methode van **GET** naar **PATCH**.

    b. Kopieer de bestaande rollen uit de toepassing en plak ze onder **Aanvraagtekst**.

    c. Stel de waarde **IsEnabled** in op **false** voor de rol die u wilt verwijderen.

    d. Selecteer **Query uitvoeren**.

    > [!NOTE]
    > Zorg ervoor dat u de msiam_access rol hebt en dat de ID overeenkomt in de gegenereerde rol.

7. Nadat de rol is uitgeschakeld, verwijdert u dat rolblok uit de sectie **appRollen.** Houd de methode als **PATCH**en selecteer **Query uitvoeren**.

8. Nadat u de query hebt uitgevoerd, wordt de rol verwijderd.

    > [!NOTE]
    > De rol moet worden uitgeschakeld voordat deze kan worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie de [app-documentatie voor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)meer stappen .

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
