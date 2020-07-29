---
title: 'Zelf studie: inkomend inrichtings werk dagen configureren in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van binnenkomende inrichting van workday naar Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: fac4f61e-d942-4429-a297-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 6fb80af84379a1a0bc174a7318c8150a98bea95e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84041811"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Zelf studie: workday configureren voor Azure AD-gebruikers inrichten
Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren om werk nemers van workday in te richten in Azure Active Directory. 

>[!NOTE]
>Gebruik deze zelf studie als de gebruikers die u wilt inrichten vanaf workday alleen Cloud gebruikers zijn die geen on-premises AD-account nodig hebben. Als de gebruikers alleen een on-premises AD-account of AD-en Azure AD-account nodig hebben, raadpleegt u de zelf studie over het [configureren van workday voor het Active Directory](workday-inbound-tutorial.md) van gebruikers inrichting. 

## <a name="overview"></a>Overzicht

De [Azure Active Directory User Provisioning Service](../app-provisioning/user-provisioning.md) kan worden geïntegreerd met de [HR Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) om gebruikers accounts in te richten. De werk stroom die door de gebruiker wordt ondersteund door de Azure AD User Provisioning-Service, biedt de mogelijkheid om de volgende scenario's voor human resources en Identity Lifecycle Management te automatiseren:

* **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan workday, wordt automatisch een gebruikers account gemaakt in azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md), met een terugschrijf bewerking van het e-mail adres naar workday.

