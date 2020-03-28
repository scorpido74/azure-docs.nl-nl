---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446416"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Formulieren voor sleutelwaardeparen en tabellen analyseren

Vervolgens gebruikt u uw nieuw getrainde model om een document te analyseren en er sleutelwaardeparen en tabellen uit te halen. Roep de **[API voor formulier analyseren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** aan door de volgende code uit te voeren in een nieuw Python-script. Breng de volgende wijzigingen aan voordat u het script uitvoert:

1. Vervang `<file path>` het bestandspad van uw formulier (bijvoorbeeld C:\temp\file.pdf). Dit kan ook de URL van een extern bestand zijn. Voor deze snelle start u de bestanden gebruiken onder de map **Testen** van de [voorbeeldgegevensset](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Vervang `<model_id>` de model-id die u in de vorige sectie hebt ontvangen.
1. Vervang `<endpoint>` het eindpunt dat u hebt verkregen door de abonnementssleutel Voor formulierherkenning. U het vinden op het tabblad Overzicht van **formulierherkenningsbronnen.**
1. Vervang `<file type>` door het bestandstype. Ondersteunde typen: `application/pdf` `image/jpeg`, `image/png` `image/tiff`, , .
1. Vervang `<subscription key>` door uw abonnementssleutel.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Sla de code op in een bestand met een .py-extensie. Bijvoorbeeld *form-recognizer-analyze.py*.
1. Open een opdrachtpromptvenster.
1. Typ bij de prompt de opdracht `python` om het voorbeeld uit te voeren. Bijvoorbeeld `python form-recognizer-analyze.py`.

Wanneer u de **API voor het analyseformulier** aanroept, ontvangt u een `201 (Success)` antwoord met een koptekst **Operatie-locatie.** De waarde van deze koptekst is een id die u gebruikt om de resultaten van de bewerking Analyseren bij te houden. Het script hierboven drukt de waarde van deze koptekst af op de console.

## <a name="get-the-analyze-results"></a>De resultaten analyseren

Voeg de volgende code toe aan de onderkant van uw Python-script. Hiermee wordt de id-waarde van de vorige aanroep in een nieuwe API-aanroep gebruikt om de analyseresultaten op te halen. De bewerking **Formulier analyseren** is asynchroon, dus dit script roept de API regelmatig aan totdat de resultaten beschikbaar zijn. We raden een interval van een seconde of meer aan.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
