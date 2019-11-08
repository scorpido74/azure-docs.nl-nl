---
title: Lead beheer voor Dynamics 365 voor klant betrokkenheid | Azure Marketplace
description: Beheer van leads voor Dynamics 365 voor klant betrokkenheid configureren.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 37cf613b6e0bd2ec9910dd3e7431c0feaa02431c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812308"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Beheer van leads voor Dynamics 365 voor klant betrokkenheid configureren

In dit artikel wordt beschreven hoe u Dynamics 365 kunt instellen voor klant betrokkenheid (voorheen Dynamics CRM Online), Lees [hier](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) meer over de wijziging om verkoop leads van uw Marketplace-aanbieding te verwerken. 

>[!Note]
>Deze instructies zijn specifiek voor de micro soft Hosted Cloud Dynamics 365 voor de klant engagement-omgeving. Het is momenteel niet mogelijk om rechtstreeks verbinding te maken met een on-premises Dynamics-omgeving. er zijn ook andere opties voor het ontvangen van leads, zoals het configureren van een [https-eind punt](./commercial-marketplace-lead-management-instructions-https.md) of een [Azure-tabel](./commercial-marketplace-lead-management-instructions-azure-table.md) om leads te ontvangen.

## <a name="prerequisites"></a>Vereisten

De volgende gebruikers machtigingen zijn nodig om de stappen in dit artikel uit te voeren:

* U moet een beheerder zijn op uw Dynamics 365-exemplaar voor klant betrokkenheid om een oplossing te kunnen installeren en deze instructies te volgen.
* U moet een Tenant beheerder zijn om een nieuw service account te maken voor de lead service die wordt gebruikt voor het verzenden van leads van Marketplace-aanbiedingen.
* U moet toegang hebben tot de Office 365-beheer Portal.
* U moet toegang hebben tot de Azure Portal.

## <a name="install-the-solution"></a>De oplossing installeren

1.  Down load de [Microsoft Marketplace lead Writer-oplossing](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) en sla deze lokaal op uw computer op.

2.  Open Dynamics 365 voor klant betrokkenheid door te navigeren naar de URL voor uw Dynamics-exemplaar (bijvoorbeeld `https://tenant.crm.dynamics.com`).