* **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in workday (zoals hun naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Beëindiging van werk nemers** : wanneer een werk nemer wordt beëindigd in workday, wordt het gebruikers account automatisch uitgeschakeld in azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* Opnieuw **inhuren van werk nemers** : wanneer een werk nemer in workday opnieuw wordt ingehuurd, kan het oude account automatisch opnieuw worden geactiveerd of worden ingericht (afhankelijk van uw voor keur) tot Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

Deze werkdag om de oplossing voor het inrichten van gebruikers te Azure Active Directory is in het ideale geval geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op de cloud gebaseerde oplossing voor het inrichten van gebruikers met werk dagen

* Organisaties die directe gebruikers inrichting van workday naar Azure Active Directory vereisen

* Organisaties waarvoor gebruikers moeten worden ingericht met gegevens die zijn verkregen van workday

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In deze sectie wordt de end-to-end-architectuur voor het inrichten van gebruikers voor Cloud gebruikers beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevens stroom: van werkdag tot Azure Active Directory:** In deze stroom worden werk gebeurtenissen (zoals nieuwe aanmeldingen, overdrachten, beëindigingen) voor het eerst uitgevoerd in workday, waarna de gebeurtenis gegevens stromen naar Azure Active Directory. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in azure AD.
* **Write-back-flow: van on-premises Active Directory naar werkdag:** Zodra het account is gemaakt in Active Directory, wordt het gesynchroniseerd met Azure AD via Azure AD Connect en kunnen gegevens zoals e-mail, gebruikers naam en telefoon nummer worden teruggeschreven naar workday.

  ![Overzicht](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Gegevens stroom van end-to-end-gebruikers

1. Het HR-team voert werknemers transacties (samenvoegers/verplaatsingen/Leavers of nieuwe huur/overschrijvingen/afsluitingen) uit in werk nemers midden van de werkdag
2. Met de Azure AD-inrichtings service worden geplande synchronisaties van identiteiten van de werkdag EC uitgevoerd en worden wijzigingen geïdentificeerd die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtings service bepaalt de wijziging en roept de bewerking voor het maken/bijwerken/inschakelen/uitschakelen van de gebruiker aan in azure AD.
4. Als de app [write-back](workday-writeback-tutorial.md) -uptoepassing is geconfigureerd, haalt deze kenmerken, zoals e-mail, gebruikers naam en telefoon nummer op uit Azure AD. 
5. Azure AD Provisioning Service stelt e-mail adres, gebruikers naam en telefoon nummer in workday in.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van in de Cloud HR gestuurde gebruikers inrichten van workday naar Azure AD vereist een aanzienlijke planning voor verschillende aspecten, zoals:

* De overeenkomende ID bepalen 
* Kenmerktoewijzing
* Kenmerk transformatie 
* Bereikfilters

Raadpleeg het [implementatie plan voor Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richt lijnen rond deze onderwerpen. 

## <a name="configure-integration-system-user-in-workday"></a>De gebruiker van het integratie systeem configureren in workday

Raadpleeg de sectie [integratie systeem gebruiker configureren](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) voor het maken van een gebruikers account voor workday-integratie systeem met machtigingen voor het ophalen van gegevens van werk nemers. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Gebruikers inrichten van workday naar Azure AD configureren

In de volgende secties worden de stappen beschreven voor het configureren van gebruikers inrichting van workday naar Azure AD voor Cloud implementaties.

* [De Azure AD Provisioning connector-app toevoegen en de verbinding met workday maken](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday-en Azure AD-kenmerk toewijzingen configureren](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: de Azure AD Provisioning connector-app toevoegen en de verbinding met workday maken

**Werk dagen configureren voor Azure Active Directory inrichting voor Cloud gebruikers:**

1. Ga naar <https://portal.azure.com>.

2. Zoek en selecteer in de Azure-portal de optie **Azure Active Directory**.

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer vervolgens de categorie **alle** .

5. Zoek naar een **werkdag naar Azure AD-gebruikers inrichten**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting**.

7. Wijzig de **inrichtings** **modus** in **automatisch**.

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Workday-gebruikers naam** : Voer de gebruikers naam in van het werk account van het workday-integratie systeem, waarbij de domein naam van de Tenant is toegevoegd. Moet er ongeveer als volgt uitzien:username@contoso4

   * **Wacht woord voor werk dagen –** Voer het wacht woord in voor het workday-integratie systeem account

   * **Workday Web Services API-URL:** Voer de URL naar het workday Web Services-eind punt voor uw Tenant in. De URL bepaalt de versie van de workday Web Services API die door de connector wordt gebruikt. 
   
     | URL-indeling | WWS API-versie gebruikt | XPATH-wijzigingen vereist |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Yes |

      > [!NOTE]
     > Als er geen versie gegevens zijn opgegeven in de URL, gebruikt de app workday Web Services (WWS) v 21.1 en zijn er geen wijzigingen vereist voor de standaard XPATH API-expressies die worden geleverd bij de app. Als u een specifieke API-versie van WWS wilt gebruiken, geeft u het versie nummer op in de URL <br>
     > Voorbeeld: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Als u een WWS API v 30.0 + gebruikt voordat u de inrichtings taak inschakelt, moet u **de API-expressies van XPath** bijwerken onder **kenmerk toewijzing-> geavanceerde opties-> kenmerk lijst bewerken voor workday** verwijzen naar de sectie [uw configuratie](workday-inbound-tutorial.md#managing-your-configuration) en [workday-kenmerk verwijzing](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)beheren.  

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.

   * Klik op de knop **verbinding testen** .

   * Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de workday-URL en referenties geldig zijn in werkdag.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Deel 2: werk dagen en Azure AD-kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van workday naar Azure Active Directory voor Cloud gebruikers.

1. Klik op het tabblad inrichting onder **toewijzingen**op werk rollen **synchroniseren met Azure AD**.

2. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in workday het bereik moeten hebben voor het inrichten van Azure AD door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in workday. Voorbeeld filters:

   * Voor beeld: bereik voor gebruikers met werk nemer-Id's tussen 1000000 en 2000000

      * Kenmerk: WorkerID

      * Operator: overeenkomende REGEX

      * Waarde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Voor beeld: alleen voorwaardelijke werk nemers en geen gewone werk nemers

      * Kenmerk: ContingentID

      * Operator: IS niet NULL

3. In het veld **acties doel object** kunt u globaal filteren welke acties er worden uitgevoerd op Azure AD. **Create** en **Update** worden het meest gebruikt.

4. In de sectie **kenmerk toewijzingen** kunt u definiëren hoe afzonderlijke workday-kenmerken worden toegewezen aan Active Directory kenmerken.

5. Klik op een bestaande kenmerk toewijzing om het bij te werken of Klik onder aan het scherm op **nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een individuele kenmerk toewijzing ondersteunt de volgende eigenschappen:

   * **Toewijzings type**

      * **Direct** : schrijft de waarde van het kenmerk workday naar het kenmerk AD, zonder wijzigingen

      * **Constante** : een statische, constante teken reeks waarde schrijven naar het AD-kenmerk

      * **Expressie** : Hiermee kunt u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer workday-kenmerken. [Zie dit artikel over expressies voor meer informatie](../app-provisioning/functions-for-customizing-application-data.md).

   * **Bron kenmerk** : het gebruikers kenmerk van workday. Als het kenmerk dat u zoekt niet aanwezig is, raadpleegt u [de lijst met gebruikers kenmerken voor workday aanpassen](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Standaard waarde** : optioneel. Als het bron kenmerk een lege waarde heeft, wordt deze waarde in plaats daarvan door de toewijzing geschreven.
            De meest voorkomende configuratie is om dit leeg te laten.

   * **Doel kenmerk** : het gebruikers kenmerk in azure AD.

   * **Objecten matchen met dit kenmerk** : ongeacht of dit kenmerk moet worden gebruikt om gebruikers te identificeren tussen workday en Azure AD. Deze waarde wordt doorgaans ingesteld op het veld werk nemer-ID voor workday, die meestal wordt toegewezen aan het kenmerk Employee ID (nieuw) of een extensie kenmerk in azure AD.

   * **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

   * **Deze toewijzing Toep assen**

     * **Altijd** : deze toewijzing Toep assen op zowel het maken van gebruikers als bij het bijwerken van acties

     * **Alleen tijdens het maken** : pas deze toewijzing alleen toe bij het maken van gebruikers acties

6. Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.


## <a name="enable-and-launch-user-provisioning"></a>Gebruikers inrichting inschakelen en starten

Zodra de configuratie van de app voor workday-inrichting is voltooid, kunt u de inrichtings service inschakelen in de Azure Portal.

> [!TIP]
> Wanneer u de inrichtings service inschakelt, worden er standaard inrichtings bewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten optreden in de toewijzings-of workday-gegevens problemen, kan de inrichtings taak mislukken en gaat u naar de quarantaine status. Om dit te voor komen best practice, raden we u aan om het bereik filter voor **bron objecten** te configureren en uw kenmerk toewijzingen te testen met enkele test gebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en u de gewenste resultaten krijgt, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Stel op het tabblad **inrichten** de **inrichtings status** in **op aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, wat een variabel aantal uur kan duren, afhankelijk van het aantal gebruikers in de workday-Tenant. U kunt de voortgangs balk controleren om de voortgang van de synchronisatie cyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **controle logboeken** in de Azure Portal om te zien welke acties de inrichtings service heeft uitgevoerd. In de controle logboeken worden alle afzonderlijke synchronisatie gebeurtenissen weer gegeven die door de inrichtings service worden uitgevoerd, bijvoorbeeld welke gebruikers worden gelezen uit de werk dagen en vervolgens worden toegevoegd of bijgewerkt aan Azure Active Directory. 

5. Zodra de initiële synchronisatie is voltooid, wordt een overzichts rapport van de controle op het tabblad **inrichten** geschreven, zoals hieronder wordt weer gegeven.

   > [!div class="mx-imgBorder"]
   > ![Voortgangs balk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ondersteunde workday-kenmerken voor inkomende inrichtingen](../app-provisioning/workday-attribute-reference.md)
* [Meer informatie over het configureren van workday-terugschrijven](workday-writeback-tutorial.md)
* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen werk dagen en Azure Active Directory](workday-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtings configuraties](../app-provisioning/export-import-provisioning-configuration.md)


