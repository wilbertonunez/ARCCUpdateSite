# Installing ARCC

To install the plugin, start by cloning this repository. Open your Eclipse installation, and go to *Help -> Install New Software -> Add -> Local*. Select the cloned repository and press *OK*. If nothing shows, uncheck the *Group by items category* option, and you should be able to see the plugin listed. Select it for installation, and proceed normally.

Once installed, go to *Window -> Show View -> Other -> Strategies*. Select the *Strategies* element, and press *OK*. You should now be able to see the *Strategies* window within your Eclipse configuration.

Import the project containing the sample classes, available at https://github.com/victorjmarin/ARCCExamples, into an **empty workspace**. 

#### Creating strategies

To create a strategy, right click on a method's signature from the *Outline* window, and select *Create Strategy*. The patterns in the knowledge-base will appear alongside the number of times they ocurr in descending order. Name the strategy, indicate the instances of every pattern involved in it as well as their highlighting color, and press *OK*. Your recently created strategy should be available for use now.

#### Searching strategies

Press *Ctrl + H* to perform a search, and go to the *Strategy Search* tab. Select one option from the dropdown, and click *Search*. Results will appear in the *Search* window. Double click twice one of the method signatures in the results to highlight the code according to the patterns matched. The *Strategy* window will display the color corresponding to every pattern and their interleaving. It is possible to deselect patterns and click on the method's signature again to re-highlight accordingly.
