---
title: Open tellingen bijhouden met Azure-toepassing inzichten | Microsoft Docs
description: Biedt instructies voor het integreren van opentellingen met de lokale doorstuur server en Application Insights
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: ddb8dfbf14f2c75e4d16693076e56a711d8861ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669978"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Gedistribueerde traceringen van Go verzamelen (preview-versie)

Application Insights ondersteunt nu gedistribueerde tracering van Go-toepassingen via integratie met [Opentellingen](https://opencensus.io) en onze nieuwe [lokale doorstuur server](./opencensus-local-forwarder.md). Dit artikel begeleidt u stapsgewijs door het proces voor het instellen van opentellingen voor Go en het ophalen van uw tracerings gegevens naar Application Insights.

## <a name="prerequisites"></a>Vereisten

- U hebt een Azure-abonnement nodig.
- Go moet worden geïnstalleerd. in dit artikel wordt gebruikgemaakt van de versie 1,11 [Go Down load](https://golang.org/dl/).
- Volg de instructies voor het installeren van de [lokale doorstuur server als Windows-service](./opencensus-local-forwarder.md).

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Application Insights resource maken

Eerst moet u een Application Insights resource maken waarmee een instrumentatie sleutel (Ikey) wordt gegenereerd. De iKey wordt vervolgens gebruikt om de lokale doorstuur server te configureren voor het verzenden van gedistribueerde traceringen van uw open-telling-toepassing naar Application Insights.   

1. Selecteer **een resource maken** > **Ontwikkelhulpprogramma's** > **Application Insights**.

   ![Application Insights-resource toevoegen](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights resource maakt, kunt u meer informatie vinden op het artikel [een Application Insights-resource maken](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

   | Instellingen        | Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik op **Create**.

## <a name="configure-local-forwarder"></a>Lokale doorstuur server configureren

1. Selecteer **Overzicht** > **Essentials** > kopieer de **Instrumentatiesleutel** van uw toepassing.

   ![Scherm opname van instrumentatie sleutel](./media/opencensus-Go/0003-instrumentation-key.png)

2. Bewerk uw `LocalForwarder.config`-bestand en voeg uw instrumentatie sleutel toe. Als u de instructies in de [vereisten](./opencensus-local-forwarder.md) van het bestand hebt gevolgd, bevindt het zich op `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Start de **lokale doorstuur server** -service van de toepassing opnieuw.

## <a name="opencensus-go-packages"></a>Open telling-pakketten

1. Installeer de open telling pakketten voor Go vanaf de opdracht regel:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Voeg de volgende code toe aan een. go-bestand en bouw en voer deze uit. (Dit voor beeld is afgeleid van de officiële opentellings richtlijn met toegevoegde code die de integratie met de lokale doorstuur Server vereenvoudigt)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Zodra de eenvoudige Go-app wordt uitgevoerd, gaat u naar `http://localhost:50030`. Bij elke vernieuwing van de browser wordt de tekst "Hallo wereld" gegenereerd, vergezeld van overeenkomende gegevens die door de lokale doorstuur server worden opgehaald.

4. Controleer het `LocalForwarder.config` bestand om te bevestigen dat de **lokale doorstuur server** de traceringen ophaalt. Als u de stappen in de [vereiste](https://docs.microsoft.com/azure/application-insights/local-forwarder)hebt gevolgd, bevindt deze zich in `C:\LF-WindowsServiceHost`.

    In de onderstaande afbeelding van het logboek bestand kunt u zien dat u voorafgaand aan het uitvoeren van het tweede script waarin we een export functie hebben toegevoegd `OpenCensus input BatchesReceived` 0 is. Zodra het bijgewerkte script is gestart `BatchesReceived` verhoogd naar het aantal ingevoerde waarden:
    
    ![Nieuw App Insights-resourceformulier](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. U kunt de pagina **overzicht** van Application Insights nu opnieuw openen in het Azure Portal om details weer te geven over de toepassing die momenteel wordt uitgevoerd. Selecteer **Live metrische stroom**.

   ![Scherm opname van het deel venster overzicht met een live metrieke stroom die is geselecteerd in het rode vak](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Als u de tweede go-app opnieuw uitvoert en de browser voor `http://localhost:50030`wilt vernieuwen, worden er Live Trace-gegevens weer gegeven terwijl deze binnenkomen in Application Insights van de lokale doorstuur server.

   ![Scherm afbeelding van een live metrieke stroom met prestatie gegevens die worden weer gegeven](./media/opencensus-go/0006-stream.png)

3. Ga terug naar de pagina **overzicht** en selecteer **toepassings overzicht** voor een visuele indeling van de afhankelijkheids relaties en roep timing tussen de onderdelen van uw toepassing aan.

    ![Scherm opname van basis toepassings toewijzing](./media/opencensus-go/0007-application-map.png)

    Omdat we slechts één methode aanroep traceren, is onze toepassings toewijzing niet zo interessant. Het is echter mogelijk dat toepassings overzicht kan worden geschaald om veel meer gedistribueerde toepassingen te visualiseren:

   ![Toepassingskaart](media/opencensus-go/application-map.png)

4. Selecteer **prestaties onderzoeken** om gedetailleerde prestatie analyses uit te voeren en de hoofd oorzaak van trage prestaties te bepalen.

    ![Scherm afbeelding van het deel venster prestaties](./media/opencensus-go/0008-performance.png)

5. Door voor **beelden** te selecteren en vervolgens te klikken op een van de voor beelden die in het rechterdeel venster worden weer gegeven, wordt de end-to-end-transactie Details geopend. Hoewel in onze voor beeld-app slechts één gebeurtenis wordt weer gegeven, zou een complexere toepassing u in staat stellen de end-to-end-trans actie te verkennen tot het niveau van de aanroep stack van een afzonderlijke gebeurtenis.

     ![Scherm opname van end-to-end trans actie-interface](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Open tellingen traceren voor Go

We hebben alleen de basis beginselen van het integreren van opentellingen voor Go met de lokale doorstuur server en Application Insights besproken. In de [officiële open-belasting gaat](https://godoc.org/go.opencensus.io) u meer over meer geavanceerde onderwerpen.

## <a name="next-steps"></a>Volgende stappen

* [Toepassings overzicht](./../../azure-monitor/app/app-map.md)
* [End-to-end prestatie bewaking](./../../azure-monitor/learn/tutorial-performance.md)
