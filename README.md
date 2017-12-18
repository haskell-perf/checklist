# The Haskell performance checklist

You have a Haskell program that's that performing how you'd like.

0. [ ] Are you compiling with -Wall? 
0. [ ] Did you compile with `-O` or above?
0. [ ] Have you run your code with the profiler? 
0. [ ] Have you setup an isolated benchmark? 
0. [ ] Have you looked at strictness of your function arguments?
0. [ ] Are you using the right data structure? 
0. [ ] Are your data types strict and/or unpacked?
0. [ ] Did you check your code isn't too polymorphic? 
0. [ ] Do you have an explicit export list?
0. [ ] Have you looked at the Core?
0. [ ] Have you considered unboxed arrays/strefs/etc? 
0. [ ] Are you using Text or ByteString instead of String?
0. [ ] Have you considered compiling with LLVM?
