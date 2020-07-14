---
title: Aan de slag met de Azure Enterprise-portal
description: In dit artikel wordt uitgelegd hoe klanten van de Azure Enterprise Agreement (Azure EA) de Azure Enterprise-portal kunnen gebruiken.
author: bandersmsft
ms.author: banders
ms.date: 06/29/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 6b31f3cd8ca6baa2d20d0281de51ed83424eca17
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560356"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Aan de slag met de Azure Enterprise-portal

In dit artikel wordt uitgelegd hoe directe en indirecte klanten van de Azure Enterprise Agreement (Azure EA) aan de slag kunnen gaan met de [Azure Enterprise-portal](https://ea.azure.com). U vindt hier algemene informatie over:

- De indeling van de Azure Enterprise-portal.
- Welke rollen in de Azure Enterprise-portal worden gebruikt.
- Hoe u abonnementen maakt.
- Kostenanalyse in de Azure Enterprise-portal en de Azure-portal.

Bekijk deze video voor een volledige onboardingsessie van de Azure Enterprise-portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Hiërarchie van de Azure Enterprise-portal

De hiërarchie van de Azure Enterprise-portal bestaat uit de volgende elementen:

- **Azure Enterprise-portal**: een online beheerportal waarmee u de kosten van uw Azure EA-services kunt beheren. U kunt:

  - Een Azure EA-hiërarchie maken met afdelingen, accounts en abonnementen.
  - De kosten van de gebruikte services afstemmen, gebruiksrapporten downloaden en prijslijsten bekijken.
  - API-sleutels voor uw inschrijving maken.

- Met **afdelingen** kunt u de kosten segmenteren in logische groepen. U kunt een budget of quotum instellen op afdelingsniveau.

- **Accounts** zijn organisatie-eenheden in de Azure Enterprise-portal. U kunt accounts gebruiken om abonnementen te beheren en rapporten te bekijken.

- **Abonnementen** vormen de kleinste eenheid in de Azure Enterprise-portal. Abonnementen zijn containers voor Azure-services die worden beheerd door de servicebeheerder.

In het volgende diagram ziet u eenvoudige Azure EA-hiërarchieën.

![Diagram van een eenvoudige Azure EA-hiërarchie](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

De volgende typen beheerdersgebruikers maken deel uit van uw Enterprise-inschrijving:

- Ondernemingsbeheerder
- Afdelingsbeheerder
- Accounteigenaar
- Servicebeheerder
- Contactpersoon voor meldingen

Rollen zijn in twee verschillende portals actief om taken te voltooien. U gebruikt de [Azure Enterprise-portal](https://ea.azure.com) om facturering en kosten te beheren. In de [Azure-portal](https://portal.azure.com) beheert u Azure-services.

Gebruikersrollen zijn gekoppeld aan een gebruikersaccount. Voor het valideren van de authenticiteit van de gebruiker moet elke gebruiker een geldig werk-, school- of Microsoft-account hebben. Zorg ervoor dat elk account is gekoppeld aan een e-mailadres dat actief wordt gecontroleerd. Accountmeldingen worden verzonden naar het e-mailadres.

Als u gebruikers gaat instellen, kunt u meerdere accounts toewijzen aan de rol van ondernemingsbeheerder. De rol van accounteigenaar kan slechts aan één account worden toegewezen. Het is ook mogelijk om de rol van ondernemingsbeheerder en accounteigenaar aan hetzelfde account toe te wijzen.

### <a name="enterprise-administrator"></a>Ondernemingsbeheerder

Gebruikers met deze rol hebben het hoogste toegangsniveau. Ze kunnen:

- Accounts en accounteigenaren beheren.
- Andere ondernemingsbeheerders beheren.
- Afdelingsbeheerders beheren.
- Contactpersonen voor meldingen beheren.
- Gebruik voor alle accounts weergeven.
- Niet-gefactureerde kosten voor alle accounts weergeven.

U kunt meerdere ondernemingsbeheerders hebben voor elke Enterprise-inschrijving. U kunt alleen-lezentoegang verlenen aan ondernemingsbeheerders. Ze nemen allemaal de rol van afdelingsbeheerder over.

### <a name="department-administrator"></a>Afdelingsbeheerder

Gebruikers met de rol kunnen:

- Afdelingen maken en beheren.
- Nieuwe accounteigenaren maken.
- Gebruiksgegevens bekijken voor de afdelingen die ze beheren.
- Kosten bekijken, indien ze over de benodigde machtigingen beschikken.

U kunt meerdere afdelingsbeheerders hebben voor elke Enterprise-inschrijving.

U kunt afdelingsbeheerders alleen-lezen toegang geven wanneer u een afdelingsbeheerder maakt of bewerkt. Stel de optie voor alleen-lezen in op **Ja**.

### <a name="account-owner"></a>Accounteigenaar

Gebruikers met de rol kunnen:

- Abonnementen maken en beheren.
- Servicebeheerders beheren.
- Gebruik weergeven voor abonnementen.

Voor elk account is een uniek werk-, school-of Microsoft-account vereist. Zie [Inzicht in Azure Enterprise Agreement-beheerdersrollen in Azure](understand-ea-roles.md) voor meer informatie over beheerdersrollen voor de Azure Enterprise-portal.

### <a name="service-administrator"></a>Servicebeheerder

De rol Servicebeheerder heeft machtigingen om services te beheren in de Azure-portal en gebruikers toe te wijzen aan de rol Co-beheerder.

### <a name="notification-contact"></a>Contactpersoon voor meldingen

De contactpersoon voor meldingen ontvangt gebruiksmeldingen met betrekking tot de inschrijving.

## <a name="activate-your-enrollment"></a>Uw inschrijving activeren

Als u uw service wilt activeren, moet de oorspronkelijke ondernemingsbeheerder de [Azure Enterprise-portal](https://ea.azure.com) openen en zich aanmelden met het e-mailadres in de uitnodigingsmail.

Als u zeker weet dat u bent ingesteld als ondernemingsbeheerder, hoeft u niet te wachten tot u de activeringsmail hebt ontvangen. Ga naar de [Azure Enterprise-portal](https://ea.azure.com) en meld u aan met het e-mailadres en wachtwoord van uw werk-, school- of Microsoft-account.

Als u meerdere inschrijvingen heeft, kiest u de inschrijving die u wilt activeren. Standaard worden alleen actieve inschrijvingen weergegeven. Als u de inschrijvingsgeschiedenis wilt weergeven, wist u de optie **Actief** rechtsboven in de Azure Enterprise-portal.

De status onder **Inschrijving** is **Actief**.

![Voorbeeld van een actieve inschrijving](./media/ea-portal-get-started/ea-enrollment-status.png)

Alleen bestaande Azure-ondernemingsbeheerders kunnen andere ondernemingsbeheerders maken.

### <a name="create-another-enterprise-administrator"></a>Een andere ondernemingsbeheerder maken

Ga als volgt te werk om een extra ondernemingsbeheerder toe te voegen:

1. Meld u aan bij de [Azure Enterprise-portal](https://ea.azure.com).
1. Ga naar **Beheren** > **Details van inschrijving**.
1. Selecteer rechtsboven **+ Beheerder toevoegen**.

Zorg ervoor dat u het e-mailadres en de gewenste verificatiemethode van de gebruiker weet, zoals het werk-, school- of Microsoft-account.

Als u geen ondernemingsbeheerder bent, neemt u contact op met een ondernemingsbeheerder om te vragen of u kunt worden toegevoegd aan een inschrijving. Zodra u bent toegevoegd aan een inschrijving, ontvangt u een activerings-e-mail.

Als de ondernemingsbeheerder u niet kan helpen, maakt u een [ondersteuningsaanvraag voor de Azure Enterprise-portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Geef de volgende informatie op:

- Inschrijvingsnummer
- E-mailadres dat moet worden toegevoegd en verificatietype (werk-, school- of Microsoft-account)
- E-mailgoedkeuring van een bestaande ondernemingsbeheerder
  - Als de bestaande ondernemingsbeheerder niet beschikbaar is, neemt u contact op met uw partner of softwareadviseur om te vragen of de contactgegevens kunnen worden gewijzigd via het hulpprogramma Servicecentrum Volumelicenties.

Zie [Azure Enterprise Agreement-beheerdersrollen in Azure begrijpen](understand-ea-roles.md) voor meer informatie over Enterprise-beheerdersrollen.

## <a name="create-an-azure-enterprise-department"></a>Een Azure Enterprise-afdeling maken

Ondernemingsbeheerders en afdelingsbeheerders gebruiken afdelingen om de zakelijke Azure-services en het gebruik per afdeling en kostenplaats te organiseren en hierover te rapporteren. De ondernemingsbeheerder kan het volgende:

- Afdelingen toevoegen of verwijderen.
- Een account aan een afdeling koppelen.
- Afdelingsbeheerders maken.
- Afdelingsbeheerders toestaan om de prijs en kosten weer te geven.

Afdelingsbeheerders kunnen nieuwe accounts toevoegen aan hun afdelingen. Ze kunnen accounts uit hun afdelingen verwijderen, maar niet uit de inschrijving.

Een afdeling toevoegen:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het linkerdeelvenster de optie **Beheren**.
1. Selecteer het tabblad **Afdeling** en vervolgens **+ Afdeling toevoegen**.
1. Voer de gegevens in.
   Het veld Naam van afdeling is het enige verplichte veld. De naam moet minstens drie tekens bevatten.
1. Als u klaar bent, selecteert u **Toevoegen**.

## <a name="add-a-department-administrator"></a>Een afdelingsbeheerder toevoegen

Nadat een afdeling is gemaakt, kan de ondernemingsbeheerder afdelingsbeheerders toevoegen en deze koppelen aan een afdeling. Afdelingsbeheerders kunnen de volgende acties uitvoeren voor hun afdeling:

- Andere afdelingsbeheerders maken
- Afdelingseigenschappen zoals naam en kostenplaats bekijken en bewerken
- Accounts toevoegen
- Accounts verwijderen
- Gebruiksgegevens downloaden
- Het maandelijkse gebruik en de kosten bekijken<sup>1</sup>

> <sup>1</sup> Een ondernemingsbeheerder moet deze machtigingen verlenen. Als u toestemming hebt om het maandelijkse gebruik en de maandelijkse kosten weer te geven maar u deze niet kunt zien, neemt u contact op met uw partner.

### <a name="to-add-a-department-administrator"></a>Een afdelingsbeheerder toevoegen

Als een ondernemingsbeheerder:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het linkerdeelvenster de optie **Beheren**.
1. Selecteer het tabblad **Afdeling** en selecteer vervolgens de betreffende afdeling.
1. Selecteer **+ Beheerder toevoegen** en voeg de vereiste informatie toe.
1. Voor alleen-lezen toegang stelt u de optie **Alleen-lezen** in op **Ja** en selecteert u vervolgens **Toevoegen**.

![Voorbeeld met het dialoogvenster Afdelingsbeheerder toevoegen](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Alleen-lezentoegang instellen

U kunt afdelingsbeheerders alleen-lezentoegang verlenen.

- Als u een nieuwe afdelingsbeheerder maakt, stelt u de optie Alleen-lezen in op **Ja**.

- Een bestaande afdelingsbeheerder bewerken:
   1. Selecteer een afdeling en selecteer vervolgens het potloodpictogram naast de **afdelingsbeheerder** die u wilt bewerken.
   1. Stel de optie Alleen-lezen in op **Ja** en selecteer **Opslaan**.

Ondernemingsbeheerders krijgen automatisch machtigingen van afdelingsbeheerders.

## <a name="add-an-account"></a>Een account toevoegen

De indeling van accounts en abonnementen bepaalt hoe ze worden beheerd en hoe ze worden weergegeven op facturen en in rapporten. Organisaties worden vaak gestructureerd op basis van bedrijfsdivisies, functionele teams en geografische locaties.

Een account toevoegen:

1. Selecteer in de Azure Enterprise-portal de optie **Beheren** in het linkernavigatievenster.
1. Selecteer het tabblad **Account**. Selecteer op de pagina **Account** de optie **+ Account toevoegen**.
1. Selecteer een afdeling of zorg ervoor dat deze niet toegewezen is en selecteer vervolgens het gewenste verificatietype.
1. Geef een beschrijvende naam op waaraan u het account in de rapportage kunt herkennen.
1. Geef het **e-mailadres van de accounteigenaar** op dat u aan het nieuwe account wilt koppelen.
1. Bevestig het e-mailadres en selecteer **Toevoegen**.

![Voorbeeld met een lijst accounts en de optie +Account toevoegen](./media/ea-portal-get-started/create-ea-add-an-account.png)

Als u nog een account wilt toevoegen, selecteert u **Nog een account toevoegen** of selecteert u rechtsonder in de linkerwerkbalk de optie **Toevoegen**.

Eigendom van het account controleren:

1. Meld u aan bij de Azure Enterprise-portal.
1. Bekijk de status.

   De status wordt gewijzigd van **In behandeling** in **Begin-/einddatum**. De Begin-/einddatum is de datum waarop de gebruiker zich voor het eerst heeft aangemeld en de einddatum van de overeenkomst.
1. Zodra het pop-upbericht **Waarschuwing** wordt weergegeven, moet de accounteigenaar **Doorgaan** selecteren om het account te activeren wanneer deze zich voor de eerste keer bij de Azure Enterprise-portal aanmeldt.

## <a name="change-account-owner"></a>Accounteigenaar wijzigen

Ondernemingsbeheerders kunnen de Azure Enterprise-portal gebruiken om het eigendom van het abonnementsaccount in een inschrijving over te dragen. Met deze actie worden alle abonnementen verplaatst van een brongebruikersaccount naar een doelgebruikersaccount.

Houd rekening met deze belangrijke informatie als u accounts gaat overdragen:

- U kunt de volgende overdrachten uitvoeren:
  - Van een werk- of schoolaccount naar een ander werk- of schoolaccount.
  - Van een Microsoft-account naar een werk- of schoolaccount.
  - Van een Microsoft-account naar een ander Microsoft-account.

    Het doelaccount moet een geldig Azure Commerce-account zijn om als geldig doel voor overdrachten te fungeren. Voor nieuwe accounts wordt u gevraagd een Azure Commerce-account te maken wanneer u zich aanmeldt bij de Azure Enterprise-portal. Voor bestaande accounts moet u eerst een nieuw Azure-abonnement maken voordat het account kan worden overgedragen.

- U kunt geen overdracht uitvoeren van een werk- of schoolaccount naar een Microsoft-account.

- Zodra u een abonnementsoverdracht hebt voltooid, wordt de eigenaar van het account door Microsoft bijgewerkt.

Krijg inzicht in deze beleidsmaatregelen voor op rollen gebaseerd toegangsbeheer (RBAC):

- Wanneer u abonnementsoverdrachten tussen twee organisatie-id's in dezelfde tenant uitvoert, blijven het RBAC-beleid en de bestaande servicebeheerders- en co-beheerdersrollen bewaard.
- Andere abonnementsoverdrachten leiden ertoe dat uw RBAC-beleid en de roltoewijzingen verloren gaan.
- Beleidsregels en beheerdersrollen kunnen niet worden overgedragen naar andere mappen. Servicebeheerders worden bijgewerkt naar eigenaar van het doelaccount.

Voordat u de accounteigenaar wijzigt:

1. Ga in de Azure Enterprise-portal naar het tabblad **Account** en identificeer het bronaccount. Het bronaccount moet actief zijn.
1. Identificeer het doelaccount en controleer of het actief is.

Accounteigendom overdragen voor alle abonnementen:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het navigatiegedeelte aan de linkerkant de optie **Beheren**.
1. Selecteer het tabblad **Account** en plaats de muisaanwijzer op een account.
1. Selecteer aan de rechterkant het pictogram voor het wijzigen van de accounteigenaar. Het pictogram lijkt op een persoon.
1. Kies een geschikt account en selecteer **Volgende**.
1. Bevestig de overdracht en selecteer **Verzenden**.

![Afbeelding van het symbool Accounteigenaar wijzigen](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Accounteigendom overdragen voor één abonnementen:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het navigatiegedeelte aan de linkerkant de optie **Beheren**.
1. Selecteer het tabblad **Account** en plaats de muisaanwijzer op een account.
1. Selecteer aan de rechterkant het pictogram voor abonnementsoverdracht. Het pictogram lijkt op een pagina.
1. Kies een geschikt abonnement en selecteer **Volgende**.
1. Bevestig de overdracht en selecteer **Verzenden**.

![Afbeelding met het symbool Abonnementen overdragen](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Bekijk deze video over gebruikersbeheer in de Azure Enterprise-portal:

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Een abonnement maken

Accounteigenaren kunnen abonnementen weergeven en beheren. U kunt abonnementen gebruiken om teams in uw organisatie toegang te geven tot ontwikkelomgevingen en projecten. Bijvoorbeeld: test, productie, ontwikkeling en fasering.

Wanneer u verschillende abonnementen voor elke toepassingsomgeving maakt, draagt u bij aan de beveiliging van elke omgeving.

- U kunt ook een ander servicebeheerdersaccount toewijzen voor elk abonnement.
- U kunt abonnementen aan elk gewenst aantal services koppelen.
- De accounteigenaar maakt abonnementen en wijst een servicebeheerdersaccount toe aan elk abonnement in zijn account.

### <a name="add-a-subscription"></a>Een abonnement toevoegen

Gebruik de volgende informatie om een abonnement toe te voegen.

De eerste keer dat u een abonnement aan uw account toevoegt, wordt u gevraagd de Microsoft Online Subscription Overeenkomst (MOSA) en een tariefplan te accepteren. Hoewel ze niet van toepassing zijn op Enterprise Agreement-klanten, zijn de MOSA en het tariefplan vereist om uw abonnement te maken. Uw aangepaste Microsoft Azure Enterprise Agreement-inschrijving vervangt de bovenstaande items en uw contractuele relatie blijft ongewijzigd. Schakel desgevraagd het selectievakje in om de voorwaarden te accepteren.

Als u een abonnement maakt, krijgt dit standaard de naam _Microsoft Azure Enterprise_. U kunt de naam wijzigen om het abonnement te onderscheiden van andere abonnementen in de inschrijving en zodat het kan worden herkend in rapporten op ondernemingsniveau.

Een abonnement toevoegen:

1. Meld u in de Azure Enterprise-portal aan bij het account.
1. Selecteer het tabblad **Beheerder** en selecteer vervolgens bovenaan de pagina **Abonnement**.
1. Controleer of u bent aangemeld als de eigenaar van het account.
1. Selecteer **+Abonnement toevoegen** en vervolgens **Aanschaffen**.

   De eerste keer dat u een abonnement aan een account toevoegt, moet u uw contactgegevens opgeven. Als u extra abonnementen toevoegt, worden uw contactgegevens voor u toegevoegd.

1. Selecteer **Abonnementen** en selecteer vervolgens het abonnement dat u hebt gemaakt.
1. Selecteer **Abonnementsgegevens bewerken**.
1. Bewerk de velden **Abonnementsnaam** en **Servicebeheerder** en selecteer vervolgens het vinkje.

   De abonnementsnaam wordt weergegeven op de rapporten. De naam komt overeen met het project dat is gekoppeld aan het abonnement in de ontwikkelingsportal.

Het kan tot 24 uur duren voordat nieuwe abonnementen worden weergegeven in de lijst abonnementen. Zodra u een abonnement hebt gemaakt, kunt u het volgende:

- [Abonnementsgegevens bewerken](https://account.azure.com/Subscriptions)
- [Abonnementsservices beheren](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Een ondernemingsabonnement overzetten naar een abonnement met betalen per gebruik

Als u een ondernemingsabonnement wilt overzetten naar een afzonderlijk abonnement dat wordt betaald naar gebruik, moet u een nieuwe ondersteuningsaanvraag maken in de Azure Enterprise-portal. Ga naar het gedeelte **Help en ondersteuning** en selecteer **+ Nieuwe ondersteuningsaanvraag** om een ondersteuningsaanvraag te maken.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Een bestaand account koppelen aan uw abonnement dat wordt betaald per gebruik

Als u al over een bestaand Microsoft Azure-account in de Azure-portal beschikt, geeft u het bijbehorende school-, werk- of Microsoft-account op om dit aan uw Enterprise Agreement-inschrijving te koppelen.

### <a name="associate-an-existing-account"></a>Een bestaand account koppelen

1. Selecteer in de Azure Enterprise-portal de optie **Beheren**.
1. Selecteer het tabblad **Account**.
1. Selecteer **+Een account toevoegen**.
1. Geef het werk-, school- of Microsoft-account op dat aan het bestaande Azure-account is gekoppeld.
1. Bevestig dat het account is gekoppeld aan het bestaande Azure-account.
1. Geef een naam op die u wilt gebruiken om dit account te identificeren in de rapportfunctie.
1. Selecteer **Toevoegen**.
1. Als u nog een account wilt toevoegen, selecteert u nogmaals de optie **+Een account toevoegen** of gaat u terug naar de startpagina door de knop **Beheerder** te selecteren.
1. Als u de pagina **Account** bekijkt, wordt het zojuist toegevoegde account weergegeven met de status **In behandeling**.

### <a name="confirm-account-ownership"></a>Eigendom van het account bevestigen

1. Meld u aan met het e-mailaccount dat is gekoppeld aan het werk-, school- of Microsoft-account dat u hebt opgegeven.
1. Open de e-mailmelding met de titel _Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing_ (Uitnodiging voor het activeren van uw account in de Microsoft Azure-service van Microsoft Volume Licensing).
1. Selecteer de link **Log into the Microsoft Azure Enterprise Portal** (Aanmelden bij de Microsoft Azure Enterprise-portal) in de uitnodiging.
1. Selecteer **Aanmelden**.
1. Geef uw werk-, school- of Microsoft-account en wachtwoord op om u aan te melden en te bevestigen dat u de accounteigenaar bent.

### <a name="azure-marketplace"></a>Azure Marketplace

Hoewel de meeste abonnementen vanuit een omgeving waarin wordt betaald per gebruik, kunnen worden omgezet naar een Azure Enterprise Agreement, geldt dit niet voor Azure Marketplace-services. Als u in één weergave alle abonnementen en kosten wilt kunnen zien, is het raadzaam de Azure Marketplace-services aan de Azure Enterprise-portal toe te voegen.

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Inschrijving**.
1. Bekijk het gedeelte **Details van inschrijving**.
1. Selecteer rechts naast het veld Azure Marketplace het potloodpictogram om de functie in te schakelen. Selecteer **Opslaan**.

De accounteigenaar kan nu Azure Marketplace-services aanschaffen die hij of zij voorheen als abonnementen met betalen per gebruik in eigendom had.

Nadat de nieuwe Azure Marketplace-abonnementen onder uw Azure EA-inschrijving zijn geactiveerd, moet u de Marketplace-services opzeggen die in de omgeving voor betalen per gebruik zijn gemaakt. Dit is een essentiële stap om ervoor te zorgen dat uw Azure Marketplace-abonnementen geen slechte status krijgen wanneer uw betaalmiddel voor betalen per gebruik verloopt.

### <a name="msdn"></a>MSDN

MSDN-abonnementen worden automatisch omgezet naar MSDN Dev/Test. Eventueel bestaand geldtegoed van de Azure EA-aanbieding gaat verloren.

### <a name="azure-in-open"></a>Azure in Open

Als u een Azure in Open-abonnement koppelt aan een Enterprise Agreement, raakt u ongebruikt Azure in Open-tegoed kwijt. We raden daarom aan al het tegoed van het Azure in Open-abonnement te gebruiken voordat u het account toevoegt aan uw Enterprise Agreement.  

### <a name="accounts-with-support-subscriptions"></a>Accounts met ondersteuningsabonnementen

Als uw Enterprise Agreement geen ondersteuningsabonnement heeft en u een bestaand account met ondersteuningsabonnement toevoegt aan de Azure Enterprise-portal, wordt uw MOSA-ondersteuningsabonnement niet automatisch overgezet. U moet tijdens de respijtperiode (voor het einde van de volgende maand) een nieuw ondersteuningsabonnement in Azure EA aanschaffen.

## <a name="view-usage-summary-and-download-reports"></a>Gebruiksoverzicht weergeven en rapporten downloaden

Ondernemingsbeheerders kunnen een overzicht van hun gebruiksgegevens, gebruikte financiële toezegging en de kosten voor aanvullend gebruik bekijken in de Azure Enterprise-portal. De kosten worden weergegeven op overzichtsniveau voor alle accounts en abonnementen.

Als u het gedetailleerde gebruik voor specifieke accounts wilt bekijken, downloadt u het gebruiksgegevensrapport:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer **Rapporten**.
1. Selecteer het tabblad **Gebruiksgegevens downloaden**.
1. Selecteer in de lijst met rapporten de optie **Downloaden** voor het maandelijkse rapport dat u wilt downloaden.

   > [!NOTE]
   > Het gebruiksgegevensrapport bevat geen toepasselijke belastingen.
   >
   > Het duurt maximaal acht uur voordat gebruik wordt weergegeven in het rapport.

U kunt als volgt rapporten en grafieken met het gebruiksoverzicht weergeven:

1. Meld u aan bij de Azure Enterprise-portal.

1. Selecteer een toezeggingstermijn.

   Als u de periode voor **Gebruiksoverzicht** wilt wijzigen, schakelt u rechtsboven van **M** (Maandelijks) naar **A** (Aangepast). Geef vervolgens de aangepaste begin- en einddatums op.

   ![Gebruiksoverzicht maken en weergeven en rapporten downloaden in aangepaste weergave](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Selecteer een periode of maand in de grafiek om aanvullende gegevens weer te geven.

   - De grafiek toont het MoM-gebruik (maand over maand), uitgesplitst in ingezet voor gebruik, te veel in rekening gebrachte service, afzonderlijke gefactureerde kosten en Azure Marketplace-kosten.
   - U kunt voor de geselecteerde maand gebruikmaken van de velden onder de grafiek om te filteren op afdelingen, accounts en abonnementen.
   - U kunt schakelen tussen **Kosten per services** en **Kosten per hiërarchie**.
   - U kunt gegevens over **Azure-services**, **afzonderlijk gefactureerde kosten** en **Azure Marketplace** bekijken door de betreffende gedeelten uit te breiden.

Bekijk deze video voor meer informatie over hoe u gebruik kunt weergeven:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-rapporten downloaden

Ondernemingsbeheerders gebruiken de pagina Maandelijks rapport downloaden om de volgende rapporten te downloaden als CSV-bestanden:

- Saldo en kosten
- Gebruiksgegevens
- Kosten voor Azure Marketplace
- Prijzenoverzicht

Rapporten downloaden:

1. Selecteer in de Azure Enterprise-portal **Rapporten**.
2. Selecteer bovenaan de pagina **Gebruiksgegevens downloaden**.
3. Selecteer naast het maandrapport de optie **Downloaden**.

   > [!NOTE]
   > Het kan na de werkelijke gebruiksdatum nog maximaal vijf dagen duren voordat het gebruik wordt weergegeven in de rapporten.
   >
   > Gebruikers die CSV-bestanden met Safari downloaden naar Excel, ervaren mogelijk indelingsfouten. Open het bestand met een teksteditor om fouten te voorkomen.

![Voorbeeld met de pagina Gebruiksgegevens downloaden](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Bekijk deze video waarin wordt uitgelegd hoe u gebruiksgegevens downloadt:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Geavanceerde rapporten downloaden

Gebruik de optie Geavanceerde rapporten downloaden om rapporten te downloaden voor specifieke perioden of accounts. Het uitvoerbestand heeft de indeling CSV, waardoor het geschikt is voor grote recordsets.

1. Selecteer in de Azure Enterprise-portal **Geavanceerde rapporten downloaden**.
1. Selecteer de relevante periode en accounts.
1. Selecteer **Gebruiksgegevens aanvragen**.
1. Selecteer de knop **Vernieuwen** totdat de rapportstatus wordt bijgewerkt naar **Downloaden**.
1. Download het rapport.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Gebruiksrapporten en factureringsgegevens downloaden voor een eerdere inschrijving

U kunt gebruiksrapporten en factureringsgegevens voor een eerdere inschrijving downloaden nadat de inschrijving is overgedragen. Historische rapportage is beschikbaar in de Azure Enterprise-portal en kostenbeheer.

Inactieve inschrijvingen worden niet weergegeven in de Azure Enterprise-portal. Verwijder het vinkje uit het vak **Actief** om inactieve overgedragen inschrijvingen weer te geven.  

![Vinkje uit het vak Actief verwijderen zodat de gebruiker inactieve inschrijvingen kan zien](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Woordenlijst met Azure EA-terminologie

- **Account**: Een organisatie-eenheid in de Azure Enterprise-portal. Accounts worden gebruikt om abonnementen te beheren en rapporten te maken.
- **Accounteigenaar**: De persoon die abonnementen en servicebeheerders in Azure beheert. Ze kunnen gebruiksgegevens van dit account en de bijbehorende abonnementen bekijken.
- **Aanpassingsabonnement**: Een abonnement voor één jaar of een uniform abonnement onder de aangepaste inschrijving.
- **Toezegging**: Toezegging van een jaarlijks bedrag voor Azure-services, tegen een gereduceerd toezeggingstarief voor het gebruik ten opzichte van deze vooruitbetaling.
- **Afdelingsbeheerder**: De persoon die afdelingen beheert, nieuwe accounts en accounteigenaren maakt, gebruiksgegevens voor de beheerde afdelingen bekijkt en kosten kan bekijken indien deze machtiging is verleend.
- **Inschrijvingsnummer**: Een unieke id die door Microsoft wordt verleend om de specifieke inschrijving te identificeren die aan een Enterprise Agreement is gekoppeld.
- **Zakelijke beheerder**: De persoon die afdelingen, afdelingseigenaren, accounts en accounteigenaren in Azure beheert. Ze beschikken over de mogelijkheid om zakelijk beheerders te beheren en gebruiksgegevens, gefactureerde hoeveelheden en niet-gefactureerde kosten te bekijken voor alle accounts en abonnementen die aan de Enterprise-inschrijving zijn gekoppeld.
- **Enterprise Agreement**: Een Microsoft-licentieovereenkomst voor klanten met gecentraliseerde aankoopmogelijkheden die hun gehele organisatie willen standaardiseren naar Microsoft-technologie en een informatietechnologie-infrastructuur handhaven op een standaard van Microsoft-software.
- **Enterprise Agreement-inschrijving**: Een inschrijving in het Enterprise Agreement-programma, zodat er gebruik kan worden gemaakt van grote volumes Microsoft-producten tegen een gereduceerd tarief.
- **Microsoft-account**: Een op het web gebaseerde service waarmee gebruikers op deelnemende sites kunnen worden geverifieerd met één set aanmeldingsgegevens.
- **Aanpassing van de Microsoft Azure Enterprise-inschrijving (aanpassing van inschrijving)** : Een aanpassing die is ondertekend door een onderneming, die deze toegang geeft tot Azure als onderdeel van de Enterprise-inschrijving.
- **Azure Enterprise-portal**: De portal die door onze Enterprise-klanten wordt gebruikt voor het beheren van hun Azure-accounts en hun bijbehorende abonnementen.
- **Hoeveelheid verbruikte resources**: De verbruikte hoeveelheid van een afzonderlijke Azure-service in één maand.
- **Servicebeheerder**: De persoon die toegang heeft tot abonnementen en ontwikkelingsprojecten in de Azure Enterprise-portal.
- **Abonnement**: Staat voor een abonnement op de Azure Enterprise-portal en fungeert als container voor Azure-services die door dezelfde servicebeheerder worden beheerd.
- **Werk- of schoolaccount**: Voor organisaties die Active Directory met federatie hebben ingesteld voor de cloud en waarbij alle accounts zich in één tenant bevinden.

### <a name="enrollment-statuses"></a>Statussen van inschrijving

- **Nieuw**: Deze status wordt toegewezen aan een enrollment die binnen 24 uur is gemaakt en wordt binnen 24 uur worden bijgewerkt naar de status In behandeling.
- **In behandeling**: De inschrijvingsbeheerder moet zich aanmelden bij de Azure Enterprise-portal. Zodra de beheerder zich heeft aangemeld, krijgt de inschrijving de status Actief.
- **Actief**: De inschrijving is actief en de gebruiker kan accounts en abonnementen maken in de Azure Enterprise-portal. De inschrijving blijft actief tot de einddatum van de Enterprise Agreement.
- **Onbeperkte verlengde termijn**: Een onbeperkte verlengde termijn wordt toegekend nadat de einddatum van de Enterprise Agreement is verstreken. Hiermee kunnen Azure EA-klanten die zich voor de verlengde termijn hebben aangemeld aan het einde van hun Enterprise Agreement onbeperkt gebruik blijven maken van Azure-services.

   Voordat de einddatum van de Enterprise Agreement is bereikt voor de Azure EA-inschrijving, moet de inschrijvingsbeheerder een van de volgende acties ondernemen:

  - De inschrijving vernieuwen door een extra financiële toezegging toe te voegen.
  - Alles overdragen naar een nieuwe inschrijving.
  - Migreren naar het Microsoft Online Subscription Program (MOSP).
  - Het uitschakelen van alle services die aan de inschrijving zijn gekoppeld, bevestigen.
- **Verlopen**: De Azure EA-klant heeft zich uitgeschreven voor de verlengde termijn en de Azure EA-inschrijving heeft de einddatum voor de Enterprise Agreement bereikt. De inschrijving verloopt en alle bijbehorende services worden uitgeschakeld.
- **Overgedragen**: Inschrijvingen waarvan de bijbehorende accounts en services zijn overgedragen naar een nieuwe inschrijving, worden weergegeven met de status Overgedragen.
  >[!NOTE]
  > Inschrijvingen worden niet automatisch overgedragen als er bij het verlengen een nieuw inschrijvingsnummer wordt gegenereerd. U moet het voorgaande inschrijvingsnummer opnemen in de verlengingsaanvraag om een automatische overdracht mogelijk te maken.

## <a name="get-started-on-azure-ea---faq"></a>Aan de slag met Azure EA - Veelgestelde vragen

In dit gedeelte vindt u antwoorden op veelvoorkomende vragen die klanten ons tijdens het onboardingproces stellen.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Kan ik mijn bestaande Azure-account aan een Azure EA-inschrijving koppelen?

Ja. Alle Azure-abonnementen waarvan u de accounteigenaar bent, worden naar uw Enterprise Agreement omgezet. Hieronder vallen alle abonnementen die maandelijks tegoed gebruiken, zoals Visual Studio, AzurePass, MPN, BizSpark en meer. Het maandelijkse tegoed gaat verloren als u deze abonnementen overzet.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Ik heb mijn bestaande Azure-account per ongeluk aan een Azure EA-inschrijving gekoppeld. Hierdoor ben ik mijn maandelijkse tegoed kwijtgeraakt. Kan ik het maandelijkse tegoed terugkrijgen?

Als u zich hebt aangemeld als Azure EA-accounteigenaar met de aanmeldingsgegevens die u ook gebruikt voor uw Visual Studio-abonnement, kunt u het Azure-voordeel van uw afzonderlijke Visual Studio-abonnement herstellen door een van de volgende acties uit te voeren:

- Verwijder de accounteigenaar uit de Azure Enterprise-portal nadat u bijbehorende Azure-abonnementen hebt verwijderd of verplaatst. Meld u vervolgens opnieuw aan voor Azure-voordelen voor het individuele Visual Studio-abonnement.
- Verwijder de Visual Studio-abonnee uit de beheersite in VLSC en wijs het abonnement opnieuw toe, maar nu aan een account met andere aanmeldingsgegevens. Meld u vervolgens opnieuw aan voor Azure-voordelen voor het individuele Visual Studio-abonnement.

### <a name="what-type-of-subscription-should-i-create"></a>Welk type abonnement moet ik maken?

De Azure Enterprise-portal biedt twee typen abonnementen voor ondernemingsklanten:

- Microsoft Azure Enterprise, ideaal voor:
  - Gebruik in elke productieomgeving
  - Beste prijzen op basis van uitgaven aan infrastructuur

  [Neem contact op met de verkoopafdeling van Azure](https://azure.microsoft.com/pricing/enterprise-agreement/) voor meer informatie.

- Enterprise Dev/Test, ideaal voor:
  - Alle werkbelastingen voor ontwikkelen en testen van het team
  - Gemiddelde tot intensieve individuele werkbelastingen voor ontwikkelen en testen
  - Toegang tot speciale MSDN-installatiekopieën en voorkeurstarieven voor services

  Bekijk de [Enterprise Dev/Test-aanbieding](https://azure.microsoft.com/offers/ms-azr-0148p/) voor meer informatie.

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Is het mogelijk om het eigendom van een abonnement over te dragen aan een ander account?

Ja, u kunt het eigendom van een abonnement overdragen aan een ander account. Als aan account A bijvoorbeeld drie abonnementen zijn gekoppeld, kan de ondernemingsbeheerder één abonnement naar account B, één naar account C en één naar account D overdragen, of alle abonnementen naar account E overdragen.

Ga als volgt te werk om abonnementen over te dragen:

1. Selecteer in de Azure Enterprise-portal **Beheren** > **Account**.
1. Plaats de muisaanwijzer helemaal rechts op **Account** om de opties voor **eigendomsoverdracht** (pictogram van persoon) en **abonnementsoverdracht** (pictogram van lijst) weer te geven. Deze opties zijn alleen zichtbaar voor actieve accounts.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Mijn abonnement heeft dezelfde naam als de aanbieding. Moet ik de abonnementsnaam wijzigen in iets dat voor mijn organisatie handig is?

Als u een abonnement maakt, krijgt het standaard de naam van het gekozen aanbiedingstype. Het wordt aanbevolen de abonnementsnaam te wijzigen in een naam waardoor u het abonnement gemakkelijk kunt traceren.

Ga als volgt te werk om de naam te wijzigen:

1. Meld u aan bij [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selecteer de lijst met abonnementen.
1. Selecteer het abonnement dat u wilt bewerken.
1. Selecteer het pictogram **Abonnement beheren**.
1. Bewerk de abonnementsgegevens.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Hoe kan ik de kosten bijhouden die door een kostenplaats zijn gemaakt?

Als u de kosten van een kostenplaats wilt bijhouden, moet u de kostenplaats definiëren op een van de volgende niveaus:

- Afdeling
- Account
- Abonnement

Op basis van uw behoeften kunt u dezelfde kostenplaats gebruiken om het gebruik en de kosten van een specifieke kostenplaats bij te houden.

Als u bijvoorbeeld kosten wilt bijhouden voor een speciaal project waaraan meerdere afdelingen hebben gewerkt, kunt u de kostenplaats op abonnementsniveau definiëren om het gebruik en de kosten bij te houden.

Het is niet mogelijk om een kostenplaats op serviceniveau te definiëren. Als u gebruik op serviceniveau wilt bijhouden, kunt u de functie _Taggen_ gebruiken.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hoe kan ik het gebruik en de uitgaven van de verschillende afdelingen in mijn organisatie bijhouden?

U kunt binnen uw Azure EA-inschrijving net zoveel afdelingen maken als u nodig hebt. Als u het gebruik zorgvuldig wilt bijhouden, is het belangrijk dat abonnementen niet met meerdere afdelingen worden gedeeld.

Nadat u afdelingen en abonnementen hebt gemaakt, kunt u gegevens bekijken in het gebruiksrapport. Deze informatie kan u helpen bij het bijhouden van gebruik en het beheren van kosten en uitgaven op afdelingsniveau.

U kunt gebruiksgegevens ook bekijken via de rapportage-API. Raadpleeg [Azure Enterprise REST API’s](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis) voor gedetailleerde informatie en voorbeeldcode.

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kan ik een bestedingsquotum instellen en meldingen krijgen als ik in de buurt kom van mijn limiet?

U kunt een bestedingsquotum op afdelingsniveau instellen. U krijgt dan van het systeem automatisch een melding wanneer u 50%, 75%, 90% en 100% van het door u gedefinieerde quotum hebt bereikt.

Selecteer een afdeling en vervolgens het pictogram Bewerken om een bestedingsquotum in te stellen. Nadat u de gegevens voor de bestedingslimiet hebt bewerkt, selecteert u **Opslaan**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Ik heb resourcegroepen gebruikt om RBAC te implementeren en gebruik bij te houden. Hoe kan ik de bijbehorende gebruiksgegevens bekijken?

Als u _resourcegroepen_ en _tags_ gebruikt, wordt deze informatie bijgehouden op serviceniveau en kunt u de gegevens raadplegen in het downloadbare CSV-bestand met gedetailleerde gebruiksinformatie. Zie het gedeelte [Gebruiksrapport downloaden](https://ea.azure.com/report/downloadusage) in de Azure Enterprise-portal.

U kunt de gegevens ook raadplegen via een API. Raadpleeg [Azure Enterprise REST API’s](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis) voor gedetailleerde informatie en voorbeeldcode.

> [!NOTE]
> U kunt alleen tags toepassen op resources die ondersteuning bieden voor Azure Resource Manager-bewerkingen. Als u een virtuele machine, virtueel netwerk of opslag hebt gemaakt via het klassieke implementatiemodel (zoals via de klassieke portal), kunt u geen tag op die resource toepassen. U moet deze resources opnieuw implementeren via de Resource Manager om tags te ondersteunen. Tags worden wel door alle andere resources ondersteund.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kan ik een analyse uitvoeren met behulp van Power BI?

Ja. Met het Microsoft Azure Enterprise-inhoudspakket voor Power BI kunt u:

- Snel Azure-gebruik importeren en analyseren voor uw ondernemingsinschrijving.
- Nagaan welke afdeling, welk account of welk abonnement het meest heeft gebruikt.
- Erachter komen welke service uw organisatie het meest heeft gebruikt.
- Trends in uitgaven en gebruik bijhouden.

U kunt als volgt aan de slag met Power BI:

1. Ga naar de Power BI-website.
1. Meld u aan met een geldig werk- of schoolaccount.

   U kunt hetzelfde werk- of schoolaccount gebruiken of een ander werk- of schoolaccount dan het account dat u hebt gebruikt voor toegang tot de inschrijving via de Azure Enterprise-portal.
1. Kies op het dashboard met services de tegel Microsoft Azure Enterprise en selecteer **Verbinding maken**.
1. Voer het volgende in op het scherm **Verbinding maken met Azure Enterprise**:
    - URL voor Azure-omgeving: [https://ea.azure.com](https://ea.azure.com)
    - Aantal maanden: getal tussen 1 en 36
    - Inschrijvingsnummer: uw inschrijvingsnummer
1. Selecteer **Next**.
1. Voer in het vak **Accountsleutel** de API-sleutel in.

   U vindt de API-sleutel in de Azure Enterprise-portal. Ga naar het tabblad **Gebruiksgegevens downloaden** en selecteer **API-toegangssleutel**. Kopieer de sleutel en plak deze in het vak **Accountsleutel** in Power BI.

Afhankelijk van de grootte van de gegevensset kan het vijf tot dertig minuten duren voordat de gegevens in Power BI worden geladen.

Power BI-rapportage is beschikbaar voor rechtstreekse Azure EA-klanten, partners en indirecte klanten die factureringsgegevens kunnen zien.

## <a name="next-steps"></a>Volgende stappen

- Azure Enterprise-portalbeheerders kunnen [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
- Raadpleeg [Problemen met toegang tot Azure EA Portal oplossen](ea-portal-troubleshoot.md) als u hulp nodig hebt om problemen op te lossen.
- Download de [Azure EA Onboarding Guide (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) voor een onboardinghandleiding voor Azure EA.
