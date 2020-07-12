---
title: 'Zelf studie: werk dagen configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers accounts op workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bbd461072a137bf32874805e5c6171d1102ef0c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245344"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Zelf studie: workday configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren om de werk profielen van workday in te richten op een on-premises Active Directory (AD).

>[!NOTE]
>Gebruik deze zelf studie als u wilt dat de gebruikers die u wilt inrichten vanuit workday, een on-premises AD-account en een Azure AD-account nodig hebben. 
>* Als de gebruikers van workday alleen een Azure AD-account nodig hebben (alleen Cloud gebruikers), raadpleegt u de zelf studie over het [configureren van workday naar Azure AD](workday-inbound-cloud-only-tutorial.md) User provisioning. 
>* Voor het configureren van write-back van kenmerken, zoals e-mail adres, gebruikers naam en telefoon nummer van Azure AD naar workday, raadpleegt u de zelf studie over het [configureren van workday-terugschrijven](workday-writeback-tutorial.md).


## <a name="overview"></a>Overzicht

De [Azure Active Directory User Provisioning Service](../app-provisioning/user-provisioning.md) kan worden geïntegreerd met de [HR Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) om gebruikers accounts in te richten. De werk stroom die door de gebruiker wordt ondersteund door de Azure AD User Provisioning-Service, biedt de mogelijkheid om de volgende scenario's voor human resources en Identity Lifecycle Management te automatiseren:

* **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan workday, wordt er automatisch een gebruikers account gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die door Azure AD worden ondersteund](../app-provisioning/user-provisioning.md), met een back-up van IT-beheerde contact gegevens naar workday.

