---
layout: post
author: PeterGerrard
---
<p>This will be the last post about moving to WPF, whilst the app is far from complete, the major parts have been moved over, a few auxiliary functions are missing (e.g Save and Load), along with a very poor visual look to the app. These problems are straight forward and not worth talking about, though f you want to hear more, let me know and I may change my mind.</p>

<p>As for today's work I need to add the last large section from the original Page class, and that is being able to select the game, route and then clicking on a PokÃ©mon to defeat it and gain the relevant effort values.</p>

<h2>Surprisingly simple to set up</h2>

<p>With all the previous changes I had made and the infrastructure already set up this was remarkably easy to get done. I needed a new command to defeat a pokÃ©mon, and be able to display all the pokÃ©mon for the selected route. Defeating a pokÃ©mon is easy, I just need to know its number, get the associated species and call Defeat using it. So long as the CommandParameter was the dex number of the defeated pokÃ©mon then I could call:</p>

[csharp]
var speciesNumber = o as int?;
if (!speciesNumber.HasValue) return;
CurrentPokemon.Defeat(_species[speciesNumber.Value]);
[/csharp]

<p>Displaying all the correct pokÃ©mon was not hard either, I had already had practice with templates. I could reuse the image display I had used in <a href="http://www.peterlgerrard.co.uk/index.php/2016/01/11/winforms-to-wpf-part-3/" target="_blank">Part 3</a>. The only difficult part was finding the correct control to use to display a series of buttons, it turns out an ItemsControl/WrapPanel was a perfect combination. Aside from adding a few extra properties to my Context there was very little to do and my new control looked like:</p>

[xml]
&lt;ItemsControl ItemsSource=&quot;{Binding Path=CurrentRoute.Pokemon}&quot;&gt;
    &lt;ItemsControl.ItemsPanel&gt;
        &lt;ItemsPanelTemplate&gt;
            &lt;WrapPanel Orientation=&quot;Horizontal&quot; ScrollViewer.HorizontalScrollBarVisibility=&quot;Disabled&quot; /&gt;
        &lt;/ItemsPanelTemplate&gt;
    &lt;/ItemsControl.ItemsPanel&gt;
    &lt;ItemsControl.ItemTemplate&gt;
        &lt;DataTemplate&gt;
            &lt;Button Command=&quot;{Binding Path=DataContext.DefeatPokemon, RelativeSource={RelativeSource Mode=FindAncestor, AncestorType={x:Type local:MainWindow}}}&quot; CommandParameter=&quot;{Binding}&quot;&gt;
                 &lt;Image Source=&quot;{Binding Converter={StaticResource DexNumberToSource}}&quot; Height=&quot;64&quot; Width=&quot;64&quot;&gt;&lt;/Image&gt;
            &lt;/Button&gt;
        &lt;/DataTemplate&gt;
    &lt;/ItemsControl.ItemTemplate&gt;
&lt;/ItemsControl&gt;
[/xml]

<h2>Conclusions</h2>

<p>Aside from the last few minor features I now have a working wpf app, there was some head scratching and frustration and times but I'm relatively happy with the outcome and I'm more confident if I ever have to work on a wpf app again. In the future I'm not sure if I'd choose to use wpf out of the gate but I would always prefer it over WinForms. You can find the final code at <a href="https://github.com/PeterGerrard/EVTracker/tree/3ac5186fbea2560082925d7fdfd19e654611cd52" target="_blank">GitHub</a>.</p>

<h3>What did I like?</h3>
<p>First and foremost not having to deal directly with updating the screen and just letting the framework handle it. At times the auto layout works nicely but for this app it needs to be booted into shape so whilst I like it in general, this app is not a good fit Unless I rework the design.</p>

<h3>What annoyed me?</h3>
<p>I'll start with a minor annoyance, no Numeric Up Downs in standard controls library. Really? Some of the bigger issues is the amount of cruft around what I am trying to show, it is not easy to just look at the xaml and say what it should look like. I also found Context scopes to be quite fiddly at times, in particular if scoped to an individual item and I wanted to talk to the main context I either had to duplicate the definitions, or look for the MainWindow item (I couldn't even look for the Context of the right type). And finally I don't like having all the knowledge about <code>INotifyPropertyChanged</code> living in my backend, it requires a bunch of boiler plate code and is indicative of the type of frontend in use, I'd rather my backend cared not a jot about the frontend.</p>
