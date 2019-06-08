---
layout: post
author: PeterGerrard
---
Recently I've been exploring <a href="http://www.specflow.org/" target="_blank">SpecFlow</a> as an alternative to standard nUnit for testing my code, and I have to say I enjoyed working with it. It was easy to set up and write my first test, but it wasn't until I started using it for test cases that it shone.

<h3>What is SpecFlow?</h3>
SpecFlow is a tool which allows you to write English-like sentences that can be translated into a standard test. For example the normal test:
[csharp]
calculator.Press(&quot;1&quot;);
calculator.Press(&quot;+&quot;);
calculator.Press(&quot;2&quot;);
calculator.Press(&quot;=&quot;);
var result = calculator.Result;
Assert.That(result, Is.EqualTo(3));
[/csharp]

is equivalent to the SpecFlow test:
[text]
When I Press 1
And I Press +
And I Press 2
And I Press =
Then the result is 3
[/text]
with the code behind:
[csharp]
[When(&quot;I Press (.*)&quot;)]
public void PressKey(string key)
{
    calculator.Press(key);
}

[Then(&quot;the result is (.*)&quot;)]
public void PressKey(decimal result)
{
    var result = calculator.Result;
    Assert.That(result, Is.EqualTo(3));
}
[/csharp]

Whilst the amount of code is fairly similar the SpecFlow test itself is really easy to show to non technical people. It can be matched against the spec and really comes into its own when documenting edge cases. I found this when I was attempting to document some <em>legacy</em> code for a <a href="https://github.com/PeterGerrard/legacy-code-kata-series/tree/origin/kata2-start" target="_blank">code kata</a>. We managed to write a whole suite of tests using just a few methods that could easily be checked against our original spec, and the people we showed it to easily understood what we were trying to test.

<h3>Surely there are some issues?</h3>
You'd be right, the main one for me is you get runtime errors if you haven't set up the definitions rather than nice compile time errors. This is more annoying than anything else. 
Also I find it easier to work entirely in C# as I can understand those tests and I don't have to jump between the test and its definition. This is the thing that makes me not want to use this for personal projects in the future, but for if there are non technical stakeholders who are interested then SpecFlow is a massive boon.
