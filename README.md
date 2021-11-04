# text-summarizing-with-python
text summarize with "words frequency" method 


required library :
# !pip install spacy
# !python -m spacy download fr_core_news_sm
# pip install nltk
# pip install docx2txt
# nltk.download('punkt')
# nltk.download('stopwords')

import spacy
import docx2txt
import re
import nltk
from spacy.lang.fr.stop_words import STOP_WORDS
from string import punctuation

nlp = spacy.load("fr_core_news_sm")

# load text
text = docx2txt.process("TexteAResumer.docx")

print (text)

# text pre-processing
clean = re.sub('<h1>.*?</h1>', '', text)
clean = re.sub('<h2>.*?</h2>', '', clean)
clean = re.sub('<h3>.*?</h3>', '', clean)
clean = re.sub('<.*?>', '', clean)
# to lower
clean = clean.lower()
# remove numbers
# clean = re.sub(r'\d+',' ', clean)
# change ’ with '
clean = re.sub(r'’',"'", clean)
# change ... with .
clean = re.sub(r'…',".", clean)
#removes leading and trailing whitespaces
clean = re.sub('\s+', ' ', clean).strip()

print (clean)

doc = nlp(clean)
print (len(clean))
print ( len (doc))

# split and tokenize to sentences 
sentences = doc.sents
i=1
tokenSentences={}
for sent in sentences:
    tokenSentences[i]=sent
    i=i+1
    
tokenSentences

# split to words
wordsNLTK = nltk.word_tokenize(clean)
wordsSPACY = [word.text for word in doc]
print(len(wordsNLTK))
print(len(wordsSPACY))
print(wordsSPACY)

# we gona use spacy doc because it have more number of words 

# stopwords spacy vs stopwords nltk
stopwords_spacy = list(STOP_WORDS)
stopwords_nltk = nltk.corpus.stopwords.words('french')
print(len(stopwords_spacy))
print(len(stopwords_nltk))

# we gona use spacy STOP_WORDS because it have more stopwords

# spacy punct list
# punctuation is mutable so you can add another symbol punctuation = punctuation + '§'
punctuation

# Extract the lemma for each word
finalCleanText = " ".join([token.lemma_ for token in doc])
finalCleanText

# convert the new lemma text from text type to nlp type
# to facilate the work later
doc = nlp(finalCleanText)

# calculat the frequencies of words without considerate the puncts and the stopwords
word_frequencies = {}
for word in doc:
    if word.text.lower() not in stopwords_spacy:
        if word.text.lower() not in punctuation:
            if word.text not in word_frequencies.keys():
                word_frequencies[word.text]=1
            else:
                word_frequencies[word.text]+=1
                
word_frequencies

# the most word exist
max_frequency =max(word_frequencies.values())
max_key = max(word_frequencies,key=word_frequencies.get)
print (max_key,' : ',max_frequency)

# calculate the score of each word
for key in word_frequencies.keys():
    word_frequencies[key] = word_frequencies[key]/max_frequency

word_frequencies

# calculate the weigh of sentences
sentencesWeight = {}
for key in tokenSentences.keys():
    weight=0
    for word in tokenSentences[key]:
        if word.text in word_frequencies:
            weight = weight + word_frequencies[word.text]
            
    sentencesWeight[key] = weight
    
sentencesWeight

# get most frequent sentences
maxSentWeight = sorted(sentencesWeight, key=sentencesWeight.get, reverse=True)[:5]
maxSentWeight

# final result 
for key in tokenSentences.keys():
    if key in maxSentWeight:
        print (tokenSentences[key])
