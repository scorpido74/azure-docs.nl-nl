---
title: Azure AD-kenmerk toewijzingen aanpassen | Microsoft Docs
description: Meer informatie over kenmerk toewijzingen voor SaaS-apps in Azure Active Directory zijn de manier waarop u ze kunt aanpassen om uw bedrijfs behoeften aan te pakken.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: kenwith
ms.openlocfilehash: 666c4e52ed521c169ff80b33e2ab0e83b13e4d03
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266697"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Kenmerk toewijzingen voor het inrichten van gebruikers aanpassen voor SaaS-toepassingen in Azure Active Directory

Microsoft Azure AD biedt ondersteuning voor het inrichten van gebruikers voor SaaS-toepassingen van derden, zoals Sales Force, G suite en anderen. Als u het inrichten van gebruikers inschakelt voor een SaaS-toepassing van derden, bepaalt de Azure Portal de kenmerk waarden via kenmerk toewijzingen.

Er is een vooraf geconfigureerde set met kenmerken en kenmerk toewijzingen tussen Azure AD-gebruikers objecten en de gebruikers objecten van de SaaS-app. Sommige apps beheren andere typen objecten samen met gebruikers, zoals groepen.

U kunt de standaard kenmerk toewijzingen aanpassen op basis van de behoeften van uw bedrijf. U kunt dus bestaande kenmerk toewijzingen wijzigen of verwijderen, of nieuwe kenmerk toewijzingen maken.

## <a name="editing-user-attribute-mappings"></a>Gebruikers kenmerk-toewijzingen bewerken

Volg deze stappen om toegang te krijgen tot de functie **toewijzingen** van het inrichten van gebruikers:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com).
1. Selecteer **bedrijfs toepassingen** in het linkerdeel venster. Er wordt een lijst met alle geconfigureerde apps weer gegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
1. Selecteer een app om het deel venster app-beheer te laden. hier kunt u rapporten bekijken en app-instellingen beheren.
1. Selecteer **inrichting** om de instellingen voor het inrichten van gebruikers accounts voor de geselecteerde app te beheren.
1. Vouw **toewijzingen** uit om de gebruikers kenmerken weer te geven en te bewerken die tussen Azure AD en de doel toepassing stroom gaan. Als de doel toepassing deze ondersteunt, kunt u in deze sectie desgewenst het inrichten van groepen en gebruikers accounts configureren.

   ![Toewijzingen gebruiken om gebruikers kenmerken weer te geven en te bewerken](./media/customize-application-attributes/21.png)

1. Selecteer een configuratie **toewijzingen** om het scherm gerelateerde **kenmerk toewijzing** te openen. Sommige kenmerk toewijzingen zijn vereist voor een juiste werking van een SaaS-toepassing. Voor vereiste kenmerken is de functie **verwijderen** niet beschikbaar.

   ![Kenmerk toewijzing gebruiken om kenmerk toewijzingen voor apps te configureren](./media/customize-application-attributes/22.png)

   In deze scherm afbeelding ziet u dat het kenmerk **username** van een beheerd object in Sales Force wordt gevuld met de waarde **userPrincipalName** van het gekoppelde Azure Active Directory-object.

1. Selecteer een bestaande **kenmerk toewijzing** om het scherm **kenmerk bewerken** te openen. Hier kunt u de gebruikers kenmerken bewerken die worden gestroomd tussen Azure AD en de doel toepassing.

   ![Kenmerk bewerken gebruiken om gebruikers kenmerken te bewerken](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informatie over kenmerk toewijzings typen

Met kenmerk toewijzingen bepaalt u hoe kenmerken worden ingevuld in een SaaS-toepassing van derden.
Er worden vier verschillende toewijzings typen ondersteund:

- **Direct** : het doel kenmerk wordt gevuld met de waarde van een kenmerk van het gekoppelde object in azure AD.
- **Constante** : het doel kenmerk wordt ingevuld met een specifieke teken reeks die u hebt opgegeven.
- **Expressie** -het doel kenmerk wordt ingevuld op basis van het resultaat van een script achtige expressie.
  Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)voor meer informatie.
- **Geen** -het doel kenmerk blijft ongewijzigd. Als het doel kenmerk echter ooit leeg is, wordt het ingevuld met de standaard waarde die u opgeeft.

