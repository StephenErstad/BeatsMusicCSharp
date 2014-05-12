Beats Music C# API
================

BeatsMusic C# API is a .net framwork that accesses the Beats Music Developer API (@[https://developer.beatsmusic.com/](https://developer.beatsmusic.com/)
). It provides a consistent C# API and data types for the Beats Music REST API. This API is a portable C# class library which is designed to work in multiple .Net environments, including: 
* .Net framework 4.5 (desktop & web) 
* Silverlight 5 (web plguin) 
* Windows 8 (tablet) 
* Windows Phone Silverlight 8 (mobile)

# Getting started
### Getting Started #1: Get the framework
To install BeatsMusicCSharpAPI, run the following command in Visual Studio's Package Manager Console.

<code style="-moz-border-radius:5px;-webkit-border-radius:5px;background-color:#202020;border:4px solid silver;border-radius:5px;box-shadow:2px 2px 3px #6e6e6e;color:#e2e2e2;display:block;font:1.5em 'andale mono','lucida console',monospace;line-height:1.5em;overflow:auto;padding:15px">PM&gt; Install-Package BeatsMusicCSharpAPI</code>
<br/>
<br/>

### Getting Started #2: Get Beats Music developer credentials
In order to use this API you'll need to register and get credentials from the Beats Music Developer website (@[https://developer.beatsmusic.com/member/register](https://developer.beatsmusic.com/member/register)). What you need to obtain is your application's ClientId and optionally a ClientSecret.
<br/><br/>
![Beats Music Developer Credentials](http://i.imgur.com/HvRscvX.png)
<br/>
<br/>


### Getting Started #3: Initialize a BeatsMusicClient
You will then use the ClientId and optionally the ClientSecret to initialize the BeatsMusicClient object which will serve as gateway to all the API calls. Depending on the security level you wish to give your application you can choose to embed your apps' ClientSecret and gain write access to the Beats Music developer APIs.

<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:green;">//&nbsp;Use&nbsp;this&nbsp;c&#39;tor&nbsp;for&nbsp;&quot;Web&nbsp;Server&nbsp;applications&quot;&nbsp;type&nbsp;authentication.&nbsp;</span>
<span style="color:green;">//&nbsp;&quot;Web&nbsp;Server&nbsp;applications&quot;&nbsp;are&nbsp;mostly&nbsp;read-write&nbsp;applications.&nbsp;</span>
<span style="color:blue;">var</span>&nbsp;client&nbsp;=&nbsp;<span style="color:blue;">new</span>&nbsp;<span style="color:#2b91af;">BeatsMusicClient</span>(ClientId,&nbsp;RedirectUrl,&nbsp;ClientSecret);
 
<span style="color:green;">//&nbsp;Or&nbsp;use&nbsp;htis&nbsp;c&#39;tor&nbsp;for&nbsp;&quot;Client&nbsp;Side&nbsp;applications&quot;&nbsp;type&nbsp;authentication.</span>
<span style="color:green;">//&nbsp;&quot;Client&nbsp;Side&nbsp;applications&quot;&nbsp;are&nbsp;mostly&nbsp;apps&nbsp;read-only&nbsp;applications.&nbsp;</span>
<span style="color:blue;">var</span>&nbsp;client&nbsp;=&nbsp;<span style="color:blue;">new</span>&nbsp;<span style="color:#2b91af;">BeatsMusicClient</span>(ClientId,&nbsp;RedirectUrl);&nbsp;</pre>

### Getting Started #4: Do stuff
That's it! You're ready to use API calls which do not require user- specific permissions.

**Sample: Searching by artist**
<table border=0><tr><td width="140px"><img src="http://i.imgur.com/vIzFjsA.png?1" /></td><td>
<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:blue;">var</span>&nbsp;artists&nbsp;=&nbsp;<span style="color:blue;">await</span>&nbsp;client.Search.SearchByArtist(<span style="color:#a31515;">&quot;Connie&quot;</span>);
<span style="color:blue;">foreach</span>&nbsp;(<span style="color:blue;">var</span>&nbsp;searchData&nbsp;<span style="color:blue;">in</span>&nbsp;artists.Data)
{
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#2b91af;">Console</span>.WriteLine(searchData.Display);
}</pre></td></tr></table>


**Sample: Fetching an album by ID** 
<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:blue;">var</span>&nbsp;album&nbsp;=&nbsp;<span style="color:blue;">await</span>&nbsp;client.Albums.GetAlbumById(<span style="color:#a31515;">&quot;al801997&quot;</span>);
<span style="color:blue;">if</span>&nbsp;(!album.HasErrors)
{
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green;">//&nbsp;prints&nbsp;out&nbsp;&quot;Stars&nbsp;-&nbsp;The&nbsp;Best&nbsp;Of&nbsp;The&nbsp;Cranberries&nbsp;1992-2002&quot;</span>
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#2b91af;">Console</span>.WriteLine(album.Data.Title);
}</pre>

**Sample: Get Playlists from the first Genre**
<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:blue;">var</span>&nbsp;playlistsInFirstGenre&nbsp;=&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:blue;">await</span>&nbsp;client.Genre.GetAllPlaylistsInGenre(
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(<span style="color:blue;">await</span>&nbsp;client.Genre.GetAllGenres()).Data.First().Id);
 
<span style="color:blue;">foreach</span>&nbsp;(<span style="color:blue;">var</span>&nbsp;playlist&nbsp;<span style="color:blue;">in</span>&nbsp;playlistsInFirstGenre.Data)
{
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#2b91af;">Console</span>.WriteLine(playlist.Name);
}</pre>

# Advanced actions and authentication
### Getting the authentication URI
If you need to perform actions which would require user specific permissions you'll need to oauth your user to Beats Music. first, have your app navigate to Beats Music's OAuth webpage. Them, you'll need to obtain the oauth login URL from the BeatsMusicClient. And finally have a web browser navigate to this address. 
The URI address you recieve from the Beats Music client depends on which c'tor was used to intialize your client.
<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:green;">//&nbsp;navigate&nbsp;a&nbsp;WebBrowser&nbsp;to&nbsp;this&nbsp;Uri&nbsp;to&nbsp;OAuth&nbsp;login&nbsp;users</span>
<span style="color:blue;">string</span>&nbsp;addressToNavigate&nbsp;=&nbsp;client.UriAddressToNavigateForPermissions();</pre>

### Getting the authentication details from the redirected URI and setting them up 
After the user inputs their credentials, the WebBrowser will be the redirected to your client's RedirectUri. The URI's query string parameters contain the relevant authorization information. Your application will need to provide that authorization information when making API calls. You will get the query string values and pass those to the BeatsMusicClient instance.
<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:green;">//&nbsp;Used&nbsp;for&nbsp;&quot;Web&nbsp;Server&nbsp;applications&quot;&nbsp;type&nbsp;authentication.</span>
client.ServerCode&nbsp;=&nbsp;queryStringParams
&nbsp;&nbsp;&nbsp;&nbsp;.GetValues(<span style="color:#a31515;">&quot;code&quot;</span>)
&nbsp;&nbsp;&nbsp;&nbsp;.FirstOrDefault();&nbsp;
 
<span style="color:green;">//&nbsp;-or-&nbsp;Used&nbsp;for&nbsp;&quot;Client&nbsp;Side&nbsp;applications&quot;&nbsp;type&nbsp;authentication</span>
client.SetClientAccessTokenFromRedirectUri(
&nbsp;&nbsp;&nbsp;&nbsp;queryStringParams.GetValues(<span style="color:#a31515;">&quot;access_token&quot;</span>).FirstOrDefault(),
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:blue;">int</span>.Parse(queryStringParams.GetValues(<span style="color:#a31515;">&quot;expires_in&quot;</span>).FirstOrDefault()));</pre>

### Calling methods requiring OAuth
After OAuthing your user you can invoke additional APIs (depends on the c'tor you've used for the BeatsMusicClient).
<pre style="font-family:Consolas;font-size:15;color:black;background:white;"><span style="color:green;">//&nbsp;Getting&nbsp;Streaming&nbsp;Info&nbsp;work&nbsp;with&nbsp;either&nbsp;BeatsMusicClient&nbsp;c&#39;tor</span>
<span style="color:blue;">var</span>&nbsp;result&nbsp;=&nbsp;<span style="color:blue;">await</span>&nbsp;client.Audio.GetAudioStreamingInfo(
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a31515;">&quot;tr61032803&quot;</span>,&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#2b91af;">Bitrate</span>.Highest,
&nbsp;&nbsp;&nbsp;&nbsp;aquire&nbsp;:&nbsp;<span style="color:blue;">true</span>);
 
<span style="color:green;">//&nbsp;Creating&nbsp;a&nbsp;playlisy&nbsp;only&nbsp;works&nbsp;with&nbsp;Web&nbsp;Server&nbsp;applications.</span>
<span style="color:blue;">var</span>&nbsp;result&nbsp;=&nbsp;<span style="color:blue;">await</span>&nbsp;client.Playlists.CreatePlaylist(
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a31515;">&quot;test&nbsp;playlist&nbsp;name&quot;</span>,&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:#a31515;">&quot;only&nbsp;works&nbsp;with&nbsp;server&nbsp;side&nbsp;permissions&quot;</span>);</pre>

Obligatory Long Code Sample
-------

```csharp
// Initialize a BeatsMusicClient object which will serve as the endpoint for accessing Beats Music API.
BeatsMusicClient client = new BeatsMusicClient(ClientId, RedirectUrl, ClientSecret);

//You can immediately call methods which do not require user permissions.
MultipleRootObject<SearchData> result2 = await client.Search.SearchByArtist("Connie");

// If you need to use methods that require user permissions-
// Get the address the web browser needs to navigate to for OAuth 2.0 protocol authentication. 
var addressString = client.UriAddressToNavigateForPermissions();

// Navigate to the BeatsMusic OAuth page. This code is browser/ platform- specific.
BeatsMusicWebBrowser.Source = new Uri(addressString);

// After the user logs in Beats Music using AOuth the redirected URL contains the authorization code you need to 
// provide the Beats Music API in order to make calls against the API.
client.Code = queryStringParams.GetValues("code").FirstOrDefault();

// That's it, now you can use the API endpoints to make calls against the server.
SingleRootObject<AudioData> result = await client.Audio.GetAudioStreamingInfo("tr61032803", Bitrate.Highest, true);
```


# Authentication levels
There are two authentication levels you can use in your application: 
* Client Side Application (@[https://developer.beatsmusic.com/docs/read/getting_started/Client_Side_Applications](https://developer.beatsmusic.com/docs/read/getting_started/Client_Side_Applications)). This authentication requires your application to provide only the ClientId when initializing the client. It provides a short- term more limited access that is not renewable.
* Web Server Application (@[https://developer.beatsmusic.com/docs/read/getting_started/Web_Server_Applications](https://developer.beatsmusic.com/docs/read/getting_started/Web_Server_Applications)). This authentication requires your application to provide ClientId and SecretId when initializing the client. It provides a long- term full access and renews automatically after timing out as long as the application is running.



