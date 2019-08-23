---
title: Webservices beveiligen met SSL
titleSuffix: Azure Machine Learning service
description: Meer informatie over het beveiligen van een webservice die wordt geïmplementeerd via de Azure Machine Learning-service door HTTPS in te scha kelen. HTTPS beveiligt gegevens van clients door middel van TLS (trans port Layer Security), een vervanging van SSL (Secure Socket Layers). Clients gebruiken ook HTTPS om de identiteit van de webservice te verifiëren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 5a2cab9dff4a075545d919cb41e72cf6e446e9d2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897330"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>SSL gebruiken om een webservice te beveiligen via Azure Machine Learning

In dit artikel wordt beschreven hoe u een webservice kunt beveiligen die via de Azure Machine Learning-service is geïmplementeerd.

U gebruikt [https](https://en.wikipedia.org/wiki/HTTPS) om de toegang tot webservices te beperken en de gegevens te beveiligen die door clients worden verzonden. Met HTTPS kunt u de communicatie tussen een client en een webservice beveiligen door de communicatie tussen de twee te versleutelen. Versleuteling maakt gebruik van [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS wordt soms nog steeds aangeduid als *Secure Sockets Layer* (SSL). Dit is de voorafgaande taak van TLS.

> [!TIP]
> De Azure Machine Learning SDK gebruikt de term ' SSL ' voor eigenschappen die betrekking hebben op beveiligde communicatie. Dit betekent niet dat uw webservice geen gebruik maakt van *TLS*. SSL is slechts een vaker herken bare periode.

TLS en SSL zijn beide afhankelijk van *digitale certificaten*, die u helpen bij het versleutelen en verifiëren van de identiteit. Zie de [open bare-sleutel infrastructuur](https://en.wikipedia.org/wiki/Public_key_infrastructure)van het Wikipedia-onderwerp voor meer informatie over de werking van digitale certificaten.

> [!WARNING]
> Als u geen HTTPS gebruikt voor uw webservice, zijn gegevens die naar en van de service worden verzonden mogelijk zichtbaar voor anderen op internet.
>
> HTTPS zorgt er ook voor dat de client de authenticiteit van de server waarmee verbinding wordt gemaakt, verifieert. Deze functie beschermt clients tegen [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) -aanvallen.

Dit is het algemene proces voor het beveiligen van een webservice:

1. Een domeinnaam krijgen.

2. Een digitaal certificaat ophalen.

3. De webservice implementeren of bijwerken met SSL ingeschakeld.

4. Werkt u uw DNS om te verwijzen naar de webservice.

> [!IMPORTANT]
> Als u implementeert in azure Kubernetes service (AKS), kunt u uw eigen certificaat kopen of een certificaat gebruiken dat door micro soft wordt verschaft. Als u een certificaat van micro soft gebruikt, hoeft u geen domein naam of SSL-certificaat op te halen. Zie de sectie [SSL inschakelen en implementeren](#enable) in dit artikel voor meer informatie.

Er zijn kleine verschillen bij het beveiligen van webservices over [implementatie doelen](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Een domeinnaam krijgen

Als u nog geen domein naam hebt, kunt u er een aanschaffen bij een *domein naam registratie*. Het proces en de prijs verschillen per registratie. De registratie service voorziet in hulpprogram ma's voor het beheren van de domein naam. U kunt deze hulpprogram ma's gebruiken om een Fully Qualified Domain Name (FQDN) (zoals www\.contoso.com) toe te wijzen aan het IP-adres dat als host fungeert voor uw webservice.

## <a name="get-an-ssl-certificate"></a>Een SSL-certificaat ophalen

Er zijn veel manieren om een SSL-certificaat (digitaal certificaat) te verkrijgen. Het meest voorkomende is om een van een certificerings *instantie* (CA) te kopen. Ongeacht waar u het certificaat krijgt, hebt u de volgende bestanden nodig:

* Een **certificaat**. Het certificaat moet de volledige certificaat keten bevatten en moet ' PEM-encoded ' zijn.
* Een **sleutel**. De sleutel moet ook worden PEM-gecodeerd.

Wanneer u een certificaat aanvraagt, moet u de FQDN-namen opgeven van het adres dat u wilt gebruiken voor de webservice (bijvoorbeeld www\.-contoso.com). Het adres dat is gestempeld in het certificaat en het adres dat de clients gebruiken, worden vergeleken om de identiteit van de webservice te controleren. Als deze adressen niet overeenkomen, wordt er een fout bericht weer gegeven.

> [!TIP]
> Als de certificerings instantie het certificaat en de sleutel niet kan leveren als met PEM gecodeerde bestanden, kunt u een hulp programma zoals [openssl](https://www.openssl.org/) gebruiken om de indeling te wijzigen.

> [!WARNING]
> Gebruik zelfondertekende certificaten alleen voor ontwikkeling. Gebruik deze niet in productie omgevingen. Zelfondertekende certificaten kunnen leiden tot problemen in uw client toepassingen. Zie de documentatie voor de netwerk bibliotheken die uw client toepassing gebruikt voor meer informatie.

## <a id="enable"></a>SSL inschakelen en implementeren

Als u de service wilt implementeren (of opnieuw wilt implementeren) met SSL ingeschakeld, stelt u de para meter *ssl_enabled* in op ' True ', waar dit van toepassing is. Stel de para meter *ssl_certificate* in op de waarde van het *certificaat* bestand. Stel de *ssl_key* in op de waarde van het *sleutel* bestand.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implementeren op AKS en veld-Programmeer bare poort matrix (FPGA)

  > [!NOTE]
  > De informatie in deze sectie is ook van toepassing wanneer u een beveiligde webservice voor de visuele interface implementeert. Als u niet bekend bent met het gebruik van de python-SDK, raadpleegt u [Wat is de Azure machine learning SDK voor python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Wanneer u implementeert in AKS, kunt u een nieuw AKS-cluster maken of een bestaande toevoegen.
  
-  Als u een nieuw cluster maakt, gebruikt u **[AksCompute. provisionining_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Als u een bestaand cluster koppelt, gebruikt u **[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Beide retour neren een configuratie object dat een **enable_ssl** -methode heeft.

De methode **enable_ssl** kan gebruikmaken van een certificaat dat door micro soft wordt verschaft of een certificaat dat u koopt.

  * Wanneer u een certificaat van micro soft gebruikt, moet u de para meter *leaf_domain_label* gebruiken. Met deze para meter wordt de DNS-naam voor de service gegenereerd. De waarde ' myservice ' maakt bijvoorbeeld een domein naam van myservice\<zes wille keurige tekens >.\< azureregio >. cloudapp. Azure. com ', waarbij \<azureregio > de regio is waarin de service is opgenomen. U kunt desgewenst de para meter *overwrite_existing_domain* gebruiken om de bestaande *leaf_domain_label*te overschrijven.

    Als u de service wilt implementeren (of opnieuw wilt implementeren) met SSL ingeschakeld, stelt u de para meter *ssl_enabled* in op ' True ', waar dit van toepassing is. Stel de para meter *ssl_certificate* in op de waarde van het *certificaat* bestand. Stel de *ssl_key* in op de waarde van het *sleutel* bestand.

    > [!IMPORTANT]
    > Wanneer u een certificaat van micro soft gebruikt, hoeft u geen eigen certificaat of domein naam aan te schaffen.

    In het volgende voor beeld ziet u hoe u een configuratie maakt waarmee een SSL-certificaat van micro soft wordt ingeschakeld:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Wanneer u *een certificaat gebruikt dat u hebt aangeschaft*, gebruikt u de para meters *ssl_cert_pem_file*, *ssl_key_pem_file*en *ssl_cname* . In het volgende voor beeld ziet u hoe u *. pem* -bestanden kunt gebruiken om een configuratie te maken die gebruikmaakt van een SSL-certificaat dat u hebt aangeschaft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Zie [AksProvisioningConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) en [AksAttachConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)voor meer informatie over *enable_ssl*.

### <a name="deploy-on-azure-container-instances"></a>Implementeren op Azure Container Instances

Wanneer u implementeert op Azure Container Instances, geeft u waarden op voor SSL-gerelateerde para meters, zoals in het volgende code fragment wordt weer gegeven:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Zie [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)voor meer informatie.

## <a name="update-your-dns"></a>Werkt u uw DNS

Vervolgens moet u uw DNS om te verwijzen naar de webservice bijwerken.

+ **Voor Container Instances:**

  Gebruik de hulpprogram ma's uit uw domein naam REGI ster om de DNS-record voor uw domein naam bij te werken. De record moet verwijzen naar het IP-adres van de service.

  Er kan een vertraging van minuten of uren zijn voordat clients de domein naam kunnen omzetten, afhankelijk van de registratie en de TTL (time to Live) die is geconfigureerd voor de domein naam.

+ **Voor AKS:**

  > [!WARNING]
  > Als u *leaf_domain_label* hebt gebruikt om de service te maken met behulp van een certificaat van micro soft, moet u de DNS-waarde voor het cluster niet hand matig bijwerken. De waarde moet automatisch worden ingesteld.

  Werk de DNS van het open bare IP-adres van de AKS-cluster op het tabblad **configuratie** onder **instellingen** in het linkerdeel venster. (Zie de volgende afbeelding.) Het open bare IP-adres is een resource type dat wordt gemaakt onder de resource groep die de AKS-agent knooppunten en andere netwerk bronnen bevat.

  [![Azure Machine Learning-service: Webservices beveiligen met SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>Het SSL-certificaat bijwerken

SSL-certificaten verlopen en moeten worden vernieuwd. Dit gebeurt meestal elk jaar. Gebruik de informatie in de volgende secties om uw certificaat bij te werken en te vernieuwen voor modellen die zijn geïmplementeerd in azure Kubernetes service:

### <a name="update-a-microsoft-generated-certificate"></a>Een door micro soft gegenereerd certificaat bijwerken

Als het certificaat oorspronkelijk is gegenereerd door micro soft (als u de *leaf_domain_label* gebruikt om de service te maken), gebruikt u een van de volgende voor beelden om het certificaat bij te werken:

**De SDK gebruiken**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**De CLI gebruiken**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Zie voor meer informatie de volgende naslag documenten:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aangepast certificaat bijwerken

Als het certificaat oorspronkelijk is gegenereerd door een certificerings instantie, gebruikt u de volgende stappen:

1. Gebruik de documentatie van de certificerings instantie om het certificaat te vernieuwen. Met dit proces worden nieuwe certificaat bestanden gemaakt.

1. Gebruik de SDK of CLI om de service bij te werken met het nieuwe certificaat:

    **De SDK gebruiken**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **De CLI gebruiken**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Zie voor meer informatie de volgende naslag documenten:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>SSL uitschakelen

Als u SSL wilt uitschakelen voor een model dat is geïmplementeerd in azure Kubernetes `SslConfiguration` service `status="Disabled"`, maakt u een met en voert u een update uit:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Volgende stappen
Leer hoe u het volgende doet:
+ [Een machine learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
+ [Veilig experimenten en demijnen uitvoeren in een virtueel Azure-netwerk](how-to-enable-virtual-network.md)
