---
title: Java script-voor beelden-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het gebruik van JavaScript in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 996893f95b11d716feb4253688d3ab1d62d140ee
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659522"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-voorbeelden voor gebruik in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

U kunt uw eigen Java script-client-side code toevoegen aan uw Azure Active Directory B2C-toepassingen (Azure AD B2C). Als u Java script wilt inschakelen voor uw toepassingen, moet u een element toevoegen aan uw [aangepaste beleid](active-directory-b2c-overview-custom.md), een [pagina-indeling](page-layout.md)selecteren en [b2clogin.com](b2clogin.md) in uw aanvragen gebruiken. In dit artikel wordt beschreven hoe u het aangepaste beleid kunt wijzigen om het uitvoeren van scripts mogelijk te maken.

> [!NOTE]
> Als u Java script wilt inschakelen voor gebruikers stromen, raadpleegt u [Java script en pagina-indelings versies in azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Vereisten

### <a name="select-a-page-layout"></a>Selecteer een pagina-indeling

* [Selecteer een pagina-indeling](page-layout.md) voor de elementen van de gebruikers interface van uw toepassing.

    Als u van plan bent java script te gebruiken, moet u [een versie van de pagina-indeling definiëren](page-layout.md#replace-datauri-values) voor *alle* inhouds definities in uw aangepaste beleid.

## <a name="add-the-scriptexecution-element"></a>Het element ScriptExecution toevoegen

U de uitvoering van script inschakelen door toe te voegen de **ScriptExecution** element op de [RelyingParty](relyingparty.md) element.

1. Open het bestand aangepast beleid. Bijvoorbeeld, *SignUpOrSignin.xml*.
2. Voeg de **ScriptExecution** element op de **UserJourneyBehaviors** element van **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Opslaan en upload het bestand.

## <a name="guidelines-for-using-javascript"></a>Richtlijnen voor het gebruik van JavaScript

Volg deze richtlijnen wanneer u de interface van uw toepassing met behulp van JavaScript aanpast:

- Een gebeurtenis niet binden op `<a>` HTML-elementen.
- Geen duren voordat een afhankelijkheid van Azure AD B2C-code of opmerkingen hebt.
- De volgorde of de hiërarchie van Azure AD B2C-HTML-elementen worden niet gewijzigd. Azure AD B2C-beleid gebruiken voor het beheren van de volgorde van de UI-elementen.
- U kunt een RESTful-service met deze overwegingen aanroepen:
    - Mogelijk moet u uw RESTful-service CORS waarmee HTTP-aanroepen aan clientzijde instellen.
    - Zorg ervoor dat uw RESTful-service is beveiligd en alleen het HTTPS-protocol wordt gebruikt.
    - Gebruik geen JavaScript rechtstreeks naar Azure AD B2C-eindpunten worden aangeroepen.
- U kunt uw JavaScript insluiten of u kunt koppelen aan externe JavaScript-bestanden. Wanneer u een bestand met externe JavaScript, zorg ervoor dat u de absolute URL zijn en niet een relatieve URL.
- JavaScript-frameworks:
    - Azure AD B2C maakt gebruik van een specifieke versie van jQuery. Een andere versie van jQuery zijn niet opgenomen. Met behulp van meer dan één versie op dezelfde pagina zorgt ervoor dat de problemen.
    - Met behulp van RequireJS wordt niet ondersteund.
    - De meeste JavaScript-frameworks worden niet ondersteund door Azure AD B2C.
- Azure AD B2C-instellingen kunnen worden gelezen door het aanroepen van `window.SETTINGS`, `window.CONTENT` objecten, zoals de huidige taal van de gebruikersinterface. De waarde van deze objecten niet te wijzigen.
- Voor het aanpassen van het foutbericht van de Azure AD B2C, lokalisatie in een beleid te gebruiken.
- Als alles kan worden bereikt met behulp van een beleid, in het algemeen is de aanbevolen manier.

## <a name="javascript-samples"></a>JavaScript-voorbeelden

### <a name="show-or-hide-a-password"></a>Weergeven of verbergen van een wachtwoord

Er is een veelgebruikte manier om te helpen uw klanten bij hun aanmelding geslaagd zodat ze om te zien wat zij hun wachtwoord hebt ingevoerd. Deze optie kan gebruikers zich aanmelden door zodat ze eenvoudig bekijken en correcties aan hun wachtwoord indien nodig. Elk veld van het type wachtwoord heeft een selectievakje met een **Show wachtwoord** label.  Dit kan de gebruiker om te zien van het wachtwoord in tekst zonder opmaak. Dit codefragment in uw sjabloon registreren of aanmelden voor een zelf-gecontroleerde pagina opnemen:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Gebruiksrechtovereenkomst toevoegen

De volgende code opnemen in uw pagina waar u wilt opnemen in een **gebruiksvoorwaarden** selectievakje. Dit selectievakje is meestal nodig in uw lokale account aanmelden en sociale account aanmeldingspagina's.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

In de code, Vervang `termsOfUseUrl` met de koppeling naar de voorwaarden van de gebruiksrechtovereenkomst. Maak voor uw Directory een nieuw gebruikers kenmerk met de naam **termsOfUse** en voeg vervolgens **termsOfUse** als een gebruikers kenmerk toe.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikersinterface van uw toepassingen kunt aanpassen [aanpassen van de gebruikersinterface van uw toepassing met behulp van een aangepast beleid in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
