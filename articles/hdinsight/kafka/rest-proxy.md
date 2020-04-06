---
title: Apache Kafka REST proxy - Azure HDInsight
description: Meer informatie over het uitvoeren van Apache Kafka-bewerkingen met een Kafka REST-proxy op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6bf34f8fb15bf8fddb1ba398ed678d5c98b8c84f
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667787"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interactie met Apache Kafka-clusters in Azure HDInsight met behulp van een REST-proxy

Met Kafka REST Proxy u communiceren met uw Kafka-cluster via een REST API via HTTP. Deze actie betekent dat uw Kafka-clients zich buiten uw virtuele netwerk kunnen bevinden. Clients kunnen eenvoudige HTTP-aanroepen naar het Kafka-cluster voeren, in plaats van te vertrouwen op Kafka-bibliotheken. In dit artikel ziet u hoe u een Kafka-cluster met REST-proxy maken. Biedt ook een voorbeeldcode die laat zien hoe u aanbelt naar REST-proxy.

## <a name="rest-api-reference"></a>Naslaginformatie over REST API

Zie [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)voor bewerkingen die worden ondersteund door de Kafka REST API.

## <a name="background"></a>Achtergrond

![Kafka REST proxy ontwerp](./media/rest-proxy/rest-proxy-architecture.png)

Zie [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)voor de volledige specificatie van bewerkingen die door de API worden ondersteund.

### <a name="rest-proxy-endpoint"></a>EINDPUNT REST Proxy

Als u een HDInsight Kafka-cluster maakt met REST-proxy, wordt een nieuw **Properties** openbaar eindpunt voor uw cluster gemaakt, dat u vinden in uw HDInsight-clustereigenschappen op de Azure-portal.

### <a name="security"></a>Beveiliging

Toegang tot de Kafka REST-proxy wordt beheerd met Azure Active Directory-beveiligingsgroepen. Wanneer u het Kafka-cluster maakt, geeft u de Azure AD-beveiligingsgroep restpointtoegang. Kafka-clients die toegang nodig hebben tot de REST-proxy moeten door de eigenaar van de groep worden geregistreerd. De groepseigenaar kan zich inschrijven via de Portal of via PowerShell.

Voor eindpuntaanvragen voor REST-proxy moeten clienttoepassingen een OAuth-token krijgen. Het token wordt gebruikt om het lidmaatschap van beveiligingsgroepen te verifiëren. Zoek hieronder een [voorbeeld van clienttoepassingen](#client-application-sample) dat laat zien hoe je een OAuth-token krijgen. De clienttoepassing geeft het OAuth-token in het HTTP-verzoek door aan de REST-proxy.

> [!NOTE]  
> Zie [App- en brontoegang beheren met Azure Active Directory-groepen](../../active-directory/fundamentals/active-directory-manage-groups.md)voor meer informatie over AAD-beveiligingsgroepen. Zie [Toegang tot Azure Active Directory-webtoepassingen autoriseren met behulp van de OAuth 2.0-codesubsidiestroom](../../active-directory/develop/v1-protocols-oauth-code.md)voor meer informatie over hoe OAuth-tokens werken.

## <a name="prerequisites"></a>Vereisten

1. U registreert een toepassing met Azure AD. De clienttoepassingen die u schrijft om te communiceren met de Kafka REST-proxy, gebruiken de id en het geheim van deze toepassing om te verifiëren aan Azure.

1. Maak een Azure AD-beveiligingsgroep. Voeg de toepassing die u hebt geregistreerd bij Azure AD toe aan de beveiligingsgroep als **lid** van de groep. Deze beveiligingsgroep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van Azure AD-groepen.

    De groep valideren is van type **Beveiliging**.
    ![Beveiligingsgroep](./media/rest-proxy/rest-proxy-group.png)

    Valideren dat de toepassing lid is van Groep.
    ![Lidmaatschap controleren](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Een Kafka-cluster maken met REST-proxy ingeschakeld

1. Schakel tijdens de werkstroom voor het maken van Kafka-cluster op het tabblad **Beveiliging + netwerken** de **proxyoptie Kafka REST in.**

     ![Kafka REST-proxy inschakelen en beveiligingsgroep selecteren](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klik **op Beveiligingsgroep selecteren**. Selecteer in de lijst met beveiligingsgroepen de beveiligingsgroep die u toegang wilt hebben tot de PROXY REST. U het zoekvak gebruiken om de juiste beveiligingsgroep te vinden. Klik onderin op de knop **Selecteren.**

     ![Kafka REST-proxy inschakelen en beveiligingsgroep selecteren](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Voer de resterende stappen uit om uw cluster te maken zoals beschreven in [Het cluster Apache Kafka maken in Azure HDInsight met Azure-portal.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Zodra het cluster is gemaakt, gaat u naar de clustereigenschappen om de URL van de Kafka REST-proxy op te nemen.

     ![REST-proxy-URL weergeven](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Voorbeeld van clienttoepassingen

U de onderstaande python-code gebruiken om te communiceren met de REST-proxy op uw Kafka-cluster. Voer de volgende stappen uit om het voorbeeld van de code te gebruiken:

1. Sla de voorbeeldcode op een machine op waarop Python is geïnstalleerd.
1. Installeer vereiste python-afhankelijkheden `pip3 install adal` `pip install msrestazure`door het uitvoeren en .
1. Wijzig de codesectie **Configureer deze eigenschappen** en werk de volgende eigenschappen voor uw omgeving bij:

    |Eigenschap |Beschrijving |
    |---|---|
    |Tenant-id|De Azure-tenant waar uw abonnement zich bevindt.|
    |Client-id|De id voor de toepassing die u hebt geregistreerd in de beveiligingsgroep.|
    |Clientgeheim|Het geheim voor de applicatie die u hebt geregistreerd in de beveiligingsgroep.|
    |Kafkarest_endpoint|Haal deze waarde op het tabblad **Eigenschappen** in het clusteroverzicht zoals beschreven in de [sectie implementatie](#create-a-kafka-cluster-with-rest-proxy-enabled). Het moet in het volgende formaat -`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Voer vanuit de opdrachtregel het python-bestand uit door uit te voeren`python <filename.py>`

Deze code doet de volgende actie:

1. Haalt een OAuth-token op uit Azure AD.
1. Laat zien hoe u een verzoek indienen bij Kafka REST proxy.

Zie [Python AuthenticationContext-klasse](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)voor meer informatie over het verkrijgen van OAuth-tokens in python. Mogelijk ziet u `topics` een vertraging terwijl deze niet is gemaakt of verwijderd via de Kafka REST-proxy daar wordt weergegeven. Deze vertraging is het gevolg van het vernieuwen van de cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Hieronder vindt u een ander voorbeeld over hoe u een token krijgen van Azure for REST-proxy met behulp van een opdracht krul. Merk op dat `resource=https://hib.azurehdinsight.net` we de opgegeven tijd nodig hebben terwijl we een token krijgen.

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&resource=https://hib.azurehdinsight.net' 'https://login.microsoftonline.com/<tenantid>/oauth2/token'
```

## <a name="next-steps"></a>Volgende stappen

* [Referentiedocumenten voor Kafka REST-proxy-API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
