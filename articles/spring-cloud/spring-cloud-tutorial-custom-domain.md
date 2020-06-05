---
title: Zelfstudie - Een bestaande aangepast domein toewijzen aan Azure Spring Cloud
description: Een bestaande naam van een aangepaste gedistribueerde naamservice (DNS) toewijzen aan een Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: ff38f923f7b33c4bc893246970c1e47d33e59269
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780411"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Een bestaand aangepast domein toewijzen aan Azure Spring Cloud
Distributed name service (DNS) is een techniek voor het opslaan van namen van netwerkknooppunten in een netwerk. In deze zelfstudie wordt een domein, zoals www.contoso.com, gekoppeld met behulp van een CNAME-record. Het aangepaste domein wordt beveiligd met een certificaat en laat zien hoe u Transport Layer Security (TLS) afdwingt, ook wel bekend als Secure Sockets Layer (SSL). 

Certificaten versleutelen webverkeer. Deze TLS/SSL-certificaten kunnen worden opgeslagen in Azure Key Vault. 

## <a name="prerequisites"></a>Vereisten
* Een toepassing die is geïmplementeerd in Azure Spring Cloud (Zie [Snelstart: Start een bestaande Azure Spring Cloud-toepassing met behulp van het Azure- portal](spring-cloud-quickstart-launch-app-portal.md)of gebruik een bestaande app).
* Een domeinnaam met toegang hebt tot het DNS-register voor uw domeinprovider zoals GoDaddy.
* Een persoonlijk certificaat (dat wil zeggen uw zelfondertekende certificaat) van een externe provider. Het certificaat moet overeenkomen met het domein.
* Een geïmplementeerd exemplaar van [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Certificaat importeren 
Voor de procedure voor het importeren van een certificaat moet het PEM-of PFX-bestand op een schijf staan en moet u de persoonlijke sleutel hebben. 

Uw certificaat uploaden naar de sleutelkluis:
1. Ga naar uw sleutelkluis-exemplaar.
1. Klik op **Certificaten** in het linkernavigatievenster.
1. Klik in het bovenste menu op **Genereren/importeren**.
1. Selecteer in het dialoogvenster **Een certificaat maken** onder **Methode voor het maken van certificaten**`Import`.
1. Ga in **Certificaatbestand uploaden** naar de locatie van het certificaat en selecteer dit.
1. Voer onder **Wachtwoord** de persoonlijke sleutel voor uw certificaat in.
1. Klik op **Create**.

    ![Certificaat 1 importeren](./media/custom-dns-tutorial/import-certificate-a.png)

Azure Spring Cloud toegang verlenen tot uw sleutelkluis voordat u het certificaat importeert:
1. Ga naar uw sleutelkluis-exemplaar.
1. Klik op **Toegangsbeleid** in het linkernavigatievenster.
1. Klik in het bovenste menu op **Toegangsbeleid toevoegen**.
1. Vul de informatie in en klik op de knop **Toevoegen** en toegangsbeleid **Opslaan**.

| Geheime machtiging | Certificaatmachtiging | Principal selecteren |
|--|--|--|
| Ophalen, Lijst | Ophalen, Lijst | Domeinbeheer van Azure Spring Cloud |

![Certificaat 2 importeren](./media/custom-dns-tutorial/import-certificate-b.png)

Of u kunt de Azure CLI gebruiken om Azure Spring Cloud toegang te verlenen tot de sleutelkluis.

Haal de object-id op via de volgende opdracht.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

Verleen Azure Spring Cloud toegang tot de sleutelkluis, vervang de object-id in de volgende opdracht.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Om een certificaat importeren naar Azure Spring Cloud:
1. Ga naar uw service-exemplaar. 
1. Selecteer **TLS/SSL-instellingen**vanuit het navigatiedeelvenster aan de linkerkant van uw app.
1. Klik vervolgens op **Key Vault-certificaat importeren**.

    ![Certificaat importeren](./media/custom-dns-tutorial/import-certificate.png)

U kunt ook de Azure CLI gebruiken om het certificaat te importeren:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Zorgt dat u Azure Spring Cloud toegang verleent tot uw sleutelkluis voordat u de vorige opdracht certificaat importeren uitvoert. Als u dat nog niet hebt gedaan, kunt u de volgende opdracht uitvoeren om de toegangsrechten te verlenen.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Wanneer u uw certificaat hebt geïmporteerd, ziet u dit in de lijst met **Persoonlijke sleutelcertificaten**.

![Certificaat met een persoonlijke sleutel](./media/custom-dns-tutorial/key-certificates.png)

U kunt ook de Azure CLI gebruiken om een lijst met certificaten weer te geven:

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u het certificaat nog steeds binden aan een specifiek domein. Volg de stappen in dit document onder de kop **SSL-binding toevoegen**.

## <a name="add-custom-domain"></a>Aangepast domein toevoegen
U kunt een CNAME-record gebruiken om een aangepaste DNS-naam toe te wijzen aan Azure Spring Cloud. 

> [!NOTE] 
> De A-record wordt niet ondersteund. 

### <a name="create-the-cname-record"></a>Het CNAME-record maken
Ga naar uw DNS-provider en voeg een CNAME-record toe om uw domein toe te wijzen aan de <service_name>.azuremicroservices.io. Hier is <service_name> de naam van uw Azure Spring Cloud-exemplaar. We ondersteunen het Wildcard-domein en -subdomein. Nadat u de CNAME toevoegt, lijkt de pagina DNS-records op het volgende voorbeeld: 

![Pagina met DNS-records](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Uw aangepaste domein toewijzen aan Azure Spring Cloud-app
Als u geen toepassing in Azure Spring Cloud hebt, volgt u de instructies in [Snelstart: Start een bestaande Azure Spring Cloud-toepassing met behulp van het Azure- portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Ga naar de toepassingspagina.

1. Selecteer **Aangepast domein**.
2. Vervolgens **Aangepast domein toevoegen**. 

    ![Aangepast domein](./media/custom-dns-tutorial/custom-domain.png)

3. Typ de volledig gekwalificeerde domeinnaam in waarvoor u een CNAME-record zoals www.contoso.com heeft toegevoegd. Zorg ervoor dat het Hostnaam-recordtype is ingesteld op CNAME (<service_name>.azuremicroservices.io)
4. Klik op **Valideren** om de knop **Toevoegen** in te schakelen.
5. Klik op **Add**.

    ![Aangepast domein toevoegen](./media/custom-dns-tutorial/add-custom-domain.png)

U kunt ook de Azure CLI gebruiken om een aangepast domein toe te voegen:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

Eén app kan meerdere domeinen hebben, maar één domein kan slechts aan één app worden toegewezen. Wanneer u uw aangepaste domein hebt toegewezen aan de app, ziet u dit in de tabel aangepast domein.

![Aangepast domeintabel](./media/custom-dns-tutorial/custom-domain-table.png)

U kunt ook de Azure CLI gebruiken om een lijst met aangepaste domeinen weer te geven:
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Een **Niet beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt een fout of waarschuwing.

## <a name="add-ssl-binding"></a>SSL-binding toevoegen
Selecteer in de tabel aangepast domein **SSL-binding toevoegen** zoals in de vorige afbeelding wordt weergegeven.  
1. Selecteer uw **Certificaat** of importeer het.
1. Klik op **Opslaan**.

    ![SSL-binding toevoegen](./media/custom-dns-tutorial/add-ssl-binding.png)

U kunt ook de Azure CLI gebruiken om **SSL- binding toe te voegen**:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Nadat u een SSL-binding hebt toegevoegd, is de status van het domein beveiligd: **In orde**. 

![SSL-binding toevoegen](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS afdwingen
Standaard kan iedereen nog steeds toegang krijgen tot uw app met behulp van HTTP, maar u kunt alle HTTP-aanvragen omleiden naar de HTTPS-poort.

Selecteer in het linkernavigatievenster van de app-pagina **Aangepaste domein**. Stel vervolgens op **Alleen HTTPS**, in op *Waar*.

![SSL-binding toevoegen](./media/custom-dns-tutorial/enforce-http.png)

U kunt ook de Azure CLI gebruiken om HTTPS af te dwingen:
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

Wanneer de bewerking is voltooid, gaat u naar een van de HTTPS-URL's die naar uw app verwijzen. Houd er rekening mee dat HTTP-URL's niet werken.

## <a name="see-also"></a>Zie ook
* [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Certificaat importeren](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Uw Spring Cloud-app starten met behulp van de Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

