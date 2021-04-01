# Public API
Samples and API descriptions

  - [Upload API details](https://github.com/cynosurarisk/PublicAPI#upload-api)
  - [Risk API details](https://github.com/cynosurarisk/PublicAPI#risk-api)



## Upload API ##

Here is how in C#, the upload would be posted. 


The **`content`** variable below is your JSON data


````csharp
CancellationTokenSource tokenSource = new CancellationTokenSource();
using (var httpClient = new HttpClient())
{
    httpClient.DefaultRequestHeaders.Add("wolfram-token", ".......YOUR TOKEN GOES HERE.......");
    try
    {
        using (var response = await httpClient.PostAsync("https://[CLIENT].wolframrisk.com/api/uploader/v0.1/portfoliodata/", content))
        {
            if (response.StatusCode != HttpStatusCode.OK)
            {
                string errorResponse = await response.Content.ReadAsStringAsync(tokenSource.Token);
            }
            else
            {
                string apiResponse = await response.Content.ReadAsStringAsync(tokenSource.Token);
            }
        }
    }
    catch (Exception ex)
    {
    }
}
````
This is C# snippet using our SDK object (future release), that would be serialized to `content` variable (above)

````csharp
List<StrategyEntity> es = new List<StrategyEntity>();
List<PLEntity> pls = new List<PLEntity>();
List<FundEntity> funds = new List<FundEntity>();
List<PutCallScheduleEntity> pces = new List<PutCallScheduleEntity>();
List<NAVEntity> navs = new List<NAVEntity>();
List<ClearerEntity> clrs = new List<ClearerEntity>();

List<SecurityEntity> secs = new List<SecurityEntity>();
List<SecurityHistoryEntity> sechs = new List<SecurityHistoryEntity>();

AllEntities aes = new AllEntities();
aes.Settings.Date = date;

TesterDB db = new TesterDB() //your own code to query your system/database
es = db.GetStrategies();
pls = db.GetPLs(aes.Settings.Date);
funds = db.GetFunds();
pces = db.GetPutCallSchedule();
navs = db.GetNAVs(aes.Settings.Date);
clrs = db.GetClearer();
secs = db.GetSecurities();
sechs = db.GetSecurityHistories(aes.Settings.Date);

aes.Strategies = new StrategyEntities();
aes.Strategies.InsertAction = InsertActionType.DELETEALL_INSERT;
aes.Strategies.InsertTransaction = InsertTransactionType.ATOMIC;
aes.Strategies.Rows = es;

aes.PLs = new PLEntities();
aes.PLs.InsertAction = InsertActionType.DELETEALL_INSERT;
aes.PLs.Rows = pls;

aes.Funds = new FundEntities();
aes.Funds.Rows = funds;

aes.PutCallSchedules = new PutCallScheduleEntities();
aes.PutCallSchedules.Rows = pces;

aes.NAVs = new NAVEntities();
aes.NAVs.Rows = navs;

aes.Clearers = new ClearerEntities();
aes.Clearers.Rows = clrs;

aes.Securities = new SecurityEntities();
aes.Securities.Rows = secs;

aes.SecurityHistories = new SecurityHistoryEntities();
aes.SecurityHistories.Rows = sechs;
````

## Risk API ##

Here is how in C#, the request would be posted. 


The **`content`** variable below is your JSON request


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
  "fund": "WolfFund",
  "strategy": "",
  "showZeroValue": false
}
````
This is equivalent in C# using our SDK object (future release), that would be serialized and generate the JSON code as above

````csharp
WSCRiskRequests view = new WSCRiskRequests();
view.Fund = "WolfFund";
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

//JSON settings
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

````


