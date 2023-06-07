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