* **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in workday (zoals hun naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Beëindiging van werk nemers** : wanneer een werk nemer wordt beëindigd in workday, wordt het gebruikers account automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* Opnieuw **inhuren van werk nemers** : wanneer een werk nemer in workday opnieuw wordt ingehuurd, kan het oude account automatisch opnieuw worden geactiveerd of worden ingericht (afhankelijk van uw voor keur) tot Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

### <a name="whats-new"></a>Nieuwe functies
In deze sectie worden verbeteringen voor de recente werk dagen van integratie vastgelegd. Voor een lijst met uitgebreide updates, geplande wijzigingen en archieven gaat u naar de pagina [Wat is er nieuw in azure Active Directory?](../fundamentals/whats-new.md) 

* **Kan 2020-de mogelijkheid om telefoon nummers naar workday te terugschrijven:** Naast e-mail en gebruikers naam kunt u nu een telefoon nummer en een mobiel telefoon nummer van Azure AD naar werkdag terugschrijven. Raadpleeg de [zelf studie write-app](workday-writeback-tutorial.md)voor meer informatie.

* **April 2020-ondersteuning voor de nieuwste versie van WWS-API (Web Services):** Twee maal per jaar in maart en september, werk dagen bieden geavanceerde updates die u helpen bij de doel stellingen van uw bedrijf en het wijzigen van de wensen van uw personeel. U kunt nu direct de API-versie van WWS opgeven die u wilt gebruiken in de verbindings-URL om de nieuwe functies van de werkdag te laten blijven. Raadpleeg de sectie over het configureren van de [werkdag connectiviteit](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)voor meer informatie over het opgeven van de workday API-versie. 

* **Jan 2020-mogelijkheid om AD accountExpires-kenmerk in te stellen:** Met de functie [NumFromDate](../app-provisioning/functions-for-customizing-application-data.md#numfromdate) kunt u nu datum velden voor workday, zoals *EndContractDate* of *StatusTerminationDate*, toewijzen. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

De oplossing voor de gebruikers inrichting van deze werkdag is in het ideale geval geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op de cloud gebaseerde oplossing voor het inrichten van gebruikers met werk dagen

* Organisaties waarvoor directe gebruikers inrichting van workday naar Active Directory is vereist of Azure Active Directory

* Organisaties waarvoor gebruikers moeten worden ingericht met gegevens die zijn verkregen via de HCM-module workday (Zie [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisaties die aan een of meer Active Directory-forests, domeinen en organisatie-eenheden moeten worden gesynchroniseerd, worden verplaatst en verlaten, alleen op basis van gewijzigde informatie die is gedetecteerd in de HCM-module van de werkdag (Zie [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur van de oplossing

In deze sectie wordt de end-to-end-oplossings architectuur voor gebruikers ingericht voor algemene hybride omgevingen beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevens stroom: van werkdag naar on-premises Active Directory:** In deze stroom worden werk gebeurtenissen (zoals nieuwe aanmeldingen, overdrachten, afsluitingen) voor het eerst uitgevoerd in de Cloud workday HR-Tenant en vervolgens worden de gebeurtenis gegevens stromen naar on-premises Active Directory via Azure AD en de inrichtings agent. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in AD.
* **Write-back-flow: van on-premises Active Directory naar werkdag:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kunnen gegevens zoals e-mail, gebruikers naam en telefoon nummer worden teruggeschreven naar workday.

![Overzicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevens stroom van end-to-end-gebruikers

1. Het HR-team voert werknemers transacties (samenvoegers/verplaatsingen/Leavers of nieuwe huur/overdrachten/afsluitingen) uit in workday HCM
2. De Azure AD-inrichtings service voert geplande synchronisaties van identiteiten uit workday HR uit en identificeert wijzigingen die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtings service roept de on-premises Azure AD Connect inrichtings agent aan met een aanvraag lading die AD-account maken/bijwerken/inschakelen/uitschakelen heeft.
4. De Azure AD Connect-inrichtings agent gebruikt een service account om AD-account gegevens toe te voegen of bij te werken.
5. De Azure AD Connect-AD Sync-Engine voert Delta synchronisatie uit voor het ophalen van updates in AD.
6. De Active Directory updates worden gesynchroniseerd met Azure Active Directory.
7. Als de app voor het [terugschrijven](workday-writeback-tutorial.md) van werk dagen is geconfigureerd, worden de kenmerken, zoals e-mail, gebruikers naam en telefoon nummer, naar werkdag geschreven.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Voor het configureren van een werkdag naar Active Directory gebruikers inrichten is een aanzienlijke planning vereist voor verschillende aspecten, zoals:
* Installatie van de Azure AD Connect-inrichtings agent 
* Aantal werk dagen dat AD-gebruikers apps kunnen implementeren
* Het selecteren van het juiste overeenkomende id-, kenmerk toewijzings-, transformatie-en bereik filters

Raadpleeg het [implementatie plan voor Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richt lijnen en aanbevolen procedures. 

## <a name="configure-integration-system-user-in-workday"></a>De gebruiker van het integratie systeem configureren in workday

Een gemeen schappelijke eis van alle connectors voor workday-inrichting is dat er referenties moeten worden vereist van een workday-integratie systeem gebruiker om verbinding te maken met de HR Human Resources API. In deze sectie wordt beschreven hoe u een integratie systeem gebruiker in workday maakt en de volgende secties heeft:

* [Een integratie systeem gebruiker maken](#creating-an-integration-system-user)
* [Een integratie beveiligings groep maken](#creating-an-integration-security-group)
* [Machtigingen voor domein beveiligings beleid configureren](#configuring-domain-security-policy-permissions)
* [Machtigingen voor het beveiligings beleid voor het bedrijfs proces configureren](#configuring-business-process-security-policy-permissions)
* [Wijzigingen in het beveiligings beleid activeren](#activating-security-policy-changes)

> [!NOTE]
> U kunt deze procedure overs Laan en in plaats daarvan een algemeen Administrator-account van workday gebruiken als systeem integratie account. Dit werkt mogelijk prima voor demo's, maar wordt niet aanbevolen voor productie-implementaties.

### <a name="creating-an-integration-system-user"></a>Een integratie systeem gebruiker maken

**Een gebruiker van het integratie systeem maken:**

1. Meld u met een beheerders account aan bij uw workday-Tenant. Voer in de werk **dagen-app**gebruiker maken in het zoekvak in en klik vervolgens op **integratie systeem gebruiker maken**.

   >[!div class="mx-imgBorder"] 
   >![Gebruiker maken](./media/workday-inbound-tutorial/wd_isu_01.png "Gebruiker maken")
2. Voltooi de **gebruikers taak integratie systeem maken** door een gebruikers naam en wacht woord op te geven voor een nieuwe gebruiker van het integratie systeem.  
  
   * Laat de optie **Nieuw wacht woord vereisen bij volgende aanmelding** uitgeschakeld, omdat deze gebruiker via een programma wordt aangemeld.
   * Houd de **time-outwaarde** voor de sessie door de standaard waarde van 0, waardoor de sessie van de gebruiker niet tijdig kan verlopen.
   * Selecteer de optie **geen UI-sessies toestaan** omdat deze een extra beveiligingslaag biedt waarmee wordt voor komen dat een gebruiker het wacht woord van het integratie systeem kan aanmelden bij workday.

   > [!div class="mx-imgBorder"]
   > ![Integratie systeem gebruiker maken](./media/workday-inbound-tutorial/wd_isu_02.png "Integratie systeem gebruiker maken")

### <a name="creating-an-integration-security-group"></a>Een integratie beveiligings groep maken

In deze stap maakt u een onbeperkte of beperkte beveiligings groep voor integratie systemen in workday en wijst u de gebruiker van het integratie systeem dat in de vorige stap is gemaakt, toe aan deze groep.

**Een beveiligings groep maken:**

1. Voer in het zoekvak beveiligings groep maken in en klik vervolgens op **beveiligings groep maken**.

   > [!div class="mx-imgBorder"]
   > ![CreateSecurity-groep](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity-groep")
2. Voltooi de taak **beveiligings groep maken** . 

   * Er zijn twee typen beveiligings groepen in workday:
     * **Onbeperkt:** Alle leden van de beveiligings groep hebben toegang tot alle gegevens instanties die zijn beveiligd door de beveiligings groep.
     * **Beperkt:** Alle leden van de beveiligings groep hebben contextuele toegang tot een subset van gegevens instanties (rijen) waartoe de beveiligings groep toegang heeft.
   * Neem contact op met uw werkdag integratie partner om het juiste type beveiligings groep voor de integratie te selecteren.
   * Zodra u het groeps type kent, selecteert u **integratie systeem beveiligings groep (onbeperkt)** of **integratie systeem beveiligings groep (beperkt)** van het type van de vervolg keuzelijst **met tenantve beveiligings groep** .

     > [!div class="mx-imgBorder"]
     >![CreateSecurity-groep](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity-groep")

3. Nadat het maken van de beveiligings groep is voltooid, ziet u een pagina waar u leden kunt toewijzen aan de beveiligings groep. Voeg de nieuwe gebruiker van het integratie systeem die in de vorige stap is gemaakt, toe aan deze beveiligings groep. Als u een *beperkte* beveiligings groep gebruikt, moet u ook het juiste organisatie bereik selecteren.

   >[!div class="mx-imgBorder"]
   >![Beveiligings groep bewerken](./media/workday-inbound-tutorial/wd_isu_05.png "Beveiligings groep bewerken")

### <a name="configuring-domain-security-policy-permissions"></a>Machtigingen voor domein beveiligings beleid configureren

In deze stap verleent u machtigingen voor domein beleid voor de gegevens van de werk nemer aan de beveiligings groep.

**Machtigingen voor domein beveiligings beleid configureren:**

1. Voer **domein beveiligings configuratie** in het zoekvak in en klik vervolgens op het **beveiligings configuratie rapport**van het domein koppelen.  
   >[!div class="mx-imgBorder"]
   >![Beveiligings beleid voor domein](./media/workday-inbound-tutorial/wd_isu_06.png "Beveiligings beleid voor domein")  
2. Zoek in het tekstvak **domein** naar de volgende domeinen en voeg deze toe aan het filter met één voor één.  
   * *Externe account inrichten*
   * *Werknemers gegevens: werk rollen*
   * *Werknemers gegevens: rapporten van open bare werk nemers*
   * *Persoons gegevens: contact gegevens voor werk*
   * *Werknemers gegevens: alle posities*
   * *Werknemers gegevens: huidige informatie over personeel*
   * *Werknemers gegevens: zakelijke titel op het werknemers profiel*
   * *Workday-accounts*
   
     >[!div class="mx-imgBorder"]
     >![Beveiligings beleid voor domein](./media/workday-inbound-tutorial/wd_isu_07.png "Beveiligings beleid voor domein")  

     >[!div class="mx-imgBorder"]
     >![Beveiligings beleid voor domein](./media/workday-inbound-tutorial/wd_isu_08.png "Beveiligings beleid voor domein") 

     Klik op **OK**.

3. In het rapport dat wordt weer gegeven, selecteert u het weglatings teken (...) dat naast het **inrichten van externe accounts** wordt weer gegeven en klikt u op de menu optie **domein-> machtigingen voor beveiligings beleid bewerken**
   >[!div class="mx-imgBorder"]
   >![Beveiligings beleid voor domein](./media/workday-inbound-tutorial/wd_isu_09.png "Beveiligings beleid voor domein")  

4. Blader op de pagina **machtigingen voor domein beveiligings beleid bewerken** omlaag naar de sectie **integratie machtigingen**. Klik op het plus teken (+) om de integratie systeem groep toe te voegen aan de lijst met beveiligings groepen met de machtigingen **Get** en **put** .
   >[!div class="mx-imgBorder"]
   >![Machtiging bewerken](./media/workday-inbound-tutorial/wd_isu_10.png "Machtiging bewerken")  

5. Klik op het plus teken (+) om de integratie systeem groep toe te voegen aan de lijst met beveiligings groepen met de machtigingen **Get** en **put** .

   >[!div class="mx-imgBorder"]
   >![Machtiging bewerken](./media/workday-inbound-tutorial/wd_isu_11.png "Machtiging bewerken")  

6. Herhaal stap 3-5 hierboven voor elk van deze resterende beveiligings beleidsregels:

   | Bewerking | Beveiligings beleid voor domein |
   | ---------- | ---------- |
   | Ophalen en plaatsen | Werknemers gegevens: rapporten van open bare werk nemers |
   | Ophalen en plaatsen | Persoons gegevens: contact gegevens voor werk |
   | Ophalen | Werknemers gegevens: werk rollen |
   | Ophalen | Werknemers gegevens: alle posities |
   | Ophalen | Werknemers gegevens: huidige informatie over personeel |
   | Ophalen | Werknemers gegevens: zakelijke titel op het werknemers profiel |
   | Ophalen en plaatsen | Workday-accounts |

### <a name="configuring-business-process-security-policy-permissions"></a>Machtigingen voor het beveiligings beleid voor het bedrijfs proces configureren

In deze stap geeft u de beleids machtigingen ' beveiliging van bedrijfs processen ' voor de werk gegevens aan de beveiligings groep. 

> [!NOTE]
> Deze stap is alleen vereist voor het instellen van de back-uptoepassing voor werkdag.

**Machtigingen voor het beveiligings beleid voor het bedrijfs proces configureren:**

1. Voer het **beleid voor bedrijfs processen** in het zoekvak in en klik vervolgens op de taak koppeling **bewerken beveiligings beleid voor bedrijfs processen** .  

   >[!div class="mx-imgBorder"]
   >![Beveiligings beleid voor bedrijfs processen](./media/workday-inbound-tutorial/wd_isu_12.png "Beveiligings beleid voor bedrijfs processen")  

2. In het tekstvak **bedrijfsproces type** zoekt u naar *contact* en selecteert u zakelijke proces **contact persoon wijzigen** en klikt u op **OK**.

   >[!div class="mx-imgBorder"]
   >![Beveiligings beleid voor bedrijfs processen](./media/workday-inbound-tutorial/wd_isu_13.png "Beveiligings beleid voor bedrijfs processen")  

3. Ga op de pagina **beveiligings beleid voor bedrijfs processen bewerken** naar de sectie **werk contact gegevens wijzigen (webservice)** .
    

4. Selecteer de nieuwe beveiligings groep integratie systeem en voeg deze toe aan de lijst met beveiligings groepen die de webservices aanvragen kunnen initiëren. 

   >[!div class="mx-imgBorder"]
   >![Beveiligings beleid voor bedrijfs processen](./media/workday-inbound-tutorial/wd_isu_15.png "Beveiligings beleid voor bedrijfs processen")  

5. Klik op **gereed**. 

### <a name="activating-security-policy-changes"></a>Wijzigingen in het beveiligings beleid activeren

**Wijzigingen in het beveiligings beleid activeren:**

1. Voer activate in het zoekvak in en klik op de koppeling **wijzigingen in behandeling van beveiligings beleid activeren**.
   >[!div class="mx-imgBorder"]
   >![Inschakelen](./media/workday-inbound-tutorial/wd_isu_16.png "Activeren")

1. Start de taak wijzigingen in wachtend op beveiligings beleid activeren door een opmerking in te voeren voor controle doeleinden en klik vervolgens op **OK**.
1. Voltooi de taak op het volgende scherm door het selectie vakje **bevestigen**te controleren en klik vervolgens op **OK**.

   >[!div class="mx-imgBorder"]
   >![Beveiliging in behandeling activeren](./media/workday-inbound-tutorial/wd_isu_18.png "Beveiliging in behandeling activeren")  

## <a name="configure-active-directory-service-account"></a>Active Directory-Service account configureren

In deze sectie worden de machtigingen voor AD-service accounts beschreven die nodig zijn voor het installeren en configureren van de Azure AD Connect inrichtings agent.

### <a name="permissions-required-to-run-the-provisioning-agent-installer"></a>Benodigde machtigingen voor het uitvoeren van het installatie programma voor de inrichtings agent
Wanneer u de Windows-Server die als host fungeert voor de inrichtings agent hebt geïdentificeerd, meldt u zich aan bij de server host met de referenties van de lokale beheerder of de domein beheerder. Het installatie proces van de agent maakt referentie bestanden voor de opslag van beveiligde sleutels en werkt de service profiel configuratie op de hostserver bij. Hiervoor moet u beheerders toegang hebben op de server die als host fungeert voor de agent. 

### <a name="permissions-required-to-configure-the-provisioning-agent-service"></a>Benodigde machtigingen voor het configureren van de service voor de inrichtings agent
Gebruik de volgende stappen om een service account in te stellen dat kan worden gebruikt voor het inrichten van agent bewerkingen. 
1.  Open Active Directory-module *gebruikers en computers* op de AD-domein controller. 
2.  Een nieuwe domein gebruiker maken (voor beeld: *provAgentAdmin*)  
3.  Klik met de rechter muisknop op de organisatie-eenheid of de domein naam en selecteer *beheer delegeren* , waarmee de *wizard Overdracht van beheer*wordt geopend. 

> [!NOTE] 
> Als u de inrichtings agent wilt beperken tot het maken en lezen van gebruikers van een bepaalde organisatie-eenheid voor test doeleinden, raden we u aan het besturings element tijdens de test uitvoeringen op het juiste OE-niveau te delegeren.

4. Klik op **volgende** in het welkomst scherm. 
5. Voeg op het scherm **gebruikers of groepen selecteren** de domein gebruiker toe die u in stap 2 hebt gemaakt. Klik op **Volgende**.
   >[!div class="mx-imgBorder"]
   >![Scherm toevoegen](./media/workday-inbound-tutorial/delegation-wizard-01.png "Scherm toevoegen")

6. Selecteer op het scherm **taken die u wilt delegeren** de volgende taken: 
   * Gebruikers accounts maken, verwijderen en beheren
   * Alle gebruikers gegevens lezen

   >[!div class="mx-imgBorder"]
   >![Scherm taken](./media/workday-inbound-tutorial/delegation-wizard-02.png "Scherm taken")

7. Klik op **volgende** en **Sla** de configuratie op


## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Gebruikers inrichten van workday naar Active Directory configureren

In deze sectie vindt u de stappen voor het inrichten van gebruikers accounts van workday naar elk Active Directory domein binnen het bereik van uw integratie.

* [De inrichtings connector-app toevoegen en de inrichtings agent downloaden](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [On-premises inrichtings agent (en) installeren en configureren](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Connectiviteit met workday en Active Directory configureren](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Kenmerk toewijzingen configureren](#part-4-configure-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Deel 1: de inrichtings connector-app toevoegen en de inrichtings agent downloaden

**Werk dagen configureren voor Active Directory inrichting:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer de categorie **alle** .

5. Zoek **naar werk dagen om de gebruikers inrichting te Active Directory**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting**.

7. Wijzig de **inrichtings** **modus** in **automatisch**.

8. Klik op het informatie banner dat wordt weer gegeven om de inrichtings agent te downloaden. 

   >[!div class="mx-imgBorder"]
   >![Agent downloaden](./media/workday-inbound-tutorial/pa-download-agent.png "Scherm van de agent downloaden")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Deel 2: on-premises inrichtings agent (en) installeren en configureren

Als u een on-premises Active Directory wilt inrichten, moet de inrichtings agent zijn geïnstalleerd op een server met .NET 4.7.1 + Framework en netwerk toegang tot de gewenste Active Directory domein (en).

> [!TIP]
> U kunt de versie van .NET Framework op uw server controleren met behulp van de instructies die u [hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)kunt vinden.
> Als op de server geen .NET 4.7.1 of hoger is geïnstalleerd, kunt u deze [hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)downloaden.  

Zet het installatie programma van de gedownloade agent over naar de server host en volg de onderstaande stappen om de configuratie van de agent te volt ooien.

1. Meld u aan bij de Windows-Server waarop u de nieuwe agent wilt installeren.

1. Start het installatie programma voor de inrichtings agent, ga akkoord met de voor waarden en klik op de knop **installeren** .

   >[!div class="mx-imgBorder"]
   >![Scherm installeren](./media/workday-inbound-tutorial/pa_install_screen_1.png "Scherm installeren")
   
1. Nadat de installatie is voltooid, wordt de wizard gestart en wordt het scherm **verbinding maken met Azure AD** weer gegeven. Klik op de knop **verifiëren** om verbinding te maken met uw Azure AD-exemplaar.

   >[!div class="mx-imgBorder"]
   >![Verbinding maken met Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Verbinding maken met Azure AD")
   
1. Verifieer uw Azure AD-exemplaar met behulp van de referenties van de hybride identiteits beheerder.

   >[!div class="mx-imgBorder"]
   >![Beheerder auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Beheerder auth")

   > [!NOTE]
   > De referenties van de Azure AD-beheerder worden alleen gebruikt om verbinding te maken met uw Azure AD-Tenant. De-agent slaat de referenties niet lokaal op de server op.

1. Nadat de verificatie met Azure AD is geslaagd, wordt het scherm **verbinding maken Active Directory** weer gegeven. In deze stap voert u de naam van uw AD-domein in en klikt u op de knop **map toevoegen** .

   >[!div class="mx-imgBorder"]
   >![Map toevoegen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Map toevoegen")
  
1. U wordt nu gevraagd om de referenties in te voeren die vereist zijn om verbinding te maken met het AD-domein. Op hetzelfde scherm kunt u de **prioriteit domein controller selecteren** gebruiken om domein controllers op te geven die de agent moet gebruiken voor het verzenden van inrichtings aanvragen.

   >[!div class="mx-imgBorder"]
   >![Domein referenties](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Na het configureren van het domein, wordt in het installatie programma een lijst met geconfigureerde domeinen weer gegeven. Op dit scherm kunt u stap #5 herhalen en #6 om meer domeinen toe te voegen of klikt u op **volgende** om door te gaan naar de agent registratie.

   >[!div class="mx-imgBorder"]
   >![Geconfigureerde domeinen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Geconfigureerde domeinen")

   > [!NOTE]
   > Als u meerdere AD-domeinen hebt (bijvoorbeeld na.contoso.com, emea.contoso.com), moet u elk domein afzonderlijk toevoegen aan de lijst.
   > Het is niet voldoende om het bovenliggende domein (bijvoorbeeld contoso.com) toe te voegen. U moet elk onderliggend domein bij de agent registreren.
   
1. Controleer de configuratie gegevens en klik op **bevestigen** om de agent te registreren.
  
   >[!div class="mx-imgBorder"]
   >![Scherm bevestigen](./media/workday-inbound-tutorial/pa_install_screen_7.png "Scherm bevestigen")
   
1. De configuratie wizard geeft de voortgang weer van de registratie van de agent.
  
   >[!div class="mx-imgBorder"]
   >![Agent registratie](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent registratie")
   
1. Zodra de registratie van de agent is geslaagd, kunt u op **Afsluiten** klikken om de wizard af te sluiten.

   >[!div class="mx-imgBorder"]
   >![Scherm afsluiten](./media/workday-inbound-tutorial/pa_install_screen_9.png "Scherm afsluiten")
   
1. Controleer de installatie van de agent en zorg ervoor dat deze wordt uitgevoerd door de module ' Services ' te openen en te zoeken naar de service met de naam ' Microsoft Azure AD Connect inrichtings agent '.

   >[!div class="mx-imgBorder"]
   >![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Deel 3: in de inrichtings-app de verbinding configureren voor workday en Active Directory
In deze stap maken we verbinding met werkdag en Active Directory in de Azure Portal. 

1. Ga in de Azure Portal terug naar de werkdag om de app te Active Directory voor het inrichten van gebruikers die is gemaakt in [deel 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Voer de sectie **beheerders referenties** als volgt uit:

   * **Workday-gebruikers naam** : Voer de gebruikers naam in van het werk account van het workday-integratie systeem, waarbij de domein naam van de Tenant is toegevoegd. Het moet er ongeveer als volgt uitzien: **username \@ tenant_name**

   * **Wacht woord voor werk dagen –** Voer het wacht woord in voor het workday-integratie systeem account

   * **Workday Web Services API-URL:** Voer de URL naar het workday Web Services-eind punt voor uw Tenant in. De URL bepaalt de versie van de workday Web Services API die door de connector wordt gebruikt. 
   
     | URL-indeling | WWS API-versie gebruikt | XPATH-wijzigingen vereist |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | Nee |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | Nee |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Ja |

      > [!NOTE]
     > Als er geen versie gegevens zijn opgegeven in de URL, gebruikt de app workday Web Services (WWS) v 21.1 en zijn er geen wijzigingen vereist voor de standaard XPATH API-expressies die worden geleverd bij de app. Als u een specifieke API-versie van WWS wilt gebruiken, geeft u het versie nummer op in de URL <br>
     > Voorbeeld: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Als u een WWS API v 30.0 + gebruikt voordat u de inrichtings taak inschakelt, moet u **de API-expressies van XPath** bijwerken onder **kenmerk toewijzing-> geavanceerde opties-> kenmerk lijst bewerken voor workday** verwijzen naar de sectie [uw configuratie](#managing-your-configuration) en [workday-kenmerk verwijzing](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)beheren.  

   * **Active Directory-forest-** De naam van uw Active Directory domein, zoals dit is geregistreerd bij de agent. Gebruik de vervolg keuzelijst om het doel domein te selecteren dat u wilt inrichten. Deze waarde is doorgaans een teken reeks zoals: *contoso.com*

   * **Active Directory-container-** Voer de container-DN in waar de agent standaard gebruikers accounts moet maken.
        Voor beeld: *OE = standaard gebruikers, ou = gebruikers, DC = contoso, dc = test*
        
     > [!NOTE]
     > Deze instelling is alleen beschikbaar voor het aanbrengen van gebruikers accounts als het kenmerk *parentDistinguishedName* niet is geconfigureerd in de kenmerk toewijzingen. Deze instelling wordt niet gebruikt voor zoek-of update bewerkingen voor gebruikers. De gehele onderliggende domein structuur valt binnen het bereik van de zoek bewerking.

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.

     > [!NOTE]
     > De Azure AD-inrichtings service verzendt een e-mail melding als de inrichtings taak een [quarantaine](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) status heeft.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de workday-referenties en de AD-referenties die zijn geconfigureerd voor de installatie van de agent geldig zijn.

     >[!div class="mx-imgBorder"]
     >![Azure-portal](./media/workday-inbound-tutorial/wd_1.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **toewijzingen** de standaard toewijzing voor **werk dagen op locatie Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Deel 4: kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van workday naar Active Directory.

1. Klik op het tabblad inrichting onder **toewijzingen**op **werk dagen synchroniseren met on-premises Active Directory**.

1. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in workday het bereik moeten hebben voor het INRICHTEN van AD door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in workday. Voorbeeld filters:

   * Voor beeld: bereik voor gebruikers met werk nemer-Id's tussen 1000000 en 2000000 (m.u.v. 2000000)

      * Kenmerk: WorkerID

      * Operator: overeenkomende REGEX

      * Waarde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Voor beeld: alleen werk nemers en niet-voorwaardelijke werk nemers

      * Kenmerk: EmployeeID

      * Operator: IS niet NULL

   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerk toewijzingen en expressies testen en controleren om ervoor te zorgen dat het gewenste resultaat wordt weer geven. Micro soft raadt aan om de bereik filters onder het bereik van de **bron object** te gebruiken om uw toewijzingen te testen met een aantal test gebruikers van workday. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

   > [!CAUTION] 
   > Het standaard gedrag van de inrichtings engine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw werkdag tot AD-integratie. Als u dit standaard gedrag wilt overschrijven, raadpleegt u het artikel [verwijdering overs laan van gebruikers accounts die buiten het bereik](../app-provisioning/skip-out-of-scope-deletions.md) vallen
  
1. In het veld **acties doel object** kunt u globaal filteren welke acties er worden uitgevoerd op Active Directory. **Create** en **Update** worden het meest gebruikt.

1. In de sectie **kenmerk toewijzingen** kunt u definiëren hoe afzonderlijke workday-kenmerken worden toegewezen aan Active Directory kenmerken.

1. Klik op een bestaande kenmerk toewijzing om het bij te werken of Klik onder aan het scherm op **nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een individuele kenmerk toewijzing ondersteunt de volgende eigenschappen:

      * **Toewijzings type**

         * **Direct** : schrijft de waarde van het kenmerk workday naar het kenmerk AD, zonder wijzigingen

         * **Constante** : een statische, constante teken reeks waarde schrijven naar het AD-kenmerk

         * **Expressie** : Hiermee kunt u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer workday-kenmerken. [Zie dit artikel over expressies voor meer informatie](../app-provisioning/functions-for-customizing-application-data.md).

      * **Bron kenmerk** : het gebruikers kenmerk van workday. Als het kenmerk dat u zoekt niet aanwezig is, raadpleegt u [de lijst met gebruikers kenmerken voor workday aanpassen](#customizing-the-list-of-workday-user-attributes).

      * **Standaard waarde** : optioneel. Als het bron kenmerk een lege waarde heeft, wordt deze waarde in plaats daarvan door de toewijzing geschreven.
            De meest voorkomende configuratie is om dit leeg te laten.

      * **Doel kenmerk** : het gebruikers kenmerk in Active Directory.

      * **Objecten met dit kenmerk matchen** : bepaalt of deze toewijzing moet worden gebruikt om gebruikers te identificeren tussen Workday en Active Directory. Deze waarde wordt doorgaans ingesteld op het veld werk nemer-ID voor workday, die meestal wordt toegewezen aan een van de kenmerken van de werk nemer-ID in Active Directory.

      * **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing Toep assen**

         * **Altijd** : deze toewijzing Toep assen op zowel het maken van gebruikers als bij het bijwerken van acties

         * **Alleen tijdens het maken** : pas deze toewijzing alleen toe bij het maken van gebruikers acties

1. Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.
   >[!div class="mx-imgBorder"]
   >![Azure-portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Hieronder ziet u enkele voor beelden van kenmerk toewijzingen tussen werk dagen en Active Directory, met enkele algemene expressies

* De expressie die wordt toegewezen aan het kenmerk *parentDistinguishedName* wordt gebruikt om een gebruiker in te richten op verschillende organisatie-eenheden op basis van een of meer workday-bron kenmerken. In dit voor beeld worden gebruikers in verschillende organisatie-eenheden geplaatst op basis van de plaats waar ze zich bevinden.

* Het kenmerk *userPrincipalName* in Active Directory wordt gegenereerd met behulp van de [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) -functie voor het maken van een gegenereerde waarde in het doel-AD-domein en stelt deze alleen in als deze uniek is.  

* [Hier vindt u documentatie over het schrijven van expressies](../app-provisioning/functions-for-customizing-application-data.md). In deze sectie vindt u voor beelden van het verwijderen van speciale tekens.

| KENMERK WORKDAY | ACTIVE DIRECTORY-KENMERK |  OVEREENKOMENDE ID? | MAKEN/BIJWERKEN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Geschreven bij alleen maken |
| **PreferredNameData**    |  genoemd    |   |   Geschreven bij alleen maken |
| **SelectUniqueValue (toevoegen (" \@ ", samen voegen (".", voor \[ naam \] , \[ achternaam \] ), "contoso.com"), samen voegen (" \@ ", samen voegen (".", Mid ( \[ FirstName \] , 1, 1), \[ LastName \] ), "contoso.com"), deel nemen (" \@ ", lid (".", Mid ( \[ FirstName \] , 1, 2), \[ LastName \] ), "contoso.com"**   | userPrincipalName     |     | Geschreven bij alleen maken 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Geschreven bij alleen maken |
| **Switch ( \[ actief \] ,, "0", "True", "1", "false")** |  accountDisabled      |     | + Update maken |
| **Voor**   | givenName       |     |    + Update maken |
| **Naam**   |   sn   |     |  + Update maken |
| **PreferredNameData**  |  displayName |     |   + Update maken |
| **Company**         | bedrijf   |     |  + Update maken |
| **SupervisoryOrganization**  | department  |     |  + Update maken |
| **ManagerReference**   | manager  |     |  + Update maken |
| **BusinessTitle**   |  titel     |     |  + Update maken | 
| **AddressLineData**    |  streetAddress  |     |   + Update maken |
| **Gemeenschap**   |   l   |     | + Update maken |
| **CountryReferenceTwoLetter**      |   co |     |   + Update maken |
| **CountryReferenceTwoLetter**    |  c  |     |         + Update maken |
| **CountryRegionReference** |  st     |     | + Update maken |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  + Update maken |
| **Code**  |   Code  |     | + Update maken |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | + Update maken |
| **Fax**      | facsimileTelephoneNumber     |     |    + Update maken |
| **Mobiel**  |    mobiel       |     |       + Update maken |
| **LocalReference** |  preferredLanguage  |     |  + Update maken |                                               
| **Switch ( \[ gemeente \] , "OE = standaard gebruikers, DC = CONTOSO, DC = com", "Rotterdam", "OE = Rotterdam, OU = Users, DC = CONTOSO, DC = com", "Austin", "OE = Austin, ou = gebruikers, DC = CONTOSO, DC = com", "Seattle", "OE = Seattle, ou = gebruikers, DC = CONTOSO, DC = com", "Londen", "OE = Londen, ou = gebruikers, DC = CONTOSO, DC = com")**  | parentDistinguishedName     |     |  + Update maken |

Zodra de configuratie van de kenmerk toewijzing is voltooid, kunt u [de User Provisioning Service nu inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Gebruikers inrichting inschakelen en starten

Zodra de configuratie van de app voor workday-inrichting is voltooid, kunt u de inrichtings service inschakelen in de Azure Portal.

> [!TIP]
> Wanneer u de inrichtings service inschakelt, worden er standaard inrichtings bewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten optreden in de toewijzings-of workday-gegevens problemen, kan de inrichtings taak mislukken en gaat u naar de quarantaine status. Om dit te voor komen best practice, raden we u aan om het bereik filter voor **bron objecten** te configureren en uw kenmerk toewijzingen te testen met enkele test gebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en u de gewenste resultaten krijgt, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Stel op het tabblad **inrichten** de **inrichtings status** in **op aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, wat een variabel aantal uur kan duren, afhankelijk van het aantal gebruikers in de workday-Tenant. 

4. Controleer op elk gewenst moment het tabblad **controle logboeken** in de Azure Portal om te zien welke acties de inrichtings service heeft uitgevoerd. In de controle logboeken worden alle afzonderlijke synchronisatie gebeurtenissen weer gegeven die door de inrichtings service worden uitgevoerd, bijvoorbeeld welke gebruikers worden gelezen uit de werk dagen en vervolgens worden toegevoegd of bijgewerkt aan Active Directory. Raadpleeg de sectie probleem oplossing voor instructies over het controleren van de audit logboeken en het oplossen van inrichtings fouten.

5. Zodra de initiële synchronisatie is voltooid, wordt een overzichts rapport van de controle op het tabblad **inrichten** geschreven, zoals hieronder wordt weer gegeven.
   > [!div class="mx-imgBorder"]
   > ![Voortgangs balk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)



## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

* **Vragen over oplossings mogelijkheden**
  * [Hoe stelt de oplossing bij het verwerken van een nieuwe huur bewerking vanuit workday het wacht woord in voor het nieuwe gebruikers account in Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Biedt de oplossing ondersteuning voor het verzenden van e-mail meldingen nadat de inrichtings bewerkingen zijn voltooid?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hoe kan ik de levering van wacht woorden voor nieuwe mede werkers beheren en een mechanisme veilig bieden om hun wacht woord opnieuw in te stellen?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Worden de gebruikers profielen van de oplossing in de Azure AD-Cloud of in de laag van de inrichtings agent in de cache opgeslagen?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Ondersteunt de oplossing de toewijzing van on-premises AD-groepen aan de gebruiker?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Welke workday-Api's gebruiken de oplossing voor het opvragen en bijwerken van werk profielen voor workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kan ik mijn workday HCM-Tenant configureren met twee Azure AD-tenants?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Waarom wordt het gebruik van de app voor het inrichten van een werkdag naar Azure AD niet ondersteund als er Azure AD Connect zijn geïmplementeerd?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hoe kan ik verbeteringen Voorst Ellen of nieuwe functies met betrekking tot workday en Azure AD-integratie aanvragen?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Vragen over het inrichten van agents**
  * [Wat is de GA-versie van de inrichtings agent?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hoe kan ik weet ik welke versie van mijn inrichtings agent u hebt?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Pusht micro soft automatisch de updates voor de inrichtings agent?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kan ik de inrichtings agent installeren op dezelfde server met Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hoe kan ik de inrichtings agent configureren voor het gebruik van een proxy server voor uitgaande HTTP-communicatie?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hoe kan ik ervoor te zorgen dat de inrichtings agent kan communiceren met de Azure AD-Tenant en dat er geen firewalls zijn die vereist zijn voor de agent?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hoe kan ik de registratie van het domein dat is gekoppeld aan mijn inrichtings agent?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [De inrichtings agent Hoe kan ik verwijderen?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Vragen over het toewijzen en configureren van workday aan AD-kenmerk**
  * [Hoe kan ik back-up maken of exporteren van een werk kopie van het kenmerk toewijzing en schema voor het inrichten van workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Ik heb aangepaste kenmerken in workday en Active Directory. Hoe kan ik de oplossing configureren voor gebruik met mijn aangepaste kenmerken?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kan ik de foto van de gebruiker van werkdag tot Active Directory inrichten?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hoe kan ik mobiele nummers van workday synchroniseren op basis van de toestemming van de gebruiker voor openbaar gebruik?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hoe kan ik indeling weer geven van namen in AD op basis van de kenmerken afdeling/land/plaats van gebruiker en regionale afwijkingen afhandelen?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hoe kan ik SelectUniqueValue gebruiken om unieke waarden voor het kenmerk samAccountName te genereren?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hoe kan ik tekens verwijderen en deze converteren naar gewone Engelse alfabetten?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Vragen over oplossings mogelijkheden

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Hoe stelt de oplossing bij het verwerken van een nieuwe huur bewerking vanuit workday het wacht woord in voor het nieuwe gebruikers account in Active Directory?

Wanneer de on-premises inrichtings agent een aanvraag voor het maken van een nieuw AD-account krijgt, genereert het automatisch een complex wille keurig wacht woord dat is ontworpen om te voldoen aan de vereisten voor wachtwoord complexiteit die zijn gedefinieerd door de AD-server en wordt deze ingesteld op het gebruikers object. Dit wacht woord wordt nergens vastgelegd.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Biedt de oplossing ondersteuning voor het verzenden van e-mail meldingen nadat de inrichtings bewerkingen zijn voltooid?

Nee, het verzenden van e-mail meldingen na het volt ooien van de inrichtings bewerkingen wordt niet ondersteund in de huidige release.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hoe kan ik de levering van wacht woorden voor nieuwe mede werkers beheren en een mechanisme veilig bieden om hun wacht woord opnieuw in te stellen?

Een van de laatste stappen bij het inrichten van nieuwe AD-accounts is de levering van het tijdelijke wacht woord dat is toegewezen aan het AD-account van de gebruiker. Veel ondernemingen gebruiken nog steeds de traditionele benadering van het leveren van het tijdelijke wacht woord aan de Manager van de gebruiker, die vervolgens het wacht woord doorneemt aan de nieuwe mede werker van de werk nemer. Dit proces heeft een inherente beveiligings fout en er is een optie beschikbaar om een betere benadering te implementeren met behulp van Azure AD-mogelijkheden.

Als onderdeel van het wervings proces voert HR-teams doorgaans een achtergrond controle uit en vet het mobiele nummer van de nieuwe huur. Met de werk dagen voor het inrichten van AD-gebruikers kunt u boven op dit feit bouwen en een selfservice voor wachtwoord herstel voor de gebruiker op dag 1 implementeren. Dit wordt bereikt door het kenmerk mobiele nummer van de nieuwe huur van workday door te geven aan AD en vervolgens van AD naar Azure AD met behulp van Azure AD Connect. Zodra het ' mobiele nummer ' aanwezig is in azure AD, kunt u de [self-service voor het opnieuw instellen van wacht woorden (SSPR)](../authentication/howto-sspr-authenticationdata.md) inschakelen voor het account van de gebruiker, zodat op dag 1 een nieuwe huur kan gebruikmaken van het geregistreerde en geverifieerde mobiele nummer voor authenticatie.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Worden de gebruikers profielen van de oplossing in de Azure AD-Cloud of in de laag van de inrichtings agent in de cache opgeslagen?

Nee, de oplossing houdt geen cache met gebruikers profielen bij. De Azure AD-inrichtings service fungeert gewoon als een gegevens processor, leest gegevens van workday en schrijft deze naar het doel Active Directory of Azure AD. Zie de sectie [persoonlijke gegevens beheren](#managing-personal-data) voor meer informatie over privacy van gebruikers en het bewaren van gegevens.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Ondersteunt de oplossing de toewijzing van on-premises AD-groepen aan de gebruiker?

Deze functionaliteit wordt momenteel niet ondersteund. De aanbevolen tijdelijke oplossing is om een Power shell-script te implementeren waarmee het Microsoft Graph API-eind punt wordt opgevraagd voor [audit logboek gegevens](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) en voor het activeren van scenario's zoals groeps toewijzing. Dit Power shell-script kan worden gekoppeld aan een taak planner en wordt geïmplementeerd op hetzelfde vak als de inrichtings agent wordt uitgevoerd.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Welke workday-Api's gebruiken de oplossing voor het opvragen en bijwerken van werk profielen voor workday?

De oplossing maakt momenteel gebruik van de volgende workday-Api's:

* De URL-indeling van de **workday Web Services API** die in de sectie **beheerders referenties** wordt gebruikt, bepaalt de API-versie die wordt gebruikt voor Get_Workers
  * Als de URL-indeling is: https:// \# \# \# \# \. workday \. com/CCX/service/tenantnaam, wordt API v 21.1 gebruikt. 
  * Als de URL-indeling is: https:// \# \# \# \# \. workday \. com/CCX/service/Tenant/Human \_ resources, wordt API v 21.1 gebruikt 
  * Als de URL-indeling is: https:// \# \# \# \# \. workday \. com/CCX/service/tenantnaam/Human \_ resources/v \# \# \. \# , wordt de opgegeven API-versie gebruikt. (Voor beeld: als v 34.0 is opgegeven, wordt het gebruikt.)  
   
* De functie voor het terugschrijven van dagen per dag maakt gebruik van Change_Work_Contact_Information (v 30.0) 
* De functie voor het terugschrijven van workday-gebruikers namen maakt gebruik van Update_Workday_Account (v 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kan ik mijn workday HCM-Tenant configureren met twee Azure AD-tenants?

Ja, deze configuratie wordt ondersteund. Dit zijn de stappen op hoog niveau voor het configureren van dit scenario:

* Implementeer de inrichtings agent #1 en Registreer deze met de Azure AD-Tenant #1.
* Implementeer de inrichtings Agent #2 en Registreer deze met de Azure AD-Tenant #2.
* Configureer elke agent met de domein (en) op basis van de onderliggende domeinen die elke inrichtings agent beheert. Eén agent kan meerdere domeinen verwerken.
* Stel in Azure Portal de werk dagen in voor het inrichten van een AD-App voor gebruikers in elke Tenant en configureer deze met de respectieve domeinen.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Waarom wordt het gebruik van de app voor het inrichten van een werkdag naar Azure AD niet ondersteund als er Azure AD Connect zijn geïmplementeerd?

Als Azure AD wordt gebruikt in de hybride modus (waarbij het een combi natie van Cloud-en on-premises gebruikers bevat), is het belang rijk dat u een duidelijke definitie van ' bron van de autoriteit ' hebt. Voor hybride scenario's is het implementeren van Azure AD Connect vereist. Wanneer Azure AD Connect is geïmplementeerd, is on-premises AD de bron van de autoriteit. Introductie van de werkdag tot Azure AD connector in de mix kan leiden tot een situatie waarin de waarden voor workday-kenmerken mogelijk de waarden overschrijven die zijn ingesteld door Azure AD Connect. Het gebruik van de inrichtings app voor werk dagen naar Azure AD wordt daarom niet ondersteund wanneer Azure AD Connect is ingeschakeld. In dergelijke situaties raden we u aan om de app voor het inrichten van ' workday to AD User ' te gebruiken voor het ophalen van gebruikers in on-premises AD en deze vervolgens te synchroniseren met Azure AD met behulp van Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hoe kan ik verbeteringen Voorst Ellen of nieuwe functies met betrekking tot workday en Azure AD-integratie aanvragen?

Uw feedback is zeer belang rijk omdat het ons helpt om de richting in te stellen voor toekomstige releases en uitbrei dingen. We geven alle feedback en moedigen u aan om uw suggestie of verbetering voor te leggen in het [Feedback forum van Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Voor specifieke feedback met betrekking tot de workday-integratie selecteert u de categorie *SaaS-toepassingen* en zoekt u op de *dag* van de tref woorden de bestaande feedback die betrekking heeft op de werkdag.

> [!div class="mx-imgBorder"]
> ![UserVoice SaaS-apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice-workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Wanneer u een nieuw idee bekijkt, moet u controleren of iemand anders een vergelijk bare functie heeft voorgesteld. In dat geval kunt u de aanvraag voor het maken van de functie of de uitbrei ding van stemmen. U kunt ook een opmerking over uw specifieke gebruiks voorbeeld plaatsen om uw ondersteuning voor het idee weer te geven en te laten zien hoe de functie ook waardevol voor u is.

### <a name="provisioning-agent-questions"></a>Vragen over het inrichten van agents

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wat is de GA-versie van de inrichtings agent?

Raadpleeg [Azure AD Connect inrichtings agent: versie geschiedenis](../app-provisioning/provisioning-agent-release-version-history.md) van de nieuwste Ga-versie van de inrichtings agent.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hoe kan ik weet ik welke versie van mijn inrichtings agent u hebt?

* Meld u aan bij de Windows-Server waarop de inrichtings agent is geïnstalleerd.
* Ga naar **configuratie scherm**  ->  **verwijderen of wijzigen van een programma** menu
* Zoek naar de versie die overeenkomt met de vermelding **Microsoft Azure AD inrichtings agent verbinden**

  >[!div class="mx-imgBorder"]
  >![Azure-portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Pusht micro soft automatisch de updates voor de inrichtings agent?

Ja, de inrichtings agent wordt automatisch door micro soft bijgewerkt als de Windows-service **Microsoft Azure AD connect agent Updater** actief is.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Kan ik de inrichtings agent installeren op dezelfde server met Azure AD Connect?

Ja, u kunt de inrichtings agent installeren op dezelfde server waarop Azure AD Connect wordt uitgevoerd.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Op het moment van de configuratie van de inrichtings agent wordt gevraagd naar de referenties van de Azure AD-beheerder. Slaat de agent de referenties lokaal op de server op?

Tijdens de configuratie vraagt de inrichtings agent alleen om de referenties van de Azure AD-beheerder om verbinding te maken met uw Azure AD-Tenant. De referenties worden niet lokaal opgeslagen op de server. Het behoudt echter de referenties die worden gebruikt om verbinding te maken met het *on-premises Active Directory domein* in een lokale Windows-wachtwoord kluis.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hoe kan ik de inrichtings agent configureren voor het gebruik van een proxy server voor uitgaande HTTP-communicatie?

De inrichtings agent ondersteunt het gebruik van een uitgaande proxy. U kunt deze configureren door het configuratie bestand van de agent te bewerken **C:\Program Files\Microsoft Azure AD Connect inrichting Agent\AADConnectProvisioningAgent.exe.config**. Voeg de volgende regels toe aan het einde van het bestand net vóór de afsluit `</configuration>` code.
Vervang de variabelen [proxy-server] en [proxy-port] door de naam- en poortwaarden van de proxyserver.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hoe kan ik ervoor te zorgen dat de inrichtings agent kan communiceren met de Azure AD-Tenant en dat er geen firewalls zijn die vereist zijn voor de agent?

U kunt ook controleren of alle [vereiste poorten](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports) zijn geopend.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan één inrichtings agent worden geconfigureerd om meerdere AD-domeinen in te richten?

Ja, een inrichtings agent kan worden geconfigureerd voor het verwerken van meerdere AD-domeinen, zolang de agent een regel voor de detectie van de betreffende domein controllers heeft. Micro soft raadt u aan om een groep van 3-inrichtings agenten in te stellen voor dezelfde set AD-domeinen om hoge Beschik baarheid te garanderen en failover-ondersteuning te bieden.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hoe kan ik de registratie van het domein dat is gekoppeld aan mijn inrichtings agent?

* Haal de *Tenant-id* van uw Azure AD-Tenant op uit het Azure Portal.
* Meld u aan bij de Windows-Server waarop de inrichtings agent wordt uitgevoerd.
* Open Power shell als Windows-beheerder.
* Ga naar de map met de registratie scripts en voer de volgende opdrachten uit om de \[ Tenant-ID- \] para meter te vervangen door de waarde van uw Tenant-id.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* In de lijst met agents die worden weer gegeven, kopieert u de waarde van het `id` veld van die resource waarvan *ResourceName* gelijk is aan uw AD-domein naam.
* Plak de ID-waarde in deze opdracht en voer de opdracht uit in Power shell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Voer de wizard agent configureren opnieuw uit.
* Alle andere agents die eerder aan dit domein zijn toegewezen, moeten opnieuw worden geconfigureerd.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>De inrichtings agent Hoe kan ik verwijderen?

* Meld u aan bij de Windows-Server waarop de inrichtings agent is geïnstalleerd.
* Ga naar **configuratie scherm**  ->  **verwijderen of wijzigen van een programma** menu
* Verwijder de volgende Program ma's:
  * Inrichtings agent Microsoft Azure AD verbinding maken
  * Updater Microsoft Azure AD-agent
  * Inrichtings agent pakket Microsoft Azure AD koppelen

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Vragen over het toewijzen en configureren van workday aan AD-kenmerk

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hoe kan ik back-up maken of exporteren van een werk kopie van het kenmerk toewijzing en schema voor het inrichten van workday?

U kunt Microsoft Graph API gebruiken voor het exporteren van de configuratie van de werk dagen voor het inrichten van gebruikers. Raadpleeg de stappen in de sectie de [configuratie van de werk inrichtings toewijzing van workday exporteren en importeren](#exporting-and-importing-your-configuration) voor meer informatie.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Ik heb aangepaste kenmerken in workday en Active Directory. Hoe kan ik de oplossing configureren voor gebruik met mijn aangepaste kenmerken?

De oplossing ondersteunt aangepaste werk dagen en kenmerken van Active Directory. Als u uw aangepaste kenmerken wilt toevoegen aan het toewijzings schema, opent u de Blade **kenmerk toewijzing** en schuift u omlaag om de sectie **Geavanceerde opties weer te geven**. 

![Kenmerk lijst bewerken](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Als u uw aangepaste workday-kenmerken wilt toevoegen, selecteert u de optie *kenmerk lijst bewerken voor workday* en voegt u uw aangepaste AD-kenmerken toe, selecteert u de optie *kenmerk lijst bewerken voor on-premises Active Directory*.

Zie ook:

* [De lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hoe kan ik de oplossing zodanig configureren dat alleen kenmerken in AD worden bijgewerkt op basis van wijzigingen in de werkdag en geen nieuwe AD-accounts maken?

Deze configuratie kan worden bereikt door de acties voor het **doel object** op de Blade **kenmerk toewijzingen** in te stellen, zoals hieronder wordt weer gegeven:

![Update-actie](./media/workday-inbound-tutorial/wd_target_update_only.png)

Schakel het selectie vakje bijwerken alleen in voor het uitvoeren van updates voor werk stromen van workday naar AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kan ik de foto van de gebruiker van werkdag tot Active Directory inrichten?

De oplossing biedt momenteel geen ondersteuning voor het instellen van binaire kenmerken, zoals *thumbnailPhoto* en *jpegPhoto* in Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hoe kan ik mobiele nummers van workday synchroniseren op basis van de toestemming van de gebruiker voor openbaar gebruik?

* Ga naar de Blade inrichten van de app voor het inrichten van workday.
* Klik op de kenmerk toewijzingen 
* Selecteer onder **toewijzingen** **werk dagen synchroniseren met on-premises Active Directory** (of **SYNCHRONISEER werk dagen in azure AD**).
* Schuif op de pagina kenmerk toewijzingen omlaag en schakel het selectie vakje ' Geavanceerde opties weer geven ' in.  Klik op **kenmerk lijst bewerken voor werkdag**
* Op de Blade die wordt geopend, zoekt u het mobiele kenmerk en klikt u op de rij zodat u de **API-expressie** ![ Mobile AVG kunt bewerken](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Vervang de **API-expressie** door de volgende nieuwe expressie, waarmee het mobiele werk nummer wordt opgehaald als de ' open bare gebruiks vlag ' is ingesteld op ' True ' in workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Sla de kenmerk lijst op.
* Sla de kenmerk toewijzing op.
* De huidige status wissen en de volledige synchronisatie opnieuw starten.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hoe kan ik indeling weer geven van namen in AD op basis van de kenmerken afdeling/land/plaats van gebruiker en regionale afwijkingen afhandelen?

Het is een algemene vereiste voor het configureren van het kenmerk *DisplayName* in AD, zodat het ook informatie biedt over de afdeling en het land/de regio van de gebruiker. Als John Smith werkt in de marketing afdeling in ons, wilt u mogelijk zijn *DisplayName* laten zien als *Smit, John (Marketing-VS)*.

Hier vindt u informatie over het afhandelen van zulke vereisten voor het samen stellen van *CN* of *DisplayName* om kenmerken zoals bedrijf, bedrijfs eenheid, plaats of land/regio op te geven.

* Elk workday-kenmerk wordt opgehaald met behulp van een onderliggende XPATH API-expressie, die kan worden geconfigureerd in **kenmerk toewijzing-> geavanceerde sectie-> kenmerk lijst bewerken voor werkdag**. Hier volgt de standaard XPATH API-expressie voor *PreferredFirstName*-, *PreferredLastName*-, *Company-* en *SupervisoryOrganization* -kenmerken.

     | Kenmerk workday | API XPATH-expressie |
     | ----------------- | -------------------- |
     | PreferredFirstName | Word: worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/text () |
     | PreferredLastName | Word: worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/text () |
     | Bedrijf | Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' bedrijf ']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' toezicht ']/WD: Organization_Name/text () |
  
   Bevestig met uw werkdag team dat de API-expressie hierboven geldig is voor de Tenant configuratie van uw werkdag. Indien nodig kunt u deze bewerken zoals beschreven in de sectie [de lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes).

* Evenzo worden de land-of regio gegevens die aanwezig zijn in workday opgehaald met behulp van het volgende XPATH: *WD: worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Er zijn vijf land-en regiospecifieke kenmerken die beschikbaar zijn in de sectie lijst met kenmerk dagen.

     | Kenmerk workday | API XPATH-expressie |
     | ----------------- | -------------------- |
     | CountryReference | Word: worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Alpha-3_Code ']/text () |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | Word: worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Numeric-3_Code ']/text () |
     | CountryReferenceTwoLetter | Word: worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = ' ISO_3166-1_Alpha-2_Code ']/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Bevestig met uw werkdag team dat de API-expressies hierboven geldig zijn voor de Tenant configuratie van uw werkdag. Indien nodig kunt u deze bewerken zoals beschreven in de sectie [de lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes).

* Als u de expressie juiste kenmerk toewijzing wilt maken, identificeert u welk workday-kenmerk ' gezaghebbend ' de voor naam, achternaam, land/regio en afdeling van de gebruiker vertegenwoordigt. Stel dat de kenmerken respectievelijk *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* en *SupervisoryOrganization* zijn. U kunt dit als volgt gebruiken om een expressie voor het kenmerk AD *DisplayName* te maken om een weergave naam op te halen *, zoals Smit, John (marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Als u de juiste expressie hebt, bewerkt u de tabel kenmerk toewijzingen en wijzigt u de kenmerk toewijzing *DisplayName* , zoals hieronder wordt weer gegeven: ![ DisplayName toewijzen](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Als u het bovenstaande voor beeld uitbreidt, kunt u plaatsnamen van de werkdag omzetten in steno waarden en deze vervolgens gebruiken om weergave namen te maken, zoals *Smit, John (Chi)* of *Jansen, Jane (NYC)*. dit resultaat kan worden bereikt met behulp van een switch-expressie met het workday-kenmerk *gemeente* als de determinant-variabele.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Zie ook:
  * [Syntaxis van de functie Switch](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Syntaxis van de functie samen voegen](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Syntaxis van de functie Append](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hoe kan ik SelectUniqueValue gebruiken om unieke waarden voor het kenmerk samAccountName te genereren?

Stel dat u unieke waarden wilt genereren voor het kenmerk *sAMAccountName* met een combi natie van de kenmerken *FirstName* en *LastName* van werkdag. Hieronder ziet u een expressie die u kunt starten met:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Hoe de bovenstaande expressie werkt: als de gebruiker John Smith is, probeert het eerst JSmith te genereren. als JSmith al bestaat, wordt er een JoSmith gegenereerd, als dat bestaat, wordt JohSmith gegenereerd. De expressie zorgt er ook voor dat de gegenereerde waarde voldoet aan de beperking van de lengte beperking en speciale tekens die aan *sAMAccountName*zijn gekoppeld.

Zie ook:

* [Syntaxis van de mid-functie](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Syntaxis van de functie Replace](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntaxis van de functie SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hoe kan ik tekens verwijderen en deze converteren naar gewone Engelse alfabetten?

Gebruik de functie [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) om speciale tekens in de voor naam en achternaam van de gebruiker te verwijderen en tegelijkertijd het e-mail adres of de CN-waarde voor de gebruiker samen te stellen.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

In deze sectie vindt u specifieke richt lijnen voor het oplossen van problemen met het inrichten van uw workday met behulp van de Azure AD-controle logboeken en Windows Server Logboeken-Logboeken. Het is gebaseerd op de algemene stappen voor probleem oplossing en de concepten die in de [zelf studie zijn vastgelegd: rapportage over automatische toewijzing van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)

In deze sectie worden de volgende aspecten van het oplossen van problemen behandeld:

* [Problemen met Windows Logboeken instellen voor agent probleem oplossing](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Azure Portal audit logboeken instellen voor het oplossen van services](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Informatie over Logboeken voor het maken van AD-gebruikers accounts](#understanding-logs-for-ad-user-account-create-operations)
* [Logboeken voor update bewerkingen voor managers](#understanding-logs-for-manager-update-operations)
* [Veelvoorkomende fouten oplossen](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Problemen met Windows Logboeken instellen voor agent probleem oplossing

* Meld u aan bij de Windows Server-computer waarop de inrichtings agent is geïmplementeerd
* Open **Windows Server logboeken** bureau blad-app.
* Selecteer **Windows-logboeken > toepassing**.
* Het **filter huidige logboek gebruiken...** optie om alle gebeurtenissen weer te geven die onder de bron-Aad zijn geregistreerd **. Connect. ProvisioningAgent** en sluit gebeurtenissen met gebeurtenis-id 5 uit door het filter '-5 ' op te geven, zoals hieronder wordt weer gegeven.

  ![Windows Logboeken](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klik op **OK** en sorteer de resultaten weergave op **datum en tijd** kolom.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Azure Portal audit logboeken instellen voor het oplossen van services

* Start de [Azure Portal](https://portal.azure.com)en navigeer naar de sectie **audit logs** van de toepassing voor het inrichten van workday.
* Gebruik de knop **kolommen** op de pagina controle Logboeken om alleen de volgende kolommen weer te geven in de weer gave (datum, activiteit, status, reden van de status). Deze configuratie zorgt ervoor dat u zich alleen richt op gegevens die relevant zijn voor het oplossen van problemen.

  ![Controle logboek kolommen](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Gebruik de query parameters **doel** en **datum bereik** om de weer gave te filteren. 
  * Stel de **doel** query parameter in op ' werk nemer-id ' of ' werk nemer-id ' van het werk object werkdag.
  * Stel het **datum bereik** in op een geschikte tijds periode waarover u wilt onderzoeken op fouten of problemen met de inrichting.

  ![Controle logboek filters](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Informatie over Logboeken voor het maken van AD-gebruikers accounts

Wanneer een nieuwe huur in workday wordt gedetecteerd (bijvoorbeeld met werk nemer-ID *21023*), probeert de Azure AD-inrichtings service een nieuw AD-gebruikers account te maken voor de werk nemer en worden er in het proces vier audit logboek records gemaakt, zoals hieronder wordt beschreven:

  [![Het audit logboek maken OPS](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Wanneer u op een van de records in het controle logboek klikt, wordt de pagina **Details van activiteit** geopend. Hier ziet u de pagina **activiteit Details** voor elk logboek record type.

* **Import** record voor workday: in deze logboek record worden de werknemers gegevens weer gegeven die zijn opgehaald uit workday. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen op te lossen bij het ophalen van gegevens uit workday. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Ad-import** record: in deze logboek record wordt informatie weer gegeven van het account dat is OPGEHAALD uit AD. Net als tijdens het maken van de eerste gebruiker is er geen AD-account, de reden van de *activiteit* geeft aan dat er geen account met de waarde van het overeenkomende id-kenmerk is gevonden in Active Directory. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen op te lossen bij het ophalen van gegevens uit workday. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Als u de logboek records van de inrichtings agent wilt vinden die overeenkomen met deze AD-import bewerking, opent u de Windows Logboeken-Logboeken en gebruikt u de **Zoek opdracht..** . menu optie voor het zoeken naar logboek vermeldingen met de waarde van het kenmerk ID/samenvoegings eigenschap (in dit geval *21023*).

  ![Find](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Zoek de vermelding met *gebeurtenis-id = 9*, waarmee u het LDAP-zoek filter dat door de agent wordt gebruikt om het ad-account op te halen. U kunt controleren of dit het juiste zoek filter is om unieke gebruikers vermeldingen op te halen.

  ![LDAP-zoek opdracht](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  De record die onmiddellijk met *gebeurtenis-id = 2* volgt, legt het resultaat van de zoek bewerking vast en als er resultaten worden geretourneerd.

  ![LDAP-resultaten](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Actie record voor synchronisatie regel** : in deze logboek record worden de resultaten van de kenmerk toewijzings regels en geconfigureerde bereik filters weer gegeven samen met de inrichtings actie die wordt ondernomen voor het verwerken van de gebeurtenis van de binnenkomende werkdag. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen met de synchronisatie actie op te lossen. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Als er problemen zijn met uw kenmerk toewijzings expressies of als de binnenkomende werk dagen problemen ondervinden (bijvoorbeeld een lege of null-waarde voor vereiste kenmerken), ziet u in deze fase een fout met de fout code om de details van de fout te geven.

* **Ad-export** record: deze logboek record geeft het resultaat van een bewerking voor het maken van een ad-account samen met de kenmerk waarden die in het proces zijn ingesteld. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen met de bewerking voor het maken van een account op te lossen. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld. In de sectie ' aanvullende details ' is de ' eventname ' ingesteld op ' EntryExportAdd ', de ' JoiningProperty ' is ingesteld op de waarde van het kenmerk matching ID, de ' source anchor ' is ingesteld op de WorkdayID (WID) die aan de record is gekoppeld en ' TargetAnchor ' is ingesteld op de waarde van het AD-kenmerk ObjectGuid van de nieuwe gebruiker. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Als u de logboek records van de inrichtings agent wilt vinden die overeenkomen met deze AD-export bewerking, opent u de Windows Logboeken-Logboeken en gebruikt u de **Zoek functie..** . menu optie voor het zoeken naar logboek vermeldingen met de waarde van het kenmerk ID/samenvoegings eigenschap (in dit geval *21023*).  

  Zoek naar een HTTP POST-record die overeenkomt met het tijds tempel van de export bewerking met *gebeurtenis-id = 2*. Deze record bevat de kenmerk waarden die door de inrichtings service zijn verzonden naar de inrichtings agent.

  [![SCIM toevoegen](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Direct na bovenstaande gebeurtenis moet er een andere gebeurtenis zijn die de reactie van de bewerking AD-account maken vastlegt. Deze gebeurtenis retourneert de nieuwe objectGuid die in AD is gemaakt en wordt ingesteld als het kenmerk TargetAnchor in de inrichtings service.

  [![SCIM toevoegen](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Logboeken voor update bewerkingen voor managers

Het kenmerk Manager is een verwijzings kenmerk in AD. De inrichtings service stelt het kenmerk Manager niet in als onderdeel van de bewerking voor het maken van een gebruiker. In plaats daarvan wordt het kenmerk Manager ingesteld als onderdeel van een *Update* -bewerking nadat het ad-account voor de gebruiker is gemaakt. Om het bovenstaande voor beeld uit te breiden, laten we zeggen dat een nieuwe huur met werk nemer-ID "21451" is geactiveerd in workday en dat de nieuwe manager van de huur (*21023*) al een ad-account heeft. In dit scenario worden in de controle logboeken voor gebruiker 21451 vijf vermeldingen weer gegeven.

  [![Update voor Manager](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

De eerste vier records zijn vergelijkbaar met die in de bewerking voor het maken van een gebruiker. De vijfde record is de export gekoppeld aan het kenmerk update van het beheer. In de logboek record wordt het resultaat van de update bewerking voor AD-account beheer weer gegeven, die wordt uitgevoerd met het kenmerk *objectGuid* van de Manager.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Veelvoorkomende fouten oplossen

In deze sectie worden veelvoorkomende fouten behandeld met het inrichten van werk dagen en het oplossen ervan. De fouten worden als volgt gegroepeerd:

* [Agent fouten inrichten](#provisioning-agent-errors)
* [Connectiviteitsfouten](#connectivity-errors)
* [Fouten bij het maken van een AD-gebruikers account](#ad-user-account-creation-errors)
* [Fouten bij het bijwerken van het AD-gebruikers account](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Agent fouten inrichten

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Fout bij het installeren van de inrichtings agent met het volgende fout bericht: *service ' Microsoft Azure AD Connect inrichtings agent ' (AADConnectProvisioningAgent) is niet gestart. Controleer of u voldoende rechten hebt om het systeem te starten.* | Deze fout wordt meestal weer gegeven als u probeert de inrichtings agent te installeren op een domein controller en groeps beleid voor komt dat de service wordt gestart.  Het wordt ook weer gegeven als er een eerdere versie van de agent wordt uitgevoerd en u deze nog niet hebt verwijderd voordat u een nieuwe installatie start.| Installeer de inrichtings agent op een niet-domein controller. Zorg ervoor dat vorige versies van de agent zijn verwijderd voordat u de nieuwe agent installeert.|
|2.| De Windows-service ' Microsoft Azure AD Connect inrichtings agent ' bevindt zich in de *Start* status en schakelt niet over naar de *actieve* status. | Als onderdeel van de installatie maakt de wizard agent een lokaal account (**NT service \\ AADConnectProvisioningAgent**) op de server. Dit is het aanmeldings account dat wordt gebruikt voor het starten van de service. Als een beveiligings beleid op uw Windows-Server verhindert dat lokale accounts de services uitvoeren, wordt deze fout weer gestuit. | Open de *console Services*. Klik met de rechter muisknop op de Windows-service ' Microsoft Azure AD Connect inrichtings agent ' en geef op het tabblad Logon het account van een domein beheerder op om de service uit te voeren. Start de service opnieuw. |
|3.| Bij het configureren van de inrichtings agent met uw AD-domein in de stap *Connect Active Directory*, neemt de wizard lange tijd in beslag bij het laden van het AD-schema en uiteindelijk een time-out. | Deze fout komt meestal voor als de wizard geen contact kan maken met de controllerserver van het AD-domein door firewallproblemen. | Wanneer u de referenties voor uw AD-domein opgeeft in het scherm *verbinding maken met* de wizard Active Directory, is de optie *domein controller selecteren prioriteit*. Gebruik deze optie om een domein controller te selecteren die zich op dezelfde site bevindt als de agent server en ervoor te zorgen dat er geen firewall regels zijn die de communicatie blok keren. |

#### <a name="connectivity-errors"></a>Connectiviteitsfouten

Als de inrichtings service geen verbinding kan maken met workday of Active Directory, kan dit ertoe leiden dat het inrichten een status in quarantaine heeft. Gebruik de onderstaande tabel om verbindings problemen op te lossen.

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Wanneer u op **verbinding testen**klikt, wordt het volgende fout bericht weer gegeven: *Er is een fout opgetreden bij het verbinden met Active Directory. Zorg ervoor dat de on-premises inrichtings agent wordt uitgevoerd en is geconfigureerd met het juiste Active Directory domein.* | Deze fout wordt meestal weer gegeven als de inrichtings agent niet actief is of als er een firewall is die de communicatie blokkeert tussen Azure AD en de inrichtings agent. Deze fout kan ook worden weer geven als het domein niet is geconfigureerd in de wizard voor de agent. | Open de *Services* -console op de Windows-Server om te controleren of de agent wordt uitgevoerd. Open de wizard inrichtings agent en controleer of het juiste domein is geregistreerd bij de agent.  |
|2.| De inrichtings taak gaat over het weekend (VR-SAT) in quarantaine status en er wordt een e-mail melding weer geven dat er een fout is opgetreden bij de synchronisatie. | Een veelvoorkomende oorzaak van deze fout is de geplande downtime voor Workday. Let op: Als u gebruikmaakt van een Workday-implementatietenant, wordt in het weekend downtime gepland voor de Workday-implementatietenants (meestal van vrijdagavond tot zaterdagochtend). Gedurende deze periode kan de Workday-app voor inrichting de status In quarantaine krijgen, omdat deze geen verbinding kan maken met Workday. De status wordt weer normaal zodra de Workday-implementatietenant weer online is. In zeldzame gevallen ziet u deze fout mogelijk ook als het wachtwoord van de integratiesysteemgebruiker is gewijzigd vanwege het vernieuwen van de tenant, of als het account is vergrendeld of de status Verlopen heeft. | Neem contact op met de Workday-beheerder of integratiepartner voor informatie over de geplande downtime van Workday om de waarschuwingen tijdens deze periode te negeren en de beschikbaarheid te bevestigen zodra het Workday-exemplaar weer online is.  |


#### <a name="ad-user-account-creation-errors"></a>Fouten bij het maken van een AD-gebruikers account

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Mislukte export bewerkingen in het audit logboek met de *volgende bericht fout: OperationsError-SvcErr: er is een bewerkings fout opgetreden. Er is geen bovenliggende verwijzing geconfigureerd voor de Directory service. De Directory service kan daarom geen verwijzingen uitgeven naar objecten buiten dit forest.* | Deze fout wordt meestal weer gegeven als de *Active Directory container* -OE niet juist is ingesteld of als er problemen zijn met de expressie toewijzing die voor *parentDistinguishedName*wordt gebruikt. | Schakel de para meter *Active Directory container* OE in voor type fouten. Als u *parentDistinguishedName* gebruikt in de kenmerktoewijzing, zorg er dan voor dat het altijd resulteert in een bekende container binnen het AD-domein. Controleer de gebeurtenis *exporteren* in de audit Logboeken om de gegenereerde waarde te bekijken. |
|2.| Mislukte export bewerkingen in het audit logboek met fout code: *SystemForCrossDomainIdentityManagementBadResponse* en bericht *fout: ConstraintViolation-AtrErr: een waarde in de aanvraag is ongeldig. Een waarde voor het kenmerk bevindt zich niet in het acceptabele bereik van waarden. Details van \nError: CONSTRAINT_ATT_TYPE-Company*. | Hoewel deze fout specifiek is voor het kenmerk *bedrijf* , wordt deze fout mogelijk ook weer geven voor andere kenmerken, zoals *CN* . Deze fout wordt weer gegeven als gevolg van een AD-afgedwongen schema beperking. Standaard hebben de kenmerken als *bedrijf* en *CN* in AD een maximum van 64 tekens. Als de waarde die afkomstig is van workday meer is dan 64 tekens, wordt dit fout bericht weer gegeven. | Controleer de gebeurtenis *exporteren* in de audit Logboeken om de waarde te zien voor het kenmerk dat in het fout bericht is gerapporteerd. Overweeg het afkappen van de waarde die afkomstig is van workday met de functie [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) of het wijzigen van toewijzingen in een AD-kenmerk dat geen vergelijk bare lengte beperkingen heeft.  |

#### <a name="ad-user-account-update-errors"></a>Fouten bij het bijwerken van het AD-gebruikers account

Tijdens het update proces van het AD-gebruikers account wordt door de inrichtings service informatie gelezen uit zowel workday als AD, worden de regels voor kenmerk toewijzing uitgevoerd en wordt bepaald of een wijziging moet worden doorgevoerd. Dienovereenkomstig wordt een update gebeurtenis geactiveerd. Als een van deze stappen een fout tegen komt, wordt deze in de audit logboeken vastgelegd. Gebruik de onderstaande tabel om veelvoorkomende Update fouten op te lossen.

|#|Foutscenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Actie fouten van de synchronisatie regel in het audit logboek met de bericht *gebeurtenisnaam = EntrySynchronizationError en error code = EndpointUnavailable*. | Deze fout wordt weer gegeven als de inrichtings service geen gebruikers profiel gegevens kan ophalen uit Active Directory vanwege een verwerkings fout die wordt aangetroffen door de on-premises inrichtings agent. | Controleer de inrichtings agent Logboeken Logboeken voor fout gebeurtenissen die duiden op problemen met de Lees bewerking (filter op gebeurtenis-ID #2). |
|2.| Het kenmerk manager in AD wordt niet bijgewerkt voor bepaalde gebruikers in AD. | De meest waarschijnlijke oorzaak van deze fout is als u bereik regels gebruikt en de Manager van de gebruiker geen deel uitmaakt van het bereik. U kunt dit probleem ook ondervinden als het overeenkomstige ID-kenmerk van de Manager (bijvoorbeeld EmployeeID) niet is gevonden in het doel-AD-domein of niet is ingesteld op de juiste waarde. | Controleer het bereik filter en voeg de Manager-gebruiker in het bereik toe. Controleer het profiel van de manager in AD om te controleren of er een waarde is voor het kenmerk matching ID. |

## <a name="managing-your-configuration"></a>Uw configuratie beheren

In deze sectie wordt beschreven hoe u uw op workday gestuurde gebruikers inrichtings configuratie verder kunt uitbreiden, aanpassen en beheren. De volgende onderwerpen komen aan bod:

* [De lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes)  
* [Uw configuratie exporteren en importeren](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>De lijst met gebruikers kenmerken van workday aanpassen

De workday-inrichtings-apps voor Active Directory en Azure AD bevatten zowel een standaard lijst met gebruikers kenmerken voor werk dagen waaruit u kunt kiezen. Deze lijsten zijn echter niet volledig. Workday ondersteunt een groot aantal honderden mogelijke gebruikers kenmerken, die standaard of uniek kunnen zijn voor uw workday-Tenant.

De Azure AD-inrichtings service ondersteunt de mogelijkheid om uw lijst-of workday-kenmerk aan te passen, zodat hierin alle kenmerken worden weer gegeven die beschikbaar zijn in de [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) -bewerking van de Human Resources API.

Als u deze wijziging wilt uitvoeren, moet u [werkdag Studio](https://community.workday.com/studio-download) gebruiken om de XPath-expressies te extra heren die de kenmerken vertegenwoordigen die u wilt gebruiken, en deze vervolgens toe te voegen aan uw inrichtings configuratie met behulp van de geavanceerde kenmerk editor in de Azure Portal.

**Een XPath-expressie ophalen voor een gebruikers kenmerk workday:**

1. Werk [dagen Studio](https://community.workday.com/studio-download)downloaden en installeren. U hebt een workday-community-account nodig om toegang te krijgen tot het installatie programma.

2. Down load de workday **Human_Resources** WSDL-bestand dat specifiek is voor de WWS API-versie die u wilt gebruiken vanuit de [map workday Web Services](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Start workday Studio.

4. Selecteer op de opdracht balk de optie **Workday > test webservice in tester** .

5. Selecteer **extern**en selecteer het Human_Resources WSDL-bestand dat u hebt gedownload in stap 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Stel het veld **locatie** in op `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` , maar vervang ' IMPL-CC ' door het daad werkelijke exemplaar type en ' TENANT ' door de naam van uw echte TENANT.

7. De **bewerking** instellen op **Get_Workers**

8.    Klik op de kleine koppeling **configureren** onder de deel Vensters aanvraag/antwoord om uw workday-referenties in te stellen. Controleer de **verificatie**en voer vervolgens de gebruikers naam en het wacht woord in voor uw workday-integratie systeem account. Zorg ervoor dat u de naam van de gebruiker wijzigt als \@ Tenant en de optie **WS-Security UsernameToken** geselecteerd laten.
   ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecteer **OK**.

10. Plak in het deel venster **aanvraag** in de onderstaande XML. Stel **Employee_ID** in op de werk nemer-id van een echte gebruiker in uw workday-Tenant. Stel **WD: Version** in op de versie van WWS die u wilt gebruiken. Selecteer een gebruiker met het kenmerk dat u wilt ophalen.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Klik op de **verzend aanvraag** (groene pijl) om de opdracht uit te voeren. Als dit lukt, wordt het antwoord weer gegeven in het deel venster **antwoorden** . Controleer het antwoord om er zeker van te zijn dat het gegevens bevat van de gebruikers-ID die u hebt ingevoerd, en geen fout.

12. Als dit het geval is, kopieert u de XML uit het **antwoord** venster en slaat u deze op als een XML-bestand.

13. Selecteer in de opdracht balk van workday Studio **file > bestand openen...** en open het XML-bestand dat u hebt opgeslagen. Met deze actie wordt het bestand in de workday Studio XML-editor geopend.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Navigeer in de bestands structuur naar **/env: envelop > env: Body > WD: Get_Workers_Response > WD: Response_Data > WD: worker** om de gegevens van uw gebruiker te vinden.

15. Zoek onder **WD: worker**het kenmerk dat u wilt toevoegen en selecteer het.

16. Kopieer de XPath-expressie voor het geselecteerde kenmerk uit het **veld documentpad** .

17. Verwijder de **/env: envelop/env: Body/WD: Get_Workers_Response/WD: Response_Data/** voor voegsel van de gekopieerde expressie.

18. Als het laatste item in de gekopieerde expressie een knoop punt is (bijvoorbeeld: "/WD: Birth_Date"), voegt u **/Text ()** toe aan het einde van de expressie. Dit is niet nodig als het laatste item een kenmerk is (bijvoorbeeld: " /@wd: type").

19. Het resultaat moet er ongeveer als volgt uitzien `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` . Deze waarde wordt gekopieerd naar de Azure Portal.

**Uw aangepaste workday-gebruikers kenmerk toevoegen aan uw inrichtings configuratie:**

1. Start de [Azure Portal](https://portal.azure.com)en navigeer naar het gedeelte inrichten van uw workday-inrichtings toepassing, zoals eerder in deze zelf studie is beschreven.

2. Stel de **inrichtings status** in op **uit**en selecteer **Opslaan**. Met deze stap zorgt u ervoor dat uw wijzigingen pas van kracht worden wanneer u klaar bent.

3. Selecteer onder **toewijzingen** **werk dagen synchroniseren met on-premises Active Directory** (of **SYNCHRONISEER werk dagen in azure AD**).

4. Ga naar de onderkant van het volgende scherm en selecteer **Geavanceerde opties weer geven**.

5. Selecteer **kenmerk lijst bewerken voor werkdag**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Schuif naar de onderkant van de lijst met kenmerken naar de locatie waar de invoer velden zich bevinden.

7. Voer bij **naam**een weergave naam in voor het kenmerk.

8. Bij **type**selecteert u type dat het beste overeenkomt met uw kenmerk (de**teken reeks** is het meest gebruikelijk).

9. Voor de **API-expressie**voert u de XPath-expressie in die u hebt gekopieerd uit workday Studio. Voorbeeld: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecteer **kenmerk toevoegen**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecteer hierboven **Opslaan** en vervolgens **Ja** in het dialoog venster. Sluit het scherm kenmerk toewijzing als het nog is geopend.

12. Klik op het tabblad belangrijkste **inrichting** op **werk dagen synchroniseren met on-premises Active Directory** (of **werk rollen synchroniseren met Azure AD**) opnieuw.

13. Selecteer **nieuwe toewijzing toevoegen**.

14. Het nieuwe kenmerk wordt nu weer gegeven in de lijst **bron kenmerk** .

15. Voeg desgewenst een toewijzing voor het nieuwe kenmerk toe.

16. Wanneer u klaar bent, moet u de **inrichtings status** opnieuw instellen op **aan** en opslaan.

### <a name="exporting-and-importing-your-configuration"></a>Uw configuratie exporteren en importeren

Raadpleeg het artikel [inrichtings configuratie exporteren en importeren](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Persoonlijke gegevens beheren

De workday-inrichtings oplossing voor Active Directory vereist dat een inrichtings agent wordt geïnstalleerd op een on-premises Windows-Server. deze agent maakt Logboeken in het Windows-gebeurtenis logboek die persoonlijke gegevens kunnen bevatten, afhankelijk van uw werkdag tot AD-kenmerk toewijzingen. Om te voldoen aan de verplichtingen van de privacy van de gebruiker, kunt u ervoor zorgen dat er niet meer dan 48 uur geen gegevens in de gebeurtenis logboeken worden bewaard door een geplande Windows-taak in te stellen om het gebeurtenis logboek te wissen.

De Azure AD-inrichtings service valt onder de **gegevens verwerkings** categorie AVG-classificatie. Als pijp lijn van de gegevens processor biedt de service gegevens verwerking aan belang rijke partners en eind gebruikers. De Azure AD-inrichtings service genereert geen gebruikers gegevens en heeft geen onafhankelijke controle over welke persoons gegevens worden verzameld en hoe deze worden gebruikt. Het ophalen van gegevens, aggregatie, analyse en rapportage in azure AD-inrichtings service zijn gebaseerd op bestaande bedrijfs gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Ten aanzien van gegevens retentie, genereert de Azure AD-inrichtings service geen rapporten, voert Analytics niet uit of bieden inzichten meer dan 30 dagen. Daarom slaat de Azure AD-inrichtings service geen gegevens die na 30 dagen worden opgeslagen, verwerkt of bewaard. Dit ontwerp is compatibel met de AVG-voor Schriften, micro soft-regelgeving voor privacybeleid en Azure AD-Bewaar beleid voor gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen werk dagen en Azure Active Directory](workday-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het gebruik van Microsoft Graph-Api's voor het beheren van inrichtings configuraties](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
