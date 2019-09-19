---
title: Aan de slag met de Azure EA Portal
description: In dit artikel wordt uitgelegd hoe Azure EA-klanten Azure EA Portal gebruiken.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900927"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Aan de slag met de Azure EA Portal

Dit artikel helpt directe en indirecte Azure EA-klanten op weg met het gebruik van [Azure EA Portal](https://ea.azure.com) en bevat de volgende basisinformatie:

- Hoe Azure EA Portal is gestructureerd.
- Rollen die worden gebruikt in Azure EA Portal.
- Hoe u kunt beginnen met het maken van abonnementen.
- Analyseren van de kosten in Azure EA Portal en Azure Portal.

Hier volgt een video met een volledige onboardingsessie voor Azure EA Portal:

[Onboardingvideo voor Azure EA Portal](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hiërarchie van Azure EA Portal

De hiërarchie van Azure EA Portal bestaat uit de volgende elementen:

**Microsoft Azure EA Portal**: Azure EA Portal is een online beheerportal waarmee u de kosten voor uw Azure EA-services kunt beheren. U gebruikt de portal om een Azure EA-hiërarchie te maken, inclusief afdelingen, accounts en abonnementen. U gebruikt de portal ook om de kosten de gebruikte services af te stemmen, gebruiksrapporten te downloaden en prijslijsten weer te geven. En u maakt API-sleutels voor uw inschrijving.

**Afdelingen**: U maakt afdelingen zodat u de kosten kunt onderverdelen in logische groeperingen en vervolgens een budget of quotum op afdelingsniveau kunt instellen.

**Accounts**: Accounts zijn organisatie-eenheden in Azure EA Portal die worden gebruikt om abonnementen te beheren. Accounts worden ook gebruikt voor de rapportage.

**Abonnementen**: Een abonnement is de kleinste eenheid in de Azure EA Portal. Abonnementen zijn containers voor Azure-services die worden beheerd door de servicebeheerder.

In het volgende diagram ziet u eenvoudige Azure EA-hiërarchieën.

![Diagram van een eenvoudige Azure EA-hiërarchie](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

Voor het beheren van de Azure-services in uw inschrijving zijn er vier verschillende gebruikersrollen met beheerdersrechten voor ondernemingen:

- Ondernemingsbeheerder
- Afdelingsbeheerder
- Accounteigenaar
- Servicebeheerder

Rollen worden gebruikt om in twee verschillende Microsoft Azure-portals taken te voltooien. U kunt Azure EA Portal (https://ea.azure.com) gebruiken om uw facturering en kosten te beheren. Azure Portal (https://portal.azure.com) wordt gebruikt voor het beheren van Azure-services.

Gebruikersrollen zijn gekoppeld aan een gebruikersaccount. Voor het valideren van de authenticiteit van de gebruiker moet elke gebruiker een geldig werk-, school- of Microsoft-account hebben. Zorg ervoor dat elk account is gekoppeld aan een e-mailadres dat actief wordt gecontroleerd. Accountmeldingen worden verzonden naar het e-mailadres.

Bij het instellen van gebruikers kunt u meerdere werk-, school- of Microsoft-accounts toewijzen aan de rol ondernemingsbeheerder. U kunt echter maar één werk-, school- of Microsoft-account aan de rol van accounteigenaar toewijzen. Bovendien is het mogelijk om de rollen ondernemingsbeheerder en accounteigenaar aan één werk-, school- of Microsoft-account toe te wijzen.

### <a name="enterprise-administrator"></a>Ondernemingsbeheerder

De rol ondernemingsbeheerder heeft het hoogste toegangsniveau. Gebruikers met de rol kunnen het volgende:

- Accounts en accounteigenaren beheren
- Andere ondernemingsbeheerders beheren
- Afdelingsbeheerders beheren
- Contactpersonen voor meldingen beheren
- Gebruik voor alle accounts weergeven
- Niet-gefactureerde kosten voor alle accounts weergeven

U kunt meerdere ondernemingsbeheerders hebben voor elke Enterprise-inschrijving. U kunt alleen-lezentoegang verlenen aan ondernemingsbeheerders. Ze nemen allemaal de rol van afdelingsbeheerder over.

### <a name="department-administrator"></a>Afdelingsbeheerder

Gebruikers met de rol kunnen het volgende:

- Afdelingen maken en beheren
- Nieuwe accounteigenaren maken
- Gebruiksgegevens bekijken voor de afdelingen die ze beheren
- Kosten weer geven, indien de benodigde machtigingen zijn verleend

U kunt meerdere afdelingsbeheerders hebben voor elke Enterprise-inschrijving.

U kunt afdelingsbeheerders alleen-lezentoegang verlenen. Als u alleen-lezentoegang wilt verlenen, bewerkt u de afdelingsbeheerder of maakt u een nieuwe afdelingsbeheerder en stelt u de optie voor alleen-lezen in op **Ja**.

### <a name="account-owner"></a>Accounteigenaar

Gebruikers met de rol kunnen het volgende:

- Abonnementen maken en beheren
- Servicebeheerders beheren
- Gebruik weergeven voor abonnementen

Voor elk account is een uniek werk-, school-of Microsoft-account vereist. Zie [Azure Enterprise Agreement-beheerdersrollen in Azure begrijpen](billing-understand-ea-roles.md) voor meer informatie over Azure EA Portal-beheerdersrollen.

### <a name="service-administrator"></a>Servicebeheerder

De servicebeheerder heeft machtigingen om services te beheren in Azure Portal en gebruikers toe te wijzen aan de rol co-beheerder.

## <a name="activate-your-enrollment"></a>Uw inschrijving activeren

Als u uw service wilt activeren, opent de oorspronkelijke ondernemingsbeheerder Azure EA Portal op [ https://ea.azure.com ](https://ea.azure.com) en meldt deze zich aan met het e-mailadres in de uitnodigingsmail.

Als u meerdere inschrijvingen heeft, kiest u de inschrijving die u wilt activeren. Standaard worden alleen actieve inschrijvingen weergegeven. Als u de inschrijvingsgeschiedenis wilt weergeven, wist u de optie **Actief** in de rechterbovenhoek van Azure EA Portal.

De status onder Inschrijving is **Actief**.

![Voorbeeld van een actieve inschrijving](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Alleen bestaande Azure-ondernemingsbeheerders kunnen andere ondernemingsbeheerders maken.

### <a name="create-another-enterprise-admin"></a>Een andere ondernemingsbeheerder maken

- Meld u aan bij [Azure EA Portal](https://ea.azure.com) en ga naar **Beheren** > **Inschrijvingsgegevens** en klik in de rechterbovenhoek van de pagina op **+ Beheerder toevoegen**.

Zorg ervoor dat u de e-mailadres en de gewenste verificatiemethode van de gebruiker hebt, zoals het werk-, school- of Microsoft-account. U hebt de informatie nodig om een gebruiker toe te voegen.

Als u geen EA-beheerder bent, kunt u contact met een EA-beheerder opnemen om te vragen of u kunt worden toegevoegd aan een inschrijving. Zodra u bent toegevoegd aan een inschrijving, ontvangt u een activerings-e-mail.

Als uw EA-beheerder u niet kan helpen, maakt u een [Azure EA Portal-ondersteuningsaanvraag](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Geef de volgende informatie op:

- Inschrijvingsnummer
- E-mailadres dat moet worden toegevoegd en verificatietype (werk-, school- of Microsoft-account)
- E-mailgoedkeuring van een bestaande EA-beheerder
  - Als de bestaande EA-beheerder niet beschikbaar is, neemt u contact op met uw partner of softwareadviseur om te vragen of de contactgegevens kunnen worden gewijzigd via het hulpprogramma VLSC.

Zie [Azure Enterprise Agreement-beheerdersrollen in Azure begrijpen](billing-understand-ea-roles.md) voor meer informatie over Enterprise-beheerdersrollen.

## <a name="create-an-azure-ea-department"></a>Een Azure EA-afdeling maken

Ondernemingsbeheerders en afdelingsbeheerders gebruiken afdelingen om de zakelijke Azure-services en het gebruik per afdeling en kostenplaats te organiseren en hierover te rapporteren. De ondernemingsbeheerder kan het volgende:

- Afdelingen toevoegen of verwijderen
- Een account aan een afdeling koppelen
- Afdelingsbeheerders maken
- Afdelingsbeheerders toestaan om de prijs en kosten weer te geven

Afdelingsbeheerders kunnen nieuwe accounts toevoegen aan hun afdelingen. Ze kunnen accounts uit hun afdelingen verwijderen, maar niet uit de inschrijving.

Een afdeling toevoegen:

1. Klik in het navigatiegedeelte links op **Beheren**.
2. Klik achtereenvolgens op het tabblad **Afdeling** en op **+ Afdeling toevoegen** en voer vervolgens de vereiste informatie in.
3. Het veld Naam van afdeling is het enige verplichte veld. De naam moet minstens drie tekens bevatten.
4. Zodra u het veld hebt ingevuld, klikt u op **OK**.

## <a name="add-a-department-admin"></a>Een afdelingsbeheerder toevoegen

Nadat een afdeling is gemaakt, kan de Azure-ondernemingbeheerder afdelingsbeheerders toevoegen en deze koppelen aan een afdeling. De afdelingsbeheerder kan het volgende:

- Andere afdelingsbeheerders maken
- Afdelingseigenschappen zoals naam en kostenplaats weergeven
- Een account toevoegen voor hun afdelingen
- Accounts verwijderen voor hun afdelingen
- Gebruiksgegevens downloaden voor hun afdelingen
- Het maandelijkse verbruik en de maandelijkse kosten voor hun afdeling weergeven als de ondernemingsbeheerder hiervoor toestemming heeft verleend<sup>1</sup>

### <a name="to-add-a-department-admin"></a>Een afdelingsbeheerder toevoegen

Als een ondernemingsbeheerder:

1. Klik in het navigatiegedeelte links op **Beheren**.
2. Klik op het tabblad **Afdeling** en klik vervolgens op de afdeling.
3. Klik op **+ Beheerder toevoegen** en voeg de vereiste informatie toe.
4. Voor alleen-lezentoegang stelt u de optie **Alleen-lezen** in op **Ja** en klikt u vervolgens op **Toevoegen**.

![Een voorbeeld met het dialoogvenster Afdelingsbeheerder toevoegen](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Alleen-lezentoegang instellen

U kunt afdelingsbeheerders alleen-lezentoegang verlenen. Wanneer u een nieuwe afdelingsbeheerder maakt:

- Stel de optie voor alleen-lezen in op **Ja**.

Een bestaande afdelingsbeheerder bewerken:

1. Selecteer een afdeling en klik vervolgens op het potloodpictogram naast de **afdelingsbeheerder** die u wilt bewerken.
2. Stel de optie voor alleen-lezen in op **Ja**. Klik vervolgens op **Opslaan**.

Gebruikers met de rol ondernemingsbeheerder krijgen automatisch afdelingsbeheerdersmachtigingen.

<sup>1</sup> Als u toestemming hebt om het maandelijkse verbruik en de maandelijkse kosten weer te geven maar u deze niet kunt zien, neemt u contact op met uw partner.

## <a name="add-an-account"></a>Een account toevoegen

De structuur van accounts en abonnementen bepalen hoe ze worden beheerd en hoe ze worden weergegeven op facturen en in rapporten. Organisaties worden vaak gestructureerd op basis bedrijfsdivisies, functionele teams of geografische locaties.

Een account toevoegen:

1. Ga naar Azure EA Portal en klik in het navigatiegedeelte links op **Beheren**.
2. Klik achtereenvolgens op het tabblad **Account** en de pagina **Account**. Klik vervolgens op **+Account toevoegen**.
3. Selecteer een afdeling of zorg ervoor dat deze niet-toegewezen is, en selecteer vervolgens het gewenste verificatietype.
4. Typ een beschrijvende naam waaraan u het account in de rapportage kunt herkennen.
5. Typ het **e-mailadres van de accounteigenaar** dat u aan het nieuwe account wilt koppelen.
6. Bevestig het e-mailadres en klik vervolgens op **Toevoegen**.

![Voorbeeld met een lijst accounts en de optie +Account toevoegen](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

U kunt op **Ander account toevoegen** klikken om nog een account toe te voegen of u kunt in de rechterbenedenhoek van de linkerwerkbalk op **Toevoegen** klikken.

Eigendom van het account controleren:

1. Meld u aan bij Azure EA Portal.
2. Controleer het accounteigendom door de status weer te geven. De status wordt gewijzigd van **In behandeling** in **Begin-/einddatum**. De Begin-/einddatum is de datum waarop de gebruiker zich voor het eerst heeft aangemeld en de einddatum van de overeenkomst.


## <a name="change-account-owner"></a>Accounteigenaar wijzigen

Ondernemingsbeheerders kunnen Azure EA Portal gebruiken om het eigendom van het abonnementsaccount in een inschrijving over te dragen. Met deze actie worden alle abonnementen verplaatst van een brongebruikersaccount naar een doelgebruikersaccount.

Belangrijke punten voor het overdragen van gebruikersaccountgegevens:

- Overdrachten van een werk- of schoolaccount naar een ander werk- of schoolaccount worden ondersteund.
- Overdrachten van een Microsoft-account naar een werk- of schoolaccount worden ondersteund.
- Overdrachten van een werk- of schoolaccount naar een Microsoft-account worden niet ondersteund.
- Overdrachten van een Microsoft-account naar een ander Microsoft-account worden ondersteund. Het doelaccount moet een geldig Azure Commerce-account zijn om als geldig doel voor overdrachten te fungeren. Voor nieuwe accounts wordt u gevraagd een Azure Commerce-account te maken wanneer u zich aanmeldt bij de Azure EA Portal. Voor bestaande accounts moet u eerst een nieuw Azure-abonnement maken voordat het account kan worden overgedragen.
- Zodra u een abonnementsoverdracht hebt voltooid, wordt de eigenaar van het account door Microsoft bijgewerkt.

RBAC-beleid:

- Alleen bij Azure-abonnementsoverdrachten tussen twee organisatie-id's in dezelfde tenant blijven het bestaande RBAC-beleid van Azure (op rollen gebaseerd toegangsbeheer) en de roltoewijzingen voor servicebeheerder en co-beheerder behouden. Andere abonnementsoverdrachten leiden ertoe dat uw RBAC-beleid en de roltoewijzingen voor servicebeheerder en co-beheerder verloren gaan. Beleidsregels en beheerdersrollen kunnen niet worden overgedragen naar andere mappen. Servicebeheerders worden bijgewerkt naar eigenaar van het doelaccount.
- Wanneer u abonnementsoverdrachten tussen twee organisatie-id's in dezelfde tenant uitvoert, blijven het RBAC-beleid en de bestaande servicebeheerders- en co-beheerdersrollen bewaard.

Voordat u de accounteigenaar wijzigt:

1. Geef het tabblad **Account** weer en identificeer het bronaccount. Het bronaccount moet actief zijn.
2. Identificeer het doelaccount. Dit account moet actief zijn.

Accounteigendom overdragen voor alle abonnementen:

1. Klik in het navigatiegedeelte links op **Beheren**.
2. Klik op het tabblad **Account** en plaats de muisaanwijzer op een account.
3. Klik aan de rechterkant op het symbool voor het wijzigen van de accounteigenaar. Het symbool lijkt op een persoon.
4. Selecteer een geschikt account en klik op **Volgende**.
5. Bevestig de overdracht en klik op **Verzenden**.

![Afbeelding van het symbool Accounteigenaar wijzigen](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Accounteigendom overdragen voor één abonnementen:

1. Klik in het navigatiegedeelte links op **Beheren**.
2. Klik op het tabblad **Account** en plaats de muisaanwijzer op een account.
3. Klik aan de rechterkant op het symbool voor het overdragen van abonnementen. Het symbool lijkt op een pagina.
4. Selecteer een geschikt abonnement en klik op **Volgende**.
5. Bevestig de overdracht en klik op **Verzenden**.

![Afbeelding met het symbool Abonnementen overdragen](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Hier volgt een video over het gebruikersbeheer in Azure EA Portal:

[Video over gebruikersbeheer in Azure EA Portal](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Een abonnement maken

Accounteigenaren kunnen abonnementen weergeven en beheren. U kunt abonnementen gebruiken om teams in uw organisatie toegang te geven tot ontwikkelomgevingen en projecten. Bijvoorbeeld test, productie, ontwikkeling en fasering. Wanneer u verschillende abonnementen voor elke toepassingsomgeving maakt, draagt u bij aan de beveiliging van elke omgeving. U kunt ook een ander servicebeheerdersaccount toewijzen voor elk abonnement. U kunt abonnementen aan elk gewenst aantal services koppelen. De accounteigenaar maakt abonnementen en wijst een servicebeheerdersaccount toe aan elk abonnement in zijn account.

### <a name="add-a-subscription"></a>Een abonnement toevoegen

Gebruik de volgende informatie om een abonnement toe te voegen.

De eerste keer dat u een abonnement aan uw account toevoegt, wordt u gevraagd de MOSA-overeenkomst en een tariefplan te accepteren. Hoewel ze niet van toepassing zijn op Enterprise Agreement-klanten, zijn ze nodig om uw abonnement te maken. Uw aangepaste Microsoft Azure Enterprise Agreement-inschrijving vervangt de bovenstaande items en uw contractuele relatie blijft ongewijzigd. Schakel desgevraagd het selectievakje in om de voorwaarden te accepteren.

Alle nieuwe abonnementen worden gemaakt met de standaard abonnementsnaam van _Microsoft Azure Enterprise_. U kunt de naam van het abonnement bijwerken om deze te onderscheiden van de andere abonnementen in uw inschrijving. En om ervoor te zorgen dat het abonnement herkenbaar is in de rapporten op ondernemingsniveau.

Een abonnement toevoegen:

1. Meld u in Azure EA Portal aan bij het account.
2. Klik op het tabblad **Beheerder** en klik bovenaan de pagina vervolgens op **Abonnement**.
2. Controleer of u bent aangemeld als de eigenaar van het account.
3. Klik op **+Abonnement toevoegen** en klik vervolgen op **Kopen**.
  De eerste keer dat u een abonnement aan een account toevoegt, moet u uw contactgegevens opgeven. Wanneer u extra abonnementen toevoegt, worden uw contactgegevens voor u toegevoegd.
4. Klik op **Abonnementen**, selecteer het abonnement dat u hebt gemaakt en klik vervolgens op **Abonnementsdetails bewerken**.
5. Werk de velden **Naam abonnement** en **Servicebeheerder** bij en selecteer vervolgens het vinkje.
  De naam van het abonnement wordt weergegeven in rapporten en is de naam van het project dat is gekoppeld aan het abonnement in de ontwikkelingsportal.

Het kan tot 24 uur duren voordat nieuwe abonnementen worden weergegeven in de lijst abonnementen. Zodra u een abonnement hebt gemaakt, kunt u het volgende:

- [Abonnementsgegevens bewerken](https://account.azure.com/Subscriptions)
- [Abonnementsservices beheren](https://portal.azure.com/#home)

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Een abonnement op gebruiksbasis overdragen naar een EA-abonnement

Als u een afzonderlijk abonnement dat wordt betaald naar gebruik, wilt overdragen naar een EA-abonnement, moet u een nieuwe ondersteuningsaanvraag maken in Azure Portal. Als u een ondersteuningsaanvraag wilt maken, klikt u in het gedeelte Help en ondersteuning op **+ Nieuwe ondersteuningsaanvraag** .


## <a name="view-usage-summary-and-download-reports"></a>Gebruiksoverzicht weergeven en rapporten downloaden

Ondernemingsbeheerders kunnen een overzicht van hun gebruiksgegevens, verbruikte financiële toezegging en de kosten voor aanvullend gebruik in Azure EA Portal weergeven. De kosten worden weergegeven op overzichtsniveau voor alle accounts en abonnementen.

Gedetailleerd gebruik voor specifieke accounts weergeven

Download het rapport Gebruiksgegevens. Klik op **Rapporten** en klik vervolgens op het tabblad **Gebruiksgegevens downloaden**. Klik in de lijst met rapporten op **Downloaden** voor het maandelijkse rapport dat u wilt downloaden.

Het rapport bevat geen toepasselijke belastingen. Er duurt maximaal acht uur voordat het gebruik wordt weergegeven in het rapport.

Rapporten en grafieken met het gebruiksoverzicht weergeven:

1. Ga naar Azure EA Portal en klik in het navigatiegedeelte aan de linkerkant op **Rapporten** en bekijk het tabblad **Gebruiksoverzicht**.  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Selecteer een toezeggingstermijn.
3. Schakel in de rechterbovenhoek van de pagina tussen **M** (Maandelijks) en **A** (Aangepast) om het **Gebruiksoverzicht** weer te geven met een aangepaste begin- en einddatum.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Selecteer een periode of maand in de grafiek om aanvullende gegevens weer te geven.
5. De grafiek toont het MoM-gebruik (maand over maand), uitgesplitst in ingezet voor gebruik, te veel in rekening gebrachte service, afzonderlijke gefactureerde kosten en Marketplace-kosten.
6. Filter de geselecteerde maand onder de grafiek op afdelingen, accounts en abonnementen.
7. Schakel tussen **Kosten per services** en **Kosten per hiërarchie**.
8. U kunt **Azure-service**, **Afzonderlijk gefactureerde kosten** en **Azure Marketplace** uit- en samenvouwen om de details weer te geven.

Hier volgt een video waarin wordt uitgelegd hoe u het gebruik kunt weergeven:

[Video over het gebruik van Azure EA Portal](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-rapporten downloaden

Ondernemingsbeheerders gebruiken de pagina Maandelijks rapport downloaden om verschillende rapporten te downloaden als CSV-bestanden. Deze omvatten:

- Saldo en kosten
- Gebruiksgegevens
- Marketplace-kosten
- Prijzenoverzicht

Rapporten downloaden:


1. Klik in Azure EA Portal op **Rapporten**.
2. Klik bovenaan de pagina op **Gebruiksgegevens downloaden**.
3. Selecteer naast het maandrapport de optie **Downloaden**.

Het kan na de werkelijke gebruiksdatum nog maximaal vijf dagen duren voordat het gebruik wordt weergegeven in de rapporten.

Gebruikers die CSV-bestanden met Safari downloaden naar Excel, ervaren mogelijk indelingsfouten. Open het bestand met een teksteditor om fouten te voorkomen.

![Voorbeeld met de pagina Gebruiksgegevens downloaden](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Hier volgt een video waarin wordt uitgelegd hoe u gebruiksgegevens downloadt:

[Video over het gebruik van Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Een telefonisch onboardinggesprek plannen

Als u een persoonlijke onboardingsessie met een klant wilt plannen, kunt u een ondersteuningsaanvraag indienen via [Azure EA Portal Support](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Selecteer **Onboarding** als **Probleemcategorie**.

## <a name="next-steps"></a>Volgende stappen
- Azure EA Portal-beheerders moeten [Beheer van Azure EA Portal](billing-ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
- Zie [Problemen met toegang tot Azure EA Portal oplossen](billing-ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen met Azure EA Portal.
