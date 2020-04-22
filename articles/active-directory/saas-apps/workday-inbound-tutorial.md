---
title: 'Zelfstudie: Werkdag configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Workday.
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
ms.openlocfilehash: bdf0cbfb91332d60516432a7a67fb10404d89113
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683837"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Zelfstudie: Werkdag configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen weer te geven die u moet uitvoeren om werknemersprofielen van Workday te importeren in zowel Active Directory als Azure Active Directory, met optionele terugschrijfgegevens van e-mailadres en gebruikersnaam naar Workday.

## <a name="overview"></a>Overzicht

De [Azure Active Directory-service voor het inrichten van gebruikers](../app-provisioning/user-provisioning.md) integreert met de [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) om gebruikersaccounts in te richten. Azure AD gebruikt deze verbinding om de volgende werkstromen voor het inrichten van gebruikers in te schakelen:

* **Gebruikers inrichten in Active Directory** - Bepaalde sets gebruikers van Workday inrichten in een of meer Active Directory-domeinen.

* **Cloudgebruikers inrichten in Azure Active Directory** - In scenario's waarin on-premises Active Directory niet wordt gebruikt, kunnen gebruikers rechtstreeks van Workday naar Azure Active Directory worden ingericht met behulp van de Azure AD-service voor gebruikersvoorziening.

* **E-mailadres en gebruikersnaam terugschrijven naar Werkdag** - De azure AD-service voor het inrichten van gebruikers kan de e-mailadressen en gebruikersnaam van Azure AD terugschrijven naar Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Welke human resources scenario's dekt het?

De Workday-gebruikersinrichtingswerkstromen die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende scenario's voor het beheer van menselijke resources en identiteitslevenscyclus:

* **Nieuwe werknemers inhuren** - Wanneer een nieuwe werknemer wordt toegevoegd aan Workday, wordt er automatisch een gebruikersaccount gemaakt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD,](../app-provisioning/user-provisioning.md)met terugschrijfgegevens van het e-mailadres naar Workday.

* **Werknemerattribuut en profielupdates** : Wanneer een werknemersrecord wordt bijgewerkt in Workday (zoals hun naam, titel of manager), wordt hun gebruikersaccount automatisch bijgewerkt in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

* **Werknemersbeëindigingen** - Wanneer een werknemer wordt beëindigd in Workday, wordt zijn gebruikersaccount automatisch uitgeschakeld in Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

* **Werknemer rehires** - Wanneer een werknemer opnieuw wordt aangenomen in Workday, kan hun oude account automatisch opnieuw worden geactiveerd of opnieuw worden ingericht (afhankelijk van uw voorkeur) naar Active Directory, Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze gebruikersinrichtingsoplossing het meest geschikt?

Deze Workday-oplossing voor het inrichten van gebruikers is bij uitstek geschikt voor:

* Organisaties die een vooraf gebouwde, cloudgebaseerde oplossing willen voor het inrichten van Workday-gebruikers

