---
title: Webservices beheren met API Management
titleSuffix: Azure Machine Learning Studio
description: Een handleiding waarin wordt getoond hoe voor het beheren van AzureML-webservices met API Management. De REST API-eindpunten beheren met het definiëren van gebruikerstoegang, beperking en dashboard bewaking.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 8b424696c3350ff2592df9a97189dde3df57845f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138563"
---
# <a name="manage-azure-machine-learning-studio-web-services-using-api-management"></a>Azure Machine Learning Studio-webservices met API Management beheren
## <a name="overview"></a>Overzicht
Deze handleiding laat zien hoe u snel aan de slag met API Management voor het beheren van uw Azure Machine Learning Studio-webservices.

## <a name="what-is-azure-api-management"></a>Wat is Azure API Management?
Azure API Management is een Azure-service waarmee u uw REST API-eindpunten beheren met het definiëren van gebruikerstoegang, beperking en bewaking van dashboard. Klik op [hier](https://azure.microsoft.com/services/api-management/) voor meer informatie over Azure API Management. Klik op [hier](/azure/api-management/import-and-publish) voor richtlijnen voor hoe u aan de slag met Azure API Management. Deze andere gids, die in deze handleiding is gebaseerd op, vindt u meer onderwerpen, waaronder melding configuraties, prijzen, antwoord verwerking, gebruikersverificatie, producten, developer-abonnementen en gebruik dashboarding maken.

## <a name="prerequisites"></a>Vereisten
Voor deze handleiding, hebt u het volgende nodig:

* Een Azure-account. Als u een Azure-account niet hebt, klikt u op [hier](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie over het maken van een gratis proefaccount.
* Een AzureML-account. Als u geen een AzureML-account hebt, klikt u op [hier](https://studio.azureml.net/) voor meer informatie over het maken van een gratis proefaccount.
* De werkruimte, service en api_key voor een AzureML-experiment geïmplementeerd als een webservice. Klik op [hier](create-experiment.md) voor meer informatie over het maken van een experiment AzureML. Klik op [hier](publish-a-machine-learning-web-service.md) voor meer informatie over het implementeren van een AzureML als een webservice experimenteren. Bijlage A bevat ook instructies voor het maken en testen van een eenvoudig AzureML-experiment implementeren als een webservice.

## <a name="create-an-api-management-instance"></a>Een API Management-exemplaar maken

U kunt uw Azure Machine Learning-webservice met een exemplaar van API Management kunt beheren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **+ Een resource maken**.
3. In het zoekvak typt u 'API management' en selecteer vervolgens de resource 'API management'.
4. Klik op **Create**.
5. De **naam** waarde wordt gebruikt om een unieke URL (in dit voorbeeld maakt gebruik van "demoazureml") te maken.
6. Selecteer een **abonnement**, **resourcegroep**, en **locatie** voor uw service-exemplaar.
7. Geef een waarde op voor **organisatienaam** (in dit voorbeeld maakt gebruik van 'demoazureml').
8. Voer uw **e-mailadres van beheerder** -dit e-mailadres wordt gebruikt voor meldingen van het API Management-systeem.
9. Klik op **Create**.

Het duurt maximaal 30 minuten voor een nieuwe service moet worden gemaakt.

![service maken](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>De API maken
Zodra het service-exemplaar is gemaakt, wordt de volgende stap is het maken van de API. Een API bestaat uit een reeks bewerkingen die vanuit een clienttoepassing kunnen worden aangeroepen. API-bewerkingen worden geproxied naar bestaande webservices. Deze handleiding maakt API's die proxy voor de bestaande AzureML RRS en BES-webservices.

Het maken van de API:

1. Open het service-exemplaar dat u zojuist hebt gemaakt in de Azure-portal.
2. Selecteer in het navigatiedeelvenster links **API's**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klik op **API toevoegen**.
2. Voer een **Web-API-naam** (in dit voorbeeld maakt gebruik van 'AzureML Demo API').
3. Voor **webservice-URL**, voer '`https://ussouthcentral.services.azureml.net`'.
4. Voer een ** Web API URL-achtervoegsel '. Hiermee wordt het laatste deel van de URL die klanten wordt gebruikt voor het verzenden van aanvragen naar het service-exemplaar (in dit voorbeeld maakt gebruik van 'azureml-demo').
5. Voor **Web API URL-schema**, selecteer **HTTPS**.
6. Voor **producten**, selecteer **Starter**.
7. Klik op **Opslaan**.


## <a name="add-the-operations"></a>De bewerkingen toevoegen

Bewerkingen zijn toegevoegd en geconfigureerd voor een API in de publicatieportal. Voor toegang tot de publicatieportal, klikt u op **publicatieportal** Selecteer in de Azure-portal voor uw API Management-service, **API's**, **Operations**, klikt u vervolgens op **Toevoegbewerking**.

![toevoegen-bewerking](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

De **nieuwe bewerking** venster wordt weergegeven en de **handtekening** tabblad wordt standaard geselecteerd.

## <a name="add-rrs-operation"></a>RRS-bewerking toevoegen
Maak eerst een bewerking voor de AzureML RRS-service:

1. Voor de **HTTP-term**, selecteer **POST**.
2. Voor de **URL sjabloon**, type '`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`'.
3. Voer een **weergavenaam** (in dit voorbeeld wordt "RRS uitvoeren").

   ![rrs-bewerking-handtekening toevoegen](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klik op **antwoorden** > **toevoegen** aan de linkerkant en selecteer **200 OK**.
5. Klik op **opslaan** om op te slaan met deze bewerking.

   ![toevoegen-rrs-bewerking-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES bewerkingen toevoegen

> [!NOTE]
> Schermafbeeldingen hier niet worden opgenomen voor de BES-bewerkingen zoals ze vergelijkbaar met die zijn voor het toevoegen van de RRS-bewerking.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Een uitvoering van de Batch-taak verzenden (maar niet worden gestart)

1. Klik op **toevoegbewerking** een BES-bewerking toevoegen aan de API.
2. Voor de **HTTP-term**, selecteer **POST**.
3. Voor de **URL sjabloon**, type '`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`'.
4. Voer een **weergavenaam** (in dit voorbeeld wordt "BES verzenden").
5. Klik op **antwoorden** > **toevoegen** aan de linkerkant en selecteer **200 OK**.
6. Klik op **Opslaan**.

### <a name="start-a-batch-execution-job"></a>Een uitvoering van de Batch-taak starten

1. Klik op **toevoegbewerking** een BES-bewerking toevoegen aan de API.
2. Voor de **HTTP-term**, selecteer **POST**.
3. Voor de **HTTP-term**, type '`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`'.
4. Voer een **weergavenaam** (in dit voorbeeld wordt 'BES Start').
6. Klik op **antwoorden** > **toevoegen** aan de linkerkant en selecteer **200 OK**.
7. Klik op **Opslaan**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>De status of het resultaat van een taak met Batch niet uitvoeren

1. Klik op **toevoegbewerking** een BES-bewerking toevoegen aan de API.
2. Voor de **HTTP-term**, selecteer **ophalen**.
3. Voor de **URL sjabloon**, type '`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`'.
4. Voer een **weergavenaam** (in dit voorbeeld maakt gebruik van 'BES-Status').
6. Klik op **antwoorden** > **toevoegen** aan de linkerkant en selecteer **200 OK**.
7. Klik op **Opslaan**.

### <a name="delete-a-batch-execution-job"></a>Een uitvoering van de Batch-taak verwijderen

1. Klik op **toevoegbewerking** een BES-bewerking toevoegen aan de API.
2. Voor de **HTTP-term**, selecteer **verwijderen**.
3. Voor de **URL sjabloon**, type '`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`'.
4. Voer een **weergavenaam** (in dit voorbeeld wordt "BES Delete").
5. Klik op **antwoorden** > **toevoegen** aan de linkerkant en selecteer **200 OK**.
6. Klik op **Opslaan**.

## <a name="call-an-operation-from-the-developer-portal"></a>Een bewerking aanroepen vanuit de portal voor ontwikkelaars

Bewerkingen kunnen rechtstreeks vanuit de portal voor ontwikkelaars, waarmee u een handige manier om te bekijken en te testen van de bewerkingen van een API worden aangeroepen. In deze stap wordt u gebeld de **RRS uitvoeren** methode die is toegevoegd aan de **AzureML Demo API**. 

1. Klik op **ontwikkelaarsportal**.

   ![Developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klik op **API's** van het bovenste menu en klik vervolgens op **AzureML Demo API** om te zien welke bewerkingen beschikbaar.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selecteer **RRS uitvoeren** voor de bewerking. Klik op **uitproberen**.

   ![Try it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Voor **aanvraagparameters**, type de **werkruimte** en **service**, type "2.0 voor de **apiversion**, en 'true' voor de **details**. U vindt uw **werkruimte** en **service** in het dashboard van de webservice AzureML (Zie **testen van de webservice** in bijlage A).

   Voor **aanvraagheaders**, klikt u op **koptekst toevoegen** en typ 'Content-Type' en ' application/json'. Klik op **koptekst toevoegen** opnieuw en typt u "Autorisatie" en "Bearer  *\<uw service API-sleutel\>* '. U vindt uw API-sleutel in het dashboard van de webservice AzureML (Zie **testen van de webservice** in bijlage A).

   Voor **aanvraagtekst**, type `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klik op **verzenden**.

   ![Verzenden](./media/manage-web-service-endpoints-using-api-management/send.png)

Nadat een bewerking is aangeroepen, de portal voor ontwikkelaars toont de **aangevraagde URL** van de back-end-service, de **antwoordstatus**, wordt de **antwoordheaders**, en eventuele  **Inhoud van de reactie**.

![antwoord-status](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bijlage A - maken en testen van een eenvoudige AzureML webservice
### <a name="creating-the-experiment"></a>Het maken van het experiment
Hieronder volgen de stappen voor het maken van een eenvoudig experiment van AzureML en deze is geïmplementeerd als een webservice. De service wordt als een kolom met willekeurige tekst invoer- en retourneert een set met functies die worden weergegeven als gehele getallen. Bijvoorbeeld:

| Tekst | Gecodeerde tekst |
| --- | --- |
| Dit is een goede dag |1 1 2 2 0 2 0 1 |

Met behulp van een browser naar keuze, navigeer naar: [ https://studio.azureml.net/ ](https://studio.azureml.net/) en voer uw referenties aan te melden. Maak vervolgens een nieuw, leeg experiment.

![sjablonen-experiment-zoeken](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Wijzig de naam **SimpleFeatureHashingExperiment**. Vouw **opgeslagen gegevenssets** en sleep **Book beoordelingen van Amazon** naar uw experiment.

![eenvoudige-functie-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Vouw **gegevenstransformatie** en **manipulatie** en sleep **Select Columns in Dataset** naar uw experiment. Verbinding maken met **boek beoordelingen van Amazon** naar **kolommen in gegevensset selecteren**.

![De module boek beoordelingen gegevensset verbinden met een module Project kolommen](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klik op **Select Columns in Dataset** en klik vervolgens op **kolomkiezer lancering** en selecteer **Col2**. Klik op het vinkje om deze wijzigingen te laten.

![Kolommen selecteren met kolom namen](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Vouw **Tekstanalyse** en sleep **hash-functies** naar het experimentcanvas. Verbinding maken met **kolommen in gegevensset selecteren** naar **hash-functies**.

![verbinding maken met project kolommen](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Type **3** voor de **bitsize Hashing**. Hiermee maakt u 8 (23) kolommen.

![hash-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Op dit moment kunt u klikken op **uitvoeren** voor het testen van het experiment.

![Uitvoeren](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Een webservice maken
Maak nu een webservice. Vouw **webservice** en sleep **invoer** naar uw experiment. Verbinding maken met **invoer** naar **hash-functies**. Ook slepen **uitvoer** naar uw experiment. Verbinding maken met **uitvoer** naar **hash-functies**.

![uitvoer-naar--hash-functies](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klik op **webservice publiceren**.

![publiceren-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klik op **Ja** voor het publiceren van het experiment.

![Ja om te publiceren](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>De webservice testen
Een web-service van AzureML bestaat uit RSS (aanvraag/antwoord-service) en BES (batchuitvoeringsservice)-eindpunten. RSS is voor synchrone uitvoering. BES is voor het uitvoeren van asynchrone taak. Als u uw webservice wilt testen met de onderstaande python-bron, moet u mogelijk de Azure SDK voor python downloaden en installeren (zie: [Python installeren](/azure/python/python-sdk-azure-install).

U moet ook de **werkruimte**, **service**, en **api_key** van uw experiment voor het onderstaande voorbeeld-bron. U kunt de werkruimte en de service vinden door te klikken op **aanvraag/antwoord** of **Batchuitvoering** voor uw experiment in het dashboard van de webservice.

![zoeken-werkruimte-en-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

U vindt de **api_key** door te klikken op uw experiment in het dashboard van de webservice.

![zoeken-api-sleutel](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Test RRS-eindpunt
##### <a name="test-button"></a>Knop Testen
Een eenvoudige manier voor het testen van de RRS-eindpunt is om op **testen** op het dashboard van de webservice.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Type **dit is een goede dag** voor **col2**. Klik op het vinkje.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

U ziet er ongeveer als

![Voorbeeld van uitvoer](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Voorbeeldcode
Een andere manier voor het testen van uw RRS is vanuit uw clientcode. Als u klikt op **aanvraag/antwoord** op het dashboard en Ga naar de onderkant, ziet u voorbeelden van code voor C#, Python en R. U ziet ook de syntaxis van de RRS-aanvraag, met inbegrip van de aanvraag-URI, headers en hoofdtekst.

Deze handleiding bevat een werkende Python-voorbeeld. U moet wijzigen met de **werkruimte**, **service**, en **api_key** van uw experiment.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Test BES-eindpunt
Klik op **Batchuitvoering** op het dashboard en Ga naar de onderkant. Ziet u voorbeelden van code voor C#, Python en R. U ziet ook de syntaxis van de BES-aanvragen voor het verzenden van een taak, een taak wordt gestart, de status of de resultaten van een taak ophalen en verwijderen van een taak.

Deze handleiding bevat een werkende Python-voorbeeld. U wilt wijzigen met de **werkruimte**, **service**, en **api_key** van uw experiment. Daarnaast moet u wijzigen de **opslagaccountnaam**, **opslagaccountsleutel**, en **naam van de opslagcontainer**. Ten slotte moet u wijzigen van de locatie van de **invoerbestand** en de locatie van de **uitvoerbestand**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
