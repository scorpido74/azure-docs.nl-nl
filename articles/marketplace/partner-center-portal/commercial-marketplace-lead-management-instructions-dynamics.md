---
title: Lead management voor Dynamics 365 voor Customer Engagement | Azure Marketplace
description: Configureer leadmanagement voor Dynamics 365 voor customer engagement.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5b3e35b6d19905e3c5262dfea3e52511510c9ffe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252778"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Leadmanagement configureren voor Dynamics 365 voor klantbetrokkenheid

In dit artikel wordt beschreven hoe u Dynamics 365 instelt voor klantbetrokkenheid (voorheen Dynamics CRM Online), lees hier meer over de [wijziging](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) om verkoopleads van uw marktplaatsaanbieding te verwerken. 

>[!Note]
>Deze instructies zijn specifiek voor de door Microsoft gehoste cloud Dynamics 365 voor customer engagement-omgeving. Rechtstreeks verbinding maken met een Dynamics on-prem-omgeving wordt momenteel niet ondersteund, er zijn andere opties voor u om leads te ontvangen, zoals het configureren van een [https-eindpunt](./commercial-marketplace-lead-management-instructions-https.md) of een [Azure-tabel](./commercial-marketplace-lead-management-instructions-azure-table.md) om leads te ontvangen.

## <a name="prerequisites"></a>Vereisten

De volgende gebruikersmachtigingen zijn nodig voor het voltooien van de stappen in dit artikel:

* U moet een beheerder zijn van uw Dynamics 365 voor customer engagement-instantie om een oplossing te kunnen installeren en deze instructies te volgen.
* U moet een tenantbeheerder zijn om een nieuw serviceaccount te maken voor de leadservice die wordt gebruikt om leads te verzenden vanuit marktplaatsaanbiedingen.
* U moet toegang hebben tot de Office 365-beheerportal.
* U moet toegang hebben tot de Azure-portal.

## <a name="install-the-solution"></a>De oplossing installeren

1.  Download de [Microsoft Marketplace Lead Writer-oplossing](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) en sla deze lokaal op uw computer op.

2.  Open Dynamics 365 voor klantbetrokkenheid door naar de URL `https://tenant.crm.dynamics.com`voor uw instantie Dynamics te navigeren (zoals ).

