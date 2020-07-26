[![Run on Repl.it](https://repl.it/badge/github/matt4biz/go-class-walk)](https://repl.it/github/matt4biz/go-class-walk)

# Go class: Walk example
This example builds out a tool to find files that have the same content as determined by a secure hash (ignoring file names, paths, mod dates, etc.).

In each case, we ignore files of length 0 as their hashes would always be a trivial match.

Of course, with the trivial test directory (and/or a small VM in repl.it) it will not be possible to see the real performance gains; you need to run this against a large filesystem.

Each of these four variations becomes progressively faster (although most of the benefit shows in the second program, as reading & hashing the files is the largest part of the work). 

## Sequential program
`walk0.go` runs without goroutines and does a sequential file walk.

```shell
$ go run ./walk1 test
864c9c6 2
   test/f1.txt
   test/local/f2.txt
```

## Method 1
`walk1.go` uses a fixed pool of worker goroutines and a sequential file walk to send file paths to the workers.

```shell
$ go run ./walk2 test
864c9c6 2
   test/f1.txt
   test/local/f2.txt
```

## Method 2
`walk2.go` uses a fixed pool of goroutines, but also starts a new file walk goroutine for each directory in the tree.


```shell
$ go run ./walk2 test
864c9c6 2
   test/f1.txt
   test/local/f2.txt
```

## Method 3
`walk3.go` creates a goroutine for each file and directory, but limits the number of goroutines that are allowed to do file system operations (to limit contention).

```shell
$ go run ./walk3 test
864c9c6 2
   test/f1.txt
   test/local/f2.txt
```
