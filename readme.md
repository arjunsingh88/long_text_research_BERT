- [1. Long text (Research)](#1-long-text-research)
  - [1.1. Define the approach: new model + {logic for long text}](#11-define-the-approach-new-model--logic-for-long-text)
  - [1.2. Load the newly finetuned - trained model](#12-load-the-newly-finetuned---trained-model)
  - [1.3. NLP AI - Pipeline functions:](#13-nlp-ai---pipeline-functions)
  - [1.4. Build Pipeline function to run everything](#14-build-pipeline-function-to-run-everything)
  - [1.5. Flow-chart](#15-flow-chart)

<br>
</br>



# 1. Long text (Research)
The focus of this research was to identify the best way to process long text for our sentiment classifier
- `Current Logic` : Iterating over sentences in summary and computing sentiments for individual statments
- `Proposed Logic` : Iterating over sentences in summary/complete text computing sentiments based on what logic needs to be implemented as per the following flowchart

## 1.1. Define the approach: new model + {logic for long text}
We load the newly finetuned model available to us or use the pretrained from Huggingface library. For long text we need to define the approach we gonna follow. based on that there are 6 notebooks corresponding to 6 logics proposed to handle large text
## 1.2. Load the newly finetuned - trained model
Newly trained model along with the newly trained tokenizer are ready to be used
## 1.3. NLP AI - Pipeline functions:
 - ### 1.3.1. Cleaning Function
    The purpose of this is to clean the text of irrelevant special character which are there in text. primary reason they are there is due to the fact that we use selenium as one of the logic to scrape data and it does tend to scrape html tags  
 - ### 1.3.2. Custom functions for the logic
      - #### Extracting Summary (Abstractive vs Extractive)
        This is an optional step only there for summarization logics, we use text rank and entity based text ranking, 2 approaches with each having its pros and cons.
        -  ##### [Abstractive Summary](long_sentence_abstrative_summary.ipynb)
            The abstractive summary is the exisiting approach currently deployed in production. We use summa library to generate the summary of text.
        -  ##### [Extractive Summary](long_sentence_textrank.ipynb)
            The extractive summary, is the summary generated using textranking algorithm i.e. top-n sentences in the text. The approach can be further drilled down into top n sentences in text v/s top n entity based sentences. 
      - #### Generate 512 token Window Large text
        This is an optional step for large/complete text. We create input of 512 token (multiple windows for sliding window, one for head and tail)
        -  ##### [Head + Tail sentence](long_sentence_head_tail_sent.ipynb)
            The apprach uses the head and tail of the text i.e. 128 tokens from starting + 382 tokens from tail. The logic behind this approach is that main context of the article is either talked/introduced in starting and summarized in the end. After this we go sentence by sentence through this paragraph. 
        -  ##### [Head + Tail paragraph](long_sentence_head_tail_512win_para.ipynb)
            This approach same as before other than the fact we feed entire 512 token to sentiment model to compute the score.  
        -  ##### [512 Token Window sentence](long_sentence_new_model_sent.ipynb)
            This approach requires breaking large text into paragraphs of 512 tokens and then passing them throughsentiment model.  
        -  ##### [512 Token Window paragraph](long_sentence_new_model_512win.ipynb)
            Same approach as before but we go paragraph wise paragraph*  

 - ### 1.3.3. Compute Sentiment
  
    The sentiment model largely remains the same other than the fact that now it can do sentence by sentence and paragraph by paragraph sentiment computation
## 1.4. Build Pipeline function to run everything
Custom function to run everything, we are not using Transformers pipeline because we prefer our scores in range of 0 - 100 rather than label 1 - 5

## 1.5. Flow-chart
[![Programmatic approaches](https://mermaid.ink/img/pako:eNqFkkGPmzAQhf-K5WsTAoaAF6mVqg3btN29dHMq6cHBBqyAjWDYhCL-e22SrHJoVZ_s9z2_Gdkz4kxzgWOcV_qUlawFtNvsFTLrcXzWqkAgzjCh5fLTZnzt65q1w3TlVkzGR103lQCBdtZ4QRuLntLkDC3LQL4JdL3668KfLP-W2hvoB1NHqYp78j1NFEgYUK6zvhMc_cU4l_iSfj50_6iRWMM23QrG0Qe0Y7JCH9HaI-jKt5a_pNmtfYtAH4W651_TTigQKhPoMKDb_r7C_x3PqY3uKslN9-gkFdcntEQNa1nRsqa8evEC16KtmeTmN0ar7TGUohZ7HJstZ-1xj_dqMj7Wg34dVIZjaHuxwH3DGYiNtHk1jnNWde9qwiXo9l2sNOPCHEcMQ2P_vZAdmMhMq1wWVu_bysglQNPFq5XFTiGh7A-OeapVJ7kdkvLtIVyFJKSM-CKMfLb2fZ4dvAeak8DLeeR6hOFpWuCGqZ9a17dWxdzOy2Xm5tGbLbbwGcfUc6jnR1HguZTSMCILPOCYEOJ4lK6p70eBS_wgMLm_51DXMXIYhdRUpcT1w3D6A-zc4JQ?type=png)](https://mermaid.live/edit#pako:eNqFkkGPmzAQhf-K5WsTAoaAF6mVqg3btN29dHMq6cHBBqyAjWDYhCL-e22SrHJoVZ_s9z2_Gdkz4kxzgWOcV_qUlawFtNvsFTLrcXzWqkAgzjCh5fLTZnzt65q1w3TlVkzGR103lQCBdtZ4QRuLntLkDC3LQL4JdL3668KfLP-W2hvoB1NHqYp78j1NFEgYUK6zvhMc_cU4l_iSfj50_6iRWMM23QrG0Qe0Y7JCH9HaI-jKt5a_pNmtfYtAH4W651_TTigQKhPoMKDb_r7C_x3PqY3uKslN9-gkFdcntEQNa1nRsqa8evEC16KtmeTmN0ar7TGUohZ7HJstZ-1xj_dqMj7Wg34dVIZjaHuxwH3DGYiNtHk1jnNWde9qwiXo9l2sNOPCHEcMQ2P_vZAdmMhMq1wWVu_bysglQNPFq5XFTiGh7A-OeapVJ7kdkvLtIVyFJKSM-CKMfLb2fZ4dvAeak8DLeeR6hOFpWuCGqZ9a17dWxdzOy2Xm5tGbLbbwGcfUc6jnR1HguZTSMCILPOCYEOJ4lK6p70eBS_wgMLm_51DXMXIYhdRUpcT1w3D6A-zc4JQ)

Experiment results
| Approach |  Sentiment Score/100 |
|---|---|
| Abstractive Summary  | 38.10  |
| Head + Tail (complete paragraph) | 27.28  |
| Head + Tail (sentence-wise) | 35.81  |
| 512 Token window (complete paragraph) | 26.45  |
| 512 Token window (sentence-wise) | 38.10 |
| Text ranking Algorithm (Biased ranking) | 36.70  |

Results reveal a variations in score depending on what technique is used. using head + tail has resulted in low scores given article talks in very negative light about the company in question. Additionally, we are running off-the-shelf Sentiment model from hugging face. We would get much better results if we finetune it on our data (training example + domain-specific vocab) for even more precise results.

Data used

```python
html_text = "What is Volkswagen accused of?\nIt's been dubbed the \"diesel dupe\". In September, the Environmental Protection Agency (EPA) found that many VW cars being sold in America had a \"defeat device\" - or software - in diesel engines that could detect when they were being tested, changing the performance accordingly to improve results. The German car giant has since admitted cheating emissions tests in the US.\nVW has had a major push to sell diesel cars in the US, backed by a huge marketing campaign trumpeting its cars' low emissions. The EPA's findings cover 482,000 cars in the US only, including the VW-manufactured Audi A3, and the VW models Jetta, Beetle, Golf and Passat. But VW has admitted that about 11 million cars worldwide, including eight million in Europe, are fitted with the so-called \"defeat device\".\nThe company has also been accused by the EPA of modifying software on the 3 litre diesel engines fitted to some Porsche and Audi as well as VW models. VW has denied the claims, which affect at least 10,000 vehicles.\nIn November, VW said it had found \"irregularities\" in tests to measure carbon dioxide emissions levels that could affect about 800,000 cars in Europe - including petrol vehicles. However, in December it said that following investigations, it had established that this only affected about 36,000 of the cars it produces each year.\nThis 'defeat device' sounds like a sophisticated piece of kit.\nFull details of how it worked are sketchy, although the EPA has said that the engines had computer software that could sense test scenarios by monitoring speed, engine operation, air pressure and even the position of the steering wheel.\nWhen the cars were operating under controlled laboratory conditions - which typically involve putting them on a stationary test rig - the device appears to have put the vehicle into a sort of safety mode in which the engine ran below normal power and performance. Once on the road, the engines switched out of this test mode.\nThe result? The engines emitted nitrogen oxide pollutants up to 40 times above what is allowed in the US.\nWhat has been VW's response?\n\"We've totally screwed up,\" said VW America boss Michael Horn, while the group's chief executive at the time, Martin Winterkorn, said his company had \"broken the trust of our customers and the public\". Mr Winterkorn resigned as a direct result of the scandal and was replaced by Matthias Mueller, the former boss of Porsche.\n\"My most urgent task is to win back trust for the Volkswagen Group - by leaving no stone unturned,\" Mr Mueller said on taking up his new post.\nVW has also launched an internal inquiry.\nBut that's unlikely to be the end of the financial impact. The EPA has the power to fine a company up to $37,500 for each vehicle that breaches standards - a maximum fine of about $18bn.\nThe costs of possible legal action by car owners and shareholders \"cannot be estimated at the current time\", VW added.\nHow widespread are VW's problems?\nWhat started in the US has spread to a growing number of countries. The UK, Italy, France, South Korea, Canada and, of course, Germany, have opened investigations. Throughout the world, politicians, regulators and environmental groups are questioning the legitimacy of VW's emissions testing.\nVW will recall 8.5 million cars in Europe, including 2.4 million in Germany and 1.2 million in the UK, and 500,000 in the US as a result of the emissions scandal.\nNo wonder the carmaker's shares have fallen by about a third since the scandal broke.\nWill more heads roll?\nIt's still unclear who knew what and when, although VW must have had a chain of management command that approved fitting cheating devices to its engines, so further departures are likely.\nChristian Klingler, a management board member and head of sales and marketing is leaving the company, although VW said this was part of long-term planned structural changes and was not related to recent events.\nIn 2014, in the US, regulators raised concerns about VW emissions levels, but these were dismissed by the company as \"technical issues\" and \"unexpected\" real-world conditions. If executives and managers wilfully misled officials (or their own VW superiors) it's difficult to see them surviving.\nAre other carmakers implicated?\nThat's for the various regulatory and government inquiries to determine. California's Air Resources Board is now looking into other manufacturers' testing results. Ford, BMW and Renault-Nissan have said they did not use \"defeat devices\", while other firms have either not commented or simply stated that they comply with the law.\nThe UK trade body for the car industry, the SMMT, said: \"The EU operates a fundamentally different system to the US - with all European tests performed in strict conditions as required by EU law and witnessed by a government-appointed independent approval agency.\"\nBut it added: \"The industry acknowledges that the current test method is outdated and is seeking agreement from the European Commission for a new emissions test that embraces new testing technologies and is more representative of on-road conditions.\"\nThat sounds like EU testing rules need tightening, too.\nEnvironmental campaigners have long argued that emissions rules are being flouted. \"Diesel cars in Europe operate with worse technology on average than the US,\" said Jos Dings, from the pressure group Transport & Environment. \"Our latest report demonstrated that almost 90% of diesel vehicles didn't meet emission limits when they drive on the road. We are talking millions of vehicles.\"\nCar analysts at the financial research firm Bernstein agree that European standards are not as strict as those in the US. However, the analysts said in a report that there was, therefore, \"less need to cheat\". So, if other European carmakers' results are suspect, Bernstein says the \"consequences are likely to be a change in the test cycle rather than legal action and fines\".\nIt's all another blow for the diesel market.\nCertainly is. Over the past decade and more, carmakers have poured a fortune into the production of diesel vehicles - with the support of many governments - believing that they are better for the environment. Latest scientific evidence suggests that's not the case, and there are even moves to limit diesel cars in some cities.\nDiesel sales were already slowing, so the VW scandal came at a bad time. \"The revelations are likely to lead to a sharp fall in demand for diesel engine cars,\" said Richard Gane, automotive expert at consultants Vendigital.\n\"In the US, the diesel car market currently represents around 1% of all new car sales and this is unlikely to increase in the short to medium term.\n\"However, in Europe the impact could be much more significant, leading to a large tranche of the market switching to petrol engine cars virtually overnight.\""
summary = "In September, the Environmental Protection Agency (EPA) found that many VW cars being sold in America had a \"defeat device\" - or software - in diesel engines that could detect when they were being tested, changing the performance accordingly to improve results. VW has had a major push to sell diesel cars in the US, backed by a huge marketing campaign trumpeting its cars' low emissions. The company has also been accused by the EPA of modifying software on the 3 litre diesel engines fitted to some Porsche and Audi as well as VW models. In November, VW said it had found \"irregularities\" in tests to measure carbon dioxide emissions levels that could affect about 800,000 cars in Europe - including petrol vehicles. VW will recall 8.5 million cars in Europe, including 2.4 million in Germany and 1.2 million in the UK, and 500,000 in the US as a result of the emissions scandal."
title = "Volkswagen: The scandal explained - BBC News"
name = "Volkswagen"
```
