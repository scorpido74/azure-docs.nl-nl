---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147404"
---
De **standaard beheerde identiteit** is de door het systeem toegewezen beheerde identiteit of de eerste door de gebruiker toegewezen beheerde identiteit.

Tijdens een uitvoering zijn er twee toepassingen van een identiteit:

1. Het systeem gebruikt een identiteit voor het instellen van de opslag koppelingen, container register en data stores van de gebruiker.

    * In dit geval gebruikt het systeem de standaard beheerde identiteit.

1. De gebruiker past een identiteit toe voor toegang tot resources vanuit de code voor een ingediende uitvoering

    * Geef in dit geval de *client_id* op die overeenkomt met de beheerde identiteit die u wilt gebruiken om een referentie op te halen.
    * U kunt ook de client-ID van de door de gebruiker toegewezen identiteit ophalen via de *DEFAULT_IDENTITY_CLIENT_ID* omgevings variabele.

    Als u bijvoorbeeld een token voor een gegevens opslag wilt ophalen met de standaard beheerde identiteit:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```