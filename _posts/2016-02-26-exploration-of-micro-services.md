---
layout: post
tags: [ASP Net, HTTP Services, Microservices, Web API]
---
Once again I am making changes to my EV Tracker project. This time I am looking at splitting up all the logic and storage so I can look after each part separately. To do this I decided to create some Web REST APIs using ASP Net Core and create a simple web page as my GUI. This webpage was using electron to host it, but there was no reason for this other than to try and keep it as a Desktop app.

Before I go much further, a quick disclaimer. This transformation was not complete, just done to a point where I could see how everything would be implemented and could add new features with ease. Also I did nothing about port detection, relying solely on hard coded web calls. But that is something I may tackle in future.

#### Choices of Services

My choice of services was done by, "Oh, I need to get this set information to display. Guess I'll create a service to be able to ask that question". This ended up roughly being one service per model. This may be overkill in certain situations, it would be best to have one service for each model the UI needs to ask about, and one for each model that multiple services need to store/get info about.

#### What ended up in the UI

In the end my UI was really dumb, I used knockout.js to handle drawing the UI based on my current state. Clicking any button in the UI would send a request to the main Pokémon service and would receive back a new json model of the tracked Pokémon. This would then just overwrite the current data and knockout would redraw everything as necessary. If you want to review the UI code you can look [here](https://github.com/PeterGerrard/EVTracker/tree/HttpServices/Pokemon.EVTracker.Html).

#### What about the services?

Most of the services were dumb and just stored a json file describing their particular models and served that when asked. The only one of note was the Pokémon service as it required talking to multiple services and manipulating the stored data. Fortunately for me ASP Net Core makes it really easy to send and receive C# objects via REST Apis, particularly with the HttpContent Extension `ReadAsAsync<T>()` method. To get the actual species data for one of my Pokémon in an easy manner, all I had to do is a simple call and read.

```csharp
using (var httpClient = new HttpClient())
{
    var speciesResponse = await httpClient.GetAsync($"http://localhost:20640/api/v0/species/{_trackedPokemon.DexNumber}");
    return await speciesResponse.Content.ReadAsAsync<PokemonType>();
}
```

This is the closest to complex code that I had to add due to splitting stuff out.

#### Final Thoughts

In a word **boilerplate**, perhaps this is because this is still a toy example. But the amount of boiler plate code I had easily dwarfs the code that actually does stuff.

As this was a simple app all running on the same machine the response from the ajax calls were quick as was the redraw, in fact it was almost indistinguishable from it just doing normal thinking. But I do believe that if there was lag to the web calls and redraw was slow (perhaps a complex UI) then the extra delay would be noticeable, if it was solely a web app, perhaps this may be fine but as a desktop it isn't.

I did enjoy being able to separate my UI from anything that resembles work, this being the services and the templating that knockout gave me, but I am unsure if this bonus is worth all the extra hassle of having to talk to multiple services, handling http requests. It probably depends on the app you are building.

The source code can be found on the HttpServices branch of the [GitHub repo](https://github.com/PeterGerrard/EVTracker/tree/HttpServices). But to reiterate this is not complete and needs more work to bring it to feature parity with the WPF app.
