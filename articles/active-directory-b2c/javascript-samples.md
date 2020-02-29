---
title: JavaScript-voorbeelden
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van JavaScript in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187658"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-voorbeelden voor gebruik in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

U kunt uw eigen Java script-client-side code toevoegen aan uw Azure Active Directory B2C-toepassingen (Azure AD B2C).

Java script inschakelen voor uw toepassingen:

* Een element toevoegen aan uw [aangepaste beleid](custom-policy-overview.md)
* Selecteer een [pagina-indeling](page-layout.md)
* [B2clogin.com](b2clogin.md) in uw aanvragen gebruiken

In dit artikel wordt beschreven hoe u het aangepaste beleid kunt wijzigen om het uitvoeren van scripts mogelijk te maken.

> [!NOTE]
> Als u Java script wilt inschakelen voor gebruikers stromen, raadpleegt u [Java script en pagina-indelings versies in azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Vereisten

### <a name="select-a-page-layout"></a>Selecteer een pagina-indeling

* Selecteer een [pagina-indeling](contentdefinitions.md#select-a-page-layout) voor de elementen van de gebruikers interface van uw toepassing.

    Als u van plan bent java script te gebruiken, moet u [een versie](contentdefinitions.md#migrating-to-page-layout) van de pagina-indeling met pagina `contract` versie definiëren voor *alle* inhouds definities in uw aangepaste beleid.

## <a name="add-the-scriptexecution-element"></a>Het element ScriptExecution toevoegen

U kunt het uitvoeren van scripts inschakelen door het element **ScriptExecution** toe te voegen aan het element [RelyingParty](relyingparty.md) .

1. Open het bestand aangepast beleid. Bijvoorbeeld *SignUpOrSignin. XML*.
2. Voeg het element **ScriptExecution** toe aan het element **UserJourneyBehaviors** van **RelyingParty**:

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

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript-voorbeelden

### <a name="show-or-hide-a-password"></a>Weergeven of verbergen van een wachtwoord

Er is een veelgebruikte manier om te helpen uw klanten bij hun aanmelding geslaagd zodat ze om te zien wat zij hun wachtwoord hebt ingevoerd. Deze optie kan gebruikers zich aanmelden door zodat ze eenvoudig bekijken en correcties aan hun wachtwoord indien nodig. Een veld van het type wacht woord heeft een selectie vakje met een **wachtwoord label weer geven** .  Dit kan de gebruiker om te zien van het wachtwoord in tekst zonder opmaak. Dit codefragment in uw sjabloon registreren of aanmelden voor een zelf-gecontroleerde pagina opnemen:

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

Voeg de volgende code toe aan de pagina waar u een selectie vakje voor de **gebruiks voorwaarden** wilt toevoegen. Dit selectievakje is meestal nodig in uw lokale account aanmelden en sociale account aanmeldingspagina's.

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

Vervang `termsOfUseUrl` door de koppeling naar uw gebruiksrecht overeenkomst in de code. Maak voor uw Directory een nieuw gebruikers kenmerk met de naam **termsOfUse** en voeg vervolgens **termsOfUse** als een gebruikers kenmerk toe.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikers interface van uw toepassingen kunt aanpassen in [de gebruikers interface van uw toepassing aanpassen met behulp van een aangepast beleid in azure Active Directory B2C](custom-policy-ui-customization.md).
