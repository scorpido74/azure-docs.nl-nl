---
title: 'Zelf studie: een bestaand aangepast domein toewijzen aan een Azure lente-Cloud'
description: Een bestaande naam van een aangepaste gedistribueerde naam service (DNS) toewijzen aan een Azure lente-Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176992"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Een bestaand aangepast domein toewijzen aan Azure lente-Cloud
Distributed name service (DNS) is een techniek voor het opslaan van namen van netwerk knooppunten in een netwerk. In deze zelf studie wordt een domein, zoals www.contoso.com, gekoppeld met behulp van een CNAME-record. Het aangepaste domein wordt beveiligd met een certificaat en laat zien hoe u Transport Layer Security (TLS) afdwingt, ook wel bekend als Secure Sockets Layer (SSL). 

Certificaten versleutelen webverkeer. Deze TLS/SSL-certificaten kunnen worden opgeslagen in Azure Key Vault. 

## <a name="prerequisites"></a>Vereisten
* Een toepassing die is geïmplementeerd in azure lente Cloud (Zie [Quick Start: een bestaande Azure lente-Cloud toepassing starten met behulp van de Azure Portal](spring-cloud-quickstart-launch-app-portal.md)of een bestaande app gebruiken).
* Een domein naam met toegang tot het DNS-REGI ster voor de domein provider, zoals GoDaddy.
* Een persoonlijk certificaat van een provider van derden. Het certificaat moet overeenkomen met het domein.
* Een geïmplementeerd exemplaar van [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Certificaat importeren 
Voor de procedure voor het importeren van een certificaat moet het PEM-of PFX-bestand op schijf zijn en moet u de persoonlijke sleutel hebben. 

Uw certificaat uploaden naar de sleutel kluis:
1. Ga naar uw sleutel kluis-exemplaar.
1. Klik in het navigatie deel venster links op **certificaten**.
1. Klik in het bovenste menu op **genereren/importeren**.
1. Selecteer in het dialoog venster **een certificaat maken** onder **methode voor het maken van een certificaat**de optie `Import`.
1. Onder **certificaat bestand uploaden**gaat u naar de locatie van het certificaat en selecteert u dit.
1. Voer onder **wacht woord**de persoonlijke sleutel voor uw certificaat in.
1. Klik op **maken**.

![Certificaat importeren 1](./media/custom-dns-tutorial/import-certificate-a.png)

Certificaat importeren in azure lente-Cloud:
1. Ga naar uw service-exemplaar. 
1. Selecteer de **instellingen voor TLS/SSL**in het navigatie deel venster aan de linkerkant van uw app.
1. Klik vervolgens op **Key Vault certificaat importeren**.

![Certificaat importeren](./media/custom-dns-tutorial/import-certificate.png)

Wanneer u uw certificaat hebt geïmporteerd, ziet u dit in de lijst met **persoonlijke sleutel certificaten**.

![Certificaat voor persoonlijke sleutel](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u het certificaat nog steeds binden aan een specifiek domein. Volg de stappen in dit document onder de kop **SSL-binding toevoegen**.

## <a name="add-custom-domain"></a>Aangepast domein toevoegen
U kunt een CNAME-record gebruiken om een aangepaste DNS-naam toe te wijzen aan een Azure lente-Cloud. 

> [!NOTE] 
> De A-record wordt niet ondersteund. 

### <a name="create-the-cname-record"></a>Het CNAME-record maken
Ga naar uw DNS-provider en voeg een CNAME-record toe om uw domein toe te wijzen aan de <service_name>. azuremicroservices.io. Hier <service_name> de naam is van uw Azure veer Cloud-exemplaar. Het Joker teken domein en subdomein worden ondersteund. Nadat u de CNAME hebt toegevoegd, lijkt de pagina met DNS-records op het volgende voor beeld: 

![Pagina met DNS-records](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Uw aangepaste domein toewijzen aan Azure lente Cloud-app
Als u geen toepassing in azure lente Cloud hebt, volgt u de instructies in [Quick Start: een bestaande Azure lente-Cloud toepassing starten met behulp van de Azure Portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Ga naar de toepassings pagina.

1. Selecteer **aangepast domein**.
2. **Voeg vervolgens aangepast domein toe**. 

![Aangepast domein](./media/custom-dns-tutorial/custom-domain.png)

3. Typ de Fully Qualified Domain Name waarvoor u een CNAME-record hebt toegevoegd, zoals www.contoso.com. Zorg ervoor dat het hostnaam-record type is ingesteld op CNAME (<service_name>. azuremicroservices.io)
4. Klik op **valideren** om de knop **toevoegen** in te scha kelen.
5. Klik op **Add**.

![Aangepast domein toevoegen](./media/custom-dns-tutorial/add-custom-domain.png)

Eén app kan meerdere domeinen hebben, maar één domein kan slechts aan één app worden toegewezen. Wanneer u uw aangepaste domein hebt toegewezen aan de app, ziet u dit in de tabel aangepast domein.

![Aangepaste domein tabel](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> Een **niet-beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt een fout of waarschuwing.

## <a name="add-ssl-binding"></a>SSL-binding toevoegen
Selecteer in de tabel aangepast domein de optie **SSL-binding toevoegen** , zoals wordt weer gegeven in de vorige afbeelding.  
1. Selecteer uw **certificaat** of importeer het.
1. Klik op **Opslaan**.

![SSL-binding toevoegen](./media/custom-dns-tutorial/add-ssl-binding.png)

Nadat u een SSL-binding hebt toegevoegd, is de status van het domein veilig: **in orde**. 

![SSL-binding toevoegen](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS afdwingen
Standaard kan iedereen nog steeds toegang krijgen tot uw app met behulp van HTTP, maar u kunt alle HTTP-aanvragen omleiden naar de HTTPS-poort.

Selecteer op de pagina van de app in de linkernavigatiebalk een **aangepast domein**. Stel vervolgens **alleen https**in op *waar*.

![SSL-binding toevoegen](./media/custom-dns-tutorial/enforce-http.png)

Wanneer de bewerking is voltooid, gaat u naar een van de HTTPS-Url's die naar uw app verwijzen. Houd er rekening mee dat HTTP-Url's niet werken.

## <a name="see-also"></a>Zie ook
* [Wat is Azure Sleutelkluis?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Certificaat importeren](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Start uw lente Cloud-app met behulp van de Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

