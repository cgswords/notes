# HOPE 2015

## Combining effects and coeffects 
Flavien Breuvart, Marco Gaboardi, Shinya Katsumata and Dominic Orchard

effects describe how programs effect their environment, while coeffects are
requirements for programs from their environments

So these can interact: we can change the environment and the environment can
give us more requirements.

comonads and monads will show buy us this; the distributive law will demonstrate
interactions

### comonad example:
!A in linear logic

!a -> a
!a -> !!a

Also, comonoid:

!a -> ()
!a -> !a x !a

## An Algebraic Approach to Type Checking and Elaboration 
Robert Atkey

We use finally tagless to do what we want as a typechecker:

    lam :: ...

That's a typechecker for a lambda.

    typecheck (Sym x)     = sym x
    typecheck (Lam x e)   = lam x e
    typecheck (App e1 e2) = app e1 e2

It has the maybe type. Also, it induces an algebra, right? Equations hold.
This gives hints about refactoring a type-checker and how it is expected to
work.

Here are some equations:

    failure = lam A failure
            = app failure x
            = app x failure
            = have i A failture
            = hasType i A failure

    lam A x = lam A (have 0  A x)
    have n A (lam B x) = lam B (have (n + 1) A x)
    have n A (app x y) = app (have n A x) (have n A y)
    have n A (var n)   = hasType A (var n)

There's a whole slew of these. Like:

    lam A (hasType B x) = hasType (A -> B) (lam A x)

See, it induces a whole algebra of rules. And if they hold, then we can use that
implementation as a valid type-checker. 

      |- t : A iff hasType A [[t]] =/= failure
      
Now we've got a monad with monad terms (free monad). The bind is as expected. Now we get type-checker scripts which look like tactic scripts:

    lam a = Lam A (Return ())
    have n A = Have n A (Return ())
    goalIs A = HasType A (Return ())
    
Now we get some tactic languages, so we can do these proof things using this monad: 

    do goalIs ((A => B) => A => B)
       lam (A => B)
       lam A
       have 1 (A => B)
       have 0 A
       goalIs B
       app (var 1) (var 0)

We have this gross scope problem, though, to make sure scope is safe. So two
options: use de Bruijn and make the class `TypeChecker (a :: Nat -> *)`,
but then we have to sort of carry that everywhere 
[Abs Syntax Fiore Plotkin Turi LICS 1999].  

That said, gross.  HOAS: `TypeChecker nu a`, abstract pointers back into the
environment. Then `Forall nu a . TypeChecker nu a => a`. Use a lam gets us a nu
variable as `lam :: Type -> (nu -> a) -> a`. Lambda takes a pointer to a
variable and then produces its term, and so now we need to do this with the free
monad.

    do goalIs ((A => B) => A => B)
       v1 <- lam (A => B)
       v2 <- lam A
       have v1 (A => B)
       have v2 A
       goalIs B
       app (var v1) (var v2)

Awesome, now we can call these by name. And we can rewrite lam to introduce and
var to assumption. Now it's a bit like a tactic script.

    do goalIs ((A => B) => A => B)
       v1 <- introduce (A => B)
       v2 <- introduce A
       have v1 (A => B)
       have v2 A
       goalIs B
       app (assumption v1) (assumption v2)

But this isn't just a dynamically typed tactic situation: it's algebraically
founded!

We can also do type-checking elaboration as a type-checker instance. It will
elaborate the term of the type and a constructed well-typed term. And that's all
principled by the algebra, of course.

The type-checker can also be rebuilt to be bi-directional: 
`TypeChecker nu (a :: {CHK, SYN} -> *)`, for checking and synthesis steps.
A variable can synthesize its own type from the environment, a lambda can
check its own.

    instance TypeChecker (CHK -> ...., SYN -> ...)

Now terms that ar active in the environment can simply be introduced. These
terms can re-type-check as they need [Omar et all ECOOP 2014]

    do v1 <- introduce
       v2 <- introduce
       ty <- goal
       case ty of
         A -> someAConst
         B -> someBConst
         _ -> assumption v1

Treating type-checkers as effectful computation opens this all wide up, like
with recovering Prolog-style type inference 
[Algebraic Presentation of Predicate Logic, Staton 2013] with low effort.

This introduces the ability to write larger tactics because we get `choice`:

    byAssumption = 
    do g     <- goal
        ctxt <- getCtxt
        v    <- search ctxt
     where
       search = ... choice ...
 
Now the type-checker is bits of operations, not some monolithic monster.
Elaboration and type-checker scripts follow up directly, and provide insight
(like trying to understand *how* elaboration works). 

- Does this work for larger type systems? Let's find out!
- How does work in practice? Is it better than the usual approach?
- What is the relation to tactic scripts in general?
- Does this subsume hygenic macros, too, using HOAS?

## A denotational semantics for Hindley-Milner Polymorphism 
Ohad Kammar and Sean Moss

About how ML gets weird and how it works...

## Combining Manifest Contracts with State 
Michael Greenberg


    let nonReentrant : 
      forall a b . (a -> b) -> { x : a | not !inside } -> b = ...

This is hard; inside is a reference that's new and oh boy we have to deal with
that. And it's gross. (Is there a better, more compelling answer?)

What's a useful, stateful, manifest contract?

The hidden effect of allocation is *bad*.

HTT has an example with an existential. It's promising.

Amal: At the very least, you want the things effected disjoint from the types
      your program cares about. HTT has tools for reasoning bout this.

### Some things for mutual stuff
- For delaying, lazy monitors versus tuple initialization

### Other Thoughts about Effects / Purity

- For idempotence problem, hey, welcome to an EFFECT!
- Effects in contracts is obviously useful; see Findler's abandonment of
  projections

### Scoping Issue
- Dunfield + Pfenning 2004 "Tridirectional"
- Information flow control is incredibly promising! (Contracts control the PC)

## Other Things

- Rusto Lenno hand others have been working on this sort of stuff in the OO
  world with imperative effects and partial initiation. Don't pay attention
  *until I set this bit*.
