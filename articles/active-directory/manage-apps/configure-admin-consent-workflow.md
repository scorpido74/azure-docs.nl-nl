---
title: De werkstroom voor beheerderstoestemming configureren - Azure Active Directory | Microsoft Documenten
description: Meer informatie over het configureren van een manier voor eindgebruikers om toegang te vragen tot toepassingen waarvoor beheerderstoestemming vereist is.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430202"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>De werkstroom voor beheerderstoestemming configureren (voorbeeld)

In dit artikel wordt beschreven hoe u de functie voor de werkstroom (preview) van beheerderstoestemming inschakelen, zodat eindgebruikers toegang kunnen vragen tot toepassingen waarvoor beheerderstoestemming vereist is.

Zonder een werkstroom voor beheerderstoestemming wordt een gebruiker in een tenant waarin de toestemming van de gebruiker is uitgeschakeld, geblokkeerd wanneer hij toegang probeert te krijgen tot een app waarvoor machtigingen zijn vereist om toegang te krijgen tot organisatiegegevens. De gebruiker ziet een algemene foutmelding waarin staat dat hij onbevoegd is om toegang te krijgen tot de app en dat hij of zij zijn beheerder om hulp moet vragen. Maar vaak weet de gebruiker niet met wie hij contact moet opnemen, dus ze geven het op of maken een nieuw lokaal account in de toepassing. Zelfs wanneer een beheerder hiervan op de hoogte wordt gesteld, is er niet altijd een gestroomlijnd proces om de beheerder te helpen toegang te verlenen en hun gebruikers hiervan op de hoogte te stellen.
 
De werkstroom voor beheerderstoestemming biedt beheerders een veilige manier om toegang te verlenen tot toepassingen waarvoor beheerdersgoedkeuring nodig zijn. Wanneer een gebruiker toegang probeert te krijgen tot een toepassing, maar geen toestemming kan geven, kan hij of zij een verzoek om goedkeuring van de beheerder verzenden. Het verzoek wordt via e-mail verzonden naar beheerders die zijn aangewezen als revisoren. Een revisor onderneemt actie op de aanvraag en de gebruiker wordt op de hoogte gebracht van de actie.

Als u aanvragen wilt goedkeuren, moet een revisor een globale beheerder, beheerder van een cloudtoepassing of toepassingsbeheerder zijn. De revisor moet al een van deze beheerdersrollen toegewezen hebben. gewoon aanwijzen ze als een recensent niet verheffen hun privileges.

## <a name="enable-the-admin-consent-workflow"></a>De werkstroom voor beheerderstoestemming inschakelen

