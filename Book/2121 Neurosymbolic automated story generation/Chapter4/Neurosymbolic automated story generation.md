Link
===============
<p>

https://smartech.gatech.edu/handle/1853/64643

</p>


Notes
===============

1. I model story generation as a planning problem: find a sequence of events that transitions the state of the world
   into one in which the desired goal holds
2. I split sentences into multiple events, where possible, which creates a potential one-tomany relationship between an
   original sentence and the event(s) produced from it.
3. Results are summarized in Table 5. Only 22.47% of the stories in the testing set, on average, end in my desired
   goals. The DRL-clustered model generated the given goals on average 93.82% of the time, compared to 37.72% on average
   for the baseline Seq2Seq and 19.935% for the DRL-unrestricted model.
4. Next Steps: Improving Sentence Generation and Addressing Global Coherence
5. Independently-trained sequence-to-sequence models have a hard time “understanding” each other. When output from the
   event-to-event model is often unlike anything the event-to-sentence model was trained on, and so the
   event-to-sentence model subsequently spits out unintelligible, irrelevant sentences. On the other hand, if it is
   something the event-to-sentence model has seen before, the output often ends up being boring and predictable.

Thoughts with Additional Information
===============

1.

Summary
===============
This chapter author mentions a deep reinforcement learning to further address the local consistency issue. 
