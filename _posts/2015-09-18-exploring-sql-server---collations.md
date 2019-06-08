---
layout: post
author: PeterGerrard
---
This is now instalment 3 in theÂ <a href="http://peterlgerrard.azurewebsites.net/index.php/2015/05/29/exploring-sql-server/" target="_blank">Exploring SQL Server</a> series, and I'm going to step back and talk a bit about a simpler topic - Collations. These are simply how to compare two pieces of text. Do I care about case, or accents? In what character set should I use?
<h4>So what values does it compare?</h4>
It compares all of your text columns - the non-unicode <strong>char</strong>, <strong>varchar</strong>, andÂ <strong>text</strong>, along with the unicode typesÂ <strong>nchar</strong>,Â <strong>nvarchar</strong>, andÂ <strong>ntext</strong>.
<h4>And how does it affect comparisons?</h4>
When sorting it defines the order of the sort. For example if you use traditional Spainish, then 'ch' will come at the end of the 'c' section rather than the middle.

As well as sorting it also affects how to tell if two strings are equivalent. Some of the more obvious things it will check for are case and accents: For example If you use a case and accent insensitive collation then 'Pokemon' and 'pokÃ©mon' will be considered as equal.

AnotherÂ thing it can be sensitive to is Japanese <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">kana</a>Â <a href="https://en.wikipedia.org/wiki/Mora_(linguistics)" target="_blank">mora</a>Â characters. Should it treat symbols from different kana as the same if they mean the same mora. e.g.Â <a class="mw-redirect" title="ã‚" href="https://en.wikipedia.org/wiki/%E3%81%82">ã‚</a>Â andÂ <a class="mw-redirect" title="ã‚¢" href="https://en.wikipedia.org/wiki/%E3%82%A2">ã‚¢</a>Â both stand for the mora 'a'.
<h4>Does it do anything else?</h4>
When using non unicode types the collation determines the character set to use. E.g. a latin collation will support standard latin characters like the ones I am using now. These are actually in most code pages as part of ASCII, this is just the first part of the page. If you look at the later part in the latin collation it is full of accented charters used in western European languages. But if I use a Japanese collation it will have Kanji, a Russian collation would have Cyrillic characters and so on.

So, if if used a standard latin collation, I could not store the valueÂ Ð”, which is from the Cyrillic alphabet.
<h4>So how do I use it?</h4>
Quick answer, if you have a database already then you already are. When you install SQL Server it will set the default collation to match the machine it is installed on.
<h4>How do I change it then?</h4>
Well, as is the case in a lot of SQL Server, it is fairly straightforward, and can be done on a variety of levels. You can set it at the server/database/column/query level.

For example I want to create a Chinese column for my table, I can add the COLLATEÂ Chinese_PRC_CS_AS to my column definition. Or if I wanted to do it a SELECT statement I can do the same.

&nbsp;

Hope this helps clear up what a collation is and why you might want to change the default. Though if you just wish to use Unicode then it is mostly just sorting and whether two characters should be considered equal.
