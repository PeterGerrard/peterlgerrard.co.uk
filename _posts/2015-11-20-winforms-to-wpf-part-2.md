---
layout: post
tags: []
---
So we had a slightly better code base to move to WPF, but still not great. In particular I was not confident in just swapping out the UI as there were no tests and the UI was tightly coupled to the business logic. So in this session I decided to focus on a particular part of the UI to decouple from the logic, today I tackled the Pokémon tab control. Last time around I had pulled out the tab page structure into its own file, but it still had the logic to handle the updating the model, and some of that logic was still in the form class. My objectives for the session were for each button press/value change to be simplified to a two liner.

```csharp
Pokemon.DoUpdate(UpdateDetails);
RedrawTabPage();
```

If I could do this then the move to WPF for this control should be fairly relaxed.

#### Simple things first
The first problem I saw was the special code I had for loading a Pokémon. This forced me to always create a page with the Null Pokémon (which is the ever faithful MissingNo), and then load the Pokémon. The main issue for me was the Page class needing special control for the idea of the Null Pokémon and having to have two seperate methods for updating the static parts of the tab page with the dynamic parts. To fix this I took the code from Load and put it into UpdateForm. But know how do you initially set the Pokémom, the constructor seems sensible so we can make a simple change to the creation of the Pokémon field and set it to a new parameter.

Alongside this simple change I noticed just how bad my updating of the model and view was. In essence it was:
1. Update appropriate user controls
1. Set the models properties to match the UI
1. Update all the user controls with computed values

This is cumbersome and really I'd much prefer:
1. Update appropriate model properties
1. Update all the user controls with model values

This was fairly straightforward, I already had methods that matched steps 2 and 3, What I wanted was to expand step 3 to all value (Not too difficult), get rid of step 2 and only ever assign to UI controls in the UpdateForm method. Again while not simple to do this was straightforward, but did make changes that started at step 2 more complicated as they now need to update the model, then call UpdateForm().

#### Having logic in Page is illogical
So we have several methods that manipulate the Pokémon state that live in the Page class, specifically the places where an Item is involved (Apply Item and the item buttons come to mind). But with the most recent change, all these methods do is call `Pokemon.Property = function();UpdateForm()`. So we can simply move them into the Pokémon class and just call them. Now the Page class is pretty much all:
```csharp
Pokemon.DoUpdate(UpdateDetails);
RedrawTabPage();
```

#### Hang on, you said some logic was in Form?
Correct, and it still is and has similar problems to those I just mentioned. Previously clicking the button ran the following code.

```csharp
class Form1 : Form
{
    void b_Click(object sender, EventArgs e)
    {
        var i = (int)((Button)sender).Tag;
        var dict = _pokemonTypes[i].GivenEffortValues;
 
        if (dict.ContainsKey(Stat.HP)) _current.UpdateStat(Stat.HP, dict[Stat.HP]);
        if (dict.ContainsKey(Stat.Attack)) _current.UpdateStat(Stat.Attack, dict[Stat.Attack]);
        if (dict.ContainsKey(Stat.Defence)) _current.UpdateStat(Stat.Defence, dict[Stat.Defence]);
        if (dict.ContainsKey(Stat.SpecialAttack)) _current.UpdateStat(Stat.SpecialAttack, dict[Stat.SpecialAttack]);
        if (dict.ContainsKey(Stat.SpecialDefence)) _current.UpdateStat(Stat.SpecialDefence, dict[Stat.SpecialDefence]);
        if (dict.ContainsKey(Stat.Speed)) _current.UpdateStat(Stat.Speed, dict[Stat.Speed]);

        _current.ApplyItem(((Items) _current.HeldItem.SelectedItem));
    }
}

class Page
{
    public void UpdateStat(Stat stat, int statIncrease)
    {
        Pokemon.UpdateStat(stat, statIncrease);
        UpdateForm();
    }

    public void ApplyItem(Items item)
    {
        Pokemon.ApplyItem(item);
        UpdateForm();
    }
}
```

And lets be honest the form should not know about stats. What clicking the button means is defeat the Pokémon associated with the button. So lets just move the code into a method inside Page, and then into Pokémon. Whilst doing this I also made sure that we only passed the associated Pokémon in and never interrogated it.

#### Final clean up
I was fairly happy with the Page class but I could see one final small change to do, and that was to simplify using it. Lets not make it have a TabPage, but instead just make it a TabPage and easily pass it around in the form. This would reduce my need for abusing the Tag system in WinForms (also a tabpage contained the associated Page, which points back at the TabPage. So lets just add the `: TabPage` and remove the TabPage field from Page. All the changes simply fell out in Page, and mostly in Form as well. The final thing was I really didn't need to store the current Page object showing if its always tabSelector.Current, so just use that.

Next time, I'm actually going to attempt to start moving to WPF. You can see the current state [in the GitHub repo](https://github.com/PeterGerrard/EVTracker/tree/e1d1d6c8c378f6d259a424769d3eff243c87a8b1)
