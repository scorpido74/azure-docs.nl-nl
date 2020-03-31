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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187658"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-voorbeelden voor gebruik in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

U uw eigen JavaScript-clientcode toevoegen aan uw Azure Active Directory B2C-toepassingen (Azure AD B2C).

Ga als u JavaScript inschakelen voor uw toepassingen:

* Een element toevoegen aan uw [aangepaste beleid](custom-policy-overview.md)
* Een [pagina-indeling selecteren](page-layout.md)
* Gebruik [b2clogin.com](b2clogin.md) in uw verzoeken

In dit artikel wordt beschreven hoe u uw aangepaste beleid wijzigen om scriptuitvoering in te schakelen.

> [!NOTE]
> Zie [JavaScript- en pagina-indelingsversies in Azure Active Directory B2C](user-flow-javascript-overview.md)als u JavaScript wilt inschakelen voor gebruikersstromen.

## <a name="prerequisites"></a>Vereisten

### <a name="select-a-page-layout"></a>Een pagina-indeling selecteren

* Selecteer een [pagina-indeling](contentdefinitions.md#select-a-page-layout) voor de gebruikersinterface-elementen van uw toepassing.

    Als u JavaScript wilt gebruiken, moet u een `contract` [pagina-indelingsversie](contentdefinitions.md#migrating-to-page-layout) met paginaversie definiëren voor *alle* inhoudsdefinities in uw aangepaste beleid.

## <a name="add-the-scriptexecution-element"></a>Het element ScriptExecution toevoegen

U schakelt scriptuitvoering in door het element **ScriptExecution** toe te voegen aan het element [RelyingParty.](relyingparty.md)

1. Open uw aangepaste beleidsbestand. U bijvoorbeeld *SignUpOrSignin.xml .*
2. Voeg het element **ScriptExecution** toe aan het element **UserJourneyBehaviors** van **RelyingParty:**

    ```XML
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

### <a name="show-or-hide-a-password"></a>Een wachtwoord weergeven of verbergen

Een veelvoorkomende manier om uw klanten te helpen met hun succes met aanmelden, is door hen te laten zien wat ze als wachtwoord hebben ingevoerd. Deze optie helpt gebruikers zich aan te melden door ze in staat te stellen hun wachtwoord gemakkelijk te zien en te corrigeren als dat nodig is. Elk veld van typewachtwoord heeft een selectievakje met een **wachtwoordlabel weergeven.**  Hierdoor kan de gebruiker het wachtwoord in platte tekst zien. Neem dit codefragment op in uw aanmeldings- of aanmeldingssjabloon voor een zelfverklaarde pagina:

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

### <a name="add-terms-of-use"></a>Gebruiksvoorwaarden toevoegen

Voeg de volgende code toe aan uw pagina waar u een **selectievakje Gebruiksvoorwaarden** wilt opnemen. Dit selectievakje is meestal nodig in uw lokale aanmeldingspagina's voor uw account en aanmeldingspagina's voor sociale netwerken.

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

Vervang in de `termsOfUseUrl` code de koppeling naar uw gebruiksvoorwaardenovereenkomst. Maak voor uw directory een nieuw gebruikerskenmerk met de naam **termsOfUse** en voeg vervolgens **termenOfUse** als gebruikerskenmerk toe.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikersinterface van uw toepassingen aanpassen in [De gebruikersinterface van uw toepassing aanpassen met behulp van een aangepast beleid in Azure Active Directory B2C](custom-policy-ui-customization.md).
