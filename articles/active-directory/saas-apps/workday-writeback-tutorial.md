---
title: 'Zelf studie: het terugschrijven van werk dagen configureren in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van kenmerk terugschrijven van Azure AD naar workday
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: ad255bd4-9e50-43a1-a92b-359215867b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 1d76fb96676ad49ce28ff4ef0d6c4fbc84636638
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041790"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Zelf studie: kenmerk terugschrijven van Azure AD naar workday configureren
Het doel van deze zelf studie is om de stappen weer te geven die u moet uitvoeren om kenmerken van Azure AD naar workday te schrijven. De werk-app voor het terugschrijven van werk dagen ondersteunt het toewijzen van waarden aan de volgende workday-kenmerken:
* E-mail werk 
* Workday-gebruikers naam
* Vast telefoon nummer voor werk (met inbegrip van land nummer, netnummer, aantal en uitbrei ding)
* Vaste telefoon nummer werk
* Mobiel telefoon nummer (inclusief land nummer, netnummer, getal)
* Primaire werk markering voor mobiele apparaten

## <a name="overview"></a>Overzicht

Nadat u de integratie van inkomende inrichtingen hebt ingesteld met behulp [van een werkdag naar een on-premises AD-](workday-inbound-tutorial.md) app voor het inrichten van een [Azure AD](workday-inbound-cloud-only-tutorial.md) -inrichtings app, kunt u optioneel de workday-app voor terugschrijven configureren om contact gegevens, zoals e-mail adres en telefoon nummer van werk, op te zetten. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

De inrichtings oplossing voor het terugschrijven van gebruikers van deze werkdag is ideaal voor:

* Organisaties die Office 365 gebruiken voor het terugschrijven van gezaghebbende kenmerken die door IT worden beheerd (zoals het e-mail adres, de gebruikers naam en het telefoon nummer), keer terug naar workday

## <a name="configure-integration-system-user-in-workday"></a>De gebruiker van het integratie systeem configureren in workday

Raadpleeg de sectie [integratie systeem gebruiker configureren](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) voor het maken van een gebruikers account voor workday-integratie systeem met machtigingen voor het ophalen van gegevens van werk nemers. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Azure AD-kenmerk terugschrijven naar workday configureren

Volg deze instructies voor het configureren van write-back van gebruikers-e-mail adressen en gebruikers naam van Azure Active Directory naar werkdag.

