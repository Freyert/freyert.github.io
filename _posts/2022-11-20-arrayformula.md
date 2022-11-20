---
layout: default
author: Fulton Byrne
title: Google Sheets ARRAYFORMULA
---

One of the things I dislike most about Google Sheets/Excel is having to "drag" for auto-complete. I often find I spend a _ton_ of time dragging, and redragging. Auto-complete can be ambigious and sometimes the intention goes awry and rows
populate incorrectly. I would prefer a more "explicit" auto-completion.

I learned recently there is a nice way to specify a column or row header in a single cell that expands to all cells in the row or column; therefore eliminating the need for dragged auto-complete.


## [ARRAYFORMULA](https://support.google.com/docs/answer/3093275)

ARRAYFORMULA is the drag and drop killer. The documentation is very vague on its usage, but a little searching and experimentation is very illuminating.

The examples from the documentation I would say are not great as far as introductory ARRAYFORMULAs go. Currently I mostly use ARRAYFORMULA to do basic operations on a single column or row.

`ARRAYFORMULA(A2:A + B2:B)`: for each cell in A, add it to each cell in B, and generate an entry in the array. That's really it! I've done some other basics like dividing all cells by a constant (`ARRAYFORMULA(A2:A/26)`), but have stayed entirely in `Nx1` dimension arrays.

## Expansion

Google sheets supports instantiating array literls via `{}`. So the pattern for a row with a header looks something like: `{"header", ARRAYFORMULA{A2:A} }`. This places the value "header" in our new array's 0th index and concatenates the result of the `ARRAYFORMULA` into the rest of the array. Using `,` to separate the values into columns therefore creating a `1xN` matrix. Using a `;` to separate values would places values into rows therefore creating a `Nx1` matrix. ([_Using arrays in Google Sheets](https://support.google.com/docs/answer/6208276)).


## Conclusion

I have used Spreadsheet software casually for several decades without being privy to the `ARRAYFORMULA` function. Knowing these functions provides a higher level of thinking that can create extremely powerful and correct sheets very quickly.

