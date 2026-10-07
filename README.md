# Bazel escaping bug for rc files

Bazel's [documentation](https://bazel.build/run/bazelrc) says, of the lines of a Bazel rc file, that

> [e]ach line contains a sequence of words, which are tokenized according to the same rules as the Bourne shell.

In Bash, backslashes in single-quoted strings are always *literal*: they're not used for escaping.

For example,

```
$ echo 'hello\world'
hello\world
```

However, Bazel's rc parser assumes that backslashes are used for escaping in all contexts, even in single-quoted strings.

This means that backslashes have to be explicitly escaped in single-quoted strings to avoid them being removed.

Demo
====

Given

`rc`:
```
common --define 'one=hello\world'
common --define 'two=hello\\world
```

according to Bash rules I'd expect `one` to have the value `hello\world` and `two` to have the value `hello\\world`.

Instead,

```
$ bazel --bazelrc rc info --announce_rc release
[...]
Inherited 'common' options: --define one=helloworld --define two=hello\world
[...]
```
