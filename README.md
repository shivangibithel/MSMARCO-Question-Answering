# MSMARCO
A Family of datasets built using technology and Data from Microsoft's Bing.

> MS MARCO: A Human Generated MAchine Reading COmprehension Dataset
> Paper URL : https://arxiv.org/abs/1611.09268

MS MARCO(Microsoft Machine Reading Comprehension) is a large scale dataset focused on machine reading comprehension, question answering, and passage ranking, Keyphrase Extraction, and Conversational Search Studies, or what the community thinks would be useful. 

First released at [NIPS 2016](https://arxiv.org/pdf/1611.09268.pdf), the current dataset has 1,010,916 unique real queries that were generated by sampling and anonymizing Bing usage logs. The dataset started off focusing on QnA but has since evolved to focus on any problem related to search. For task specifics please explore some of the tasks that have been built out of the dataset. If you think there is a relevant task we have missed please open an issue explaining your ideas?  

For more information about [TREC 2019 Deep Learning](https://github.com/microsoft/TREC-2019-Deep-Learning)

For more information about [Q&A](https://github.com/microsoft/MSMARCO-Question-Answering)

For more information about [Ranking](https://github.com/microsoft/MSMARCO-Passage-Ranking)

For more information about [Keyphrase Extraction](https://github.com/microsoft/MSMARCO-OpenKP)

For more information about [Conversational Search](https://github.com/microsoft/MSMARCO-Conversational-Search)

For more information about [Polite Crawling](https://github.com/microsoft/MSMARCO-Optimal-Freshness-Crawl-Under-Politeness-Constraints)


## Dataset Generation, Data Format, And Statistics
What is the difference between MSMARCO and other MRC datasets? We believe the advantages that are special to MSMARCO are:
- Real questions: All questions have been sample from real anonymized bing queries.
- Real Documents: Most Url's that we have source the passages from contain the full web documents. These can be used as extra contextual information to improve systems or be used to compete in our expert task.
- Human Generated Answers: All questions have an answer written by a human. If there was no answer in the passages the judge read they have written 'No Answer Present.'
- Human Generated Well-Formed: Some questions contain extra human evaluation to create well formed answers that could be used by intelligent agents like Cortana, Siri, Google Assistant, and Alexa.
- Dataset Size: At over 1 million queries the dataset is large enough to train the most complex systems and also sample the data for specific applications.

## Download the Dataset
To Download the MSMARCO Dataset please navigate to [msmarco.org](http://www.msmarco.org/dataset.aspx) and agree to our Terms and Conditions. If there is some data you think we are missing and would be useful please open an issue.


# Q&A
MS MARCO(Microsoft Machine Reading Comprehension) is a large scale dataset focused on machine reading comprehension, question answering, and passage ranking. 

In MS MARCO, all question have been generated from real anonymized Bing user queries which grounds the dataset in a real world problem and can provide researchers real contrainsts their models might be used in.The context passages, from which the answers in the dataset are derived, are extracted from real web documents using the most advanced version of the Bing search engine. The answers to the queries are human generated. 

### Generation
The MSMARCO dataset is generated by a well oiled pipeline optimized for the highest quality examples. the general process runs as follows.
1. Bing logs are sampled, filtered and anonymized to make sure the queries we are collecting are both useful to the research community and respectful to our bing users and fans.
2. Using the sampled and anonymized queries Bing generates the 10 most relevant passages for the query.
3. Highly trained judges read the query and its related passages and if there is an answer present, the supporting passages are annotated and a natural language answer is generated.
4. A smaller proportion of queries(~17% of overall dataset with 182,887 unique queries) are then passed on to a second round of judges who are asked to verify the answer is correct and rewrite(if possible) the query to be a well formed answer. These answers are designed to be understood without perfect context and are designed with smart speakers/digital assistants in mind.

### Data Format
Much like the v2.0 release, the v2.1 release is provided as a json file. This is for easy exploration and debugging and loading. Based on feedback from our community the V2.1 now dataset now has utilities for easy conversion to the [JSONL](http://jsonlines.org/) format. Official downloads from the website are as one large json object but use the tojson.py or tojsonl.py utilites to switch easy between file formats. 

Each line/entry containts the following parameters to be described below: query_id, query_type, query, passages, answers, and wellFormedAnswers.

For the QA task the target output is present in 'answers'. For NLGen task the target output is present in 'wellFormedAnswers'.

1. query_id: A unique id for each query that is used in evaluation
2. query: A unique query based on initial Bing usage
3. passages: A set of 10:passages, URLs, and an annotation if they were used to formulate and answer(is_selected:1). Two passages may come from the URL and these passages have been obtained by Bing as the most relevant passages. If a passage is maked as is_selected:1 it means the judge used that passage to formulate their answer. If a passage is marked as is_selected:0 it means the judge did not use that passage to generate their answer. Questions that have the answer of 'No Answer Present.' will have all passages marked as is_selecte: 0.
4. query_type: A basic division of queries based on a trained classifier. Categories are:{LOCATION,NUMERIC,PERSON,DESCRIPTION,ENTITY} and can be used to debug model performance or make smaller more forcused datasets.
5. answers: An array of answers produced by human judges, most contain a single answer but ~1% contain more than one answer(average of ~2 answers if there are multiple answers). These answers were generated by real people in their own words instead of selecting a span of text. The language used in their answer may be similair or match the language in any of the passages.
6. wellFormedAnswers. An array of rewritten answers, most contain a single answer but ~1% contain more than one answer(average of ~5 answers if there are multiple answers). These answers were generated by having a new judge read the answer and the query and they would rewrite the answer if it did not (i) include proper grammar to make it a full sentence, (ii) make sense without the context of either the query or the passage, (iii) had a high overlap with exact portions in one of the context passages. This ensures that well formed answers are true natural languge and not just span selection. Well Formed Answers are a more difficult for of Question answering because they contain words that may not be present in either the question or any of the context passages. 

example
~~~
{
	"answers":["A corporation is a company or group of people authorized to act as a single entity and recognized as such in law."],
	"passages":[
		{
			"is_selected":0,
			"url":"http:\/\/www.wisegeek.com\/what-is-a-corporation.htm",
			"passage_text":"A company is incorporated in a specific nation, often within the bounds of a smaller subset of that nation, such as a state or province. The corporation is then governed by the laws of incorporation in that state. A corporation may issue stock, either private or public, or may be classified as a non-stock corporation. If stock is issued, the corporation will usually be governed by its shareholders, either directly or indirectly."},
		...
		}],
	"query":". what is a corporation?",
	"query_id":1102432,
	"query_type":"DESCRIPTION",
	"wellFormedAnswers":"[]"
}
~~~
## Utilities, Stats and Related Content
Besides the main files containing judgments, we are releasing various utilites to help people explore the data and optimize the data for their needs. They have only been tested with python 3.5 and are provided as is. Usage is noted below. If you write any utils you feel the community could use and enjoy please submit them with a pull request.

### File Conversion
Our community told us that they liked being able to have the data in both json format for easy exploration and [JSONL](http://jsonlines.org/) format to make running models easier. To help the easy transition from one file format to another we have included tojson.py and tojsonl.py.

Convert a JSONl(V1 Format) file to JSON(V2 format)
~~~
python3 tojson.py <your_jsonl_file> <target_json_filename>
~~~
Convert a JSON(V2 Format) file to JSONL(V1 format)
~~~
python3 tojsonl.py <your_json_file> <target_jsonl_filename>
~~~

Additionally, you can use converttowellformed.py to take an existing slice of the dataset and narrow it to only queries that have a well formed answer. Usage bellow.

~~~
python3 converttowellformed.py <your_input_file(json)> <target_json_filename>
~~~

### Dataset Statistics
Statistics about the dataset were generated with the exploredata.py file. They can be found in the Stats folder. 
You can use the explore datafile to generate similiar statistics on any slice you create of the dataset.

~~~
python3 exploredata.py <your_input_file(json)> <-p if you are using a dataslice without answers>
~~~

### Evaluation
Evaluation of systems will be done using the industry standard BLEU and ROUGE-L. These are far from perfect but have been the best option we have found that scales. If you know of a better metric or want to brainstorm creating one please contact us.

We have made the official evaluation script along with a sample output file on the dev set available for download as well so that you can evaluate your models. [Download the evaluation scripts](https://github.com/dfcf93/MSMARCOV2/tree/master/Q%2BA/Evaluation) The evaluation script takes as inputs a reference and candidate output file. You can execute the evaluation script to evaluate your models as follows:
./run.sh <path to reference json file> <path to candidate json file> 
	
### Leaderboard Results
To Help Teams iterate we are making the results of official submissions on our evaluation script(the scores, not the full submissions) available. We will update these files as we update metrics and as new submissions come in. They can be found in the [Leaderboard Results](https://github.com/dfcf93/MSMARCOV2/tree/master/Q%2BA/Leaderboard%20Results) folder.

### Submissions
Once you have built a model that meets your expectations on evaluation with the dev set, you can submit your test results to get official evaluation on the test set. To ensure the integrity of the official test results, we do not release the correct answers for test set to the public. To submit your model for official evaluation on the test set, follow the below steps:
Run the evaluation script on the test set and generate the output results file for submission
Submit the following information by [contacting us](mailto:ms-marco@microsoft.com?subject=MS Marco Submission)
Individual/Team Name: Name of the individual or the team to appear in the leaderboard [Required]
Individual/Team Institution: Name of the institution of the individual or the team to appear in the leaderboard [Optional]
Model information: Name of the model/technique to appear in the leaderboard [Required]
Paper Information: Name, Citation, URL of the paper if model is from a published work to appear in the leaderboard [Optional]

Please submit your results either in json or jsonl format and ensure that each answer you are providing has its refrence query_id and query_text. If your model does not have query_id and query_text it is difficult/impossible to evalutate the submission.
To avoid "P-hacking" we discourage too many submissions from the same group in a short period of time. Because submissions don't require the final trained model we also retain the right to request a model to validate the results being submitted
### Run baseline systems on multilingual datasets
To encourage competitors to generate performant systems regardless of the langauge we recommend teams also test their systems on datasets in other langauges such as Baidu's DuReader.

[DuReader](https://ai.baidu.com/broad/subordinate?dataset=dureader) is a Chinese dataset focused on machine reading comprehension and question answering. Its design and area of focus is very similair to that of MSMARCO. The DuReader team has created scripts to allow DuReader system to use msmarco data and we have created scripts to allow MSMARCO teams to use DuReader data. We Strongly recommend training and testing your system with both datasets. We are in the process of creating an analysis tool that would take results to both systems and debug the wins/losses. 

To download the DuReader Data navigate to their [Git Repo](https://github.com/baidu/DuReader) and follow their instructions to download the data. After you have downloaded and processed the data you can run our converter scripts to turn the data into MSMARCO format as below.
'''
python3 duread_to_msmarco.py ~/Data/dureader/train/search.train.json ~/Data/dureader/train/search.train.msmarcoformat.json
'''

We have not experimented with how model perform using [Transfer Learning](http://cs231n.github.io/transfer-learning/) but are excited to see what the community finds.


# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
