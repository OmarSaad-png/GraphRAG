# GraphRAG
# Exploring & Implementing Graph RAG with Microsoft's GraphRAG framework & ollama

## **Required installtions**
 - Python 3.10.X - 3.12.X
 - Ollama

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
  4. Get a copy of A Christmas Carol by Charles Dickens
```Bash
  curl https://www.gutenberg.org/cache/epub/24022/pg24022.txt -o ./ragtest/input/book.txt
  ```
  5. To initialize your workspace
```Bash
  graphrag init --root ./ragtest
  ```
This will create two files: .env and settings.yaml in the ./ragtest directory.
  - settings.yaml contains the settings for the pipeline
  - .env contains the <API_KEY> variable

  6. Get LLM model from ollama
```Bash
  ollama pull gemma:7b
  ```
*Can be substituted with gemma:2b for lower performance impact*

  7. Ensuring ollama model is running
```Bash
  ollama run gemma:7b
  ```
*Or gemma:2b based on previous step*

  8. Get embedding model from ollama
```Bash
  ollama pull nomic-embed-text
```
*As of now, **nomic-embed-text** is the most recommended and high-quality embedding model available through Ollama.*

  9. To ensure both models are running, Run
```Bash
  ollama list
```


### **Editing the config file**
  **Key changes made**

- Changed api_base to Ollama's OpenAI-compatible endpoint (/v1) *Mostly will be:* http://localhost:11434/v1
- Set api_key to "dummy" (Ollama doesn't require authentication)
- Changed model names to Ollama model format
- Reduced concurrent_requests to 1 for local processing
- Disabled rate limiting with null values
- Adjuasted request timeout according to harware capabilities

  
## Initializing pipeline
```Bash
graphrag index --root ./ragtest
```


### **The primary query modes are**

**Global Search** for reasoning about holistic questions about the corpus by leveraging the community summaries.

**Local Search** for reasoning about specific entities by fanning-out to their neighbors and associated concepts.

**DRIFT Search** for reasoning about specific entities by fanning-out to their neighbors and associated concepts, but with the added context of community information.

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


## Resources
  [Microsoft's GraphRAG documentation](https://microsoft.github.io/graphrag/)

  [Microsoft's GraphRAG guide](https://microsoft.github.io/graphrag/get_started/)

  [Configuration guide](https://microsoft.github.io/graphrag/config/yaml/)

  [Auto Prompt Tuning](https://microsoft.github.io/graphrag/prompt_tuning/auto_prompt_tuning/)

  [Visualizing the knowledge graph](https://microsoft.github.io/graphrag/visualization_guide/)

Medium paper explaining Microsoft's GraphRAG:

  [What exactly happens in Microsoft’s GraphRAG that makes it so unique?](https://medium.com/@divi.pothukuchi/what-exactly-happens-in-microsofts-graphrag-that-makes-it-so-unique-13ca7d93acf0)
    
