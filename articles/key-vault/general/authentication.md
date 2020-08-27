---
title: Verifiëren bij Azure Key Vault
description: Informatie over verifiëren bij Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6336a0d4d8aa9c781befed0470d9a190af5aa9eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930856"
---
# <a name="authenticate-to-azure-key-vault"></a>Verifiëren bij Azure Key Vault

## <a name="overview"></a>Overzicht

Azure Key Vault is een oplossing voor het beheren van geheimen waarmee u de opslag van toepassingsgeheimen kunt centraliseren en de distributie ervan kunt beheren. Met Azure Key Vault hoeft u geen referenties meer op te slaan in toepassingen. Uw toepassing kan verifiëren bij de sleutelkluis om de vereiste referenties op te halen. In dit document worden de basisbeginselen van verificatie bij de sleutelkluis uitgelegd.

In dit document leert u hoe sleutelkluisverificatie werkt. In dit document wordt de verificatiestroom toegelicht en wordt uitgelegd hoe u toegang verleent tot uw sleutelkluis. Ook vindt u hier een zelfstudie voor het ophalen van een opgeslagen geheim in de sleutelkluis vanuit een Python-voorbeeldtoepassing.

In dit document wordt het volgende behandeld:

* Belangrijkste concepten
* Registratie van beveiligingsprincipals
* Informatie over de Key Vault-verificatiestroom
* Een service-principal toegang verlenen tot Key Vault
* Zelfstudie (Python)

## <a name="key-concepts"></a>Belangrijkste concepten

### <a name="azure-active-directory-concepts"></a>Concepten van Azure Active Directory

* Azure Active Directory (AAD): Azure Active Directory (Azure AD) is de service voor identiteits- en toegangsbeheer in de cloud van Microsoft, waarmee uw medewerkers zich kunnen aanmelden en toegang kunnen krijgen tot resources

* Roldefinitie: een roldefinitie is een verzameling machtigingen.  AAD heeft standaardrollen (Eigenaar, Inzender of Lezer) met machtigingsniveaus voor het uitvoeren van bewerkingen, zoals lezen, schrijven en verwijderen, in een Azure-resource. Rollen kunnen ook aangepaste definities zijn die zijn gemaakt door gebruikers met specifieke gedetailleerde machtigingen.

* Registratie van toepassingen: wanneer u een Azure AD-toepassing registreert, worden er twee objecten gemaakt in uw Azure AD-Tenant: een toepassingsobject en een service-principalobject. U kunt het toepassingsobject beschouwen als de globale representatie van uw toepassing voor gebruik in alle tenants en de service-principal als de lokale representatie voor gebruik in een specifieke tenant.

### <a name="security-principal-concepts"></a>Concepten van beveiligingsprincipals

* Beveiligingsprincipal: een beveiligingsprincipal is een object dat een gebruiker, groep, service-principal of beheerde identiteit representeert die toegang tot Azure-resources aanvraagt.

* Gebruiker: een persoon een profiel heeft in Azure Active Directory.

* Groep: een aantal gebruikers dat in Azure Active Directory is gemaakt. Wanneer u een rol aan een groep toewijst, hebben alle gebruikers binnen die groep die rol.

* Service-principal: een beveiligings-id die wordt gebruikt door toepassingen of services om toegang tot specifieke Azure-resources te krijgen. U kunt het zien als een gebruikersidentiteit (gebruikersnaam en wachtwoord of certificaat) voor een toepassing.

* Beheerde identiteit: een identiteit in Azure Active Directory die automatisch wordt beheerd door Azure.

* Object-id (client-id): een unieke id die door Azure AD wordt gegenereerd. Deze wordt tijdens de eerste inrichting gekoppeld aan een service-principal.

## <a name="security-principal-registration"></a>Registratie van beveiligingsprincipals

1. De beheerder registreert een gebruiker of toepassing (service-principal) in Azure Active Directory.

