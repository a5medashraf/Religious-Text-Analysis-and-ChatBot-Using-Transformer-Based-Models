# Religious-Text-Analysis-and-ChatBot-Using-Transformer-Based-Models

## Abstract
While there are more than 313 million Arabic speakers in the world, and more than 1.9 billion Muslims, there is a lack of true guidance in regards of the Sharia of Islam and most importantly in the provisions of pillars of Islam such as the prayer, fasting, etc.
Nowadays, the internet is filled with misleading fatwas and the Muslim who really searching for the true information which is based on the Quran or on the Hadith of Prophet Mohamed is in a problem, he/she has to search about who gave the fatwa and to check the validity of the Hadith that the Sheikh relied on in his fatwa.
In ‘مرجعنا’, we will make a GPT2 bot that can give a reliable and valid fatwa. We will use transformers (GPT2) for the bot to give fatwa because we need it to give
“human-like” responses. The model will be mainly and initially trained on islamweb.net fatwas, it is a famous and valid source where any fatwa is given by a knowledgeable and reliable Sheikh with the Quran and trusted Hadith. The bot is going to be initially in Arabic language only.

## Methodology
In this project, we used islamweb.net data that we scraped as we mentioned in the Abstract section.<br>
The Scraped Data: https://www.kaggle.com/datasets/abdallahelsaadany/fatawa <br>
<br>
we cleaned the data from NULL values, irrelevant text, and text that contains a reference that cannot be accessed. Then, we concatenate the question/title with the answer in one single column. After all the data cleaning we end up with 40,859 data-points (pair of question and answer), for computational cost reasons, we only select the first 20,000 data-point, 90% for training and 10% for validation. the data is in csv format, but for gpt2 (text generation task) it is better to feed the transformer text file, so we convert the csv file to text file, one for training and one for validation. We then fine-tuned the pre-trained model araGPT2-base (by aubmindlab) to our data, using their tokenizer. Also, for computational cost reasons, we used the base version of the transformer. special token added for the tokenizer (<question:>, <answer:>) to let the model understand which part is question and which part is answer. We created a function 'load_dataset()' that loads the training and validation dataset, tokenizes it, and pad the input with 'DataCollatorForLanguageModeling()' function. The Majority of the functions and methods used are from the 'transformers' huggingface library. The number of trainable parameters are 134,994432 parameters.
Then we give the trainer the model, the arguments, data_collator, train and validation dataset. we tracked Training loss and validation loss only during the training. The model is trained on a single GPU (NVIDIA GeForce RTX 3070 8GB GDDR6), the training/validation process took 8 hours and 20 minutes.
After the fine-tuning the model, the weights are saved in the local device to use the model afterwards without the need of training again. To deploy the model, 'gradio' library has been used, the user asks a question, the answer then appears on the right side of the web-page in a text box. the maximum length of a text that the model can generate is set to 300.
Last but not least, there is a hold-out-test-set that the model must be tested on. the hold-out-test-set contains 15 questions related to the Islamic field, the model have to generate responses for the 15 questions. Perplexity meausure is used to determine the performance of the model on a new unseen data, perplexity is a common metric used for language models and
it measures the wellness of a model or a probability distribution on predicting the next word or phrase. In other words, when perplexity is high, it means that the level of confidence of the model while predicting the next words is low. And when the perplexity is low, the model's confidence while predicting the next word is high.
<br>
## Results
Before, we fine-tuned the model where no special tokens are provided, the model started with training loss of 4.9 and is learning the data slowly. Therefore, we tried to add special tokens to improve the model's performance. The model started with training loss of 3.2 when special tokens are provided and is learning much faster. The model finished the training/validation process with training loss of 1.9 and validation loss of 2.6, it is reasonably great due to computational and data limitations, in addition that there is no sign of over-fitting nor under-fitting as shown in the figure below.

![image](https://github.com/Marje3na/Religious-Text-Analysis-and-ChatBot-Using-Transformer-Based-Models/assets/67977986/f3dcfbff-521e-491a-8948-06c6569eb3f8)

Now it's time to test the fine-tuned model on unseen data and that is the hold-out-test-set we have prepared; it contains 15 questions that any random person might ask, and it is relatively new to the model. We used the perplexity measure as we discussed in the Methodology section of the documentation. The mean perplexity scores the fine-tuned model got on the 15 unseen questions is 2.3. The perplexity score is very low, and it is good news for us. However, we have noticed that the model got low perplexity scores on the majority of questions related to the Islamic Religion, and that is understandable because our fine-tuned model is closed domain and is very specific in terms of the knowledge the model has. So, almost any question related to Islam and Islamic Sharia and whatsoever asked to the fine-tuned model it is going to get a low perplexity score. Nevertheless, when the model generates a relatively perfect answer to the question asked the perplexity score is the lowest the score for the model, and if the answer has some manipulated text and does not answer the question directly it will get a higher perplexity score from the one before we talked about, and if the answer is completely does not answer the question and there is some verses of the Quran or Hadiths are manipulated the model would get the highest perplexity score of the 3 examples we stated. Thus, Perplexity score is a good measure to test a text-generation model like ours and we got good and informative results overall and after all.
