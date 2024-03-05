## Greedy and non-greedy

By defaut, regex are greedy in Java. Adding a ? makes the regex non greedy.

```sh
A.*c   # greedy     : AbcAbc is matched in AbcAbc
A.*?c  # non greedy : Abc is matched in AbcAbc 
```

## Negative look-ahead

Important : negative look-ahead does not consume characters !

```sh
q(?!u) # match q in "Iraq", but does not match q in "quit"
```

## Non-capturing group

```sh
(?:X) # X is the pattern
```
