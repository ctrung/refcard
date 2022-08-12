## Doc options perl
`perldoc perlrun`

## Multi line replace
```perl
perl -i -0pe 's/...\n.../...\n.../' [FILES...]

# -i : inline replace in files
# -0 : slurp mode (whole file content is one big line)
# -p : iteration mode (program run all FILES)
# -e : 'sed like' program instruction provided on CLI 
```
