Link
===============
<p>

https://jalammar.github.io/illustrated-transformer/

http://nlp.seas.harvard.edu/2018/04/03/attention.html

</p>


Notes
===============

1. The biggest benefit, however, comes from how The Transformer lends itself to parallelization.
2. here is the architecture of the transformer model in the paper <Attention is all you need>:
   ![img.png](img.png)
   The encoders are all identical in structure (yet they do not share weights). Each one is broken down into two
   sub-layers:
   ![img_1.png](img_1.png)
   The decoder has both those layers, but between them is an attention layer that helps the decoder focus on relevant
   parts of the input sentence (similar what attention does in seq2seq models).
   ![img_2.png](img_2.png)
3. As is the case in NLP applications in general, we begin by turning each input word into a vector using an embedding
   algorithm. The embedding only happens in the bottom-most encoder. The abstraction that is common to all the encoders
   is that they receive a list of vectors each of the size 512 – In the bottom encoder that would be the word
   embeddings, but in other encoders, it would be the output of the encoder that’s directly below. The size of this list
   is hyperparameter we can set – basically it would be the length of the longest sentence in our training dataset.
   After embedding the words in our input sequence, each of them flows through each of the two layers of the encoder.
   and we see that the word in each position flows through its own path in the decoder. In other word, there are
   dependencies between these paths in the self-attention layer. The feed-forward layer does not have those
   dependencies, however, and thus the various paths can be executed in parallel while flowing through the feed-forward
   layer.
   ![img_3.png](img_3.png)
4. Self-attention is the method the Transformer uses to bake the “understanding” of other relevant words into the one
   we’re currently processing.
   ![img_4.png](img_4.png)
5. How to calculate self-attention using vectors.
    1. The first step in calculating self-attention is to create three vectors from each of the encoder’s input
       vectors (in this case, the embedding of each word). So for each word, we create a Query vector, a Key vector, and
       a Value vector. These vectors are created by multiplying the embedding by three matrices that we trained during
       the training process. Notice that these new vectors are smaller in dimension than the embedding vector. Their
       dimensionality is 64, while the embedding and encoder input/output vectors have dimensionality of 512. They don’t
       HAVE to be smaller, this is an architecture choice to make the computation of multiheaded attention (mostly)
       constant.
       ![img_5.png](img_5.png)
    2. The second step in calculating self-attention is to calculate a score. Say we’re calculating the self-attention
       for the first word in this example, “Thinking”. We need to score each word of the input sentence against this
       word. The score determines how much focus to place on other parts of the input sentence as we encode a word at a
       certain position. The score is calculated by taking the dot product of the query vector with the key vector of
       the respective word we’re scoring. So if we’re processing the self-attention for the word in position #1, the
       first score would be the dot product of q1 and k1. The second score would be the dot product of q1 and k2.
       ![img_6.png](img_6.png)
       The third and fourth steps are to divide the scores by 8 (the square root of the dimension of the key vectors
       used in the paper – 64. This leads to having more stable gradients. There could be other possible values here,
       but this is the default), then pass the result through a softmax operation. Softmax normalizes the scores so
       they’re all positive and add up to 1.
       ![img_7.png](img_7.png)
       This softmax score determines how much each word will be expressed at this position. Clearly the word at this
       position will have the highest softmax score, but sometimes it’s useful to attend to another word that is
       relevant to the current word.
    3. The fifth step is to multiply each value vector by the softmax score (in preparation to sum them up). The
       intuition here is to keep intact the values of the word(s) we want to focus on, and drown-out irrelevant words (
       by multiplying them by tiny numbers like 0.001, for example).
    4. The sixth step is to sum up the weighted value vectors. This produces the output of the self-attention layer at
       this position (for the first word).
       ![img_8.png](img_8.png)
       That concludes the self-attention calculation. The resulting vector is one we can send along to the feed-forward
       neural network. In the actual implementation, however, this calculation is done in matrix form for faster
       processing. So let’s look at that now that we’ve seen the intuition of the calculation on the word level.
6. For faster processing, transformers use matrix to represent the whole sequences instead of vector to represent each
   word.
   ![img_9.png](img_9.png)
   Every row in the X matrix corresponds to a word in the input sentence. We again see the difference in size of the
   embedding vector (512, or 4 boxes in the figure), and the q/k/v vectors (64, or 3 boxes in the figure)
   Finally, since we’re dealing with matrices, we can condense steps two through six in one formula to calculate the
   outputs of the self-attention layer.
   ![img_10.png](img_10.png)
7. “multi-headed” attention improves the performance of the attention layer in two ways:
    1. It expands the model’s ability to focus on different positions. Yes, in the example above, z1 contains a little
       bit of every other encoding, but it could be dominated by the the actual word itself. It would be useful if we’re
       translating a sentence like “The animal didn’t cross the street because it was too tired”, we would want to know
       which word “it” refers to.
    2. It gives the attention layer multiple “representation subspaces”. As we’ll see next, with multi-headed attention
       we have not only one, but multiple sets of Query/Key/Value weight matrices (the Transformer uses eight attention
       heads, so we end up with eight sets for each encoder/decoder). Each of these sets is randomly initialized. Then,
       after training, each set is used to project the input embeddings (or vectors from lower encoders/decoders) into a
       different representation subspace.
       ![img_11.png](img_11.png)
       This leaves us with a bit of a challenge. The feed-forward layer is not expecting eight matrices – it’s expecting
       a single matrix (a vector for each word). So we need a way to condense these eight down into a single matrix. We
       concat the matrices then multiply them by an additional weights matrix WO.
       ![img_12.png](img_12.png)![img_13.png](img_13.png)