* Organisaties die directe gebruikersinrichting van Workday naar Active Directory of Azure Active Directory vereisen

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit de Workday HCM-module (zie [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organisaties die gebruikers moeten samenvoegen, verplaatsen en laten synchroniseren met een of meer Active Directory-forests, Domeinen en -gegevens op basis van wijzigingsgegevens die zijn gedetecteerd in de Workday HCM-module (zie [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Oplossingsarchitectuur

In deze sectie wordt de end-to-end-inrichtingsoplossingsarchitectuur voor veelvoorkomende hybride omgevingen beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevensstroom – van werkdag tot on-premises Active Directory:** In deze stroomwerkgebeurtenis (zoals Nieuwe aanwervingen, Overdrachten, Beëindigingen) vinden eerst plaats in de HR-tenant van De Werkdag in de cloud en vervolgens worden de gebeurtenisgegevens via Azure AD en de inrichtingsagent in on-premises Active Directory ingevoerd. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in AD.
* **Terugvloeiingsstroom voor e-mail en gebruikersnaam – van on-premises Active Directory tot Werkdag:** Zodra het aanmaken van een account is voltooid in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kan het kenmerk e-mail en gebruikersnaam worden teruggeschreven naar Workday.

![Overzicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevensstroom van eind-tot-eindgebruiker

1. Het HR-team voert werknemerstransacties uit (Joiners/Verhuizers/Leavers of New Hires/Transfers/Terminations) in Workday HCM
2. De Azure AD Provisioning Service voert geplande synchronisaties van identiteiten van Workday HR uit en identificeert wijzigingen die moeten worden verwerkt voor synchronisatie met on-premises Active Directory.
3. De Azure AD Provisioning Service roept de on-premises Azure AD Connect Provisioning Agent op met een request payload met AD-account maken/bijwerken/inschakelen/uitschakelen bewerkingen.
4. De Azure AD Connect Provisioning Agent gebruikt een serviceaccount om AD-accountgegevens toe te voegen/bijwerken.
5. De Azure AD Connect / AD Sync-engine voert deltasynchronisatie uit om updates in AD op te halen.
6. De Active Directory-updates worden gesynchroniseerd met Azure Active Directory.
7. Als de Workday Writeback-connector is geconfigureerd, wordt het e-mailkenmerk en de gebruikersnaam teruggeschreven naar Workday, op basis van het gebruikte overeenkomende kenmerk.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Voordat u begint met uw Workday-integratie, raadpleegt u de onderstaande vereisten en leest u de volgende richtlijnen voor het afstemmen van uw huidige Active Directory-architectuur en gebruikersinrichtingsvereisten met de oplossing(en) die door Azure Active Directory worden geleverd. Er is ook een uitgebreid [implementatieplan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) met planningswerkbladen beschikbaar om u te helpen bij de samenwerking met uw Workday-integratiepartner en HR-belanghebbenden.

In deze sectie worden de volgende aspecten van de planning bewerk:

* [Vereisten](#prerequisites)
* [Inrichtingsconnector-apps selecteren om te implementeren](#selecting-provisioning-connector-apps-to-deploy)
* [Implementatie van Azure AD Connect Provisioning Agent plannen](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integreren met meerdere Active Directory-domeinen](#integrating-with-multiple-active-directory-domains)
* [Workday plannen voor toewijzing en transformaties van Active Directory-gebruikerskenmerken](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een geldige Azure AD Premium P1- of hogere abonnementslicentie voor elke gebruiker die afkomstig is van Workday en is ingericht in on-premises Active Directory of Azure Active Directory.
* Toegang tot globale ad-beheerder van Azure AD om de inrichtingsagent te configureren
* Een Workday implementatietenant voor test- en integratiedoeleinden
* Beheerdersmachtigingen in Workday om een systeemintegratiegebruiker te maken en wijzigingen aan te brengen in het testen van werknemersgegevens voor testdoeleinden
* Voor gebruikersbepaling in Active Directory is een server met Windows Server 2012 of hoger met .NET 4.7.1+ runtime vereist om de [on-premises inrichtingsagent](https://go.microsoft.com/fwlink/?linkid=847801) te hosten
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) voor het synchroniseren van gebruikers tussen Active Directory en Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Inrichtingsconnector-apps selecteren om te implementeren

Om het inrichten van werkstromen tussen Workday en Active Directory te vergemakkelijken, biedt Azure AD meerdere inrichtingsconnector-apps die u toevoegen vanuit de azure AD-app-galerie:

![Azure AD-app-galerie](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday naar Active Directory User Provisioning** - Deze app vergemakkelijkt het inrichten van gebruikersaccounts van Workday naar één Active Directory-domein. Als u meerdere domeinen hebt, u één exemplaar van deze app toevoegen vanuit de Azure AD-app-galerie voor elk Active Directory-domein waaraan u moet inrichten.

* **Workday to Azure AD User Provisioning** - Hoewel Azure AD Connect het hulpmiddel is dat moet worden gebruikt om Active Directory-gebruikers te synchroniseren met Azure Active Directory, kan deze app worden gebruikt om de inrichting van alleen cloudgebruikers vanaf Workday naar één Azure Active Directory-tenant te vergemakkelijken.

* **Workday Writeback** - Deze app vergemakkelijkt het terugschrijven van e-mailadressen van gebruikers van Azure Active Directory naar Workday.

> [!TIP]
> De reguliere app 'Werkdag' wordt gebruikt voor het instellen van eenmalige aanmelding tussen Workday en Azure Active Directory.

Gebruik het onderstaande grafiek met beslissingsstroom om te bepalen welke Workday provisioning-apps relevant zijn voor uw scenario.
    ![Beslissingsstroomdiagram](./media/workday-inbound-tutorial/wday_app_flowchart.png "Beslissingsstroomdiagram")

Gebruik de inhoudsopgave om naar het desbetreffende gedeelte van deze zelfstudie te gaan.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Implementatie van Azure AD Connect Provisioning Agent plannen

> [!NOTE]
> Deze sectie is alleen relevant als u van plan bent de Werkdag te implementeren in active directory-gebruikersinrichtingsapp. U dit overslaan als u de Workday Writeback of Workday implementeert in de Azure AD User Provisioning App.

De oplossing voor het inrichten van de gebruiker van Workday naar AD vereist het implementeren van een of meer provisioning agents op servers met Windows 2012 R2 of hoger met minimaal 4 GB RAM en .NET 4.7.1+ runtime. Bij de installatie van de voorzieningsagent moet rekening worden gehouden met de volgende overwegingen:

* Ervoor zorgen dat de hostserver waarop de provisioningagent wordt uitgevoerd, netwerktoegang heeft tot het doel-AD-domein
* De wizard Configuratie van provisioning agent registreert de agent met uw Azure AD-tenant en het registratieproces vereist toegang tot *.msappproxy.net via de TLS-poort 443. Zorg ervoor dat er uitgaande firewallregels zijn die deze communicatie mogelijk maken. De agent ondersteunt [uitgaande HTTPS-proxyconfiguratie](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* De inrichtingsagent gebruikt een serviceaccount om te communiceren met het on-premises AD-domein(en). Voorafgaand aan de installatie van de agent wordt aanbevolen dat u een serviceaccount maakt met machtigingen voor domeinbeheerders en een wachtwoord dat niet verloopt.  
* Tijdens de configuratie van provisioning agent u domeincontrollers selecteren die inrichtingsaanvragen moeten afhandelen. Als u meerdere geografisch gedistribueerde domeincontrollers hebt, installeert u de provisioning agent op dezelfde site als uw voorkeursdomeincontroller(s) om de betrouwbaarheid en prestaties van de end-to-end oplossing te verbeteren
* Voor hoge beschikbaarheid u meer dan één inrichtingsagent implementeren en registreren om dezelfde set on-premises AD-domeinen te verwerken.

> [!IMPORTANT]
> In productieomgevingen raadt Microsoft u aan minimaal 3 provisioning agents te configureren met uw Azure AD-tenant voor hoge beschikbaarheid.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integreren met meerdere Active Directory-domeinen

> [!NOTE]
> Deze sectie is alleen relevant als u van plan bent de Werkdag te implementeren in active directory-gebruikersinrichtingsapp. U dit overslaan als u de Workday Writeback of Workday implementeert in de Azure AD User Provisioning App.

Afhankelijk van uw Active Directory-topologie moet u het aantal gebruikersinrichtingsconnector-apps en het aantal inrichtingsagents bepalen dat moet worden geconfigureerd. Hieronder vindt u enkele van de veelvoorkomende implementatiepatronen die u verwijzen naar uw implementatie.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Implementatiescenario #1 : Single Workday Tenant -> Één AD-domein

In dit scenario hebt u één Workday-tenant en wilt u gebruikers inrichten voor één advertentiedomein voor één doel. Hier is de aanbevolen productieconfiguratie voor deze implementatie.

|   |   |
| - | - |
| Nee. van de voorzieningsmiddelen om on-premises in te zetten | 3 (voor hoge beschikbaarheid en fail over) |
| Nee. van Workday naar AD-gebruikersinrichtingsapps om te configureren in Azure-portal | 1 |

  ![Scenario 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Implementatiescenario #2 : Single Workday Tenant -> Meerdere AD-domeinen met meerdere onderliggende kinderen

In dit scenario worden gebruikers van Workday ingericht naar meerdere ad-onderliggende doeldomeinen in een forest. Hier is de aanbevolen productieconfiguratie voor deze implementatie.

|   |   |
| - | - |
| Nee. van de voorzieningsmiddelen om on-premises in te zetten | 3 (voor hoge beschikbaarheid en fail over) |
| Nee. van Workday naar AD-gebruikersinrichtingsapps om te configureren in Azure-portal | één app per onderliggend domein |

  ![Scenario 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Implementatiescenario #3 : Single Workday Tenant -> Onsamenhangende AD-forests

Dit scenario omvat het inrichten van gebruikers van Workday naar domeinen in onsamenhangende AD-forests. Hier is de aanbevolen productieconfiguratie voor deze implementatie.

|   |   |
| - | - |
| Nee. van de voorzieningsmiddelen om on-premises in te zetten | 3 per onsamenhangend AD-bos |
| Nee. van Workday naar AD-gebruikersinrichtingsapps om te configureren in Azure-portal | één app per onderliggend domein |

  ![Scenario 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Workday plannen voor toewijzing en transformaties van Active Directory-gebruikerskenmerken

> [!NOTE]
> Deze sectie is alleen relevant als u van plan bent de Werkdag te implementeren in active directory-gebruikersinrichtingsapp. U dit overslaan als u de Workday Writeback of Workday implementeert in de Azure AD User Provisioning App.

Voordat u gebruikersinrichting voor een Active Directory-domein configureert, moet u rekening houden met de volgende vragen. De antwoorden op deze vragen bepalen hoe uw scopingfilters en kenmerktoewijzingen moeten worden ingesteld.

* **Welke gebruikers in Workday moeten worden ingericht in dit Active Directory-forest?**

  * *Voorbeeld: gebruikers waarbij het kenmerk Workday 'Bedrijf' de waarde 'Contoso' bevat en het kenmerk 'Worker_Type' 'Normaal' bevat*

* **Hoe worden gebruikers doorgestuurd naar verschillende organisatie-eenheden (OE's)?**

  * *Voorbeeld: gebruikers worden doorgestuurd naar Gegevens die overeenkomen met een kantoorlocatie, zoals gedefinieerd in de kenmerken 'Gemeente' en 'Country_Region_Reference'*

* **Hoe moeten de volgende kenmerken worden ingevuld in de Active Directory?**

  * Algemene naam (cn)
    * *Voorbeeld: De waarde workday-User_ID gebruiken, zoals ingesteld door human resources*

  * Werknemers-ID (employeeId)
    * *Voorbeeld: de waarde Workday Worker_ID gebruiken*

  * SAM-accountnaam (sAMAccountName)
    * *Voorbeeld: gebruik de waarde Workday User_ID, gefilterd door een Azure AD-inrichtingsexpressie om illegale tekens te verwijderen*

  * Gebruikersnaam (userPrincipalName)
    * *Voorbeeld: de waarde Workday User_ID gebruiken, met een Azure AD-inrichtingsexpressie om een domeinnaam toe te schrijven*

* **Hoe moeten gebruikers worden gekoppeld tussen Workday en Active Directory?**

  * *Voorbeeld: Gebruikers met een specifieke waarde voor Workday "Worker_ID" worden gekoppeld aan Active Directory-gebruikers waarbij 'employeeID' dezelfde waarde heeft. Als de Worker_ID waarde niet wordt gevonden in Active Directory, maakt u een nieuwe gebruiker.*
  
* **Bevat het Active Directory-forest al de gebruikers-i-apps die nodig zijn om de overeenkomende logica te laten werken?**

  * *Voorbeeld: Als deze instelling een nieuwe Workday-implementatie is, wordt aanbevolen dat Active Directory vooraf wordt ingevuld met de juiste workday-Worker_ID waarden (of unieke id-waarde naar keuze) om de overeenkomende logica zo eenvoudig mogelijk te houden.*

Het instellen en configureren van deze speciale inrichtingsconnector-apps is het onderwerp van de resterende secties van deze zelfstudie. Welke apps u wilt configureren, hangt af van de systemen die u moet inrichten en hoeveel Active Directory-domeinen en Azure AD-tenants zich in uw omgeving bevinden.

## <a name="configure-integration-system-user-in-workday"></a>Gebruiker van het integratiesysteem configureren in Workday

Een veelvoorkomende vereiste van alle Workday provisioning connectors is dat ze referenties van een Workday-integratiesysteemgebruiker nodig hebben om verbinding te maken met de Workday Human Resources API. In deze sectie wordt beschreven hoe u een gebruiker van een integratiesysteem maakt in Workday en de volgende secties:

* [Een gebruiker van een integratiesysteem maken](#creating-an-integration-system-user)
* [Een integratiebeveiligingsgroep maken](#creating-an-integration-security-group)
* [Machtigingen voor domeinbeveiligingsbeleid configureren](#configuring-domain-security-policy-permissions)
* [Machtigingen voor bedrijfsprocesbeveiligingsbeleid configureren](#configuring-business-process-security-policy-permissions)
* [Wijzigingen in het beveiligingsbeleid activeren](#activating-security-policy-changes)

> [!NOTE]
> Het is mogelijk om deze procedure te omzeilen en in plaats daarvan een Workday global administrator account te gebruiken als het systeemintegratieaccount. Dit kan prima werken voor demo's, maar wordt niet aanbevolen voor productie-implementaties.

### <a name="creating-an-integration-system-user"></a>Een gebruiker van een integratiesysteem maken

**Ga als u een gebruiker van het integratiesysteem voor maken:**

1. Meld u aan bij uw Workday-tenant met behulp van een beheerdersaccount. Voer in de **Workday-toepassing**de gebruiker maken in het zoekvak in en klik op **De gebruiker van het integratiesysteem maken.**

   ![Gebruiker maken](./media/workday-inbound-tutorial/wd_isu_01.png "Gebruiker maken")
2. Voltooi de taak **Gebruikersvan het integratiesysteem maken** door een gebruikersnaam en wachtwoord op te geven voor een nieuwe gebruiker van het integratiesysteem.  
  
   * Laat de optie **Nieuw wachtwoord vereisen bij Volgende aanmelding** niet aangevinkt, omdat deze gebruiker zich programmatisch aanmeldt.
   * Laat de **time-outminuten** van de sessie met de standaardwaarde van 0, waardoor de sessies van de gebruiker niet voortijdig worden getimed.
   * Selecteer de optie **Gebruikersinterfacesessies niet toestaan** omdat deze een extra beveiligingslaag biedt die voorkomt dat een gebruiker met het wachtwoord van het integratiesysteem zich aanmeldt bij Workday.

   ![Gebruiker van het integratiesysteem maken](./media/workday-inbound-tutorial/wd_isu_02.png "Gebruiker van het integratiesysteem maken")

### <a name="creating-an-integration-security-group"></a>Een integratiebeveiligingsgroep maken

In deze stap maakt u een beveiligingsgroep voor onbeperkte of beperkte integratiesystemen in Workday en wijst u de in de vorige stap gemaakte integratiesysteemgebruiker toe aan deze groep.

**Ga als lid van het werk om een beveiligingsgroep te maken:**

1. Voer de beveiligingsgroep maken in het zoekvak in en klik op **Beveiligingsgroep maken**.

    ![Beveiligingsgroep maken](./media/workday-inbound-tutorial/wd_isu_03.png "Beveiligingsgroep maken")
2. Voltooi de taak **Beveiligingsgroep maken.** 

   * Er zijn twee soorten beveiligingsgroepen in Workday:
     * **Niet beperkt:** Alle leden van de beveiligingsgroep hebben toegang tot alle gegevensinstanties die door de beveiligingsgroep zijn beveiligd.
     * **Beperkt:** Alle leden van de beveiligingsgroep hebben contextuele toegang tot een subset van gegevensinstanties (rijen) waartoe de beveiligingsgroep toegang heeft.
   * Neem contact op met uw Workday-integratiepartner om het juiste type beveiligingsgroep voor de integratie te selecteren.
   * Zodra u het groepstype kent, selecteert u **Integration System Security Group (Unconstrained)** of Integration System Security Group **(Constrained)** in de vervolgkeuzelijst **Type tenantbeveiligingsgroep.**

     ![Beveiligingsgroep maken](./media/workday-inbound-tutorial/wd_isu_04.png "Beveiligingsgroep maken")

3. Nadat de creatie van de beveiligingsgroep is geslaagd, ziet u een pagina waarop u leden toewijzen aan de beveiligingsgroep. Voeg de nieuwe gebruiker van het integratiesysteem die in de vorige stap is gemaakt toe aan deze beveiligingsgroep. Als u een *beperkte* beveiligingsgroep gebruikt, moet u ook het juiste organisatiebereik selecteren.

    ![Beveiligingsgroep bewerken](./media/workday-inbound-tutorial/wd_isu_05.png "Beveiligingsgroep bewerken")

### <a name="configuring-domain-security-policy-permissions"></a>Machtigingen voor domeinbeveiligingsbeleid configureren

In deze stap verleent u beleidsmachtigingen voor domeinbeveiliging voor de werknemersgegevens aan de beveiligingsgroep.

**Machtigingen voor domeinbeveiligingsbeleid configureren:**

1. Voer **domeinbeveiligingsconfiguratie** in het zoekvak in en klik op het **koppelingsrapport voor domeinbeveiliging**.  

    ![Domeinbeveiligingsbeleid](./media/workday-inbound-tutorial/wd_isu_06.png "Domeinbeveiligingsbeleid")  
2. Zoek **in het** tekstvak Domein naar de volgende domeinen en voeg deze één voor één toe aan het filter.  
   * *Externe rekeningvoorziening*
   * *Werknemersgegevens: werknemers*
   * *Werknemersgegevens: rapporten van openbare werknemers*
   * *Persoonsgegevens: contactgegevens voor werk*
   * *Werknemersgegevens: alle posities*
   * *Werknemersgegevens: huidige personeelsinformatie*
   * *Werknemersgegevens: bedrijfstitel op werknemersprofiel*
   * *Werkdagaccounts*
   
     ![Domeinbeveiligingsbeleid](./media/workday-inbound-tutorial/wd_isu_07.png "Domeinbeveiligingsbeleid")  

     ![Domeinbeveiligingsbeleid](./media/workday-inbound-tutorial/wd_isu_08.png "Domeinbeveiligingsbeleid") 

     Klik op **OK**.

3. Selecteer in het rapport dat wordt weergegeven de ellips (...) die wordt weergegeven naast **Extern accountinrichting** en klik op de menuoptie **Domain -> Machtigingen voor beveiligingsbeleid** bewerken

    ![Domeinbeveiligingsbeleid](./media/workday-inbound-tutorial/wd_isu_09.png "Domeinbeveiligingsbeleid")  

4. Schuif op de pagina **Machtigingen voor domeinbeveiliging** bewerken naar de sectie **Integratiemachtigingen**. Klik op het teken '+' om de integratiesysteemgroep toe te voegen aan de lijst met beveiligingsgroepen met integratiemachtigingen **voor klikken** en **plaatsen.**

    ![Machtigingen bewerken](./media/workday-inbound-tutorial/wd_isu_10.png "Machtigingen bewerken")  

5. Klik op het teken '+' om de integratiesysteemgroep toe te voegen aan de lijst met beveiligingsgroepen met integratiemachtigingen **voor klikken** en **plaatsen.**

    ![Machtigingen bewerken](./media/workday-inbound-tutorial/wd_isu_11.png "Machtigingen bewerken")  

6. Herhaal de volgende stappen 3-5 voor elk van deze resterende beveiligingsbeleidsregels:

   | Bewerking | Domeinbeveiligingsbeleid |
   | ---------- | ---------- |
   | Get and Put | Werknemersgegevens: rapporten van openbare werknemers |
   | Get and Put | Persoonsgegevens: contactgegevens voor werk |
   | Ophalen | Werknemersgegevens: werknemers |
   | Ophalen | Werknemersgegevens: alle posities |
   | Ophalen | Werknemersgegevens: huidige personeelsinformatie |
   | Ophalen | Werknemersgegevens: bedrijfstitel op werknemersprofiel |
   | Get and Put | Werkdagaccounts |

### <a name="configuring-business-process-security-policy-permissions"></a>Machtigingen voor bedrijfsprocesbeveiligingsbeleid configureren

In deze stap verleent u beleidsmachtigingen voor bedrijfsprocesbeveiliging voor de werknemersgegevens aan de beveiligingsgroep. Deze stap is vereist voor het instellen van de Workday Writeback-app-connector.

**Machtigingen voor bedrijfsprocesbeveiligingsbeleid configureren:**

1. Typ **Bedrijfsprocesbeleid** in het zoekvak en klik op de taak **Bedrijfsprocesbeveiligingsbeleid bewerken.**  

    ![Beveiligingsbeleid voor bedrijfsprocessen](./media/workday-inbound-tutorial/wd_isu_12.png "Beveiligingsbeleid voor bedrijfsprocessen")  

2. Zoek in het tekstvak **Bedrijfsprocestype** naar *Contactpersoon* en selecteer **Bedrijfsproces Wijzigen** van contactpersoon en klik op **OK**.

    ![Beveiligingsbeleid voor bedrijfsprocessen](./media/workday-inbound-tutorial/wd_isu_13.png "Beveiligingsbeleid voor bedrijfsprocessen")  

3. Schuif op de pagina **Bewerk beleid voor bedrijfsprocesbeveiliging** naar de sectie **Contactgegevens (Webservice) behouden.**

    ![Beveiligingsbeleid voor bedrijfsprocessen](./media/workday-inbound-tutorial/wd_isu_14.png "Beveiligingsbeleid voor bedrijfsprocessen")  

4. Selecteer en voeg de nieuwe beveiligingsgroep voor integratiesystemen toe aan de lijst met beveiligingsgroepen die de aanvraag voor webservices kunnen initiëren. Klik op **Gereed**. 

    ![Beveiligingsbeleid voor bedrijfsprocessen](./media/workday-inbound-tutorial/wd_isu_15.png "Beveiligingsbeleid voor bedrijfsprocessen")  

### <a name="activating-security-policy-changes"></a>Wijzigingen in het beveiligingsbeleid activeren

**Wijzigingen in het beveiligingsbeleid activeren:**

1. Voer activeren in het zoekvak in en klik op de koppeling **Wijzigingen in het beveiligingsbeleid activeren in afwachting**van het beveiligingsbeleid .

    ![Activeren](./media/workday-inbound-tutorial/wd_isu_16.png "Activeren")

1. Begin de taak Wijzigingen in het beveiligingsbeleid activeren in behandeling door een opmerking in te voeren voor controledoeleinden en klik vervolgens op **OK**.
1. Voltooi de taak op het volgende scherm door het selectievakje **Bevestigen in te schakelen**en klik op **OK**.

    ![Beveiliging activeren in afwachting van](./media/workday-inbound-tutorial/wd_isu_18.png "Beveiliging activeren in afwachting van")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Gebruikersvoorziening configureren van Workday naar Active Directory

In deze sectie vindt u stappen voor het inrichten van gebruikersaccounts van Workday naar elk Active Directory-domein binnen het bereik van uw integratie.

* [De inrichtingsconnector-app toevoegen en de inrichtingsagent downloaden](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [On-premises inrichtingsagent(s) installeren en configureren](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Connectiviteit configureren met Workday en Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Kenmerkentoewijzingen configureren](#part-4-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Deel 1: De inrichtingsconnector-app toevoegen en de inrichtingsagent downloaden

**Ga als het gaat om het configureren van Workday naar Active Directory provisioning:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer **Azure Active Directory**in de Azure-portal.

3. Selecteer **Enterprise-toepassingen**en **vervolgens Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen**en selecteer de categorie **Alles.**

5. Zoek naar **Workday Provisioning naar Active Directory**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met app-details wordt weergegeven, selecteert u **Inrichten**.

7. Wijzig de **inrichtingsmodus** **Mode** in **Automatisch**.

8. Klik op de informatiebanner die wordt weergegeven om de provisioning agent te downloaden. 

   ![Agent downloaden](./media/workday-inbound-tutorial/pa-download-agent.png "Scherm agent downloaden")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Deel 2: On-premises provisioning agent(s) installeren en configureren

Als u Active Directory on-premises wilt inrichten, moet de inrichtingsagent zijn geïnstalleerd op een server met .NET 4.7.1+ Framework en netwerktoegang tot het gewenste Active Directory-domein(en).

> [!TIP]
> U de versie van het .NET-framework op uw server controleren met behulp van de [instructies die hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)worden gegeven.
> Als de server geen .NET 4.7.1 of hoger heeft geïnstalleerd, u deze [vanaf hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)downloaden.  

Breng het gedownloade agent-installatieprogramma over naar de serverhost en volg de onderstaande stappen om de agentconfiguratie te voltooien.

1. Meld u aan bij de Windows Server waar u de nieuwe agent wilt installeren.

1. Start het installatiebedrijf, ga akkoord met de voorwaarden en klik op de knop **Installeren.**

   ![Scherm installeren](./media/workday-inbound-tutorial/pa_install_screen_1.png "Scherm installeren")
   
1. Nadat de installatie is voltooid, wordt de wizard gestart en ziet u het **SCHERM Azure AD verbinden.** Klik op de knop **Verifiëren** om verbinding te maken met uw Azure AD-exemplaar.

   ![Verbinding maken met Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Verbinding maken met Azure AD")
   
1. Verifieer naar uw Azure AD-exemplaar met globale beheerdersreferenties.

   ![Beheerder Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Beheerder Auth")

   > [!NOTE]
   > De Azure AD-beheerdersreferenties worden alleen gebruikt om verbinding te maken met uw Azure AD-tenant. De agent slaat de referenties niet lokaal op de server op.

1. Na succesvolle verificatie met Azure AD ziet u het **Active Directory-scherm verbinden.** Voer in deze stap uw AD-domeinnaam in en klik op de knop **Map toevoegen.**

   ![Map toevoegen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Map toevoegen")
  
1. U wordt nu gevraagd om de referenties in te voeren die nodig zijn om verbinding te maken met het AD-domein. Op hetzelfde scherm u de **prioriteit Voor domeincontroller selecteren** gebruiken om domeincontrollers op te geven die de agent moet gebruiken voor het verzenden van inrichtingsaanvragen.

   ![Domeinreferenties](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Na het configureren van het domein geeft het installatieprogramma een lijst met geconfigureerde domeinen weer. Op dit scherm u stap #5 herhalen en #6 om meer domeinen toe te voegen of op **Volgende** klikken om over te gaan tot agentregistratie.

   ![Geconfigureerde domeinen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Geconfigureerde domeinen")

   > [!NOTE]
   > Als u meerdere AD-domeinen hebt (bijvoorbeeld na.contoso.com, emea.contoso.com), voegt u elk domein afzonderlijk toe aan de lijst.
   > Alleen het toevoegen van het bovenliggende domein (bijvoorbeeld contoso.com) is niet voldoende. U moet elk onderliggend domein registreren bij de agent.
   
1. Bekijk de configuratiegegevens en klik op **Bevestigen** om de agent te registreren.
  
   ![Scherm bevestigen](./media/workday-inbound-tutorial/pa_install_screen_7.png "Scherm bevestigen")
   
1. De wizard Configuratie geeft de voortgang van de agentregistratie weer.
  
   ![Agent registratie](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent registratie")
   
1. Zodra de agentregistratie is geslaagd, u op **Afsluiten** klikken om de wizard te verlaten.
  
   ![Scherm afsluiten](./media/workday-inbound-tutorial/pa_install_screen_9.png "Scherm afsluiten")
   
1. Controleer de installatie van de agent en controleer of deze wordt uitgevoerd door de module 'Services' te openen en te zoeken naar de service met de naam 'Microsoft Azure AD Connect Provisioning Agent'.
  
   ![Services](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Deel 3: Configureer in de inrichtingsapp de verbinding met Workday en Active Directory
In deze stap maken we verbinding met Workday en Active Directory in de Azure-portal. 

1. Ga in de Azure-portal terug naar de Workday naar Active Directory User Provisioning App die is gemaakt in [deel 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Vul de sectie **Beheerdersreferenties** als volgt in:

   * **Gebruikersnaam op werkdagen** : voer de gebruikersnaam van het Workday-integratiesysteemaccount in, waarbij de tenantdomeinnaam is toegevoegd. Het moet er ongeveer uitzien als: **gebruikersnaam\@tenant_name**

   * **Workday-wachtwoord –** Voer het wachtwoord in van het Workday-integratiesysteemaccount

   * **URL van de Workday Web Services API –** Voer de URL in naar het eindpunt van de Workday-webservices voor uw tenant. Deze waarde moet https://wd3-impl-services1.workday.com/ccx/service/contoso4eruit zien als: , waar *contoso4* wordt vervangen door uw juiste tenant naam en *wd3-impl* wordt vervangen door de juiste omgevingstekenreeks.

     > [!NOTE]
     > Standaard gebruikt de app Workday Web Services v21.1 als er geen versiegegevens zijn opgegeven in de URL. Als u een specifieke Workday Web Services API-versie wilt gebruiken, gebruikt u de URL-indeling:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Voorbeeld: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0

   * **Active Directory Forest -** De naam van uw Active Directory-domein, zoals geregistreerd bij de agent. Gebruik de vervolgkeuzelijst om het doeldomein voor inrichten te selecteren. Deze waarde is meestal een tekenreeks zoals: *contoso.com*

   * **Active Directory-container -** Voer de container DN in waar de agent standaard gebruikersaccounts moet maken.
        Voorbeeld: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
        
     > [!NOTE]
     > Deze instelling wordt alleen in het spel gebracht voor gebruikersaccountcreaties als het *kenmerk ParentDistinguishedName* niet is geconfigureerd in de kenmerktoewijzingen. Deze instelling wordt niet gebruikt voor zoekopdrachten of updatebewerkingen van gebruikers. De volledige substructuur van het domein valt in het bereik van de zoekbewerking.

   * **Melding e-mail –** Voer uw e-mailadres in en schakel het selectievakje 'E-mail verzenden als er mislukt' in.

     > [!NOTE]
     > De Azure AD Provisioning Service verzendt e-mailmelding als de inrichtingstaak in een [quarantainestatus](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) terechtkomt.

   * Klik op de knop **Verbinding testen.** Als de verbindingstest slaagt, klikt u bovenaan op de knop **Opslaan.** Als dit niet lukt, controleert u of de workday-referenties en de AD-referenties die zijn geconfigureerd op de installatie van de agent geldig zijn.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **Toewijzingen** de **standaardtoewijzingswerkdagwerkers gesynchroniseerd met Active Directory voor on-premises weergegeven.**

### <a name="part-4-configure-attribute-mappings"></a>Deel 4: Toewijzingen van kenmerken configureren

In deze sectie configureert u hoe gebruikersgegevens van Workday naar Active Directory stromen.

1. Klik op het tabblad Inrichten onder **Toewijzingen**op **Werkdagwerkers synchroniseren met Active Directory voor on-premises**.

1. In het veld **Bronobjectbereik** u selecteren welke sets gebruikers in Workday in het bereik moeten zijn om in te richten op AD, door een set op kenmerken gebaseerde filters te definiëren. Het standaardbereik is "alle gebruikers in Workday". Voorbeeldfilters:

   * Voorbeeld: Bereik voor gebruikers met werknemers-i-d's tussen 1000000 en 2000000 (exclusief 2000000)

      * Kenmerk: WorkerID

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen werknemers en geen voorwaardelijke werknemers

      * Kenmerk: EmployeeID

      * Operator: IS NIET NULL

   > [!TIP]
   > Wanneer u de inrichtingsapp voor de eerste keer configureert, moet u uw kenmerktoewijzingen en -expressies testen en verifiëren om ervoor te zorgen dat deze het gewenste resultaat oplevert. Microsoft raadt aan om de scopingfilters onder **Source Object Scope** te gebruiken om uw toewijzingen te testen met een paar testgebruikers van Workday. Zodra u hebt geverifieerd dat de toewijzingen werken, u het filter verwijderen of geleidelijk uitbreiden met meer gebruikers.

   > [!CAUTION] 
   > Het standaardgedrag van de inrichtingsengine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw Workday-integratie met AD. Als u dit standaardgedrag wilt overschrijven, verwijst u naar het artikel [Verwijdering van gebruikersaccounts](../app-provisioning/skip-out-of-scope-deletions.md) overslaan die buiten het bereik vallen
  
1. In het veld **Doelobjectacties** u globaal filteren welke acties worden uitgevoerd in Active Directory. **Maken** en **bijwerken** komen het meest voor.

1. In de sectie **Toewijzingen van kenmerken** u definiëren hoe afzonderlijke workday-kenmerken worden toegewezen aan Active Directory-kenmerken.

1. Klik op een bestaande kenmerktoewijzing om deze bij te werken of klik op **Nieuwe toewijzing** onder aan het scherm toevoegen om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt deze eigenschappen:

      * **Toewijzingstype**

         * **Direct** : hiermee schrijft u de waarde van het kenmerk Werkdag aan het AD-kenmerk, zonder wijzigingen

         * **Constant** - Schrijf een statische, constante tekenreekswaarde naar het AD-kenmerk

         * **Expressie** : hiermee u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer kenmerken van Werkdag. [Zie dit artikel over expressies voor meer informatie.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Bronkenmerk** - Het gebruikerskenmerk van Workday. Zie De lijst met [gebruikerskenmerken van Workday](#customizing-the-list-of-workday-user-attributes)aanpassen als het kenmerk dat u zoekt niet aanwezig is.

      * **Standaardwaarde** – Optioneel. Als het bronkenmerk een lege waarde heeft, wordt deze waarde in de toewijzing geschreven.
            Meest voorkomende configuratie is om deze leeg te laten.

      * **Kenmerk Doel** : het gebruikerskenmerk in Active Directory.

      * **Objecten matchen met dit kenmerk** : of deze toewijzing al dan niet moet worden gebruikt om gebruikers op unieke wijze te identificeren tussen Workday en Active Directory. Deze waarde wordt meestal ingesteld op het veld Werknemers-id voor Werkdag, dat doorgaans is toegewezen aan een van de kenmerken van werknemers-id in Active Directory.

      * **Overeenkomende prioriteit** – Meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden ze geëvalueerd in de volgorde die door dit veld is gedefinieerd. Zodra een overeenkomst wordt gevonden, worden geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing toepassen**

         * **Altijd** – Deze toewijzing toepassen op zowel acties voor het maken en bijwerken van gebruikers

         * **Alleen tijdens het maken** - Pas deze toewijzing alleen toe op acties voor het maken van gebruikers

1. Als u uw toewijzingen wilt opslaan, klikt u boven aan de sectie Kenmerktoewijzing op **Opslaan.**

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Hieronder vindt u enkele voorbeeldtoewijzingen tussen Workday en Active Directory, met enkele veelvoorkomende expressies

* De expressie die wordt toegewezen aan het kenmerk *ParentDistinguishedName,* wordt gebruikt om een gebruiker in te richten op verschillende OK's op basis van een of meer werkdagenbronkenmerken. Dit voorbeeld plaatst gebruikers in verschillende OE's op basis van in welke stad ze zich bevinden.

* Het kenmerk *userPrincipalName* in Active Directory wordt gegenereerd met de deduplicatiefunctie [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) die controleert op het bestaan van een gegenereerde waarde in het doel-AD-domein en deze alleen instelt als deze uniek is.  

* [Er is documentatie over het schrijven van uitdrukkingen hier](../app-provisioning/functions-for-customizing-application-data.md). In deze sectie worden voorbeelden gegeven over het verwijderen van speciale tekens.

| KENMERK WERKDAG | ACTIVE DIRECTORY-KENMERK |  BIJPASSENDE ID? | MAKEN / BIJWERKEN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Geschreven op alleen maken |
| **PreferredNameData**    |  Cn    |   |   Geschreven op alleen maken |
| **SelectUniqueValue( Join"\@" " Join".", \[voornaam\] \[, achternaam\]),\@"contoso.com"), Join("\[",\]Join(".", Mid( voornaam , \[1, achternaam\]), "contoso.com"), Join(" \[\]\@", Join(".", Mid(\[voornaam\], 1, 2), achternaam ), "contoso.com"))**   | userPrincipalName     |     | Geschreven op alleen maken 
| **Vervangen(Mid(Replace(\[\]UserID ,\[\\\\/\\\\\\\\\\\\\[\\\\"(\]\\:\\;\\ \\\\ \\\|\\\\=\\\\,\\\\+\\\\\*\\\\? \\\\\\)", , "", , , 1, 20), "([ .) \\ &lt; \\ \\ &gt; \] \*](file:///\\ \$.) *$)", , "", , )**      |    sAMAccountName            |     |         Geschreven op alleen maken |
| **Switch(\[\]Actief , "0", "Waar", "1", "False")** |  accountUitgeschakeld      |     | Maken + bijwerken |
| **Voornaam**   | givenName       |     |    Maken + bijwerken |
| **Achternaam**   |   sn   |     |  Maken + bijwerken |
| **PreferredNameData**  |  displayName |     |   Maken + bijwerken |
| **Bedrijf**         | bedrijf   |     |  Maken + bijwerken |
| **ToezichtOrganisatie**  | department  |     |  Maken + bijwerken |
| **ManagerReferentie**   | manager  |     |  Maken + bijwerken |
| **BusinessTitle**   |  titel     |     |  Maken + bijwerken | 
| **AddressLineData**    |  streetAddress  |     |   Maken + bijwerken |
| **Gemeente**   |   l   |     | Maken + bijwerken |
| **LandReferenceTwoLetter**      |   Co |     |   Maken + bijwerken |
| **LandReferenceTwoLetter**    |  c  |     |         Maken + bijwerken |
| **CountryRegionReference** |  st     |     | Maken + bijwerken |
| **WerkruimteReferentie** | physicalDeliveryOfficeName    |     |  Maken + bijwerken |
| **Postcode**  |   Postcode  |     | Maken + bijwerken |
| **PrimaryWorkTelefoon**  |  telefoonNummer   |     | Maken + bijwerken |
| **Fax**      | facsimileTelefoonnummer     |     |    Maken + bijwerken |
| **Mobiel**  |    mobiel       |     |       Maken + bijwerken |
| **LokaalReferentie** |  voorkeurTaal  |     |  Maken + bijwerken |                                               
| **Switch(\[\]Gemeente , "OU=Standaardgebruikers,OU=Gebruikers,OU=Standaard,OU=Locaties,DC=contoso,DC=com", "Dallas", "OU=Standaardgebruikers,OU=Gebruikers,OU=Dallas,OU=Locaties,DC=contoso,DC=com", "Austin", "OU=Standaardgebruikers, OU=Gebruikers,OU=Austin,OU=Austin,OU=Locaties,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Ou=Seattle,OU=Locations,DC=dc=dc=com", "London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | bovenliggendeDistinguishedName     |     |  Maken + bijwerken |

Zodra de configuratie van de toewijzing van kenmerken is voltooid, u [nu de service voor het inrichten van de gebruiker inschakelen en starten.](#enable-and-launch-user-provisioning)

## <a name="configuring-user-provisioning-to-azure-ad"></a>Gebruikersvoorziening configureren voor Azure AD

In de volgende secties worden stappen beschreven voor het configureren van gebruikersinrichting van Workday naar Azure AD voor implementaties met alleen de cloud.

* [De Azure AD-inrichtingsconnector-app toevoegen en de verbinding met Workday maken](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Toewijzingen van Workday- en Azure AD-kenmerken configureren](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Volg de onderstaande procedure alleen als u alleen cloudgebruikers hebt die moeten worden ingericht op Azure AD en niet on-premises Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: De app Azure AD-inrichting toevoegen en de verbinding met Workday maken

**Ga als u Workday configureert voor Azure Active Directory provisioning voor alleen cloudgebruikers:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer **Azure Active Directory**in de Azure-portal.

3. Selecteer **Enterprise-toepassingen**en **vervolgens Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen**en selecteer de categorie **Alles.**

5. Zoek **naar Workday naar Azure AD-inrichting**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met app-details wordt weergegeven, selecteert u **Inrichten**.

7. Wijzig de **inrichtingsmodus** **Mode** in **Automatisch**.

8. Vul de sectie **Beheerdersreferenties** als volgt in:

   * **Gebruikersnaam op werkdagen** : voer de gebruikersnaam van het Workday-integratiesysteemaccount in, waarbij de tenantdomeinnaam is toegevoegd. Moet er ongeveer uitzien als:username@contoso4

   * **Workday-wachtwoord –** Voer het wachtwoord in van het Workday-integratiesysteemaccount

   * **URL van de Workday Web Services API –** Voer de URL in naar het eindpunt van de Workday-webservices voor uw tenant. Deze waarde moet https://wd3-impl-services1.workday.com/ccx/service/contoso4eruit zien als: , waar *contoso4* wordt vervangen door uw juiste tenant naam en *wd3-impl* wordt vervangen door de juiste omgevingstekenreeks. Als deze URL niet bekend is, werkt u samen met uw Workday-integratiepartner of ondersteuningsmedewerker om de juiste URL te bepalen die u wilt gebruiken.

     > [!NOTE]
     > Standaard gebruikt de app Workday Web Services v21.1 als er geen versiegegevens zijn opgegeven in de URL. Als u een specifieke Workday Web Services API-versie wilt gebruiken, gebruikt u de URL-indeling:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Voorbeeld: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0


   * **Melding e-mail –** Voer uw e-mailadres in en schakel het selectievakje 'E-mail verzenden als er mislukt' in.

   * Klik op de knop **Verbinding testen.**

   * Als de verbindingstest slaagt, klikt u bovenaan op de knop **Opslaan.** Als dit niet lukt, controleert u of de URL van Workday en de referenties geldig zijn in Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Deel 2: Toewijzingen van Workday- en Azure AD-kenmerken configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van Workday naar Azure Active Directory voor alleen cloudgebruikers.

1. Klik op het tabblad Inrichten onder **Toewijzingen**op **Werknemers synchroniseren met Azure AD**.

2. In het veld **Bronobjectbereik** u selecteren welke sets gebruikers in Workday in het bereik moeten zijn voor de inrichting van Azure AD, door een set op kenmerken gebaseerde filters te definiëren. Het standaardbereik is "alle gebruikers in Workday". Voorbeeldfilters:

   * Voorbeeld: Bereik voor gebruikers met werknemers-i-d's tussen 1000000 en 2000000

      * Kenmerk: WorkerID

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen voorwaardelijke werknemers en geen gewone werknemers

      * Kenmerk: ContingentID

      * Operator: IS NIET NULL

3. In het veld **Doelobjectacties** u globaal filteren welke acties worden uitgevoerd op Azure AD. **Maken** en **bijwerken** komen het meest voor.

4. In de sectie **Toewijzingen van kenmerken** u definiëren hoe afzonderlijke workday-kenmerken worden toegewezen aan Active Directory-kenmerken.

5. Klik op een bestaande kenmerktoewijzing om deze bij te werken of klik op **Nieuwe toewijzing** onder aan het scherm toevoegen om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt deze eigenschappen:

   * **Toewijzingstype**

      * **Direct** : hiermee schrijft u de waarde van het kenmerk Werkdag aan het AD-kenmerk, zonder wijzigingen

      * **Constant** - Schrijf een statische, constante tekenreekswaarde naar het AD-kenmerk

      * **Expressie** : hiermee u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer kenmerken van Werkdag. [Zie dit artikel over expressies voor meer informatie.](../app-provisioning/functions-for-customizing-application-data.md)

   * **Bronkenmerk** - Het gebruikerskenmerk van Workday. Zie De lijst met [gebruikerskenmerken van Workday](#customizing-the-list-of-workday-user-attributes)aanpassen als het kenmerk dat u zoekt niet aanwezig is.

   * **Standaardwaarde** – Optioneel. Als het bronkenmerk een lege waarde heeft, wordt deze waarde in de toewijzing geschreven.
            Meest voorkomende configuratie is om deze leeg te laten.

   * **Kenmerk Target** : het gebruikerskenmerk in Azure AD.

   * **Objecten overeenkomen met dit kenmerk** : of dit kenmerk al dan niet moet worden gebruikt om gebruikers op unieke wijze te identificeren tussen Workday en Azure AD. Deze waarde wordt meestal ingesteld op het veld Werknemers-id voor Werkdag, dat doorgaans is toegewezen aan het kenmerk Werknemers-id (nieuw) of een extensiekenmerk in Azure AD.

   * **Overeenkomende prioriteit** – Meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden ze geëvalueerd in de volgorde die door dit veld is gedefinieerd. Zodra een overeenkomst wordt gevonden, worden geen verdere overeenkomende kenmerken geëvalueerd.

   * **Deze toewijzing toepassen**

     * **Altijd** – Deze toewijzing toepassen op zowel acties voor het maken en bijwerken van gebruikers

     * **Alleen tijdens het maken** - Pas deze toewijzing alleen toe op acties voor het maken van gebruikers

6. Als u uw toewijzingen wilt opslaan, klikt u boven aan de sectie Kenmerktoewijzing op **Opslaan.**

Zodra de configuratie van de toewijzing van kenmerken is voltooid, u [nu de service voor het inrichten van de gebruiker inschakelen en starten.](#enable-and-launch-user-provisioning)

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Azure AD-attribuut terugschrijven naar Werkdag configureren

Volg deze instructies om het terugschrijven van e-mailadressen van gebruikers en de gebruikersnaam te configureren van Azure Active Directory naar Workday.

* [De app Voor terugschrijven-connector toevoegen en de verbinding met Workday maken](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Toewijzingen van schrijfschrijfkenmerken configureren](#part-2-configure-writeback-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: De app Voor terugschrijfconnector toevoegen en de verbinding met Workday maken

**Ga als een workday Writeback-connector als gevolg van het configureren van een Workday Writeback-connector:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer **Azure Active Directory**in de Azure-portal.

3. Selecteer **Enterprise-toepassingen**en **vervolgens Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen**en selecteer vervolgens de categorie **Alles.**

5. Zoek naar **Workday Writeback**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met app-details wordt weergegeven, selecteert u **Inrichten**.

7. Wijzig de **inrichtingsmodus** **Mode** in **Automatisch**.

8. Vul de sectie **Beheerdersreferenties** als volgt in:

   * **Gebruikersnaam beheerder** : voer de gebruikersnaam in van het Workday-integratiesysteemaccount, waarbij de tenantdomeinnaam is toegevoegd. Moet er iets uitzien als: *gebruikersnaam\@contoso4*

   * **Beheerderswachtwoord –** Voer het wachtwoord in van het Workday-integratiesysteemaccount

   * **URL van tenant –** Voer de URL in naar het eindpunt van de Workday-webservices voor uw tenant. Deze waarde moet https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourceseruit zien als: , waar *contoso4* wordt vervangen door uw juiste tenant naam en *wd3-impl* wordt vervangen door de juiste omgevingstekenreeks (indien nodig).

   * **Melding e-mail –** Voer uw e-mailadres in en schakel het selectievakje 'E-mail verzenden als er mislukt' in.

   * Klik op de knop **Verbinding testen.** Als de verbindingstest slaagt, klikt u bovenaan op de knop **Opslaan.** Als dit niet lukt, controleert u of de URL van Workday en de referenties geldig zijn in Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Deel 2: Toewijzingen van schrijfschrijfkenmerken configureren

In deze sectie configureert u hoe terugschrijfkenmerken van Azure AD naar Workday stromen. Op dit moment ondersteunt de connector alleen het terugschrijven van e-mailadres en gebruikersnaam naar Workday.

1. Klik op het tabblad Inrichten onder **Toewijzingen**op **Azure Active Directory Users synchroniseren met werkdag**.

2. In het veld **Bronobjectbereik** u optioneel filteren, welke sets gebruikers in Azure Active Directory hun e-mailadressen moeten hebben teruggeschreven naar Workday. De standaardscope is 'alle gebruikers in Azure AD'.

3. Werk in de sectie **Toewijzingen van kenmerken** de overeenkomende id bij om het kenmerk aan te geven in Azure Active Directory waar de werkdagwerk-id of werknemers-id is opgeslagen. Een populaire matchingmethode is het synchroniseren van de Werkdag-werk-id of werknemers-id naar extensieAttribuut1-15 in Azure AD en vervolgens dit kenmerk in Azure AD gebruiken om gebruikers terug te koppelen in Workday.

4. Meestal wijst u het kenmerk Azure AD *userPrincipalName* toe aan het kenmerk Workday *UserID* en brengt u het azure *AD-e-mailkenmerk* in kaart met het kenmerk *Werkdag-e-mailadres.* Als u uw toewijzingen wilt opslaan, klikt u boven aan de sectie Kenmerktoewijzing op **Opslaan.**

Zodra de configuratie van de toewijzing van kenmerken is voltooid, u [nu de service voor het inrichten van de gebruiker inschakelen en starten.](#enable-and-launch-user-provisioning)

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de configuratie van de Workday-inrichtingsapp is voltooid, u de inrichtingsservice inschakelen in de Azure-portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, wordt standaard de inrichtingsbewerking gestart voor alle gebruikers in het bereik. Als er fouten zijn in de problemen met de toewijzing of workday-gegevens, kan de inrichtingstaak mislukken en in de quarantainestatus gaan. Om dit te voorkomen, raden we u aan het filter **Source Object Scope** te configureren en uw kenmerktoewijzingen te testen met een paar testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Zodra u hebt geverifieerd dat de toewijzingen werken en geven u de gewenste resultaten, dan u ofwel verwijderen van het filter of geleidelijk uit te breiden naar meer gebruikers op te nemen.

1. Stel op het tabblad **Inrichten** de **inrichtingsstatus** in **op Aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, die een variabel aantal uren kan duren, afhankelijk van het aantal gebruikers dat zich in de workday-tenant bevindt. 

4. Controleer op elk gewenst moment het tabblad **Controlelogboeken** in de Azure-portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. De controlelogboeken bevatten alle afzonderlijke synchronisatiegebeurtenissen die door de inrichtingsservice worden uitgevoerd, zoals welke gebruikers buiten Workday worden voorgelezen en vervolgens worden toegevoegd of bijgewerkt naar Active Directory. Raadpleeg de sectie Probleemoplossing voor instructies over het controleren van de controlelogboeken en het oplossen van inrichtingsfouten.

5. Zodra de eerste synchronisatie is voltooid, wordt een controleoverzichtrapport geschreven op het tabblad **Inname,** zoals hieronder wordt weergegeven.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

* **Vragen over oplossingsmogelijkheden**
  * [Hoe stelt de oplossing bij het verwerken van een nieuwe medewerker van Workday het wachtwoord in voor het nieuwe gebruikersaccount in Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Ondersteunt de oplossing het verzenden van e-mailmeldingen nadat provisioning-bewerkingen zijn voltooid?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hoe beheer ik de levering van wachtwoorden voor nieuwe werknemers en zorg ik veilig voor een mechanisme om hun wachtwoord opnieuw in te stellen?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Worden de gebruikersprofielen van Workday in de cache van de oplossing opgeslagen in de Azure AD-cloud of op de lijst met inrichtingsagents?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Ondersteunt de oplossing het toewijzen van on-premises AD-groepen aan de gebruiker?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Welke Workday API's gebruikt de oplossing om werkdagwerkprofielen op te vragen en bij te werken?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kan ik mijn Workday HCM-tenant configureren met twee Azure AD-tenant's?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Waarom de gebruikersinrichtingsapp 'Workday to Azure AD' niet wordt ondersteund als we Azure AD Connect hebben geïmplementeerd?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hoe stel ik verbeteringen voor of vraag ik nieuwe functies aan met betrekking tot Workday- en Azure AD-integratie?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Vragen van provisioning agent**
  * [Wat is de GA-versie van de provisioning agent?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hoe ken ik de versie van mijn inprovisioning agent?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Duwt Microsoft automatisch Provisioning Agent-updates?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kan ik de inrichtingsagent installeren op dezelfde server met Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hoe configureer ik de inrichtingsagent om een proxyserver te gebruiken voor uitgaande HTTP-communicatie?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hoe zorg ik ervoor dat de provisioning agent in staat is om te communiceren met de Azure AD-tenant en geen firewalls blokkeren poorten die vereist zijn door de agent?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hoe kan ik het domein dat is gekoppeld aan mijn inprovisioning agent de-registreren?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hoe verwijder ik de provisioning agent?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Kaart- en configuratievragen van werkdag tot AD-kenmerk**
  * [Hoe maak ik een back-up of exporteer ik een werkkopie van mijn Workday Provisioning Attribute Mapping and Schema?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Ik heb aangepaste kenmerken in Workday en Active Directory. Hoe configureer ik de oplossing om te werken met mijn aangepaste kenmerken?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kan ik de foto van de gebruiker inrichten van Workday naar Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hoe synchroniseer ik mobiele nummers van Workday op basis van toestemming van de gebruiker voor openbaar gebruik?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hoe maak ik weergavenamen op in AD op basis van de kenmerken van de gebruiker/land/stad en verwerkt ik regionale varianties?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hoe kan ik SelectUniqueValue gebruiken om unieke waarden te genereren voor het kenmerk samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hoe verwijder ik tekens met diacritics en zet ze om in normale Engelse alfabetten?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Vragen over oplossingsmogelijkheden

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Hoe stelt de oplossing bij het verwerken van een nieuwe medewerker van Workday het wachtwoord in voor het nieuwe gebruikersaccount in Active Directory?

Wanneer de on-premises inrichtingsagent een verzoek krijgt om een nieuw AD-account te maken, genereert deze automatisch een complex willekeurig wachtwoord dat is ontworpen om te voldoen aan de vereisten voor de complexiteit van wachtwoorden die door de AD-server zijn gedefinieerd en wordt dit ingesteld op het gebruikersobject. Dit wachtwoord wordt nergens geregistreerd.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Ondersteunt de oplossing het verzenden van e-mailmeldingen nadat provisioning-bewerkingen zijn voltooid?

Nee, het verzenden van e-mailmeldingen na het voltooien van de inrichtingsbewerkingen wordt niet ondersteund in de huidige versie.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hoe beheer ik de levering van wachtwoorden voor nieuwe werknemers en zorg ik veilig voor een mechanisme om hun wachtwoord opnieuw in te stellen?

Een van de laatste stappen die betrokken zijn bij de nieuwe AD-account inrichting is de levering van het tijdelijke wachtwoord toegewezen aan het AD-account van de gebruiker. Veel ondernemingen gebruiken nog steeds de traditionele benadering van het leveren van het tijdelijke wachtwoord aan de manager van de gebruiker, die vervolgens het wachtwoord overhandigt aan de nieuwe werknemer in huur/voorwaardelijke werknemer. Dit proces heeft een inherent beveiligingslek en er is een optie beschikbaar om een betere aanpak te implementeren met behulp van Azure AD-mogelijkheden.

Als onderdeel van het wervingsproces voeren HR-teams meestal een achtergrondcontrole uit en doorlichten ze het mobiele nummer van de nieuwe inhuur. Met de integratie van Workday to AD User Provisioning u op de top van dit feit voortbouwen en op dag 1 een selfservice-wachtwoordresetmogelijkheid voor de gebruiker uitrollen. Dit wordt bereikt door het kenmerk 'Mobiel nummer' van de nieuwe medewerker van Workday naar AD en vervolgens van AD naar Azure AD te propageren met Azure AD Connect. Zodra het 'mobiele nummer' aanwezig is in Azure AD, u de [Self-Service Password Reset (SSPR)](../authentication/howto-sspr-authenticationdata.md) inschakelen voor het account van de gebruiker, zodat een nieuwe medewerker op dag 1 het geregistreerde en geverifieerde mobiele nummer kan gebruiken voor verificatie.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Worden de gebruikersprofielen van Workday in de cache van de oplossing opgeslagen in de Azure AD-cloud of op de lijst met inrichtingsagents?

Nee, de oplossing onderhoudt geen cache met gebruikersprofielen. De Azure AD-inrichtingsservice fungeert gewoon als een gegevensverwerker, het lezen van gegevens van Workday en het schrijven naar de beoogde Active Directory of Azure AD. Zie de sectie [Beheren van persoonsgegevens](#managing-personal-data) voor gegevens met betrekking tot de privacy van gebruikers en het bewaren van gegevens.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Ondersteunt de oplossing het toewijzen van on-premises AD-groepen aan de gebruiker?

Deze functionaliteit wordt momenteel niet ondersteund. Aanbevolen tijdelijke oplossing is het implementeren van een PowerShell-script dat het Microsoft Graph API-eindpunt opvraagt voor [controlelogboekgegevens](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) en deze gebruikt om scenario's zoals groepstoewijzing te activeren. Dit PowerShell-script kan worden gekoppeld aan een taakplanner en worden geïmplementeerd in hetzelfde vak met de inrichtingsagent.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Welke Workday API's gebruikt de oplossing om werkdagwerkprofielen op te vragen en bij te werken?

De oplossing maakt momenteel gebruik van de volgende Workday API's:

* De **URL-indeling van** de Workday Web Services API die wordt gebruikt in de sectie **Beheerdersreferenties,** bepaalt de API-versie die wordt gebruikt voor Get_Workers
  * Als de URL-indeling\#\#\#\#\.is: https:// werkdag\.com/ccx/service/tenantName, wordt API v21.1 gebruikt. 
  * Als de URL-indeling\#\#\#\#\.is: https:// werkdag\.com/ccx/service/tenantName/Human\_Resources, wordt API v21.1 gebruikt 
  * Als de URL-indeling\#\#\#\#\.is: https:// werkdag\.com/ccx/service/tenantName/Human\_Resources/v,\# \# \. \# wordt de opgegeven API-versie gebruikt. (Voorbeeld: als v34.0 is opgegeven, wordt het gebruikt.)  
   
* Workday E-mail terugschrijven functie maakt gebruik van Maintain_Contact_Information (v26.1) 
* Workday Gebruikersnaam Writeback-functie gebruikt Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kan ik mijn Workday HCM-tenant configureren met twee Azure AD-tenant's?

Ja, deze configuratie wordt ondersteund. Dit zijn de stappen op hoog niveau om dit scenario te configureren:

* Implementeer #1 en registreer deze bij azure AD-tenant #1.
* Implementeer #2 en registreer deze bij azure AD-tenant #2.
* Op basis van de 'Onderliggende domeinen' die elke provisioning agent beheert, configureert u elke agent met het domein(en). Eén agent kan meerdere domeinen verwerken.
* Stel in Azure-portal de Workday-app voor AD-gebruikersinrichting in elke tenant in en configureer deze met de respectievelijke domeinen.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Waarom de gebruikersinrichtingsapp 'Workday to Azure AD' niet wordt ondersteund als we Azure AD Connect hebben geïmplementeerd?

Wanneer Azure AD wordt gebruikt in de hybride modus (waar het een mix van cloud + on-premises gebruikers bevat), is het belangrijk om een duidelijke definitie van "bron van autoriteit" te hebben. Doorgaans vereisen hybride scenario's implementatie van Azure AD Connect. Wanneer Azure AD Connect wordt geïmplementeerd, is on-premises AD de bron van autoriteit. Als u de Workday introduceert in de Azure AD-connector in de mix, kan dit leiden tot een situatie waarin kenmerkenwaarden van Workday mogelijk de waarden kunnen overschrijven die zijn ingesteld door Azure AD Connect. Daarom wordt het gebruik van de inrichtingsapp 'Workday to Azure AD' niet ondersteund wanneer Azure AD Connect is ingeschakeld. In dergelijke situaties raden we u aan de provisioning-app 'Workday to AD-gebruiker' te gebruiken om gebruikers in on-premises AD te krijgen en deze vervolgens te synchroniseren met Azure AD met Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hoe stel ik verbeteringen voor of vraag ik nieuwe functies aan met betrekking tot Workday- en Azure AD-integratie?

Uw feedback wordt zeer gewaardeerd omdat het ons helpt de richting te bepalen voor de toekomstige releases en verbeteringen. We zijn blij met alle feedback en moedigen u aan om uw idee of verbeteringssuggestie in te dienen in het [feedbackforum van Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory) Selecteer de categorie *SaaS-toepassingen* en zoek met de trefwoorden *Workday* naar bestaande feedback met betrekking tot de Werkdag voor specifieke feedback met betrekking tot de Workday-integratie.

![UserVoice SaaS-apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Wanneer u een nieuw idee voorstelt, controleert u of iemand anders al een soortgelijke functie heeft voorgesteld. In dat geval u de functie of het verbeteringsverzoek omhoog stemmen. U ook een opmerking achterlaten over uw specifieke use case om uw steun voor het idee te tonen en te laten zien hoe de functie ook voor u waardevol zal zijn.

### <a name="provisioning-agent-questions"></a>Vragen van provisioning agent

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wat is de GA-versie van de provisioning agent?

* De GA-versie van de provisioning agent is 1.1.30 en hoger.
* Als uw agentversie minder dan 1.1.30 is, voert u de openbare preview-versie uit en wordt deze automatisch bijgewerkt naar de GA-versie als de server die de agent host .NET 4.7.1 runtime heeft.
  * U [de .NET-versie controleren](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) die op uw server is geïnstalleerd. Als de server geen .NET 4.7.1 draait, u [.NET 4.7.1 downloaden en installeren.](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows) Uw inrichtingsagent wordt automatisch bijgewerkt naar de GA-versie nadat u .NET 4.7.1 hebt geïnstalleerd.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hoe ken ik de versie van mijn inprovisioning agent?

* Meld u aan bij de Windows-server waar de inrichtingsagent is geïnstalleerd.
* Ga naar **configuratiescherm Een** -> menu**verwijderen of Een programma wijzigen**
* Zoek naar de versie die overeenkomt met de vermelding **Microsoft Azure AD Connect Provisioning Agent**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Duwt Microsoft automatisch Provisioning Agent-updates?

Ja, Microsoft werkt de inrichtingsagent automatisch bij. U automatische updates uitschakelen door de Windows-service **Microsoft Azure AD Connect Agent Updater te**stoppen.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Kan ik de inrichtingsagent installeren op dezelfde server met Azure AD Connect?

Ja, u de inrichtingsagent installeren op dezelfde server waarop Azure AD Connect wordt uitgevoerd.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Op het moment van configuratie vraagt de inrichtingsagent om azure AD-beheerdersreferenties. Slaat de agent de referenties lokaal op de server op?

Tijdens de configuratie vraagt de inrichtingsagent om Azure AD-beheerdersreferenties die alleen verbinding maken met uw Azure AD-tenant. De referenties worden niet lokaal op de server opgeslagen. Het behoudt echter wel de referenties die worden gebruikt om verbinding te maken met het *on-premises Active Directory-domein* in een lokale Windows-wachtwoordkluis.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hoe configureer ik de inrichtingsagent om een proxyserver te gebruiken voor uitgaande HTTP-communicatie?

De provisioningagent ondersteunt het gebruik van uitgaande proxy. U het configureren door het agentconfig-bestand **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**te bewerken. Voeg de volgende regels toe, tegen het einde `</configuration>` van het bestand vlak voor de afsluitende tag.
Vervang de variabelen [proxy-server] en [proxy-poort] door de naam en poortwaarden van uw proxyserver.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hoe zorg ik ervoor dat de provisioning agent in staat is om te communiceren met de Azure AD-tenant en geen firewalls blokkeren poorten die vereist zijn door de agent?

U ook controleren of u alle vereiste poorten hebt geopend door de [Connector Ports Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) te openen vanuit uw on-premises netwerk. Meer groene vinkjes betekent meer veerkracht.

Om ervoor te zorgen dat de tool u de juiste resultaten geeft, moet u:

* Open het hulpprogramma in een browser vanaf de server waar u de provisioning agent hebt geïnstalleerd.
* Zorg ervoor dat alle proxy's of firewalls die van toepassing zijn op uw inrichtingsagent ook op deze pagina worden toegepast. Dit kan in Internet Explorer door naar **Instellingen -> Internet Options -> Connections -> LAN-instellingen**te gaan. Op deze pagina ziet u het veld "Gebruik een proxyserver voor uw LAN". Selecteer dit vak en plaats het proxyadres in het veld Adres.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan één inrichtingsagent worden geconfigureerd om meerdere AD-domeinen in te richten?

Ja, één provisioningagent kan worden geconfigureerd om meerdere AD-domeinen te verwerken, zolang de agent zichtlijn heeft met de desbetreffende domeincontrollers. Microsoft raadt aan een groep van 3 inrichtingsagents in te stellen die dezelfde set AD-domeinen bedienen om een hoge beschikbaarheid te garanderen en ondersteuning te bieden.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hoe kan ik het domein dat is gekoppeld aan mijn inprovisioning agent de-registreren?

* Haal vanuit de Azure-portal de *tenant-id* van uw Azure AD-tenant op.
* Meld u aan bij de Windows-server waarop de inrichtingsagent wordt uitgevoerd.
* Open PowerShell als Windows-beheerder.
* Wijzig in de map met de registratiescripts en voer \[de\] volgende opdrachten uit die de parameter tenant-id vervangen door de waarde van uw tenant-id.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Kopieer vanuit de lijst met agents die `id` worden weergegeven: kopieer de waarde van het veld uit die bron waarvan *de resourceName* gelijk is aan uw AD-domeinnaam.
* Plak de ID-waarde in deze opdracht en voer de opdracht uit in PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* De wizard Agentconfiguratie opnieuw uitvoeren.
* Alle andere agents die eerder aan dit domein zijn toegewezen, moeten opnieuw worden geconfigureerd.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hoe verwijder ik de provisioning agent?

* Meld u aan bij de Windows-server waar de inrichtingsagent is geïnstalleerd.
* Ga naar **configuratiescherm Een** -> menu**verwijderen of Een programma wijzigen**
* Verwijder de volgende programma's:
  * Microsoft Azure AD Connect Provisioning Agent
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect Provisioning Agent-pakket

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Kaart- en configuratievragen van werkdag tot AD-kenmerk

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hoe maak ik een back-up of exporteer ik een werkkopie van mijn Workday Provisioning Attribute Mapping and Schema?

U de Microsoft Graph API gebruiken om uw configuratie voor het inrichten van workday-gebruikers te exporteren. Raadpleeg de stappen in de sectie Exporteren en importeren van de configuratie Voor het [inrichten van kenmerktoewijzing](#exporting-and-importing-your-configuration) voor werkdagen voor meer informatie.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Ik heb aangepaste kenmerken in Workday en Active Directory. Hoe configureer ik de oplossing om te werken met mijn aangepaste kenmerken?

De oplossing ondersteunt aangepaste Workday- en Active Directory-kenmerken. Als u uw aangepaste kenmerken wilt toevoegen aan het toewijzingsschema, opent u het blad **Voortoewijzing** en schuift u omlaag om de sectie **Geavanceerde opties weergeven**uit te vouwen . 

![Kenmerklijst bewerken](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Als u uw aangepaste Workday-kenmerken wilt toevoegen, selecteert u de *lijst met kenmerkopties* bewerken voor Workday en selecteert u de lijst met aangepaste AD-kenmerken, selecteert u de lijst met kenmerkopties bewerken voor Active Directory voor *onlocatie*.

Zie ook:

* [De lijst met gebruikerskenmerken van Workday aanpassen](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hoe configureer ik de oplossing om alleen kenmerken in AD bij te werken op basis van wijzigingen op werkdagen en geen nieuwe AD-accounts te maken?

Deze configuratie kan worden bereikt door de **doelobjectacties** in te stellen in het blad **Kenmerkentoewijzingen** zoals hieronder wordt weergegeven:

![Actie bijwerken](./media/workday-inbound-tutorial/wd_target_update_only.png)

Schakel het selectievakje 'Bijwerken' in om alleen bewerkingen bij te werken die van Werkdag naar AD kunnen stromen. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kan ik de foto van de gebruiker inrichten van Workday naar Active Directory?

De oplossing biedt momenteel geen ondersteuning voor het instellen van binaire kenmerken zoals *thumbnailPhoto* en *jpegPhoto* in Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hoe synchroniseer ik mobiele nummers van Workday op basis van toestemming van de gebruiker voor openbaar gebruik?

* Ga naar het 'Provisioning' mes van uw Workday Provisioning App.
* Klik op de attribuuttoewijzingen 
* Selecteer **Onder Toewijzingen**de optie **Werkdagwerkers synchroniseren met Active Directory voor on-premises** (of **Werkdagwerknemers synchroniseren met Azure AD**).
* Schuif op de pagina Toewijzingen van kenmerken naar beneden en schakel het selectievakje 'Geavanceerde opties weergeven' in.  Klik op **De lijst met kenmerk bewerken voor Werkdag**
* Zoek in het blad dat wordt geopend het kenmerk 'Mobiel' en klik op de rij zodat u de **mobiele GDPR van API Expression** ![bewerken](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Vervang de **API-expressie** door de volgende nieuwe expressie, die het mobiele werknummer alleen ophaalt als de vlag 'Openbaar gebruik' is ingesteld op 'True' in Werkdag.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Sla de lijst met kenmerken op.
* Sla de toewijzing van kenmerken op.
* De huidige status wissen en de volledige synchronisatie opnieuw starten.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hoe maak ik weergavenamen op in AD op basis van de kenmerken van de gebruiker/land/stad en verwerkt ik regionale varianties?

Het is een veelvoorkomende vereiste om het *kenmerk displayName* in AD zo te configureren dat het ook informatie geeft over de afdeling en het land/regio van de gebruiker. Bijvoorbeeld als John Smith werkt in de Marketing Department in de VS, wilt u misschien dat zijn *displayName* wordt weergegeven als *Smith, John (Marketing-US).*

Hier vindt u hoe u met dergelijke vereisten voor het maken van *CN* of *displayName* omgaan met kenmerken zoals bedrijf, business unit, stad of land/regio.

* Elk kenmerk van de werkdag wordt opgehaald met een onderliggende XPATH API-expressie, die kan worden geconfigureerd in **Kenmerktoewijzing -> Advanced Section -> Lijst met kenmerk bewerken voor Werkdag**. Hier vindt u de standaard XPATH API-expressie voor kenmerken Van Workday *PreferredFirstName*, *PreferredLastName,* *Bedrijfs-* en *Toezichtorganisatie.*

     | Kenmerk Werkdag | API XPATH-expressie |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Bedrijf | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | ToezichtOrganisatie | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Bevestig met uw Workday-team dat de bovenstaande API-expressie geldig is voor de tenantconfiguratie van Workday. Indien nodig u ze bewerken zoals beschreven in de sectie [Het aanpassen van de lijst met gebruikerskenmerken van Workday.](#customizing-the-list-of-workday-user-attributes)

* Ook de landinformatie die in Workday aanwezig is, wordt opgehaald met de volgende XPATH: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Er zijn 5 landgerelateerde kenmerken die beschikbaar zijn in de sectie Lijst met kenmerken van Workday.

     | Kenmerk Werkdag | API XPATH-expressie |
     | ----------------- | -------------------- |
     | LandReferentie | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeriek | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | LandReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Bevestig met uw Workday-team dat de bovenstaande API-expressies geldig zijn voor de tenantconfiguratie van Workday. Indien nodig u ze bewerken zoals beschreven in de sectie [Het aanpassen van de lijst met gebruikerskenmerken van Workday.](#customizing-the-list-of-workday-user-attributes)

* Als u de juiste kenmerktoewijzingsexpressie wilt maken, geeft u aan welk kenmerk Workday "gezaghebbend" de voornaam, achternaam, land/regio en afdeling van de gebruiker vertegenwoordigt. Stel dat de kenmerken respectievelijk *PreferredFirstName,* *PreferredLastName,* *CountryReferenceTwoLetter* en *SupervisoryOrganization* zijn. U dit gebruiken om een expressie voor het kenmerk AD *displayName* als volgt te maken om een weergavenaam als *Smith, John (Marketing-US)* te krijgen.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Zodra u de juiste expressie hebt, bewerkt u de tabel Toewijzingen van ![kenmerken en wijzigt u de toewijzing van het *kenmerk displayName* zoals hieronder weergegeven: DisplayName-toewijzing](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Als u het bovenstaande voorbeeld uitbreidt, stel dat u de naam van de stad van Workday wilt omzetten in stenowaarden en deze vervolgens wilt gebruiken om weergavenamen zoals *Smith, John (CHI)* of *Doe, Jane (NYC)* te bouwen, dan kan dit resultaat worden bereikt met behulp van een Switch-expressie met het kenmerk Workday *Municipality* als determinantvariabele.

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
  * [Syntaxis van de functie Schakelen](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Syntaxis van de functie](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Syntaxis van de functie Toevoegen](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hoe kan ik SelectUniqueValue gebruiken om unieke waarden te genereren voor het kenmerk samAccountName?

Stel dat u unieke waarden wilt genereren voor het kenmerk *samAccountName* met behulp van een combinatie van *voornaam-* en *achternaamkenmerken* van Workday. Hieronder ziet u een uitdrukking waar u mee beginnen:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Hoe de bovenstaande expressie werkt: Als de gebruiker Is John Smith, het eerst probeert te genereren JSmith, als JSmith al bestaat, dan genereert JoSmith, als dat bestaat, genereert het JohSmith. De expressie zorgt er ook voor dat de gegenereerde waarde voldoet aan de lengtebeperking en speciale tekensbeperking die is gekoppeld aan *samAccountName*.

Zie ook:

* [Syntaxis van de functie Mid](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Functiesyntaxis vervangen](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntaxis van de functie Unieke waarde selecteren](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hoe verwijder ik tekens met diacritics en zet ze om in normale Engelse alfabetten?

Gebruik de functie [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) om speciale tekens in voor- en achternaam van de gebruiker te verwijderen, terwijl u het e-mailadres of de CN-waarde voor de gebruiker maakt.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

In deze sectie vindt u specifieke richtlijnen voor het oplossen van inrichtingsproblemen met uw Workday-integratie met behulp van de Azure AD-controlelogboeken en logboeken van Windows Server-logboeken. Het bouwt voort op de algemene stappen en concepten voor het oplossen van problemen die zijn vastgelegd in de [zelfstudie: rapportage over het inrichten van automatische gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md)

In deze sectie worden de volgende aspecten van het oplossen van problemen bezien:

* [Windows Logboeken instellen voor het oplossen van problemen met agenten](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Azure-portalcontrolelogboeken instellen voor het oplossen van serviceproblemen](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Logboeken voor het maken van AD-gebruikersaccounts begrijpen](#understanding-logs-for-ad-user-account-create-operations)
* [Logboeken voor managerupdatebewerkingen begrijpen](#understanding-logs-for-manager-update-operations)
* [Vaak voorkomende fouten oplossen](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Windows Logboeken instellen voor het oplossen van problemen met agenten

* Meld u aan bij de Windows Server-machine waar de inrichtingsagent is geïmplementeerd
* Open **de bureaubladapp Windows Server Event Viewer.**
* Selecteer **Windows-logboeken > toepassing**.
* Gebruik het **logboek Filterhuidig...** optie om alle gebeurtenissen die zijn aangemeld onder de bron AAD te **bekijken. Connect.ProvisioningAgent** en sluit gebeurtenissen uit met gebeurtenis-id "5", door het filter "-5" op te geven zoals hieronder wordt weergegeven.

  ![Windows-logboeken](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klik **op OK** en sorteer de resultaatweergave op de kolom Datum en **tijd.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Azure-portalcontrolelogboeken instellen voor het oplossen van serviceproblemen

* Start de [Azure-portal](https://portal.azure.com)en navigeer naar het gedeelte **Controlelogboeken** van uw workday-inrichtingstoepassing.
* Gebruik de knop **Kolommen** op de pagina Controlelogboeken om alleen de volgende kolommen in de weergave weer te geven (Datum, activiteit, status, statusreden). Deze configuratie zorgt ervoor dat u zich alleen richt op gegevens die relevant zijn voor het oplossen van problemen.

  ![Kolommen van controlelogboeken](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Gebruik de queryparameters **Doel-** en **Datumbereik** om de weergave te filteren. 
  * Stel de parameter **Doelquery** in op de 'Werknemers-id' of 'Werknemers-id' van het werkdagwerkobject.
  * Stel het **datumbereik** in op een geschikte periode waarover u wilt onderzoeken op fouten of problemen met de inrichting.

  ![Controlelogboekfilters](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Logboeken voor het maken van AD-gebruikersaccounts begrijpen

Wanneer een nieuwe werknemer in Workday wordt gedetecteerd (laten we zeggen met Werknemers-id *21023),* probeert de Azure AD-inrichtingsservice een nieuw AD-gebruikersaccount voor de werknemer te maken en maakt in het proces 4 controlelogboekrecords zoals hieronder beschreven:

  [![Ops maken van controlelogboeken](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Wanneer u op een van de controlelogboekrecords klikt, wordt de pagina **Activiteitsgegevens** geopend. Dit is wat de pagina **Activiteitsdetails** wordt weergegeven voor elk type logboekrecord.

* **Record voor het importeren van werkdagen:** in deze logboekrecord worden de werknemersgegevens weergegeven die zijn opgehaald bij Werkdag. Gebruik informatie in de sectie *Aanvullende details* van de logboekrecord om problemen met het ophalen van gegevens van Workday op te lossen. Hieronder wordt een voorbeeldrecord weergegeven, samen met aanwijzingen over hoe u elk veld interpreteert.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD-importrecord:** in deze logboekrecord worden gegevens weergegeven van het account dat is opgehaald bij AD. Aangezien er tijdens het maken van de gebruiker geen AD-account is, geeft de *reden activiteitsstatus* aan dat er geen account met de kenmerkwaarde Matching ID is gevonden in Active Directory. Gebruik informatie in de sectie *Aanvullende details* van de logboekrecord om problemen met het ophalen van gegevens van Workday op te lossen. Hieronder wordt een voorbeeldrecord weergegeven, samen met aanwijzingen over hoe u elk veld interpreteert.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Als u logboekrecords voor provisioning agent wilt vinden die overeenkomen met deze AD-importbewerking, opent u de logboeken van Windows Logboeken en gebruikt u de **Zoek...** menuoptie om logboekvermeldingen te zoeken die de kenmerkwaarde Matching ID/Joining Property bevatten (in dit geval *21023*).

  ![Find](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Zoek naar de vermelding met *gebeurtenis-id = 9*, waarmee u het LDAP-zoekfilter krijgt dat door de agent wordt gebruikt om het AD-account op te halen. U controleren of dit het juiste zoekfilter is om unieke gebruikersvermeldingen op te halen.

  ![LDAP-zoekopdracht](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  De record die onmiddellijk volgt met *Event ID = 2* vangt het resultaat van de zoekbewerking en als het geen resultaten heeft geretourneerd.

  ![LDAP-resultaten](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Actierecord synchronisatieregel:** in deze logboekrecord worden de resultaten van de regels voor het toewijzen van kenmerken en geconfigureerde scopingfilters weergegeven, samen met de indringsactie die wordt uitgevoerd om de inkomende gebeurtenis Workday te verwerken. Gebruik informatie in de sectie *Aanvullende details* van de logboekrecord om problemen met de synchronisatieactie op te lossen. Hieronder wordt een voorbeeldrecord weergegeven, samen met aanwijzingen over hoe u elk veld interpreteert.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Als er problemen zijn met uw kenmerktoewijzingsexpressies of als de binnenkomende Workday-gegevens problemen hebben (bijvoorbeeld: lege of null-waarde voor vereiste kenmerken), ziet u in dit stadium een fout met de ErrorCode met details over de fout.

* **AD-exportrecord:** met deze logboekrecord wordt het resultaat weergegeven van de bewerking van AD-accountcreatie, samen met de kenmerkwaarden die in het proces zijn ingesteld. Gebruik informatie in de sectie *Aanvullende details* van de logboekrecord om problemen met de accountbewerking op te lossen. Hieronder wordt een voorbeeldrecord weergegeven, samen met aanwijzingen over hoe u elk veld interpreteert. In de sectie 'Aanvullende details' is de 'Gebeurtenisnaam' ingesteld op 'EntryExportAdd', de 'JoiningProperty' is ingesteld op de waarde van het kenmerk Matching ID, is het 'SourceAnchor' ingesteld op de WorkdayID (WID) die aan de record is gekoppeld en is de 'TargetAnchor' ingesteld op de waarde van het kenmerk AD "ObjectGuid" van de nieuw gemaakte gebruiker. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Als u logboekrecords voor provisioning agent wilt vinden die overeenkomen met deze AD-exportbewerking, opent u de logboeken van Windows Logboeken en gebruikt u de **Zoek...** menuoptie om logboekvermeldingen te zoeken die de kenmerkwaarde Matching ID/Joining Property bevatten (in dit geval *21023*).  

  Zoek naar een HTTP POST-record die overeenkomt met de tijdstempel van de exportbewerking met *gebeurtenis-id = 2*. Deze record bevat de attribuutwaarden die door de inrichtingsservice naar de inrichtingsagent worden verzonden.

  [![SCIM-toevoegen](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Onmiddellijk na de bovenstaande gebeurtenis moet er een andere gebeurtenis zijn die de reactie van de bewerking AD-account maken vastlegt. Met deze gebeurtenis wordt het nieuwe objectGuid geretourneerd dat in AD is gemaakt en wordt deze ingesteld als het kenmerk TargetAnchor in de inrichtingsservice.

  [![SCIM-toevoegen](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Logboeken voor managerupdatebewerkingen begrijpen

Het kenmerk Manager is een referentiekenmerk in AD. De inrichtingsservice stelt het kenmerk manager niet in als onderdeel van de bewerking voor het maken van de gebruiker. Het kenmerk Manager wordt eerder ingesteld als onderdeel van een *updatebewerking* nadat het AD-account voor de gebruiker is gemaakt. Als u het bovenstaande voorbeeld uitbreidt, wordt een nieuwe werknemer met werknemers-id '21451' geactiveerd in Workday en heeft de manager van de nieuwe werknemer (*21023*) al een AD-account. In dit scenario worden in de controlelogboeken voor gebruiker 21451 5 vermeldingen weergegeven.

  [![Manager-update](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

De eerste 4 records zijn als die we verkend als onderdeel van de gebruiker te maken operatie. De vijfde record is de export die is gekoppeld aan managerkenmerkupdate. De logboekrecord geeft het resultaat weer van de updatebewerking van AD-accountmanager, die wordt uitgevoerd met het *objectGuid-kenmerk* van de manager.

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

### <a name="resolving-commonly-encountered-errors"></a>Vaak voorkomende fouten oplossen

In deze sectie worden vaak geziene fouten met workday-gebruikersinrichting behandeld en hoe deze worden opgelost. De fouten zijn als volgt gegroepeerd:

* [Fouten in de inrichtingsagent](#provisioning-agent-errors)
* [Connectiviteitsfouten](#connectivity-errors)
* [Fouten bij het maken van AD-gebruikersaccounts](#ad-user-account-creation-errors)
* [Fouten bij het bijwerken van AD-gebruikersaccounts](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Fouten in de inrichtingsagent

|#|Foutscenario |Waarschijnlijke oorzaken|Aanbevolen resolutie|
|--|---|---|---|
|1.| Fout bij het installeren van de inrichtingsagent met foutbericht: *Service 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) kan niet worden gestart. Controleer of u voldoende bevoegdheden hebt om het systeem te starten.* | Deze fout wordt meestal weergegeven als u de inrichtingsagent op een domeincontroller probeert te installeren en groepsbeleid voorkomt dat de service wordt gestart.  Het is ook gezien als u een vorige versie van de agent draait en je hebt niet verwijderd voordat u een nieuwe installatie.| Installeer de inrichtingsagent op een niet-DC-server. Controleer of eerdere versies van de agent zijn verwijderd voordat u de nieuwe agent installeert.|
|2.| De Windows Service 'Microsoft Azure AD Connect Provisioning Agent' bevindt zich *in de status Start* en schakelt niet over naar de status *Uitvoeren.* | Als onderdeel van de installatie maakt de wizard agent een lokaal account **(NT Service\\AADConnectProvisioningAgent)** op de server en dit is het aanmeldingsaccount dat wordt gebruikt voor het starten van de service. Als een beveiligingsbeleid op uw Windows-server voorkomt dat lokale accounts de services uitvoeren, komt u deze fout tegen. | Open de *console Services*. Klik met de rechtermuisknop op de Windows Service 'Microsoft Azure AD Connect Provisioning Agent' en geef op het tabblad aanmelding het account op van een domeinbeheerder om de service uit te voeren. Start de service opnieuw. |
|3.| Wanneer u de inrichtingsagent configureert met uw AD-domein in de stap *Active Directory verbinden,* neemt de wizard lang het tijd om het AD-schema te laden en uiteindelijk een time-out te maken. | Deze fout komt meestal voor als de wizard geen contact kan maken met de controllerserver van het AD-domein door firewallproblemen. | In het *wizardscherm Active Directory verbinden* is er een optie met de *naam Select domain controller priority.* Gebruik deze optie om een domeincontroller te selecteren die zich op dezelfde site bevindt als de agentserver en ervoor te zorgen dat er geen firewallregels zijn die de communicatie blokkeren. |

#### <a name="connectivity-errors"></a>Connectiviteitsfouten

Als de inrichtingsservice geen verbinding kan maken met Workday of Active Directory, kan de inrichting in een quarantainestatus worden geplaatst. Gebruik de onderstaande tabel om verbindingsproblemen op te lossen.

|#|Foutscenario |Waarschijnlijke oorzaken|Aanbevolen resolutie|
|--|---|---|---|
|1.| Wanneer u op **Testverbinding**klikt, krijgt u het foutbericht: *er is een fout opgetreden bij active directory. Zorg ervoor dat de on-premises provisioning agent wordt uitgevoerd en deze is geconfigureerd met het juiste Active Directory-domein.* | Deze fout wordt meestal weergegeven als de inrichtingsagent niet wordt uitgevoerd of als er een firewall is die de communicatie tussen Azure AD en de inrichtingsagent blokkeert. U deze fout ook zien als het domein niet is geconfigureerd in de wizard Agent. | Open de *Services-console* op de Windows-server om te controleren of de agent actief is. Open de wizard inrichtende agent en controleer of het juiste domein is geregistreerd bij de agent.  |
|2.| De inrichtingstaak gaat in quarantaine tijdens het weekend (vr-zat) en we krijgen een e-mailmelding dat er een fout is opgetreden bij de synchronisatie. | Een veelvoorkomende oorzaak van deze fout is de geplande downtime voor Workday. Let op: Als u gebruikmaakt van een Workday-implementatietenant, wordt in het weekend downtime gepland voor de Workday-implementatietenants (meestal van vrijdagavond tot zaterdagochtend). Gedurende deze periode kan de Workday-app voor inrichting de status In quarantaine krijgen, omdat deze geen verbinding kan maken met Workday. De status wordt weer normaal zodra de Workday-implementatietenant weer online is. In zeldzame gevallen ziet u deze fout mogelijk ook als het wachtwoord van de integratiesysteemgebruiker is gewijzigd vanwege het vernieuwen van de tenant, of als het account is vergrendeld of de status Verlopen heeft. | Neem contact op met de Workday-beheerder of integratiepartner voor informatie over de geplande downtime van Workday om de waarschuwingen tijdens deze periode te negeren en de beschikbaarheid te bevestigen zodra het Workday-exemplaar weer online is.  |


#### <a name="ad-user-account-creation-errors"></a>Fouten bij het maken van AD-gebruikersaccounts

|#|Foutscenario |Waarschijnlijke oorzaken|Aanbevolen resolutie|
|--|---|---|---|
|1.| Fout in de uitvoerbewerking in het controlelogboek met het bericht *Fout: OperationsError-SvcErr: Er is een bewerkingsfout opgetreden. Er is geen superieure referentie geconfigureerd voor de directoryservice. De directoryservice kan daarom geen verwijzingen naar objecten buiten dit forest geven.* | Deze fout wordt meestal weergegeven als de *active directorycontainer-eenheid* niet correct is ingesteld of als er problemen zijn met de expressietoewijzing die wordt gebruikt voor *bovenliggende Naam .* | Controleer de parameter *Active Directory Container* OU op typefouten. Als u *parentDistinguishedName* gebruikt in de kenmerktoewijzing, zorg er dan voor dat het altijd resulteert in een bekende container binnen het AD-domein. Controleer de gebeurtenis *Exporteren* in de controlelogboeken om de gegenereerde waarde weer te geven. |
|2.| Foutieel uitvoeren in het controlelogboek met foutcode: *SystemForCrossDomainIdentityManagementBadResponse* en *berichtfout: BeperkingSchending-AtrErr: Een waarde in de aanvraag is ongeldig. Een waarde voor het kenmerk bevond zich niet in het aanvaardbare waardenbereik. \nError Details: CONSTRAINT_ATT_TYPE - bedrijf*. | Hoewel deze fout specifiek is voor het *bedrijfskenmerk,* ziet u deze fout mogelijk ook voor andere kenmerken zoals *CN.* Deze fout wordt weergegeven als gevolg van ad afgedwongen schemabeperking. Standaard hebben de kenmerken zoals *bedrijf* en *CN* in AD een bovengrens van 64 tekens. Als de waarde van Workday meer dan 64 tekens bedraagt, ziet u dit foutbericht. | Controleer de gebeurtenis *Exporteren* in de controlelogboeken om de waarde te zien voor het kenmerk dat in het foutbericht wordt gerapporteerd. Overweeg de waarde van Workday af te catafen met de functie [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) of de toewijzingen te wijzigen in een AD-kenmerk dat geen vergelijkbare lengtebeperkingen heeft.  |

#### <a name="ad-user-account-update-errors"></a>Fouten bij het bijwerken van AD-gebruikersaccounts

Tijdens het updateproces van het AD-gebruikersaccount leest de inrichtingsservice informatie van zowel Werkdag als AD, voert de regels voor het toewijzen van kenmerken uit en bepaalt of wijzigingen van kracht moeten worden. Daarom wordt een updategebeurtenis geactiveerd. Als een van deze stappen een fout ondervindt, wordt deze geregistreerd in de controlelogboeken. Gebruik de onderstaande tabel om veelvoorkomende updatefouten op te lossen.

|#|Foutscenario |Waarschijnlijke oorzaken|Aanbevolen resolutie|
|--|---|---|---|
|1.| Synchronisatieregelactiefouten in het controlelogboek met het bericht *EventName = EntrySynchronizationError and ErrorCode = EndpointUnavailable*. | Deze fout wordt weergegeven als de inrichtingsservice gebruikersprofielgegevens niet kan ophalen uit Active Directory vanwege een verwerkingsfout die is opgetreden door de on-premises inrichtingsagent. | Controleer de logboeken van Provisioning Agent Event Viewer op foutgebeurtenissen die problemen met de leesbewerking aangeven (Filter op gebeurtenis-id #2). |
|2.| Het kenmerk Manager in AD wordt niet bijgewerkt voor bepaalde gebruikers in AD. | De meest waarschijnlijke oorzaak van deze fout is als u scopingregels gebruikt en de manager van de gebruiker geen deel uitmaakt van het bereik. U dit probleem ook tegenkomen als het overeenkomende ID-kenmerk van de manager (bijvoorbeeld EmployeeID) niet wordt gevonden in het doel-AD-domein of niet is ingesteld op de juiste waarde. | Bekijk het scopingfilter en voeg de managergebruiker toe in het bereik. Controleer het profiel van de manager in AD om te controleren of er een waarde is voor het kenmerk overeenkomende id. |

## <a name="managing-your-configuration"></a>Uw configuratie beheren

In dit gedeelte wordt beschreven hoe u uw Workday-gestuurde gebruikersinrichtingsconfiguratie verder uitbreiden, aanpassen en beheren. Het behandelt de volgende onderwerpen:

* [De lijst met gebruikerskenmerken van Workday aanpassen](#customizing-the-list-of-workday-user-attributes)  
* [Uw configuratie exporteren en importeren](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>De lijst met gebruikerskenmerken van Workday aanpassen

De Workday provisioning-apps voor Active Directory en Azure AD bevatten beide een standaardlijst met gebruikerskenmerken van Workday waaruit u kiezen. Deze lijsten zijn echter niet volledig. Workday ondersteunt vele honderden mogelijke gebruikerskenmerken, die standaard of uniek kunnen zijn voor uw Workday-tenant.

De Azure AD-inrichtingsservice ondersteunt de mogelijkheid om uw lijst of het kenmerk Workday aan te passen om alle kenmerken op te nemen die zijn blootgesteld in de [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) werking van de Human Resources API.

Als u deze wijziging wilt doen, moet u [Workday Studio](https://community.workday.com/studio-download) gebruiken om de XPath-expressies te extraheren die de kenmerken vertegenwoordigen die u wilt gebruiken en deze vervolgens toe te voegen aan uw inrichtingsconfiguratie met behulp van de geavanceerde kenmerkeditor in de Azure-portal.

**Ga als volgt te werk om een XPath-expressie op te halen voor een gebruikerskenmerk Workday:**

1. Workday [Studio](https://community.workday.com/studio-download)downloaden en installeren . Je hebt een Workday-communityaccount nodig om toegang te krijgen tot de installer.

2. Download het Workday Human_Resources WSDL-bestand via deze URL:https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Start Workday Studio.

4. Selecteer op de opdrachtbalk de optie **Workday > Test Web Service in Tester.**

5. Selecteer **Extern**en selecteer het Human_Resources WSDL-bestand dat u in stap 2 hebt gedownload.

    ![Werkdag Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Stel **Location** het veld `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`Locatie in op , maar vervang 'IMPL-CC' door uw werkelijke instantietype en 'TENANT' door uw echte tenantnaam.

7. **Bewerking** instellen op **Get_Workers**

8.  Klik op de koppeling Kleine **configureren** onder de deelvensters Aanvragen/antwoord om uw werkdagreferenties in te stellen. Controleer **Verificatie**en voer de gebruikersnaam en het wachtwoord in voor uw Workday-integratiesysteemaccount. Zorg ervoor dat u de\@gebruikersnaam opmaakt als naamtenant en de optie **GebruikersnaamToken voor WS-beveiliging** geselecteerd laat.

    ![Werkdag Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Selecteer **OK**.

10. Plak in het deelvenster **Aanvragen** de onderstaande XML en stel **Employee_ID** in op de werknemers-id van een echte gebruiker in uw Workday-tenant. Selecteer een gebruiker met het kenmerk dat u wilt extraheren.

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

11. Klik **op de opdracht Aanvraag verzenden** (groene pijl) om de opdracht uit te voeren. Als dit is gelukt, moet het antwoord worden weergegeven in het **deelvenster Antwoord.** Controleer het antwoord om te controleren of de gegevens van de gebruikers-id die u hebt ingevoerd, en niet een fout.

12. Als dit is gelukt, kopieert u de XML vanuit het **deelvenster Respons** en slaat u deze op als een XML-bestand.

13. In the command bar of Workday Studio, select **File > Open File...** and open the XML file you saved. Met deze actie wordt het bestand geopend in de XML-editor van Workday Studio.

    ![Werkdag Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Navigeer in de bestandsstructuur door **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** to find your user's data.

15. Zoek **onder wd: Worker**het kenmerk dat u wilt toevoegen en selecteer het.

16. Kopieer de XPath-expressie voor het geselecteerde kenmerk uit het veld **Documentpad.**

17. Verwijder het **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** voorvoegsel uit de gekopieerde expressie.

18. Als het laatste item in de gekopieerde expressie een knooppunt is (voorbeeld: "/wd: Birth_Date"), voeg **/text()** toe aan het einde van de expressie. Dit is niet nodig als het laatste item/@wd: een kenmerk is (voorbeeld: "type").

19. Het resultaat moet `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`zoiets zijn als . Deze waarde kopieert u naar de Azure-portal.

**Ga als volgende over het aangepaste gebruikerskenmerk Workday toe aan uw inrichtingsconfiguratie:**

1. Start de [Azure-portal](https://portal.azure.com)en navigeer naar het gedeelte Provisioning van uw workday-inrichtingstoepassing, zoals eerder in deze zelfstudie wordt beschreven.

2. **Inrichtenstatus** instellen in **uit**en selecteer **Opslaan**. Deze stap zal ervoor zorgen dat uw wijzigingen alleen van kracht worden wanneer u er klaar voor bent.

3. Selecteer **Onder Toewijzingen**de optie **Werkdagwerkers synchroniseren met Active Directory voor on-premises** (of **Werkdagwerknemers synchroniseren met Azure AD**).

4. Schuif naar de onderkant van het volgende scherm en selecteer **Geavanceerde opties weergeven**.

5. Selecteer **Kenmerklijst bewerken voor Werkdag**.

    ![Werkdag Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Schuif naar de onderkant van de kenmerklijst naar de plaats waar de invoervelden zich bevinden.

7. Voer voor **Naam**een weergavenaam in voor uw kenmerk.

8. Selecteer **bij Tekst**tekst tekst dat op de juiste manier overeenkomt met uw kenmerk **(Tekenreeks** komt het meest voor).

9. Voer voor **API-expressie**de XPath-expressie in die u hebt gekopieerd vanuit Workday Studio. Voorbeeld: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selecteer **Kenmerk toevoegen**.

    ![Werkdag Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Selecteer Hierboven **opslaan** en vervolgens **Ja** in het dialoogvenster. Sluit het scherm Attribute-Mapping als het nog open is.

12. Selecteer **Workday Workers synchroniseren met Active Directory voor on-premises** (of **Werknemers synchroniseren met Azure AD)** opnieuw op het tabblad **Provisioning.**

13. Selecteer **Nieuwe toewijzing toevoegen**.

14. Uw nieuwe kenmerk moet nu worden weergegeven in de lijst **met bronkenmerken.**

15. Voeg desgewenst een toewijzing toe voor uw nieuwe kenmerk.

16. Als u klaar bent, moet u **de instelstatus** weer instellen **op Aan** en opslaan.

### <a name="exporting-and-importing-your-configuration"></a>Uw configuratie exporteren en importeren

Raadpleeg het artikel [Voor het exporteren en importeren van inrichtingsconfiguratie](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Persoonlijke gegevens beheren

De oplossing voor het inrichten van Workday voor Active Directory vereist dat een inrichtingsagent wordt geïnstalleerd op een on-premises Windows-server en deze agent maakt logboeken in het Windows-gebeurtenislogboek die mogelijk persoonlijke gegevens bevatten, afhankelijk van uw toewijzingen van uw werkdag tot AD-kenmerk. Als u wilt voldoen aan de privacyverplichtingen van de gebruiker, u ervoor zorgen dat er geen gegevens worden bewaard in de logboeken van gebeurtenissen na 48 uur door een geplande Windows-taak in te stellen om het gebeurtenislogboek te wissen.

De Azure AD-inrichtingsservice valt in de categorie **gegevensverwerker** van GDPR-classificatie. Als pijplijn voor gegevensverwerkers biedt de service gegevensverwerkingsdiensten aan belangrijke partners en eindgebruikers. Azure AD-inrichtingsservice genereert geen gebruikersgegevens en heeft geen onafhankelijke controle over welke persoonlijke gegevens worden verzameld en hoe deze worden gebruikt. Het ophalen, samenvoegen, analyseren en rapporteren van gegevens in azure AD-inrichtingsservice zijn gebaseerd op bestaande bedrijfsgegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Met betrekking tot het bewaren van gegevens genereert de Azure AD-inrichtingsservice geen rapporten, voert u geen analyses uit of biedt u geen inzichten van meer dan 30 dagen. Daarom slaat de Azure AD-inrichtingsservice geen gegevens op, verwerkt of bewaart deze niet langer dan 30 dagen. Dit ontwerp voldoet aan de GDPR-voorschriften, de privacynalevingsvoorschriften van Microsoft en het azure AD-beleid voor het bewaren van gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen Workday en Azure Active Directory](workday-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het gebruik van Microsoft Graph API's voor het beheren van inrichtingsconfiguraties](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
