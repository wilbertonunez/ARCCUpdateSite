## ARCC: Assistant for Repetitive Code Comprehension

As software projects evolve, carefully understanding the behavior
of a program is mandatory before making any change. In addition, repetitive code snippets tend to appear throughout the codebase, and developers have to understand similar semantics multiple times. Building on this observation, we present ARCC: an Assistant for Repetitive Code Comprehension. Our tool, implemented
as an Eclipse plugin, helps developers leverage knowledge of a
program to understand other programs containing a subset of the
semantics in the former. ARCC differs from existing approaches in
that it uses an extensible knowledge base of recurrent semantic
code snippets–instead of heuristics or salient features–to summarize the behavior of a program. Given a program, we detect the occurrences of such snippets. Then, developers can create strategies as combinations of the snippets found, and perform a search for all the occurrences of a strategy in their workspace. The source
code relating to every snippet–aswell as their interleaving–is highlighted with a description associated, allowing developers to get an intuition of similar programs at a glance. ARCC also underlines potential common errors associated with the snippets, assisting in detecting overlooked problems.

##

### Installing ARCC

To install the plugin, start by cloning this repository. Open your Eclipse installation, and go to *Help -> Install New Software -> Add -> Local*. Select the cloned repository and press *OK*. If nothing shows, uncheck the *Group by items category* option, and you should be able to see the plugin listed. Select it for installation, and proceed normally.

Once installed, go to *Window -> Show View -> Other -> Strategies*. Select the *Strategies* element, and press *OK*. You should now be able to see the *Strategies* window within your Eclipse configuration.

Import the project containing the sample classes, available at https://github.com/victorjmarin/ARCCExamples, into an **empty workspace**. 

### Creating strategies

To create a strategy, right click on a method's signature from the *Outline* window, and select *Create Strategy*. The patterns in the knowledge-base will appear alongside the number of times they ocurr in descending order. Name the strategy, indicate the instances of every pattern involved in it as well as their highlighting color, and press *OK*. Your recently created strategy should be available for use now.

### Searching strategies

Press *Ctrl + H* to perform a search, and go to the *Strategy Search* tab. Select one option from the dropdown, and click *Search*. Results will appear in the *Search* window. Double click twice one of the method signatures in the results to highlight the code according to the patterns matched. The *Strategy* window will display the color corresponding to every pattern and their interleaving. It is possible to deselect patterns and click on the method's signature again to re-highlight accordingly.


For a showcase of ARCC, please watch our videos [here](https://www.youtube.com/watch?v=5LAK7en2Sr8&list=PLmizZtBESdPHDyKXKHMXj13r2pBCKzIoA).

### Creating new patterns

Creating new patterns in ARCC requires some expertise with using the tool, in addition to some *basic* knowledge on how the program dependence graphs are generated to search for patterns and strategies. To see how the program dependence graph is created, check Section 3 (Under-the-hood technique) in our FSE paper.

A pattern is a graph. More specifically, a pattern is a set of vertices and edges representing a small set of statements (or approximate statements) and with dependencies between them. A pattern does not represent a whole program or procedure like the program dependence graph does.

To create a new pattern, we first manually design a graph from a small set of target statements and then save this graph using a particular XML notation.

Let us consider the following snippet:
```java
	// ... some code
	int counter = 0;
	//... more code

    for (int i = 0; i <= m; i++) {        
        
        if (m < getValue(i, true)){
        	counter++;
        	//... more more code
        }
        
    }
```

In this piece of code we are incrementing the value of an integer variable inside a loop, provided a certain condition is met. We can create a pattern from this if we are interested in generating strategies that include this pattern. Each one of the statements will be a vertex in our pattern:

1. `counter = 0`  -> *Assign*
2. `i = 0`        -> *Assign*
3. `i <= m`       -> *Cond*
4. `i++`          -> *Assign*
5. `m < getValue(i, true)` -> *Cond*
6. `counter++`     -> *Assign*

Notice we do not care about the variable types in assignments or declarations. Also, notice that in those statements that can have two vertex types, only the main type is considered. For instance, statement 5 contains a comparison (*Cond*) and a function call (*Call*) and we pick *Cond*.

Now we can draw depencency edges between vertices. Recall that edges can be of type *Data*, to represent a data dependency between statements, or type *Ctrl* to represent control dependencies. Here, *Data* and *Ctrl* edges are represented with solid and dashed lines respectively:

----

This is a complete valid pattern, however, we may want to loosen it up so that it can match with more variations of the given code snippet, hence making the pattern more reusable. Assume that instead of considering only increments by 1 in statement (6), we want the pattern to match statements with any increment. Thus, we can change the regular expression in vertex (6) to `counter+` so that it matches with `counter++` or `counter+=`.

We may also remove vertices and/or edges from a pattern to make it more flexible, or remove the expression inside the vertex if we only care about the vertex type. In this example, we will remove the expression in vertex (3) and will get rid of vertices (2) and (4) so that the pattern can match with any type of loop (`for/while`) with any kind of loop condition, as long as the code is indeed iteratively incrementing a variable under a certain if condition:

![Alt text](/figures/CondCumulativelyAdd.PNG?raw=true "Pattern: Conditional cumulatively adding")

Another common way to make a pattern more flexible and reusable is to remove the type from an *Assign* vertex (making it *Untyped*). That way the vertex will be able to match with either an assinment statement or a declaration. This is a useful strategy if we know that the variable being assigned in the reference code snippet may actually be a method parameter in another version of the code. Method parameters are basically declaration statements (type *Decl*) in our graph structure. We don't need to use this tactic in this example.

Now that we have designed our pattern, we write it in XML form. This is pretty straightforward:

```xml
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<xmlPatternGraph>
    <description>Conditional cumulatively adding</description>
    <edges>
        <from>1</from>
        <to>6</to>
        <type>DATA</type>
    </edges>
    <edges>
        <from>5</from>
        <to>6</to>
        <type>CTRL</type>
    </edges>
    <edges>
        <from>3</from>
        <to>5</to>
        <type>CTRL</type>
    </edges>
    <edges>
        <from>3</from>
        <to>3</to>
        <type>CTRL</type>
    </edges>
    <missingFeedback>consider using a loop and a condition, recall to add and assign the same variable</missingFeedback>
    <type>COND_CUMULATIVELY_ADD</type>
    <vertices>
        <assignedVariable>:c</assignedVariable>
        <id>1</id>
        <label>:c=0</label>
        <length>0</length>
        <offset>0</offset>
        <type>ASSIGN</type>
        <approxLabel>:c=\d+</approxLabel>
    </vertices>
    <vertices>
        <assignedVariable>:c</assignedVariable>
        <id>6</id>
        <label>:c\+</label>
        <length>0</length>
        <offset>0</offset>
        <readingVariables>:c</readingVariables>
        <type>ASSIGN</type>
    </vertices>
    <vertices>
        <id>5</id>
        <label></label>
        <length>0</length>
        <offset>0</offset>
        <type>CTRL</type>
    </vertices>
    <vertices>
        <id>3</id>
        <label></label>
        <length>0</length>
        <offset>0</offset>
        <type>CTRL</type>
    </vertices>
</xmlPatternGraph>
```

(More to come...)
