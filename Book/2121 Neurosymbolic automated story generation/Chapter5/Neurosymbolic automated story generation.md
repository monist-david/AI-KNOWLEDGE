Link
===============
<p>

https://smartech.gatech.edu/handle/1853/64643

</p>


Notes
===============

1. For neural-based approaches to story and plot generation, a neural language model is trained on a corpus of stories
   to predict the next character, word, or sentence in a sequence based on a history of tokens. The advantage of
   neural-based approaches is that there is no need for explicit domain modeling beyond providing a corpus of example
   stories. The primary pitfall of neural language model approaches for story generation is that the space of stories
   that can be generated is huge, which in turn, implies that, in a textual story corpora, any given sentence will
   likely only be seen once.
2. I can reduce the sparsity in a dataset that comes from an abundance of unique sentences [58]. The event
   representation enables the decomposition of the plot generation task into two sub-problems: event-to-event (Section
   3.3) and eventto-sentence (Section 3.5). The event-to-sentence problem can be thought of as a translation
   task—translating from the language of events into natural language. I found [58], however, that independently-trained
   sequence-to-sequence LSTM networks [70] frequently ignore the input event and only generate text based on the
   original corpus, overwriting the plot-based decisions made during event-to-event.
    1. Firstly, event-to-event models tend to produce previously-unseen events, which, when fed into the
       event-to-sentence model result in unpredictable behavior. A basic sequence-to-sequence model is unable to learn
       how to map these unseen events to sentences
    2. Secondly, sentences are often only seen once in the entire corpus. Despite the conversion into events, the
       sparsity of the data means that each event is still likely seen a limited number of times. For these reasons, I
       framed the event-to-sentence task as guided language generation, using a generated event as a guide.
3. I thus look for other forms of guided neural language generation, with the goals of preserving the semantic meaning
   from the event in addition to keeping the generated sentences interesting.
4. In order, the models are: (1) a retrieve-and-edit model based on Hashimoto et al. [98]; (2) template filling; (3)
   sequence-to-sequence with Monte Carlo beam decoding;
   (4) sequence-to-sequence with a finite state machine decoder; and (5) vanilla (beamdecoding) sequence-to-sequence. I
   find that none of these models by themselves can successfully find a balance between the goals of retaining all of
   the event tokens and 63 generating interesting output. However, each of the models possess their own strengths and
   weaknesses—each model is essentially optimized towards a different point on the spectrum between the two goals. I
   combine these models into an ensemble in an attempt to minimize the weaknesses of each individual model and to
   achieve a balance.
5. In order to generate a sentence from a given input event, there are two key phases:
   “retrieve” phase and “edit” phase. With respect to the input event, I first retrieve the 64 nearest-neighbor event
   and its corresponding sentence in the training set using the retriever model. Passing both the retrieved
   event-sentence pair and the input event as inputs, I use the editor model to generate a sentence using beam search.

Thoughts with Additional Information
===============


Summary
===============
This chapter, author provides a 5 in 1 method on generating the sentence based on the given event in order to solve 
the issue of neural network generate sentence without using the event information. 