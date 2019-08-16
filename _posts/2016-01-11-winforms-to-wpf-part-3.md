---
layout: post
tags: []
---
And now for the *fun* of implementing a new UI. Surely this will be easy, oh how I was mistaken. First thing I decided to do was to create a Control that displays a single pokÃ©mon, this was originally part of Page but splitting it out would be easiest. It would show the current pokémon details and allow you to manually update level, effort values and individual values.

## Final Prep work

Having some small experience with WPF prior to this conversion I knew that `INotifyPropertyChanged` is instrumental when binding to a wpf control. So I went and made `Pokémon` implement the interface. I use ReSharper and it gave me a handy shortcut to make all my properties call PropertyChanged. Something I forgot at this point but soon found out was I needed to observe the values in the dictionaries and not just the dictionaries so I went and found a nice NuGet package that did this (MVVMHelpers).

To check that I hadn't messed this up, I got my Page class to use the PropertyChanged event on Pokémon to UpdateTheForm rather than manually updating every single time I made a change to Pokémon.

## Creating my first Wpf Control

For simplicity I created this as part of the Main Window, and once it was working moved it to a Control, so there were less possible causes for any issues.

The first thing was to show the Pokémon header with none of the complexities of showing the stats, the things I aimed to show were:
- Species, changed via drop down
- Nature, changed via drop down
- Pokérus status, changed via a checkbox
- Level, changed via a Numeric UpDown
- Held Item, changed via a drop down
- Image

Loading the values for the drop downs was not satisfying as I had the code:
```csharp
var species = new SpeciesLoader().Load();
SpeciesComboBox.ItemsSource = species;
```

I would prefer to have this automatically loaded for me, and bound from the xaml rather than the code behind. *Spoiler Alert: I work out how to do this later, but have yet to fix this part in particular.* Each worked, after I overrode the equality checks in `PokemonType` and `Nature`. The only difficult thing was binding the image. Rather than putting the image inside the species object I decided to create a converter that would take a dex number and give the path to the image. I chose not to put it in the class because I didn't want UI only issues to creep into the backend class design. Detailing the path was very simple as I had already the images with the names being equal to the dex number, I just needed to add padding.

```csharp
public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
{
    var v = value.ToString();
    return $"Resources/{v.PadLeft(3, '0')}.png";
}
```

And then displaying the image was as simple as binding to the Source property and using the new converter.

```xml
<Image Source="{Binding Path=Species.DexNumber, Converter={StaticResource DexNumberToSource}}" DockPanel.Dock="Right"></Image>
```

## Now to show some useful information<

The next part of the screen I aimed to display was the Pokémon's stat grid, that is the base value, individual value, effort value and current value for each of the six stats. This grid, it turns out, was very easy to set up, with the changes I had made to Pokémon updating any of the values, would update the associated calculated values.

All I had to do now was to stick this (still rather ugly) control into its own file, and then add it to a tab control on the Main Window to give it a similar look and feel to the winforms control. My MainView Now looked like:

```xml
<TabControl Name="PokemonHolder" ItemsSource="{Binding Path=PokemonList}">
    <TabControl.ContentTemplate>
        <DataTemplate>
            <wpf:PokemonControl></wpf:PokemonControl>
        </DataTemplate>
    </TabControl.ContentTemplate>
</TabControl>
```

It didn't auto select the first page just, but for now I was happy enough to move on. My next aim was to allow users to apply stat items to the current pokemon, but this was a bit of a minefield which I'll explore next time. You can see the current state [in the GitHub repo](https://github.com/PeterGerrard/EVTracker/tree/eabdaeec4016767f84875dd30f6b3b6ca951480f)