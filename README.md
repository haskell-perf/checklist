# The Haskell performance checklist

You have a Haskell program that's not performing how you'd like. Use
this list to check that you've done the usual steps to performance
nirvana:

✓ [Are you compiling with -Wall?](#are-you-compiling-with--wall)

✓ [Are you compiling with `-O` or above?](#are-you-compiling-with--o-or-above)

✓ [Have you run your code with the profiler?](#have-you-run-your-code-with-the-profiler)

✓ [Have you checked for stack space leaks?](#have-you-checked-for-stack-space-leaks)

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
forget to add `-O`.

Enable `-O2` for serious, non-dangerous optimizations.

* [GHC manual on optimization flags](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/using-optimisation.html#o-convenient-packages-of-optimisation-flags)

## Have you run your code with the profiler?

Profiling is the standard way to see for expressions in your program:

1. How many times they run?
2. How much do they allocate?

Resources on profiling:

* [GHC manual on profiling](https://downloads.haskell.org/~ghc/master/users-guide/profiling.html)
* [Profiling with Stack](https://stackoverflow.com/questions/32123475/profiling-builds-with-stack)
* [Profiling with Cabal](https://nikita-volkov.github.io/profiling-cabal-projects/)

## Have you checked for stack space leaks?

Most space leaks result in an excess use of stack. If you look for the
part of the program that results in the largest stack usage, that is
the most likely space leak, and the one that should be investigated
first.

Resource on stack space leak:

* [Detecting Space Leaks](http://neilmitchell.blogspot.co.uk/2015/09/detecting-space-leaks.html?m=1)

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
  sometimes they are the right one.

See also
[HaskellWiki on data structures](https://wiki.haskell.org/Performance#Specific_comparisons_of_data_structures).

## Are your data types strict and/or unpacked?

By default, Haskell fields are lazy and boxed. Making them strict can
often (not always) give them more predictable performance, and unboxed
fields (such as integers) do not require pointer indirection.

Resources on data type strictness:

* [HaskellWiki on strict fields](https://wiki.haskell.org/Performance/Data_types#Unpacking_strict_fields)

## Did you check your code isn't too polymorphic?

Code which is type-class-polymorphic, such as,

``` haskell
genericLength :: Num n => [a] -> n
```

has to accept an addictional dictionary argument for which class
instance you want to use for `Num`. That can make things slower.

Resources on overloading:

* [HaskellWiki on overloading](https://wiki.haskell.org/Performance/Overloading)

## Do you have an explicit export list?

https://wiki.haskell.org/Performance/Modules

## Have you looked at the Core?

Haskell compiles down to a small language, Core, which represents the
real code generated before assembly. This is where many optimization
passes take place.

Resources on core:

* [HaskellWiki on core](https://wiki.haskell.org/Performance/GHC#Looking_at_the_Core)

## Have you considered unboxed arrays/strefs/etc?

An array with boxed elements such as `Data.Vector.Vector a` means each
element is a pointer to the value, instead of containing the values
inline.

Use an unboxed vector where you can (integers and atomic types like
that) to avoid the pointer indirection. The vector may be stored and
accessed in the CPU cache, avoiding mainline memory altogether.

Likewise, a mutable container like `IORef` or `STRef` both contain a
pointer rather than the value. Use `URef` for an unboxed version.

* [Data.Vector.Unboxed](https://hackage.haskell.org/package/vector-0.12.0.1/docs/Data-Vector-Unboxed.html)
* [Data.Mutable](https://hackage.haskell.org/package/mutable-containers-0.3.3/docs/Data-Mutable.html#t:URef)

## Are you using Text or ByteString instead of String?

The `String` type is slow for these reasons:

* It's a linked list, meaning access is linear.
* It's not a packed representation, so each character is a separate
  structure with a pointer to the next. It requires access to mainline
  memory.
* It allocates a lot more memory than packed representations.

Resources on string types:

* [Haskell String Types](http://www.alexeyshmalko.com/2015/haskell-string-types/)

Case studis:

* [A reddit case study](https://www.reddit.com/r/haskell/comments/120h6i/why_is_this_simple_text_processing_program_so/)

## Have you considered compiling with LLVM?

https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/codegens.html#llvm-code-generator-fllvm
