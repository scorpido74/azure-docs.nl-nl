---
title: JavaScript-voorbeelden
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van Java script in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5c66ccb8ae9db46ca8e94d9c73e9c61b64119dd0
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201783"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Java script-voor beelden voor gebruik in Azure Active Directory B2C

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

    Als u van plan bent java script te gebruiken, moet u [een versie van de pagina-indeling definiëren](contentdefinitions.md#migrating-to-page-layout) met `contract` de pagina versie voor *alle* inhouds definities in uw aangepaste beleid.

## <a name="add-the-scriptexecution-element"></a>Het element ScriptExecution toevoegen

U kunt het uitvoeren van scripts inschakelen door het element **ScriptExecution** toe te voegen aan het element [RelyingParty](relyingparty.md) .

1. Open uw aangepaste beleids bestand. Bijvoorbeeld *SignUpOrSignin.xml*.
2. Voeg het element **ScriptExecution** toe aan het element **UserJourneyBehaviors** van **RelyingParty**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Sla het bestand op en upload het.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript-voorbeelden

### <a name="show-or-hide-a-password"></a>Een wacht woord weer geven of verbergen

Een veelgebruikte manier om uw klanten te helpen hun registratie te laten slagen is om te voor komen dat ze hun wacht woord hebben ingevoerd. Met deze optie kunnen gebruikers zich registreren door ze in te scha kelen om hun wacht woord zo nodig eenvoudig te kunnen bekijken en te corrigeren. Een veld van het type wacht woord heeft een selectie vakje met een **wachtwoord label weer geven** .  Op deze manier kan de gebruiker het wacht woord als tekst zonder opmaak weer geven. Voeg dit code fragment toe aan uw registratie-of aanmeldings sjabloon voor een zelfbevestigende pagina:

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

### <a name="add-terms-of-use"></a>Gebruiks voorwaarden toevoegen

Voeg de volgende code toe aan de pagina waar u een selectie vakje voor de **gebruiks voorwaarden** wilt toevoegen. Dit selectie vakje is doorgaans nodig voor de aanmeldings pagina's van uw lokale account en voor het account voor sociale accounts.

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

Vervang in de code door `termsOfUseUrl` de koppeling naar uw gebruiksrecht overeenkomst. Maak voor uw Directory een nieuw gebruikers kenmerk met de naam **termsOfUse** en voeg vervolgens **termsOfUse** als een gebruikers kenmerk toe.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikers interface van uw toepassingen kunt aanpassen in [de gebruikers interface van uw toepassing aanpassen met behulp van een aangepast beleid in azure Active Directory B2C](custom-policy-ui-customization.md).
