---
title: 'Zelfstudie: Werk dagen configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
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
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63176c325fd42c46e988ab3798f46089a43e70bf
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326790"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Zelfstudie: Workday configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren om worker-profielen van werk dagen te importeren in zowel Active Directory als Azure Active Directory, met een optionele write-back van e-mail adres en gebruikers naam naar werkdag.

## <a name="overview"></a>Overzicht

De [Azure Active Directory User Provisioning Service](../manage-apps/user-provisioning.md) kan worden geïntegreerd met de [HR Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) om gebruikers accounts in te richten. Azure AD gebruikt deze verbinding voor het inschakelen van de volgende werk stromen voor gebruikers inrichting:

* **Gebruikers inrichten voor het Active Directory** inrichten van geselecteerde sets gebruikers van workday in een of meer Active Directory domeinen.

* **Alleen Cloud gebruikers inrichten voor Azure Active Directory** scenario's waarbij on-premises Active Directory niet worden gebruikt, kunnen gebruikers rechtstreeks vanuit workday worden ingericht tot Azure Active Directory met behulp van de Azure AD User Provisioning-Service.

* **E-mail adres en gebruikers naam naar werkdag schrijven** : de Azure AD-gebruikers Provisioning Service kan de e-mail adressen en gebruikers naam van Azure AD terug naar workday schrijven.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Wat zijn de scenario's voor human resources?

De werk stroom die door de gebruiker wordt ondersteund door de Azure AD User Provisioning-Service, biedt de mogelijkheid om de volgende scenario's voor human resources en Identity Lifecycle Management te automatiseren:

* **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan workday, wordt automatisch een gebruikers account gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die door Azure AD worden ondersteund](../manage-apps/user-provisioning.md), met terugschrijven van het e-mail adres voor workday.

* **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in workday (zoals hun naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../manage-apps/user-provisioning.md).

* **Beëindiging van werk nemers** : wanneer een werk nemer wordt beëindigd in workday, wordt het gebruikers account automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../manage-apps/user-provisioning.md).

* Opnieuw **inhuren van werk nemers** : wanneer een werk nemer in workday opnieuw wordt ingehuurd, kan het oude account automatisch opnieuw worden geactiveerd of worden ingericht (afhankelijk van uw voor keur) tot Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS toepassingen die worden ondersteund door Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

De oplossing voor de gebruikers inrichting van deze werkdag is in het ideale geval geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op de cloud gebaseerde oplossing voor het inrichten van gebruikers met werk dagen

* Organisaties waarvoor directe gebruikers inrichting van workday naar Active Directory is vereist of Azure Active Directory

