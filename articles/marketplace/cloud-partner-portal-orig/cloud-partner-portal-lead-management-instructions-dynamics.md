---
title: Dynamics CRM | Azure Marketplace
description: Leadmanagement configureren voor Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416311"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Leadmanagement configureren voor Dynamics CRM online

In dit artikel wordt beschreven hoe u Dynamics CRM Online instelt om verkoopleads te verwerken.

## <a name="prerequisites"></a>Vereisten

De volgende gebruikersmachtigingen zijn nodig voor het invullen van de stappen in dit artikel:
- U moet een beheerder zijn van uw Dynamics CRM Online-exemplaar om een oplossing te installeren.
- U moet een tenantbeheerder zijn om een nieuw serviceaccount voor leadservice te maken.

<a name="install-the-solution"></a>De oplossing installeren
--------------------

1.  Download de [Microsoft Marketplace Lead Writer-oplossing](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) en sla deze lokaal op.

2.  Open Dynamics CRM Online en ga naar Instellingen.
    >[!NOTE]
    >Als u de opties in de volgende schermopname niet ziet, hebt u niet de machtigingen die u nodig hebt.
 
       ![Weergave dynamica-instelling](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecteer **Importeren**en selecteer de oplossing die u in stap 1 hebt gedownload.
 
    ![Import voor dynamica, optie](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Maak het installeren van de oplossing af.

## <a name="configure-user-permissions"></a>Gebruikersmachtigingen configureren

Als u leads wilt schrijven naar uw Dynamics CRM-exemplaar, moet u een serviceaccount met ons delen en machtigingen voor het account configureren.

Gebruik de volgende stappen om het serviceaccount te maken en machtigingen toe te wijzen. U **Azure Active Directory** of Office **365**gebruiken.

### <a name="azure-active-directory"></a>Azure Active Directory

We raden deze optie aan omdat je het extra voordeel krijgt dat je je gebruikersnaam/wachtwoord nooit hoeft bij te werken om leads te blijven krijgen. Als u de optie Azure Active Directory wilt gebruiken, geeft u de app-id, toepassingssleutel en adreslijst-id op van uw Active Directory-toepassing.

Gebruik de volgende stappen om Azure Active Directory voor Dynamics CRM te configureren.

1.  Meld u aan bij [azure portal](https://portal.azure.com/) en selecteer vervolgens de Azure Active Directory-service.

2.  Selecteer **Eigenschappen** en kopieer de **adreslijst-id**. Dit is de identificatie van uw tenantaccount die u moet gebruiken in de Cloud Partner Portal.

    ![Directory-id](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecteer **App-registraties**en selecteer **Vervolgens Nieuwe toepassingsregistratie**.
4.  Voer de toepassingsnaam in.
5.  Selecteer voor Type **web-app / API**.
6.  Geef een URL op. Dit veld is niet nodig voor leads, maar is vereist om een toepassing te maken.
7. Selecteer **Maken**.
8.  Nu uw toepassing is geregistreerd, selecteert u **Eigenschappen** en selecteert u **de toepassings-id kopiëren**. U gebruikt deze verbindingsgegevens in de Cloud Partner Portal.
9.  Stel in Eigenschappen de toepassing in als Meervoudige tenant en selecteer **Vervolgens Opslaan**.

10. Selecteer **Toetsen** en maak een nieuwe sleutel met de ingestelde duur op *Nooit verloopt*. Selecteer **Opslaan** om de sleutel te maken. 
11. Selecteer in het menu Toetsen de optie **De sleutelwaarde kopiëren.** Sla een kopie van deze waarde op omdat u deze nodig hebt voor de Cloud Partner Portal.
    
    ![Dynamics get registered key Dynamics get registered key Dynamics get registered key Dynamics get](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecteer **Vereiste machtigingen** en selecteer Vervolgens **Toevoegen**. 
13. Selecteer **Dynamics CRM Online** als de nieuwe API en controleer de machtiging voor Access CRM Online als *organisatiegebruikers.*

14. Ga bij Dynamics CRM naar Gebruikers en selecteer de vervolgkeuzelijst 'Ingeschakelde gebruikers' om over te schakelen naar **gebruikers van toepassingen.**
    
    ![Gebruikers van toepassingen](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecteer **Nieuw** om een nieuwe gebruiker te maken. Selecteer de vervolgkeuzelijst **GEBRUIKER: GEBRUIKER van toepassing.**
    
    ![Nieuwe toepassingsgebruiker toevoegen](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Geef in **Nieuwe gebruiker**de naam en e-mail op die u met deze verbinding wilt gebruiken. Plak in de **toepassings-id** voor de app die u hebt gemaakt in de Azure-portal.

     ![Nieuwe gebruiker configureren](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Ga naar 'Beveiligingsinstellingen' in dit artikel om de configuratie van de verbinding voor deze gebruiker te voltooien.

### <a name="office-365"></a>Office 365

Als u Azure Active Directory niet wilt gebruiken, u een nieuwe gebruiker registreren in het *Microsoft 365-beheercentrum.* Je moet je gebruikersnaam/wachtwoord elke 90 dagen bijwerken om leads te blijven krijgen.

Gebruik de volgende stappen om Office 365 voor Dynamics CRM te configureren.

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

2. Selecteer de tegel **Beheerder.**

    ![Office Online-beheerder](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecteer **Een gebruiker toevoegen**.

    ![Een gebruiker toevoegen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Maak een nieuwe gebruiker voor de leadwriter-service. Configureer de volgende instellingen:

    -   Geef een wachtwoord op en schakel de optie 'Deze gebruiker zijn wachtwoord wijzigen wanneer hij zich voor het eerst aanmeldt' uit.
    -   Selecteer 'Gebruiker (geen beheerderstoegang)' als rol voor de gebruiker.
    -   Selecteer de productlicentie die wordt weergegeven in de volgende schermopname. Er worden kosten in rekening gebracht voor de licentie die u kiest. De oplossing werkt ook met dynamics CRM Online Basic-licentie.
    
    ![Gebruikersmachtigingen en -licentie configureren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Beveiligingsinstellingen

De laatste stap is om de gebruiker die u hebt gemaakt in staat te stellen de leads te schrijven.

1.  Meld je online aan bij Dynamics CRM.
2.  Selecteer **Beveiliging** **in Instellingen**.
    
    ![Beveiligingsinstellingen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecteer de gebruiker die u hebt gemaakt in **gebruikersmachtigingen**en selecteer **vervolgens Gebruikersrollen beheren**. Controleer **Microsoft Marketplace Lead Writer** om de rol toe te wijzen.

    ![Gebruikersrol toewijzen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Deze rol wordt gemaakt door de oplossing die u hebt geïmporteerd en heeft alleen machtigingen om de leads te schrijven en om de oplossingsversie bij te houden om compatibiliteit te garanderen.

4.  Selecteer in Beveiliging **beveiligingsrollen** en zoek de rol voor Microsoft Marketplace-leadwriter.
    
    ![Beveiligingsleadwriter configureren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecteer het tabblad **Kernrecords.** Maak/Lezen/schrijven inschakelen voor de gebruikersinterface van de gebruikersentiteit.

    ![Maken/lezen/schrijven inschakelen voor de gebruiker](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Inpakken

Voltooi het configureren van Dynamics CRM voor leadbeheer door de gegenereerde accountgegevens toe te voegen aan de Cloud Partner Portal. Bijvoorbeeld:

-   **Azure Active Directory** - **Application Id** (voorbeeld: *23456052-aaaa-bbbb-8662-1234df56788f),* **Directory Id** (voorbeeld: *12345678-8af1-4 asf-1234-12234d01db47*), en **Application Key** (voorbeeld: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **Url** **`https://contoso.crm4.dynamics.com`**(voorbeeld: ), **`contoso\@contoso.onmicrosoft.com`** **Gebruikersnaam** (voorbeeld: ) en **Wachtwoord** (voorbeeld: *P\@ssw0rd*).
