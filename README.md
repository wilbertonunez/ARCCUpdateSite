## ARCC: Assistant for Repetitive Code Comprehension

As software projects evolve, carefully understanding the behavior
of a program is mandatory before making any change. In addition, repetitive code snippets tend to appear throughout the codebase, and developers have to understand similar semantics multiple times. Building on this observation, we present ARCC: an Assistant for Repetitive Code Comprehension. Our tool, implemented
as an Eclipse plugin, assists developers leverage knowledge of a
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


For a showcase of ARCC, please watch https://www.youtube.com/watch?v=5LAK7en2Sr8&list=PLmizZtBESdPHDyKXKHMXj13r2pBCKzIoA.
