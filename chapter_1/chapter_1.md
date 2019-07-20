---
fontfamily: libertinus
---

Notes on Abelson and Sussman, [*Structure and Interpretation of Computer Programs*](https://mitpress.mit.edu/books/structure-and-interpretation-computer-programs-second-edition)

# Chapter 1, "Building Abstractions with Procedures"

**Cameron Clarke**

July 2019

---

## 1. Building Abstractions with Procedures (pp. 1-106)

* Target of study: **computational processes** (1)

  * intuition: "Computational processes are abstract beings that inhabit computers" (1)

* Processes manipulate **data** (1)

* Evolution of a process is directed by a pattern of rules called a **program** (2)

* An upshot: "**People create programs to direct processes**" (2)

* Programs are composed from **programming languages** (2)

* A framing thought, among others (2-3)

  > Well-designed computational systems, like well-designed automobiles or nuclear reactors, are designed in a modular manner, so that parts can be constructed, replaced, and debugged separately.

##### Programming in Lisp

* "[...] our **procedural** thoughts will be expressed in Lisp" (3)

* Lisp **interpreter**: "a machine that carries out the processes described in the Lisp language" (3)

* Interesting historical note, about one of Lisp's original use cases (4)

  > Lisp [...] was designed to provide symbol-manipulating capabilities for attacking programming problems such as the symbolic differentiation and integration of algebraic expressions.

* A note about dialects of Lisp (4)

  > Lisp is by now a family of dialects, which, while sharing most of the original features, may differ from one another in significant ways

  * This book makes use of the [Scheme](https://groups.csail.mit.edu/mac/projects/scheme/) dialect of Lisp

* A main reason why Lisp is being used as the framework for the book's discussion of programming (5)

  > If Lisp is not a mainstream language, why are we using it as the framework for our discussion of programming? Because the language possesses unique features that make it an excellent medium for studying important programming constructs and data structures for relating them to the linguistic features that support them. **The most significant of these features is the fact that Lisp descriptions of processes, called _procedures_, can themselves be represented and manipulated as Lisp data**. The importance of this is that there are powerful program-design techniques that rely on the ability to **blur the traditional distinction between "passive" data and "active" processes**.

  * The above facts make Lisp good at writing programs that **manipulate other programs as data** (like interpreters and compilers)

### 1.1 The Elements of Programming (pp. 6-39)

* A powerful programming language can be used as: (6)

  * a means for instructing a computer to perform tasks
  * a framework within which we organize our ideas about processes

* Combining simple ideas into complex ideas (6)

  > [...] when we describe a language, we should **pay particular attention to the means that the language provides for combining simple ideas to form more complex ideas**.

  * Three mechanisms for doing this

    1. **Primitive expressions**, which represent the simplest entities the language is concerned with

    2. **Means of combination**, by which compound elements are built from simpler ones

    3. **Means of abstraction**, by which compound elements can be named and manipulated as units

* Two kinds of elements in programming: **procedures** and **data** (6)

  * Data is the "stuff" that we manipulate; procedures are the rules for manipulating the data

* An upshot: (6)

  > [...] any powerful programming language should be able to describe primitive data and primitive procedures and should have methods for combining and abstracting procedures and data.

#### 1.1.1 Expressions (pp. 7-10)

* An initial framing (7)

  > Imagine that you are sitting at a computer terminal. You type an _expression_, and the interpreter responds by displaying the result of its _evaluating_ that expression.

* Some vocab (8)

  * **combinations**: expressions formed by delimiting a list of expressions within parentheses, in order to denote procedure application

  * **operator**: leftmost element in the list

  * **operand**: other elements in the list than the operator

  * **argument**: "The value of a combination is obtained by applying the procedure specified by the operator to the _arguments_ that are the values of the operands"

* Scheme uses **prefix notation**: operator to the left of the operands (8)

  * Advantages of prefix notation (8-9)

    * can accommodate procedures that may take an arbitrary number of arguments

      * e.g., `(+ 21 35 12 7 5 6)`, which evaluates to `86`

    * extends in a straightforward way to allow combinations to be _nested_, i.e., combinations themselves can have combinations as elements

      * e.g., `(+ (* 3 5) (- 10 6))`, which evaluates to `19`

      * might think to use a _pretty-printing_ convention—where each long combination is written so that the operands are aligned vertically—if you have lots of nested things, like
        ```scheme
        (+ (* 3
              (+ (* 2 4)
                 (+ 3 5)))
           (+ (- 10 7)
              6))
        ```
        instead of
        ```scheme
        (+ (* 3 (+ (* 2 4) (+ 3 5))) (+ (- 10 7) 6))
        ```

* Scheme interpreter runs in a **REPL** (_read-eval-print loop_)

  * Interesting note here (10)

    > Observe in particular that it is not necessary to explicitly instruct the interpreter to print the value of the expression.



#### 1.1.2 Naming and the Environment (pp. 10-12)

* **names** to refer to computation objects (10)

  > We say that the name identifies a _variable_ whose _value_ is the object.

* The Scheme dialect of Lisp uses `define` to name things (10)

  * e.g.,
  ```scheme
  (define size 2)
  ```

* `define` is Scheme's **simplest means of abstraction**, since it lets us to use simple names to refer to the results of compound operations (11)

* An upshot (11)

  > [...] complex programs are constructed by building, step by step, computational objects of increasing complexity.

* In order to associate values with symbols, need to have some sort of memory that keeps track of the name object pairs (11)

  * This memory is called the **environment** (more precisely, the **global environment** in this specific case)

#### 1.1.3 Evaluating Combinations (pp. 12-15)

* The interpreter itself is following a **procedure** when it evaluates combinations (12)

  > To evaluate a combination, do the following:
  > 1. Evaluate the subexpression of the combination.
  > 2. Apply the procedure that is the value of the leftmost subexpression (the operator) to the arguments that are the values of the other subexpressions (the operands).

* Important points about processes in general, that are illustrated by the above example (12-14)

  * The evaluation rule is **recursive**: the first step above tells us to _recursively_ evaluate the evaluation process on each element of a given combination (12)

    * Can think of this recursive evaluation process as a process of **tree accumulation** (where the tree structure is implied by the nested structure of the combinations)—the **values of the operands "percolate upward"**

  * repeated application of the first step in the evaluation rule brings us to the point where we need to evaluate **primitive expressions** (14)

    * examples of primitive expressions are numerals, built-in operators, and other names

    * Rules for handling primitive cases (14)

      > We take care of the primitive cases by stipulating that
      > * the values of numerals are the numbers that they name,
      > * the values of built-in operators are the machine instruction sequences that carry out the corresponding operations, and
      > * the values of other names are the objects associate with those names in the environment.

    * A note about the relationship between meaning of symbols in expressions and the _environment_ (14)

      > We may regard the second rule [from the rules for handling primitive cases, above] as a special case of the third one by stipulating that symbols such as `+` and `*` are also included in the global environment, and are associated with the sequences of machine instructions that are their "values." **The key point to notice is the role of the environment in determining the meaning of the symbols in expressions**. In an interactive programming language such as Lisp, it is meaningless to speak of the value of an expression such as `(+ x 1)` without specifying any information about the environment that would provide a meaning for the symbol `x` (or even for the symbol `+`).

  * The evaluation rule does not handle definitions (14)

    * Corollary: `(define x 3)`, for example, is not a combination

* Exceptions to the general evaluation rule are called **special forms** (14)

  * Each special form has its own evaluation rule

* Note about **syntax** of the programming language (14-5)

  > The various kinds of expressions (each with its associated evaluation rule) constitute the syntax of the programming language.

* Lisp's syntax is simple relative to other programming languages' syntaxes (15)

  > [In Lisp], the evaluation rule for expressions can be described by as simple general rule together with specialized rules for a small number of special forms.

* A note here also about ***syntactic sugar*** (15n11)

  > Special syntactic forms that are simply convenient alternative surface structures for things that can be written in more uniform ways are sometimes called _syntactic sugar_, to use a phrase coined by Peter Landin.


#### 1.1.4 Compound Procedures (pp. 15-18)

* Elements that Lisp has, that must appear in any "powerful" programming language (15)

  > * Numbers and arithmetic operations are primitive data and procedures
  >
  > * Nesting of combinations provides a means of combining operations
  >
  > * Definitions that associate names with values provide a limited means of abstraction

* This section focuses on **procedure definitions**, which are... (15)

  > [...] a much more powerful abstraction technique by which a **compound operation can be given a name and then referred to as a unit**.

* Example: the "squaring" operation (16)

  ```scheme
  (define (square x) (* x x))
  ```

  * Could read/understand this as: "To (`define`) square (`square`) something (`x`), multiply (`*`) it (`x`) by itself (`x`)"

    * Interesting description (16)

      > The thing to be multiplied is given a local name, `x`, which plays the same role that a pronoun plays in natural language.

  * This is an example of a **compound procedure** (16)

    > Evaluating the definition creates this compound procedure and associates it with the name `square`.

* General form of a procedure definition (16-7)

  ```
  (define ([name] [formal parameters]) [body])
  ```

  * `[name]` is a symbol to be associated with the procedure definition in the environment

  * `[formal parameters]` are the names used within the body of the procedure to refer to the corresponding arguments of the procedure

  * `[body]` is an expression that will yield the value of the procedure application when the formal parameters are replaced by the actual arguments to which the procedure is applied

    * `[body]` could also include names of procedures that have already been defined—e.g., we could use `square`, which we defined above, as a building block to define a `sum-of-squares` procedure (this example is given in the book)

    * In general, `[body]` can be a sequence of expressions (17n14)

#### 1.1.5 The Substitution Model for Procedure Application (pp. 18-22)

* Evaluating a combination whose operator names a _compound_ procedure is similar to evaluating a combination whose operator is a _primitive_ procedure (18)

  > That is, the interpreter evaluates the elements of the combination and applies the procedure (which is the value of the operator of the combination) to the arguments (which are the values of the operands of the combination).

* **Application process for compound procedures** (18)

  > To apply a compound procedure to arguments, evaluate the body of the procedure with each formal parameter replaced by the corresponding argument.

  * (We assume that the mechanism for applying primitive procedures to arguments is built into the interpreter)


* **substitution model** for procedure application (18-9)

  * An overview from the example given in the book (19)

    > Evaluating this combination [the one in the example in the book] involves three subproblems. We must evaluate the operator to get the procedure to be applied, and we must evaluate the operands to get the arguments.

  * The substitution model can be thought of as a model that determines the "meaning" of procedure application—but it's **not the whole (or accurate) story**; it's just a helpful way to think about procedure application (19)

    * **Two points** to stress here (19-20)

      1. Typical interpreters do not actually evaluate procedure applications by manipulating the text of a procedure to substitute values for formal parameters; in practice this happens using a **local environment** for the formal parameters

      2. The substitution model is relatively simple, but it doesn't correctly model all that we'll eventually need to model; in particular, the substitution model breaks down when we try to use procedures with "mutable data" (this will be addressed in Chapter 3)

##### Applicative order versus normal order (20-22)

* The intuition behind **normal-order evaluation**: **"fully expand and then reduce"** (21)

  * i.e., first substitute operand expressions for parameters until there are only primitive operators, and then perform evaluation

* This stands in contrast to the **applicative-order evaluation**, which says: **"evaluate the arguments and then apply"** (21)

  * This is the kind of evaluation we've discussed in previous sections

* **Lisp uses applicative-order evaluation** (21)

* A claim about an equivalence result for normal-order evaluation and applicative-order evaluation, under certain conditions (21)

  > It can be shown that, for procedure applications that can be modeled using substitution (including all the procedures in the first two chapters of this book) and that yield legitimate values, **normal-order and applicative-order evaluation produce the same value**.

* Reasons why Lisp uses applicative-order evaluation (21-2)

  * Partly because of the additional efficiency obtained from avoiding multiple evaluations of expressions

  * More significantly—normal-order evaluation becomes much more complicated to deal with when considering procedures that can't be modeled using substitution

  * (Even given these reasons, normal-order evaluation can be a valuable tool. More on this in Chapters 3 and 4.)

#### 1.1.6 Conditional Expressions and Predicates (pp. 22-27)

* The `cond` construct (short for "conditional") is used for doing **case analysis** in Lisp (22)

  * e.g.,

    ```scheme
    (define (abs x)
      (cond ((> x 0) x)
            ((= x 0) 0)
            ((< x 0) (- x))))
    ```

  * general form:

    ```scheme
    (cond ([p_1] [e_1])
          ([p_2] [e_2])
          ([p_3] [e_3])
          ...
          ([p_n] [e_n]))
    ```

  * each of the `([p_i] [e_i])` are called **clauses**; the `[p_i]` are **predicates**, and the `[e_i]` are **consequent expressions** (22)

  * In conditional expressions, the interpreter (informally put) "goes until it finds a predicate whose value is true"; at this point the interpreter returns the value of the corresponding consequent expression as the value of the conditional expression overall

    * In Lisp, the constant `#t` denotes "true" and the constant `#f` denotes "false", in a sense (23n17)

  * If none of the `[p_i]` is found to be true, the value of the `cond` is undefined

* The `else` symbol can be used in place of the predicate in the final clause of `cond` (24)

  * Example: could've also defined `abs` above as

    ```scheme
    (define (abs x)
      (cond ((< x 0) (- x))
            (else x)))
    ```

  * In fact, can use any expression that always evaluates to a true value like `else` here, in place of the final predicate

* Can also use `if`, an example of a **special form**, to define the absolute-value procedure (24)

  ```scheme
  (define (abs x)
    (if (< x 0)
        (- x)
        x))
  ```

  * In general: (24)

    ```
    (if [predicate] [consequent] [alternative])
    ```

* There are **logical composition operations**, which allow us to construct **compound predicates** (24-5)

  * `and`: `(and [e_1] ... [e_n])`

    > The interpreter evaluates the expressions [`[e]`] one at a time, in left-to-right order. **If any [`[e]`] evaluates to false, the value of the `and` expression is false and the rest of the [`[e]`]'s** [sic] **are not evaluated. If all [`[e]`]'s** [sic] **evaluate to true values, the value of the end expression is the value of the last one.**

  * `or`: `(or [e_1] ... [e_n])`

    > The interpreter evaluates the expressions [`[e]`] one at a time, in left-to-right order. **If any [`[e]`] evaluates to a true value, that value is returned as the value of the `or` expression, and the rest of the [`[e]`]'s** [sic] **are not evaluated. If all [`[e]`]'s** [sic] **evaluate to false, the value of the `or` expression is false.**

  * `not`: `(not [e])`

    > The value of a `not` expression is true when the expression [`[e]`] evaluates to false, and false otherwise.

* Important: **`and` and `or` are special forms, not procedures** (25)

  * This is because the subexpressions are not necessarily all evaluated with `and` and `or`

  *  (`not` is an ordinary procedure, though)

**Exercises** (26-8)

* Exercise 1.1

    1. ```scheme
       10
       ```

       prints

       ```scheme
       ;Value: 10
       ```

    2. ```scheme
       (+ 5 3 4)
       ```

       prints

       ```scheme
       ;Value: 12
       ```

    3. ```scheme
       (- 9 1)
       ```

       prints

       ```scheme
       ;Value: 8
       ```

    4. ```scheme
       (/ 6 2)
       ```

       prints

       ```scheme
       ;Value: 3
       ```

    5. ```scheme
       (+ (* 2 4) (- 4 6))
       ```

       prints

       ```scheme
       ;Value: 6
       ```

    6. ```scheme
        (define a 3)
       ```

       prints

       ```scheme
       ;Value: a
       ```

    7. ```scheme
       (define b (+ a 1))
       ```

       prints

       ```scheme
       ;Value: b
       ```

    8. ```scheme
       (+ a b (* a b))
       ```

       prints

       ```scheme
       ;Value: 19
       ```

    9. ```scheme
       (= a b)
       ```

       prints

       ```scheme
       ;Value: #f
       ```

    10. ```scheme
        (if (and (> b a) (< b (* a b)))
            b
            a)
        ```

        prints

        ```scheme
        ;Value: 4
        ```

    11. ```scheme
        (cond ((= a 4) 6)
              ((= b 4) (+ 6 7 a))
              (else 25))
        ```

        prints

        ```scheme
        ;Value: 16
        ```

    12. ```scheme
        (+ 2 (if (> b a) b a))
        ```

        prints

        ```scheme
        ;Value: 6
        ```

    13. ```scheme
        (* (cond ((> a b) a)
                 ((< a b) b)
                 (else -1))
           (+ a 1))
        ```

        prints

        ```scheme
        ;Value: 16
        ```

* Exercise 1.2

  * ```scheme
    (/ (+ 5
          4
          (- 2
             (- 3
                (+ 6
                   (/ 4 5)))))
       (* 3
          (- 6 2)
          (- 2 7)))
    ```

* Exercise 1.3

  * Making use of the `square` and `sum-of-squares` procedures defined earlier in the chapter:

    ```scheme
    (define (ssq-largest-two-cond a b c)
            (cond ((and (> a b) (> b c)) (sum-of-squares a b))
                  ((and (> a b) (> c b)) (sum-of-squares a c))
                  ((> a c)               (sum-of-squares a b)) ;implicitly, (> b a)
                  (else                  (sum-of-squares b c))))
    ```

    Could also implement this using `if`:

    ```scheme
    (define (ssq-largest-two-if a b c)
            (if (> a b)
                (if (> b c)
                    (sum-of-squares a b)
                    (sum-of-squares a c))
                (if (> a c)
                    (sum-of-squares a b)
                    (sum-of-squares b c))))
    ```

* Exercise 1.4

  * [TODO]

* Exercise 1.5

  * [TODO]

#### 1.1.7 Example: Square Roots by Newton's Method (pp. 28-33)

* [TODO]

#### 1.1.8 Procedures as Black-Box Abstractions (pp. 33-39)

* [TODO]



### 1.2 Procedures and the Processes They Generate (pp. 40-74)

### 1.3 Formulating Abstractions with Higher-Order Procedures (pp. 74-106)
