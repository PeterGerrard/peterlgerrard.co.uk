---
layout: post
tags: [SQL Server]
---
This is now instalment 3 in the [Exploring SQL Server]({% post_url 2015-05-29-exploring-sql-server %}) series, and I'm going to step back and talk a bit about a simpler topic - Collations. These are simply how to compare two pieces of text. Do I care about case, or accents? In what character set should I use?

#### So what values does it compare?
It compares all of your text columns - the non-unicode **char**, **varchar**, and **text**, along with the unicode types **nchar**, **nvarchar**, and **ntext**.

#### And how does it affect comparisons?
When sorting it defines the order of the sort. For example if you use traditional Spainish, then 'ch' will come at the end of the 'c' section rather than the middle.

As well as sorting it also affects how to tell if two strings are equivalent. Some of the more obvious things it will check for are case and accents: For example If you use a case and accent insensitive collation then 'Pokemon' and 'pokémon' will be considered as equal.

Another thing it can be sensitive to is Japanese [kana](https://en.wikipedia.org/wiki/Kana) [mora](https://en.wikipedia.org/wiki/Mora_(linguistics)) characters. Should it treat symbols from different kana as the same if they mean the same mora. e.g. [あ]]https://en.wikipedia.org/wiki/%E3%81%82)‚ and [ア](https://en.wikipedia.org/wiki/%E3%82%A2) both stand for the mora 'a'.

#### Does it do anything else?
When using non unicode types the collation determines the character set to use. E.g. a latin collation will support standard latin characters like the ones I am using now. These are actually in most code pages as part of ASCII, this is just the first part of the page. If you look at the later part in the latin collation it is full of accented charters used in western European languages. But if I use a Japanese collation it will have Kanji, a Russian collation would have Cyrillic characters and so on.

So, if if used a standard latin collation, I could not store the value Д, which is from the Cyrillic alphabet.

#### So how do I use it?
Quick answer, if you have a database already then you already are. When you install SQL Server it will set the default collation to match the machine it is installed on.

#### How do I change it then?
Well, as is the case in a lot of SQL Server, it is fairly straightforward, and can be done on a variety of levels. You can set it at the server/database/column/query level.

For example I want to create a Chinese column for my table, I can add the COLLATE Chinese_PRC_CS_AS to my column definition. Or if I wanted to do it a SELECT statement I can do the same.

Hope this helps clear up what a collation is and why you might want to change the default. Though if you just wish to use Unicode then it is mostly just sorting and whether two characters should be considered equal.
