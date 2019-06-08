---
layout: post
tags: []
---
So, I'm a bit of a Pokémon fan and a few years ago I decided to have a crack creating a stat tracker for the Pokémon Black and White. It worked and all that but I created it 3 years ago and my skills have improved since then.

Recently I wanted to have a go at creating a WPF app so I thought I'd crack this old code out and have a go at updating it. I'm going to blog about the update as I go. So first the code as it was when I picked it up (see the [Github repo](https://github.com/PeterGerrard/EVTracker/tree/0a78d0e35f6f0ff98cb161dfe9ece7398e6db2d6")) was a bit of a mess and required some fixes before I started working. So I added the .gitignore file and updated to Visual Studio 2015 so I could use shiny new features.

Now I had a code base I could work on it was time to get cracking. But I immediately hit several problems: I had no tests, had static managers, the form was also a bit unwieldy as it contains all the logic for updating the views. So I decided to start with the low hanging fruit.

#### Fixing some low hanging fruit
My first pick of the fruit was to have a pass at all my ReSharper squiggles. This was mostly moving files and removing unused code. But it helped to remind me what the classes were and roughly what they were for. I then noticed I was not using the `using` pattern when serializing stuff to files, so I quickly did that.

About now I start cursing my younger self for not having any tests. So, I decided to look at adding tests, but I found this very hard with my current set up so I went back to safe changes with the mindset of making it testable, or at least making it so the UI was really dumb.

Interspersed with ReSharper fixes I also made the manager non static, then got rid of it entirely. Instead just passing in the objects that it would have supplied.

This is when I spotted a nasty piece of UI code I decided I could safely change with just quick manual tests.

#### Creating tab pages
Part of the UI has tab pages that relate to a single tracked pokemon, and you can have many of these tabs. And the code for producing these pages was, of course, entirely in the Form class. So out it came into its own class, it still had a bunch of problems but it was starting to look like a combined WPF control and model. This was mostly pain free, and actually reduced the number of lookups needed as each tab page could look after itself rather than the form needing to work out the correct stuff knowing what the current page is.

Previously had something akin to:

```csharp
int i = (int)((Button)sender).Tag;
IDictionary&lt;Stat, int&gt; dict = _pokemonTypes[i].GivenEffortValues;
foreach (Stat s in dict.Keys)
    switch (s)
    {
        case Stat.HP: _current.EVHP.Value = Math.Min(dict[s] * (_current.HeldItem.SelectedItem.ToString() == GetEnumDescription(Items.MachoBrace) ? 2 : 1) * (_current.Pokerus.Checked ? 2 : 1) + _current.EVHP.Value, 255);
						break;
        // Cases omitted for brevity
}
```

Which now looks something like
```csharp
if (dict.ContainsKey(Stat.HP)) _current.UpdateStat(Stat.HP, dict[Stat.HP]);
// Other cases omitted for brevity
```
with the code calling
```csharp
UpdateStat(statIncrease, _statNumericUpDowns[stat]);

// Which is this method
private void UpdateStat(int statIncrease, NumericUpDown numericUpDown)
{
    var items = HeldItem.SelectedItem as Items?;
    numericUpDown.Value = Math.Min(statIncrease * (items.HasValue && items.Value == Items.MachoBrace ? 2 : 1) * (Pokerus.Checked ? 2 : 1) + numericUpDown.Value, 255);
}
```

#### Summary
Well, this is going to take longer than I thought but at least its fodder for blogging about. You can see the code as it stood after my first session on [GitHub](https://github.com/PeterGerrard/EVTracker/tree/2055ab43a5008ec5f3acfd6c1dab9936d0d814cf), the code is in a slightly better state, but for the User the only change is there is no longer an installer. Next up, the form should not be dealing with loading of a file, so lets stop that.
