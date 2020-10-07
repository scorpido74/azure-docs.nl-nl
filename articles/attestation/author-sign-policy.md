---
title: Een Azure Attestation-beleid ontwerpen en ondertekenen
description: Uitleg van het ontwerpen en ondertekenen van een Attestation-beleid.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a3afb12ac831d87b03d0bb16d1b7ef553f1bb906
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90006816"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Een Attestation-beleid ontwerpen en ondertekenen

Een Attestation-beleid is een naar Microsoft Azure Attestation geüpload bestand. Azure Attestation biedt de flexibiliteit voor het uploaden van een beleid in een beleidsindeling die specifiek is voor Attestation. U kunt ook een gecodeerde versie van het beleid in JSON Web Signature uploaden. De beleidsbeheerder is verantwoordelijk voor het schrijven van het Attestation-beleid. In de meeste scenario's fungeert de Relying Party als beleidsbeheerder. De client die de Attestation-aanroep doet, verzendt Attestation-bewijzen, die door de service worden geparseerd en omgezet in binnenkomende claims (sets van eigenschappen, waarde). De service verwerkt vervolgens de claims op basis van het gedefinieerde beleid en retourneert het berekende resultaat.

Het beleid bevat regels die de autorisatiecriteria, eigenschappen en de inhoud van de Attestation-token bepalen. Een voorbeeld van een beleidsbestand ziet er als volgt uit:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Een beleidsbestand heeft drie segmenten, zoals hierboven te zien is:

- **version** (versie):  De versie is het versienummer van de grammatica die wordt gevolgd. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Momenteel wordt alleen versie 1.0 ondersteund.

- **authorizationrules** (autorisatieregels): Een verzameling claimregels die eerst worden gecontroleerd, om te bepalen of Azure Attestation door moet gaan naar **issuancerules**. De claimregels worden toegepast in de volgorde waarin ze zijn gedefinieerd.

- **issuancerules** (uitgifteregels): Een verzameling claimregels die wordt geëvalueerd om aanvullende informatie toe te voegen aan het Attestation-resultaat zoals gedefinieerd in het beleid. De claimregels worden toegepast in de volgorde waarin ze zijn gedefinieerd en zijn ook optioneel.

Zie [claim en claimregels](claim-rule-grammar.md) voor meer informatie.
   
## <a name="drafting-the-policy-file"></a>Het beleidsbestand opstellen

1. Maak een nieuw bestand.
1. Voeg de versie toe aan het bestand.
1. Voeg secties toe voor **authorizationrules** en **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  De autorisatieregels bevatten de actie deny() (afwijzen) zonder voorwaarde, om ervoor te zorgen dat er geen uitgifteregels worden verwerkt. De autorisatieregel kan ook de actie permit() (toestaan) bevatten, om de verwerking van uitgifteregels toe te staan.
  
4. Claimregels toevoegen aan de autorisatieregels

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Als de binnenkomende claimset een claim bevat die overeenkomt met het type, de waarde en de verlener, geeft de actie permit() aan dat de beleidsengine de **issuancerules** moet verwerken.
  
5. Voeg claimregels toe aan **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  De set uitgaande claims bevat een claim met:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Complexe beleidsregels kunnen op een vergelijkbare manier worden ontworpen. Zie [Attestation-beleidsvoorbeelden](policy-examples.md)voor meer informatie.
  
6. Sla het bestand op.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Het beleidsbestand maken in de indeling JSON Web Signature

Nadat u een beleidsbestand hebt gemaakt, volgt u de onderstaande stappen om een beleid te uploaden in de JWS-indeling.

1. Genereer de JWS, RFC 7515 met beleid (UTF-8 gecodeerd) als de payload
     - De payload-id voor het met Base64Url gecodeerde beleid moet "AttestationPolicy" zijn.
     
     Voorbeeld-JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (Optioneel) Onderteken het beleid. Azure Attestation ondersteunt de volgende algoritmen:
     - **Geen**: De payload van het beleid niet ondertekenen.
     - **RS256**: Ondersteund algoritme voor het ondertekenen van de payload van het beleid

3. Upload de JWS en valideer het beleid.
     - Als het beleidsbestand geen syntaxisfouten bevat, wordt het geaccepteerd door de service.
     - Als het beleidsbestand wel syntaxisfouten bevat, wordt het afgewezen door de service.

## <a name="signing-the-policy"></a>Het beleid ondertekenen

Hieronder volgt een voorbeeld van een Python-script voor het uitvoeren van de bewerking voor het ondertekenen van het beleid

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Volgende stappen
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
- [Een SGX-enclave-attest verklaren met codevoorbeelden](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
