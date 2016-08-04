# Naive Bayes Classifier Implementation

[Original URL](http://www.eggie5.com/66-naive-bayes-classifier-implementation)

> The Bayesian approach to classifying a new instance is to assign the most probable target value, $v_{nb}$, given the attribute values $ \langle a_1, a_2... a_n\rangle $ that describe the instance. $$...

The Bayesian approach to classifying a new instance is to assign the most probable target value, $v_{nb}$, given the attribute values $ \langle a_1, a_2... a_n\rangle $ that describe the instance.

$$<br>
v_{nb} = argmax P(a_1,a_2...a_n)<br>
$$

We can use Bayes theorem to rewrite the above express as:

$$<br>
v_{nb} = argmax \frac{P(a_1,a_2...a_n | v_j)P(v_j)}{P(a_1,a_2...a_n)}<br>
$$

$$<br>
= argmax P(a_1,a_2...a_n | v_j)P(v_j) ...........(1)<br>
$$

Now we can estimate the two terms in equation (1) based on the training data. We can compute $P(v_j)$ by counting the frequency that each target value $v_j$ occurs in the training data. However, calculating the individual $P(a_1,a_2...a_n | v_j)$ terms in equation 1 in this fashion is not feasible. The problem is that the number of terms is equal to the number of possible instances times the number of possible target values. Therefore, we need to see every instance in the instance space many time sin order to obtain reliable estimates.

The naive Bayes classifier is based on the simplifying assumption that the attribute values are conditionally independent given the target value. In other words the assumption is that given the target value of the instance, the probability of observing the conjunction $a_1,a_2...a_n|v_j)$ is just the product of the probabilities for the individual attributes: $P(a_1,a_2...a_n | v_j) = \prod_{i} P(a_i|v_j)$. If we substitute this into equation 1, we have the approach we can use in a classifier:

## Naive Bayes Classifier

$$<br>
V_{nb} = argmax P(v_j) \prod_{i} P(a_i|v_j)<br>
$$

where $v_{nb} denotes the target value output by the naive Bayes classifier. Notice that the total number of calculations for the classifier is: number of distinct attribute values times the number of distinct target values.

$$<br>
| \mathbf{P(a_j|v_j)} | = categories articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii words<br>
$$

To summarize, the Naive Bayes Classifier, has two steps: training and classification. In training, various $P(v_j)$ and $P(a_j|v_J) values are calculated based on their frequencies in the training data. Then in the classification step, these values are used to evaluate the probably of new input using equation 2.

## Application to Newsgroups Dataset

Our approach to classifying the text in the newsgroups dataset is simple: Given a text document each word is assigned an ID based on it's position in the document. This process is called tokenization. Now that we have all numbers we can apply the classifier. Once again the classification equation is:

$$<br>
\DeclareMathOperator*{\argmax}{arg\,max}<br>
v_{NB} = \argmax_{v_j all docs} P(v_j) \prod\limits_{i=1}^{|V|} P(a_i|v_j)<br>
$$

So this is saying, for each category/class in the corpus, lookup the Bayes probabilities of all the input words $a_i$ and take the product and choose the largest. So for example, if we have 20 classes, we'll have 20 iterations of the outer loop and $|v|$ iterations of the inner loop. We then choose the largest probability out of the 20.

To complete our design of the Bayes classifier, we must still choose a method for estimating the probability terms. A common metric is the m-estimate equation with lapace smoothing:

$$<br>
p(a_j|v_j) = \frac{n_k + 1}{n+ |Vocabulary|}<br>
$$

where n is the total number of word position in all training examples whose target value is $v_j$, $n_k$ is the number of times work $w_k$ is found among these $n$ word positions and $|Vocabulary|$ is the total number of distinct words (and other tokens) found within the training data.

## Pseudo Code

```
Train(Examples, V)

1\. collect all words that occur in Examples
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Vocabulary <- the set of all distinct words and other tokens occurring in any documents from examples
2\. calculate the required P(v_j) and P(w_k|v_j) probability terms
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii For each target value v_j in V do
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii docs_j <- the subset of docs from Examples for which the target value is v_j
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii P(v_j) <- |docj|/|examples|
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Text_j <- a single document created by concating all members of docsj
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii n <- total # of distinct word positions in Textj
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii for each word w_k in Vocabulary
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii n_k <- number of times word w_k occurs in Textj
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii P(w_k|vj) <- n_k+1 / n+|vocabulary|

Classify(Doc)
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii positions <- all word position in Doc that contains tokens found in Vocabulary
 articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii Return v_nb, where
 v_nb = argmax P(vj) product P(aj|vj)
```

## Python Implementation

### Discrete Environment Note

Due to the fact that we are running this classifier in a discrete-finite-percision environment (computer) underflow is a real problem when we multiply a large number for probability fractions. One work around for this is to use logs, see the revised equation below:

#### Discrete Naive Bayes Classifier

$$<br>
V_{nb} = argmax{ log(P(v_j)) \sum_{i} log(P(a_i|v_j))}<br>
$$

