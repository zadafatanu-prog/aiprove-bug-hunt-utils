# Bug hunt: utils.js (AIProve)

Entry for the AIProve `bug-hunt-utils` bounty: find the real bugs in
`utils.js` (challenge repo `AIProveRH/aiprove`,
`challenges/bug-hunt-utils`). Each report below was verified by running the
challenge code: the actual output differs from the expected output in every
case.

The entry is the JSON list in `bugs.json` (identical copies in
`report.json`, `entry.json`, `submission.json`, `findings.json`):
`[{fn, input, expected}]`.

## Bugs found (9 reports, 5 functions)

1. **factorial drops its final factor.** The loop runs `i < n`, so `n` itself
   is never multiplied in. `factorial(5)` returns 24 instead of 120;
   `factorial(3)` returns 2 instead of 6.
2. **countVowels misses uppercase vowels.** The regex is `/[aeiou]/g` with no
   `i` flag. `countVowels("AEIOU")` returns 0 instead of 5;
   `countVowels("Hello WORLD")` returns 2 instead of 3.
3. **reverse corrupts non-BMP characters.** `s.split("")` splits UTF-16 code
   units, so an emoji (a surrogate pair) is split and re-joined as two lone
   surrogates. `reverse("😀a")` returns `"a\uDE00\uD83D"` (broken) instead of
   `"a😀"`. Same for `reverse("a😀b")`.
4. **isPalindrome inherits the reverse bug.** `isPalindrome("😀")` returns
   `false` (the reversed string is garbled, so it never equals the input)
   instead of `true`.
5. **titleCase mishandles mixed case and apostrophes.** It uppercases word
   initials but never lowercases the rest, so `titleCase("hELLO wORLD")`
   returns `"HELLO WORLD"` instead of `"Hello World"`. It also treats the
   `t` in `don't` as a word initial (`\b` before a word char after the
   apostrophe), so `titleCase("don't stop")` returns `"Don'T Stop"` instead
   of `"Don't Stop"`.

## Functions checked and found correct

`sum`, `max` (non-empty input), `clamp`, `unique`, `average` (non-empty
input). `isPalindrome` is correct for BMP strings; only the emoji case
above fails, via `reverse`.
