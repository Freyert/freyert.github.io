---
layout: default
author: Fulton Byrne
title: Rethinking Pull Request Reviews
---

Currently I work as an engineering manager in an organization whose culture recommends _two_ reviews for every pull request.
I have noticed for a while that as we have grown; this two review system seems painfully ineffective. Two seems like a small
number, but the time it takes to get a second review can be tremendous due to many factors. Imagine my relief when I 
read the following from Don Norman's _The Design of Everyday Things_:

> One paradox of groups is that quite often, adding more people to check a task makes it less likely that it will be done right.
> Why? Well, if you were responsible for checking the correct readings on a row of fity gauges and displays, but you know that
> two people before you had checked them and that one or two people who come after you will check your work, you might relax,
> thinking that you don't have to be extra careful. (_The Design of Everyday Things_ p. 180).

I've seen this first hand many times. The first reviewer of a pull request often leaves numerous comments and critiques while
the second reviewer merely leaves a check mark. Did the first person catch all the errors? Maybe, but the second review
certainly did not catch any new ones. So why waste our time waiting for a second review and commit the same number of errors?

## To Err is Human

One of the key ideas that Mr. Norman puts forth in Chapter 5 of _The Design of Every Day Things_ is that we can categorize
human error and with the help of these categories we can design systems that are resilient to these expected errors. In short
do not blame the person who erred, but the system that allowed the error to occur.

Take the C programming language for example. This is a system that allows great flexibility to developers, but allows the human
user to unwittingly insert vulnerabilities into their program. When the Android team started using Rust, which has systems to 
mitigate memory vulnerabilities, they observed a dramatic decrease in CVEs ([_Google Security Blog: Memory Safe Languages in Android 13_](https://security.googleblog.com/2022/12/memory-safe-languages-in-android-13.html)).
Just another reminder to focus on improving the system rather than punishing human error.
Applying this systematic strategy to pull requests we want to reduce the load and responsibility we place on the people in our system.

## The Human's Role In Review

Reviews are very tedious so we want to reduce the amount of tedium we expose anyone to; tedium causes lapses. When we require someone
to review code we want them to focus on critical aspects that we can not automatically protect against. We need to reduce the search
space for our reviewer by using a check list.

I first remember seeing a Code Review checklist when I read _Code Complete_ by Steve McConnell. I still remember that the book contained
the most thorough and wonderful checklists, but I have _never_ used any of them. Why? They are long and complex; unwieldy in a review process.
A well designed check list should avoid tedium or else people will avoid the list.

To be fair though, many of the items in Mr. McConnell's checklists have made their way into many static analysis tools and linters
commonly used on engineering teams. This is the way it should be. The tedious indentation rules and formatting handled automatically
through the system we spent time designing instead of enforcing manually.

What then should we focus on? Ensuring a new test exists for the new code? That should be automated. Ensuring that the test adequately
covers potential failure cases? Closer, but automation may even handle this.

Rather than develop "the one true check list" I would rather adopt a system that allows iterative improvement to the review cycle.
When an error occurs that review did not catch, attempt to capture the error class in a check list. Given enough time and experience
a team should eventually learn how they can automate away the error case.

My suggestion is to _learn_ how to extend any linter your team uses and attempt to develop rules specific to your domain (with extensive
documentation of course). I would also encourage learning about the wider world of static analysis and how it can help your team avoid common pitfalls.

## Conclusion

Human review should mainly be a supplemental exercise for flaws that require our "unique creative engines" (brains). If a process
exudes tedium then we should seek a machine. Restricting the review process to a single required reviewer with an emphasis on 
an automated system for capturing most errors is a better use of time. If an error escapes review the team should focus their time
on automating that error out of the system in the future, rather than expending calories searching for incorrect indentations on every pull request.


## Additional Reading

* [_Human error: models and management_](https://pmc.ncbi.nlm.nih.gov/articles/PMC1117770/)
* [_Human factors: Managing human failures_](https://www.hse.gov.uk/humanfactors/topics/humanfail.htm)