```
import numpy as np
import time

class NaiveBayesClassifier(object):
 """docstring for NaiveBayesClassifier"""

 def __init__(self, arg):
 super(NaiveBayesClassifier, self).__init__()
 self.arg = arg
 self.training = {}
 self.vocabulary = set()

 def train(self, features, targets, vocab=None):
 start = time.time()
 target_count = len(set(targets))
 target_names = list(set(targets))
 example_count = len(features)
 print("Total training examples: ")+str(example_count)
 print("Total targets: ")+str(target_count)

 #add words to vocabulary set
 if vocab is None:
 print "Building vocabulary from corpus"
 [self.vocabulary.add(word) for feature in features for word in feature]
 else:
 print "Buildign vocabulary from user input"
 self.vocabulary=vocab

 # lets get the vocab from vocabulary.txt
 vocab_count = len(self.vocabulary)
 print "Vocabulary count: "+str(vocab_count)
 print "Estimated iterations: "+str(vocab_count articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii target_count)

 #preprocessing -- need these constants before calculating posteri
 print "training step 1..."
 for i in range(example_count):
 # print "Training step #1 for example: "+str(i)+" ..."
 example = features[i]
 target = targets[i]

 #setup the internal data structure
 if(target not in self.training):
 self.training[target]={'count':0, 'prob': 0.0, 'blob': [], 'text':{}, 'n': 0, 'words':{}}

 #put the example doc words in the category bag
 self.training[target]['blob']+=example
 for word in example:
 self.training[target]['text'][word]=True
 self.training[target]['n'] = len(list(set(self.training[target]['blob'])))

 #calc. priori probabilities
 self.training[target]['count']+=1
 self.training[target]['prob']= float(self.training[target]['count']) / float(len(targets))


 print "training step 2..."
 #now iterate the vocabulary for each category (20 iterations)
 for i in range(target_count):
 target = target_names[i]
 # print "Training step #2 for target: "+str(target)+" ..."

 m=len(self.vocabulary)
 n = self.training[target]['n']
 norm = 1#n+m

 for word in self.vocabulary:
 if(word not in self.training[target]['words']):
 # in the vocab but not in the blob
 self.training[target]['words'][word]={'count':0, 'prob':1.0}

 #only words in the document blob
 if(word in self.training[target]['text'] ):
 self.training[target]['words'][word]['count']+=1
 count = self.training[target]['words'][word]['count']
 self.training[target]['words'][word]['prob']= (count + 1.0)# / norm

 # pp.pprint(self.training)
 end = time.time()
 elapsed = (end - start)
 print "Training Complete: {0}s".format(elapsed)
 return True



 # print vocabulary

 def predict(self, Y):
 # print ("running predictions...")
 #TODO: Fix -> this removes duplicates, e.g.: Y=[1,2,3,9,9,9,9]
 positions = Y#list(set(self.vocabulary) & set(Y)) #remove words that are out of vocab.

 probabilities = {}

 for target in self.training.keys():
 prob_target = self.training[target]['prob']
 rsum=0
 for word in positions:
 word_prob = self.training[target]['words'][word]['prob']
 word_count = self.training[target]['words'][word]['count']
 rsum+= np.log2(word_prob)
 target_prob = np.log2(prob_target) + rsum
 probabilities[target_prob]=target
 return probabilities[max(probabilities)]
```

## Demo

```
import utils as utils
import numpy as np
```

### Setup

I did some preprocessing of the dataset w/ my utils library that I imported in the above cell. We then load the training data and the vocabulary.

```
path = '20news-bydate-matlab/matlab'
features = utils.read_features("expanded.txt")
targets = utils.read_label(path, 'train.label')
vocab = utils.read_vocab("vocabulary.txt")
```

### Train

Next we import and setup my classifier which I have packaged into the NaiveBayesClassifier class. We then pass it the training examples and see how long it takes to train:

```
clf = NaiveBayesClassifier("")
clf.train(features, targets, vocab=vocab)

Total training examples: 11269
Total targets: 20
Building vocabulary from user input
Vocabulary count: 61188
Estimated iterations: 1223760
training step 1...
training step 2...
Training Complete: 29.5654859543s
```

### Classify

The training took about 30 seconds. Now lets test our classifier on the test dataset. We will feed it examples and compare the predicted output the the known answers in the test dataset.

```
answer_label_array = utils.read_label(path, 'test.label')
test_features = utils.read_features("test_expanded.txt")

correct_count = 0
for i in range(len(answer_label_array)):
 expected = clf.predict(test_features[i])
 actual = answer_label_array[i]
 if expected == actual:
 correct_count+=1


print "{0}/{1}".format(correct_count, len(answer_label_array))
print float(correct_count)/len(answer_label_array)

5364/7505
0.714723517655
```

### Results

So as you can see the classifier has a 28% error rate. This error rate could possibly be lowered by the use of different smoothing techniques or stop word analysis.

**Permalink:** naive-bayes-classifier-implementation

**Tags:**
