# Byte Pair Encoding 


## Word Segmentation in Natural Language Processing

In Natural Language Processing (NLP), tokenization, or word segmentation, is a process that splits a character sequence into meaningful units for processing, called tokens. 

At the word level, tokenization breaks up a sentence into word units. For example, the sentence 'I like eating ice cream.' can be tokenized as  ['I', 'like', 'eating', 'ice', 'cream', ' . ']

We can also treat individual characters as tokens and further breaking up each word into a sequence of characters. For example, the tokenization of ‘eating’ is [‘e’, ‘a’, ‘t’, ‘i’, ‘n’, ‘g’]. Character level tokenization is most useful for character-based language, e.g. Chinese. 

We are concerned with word level segmentation. A simple tokenization technique splits on spaces between words. What could be a problem with this approach?

## The problem

Consider the following words: 'low', 'lower' and 'lowest'. They are distinct words that come from the same word family: 'low'. The differences in the word structure, or morphology, are obvious: the affixes '-er' and '-est' that are tacked onto the end of the root 'low'. 


What about semantics? How do affixes like '-er' and '-est' modify the original meaning of a word like 'low'? According to the Oxford Dictionary, 'low' has the meaning of being below average in amount, extent, or intensity. What about 'lower' and 'lowest'? There are no seperate entries for 'lower' and 'lowest' in the Oxford Dictionary. However, we know that 'lower' refers to an object being less high, or great in amount, compared to another object. 

Similarly, if we say an object A is the 'lowest' in a group of objects that consists of object A, B and C, then the following conclusions can be drawn:
 1. Object A is lower than both object B and C.
 2. There can be no other object that is lower than object A in the space that holds objects A, B and C. 

It seems that 'low' has an absolute meaning of being below average, while the meanings of 'lower' and 'lowest' are relative. In this way, we create new words by compounding the root with known affixes, and derive new meanings from them. The question is, can we teach machines to do the same? In other words, given new words like 'lower' and 'lowest', can the machine learn to discern the nuances in meaning by seperating these words into its common root and affixes? 


## Byte Pair Encoding for Word Segmentation 

In information theory, Byte Pair Encoding (BPE) (Gage, 1994) is a data compression technique that replaces the most frequent pairs of bytes in a sequence with a single byte that does not occur within the sequence. 

For example, supose we have the following sequence: 'aaabababaa'. Since the byte pair 'ab' occurs most often, we replace 'ab' with a single, unused byte, say 'Y'. The result is a new sequence: 'aaYYYaa'. The next frequent byte pair in our data is 'aa', so we replace it with another new byte 'X'. Our sequence is now encoded as 'XYYYX'. We stop here since there is no byte pair that appears more than once. To decode, simply replace 'X' and 'Y' with their corresponding byte pairs, 'aa' and 'ab', and we get back the original sequence. 

We will see how the BPE algorithm can be applied to encode sub-word units. Consider a vocabulary with the following tokenized words and their corresponding counts of frequency (the number of times a word appears in a corpus):

 ['l o w \</w>': 5,  'l o w e r \</w>': 2 ,  'n e w e s t \</w>': 6,  'w i d e s t \</w>': 3]

(Note: To represent word boundaries, we append '\</w>' to denotes the end of a word. This is similar to the ' . ' (dot) symbol)

We iterate through our vocabulary list and apply BPE to compute the most frequent consecutive byte pairs. Since the pair 'e' and 's' appears the most frequently: 6 + 3 = 9 times, we merge these into a new token 'es'. 

 ['l o w \</w>': 5,  'l o w e r \</w>': 2 ,  'n e w **es** t \</w>': 6,  'w i d **es** t \</w>': 3]

The next frequent byte pair is 'es' and 't', which appear 6 + 3 = 9 times. We merge these into a new token 'est' in the second iteration. 

 ['l o w \</w>': 5,  'l o w e r \</w>': 2 ,  'n e w **est** \</w>': 6,  'w i d **est** \</w>': 3]


We have the subsequent BPE merges: 'est' +  '\</w>' -> 'est\</w>',  'l' + 'o' -> 'lo', 'lo' + 'w' -> 'low', etc. 

The final vocabulary size is the sum of the number of BPE merge operations and the number of characters in the training data.
