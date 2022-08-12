## Write to file and display to stdout

```sh
$ command | tee file.out
$ command | tee A.out B.out C.out 	# multiple files
$ command | tee -a file.out 		# append mode
```

## Combine with sudo

```sh
# Will fail because redirection is outside of sudo scope
$ sudo echo "newline" > /etc/file.conf

# Instead, use tee
$ echo "newline" | sudo tee -a /etc/file.conf
```
