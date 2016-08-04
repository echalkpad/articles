# Practical Recursion Schemes

[Original URL](https://medium.com/@jaredtobin/practical-recursion-schemes-c10648ec1c29#.53n1ucixq)

> Recursion schemes are elegant and useful patterns for expressing general computation. In particular, they allow you to 'factor recursion out' of whatever semantics you may be trying to express when...

Recursion schemes are elegant and useful patterns for expressing general computation. In particular, they allow you to 'factor recursion out' of whatever semantics you may be trying to express when interpreting programs, keeping your interpreters concise, your concerns separated, and your code more maintainable.

What's more, formulating programs in terms of recursion schemes seems to help suss out particular similarities in structure between what might be seen as [disparate problems](https://colah.github.io/posts/2015-09-NN-Types-FP/) in other domains. So aside from being a practical computational tool, they seem to be of some use when it comes to 'hacking understanding' in varied areas.

Unfortunately, they come with a pretty forbidding barrier to entry. While there are a [few](http://jozefg.bitbucket.org/posts/2014-05-19-like-recursion-but-cooler.html) [nice](https://www.youtube.com/watch?v=Zw9KeP3OzpU) [resources](http://patrickthomson.ghost.io/an-introduction-to-recursion-schemes/) out there for learning about recursion schemes and how they work, most literature around them is [quite academic](http://eprints.eemcs.utwente.nl/7281/01/db-utwente-40501F46.pdf) and awash in some astoundingly technical jargon (more on this later). Fortunately, the accessible resources out there do a great job of explaining what recursion schemes are and how you might use them, so they go through some effort to build up the required machinery from scratch.

In this article I want to avoid building up the machinery meticulously and instead concentrate mostly on understanding and using Edward Kmett's [_recursion-schemes_](https://hackage.haskell.org/package/recursion-schemes) library, which, while lacking in documentation, is very well put together and implements all the background plumbing one needs to get started.

In particular, to feel comfortable using _recursion-schemes_ I found that there were a few key patterns worth understanding:

- Factoring recursion out of your data types using pattern functors and a fixed-point wrapper.

- Using the 'Foldable' & 'Unfoldable' classes, plus navigating the 'Base' type family.

- How to use some of the more common recursion schemes out there for everyday tasks.

If you're following along in GHCi, I'm going to first bring in some imports and add a useful pragma. I'll dump a gist at the bottom; note that this article targets GHC 7.10.2 and recursion-schemes-4.1.2, plus I'll also require data-ordlist-0.4.7.0 for an example later. Here's the requisite boilerplate:

```
{-# LANGUAGE DeriveFunctor #-}

import Data.Functor.Foldable
import Data.List.Ordered (merge)
import Prelude hiding (Foldable, succ)
```

So, let's get started.

Recursion schemes are applicable to data types that have a suitable recursive structure. Lists, trees, and natural numbers are illustrative candidates.

Being so dead-simple, let's take the natural numbers as an illustrative/toy example. We can define them recursively as follows:

```
data Natural =
 Zero
 | Succ Natural
```

This is a fine definition, but many such recursive structures can also be defined in a different way: we can first 'factor out' the recursion by defining some base structure, and then 'add it back in' by using a recursive wrapper type.

The price of this abstraction is a slightly more involved type definition, but it unlocks some nice benefits -- namely, the ability to reason about recursion and base structures separate from each other. This turns out to be a very useful pattern for getting up and running with recursion schemes.

The trick is to create a different, parameterized type, in which the new parameter takes the place of all recursive points in the original type. We can create this kind of base structure for the natural numbers example as follows:

```
data NatF r =
 ZeroF
 | SuccF r
 deriving (Show, Functor)
```

This type _must_ be a functor in this new parameter, so the type is often called a 'pattern functor' for some other type. I like to use the notation '<Constructor>F' when defining constructors for pattern functors.

We can define pattern functors for lists and trees in the same way:

```
data ListF a r =
 NilF
 | ConsF a r
 deriving (Show, Functor)

data TreeF a r =
 EmptyF
 | LeafF a
 | NodeF r r
 deriving (Show, Functor)
```

Now, to add recursion to these pattern functors we're going to use the famous fixed-point type, 'Fix', to wrap them in:

```
type Nat = Fix NatF
type List a = Fix (ListF a)
type Tree a = Fix (TreeF a)
```

'Fix' is a standard fixed-point type imported from the _recursion-schemes_ library. You can get a ton of mileage from it. It introduces the 'Fix' constructor everywhere, but that's actually not much of an issue in practice. One thing I typically like to do is add some smart constructors to get around it:

```
zero :: Nat
zero = Fix ZeroF

succ :: Nat -> Nat
succ = Fix . SuccF

nil :: List a
nil = Fix NilF

cons :: a -> List a -> List a
cons x xs = Fix (ConsF x xs)
```

Then you can write expressions like 'succ (succ (succ zero))' without having to deal with the 'Fix' constructor explicitly. Note also that these expressions are Showable à la carte, for example in GHCi:

```
> succ (succ (succ zero))
Fix (SuccF (Fix (SuccF (Fix (SuccF (Fix ZeroF))))))
```

The 'Fix' type is brought into scope from 'Data.Functor.Foldable', but it's worth looking at it in some detail. It can be defined as follows, along with two helpful functions for working with it:

```
newtype Fix f = Fix (f (Fix f))

fix :: f -> Fix f
fix = Fix

unfix :: Fix f -> f (Fix f)
unfix (Fix f) = f
```

'Fix' has a simple recursive structure. For a given value, you can think of 'fix' as adding one level of recursion to it. 'unfix' in turn removes one level of recursion.

This generic recursive structure is what makes 'Fix' so useful: we can write some nominally recursive type we're interested in without actually using recursion, but then package it up in 'Fix' to hijack the recursion it provides automatically.

If we wrap a pattern functor in 'Fix' then the underlying machinery of _recursion-schemes_ should 'just work'. Here it's worth explaining a little as to why that's the case.

There are two fundamental type classes involved in _recursion-schemes:_ 'Foldable' and 'Unfoldable'. These serve to tease apart the recursive structure of something like 'Fix' even more: loosely, 'Foldable' corresponds to types that can be 'unfixed', and 'Unfoldable' corresponds to types that can be 'fixed'. That is, we can add more layers of recursion to instances of 'Unfoldable', and we can peel off layers of recursion from instances of 'Foldable'.

In particular 'Foldable' and 'Unfoldable' contain functions called 'project' and 'embed' respectively, corresponding to more general forms of 'unfix' and 'fix'. Their types are as follows:

```
project :: Foldable t => t -> Base t t
embed :: Unfoldable t => Base t t -> t
```

I've found it useful while using recursion-schemes to have a decent understanding of how to interpret the type family 'Base'. It appears frequently in type signatures of various recursion schemes and being able to reason about it can help a lot.

Type families are type-level functions; they take types as input and return types as output. The 'Base' definition in _recursion-schemes_ looks like this:

```
type family Base t :: articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii -> *
```

You can interpret this as a function that takes one type 't' as input and returns some other type. An implementation of this function is called an _instance_ of the family. The instance for 'Fix', for example, looks like:

```
type instance Base (Fix f) = f
```

In particular, a type family like 'Base' is a _synonym_ for instances of the family. So using the above example: anywhere you see something like 'Base (Fix f)' you can mentally replace it with 'f'.

Instances of the 'Base' type family have a structure like 'Fix', but using 'Base' enables all the internal machinery of _recursion-schemes_ to work out-of-the-box for types other than 'Fix' alone. This has a typical Kmettian flavour: first solve the most general problem, and then recover useful, specific solutions to it automatically.

Predictably, 'Fix f' is an instance of 'Base', 'Foldable', and 'Unfoldable' for some functor 'f', so if you use it, you can freely use all of _recursion-schemes_'s innards without needing to manually write any instances for your own data types. But as mentioned above, it's worth noting that you can exploit the various typeclass & type family machinery to get by without using 'Fix' at all: see i.e. [Danny Gratzer's recursion-schemes post](http://jozefg.bitbucket.org/posts/2014-05-19-like-recursion-but-cooler.html) for an example of this.

So, with some discussion of the internals out of the way, we can look at some of the more common and useful recursion schemes. I'll concentrate on the following four, as they're the ones I've found the most use for:

- _catamorphisms_, implemented via 'cata', are generalized folds.

- _anamorphisms_, implemented via 'ana_'_, are generalized unfolds.

- _hylomorphisms_, implemented via 'hylo', are anamorphisms followed by catamorphisms (corecursive production followed by recursive consumption).

- _paramorphisms_, implemented via 'para_'_, are generalized folds with access to the input argument corresponding to the most recent state of the computation.

Let me digress slightly on nomenclature.

Yes, the names of these things are celebrations of the ridiculous. There's no getting around it; they look like self-parody to almost anyone not pre-acquainted with categorical concepts. They have been accused -- probably correctly -- of being off-putting.

_That said_, they communicate important technical details and are actually not so bad when you get used to them. It's perfectly fine and even encouraged to arm-wave about folds or unfolds when speaking informally, but the moment someone distinguishes one particular style of fold from another via a prefix like i.e. _para,_ I know exactly the relevant technical distinctions required to understand the discussion. The names might be silly, but they have their place.

Anyway.

There are many other more exotic schemes that I'm sure are quite useful (see [Tim Williams's recursion schemes talk](http://www.timphilipwilliams.com/slides.html), for example), but I haven't made use of any outside of these four just yet. The _recursion-schemes_ library contains a plethora of unfamiliar schemes just waiting to be grokked, but in the interim even _cata_ and _ana_ alone will get you plenty far.

Now let's use the motley crew of schemes to do some useful computation on our example data types.

Take our natural numbers type, 'Nat'. To start, we can use a catamorphism to represent a 'Nat' as an 'Int' by summing it up.

```
natsum :: Nat -> Int
natsum = cata alg where
 alg ZeroF = 0
 alg (SuccF n) = n + 1
```

Here 'alg' refers to 'algebra', which is the function that we use to define our reducing semantics. Notice that the semantics are not defined recursively! The recursion present in 'Nat' has been decoupled and is handled for us by 'cata'. And as a plus, we still don't have to deal with the 'Fix' constructor anywhere.

As a brief aside: I like to write my recursion schemes in this way, but your mileage may vary. If you'd like to enable the 'LambdaCase' extension, then another option is to elide mentioning the algebra altogether using a very simple case statement:

```
{-# LANGUAGE LambdaCase #-}

natsum :: Nat -> Int
natsum = cata $ \case ->
 ZeroF -> 0
 SuccF n -> n + 1 
```

Some people find this more readable.

To understand how we used 'cata' to build this function, take a look at its type:

```
cata :: Foldable t => (Base t a -> a) -> t -> a
```

The 'Base t a -> a' term is the algebra; 't' is our recursive datatype (i.e. 'Nat'), and 'a' is whatever type we're reducing a value to.

Historically I've found 'Base' here to be confusing, but here's a neat trick to help reason through it.

Remember that 'Base' is a type family, so for some appropriate 't' and 'a', 'Base t a' is going to be a synonym ** for some other type. To figure out what 'Base t a' corresponds to for some concrete 't' and 'a', we can ask GHCi via this lesser-known command that evaluates type families:

```
> :kind! Base Nat Int
Base Nat Int :: *
= NatF Int
```

So in the 'natsum' example the algebra used with 'cata' must have type 'NatF Int -> Int'. This is pretty obvious for 'cata', but I initially found that figuring out what type should be replaced for 'Base' exactly could be confusing for some of the more exotic schemes.

As another example, we can use a catamorphism to implement 'filter' for our list type:

```
filterL :: (a -> Bool) -> List a -> List a
filterL p = cata alg where
 alg NilF = nil
 alg (ConsF x xs)
 | p x = cons x xs
 | otherwise = xs
```

It follows the same simple pattern: we define our semantics by interpreting recursion-less constructors through an algebra, then pump it through 'cata'.

These running examples are toys, but even here it's really annoying to have to type 'succ (succ (succ (succ (succ (succ zero)))))' to get a natural number corresponding to six for debugging or what have you.

We can use an anamorphism to build a 'Nat' value from an 'Int':

```
nat :: Int -> Nat
nat = ana coalg where
 coalg n
 | n <= 0 = ZeroF
 | otherwise = SuccF (n - 1)
```

Just as a small detail: to be descriptive, here I've used 'coalg' as the argument to 'ana', for 'coalgebra'.

Now the expression 'nat 6' will do the same for us as the more verbose example above. As always, recursion is not part of the semantics; to have the integer 'n' we pass in correspond to the correct natural number, we use the successor value of 'n -- 1'.

As an example, try to express a factorial on a natural number in terms of 'cata'. It's (apparently) doable, but an implementation is not immediately clear.

A paramorphism will operate on an algebra that provides access to the input argument corresponding to the running state of the recursion. Check out the type of 'para' below:

```
para :: Foldable t => (Base t (t, a) -> a) -> t -> a
```

If we're implementing a factorial on 'Nat' values then 't' is going to correspond to 'Nat' and 'a' is going to correspond to (say) 'Integer'. Here it might help to use the ':kind!' trick to help reason through the requirements of the algebra. We can ask GHCi to help us out:

```
> :kind! Base Nat (Nat, Int)
Base Nat (Nat, Int) :: *
= NatF (Nat, Int)
```

Side note: after doing this trick a few times you'll probably find it much easier to reason about type families sans-GHCi. In any case, we can now implement an algebra corresponding to the required type:

```
natfac :: Nat -> Int
natfac = para alg where
 alg ZeroF = 1
 alg (SuccF (n, f)) = natsum (succ n) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii f
```

Here there are some details to point out.

The type of our algebra is 'NatF (Nat, Int) -> Int'; the value with the 'Nat' type, 'n', holds the most recent input argument used to compute the state of the computation, 'f'.

If you picture a factorial defined as

```
0! = 1
(k + 1)! = (k + 1) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii k!
```

Then 'n' corresponds to 'k' and 'f' corresponds to 'k!'. To compute the factorial of the successor to 'n', we just convert 'succ n' to an integer (via 'natsum') and multiply it by 'f'.

Paramorphisms tend to be pretty useful for a lot of mundane tasks. We can easily implement 'pred' on natural numbers via 'para':

```
natpred :: Nat -> Nat
natpred = para alg where
 alg ZeroF = zero
 alg (SuccF (n, _)) = n
```

We can also implement 'tail' on lists. To check the type of the required algebra we can again get some help from GHCi; here I'll evaluate a general type family, for illustration:

```
> :set -XRankNTypes
> :kind! forall a b. Base (List a) (List a, b)
forall a b. Base (List a) (List a, b) :: *
= forall a b. ListF a (Fix (ListF a), b)
```

Providing an algebra of the correct structure lets 'tailL' fall out as follows:

```
tailL :: List a -> List a
tailL = para alg where
 alg NilF = nil
 alg (ConsF _ (l, _)) = l
```

You can check that 'tailL' indeed returns the tail of its argument.

Hylomorphisms can express general computation -- corecursive production followed by recursive consumption. Compared to the other type signatures in _recursion-schemes_, 'hylo' is quite simple:

```
hylo :: Functor f => (f b -> b) -> (a -> f a) -> a -> b
```

It doesn't even require the full structure built up for i.e. 'cata' and 'ana'; just very simple F-{co}algebras.

My favourite example hylomorphism is an absolutely beautiful implementation of mergesort. I think it helps illustrate how recursion schemes can help tease out incredibly simple structure in what could otherwise be a more involved problem.

Our input will be a Haskell list containing some orderable type. We'll use it to build a balanced binary tree via an anamorphism and then tear it down with a catamorphism, merging lists together and sorting them as we go.

The resulting code looks like this:

```
mergeSort :: Ord a => [a] -> [a]
mergeSort = hylo alg coalg where
 alg EmptyF = []
 alg (LeafF c) = [c]
 alg (NodeF l r) = merge l r

 coalg [] = EmptyF
 coalg [x] = LeafF x
 coalg xs = NodeF l r where
 (l, r) = splitAt (length xs `div` 2) xs
```

What's more, the [fusion achieved via this technique](http://fho.f12n.de/posts/2014-05-07-dont-fear-the-cat.html) is really quite lovely.

Hopefully this article helps fuel any 'programming via structured recursion' trend that might be ever-so-slowly growing.

When programming in a language like Haskell, a very natural pattern is to write little embedded languages and mini-interpreters or compilers to accomplish tasks. Typically these tiny embedded languages have a recursive structure, and when you're interpreting a recursive structure, you have use all these lovely off-the-shelf strategies for recursion available to you to keep your programs concise, modular, and efficient. The _recursion-schemes_ library really has all the built-in machinery you need to start using these things for real.

If you're interested about hearing about using recursion schemes 'for real' I recommend Tim Williams's [Exotic Tools For Exotic Trades](http://www.timphilipwilliams.com/slides.html) talk (for a motivating example for the use of recursion schemes in production) or his [talk on recursion schemes](https://www.youtube.com/watch?v=Zw9KeP3OzpU) from the London Haskell User's Group a few years ago.

So happy recursing! I've dropped the code from this article [into a gist](https://gist.github.com/jtobin/ec5bb67a1ca2fed9c461).

Thanks to Maciej Woś for review and helpful comments.
