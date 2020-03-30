---
title: Teststation voor logische apps | Azure Marketplace
description: Hier wordt uitgelegd hoe u hun teststation bouwen dat verbinding maakt met een Dynamics AX/CRM-exemplaar of een andere bron die verder gaat dan alleen Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278379"
---
<a name="logic-app-test-drive"></a>Teststation voor logische apps
====================

Dit artikel is voor uitgevers die hun aanbod op AppSource hebben en hun Test Drive willen bouwen die verbinding maakt met een Dynamics AX/CRM-exemplaar of een andere bron die verder gaat dan alleen Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Een Logic App-teststation maken
-----------------------------------

Test Drive documentatie voor Logic App Test Drives is momenteel nog op GitHub voor [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), ga daar om meer te lezen.

<a name="how-to-publish-a-test-drive"></a>Een teststation publiceren
---------------------------

Nu u uw testrit hebt laten bouwen, loopt deze sectie door elk van de velden die nodig zijn om uw testrit met succes te publiceren.

![De functie Teststation inschakelen](./media/azure-resource-manager-test-drive/howtopub1.png)

Het eerste en belangrijkste veld is om te schakelen of u wilt Test van het formulier met alle vereiste velden worden gepresenteerd voor u in te vullen. Wanneer u **Nee selecteert,** wordt het formulier uitgeschakeld en als u opnieuw publiceert met de testdrive uitgeschakeld, wordt uw teststation uit de productie verwijderd.

*Opmerking:* Als er teststations zijn die actief door gebruikers worden gebruikt, blijven deze teststations worden uitgevoerd totdat hun sessie is verlopen.

### <a name="details"></a>Details

Het volgende gedeelte dat u moet invullen, zijn de details over uw testdrive-aanbieding.

