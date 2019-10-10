---
title: Azure AD-kenmerk toewijzingen aanpassen | Microsoft Docs
description: Meer informatie over kenmerk toewijzingen voor SaaS-apps in Azure Active Directory zijn de manier waarop u ze kunt aanpassen om uw bedrijfs behoeften aan te pakken.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240997"
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
  Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](functions-for-customizing-application-data.md)voor meer informatie.
- **Geen** -het doel kenmerk blijft ongewijzigd. Als het doel kenmerk echter ooit leeg is, wordt het ingevuld met de standaard waarde die u opgeeft.

Naast deze vier basis typen ondersteunen aangepaste kenmerk toewijzingen het concept van een optionele **standaard** waarde voor toewijzingen. De toewijzing van de standaard waarde zorgt ervoor dat een doel kenmerk wordt gevuld met een waarde als er geen waarde in azure AD of op het doel object is. De meest voorkomende configuratie is om dit leeg te laten.

### <a name="understanding-attribute-mapping-properties"></a>Informatie over kenmerk toewijzings eigenschappen

In de vorige sectie hebt u al een kenmerk-toewijzings type-eigenschap geïntroduceerd.
Naast deze eigenschap ondersteunen kenmerk toewijzingen ook de volgende kenmerken:

- **Bron kenmerk** : het gebruikers kenmerk van het bron systeem (voor beeld: Azure Active Directory).
- **Doel kenmerk** : het gebruikers kenmerk in het doel systeem (voor beeld: ServiceNow).
- **Objecten met dit kenmerk matchen** : bepaalt of deze toewijzing moet worden gebruikt om gebruikers tussen de bron-en doel systemen uniek te identificeren. Deze is doorgaans ingesteld op het kenmerk userPrincipalName of mail in azure AD, dat meestal wordt toegewezen aan een gebruikersnaam veld in een doel toepassing.
- **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.
- **Deze toewijzing Toep assen**
  - **Altijd** : deze toewijzing Toep assen op acties voor zowel het maken van de gebruiker als bij het bijwerken.
  - **Alleen tijdens het maken** : pas deze toewijzing alleen toe op acties voor het maken van een gebruiker.

## <a name="editing-group-attribute-mappings"></a>Groeps kenmerk-toewijzingen bewerken

Een geselecteerd aantal toepassingen, zoals ServiceNow, box en G suite, ondersteunt de mogelijkheid om groeps objecten en gebruikers objecten in te richten. Groeps objecten kunnen groeps eigenschappen bevatten, zoals weergave namen en e-mail aliassen, samen met groeps leden.

![Voor beeld toont ServiceNow met ingerichte groeps-en gebruikers objecten](./media/customize-application-attributes/24.png)

Groeps inrichting kan optioneel worden in-of uitgeschakeld door de groeps toewijzing onder **toewijzingen**te selecteren en in **te scha kelen op** de gewenste optie in het scherm **kenmerk toewijzing** .

De kenmerken die als onderdeel van groeps objecten worden ingericht, kunnen op dezelfde manier worden aangepast als gebruikers objecten, zoals eerder beschreven. 

> [!TIP]
> Het inrichten van groeps objecten (eigenschappen en leden) is een afzonderlijk concept van het [toewijzen van groepen](assign-user-or-group-access-portal.md) aan een toepassing. U kunt een groep toewijzen aan een toepassing, maar alleen de gebruikers objecten inrichten die deel uitmaken van de groep. Het inrichten van volledige groeps objecten is niet vereist voor het gebruik van groepen in toewijzingen.

## <a name="editing-the-list-of-supported-attributes"></a>De lijst met ondersteunde kenmerken bewerken

De gebruikers kenmerken die worden ondersteund voor een bepaalde toepassing, zijn vooraf geconfigureerd. De gebruikers beheer-Api's van de meeste toepassingen bieden geen ondersteuning voor schema detectie. De Azure AD-inrichtings service kan de lijst met ondersteunde kenmerken dus niet dynamisch genereren door aanroepen van de toepassing.

Sommige toepassingen ondersteunen echter aangepaste kenmerken en de Azure AD-inrichtings service kan lees-en schrijf bewerkingen naar aangepaste kenmerken. Als u hun definities wilt invoeren in de Azure Portal, schakelt u het selectie vakje **Geavanceerde opties weer geven** onder in het scherm **kenmerk toewijzing** in en selecteert u **kenmerk lijst bewerken voor** uw app.

