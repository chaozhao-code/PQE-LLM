# PQE-LLM
The official repository for "Retrieve, Represent, Generate: Personalized Query Expansion for Academic Sear"

## Data


We have released a real-world toy dataset for the community to evaluate the performance of personalized query expansion. The dataset contains 100 queries and their corresponding relevant documents (clicked by users). It is available at ```./data/query2doc.json```.

To protect our clients' privacy, we de-identified the dataset by removing any personal information. Specifically, we excluded users' publications and reading histories to prevent identification. Instead, we generated an author representation for each user, and this information is stored in ```./data/query2doc.json```.

Dense Retrieval System: ````./data/EID2EMBEDDING.json```` includes information on all relevant documents, such as titles, abstracts, and keywords. Additionally, we generated embeddings for each document. With this data, we can create an index to accelerate the retrieval process. The procedure for building an index is demonstrated in ```./data/build_index.ipynb```.



## Scritps


The implementation of the main methodology for a dummy example is illustrated in ```./scripts/demo.ipynb```, with the core implementation located in ```./scripts/utils.ipynb```.

PQE-LLM consists of the following components:

1. Author Representation: PQE-LLM retrieves the top-*k* (here, *k* = 1) documents that are most relevant to the user's query from the user's publications or reading history. Based on these documents, the LLM generates a sequence of keywords to represent the user's interests.

2. Query Expansion: PQE-LLM expands the user's query by generating a set of keywords based on the user's representation obtained in Step 1. These generated keywords are used to enhance the original query. For non-personalized query expansion, PQE-LLM simply prompts the LLM to answer the query.

3. Evaluation: PQE-LLM evaluates the performance of (non-)personalized query expansion using the expanded query (original query + expansions). The evaluation is conducted on the dense retrieval system. The evaluation metrics include NDCG@10, NDCG@20, MAP@10, Recall@10, Recall@50, Recall@200, Precision@10, Precision@100, and MRR.

The LLM used here is GPT-4-mini, but it can be easily replaced with other LLMs.

## Prompts
Prompts are located under ```./prompts```

## Case study


We present a case study to demonstrate the performance of different LLMs in query expansion. The user’s original query was `perceived benefits ebusiness`. As shown in the table below, the NDCG@10 score for the original query is 0, indicating that the dense retrieval system failed to retrieve any relevant documents in the top-10 results. 

The table highlights the personalized query expansion results across several models. GPT-4 provided expansion terms that successfully covered three distinct aspects of the perceived benefits of e-business: supply chain management, small and medium-sized enterprises (SMEs), and interoperable technological solutions. Among the open-source models, Phi and Llama improved the effectiveness by expanding the query for this case study.

| **Model**      | **Expansion Terms**                                                                                                                                                                                                                   | **NDCG@10** |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|
| **Original Q**  | _perceived benefits ebusiness_                                                                                                                                                                                                      | 0.00        |
| **GPT-4**       | - `Perceived benefits of electronic business in supply chain management`<br> - `Perceived benefits of electronic business for Small and Medium-sized Enterprises`<br> - `Perceived benefits of electronic business with interoperable technological solutions` | 1.00        |
| **GPT-3.5**     | - `perceived benefits of electronic business`<br> - `e-business interoperability`<br> - `SME e-collaboration`                                                                                                                       | 0.63        |
| **Claude**      | - `perceived benefits electronic business`<br> - `ebusiness adoption rates`<br> - `sme ebusiness adoption`<br> - `ebusiness value creation`                                                                                           | 0.63        |
| **Llama**       | - `online shopping advantages`<br> - `digital marketplaces benefits`<br> - `small business e-commerce`                                                                                                                              | 0.30        |
| **Mistral**     | - `benefits of e-business in supply chain management`<br> - `perceived advantages of e-collaboration in clothing industry`<br> - `impact of e-business on interoperability in SCM`                                                   | 0.00        |
| **Phi**         | - `E-business adoption`<br> - `Supply chain interoperability`<br> - `SME competitiveness`                                                                                                                                           | 0.63        |

Comparison of expansion terms and NDCG@10 for different models.
