# Stock Price Prediction using Sentiment Analysis of Reddit Text

## Introduction: Reddit and Alternative Data in Stock Performance Analysis

In recent times, Reddit has surfaced as a favored platform for engaging in conversations and deliberations spanning a broad array of subjects, which encompass financial matters and investments. With many users sharing their viewpoints and understandings concerning diverse stocks and financial markets, Reddit has transformed into a valuable information pool for gauging sentiments within the financial domain. Additionally, with the emergence of Alternative Data as a growing source to evaluate Market Performance, studying the impact of market sentiment could add an invaluable measure while studying possible vulnerabilities of a stock.

This project delves into the application of sentiment analysis for assessing the fluctuations of widely-held stocks, employing Reddit headlines and comments as a wellspring of market sentiment. This project aims to scrutinize pre-existing studies that leverage sentiment analysis derived from Reddit to approximate market volatility, and consequently, future gains.

## Authors
[@Soujanya-Hassan-Prabhakar](https://github.com/Soujanya-Hassan-Prabhakar) - 22202225
[@Hrishita-Bapuram](https://github.com/hbapuram) - 22204557

## Installation

Following is the exhaustive list of all the libraries used throughout the course of the project:

```python
!pip install numpy
!pip install pandas
!pip install plotly
!pip install matplotlib
!pip install statsmodels
!pip install seaborn
!pip install sklearn
!pip install scipy
!pip install datasets >> NULL
!pip install transformers >> NULL
!pip install swifter >> NULL
!pip install -U sentence-transformers
!pip install -U scikit-learn >> NULL
!pip install vaderSentiment
!pip install wordcloud
import nltk
nltk.download('all')
```

## Project Flow

The structure and flow of the project can be summarized in the following flow chart:
![Project_Flow]()

## Data Extraction 

The Reddit Data obtained for this project was extracted from their `PRAW` package. While the extracted files are available in [RAW_DATA]() for each company under their respective company folders, the code used for extraction has been uploaded in the [Python Files]() directory as `reddit_data_scrapping.ipynb` linked [here]()

Please note that in order to use the code, you must have a Reddit developer account and the API keys to retrieve data. The keys referenced in the code have been masked. The documentation for PRAW can be found [here](https://praw.readthedocs.io/en/stable/)

The Financial Data obtained for this project was sourced from [Yahoo Finance](https://finance.yahoo.com/) and the files thus obtained are uploaded in the `FINANCIAL DATA` folder in [RAW_DATA]()

## Reddit Data Pre-Processing and Sentiment Analysis

> The code for this section can be referred to from the `data_preprocess_text.ipynb` file found [here]() in the Python Files directory. Specific parts/functions are outlined in the summary below:

The textual data prior to any analysis, required extensive cleaning and reformatting and the process can be outlined in the following steps as executed by the `preprocess_text()` function:

* Check if 'text' is a non-null string
* Remove punctuation
* Convert to lowercase
* Tokenize text (i.e., separate the words in a sentence into individual tokens to analyze)
* Remove stopwords
* Filter out short words
* Join tokens back into a string

Some initial cleaning was applied to restructure the `headlines` and `comments` files in order to extract all relevant variables for analysis prior to merging all the textual files for a company into one common dataframe.

Post Analysis, the processed text looks something similar to the output obtained below:
![Text_Preprocess_Output]()

## Sentiment Analysis

In order to perform sentiment analysis on the text, we first filter the data for the required time range and create functions to extract the __Subjectivity Score__ (from `Text Blob`) __Compound Score__ (from `VADER`) which will form the basis of the Sentiment Score used in prediction.

Post this analysis, we can also obtain a wordcloud to explore the most frequently used terms in the conversations about a particular company. Here's an example for Apple:
![Apple_wordCloud]()

The sentiment scores thus obtained using the `getPolarity(text)` and `getSIA(text)` functions are then aggregated for each day by taking a weighted average of the `score` weighted by the score variable from Reddit which is indicative of the popularity of each comment. The end result for each day would thus be an average of the scores obtained for all texts published on that day with more importance given to texts with greater popularity/interaction (implied by higher score) and vice versa.

Before we proceed to merge the results here with the financial data, we `Forward Fill` the days with missing observations for each company. The final processed text file will have the following structure:

123 rows corresponding to each day of the target date range
5 columns corresponding to `date`, `score`, `w_subj` (weighted subjectivity score), `w_polar` (weighted polarity score), and `w_comp` (weighted compound score)
Please note that the polarity score and compound score are comparable - both are metrics that convey the extent of 'positivity' or 'negativity' detected in a sentence. The polarity metric is obtained from the `Text Blob` library and the compound score from `VADER`. For the purpose of our study, we chose the compound score as the metric to convey sentiment since VADER has been found to perform better in analyzing social media content.

## Financial Data Pre-processing

> The code for this section can be referred to from the `data_preprocess_fin.ipynb` file found [here]() in the Python Files directory. Specific parts/functions are outlined in the summary below:

The financial data does not require any implicit cleaning since the data is very well structured. The only pre-processing performed here was the application of Forward fill to get continuous data without any missing observations in the date range. The data was also filtered for the target date range. The final processed text file will have the following structure:

* 123 rows corresponding to each day of the target date range
* 7 columns corresponding to `date`, `open`, `high`, `low`, `close`, `adj_close` (which is the response variable in our prediction model), and `volume` 

## Merging the financial data and the Sentiment Scores by Date

> The preprocessed files were then merged together by date using the concat() function in Python. The code for the same can be found the the `merge_fin_text.ipynb` file and the combined data files can be found in the [DATA]() directory

## Prediction Model using RNN LSTM

## Predicting Adjusted Close Price without Sentiment Scores

> The code for this section can be referred to from the `Prediction_Model_without_Sentiment.ipynb` file found [here]() in the Python Files directory. Specific parts/functions are outlined in the summary below:

## Predicting Adjusted Close Price with Sentiment Scores

> The code for this section can be referred to from the `Prediction_Model_with_Sentiment.ipynb` file found [here]() in the Python Files directory. Specific parts/functions are outlined in the summary below:

## Model Evaluation using RMSE, MAE, MAPE, and Validation Loss

## Results and Conclusion

* The results obtained upon the implementation of the prediction algorithm for Adjusted Closing Price, we notice that the inclusion of the Sentiment Parameters makes a positive impact on improving performance
* The model fit didn't require a lot of hyperparameter tuning, with only minor adjustments made to the __number of input units__, __number of epochs__, and __dropout rate__.
* The sentiment analysis scores weighted by Reddit Score (Given as Number of Upvotes - Number of Downvotes) as the weight component in calculating the aggregate sentiment score for each day accounted for the level of interaction on a particular comment/headline thereby evaluating the average sentiment for each day appropriately in accordance with the "influence" of the particular text.
* Sentiment features, i.e., Subjectivity Score and Compound score prove to be particularly impactful in predicting future values where there have been huge spikes/dips (as observed in the behavior of Microsoft, Nvidia, and Tesla) since the improvement in the evaluation measures of Root Mean Squared Error, Mean Absolute Error and Mean Absolute Percentage Error has been greater in these cases than the rest.
* As an extended study, evaluating the data and analyzing the unpredictable highs and lows might facilitate the creation of a simulation to generate worst-case scenarios for companies to use while conducting stress tests or developing Business Continuity Plans.

## Poster:
[Poster Link]()

## Literature Review:
[Literature Review Link]()

## Acknowledgements

I want to extend my heartfelt gratitude to Dr. Sarp Akcay for the invaluable guidance and unwavering support provided during the duration of the module at University College Dublin. My gratitude also extends to University College Dublin for furnishing the necessary resources that played a pivotal role in realizing this project. Finally, I wish to express my deep thanks to all online resources including several publications that made the learning required for this project easy and accessible for everyone.
