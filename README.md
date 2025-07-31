# GraphRAG
# Exploring & Implementing Graph RAG with Microsoft's GraphRAG framework & ollama

## **Required installtions**
 - Python 3.10.X - 3.12.X
 - Ollama
 - WSL2

### Enviroment is running on **python 3.12.1**


## **Steps**
#### After initializing the Virtual enviroment
  1. Choose Python 3.12.1 for the enviroment
  2. Install Microsoft's GraphRAG package
```Bash
  pip install GraphRAG
  ```
  3. Make directory for testing, Including the input where documents to be processed are stored
```Bash
  mkdir -p ./ragtest/input
  ```
  4. Get a copy of A Christmas Carol by Charles Dickens and (ألف ليله و ليله)
```Bash
  curl https://www.gutenberg.org/cache/epub/24022/pg24022.txt -o ./ragtest/input/En_book.txt

  curl https://raw.githubusercontent.com/OmarSaad-png/.ignore/refs/heads/main/%D8%A3%D9%84%D9%81%20%D9%84%D9%8A%D9%84%D9%87%20%D9%88%20%D9%84%D9%8A%D9%84%D9%87.txt -o ./ragtest/input/Ar_book.txt
  ```
  5. To initialize your workspace
```Bash
  graphrag init --root ./ragtest
  ```
This will create two files: .env and settings.yaml in the ./ragtest directory.
  - settings.yaml contains the settings for the pipeline
  - .env contains the <API_KEY> variable
## For local GPU acceleration on windows ollama should be ran in WSL2
  6. Get LLM model from ollama
```Bash
  ollama pull gemma:2b
  ```
*Can be substituted with gemma3:27b for optimal performance*

  7. Ensuring ollama model is running
```Bash
  ollama run gemma:2b
  ```
*Or gemma3:27b based on previous step*

  8. Get embedding model from ollama
```Bash
  ollama pull bge-m3
```
*As of now, **BGE-M3** is the most recommended and high-quality multilingual embedding model available through Ollama.*

  9. To ensure both models are running, Run
```Bash
  ollama list
```


### **Editing the config file**
  **Key changes made**

- Changed **api_base** to Ollama's OpenAI-compatible endpoint (/v1) *Mostly will be:* http://localhost:11434/v1
- Set **api_key** to "dummy" (Ollama doesn't require authentication)
- Changed model names to Ollama model format
- Disabled **rate limiting** with null values
- Adjuasted **request timeout** according to hardware capabilities
- Enabled **extract_claims** is enabled
- Optimize **max_gleanings** depending on hardware capabilitites
  - Max gleaning is often a major contributer to the quality of the output graphDB, Where it stands for the number of iterations done on the document in the graph extracting phase where with each iteration the information from the previous glean is used for the new one
- Ensure **graphml snapshots** is enabled to enable DB visualization
- Set **chunking strategy** to **sentence**
- Set the **max_input_length** in **community_reports** to match the context window of the used LLM
- Change **Cluster size** where smaller clusters enable the diversity of communities and increase their number

Refer to [Configuration guide](https://microsoft.github.io/graphrag/config/yaml/) for a detailed guide of **setting.yaml** config file

## Initializing pipeline
```Bash
graphrag index --root ./ragtest
```
### Validating the graphDB, Run
```Python
import networkx as nx
G = nx.read_graphml("output/graph.graphml")
print("Nodes:", G.number_of_nodes())
print("Edges:", G.number_of_edges())
print("Sample nodes:", list(G.nodes(data=True))[:5])
```
#### This should preview the number of **nodes** and **edges** (Entities, Relationships respectively) and a sample of 5 nodes
#### For visualization follow Microsoft's [Visualization guide](https://microsoft.github.io/graphrag/visualization_guide/) using Gephi


### **The primary query modes are**

**Global Search** for reasoning about holistic questions about the corpus by leveraging the community summaries.

**Local Search** for reasoning about specific entities by fanning-out to their neighbors and associated concepts.

**Drift Search** for reasoning about specific entities by fanning-out to their neighbors and associated concepts, but with the added context of community information.

**Basic Search** for Standard vector-based retrieval

#### *“community” typically refers to a group of related entities that are interconnected through various relationships or attributes. These entities could be people, organizations, concepts, or any other type of data object.*

## Queries examples
Using global search mode
```Bash
graphrag query \
--root ./ragtest \
--method global \
--query "What are the top themes in this story?"
```

Using local search mode
```Bash
graphrag query \
--root ./ragtest \
--method local \
--query "Who is Scrooge and what are his main relationships?"
```
### Download query example file, Run
```Bash
curl https://raw.githubusercontent.com/OmarSaad-png/.ignore/refs/heads/main/Example_Queries.txt -o ./ragtest/input/Example_Queries.txt
```
## Prompts
### For optimal graph extraction and query responses prompts should be optimized for the document's domain, More focused prompts steer the model away of halucinations

___

## Resources
  [Microsoft's GraphRAG documentation](https://microsoft.github.io/graphrag/)

  [Microsoft's GraphRAG guide](https://microsoft.github.io/graphrag/get_started/)

  [Auto Prompt Tuning](https://microsoft.github.io/graphrag/prompt_tuning/auto_prompt_tuning/)

  Medium paper [What exactly happens in Microsoft’s GraphRAG that makes it so unique?](https://medium.com/@divi.pothukuchi/what-exactly-happens-in-microsofts-graphrag-that-makes-it-so-unique-13ca7d93acf0)
    
