---
title: Azure Active Directory-integratie voor Azure Red Hat OpenShift
description: Meer informatie over het maken van een Azure AD-beveiligingsgroep en -gebruiker voor het testen van apps in uw Microsoft Azure Red Hat OpenShift-cluster.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: a2eade6c5a9c826d28d435a09861ba58463ae8c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280532"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory-integratie voor Azure Red Hat OpenShift

Als u nog geen Azure Active Directory-tenant (Azure AD) hebt gemaakt, volgt u de aanwijzingen in [Een Azure AD-tenant maken voor Azure Red Hat OpenShift](howto-create-tenant.md) voordat u deze instructies vervolgt.

Microsoft Azure Red Hat OpenShift heeft machtigingen nodig om taken uit te voeren namens uw cluster. Als uw organisatie nog geen Azure AD-gebruiker, Azure AD-beveiligingsgroep of een Azure AD-app-registratie heeft om als serviceprincipal te gebruiken, volgt u deze instructies om ze te maken.

## <a name="create-a-new-azure-active-directory-user"></a>Een nieuwe Azure Active Directory-gebruiker maken

Controleer in de [Azure-portal](https://portal.azure.com)of uw tenant rechtsboven in de portal onder uw gebruikersnaam wordt weergegeven:

![Schermafbeelding van portal met tenant](./media/howto-create-tenant/tenant-callout.png) rechtsboven Als de verkeerde tenant wordt weergegeven, klikt u rechtsboven op uw gebruikersnaam, klikt u op **Switch Directory**en selecteert u de juiste tenant in de lijst **Alle mappen.**

Maak een nieuwe Azure Active Directory globale beheerder gebruiker aan te melden bij uw Azure Red Hat OpenShift cluster.

1. Ga naar het gebruikersblad [van gebruikers.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)
2. Klik **op +Nieuwe gebruiker** om het deelvenster Gebruiker **te** openen.
3. Voer een **naam** in voor deze gebruiker.
4. Maak een **gebruikersnaam** op basis van de naam `.onmicrosoft.com` van de tenant die u hebt gemaakt, met aan het einde toegevoegd. Bijvoorbeeld `yourUserName@yourTenantName.onmicrosoft.com`. Noteer deze gebruikersnaam. Je hebt het nodig om je aan te melden bij je cluster.
5. Klik op **Maprol** om het maprolvenster te openen en selecteer **Globale beheerder** en klik vervolgens op **Ok** onder aan het deelvenster.
6. Klik **in** het deelvenster Gebruiker op **Wachtwoord weergeven** en neem het tijdelijke wachtwoord op. Nadat je je de eerste keer hebt aangemeld, wordt je gevraagd deze opnieuw in te stellen.
7. Klik onder aan het deelvenster op **Maken** om de gebruiker te maken.

## <a name="create-an-azure-ad-security-group"></a>Een Azure AD-beveiligingsgroep maken

Als u toegang voor clusterbeheerders wilt verlenen, worden de lidmaatschappen in een Azure AD-beveiligingsgroep gesynchroniseerd met de OpenShift-groep 'osa-klant-beheerders'. Als dit niet is opgegeven, wordt er geen toegang tot clusterbeheerders verleend.

1. Open het blade [van Azure Active Directory-groepen.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)
2. Klik **op +Nieuwe groep**.
3. Geef een groepsnaam en beschrijving op.
4. **Groepstype** instellen op **Beveiliging**.
5. **Stel het lidmaatschapstype** **in op Toegewezen**.

    Voeg de Azure AD-gebruiker die u in de eerdere stap hebt gemaakt toe aan deze beveiligingsgroep.

6. Klik **op Leden** om het deelvenster Leden **selecteren** te openen.
7. Selecteer in de lijst met leden de Azure AD-gebruiker die u hierboven hebt gemaakt.
8. Klik onder aan de portal op **Selecteren** en maak vervolgens **de** beveiligingsgroep om de beveiligingsgroep te maken.

    Noteer de groeps-id-waarde.

9. Wanneer de groep wordt gemaakt, ziet u deze in de lijst met alle groepen. Klik op de nieuwe groep.
10. Kopieer op de pagina die wordt weergegeven de **object-id**. We verwijzen naar deze `GROUPID` waarde als in de [zelfstudie van Een Azure Red Hat OpenShift](tutorial-create-cluster.md) maken.

> [!IMPORTANT]
> Als u deze groep wilt synchroniseren met de OpenShift-groep osa-klant-klant-klant, maakt u het cluster met behulp van de Azure CLI. De Azure-portal heeft momenteel geen veld om deze groep in te stellen.

## <a name="create-an-azure-ad-app-registration"></a>Een Azure AD-app-registratie maken

U automatisch een Azure Active Directory-app-registratieclient (Azure AD) maken `--aad-client-app-id` als onderdeel `az openshift create` van het maken van het cluster door de vlag weg te laten naar de opdracht. In deze zelfstudie ziet u hoe u de Azure AD-appregistratie maakt voor volledigheid.

Als uw organisatie nog geen Azure Active Directory-app-appregistratie (Azure AD) heeft om als serviceprincipal te gebruiken, volgt u deze instructies om er een te maken.

1. Open het [mes app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) en klik op **+Nieuwe registratie**.
2. Voer **in het deelvenster Een aanvraag registreren** een naam in voor uw aanvraagregistratie.
3. Controleer of onder **Ondersteunde accounttypen** **alleen Accounts in deze organisatiemap** is geselecteerd. Dit is de meest veilige keuze.
4. We zullen later een omleiding URI toevoegen zodra we de URI van het cluster kennen. Klik **op** de knop Registreren om de Azure AD-toepassingsregistratie te maken.
5. Kopieer op de pagina die wordt weergegeven de **id van toepassing (client).** We verwijzen naar deze `APPID` waarde als in de [zelfstudie van Een Azure Red Hat OpenShift](tutorial-create-cluster.md) maken.

![Schermafbeelding van de pagina met app-objecten](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Een klantgeheim maken

Genereer een clientgeheim voor het verifiëren van uw app naar Azure Active Directory.

1. Klik **in** het gedeelte Beheren van de pagina app-registraties op **Certificaten & geheimen**.
2. Klik in het deelvenster **Certificaten & geheimen** op **+Nieuw clientgeheim**.  Het geheime deelvenster **Een client** toevoegen wordt weergegeven.
3. Geef een **beschrijving**.
4. **Stel Verloopt in op** de duur die u verkiest, bijvoorbeeld in 2 **jaar.**
5. Klik **op Toevoegen** en de sleutelwaarde wordt weergegeven in het gedeelte **Klantgeheimen** van de pagina.
6. Kopieer de sleutelwaarde. We verwijzen naar deze `SECRET` waarde als in de [zelfstudie van Een Azure Red Hat OpenShift](tutorial-create-cluster.md) maken.

![Schermafbeelding van het deelvenster certificaten en geheimen](./media/howto-create-tenant/create-key.png)

Zie [Hoofdobjecten voor toepassingen en service in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)voor meer informatie over Azure-toepassingsobjecten.

Zie [Een app registreren met het Azure Active Directory v1.0-eindpunt voor](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)meer informatie over het maken van een nieuwe Azure AD-toepassing.

## <a name="add-api-permissions"></a>API-machtigingen toevoegen

[//]: # (Niet wijzigen in Microsoft Graph. Het werkt niet met Microsoft Graph.)
1. Klik in de sectie **Beheren** op **API-machtigingen**
2. Klik **op Machtigingen toevoegen** en selecteer Azure Active Directory **Graph** en **vervolgens gedelegeerde machtigingen**.
> [!NOTE]
> Zorg ervoor dat u de tegel 'Azure Active Directory Graph' hebt geselecteerd en niet de tegel 'Microsoft Graph'.

3. Vouw **gebruiker** uit in de onderstaande lijst en schakel de toestemming **voor Gebruiker.Lezen in.** Als **User.Read** standaard is ingeschakeld, controleert u of dit de **machtiging User.Read**van Azure Active **Directory Graph** is.
4. Schuif omhoog en selecteer **Toepassingsmachtigingen**.
5. Vouw **directory** uit in de onderstaande lijst en schakel **Directory.ReadAll in**.
6. Klik **op Machtigingen toevoegen** om de wijzigingen te accepteren.
7. In het deelvenster API-machtigingen moet nu zowel *User.Read* als *Directory.ReadAll*worden weergegeven. Let op: de waarschuwing in **de kolom Die** toestemming voor beheerders vereist is naast *Directory.ReadAll*.
8. Als u de *Azure-abonnementsbeheerder bent,* klikt u hieronder op **Toestemming verlenen van beheerders voor *abonnementsnaam.* ** Als u niet de *Azure Subscription Administrator bent,* vraagt u de toestemming aan bij uw beheerder.

![Schermafbeelding van het deelvenster API-machtigingen. User.Read en Directory.ReadAlle machtigingen toegevoegd, toestemming van de beheerder vereist voor Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Synchronisatie van de clusterbeheerdersgroep werkt pas nadat toestemming is verleend. U ziet een groene cirkel met een vinkje en een bericht 'Verleend voor *abonnementsnaam'* in de *kolom Vereiste* beheerderstoestemming.

Zie [Azure-abonnementsbeheerders toevoegen of wijzigen](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)voor meer informatie over beheerders van beheerders en andere rollen.

## <a name="resources"></a>Resources

* [Hoofdobjecten voor toepassingen en services in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Snelstart: Een app registreren bij het Azure Active Directory v1.0-eindpunt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Volgende stappen

Als u aan alle [Azure Red Hat OpenShift-vereisten](howto-setup-environment.md)hebt voldaan, bent u klaar om uw eerste cluster te maken!

Probeer de zelfstudie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)