* Organisaties die gebruikers moeten inrichten met behulp van gegevens die zijn verkregen uit de HCM-module workday (Zie [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisaties die aan een of meer Active Directory-forests, domeinen en organisatie-eenheden moeten worden gesynchroniseerd, kunnen ze verplaatsen en verlaten, alleen op basis van gewijzigde gegevens die zijn gedetecteerd in de HCM-module van workday (Zie [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In deze sectie wordt de end-to-end-oplossings architectuur voor gebruikers ingericht voor algemene hybride omgevingen beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevens stroom: van werkdag naar on-premises Active Directory:** In deze stroom worden werk gebeurtenissen (zoals nieuwe aanmeldingen, overdrachten, afsluitingen) voor het eerst uitgevoerd in de Cloud workday HR-Tenant en vervolgens worden de gebeurtenis gegevens stromen naar on-premises Active Directory via Azure AD en de inrichtings agent. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in AD.
* **E-mail adres en wacht woord van de gebruikers naam write-on-premises Active Directory naar workday:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kan het kenmerk email en username worden teruggeschreven naar workday.

![Overzicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevens stroom van end-to-end-gebruikers

1. Het HR-team voert werknemers transacties (samenvoegers/verplaatsingen/Leavers of nieuwe huur/overdrachten/afsluitingen) uit in workday HCM
2. De Azure AD-inrichtings service voert geplande synchronisaties van identiteiten uit workday HR uit en identificeert wijzigingen die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtings service roept de on-premises AAD Connect-inrichtings agent aan met een aanvraag lading die AD-account maken/bijwerken/inschakelen/uitschakelen heeft.
4. De Azure AD Connect-inrichtings agent gebruikt een service account om AD-account gegevens toe te voegen of bij te werken.
5. De Azure AD Connect-AD Sync-Engine voert Delta synchronisatie uit voor het ophalen van updates in AD.
6. De Active Directory updates worden gesynchroniseerd met Azure Active Directory.
7. Als de workday-terugschrijf connector is geconfigureerd, worden de e-mail kenmerken en gebruikers namen naar workday geschreven op basis van het overeenkomende kenmerk dat wordt gebruikt.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Controleer voordat u begint met het integreren van de workday de onderstaande vereisten en lees de volgende richt lijnen om te voldoen aan uw huidige Active Directory architectuur en vereisten voor het inrichten van gebruikers met de door Azure Active Directory geleverde oplossingen. Er is ook een uitgebreid [implementatie plan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) met plannings voorstellen beschikbaar om u te helpen samen te werken met uw workday-integratie partner en HR-belanghebbenden.

In deze sectie worden de volgende aspecten van het plannen besproken:

* [Vereisten](#prerequisites)
* [De inrichtings connector-apps selecteren die u wilt implementeren](#selecting-provisioning-connector-apps-to-deploy)
* [Implementatie van Azure AD Connect-inrichtings agent plannen](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integreren met meerdere Active Directory domeinen](#integrating-with-multiple-active-directory-domains)
* [Dag plannen voor het Active Directory van toewijzings-en trans formaties van gebruikers kenmerken](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een geldige licentie voor Azure AD Premium P1 of hoger voor elke gebruiker die vanaf workday wordt gebrond en wordt ingericht in een on-premises Active Directory of Azure Active Directory.
* Globale beheerders toegang van Azure AD om de inrichtings agent te configureren
* Een implementatie-Tenant voor werk dagen voor testen en integratie doeleinden
* Beheerders machtigingen in workday voor het maken van een systeem integratie-gebruiker en het maken van wijzigingen in de test werknemers gegevens voor test doeleinden
* Voor het inrichten van gebruikers aan Active Directory is een server met Windows Server 2012 of hoger met .NET 4.7.1 + runtime vereist voor het hosten [van de on-premises Provisioning agent](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) voor het synchroniseren van gebruikers tussen Active Directory en Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>De inrichtings connector-apps selecteren die u wilt implementeren

Om het inrichten van werk stromen tussen workday en Active Directory mogelijk te maken, biedt Azure AD meerdere apps voor het inrichten van connectors die u kunt toevoegen vanuit de Azure AD-App-galerie:

![AAD-app-galerie](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday Active Directory het inrichten** van de gebruiker: deze app vereenvoudigt het inrichten van gebruikers accounts van workday tot één Active Directory domein. Als u meerdere domeinen hebt, kunt u één exemplaar van deze app toevoegen vanuit de Azure AD-App-galerie voor elk Active Directory domein dat u wilt inrichten.

* **Werkdag naar Azure AD-gebruikers inrichten** : Hoewel verbinding maken met Aad is het hulp programma dat moet worden gebruikt voor het synchroniseren van Active Directory gebruikers naar Azure Active Directory, kan deze app worden gebruikt om het inrichten van alleen Cloud gebruikers van workday naar één Azure te vergemakkelijken Active Directory Tenant.

* **Write** -back werkdag: deze app vereenvoudigt het terugschrijven van e-mail adressen van gebruikers van Azure Active Directory naar workday.

> [!TIP]
> De normale werkdag-app wordt gebruikt voor het instellen van eenmalige aanmelding tussen werk dagen en Azure Active Directory.

In het onderstaande diagram van de beslissings stroom kunt u zien welke apps voor workday-inrichting relevant zijn voor uw scenario.
    ![](./media/workday-inbound-tutorial/wday_app_flowchart.png "Stroom diagram beslissings") besluit

Gebruik de inhouds opgave om naar het betreffende gedeelte van deze zelf studie te gaan.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Implementatie van Azure AD Connect-inrichtings agent plannen

> [!NOTE]
> Deze sectie is alleen relevant als u van plan bent om de werkdag te implementeren naar Active Directory gebruikers inrichting-app. U kunt dit overs Laan als u de workday-write-of workday implementeert in de Azure AD-App voor gebruikers inrichting.

Voor de oplossing voor het inrichten van een AD-gebruiker moet een of meer inrichtings agenten worden geïmplementeerd op servers met Windows 2012 R2 of hoger met mini maal 4 GB RAM en .NET 4.7.1 + runtime. U moet rekening houden met de volgende overwegingen voordat u de inrichtings Agent installeert:

* Zorg ervoor dat de hostserver die de inrichtings agent uitvoert netwerk toegang heeft tot het doel-AD-domein
* De wizard voor het inrichten van de agent configuratie registreert de agent bij uw Azure AD-Tenant en het registratie proces vereist toegang tot *. msappproxy.net via de SSL-poort 443. Zorg ervoor dat er uitgaande firewall regels aanwezig zijn die deze communicatie mogelijk maken. De agent ondersteunt de configuratie van de [uitgaande HTTPS-proxy](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* De inrichtings agent gebruikt een service account om te communiceren met de on-premises AD-domein (en). Voordat de agent werd geïnstalleerd, is het raadzaam om een service account met beheerders machtigingen voor het domein te maken en een wacht woord dat niet verloopt.  
* Tijdens de configuratie van de inrichtings agent kunt u domein controllers selecteren die de inrichting van aanvragen moeten verwerken. Als u meerdere geografisch gedistribueerde domein controllers hebt, installeert u de inrichtings agent op dezelfde site als uw voorkeurs domein controller (s) om de betrouw baarheid en prestaties van de end-to-end oplossing te verbeteren
* Voor maximale Beschik baarheid kunt u meer dan één inrichtings agent implementeren en deze registreren voor het afhandelen van dezelfde set on-premises AD-domeinen.

> [!IMPORTANT]
> In productie omgevingen raadt micro soft aan dat u Mini maal drie ingerichte agents hebt geconfigureerd met uw Azure AD-Tenant voor hoge Beschik baarheid.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integreren met meerdere Active Directory domeinen

> [!NOTE]
> Deze sectie is alleen relevant als u van plan bent om de werkdag te implementeren naar Active Directory gebruikers inrichting-app. U kunt dit overs Laan als u de workday-write-of workday implementeert in de Azure AD-App voor gebruikers inrichting.

Afhankelijk van uw Active Directory topologie moet u het aantal apps voor gebruikers toewijzings connector en het aantal inrichtings agenten bepalen dat moet worden geconfigureerd. Hieronder vindt u enkele van de algemene implementatie patronen waarnaar u kunt verwijzen tijdens het plannen van uw implementatie.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>#1 implementatie scenario: Tenant voor één werkdag-> één AD-domein

In dit scenario hebt u één workday-Tenant en wilt u gebruikers inrichten voor één doel-AD-domein. Hier volgt de aanbevolen productie configuratie voor deze implementatie.

|   |   |
| - | - |
| Nee. van inrichtings agenten om on-premises te implementeren | 3 (voor hoge Beschik baarheid en failover) |
| Nee. van workday tot AD-gebruikers die apps moeten inrichten in Azure Portal | 1 |

  ![Scenario 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>#2 implementatie scenario: Tenant voor één werkdag-> meerdere onderliggende AD-domeinen

In dit scenario moet u gebruikers van workday inrichten voor meerdere onderliggende AD-domeinen in een forest. Hier volgt de aanbevolen productie configuratie voor deze implementatie.

|   |   |
| - | - |
| Nee. van inrichtings agenten om on-premises te implementeren | 3 (voor hoge Beschik baarheid en failover) |
| Nee. van workday tot AD-gebruikers die apps moeten inrichten in Azure Portal | Eén app per onderliggend domein |

  ![Scenario 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>#3 implementatie scenario: Tenant voor één werkdag-> niet-aaneengesloten AD-forests

Dit scenario omvat het inrichten van gebruikers van workday naar domeinen in niet-aaneengesloten AD-forests. Hier volgt de aanbevolen productie configuratie voor deze implementatie.

|   |   |
| - | - |
| Nee. van inrichtings agenten om on-premises te implementeren | 3 per niet-aaneengesloten AD-forest |
| Nee. van workday tot AD-gebruikers die apps moeten inrichten in Azure Portal | Eén app per onderliggend domein |

  ![Scenario 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Dag plannen voor het Active Directory van toewijzings-en trans formaties van gebruikers kenmerken

> [!NOTE]
> Deze sectie is alleen relevant als u van plan bent om de werkdag te implementeren naar Active Directory gebruikers inrichting-app. U kunt dit overs Laan als u de workday-write-of workday implementeert in de Azure AD-App voor gebruikers inrichting.

Houd rekening met de volgende vragen voordat u de gebruikers inrichting instelt voor een Active Directory domein. De antwoorden op deze vragen bepalen hoe uw bereik filters en kenmerk toewijzingen moeten worden ingesteld.

* **Welke gebruikers in workday moeten worden ingericht voor deze Active Directory-forest?**

  * *Voorbeeld: Gebruikers waar het workday-kenmerk ' bedrijf ' de waarde ' Contoso ' bevat, en het kenmerk ' Worker_Type ' bevat ' Regular '*

* **Hoe worden gebruikers doorgestuurd naar verschillende organisatie-eenheden?**

  * *Voorbeeld: Gebruikers worden doorgestuurd naar organisatie-eenheden die overeenkomen met een kantoor locatie, zoals gedefinieerd in de workday "gemeente" en "Country_Region_Reference"-kenmerken @ no__t-0

* **Hoe moeten de volgende kenmerken worden ingevuld in de Active Directory?**

  * Algemene naam (CN)
    * *Voorbeeld: De waarde van de workday User_ID gebruiken, zoals ingesteld door Human Resources @ no__t-0

  * Werknemers-ID (employeeId)
    * *Voorbeeld: Gebruik de workday Worker_ID waarde @ no__t-0

  * SAM-account naam (sAMAccountName)
    * *Voorbeeld: Gebruik de waarde voor workday User_ID, gefilterd via een Azure AD-inrichtings expressie om ongeldige tekens te verwijderen @ no__t-0

  * Principal-naam van gebruiker (userPrincipalName)
    * *Voorbeeld: Gebruik de waarde voor workday User_ID, met een Azure AD-inrichtings expressie voor het toevoegen van een domein naam @ no__t-0

* **Hoe moeten gebruikers tussen werk dagen en Active Directory worden afgestemd?**

  * *Voorbeeld: Gebruikers met een specifieke werkdag ' Worker_ID ' komen overeen met Active Directory gebruikers waarbij ' werk nemers ' dezelfde waarde heeft. Als de waarde Worker_ID niet wordt gevonden in Active Directory, maakt u een nieuwe gebruiker.*
  
* **Bevat het Active Directory forest al de gebruikers-Id's die nodig zijn om de overeenkomende logica te laten werken?**

  * *Voorbeeld: Als deze installatie een nieuwe workday-implementatie is, is het raadzaam om Active Directory vooraf in te vullen met de juiste waarden voor workday Worker_ID (of de unieke ID-waarde van Choice), zodat de overeenkomende logica zo eenvoudig mogelijk blijft.*

Het instellen en configureren van deze speciale Provisioning connector-apps is het onderwerp van de resterende secties van deze zelf studie. Welke apps u wilt configureren, is afhankelijk van welke systemen u moet inrichten en hoeveel Active Directory domeinen en Azure AD-tenants zich in uw omgeving bevinden.

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

1. Meld u aan bij uw workday-Tenant met een Administrator-account. Voer in de werk **dagen-app**gebruiker maken in het zoekvak in en klik vervolgens op **integratie systeem gebruiker maken**.

    ![Gebruiker aanmaken](./media/workday-inbound-tutorial/wd_isu_01.png "gebruiker maken")
2. Voltooi de **gebruikers taak integratie systeem maken** door een gebruikers naam en wacht woord op te geven voor een nieuwe gebruiker van het integratie systeem.  
  
* Laat de optie **Nieuw wacht woord vereisen bij volgende aanmelding** uitgeschakeld, omdat deze gebruiker via een programma wordt aangemeld.
* Houd de **time-outwaarde** voor de sessie door de standaard waarde van 0, waardoor de sessie van de gebruiker niet tijdig kan verlopen.
* Selecteer de optie **geen UI-sessies toestaan** omdat deze een extra beveiligingslaag biedt waarmee wordt voor komen dat een gebruiker het wacht woord van het integratie systeem kan aanmelden bij workday.

    ![Integratie systeem gebruiker maken](./media/workday-inbound-tutorial/wd_isu_02.png "integratie systeem gebruiker maken")

### <a name="creating-an-integration-security-group"></a>Een integratie beveiligings groep maken

In deze stap maakt u een onbeperkte of beperkte beveiligings groep voor integratie systemen in workday en wijst u de gebruiker van het integratie systeem dat in de vorige stap is gemaakt, toe aan deze groep.

**Een beveiligings groep maken:**

1. Voer in het zoekvak beveiligings groep maken in en klik vervolgens op **beveiligings groep maken**.

    ![](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity groep") CreateSecurity-groep
2. Voltooi de taak **beveiligings groep maken** . 

   * Er zijn twee typen beveiligings groepen in workday:
     * **Onbeperkte** Alle leden van de beveiligings groep hebben toegang tot alle gegevens instanties die zijn beveiligd door de beveiligings groep.
     * **Beperkte** Alle leden van de beveiligings groep hebben contextuele toegang tot een subset van gegevens instanties (rijen) waartoe de beveiligings groep toegang heeft.
   * Neem contact op met uw werkdag integratie partner om het juiste type beveiligings groep voor de integratie te selecteren.
   * Zodra u het groeps type kent, selecteert u **integratie systeem beveiligings groep (onbeperkt)** of **integratie systeem beveiligings groep (beperkt)** van het type van de vervolg keuzelijst **met tenantve beveiligings groep** .

     ![](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity groep") CreateSecurity-groep

3. Nadat het maken van de beveiligings groep is voltooid, ziet u een pagina waar u leden kunt toewijzen aan de beveiligings groep. Voeg de nieuwe gebruiker van het integratie systeem die in de vorige stap is gemaakt, toe aan deze beveiligings groep. Als u een *beperkte* beveiligings groep gebruikt, moet u ook het juiste organisatie bereik selecteren.

    Beveiligings ![groep bewerken](./media/workday-inbound-tutorial/wd_isu_05.png "beveiligings groep bewerken")

### <a name="configuring-domain-security-policy-permissions"></a>Machtigingen voor domein beveiligings beleid configureren

In deze stap verleent u machtigingen voor domein beleid voor de gegevens van de werk nemer aan de beveiligings groep.

**Machtigingen voor domein beveiligings beleid configureren:**

1. Voer **domein beveiligings configuratie** in het zoekvak in en klik vervolgens op het **beveiligings configuratie rapport**van het domein koppelen.  

    ![Domein beveiligings beleidsregels](./media/workday-inbound-tutorial/wd_isu_06.png "domein beveiligings beleid")  
2. Zoek in het tekstvak **domein** naar de volgende domeinen en voeg deze toe aan het filter met één voor één.  
   * *Externe account inrichten*
   * @no__t 0Worker gegevens: Rapporten van open bare werk nemers @ no__t-0
   * @no__t 0Person gegevens: Contact gegevens voor werk @ no__t-0
   * @no__t 0Worker gegevens: Alle posities @ no__t-0
   * @no__t 0Worker gegevens: Huidige informatie over personeel @ no__t-0
   * @no__t 0Worker gegevens: Zakelijke titel voor het worker-profiel @ no__t-0
   * *Workday-accounts*
   
     ![Domein beveiligings beleidsregels](./media/workday-inbound-tutorial/wd_isu_07.png "domein beveiligings beleid")  

     ![Domein beveiligings beleidsregels](./media/workday-inbound-tutorial/wd_isu_08.png "domein beveiligings beleid") 

     Klik op **OK**.

3. In het rapport dat wordt weer gegeven, selecteert u het weglatings teken (...) dat naast het **inrichten van externe accounts** wordt weer gegeven en klikt u op de menu optie **domein-> machtigingen voor beveiligings beleid bewerken**

    ![Domein beveiligings beleidsregels](./media/workday-inbound-tutorial/wd_isu_09.png "domein beveiligings beleid")  

4. Blader op de pagina **machtigingen voor domein beveiligings beleid bewerken** omlaag naar de sectie **integratie machtigingen**. Klik op het plus teken (+) om de integratie systeem groep toe te voegen aan de lijst met beveiligings groepen met de machtigingen **Get** en **put** .

    ![](./media/workday-inbound-tutorial/wd_isu_10.png "Machtiging") bewerken bewerken  

5. Klik op het plus teken (+) om de integratie systeem groep toe te voegen aan de lijst met beveiligings groepen met de machtigingen **Get** en **put** .

    ![](./media/workday-inbound-tutorial/wd_isu_11.png "Machtiging") bewerken bewerken  

6. Herhaal stap 3-5 hierboven voor elk van deze resterende beveiligings beleidsregels:

   | Bewerking | Beveiligings beleid voor domein |
   | ---------- | ---------- |
   | Ophalen en plaatsen | Werknemers gegevens: Rapporten van open bare werk nemer |
   | Ophalen en plaatsen | Persoons gegevens: Contact gegevens voor werk |
   | Ophalen | Werknemers gegevens: Alle posities |
   | Ophalen | Werknemers gegevens: Huidige informatie over personeel |
   | Ophalen | Werknemers gegevens: Zakelijke titel op het werknemers profiel |
   | Ophalen en plaatsen | Workday-accounts |

### <a name="configuring-business-process-security-policy-permissions"></a>Machtigingen voor het beveiligings beleid voor het bedrijfs proces configureren

In deze stap geeft u de beleids machtigingen ' beveiliging van bedrijfs processen ' voor de werk gegevens aan de beveiligings groep. Deze stap is vereist voor het instellen van de app-connector voor het terugschrijven van werk dagen.

**Machtigingen voor het beveiligings beleid voor het bedrijfs proces configureren:**

1. Voer het **beleid voor bedrijfs processen** in het zoekvak in en klik vervolgens op de taak koppeling **bewerken beveiligings beleid voor bedrijfs processen** .  

    Beveiligings beleid voor ![bedrijfs processen voor](./media/workday-inbound-tutorial/wd_isu_12.png "bedrijfs processen")  

2. In het tekstvak **bedrijfsproces type** zoekt u naar *contact* en selecteert u **contact persoon wijzigen** zakelijk proces en klikt u op **OK**.

    Beveiligings beleid voor ![bedrijfs processen voor](./media/workday-inbound-tutorial/wd_isu_13.png "bedrijfs processen")  

3. Ga op de pagina **beveiligings beleid voor bedrijfs processen bewerken** naar de sectie **contact gegevens onderhouden (webservice)** .

    Beveiligings beleid voor ![bedrijfs processen voor](./media/workday-inbound-tutorial/wd_isu_14.png "bedrijfs processen")  

4. Selecteer de nieuwe beveiligings groep integratie systeem en voeg deze toe aan de lijst met beveiligings groepen die de webservices aanvragen kunnen initiëren. Klik op **gereed**. 

    Beveiligings beleid voor ![bedrijfs processen voor](./media/workday-inbound-tutorial/wd_isu_15.png "bedrijfs processen")  

### <a name="activating-security-policy-changes"></a>Wijzigingen in het beveiligings beleid activeren

**Wijzigingen in het beveiligings beleid activeren:**

1. Voer activate in het zoekvak in en klik op de koppeling **wijzigingen in behandeling van beveiligings beleid activeren**.

    ![Activeren activeren](./media/workday-inbound-tutorial/wd_isu_16.png "")

1. Start de taak wijzigingen in wachtend op beveiligings beleid activeren door een opmerking in te voeren voor controle doeleinden en klik vervolgens op **OK**.
1. Voltooi de taak op het volgende scherm door het selectie vakje **bevestigen**te controleren en klik vervolgens op **OK**.

    Beveiliging ![in behandeling activeren]is(./media/workday-inbound-tutorial/wd_isu_18.png "in afwachting van beveiliging")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Gebruikers inrichten van workday naar Active Directory configureren

In deze sectie vindt u de stappen voor het inrichten van gebruikers accounts van workday naar elk Active Directory domein binnen het bereik van uw integratie.

* [On-premises inrichtings agent (en) installeren en configureren](#part-1-install-and-configure-on-premises-provisioning-agents)
* [De inrichtings connector-app toevoegen en de verbinding met workday maken](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Kenmerk toewijzingen configureren](#part-3-configure-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Deel 1: On-premises inrichtings agent (en) installeren en configureren

Als u on-premises wilt inrichten Active Directory, moet een agent worden geïnstalleerd op een server met .NET 4.7.1 + Framework en netwerk toegang tot de gewenste Active Directory domein (en).

> [!TIP]
> U kunt de versie van .NET Framework op uw server controleren met behulp van de instructies die u [hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)kunt vinden.
> Als op de server geen .NET 4.7.1 of hoger is geïnstalleerd, kunt u deze [hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)downloaden.  

Nadat u .NET 4.7.1 + hebt geïmplementeerd, kunt u hier de **[on-premises inrichtings agent](https://go.microsoft.com/fwlink/?linkid=847801)** downloaden en de volgende stappen volgen om de configuratie van de agent te volt ooien.

1. Meld u aan bij de Windows-Server waarop u de nieuwe agent wilt installeren.

1. Start het installatie programma voor de inrichtings agent, ga akkoord met de voor waarden en klik op de knop **installeren** .

   ![](./media/workday-inbound-tutorial/pa_install_screen_1.png "Scherm") installatie scherm installeren
   
1. Nadat de installatie is voltooid, wordt de wizard gestart en wordt het scherm **verbinding maken met Azure AD** weer gegeven. Klik op de knop **verifiëren** om verbinding te maken met uw Azure AD-exemplaar.

   ![Verbinding maken met Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Connect Azure AD")
   
1. Verifieer uw Azure AD-exemplaar met behulp van globale beheerders referenties.

   Beheer ![auth beheerder](./media/workday-inbound-tutorial/pa_install_screen_3.png "auth")

   > [!NOTE]
   > De referenties van de Azure AD-beheerder worden alleen gebruikt om verbinding te maken met uw Azure AD-Tenant. De-agent slaat de referenties niet lokaal op de server op.

1. Nadat de verificatie met Azure AD is geslaagd, wordt het scherm **verbinding maken Active Directory** weer gegeven. In deze stap voert u de naam van uw AD-domein in en klikt u op de knop **map toevoegen** .

   ![Map add]Directory(./media/workday-inbound-tutorial/pa_install_screen_4.png "toevoegen")
  
1. U wordt nu gevraagd om de referenties in te voeren die vereist zijn om verbinding te maken met het AD-domein. Op hetzelfde scherm kunt u de **prioriteit domein controller selecteren** gebruiken om domein controllers op te geven die de agent moet gebruiken voor het verzenden van inrichtings aanvragen.

   ![Domein referenties](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Na het configureren van het domein, wordt in het installatie programma een lijst met geconfigureerde domeinen weer gegeven. Op dit scherm kunt u stap #5 herhalen en #6 om meer domeinen toe te voegen of klikt u op **volgende** om door te gaan naar de agent registratie.

   Geconfigureerde ![domeinen]die(./media/workday-inbound-tutorial/pa_install_screen_6.png "zijn geconfigureerd")

   > [!NOTE]
   > Als u meerdere AD-domeinen hebt (bijvoorbeeld na.contoso.com, emea.contoso.com), moet u elk domein afzonderlijk toevoegen aan de lijst.
   > Het is niet voldoende om het bovenliggende domein (bijvoorbeeld contoso.com) toe te voegen. U moet elk onderliggend domein bij de agent registreren.
   
1. Controleer de configuratie gegevens en klik op **bevestigen** om de agent te registreren.
  
   Scherm(./media/workday-inbound-tutorial/pa_install_screen_7.png "bevestigen") scherm ![bevestigen]
   
1. De configuratie wizard geeft de voortgang weer van de registratie van de agent.
  
   Registratie van ![agent registratie](./media/workday-inbound-tutorial/pa_install_screen_8.png "agent")
   
1. Zodra de registratie van de agent is geslaagd, kunt u op **Afsluiten** klikken om de wizard af te sluiten.
  
   ![](./media/workday-inbound-tutorial/pa_install_screen_9.png "Scherm") afsluiten sluiten
   
1. Controleer de installatie van de agent en zorg ervoor dat deze wordt uitgevoerd door de module ' Services ' te openen en te zoeken naar de service met de naam ' Microsoft Azure AD Connect inrichtings agent '.
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Deel 2: De inrichtings connector-app toevoegen en de verbinding met workday maken

**Werk dagen configureren voor Active Directory inrichting:**

1. Ga naar <https://portal.azure.com>

2. Selecteer in de linker navigatie balk de optie **Azure Active Directory**

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer de categorie **alle** .

5. Zoek **naar Active Directory werk dagen**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting** maken

7. De **inrichtings** **modus** wijzigen in **automatisch**

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het werk account van het workday-integratie systeem, waarbij de domein naam van de Tenant is toegevoegd. Het ziet er ongeveer als volgt uit: **gebruikers naam @ no__t-1tenant_name**

   * **Beheerders wachtwoord –** Voer het wacht woord in voor het workday-integratie systeem account

   * **Tenant-URL:** Voer de URL naar het workday Web Services-eind punt voor uw Tenant in. Deze waarde moet er als volgt uitzien: https://wd3-impl-services1.workday.com/ccx/service/contoso4, waarbij *contoso4* wordt vervangen door de juiste Tenant naam en *WD3-impl* vervangen door de juiste omgevings teken reeks.

   * **Active Directory-forest-** De naam van uw Active Directory domein, zoals dit is geregistreerd bij de agent. Gebruik de vervolg keuzelijst om het doel domein te selecteren dat u wilt inrichten. Deze waarde is doorgaans een teken reeks zoals: *contoso.com*

   * **Active Directory-container-** Voer de container-DN in waar de agent standaard gebruikers accounts moet maken.
        Voorbeeld: *OE = standaard gebruikers, OU = gebruikers, DC = contoso, DC = test*
        
     > [!NOTE]
     > Deze instelling is alleen beschikbaar voor het aanbrengen van gebruikers accounts als het kenmerk *parentDistinguishedName* niet is geconfigureerd in de kenmerk toewijzingen. Deze instelling wordt niet gebruikt voor zoek-of update bewerkingen voor gebruikers. De gehele onderliggende domein structuur valt binnen het bereik van de zoek bewerking.

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.

     > [!NOTE]
     > De Azure AD-inrichtings service verzendt een e-mail melding als de inrichtings taak een [quarantaine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) status heeft.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de workday-referenties en de AD-referenties die zijn geconfigureerd voor de installatie van de agent geldig zijn.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **toewijzingen** de standaard toewijzing voor **werk dagen op locatie Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>Deel 3: Kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van workday naar Active Directory.

1. Klik op het tabblad inrichting onder **toewijzingen**op **werk dagen synchroniseren met on-premises Active Directory**.

1. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in workday het bereik moeten hebben voor het INRICHTEN van AD door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in workday. Voorbeeld filters:

   * Voorbeeld: Bereik voor gebruikers met werk nemer-Id's tussen 1000000 en 2000000 (exclusief 2000000)

      * Geschreven WorkerID

      * Operator: Overeenkomende REGEX

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen werk nemers en niet-voorwaardelijke werk nemers

      * Geschreven EmployeeID

      * Operator: IS NIET NULL

   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerk toewijzingen en expressies testen en controleren om ervoor te zorgen dat het gewenste resultaat wordt weer geven. Micro soft raadt aan om de bereik filters onder het bereik van de **bron object** te gebruiken om uw toewijzingen te testen met een aantal test gebruikers van workday. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

   > [!CAUTION] 
   > Het standaard gedrag van de inrichtings engine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw werkdag tot AD-integratie. Als u dit standaard gedrag wilt overschrijven, raadpleegt u het artikel [verwijdering overs laan van gebruikers accounts die buiten het bereik](../manage-apps/skip-out-of-scope-deletions.md) vallen
  
1. In het veld **acties doel object** kunt u globaal filteren welke acties er worden uitgevoerd op Active Directory. **Create** en **Update** worden het meest gebruikt.

1. In de sectie **kenmerk toewijzingen** kunt u definiëren hoe afzonderlijke workday-kenmerken worden toegewezen aan Active Directory kenmerken.

1. Klik op een bestaande kenmerk toewijzing om het bij te werken of Klik onder aan het scherm op **nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een individuele kenmerk toewijzing ondersteunt de volgende eigenschappen:

      * **Toewijzings type**

         * **Direct** : schrijft de waarde van het kenmerk workday naar het kenmerk AD, zonder wijzigingen

         * **Constante** : een statische, constante teken reeks waarde schrijven naar het AD-kenmerk

         * **Expressie** : Hiermee kunt u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer workday-kenmerken. [Zie dit artikel over expressies voor meer informatie](../manage-apps/functions-for-customizing-application-data.md).

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

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Hieronder ziet u enkele voor beelden van kenmerk toewijzingen tussen werk dagen en Active Directory, met enkele algemene expressies

* De expressie die wordt toegewezen aan het kenmerk *parentDistinguishedName* wordt gebruikt om een gebruiker in te richten op verschillende organisatie-eenheden op basis van een of meer workday-bron kenmerken. In dit voor beeld worden gebruikers in verschillende organisatie-eenheden geplaatst op basis van de plaats waar ze zich bevinden.

* Het kenmerk *userPrincipalName* in Active Directory wordt gegenereerd met behulp van de [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) -functie voor het maken van een gegenereerde waarde in het doel-AD-domein en stelt deze alleen in als deze uniek is.  

* [Hier vindt u documentatie over het schrijven van expressies](../manage-apps/functions-for-customizing-application-data.md). In deze sectie vindt u voor beelden van het verwijderen van speciale tekens.

| KENMERK WORKDAY | ACTIVE DIRECTORY-KENMERK |  OVEREENKOMENDE ID? | MAKEN/BIJWERKEN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Geschreven bij alleen maken |
| **PreferredNameData**    |  algemene naam    |   |   Geschreven bij alleen maken |
| **SelectUniqueValue (toevoegen (' \@ ', samen voegen (".", \[FirstName @ no__t-3, \[LastName @ no__t-5), "contoso.com"), samen voegen ("\@", samen voegen (".", Mid (\[FirstName @ no__t-8, 1, 1), \[LastName @ no__t-10), "contoso.com"), lid t-11, samen voegen (".", Mid (2FirstName @ no__t-13, 1, 2), 4LastName @ no__t-15), "contoso.com")**   | userPrincipalName     |     | Geschreven bij alleen maken 
| **Vervang(Mid(Vervang(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Geschreven bij alleen maken |
| **Switch (\[Active @ no__t-2,, "0", "True", "1", "false")** |  accountDisabled      |     | \+ Update maken |
| **Voornaam**   | givenName       |     |    \+ Update maken |
| **LastName**   |   SN   |     |  \+ Update maken |
| **PreferredNameData**  |  displayName |     |   \+ Update maken |
| **Bedrijfs**         | Bedrijf   |     |  \+ Update maken |
| **SupervisoryOrganization**  | Afdeling  |     |  \+ Update maken |
| **ManagerReference**   | beheerder  |     |  \+ Update maken |
| **BusinessTitle**   |  title     |     |  \+ Update maken | 
| **AddressLineData**    |  streetAddress  |     |   \+ Update maken |
| **Gemeenschap**   |   l   |     | \+ Update maken |
| **CountryReferenceTwoLetter**      |   CO |     |   \+ Update maken |
| **CountryReferenceTwoLetter**    |  c  |     |         \+ Update maken |
| **CountryRegionReference** |  St     |     | \+ Update maken |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  \+ Update maken |
| **PostalCode**  |   Postcode  |     | \+ Update maken |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | \+ Update maken |
| **Fax**      | facsimileTelephoneNumber     |     |    \+ Update maken |
| **Mobile**  |    mobiele       |     |       \+ Update maken |
| **LocalReference** |  preferredLanguage  |     |  \+ Update maken |                                               
| **Switch (\[Municipality @ no__t-2, "OE = standaard gebruikers, OE = gebruikers, OE = standaard, OE = locaties, DC = contoso, DC = com", "Rotterdam", "OE = Standard gebruikers, OU = Users, OU = Rotterdam, OU = locations, DC = contoso, DC = com", "Austin", "OE = Standard users, OU = Users, OE = Austin, OE = locations, DC = contoso, DC = com "," Seattle "," OE = standaard gebruikers, OU = gebruikers, OE = Seattle, OU = locaties, DC = contoso, DC = com "," Londen "," OE = standaard gebruikers, OU = gebruikers, OU = contoso, DC = "**  | parentDistinguishedName     |     |  \+ Update maken |

Zodra de configuratie van de kenmerk toewijzing is voltooid, kunt u [de User Provisioning Service nu inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Gebruikers inrichten configureren voor Azure AD

In de volgende secties worden de stappen beschreven voor het configureren van gebruikers inrichting van workday naar Azure AD voor Cloud implementaties.

* [De Azure AD Provisioning connector-app toevoegen en de verbinding met workday maken](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday-en Azure AD-kenmerk toewijzingen configureren](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Volg de onderstaande procedure alleen als u Cloud gebruikers hebt die moeten worden ingericht voor Azure AD en niet op locatie Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: De Azure AD Provisioning connector-app toevoegen en de verbinding met workday maken

**Werk dagen configureren voor Azure Active Directory inrichting voor Cloud gebruikers:**

1. Ga naar <https://portal.azure.com>.

2. Selecteer in de linker navigatie balk de optie **Azure Active Directory**

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer vervolgens de categorie **alle** .

5. Zoek naar een **werkdag naar Azure AD inrichten**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting** maken

7. De **inrichtings** **modus** wijzigen in **automatisch**

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het werk account van het workday-integratie systeem, waarbij de domein naam van de Tenant is toegevoegd. Moet er ongeveer als volgt uitzien:username@contoso4

   * **Beheerders wachtwoord –** Voer het wacht woord in voor het workday-integratie systeem account

   * **Tenant-URL:** Voer de URL naar het workday Web Services-eind punt voor uw Tenant in. Deze waarde moet er als volgt uitzien: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, waarbij *contoso4* wordt vervangen door de juiste Tenant naam en *WD3-impl* vervangen door de juiste omgevings teken reeks. Als deze URL niet bekend is, moet u samen werken met uw werkdag integratie partner of ondersteunings medewerker om te bepalen welke URL moet worden gebruikt.

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.

   * Klik op de knop **verbinding testen** .

   * Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de workday-URL en referenties geldig zijn in werkdag.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Deel 2: Workday-en Azure AD-kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van workday naar Azure Active Directory voor Cloud gebruikers.

1. Klik op het tabblad inrichting onder **toewijzingen**op werk rollen **synchroniseren met Azure AD**.

2. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in workday het bereik moeten hebben voor het inrichten van Azure AD door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in workday. Voorbeeld filters:

   * Voorbeeld: Bereik voor gebruikers met werk nemer-Id's tussen 1000000 en 2000000

      * Geschreven WorkerID

      * Operator: Overeenkomende REGEX

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen voorwaardelijke werk nemers en niet-gewone mede werkers

      * Geschreven ContingentID

      * Operator: IS NIET NULL

3. In het veld **acties doel object** kunt u globaal filteren welke acties er worden uitgevoerd op Azure AD. **Create** en **Update** worden het meest gebruikt.

4. In de sectie **kenmerk toewijzingen** kunt u definiëren hoe afzonderlijke workday-kenmerken worden toegewezen aan Active Directory kenmerken.

5. Klik op een bestaande kenmerk toewijzing om het bij te werken of Klik onder aan het scherm op **nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een individuele kenmerk toewijzing ondersteunt de volgende eigenschappen:

   * **Toewijzings type**

      * **Direct** : schrijft de waarde van het kenmerk workday naar het kenmerk AD, zonder wijzigingen

      * **Constante** : een statische, constante teken reeks waarde schrijven naar het AD-kenmerk

      * **Expressie** : Hiermee kunt u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer workday-kenmerken. [Zie dit artikel over expressies voor meer informatie](../manage-apps/functions-for-customizing-application-data.md).

   * **Bron kenmerk** : het gebruikers kenmerk van workday. Als het kenmerk dat u zoekt niet aanwezig is, raadpleegt u [de lijst met gebruikers kenmerken voor workday aanpassen](#customizing-the-list-of-workday-user-attributes).

   * **Standaard waarde** : optioneel. Als het bron kenmerk een lege waarde heeft, wordt deze waarde in plaats daarvan door de toewijzing geschreven.
            De meest voorkomende configuratie is om dit leeg te laten.

   * **Doel kenmerk** : het gebruikers kenmerk in azure AD.

   * **Objecten matchen met dit kenmerk** : ongeacht of dit kenmerk moet worden gebruikt om gebruikers te identificeren tussen workday en Azure AD. Deze waarde wordt doorgaans ingesteld op het veld werk nemer-ID voor workday, die meestal wordt toegewezen aan het kenmerk Employee ID (nieuw) of een extensie kenmerk in azure AD.

   * **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

   * **Deze toewijzing Toep assen**

     * **Altijd** : deze toewijzing Toep assen op zowel het maken van gebruikers als bij het bijwerken van acties

     * **Alleen tijdens het maken** : pas deze toewijzing alleen toe bij het maken van gebruikers acties

6. Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.

Zodra de configuratie van de kenmerk toewijzing is voltooid, kunt u [de User Provisioning Service nu inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Azure AD-kenmerk terugschrijven naar workday configureren

Volg deze instructies voor het configureren van write-back van gebruikers-e-mail adressen en gebruikers naam van Azure Active Directory naar werkdag.

* [De back-upconnector-app toevoegen en de verbinding met workday maken](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Toewijzing van write-back-kenmerk configureren](#part-2-configure-writeback-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: De back-upconnector-app toevoegen en de verbinding met workday maken

**De back-upconnector voor workday configureren:**

1. Ga naar <https://portal.azure.com>

2. Selecteer in de linker navigatie balk de optie **Azure Active Directory**

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer vervolgens de categorie **alle** .

5. Zoek naar **werkdag terugschrijven**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting** maken

7. De **inrichtings** **modus** wijzigen in **automatisch**

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het werk account van het workday-integratie systeem, waarbij de domein naam van de Tenant is toegevoegd. Moet er ongeveer als volgt uitzien: *username @ no__t-1contoso4*

   * **Beheerders wachtwoord –** Voer het wacht woord in voor het workday-integratie systeem account

   * **Tenant-URL:** Voer de URL naar het workday Web Services-eind punt voor uw Tenant in. Deze waarde moet er als volgt uitzien: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, waarbij *contoso4* wordt vervangen door de juiste Tenant naam en *WD3-impl* vervangen door de juiste omgevings teken reeks (indien nodig).

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de workday-URL en referenties geldig zijn in werkdag.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Deel 2: Toewijzing van write-back-kenmerk configureren

In deze sectie configureert u hoe terugschrijf kenmerken stromen van Azure AD naar werkdag. Op dit moment ondersteunt de connector alleen het terugschrijven van e-mail adres en gebruikers naam naar workday.

1. Klik op het tabblad inrichting onder **toewijzingen**op **synchroniseren Azure Active Directory gebruikers naar werkdag**.

2. In het veld **bereik van bron object** kunt u desgewenst filteren welke gebruikers sets in azure Active Directory hun e-mail adressen terug naar workday moeten schrijven. Het standaard bereik is alle gebruikers in azure AD.

3. Werk in de sectie **kenmerk toewijzingen** de overeenkomende id bij om het kenmerk in azure Active Directory aan te geven, waarbij de werk nemer-id van de werkdag of de werknemers-id wordt opgeslagen. Een populaire overeenkomende methode is het synchroniseren van de werk nemer-ID van de werkdag of de werknemers-ID naar extensionAttribute1-15 in azure AD. vervolgens gebruikt u dit kenmerk in azure AD om gebruikers terug te vinden in workday.

4. Normaal gesp roken wijst u het kenmerk Azure AD *userPrincipalName* toe aan het kenmerk workday *GebruikersID* en wijst u het Azure AD *mail-* kenmerk toe aan het kenmerk *EmailAddress* . Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.

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

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

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
  * [Kan ik de inrichtings agent installeren op dezelfde server met AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Hoe kan ik de inrichtings agent configureren voor het gebruik van een proxy server voor uitgaande HTTP-communicatie?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hoe kan ik ervoor te zorgen dat de inrichtings agent kan communiceren met de Azure AD-Tenant en dat er geen firewalls zijn die vereist zijn voor de agent?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hoe kan ik de registratie van het domein dat is gekoppeld aan mijn inrichtings agent?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [De inrichtings agent Hoe kan ik verwijderen?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Vragen over het toewijzen en configureren van workday aan AD-kenmerk**
  * [Hoe kan ik back-up maken of exporteren van een werk kopie van het kenmerk toewijzing en schema voor het inrichten van workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [I hebben aangepaste kenmerken in workday en Active Directory. Hoe kan ik de oplossing configureren voor het werken met aangepaste kenmerken? ](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
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

Als onderdeel van het wervings proces voert HR-teams doorgaans een achtergrond controle uit en vet het mobiele nummer van de nieuwe huur. Met de werk dagen voor het inrichten van AD-gebruikers kunt u boven op dit feit bouwen en een selfservice voor wachtwoord herstel voor de gebruiker op dag 1 implementeren. Dit wordt bereikt door het kenmerk mobiele nummer van de nieuwe huur van workday door te geven aan AD en vervolgens van AD naar Azure AD met behulp van AAD Connect. Zodra het ' mobiele nummer ' aanwezig is in azure AD, kunt u de [self-service voor het opnieuw instellen van wacht woorden (SSPR)](../authentication/howto-sspr-authenticationdata.md) inschakelen voor het account van de gebruiker, zodat op dag 1 een nieuwe huur kan gebruikmaken van het geregistreerde en geverifieerde mobiele nummer voor authenticatie.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Worden de gebruikers profielen van de oplossing in de Azure AD-Cloud of in de laag van de inrichtings agent in de cache opgeslagen?

Nee, de oplossing houdt geen cache met gebruikers profielen bij. De Azure AD-inrichtings service fungeert gewoon als een gegevens processor, leest gegevens van workday en schrijft deze naar het doel Active Directory of Azure AD. Zie de sectie [persoonlijke gegevens beheren](#managing-personal-data) voor meer informatie over privacy van gebruikers en het bewaren van gegevens.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Ondersteunt de oplossing de toewijzing van on-premises AD-groepen aan de gebruiker?

Deze functionaliteit wordt momenteel niet ondersteund. De aanbevolen tijdelijke oplossing is om een Power shell-script te implementeren waarmee een query wordt uitgevoerd op het Azure AD Graph API-eind punt voor audit logboek gegevens en voor het activeren van scenario's zoals groeps toewijzing. Dit Power shell-script kan worden gekoppeld aan een taak planner en wordt geïmplementeerd op hetzelfde vak als de inrichtings agent wordt uitgevoerd.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Welke workday-Api's gebruiken de oplossing voor het opvragen en bijwerken van werk profielen voor workday?

De oplossing maakt momenteel gebruik van de volgende workday-Api's:

* Get_Workers (v 21.1) voor het ophalen van gegevens van werk nemers
* Maintain_Contact_Information (v 26.1) voor de functie voor het terugschrijven van E-mail voor werk
* Update_Workday_Account (v 31.2) voor de functie voor het terugschrijven van gebruikers naam

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

![UserVoice SaaS-apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Wanneer u een nieuw idee bekijkt, moet u controleren of iemand anders een vergelijk bare functie heeft voorgesteld. In dat geval kunt u de aanvraag voor het maken van de functie of de uitbrei ding van stemmen. U kunt ook een opmerking over uw specifieke gebruiks voorbeeld plaatsen om uw ondersteuning voor het idee weer te geven en te laten zien hoe de functie ook waardevol voor u is.

### <a name="provisioning-agent-questions"></a>Vragen over het inrichten van agents

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wat is de GA-versie van de inrichtings agent?

* De GA-versie van de inrichtings agent is 1.1.30 en hoger.
* Als uw agent versie lager is dan 1.1.30, gebruikt u de open bare preview-versie en wordt deze automatisch bijgewerkt naar de GA-versie als de server die als host fungeert voor de agent .NET 4.7.1 runtime heeft.
  * U kunt [controleren of de .net-versie](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) is geïnstalleerd op uw server. Als .NET 4.7.1 niet op de server wordt uitgevoerd, kunt u [.net 4.7.1 downloaden en installeren](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). De inrichtings agent wordt automatisch bijgewerkt naar de GA-versie nadat u .NET 4.7.1 hebt geïnstalleerd.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hoe kan ik weet ik welke versie van mijn inrichtings agent u hebt?

* Meld u aan bij de Windows-Server waarop de inrichtings agent is geïnstalleerd.
* Ga naar **configuratie scherm** -> **een programma menu verwijderen of wijzigen**
* Zoek naar de versie die overeenkomt met de vermelding **Microsoft Azure AD inrichtings agent verbinden**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Pusht micro soft automatisch de updates voor de inrichtings agent?

Ja, de inrichtings agent wordt automatisch bijgewerkt. U kunt automatische updates uitschakelen door de Windows-service **Microsoft Azure AD connect agent Updater**te stoppen.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Kan ik de inrichtings agent installeren op dezelfde server met AAD Connect?

Ja, u kunt de inrichtings agent installeren op dezelfde server waarop AAD Connect wordt uitgevoerd.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Op het moment van de configuratie van de inrichtings agent wordt gevraagd naar de referenties van de Azure AD-beheerder. Slaat de agent de referenties lokaal op de server op?

Tijdens de configuratie vraagt de inrichtings agent alleen om de referenties van de Azure AD-beheerder om verbinding te maken met uw Azure AD-Tenant. De referenties worden niet lokaal opgeslagen op de server. Het behoudt echter de referenties die worden gebruikt om verbinding te maken met het *on-premises Active Directory domein* in een lokale Windows-wachtwoord kluis.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hoe kan ik de inrichtings agent configureren voor het gebruik van een proxy server voor uitgaande HTTP-communicatie?

De inrichtings agent ondersteunt het gebruik van een uitgaande proxy. U kunt deze configureren door het configuratie bestand van de agent te bewerken **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Voeg de volgende regels toe aan het einde van het bestand net vóór de afsluit `</configuration>`-tag.
Vervang de variabelen [proxy-server] en [proxy-port] door de naam en poort waarden van de proxy server.

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

U kunt ook controleren of alle vereiste poorten zijn geopend door het [test hulpprogramma voor connector poorten](https://aadap-portcheck.connectorporttest.msappproxy.net/) te openen vanaf uw on-premises netwerk. Meer een groen vinkje betekent meer flexibiliteit.

Als u wilt controleren of dat het hulpprogramma biedt u de juiste resultaten, moet u:

* Open het hulp programma op een browser van de server waarop u de inrichtings agent hebt geïnstalleerd.
* Zorg ervoor dat alle proxy's of firewalls die van toepassing zijn op uw inrichtings agent ook worden toegepast op deze pagina. U kunt dit doen in Internet Explorer door te gaan naar **instellingen-> Internet opties-> verbindingen-> LAN-instellingen**. Op deze pagina ziet u het veld "een proxy server gebruiken voor uw LAN". Schakel dit selectie vakje in en plaats het proxy adres in het veld adres.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan één inrichtings agent worden geconfigureerd om meerdere AD-domeinen in te richten?

Ja, een inrichtings agent kan worden geconfigureerd voor het verwerken van meerdere AD-domeinen, zolang de agent een regel voor de detectie van de betreffende domein controllers heeft. Micro soft raadt u aan om een groep van 3-inrichtings agenten in te stellen voor dezelfde set AD-domeinen om hoge Beschik baarheid te garanderen en failover-ondersteuning te bieden.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hoe kan ik de registratie van het domein dat is gekoppeld aan mijn inrichtings agent?

* Haal de *Tenant-id* van uw Azure AD-Tenant op uit het Azure Portal.
* Meld u aan bij de Windows-Server waarop de inrichtings agent wordt uitgevoerd.
* Open Power shell als Windows-beheerder.
* Ga naar de map met de registratie scripts en voer de volgende opdrachten uit om \[de Tenant\] -id-para meter te vervangen door de waarde van uw Tenant-id.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* In de lijst met agents die worden weer gegeven, kopieert u de waarde van het veld ' id ' van de resource waarvan *ResourceName* gelijk is aan uw AD-domein naam.
* Plak de ID-waarde in deze opdracht en voer de opdracht uit in Power shell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Voer de wizard agent configureren opnieuw uit.
* Alle andere agents die eerder aan dit domein zijn toegewezen, moeten opnieuw worden geconfigureerd.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>De inrichtings agent Hoe kan ik verwijderen?

* Meld u aan bij de Windows-Server waarop de inrichtings agent is geïnstalleerd.
* Ga naar **configuratie scherm** -> **een programma menu verwijderen of wijzigen**
* Verwijder de volgende Program ma's:
  * Inrichtings agent Microsoft Azure AD verbinding maken
  * Updater Microsoft Azure AD-agent
  * Inrichtings agent pakket Microsoft Azure AD koppelen

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Vragen over het toewijzen en configureren van workday aan AD-kenmerk

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hoe kan ik back-up maken of exporteren van een werk kopie van het kenmerk toewijzing en schema voor het inrichten van workday?

U kunt Microsoft Graph API gebruiken voor het exporteren van de configuratie van de werk dagen voor het inrichten van gebruikers. Raadpleeg de stappen in de sectie de [configuratie van de werk inrichtings toewijzing van workday exporteren en importeren](#exporting-and-importing-your-configuration) voor meer informatie.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Ik heb aangepaste kenmerken in workday en Active Directory. Hoe kan ik de oplossing configureren voor gebruik met mijn aangepaste kenmerken?

De oplossing ondersteunt aangepaste werk dagen en kenmerken van Active Directory. Als u uw aangepaste kenmerken wilt toevoegen aan het toewijzings schema, opent u de Blade **kenmerk toewijzing** en schuift u omlaag om de sectie **Geavanceerde opties weer te geven**. 

![Kenmerkenlijst bewerken](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

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
* Op de Blade die wordt geopend, zoekt u het mobiele kenmerk en klikt u op de rij zodat u de **API-expressie** kunt bewerken ![Mobile AVG @ no__t-2

* Vervang de **API-expressie** door de volgende nieuwe expressie, waarmee het mobiele werk nummer wordt opgehaald als de ' open bare gebruiks vlag ' is ingesteld op ' True ' in workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Sla de kenmerk lijst op.
* Sla de kenmerk toewijzing op.
* De huidige status wissen en de volledige synchronisatie opnieuw starten.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hoe kan ik indeling weer geven van namen in AD op basis van de kenmerken afdeling/land/plaats van gebruiker en regionale afwijkingen afhandelen?

Het is een algemene vereiste voor het configureren van het kenmerk *DisplayName* in AD, zodat het ook informatie biedt over de afdeling en het land/de regio van de gebruiker. Als John Smith werkt in de marketing afdeling in ons, wilt u mogelijk zijn *DisplayName* laten zien als *Smit, John (Marketing-VS)* .

Hier vindt u informatie over het afhandelen van zulke vereisten voor het samen stellen van *CN* of *DisplayName* om kenmerken zoals bedrijf, bedrijfs eenheid, plaats of land/regio op te geven.

* Elk workday-kenmerk wordt opgehaald met behulp van een onderliggende XPATH API-expressie, die kan worden geconfigureerd in **kenmerk toewijzing-> geavanceerde sectie-> kenmerk lijst bewerken voor werkdag**. Hier volgt de standaard XPATH API-expressie voor *PreferredFirstName*-, *PreferredLastName*-, *Company-* en *SupervisoryOrganization* -kenmerken.

     | Kenmerk workday | API XPATH-expressie |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Bedrijf | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | Word: worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [@wd:type = ' Organization_Type_ID '] = ' toezicht ']/WD: Naam_organisatie/text () |
  
   Bevestig met uw werkdag team dat de API-expressie hierboven geldig is voor de Tenant configuratie van uw werkdag. Indien nodig kunt u deze bewerken zoals beschreven in de sectie [de lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes).

* Evenzo worden de land gegevens die aanwezig zijn in workday opgehaald met behulp van het volgende XPATH: *WD: worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Er zijn vijf landspecifieke kenmerken die beschikbaar zijn in de sectie lijst met kenmerk dagen.

     | Kenmerk workday | API XPATH-expressie |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Bevestig met uw werkdag team dat de API-expressies hierboven geldig zijn voor de Tenant configuratie van uw werkdag. Indien nodig kunt u deze bewerken zoals beschreven in de sectie [de lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes).

* Als u de expressie juiste kenmerk toewijzing wilt maken, identificeert u welk workday-kenmerk ' gezaghebbend ' de voor naam, achternaam, land/regio en afdeling van de gebruiker vertegenwoordigt. Stel dat de kenmerken respectievelijk *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* en *SupervisoryOrganization* zijn. U kunt dit als volgt gebruiken om een expressie voor het kenmerk AD *DisplayName* te maken om een weergave naam op te halen *, zoals Smit, John (marketing-US)* .

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Als u de juiste expressie hebt, bewerkt u de tabel kenmerk toewijzingen en wijzigt u de kenmerk toewijzing *DisplayName* , zoals hieronder wordt weer gegeven:   ![DisplayName toewijzing @ no__t-1

* Als u het bovenstaande voor beeld uitbreidt, kunt u plaatsnamen van een werkdag omzetten in steno waarden en deze vervolgens gebruiken om weergave namen te maken, zoals *Smit, John (Chi)* of *Jansen, Jane (NYC)* . vervolgens kan dit resultaat worden bereikt met een switch expressie met het kenmerk van de werkdag *gemeente* als de determinant-variabele.

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
  * [Syntaxis van de functie Switch](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Syntaxis van de functie samen voegen](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Syntaxis van de functie Append](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hoe kan ik SelectUniqueValue gebruiken om unieke waarden voor het kenmerk samAccountName te genereren?

Stel dat u unieke waarden wilt genereren voor het kenmerk *sAMAccountName* met een combi natie van de kenmerken *FirstName* en *LastName* van werkdag. Hieronder ziet u een expressie die u kunt starten met:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

De werking van de bovenstaande expressie: Als de gebruiker John Smith is, probeert het eerst JSmith te genereren. als JSmith al bestaat, wordt er een JoSmith gegenereerd, als dat bestaat, wordt JohSmith gegenereerd. De expressie zorgt er ook voor dat de gegenereerde waarde voldoet aan de beperking van de lengte beperking en speciale tekens die aan *sAMAccountName*zijn gekoppeld.

Zie ook:

* [Syntaxis van de mid-functie](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Syntaxis van de functie Replace](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue Function Syntax](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hoe kan ik tekens verwijderen en deze converteren naar gewone Engelse alfabetten?

Gebruik de functie [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) om speciale tekens in de voor naam en achternaam van de gebruiker te verwijderen en tegelijkertijd het e-mail adres of de CN-waarde voor de gebruiker samen te stellen.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

In deze sectie vindt u specifieke richt lijnen voor het oplossen van problemen met het inrichten van uw workday met behulp van de Azure AD-controle logboeken en Windows Server Logboeken-Logboeken. Het is gebaseerd op de algemene stappen voor probleem oplossing en de concepten die zijn vastgelegd in de [Tutorial: Rapportage over het automatisch inrichten van gebruikers accounts @ no__t-0

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

  [![Audit-logboek maken OPS](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Wanneer u op een van de records in het controle logboek klikt, wordt de pagina **Details van activiteit** geopend. Hier ziet u de pagina **activiteit Details** voor elk logboek record type.

* **Import** record voor workday: In deze logboek record worden de werknemers gegevens weer gegeven die zijn opgehaald uit workday. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen op te lossen bij het ophalen van gegevens uit workday. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Ad-import** record: In deze logboek record wordt informatie weer gegeven over het account dat is opgehaald uit AD. Net als tijdens het maken van de eerste gebruiker is er geen AD-account, de reden van de *activiteit* geeft aan dat er geen account met de waarde van het overeenkomende id-kenmerk is gevonden in Active Directory. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen op te lossen bij het ophalen van gegevens uit workday. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld.

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

* **Actie record synchronisatie regel** : In deze logboek record worden de resultaten van de kenmerk toewijzings regels en geconfigureerde bereik filters weer gegeven samen met de inrichtings actie die wordt ondernomen voor het verwerken van de gebeurtenis van de binnenkomende werkdag. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen met de synchronisatie actie op te lossen. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Als er problemen zijn met uw kenmerk toewijzings expressies of als de binnenkomende werk dagen problemen ondervinden (bijvoorbeeld een lege of null-waarde voor vereiste kenmerken), ziet u in deze fase een fout met de fout code om de details van de fout te geven.

* **Ad-export** record: In deze logboek record wordt het resultaat van het maken van een AD-account weer gegeven, samen met de kenmerk waarden die in het proces zijn ingesteld. Gebruik de informatie in de sectie met *aanvullende details* van het logboek record om problemen met de bewerking voor het maken van een account op te lossen. Hieronder wordt een voorbeeld record weer gegeven, samen met verwijzingen voor het interpreteren van elk veld. In de sectie ' aanvullende details ' is de ' eventname ' ingesteld op ' EntryExportAdd ', de ' JoiningProperty ' is ingesteld op de waarde van het kenmerk matching ID, de ' source anchor ' is ingesteld op de WorkdayID (WID) die aan de record is gekoppeld en ' TargetAnchor ' is ingesteld op de waarde van het AD-kenmerk ObjectGuid van de zojuist gemaakte gebruiker. 

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

  [![Manager-update](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

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

|#|Fout scenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Fout bij het installeren van de inrichtings agent met het fout bericht:  *Service Microsoft Azure AD Connect inrichtings agent (AADConnectProvisioningAgent) niet starten. Controleer of u voldoende rechten hebt om het systeem te starten.* | Deze fout wordt meestal weer gegeven als u probeert de inrichtings agent te installeren op een domein controller en groeps beleid voor komt dat de service wordt gestart.  Het wordt ook weer gegeven als er een eerdere versie van de agent wordt uitgevoerd en u deze nog niet hebt verwijderd voordat u een nieuwe installatie start.| Installeer de inrichtings agent op een niet-domein controller. Zorg ervoor dat vorige versies van de agent zijn verwijderd voordat u de nieuwe agent installeert.|
|2.| De Windows-service ' Microsoft Azure AD Connect inrichtings agent ' bevindt zich in de *Start* status en schakelt niet over naar de *actieve* status. | Als onderdeel van de installatie maakt de wizard agent een lokaal account (**NT Service @ no__t-1AADConnectProvisioningAgent**) op de server. Dit is het **aanmeldings** account dat wordt gebruikt voor het starten van de service. Als een beveiligings beleid op uw Windows-Server verhindert dat lokale accounts de services uitvoeren, wordt deze fout weer gestuit. | Open de *console Services*. Klik met de rechter muisknop op de Windows-service ' Microsoft Azure AD Connect inrichtings agent ' en geef in het tabblad Aanmelden het account van een domein beheerder op om de service uit te voeren. Start de service opnieuw. |
|3.| Bij het configureren van de inrichtings agent met uw AD-domein in de stap *Connect Active Directory*, neemt de wizard lange tijd in beslag bij het laden van het AD-schema en uiteindelijk een time-out. | Deze fout komt meestal voor als de wizard geen contact kan maken met de controllerserver van het AD-domein door firewallproblemen. | Wanneer u de referenties voor uw AD-domein opgeeft in het scherm *verbinding maken met* de wizard Active Directory, is de optie *domein controller selecteren prioriteit*. Gebruik deze optie om een domein controller te selecteren die zich op dezelfde site bevindt als de agent server en ervoor te zorgen dat er geen firewall regels zijn die de communicatie blok keren. |

#### <a name="connectivity-errors"></a>Fouten in de basisnetwerkverbinding

Als de inrichtings service geen verbinding kan maken met workday of Active Directory, kan dit ertoe leiden dat het inrichten een status in quarantaine heeft. Gebruik de onderstaande tabel om verbindings problemen op te lossen.

|#|Fout scenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Wanneer u op **verbinding testen**klikt, wordt het volgende fout bericht weer gegeven: *There is een fout opgetreden bij het verbinding maken met Active Directory. Zorg ervoor dat de on-premises inrichtings agent wordt uitgevoerd en is geconfigureerd met het juiste Active Directory domein.* | Deze fout wordt meestal weer gegeven als de inrichtings agent niet actief is of als er een firewall is die de communicatie blokkeert tussen Azure AD en de inrichtings agent. Deze fout kan ook worden weer geven als het domein niet is geconfigureerd in de wizard voor de agent. | Open de *Services* -console op de Windows-Server om te controleren of de agent wordt uitgevoerd. Open de wizard inrichtings agent en controleer of het juiste domein is geregistreerd bij de agent.  |
|2.| De inrichtings taak gaat over het weekend (VR-SAT) in quarantaine status en er wordt een e-mail melding weer geven dat er een fout is opgetreden bij de synchronisatie. | Een veelvoorkomende oorzaak van deze fout is de geplande downtime voor Workday. Let op: Als u gebruikmaakt van een Workday-implementatietenant, wordt in het weekend downtime gepland voor de Workday-implementatietenants (meestal van vrijdagavond tot zaterdagochtend). Gedurende deze periode kan de Workday-app voor inrichting de status In quarantaine krijgen, omdat deze geen verbinding kan maken met Workday. De status wordt weer normaal zodra de Workday-implementatietenant weer online is. In zeldzame gevallen ziet u deze fout mogelijk ook als het wachtwoord van de integratiesysteemgebruiker is gewijzigd vanwege het vernieuwen van de tenant, of als het account is vergrendeld of de status Verlopen heeft. | Neem contact op met de Workday-beheerder of integratiepartner voor informatie over de geplande downtime van Workday om de waarschuwingen tijdens deze periode te negeren en de beschikbaarheid te bevestigen zodra het Workday-exemplaar weer online is.  |


#### <a name="ad-user-account-creation-errors"></a>Fouten bij het maken van een AD-gebruikers account

|#|Fout scenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Mislukte export bewerkingen in het audit logboek met het bericht *Error: OperationsError-SvcErr: Er is een bewerkingsfout opgetreden. Er is geen hoofdverwijzing geconfigureerd voor de directoryservice. De Directory service kan daarom geen verwijzingen naar objecten buiten dit forest verzenden.* | Deze fout wordt meestal weer gegeven als de *Active Directory container* -OE niet juist is ingesteld of als er problemen zijn met de expressie toewijzing die voor *parentDistinguishedName*wordt gebruikt. | Schakel de para meter *Active Directory container* OE in voor type fouten. Als u *parentDistinguishedName* gebruikt in de kenmerktoewijzing, zorg er dan voor dat het altijd resulteert in een bekende container binnen het AD-domein. Controleer de gebeurtenis *exporteren* in de audit Logboeken om de gegenereerde waarde te bekijken. |
|2.| Mislukte export bewerkingen in het audit logboek met fout code: *SystemForCrossDomainIdentityManagementBadResponse* en Message *Error: ConstraintViolation-AtrErr: Een waarde in de aanvraag is ongeldig. Een waarde voor het kenmerk bevindt zich niet in het acceptabele bereik van waarden. Details van \nError: CONSTRAINT_ATT_TYPE-Company @ no__t-0. | Hoewel deze fout specifiek is voor het kenmerk *bedrijf* , wordt deze fout mogelijk ook weer geven voor andere kenmerken, zoals *CN* . Deze fout wordt weer gegeven als gevolg van een AD-afgedwongen schema beperking. Standaard hebben de kenmerken als *bedrijf* en *CN* in AD een maximum van 64 tekens. Als de waarde die afkomstig is van workday meer is dan 64 tekens, wordt dit fout bericht weer gegeven. | Controleer de gebeurtenis *exporteren* in de audit Logboeken om de waarde te zien voor het kenmerk dat in het fout bericht is gerapporteerd. Overweeg het afkappen van de waarde die afkomstig is van workday met de functie [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) of het wijzigen van toewijzingen in een AD-kenmerk dat geen vergelijk bare lengte beperkingen heeft.  |

#### <a name="ad-user-account-update-errors"></a>Fouten bij het bijwerken van het AD-gebruikers account

Tijdens het update proces van het AD-gebruikers account wordt door de inrichtings service informatie gelezen uit zowel workday als AD, worden de regels voor kenmerk toewijzing uitgevoerd en wordt bepaald of een wijziging moet worden doorgevoerd. Dienovereenkomstig wordt een update gebeurtenis geactiveerd. Als een van deze stappen een fout tegen komt, wordt deze in de audit logboeken vastgelegd. Gebruik de onderstaande tabel om veelvoorkomende Update fouten op te lossen.

|#|Fout scenario |Mogelijke oorzaken|Aanbevolen oplossing|
|--|---|---|---|
|1.| Actie fouten van de synchronisatie regel in het audit logboek met de bericht *gebeurtenisnaam = EntrySynchronizationError en error code = EndpointUnavailable*. | Deze fout wordt weer gegeven als de inrichtings service geen gebruikers profiel gegevens kan ophalen uit Active Directory vanwege een verwerkings fout die wordt aangetroffen door de on-premises inrichtings agent. | Controleer de inrichtings agent Logboeken Logboeken voor fout gebeurtenissen die duiden op problemen met de Lees bewerking (filter op gebeurtenis-ID #2). |
|2.| Het kenmerk manager in AD wordt niet bijgewerkt voor bepaalde gebruikers in AD. | De meest waarschijnlijke oorzaak van deze fout is als u bereik regels gebruikt en de Manager van de gebruiker geen deel uitmaakt van het bereik. U kunt dit probleem ook ondervinden als het overeenkomstige ID-kenmerk van de Manager (bijvoorbeeld EmployeeID) niet is gevonden in het doel-AD-domein of niet is ingesteld op de juiste waarde. | Controleer het bereik filter en voeg de Manager-gebruiker in het bereik toe. Controleer het profiel van de manager in AD om te controleren of er een waarde is voor het kenmerk matching ID. |

## <a name="managing-your-configuration"></a>Uw configuratie beheren

In deze sectie wordt beschreven hoe u uw op workday gestuurde gebruikers inrichtings configuratie verder kunt uitbreiden, aanpassen en beheren. De volgende onderwerpen komen aan bod:

* [De lijst met gebruikers kenmerken van workday aanpassen](#customizing-the-list-of-workday-user-attributes)  
* [De configuratie exporteren en importeren](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>De lijst met gebruikers kenmerken van workday aanpassen

De workday-inrichtings-apps voor Active Directory en Azure AD bevatten zowel een standaard lijst met gebruikers kenmerken voor werk dagen waaruit u kunt kiezen. Deze lijsten zijn echter niet volledig. Workday ondersteunt een groot aantal honderden mogelijke gebruikers kenmerken, die standaard of uniek kunnen zijn voor uw workday-Tenant.

De Azure AD-inrichtings service ondersteunt de mogelijkheid om uw lijst-of workday-kenmerk aan te passen, zodat hierin alle kenmerken worden weer gegeven die beschikbaar zijn in de [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) -bewerking van de Human Resources API.

Als u deze wijziging wilt uitvoeren, moet u [werkdag Studio](https://community.workday.com/studio-download) gebruiken om de XPath-expressies te extra heren die de kenmerken vertegenwoordigen die u wilt gebruiken, en deze vervolgens toe te voegen aan uw inrichtings configuratie met behulp van de geavanceerde kenmerk editor in de Azure Portal.

**Een XPath-expressie ophalen voor een gebruikers kenmerk workday:**

1. Werk [dagen Studio](https://community.workday.com/studio-download)downloaden en installeren. U hebt een workday-community-account nodig om toegang te krijgen tot het installatie programma.

2. Down load het Human_Resources WSDL-bestand van de werkdag via deze URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Start workday Studio.

4. Selecteer op de opdracht balk de optie **Workday > test webservice in tester** .

5. Selecteer **extern**en selecteer het Human_Resources WSDL-bestand dat u hebt gedownload in stap 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Stel het veld **locatie** in op `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, maar vervang ' IMPL-CC ' door het daad werkelijke exemplaar type en ' TENANT ' door de naam van uw echte TENANT.

7. Stel de **bewerking** in op **Get_Workers**

8.  Klik op de kleine koppeling **configureren** onder de deel Vensters aanvraag/antwoord om uw workday-referenties in te stellen. Controleer de **verificatie**en voer vervolgens de gebruikers naam en het wacht woord in voor uw workday-integratie systeem account. Zorg ervoor dat u de gebruikers naam met de naam @ no__t-0tenant formatteert en de optie **WS-Security UsernameToken** geselecteerd laten.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecteer **OK**.

10. Plak in het deel venster **aanvraag** in de onderstaande XML en stel **Employee_ID** in op de werk nemer-id van een echte gebruiker in uw workday-Tenant. Selecteer een gebruiker met het kenmerk dat u wilt ophalen.

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

14. Navigeer in de bestands structuur naar **/env: Envelop > env: Hoofd tekst > WD: Get_Workers_Response > WD: Response_Data > WD: Werk nemer @ no__t-0 om de gegevens van uw gebruiker te vinden.

15. Onder **wd: Werk nemer @ no__t-0, zoek het kenmerk dat u wilt toevoegen en selecteer het.

16. Kopieer de XPath-expressie voor het geselecteerde kenmerk uit het **veld documentpad** .

17. Verwijder de **/env: envelop/env: Body/WD: Get_Workers_Response/WD: Response_Data/** voor voegsel van de gekopieerde expressie.

18. Als het laatste item in de gekopieerde expressie een knoop punt is (bijvoorbeeld: "/WD: Birth_Date ') en voeg vervolgens **/Text ()** aan het einde van de expressie toe. Dit is niet nodig als het laatste item een kenmerk is (bijvoorbeeld: "/@wd: type").

19. Het resultaat moet er ongeveer als `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`volgt uitzien. Deze waarde wordt gekopieerd naar de Azure Portal.

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

Raadpleeg het artikel [inrichtings configuratie exporteren en importeren](../manage-apps/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Persoonlijke gegevens beheren

De workday-inrichtings oplossing voor Active Directory vereist dat een inrichtings agent wordt geïnstalleerd op een on-premises Windows-Server. deze agent maakt Logboeken in het Windows-gebeurtenis logboek die persoonlijke gegevens kunnen bevatten, afhankelijk van uw workday to AD-kenmerk toewijzingen. Om te voldoen aan de verplichtingen van de privacy van de gebruiker, kunt u ervoor zorgen dat er niet meer dan 48 uur geen gegevens in de gebeurtenis logboeken worden bewaard door een geplande Windows-taak in te stellen om het gebeurtenis logboek te wissen.

De Azure AD-inrichtings service valt onder de **gegevens verwerkings** categorie AVG-classificatie. Als pijp lijn van de gegevens processor biedt de service gegevens verwerking aan belang rijke partners en eind gebruikers. De Azure AD-inrichtings service genereert geen gebruikers gegevens en heeft geen onafhankelijke controle over welke persoons gegevens worden verzameld en hoe deze worden gebruikt. Het ophalen van gegevens, aggregatie, analyse en rapportage in azure AD-inrichtings service zijn gebaseerd op bestaande bedrijfs gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Ten aanzien van gegevens retentie, genereert de Azure AD-inrichtings service geen rapporten, voert Analytics niet uit of bieden inzichten meer dan 30 dagen. Daarom slaat de Azure AD-inrichtings service geen gegevens die na 30 dagen worden opgeslagen, verwerkt of bewaard. Dit ontwerp is compatibel met de AVG-voor Schriften, micro soft-regelgeving voor privacybeleid en Azure AD-Bewaar beleid voor gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen werk dagen en Azure Active Directory](workday-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het gebruik van Microsoft Graph-Api's voor het beheren van inrichtings configuraties](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
