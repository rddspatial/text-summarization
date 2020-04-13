# Extractive Text Summarization based on Thematic Frequency

In this project we developed an extractive text summarization model 
based on thematic frequency. To do that, we developed a number of 
thematic lexicons critical to a disaster. The lexicons are related to 
place and time of a disaster event, reporting of an event, intensity of the event. 
The notion behind developing a lexicon-based approach is, it is simpler 
and faster to implement during a disaster. On the contrary text 
summarization models based on word-embeddings (e.g., Glove and Word2Vec) 
require a comparatively big data set to train the word embedding model(s) 
and create the word vectors, which is time consuming and also this does not 
guarantee if the embedding can capture the context of the disaster related 
terms which are critical to the emergency response operations, and thus 
generating an effective summary. To investigate how thematic frequency-based 
model performs, we compared the model with three other models 
(a general term frequency-based model, a Glove model and a Word2Vec model)
on Twitter threads. 

Note:

** Files start with def_def_* are the lexicons of different themes.
To develop the lexicons we used WordNet. First we defined a number of
seed words for each theme. Then we expanded our list by adding terms 
from the related synsets from the WordNet in recursive mnanner. 
For every word in a given synset we looked for the same word or
its nearby word (in terms of its semantics) to other synsets.

** There are 4 summary samples added in the repository.

** Due to Twitter policy we could not publish the threads but
the models can be deployed on any text dataset.

In the following we explain how thematic summarization model works using toy example.

Examples:

Example thread 1:

----------------------------------------------------------
Thread ID,Tweet ID,User name,Time,Content

-----------------------------------------------------------
1,11,TID1,06/08/2015 2:23,I am feeling an earthquake at Tokyo. I felt another earthquake yesterday.

1,12,TID2,06/08/2015 3:18,Everything will be alright.

1,13,TID3,07/08/2015 7:22,Just felt another earthquake in Yokohama in Japan. It's scary when you experience it for the 1st few times!

1,14,TID1,07/08/2015 7:44,Too scary indeed

----------------------------------------------------------
To generate the summary the following steps are followed.

1.	Pre-processing and tokenization:

We pre-process the above text and tokenize each tweet into a number of sentences. We remove the stop-words. 

2.	Thematic parsing: 
After running thematic parser we have retrieved the following thematic terms over all the sentences in the given thread.

SPATIAL THEME SET:  {'tokyo', 'yokohama', 'japan'}
TEMPORAL THEME SET:  {'yesterday'}
INTENSITY THEME SET:  {}
HELP THEME SET:  {}
REPORT THEME SET:  {'feeling', 'felt', 'experience', 'earthquake'}
CASUALTY THEME SET:  {} 

3.	Weight assignment:

Based on the term frequency and thematic weight, the tokens are assigned with following weights.
{'feeling': 6, 'earthquake': 18, 'tokyo': 11, 'felt': 12, 'another': 2, 'yesterday': 11, 'everything': 1, 'alright': 1, 'yokohama': 11, 'japan': 11, 'scary': 2, 'experience': 6, '1st': 1, 'times': 1, 'indeed': 1}

Then the weights are normalized based on the maximum weight. In this example, the word ‘earthquake’ has maximum weight.

Normalized token weights: {'feeling': 0.33, 'earthquake': 1.0, 'tokyo': 0.61, 'felt': 0.66, 'another': 0.11, 'yesterday': 0.61, 'everything': 0.05, 'alright': 0.05, 'yokohama': 0.61, 'japan': 0.61, 'scary': 0.11, 'experience': 0.33, '1st': 0.05, 'times': 0.05, 'indeed': 0.05}

4.	Sentence scoring and ranking:

Then we tokenize each thread into a number of sentences. For each sentence we compute an aggregate score using the weights assigned to its constituent tokens. For example, in the following sentence there are four thematic terms (felt, earthquake, Yokohama, Japan) and one non-thematic terms (another). There is one stop-word ‘just’, which is not considered while computing the weight assignment. 

‘Just felt(0.66) another(0.11) earthquake(1.0) in Yokohama(0.61) in Japan(0.61)’

Thus, the aggregate score for the sentence is 0.66+0.11+1.0+0.61+0.61=3 (approx)

Likewise, we compute the score for all the sentences in the above thread and rank them based on their scores as follows.

---------------------------------------------------
Sentence, Score, Rank

---------------------------------------------------
I am feeling an earthquake at Tokyo., 1.94,	3

I felt another earthquake yesterday.,	2.38,	2

Everything will be alright.,	0.11,	6

Just felt another earthquake in Yokohama in Japan.,	3.0,	1

It's scary when you experience it for the 1st few times!,	0.55,	5

Too scary indeed,	0.16,	4

-------------------------------------------------------

5.	Summary generation:

Once the sentences are ranked, top-k (30%) sentences are selected that contain most relevant (thematic) information. 

The resultant summary:

Just felt another earthquake in Yokohama in Japan. I felt another earthquake yesterday.

