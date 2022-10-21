# Correlations Between Risk and Returns during COVID Onset

Created as a project in Professor Bowen's FIN377 Class at Lehigh University

This assignment uncovers what types of firms were hurt most by the vast market drops across the world during the period of early March. In this study, I defined a type of firm by the sort of risks it faced, and attempted to quantify these risks by parsing 10K text for firms in the S&P500 that had public filings before the date 03/01/2020. The approach I took can be divided into 3 main parts:

1. [Downloading the 10K files](download_10Ks.ipynb)
2. [Measuring and quantifying the risk found in each 10K for each firm](measure_risk.ipynb)
3. [Analyzing risk findings, and correlating risk to market returns](analysis.ipynb)

## 1. Download 10K Files

This file begins with accessing a list of current firms in the SP500 from wikipedia, and saves the tickers for reference. I then made a method which, when given a stock ticker, fetches the most recently filed 10K before the cutoff date of 03/01/2020. If no documents meet our criteria, the method returns -1 to denote the ticker has no file of the sort. Of the 505 firms in the S&P500, 6 did not have filings. They are listed in the download 10K file. After all the proper URLs are fetched, the program then donwloads the 10K to the text_files folder, which is created in the same directory as the file location. This [screenshot](screenshot-10ks.png) shows the folder in my desktop with the 499 10K files after download, as this folder was gitignored. These urls and tickers are saved into a csv file. 

## 2. Measure Risk

In this file, each of the donwloaded 10Ks are scanned for instances of risk discussion. In order to determine the risks faced by each firm, I first attempted to hone in on the discussion of risk factos section found in most 10Ks. Labeled typically as 'item 1a', I searched the 10K in string format for all the occurences of this phrase, and returned their index locations within a list. Despite having this information, 'item 1a' usually occurs multiple times throughout the document, and I had to find a repeatable way to find the correct section header in each filing. To do this, I searched the surrounding area for words that were typically found at the top of the risk section. These included:
```
risk_ids = ['below', 'important', 'material', 'immaterial', 'consider', 'considered', 'consideration',
            'affect', 'effect', 'carefully', 'following', 'uncertainties', 'you', 'forth', 'adverse', 'adversely']
```
I came to this conclusion after reading many different 10K documents, and saw that the langauge within the first paragraph was largely the same. I also removed indicies from consideradion if the surrounding areas contained references to other items, as you would see in a table of contents. Contenders were also removed if they were located close to the phrase 'incorporated in', since this usually meant that the risk section was referenced breifly before the overall description of the business. Once I had found the correct starting index, I the searched for the first occurrence of 'item 1b' after this point, which meant that the risk section was over and that the next topic was beginning. I then saved a slice of the 10K string between these two indicies as the risk section.

This worked on most documents. However, there were a few that didn't use the usual convention of calling the risk section 'item 1a'. In this case, I instead scanned the entire 10K document for discussion of my chosen risk factors. I avoided doing this for every firm, and decided to go through the trouble of extracting the risk section since I assumed it would make my code faster, as it ultimately had less words to search through, and that most of the hits on each risk factor are more credible if they are discussed in the risk section rather than somewhere else in the document. As shown by the analysis of the 'doc_type' variable, which tracked if the risk section or the entire document was searched for each firm, my code was able to successfully find the risk section on most documents - 478 of 499.

This also depends on the assumption that the occurence of 'item 1a' it settled on is correct. I manually checked around 50 risk sections my code found, and they all were right, which lead me to believe that my code largely did this correctly. Even if an incorrect section was found, I used near_regex and made sure the occurrences of the words I searched for were still somewhat near negative words, so its possible the code would still be able to perform well even if using the wrong section. As the documents are being scaned, my program will randomly output a few risk sections it finds in order to ensure acuraccy. For firms that my code could find the risk section for, the maximum number of words between a risk word and a risk descriptor was 100 - the length of a few sentences. I made this measure lenient, since if a risk factor is at all mentioned in the risk section, it is likely a risk itself. The risk factors I settled on were as follows:

- Supply Chain
- Regulatory
- Inflationary
- Macroeconomic
- Debt and solvency
- Liquidity

I decided to search for these factors, since they were easy to measure using specific words or short phrases, had a reasonably large number of hits, and were applicable to most large companies regardless of sector. In order to find a discussion of each risk, I created an array of negative words:
```
risk_words = '(liability|liabilities|risk|risks|headwind|headwinds|concerning)'
```
And paired this with words that described each of the 6 factors above. These were:
```
supply_chain = '(supply|supply chain|logistical|supplier|suppliers)'
regulatory = '(regulation|regulatory|regulate)'
inflation = '(inflation|inflationary)'
macro = '(macroeconomic|macro economic|trade|trading|import|imports|importing|exchange)'
debt = '(debt|debts|interest payments|principal payments|repay|repay lenders|default|solvent|solvency)'
liquidity = '(liquid|liquidity)'
```
When combining these lists of risk words and risk factors, the program was able to distinuish approximately how many times a document discussed each risk.

## 3. Analysis

This file dives deeper into the risk factors found in the previous step, by performing a number of statiscal calculations, and producing graphs for different measures. I made this program pretty straightfoward, mostly using ```seaborn``` plots. When running this program, follow the comments in each cell and read the commentary as the program continues to a final conclusion. 

 ### Within the program, further explaination, printouts, graphs, and tables will be provided. It is important to use this information in conjunction with the readme. 

### Finally, thanks for reading through all this, and for Professor Bowen and Jason's help thorughout this project!
