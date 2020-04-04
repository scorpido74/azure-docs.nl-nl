---
title: Het maken van certificaten controleren en beheren
description: Scenario's die een reeks opties weergeven voor het maken, bewaken en interactie met het proces voor het maken van certificaten met Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2c6d33acddb106a3c032c8e21d3111cb428b9a45
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631647"
---
# <a name="monitor-and-manage-certificate-creation"></a>Het maken van certificaten controleren en beheren
Van toepassing op: Azure

De scenario's /bewerkingen die in dit artikel worden beschreven zijn:

- Een KV-certificaat aanvragen bij een ondersteunde uitgever
- Aanvragen in behandeling krijgen - de aanvraagstatus is 'inProgress'
- Aanvragen in behandeling krijgen - de aanvraagstatus is 'voltooid'
- Aanvragen in behandeling krijgen - de status van de aanvraag is 'geannuleerd' of 'mislukt'
- Aanvragen in behandeling krijgen - in behandeling zijnde aanvraagstatus is 'verwijderd' of 'overschreven'
- Maken (of importeren) wanneer de aanvraag in behandeling is- status is 'inProgress'
- Samenvoegen wanneer aanvraag in behandeling wordt gemaakt met een uitgever (bijvoorbeeld DigiCert)
- Een annulering aanvragen terwijl de status van de aanvraag in behandeling is "inProgress"
- Een object in behandeling verwijderen
- Handmatig een KV-certificaat maken
- Samenvoegen wanneer een aanvraag in behandeling wordt gemaakt - handmatige certificaatcreatie

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Een KV-certificaat aanvragen bij een ondersteunde uitgever 

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

De volgende voorbeelden vereisen dat een object met de naam "mydigicert" al beschikbaar is in uw sleutelkluis bij de uitgever provider als DigiCert. De certificaatuitgever is een entiteit die in Azure Key Vault (KV) wordt vertegenwoordigd als een Bron van CertificateIssuer. Het wordt gebruikt om informatie te verstrekken over de bron van een KV-certificaat; naam van de uitgever, provider, referenties en andere administratieve details.

### <a name="request"></a>Aanvraag

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Antwoord

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Aanvragen in behandeling krijgen - de aanvraagstatus is 'inProgress'

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Als *request_id* is opgegeven in de query, werkt deze als een filter. Als de *request_id* in de query en in het in behandeling zijnde object verschillen, wordt een http-statuscode van 404 geretourneerd.

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Aanvragen in behandeling krijgen - de aanvraagstatus is 'voltooid'

### <a name="request"></a>Aanvraag

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Aanvragen in behandeling krijgen - de status van de aanvraag is 'geannuleerd' of 'mislukt'

### <a name="request"></a>Aanvraag

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> De waarde van de *foutcode* kan zijn "Certificaat uitgevende fout" of "Verzoek afgewezen" op basis van emittent of gebruiker fout respectievelijk.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Aanvragen in behandeling krijgen - in behandeling zijnde aanvraagstatus is 'verwijderd' of 'overschreven'
Een object in behandeling kan worden verwijderd of overschreven door een bewerking maken/importeren wanneer de status niet 'inProgress' is.

|Methode|Aanvraag-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Toevoegen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Maken (of importeren) wanneer de aanvraag in behandeling is- status is 'inProgress'
Een object in behandeling heeft vier mogelijke statussen; "inprogress", "geannuleerd", "mislukt" of "voltooid".

Wanneer de status van een in behandeling zijnde aanvraag 'inprogress' is, mislukken (en importeren) bewerkingen met een http-statuscode van 409 (conflict).

Ga als lid van het conflict om een conflict op te lossen:

- Als het certificaat handmatig wordt gemaakt, u het KV-certificaat voltooien door een samenvoeging uit te brengen of het object in behandeling te verwijderen.

- Als het certificaat wordt gemaakt met een uitgever, u wachten tot het certificaat is voltooid, mislukt of wordt geannuleerd. U het object in behandeling ook verwijderen.

> [!NOTE]
> Het verwijderen van een object in behandeling kan de x509-certificaataanvraag al dan niet annuleren bij de provider.

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Antwoord

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Samenvoegen wanneer aanvraag in behandeling is gemaakt met een emittent
Samenvoegen is niet toegestaan wanneer een object in behandeling is gemaakt met een uitgever, maar is toegestaan wanneer de status 'inProgress' is. 

Als het verzoek om het x509-certificaat te maken mislukt of om de een of andere reden wordt geannuleerd en als een x509-certificaat kan worden opgehaald met out-of-band-middelen, kan een samenvoegbewerking worden uitgevoerd om het KV-certificaat te voltooien.

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Antwoord

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Een annulering aanvragen terwijl de status van de aanvraag in behandeling is "inProgress"
Een annulering kan alleen worden aangevraagd. Een verzoek kan al dan niet worden geannuleerd. Als een aanvraag niet "inProgress" is, wordt een http-status van 400 (Slecht verzoek) geretourneerd.

|Methode|Aanvraag-URI|
|------------|-----------------|
|Patch|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Patch`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Patch`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Een object in behandeling verwijderen

> [!NOTE]
> Het verwijderen van het in behandeling zijnde object kan de x509-certificaataanvraag al dan niet annuleren bij de provider.

|Methode|Aanvraag-URI|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Aanvraag
Verwijderen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OF

Verwijderen`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Antwoord

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Handmatig een KV-certificaat maken
U een certificaat maken dat is uitgegeven met een CA naar keuze via een handmatig creatieproces. Stel de naam van de uitgever in op 'Onbekend' of geef het veld van de uitgever niet op.

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Antwoord

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Samenvoegen wanneer een aanvraag in behandeling wordt gemaakt - handmatige certificaatcreatie

|Methode|Aanvraag-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Aanvraag

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Elementnaam|Vereist|Type|Versie|Beschrijving|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Ja|matrix|\<introductie versie>|X509-certificaatketen als basis64-snaararray.|

### <a name="response"></a>Antwoord

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```

## <a name="see-also"></a>Zie ook
- [Informatie over sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
