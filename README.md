# PublicAPI
Samples and API descriptions

Here is how in C#, the request would be posted. 

> **content** is your JSON reuqest

````csharp
CancellationTokenSource tokenSource = new CancellationTokenSource();
string batchID = "-1";

using (var httpClient = new HttpClient())
{
    httpClient.DefaultRequestHeaders.Add("wolfram-token", ".......YOUR OWN TOKEN.......");
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
