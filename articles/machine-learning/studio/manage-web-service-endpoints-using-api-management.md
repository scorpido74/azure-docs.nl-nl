---
title: Webservices beheren met API-beheer
titleSuffix: ML Studio (classic) - Azure
description: Een handleiding met informatie over het beheren van AzureML-webservices met API-beheer. Beheer uw REST API-eindpunten door gebruikerstoegang, beperking van het gebruik en dashboardbewaking te definiëren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: cbe01ee9b8edeab349db484cea6c25dca32bf213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218013"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Azure Machine Learning Studio (klassieke) webservices beheren met API-beheer

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Overzicht
In deze handleiding ziet u hoe u snel aan de slag met API-beheer om uw Azure Machine Learning Studio (klassieke) webservices te beheren.

## <a name="what-is-azure-api-management"></a>Wat is Azure API Management?
Azure API Management is een Azure-service waarmee u uw REST API-eindpunten beheren door gebruikerstoegang, beperking van het gebruik en dashboardbewaking te definiëren. Zie de [Azure API-beheersite](https://azure.microsoft.com/services/api-management/) voor meer informatie. Zie [de handleiding importeren en publiceren](/azure/api-management/import-and-publish)om aan de slag te gaan met Azure API Management. Deze andere handleiding, waarop deze handleiding is gebaseerd, behandelt meer onderwerpen, waaronder meldingsconfiguraties, laagprijzen, responsafhandeling, gebruikersverificatie, het maken van producten, ontwikkelaarsabonnementen en dashboarding voor gebruik.

## <a name="prerequisites"></a>Vereisten
Om deze gids te voltooien, moet u het:

* Een Azure-account.
* Een AzureML-account.
* De werkruimte, service en api_key voor een AzureML-experiment dat als webservice is geïmplementeerd. Zie de [Studio snel aan](create-experiment.md)de slag voor meer informatie over het maken van een AzureML-experiment. Zie de [handleiding voor studio-implementatie](deploy-a-machine-learning-web-service.md) voor meer informatie over het implementeren van een AzureML-experiment als webservice voor informatie over het implementeren van een AzureML-experiment als webservice. Aanhangsel A bevat afwisselend instructies voor het maken en testen van een eenvoudig AzureML-experiment en het implementeren als een webservice.

## <a name="create-an-api-management-instance"></a>Een API Management-exemplaar maken

U uw Azure Machine Learning-webservice beheren met een API-beheerexemplaar.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **+ Een resource maken**.
3. Typ in het zoekvak 'API-beheer' en selecteer vervolgens de bron 'API-beheer'.
4. Klik **op Maken**.
5. De **waarde Naam** wordt gebruikt om een unieke URL te maken (in dit voorbeeld wordt 'demoazureml' gebruikt).
6. Selecteer een **abonnementsgroep**, **resourcegroep**en **locatie** voor uw service-instantie.
7. Geef een waarde op voor **organisatienaam** (in dit voorbeeld wordt 'demoazureml' gebruikt).
8. Voer uw **e-mail van de beheerder** in : deze e-mail wordt gebruikt voor meldingen van het API-beheersysteem.
9. Klik **op Maken**.

Het kan tot 30 minuten duren voordat een nieuwe service wordt gemaakt.

![create-service](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>De API maken
Zodra de service-instantie is gemaakt, is de volgende stap het maken van de API. Een API bestaat uit een reeks bewerkingen die vanuit een clienttoepassing kunnen worden aangeroepen. API-bewerkingen worden geproxied naar bestaande webservices. Met deze handleiding worden API's die proxy maken voor de bestaande AzureML RRS- en BES-webservices.

Ga als een te werk om de API te maken:

1. Open in de Azure-portal het service-exemplaar dat u hebt gemaakt.
2. Selecteer **API's**in het linkernavigatiedeelvenster .

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klik **op API toevoegen**.
2. Voer een **web-API-naam** in (in dit voorbeeld wordt 'AzureML Demo API' gebruikt).
3. Voer voor de URL`https://ussouthcentral.services.azureml.net`van **webservice**" in.
4. Voer een **URL-achtervoegsel van web-API in". Dit wordt het laatste deel van de URL die klanten zullen gebruiken voor het verzenden van aanvragen naar de service-instantie (dit voorbeeld maakt gebruik van 'azureml-demo').
5. Selecteer **HTTPS**voor **het URL-schema van web-API**.
6. Selecteer **Starter**voor **producten.**
7. Klik op **Opslaan**.


## <a name="add-the-operations"></a>De bewerkingen toevoegen

Bewerkingen worden toegevoegd en geconfigureerd aan een API in de uitgeversportal. Als u de uitgeversportal wilt openen, klikt u op **Publisher-portal** in de Azure-portal voor uw API-beheerservice, selecteert u **API's**, **Bewerkingen**en klikt u op **Bewerking toevoegen**.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Het **bewerkingsvenster Nieuw** wordt weergegeven en het tabblad **Handtekening** wordt standaard geselecteerd.

## <a name="add-rrs-operation"></a>RRS-bewerking toevoegen
Maak eerst een bewerking voor de AzureML RRS-service:

1. Selecteer POST voor het **HTTP-werkwoord** **.**
2. Typ voor de **URL-sjabloon**"`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Voer een **weergavenaam in** (in dit voorbeeld wordt 'RRS Execute' gebruikt).

   ![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klik op**Toevoegen** **aan** > reacties aan de linkerkant en selecteer **200 OK**.
5. Klik **op Opslaan** om deze bewerking op te slaan.

   ![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES-bewerkingen toevoegen

> [!NOTE]
> Screenshots zijn hier niet opgenomen voor de BES-bewerkingen, omdat ze zeer vergelijkbaar zijn met die voor het toevoegen van de RRS-bewerking.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Een batchuitvoeringstaak verzenden (maar niet starten)

1. Klik **op Bewerking toevoegen** om een BES-bewerking aan de API toe te voegen.
2. Selecteer POST voor het **HTTP-werkwoord** **.**
3. Typ voor de **URL-sjabloon**"`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Voer een **weergavenaam in** (in dit voorbeeld wordt BES Submit gebruikt).
5. Klik op**Toevoegen** **aan** > reacties aan de linkerkant en selecteer **200 OK**.
6. Klik op **Opslaan**.

### <a name="start-a-batch-execution-job"></a>Een batchuitvoeringstaak starten

1. Klik **op Bewerking toevoegen** om een BES-bewerking aan de API toe te voegen.
2. Selecteer POST voor het **HTTP-werkwoord** **.**
3. Voor het **HTTP-werkwoord**" typ "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Voer een **weergavenaam in** (in dit voorbeeld wordt BES Start gebruikt).
6. Klik op**Toevoegen** **aan** > reacties aan de linkerkant en selecteer **200 OK**.
7. Klik op **Opslaan**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>De status of het resultaat van een batchuitvoeringstaak ophalen

1. Klik **op Bewerking toevoegen** om een BES-bewerking aan de API toe te voegen.
2. Selecteer **GET**voor het **HTTP-werkwoord**.
3. Typ voor de **URL-sjabloon**"`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Voer een **weergavenaam in** (in dit voorbeeld wordt bes-status gebruikt).
6. Klik op**Toevoegen** **aan** > reacties aan de linkerkant en selecteer **200 OK**.
7. Klik op **Opslaan**.

### <a name="delete-a-batch-execution-job"></a>Een taak Batchexecution verwijderen

1. Klik **op Bewerking toevoegen** om een BES-bewerking aan de API toe te voegen.
2. Selecteer **Verwijderen**voor het **HTTP-werkwoord**.
3. Typ voor de **URL-sjabloon**"`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Voer een **weergavenaam in** (in dit voorbeeld wordt 'BES Delete' gebruikt).
5. Klik op**Toevoegen** **aan** > reacties aan de linkerkant en selecteer **200 OK**.
6. Klik op **Opslaan**.

## <a name="call-an-operation-from-the-developer-portal"></a>Een bewerking aanroepen vanaf de ontwikkelaarsportal

Bewerkingen kunnen rechtstreeks worden aangeroepen vanuit de developer portal, die een handige manier biedt om de bewerkingen van een API te bekijken en te testen. In deze stap roept u de **RRS Execute-methode aan** die is toegevoegd aan de **AzureML Demo API.** 

1. Klik **op Ontwikkelaarsportal**.

   ![developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klik in het bovenste menu op **API's** en klik vervolgens op **AzureML Demo API** om de beschikbare bewerkingen te bekijken.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selecteer **RRS Uitvoeren** voor de bewerking. Klik **op Proberen**.

   ![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Typ **voor Parameters aanvragen**uw **werkruimte** en **service**, typ "2.0 voor de **bijenversie**en "true" voor de **details**. U uw **werkruimte** en **service** vinden in het AzureML-webservicedashboard (zie **De webservice testen** in aanhangsel A).

   Klik **voor Kopteksten aanvragen**op **Koptekst toevoegen** en typ 'Inhoudstype' en 'toepassing/json'. Klik nogmaals **op Koptekst toevoegen** en typ 'Autorisatie' en 'Toon * \<uw service\>API-KEY'.* U uw API-KEY vinden in het AzureML-webservicedashboard (zie **De webservice testen** in aanhangsel A).

   Voor de instantie `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`Van **aanvraag**, typ .

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klik op **Verzenden**.

   ![verzenden](./media/manage-web-service-endpoints-using-api-management/send.png)

Nadat een bewerking is aangeroepen, geeft de ontwikkelaarsportal de **opgevraagde URL** van de back-endservice, de **status Antwoord,** de **antwoordkoppen**en eventuele **inhoud van het antwoord**weer .

![reactiestatus](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bijlage A - Een eenvoudige AzureML-webservice maken en testen
### <a name="creating-the-experiment"></a>Het experiment maken
Hieronder staan de stappen voor het maken van een eenvoudig AzureML-experiment en het implementeren ervan als webservice. De webservice neemt als invoer een kolom met willekeurige tekst en retourneert een set functies die worden weergegeven als gehele getallen. Bijvoorbeeld:

| Tekst | Gehashte tekst |
| --- | --- |
| Dit is een goede dag |1 1 2 2 0 2 0 1 |

Ga eerst met een browser naar [https://studio.azureml.net/](https://studio.azureml.net/) keuze naar: en voer uw referenties in om in te loggen. Maak vervolgens een nieuw leeg experiment.

![zoek-experiment-sjablonen](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Hernoem het naar **SimpleFeatureHashingExperiment**. Vouw **opgeslagen gegevenssets** uit en sleep **boekrecensies van Amazon** naar uw experiment.

![eenvoudige-feature-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Vouw **gegevenstransformatie** en **-manipulatie** uit en sleep **Kolommen selecteren in gegevensset** naar uw experiment. Boekrecensies **van Amazon** verbinden met Kolommen selecteren **in gegevensset**.

![De gegevenssetmodule voor boekrecensies koppelen aan een projectkolommenmodule](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klik **op Kolommen selecteren in gegevensset** en klik vervolgens op **Kolomkiezer starten** en selecteer **Col2**. Klik op het vinkje om deze wijzigingen toe te passen.

![Kolommen selecteren met kolomnamen](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Vouw **Tekstanalyse** uit en sleep **Functie Hashing** naar het experiment. **Sluit Kolommen selecteren in gegevensset** aan op Functie **Hashing**.

![connect-project-kolommen](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Type **3** voor de **Hashing bitsize**. Hierdoor worden 8 (23) kolommen gemaakt.

![hashing-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Op dit punt u op **Uitvoeren** klikken om het experiment te testen.

![Uitvoeren](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Een webservice maken
Maak nu een webservice. Vouw **Webservice** uit en sleep **Invoer** naar uw experiment. **Invoer verbinden** met **Feature Hashing**. Sleep ook **de uitvoer** naar uw experiment. **Uitvoer verbinden met** Feature **Hashing**.

![output-to-feature-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klik **op Webservice publiceren**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klik **op Ja** om het experiment te publiceren.

![ja-om te publiceren](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>De webservice testen
Een AzureML-webservice bestaat uit RSS-eindpunten (request/response service) en BES (batch execution service). RSS is voor synchrone uitvoering. BES is voor asynchrone taakuitvoering. Als u uw webservice wilt testen met de onderstaande voorbeeldpython-bron, moet u mogelijk de Azure SDK voor Python downloaden en installeren (zie: [Python installeren).](/azure/python/python-sdk-azure-install)

U hebt ook de **werkruimte,** **service**en **api_key** van uw experiment nodig voor de onderstaande voorbeeldbron. U de werkruimte en service vinden door op **Aanvraag/antwoord** of **Batch-uitvoering** te klikken voor uw experiment in het dashboard van de webservice.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

U de **api_key** vinden door op uw experiment te klikken in het dashboard van de webservice.

![find-api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>RRS-eindpunt testen
##### <a name="test-button"></a>Knop Testen
Een eenvoudige manier om het RRS-eindpunt te testen, is door op **Testen** op het dashboard van de webservice te klikken.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Type **Dit is een goede dag** voor **col2**. Klik op het vinkje.

![enter-data](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Je ziet iets als

![monsteruitvoer](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Voorbeeldcode
Een andere manier om uw RRS te testen is van uw clientcode. Als u op **Verzoek/antwoord** op het dashboard klikt en naar de onderkant schuift, ziet u voorbeeldcode voor C#, Python en R. U ziet ook de syntaxis van de RRS-aanvraag, inclusief de aanvraag URI, kopteksten en hoofdtekst.

Deze handleiding toont een werkend Python-voorbeeld. U moet het wijzigen met de **werkruimte,** **service**en **api_key** van uw experiment.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
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

#### <a name="test-bes-endpoint"></a>BES-eindpunt testen
Klik op **Batch-uitvoering** op het dashboard en blader naar de onderkant. U ziet voorbeeldcode voor C#, Python en R. U ziet ook de syntaxis van de BES-aanvragen om een taak in te dienen, een taak te starten, de status of resultaten van een taak op te halen en een taak te verwijderen.

Deze handleiding toont een werkend Python-voorbeeld. U moet het wijzigen met de **werkruimte,** **service**en **api_key** van uw experiment. Bovendien moet u de naam van het **opslagaccount,** **de opslagaccountsleutel**en **de naam van de opslagcontainer**wijzigen. Ten slotte moet u de locatie van het **invoerbestand** en de locatie van het **uitvoerbestand**wijzigen.

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
    with open(output_file, "wb+") as f:
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