![Gegevens teststuurprogramma](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beschrijving -** *[Vereist veld]* Hier schrijf je de hoofdbeschrijving over wat er op je teststation staat. De klant komt hier om te lezen welke scenario's uw testdrive zal behandelen over uw product. 

**Handleiding -** *[Vereist veld]* Dit is de diepgaande doorloop van uw Test Drive-ervaring. De klant opent dit en kan precies doorlopen wat u wilt dat ze doen tijdens hun proefrit. Het is belangrijk dat deze inhoud gemakkelijk te begrijpen en te volgen is! (Moet een .pdf-bestand zijn)

**Demovideo teststation** \[-\] Aanbevolen Net als in de gebruikershandleiding u het beste een video-zelfstudie van uw Test Drive-ervaring opnemen. De klant zal dit voorafgaand of tijdens zijn proefrit bekijken en kan precies doorlopen wat u wilt dat ze doen tijdens hun proefrit. Het is belangrijk dat deze inhoud gemakkelijk te begrijpen en te volgen is!

- **Naam** - Titel van je video
- **Link** - Moet een ingesloten URL van YouTube of Vimeo zijn. Voorbeeld over hoe u de ingesloten url krijgt, vindt u:
- **Miniatuur** - Moet een afbeelding van hoge kwaliteit (533x324) pixels zijn. Het wordt aanbevolen om hier een screenshot te maken van een deel van je Test Drive-ervaring.

Hieronder vindt u hoe deze velden voor uw klant worden weergegeven tijdens hun Test Drive-ervaring.

![De velden Testdrive zien en voelen](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technische configuratie

De volgende sectie die u moet invullen, is waar u de App Voor de logica van uw teststation configureert en bepaalt hoe specifiek uw teststation-exemplaren werken.

![Technische configuratie van Test Drive](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Regio** - *[Vereist veld]* Het gebied dat u selecteert, is waar u kiest waar uw Test Drive Logic App-bronnen worden geïmplementeerd.

    *Let op:* Als uw Logic App aangepaste bronnen heeft die in een regio zijn opgeslagen, controleert u of dat gebied hier is geselecteerd. De beste manier om dit te doen is door **uw Logic App lokaal volledig te implementeren op uw Azure-abonnement in de portal en te controleren of het werkt** voordat u het hier schrijft.

- **Maximaal gelijktijdige teststations** - *[Vereist veld]* Aantal teststation-exemplaren dat al is geïmplementeerd en in afwachting van toegang per geselecteerde regio. Klanten hebben direct toegang tot deze testritten in plaats van te hoeven wachten op een implementatie.

    *Let op:* Als u een webinar/klas uitvoert waarbij u wilt dat al uw N-studenten een teststation uitvoeren, wordt aanbevolen om te publiceren met N-nummer van Hot-instanties en vervolgens zodra de klasse is afgelopen om opnieuw te publiceren naar uw normale aantal Hotspots.

- **Duur van de testrit (uren) -** *[Vereist veld]* Duur \# voor hoe lang de testrit binnen enkele uren actief blijft. De testdrive eindigt automatisch nadat deze periode is afgelopen.

- **Azure Resource Group Name -** *[Vereist veld]* Schrijf in de naam van de resourcegroep waar uw Logic App Test Drives worden opgeslagen.

- **Logische app-naam toewijzen -** *[Vereist veld]* Schrijf in de Logische App die wordt gebruikt om een gebruiker toe te wijzen in het teststation voordat de klant deze krijgt, schrijf hier in de naam van die Logic App. Controleer of dit bestand is opgeslagen in de brongroep hierboven.

- **Naam van de logische app deprovisionen -** *[Vereist veld]* Schrijf in de naam van de Logische app voor het deprovisioneren van alle bronnen die in de testdrive zijn gemaakt. Controleer of dit bestand is opgeslagen in de brongroep hierboven.

- **Toegangsinformatie -** *[Vereist veld]* Nadat een klant zijn teststation heeft opgevraagd, wordt de toegangsinformatie aan hem of haar gepresenteerd. Deze instructies zijn bedoeld om de nuttige uitvoerparameters te delen van de sjabloon Test Drive Resource Manager. Als u uitvoerparameters wilt opnemen, gebruikt u dubbele krullende haakjes (bijvoorbeeld **{{outputname}}** en worden ze correct ingevoegd op de locatie. (HTML-tekenreeksopmaak wordt hier aanbevolen om aan de voorkant weer te geven).

### <a name="test-drive-deployment-subscription-details"></a>Abonnementsgegevens voor implementatie van teststation

Het laatste gedeelte dat moet worden ingevuld, is om de teststations automatisch te kunnen implementeren door uw Azure Subscription en Azure Active Directory (AD) met elkaar te verbinden.

![Abonnementsgegevens voor Proefstation-implementatie](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure Subscription ID** *[Vereist veld]* Hiermee wordt toegang vergund tot Azure-services en de Azure-portal. Het abonnement is waar resourcegebruik wordt gerapporteerd en services worden gefactureerd. Als u nog niet alleen een **apart** Azure-abonnement voor teststations hebt, gaat u verder en maakt u er een. U Azure Subscription Id's vinden door u aan te melden bij azure portal en te navigeren naar de abonnementen in het menu aan de linkerkant.
(Voorbeeld: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-abonnementen](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-tenant-id** *[Vereist veld]* Als u al een tenant-id\> beschikbaar hebt, u deze hieronder vinden in de eigenschappen - directory-id.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Maak anders een nieuwe tenant in Azure Active Directory.

![Scherm Azure Active Directory-eigenschappen](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-teststation/subdetails5.png)

![Azure Active Directory-tenants](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD-app-id** *[Vereist veld]* De volgende stap is het maken en registreren van een nieuwe toepassing. We gebruiken deze toepassing om bewerkingen uit te voeren op uw Test Drive-exemplaar.

1. Navigeer naar de nieuw gemaakte map of reeds bestaande map en selecteer Azure Active-map in het filtervenster.
2. Zoek op 'App-registraties' en klik op 'Toevoegen'
3. Geef een toepassingsnaam op.
4. Selecteer het type als "Web-app / API"
5. Geef enige waarde op in de\'URL van aanmelding, we gebruiken dat veld niet.
6. Klik op Maken.
7. Nadat de toepassing is gemaakt,\> gaat u naar Eigenschappen - Stel de toepassing in als meervoudige tenant en gaat u op Opslaan.

Klik op Opslaan. De laatste stap is om de applicatie-id voor deze geregistreerde app te pakken en deze hier in het veld Testdrive te plakken.

![Azure Active Directory-toepassings-id](./media/azure-resource-manager-test-drive/subdetails7.png)

Aangezien we de toepassing gebruiken om te implementeren op het abonnement, moeten we de toepassing toevoegen als bijdrager aan het abonnement. De instructies hiervoor zijn hieronder:

1. Navigeer naar het blad Abonnementen en selecteer het juiste abonnement dat u alleen voor het teststation gebruikt.
1. Klik op **Toegangsbeheer (IAM)**.
1. Klik op het tabblad **Roltoewijzingen.**  ![Azure Active Directory, een nieuwe access control-principal toevoegen](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klik **op Roltoewijzing toevoegen**.
1. Stel de rol in als **bijdrager**.
1. Typ de naam van de Azure AD-toepassing en selecteer de toepassing om de rol toe te wijzen.
    ![Azure Active Directory-machtigingen](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klik op **Opslaan**.

**Azure AD-appsleutel -** *[Vereist veld]* Het laatste veld is het genereren van een verificatiesleutel. Voeg onder toetsen een sleutelbeschrijving toe, stel de duur in om nooit te verlopen en selecteer opslaan. Het is **belangrijk** om te voorkomen dat u een verlopen sleutel, die uw proefrit in productie zal breken. Kopieer deze waarde en plak deze in het veld Vereist Testdrive.

![De sectie Azure Active Directory Keys](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> U de Azure App Registration Preview niet gebruiken omdat er momenteel geen basissleutel van 64 wordt gegenereerd.


<a name="next-steps"></a>Volgende stappen
----------

Nu u al uw testdrive-velden hebt ingevuld, gaat u door en publiceert u uw aanbieding **opnieuw.** Zodra uw testdrive het certificeringsproces heeft doorstaan, moet u de klantervaring uitgebreid testen in de **preview** van uw aanbieding. Start een testrit in de gebruikersinterface en controleer of uw teststations volledig correct worden geïmplementeerd.

Het is belangrijk op te merken dat u geen enkel deel van de testdrive verwijdert omdat ze zijn ingericht voor uw klanten, dus de Test Drive-service zal deze resourcegroepen automatisch opschonen nadat een klant ermee is voltooid.

Zodra u zich comfortabel voelt met uw Preview-aanbod, is het nu tijd om live te **gaan!** Er is een laatste beoordelingsproces van Microsoft zodra de aanbieding is gepubliceerd om de volledige ervaring van eind tot eind te controleren. Als om de een of andere reden het aanbod wordt afgewezen, sturen we een melding naar de contactpersoon voor uw aanbieding waarin wordt uitgelegd wat er moet worden opgelost.

Als u meer vragen hebt, op zoek bent naar advies over het oplossen van problemen of uw testrit succesvoller wilt maken, gaat u naar [veelgestelde vragen, probleemoplossing, & Aanbevolen procedures.](./marketing-and-best-practices.md)
