---
title: Test station voor logische apps | Azure Marketplace
description: Hierin wordt uitgelegd hoe u de test schijf bouwt die met een Dynamics AX/CRM-exemplaar of een andere resource dan alleen Azure wordt verbonden.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278379"
---
<a name="logic-app-test-drive"></a>Test station voor logische apps
====================

Dit artikel is bedoeld voor uitgevers die hun aanbieding op AppSource hebben en hun test station willen bouwen dat is verbonden met een Dynamics AX/CRM-exemplaar of een andere resource dan alleen Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Een test station voor een logische app maken
-----------------------------------

Test de documentatie voor de test schijven voor logische apps is momenteel nog steeds beschikbaar op GitHub voor [bewerkingen](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en de [klant betrokkenheid](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app). Ga hiervoor naar meer informatie.

<a name="how-to-publish-a-test-drive"></a>Een test station publiceren
---------------------------

Nu u uw test station hebt gemaakt, gaat u in deze sectie naar elk van de velden die nodig zijn voor het publiceren van uw test station.

![De functie test drive inschakelen](./media/azure-resource-manager-test-drive/howtopub1.png)

Het eerste en belangrijkste veld is om te scha kelen of u het formulier wilt testen met alle vereiste velden, zodat u het kunt invullen. Wanneer u **Nee selecteert,** wordt het formulier uitgeschakeld en als u opnieuw publiceert met het test station is uitgeschakeld, wordt het test station uit productie verwijderd.

*Opmerking*: als er test stations actief worden gebruikt door gebruikers, blijven die test stations actief totdat de sessie verloopt.

### <a name="details"></a>Details

De volgende sectie die u moet invullen, is de details van de aanbieding voor uw test drive.

![Details van test stuur programma](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beschrijving-** *[vereist veld]* dit is de plaats waar u de hoofd beschrijving schrijft over wat er op uw test station staat. De klant komt hier om te lezen welke scenario's uw test station zal bedekken over uw product. 

**Gebruikers handleiding-** *[vereist veld]* dit is het gedetailleerde overzicht van uw test drive-ervaring. De klant opent dit en kan leiden tot precies wat u nodig hebt voor hun test station. Het is belang rijk dat deze inhoud eenvoudig te begrijpen is en volgt. (Moet een PDF-bestand zijn)

**Demo video testen:** \[aanbevolen\] , vergelijkbaar met de gebruikers handleiding, het is het beste om een video-zelf studie voor uw test beleving op te vragen. De klant zal dit vóór of tijdens hun test station bekijken en kunnen door lopen wat u nodig hebt voor hun test station. Het is belang rijk dat deze inhoud eenvoudig te begrijpen is en volgt.

- **Naam** : titel van uw video
- **Link** -moet een Inge sloten URL zijn van YouTube of Vimeo. Hieronder vindt u een voor beeld van het ophalen van de Inge sloten URL:
- **Miniatuur** : moet een afbeelding van hoge kwaliteit (533x324) zijn. Het is raadzaam om hier een scherm opname van een deel van de test drive-ervaring te maken.

Hieronder ziet u hoe deze velden voor uw klant worden weer gegeven tijdens hun test beleving.

![Het uiterlijk van de velden in een station testen](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technische configuratie

In het volgende gedeelte om in te vullen kunt u de logische app voor test schijven configureren en definiëren hoe specifiek uw test drive-instanties werken.

![Technische configuratie van test station](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Regio** - *[vereist veld]* de regio die u selecteert, is de locatie waar de logische app-resources van uw test station worden geïmplementeerd.

    *Opmerking:* Als uw logische app aangepaste resources heeft die zijn opgeslagen in een regio, moet u ervoor zorgen dat de regio hier wordt geselecteerd. De beste manier om dit te doen is om **uw logische app lokaal te implementeren in uw Azure-abonnement in de portal en te controleren of deze werkt** voordat u deze hier schrijft.

- **Maxi maal aantal gelijktijdige test schijven** - *[vereist veld]* nummer van exemplaren van het test station die al zijn geïmplementeerd en wachten op toegang per geselecteerde regio. Klanten hebben direct toegang tot deze test stations in plaats van te wachten op een implementatie.

    *Opmerking:* Als u een webinar/klasse uitvoert waar u al uw N-aantal studenten een test station wilt maken, is het raadzaam om te publiceren met N Hot instances en zodra de klasse opnieuw wordt gepubliceerd naar uw normale aantal hot instances.

- **Test drive duur (uren)-** *[vereist veld]* de duur van hoe lang het test station actief blijft, in \# uren. Het test station wordt automatisch beëindigd nadat deze periode is verstreken.

- **Naam van de Azure-resource groep-** *[vereist veld]* Schrijf in de naam van de resource groep waar de test stations van de logische app worden opgeslagen.

- De naam van de **logische app toewijzen-** *[vereist veld]* schrijven in de logische app die wordt gebruikt om een gebruiker toe te wijzen in het test station voordat de klant deze ontvangt, moet u hier de naam van de logische app schrijven. Zorg ervoor dat dit bestand wordt opgeslagen in de bovenstaande resource groep.

- De naam van de logische app voor het ongedaan maken van de **inrichting-** *[vereist veld]* Schrijf in de naam van de logische app op voor uw onbeschikbaarstelling van alle resources die zijn gemaakt in het test station. Zorg ervoor dat dit bestand wordt opgeslagen in de bovenstaande resource groep.

- **Toegangs gegevens-** *[vereist veld]* nadat een klant hun test station heeft opgehaald, worden de toegangs gegevens weer gegeven. Deze instructies zijn bedoeld voor het delen van de bruikbare uitvoer parameters van uw test station Resource Manager-sjabloon. Als u uitvoer parameters wilt toevoegen, gebruikt u dubbele accolades (bijvoorbeeld **{{uitvoermap}}**). deze worden op de juiste wijze ingevoegd op de locatie. (De opmaak van HTML-teken reeksen wordt hier aanbevolen om weer te geven in de front-end).

### <a name="test-drive-deployment-subscription-details"></a>Details van implementatie abonnement testen

De laatste sectie die moet worden ingevuld, is om de test stations automatisch te kunnen implementeren door uw Azure-abonnement en Azure Active Directory (AD) te verbinden.

![Details van implementatie abonnement testen](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-abonnements-id** *[vereist veld]* Hiermee krijgt u toegang tot Azure-Services en de Azure Portal. In het abonnement wordt het resource gebruik gerapporteerd en de services worden gefactureerd. Als u nog geen **apart** Azure-abonnement voor test stations hebt, kunt u er een maken. U kunt Azure-abonnement-Id's vinden door u aan te melden bij Azure Portal en te navigeren naar de abonnementen in het menu aan de linkerkant.
(Bijvoorbeeld: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Azure-abonnementen](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-Tenant-id** *[vereist veld]* als u een Tenant-id al hebt, kunt u deze hieronder in de\> eigenschappen-map-id vinden.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Als dat niet het geval is, maakt u een nieuwe Tenant in Azure Active Directory.

![Scherm Eigenschappen van Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-test-drive/subdetails5.png)

![Azure Active Directory-tenants](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD-App-ID** *[vereist veld]* is de volgende stap het maken en registreren van een nieuwe toepassing. Deze toepassing wordt gebruikt voor het uitvoeren van bewerkingen op uw exemplaar van het test station.

1. Ga naar de zojuist gemaakte map of al een bestaande map en selecteer Azure Active Directory in het deel venster filter.
2. Zoek "App-registraties" en klik op toevoegen.
3. Geef een toepassings naam op.
4. Het type van als web-app/API selecteren
5. Geef een waarde op voor de aanmeldings-URL, die\'we hebben gewonnen door dit veld te gebruiken.
6. Klik op maken.
7. Nadat de toepassing is gemaakt, gaat u naar eigenschappen:\> Stel de toepassing in als multi tenant en druk op opslaan.

Klik op Opslaan. De laatste stap bestaat uit het oppakken van de toepassings-ID voor deze geregistreerde app en deze in het veld test station te plakken.

![Azure Active Directory-toepassings-id](./media/azure-resource-manager-test-drive/subdetails7.png)

Omdat we de toepassing gebruiken om te implementeren in het abonnement, moeten we de toepassing toevoegen als een bijdrager aan het abonnement. Hieronder vindt u instructies:

1. Navigeer naar de Blade abonnementen en selecteer het juiste abonnement dat u alleen voor het test station gebruikt.
1. Klik op **Toegangsbeheer (IAM)**.
1. Klik op **het tabblad roltoewijzingen.**  ![Azure Active Directory, een nieuwe Access Control Principal toevoegen](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klik op **roltoewijzing toevoegen**.
1. Stel de rol in als **Inzender**.
1. Typ de naam van de Azure AD-toepassing en selecteer de toepassing om de rol toe te wijzen.
    ![Azure Active Directory machtigingen](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klik op **Opslaan**.

**Azure AD-App sleutel-** *[vereist veld]* het laatste veld is om een verificatie sleutel te genereren. Voeg onder sleutels een sleutel beschrijving toe, stel de duur in op nooit verlopen en selecteer vervolgens opslaan. Het is **belang rijk** om te voor komen dat u een verlopen sleutel hebt, waardoor uw test drive in productie worden verbroken. Kopieer deze waarde en plak deze in het verplichte veld test station.

![Sectie Azure Active Directory sleutels](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> U kunt de preview van Azure-app-registratie niet gebruiken omdat er momenteel geen base64-gecodeerde sleutel wordt gegenereerd.


<a name="next-steps"></a>Volgende stappen
----------

Nu u alle velden van het test station hebt ingevuld, gaat u naar uw aanbieding en publiceert u deze **opnieuw** . Als uw test station het certificerings proces heeft door lopen, moet u de gebruikers ervaring in de **Preview** van uw aanbieding uitgebreid testen. Start een test station in de gebruikers interface en controleer of de test stations volledig correct worden geïmplementeerd.

Het is belang rijk te weten dat u geen enkel deel van het test station verwijdert wanneer het is ingericht voor uw klanten, zodat de service voor het testen van deze resource groepen automatisch opschoont nadat een klant is voltooid.

Zodra u vertrouwd bent met uw preview-aanbieding, is het tijd om **Live te gaan**! Er is een laatste beoordelings proces van micro soft wanneer de aanbieding is gepubliceerd om de volledige end-to-end-ervaring dubbel te controleren. Als het aanbod om de een of andere reden wordt afgewezen, wordt er een melding verzonden naar de technische contact persoon van uw aanbieding waarin wordt uitgelegd wat er moet worden opgelost.

Als u meer vragen hebt, zoekt u naar advies over het oplossen van problemen of wilt u uw test station sneller laten verlopen, ga dan naar [Veelgestelde vragen, probleem oplossing & aanbevolen procedures](./marketing-and-best-practices.md).