Naast deze vier basis typen ondersteunen aangepaste kenmerk toewijzingen het concept van een optionele **standaard** waarde voor toewijzingen. De toewijzing van de standaard waarde zorgt ervoor dat een doel kenmerk wordt gevuld met een waarde als er geen waarde in azure AD of op het doel object is. De meest voorkomende configuratie is om dit leeg te laten.

### <a name="understanding-attribute-mapping-properties"></a>Informatie over kenmerk toewijzings eigenschappen

In de vorige sectie hebt u al een kenmerk-toewijzings type-eigenschap geïntroduceerd.
Naast deze eigenschap ondersteunen kenmerk toewijzingen ook de volgende kenmerken:

- **Bron kenmerk** : het gebruikers kenmerk van het bron systeem (voor beeld: Azure Active Directory).
- **Doel kenmerk** : het gebruikers kenmerk in het doel systeem (voor beeld: ServiceNow).
- **Standaard waarde indien Null (optioneel)** : de waarde die wordt door gegeven aan het doel systeem als het bron kenmerk null is. Deze waarde wordt alleen ingericht wanneer een gebruiker wordt gemaakt. De ' standaard waarde wanneer null ' wordt niet ingericht bij het bijwerken van een bestaande gebruiker. Als u bijvoorbeeld alle bestaande gebruikers in het doel systeem met een bepaalde functie titel wilt inrichten (als deze null is in het bron systeem), kunt u de volgende [expressie](../app-provisioning/functions-for-customizing-application-data.md)gebruiken: switch (IsPresent ([jobTitle]), "DefaultValue", "True", [jobtitle]). Zorg ervoor dat u de "standaard waarde" vervangt door de inhoud die u wilt inrichten als deze null is in het bron systeem. 
- **Objecten met dit kenmerk matchen** : bepaalt of deze toewijzing moet worden gebruikt om gebruikers tussen de bron-en doel systemen uniek te identificeren. Deze is doorgaans ingesteld op het kenmerk userPrincipalName of mail in azure AD, dat meestal wordt toegewezen aan een gebruikersnaam veld in een doel toepassing.
- **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd. U kunt zoveel overeenkomende kenmerken instellen als u wilt, u kunt overwegen of de kenmerken die u als overeenkomende kenmerken gebruikt, echt uniek zijn en moeten overeenkomen met kenmerken. Over het algemeen hebben klanten 1 of 2 overeenkomende kenmerken in hun configuratie. 
- **Deze toewijzing Toep assen**
  - **Altijd** : deze toewijzing Toep assen op acties voor zowel het maken van de gebruiker als bij het bijwerken.
  - **Alleen tijdens het maken** : pas deze toewijzing alleen toe op acties voor het maken van een gebruiker.

## <a name="matching-users-in-the-source-and-target--systems"></a>Overeenkomende gebruikers in de bron-en doel systemen
De Azure AD-inrichtings service kan worden geïmplementeerd in zowel ' ontwikkel-scenario's (waar gebruikers niet worden afgesloten in het doel systeem) als ' Brownfield-scenario's (waar gebruikers al bestaan in het doel systeem). Ter ondersteuning van beide scenario's gebruikt de inrichtings service het concept van overeenkomende kenmerken. Met overeenkomende kenmerken kunt u bepalen hoe een gebruiker in de bron uniek wordt geïdentificeerd en overeenkomt met de gebruiker in het doel. Als onderdeel van het plannen van uw implementatie, identificeert u het kenmerk dat kan worden gebruikt om een gebruiker op de bron-en doel systemen uniek te identificeren. Denk aan het volgende:

