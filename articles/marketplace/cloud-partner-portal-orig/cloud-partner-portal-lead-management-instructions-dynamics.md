---
title: Dynamics CRM | Azure Marketplace
description: Beheer van leads voor Dynamics CRM configureren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416311"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Beheer van leads configureren voor Dynamics CRM Online

In dit artikel wordt beschreven hoe u Dynamics CRM Online instelt om verkoop leads te verwerken.

## <a name="prerequisites"></a>Vereisten

De volgende gebruikers machtigingen moeten worden uitgevoerd om de stappen in dit artikel uit te voeren:
- U moet een beheerder zijn op uw Dynamics CRM Online-exemplaar om een oplossing te installeren.
- U moet een Tenant beheerder zijn om een nieuw service account voor de lead service te maken.

<a name="install-the-solution"></a>De oplossing installeren
--------------------

1.  Down load de [Microsoft Marketplace lead Writer-oplossing](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) en sla deze lokaal op.

2.  Open Dynamics CRM Online en ga naar instellingen.
    >[!NOTE]
    >Als de opties in de volgende scherm opname niet worden weer gegeven, hebt u niet de benodigde machtigingen.
 
       ![Weer gave Dynamics instellen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecteer **importeren**en selecteer vervolgens de oplossing die u in stap 1 hebt gedownload.
 
    ![Optie Dynamics importeren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Voltooi de installatie van de oplossing.

## <a name="configure-user-permissions"></a>Gebruikers machtigingen configureren

Als u leads naar uw Dynamics CRM-exemplaar wilt schrijven, moet u een service account met ons delen en machtigingen voor het account configureren.

Gebruik de volgende stappen om het service account te maken en machtigingen toe te wijzen. U kunt **Azure Active Directory** of **Office 365**gebruiken.

### <a name="azure-active-directory"></a>Azure Active Directory

We raden u aan deze optie te gebruiken omdat u het toegevoegde voor deel van nooit nodig hebt om uw gebruikers naam en wacht woord bij te werken, zodat u leads kunt blijven ontvangen. Als u de Azure Active Directory optie wilt gebruiken, geeft u de App-ID, toepassings sleutel en map-id van uw Active Directory toepassing op.

Gebruik de volgende stappen om Azure Active Directory te configureren voor Dynamics CRM.

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/) en selecteer vervolgens de Azure Active Directory-service.

2.  Selecteer **Eigenschappen** en kopieer vervolgens de **map-id**. Dit is de id van uw Tenant account die u moet gebruiken in de Cloud Partner-portal.

    ![Directory-ID ophalen](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecteer **app-registraties**en selecteer vervolgens **nieuwe toepassings registratie**.
4.  Voer de toepassingsnaam in.
5.  Selecteer bij type de optie **Web-app/API**.
6.  Geef een URL op. Dit veld is niet nodig voor leads, maar is wel vereist voor het maken van een toepassing.
7. Selecteer **Maken**.
8.  Nu uw toepassing is geregistreerd, selecteert u **Eigenschappen** en selecteert u vervolgens **de toepassings-id kopiëren**. U gebruikt deze verbindings gegevens in de Cloud Partner-portal.
9.  Stel in eigenschappen de toepassing in als multi tenant en selecteer vervolgens **Opslaan**.

10. Selecteer **sleutels** en maak een nieuwe sleutel met de duur ingesteld op *nooit verloopt*. Selecteer **Opslaan** om de sleutel te maken. 
11. Selecteer **de sleutel waarde kopiëren** in het menu sleutels. Sla een kopie van deze waarde op omdat u deze nodig hebt voor de Cloud Partner-portal.
    
    ![Dynamics Get register sleutel](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecteer de **vereiste machtigingen** en selecteer vervolgens **toevoegen**. 
13. Selecteer **Dynamics CRM Online** als de nieuwe API en controleer de machtiging voor *Access CRM Online als organisatie gebruikers*.

14. Ga in Dynamics CRM naar gebruikers en selecteer de vervolg keuzelijst ' ingeschakelde gebruikers ' om over te scha kelen naar **toepassings gebruikers**.
    
    ![Toepassings gebruikers](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecteer **Nieuw** om een nieuwe gebruiker te maken. Selecteer de **gebruiker:** vervolg KEUZELIJST voor toepassings gebruiker.
    
    ![Nieuwe toepassings gebruiker toevoegen](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Geef bij **nieuwe gebruiker**de naam en het e-mail adres op die u wilt gebruiken met deze verbinding. Plak de **toepassings-id** voor de app die u hebt gemaakt in de Azure Portal.

     ![Nieuwe gebruiker configureren](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Ga naar beveiligings instellingen in dit artikel om de configuratie van de verbinding voor deze gebruiker te volt ooien.

### <a name="office-365"></a>Office 365

Als u Azure Active Directory niet wilt gebruiken, kunt u een nieuwe gebruiker registreren op het *Microsoft 365 beheer centrum*. U moet uw gebruikers naam/wacht woord elke 90 dagen bijwerken om leads te kunnen blijven ontvangen.

Gebruik de volgende stappen om Office 365 voor Dynamics CRM te configureren.

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

2. Selecteer de tegel **beheer** .

    ![Office Online-beheerder](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecteer **een gebruiker toevoegen**.

    ![Een gebruiker toevoegen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Maak een nieuwe gebruiker voor de lead Writer-service. Configureer de volgende instellingen:

    -   Geef een wacht woord op en schakel de optie ' deze gebruiker moet hun wacht woord wijzigen wanneer ze zich voor het eerst aanmelden ' uit.
    -   Selecteer gebruiker (geen beheerders toegang) als de rol voor de gebruiker.
    -   Selecteer de product licentie die wordt weer gegeven in de volgende scherm opname. Er worden kosten in rekening gebracht voor de licentie die u kiest. De oplossing werkt ook met de Basic-licentie van Dynamics CRM Online.
    
    ![Gebruikers machtigingen en-licenties configureren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Beveiligingsinstellingen

De laatste stap bestaat uit het inschakelen van de gebruiker die u hebt gemaakt voor het schrijven van de leads.

1.  Meld u aan bij Dynamics CRM Online.
2.  Selecteer bij **instellingen**de optie **beveiliging**.
    
    ![Beveiligingsinstellingen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecteer de gebruiker die u hebt gemaakt in **gebruikers machtigingen**en selecteer vervolgens **gebruikers rollen beheren**. Controleer **Microsoft Marketplace lead Writer** om de rol toe te wijzen.

    ![Gebruikersrol toewijzen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Deze rol wordt gemaakt door de oplossing die u hebt geïmporteerd en heeft alleen machtigingen voor het schrijven van de leads en het bijhouden van de oplossings versie om compatibiliteit te garanderen.

4.  In beveiliging selecteert u **beveiligings rollen** en zoekt u de rol voor Microsoft Marketplace lead Writer.
    
    ![Auteur van beveiligings lead configureren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecteer het tabblad **kern records** . Schakel maken/lezen/schrijven in voor de gebruikers interface van de gebruiker.

    ![Maken/lezen/schrijven inschakelen voor gebruiker](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Inpakken

Voltooi de configuratie van Dynamics CRM voor lead beheer door de gegenereerde account gegevens toe te voegen aan de Cloud Partner-portal. Bijvoorbeeld:

-   **Azure Active Directory** - **toepassings-id** (bijvoorbeeld *: 23456052-aaaa-bbbb-8662-1234df56788f*), **Directory-id** (bijvoorbeeld: *12345678-8af1-4asf-1234-12234d01db47*) en **toepassings sleutel** (bijvoorbeeld: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =*).
-   **Office 365** - **-URL** (bijvoorbeeld **`https://contoso.crm4.dynamics.com`**:) **, gebruikers naam** (voor **`contoso\@contoso.onmicrosoft.com`** beeld:) en **wacht woord** (voor beeld: *P\@ssw0rd*).
