---
title: Aan de slag met de Azure EA Portal
description: In dit artikel wordt uitgelegd hoe Azure EA-klanten Azure EA Portal gebruiken.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992229"
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

![Diagram van een eenvoudige Azure EA-hiërarchie](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

Als u de Azure-Services in uw registratie wilt beheren, zijn er vijf afzonderlijke gebruikers rollen met beheerders rechten voor ondernemingen:

- Ondernemingsbeheerder
- Afdelingsbeheerder
- Accounteigenaar
- Servicebeheerder
- Contact persoon voor meldingen

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

Voor elk account is een uniek werk-, school-of Microsoft-account vereist. Zie [Azure Enterprise Agreement-beheerdersrollen in Azure begrijpen](understand-ea-roles.md) voor meer informatie over Azure EA Portal-beheerdersrollen.

### <a name="service-administrator"></a>Servicebeheerder

De servicebeheerder heeft machtigingen om services te beheren in Azure Portal en gebruikers toe te wijzen aan de rol co-beheerder.

### <a name="notification-contact"></a>Contact persoon voor meldingen

De contact persoon van de melding ontvangt gebruiks meldingen met betrekking tot de inschrijving.

## <a name="activate-your-enrollment"></a>Uw inschrijving activeren

Als u uw service wilt activeren, opent de oorspronkelijke ondernemingsbeheerder Azure EA Portal op [https://ea.azure.com](https://ea.azure.com) en meldt deze zich aan met het e-mailadres in de uitnodigingsmail.

Als u als EA-beheerder hebt ingesteld, hoeft u de activerings-e-mail niet te ontvangen om u aan te melden bij de Azure EA-Portal. U kunt door gaan met [https://ea.azure.com](https://ea.azure.com) en u aanmelden met uw e-mail adres (werk, school of Live ID) en wacht woord.

Als u meerdere inschrijvingen heeft, kiest u de inschrijving die u wilt activeren. Standaard worden alleen actieve inschrijvingen weergegeven. Als u de inschrijvingsgeschiedenis wilt weergeven, wist u de optie **Actief** in de rechterbovenhoek van Azure EA Portal.

De status onder Inschrijving is **Actief**.

![Voorbeeld van een actieve inschrijving](./media/ea-portal-get-started/ea-enrollment-status.png)

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

Zie [Azure Enterprise Agreement-beheerdersrollen in Azure begrijpen](understand-ea-roles.md) voor meer informatie over Enterprise-beheerdersrollen.

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

![Een voorbeeld met het dialoogvenster Afdelingsbeheerder toevoegen](./media/ea-portal-get-started/ea-create-add-department-admin.png)

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

![Voorbeeld met een lijst accounts en de optie +Account toevoegen](./media/ea-portal-get-started/create-ea-add-an-account.png)

U kunt op **Ander account toevoegen** klikken om nog een account toe te voegen of u kunt in de rechterbenedenhoek van de linkerwerkbalk op **Toevoegen** klikken.

Eigendom van het account controleren:

1. Meld u aan bij Azure EA Portal.
1. Controleer het accounteigendom door de status weer te geven. De status wordt gewijzigd van **In behandeling** in **Begin-/einddatum**. De Begin-/einddatum is de datum waarop de gebruiker zich voor het eerst heeft aangemeld en de einddatum van de overeenkomst.
1. Zodra het pop-upbericht Waarschuwing wordt weergegeven, moet een eigenaar van het account op **Doorgaan** klikken om het account te activeren wanneer deze zich voor de eerste keer bij Azure EA-portal aanmeldt.


## <a name="change-account-owner"></a>Accounteigenaar wijzigen

Ondernemingsbeheerders kunnen Azure EA Portal gebruiken om het eigendom van het abonnementsaccount in een inschrijving over te dragen. Met deze actie worden alle abonnementen verplaatst van een brongebruikersaccount naar een doelgebruikersaccount.

Belangrijke punten voor het overdragen van gebruikersaccountgegevens:

- Overdrachten van een werk- of schoolaccount naar een ander werk- of schoolaccount worden ondersteund.
- Overdrachten van een Microsoft-account naar een werk- of schoolaccount worden ondersteund.
- Overdrachten van een werk- of schoolaccount naar een Microsoft-account worden niet ondersteund.
- Overdrachten van een Microsoft-account naar een ander Microsoft-account worden ondersteund. Het doelaccount moet een geldig Azure Commerce-account zijn om als geldig doel voor overdrachten te fungeren. Voor nieuwe accounts wordt u gevraagd een Azure Commerce-account te maken wanneer u zich aanmeldt bij de Azure EA Portal. Voor bestaande accounts moet u eerst een nieuw Azure-abonnement maken voordat het account kan worden overgedragen.
- Zodra u een abonnementsoverdracht hebt voltooid, wordt de eigenaar van het account door Microsoft bijgewerkt.

Beleidsregels voor op rollen gebaseerd toegangsbeheer:

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

![Afbeelding van het symbool Accounteigenaar wijzigen](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Accounteigendom overdragen voor één abonnementen:

1. Klik in het navigatiegedeelte links op **Beheren**.
2. Klik op het tabblad **Account** en plaats de muisaanwijzer op een account.
3. Klik aan de rechterkant op het symbool voor het overdragen van abonnementen. Het symbool lijkt op een pagina.
4. Selecteer een geschikt abonnement en klik op **Volgende**.
5. Bevestig de overdracht en klik op **Verzenden**.

![Afbeelding met het symbool Abonnementen overdragen](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

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

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Een EA-abonnement overdragen naar een abonnement op gebruiksbasis

Als u een EA-abonnement wilt overdragen naar afzonderlijk abonnement dat wordt betaald naar gebruik, moet u een nieuwe ondersteuningsaanvraag maken in Azure EA-portal. Als u een ondersteuningsaanvraag wilt maken, klikt u in het gedeelte Help en ondersteuning op **+ Nieuwe ondersteuningsaanvraag** .

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Een bestaand account koppelen aan uw abonnement dat wordt betaald per gebruik

Als u al over een bestaand Microsoft Azure-account in de Microsoft Azure-portal beschikt, voert u het bijbehorende Microsoft-account of een werk- of schoolaccount in om dit aan uw Enterprise Agreement-inschrijving te koppelen.

### <a name="associate-an-existing-account"></a>Een bestaand account koppelen

1. Klik in Enterprise Portal op **Beheren**.
1. Klik op het tabblad **Account**.
1. Klik op **+Een account toevoegen**.
1. Voer het Microsoft-account of het werk- of schoolaccount in dat aan het bestaande account is gekoppeld.
1. Bevestig het Microsoft-account of het werk- of schoolaccount dat aan het bestaande account is gekoppeld.
1. Geef een naam op die u wilt gebruiken om dit account te identificeren in de rapportfunctie.
1. Klik op **Add**.
1. U kunt nog een account toevoegen door nogmaals de optie **+Een account toevoegen** te selecteren, of keer terug naar de startpagina door op de knop **Beheerder** te klikken.
1. Als u klikt om de pagina **Account** te openen, wordt het zojuist toegevoegde account weergegeven met de status **In behandeling**.

### <a name="confirm-account-ownership"></a>Eigendom van het account bevestigen

1. Meld u aan met het e-mailaccount dat is gekoppeld aan het Microsoft-account of het werk- of schoolaccount dat u hebt opgegeven.
1. Open de e-mailmelding met de titel _Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing_ (Uitnodiging voor het activeren van uw account in de Microsoft Azure-service van Microsoft Volume Licensing).
1. Klik op de koppeling **Log into the Microsoft Azure Enterprise Portal** (Aanmelden bij Microsoft Azure Enterprise Portal) in de uitnodiging.
1. Klik op **Aanmelden**.
1. Voer uw Microsoft-account of werk- of schoolaccount en wachtwoord in om u aan te melden en te bevestigen dat u eigenaar van het account bent.

### <a name="azure-marketplace"></a>Azure Marketplace

Hoewel de meeste abonnementen vanuit een omgeving waarin wordt betaald per gebruik, worden omgezet naar Enterprise Azure, geldt dit niet voor Azure Marketplace-services. Als u in één weergave alle abonnementen en kosten wilt kunnen zien, is het raadzaam de Azure Marketplace-services aan Enterprise Portal toe te voegen:

1. Klik in het linkernavigatievenster op **Beheren**.
1. Klik op het **tabblad Inschrijving**.
1. Bekijk de sectie Inschrijvingsdetails.
1. Klik rechts naast het veld Azure Marketplace op het potloodpictogram om de functie in te schakelen en druk op **Opslaan**.

De accounteigenaar kan nu Azure Marketplace-abonnementen aanschaffen die hij of zij voorheen als abonnementen met betalen per gebruik in eigendom had.

Zodra nieuwe Azure Marketplace-abonnementen onder uw inschrijving zijn geactiveerd, worden Marketplace-abonnementen opgezegd die in de betalen per gebruik-omgeving zijn gemaakt. Dit is een essentiële stap om ervoor te zorgen dat uw Marketplace-abonnementen geen slechte status krijgen wanneer uw betaalmiddel voor betalen per gebruik-abonnementen verloopt.

### <a name="msdn"></a>MSDN

MSDN-abonnementen worden automatisch omgezet naar MSDN Dev/Test; eventueel bestaand geldtegoed op de EA-aanbieding gaat verloren.

### <a name="azure-in-open"></a>Azure in Open

Wanneer u een Azure in Open-abonnement koppelt aan een EA, raakt u niet-verbruikte Azure in Open-tegoeden kwijt. Om mogelijk verlies van tegoeden te voorkomen, is het raadzaam alle tegoeden van een Azure in Open-abonnement te verbruiken voordat het account aan een EA wordt toegevoegd.  

### <a name="accounts-with-support-subscriptions"></a>Accounts met ondersteuningsabonnementen

Let op: wanneer u bestaande accounts aan Enterprise Portal toevoegt waaraan een ondersteuningsabonnement is gekoppeld (en waar niet ook al een EA-ondersteuningsabonnement aan is gekoppeld), wordt het MOSA-ondersteuningsabonnement niet automatisch overgedragen en zult u de ondersteuning opnieuw moeten aanschaffen in EA. Tot het einde van de volgende maand wordt nog een respijtperiode gehanteerd waarin nog ondersteuning wordt geboden, zodat u meer tijd hebt om ondersteuning opnieuw te bestellen.

## <a name="view-usage-summary-and-download-reports"></a>Gebruiksoverzicht weergeven en rapporten downloaden

Ondernemingsbeheerders kunnen een overzicht van hun gebruiksgegevens, verbruikte financiële toezegging en de kosten voor aanvullend gebruik in Azure EA Portal weergeven. De kosten worden weergegeven op overzichtsniveau voor alle accounts en abonnementen.

U kunt als volgt gedetailleerd gebruik voor specifieke accounts weergeven:

Download het rapport Gebruiksgegevens. Klik op **rapporten** en klik vervolgens op het tabblad **gebruik downloaden** . Klik in de lijst met rapporten op **downloaden** voor het maandelijkse rapport dat u wilt ophalen.

Het rapport bevat geen toepasselijke belastingen. Er duurt maximaal acht uur voordat het gebruik wordt weergegeven in het rapport.

U kunt als volgt rapporten en grafieken met het gebruiksoverzicht weergeven:

1. Ga naar Azure EA Portal en klik in het navigatiegedeelte aan de linkerkant op **Rapporten** en bekijk het tabblad **Gebruiksoverzicht**.  
  ![Gebruiksoverzicht maken en weergeven en rapporten downloaden](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Selecteer een toezeggingstermijn.
3. Schakel in de rechterbovenhoek van de pagina tussen **M** (Maandelijks) en **A** (Aangepast) om het **Gebruiksoverzicht** weer te geven met een aangepaste begin- en einddatum.  
  ![Gebruiksoverzicht maken en weergeven en rapporten downloaden in aangepaste weergave](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
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

![Voorbeeld met de pagina Gebruiksgegevens downloaden](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Hier volgt een video waarin wordt uitgelegd hoe u gebruiksgegevens downloadt:

[Video over het gebruik van Azure EA Portal](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Geavanceerde rapporten downloaden

Als u rapporten wilt maken voor specifieke datumbereiken of accounts, kunt u geavanceerde rapporten downloaden. Met ingang van 30 augustus 2016 is de indeling van het uitvoerbestand gewijzigd van .xlsx naar .csv, zodat er grotere recordsets mogelijk zijn.

1. Selecteer **Geavanceerde rapporten downloaden**.
1. Selecteer **Geldig datumbereik**.
1. Selecteer **Geldige accounts**.
1. Selecteer **Gebruiksgegevens aanvragen**.
1. Druk op de knop **Vernieuwen** totdat de rapportstatus wordt bijgewerkt naar **Downloaden**.
1. Download het rapport.

## <a name="ea-term-glossary"></a>Woordenlijst met EA-terminologie

- **Account**: een organisatie-eenheid op de Azure EA-portal die wordt gebruikt voor het beheren van abonnementen en het gebruik voor rapportage.
- **Eigenaar**van het account: de persoon die is geïdentificeerd om abonnementen en service beheerders te beheren op Microsoft Azure. Ze kunnen gebruiksgegevens van dit account en de bijbehorende abonnementen bekijken.
- **Wijzigings abonnement**: één jaar of coterminous abonnement bij de wijziging van de inschrijving.
- **Toezeg ging**: de toezeg ging van een jaarlijks monetair bedrag voor Microsoft Azure Services tegen een korting op kosten voor gebruik op basis van deze voor uitbetaling.
- **Afdelings beheerder**: de persoon (s) die is geïdentificeerd voor het beheren van afdelingen, het maken van nieuwe accounts en eigen aren van accounts, het weer geven van gebruiks gegevens voor de afdelingen die ze beheren en het weer geven van kosten bij het verlenen van machtigingen.
- **Inschrijvings nummer**: een unieke id die door micro soft is geleverd om de specifieke inschrijving te identificeren die is gekoppeld aan een Enter prise Agreement.
- **Ondernemings beheerder**: de persoon (s) die is geïdentificeerd voor het beheren van afdelingen en eigen aren van afdelingen en accounts en account eigenaren op Microsoft Azure. Ze beschikken over de mogelijkheid om zakelijk beheerders te beheren en gebruiksgegevens, gefactureerde hoeveelheden en niet-gefactureerde kosten te bekijken voor alle accounts en abonnementen die aan de Enterprise-inschrijving zijn gekoppeld.
- **Enter prise Agreement**: een micro soft-licentie overeenkomst voor klanten met gecentraliseerde aankopen die hun hele organisatie willen standaardiseren in micro soft-technologie en een IT-infra structuur kunnen onderhouden met een standaard micro soft-software.
- **Inschrijving van de Enter prise Agreement**: een inschrijving in het Enter prise Agreement-programma waarmee micro soft-producten op kortings tarieven worden verstrekt.
- **Microsoft-account**: een webservice die deelnemende sites in staat stelt om een gebruiker met één set referenties te verifiëren.
- **Microsoft Azure wijziging van de Enter prise-inschrijving (inschrijvings wijziging)** : een wijziging die is ondertekend door een onderneming en die hen toegang biedt tot Microsoft Azure als onderdeel van hun zakelijke inschrijving.
- **Azure EA-Portal**: de portal die onze zakelijke klanten gebruiken voor het beheren van hun Microsoft Azure accounts en de bijbehorende abonnementen.
- **Verbruikte resource hoeveelheid**: de hoeveelheid van een afzonderlijke Microsoft Azure service die in een maand is gebruikt.
- **Service beheerder**: de persoon die is geïdentificeerd voor het openen en beheren van abonnementen en ontwikkelings projecten op de Azure EA-Portal.
- **Abonnement**: vertegenwoordigt een Azure EA-Portal en is een container van Microsoft Azure Services die worden beheerd door dezelfde service beheerder.
- **Werk-of school account**: voor organisaties die Active Directory met Federatie hebben ingesteld voor de Cloud en alle accounts zich op één Tenant bevinden.

### <a name="enrollment-statuses"></a>Statussen van inschrijving:

- **In behandeling**: de registratie beheerder moet zich aanmelden bij de Azure EA-Portal. Zodra de beheerder zich heeft aangemeld, schakelt de inschrijving over tot de status Actief.
- **Actief**: de registratie is actief en accounts en abonnementen kunnen worden gemaakt in de Azure EA-Portal. De inschrijving blijft actief tot de einddatum van de Enterprise Agreement.
- **Onbepaalde uitgebreide term**: een onbepaalde uitgebreide term vindt plaats nadat de eind datum van de Enter prise Agreement is verstreken. Hiermee kunnen EA-klanten die zich voor de verlengde termijn hebben aangemeld aan het einde van hun Enterprise Agreement onbeperkt gebruik blijven maken van Azure. Voordat de Enterprise Agreement-einddatum van de EA-inschrijving is bereikt, moet de inschrijvingsbeheerder bepalen of de inschrijving moet worden verlengd door extra financiële toezeggingen te doen, moet worden overgestapt naar een nieuwe inschrijving, een migratie naar het Microsoft Online Subscription Program (MOSP) moet worden uitgevoerd of alle services die aan de inschrijving zijn gekoppeld, moeten worden uitgeschakeld.
- **Verlopen**: de EA-klant wordt afgemeld bij de verlengde termijn en de EA-inschrijving heeft de eind datum van de Enter prise Agreement bereikt, de inschrijving verloopt en alle gekoppelde services worden uitgeschakeld.
- **Overgedragen**: inschrijvingen waarbij alle gekoppelde accounts en services zijn overgedragen naar een nieuwe inschrijving, worden weer gegeven met de status overgezet. Let op: inschrijvingen worden niet automatisch overgedragen als er tijdens het verlengen een nieuw inschrijvingsnummer wordt gegenereerd. Het voorafgaande inschrijvingsnummer moet worden opgenomen in de verlengingsaanvraag van de klant om een automatische overdracht mogelijk te maken.

## <a name="get-started-on-azure-ea-faq"></a>Veelgestelde vragen om aan de slag te gaan met Azure EA

In dit document vindt u meer informatie over gangbare vragen die klanten ons tijdens het onboardingproces stellen.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Kan ik mijn bestaande Azure-account aan een Enterprise-inschrijving koppelen?

Ja, dat is mogelijk. Belangrijk: alle Azure-abonnementen waarvan u de accounteigenaar bent, worden naar uw Enterprise Agreement omgezet. Dit is inclusief abonnementen waarvoor maandelijks tegoed wordt gebruikt (bijvoorbeeld Visual Studio, AzurePass, MPN, BizSpark, enzovoort). Dit betekent dat u hierdoor ook uw maandelijkse tegoed verliest.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>Ik heb mijn bestaande Azure-account per ongeluk aan een Enterprise-inschrijving gekoppeld. Hierdoor ben ik mijn maandelijkse tegoed kwijtgeraakt. Is het mogelijk om mijn maandelijkse tegoed terug te krijgen?

Als u uw afzonderlijke Azure-tegoed voor uw Visual Studio-abonnement wilt herstellen nadat u zich als een EA-accounteigenaar hebt geverifieerd en voor EA dezelfde aanmelding als voor uw Visual Studio-abonnement hebt gebruikt, moet u een van de volgende acties uitvoeren:
1. Verwijder alle Azure-abonnementen waarvan deze accounteigenaar de eigenaar is, verwijder de accounteigenaar uit EA Portal en zorg ervoor dat deze persoon zich opnieuw aanmeldt voor zijn of haar afzonderlijke Azure-voordelen via Visual Studio.
 OF
1. Verwijder de Visual Studio-abonnee van de beheersite in VLSC en wijs het abonnement opnieuw toe. Laat de abonnee hierbij een andere aanmelding gebruiken. Vervolgens kan de abonnee zich opnieuw aanmelden voor zijn of haar afzonderlijke Azure-voordelen via Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Welk type abonnement moet ik maken?

EA Portal biedt twee abonnementstypen voor zakelijke klanten:

- Microsoft Azure Enterprise is ideaal voor:
  - Gebruik in elke productieomgeving
  - Beste prijzen op basis van uitgaven aan infrastructuur
  - Kijk op https://azure.microsoft.com/pricing/enterprise-agreement/ voor meer informatie
- Enterprise Dev/Test is ideaal voor:
  - Alle werkbelastingen voor ontwikkelen en testen van het team
  - Gemiddelde tot intensieve individuele werkbelastingen voor ontwikkelen en testen
  - Toegang tot speciale MSDN-installatiekopieën en voorkeurstarieven voor services
  - Kijk op https://azure.microsoft.com/offers/ms-azr-0148p/ voor meer informatie

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Is het mogelijk om het eigendom van een abonnement over te dragen aan een ander account?

Ja, het is mogelijk om het eigendom van een abonnement over te dragen aan een ander account. Als aan account A bijvoorbeeld drie abonnementen zijn gekoppeld, kan de bedrijfsbeheerder één abonnement naar account B, één naar account C en één naar account D overdragen, of alle abonnementen naar account E overdragen.

Ga naar EA en klik op Beheren > Account en beweeg de muisaanwijzer over **Account** (uiterst rechts). U ziet de opties Eigendom overdragen (portretpictogram) en Abonnement overdragen (lijstpictogram).

Deze optie is alleen zichtbaar voor actieve accounts.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Ik zie dat de naam van het abonnement standaard wordt ingesteld op de naam van de aanbieding. Moet ik de abonnementsnaam wijzigen in iets dat voor mijn organisatie handig is?

Elk abonnement dat wordt gemaakt, krijgt standaard het gekozen aanbiedingstype. Het wordt aanbevolen de abonnementsnaam te wijzigen in een naam waardoor u het abonnement gemakkelijk kunt traceren.

**U kunt de naam als volgt wijzigen:**
1. Meld u aan bij [https://account.windowsazure.com](https://account.windowsazure.com).
1. Klik op de lijst met abonnementen.
1. Selecteer Abonnement.
1. Klik op het pictogram **Abonnement beheren**.
1. Bewerk de abonnementsgegevens.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Hoe kan ik de kosten bijhouden die door de Kostenplaats zijn gemaakt?

Als u kosten van de Kostenplaats wilt bijhouden, moet u de Kostenplaats op een van de volgende niveaus definiëren:
- Afdeling
- Account
- Abonnement

Op basis van uw behoeften kunt u dezelfde Kostenplaats gebruiken om het gebruik en de kosten bij te houden die aan een specifieke Kostenplaats zijn gekoppeld.

Als u bijvoorbeeld kosten wilt bijhouden voor een speciaal project waaraan meerdere afdelingen hebben gewerkt, kunt u de Kostenplaats op abonnementsniveau gebruiken om het gebruik en de kosten bij te houden.

U kunt de Kostenplaats niet op serviceniveau definiëren. Als u het gebruik op serviceniveau wilt bijhouden, kunt u de functie Taggen gebruiken (deze is beschikbaar op serviceniveau).

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hoe kan ik het gebruik en de uitgaven van de verschillende afdelingen in mijn organisatie bijhouden?

U kunt binnen uw EA-inschrijving net zoveel afdelingen maken als u nodig hebt. Zorg ervoor dat abonnementen niet met meerdere afdelingen worden gedeeld, om het gebruik goed te kunnen bijhouden.

Zodra de afdeling en het abonnement zijn gemaakt, ziet u dat het gebruiksrapport wordt gevuld met informatie waarmee u de gebruiksgegevens kunt bijhouden en kosten/onkosten kunt beheren op afdelingsniveau.

Ook kunt u toegang krijgen tot de gebruiksgegevens via gedetailleerde API-informatie; er is voorbeeldcode beschikbaar op [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kan ik het bestedingsquotum instellen en meldingen krijgen zodra ik mijn limiet benader?

U kunt het bestedingsquotum op afdelingsniveau instellen. U krijgt dan van het systeem automatisch een melding wanneer u 50%, 75%, 90% en 100% van het door u gedefinieerde quotum hebt bereikt.

Klik op de afdeling waaraan u een bestedingsquotum wilt toevoegen en klik op het pictogram voor bewerken om uw bestedingsquotum te definiëren. Klik op **Opslaan** om de details op te slaan.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Ik heb resourcegroepen (RG's) gebruikt om RBAC te implementeren en het gebruik bij te houden. Hoe kan ik de bijbehorende gebruiksgegevens bekijken?

Als informatie zoals resourcegroepen en tags zijn gebruikt, wordt dit bijgehouden op serviceniveau. Deze informatie is beschikbaar in het uitgebreide downloadbare gebruiksbestand (CSV) dat u kunt downloaden via Azure EA Portal [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

Ook kunt u toegang krijgen tot de gebruiksgegevens via gedetailleerde API-informatie. Voorbeeldcode is beschikbaar op [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

U kunt alleen tags toepassen op resources die ondersteuning bieden voor Resource Manager-bewerkingen. Als u een virtuele machine, virtueel netwerk of opslag hebt gemaakt via het klassieke implementatiemodel (zoals via de klassieke portal), kunt u geen tag op die resource toepassen. U moet deze resources opnieuw implementeren via de Resource Manager om tags te ondersteunen. Tags worden wel door alle andere resources ondersteund.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kan ik een analyse uitvoeren met behulp van Power BI?

Ja. Met het Microsoft Azure Enterprise-inhoudspakket voor Power BI kunt u snel het Azure-verbruik voor uw Enterprise-inschrijving importeren en analyseren. Ontdek welke afdeling, welk account of welk abonnement het hoogste verbruik had, welke service het meest is gebruikt door uw organisatie of houd uitgave- en gebruikstrends bij.

**Ga naar de Power BI-website:**

 1. Meld u aan met geldig werk- of schoolaccount.
    - U kunt hetzelfde werk- of schoolaccount gebruiken of een ander werk- of schoolaccount dan het account dat u hebt gebruikt voor toegang tot de inschrijving via Azure EA Portal.
 1. Kies het volgende op het dashboard met services:
    - de tegel Microsoft Azure Enterprise.
    - Klik op **Connect** (Verbinden).
 1. Kies het volgende op het scherm Verbinding maken met Azure Enterprise:
    - URL voor Azure-omgeving: [https://ea.azure.com](https://ea.azure.com).
    - Aantal maanden: kies een aantal tussen 1 en 36.
    - Inschrijvingsnummer: voer het inschrijvingsnummer in.
    - Klik op **Volgende**.
 1. Voer in het vak Verificatiesleutel de API-sleutel in. U kunt de API-sleutel ophalen in Azure EA Portal. Klik daar op het tabblad Gebruiksgegevens downloaden op de optie **API-toegangssleutel**.
    - Kopieer de sleutel en plak deze in het vak Accountsleutel.
    - Het duurt ongeveer 5 tot 30 minuten voordat de gegevens in Power BI worden geladen. Dit hangt af van de grootte van de gegevensset.

Power BI-rapportage is beschikbaar voor rechtstreekse EA-klanten, partners en indirecte klanten die factureringsgegevens kunnen zien.

## <a name="next-steps"></a>Volgende stappen

- Azure EA Portal-beheerders moeten [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
- Zie [Problemen met toegang tot Azure EA Portal oplossen](ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen met Azure EA Portal.
- Zie [Azure EA Onboarding Guide](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) voor een onboardinghandleiding voor Azure EA.