3.  Toegangsinstellingen door het tandwielpictogram en **geavanceerde instellingen** op de bovenste navigatiebalk te selecteren.
 
    ![Dynamica - Geavanceerde instellingen](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Zodra u op de pagina Instellingen staat, opent u het menu Instellen via de bovenste navigatiebalk en selecteert u **Oplossingen**.

    >[!Note]
    >Als u de opties in de volgende schermopname niet ziet, hebt u niet de machtigingen die u nodig hebt om verder te gaan. Neem contact op met een beheerder op uw Dynamics 365 voor bijvoorbeeld Klantbetrokkenheid.

    ![Dynamics 365 - Oplossingen](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Selecteer eenmaal op de pagina Oplossingen de optie **Importeren** en navigeer naar de plaats waar u de *Microsoft Marketplace Lead Writer-oplossing* hebt opgeslagen die u in stap 1 hebt gedownload.

    ![Dynamics 365 voor klantbetrokkenheid - Importeren](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Voltooi het importeren van de oplossing door de wizard Oplossing importeren te volgen.

## <a name="configure-user-permissions"></a>Gebruikersmachtigingen configureren

Als u leads wilt schrijven naar uw Dynamics 365 voor bijvoorbeeld Klantbetrokkenheid, moet u een serviceaccount met ons delen en machtigingen voor het account configureren.

Gebruik de volgende stappen om het serviceaccount te maken en machtigingen toe te wijzen. U **Azure Active Directory** of Office **365**gebruiken.

>[!Note]
>Op basis van de verificatieoptie die u selecteert, u naar de bijbehorende instructies gaan op basis van uw keuze. Zie [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) of Office [365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

We raden deze optie aan omdat je het extra voordeel krijgt dat je je gebruikersnaam/wachtwoord nooit hoeft bij te werken om leads te blijven krijgen. Als u de optie Azure Active Directory wilt gebruiken, geeft u de app-id, toepassingssleutel en map-id op van uw Active Directory-toepassing.

Gebruik de volgende stappen om Azure Active Directory voor Dynamics 365 voor klantbetrokkenheid te configureren.

1. Meld u aan bij [azure portal](https://portal.azure.com/)en selecteer vervolgens de Azure Active Directory-service vanaf de linkernavigatie.

2. Selecteer **Eigenschappen** in de Azure Active Directory linker navigatie en kopieer de **waarde directory-id** op die pagina. Sla deze waarde op, omdat het de *directory-id-waarde* is die u in de publicatieportal moet verstrekken om leads voor uw marktplaatsaanbieding te ontvangen.

    ![Azure Active Directory - Eigenschappen](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selecteer **App-registraties** in de azure Active Directory links navigatie en selecteer **Vervolgens Nieuwe registratie** op die pagina.
4. Voer een naam in voor de toepassingsnaam. Geef een zinvolle toepassingsnaam op.
5. Selecteer **accounts in een organisatiemap**onder Ondersteunde accounttypen .
6. Selecteer onder URI omleiden **en** geef `https://contosoapp1/auth`een URI (zoals ). 
7. Selecteer **Registreren**.

    ![Een toepassing registreren](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Nu uw aanvraag is geregistreerd, u de overzichtspagina van de toepassing openen en de waarde van de **toepassingswaarde (client)** op die pagina kopiëren. Sla deze waarde op, want het is de waarde van de *toepassings-id (client)* die u moet leveren in de publicatieportal en in Dynamics om leads voor uw marktplaatsaanbieding te ontvangen.

    ![(Client-)id van de app](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Selecteer **Certificaten en geheimen** uit de linkernavigatie van de app en selecteer Nieuw **clientgeheim** op die pagina. Voer een zinvolle beschrijving in voor het clientgeheim en selecteer de optie **Nooit** onder Verloopt. Selecteer **Toevoegen** om het clientgeheim te maken.

    ![Toepassing - Certificering en Geheimen](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Zodra het clientgeheim met succes is gemaakt, **kopieert u de geheime waarde van de client.** U de waarde niet ophalen nadat u van de pagina bent weggenavigeerd. Sla deze waarde op, omdat het de geheime waarde van de *klant* is die u in de publicatieportal moet verstrekken om leads voor uw marktplaatsaanbieding te ontvangen. 
11. Selecteer **API-machtigingen** in de linkernavigatie van de apps en selecteer **vervolgens Een machtiging toevoegen**.
12. Selecteer Microsoft API's en selecteer **Dynamics CRM** als API.
13. Controleer *onder Welk type machtigingen uw toepassing vereist,* of **gedelegeerde machtigingen** zijn geselecteerd. Controleer de machtiging voor **user_impersonation** *Access Common Data Service als organisatiegebruikers*. Selecteer **Machtigingen toevoegen**.

    ![Machtigingen toevoegen](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Nadat u stappen 1-13 op de Azure-portal hebt voltooid, navigeert u naar uw `https://tenant.crm.dynamics.com`instantie Dynamics 365 voor klantbetrokkenheid door naar de URL te navigeren (zoals ).
15. Toegangsinstellingen door het tandwielpictogram en **geavanceerde instellingen** op de bovenste navigatiebalk te selecteren.
16. Zodra u op de pagina Instellingen staat, opent u het menu Instellen op de bovenste navigatiebalk en selecteert u **Beveiliging**.
17. Selecteer **Gebruikers**eenmaal op de pagina Beveiliging .  Selecteer op de pagina Gebruikers de vervolgkeuzelijst 'Ingeschakelde gebruikers' om over te schakelen naar **gebruikers van toepassingen.**
18. Selecteer **Nieuw** om een nieuwe gebruiker te maken. 

    ![Een nieuwe gebruiker maken](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Controleer **in Nieuwe gebruiker**of de GEBRUIKER: GEBRUIKER van toepassing is geselecteerd. Geef een gebruikersnaam, volledige naam en e-mailadres op voor de gebruiker die u met deze verbinding wilt gebruiken. Plak ook de **toepassings-id** voor de app die u hebt gemaakt in de Azure-portal vanaf stap 8. Selecteer **Opslaan en Sluiten** om de gebruiker toe te voegen.

    ![Nieuwe gebruiker](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Ga naar 'Beveiligingsinstellingen' in dit artikel om de configuratie van de verbinding voor deze gebruiker te voltooien.

### <a name="office-365"></a>Office 365

Als u Azure Active Directory niet wilt gebruiken, u een nieuwe gebruiker registreren in het *Microsoft 365-beheercentrum.* Je moet je gebruikersnaam/wachtwoord elke 90 dagen bijwerken om leads te blijven krijgen.

Gebruik de volgende stappen om Office 365 voor Dynamics 365 voor klantbetrokkenheid te configureren.

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

2. Selecteer **Een gebruiker toevoegen**.

    ![Microsoft 365-beheercentrum - een gebruiker toevoegen](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Maak een nieuwe gebruiker voor de leadwriter-service. Configureer de volgende instellingen:

    * Geef een gebruikersnaam op
    * Geef een wachtwoord op en schakel de optie 'Deze gebruiker zijn wachtwoord wijzigen wanneer hij zich voor het eerst aanmeldt' uit.
    * Selecteer 'Gebruiker (geen beheerderstoegang)' als rol voor de gebruiker.
    * Selecteer 'Dynamics 365 Customer Engagement plan' als productlicentie die wordt weergegeven in de volgende schermopname. Er worden kosten in rekening gebracht voor de licentie die u kiest. 

Sla deze waarden op omdat dit de *gebruikersnaam- en wachtwoordwaarden* zijn die u in de publicatieportal moet opgeven om leads voor uw marktplaatsaanbieding te ontvangen.

![Microsoft 365-beheercentrum - nieuwe gebruiker](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Beveiligingsinstellingen

De laatste stap is om de gebruiker die u hebt gemaakt in staat te stellen de leads te schrijven.

1. Open Dynamics 365 voor klantbetrokkenheid door naar de URL `https://tenant.crm.dynamics.com`voor uw instantie Dynamics te navigeren (zoals ).
2. Toegangsinstellingen door het tandwielpictogram en **geavanceerde instellingen** op de bovenste navigatiebalk te selecteren.
3. Zodra u op de pagina Instellingen staat, opent u het menu Instellen op de bovenste navigatiebalk en selecteert u **Beveiliging**.
4. Selecteer eenmaal op de pagina Beveiliging de optie **Gebruikers** en selecteer de gebruiker die u hebt gemaakt in de sectie Gebruikersmachtigingen configureren van dit document en selecteer **Rollen beheren**. 

    ![Rollen beheren](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Zoek naar de rolnaam 'Microsoft Marketplace Lead Writer' en selecteer deze om de gebruiker de rol toe te wijzen.

    ![Gebruikersrollen beheren](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Deze rol wordt gemaakt door de oplossing die u hebt geïmporteerd en heeft alleen machtigingen om de leads te schrijven en om de oplossingsversie bij te houden om compatibiliteit te garanderen.

6. Navigeer terug naar de beveiligingspagina en selecteer **Beveiligingsrollen**. Zoek naar de rol 'Microsoft Marketplace Lead Writer' en selecteer deze.

    ![Beveiligingsrollen](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Eenmaal in de beveiligingsrol selecteert u het tabblad **Kernrecords.** Zoek naar de entiteit 'Gebruikersentiteit-gebruikersinterface-instellingen' en schakel de machtigingen voor maken, lezen en schrijven aan de gebruiker (1/4 gele cirkel) in voor die entiteit door eenmaal in elk van de overeenkomstige cirkels te klikken.

    ![Microsoft Marketplace-leadwriter - Kernrecords](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Navigeer nu naar het tabblad **Aanpassen.** Zoek de entiteit 'Systeemtaak' en hiermee u de machtigingen Lezen, Schrijven en AppendTo voor organisatie (effen groen) voor die entiteit door vier keer in elk van de overeenkomstige cirkels te klikken.

    ![Microsoft Marketplace Lead Writer - aanpassing](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Opslaan en sluiten**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configureer uw aanbieding om leads naar Dynamics 365 For Customer Engagement te verzenden

Wanneer u klaar bent om de leadmanagementgegevens voor uw aanbieding in de publicatieportal te configureren, volgt u de volgende stappen:

1. Navigeer naar de pagina **Installatie van aanbieding** voor uw aanbieding.
2. Selecteer **Verbinding maken** onder de sectie Leadbeheer.

    ![Verbinding maken met Leadmanagement](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Selecteer **Dynamics 365 voor klantbetrokkenheid** voor de hoofdbestemming in het pop-upvenster Verbindingsdetails

    ![Verbindingsgegevens - hoofdbestemming](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Geef de **URL van dynamics 365 Instance** op, zoals `https://contoso.crm4.dynamics.com`.

5. Selecteer de **verificatiemethode**, Azure Active Directory of Office 365. 
6. Als u Azure Active Directory hebt geselecteerd, moet `23456052-aaaa-bbbb-8662-1234df56788f`u de **toepassings-id (client) (voorbeeld:** ), **Directory-id** (voorbeeld: `12345678-8af1-4asf-1234-12234d01db47`) en **Clientgeheim** (voorbeeld: ) opvragen. `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`

    ![Verbindingsgegevens - Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Als u Office 365 hebt geselecteerd, `contoso@contoso.onmicrosoft.com`geeft u de `P@ssw0rd` **gebruikersnaam** op (voorbeeld: ) en Wachtwoord (voorbeeld: ).

    ![Verbindingsgegevens - Gebruikersnaam](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

8. **E-mail contact** opnemen : e-mails verstrekken aan mensen in uw bedrijf die e-mailmeldingen moeten ontvangen wanneer een nieuwe lead wordt ontvangen. U meerdere e-mails verstrekken door ze te scheiden met puntkomma.
9. Selecteer **OK**.

Klik op de knop Valideren om ervoor te zorgen dat u met succes verbinding hebt gemaakt met een leadbestemming. Als dit lukt, heb je een testlead in de hoofdbestemming.

![Leadbeheer - opslagaccount voor verbindingsgegevens](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!Note]
>U moet de rest van de aanbieding voltooien en publiceren voordat u leads voor de aanbieding ontvangen.