Ga als volgt te werk om de werkstroom voor beheerderstoestemming in te schakelen en revisoren te kiezen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.
2. Klik op **Alle services** boven aan het navigatiemenu aan de linkerkant. De **Azure Active Directory Extension** wordt geopend.
3. Typ in het zoekvak van het filter "**Azure Active Directory**" en selecteer het Azure Active **Directory-item.**
4. Klik in het navigatiemenu op **Enterprise-toepassingen**. 
5. Selecteer **onder Beheren**de optie **Gebruikersinstellingen**.
6. Stel **onder toestemmingsverzoeken voor beheerders (voorbeeld)** instellen **gebruikers kunnen beheerderstoestemming vragen voor apps waarvoor ze geen toestemming kunnen geven voor** **Ja**.

   ![Werkstroominstellingen voor beheerderstoestemming configureren](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configureer de volgende instellingen:

   * **Selecteer gebruikers om toestemmingsverzoeken voor beheerders te controleren.** Selecteer revisoren voor deze werkstroom uit een reeks gebruikers met de algemene beheerders-, cloudtoepassingsbeheerder en toepassingsbeheerdersrollen.
   * **Geselecteerde gebruikers ontvangen e-mailmeldingen voor verzoeken.** E-mailmeldingen in- of uitschakelen aan de revisoren wanneer een verzoek wordt ingediend.  
   * **Geselecteerde gebruikers ontvangen herinneringen voor het verlopen van aanvragen.** Herinneringen e-mailmeldingen in- of uitschakelen aan de revisoren wanneer een verzoek bijna verloopt.  
   * **Toestemmingsaanvraag verloopt na (dagen)**. Geef op hoe lang aanvragen geldig blijven.

7. Selecteer **Opslaan**. Het kan tot een uur duren voordat de functie is ingeschakeld.

> [!NOTE]
> U revisoren voor deze werkstroom toevoegen of verwijderen door de lijst **met revisoren met machtigingen voor beheerders** selecteren te wijzigen. Houd er rekening mee dat een huidige beperking van deze functie is dat revisoren de mogelijkheid kunnen behouden om aanvragen te controleren die zijn uitgevoerd terwijl ze zijn aangewezen als een revisor.

## <a name="how-users-request-admin-consent"></a>Hoe gebruikers toestemming van de beheerder vragen

Nadat de werkstroom voor beheerderstoestemming is ingeschakeld, kunnen gebruikers beheerdersgoedkeuring aanvragen voor een toepassing waarvoor ze geen toestemming hebben gegeven. In de volgende stappen wordt de ervaring van de gebruiker beschreven bij het aanvragen van goedkeuring. 

1. De gebruiker probeert zich aan te melden bij de toepassing.

2. Het vereiste bericht **Voor goedkeuring** wordt weergegeven. De gebruiker typt een rechtvaardiging voor het nodig hebben van toegang tot de app en selecteert vervolgens **Goedkeuring aanvragen**.

   ![Gebruikerverzoek en motivering van beheerderstoestemming](media/configure-admin-consent-workflow/end-user-justification.png)

3. Een **verzonden bericht met aanvraag** bevestigt dat de aanvraag is ingediend bij de beheerder. Als de gebruiker meerdere aanvragen verzendt, wordt alleen de eerste aanvraag ingediend bij de beheerder.

   ![Gebruikerverzoek en motivering van beheerderstoestemming](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. De gebruiker ontvangt een e-mailmelding wanneer zijn verzoek wordt goedgekeurd, geweigerd of geblokkeerd. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Verzoeken om toestemming van beheerders controleren en uitvoeren

Ga als u de toestemmingsverzoeken van beheerders bekijken en onderneem:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als een van de geregistreerde revisoren van de werkstroom voor beheerderstoestemming.
2. Selecteer **Alle services** boven aan het navigatiemenu aan de linkerkant. De **Azure Active Directory Extension** wordt geopend.
3. Typ in het zoekvak van het filter "**Azure Active Directory**" en selecteer het Azure Active **Directory-item.**
4. Klik in het navigatiemenu op **Enterprise-toepassingen**.
5. Selecteer **onder Activiteit**de optie **Toestemmingsaanvragen voor beheerders (voorbeeld)**.

   > [!NOTE]
   > Revisoren zien alleen beheerdersverzoeken die zijn gemaakt nadat ze zijn aangewezen als revisor.

1. Selecteer de toepassing die wordt aangevraagd.
2. Bekijk de details over de aanvraag:  

   * Als u wilt zien wie toegang aanvraagt en waarom, selecteert u het **tabblad Opgevraagd.**
   * Als u wilt zien welke machtigingen door de toepassing worden aangevraagd, selecteert **u Machtigingen en toestemming controleren**.

8. Evalueer het verzoek en onderneem de juiste actie:

   * **Keur het verzoek goed**. Als u een aanvraag wilt goedkeuren, verleent u toestemming van de beheerder voor de toepassing. Zodra een verzoek is goedgekeurd, worden alle aanvragers meegedeeld dat ze toegang hebben gekregen.  
   * **Weiger het verzoek.** Als u een verzoek wilt weigeren, moet u een motivering geven die aan alle aanvragers wordt verstrekt. Zodra een verzoek is afgewezen, worden alle aanvragers op de hoogte gebracht dat hun de toegang tot de aanvraag is ontzegd. Het weigeren van een verzoek belet gebruikers niet om in de toekomst opnieuw toestemming te vragen voor de app.  
   * **Blokkeer het verzoek**. Als u een verzoek wilt blokkeren, moet u een motivering geven die aan alle aanvragers wordt verstrekt. Zodra een aanvraag is geblokkeerd, krijgen alle aanvragers een melding dat ze de toegang tot de toepassing is ontzegd. Als u een aanvraag blokkeert, wordt een hoofdobject voor de service voor de toepassing in uw tenant in een uitgeschakelde status gemaakt. Gebruikers kunnen in de toekomst geen toestemming van de beheerder voor de toepassing aanvragen.
 
## <a name="email-notifications"></a>E-mailmeldingen
 
Als deze is geconfigureerd, ontvangen alle revisoren e-mailmeldingen wanneer:

* Er is een nieuwe aanvraag gemaakt
* Een aanvraag is verlopen
* Een aanvraag nadert de vervaldatum  
 
Aanvragers ontvangen e-mailmeldingen wanneer:

* Zij dienen een nieuw verzoek om toegang in
* Hun verzoek is verlopen
* Hun verzoek is afgewezen of geblokkeerd
* Hun verzoek is goedgekeurd
 
## <a name="audit-logs"></a>Auditlogboeken 
 
In de onderstaande tabel worden de scenario's en controlewaarden beschreven die beschikbaar zijn voor de werkstroom voor beheerderstoestemming. 

> [!NOTE]
> De gebruikerscontext van de auditactor ontbreekt momenteel in alle scenario's. Dit is een bekende beperking in de preview-versie.


|Scenario  |Auditservice  |Controlecategorie  |Controleactiviteit  |Controleactor  |Beperkingen van controlelogboeken  |
|---------|---------|---------|---------|---------|---------|
|Beheerder die de werkstroom voor toestemmingsaanvragen inschakelt        |Toegangsbeoordelingen           |UserManagement           |Sjabloon voor governancebeleid maken          |App-context            |Op dit moment u de gebruikerscontext niet vinden            |
|Beheerder die de werkstroom voor toestemmingsaanvragen uitschakelt       |Toegangsbeoordelingen           |UserManagement           |Sjabloon voor governancebeleid verwijderen          |App-context            |Op dit moment u de gebruikerscontext niet vinden           |
|Beheerder die de configuratie van de toestemmingswerkstroom bijwerkt        |Toegangsbeoordelingen           |UserManagement           |Sjabloon voor governancebeleid bijwerken          |App-context            |Op dit moment u de gebruikerscontext niet vinden           |
|Eindgebruiker die een verzoek tot toestemming van beheerders maakt voor een app       |Toegangsbeoordelingen           |Beleid         |Aanvraag maken           |App-context            |Op dit moment u de gebruikerscontext niet vinden           |
|Revisoren die een verzoek om toestemming voor beheerders goedkeuren       |Toegangsbeoordelingen           |UserManagement           |Alle aanvragen in de bedrijfsstroom goedkeuren          |App-context            |Op dit moment u de gebruikerscontext of de app-id die admin toestemming heeft gekregen, niet vinden.           |
|Revisoren die een toestemmingsverzoek voor beheerders weigeren       |Toegangsbeoordelingen           |UserManagement           |Alle aanvragen in de bedrijfsstroom goedkeuren          |App-context            | Op dit moment u de gebruikerscontext van de actor die een verzoek tot toestemming van beheerders heeft geweigerd, niet vinden          |

## <a name="faq"></a>Veelgestelde vragen 

**Ik heb deze workflow ingeschakeld, maar waarom kan ik bij het testen van de functionaliteit niet de nieuwe prompt 'Vereiste goedkeuring' zien waarmee ik toegang kan aanvragen?**

Na het inschakelen van de functie kan het tot 60 minuten duren voordat eindgebruikers de update kunnen zien. U controleren of de configuratie juist is uitgevoerd door `https://graph.microsoft.com/beta/settings` de waarde **EnableAdminConsentRequests** in de API weer te geven.

**Waarom kan ik als recensent niet alle aanvragen in behandeling zien?**

Revisoren kunnen alleen beheerdersverzoeken zien die zijn gemaakt nadat ze zijn aangewezen als revisor. Als u dus onlangs als revisor bent toegevoegd, ziet u geen aanvragen die vóór uw toewijzing zijn gemaakt.

**Waarom zie ik als revisor meerdere aanvragen voor dezelfde toepassing?**
  
Als een toepassingsontwikkelaar zijn app heeft geconfigureerd om statische en dynamische toestemming te gebruiken om toegang te vragen tot de gegevens van de eindgebruiker, ziet u twee toestemmingsverzoeken voor beheerders. Het ene verzoek vertegenwoordigt de statische machtigingen en de andere voor de dynamische machtigingen.

**Kan ik als aanvrager de status van mijn verzoek controleren?**  

Nee, voor nu kunnen aanvragers alleen updates ontvangen via e-mailmeldingen.

**Is het als recensent mogelijk om de toepassing goed te keuren, maar niet voor iedereen?**
 
Als u zich zorgen maakt over het verlenen van toestemming van de beheerder en het toestaan van alle gebruikers in de tenant om de toepassing te gebruiken, raden we u aan het verzoek te weigeren. Geef vervolgens handmatig toestemming voor beheerders door de toegang tot de toepassing te beperken door gebruikerstoewijzing te vereisen en gebruikers of groepen aan de toepassing toe te kennen. Zie [Methoden voor het toewijzen van gebruikers en groepen voor](methods-for-assigning-users-and-groups.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie Azure Active Directory Consent [Framework](../develop/consent-framework.md)voor meer informatie over het toestaan van toepassingen.

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[Toestemming voor een tenantbeheerder verlenen voor een toepassing](grant-admin-consent.md)

[Machtigingen en toestemming in het Microsoft-identiteitsplatform](../develop/active-directory-v2-scopes.md)

[Azure AD op StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
