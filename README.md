ConcatMap for Elm Signals
=========================

The concatMap function means to map a function over a list and concatenate the results, like this:

```elm
List.concatMap (List.repeat 3) [1,2,3] == [1,1,1,2,2,2,3,3,3]
```

With this library, you can `concatMap` Signals too:

```elm
evenSignal : Signal String
evenSignal = Signal.constant "even"

oddSignal : Signal String
oddSignal = Signal.constant "odd"

timelyEvenOddSignal : Signal String
timelyEvenOddSignal =
  (every <| 1 * second)
    |> map (round << (/) second)
    |> concatMap (\x -> if x % 2 == 0 then evenSignal else oddSignal)
```

This example creates a time signal using the `every` function, which returns the current time milliseconds in each interaction.
Then it maps the milliseconds to seconds again. Then if the current second is an even number, `concatMap` it to the evenSignal, else with the oddSignal.

Basically this will return "even" or "odd" every second.

### Do not use this

Elm doesn't have a native way to `concatMap` signals and this is intentional, so you should avoid using this library as most as you can.
From [Evan's Paper](http://elm-lang.org/papers/concurrent-frp.pdf):

    Signals are generally quite flexible, but there are certain signals that we want to
    rule out altogether: signals of signals. Signals of signals would permit programs that
    dynamically change the structure of the Elm runtime system. In many circumstances
    this is fine. For instance, there would be no problem if two input signals were
    dynamically switched in and out. The real issue is stateful signals created with
    foldp. Imagine if a stateful signal – dependent on mouse inputs – was switched out
    of the Elm program. Should it still update on mouse inputs? Should it only update
    when it is in the program? When working directly with signals, neither answer is
    pleasant. Elm opts to avoid this behavior by ruling out switching.
    Elm’s type system rules out signals of signals by dividing types into two categories:
    primitive types and signal types. Primitive types are safe types that are
    allowed everywhere, including in signals. Signal types are dangerous types that are
    allowed everywhere except in signals.

So, in a way, concatMapping signals does help you getting rid of signals of signals, but it create dynamic signals on the fly in order to do that.