* [De back-upconnector-app toevoegen en de verbinding met workday maken](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Toewijzing van write-back-kenmerk configureren](#part-2-configure-writeback-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Deel 1: de back-upconnector-app toevoegen en de verbinding met workday maken

**De back-upconnector voor workday configureren:**

1. Ga naar <https://portal.azure.com>.

2. Zoek in het Azure Portal naar en selecteer **Azure Active Directory**.

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer vervolgens de categorie **alle** .

5. Zoek naar **werkdag terugschrijven**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting**.

7. Wijzig de **inrichtings** **modus** in **automatisch**.

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het werk account van het workday-integratie systeem, waarbij de domein naam van de Tenant is toegevoegd. Moet er ongeveer als volgt uitzien: *gebruikers naam \@ contoso4*

   * **Beheerders wachtwoord –** Voer het wacht woord in voor het workday-integratie systeem account

   * **Tenant-URL:** Voer de URL naar het workday Web Services-eind punt voor uw Tenant in. Deze waarde moet er als volgt uitzien: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , waarbij *contoso4* wordt vervangen door de juiste Tenant naam en *WD3-impl* vervangen door de juiste omgevings teken reeks (indien nodig).

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de workday-URL en referenties geldig zijn in werkdag.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Deel 2: toewijzing van write-back-kenmerk configureren

In deze sectie configureert u hoe terugschrijf kenmerken stromen van Azure AD naar werkdag. 

1. Klik op het tabblad inrichting onder **toewijzingen**op de naam van de toewijzing.

2. In het veld **bereik van bron object** kunt u optioneel filteren welke sets gebruikers in azure Active Directory deel moeten uitmaken van de terugschrijf functie. Het standaard bereik is alle gebruikers in azure AD.

3. Werk in de sectie **kenmerk toewijzingen** de overeenkomende id bij om het kenmerk in azure Active Directory aan te geven, waarbij de werk nemer-id van de werkdag of de werknemers-id wordt opgeslagen. Een populaire overeenkomende methode is het synchroniseren van de werk nemer-ID van de werkdag of de werknemers-ID naar extensionAttribute1-15 in azure AD. vervolgens gebruikt u dit kenmerk in azure AD om gebruikers terug te vinden in workday.

4. Normaal gesp roken wijst u het kenmerk Azure AD *userPrincipalName* toe aan het kenmerk workday *GebruikersID* en wijst u het Azure AD *mail-* kenmerk toe aan het kenmerk *EmailAddress* . 

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Gebruik de onderstaande richt lijnen voor het toewijzen van telefoon nummer kenmerk waarden van Azure AD op werkdag. 

     | Kenmerk workday telefoon | Verwachte waarde | Richt lijnen voor toewijzing |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | waar/onwaar | Constante of expressie toewijzing waarvan de uitvoer de teken reeks waarde ' True ' of ' false ' is. |
     | WorkphoneLandlineCountryCodeName | [ISO 3166-1-land code van drie letters](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Constante of expressie toewijzing waarvan de uitvoer een land code van drie letters is. |
     | WorkphoneLandlineCountryCodeNumber | [Code van internationaal land dat wordt gebeld](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Een constante of expressie toewijzing waarvan de uitvoer een geldige land code is (zonder het plus teken). |
     | WorkphoneLandlineNumber | Volledige telefoon nummer inclusief de netnummer | Wijs toe aan het kenmerk *telephoneNumber* . Gebruik regex om witruimte, haakjes en land code te verwijderen. Zie het onderstaande voor beeld. |
     | WorkphoneLandlineExtension | Extensie nummer | Als *telephoneNumber* de extensie bevat, gebruikt u regex om de waarde te extra heren. |
     | WorkphoneMobileIsPrimary | waar/onwaar | Constante toewijzing of expressie toewijzing waarvan de uitvoer de teken reeks waarde ' True ' of ' onwaar ' is |
     | WorkphoneMobileCountryCodeName | [ISO 3166-1-land code van drie letters](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Constante of expressie toewijzing waarvan de uitvoer een land code van drie letters is. |
     | WorkphoneMobileCountryCodeNumber | [Code van internationaal land dat wordt gebeld](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Een constante of expressie toewijzing waarvan de uitvoer een geldige land code is (zonder het plus teken). |
     | WorkphoneMobileNumber | Volledige telefoon nummer inclusief de netnummer | Wijs toe aan het *mobiele* kenmerk. Gebruik regex om witruimte, haakjes en land code te verwijderen. Zie het onderstaande voor beeld. |

     > [!NOTE]
     > Wanneer de Change_Work_Contact workday-webservice wordt aangeroepen, verzendt Azure AD de volgende constante waarden: <br>
     > * **Communication_Usage_Type_ID** is ingesteld op de constante teken reeks ' werk ' <br>
     > * **Phone_Device_Type_ID** is ingesteld op constante teken reeks "mobiel" voor mobiele telefoon nummers en "vaste" voor vaste telefoon nummers. <br>
     > 
     > U ontvangt een terugschrijf fout als uw werkdag gebruikmaakt van verschillende Type_IDs. Om dergelijke storingen te voor komen, kunt u de taak **referentie-id's** voor workday onderhouden gebruiken en de Type_IDs bijwerken zodat deze overeenkomen met de waarden die worden gebruikt door Azure AD. <br>
     >  

     **Verwijzing regex-expressies-voor beeld 1**

     Gebruik de onderstaande reguliere expressie als telefoon nummer in azure AD is ingesteld met behulp van de vereiste indeling voor selfservice voor wachtwoord herstel (SSPR). <br>
     Voor beeld: als de waarde voor telefoon nummer + 1 1112223333->, dan wordt de regex-expressie uitgevoerd 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Verwijzing regex-expressies-voor beeld 2**

     Gebruik de onderstaande reguliere expressie als telefoon nummer in azure AD is ingesteld met de indeling (XXX) XXX-XXXX. <br>
     Voor beeld: als de waarde voor telefoon nummer (111) 222-3333->, wordt de regex-expressie uitgevoerd 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.

## <a name="enable-and-launch-user-provisioning"></a>Gebruikers inrichting inschakelen en starten

Zodra de configuratie van de app voor workday-inrichting is voltooid, kunt u de inrichtings service inschakelen in de Azure Portal.

> [!TIP]
> Wanneer u de inrichtings service inschakelt, worden er standaard inrichtings bewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten optreden in de toewijzings-of workday-gegevens problemen, kan de inrichtings taak mislukken en gaat u naar de quarantaine status. Om dit te voor komen best practice, raden we u aan om het bereik filter voor **bron objecten** te configureren en uw kenmerk toewijzingen te testen met enkele test gebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en u de gewenste resultaten krijgt, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Stel op het tabblad **inrichten** de **inrichtings status** in **op aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, wat een variabel aantal uur kan duren, afhankelijk van het aantal gebruikers in de bronmap. U kunt de voortgangs balk controleren om de voortgang van de synchronisatie cyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **controle logboeken** in de Azure Portal om te zien welke acties de inrichtings service heeft uitgevoerd. In de controle logboeken worden alle afzonderlijke synchronisatie gebeurtenissen vermeld die worden uitgevoerd door de inrichtings service, zoals welke gebruikers worden geïmporteerd uit de bron en worden geëxporteerd naar de doel toepassing.  

5. Zodra de initiële synchronisatie is voltooid, wordt er een samenvattings rapport op het tabblad **inrichten** geschreven, zoals hieronder wordt weer gegeven.

     > [!div class="mx-imgBorder"]
     > ![Voortgangs balk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen werk dagen en Azure Active Directory](workday-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtings configuraties](../app-provisioning/export-import-provisioning-configuration.md)

