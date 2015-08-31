# Haskell Implementor's Workshop

## SJP's State of GHC

Partial type signatures, api annotations for source code intermation (every non-terminal in source text)

Cloud haskell static pointers for information movement!

### Coming Soon (8.0)

exhaustiveness for GADTs

injective type families (Stolarek, HS)

(You get "I can't prove fa is equal to fa', and you think, 'it's not very hard, just make them equal!'.")

Applicative do-notation (sick!)

Strict haskell compiler flag

kind equalities, type-level GADTs

visible type app (concrete arguments to poly types)

backpack (something, not full)

### more notes

#### implicit locations

implicit location for error info (might be nice to have subjective blame!) 

-- Implicit argument for head
(?cs :: CallStack) => [a] - > a
head [] = error ("..." ++ showCallStack ?cs)

And you can stack these for the whole callstack:

a :: (?cs :: CS) => ...
b :: (?cs :: CS) => ...
b = ... (a ...) ...

This gives you b and a as the callstack; it automatically adds the stack
information in. EASY SEMANTICS.

#### record overloading

There's a hasField typeclass that gets generated for records with fields, and
now you can use dispatch by figuring out the thing you're getting into.

    #x r 

This gets us type-smart selection syntax. It can be a lens, too! Because it's
all part of the IsLabel class, and it can turn a `fromLabel` (where that's `x`
above) into a `getField`.

#### consider helping

There's plenty of low-hanging fruit and a huge place; it relies on people
stepping up. So step up! It's "your compiler, not Simon's compiler."

The pain points:
- Connection between profiler output and -ddump-simpl for auto-generated SCCs
  (see DWARF) 
- Sample-based profiling
- Core-to-core API is hard to use for partial functions and the other (just
  needs love from someone who cares, and arguments of API)
- Automatic show instances for errors in containers, etc.
  - From Michael Adams:
    - To preserve referential transparency, disallow inspecting the result AT
      ALL
    - `errorShow "...." p` will let you print p, but not let you use it in the
      program (preserve RT)
 
## Levity Kinds

Learn about Induction Recursion

## Lightning Talks
