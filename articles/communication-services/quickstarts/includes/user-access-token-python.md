---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944583"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python](https://www.python.org/downloads/) 2.7, 3.5 of hoger.
- Een actieve Communication Services-resource en verbindingsreeks. [Een communicatieresource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Gebruik een tekst editor om een bestand te maken met de naam **issue-tokens.py** in de hoofdmap van het project en voeg de structuur voor het programma toe, waaronder de basale verwerking van uitzonderingen. In de volgende secties voegt u alle broncode voor deze quickstart toe aan dit bestand.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer met de opdracht `pip install` de clientbibliotheek voor het Azure Communications Services-beheer voor het Python-pakket.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `CommunicationIdentityClient` tot stand met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg deze code toe in het `try`-blok:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Een gebruiker maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `create_user` om een nieuwe vermelding in de map te maken met een unieke `Id`. U moet een toewijzing onderhouden tussen de gebruikers van uw toepassing en de door de Communication Services gegenereerde identiteiten (bijv. door ze op te slaan in de database van uw toepassingsserver).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Tokens voor gebruikerstoegang uitgeven

Gebruik de methode `issue_token` om een toegangstoken voor een Communication Services-gebruiker uit te geven. Als u de optionele `user` parameter niet opgeeft, wordt er een nieuwe gebruiker gemaakt en geretourneerd met het token.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Tokens voor gebruikerstoegang zijn kortdurige referenties die opnieuw moeten worden uitgegeven om te voor komen dat uw gebruikers problemen ondervinden met de service. De responseigenschap `expires_on` geeft de levensduur van het token aan.

## <a name="revoke-user-access-tokens"></a>Tokens voor gebruikerstoegang intrekken

In sommige gevallen moet u de tokens voor gebruikerstoegang wellicht intrekken, bijvoorbeeld wanneer een gebruiker het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. Deze functionaliteit is beschikbaar via de clientbibliotheek voor beheer van Azure Communication Services.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Een gebruiker verwijderen

Als u een identiteit verwijdert, worden alle actieve tokens ingetrokken en wordt voorkomen dat nieuwe tokens voor de identiteiten worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de gebruiker is gekoppeld.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer vanuit een consoleprompt naar de map die het bestand *issue-token.py* bevat, en voer vervolgens de volgende `python`-opdracht uit om de app uit te voeren.

```console
python ./issue-token.py
```
