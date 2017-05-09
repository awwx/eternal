# Eternal

I’m working on a programming language I call “Eternal”, which
automatically saves your program’s data to persistent storage.

The usual approach for programming languages is that your program runs
in memory... ephemeral memory, memory that goes away when you turn off
your computer or device or end your program’s process... and
everything your program has done or has created is thrown away.

Unless you save it somewhere.  And then, when you run your program
again, read it back in again.

I notice that when I’m writing a program I often spend a lot of time
writing code to read and write data (whether to files, local storage,
databases, communicating to other devices, etc).

My hope is that by automating this part, we’ll be able to write
programs with less time and effort.


## Succinctness

In [Succinctness is Power](http://paulgraham.com/power.html), Paul
Graham has a hypothesis that a powerful language is one that is
*succinct*, where you don’t need to write as much code to create your
program.

By this he doesn’t mean *abbreviations*, the point is not that it’s
better to write code using as few letters as possible so that it looks
like you’ve been banging your head against the keyboard, but that more
powerful language features enable writing less code.

As an example, consider a program written in JavaScript using
callbacks to read `A`, read `B`, and then write `C` as the addition of
A and B.

```
read_a(function (err, a) {
  if (err)
    return callback(err);
  read_b(function (err, b) {
    if (err)
      return callback(err);
    write_c(a + b, function (err) {
      if (err)
        return callback(err);
      callback(null);
    });
  });
});
```

Using JavaScript’s new async/await feature, this could be written
instead:

```
await write_c(await read_a() + await read_b());
```

Thus, by Paul Graham’s definition, we can see that async/await is a
more powerful language feature (for this particular application at
least) compared to callbacks, because we can accomplish the same thing
by writing less code.

Paul’s language [Arc](http://arclanguage.org/) is written in Scheme
(both Lisp dialects), and Scheme has a language feature called
“continuations” which allows execution to be suspended (such as when
we’re waiting for an operation such as reading something or writing)
and later resumed (such as when that operation is ready).

Thus in Arc this same program can be written:

```
(write-c (+ (read-a) (read-b)))
```

Which, if we were writing in a C or JavaScript style of syntax instead
of in Lisp, would look something like:

```
write_c(read_a() + read_b());
```

Not everyone likes the prefix notation of Lisp, however syntax aside,
I’d argue that continuations are in turn more powerful than
async/await because I don’t have to sprinkle “await” throughout my
code in the right places in order to get it to work: I can get the
same result by writing less code.

In Eternal, where data can be saved automatically, the program could
look something like:

```
(= c (+ a b))
```


## Making vs. Engineering

Arc was designed to be a *powerful* language, by the definition of
succinctness.

What surprised me was how *pleasant* I found programming in Arc.

I’ve programmed in a *lot* of languages... Snobol, Icon, C, Pascal,
C++, Python, Perl, Ruby, JavaScript, Haskell, Clojure, Objective-C,
Forth, Java, Common Lisp, Scheme, and Basic are the ones I recall off
of the top of my head... and I *enjoy* programming in Arc when I can.

Arc was designed for *makers* (or, as Paul Graham put it in his
essays, hackers).

Engineering is important.  Most of the products we use have been
engineered.  When I’ve worked as a developer, most of what I do is
engineering.

However, there are times when I just want to get something done.  For
myself.  The kind of thing, where if for example I just want to make
some quick calculations that I could do in a spreadsheet, I’ll using a
spreadsheet instead of going through the rigmarole of writing a
program.

A spreadsheet isn’t (usually) a good engineering solution.  If you
start thinking “how do I unit test this?”, “how do I deliver this to
my customers?”, “does this spreadsheet meet our requirements”... it
may be time to start thinking about how to engineer a solution instead
of using a spreadsheet.

But for the sorts of things that I *can* use a spreadsheet for (that
it makes sense to use a spreadsheet for), it’s easier, faster, more
productive, more fun to do in a spreadsheet.

This is an analogy, since after all Arc is a programming
language... however if a programming language were like a spreadsheet
in the sense of letting you just *do* things, that’s the kind of
feeling I get from programming in Arc.

My hypothesis is that a programming language that automatically saves
a program’s data is going to be most useful, most interesting, to makers.

For a project being worked on by a team of software engineers being
able to persist data is another requirement, part of the work of the
project.  Having that part done automatically might be a bit useful,
perhaps save *some* time, but not exceptionally so.

And, for most software engineering kinds of projects, we want to save
data in an intelligible form, where it can be used independently.
In Eternal, on the other hand, saved data looks like a memory
dump... because it pretty much *is* a memory dump.

Which the typical software development project isn’t going to find
all that useful.

For a *maker* however, for the kind of thing where I’m just writing a
tool in a half an hour, than I *would* find it useful to have my data
saved for me.  Because it might take me twice as long to add the code
to read and write the data manually.


## JavaScript

Arc is written in [Racket](http://racket-lang.org/), which provides
Arc with many powerful language features.

It has continuations, which as I’ve mentioned provides a simpler and
more straightforward way of dealing with the kind of code that in
JavaScript we’d need to use callbacks, promises, or async/await for.

Along with continuations it has dynamic binding (in the form of
[parameters](https://docs.racket-lang.org/guide/parameterize.html)
which is a simple and straightforward way of passing information down
to executing code.

And for code which I can be most easily and naturally write in a
recursive form, Racket has proper tail calls, which means I can
*write* recursive code without blowing up the stack.

Arc has cool features such as being able to capture output to a string
(much less hassle than the usual string concatenation we have to do),
which are in turn built upon the features Racket provides.

Not to mention quick and easy macros (which Arc actually implements
itself without relying on Racket).

Going from Arc to a typical mainstream programming language without
these features feels like trying to jog through molasses.

Yet I don’t get to program in Arc very often.

Racket is its own isolated island, running on the server, with access
to Racket libraries.

JavaScript, while it doesn’t have the powerful language features I
wish I could use, runs in the browser.  It runs on [mobile
devices](http://cordova.apache.org/).  And on the
[server](https://nodejs.org/en/), and you can even run it [inside of a
database](https://pgxn.org/dist/plv8/doc/plv8.html) if you want to.

As well as having [475,000 libraries](https://www.npmjs.com/) available.

In terms of *getting things done*, I’m most often reaching for
JavaScript or another mainstream language.

Because they have the facilities available to get things done with.
Even if it may be slower and more cumbersome to get them done in a
mainstream language.

Interestingly, JavaScript is gaining a new facility called
[WebAssembly](http://webassembly.org/) which allows other languages
such as C to be compiled and run inside the JavaScript runtime
environment &mdash; at almost full native speed, and with access to
JavaScript libraries.

I hope to be able to compile Eternal to WebAssembly where we’ll be
able to use many of the powerful language features of Arc / Racket /
Scheme / Lisp, while also having full access to the many facilities
available in the JavaScript runtime environment.


## Building on Arc

*talk about syntax*

*not attempting to be backwards compatible*


## Concurrency

Suppose we write a mobile phone app, where do we save our data?  We
can store data just on the device, but most apps want to *communicate*
data between devices and often also to a server.

[Firebase](https://firebase.google.com/) is an interesting example
because data stored into Firebase on one device is automatically
replicated to other devices.

From the perspective of the app running on the device, it doesn’t
matter whether it updated a piece of data *itself* or whether it was
updated from *another* device also running the app.

[Meteor](https://www.meteor.com/) is another example where one device
can update a collection, and that update is automatically replicated
to other devices subscribed to that collection.

I could restrict Eternal so that a running instance of a program could
only access its *own* data, separate from any other running
instances... but that wouldn’t be as powerful.

If however we say that *different* running instances of a program can
access the *same* data, how do we keep these programs from stepping
all over each other and creating a big mess?


## Problems

Suppose I want to move two munchkins from the east room to the west room.

```
(++ west 2)
(-- east 2)
```

That’s pretty simple... but what if there’s *another*
instance of the program moving munchkins around at the same
time... say moving a munchkin from west to east?

```
(-- west 1)
(++ east 1)
```

* I look at the west room and see that there are 6 munchkins.
* The other instance looks and also sees that there are 6 munchkins.
* I write 8 (that is, 6 + 2) to the west room.
* The other instance writes 5 (that is, 6 - 1) to the west room.

ARGH!  The answer we were *supposed* to get was 7 (6 + 2 - 1), and
that’s the answer we *would* have gotten if the two pieces of code had
executed in *sequence*, but because they happen to run at exactly the
same time we got the wrong answer.

All I was trying to do was write a *simple* program to move munchkins
around and instead I’m neck deep in duplicated munchkins!

(Or if the other instance happened to write first, mysteriously
missing munchkins!)

And I can’t even *replicate* the bug!  The bug only happens if the two
pieces of code happen to run at *exactly* time.  Usually they won’t.
Usually one will manage to run before the other.  And then the program
will work fine.

So I test my program, it works fine, and then when my users are
running the app... it will rarely... and randomly... have a bug.

What a pain.


## Transactions

The core idea of a transaction is simple: *commit* the transaction and
only make our changes *if* the initial state that we *started* with at
the beginning of the transaction is still the *same*.  Otherwise,
retry.

* I look at the west room and see that there are 6 munchkins, and in
  the east room there are 5 munchkins.

* The other instance looks and also sees that there are 6 and 5.

* I write 8 (6 + 2) to the west room and 3 (5 - 2) to the east room,
  *if* there are *still* 6 and 5 currently... which there are, so my
  transaction succeeds and my writes go through.

* The other instance *would* writes 5 (6 - 1) to the west room and 6
  (5 + 1) to the east room, *if* there *were* still 6 and 5 currently,
  but there aren’t!  So that transaction *fails*, and nothing happens.

* The other instance gets to *retry*, so it tries again, and *this*
  time it sees *8* and *3*, so it writes 7 and 4... the right answers!

Or, the other instance might have finished first, in which case I
would have retried.  Either way it works.

```
(tx
  (++ west 2)
  (-- east 2))
```

We can’t *always* use transactions as a solution because if there’s a
lot of *contention* (if a lot of instances are trying to write to the
same data at the same time), the program will thrash and spend all its
time retrying transactions instead of making any progress.

And in those cases we need to figure out what else to do instead, such
as finding a way to divide up the data so that the same data doesn’t
need to be accessed so much.

But when we *can* use transactions they’re very convenient because
we’re able to write our code *as if* it were being run in series.

As if the code inside of transactions was being run one at a time.

Without possibility of one transaction messing up another one.

The code in transactions don’t *actually* run one at a time because
that would be horribly slow... and, if a transactions *were* only run
one a time, you’d never see a transaction retry.

If you take an *action* inside of a transaction, you’ll see that
action happening more than once if the transactions needs to retry.

```
(tx
  (prn "Moving munchkins!")
  (++ west 2)
  (-- east 2))
```

Usually you’ll see “Moving munchkins” printed only once because
the transaction doesn’t need to retry.  But you might see it
printed twice, or more, however many times the transaction might retry.

(If you see transactions retrying a *lot* then it’s time to make
adjustments, just as you would if you had some code that was running
too slowly).

We can use a special form to *delay* an action until the transaction
completes.

```
(tx
  (act (prn "Moving munchkins!"))
  (++ west 2)
  (-- east 2))
```

Now we’ll see “Moving munchkins” printed only *once*, after the
transaction completes, no matter how often it might have had to retry.


## Unnecessarily Mutated Data

Here’s a trick question, what does this JavaScript program print out?

```
for (var i = 0;  i < 4;  ++i) {
  setTimeout(
    function () {
      console.log(i);
    },
    100
  );
}
```

We might think this program would print out 0, 1,
2, 3.  In fact, it prints out 4, 4, 4, 4.  Why?

`setTimeout` schedules a function to run *later*, in this case 100
milliseconds later.  By *that* time, the `for` loop has already
*finished* (inside the loop we’re simply *scheduling* code to run
later), and by the time it’s finished `i` has been incremented to 4.

We have a *value* (0, 1, 2, or 3) that some code (the console.log) was
going to *use*, but it got changed by some *other* code (the for loop)
before it ran.

When we *modify* data (such as the variable `i`), we’re *throwing
away* the old value... and we need to think carefully about our
program to ensure that old value wasn’t being used anywhere.

An approach originally from functional programming languages,
popularized by Clojure, and now used in libraries such as React is to
work with *immutable* data structures, where we don’t throw away old
versions of data when we create new ones.

One solution here is that we could create a function closure to
remember the value of `i` (and not throw it away), so that it would be
available later.

```
for (var i = 0;  i < 4;  ++i) {
  (function (i) {
    setTimeout(
      function () {
        console.log(i);
      },
      100
    );
  })(i);
}
```

This problem is so common when programming with callbacks that
CoffeeScript has a special [`do`](http://coffeescript.org/#loops)
keyword just to create such a function closure for you.

```
for i in [0..3]
  do (i) ->
    setTimeout (-> console.log(i)), 100
```

A goal of Eternal is that while you, as the programmer, are free to
create as much mutable data as is convenient or useful to you, the
*language* works hard not to mutate things unnecessarily itself.

For example,

```
(for i 0 3
  (delay 100 (prn i)))
```

Here in Eternal, a *fresh* copy of `i` is provided to the loop body
each time through the loop, which means that later when `(prn i)`
executes it still has access to its own value of `i`.


## Communicating Changes

Transactions can be thought of a special case of wanting to be able to
*communicate* and coordinate changes (in this case to a database
storing data).

Another example would be our mobile app where we make a change on one
device and we’d like to see that change *communicated* to other
devices.

In both examples we’d like to keep the amount of data that we’re
changing (the mutable data) to a minimum, so that we’re not trying to
communicate or coordinate too much.

In a database transaction, updating 5 items is likely to be entirely
reasonable.  Updating 5,000 items in a transaction is likely to
overload most databases.

[Google Cloud Datastore](https://cloud.google.com/datastore/) is an
interesting example.  It offers fully serializable transactions, and
is a distributed database that can scale up for as much as you’d like
to pay for.

There’s a catch though: writes to any one object using transactions
are limited to one write per second for that particular object, and
you can’t have any more than *25* distinct objects participating in a
transaction.

Which is another reason why Eternal works hard to avoid mutating data
itself.

If the *language* is *automatically* saving data (and changes to
data), *and* it was mutating variables itself (such as updating the
index of a `for` loop)... that would be a *lot* of data to be stored,
communicated, and participating in transactions.

Now *25* might turn out to be too tough to deal with as a hard limit,
but even a more flexible database such as
[PostgreSQL](https://www.postgresql.org/) or
[CockroachDB](https://www.cockroachlabs.com/) would probably struggle
with the volume of data generated by a language where most of the data
was mutable and all that data was being automatically saved in a
transaction.

However, another key insight of functional programming and of
immutable data structures is that *most* data in a program doesn’t
need to be mutable.

Often, all we need is a *small* amount of mutable data *referring* to
a larger amount of immutable data.


## Immutable Data

Let’s say you have a blog publishing platform and you have a
user editing a 10,000 word blog post.

That’s a lot of data, and conventionally that data would go in a
database along with everything else.

However, suppose the user writes a first draft of their post, and
that’s version 1.  They make some edits, and that’s version 2.  They
make some more changes, and that’s version 3.  They publish their
post, and it’s version 3 that gets published.

Version 1 of the post, once created, never changes.  Neither does
version 2 or 3.  They’re a *snapshot* of the post.  Immutable.

The only part of the data here that *needs* to be mutable is to know
*which* version is the *current* version of the post, and whether it’s
been published or not.

```JavaScript
{
  version: 3,
  published: true
}
```

If we’re using a database for the *purpose* of handling transactions
for us, then the 10,000 words the user has written doesn’t need to go
in the database at all.

The *immutable* data can be stored anywhere convenient, copied,
shared, cached, without worrying about whether it might have gotten
out of date or become inconsistent... because the immutable part of
the data doesn’t change.

As another example, suppose we have some data stored in a function
closure,

```JavaScript
function (i) {
  ...
}
```

and, as it happens, that data is never modified (we never assign to
the variable `i`).

Which is quite common.  *Sometimes* you’ll see an assignment to a
function argument... but *often* it’s never modified.

A function closure is a data structure, and in a language which
automatically saves its program’s data it would be saved along with
everything else... but in the case where arguments aren’t modified
that closure is immutable.

Which means that you can have lots of function closures (and in a
running program you’ll usually a have a lot), but that doesn’t need to
put any burden on your transactional database.

Unless you specifically *want* to have a mutable variable (one that
you modify with an assignment statement), in which case it becomes
mutable data that participates in a transaction like any other mutable
data.
