---
title: Apps voor openbare client met één en meerdere account | Azure
description: Een overzicht van afzonderlijke en meerdere account openbare client apps.
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701413"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Apps voor openbare client met één en meerdere account

In dit artikel u inzicht krijgen in de typen die worden gebruikt in openbare client-apps met één en meerdere accounten, met een focus op openbare client-apps met één account. 

De ADAL (Azure Active Directory Authentication Library) modelleert de server.  De Microsoft Authentication Library (MSAL) modelleert in plaats daarvan uw clienttoepassing.  De meeste Android-apps worden beschouwd als openbare clients. Een openbare client is een app die niet veilig een geheim kan bewaren.  

MSAL is gespecialiseerd in `PublicClientApplication` het API-oppervlak om de ontwikkelingservaring te vereenvoudigen en te verduidelijken voor apps waarmee slechts één account tegelijk kan worden gebruikt. `PublicClientApplication`wordt gesubclasseerd door `SingleAccountPublicClientApplication` en `MultipleAccountPublicClientApplication`.  In het volgende diagram ziet u de relatie tussen deze klassen.

![UML-klassediagram met singleaccountpublicclienttoepassing](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Single account openbare client applicatie

Met `SingleAccountPublicClientApplication` de klasse u een msal-gebaseerde app maken waarmee slechts één account tegelijk kan worden aangemeld. `SingleAccountPublicClientApplication`verschilt van `PublicClientApplication` de volgende manieren:

- MSAL volgt het momenteel aangemelde account.
  - Als uw app een broker gebruikt (de standaardregistratie tijdens de registratie van azure-portal-apps) en is geïnstalleerd op een apparaat waar een broker aanwezig is, controleert MSAL of het account nog steeds beschikbaar is op het apparaat.
- `signIn`hiermee u zich expliciet en los van het aanvragen van scopes aanmelden.
- `acquireTokenSilent`vereist geen accountparameter.  Als u een account opgeeft en het account dat u opgeeft niet `MsalClientException` overeenkomt met de lopende rekening die door MSAL wordt bijgehouden, wordt er een account gegooid.
- `acquireToken`staat de gebruiker niet toe om van account te wisselen. Als de gebruiker probeert over te schakelen naar een ander account, wordt er een uitzondering gemaakt.
- `getCurrentAccount`retourneert een resultaatobject dat het volgende biedt:
  - Een booleaan die aangeeft of het account is gewijzigd. Een account kan worden gewijzigd als gevolg van het verwijderen van het apparaat, bijvoorbeeld.
  - De eerdere rekening. Dit is handig als u lokale gegevens moet opruimen wanneer het account van het apparaat wordt verwijderd of wanneer een nieuw account is aangemeld.
  - De huidigeAccount.
- `signOut`verwijdert alle tokens die aan uw client zijn gekoppeld, van het apparaat.  

Wanneer een Android Authentication broker zoals Microsoft Authenticator of Intune Company Portal op het apparaat `signOut` is geïnstalleerd en uw app is geconfigureerd om de broker te gebruiken, wordt het account niet van het apparaat verwijderd.

## <a name="single-account-scenario"></a>Scenario voor één account

De volgende pseudo-code `SingleAccountPublicClientApplication`illustreert het gebruik van .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Openbare clienttoepassing met meerdere account

De `MultipleAccountPublicClientApplication` klasse wordt gebruikt om op MSAL gebaseerde apps te maken waarmee meerdere accounts tegelijkertijd kunnen worden aangemeld. Hiermee u accounts als volgt krijgen, toevoegen en verwijderen:

### <a name="add-an-account"></a>Een account toevoegen

Gebruik een of meer accounts `acquireToken` in uw toepassing door een of meer keren te bellen.  

### <a name="get-accounts"></a>Accounts openen

- Bel `getAccount` om een specifiek account te krijgen.
- Bel `getAccounts`om een lijst met accounts te krijgen die momenteel bekend zijn bij de app.

Uw app kan niet alle Microsoft-accounts voor identiteitsplatformen opsommen op het apparaat dat bekend is bij de broker-app. Het kan alleen accounts opsommen die door uw app zijn gebruikt.  Accounts die van het apparaat zijn verwijderd, worden niet geretourneerd door deze functies.

### <a name="remove-an-account"></a>Een account verwijderen

Een account verwijderen `removeAccount` door te bellen met een account-id.

Als uw app is geconfigureerd om een broker te gebruiken en een broker op het apparaat is `removeAccount`geïnstalleerd, wordt het account niet verwijderd van de broker wanneer u belt.  Alleen tokens die aan uw client zijn gekoppeld, worden verwijderd.

## <a name="multiple-account-scenario"></a>Scenario voor meerdere account

De volgende pseudo-code laat zien hoe je een app met meerdere accounts maakt, accounts op het apparaat vermeldt en tokens verwerft.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