- **Overeenkomende kenmerken moeten uniek zijn:** Klanten gebruiken vaak kenmerken als userPrincipalName, mail of object-ID als het overeenkomstige kenmerk.
- **Meerdere kenmerken kunnen worden gebruikt als overeenkomende kenmerken:** U kunt meerdere kenmerken definiëren die moeten worden geëvalueerd wanneer overeenkomende gebruikers en de volg orde waarin ze worden geëvalueerd (gedefinieerd als overeenkomende prioriteit in de gebruikers interface). Als u bijvoorbeeld drie kenmerken definieert als overeenkomende kenmerken en een gebruiker heeft een unieke overeenkomst na het evalueren van de eerste twee kenmerken, wordt het derde kenmerk niet geëvalueerd door de service. De service evalueert overeenkomende kenmerken in de opgegeven volg orde en stopt met de evaluatie wanneer er een overeenkomst wordt gevonden.  
- **De waarde in de bron en het doel hoeven niet exact overeen te komen:** De waarde in het doel kan een eenvoudige functie van de waarde in de bron zijn. Een voor waarde kan dus een kenmerk emailAddress hebben in de bron en de userPrincipalName in het doel en overeenkomen met een functie van het kenmerk emailAddress, waarmee een aantal tekens wordt vervangen door een constantewaarde.  
- **Het vergelijken op basis van een combi natie van kenmerken wordt niet ondersteund:** De meeste toepassingen ondersteunen query's op basis van twee eigenschappen niet. Daarom is het niet mogelijk om te vergelijken op basis van een combi natie van kenmerken. Het is mogelijk om afzonderlijke eigenschappen te evalueren op na een andere.
- **Alle gebruikers moeten een waarde hebben voor ten minste één overeenkomend kenmerk:** Als u één overeenkomend kenmerk definieert, moeten alle gebruikers een waarde voor dat kenmerk in het bron systeem hebben. Als u bijvoorbeeld userPrincipalName definieert als het overeenkomende kenmerk, moeten alle gebruikers een userPrincipalName hebben. Als u meerdere overeenkomende kenmerken definieert (bijvoorbeeld extensionAttribute1 en e-mail), moeten niet alle gebruikers hetzelfde overeenkomende kenmerk hebben. Een gebruiker kan een extensionAttribute1 maar geen e-mail hebben terwijl een andere gebruiker e-mail berichten heeft maar geen extensionAttribute1 heeft. 
- **De doel toepassing moet filteren op het overeenkomende kenmerk ondersteunen:** Toepassings ontwikkelaars kunnen filteren op een subset van kenmerken voor hun gebruikers-of groeps-API. Voor toepassingen in de galerie zorgen we ervoor dat de standaard kenmerk toewijzing een kenmerk is dat de API van de doel toepassing filtering ondersteunt. Wanneer u het standaard overeenkomende kenmerk voor de doel toepassing wijzigt, raadpleegt u de documentatie van de API van derden om te controleren of het kenmerk kan worden gefilterd.  

## <a name="editing-group-attribute-mappings"></a>Groeps kenmerk-toewijzingen bewerken

Een geselecteerd aantal toepassingen, zoals ServiceNow, box en G suite, ondersteunt de mogelijkheid om groeps objecten en gebruikers objecten in te richten. Groeps objecten kunnen groeps eigenschappen bevatten, zoals weergave namen en e-mail aliassen, samen met groeps leden.

![Voor beeld toont ServiceNow met ingerichte groeps-en gebruikers objecten](./media/customize-application-attributes/24.png)

Groeps inrichting kan optioneel worden in-of uitgeschakeld door de groeps toewijzing onder **toewijzingen**te selecteren en in **te scha kelen op** de gewenste optie in het scherm **kenmerk toewijzing** .

De kenmerken die als onderdeel van groeps objecten worden ingericht, kunnen op dezelfde manier worden aangepast als gebruikers objecten, zoals eerder beschreven. 

> [!TIP]
> Het inrichten van groeps objecten (eigenschappen en leden) is een afzonderlijk concept van het [toewijzen van groepen](../manage-apps/assign-user-or-group-access-portal.md) aan een toepassing. U kunt een groep toewijzen aan een toepassing, maar alleen de gebruikers objecten inrichten die deel uitmaken van de groep. Het inrichten van volledige groeps objecten is niet vereist voor het gebruik van groepen in toewijzingen.

## <a name="editing-the-list-of-supported-attributes"></a>De lijst met ondersteunde kenmerken bewerken

De gebruikers kenmerken die worden ondersteund voor een bepaalde toepassing, zijn vooraf geconfigureerd. De gebruikers beheer-Api's van de meeste toepassingen bieden geen ondersteuning voor schema detectie. De Azure AD-inrichtings service kan de lijst met ondersteunde kenmerken dus niet dynamisch genereren door aanroepen van de toepassing.

