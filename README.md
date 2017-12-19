# The Haskell performance checklist

You have a Haskell program that's not performing how you'd like. Use
this list to check that you've done the usual steps to performance
nirvana:

✓ [Are you compiling with -Wall?](#are-you-compiling-with--wall)

✓ [Are you compiling with `-O` or above?](#are-you-compiling-with--o-or-above)

✓ [Have you run your code with the profiler?](#have-you-run-your-code-with-the-profiler)

✓ [Have you setup an isolated benchmark?](#have-you-setup-an-isolated-benchmark)

✓ [Have you looked at strictness of your function arguments?](#have-you-looked-at-strictness-of-your-function-arguments)

✓ [Are you using the right data structure?](#are-you-using-the-right-data-structure)

✓ [Are your data types strict and/or unpacked?](#are-your-data-types-strict-andor-unpacked)

✓ [Did you check your code isn't too polymorphic?](#did-you-check-your-code-isnt-too-polymorphic)

✓ [Do you have an explicit export list?](#do-you-have-an-explicit-export-list)

✓ [Have you looked at the Core?](#have-you-looked-at-the-core)

✓ [Have you considered unboxed arrays/strefs/etc?](#have-you-considered-unboxed-arraysstrefsetc)

✓ [Are you using Text or ByteString instead of String?](#are-you-using-text-or-bytestring-instead-of-string)

✓ [Have you considered compiling with LLVM?](#have-you-considered-compiling-with-llvm)

## Are you compiling with -Wall?

GHC warns about type defaults and missing type signatures:

* If you let GHC default integers, it will choose `Integer`. This is
  [10x slower](https://github.com/haskell-perf/numbers#numbers) than
  `Int`. So make sure you explicitly choose your types.
* You should have explicit types to not miss something obvious in the
  types that is slow.

## Are you compiling with `-O` or above?

By default GHC does not optimize your programs. Cabal and Stack enable
this in the build process. If you're calling `ghc` directly, don't
forget to add it.

Enable `-O2` for more optimizations.

## Have you run your code with the profiler?

Profiling is the standard way to see for expressions in your program:

1. How many times they run?
2. How much do they allocate?

Resources on profiling:

* [GHC manual on profiling](https://downloads.haskell.org/~ghc/master/users-guide/profiling.html)
* [Profiling with Stack](https://stackoverflow.com/questions/32123475/profiling-builds-with-stack)
* [Profiling with Cabal](https://nikita-volkov.github.io/profiling-cabal-projects/)

## Have you setup an isolated benchmark?

Benchmarking is a tricky business to get right, especially when timing
things at a smaller scale. Haskell is lucky to have a very good
benchmarking package. If you are asking someone for help, you are
helping them by providing benchmarks, and they are likely to ask for
them.

Do it right and use Criterion.

Resources on Criterion:

* [Criterion homepage](http://www.serpentine.com/criterion/)

## Have you looked at strictness of your function arguments?

https://wiki.haskell.org/Performance/Strictness

## Are you using the right data structure?

This GitHub organization provides comparative benchmarks against a few
types of data structures. You can often use this to determine which
data structure is best for your problem:

* [sets](https://github.com/haskell-perf/sets) - for set-like things
* [dictionaries](https://github.com/haskell-perf/dictionaries)  -
  dictionaries, hashmaps, maps, etc.
* [sequences](https://github.com/haskell-perf/sequences) - lists,
  vectors/arrays, sequences, etc.

**Tip**: Lists are *almost always* the wrong data structure. But
  sometimes they are.

See also
[HaskellWiki on data structures](https://wiki.haskell.org/Performance#Specific_comparisons_of_data_structures).

## Are your data types strict and/or unpacked?

https://wiki.haskell.org/Performance/Data_types

## Did you check your code isn't too polymorphic?

https://wiki.haskell.org/Performance/Overloading

## Do you have an explicit export list?

https://wiki.haskell.org/Performance/Modules

## Have you looked at the Core?

https://wiki.haskell.org/Performance/GHC#Looking_at_the_Core

## Have you considered unboxed arrays/strefs/etc?

https://hackage.haskell.org/package/vector-0.12.0.1/docs/Data-Vector-Unboxed.html

https://hackage.haskell.org/package/mutable-containers-0.3.3/docs/Data-Mutable.html#t:URef

## Are you using Text or ByteString instead of String?

http://www.alexeyshmalko.com/2015/haskell-string-types/

https://www.reddit.com/r/haskell/comments/120h6i/why_is_this_simple_text_processing_program_so/

## Have you considered compiling with LLVM?

https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/codegens.html#llvm-code-generator-fllvm