Toepassingen en systemen die ondersteuning bieden voor aanpassing van de lijst met kenmerken zijn onder andere:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory ([standaard kenmerken van Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) en aangepaste Directory-extensies worden ondersteund)
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
  - *String* -kenmerk bevat een teken reeks.
- **Primaire sleutel?** -Of het kenmerk wordt gedefinieerd als een primaire-sleutel veld in het schema van het doel object.
- **Vereist?** -Of het kenmerk moet worden ingevuld in de doel toepassing of het systeem.
- **Meerdere waarden?** -Of het kenmerk meerdere waarden ondersteunt.
- **Wat is er precies?** -Of de kenmerken waarden in een hoofdletter gevoelige manier worden geëvalueerd.
- **API-expressie** : gebruik deze, tenzij dit wordt gedaan door de documentatie voor een specifieke inrichtings connector (zoals workday).
- **Object kenmerk waarnaar wordt verwezen** : als het een verwijzings type kenmerk is, kunt u in dit menu de tabel en het kenmerk selecteren in de doel toepassing die de waarde bevat die aan het kenmerk is gekoppeld. Als u bijvoorbeeld een kenmerk met de naam ' Department ' hebt waarvan de opgeslagen waarde verwijst naar een object in een afzonderlijke tabel ' departments ', selecteert u ' Departments.Name '. De referentie tabellen en de primaire ID-velden die worden ondersteund voor een bepaalde toepassing, zijn vooraf geconfigureerd en kunnen momenteel niet worden bewerkt met de Azure Portal, maar u kunt deze bewerken met behulp van de [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Als u een nieuw kenmerk wilt toevoegen, bladert u naar het einde van de lijst met ondersteunde kenmerken, vult u de bovenstaande velden in met behulp van de beschik bare invoer en selecteert u **kenmerk toevoegen**. Selecteer **Opslaan** wanneer u klaar bent met het toevoegen van kenmerken. Vervolgens moet u het tabblad **inrichten** opnieuw laden om de nieuwe kenmerken beschikbaar te maken in de editor voor kenmerk toewijzing.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>De standaard kenmerken en kenmerk toewijzingen herstellen

Als u moet beginnen met het opnieuw instellen van de standaard status van uw bestaande toewijzingen, kunt u het selectie vakje **standaard toewijzingen herstellen** inschakelen en de configuratie opslaan. Hierdoor worden alle toewijzingen ingesteld alsof de toepassing zojuist is toegevoegd aan uw Azure AD-Tenant vanuit de toepassings galerie.

Als u deze optie selecteert, wordt een hersynchronisatie van alle gebruikers geforceerd wanneer de inrichtings service wordt uitgevoerd.

> [!IMPORTANT]
> We raden u ten zeerste aan om de **inrichtings status** in te stellen op **uit** voordat u deze optie aanroept.

## <a name="what-you-should-know"></a>Wat u moet weten

- Microsoft Azure AD biedt een efficiënte implementatie van een synchronisatie proces. In een geïnitialiseerde omgeving worden alleen objecten waarvoor updates vereist zijn verwerkt tijdens een synchronisatie cyclus.
- Het bijwerken van kenmerk toewijzingen heeft gevolgen voor de prestaties van een synchronisatie cyclus. Voor een update van de kenmerk toewijzings configuratie moeten alle beheerde objecten opnieuw worden geëvalueerd.
- Een aanbevolen best practice is om het aantal opeenvolgende wijzigingen in uw kenmerk toewijzingen ten minste te hand haven.
- Het toevoegen van een foto kenmerk dat moet worden ingericht voor een app, wordt niet ondersteund. u kunt de indeling niet opgeven om de foto te synchroniseren. U kunt de functie voor [gebruikers spraak](https://feedback.azure.com/forums/169401-azure-active-directory) aanvragen
- Het kenmerk IsSoftDeleted maakt vaak deel uit van de standaard toewijzingen voor een toepassing. IsSoftdeleted kan in een van de vier scenario's waar zijn (de gebruiker valt buiten het bereik omdat de toewijzing van de toepassing ongedaan is geworden, de gebruiker valt buiten het bereik komt omdat er geen filter voor het bereik is bereikt, de gebruiker is tijdelijk verwijderd in azure AD of de eigenschap AccountEnabled is ingesteld op False  op de gebruiker). 
- De Azure AD-inrichtings service biedt geen ondersteuning voor het inrichten van Null-waarden

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers inrichting en ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
- [Expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)
- [Filters voor het inrichten van gebruikers in bereik](define-conditional-rules-for-provisioning-user-accounts.md)
- [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
- [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