Sommige toepassingen ondersteunen echter aangepaste kenmerken en de Azure AD-inrichtings service kan lees-en schrijf bewerkingen naar aangepaste kenmerken. Als u hun definities wilt invoeren in de Azure Portal, schakelt u het selectie vakje **Geavanceerde opties weer geven** onder in het scherm **kenmerk toewijzing** in en selecteert u **kenmerk lijst bewerken voor** uw app.

Toepassingen en systemen die ondersteuning bieden voor aanpassing van de lijst met kenmerken zijn onder andere:

- SalesForce
- ServiceNow
- Workday
- Azure Active Directory ([standaard kenmerken van Azure AD Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) en aangepaste Directory-extensies worden ondersteund)
- Apps die ondersteuning bieden voor [SCIM 2,0](https://tools.ietf.org/html/rfc7643), waarbij kenmerken die zijn gedefinieerd in het [kern schema](https://tools.ietf.org/html/rfc7643) , moeten worden toegevoegd

> [!NOTE]
> Het bewerken van de lijst met ondersteunde kenmerken wordt alleen aanbevolen voor beheerders die het schema van hun toepassingen en systemen hebben aangepast, en die over de eerste kennis beschikken over hoe hun aangepaste kenmerken zijn gedefinieerd. Dit vereist soms vertrouwd met de Api's en ontwikkel hulpprogramma's van een toepassing of systeem.

Bij het bewerken van de lijst met ondersteunde kenmerken, worden de volgende eigenschappen weer gegeven:

- **Name** -de systeem naam van het kenmerk, zoals gedefinieerd in het schema van het doel object.
- **Type** : het type gegevens dat het kenmerk opslaat, zoals gedefinieerd in het schema van het doel object, wat een van de volgende typen kan zijn:
  - *Binary* -kenmerk bevat binaire gegevens.
  - *Booleaans* -kenmerk bevat een waarde waar of onwaar.
  - *DateTime* -kenmerk bevat een datum teken reeks.
  - Het kenmerk *Integer* bevat een geheel getal.
  - Het *referentie* kenmerk bevat een id die verwijst naar een waarde die is opgeslagen in een andere tabel in de doel toepassing.
  - *String*  -kenmerk bevat een teken reeks.
- **Primaire sleutel?** -Of het kenmerk wordt gedefinieerd als een primaire-sleutel veld in het schema van het doel object.
- **Vereist?** -Of het kenmerk moet worden ingevuld in de doel toepassing of het systeem.
- **Meerdere waarden?** -Of het kenmerk meerdere waarden ondersteunt.
- **Wat is er precies?** -Of de kenmerken waarden in een hoofdletter gevoelige manier worden geëvalueerd.
- **API-expressie** : gebruik deze, tenzij dit wordt gedaan door de documentatie voor een specifieke inrichtings connector (zoals workday).
- **Object kenmerk waarnaar wordt verwezen** : als het een verwijzings type kenmerk is, kunt u in dit menu de tabel en het kenmerk selecteren in de doel toepassing die de waarde bevat die aan het kenmerk is gekoppeld. Als u bijvoorbeeld een kenmerk met de naam ' Department ' hebt waarvan de opgeslagen waarde verwijst naar een object in een afzonderlijke tabel ' departments ', selecteert u ' Departments.Name '. De referentie tabellen en de primaire ID-velden die worden ondersteund voor een bepaalde toepassing, zijn vooraf geconfigureerd en kunnen momenteel niet worden bewerkt met de Azure Portal, maar u kunt deze bewerken met behulp van de [Microsoft Graph-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Een aangepast uitbreidings kenmerk inrichten voor een SCIM-compatibele toepassing
De SCIM-RFC definieert een kern gebruikers-en groeps schema, en biedt ook uitbrei dingen voor het schema om te voldoen aan de behoeften van uw toepassing. Een aangepast kenmerk toevoegen aan een SCIM-toepassing:
   1. Meld u aan bij de [Azure Active Directory-Portal](https://aad.portal.azure.com), selecteer **zakelijke toepassingen**, selecteer uw toepassing en selecteer vervolgens **inrichting**.
   2. Selecteer onder **toewijzingen**het object (gebruiker of groep) waarvoor u een aangepast kenmerk wilt toevoegen.
   3. Selecteer onder aan de pagina **Geavanceerde opties weer geven**.
   4. Selecteer **kenmerk lijst bewerken voor appName**.
   5. Typ onder aan de lijst met kenmerken informatie over het aangepaste kenmerk in de opgegeven velden. Selecteer vervolgens **kenmerk toevoegen**.

Voor SCIM-toepassingen moet de naam van het kenmerk voldoen aan het patroon dat in het voor beeld hieronder wordt weer gegeven. De ' CustomExtensionName ' en ' CustomAttribute ' kunnen worden aangepast volgens de vereisten van uw toepassing, bijvoorbeeld:  
 * urn: IETF: params: scim: schemas: extension: CustomExtensionName: 2.0: gebruiker: CustomAttribute 
 * urn: IETF: params: scim: schemas: extension: 2.0: CustomExtensionName: CustomAttribute  
 * urn: IETF: params: scim: schemas: extension: CustomExtensionName: 2.0: User. CustomAttributeName: waarde

Deze instructies zijn alleen van toepassing op SCIM-toepassingen. Toepassingen zoals ServiceNow en Sales Force zijn niet geïntegreerd met Azure AD met behulp van SCIM. deze specifieke naam ruimte is daarom niet vereist bij het toevoegen van een aangepast kenmerk.

Aangepaste kenmerken kunnen geen referentiële kenmerken of kenmerken met meerdere waarden zijn. Aangepaste extensie kenmerken voor meerdere waarden worden momenteel alleen ondersteund voor toepassingen in de galerie.  
 
**Voor beeld van een gebruiker met een extensie kenmerk:**

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
Volg de onderstaande stappen om rollen voor een gebruiker in te richten voor uw toepassing. Houd er rekening mee dat de onderstaande beschrijving specifiek is voor aangepaste SCIM-toepassingen. Gebruik voor galerie toepassingen zoals Sales Force en ServiceNow de vooraf gedefinieerde roltoewijzingen. In de onderstaande opsommings tekens wordt beschreven hoe u het kenmerk AppRoleAssignments transformeert naar de indeling die uw toepassing verwacht.

- Als u een appRoleAssignment in azure AD wilt toewijzen aan een rol in uw toepassing, moet u het kenmerk transformeren met behulp van een [expressie](../app-provisioning/functions-for-customizing-application-data.md). Het kenmerk appRoleAssignment **mag niet rechtstreeks worden toegewezen** aan een Role-kenmerk zonder een expressie te gebruiken om de functie details te parseren. 

- **SingleAppRoleAssignment** 
  - **Wanneer gebruiken:** Gebruik de SingleAppRoleAssignment-expressie om één rol voor een gebruiker in te richten en de primaire functie op te geven. 
  - **Configureren:** Volg de stappen die hierboven worden beschreven om naar de pagina kenmerk toewijzingen te gaan en gebruik de SingleAppRoleAssignment-expressie om toe te wijzen aan het kenmerk roles. Er zijn drie kenmerken waaruit u kunt kiezen: (rollen [Primary EQ "True"]. weer gave, rollen [Primary EQ "True]. type en roles [Primary EQ" True "]. Value). U kunt ervoor kiezen om een of alle functie kenmerken in uw toewijzingen op te neemt. Als u meer dan één wilt gebruiken, voegt u gewoon een nieuwe toewijzing toe en neemt u deze op als doel kenmerk.  
  
  ![SingleAppRoleAssignment toevoegen](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Aandachtspunten**
    - Zorg ervoor dat er geen meerdere rollen aan een gebruiker zijn toegewezen. We kunnen niet garanderen welke rol zal worden ingericht.
    
  - **Voorbeeld uitvoer** 

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
  - **Wanneer gebruiken:** Gebruik de AppRoleAssignmentsComplex-expressie om meerdere rollen voor een gebruiker in te richten. 
  - **Configureren:** Bewerk de lijst met ondersteunde kenmerken, zoals hierboven wordt beschreven, om een nieuw kenmerk op te geven voor rollen: 
  
    ![Functies toevoegen](./media/customize-application-attributes/add-roles.png)<br>

    Gebruik vervolgens de AppRoleAssignmentsComplex-expressie om toe te wijzen aan het aangepaste Role-kenmerk, zoals wordt weer gegeven in de onderstaande afbeelding:

    ![AppRoleAssignmentsComplex toevoegen](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Aandachtspunten**
    - Alle rollen worden ingericht als primair = onwaar.
    - Het bericht bevat het type rol. De PATCH-aanvraag bevat geen type. Er wordt gewerkt aan het verzenden van het type in POST-en PATCH-aanvragen.
    
  - **Voorbeeld uitvoer** 
  
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

  


## <a name="provisioning-a-multi-value-attribute"></a>Een kenmerk met meerdere waarden inrichten
Bepaalde kenmerken, zoals phoneNumbers en e-mail berichten, zijn kenmerken met meerdere waarden, waarbij u mogelijk verschillende typen telefoon nummers of e-mail berichten moet opgeven. Gebruik de onderstaande expressie voor kenmerken met meerdere waarden. Hiermee kunt u het kenmerk type opgeven en toewijzen aan het bijbehorende Azure AD-gebruikers kenmerk voor de waarde. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers [type EQ "fax"]. waarde

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

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>De standaard kenmerken en kenmerk toewijzingen herstellen

Als u moet beginnen met het opnieuw instellen van de standaard status van uw bestaande toewijzingen, kunt u het selectie vakje **standaard toewijzingen herstellen** inschakelen en de configuratie opslaan. Hiermee stelt u alle toewijzingen en bereik filters zo in dat de toepassing zojuist vanuit de toepassings galerie aan uw Azure AD-Tenant is toegevoegd.

Als u deze optie selecteert, wordt een hersynchronisatie van alle gebruikers geforceerd wanneer de inrichtings service wordt uitgevoerd.

> [!IMPORTANT]
> We raden u ten zeerste aan om de **inrichtings status** in te stellen op **uit** voordat u deze optie aanroept.

## <a name="what-you-should-know"></a>Wat u moet weten

- Microsoft Azure AD biedt een efficiënte implementatie van een synchronisatie proces. In een geïnitialiseerde omgeving worden alleen objecten waarvoor updates vereist zijn verwerkt tijdens een synchronisatie cyclus.
- Het bijwerken van kenmerk toewijzingen heeft gevolgen voor de prestaties van een synchronisatie cyclus. Voor een update van de kenmerk toewijzings configuratie moeten alle beheerde objecten opnieuw worden geëvalueerd.
- Een aanbevolen best practice is om het aantal opeenvolgende wijzigingen in uw kenmerk toewijzingen ten minste te hand haven.
- Het toevoegen van een foto kenmerk dat moet worden ingericht voor een app, wordt niet ondersteund. u kunt de indeling niet opgeven om de foto te synchroniseren. U kunt de functie voor [gebruikers spraak](https://feedback.azure.com/forums/169401-azure-active-directory) aanvragen
- Het kenmerk IsSoftDeleted maakt vaak deel uit van de standaard toewijzingen voor een toepassing. IsSoftdeleted kan in een van de vier scenario's waar zijn (de gebruiker valt buiten het bereik omdat de toewijzing van de toepassing ongedaan is geworden, de gebruiker valt buiten het bereik komt omdat er geen filter voor het bereik is bereikt, de gebruiker is tijdelijk verwijderd in azure AD of de eigenschap AccountEnabled is ingesteld op False voor de gebruiker). Het is niet raadzaam om het kenmerk IsSoftDeleted uit de kenmerk toewijzingen te verwijderen.
- De Azure AD-inrichtings service biedt geen ondersteuning voor het inrichten van Null-waarden.
- De primaire sleutel, meestal ' ID ', mag niet worden opgenomen als een doel kenmerk in uw kenmerk toewijzingen. 
- Het kenmerk role moet meestal worden toegewezen met behulp van een expressie, in plaats van een directe toewijzing. Zie de sectie hierboven voor meer informatie over de toewijzing van rollen. 
- Hoewel u groepen uit uw toewijzingen kunt uitschakelen, wordt het uitschakelen van gebruikers niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers inrichting en ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
- [Expressies schrijven voor kenmerk toewijzingen](../app-provisioning/functions-for-customizing-application-data.md)
- [Filters voor het inrichten van gebruikers in bereik](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
- [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
