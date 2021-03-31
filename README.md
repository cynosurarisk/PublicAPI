# PublicAPI
Samples and API descriptions

````csharp

WSCRiskRequests view = new WSCRiskRequests();
view.Fund = "Tungsten";
view.Groupings.Clear();
view.Groupings.Add("strat");

WSCVaRRequest r1 = new WSCVaRRequest();
r1.ShowPrevious = false;
r1.CustomIdentifier = "Confidence 0.95";
r1.UseModelMonteCarlo = true;
r1.UseModelHistorical = true;
r1.Confidence = 0.95;
r1.ShowIncremental = true;
r1.ShowNAV = true;
r1.ShowTailRisk = true;

view.RiskRequests.Add(r1);

CancellationTokenSource tokenSource = new CancellationTokenSource();
string batchID = "-1";

using (var httpClient = new HttpClient())
{
    httpClient.DefaultRequestHeaders.Add("wolfram-token", ".......YOUR OWN TOKEN.......");
    var indented = Formatting.Indented;
    var settings = new JsonSerializerSettings()
    {
        TypeNameHandling = TypeNameHandling.Auto,
        ContractResolver = new CamelCasePropertyNamesContractResolver()
    };
    string serObj = JsonConvert.SerializeObject(view, indented, settings);

    var bytes = Encoding.UTF8.GetBytes(serObj);
    int size = bytes.Length;

    StringContent content = new StringContent(serObj, Encoding.UTF8, "application/json");

    try
    {
        using (var response = await httpClient.PostAsync("https://[CLIENT].wolframrisk.com/api/risk/", content))
        {
            if (response.StatusCode != HttpStatusCode.OK)
            {
                string apiResponse = await response.Content.ReadAsStringAsync(tokenSource.Token);
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
