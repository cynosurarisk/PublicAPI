# PublicAPI
Samples and API descriptions

Here is how in C#, the request would be posted. 

> **content** variable below is your JSON request

````csharp
CancellationTokenSource tokenSource = new CancellationTokenSource();
string batchID = "-1";

using (var httpClient = new HttpClient())
{
    httpClient.DefaultRequestHeaders.Add("wolfram-token", ".......YOUR TOKEN GOES HERE.......");
    try
    {
        using (var response = await httpClient.PostAsync("https://[CLIENT].wolframrisk.com/api/risk/", content))
        {
            if (response.StatusCode != HttpStatusCode.OK)
            {
                string errorResponse = await response.Content.ReadAsStringAsync(tokenSource.Token);
            }
            else
            {
                batchID = await response.Content.ReadAsStringAsync(tokenSource.Token);
            }
        }
    }
    catch (Exception ex)
    {
    }
}
````
JSON content would look like this:

````json
{
  "date": null,
  "riskRequests": [
    {
      "$type": "Wolfram.WSCVaRRequest, wolfram.lib",
      "confidence": 0.99,
      "horizon": 1.0,
      "useModelMonteCarlo": true,
      "useModelHistorical": true,
      "useModelHybrid": false,
      "useModelParametric": false,
      "showIncremental": false,
      "showComponent": false,
      "showComponentRatio": false,
      "showTailRisk": false,
      "customIdentifier": "Confidence 0.99",
      "showCurrency": true,
      "showRatio": false,
      "showNAV": false,
      "showPrevious": false,
      "showChange": false
    }
  ],
  "groupings": [
    "strat"
  ],
  "parentFund": "",
  "fund": "Tungsten",
  "strategy": "",
  "showZeroValue": false
}
````
