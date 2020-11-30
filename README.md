# Anuvaad
State of the art open-source translation models for Indic languages.


# Installation

```bash
# CPU pytorch will be installed if torch is not installed
pip install --upgrade anuvaad
```

# Usage

**As a Python module**

```python
from anuvaad import Anuvaad
anu = Anuvaad("english-telugu")

# Single sentence translation
# beam_size is optional and defaults to 4
anu.anuvaad("YS Jagan is the chief minister of Andhra Pradesh.")
# "వైఎస్ జగన్ ఆంధ్రప్రదేశ్ ముఖ్యమంత్రి."

# Batch translation
anu.anuvaad(["YS Jagan is the chief minister of Andhra Pradesh.",
            "Nara Lokesh suffered a humiliating defeat in Mangalagiri."])
# ['వైఎస్ జగన్ ఆంధ్రప్రదేశ్ ముఖ్యమంత్రి.', 'మంగళగిరిలో నారా లోకేష్కు అవమానకరమైన ఓటమి ఎదురైంది.']

```

**As a service**
```bash
# Starting the api service
docker run -it -e BATCH_SIZE=1 -p 8080:8080 notaitech/anuvaad:english-telugu

# Running a prediction
curl -d '{"data": ["YS Jagan is the chief minister of Andhra Pradesh."]}' -H "Content-Type: application/json" -X POST http://localhost:8080/sync
```


|Available Models   |   Anuvaad BLEU    |  Google BLEU    |
|--------|:--------------:|--------|
|english-telugu |   12.721173743764009   |  6.841437460383768 |
|english-tamil | 12.737036149214694 | 5.558450942590664
|english-malayalam |       |   |
|english-kannada |   7.888886041933815    | 3.2803251953567893  |
|english-marathi |       |   |
|english-hindi |       |   |

 - Google BLEU is calculated from translations generated by the [GOOGLETRANSLATE() function](https://support.google.com/docs/answer/3093331?hl=en) on google sheets.

 - The testing scripts and data from Tatoeba is present at https://github.com/notAI-tech/Anuvaad-testing-scripts

 - https://docs.google.com/spreadsheets/d/1tYYZObELj-k6mJCnM6uf7xg3JChbSkjs8YvZsOgHacQ/edit?usp=sharing is the sheet containing the predictions from Anuvaad and GOOGLETRANSLATE function on the Tatoeba data.

# My thoughts on the evaluation/accuracy of the model(s):

1. Unlike classification/ sequence labelling tasks, for open-domain translation or summarization systems it is very hard to quantify the accuracy through numbers.
2. This is because, most accuracy measurements actually measure the overlap of character/word n-grams between the expected output and predicted output.
3. These scores definitely help when evaluating/comparing multiple models on a particular dataset, but the number don't translate well for open-domain models.
4. For example, Anuvaad translates the sentence ***An advance is placed with the Medical Superintendents of such hospitals who then provide assistance on a case to case basis.*** (taken from http://data.statmt.org/pmindia/v1/parallel corpus) to ***ऐसे अस्पतालों के चिकित्सा अधीक्षकों के साथ एडवांस रखा जाता है, जिसके बाद मामले के आधार पर सहायता प्रदान की जाती है।*** where as the expected translation of the sentence from the dataset is ***अग्रिम धन राशि इन अस्पतालों को चिकित्सा निरीक्षकों को दी जाएगी, जो हर मामले को देखते हुए सहायता प्रदान करेंगे।***.
5. In the above example, Although Anuvaad's translation is correct (in the sense that translation conveys the same thing as the original sentence), the BLEU score with n=3 will be 0.
6. Similarly, a model trained on the pmindia dataset will have bad score on a different dataset which uses a different style of writing, even if the translation is semantically correct.
7. Our aim in building Anuvaad is to build a general purpose, open-domain translation module that can flexibly translate text from various domains.
8. https://docs.google.com/spreadsheets/d/1_TTtBEvVgemQfGbRBSZYkECMMt5r7L9-dt0FGVUbmOY/edit?usp=sharing is a sheet comparing translations from Anuvaad, ilmulti (https://github.com/jerinphilip/ilmulti) and Google Translate (=GOOGLETRANSLATE(text, "en", "language") function on google sheets) on 100 randomly selected English sentences from Tatoeba. 