2. De beheerder maakt een Azure Key Vault en configureert toegangsbeleid (ACL's).

3. (Optioneel) De beheerder configureert de Azure Key Vault-firewall.

![AFBEELDING](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Informatie over de Key Vault-verificatiestroom

1. Een service-principal roept AAD aan om te verifiëren. Dit kan op verschillende manieren:
    * Een gebruiker kan zich aanmelden bij Azure Portal met een gebruikersnaam en wachtwoord.
    * Een toepassing maakt gebruik van een client-id en presenteert een clientgeheim of clientcertificaat aan AAD
    * Een Azure-resource, bijvoorbeeld een virtuele machine, heeft een toegewezen MSI en neemt contact op met het IMDS REST-eindpunt om een toegangstoken op te halen.

2. Als verificatie bij AAD slaagt, wordt een OAuth-token verleend aan de service-principal.
3. De service-principal roept Key Vault aan.
4. Via de Azure Key Vault-firewall wordt bepaald of de aanroep is toegestaan.
    * Scenario 1: De Key Vault-firewall is uitgeschakeld. Het openbare eindpunt (URI) van de sleutelkluis is bereikbaar via het openbare internet. De aanroep is toegestaan.
    * Scenario 2: De oproepende functie is een vertrouwde service van Azure Key Vault. Bepaalde Azure-services kunnen de firewall van de sleutelkluis overslaan als de optie daarvoor is geselecteerd. [Lijst met vertrouwde services van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Scenario 3: Het IP-adres, virtuele netwerk of service-eindpunt van de oproepende functie wordt vermeld in de Azure Key Vault-firewall.
    * Scenario 4: De aanroepende functie kan Azure Key Vault bereiken via een geconfigureerde Private Link-verbinding.
    * Scenario 5: De aanroepende functie is niet geautoriseerd en er wordt een verbodsreactie geretourneerd.
5. Key Vault roept AAD aan om het toegangstoken van de service-principal te valideren.
6. Key Vault controleert of de service-principal voldoende toegangsbeleidmachtigingen heeft om de aangevraagde bewerking uit te voeren. De bewerking in dit voorbeeld is het geheim ophalen.
7. Key Vault verstrekt het geheim aan de service-principal.

![AFBEELDING](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Een service-principal toegang verlenen tot Key Vault

1. Maak een service-principal als u er nog geen hebt. [Een service-principal maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Voeg in de IAM-instellingen van Azure Key Vault een roltoewijzing toe aan uw service-principal. U kunt de vooraf toegewezen rollen Eigenaar, Inzender of Lezer toevoegen. U kunt ook aangepaste rollen maken voor uw service-principal. Houd de principal met de laagste machtiging aan en bied alleen de minimale toegang die nodig is voor uw service-principal. 
3.  Configureer de firewall van de sleutelkluis. U kunt de firewall van de sleutelkluis uitgeschakeld laten en toegang toestaan vanaf het openbare internet (minder veilig, maar gemakkelijker te configureren). U kunt ook de toegang beperken tot specifieke IP-bereiken, service-eindpunten, virtuele netwerken of privé-eindpunten (veiliger).
4.  Voeg toegangsbeleid voor uw service-principal toe. Dat is een lijst met bewerkingen die uw service-principal mag uitvoeren op de sleutelkluis. Maak gebruik van de principal met de laagste machtiging en beperk de bewerkingen die de service-principal mag uitvoeren. Als u echter onvoldoende machtigingen opgeeft, krijgt uw service-principal geen toegang.

## <a name="tutorial"></a>Zelfstudie

In deze zelfstudie leert u hoe u een service-principal kunt instellen voor verificatie bij de sleutelkluis en het ophalen van een geheim. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Deel 1:  Een service-principal maken in Azure Portal

1. Aanmelden bij Azure Portal
1. Zoek naar Azure Active Directory
1. Klik op het tabblad App-registraties
1. Klik op + Nieuwe registratie
1. Maak een naam voor de service-principal
1. Selecteer Registreren

Nu hebt u een geregistreerde service-principal. U kunt deze weergeven door App-registraties te selecteren. Aan uw service-principal wordt nu een client-id-GUID toegewezen. Dat is een soort gebruikersnaam voor uw service-principal. Nu moeten we een wachtwoord voor uw service-principal maken. Hiervoor kunt u een clientgeheim of een clientcertificaat gebruiken. Houd er rekening mee dat het gebruik van een clientgeheim voor verificatie niet veilig is en alleen moet worden gebruikt voor testdoeleinden. In deze zelfstudie leert u hoe u een clientcertificaat gebruikt.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Deel 2: Een clientcertificaat voor uw service-principal maken

1. Een certificaat maken

    * Optie 1: Een certificaat maken met behulp van [OpenSSL](https://www.openssl.org/) (alleen voor testdoeleinden, gebruik geen zelfondertekende certificaten in productie)
    * Optie 2: Een certificaat maken met behulp van de sleutelkluis. [Een certificaat maken met behulp van Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Het certificaat in de PEM- of PFX-indeling downloaden
1. Meld u aan bij Azure Portal en ga naar Azure Active Directory
1. Klik op App-registraties
1. Selecteer de service-principal die u in deel 1 hebt gemaakt.
1. Klik op het tabblad Certificaten en geheimen van de service-principal
1. Upload het certificaat met behulp van de knop Certificaat uploaden

### <a name="part-3-configure-an-azure-key-vault"></a>Deel 3: Een Azure Key Vault configureren

1. Maak een Azure Key Vault [via deze koppeling](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)

2. Configureer IAM-machtigingen van Key Vault
    1. Navigeer naar uw sleutelkluis
    1. Selecteer het tabblad Toegangsbeheer (IAM)
    1. Klik op Roltoewijzing toevoegen
    1. Selecteer de rol Inzender in de vervolgkeuzelijst
    1. Voer de naam of client-id in van de service-principal die u hebt gemaakt
    1. Klik op Roltoewijzingen weergeven om te bevestigen dat uw service-principal wordt vermeld

3. Toegangsbeleidmachtigingen voor Key Vault configureren
    1. Navigeer naar uw sleutelkluis
    1. Selecteer het tabblad Toegangsbeleid onder Instellingen
    1. Selecteer de koppeling + Toegangsbeleid toevoegen
    1. Schakel onder de vervolgkeuzelijst Geheime machtigingen de machtigingen Ophalen en Weergeven in.
    1. Selecteer uw service-principal op basis van de naam of client-id.
    1. Selecteer Toevoegen
    1. Selecteer Opslaan

4. Maak een geheim in uw sleutelkluis.
    1. Navigeer naar uw sleutelkluis
    1. Klik op het tabblad Geheimen onder Instellingen
    1. Klik op + Genereren/importeren
    1. Maak een naam voor het geheim (in dit voorbeeld wordt de naam test gebruikt)
    1. Maak een waarde voor het geheim (in dit voorbeeld wordt de waarde password123 ingesteld)

Wanneer u nu code uitvoert vanaf uw lokale computer, kunt u verifiëren bij de sleutelkluis door een toegangstoken op te halen door de client-id en een pad naar het certificaat te presenteren.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Deel 4: Het geheim ophalen uit uw Azure Key Vault via een toepassing (Python)

Gebruik het volgende codevoorbeeld om te testen of uw toepassing een geheim kan ophalen uit uw sleutelkluis met behulp van de service-principal die u hebt geconfigureerd. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![AFBEELDING](../media/authentication-3.png)


## <a name="next-steps"></a>Volgende stappen

1. Meer informatie over het oplossen van problemen met sleutelkluisverificatie. [Handleiding voor het oplossen van problemen met Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
