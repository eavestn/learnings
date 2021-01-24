# Setting Up Kubernetes On Windows

## Motivation(s) For This Document

**Priority: Low**

Engineering is increasingly so a matter of stringing together the right resources - nicely wrapping and applying glue to the orthogonal boundaries between services.
Many resources on the web are partial, lack thorough research, or - simply - are piecemeal hacks solving problems that have _better_ solutions. This entry in my _learnings_
repository coherently strings together official (and unofficial) documentation to increase your confidence that you are applying glue in the right areas - and doing so
with a bit of finesse that will make you feel like the programmer god you love to feel like.

### Anything Windows Sucks

Windows feels like the bastard child no one wants to acknowledge. Occasionally, Windows shows up to dinner - strung out, hungover, down on its luck - and our parental sympathies kick in: "I _guess_ I should support you". Linux and Mac roll their eyes and the hardcore Engineer parents regurgitate some meaningless platitudes. But Windows doesn't have to suck - in fact, familiarize yourself with the [recently-revamped Powershell (WSL2)](https://docs.microsoft.com/en-us/windows/wsl/about) and the beauty of Windows OS begins to unveil.

#### Windows Has Every Opinion

Microsoft wants to be the cool kid. This deep desire is reflected in _everything_ Microsoft releases: languages, editors, utilities, short cuts, cloud solutions, etc. Microsoft doesn't really seem to break ground (VS Code; TS aside). Microsoft isn't the trend setter (sorry Microsoft, I promise I love you), Microsoft is the trend follower. By many developers, engineers, support specialists - whatever the professional flavor - Microsoft seems to always have been deemed wholly incomplete; so much so that one of the first things I used to tell my students to do - or my new-to-field colleagues - was install some sort of terminal emulator like [Cmder](https://cmder.net/) because Windows never did everything as quickly as you wanted it to.

One of my former colleagues hated C#. Unlike a language like Java (which I think is massively behind the times), C# does not have an opinion about how it should be written. Opinions about its composition are not built into the language through the lack of feature set additions. C# sees another language - say Java or JavaScript - adopt a functionality - say lambda functions or duck typing - and without care for its relevance integrates it.

All of this is to say: **knowing** how to do something _right_ in the complex world of Microsoft is often difficult. Microsoft allows you to have every opinion you could possibly want to have.

### Could I Learn This Elsewhere? 

Probably. I learn by writing. So while - hopefully - you learn by reading my ramblings, I learn by composing this document.


