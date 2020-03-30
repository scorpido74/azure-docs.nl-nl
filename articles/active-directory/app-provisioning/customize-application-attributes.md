---
title: Azure AD-kenmerktoewijzingen aanpassen | Microsoft Documenten
description: Lees welke kenmerktoewijzingen voor SaaS-apps in Azure Active Directory zijn, zo u deze aanpassen om aan uw bedrijfsbehoeften te voldoen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7003899b59e409a785c3a50e89aae6674e377b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264087"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Gebruikersinrichting van kenmerktoewijzingen aanpassen voor SaaS-toepassingen in Azure Active Directory

Microsoft Azure AD biedt ondersteuning voor het inrichten van gebruikers aan SaaS-toepassingen van derden, zoals Salesforce, G Suite en anderen. Als u gebruikersvoorzieningen voor een SaaS-toepassing van derden inschakelt, bepaalt de Azure-portal de kenmerkenswaarden via kenmerktoewijzingen.

Er is een vooraf geconfigureerde set kenmerken en kenmerktoewijzingen tussen Azure AD-gebruikersobjecten en de gebruikersobjecten van elke SaaS-app. Sommige apps beheren andere typen objecten samen met gebruikers, zoals Groepen.

U de standaardtoewijzingen voor kenmerken aanpassen aan uw bedrijfsbehoeften. U dus bestaande kenmerktoewijzingen wijzigen of verwijderen of nieuwe kenmerktoewijzingen maken.

## <a name="editing-user-attribute-mappings"></a>Toewijzingen van gebruikerskenmerken bewerken

Volg de volgende stappen om toegang te krijgen tot de functie **Toewijzingen** van gebruikersinrichting:

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com).
1. Selecteer **Enterprise-toepassingen** in het linkerdeelvenster. Er wordt een lijst met alle geconfigureerde apps weergegeven, inclusief apps die vanuit de galerie zijn toegevoegd.
1. Selecteer een app om het app-beheervenster te laden, waar u rapporten bekijken en app-instellingen beheren.
1. Selecteer **Inrichten** om instellingen voor het inrichten van gebruikersaccounts voor de geselecteerde app te beheren.
1. **Toewijzingen** uitvouwen om de gebruikerskenmerken weer te geven en te bewerken die tussen Azure AD en de doeltoepassing stromen. Als de doeltoepassing dit ondersteunt, u in deze sectie optioneel de inrichting van groepen en gebruikersaccounts configureren.

   ![Toewijzingen gebruiken om gebruikerskenmerken weer te geven en te bewerken](./media/customize-application-attributes/21.png)

1. Selecteer een **toewijzingsconfiguratie** om het bijbehorende **scherm Toewijzing van kenmerken te** openen. Sommige attribuuttoewijzingen zijn vereist door een SaaS-toepassing om correct te functioneren. Voor vereiste kenmerken is de functie **Verwijderen** niet beschikbaar.

   ![Kenmerktoewijzing gebruiken om kenmerktoewijzingen voor apps te configureren](./media/customize-application-attributes/22.png)

   In deze schermafbeelding u zien dat het kenmerk **Gebruikersnaam** van een beheerd object in Salesforce wordt gevuld met de **userPrincipalName-waarde** van het gekoppelde Azure Active Directory-object.

