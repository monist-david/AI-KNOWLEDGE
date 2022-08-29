Link
===============
<p>

https://smartech.gatech.edu/handle/1853/64643

</p>


Notes
===============

1. automated story generation is the problem of creating a sequence of main plot points for a story in a given domain
   and with a set of specifications.
2. closed-world domains:
    1. a virtual world, game, or simulation environment constrained by the set of characters, objects, places, and the
       actions that can be legally performed in a given state.
3. Open-world storytelling
    1. the generation of stories that can theoretically tell a story about any domain—with the action space being
       anything that can expressed through natural language.
4. The methods for open-world story generation, like much of artificial intelligence, can generally be divided into two
   categories: symbolic/discrete or neural/probabilistic.
5. The perceived coherence of stories produced by neural-based automated story generation systems can be improved by
   incorporating symbolic approaches—such as schemas, goals, and causal reasoning.
6. This brings me to my definition of coherence: it is the measure of the local and global consistency of textual
   segments along with the causal/logical ordering of sentences.This brings me to my definition of coherence: it is the
   measure of the local and global consistency of textual segments along with the causal/logical ordering of sentences.
7. measuring the semantic relatedness between sentences (local consistency) [10]–[13] and sustaining meaning & topic
   modeling (global consistency)
8. causal relations— when human readers model the relationships between story events—for coherence. In narrative, a
   causal relation between two events is an indication that the temporally latter event is made possible in part by the
   presence of the temporally former event. This can be due to direct cause—a wheel falling off a car causes it to
   crash—or through enablement— the car could not have crashed if the ignition had not been turned on.
9. The narrative causality just described differs from the causality as used in statistics or Bayesian inference, where
   causality between random variables means that the value of one random variable affects the distribution of values
   from another random variable. This Bayesian causality limits neural story generators to solely temporal orderings
   without a guarantee of narrative causal relations.
10. This brings me to my definition of coherence: it is the measure of the local and global consistency of textual
    segments along with the causal/logical ordering of sentences.

Thoughts with Additional Information
===============

1. telling story is definitely interesting. People love stories. However, stories themselves don't have the interaction
   ability. What should we do?
2. Why is coherence, the author emphasized in the book?
3. For building a model that have full capability of causal relations. It is important for a model to understand how
   certain objects are influenced by others, under certain rules, under certain environments.

Summary
===============
Chapter 1 mainly talks about author's understanding of the current story generation. Mainly on its low consistency. Not
only on the story generation task, the conversational AI is also facing the similar issue, among with a lot other NLP
task. Author defined what is coherence, which includes three components: local consistency, global consistency with
causal/logical ordering of sentences. 