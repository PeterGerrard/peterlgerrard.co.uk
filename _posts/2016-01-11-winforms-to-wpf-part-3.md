---
layout: post
author: PeterGerrard
---
<p>And now for the <i>fun</i> of implementing a new UI. Surely this will be easy, oh how I was mistaken. First thing I decided to do was to create a Control that displays a single pokÃ©mon, this was originally part of Page but splitting it out would be easiest. It would show the current pokÃ©mon details and allow you to manually update level, effort values and individual values.</p>

<h2>Final Prep work</h2>

<p>Having some small experience with WPF prior to this conversion I knew that <code>INotifyPropertyChanged</code> is instrumental when binding to a wpf control. So I went and made <code>PokÃ©mon</code> implement the interface. I use ReSharper and it gave me a handy shortcut to make all my properties call PropertyChanged. Something I forgot at this point but soon found out was I needed to observe the values in the dictionaries and not just the dictionaries so I went and found a nice NuGet package that did this (MVVMHelpers).</p>

<p>To check that I hadn't messed this up, I got my Page class to use the PropertyChanged event on PokÃ©mon to UpdateTheForm rather than manually updating every single time I made a change to PokÃ©mon.</p>

<h2>Creating my first Wpf Control</h2>

<p>For simplicity I created this as part of the Main Window, and once it was working moved it to a Control, so there were less possible causes for any issues.</p>

<p>The first thing was to show the Pokemon header with none of the complexities of showing the stats, the tthings I aimed to show were:</p>
<ul>
  <li>Species, changed via drop down</li>
  <li>Nature, changed via drop down</li>
  <li>PokÃ©rus status, changed via a checkbox</li>
  <li>Level, changed via a Numeric UpDown</li>
  <li>Held Item, changed via a drop down</li>
  <li>Image</li>
</ul>

<p>Loading the values for the drop downs was not satisfying as I had the code:</p>
[csharp]
var species = new SpeciesLoader().Load();
SpeciesComboBox.ItemsSource = species;
[/csharp]

<p>I would prefer to have this automatically loaded for me, and bound from the xaml rather than the code behind. <i>Spoiler Alert: I work out how to do this later, but have yet to fix this part in particular.</i> Each worked, after I overrode the equality checks in <code>PokemonType</code> and <code>Nature</code>. The only difficult thing was binding the image. Rather than putting the image inside the species object I decided to create a converter that would take a dex number and give the path to the image. I chose not to put it in the class because I didn't want UI only issues to creep into the backend class design. Detailing the path was very simple as I had already the images with the names being equal to the dex number, I just eeded to add padding.</p>

[csharp]
public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
{
    var v = value.ToString();
    return $&quot;Resources/{v.PadLeft(3, '0')}.png&quot;;
}
[/csharp]

<p>And then displaying the image was as simple as binding to the Source property and using the new converter.</p>

[xml]
&lt;Image Source=&quot;{Binding Path=Species.DexNumber, Converter={StaticResource DexNumberToSource}}&quot; DockPanel.Dock=&quot;Right&quot;&gt;&lt;/Image&gt;
[/xml]

<h2>Now to show some useful information</h2>

<p>The next part of the screen I aimed to display was the Pokemon's stat grid, that is the base value, individual value, effort value and current value for each of the six stats. This grid, it turns out, was very easy to set up, with the changes I had made to PokÃ©mon updating any of the values, would update the associated calculated values.</p>

<p>All I had to do now was to stick this (still rather ugly) control into its own file, and then add it to a tab control on the Main Window to give it a similar look and feel to the winforms control. My MainView Now looked like:</P.

[xml]
&lt;TabControl Name=&quot;PokemonHolder&quot; ItemsSource=&quot;{Binding Path=PokemonList}&quot;&gt;
    &lt;TabControl.ContentTemplate&gt;
        &lt;DataTemplate&gt;
            &lt;wpf:PokemonControl&gt;&lt;/wpf:PokemonControl&gt;
        &lt;/DataTemplate&gt;
    &lt;/TabControl.ContentTemplate&gt;
&lt;/TabControl&gt;
[/xml]

<p>It didn't auto select the first page just, but for now I was happy enough to move on. My next aim was to allow users to apply stat items to the current pokemon, but this was a bit of a minefield which I'll explore next time. You can see the current state <a href="https://github.com/PeterGerrard/EVTracker/tree/eabdaeec4016767f84875dd30f6b3b6ca951480f">in the GitHub repo</a></p>