8. If we add all the attention heads to the picture, however, things can be harder to interpret:
   ![img_14.png](img_14.png)
   One thing that’s missing from the model as we have described it so far is a way to account for the order of the words
   in the input sequence. To address this, the transformer adds a vector to each input embedding. These vectors follow a
   specific pattern that the model learns, which helps it determine the position of each word, or the distance between
   different words in the sequence. The intuition here is that adding these values to the embeddings provides meaningful
   distances between the embedding vectors once they’re projected into Q/K/V vectors and during dot-product attention.
   ![img_15.png](img_15.png)![img_16.png](img_16.png)
   What might this pattern look like? In the following figure, each row corresponds to a positional encoding of a
   vector. So the first row would be the vector we’d add to the embedding of the first word in an input sequence. Each
   row contains 512 values – each with a value between 1 and -1. We’ve color-coded them so the pattern is visible.
   ![img_17.png](img_17.png)
9. The Residuals
    1. One detail in the architecture of the encoder is that each sub-layer (self-attention, ffnn)
       in each encoder has a residual connection around it, and is followed by a layer-normalization step.
       ![img_18.png](img_18.png)
    2. If we’re to visualize the vectors and the layer-norm operation associated with self attention, it would look like
       this:
       ![img_19.png](img_19.png)
    3. This goes for the sub-layers of the decoder as well. If we’re to think of a Transformer of 2 stacked encoders and
       decoders, it would look something like this:
       ![img_20.png](img_20.png)
10. The Decoder
    1. The encoder start by processing the input sequence. The output of the top encoder is then transformed into a set
       of attention vectors K and V. These are to be used by each decoder in its “encoder-decoder attention” layer which
       helps the decoder focus on appropriate places in the input sequence. The following steps repeat the process until
       a special symbol is reached indicating the transformer decoder has completed its output. The output of each step
       is fed to the bottom decoder in the next time step, and the decoders bubble up their decoding results just like
       the encoders did. And just like we did with the encoder inputs, we embed and add positional encoding to those
       decoder inputs to indicate the position of each word.
    2. The self attention layers in the decoder operate in a slightly different way than the one in the encoder: In the
       decoder, the self-attention layer is only allowed to attend to earlier positions in the output sequence. This is
       done by masking future positions
       (setting them to -inf) before the softmax step in the self-attention calculation. The “Encoder-Decoder Attention”
       layer works just like multiheaded self-attention, except it creates its Queries matrix from the layer below it,
       and takes the Keys and Values matrix from the output of the encoder stack.
11. The Final Linear and Softmax Layer
    1. The Linear layer is a simple fully connected neural network that projects the vector produced by the stack of
       decoders, into a much, much larger vector called a logits vector. Let’s assume that our model knows 10,000 unique
       English words (our model’s “output vocabulary”) that it’s learned from its training dataset. This would make the
       logits vector 10,000 cells wide – each cell corresponding to the score of a unique word. That is how we interpret
       the output of the model followed by the Linear layer.
    2. The softmax layer then turns those scores into probabilities (all positive, all add up to 1.0). The cell with the
       highest probability is chosen, and the word associated with it is produced as the output for this time step.
       ![img_21.png](img_21.png)
12. The goal of reducing sequential computation also forms the foundation of the Extended Neural GPU, ByteNet and
    ConvS2S, all of which use convolutional neural networks as basic building block, computing hidden representations in
    parallel for all input and output positions. In these models, the number of operations required to relate signals
    from two arbitrary input or output positions grows in the distance between positions, linearly for ConvS2S and
    logarithmically for ByteNet. This makes it more difficult to learn dependencies between distant positions. In the
    Transformer this is reduced to a constant number of operations, albeit at the cost of reduced effective resolution
    due to averaging attention-weighted positions, an effect we counteract with Multi-Head Attention.
13. Here, the encoder maps an input sequence of symbol representations to a sequence of continuous representation  
    Given the decoder then generates an output sequence of symbols one element at a time.

14. Here is a basic EncoderDecoder architecture
    ```
    class EncoderDecoder(nn.Module):
    """
    A standard Encoder-Decoder architecture. Base for this and many 
    other models.
    """
    def __init__(self, encoder, decoder, src_embed, tgt_embed, generator):
        super(EncoderDecoder, self).__init__()
        self.encoder = encoder
        self.decoder = decoder
        self.src_embed = src_embed
        self.tgt_embed = tgt_embed
        self.generator = generator
        
    def forward(self, src, tgt, src_mask, tgt_mask):
        "Take in and process masked src and target sequences."
        return self.decode(self.encode(src, src_mask), src_mask,
                            tgt, tgt_mask)
    
    def encode(self, src, src_mask):
        return self.encoder(self.src_embed(src), src_mask)
    
    def decode(self, memory, src_mask, tgt, tgt_mask):
        return self.decoder(self.tgt_embed(tgt), memory, src_mask, tgt_mask)
    
    class Generator(nn.Module):
    "Define standard linear + softmax generation step."
    def __init__(self, d_model, vocab):
        super(Generator, self).__init__()
        self.proj = nn.Linear(d_model, vocab)

    def forward(self, x):
        return F.log_softmax(self.proj(x), dim=-1)
    ```
