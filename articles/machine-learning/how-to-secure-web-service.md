---
title: Beveiligde webservices met TLS
titleSuffix: Azure Machine Learning
description: Meer informatie over het inschakelen van HTTPS om een webservice te beveiligen die is geïmplementeerd via Azure Machine Learning. Azure Machine Learning gebruikt TLS-versie 1.2 om modellen te beveiligen die als webservices zijn geïmplementeerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287336"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS gebruiken om een webservice te beveiligen via Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel ziet u hoe u een webservice beveiligen die is geïmplementeerd via Azure Machine Learning.

U gebruikt [HTTPS](https://en.wikipedia.org/wiki/HTTPS) om de toegang tot webservices te beperken en de gegevens die clients indienen te beveiligen. HTTPS helpt de communicatie tussen een client en een webservice te beveiligen door de communicatie tussen de twee te versleutelen. Versleuteling maakt gebruik [van Transport Layer Security (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS wordt soms nog steeds aangeduid als *Secure Sockets Layer* (SSL), dat was de voorganger van TLS.

> [!TIP]
> De Azure Machine Learning SDK gebruikt de term 'SSL' voor eigenschappen die gerelateerd zijn aan beveiligde communicatie. Dit betekent niet dat uw webservice geen *TLS*gebruikt. SSL is gewoon een meer algemeen erkende term.
>
> Webservices die worden geïmplementeerd via Azure Machine Learning ondersteunen met name TLS-versie 1.2.

TLS en SSL zijn beide afhankelijk van *digitale certificaten*, die helpen bij versleuteling en identiteitsverificatie. Zie het Wikipedia-onderwerp [Public key infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure)voor meer informatie over hoe digitale certificaten werken.

> [!WARNING]
> Als u geen HTTPS gebruikt voor uw webservice, zijn gegevens die van en naar de service worden verzonden mogelijk zichtbaar voor anderen op internet.
>
> HTTPS stelt de client ook in staat om de authenticiteit van de server waarmee de server verbinding maakt te verifiëren. Deze functie beschermt klanten tegen [man-in-the-middle-aanvallen.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Dit is het algemene proces om een webservice te beveiligen:

1. Een domeinnaam.

2. Ontvang een digitaal certificaat.

3. De webservice implementeren of bijwerken met TLS ingeschakeld.

4. Werk uw DNS bij om naar de webservice te wijzen.

> [!IMPORTANT]
> Als u implementeert naar Azure Kubernetes Service (AKS), u uw eigen certificaat kopen of een certificaat gebruiken dat door Microsoft wordt geleverd. Als u een certificaat van Microsoft gebruikt, hoeft u geen domeinnaam of TLS/SSL-certificaat te krijgen. Zie het gedeelte [TLS inschakelen en implementeren](#enable) van dit artikel voor meer informatie.

Er zijn kleine verschillen wanneer u s voor [implementatiedoelen](how-to-deploy-and-where.md)beveiligt.

## <a name="get-a-domain-name"></a>Een domeinnaam opmaken

Als u nog geen domeinnaam hebt, koopt u er een bij een *domeinnaamregistrar.* Het proces en de prijs verschillen tussen registrars. De registrar biedt tools om de domeinnaam te beheren. U gebruikt deze tools om een volledig gekwalificeerde domeinnaam (FQDN) (zoals www\.contoso.com) toe te stellen aan het IP-adres dat uw webservice host.

## <a name="get-a-tlsssl-certificate"></a>Een TLS/SSL-certificaat aanvragen

Er zijn veel manieren om een TLS/SSL-certificaat (digitaal certificaat) te krijgen. De meest voorkomende is om er een te kopen bij een *certificaatautoriteit* (CA). Waar u het certificaat ook vandaan haalt, u hebt de volgende bestanden nodig:

* Een **certificaat**. Het certificaat moet de volledige certificaatketen bevatten en moet "PEM-gecodeerd" zijn.
* Een **sleutel.** De sleutel moet ook PEM-gecodeerd zijn.

Wanneer u een certificaat aanvraagt, moet u de FQDN opgeven van het adres\.dat u voor de webservice wilt gebruiken (bijvoorbeeld contoso.com www). Het adres dat in het certificaat is gestempeld en het adres dat de clients gebruiken, worden vergeleken om de identiteit van de webservice te verifiëren. Als deze adressen niet overeenkomen, krijgt de client een foutmelding.

> [!TIP]
> Als de certificaatautoriteit het certificaat en de sleutel niet kan leveren als PEM-gecodeerde bestanden, u een hulpprogramma zoals [OpenSSL](https://www.openssl.org/) gebruiken om de indeling te wijzigen.

> [!WARNING]
> Gebruik *zelfondertekende* certificaten alleen voor ontwikkeling. Gebruik ze niet in productieomgevingen. Zelfondertekende certificaten kunnen problemen veroorzaken in uw clienttoepassingen. Zie de documentatie voor de netwerkbibliotheken die uw clienttoepassing gebruikt voor meer informatie.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>TLS inschakelen en implementeren

Als u de service wilt implementeren (of opnieuw wilt implementeren) met TLS ingeschakeld, stelt u de *parameter ssl_enabled* in op 'True' waar deze ook van toepassing is. Stel de *parameter ssl_certificate* in op de waarde van het *certificaatbestand.* Stel de *ssl_key* in op de waarde van het *sleutelbestand.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implementeren op AKS en veldprogrammeerbare gatearray (FPGA)

  > [!NOTE]
  > De informatie in deze sectie is ook van toepassing wanneer u een beveiligde webservice voor de ontwerper implementeert. Zie Wat is de Azure Machine Learning SDK voor Python als u niet bekend bent met het gebruik van de Python [SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Wanneer u implementeert op AKS, u een nieuw AKS-cluster maken of een bestaand cluster toevoegen. Zie Een model implementeren voor een [Azure Kubernetes Service-cluster voor](how-to-deploy-azure-kubernetes-service.md)meer informatie over het maken of koppelen van een cluster.
  
-  Als u een nieuw cluster maakt, gebruikt u **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Als u een bestaand cluster koppelt, gebruikt u **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Beide retourneren een configuratieobject met een **enable_ssl-methode.**

De **enable_ssl** methode kan een certificaat gebruiken dat wordt geleverd door Microsoft of een certificaat dat u koopt.

  * Wanneer u een certificaat van Microsoft gebruikt, moet u de *parameter leaf_domain_label* gebruiken. Met deze parameter wordt de DNS-naam voor de service gegenereerd. Een waarde van 'contoso' maakt bijvoorbeeld een domeinnaam\<van 'contoso six-random-characters>. \<azureregion>.cloudapp.azure.com", \<waarbij azureregion> de regio is die de service bevat. Optioneel u de *parameter overwrite_existing_domain* gebruiken om de bestaande *leaf_domain_label*te overschrijven.

    Als u de service wilt implementeren (of opnieuw wilt implementeren) met TLS ingeschakeld, stelt u de *parameter ssl_enabled* in op 'True' waar deze ook van toepassing is. Stel de *parameter ssl_certificate* in op de waarde van het *certificaatbestand.* Stel de *ssl_key* in op de waarde van het *sleutelbestand.*

    > [!IMPORTANT]
    > Wanneer u een certificaat van Microsoft gebruikt, hoeft u uw eigen certificaat of domeinnaam niet te kopen.

    In het volgende voorbeeld wordt uitgelegd hoe u een configuratie maakt waarmee een TLS/SSL-certificaat van Microsoft wordt gemaakt:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Wanneer u een certificaat gebruikt *dat u hebt gekocht,* gebruikt u de *ssl_cert_pem_file,* *ssl_key_pem_file*en *ssl_cname* parameters. In het volgende voorbeeld wordt uitgelegd hoe *u .pem-bestanden* gebruiken om een configuratie te maken die gebruikmaakt van een TLS/SSL-certificaat dat u hebt gekocht:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Zie [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) en [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)voor meer informatie over *enable_ssl.*

### <a name="deploy-on-azure-container-instances"></a>Implementeren op Azure Container Instances

Wanneer u implementeert naar Azure Container Instances, geeft u waarden op voor TLS-gerelateerde parameters, zoals in het volgende codefragment wordt weergegeven:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Zie [AciWebservice.deploy_configuration() voor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)meer informatie.

## <a name="update-your-dns"></a>Uw DNS bijwerken

Vervolgens moet u uw DNS bijwerken om naar de webservice te wijzen.

+ **Voor containerinstanties:**

  Gebruik de hulpprogramma's van uw domeinnaamregistrar om de DNS-record voor uw domeinnaam bij te werken. De record moet wijzen op het IP-adres van de service.

  Er kan een vertraging van minuten of uren zijn voordat clients de domeinnaam kunnen oplossen, afhankelijk van de registrar en de "time to live" (TTL) die is geconfigureerd voor de domeinnaam.

+ **Voor AKS:**

  > [!WARNING]
  > Als u *leaf_domain_label* hebt gebruikt om de service te maken met behulp van een certificaat van Microsoft, werkt u de DNS-waarde voor het cluster niet handmatig bij. De waarde moet automatisch worden ingesteld.

  Werk de DNS van het openbare IP-adres van het AKS-cluster bij op het tabblad **Configuratie** onder **Instellingen** in het linkerdeelvenster. (Zie de volgende afbeelding.) Het openbare IP-adres is een resourcetype dat is gemaakt onder de resourcegroep die de AKS-agentknooppunten en andere netwerkbronnen bevat.

  [![Azure Machine Learning: webservices beveiligen met TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Het TLS/SSL-certificaat bijwerken

TLS/SSL-certificaten verlopen en moeten worden verlengd. Meestal gebeurt dit elk jaar. Gebruik de informatie in de volgende secties om uw certificaat bij te werken en te vernieuwen voor modellen die zijn geïmplementeerd in Azure Kubernetes Service:

### <a name="update-a-microsoft-generated-certificate"></a>Een door Microsoft gegenereerd certificaat bijwerken

Als het certificaat oorspronkelijk door Microsoft is gegenereerd (wanneer de *leaf_domain_label* wordt gebruikt om de service te maken), gebruikt u een van de volgende voorbeelden om het certificaat bij te werken:

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

Zie de volgende referentiedocumenten voor meer informatie:

* [SslConfiguratie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguratie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aangepast certificaat bijwerken

Als het certificaat oorspronkelijk is gegenereerd door een certificaatautoriteit, gebruikt u de volgende stappen:

1. Gebruik de documentatie van de certificaatautoriteit om het certificaat te vernieuwen. Met dit proces worden nieuwe certificaatbestanden gemaakt.

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

Zie de volgende referentiedocumenten voor meer informatie:

* [SslConfiguratie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguratie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>TLS uitschakelen

Als u TLS wilt uitschakelen voor een model `SslConfiguration` `status="Disabled"`dat is geïmplementeerd in Azure Kubernetes Service, maakt u een met , voert u vervolgens een update uit:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Volgende stappen
Leer hoe u het volgende doet:
+ [Een machine learning-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
+ [Experimenten en gevolgtrekkingveilig uitvoeren binnen een virtueel Azure-netwerk](how-to-enable-virtual-network.md)
