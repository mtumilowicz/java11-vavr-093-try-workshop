[![Build Status](https://travis-ci.com/mtumilowicz/java11-vavr093-try-workshop.svg?branch=master)](https://travis-ci.com/mtumilowicz/java11-vavr093-try-workshop)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

# java11-vavr093-try-workshop

# project description
* https://www.vavr.io/vavr-docs/#_try
* https://static.javadoc.io/io.vavr/vavr/0.9.3/io/vavr/control/Try.html
* https://github.com/mtumilowicz/java11-vavr-try
* on the workshop we will try to fix failing `Workshop`
* answers: `Answers` (same tests as in `Workshop` but correctly solved)

# theory in a nutshell
* `Try` models a computation that may either result in an exception (`Throwable`), or return a successfully 
computed value
* you can think about `Try` as a pair `(Failure, Success) ~ (Throwable, Object)` that has either left or right value
* you can think about `Try` as an object representation of try-catch-finally
* `interface Try<T> extends Value<T>, Serializable`
    * `interface Value<T> extends Iterable<T>`
* two implementations:
    * `final class Success<T>`
    * `final class Failure<T>`

# conclusions in a nutshell
* conversion `Option<T>` <-> `Try<T>`
    * `try.toOption()`
    * `option.toTry()`
        * `None` -> `Failure(NoSuchElementException)`
* conversion: `Try<T>` -> `Either<T>`
* wrapping computations
    ```
    static <T> Try<T> of(CheckedFunction0<? extends T> supplier) {
        Objects.requireNonNull(supplier, "supplier is null");
        try {
            return new Success<>(supplier.apply());
        } catch (Throwable t) {
            return new Failure<>(t);
        }
    }
    ```
    * note that we could also wrap computations that throws check exception (argument type - `CheckedFunction0`)
* conversion: `List<Try<T>>` -> `Try<List<T>>`
    * `Try.sequence(list)`
    * If any of the `Try` is `Failure`, returns first `Failure`
* performing side-effects
    * on success: 
        * `Try<T> andThen(Runnable runnable)`
        * `onSuccess(Consumer<? super T> action)`
    * on failure: `Try<T> onFailure(Consumer<? super Throwable> action)`
* mapping with partial function
    * `Try<R> collect(PartialFunction<? super T, ? extends R> partialFunction)`
    * if function is not defined at a value - returns `Failure(NoSuchElementException)`
* we could filter value (same as `Option`)
* lazy alternative
    * `Try<T> orElse(Supplier<? extends Try<? extends T>> supplier)`
* recovery from specific exceptions:
    * `Try<T> recover(Class<X> exception, Function<? super X, ? extends T> f)`
    * `Try<T> recoverWith(Class<X> exception, Function<? super X, Try<? extends T>> f)`
* finally block
    * `Try<T> andFinally(Runnable runnable)`
* try with resources
    * `Try.withResources(CheckedFunction0<? extends T1> t1Supplier)`
* mapping exception
    * `Try<T> mapFailure(Match.Case<? extends Throwable, ? extends Throwable>... cases)`