![img_22.png](img_22.png)
15. There are multiple Encoder and Decoder stack to form the transformers
    ```
    def clones(module, N):
       "Produce N identical layers."
       return nn.ModuleList([copy.deepcopy(module) for _ in range(N)])
    
    class Encoder(nn.Module):
    "Core encoder is a stack of N layers"
    def __init__(self, layer, N):
        super(Encoder, self).__init__()
        self.layers = clones(layer, N)
        self.norm = LayerNorm(layer.size)
        
    def forward(self, x, mask):
        "Pass the input (and mask) through each layer in turn."
        for layer in self.layers:
            x = layer(x, mask)
        return self.norm(x)
    
    class LayerNorm(nn.Module):
    "Construct a layernorm module (See citation for details)."
    def __init__(self, features, eps=1e-6):
        super(LayerNorm, self).__init__()
        self.a_2 = nn.Parameter(torch.ones(features))
        self.b_2 = nn.Parameter(torch.zeros(features))
        self.eps = eps

    def forward(self, x):
        mean = x.mean(-1, keepdim=True)
        std = x.std(-1, keepdim=True)
        return self.a_2 * (x - mean) / (std + self.eps) + self.b_2
    ```
16. The reason for having the residual connection in Transformer is more technical than motivated by the architecture
    design. Residual connections mainly help mitigate the vanishing gradient problem. During the back-propagation, the
    signal gets multiplied by the derivative of the activation function. In the case of ReLU, it means that in
    approximately half of the cases, the gradient is zero. Without the residual connections, a large part of the
    training signal would get lost during back-propagation. Residual connections reduce effect because summation is
    linear with respect to derivative, so each residual block also gets a signal that is not affected by the vanishing
    gradient. The summation operations of residual connections form a path in the computation graphs where the gradient
    does not get lost. Another effect of residual connections is that the information stays local in the Transformer
    layer stack. The self-attention mechanism allows an arbitrary information flow in the network and thus arbitrary
    permuting the input tokens. The residual connections, however, always "remind" the representation of what the
    original state was. To some extent, the residual connections give a guarantee that contextual representations of the
    input tokens really represent the tokens.

Thoughts
===============

1. why multipling each value vector with the softmax score is able to keep intact the values of the word?
    1. as self-attention not only output the attention attribute but also the word information itself, so having a value
       vector to represent its word information should be important
2. what is sum up means in the sixth step?
    1. add all vectors numbers together or just simply combine all vector parallel together?
3. how fast can it be to use matrix instead of vectors?
4. why eight heads? Is it a magic number?
5. why multiple heads is able to expand the model's ability to focus on different positions?
6. what's the difference between one hot vector and logits vector?
    1. one hot vector is only 1 and 0. Logits includes a bunch of possibilities
7. is self attention one of the key component for parallel computing?

Summary
===============
Transformers model use self-attention and parallel operation (Constant running time). The Transformers model is composed
of a stack of Encoder and a stack of Decoder. In the original standard Transformers model, there are the same number of
encoder and decoder, which is six. This number could be adjusted according to different situation. Different from
attention mechanism, self attention use key, query and values to calculate and represent the sequence embeddings. In
detail, by training the neural network, a key embedding and query embedding is generated. The key and query embeddings
will be multiplied by the input sentence embeddings to generate the key and query vectors. Cause each word in the
sentence will have a key and query vectors, we will then use a softmax function to get the possibilities
(the weight) of each word in the sentence related to the current word. In order to keep the original word information,
the value vector is multiplied to the result comes from the softmax function. Then we got the final attention
representation of the input sentence. To accelerate the attention calculation process, we use matrix instead of vectors
to process the calculating process. Multi-head attention is to further classify different feature of the words. Also a
position embeddings is used to maintain the distance information between each word. After getting the attention score
from the input embedding, the feed forward neural network is used to process the input from self-attention. Then the
ffnn gives a output embedding to the next encoder. Both the self-attention and ffnn compose of a single encoder layer.
Further more, we add residual between self-attention and ffnn, from both direction, except at the first layer of the
encoder. Decoder has similar structure with the encoder. However, between self-attention and ffnn, there is an
encoder-decoder attention layer, similar to the attention mechanism. After encoder, there is a linear layer which makes
logits vectors with the same number of words size. And then there is a softmax layer to calculate the probabilities of
each word and get the final words selection. Regarding to how to select those words and get the output sentence, there
are several ways. For example, greedy search, which is get all the highest possibility word as the next word. Beam
search, more searching words from long-term.
