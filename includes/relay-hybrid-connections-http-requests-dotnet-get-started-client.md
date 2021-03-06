---
title: bestand opnemen
description: bestand opnemen
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 4a3f38e1423db0755d8c76f8850e41173d250f43
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33904979"
---
### <a name="create-a-console-application"></a>Een consoletoepassing maken

Als u de optie 'Vereist clientautorisatie' hebt uitgeschakeld tijdens het maken van de relay, kunt u met elke browser aanvragen verzenden naar de URL van de hybride verbindingen. Voor toegang tot beveiligde eindpunten moet u een token maken en doorgeven in de header `ServiceBusAuthorization`, die hier wordt weergegeven.

Maak in Visual Studio een nieuw **Console-app (.NET Framework)**-project.

### <a name="add-the-relay-nuget-package"></a>Het pakket Relay NuGet toevoegen

1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.
2. Selecteer **Bladeren** en zoek naar **Microsoft.Azure.Relay**. Selecteer **Microsoft Azure Relay** in de lijst met zoekresultaten. 
3. Selecteer **Installeren** om de installatie uit te voeren. Sluit het dialoogvenster.

### <a name="write-code-to-send-requests"></a>Code schrijven om aanvragen te verzenden

1. Vervang de bestaande `using`-instructies bovenaan het bestand Program.cs door de volgende `using`-instructies:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Voeg constanten toe aan de klasse `Program` voor de gegevens van de hybride verbinding. Vervang de tijdelijke aanduidingen tussen punthaken door de waarden die u hebt verkregen bij het maken van de hybride verbinding. Zorg ervoor dat u de volledig gekwalificeerde naamruimte gebruikt.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Voeg de volgende methode toe aan de klasse `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
    ```
4. Voeg de volgende coderegel toe aan de methode `Main` in de klasse `Program`.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Het bestand Program.cs moet er dan zo uitzien:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

