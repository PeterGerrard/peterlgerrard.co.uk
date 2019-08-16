---
layout: post
tags: []
---
Now that we have stat display, we should allow the user to use certain stat altering items at the press of a button. Pressing this button should update the correct effort value and the appropriate calculated stat.

## So let's add a button

First things first, we need to know which is the currently selected Pokémon, so we can change the correct Pokémon's stats. This is fairly simple, just add a property to my main Context and bind it to the current tab's Pokémon:

```xml
<TabControl Name="PokemonHolder" ItemsSource="{Binding Path=PokemonList}" SelectedItem="{Binding Path=CurrentPokemon}" DockPanel.Dock="Left">;
```

Now we need to have a command that a button can call to do a particular item's effect. This command is fairly straightforward, it is a member of the Context so can just call `CurrentPokemon.ApplyStatBerry(Stat.SpecialAttack)`, and wiring the command to a button is almost simplicity itself.

## It can't be that easy, right?

Of course it wasn't, it would update all the values correctly, but refused to change the value of the effort value shown on screen. It took many attempts and several "walking away from the computer" moments, but I couldn't get the value in the observable dictionary to be updated on the screen. What would work was to remove the dictionaries and use six properties for each one as well. This was a mechanical change, but forced me to something I had hoped not to do writing the wpf app itself, and that was to change the original WinForms app (thankfully due to the changes I did in prepping for the new UI, I only had to change the Page class). I still left in some functions to allow me to do lookups based of the Stat enum, but when referenced from the xaml I had to reference the new properties rather than a value in a dictionary.

Perhaps there was some way of properly binding to a dictionary value, but I was so frustrated I just moved on. But with this new change everything updated correctly, all I had to do was add new Commands and Buttons for each item and then move them into their own Control. This was done and the app was starting to take shape, the last major problem was to allow the user to click on a Pokémon to defeat it and automatically add the correct effort values. You can see the current state [in the GitHub repo](https://github.com/PeterGerrard/EVTracker/tree/02b023220ffccffba163821cf4e6bef4f0a14b8a)