3.  Toegangs instellingen door het tandwiel pictogram en **Geavanceerde instellingen** op de bovenste navigatie balk te selecteren.
 
    ![Dynamics-geavanceerde instellingen](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Klik eenmaal op de pagina instellingen op de bovenste navigatie balk en selecteer **oplossingen**.

    >[!Note]
    >Als de opties in de volgende scherm opname niet worden weer gegeven, hebt u niet de benodigde machtigingen om door te gaan. Neem contact op met een beheerder in uw Dynamics 365 voor klant engagement-exemplaar.

    ![Dynamics 365-oplossingen](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Selecteer op de pagina oplossingen de optie **importeren** en navigeer naar de locatie waar u de *Microsoft Marketplace lead Writer* -oplossing hebt opgeslagen die u in stap 1 hebt gedownload.

    ![Dynamics 365 voor klant betrokkenheid-importeren](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Voer de wizard oplossing importeren uit om de oplossing te volt ooien.

## <a name="configure-user-permissions"></a>Gebruikers machtigingen configureren

Als u leads naar uw Dynamics 365 voor klant engagement wilt schrijven, moet u een service account met ons delen en machtigingen voor het account configureren.

Gebruik de volgende stappen om het service account te maken en machtigingen toe te wijzen. U kunt **Azure Active Directory** of **Office 365**gebruiken.

>[!Note]
>Op basis van de verificatie optie die u selecteert, kunt u door gaan naar de overeenkomende instructies op basis van uw keuze. Zie [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) of [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

We raden u aan deze optie te gebruiken omdat u het toegevoegde voor deel van nooit nodig hebt om uw gebruikers naam en wacht woord bij te werken om leads te blijven ontvangen. Als u de Azure Active Directory optie wilt gebruiken, geeft u de App-ID, toepassings sleutel en map-ID van uw Active Directory toepassing op.

Voer de volgende stappen uit om Azure Active Directory voor Dynamics 365 voor klant betrokkenheid te configureren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/)en selecteer vervolgens de Azure Active Directory-service in de linkernavigatiebalk.

2. Selecteer **Eigenschappen** in het Azure Active Directory navigatie links en kopieer de waarde van de **Directory-id** op die pagina. Sla deze waarde op, omdat dit de *Directory-ID-* waarde is die u in de portal voor publiceren moet opgeven om leads voor uw Marketplace-aanbieding te ontvangen.

    ![Azure Active Directory-eigenschappen](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selecteer **app-registraties** in het Azure Active Directory linkernavigatiebalk en selecteer vervolgens **nieuwe registratie** op die pagina.
4. Voer een naam in voor de toepassings naam. Geef een zinvolle toepassings naam op.
5. Onder ondersteunde account typen selecteert u **accounts in elke organisatie Directory**.
6. Onder omleidings-URI selecteert u **Web** en geeft u een URI op (zoals `https://contosoapp1/auth`). 
7. Selecteer **Registreren**.

    ![Een toepassing registreren](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Nu uw toepassing is geregistreerd, opent u de overzichts pagina van de toepassing en kopieert u de waarde van de **toepassings-id (client)** op die pagina. Sla deze waarde op, omdat dit de waarde van de *toepassing (client)* is die u moet opgeven in de portal voor publiceren en in Dynamics om leads voor uw Marketplace-aanbieding te ontvangen.

    ![Toepassings-ID (client)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Selecteer **certificaten en geheimen** in de linkernavigatiebalk van de app en selecteer **Nieuw client geheim** op die pagina. Voer een duidelijke beschrijving in voor het client geheim en selecteer de optie **nooit** onder expires. Selecteer **toevoegen** om het client geheim te maken.

    ![Toepassings certificering en geheimen](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Zodra het client geheim is gemaakt, **kopieert u de waarde van het client geheim**. U kunt de waarde niet ophalen nadat u de pagina hebt verlaten. Sla deze waarde op, omdat het de *geheime* waarde van de client is die u moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen. 
11. Selecteer **API-machtigingen** van de linkernavigatiebalk van de apps en selecteer vervolgens **een machtiging toevoegen**.
12. Selecteer micro soft-Api's en selecteer vervolgens **Dynamics CRM** als de API.
13. Zorg ervoor dat de machtiging **gedelegeerde machtigingen** is geselecteerd onder *het type machtigingen dat uw toepassing nodig heeft*. Controleer de machtiging voor **user_impersonation** *toegang common data service als organisatie gebruikers*. Selecteer **machtigingen toevoegen**.

    ![Machtigingen toevoegen](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Nadat u de stappen 1-13 op het Azure Portal hebt voltooid, gaat u naar het exemplaar van Dynamics 365 voor klant betrokkenheid door te navigeren naar de URL (bijvoorbeeld `https://tenant.crm.dynamics.com`).
15. Toegangs instellingen door het tandwiel pictogram te selecteren en **Geavanceerde instellingen** op de bovenste navigatie balk.
16. Klik eenmaal op de pagina instellingen op de bovenste navigatie balk en selecteer **beveiliging**.
17. Selecteer op de pagina Beveiliging de optie **gebruikers**.  Selecteer op de pagina gebruikers de vervolg keuzelijst ' ingeschakelde gebruikers ' om over te scha kelen naar **toepassings gebruikers**.
18. Selecteer **Nieuw** om een nieuwe gebruiker te maken. 

    ![Een nieuwe gebruiker maken](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Zorg er bij **nieuwe gebruiker**voor dat de gebruiker: toepassings gebruiker is geselecteerd. Geef een gebruikers naam, de volledige naam en het e-mail adres op voor de gebruiker die u met deze verbinding wilt gebruiken. Plak ook de **toepassings-id** voor de app die u hebt gemaakt in de Azure Portal uit stap 8. Selecteer **opslaan en sluiten** om het toevoegen van de gebruiker te volt ooien.

    ![Nieuwe gebruiker](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Ga naar beveiligings instellingen in dit artikel om de configuratie van de verbinding voor deze gebruiker te volt ooien.

### <a name="office-365"></a>Office 365

Als u Azure Active Directory niet wilt gebruiken, kunt u een nieuwe gebruiker registreren op het *Microsoft 365 beheer centrum*. U moet uw gebruikers naam/wacht woord elke 90 dagen bijwerken om leads te kunnen blijven ontvangen.

Gebruik de volgende stappen om Office 365 voor Dynamics 365 te configureren voor klant betrokkenheid.

1. Meld u aan bij het [Microsoft 365-beheer centrum](https://admin.microsoft.com).

2. Selecteer **een gebruiker toevoegen**.

    ![Microsoft 365-beheer centrum: een gebruiker toevoegen](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Maak een nieuwe gebruiker voor de lead Writer-service. Configureer de volgende instellingen:

    * Geef een gebruikers naam op
    * Geef een wacht woord op en schakel de optie ' deze gebruiker kan hun wacht woord wijzigen wanneer ze zich voor het eerst aan te melden ' in.
    * Selecteer gebruiker (geen beheerders toegang) als de rol voor de gebruiker.
    * Selecteer Dynamics 365 Customer engagement plan als product licentie die wordt weer gegeven in de volgende scherm opname. Er worden kosten in rekening gebracht voor de licentie die u kiest. 

Sla deze waarden op als de waarden voor *gebruikers naam en wacht woord* die u moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen.

![Microsoft 365-beheer centrum: nieuwe gebruiker](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Beveiligings instellingen

De laatste stap bestaat uit het inschakelen van de gebruiker die u hebt gemaakt voor het schrijven van de leads.

1. Open Dynamics 365 voor klant betrokkenheid door te navigeren naar de URL voor uw Dynamics-exemplaar (bijvoorbeeld `https://tenant.crm.dynamics.com`).
2. Toegangs instellingen door het tandwiel pictogram en **Geavanceerde instellingen** op de bovenste navigatie balk te selecteren.
3. Klik eenmaal op de pagina instellingen op de bovenste navigatie balk en selecteer **beveiliging**.
4. Selecteer op de pagina Beveiliging de optie **gebruikers** en selecteer de gebruiker die u hebt gemaakt in de sectie gebruikers machtigingen configureren van dit document en selecteer vervolgens **rollen beheren**. 

    ![Rollen beheren](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Zoek naar de rolnaam "Microsoft Marketplace lead Writer" en selecteer deze om de gebruiker de rol toe te wijzen.

    ![Gebruikers rollen beheren](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Deze rol wordt gemaakt door de oplossing die u hebt geïmporteerd en heeft alleen machtigingen voor het schrijven van de leads en het bijhouden van de oplossings versie om compatibiliteit te garanderen.

6. Ga terug naar de beveiligings pagina en selecteer **beveiligings rollen**. Zoek naar de rol ' Microsoft Marketplace lead Writer ' en selecteer deze.

    ![Beveiligings rollen](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Selecteer in de beveiligingsrol het tabblad **kern records** . Zoek de entiteit gebruikers interface-instellingen van gebruiker en schakel de machtigingen maken, lezen en schrijven in voor de gebruiker (1/4 Yellow cirkel) voor die entiteit door te klikken op elk van de corresponderende cirkels.

    ![Microsoft Marketplace lead Writer-kern records](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Ga nu naar het tabblad **aanpassing** . Zoek naar de entiteit ' systeem taak ' en schakelt de machtigingen lezen, schrijven en AppendTo in voor de organisatie (effen groen) voor die entiteit door vier keer in elk van de corresponderende cirkels te klikken.

    ![Microsoft Marketplace lead-schrijver-aanpassing](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Opslaan en sluiten**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 voor klant betrokkenheid

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, volgt u de onderstaande stappen:

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
2. Selecteer **verbinding maken** in het gedeelte Lead beheer.

    ![Verbinding maken met het beheer van leads](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Selecteer in het pop-upvenster verbindings Details de optie **Dynamics 365 voor klant betrokkenheid** voor het doel van de lead

    ![Verbindings Details-Lead bestemming](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Geef de **URL van het Dynamics 365-exemplaar** , zoals `https://contoso.crm4.dynamics.com`, op.
5. Selecteer de methode voor **verificatie**, Azure Active Directory of Office 365. 
6. Als u Azure Active Directory hebt geselecteerd, geeft u de **toepassings-id (client)** op (bijvoorbeeld: `23456052-aaaa-bbbb-8662-1234df56788f`), **map-id** (voor beeld: `12345678-8af1-4asf-1234-12234d01db47`) en **client geheim** (voor beeld: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Verbindings Details-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Als u Office 365 hebt geselecteerd, geeft u de **gebruikers naam** (bijvoorbeeld: `contoso@contoso.onmicrosoft.com`) en het wacht woord (bijvoorbeeld: `P@ssw0rd`) op.

    ![Verbindings Details-gebruikers naam](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.