1. Selecteer een bestaande **kenmerktoewijzing** om het scherm **Kenmerk bewerken te** openen. Hier u de gebruikerskenmerken bewerken die tussen Azure AD en de doeltoepassing stromen.

   ![Kenmerk bewerken gebruiken om gebruikerskenmerken te bewerken](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Kenmerken-toewijzingstypen begrijpen

Met kenmerktoewijzingen bepaalt u hoe kenmerken worden ingevuld in een SaaS-toepassing van derden.
Er worden vier verschillende kaarttypen ondersteund:

- **Direct** : het doelkenmerk wordt gevuld met de waarde van een kenmerk van het gekoppelde object in Azure AD.
- **Constant** : het doelkenmerk wordt gevuld met een specifieke tekenreeks die u hebt opgegeven.
- **Expressie** - het doelkenmerk wordt ingevuld op basis van het resultaat van een scriptachtige expressie.
  Zie [Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory voor](../app-provisioning/functions-for-customizing-application-data.md)meer informatie.
- **Geen** - het doelkenmerk blijft ongewijzigd. Als het doelkenmerk echter ooit leeg is, wordt het gevuld met de standaardwaarde die u opgeeft.

Samen met deze vier basistypen ondersteunen aangepaste kenmerktoewijzingen het concept van een optionele **standaardwaardetoewijzing.** De standaardwaardetoewijzing zorgt ervoor dat een doelkenmerk wordt gevuld met een waarde als er geen waarde is in Azure AD of op het doelobject. De meest voorkomende configuratie is om deze leeg te laten.

### <a name="understanding-attribute-mapping-properties"></a>Eigenschappen voor het toewijzen van kenmerken begrijpen

In de vorige sectie maakte u al kennis met de eigenschap kenmerktoewijzing.
Samen met deze eigenschap ondersteunen kenmerktoewijzingen ook de volgende kenmerken:

- **Bronkenmerk** - Het gebruikerskenmerk van het bronsysteem (bijvoorbeeld: Azure Active Directory).
- **Doelkenmerk** : het kenmerk van de gebruiker in het doelsysteem (voorbeeld: ServiceNow).
- **Standaardwaarde als null (optioneel)** - de waarde die wordt doorgegeven aan het doelsysteem als het bronkenmerk null is. Deze waarde wordt alleen ingerichte wanneer een gebruiker wordt gemaakt. De 'standaardwaarde wanneer null' wordt niet ingericht bij het bijwerken van een bestaande gebruiker. Als u bijvoorbeeld alle bestaande gebruikers in het doelsysteem wilt invoorzien van een bepaalde functietitel (wanneer deze niet in het bronsysteem staat), u de volgende [expressie](../app-provisioning/functions-for-customizing-application-data.md)gebruiken: Switch(IsPresent([jobTitle]), DefaultValue, True, [jobTitle]). Zorg ervoor dat u de standaardwaarde vervangt door wat u wilt inrichten wanneer null in het bronsysteem. 
- **Objecten overeenkomen met dit kenmerk** : of deze toewijzing moet worden gebruikt om gebruikers op unieke wijze te identificeren tussen de bron- en doelsystemen. Het is meestal ingesteld op het kenmerk userPrincipalName of e-mail in Azure AD, dat doorgaans is toegewezen aan een gebruikersnaamveld in een doeltoepassing.
- **Overeenkomende prioriteit** – Meerdere overeenkomende kenmerken kunnen worden ingesteld. Als er meerdere zijn, worden ze geëvalueerd in de volgorde die door dit veld is gedefinieerd. Zodra een overeenkomst wordt gevonden, worden geen verdere overeenkomende kenmerken geëvalueerd.
- **Deze toewijzing toepassen**
  - **Altijd** : deze toewijzing toepassen op zowel acties voor het maken van gebruikers als bij het bijwerken.
  - **Alleen tijdens het maken** - Pas deze toewijzing alleen toe op acties voor het maken van gebruikers.

## <a name="matching-users-in-the-source-and-target--systems"></a>Overeenkomende gebruikers in de bron- en doelsystemen
De Azure AD-inrichtingsservice kan worden geïmplementeerd in zowel 'greenfield'-scenario's (waarbij gebruikers niet worden afgesloten in het doelsysteem) als 'brownfield'-scenario's (waarbij gebruikers al bestaan in het doelsysteem). Om beide scenario's te ondersteunen, gebruikt de inrichtingsservice het concept van overeenkomende kenmerken. Met overeenkomende kenmerken u bepalen hoe u een gebruiker in de bron op unieke wijze identificeren en de gebruiker in het doel matchen. Als onderdeel van het plannen van uw implementatie, identificeert u het kenmerk dat kan worden gebruikt om een gebruiker op unieke wijze te identificeren in de bron- en doelsystemen. Dingen om op te merken:

- **Overeenkomende kenmerken moeten uniek zijn:** Klanten gebruiken vaak kenmerken zoals userPrincipalName, mail of object ID als het overeenkomende kenmerk.
- **Meerdere kenmerken kunnen worden gebruikt als overeenkomende kenmerken:** U meerdere kenmerken definiëren die moeten worden geëvalueerd bij het matchen van gebruikers en de volgorde waarin ze worden geëvalueerd (gedefinieerd als overeenkomende prioriteit in de gebruikersinterface). Als u bijvoorbeeld drie kenmerken definieert als overeenkomende kenmerken en een gebruiker uniek is na evaluatie van de eerste twee kenmerken, evalueert de service het derde kenmerk niet. De service evalueert overeenkomende kenmerken in de opgegeven volgorde en stopt met evalueren wanneer een overeenkomst wordt gevonden.  
- **De waarde in de bron en het doel hoeft niet exact overeen te komen:** De waarde in het doel kan een eenvoudige functie zijn van de waarde in de bron. Men kan dus een e-mailAdreskenmerk in de bron en de userPrincipalName in het doel hebben en overeenkomen met een functie van het kenmerk emailAddress dat sommige tekens vervangt door een constante waarde.  
- **Matching op basis van een combinatie van kenmerken wordt niet ondersteund:** De meeste toepassingen ondersteunen query's niet op basis van twee eigenschappen. Daarom is het niet mogelijk om te matchen op basis van een combinatie van kenmerken. Het is mogelijk om afzonderlijke eigenschappen op na elkaar te evalueren.
- **Alle gebruikers moeten een waarde hebben voor ten minste één overeenkomend kenmerk:** Als u één overeenkomend kenmerk definieert, moeten alle gebruikers een waarde voor dat kenmerk in het bronsysteem hebben. Als u bijvoorbeeld userPrincipalName definieert als het overeenkomende kenmerk, moeten alle gebruikers een userPrincipalName hebben. Als u meerdere overeenkomende kenmerken definieert (bijvoorbeeld extensieAttribuut1 en e-mail), hoeven niet alle gebruikers hetzelfde overeenkomende kenmerk te hebben. Een gebruiker kan een extensieAttribute1 hebben, maar niet e-mail, terwijl een andere gebruiker kan e-mail hebben, maar geen extensieAttribute1. 
- **De doeltoepassing moet filtering op het overeenkomende kenmerk ondersteunen:** Toepassingsontwikkelaars staan het filteren van een subset van kenmerken op hun gebruikers- of groeps-API toe. Voor toepassingen in de galerie zorgen we ervoor dat de standaardtoewijzing van kenmerken is voor een kenmerk waarop de API van de doeltoepassing filtert. Controleer bij het wijzigen van het standaardkenmerk voor de doeltoepassing de API-documentatie van derden om te controleren of het kenmerk kan worden gefilterd.  

## <a name="editing-group-attribute-mappings"></a>Kenmerkentoewijzingen voor groepen bewerken

Een geselecteerd aantal toepassingen, zoals ServiceNow, Box en G Suite, ondersteunt de mogelijkheid om groepsobjecten en gebruikersobjecten in te richten. Groepsobjecten kunnen groepseigenschappen bevatten, zoals weergavenamen en e-mailaliassen, samen met groepsleden.

![Voorbeeld toont ServiceNow met ingerichte groeps- en gebruikersobjecten](./media/customize-application-attributes/24.png)

Groepsinrichting kan optioneel worden ingeschakeld of uitgeschakeld door de groepstoewijzing te selecteren onder **Toewijzingen**en ingeschakeld in te **stellen op** de gewenste optie in het scherm Toewijzing **van kenmerken.**

De kenmerken die zijn ingericht als onderdeel van groepsobjecten, kunnen op dezelfde manier worden aangepast als gebruikersobjecten die eerder zijn beschreven. 

> [!TIP]
> Het inrichten van groepsobjecten (eigenschappen en leden) is een verschillend concept van [het toewijzen van groepen](../manage-apps/assign-user-or-group-access-portal.md) aan een toepassing. Het is mogelijk om een groep aan een toepassing toe te wijzen, maar alleen de gebruikersobjecten in de groep in te richten. Het inrichten van volledige groepsobjecten is niet vereist om groepen in toewijzingen te gebruiken.

## <a name="editing-the-list-of-supported-attributes"></a>De lijst met ondersteunde kenmerken bewerken

De gebruikerskenmerken die voor een bepaalde toepassing worden ondersteund, zijn vooraf geconfigureerd. De meeste API's voor gebruikersbeheer van de meeste toepassingen ondersteunen geen schemadetectie. De Azure AD-inrichtingsservice kan de lijst met ondersteunde kenmerken dus niet dynamisch genereren door naar de toepassing te bellen.

Sommige toepassingen ondersteunen echter aangepaste kenmerken en de Azure AD-inrichtingsservice kan lezen en schrijven naar aangepaste kenmerken. Als u hun definities wilt invoeren in de Azure-portal, schakelt u het selectievakje **Geavanceerde opties weergeven** onder aan het scherm **Kenmerktoewijzing** in en selecteert u **de lijst met kenmerken bewerken voor** uw app.

Toepassingen en systemen die aanpassing van de kenmerklijst ondersteunen, zijn onder andere:

- SalesForce
- ServiceNow
- Workday
- Azure Active Directory[(Microsoft Graph REST API v1.0-verwijzing](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) en aangepaste directory-extensies worden ondersteund)
- Apps die [SCIM 2.0](https://tools.ietf.org/html/rfc7643)ondersteunen , waarbij kenmerken die in het [kernschema](https://tools.ietf.org/html/rfc7643) zijn gedefinieerd, moeten worden toegevoegd

> [!NOTE]
> Het bewerken van de lijst met ondersteunde kenmerken wordt alleen aanbevolen voor beheerders die het schema van hun toepassingen en systemen hebben aangepast en uit de eerste hand weten hoe hun aangepaste kenmerken zijn gedefinieerd. Dit vereist soms vertrouwdheid met de API's en ontwikkeltools die door een toepassing of systeem worden geleverd.

Bij het bewerken van de lijst met ondersteunde kenmerken worden de volgende eigenschappen weergegeven:

- **Naam** - De systeemnaam van het kenmerk, zoals gedefinieerd in het schema van het doelobject.
- **Type** : het type gegevens dat het kenmerk opslaat, zoals gedefinieerd in het schema van het doelobject, dat een van de volgende typen kan zijn:
  - *Binair* - Kenmerk bevat binaire gegevens.
  - *Booleaan* - Kenmerk bevat een waarde waar of onwaar.
  - *DateTime* - Kenmerk bevat een datumtekenreeks.
  - *Geheel getal* - Kenmerk bevat een geheel getal.
  - *Verwijzing* - Kenmerk bevat een id die verwijst naar een waarde die is opgeslagen in een andere tabel in de doeltoepassing.
  - *Tekenreeks* - Kenmerk bevat een tekenreeks.
- **Primaire sleutel?** - Of het kenmerk wordt gedefinieerd als een primair sleutelveld in het schema van het doelobject.
- **Vereist?** - Of het kenmerk moet worden ingevuld in de doeltoepassing of het systeem.
- **Multi-value?** - Of het kenmerk meerdere waarden ondersteunt.
- **Exacte zaak?** - Of de kenmerkenwaarden op een casegevoelige manier worden geëvalueerd.
- **API-expressie** - Gebruik dit niet, tenzij de instructies daartoe zijn opgedragen door de documentatie voor een specifieke inrichtingsconnector (zoals Werkdag).
- **Objectkenmerk naverwezen** - Als het een kenmerk van het type Verwijzing is, u in dit menu de tabel en het kenmerk selecteren in de doeltoepassing die de waarde bevat die aan het kenmerk is gekoppeld. Als u bijvoorbeeld een kenmerk met de naam 'Afdeling' hebt waarvan de opgeslagen waarde verwijst naar een object in een afzonderlijke tabel 'Afdelingen', selecteert u 'Departments.Name'. De referentietabellen en de primaire ID-velden die voor een bepaalde toepassing worden ondersteund, zijn vooraf geconfigureerd en kunnen momenteel niet worden bewerkt met de Azure-portal, maar kunnen worden bewerkt met behulp van de [Microsoft Graph API.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Een aangepast extensiekenmerk inrichten op een SCIM-compatibele toepassing
De SCIM RFC definieert een kerngebruikers- en groepsschema, terwijl het ook mogelijk is voor extensies van het schema om aan de behoeften van uw toepassing te voldoen. Ga als u een aangepast kenmerk toevoegt aan een SCIM-toepassing:
   1. Meld u aan bij de [Azure Active Directory-portal,](https://aad.portal.azure.com)selecteer **Enterprise-toepassingen,** selecteer uw toepassing en selecteer **Vervolgens Inrichten**.
   2. Selecteer **onder Toewijzingen**het object (gebruiker of groep) waarvoor u een aangepast kenmerk wilt toevoegen.
   3. Selecteer onder aan de pagina **Geavanceerde opties weergeven**.
   4. Selecteer **Lijst met kenmerk bewerken voor AppName**.
   5. Voer onder aan de lijst met kenmerken informatie in over het aangepaste kenmerk in de velden die worden verstrekt. Selecteer vervolgens **Kenmerk toevoegen**.

Voor SCIM-toepassingen moet de kenmerknaam het patroon volgen dat in het onderstaande voorbeeld wordt weergegeven. De "CustomExtensionName" en "CustomAttribute" kunnen worden aangepast aan de vereisten van uw toepassing, bijvoorbeeld: urn:ietf:params:scim:schema's:extension:2.0:CustomExtensionName:CustomAttribute of urn:ietf:params:scim:schema's:extension: CustomExtensionName:2.0:User.CustomAttributeName:waarde

Deze instructies zijn alleen van toepassing op toepassingen met SCIM-toepassingen. Toepassingen zoals ServiceNow en Salesforce zijn niet geïntegreerd met Azure AD met SCIM en hebben daarom deze specifieke naamruimte niet nodig bij het toevoegen van een aangepast kenmerk.

Aangepaste kenmerken mogen geen referentiële kenmerken of multi-value kenmerken zijn. Aangepaste extensiekenmerken met meerdere waarden worden momenteel alleen ondersteund voor toepassingen in de galerie.  
 
**Voorbeeldweergave van een gebruiker met een extensiekenmerk:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Een rol inrichten voor een SCIM-app
Gebruik de onderstaande stappen om rollen voor een gebruiker in te richten op uw toepassing. Houd er rekening mee dat de onderstaande beschrijving specifiek is voor aangepaste SCIM-toepassingen. Gebruik voor galerietoepassingen zoals Salesforce en ServiceNow de vooraf gedefinieerde roltoewijzingen. In de onderstaande opsommingstekens wordt beschreven hoe u het kenmerk AppRoleAssignments transformeren naar de indeling die uw toepassing verwacht.

- Een appRoleAssignment in Azure AD toewijzen aan een rol in uw toepassing vereist dat u het kenmerk transformeert met een [expressie](../app-provisioning/functions-for-customizing-application-data.md). Het kenmerk appRoleAssignment **mag niet rechtstreeks aan** een rolkenmerk worden toegewezen zonder een expressie te gebruiken om de roldetails te ontken. 

- **SingleAppRoleAssignment (SingleAppRoleAssignment)** 
  - **Wanneer te gebruiken:** Gebruik de expressie SingleAppRoleAssignment om één rol voor een gebruiker in te richten en de primaire rol op te geven. 
  - **Configureren:** Gebruik de hierboven beschreven stappen om naar de pagina toewijzingen van kenmerken te navigeren en gebruik de expressie SingleAppRoleAssignment om het kenmerk rollen toe te wijzen. Er zijn drie rolkenmerken om uit te kiezen: (rollen[primaire eq "True"].display, rollen[primaire eq "True].type en rollen[primaire eq "True"].value). U ervoor kiezen om een of alle rolkenmerken in uw toewijzingen op te nemen. Als u er meer dan één wilt opnemen, voegt u gewoon een nieuwe toewijzing toe en neemt u deze op als doelkenmerk.  
  
  ![SingleAppRoleAssignment toevoegen](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Dingen om te overwegen**
    - Controleer of meerdere rollen niet aan een gebruiker zijn toegewezen. Wij kunnen niet garanderen welke rol zal worden ingenomen.
    
  - **Voorbeelduitvoer** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **Wanneer te gebruiken:** Gebruik de expressie AppRoleAssignmentsComplex om meerdere rollen voor een gebruiker in te richten. 
  - **Configureren:** Bewerk de lijst met ondersteunde kenmerken zoals hierboven beschreven om een nieuw kenmerk voor rollen op te nemen: 
  
    ![Functies toevoegen](./media/customize-application-attributes/add-roles.png)<br>

    Gebruik vervolgens de expressie AppRoleAssignmentsComplex om het aangepaste rolkenmerk zoals in de onderstaande afbeelding te toewijzen:

    ![AppRoleAssignmentsComplex toevoegen](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Dingen om te overwegen**
    - Alle rollen worden ingericht als primair = onwaar.
    - De POST bevat het roltype. De PATCH-aanvraag bevat geen type. We werken aan het verzenden van het type in zowel POST en PATCH verzoeken.
    
  - **Voorbeelduitvoer** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Een kenmerk met meerdere waarde inrichten
Bepaalde kenmerken zoals telefoonnummers en e-mails zijn multi-value attributen waar u mogelijk verschillende soorten telefoonnummers of e-mails moet opgeven. Gebruik de onderstaande expressie voor kenmerken met meerdere waarden. Hiermee u het kenmerktype opgeven en deze toewijzen aan het bijbehorende Azure AD-gebruikerskenmerk voor de waarde. 

* phoneNumbers[type eq "werk"].waarde
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>De standaardkenmerken en toewijzingen van kenmerken herstellen

Als u opnieuw wilt beginnen en uw bestaande toewijzingen opnieuw wilt instellen naar de standaardstatus, u het selectievakje **Standaardtoewijzingen herstellen** en de configuratie opslaan. Hiermee worden alle toewijzingen en scopingfilters ingesteld alsof de toepassing zojuist vanuit de toepassingsgalerie aan uw Azure AD-tenant is toegevoegd.

Als u deze optie selecteert, wordt een resynchronisatie van alle gebruikers gedwongen terwijl de inrichtingsservice wordt uitgevoerd.

> [!IMPORTANT]
> We raden u ten zeerste aan de **provisioning-status** in te stellen op **Uit** voordat u een beroep doet op deze optie.

## <a name="what-you-should-know"></a>Wat u moet weten

- Microsoft Azure AD biedt een efficiënte implementatie van een synchronisatieproces. In een geïnitialiseerde omgeving worden alleen objecten verwerkt die updates nodig hebben tijdens een synchronisatiecyclus.
- Het bijwerken van kenmerktoewijzingen heeft een impact op de prestaties van een synchronisatiecyclus. Voor een update van de configuratie van de kenmerktoewijzing moeten alle beheerde objecten opnieuw worden geëvalueerd.
- Een aanbevolen aanbevolen best practice is om het aantal opeenvolgende wijzigingen in uw kenmerktoewijzingen tot een minimum te beperken.
- Het toevoegen van een fotokenmerk dat aan een app moet worden ingericht, wordt vandaag niet ondersteund omdat u de indeling niet opgeven om de foto te synchroniseren. U de functie aanvragen op [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- Het kenmerk IsSoftDeleted maakt vaak deel uit van de standaardtoewijzingen voor een toepassing. IsSoftdeleted kan waar zijn in een van de vier scenario's (de gebruiker is buiten bereik vanwege het feit dat deze niet is toegewezen aan de toepassing, de gebruiker buiten het bereik is vanwege het niet voldoen aan een scopingfilter, de gebruiker is zacht verwijderd in Azure AD of de eigenschap AccountEnabled is ingesteld op false op de gebruiker). 
- De Azure AD-inrichtingsservice biedt geen ondersteuning voor het inrichten van null-waarden

## <a name="next-steps"></a>Volgende stappen

- [Gebruikersvoorziening/deprovisioning automatiseren naar SaaS-apps](user-provisioning.md)
- [Expressies schrijven voor kenmerktoewijzingen](../app-provisioning/functions-for-customizing-application-data.md)
- [Scoping-filters voor gebruikersinrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
- [Lijst met handleidingen voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
