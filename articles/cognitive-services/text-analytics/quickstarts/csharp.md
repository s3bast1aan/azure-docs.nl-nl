---
title: C# Quickstart voor Azure Cognitive Services Text Analytics-API | Microsoft Docs
description: Get-informatie en codevoorbeelden om u te helpen snel aan de slag met behulp van de Tekstanalyse-API in Microsoft Cognitive Services op Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: 59e2254054f51a8d5f30e1b38dc5e6c23899c054
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284318"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Snelstartgids voor Tekstanalyse-API met C# 
<a name="HOLTop"></a>

Dit artikel leest u hoe u taal detecteren, sentiment analyseren en belangrijke woordgroepen met behulp van de [Tekstanalyse-API's](//go.microsoft.com/fwlink/?LinkID=759711) met C#. De code is geschreven om te werken op een .net Core-toepassing, met minimale verwijzingen naar externe bibliotheken, dus u het ook in Linux of MacOS uitvoeren kunt.

Raadpleeg de [API-definities](//go.microsoft.com/fwlink/?LinkID=759346) voor technische documentatie voor de API's.

## <a name="prerequisites"></a>Vereisten

Hebt u een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met **Tekstanalyse-API**. U kunt de **gratis laag voor 5000 transacties per maand** om uit te voeren van deze Quick Start.

Ook moet u de [eindpunt en de toegangssleutel](../How-tos/text-analytics-how-to-access-key.md) die is gegenereerd voor u tijdens het aanmelden van. 


## <a name="install-the-nuget-sdk-package"></a>Installeer de SDK Nuget-pakket
1. Maak een nieuwe Console-oplossing in Visual Studio.
1. Klik met de rechtermuisknop op de oplossing en klik op **NuGet-pakketten beheren voor oplossing**
1. Markeren de **voorlopige versie opnemen** selectievakje.
1. Selecteer de **Bladeren** tabblad en zoek naar de **Microsoft.Azure.CognitiveServices.Language**
1. Selecteer het Nuget-pakket en installeer deze.

> [!Tip]
>  Terwijl u de [HTTP-eindpunten](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) rechtstreeks via C#, de SDK Microsoft.Azure.CognitiveServices.Language veel gemakkelijker om aan te roepen van de service zonder te hoeven maken over het serialiseren en deserialiseren van JSON.
>
> Een paar nuttige koppelingen:
> - [SDK Nuget-pagina](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-code ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Aanroep van de Tekstanalyse-API met behulp van de SDK
1. Vervang Program.cs door de code hieronder. Dit programma ziet u de mogelijkheden van de Tekstanalyse-API in 3 secties (taal uitpakken, sleutel vindt er sleuteltermextractie plaats en sentimentanalyse).
1. Vervang de `Ocp-Apim-Subscription-Key` headerwaarde met een geldige toegangssleutel voor uw abonnement.
1. Vervang de locatie in `Endpoint` naar het eindpunt dat u zich heeft aangemeld. U vindt het eindpunt voor de resource van Azure Portal. Het eindpunt doorgaans begint met "https://[region].api.cognitive.microsoft.com" en zich hier alleen Neem protocol en de hostnaam.
1. Voer het programma.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Text Analytics met Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zie ook 

 [Text Analytics-overzicht](../overview.md)  
 [Veelgestelde vragen (FAQ)](../text-analytics-resource-faq.md)

