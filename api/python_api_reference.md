# DRAFT Python API Reference

**THE API REFERENCES BELOW ARE STILL UNDER DEVELOPMENT.**

---

:::tip NOTE
Dataset Management
:::

---

## Create dataset

```python
RAGFlow.create_dataset(
    name: str,
    avatar: str = "",
    description: str = "",
    language: str = "English",
    permission: str = "me", 
    document_count: int = 0,
    chunk_count: int = 0,
    chunk_method: str = "naive",
    parser_config: DataSet.ParserConfig = None
) -> DataSet
```

Creates a dataset.

### Parameters

#### name: `str`, *Required*

The unique name of the dataset to create. It must adhere to the following requirements:

- Permitted characters include:
  - English letters (a-z, A-Z)
  - Digits (0-9)
  - "_" (underscore)
- Must begin with an English letter or underscore.
- Maximum 65,535 characters.
- Case-insensitive.

#### avatar: `str`

Base64 encoding of the avatar. Defaults to `""`

#### description: `str`

A brief description of the dataset to create. Defaults to `""`.

#### language: `str`

The language setting of the dataset to create. Available options:

- `"English"` (Default)
- `"Chinese"`

#### permission

Specifies who can access the dataset to create. You can set it only to `"me"` for now.

#### chunk_method, `str`

The chunking method of the dataset to create. Available options:

- `"naive"`: General (default)
- `"manual`: Manual
- `"qa"`: Q&A
- `"table"`: Table
- `"paper"`: Paper
- `"book"`: Book
- `"laws"`: Laws
- `"presentation"`: Presentation
- `"picture"`: Picture
- `"one"`:One
- `"knowledge_graph"`: Knowledge Graph
- `"email"`: Email

#### parser_config

The parser configuration of the dataset. A `ParserConfig` object contains the following attributes:

- `chunk_token_count`: Defaults to `128`.
- `layout_recognize`: Defaults to `True`.
- `delimiter`: Defaults to `"\n!?。；！？"`.
- `task_page_size`: Defaults to `12`.

### Returns

- Success: A `dataset` object.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.create_dataset(name="kb_1")
```

---

## Delete datasets

```python
RAGFlow.delete_datasets(ids: list[str] = None)
```

Deletes specified datasets or all datasets in the system.

### Parameters

#### ids: `list[str]`

The IDs of the datasets to delete. Defaults to `None`. If not specified, all datasets in the system will be deleted.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
rag_object.delete_datasets(ids=["id_1","id_2"])
```

---

## List datasets

```python
RAGFlow.list_datasets(
    page: int = 1, 
    page_size: int = 1024, 
    orderby: str = "create_time", 
    desc: bool = True,
    id: str = None,
    name: str = None
) -> list[DataSet]
```

Retrieves a list of datasets.

### Parameters

#### page: `int`

Specifies the page on which the datasets will be displayed. Defaults to `1`.

#### page_size: `int`

The number of datasets on each page. Defaults to `1024`.

#### orderby: `str`

The field by which datasets should be sorted. Available options:

- `"create_time"` (default)
- `"update_time"`

#### desc: `bool`

Indicates whether the retrieved datasets should be sorted in descending order. Defaults to `True`.

#### id: `str`

The ID of the dataset to retrieve. Defaults to `None`.

#### name: `str`

The name of the dataset to retrieve. Defaults to `None`.

### Returns

- Success: A list of `DataSet` objects.
- Failure: `Exception`.

### Examples

#### List all datasets

```python
for dataset in rag_object.list_datasets():
    print(dataset)
```

#### Retrieve a dataset by ID

```python
dataset = rag_object.list_datasets(id = "id_1")
print(dataset[0])
```

---

## Update dataset

```python
DataSet.update(update_message: dict)
```

Updates configurations for the current dataset.

### Parameters

#### update_message: `dict[str, str|int]`, *Required*

A dictionary representing the attributes to update, with the following keys:

- `"name"`: `str` The name of the dataset to update.
- `"embedding_model"`: `str` The embedding model name to update.
  - Ensure that `"chunk_count"` is `0` before updating `"embedding_model"`.
- `"chunk_method"`: `str` The chunking method for the dataset. Available options:
  - `"naive"`: General
  - `"manual`: Manual
  - `"qa"`: Q&A
  - `"table"`: Table
  - `"paper"`: Paper
  - `"book"`: Book
  - `"laws"`: Laws
  - `"presentation"`: Presentation
  - `"picture"`: Picture
  - `"one"`:One
  - `"knowledge_graph"`: Knowledge Graph
  - `"email"`: Email

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.list_datasets(name="kb_name")
dataset.update({"embedding_model":"BAAI/bge-zh-v1.5", "chunk_method":"manual"})
```

---

:::tip API GROUPING
File Management within Dataset
:::

---

## Upload documents

```python
DataSet.upload_documents(document_list: list[dict])
```

Uploads documents to the current dataset.

### Parameters

#### document_list: `list[dict]`, *Required*

A list of dictionaries representing the documents to upload, each containing the following keys:

- `"display_name"`: (Optional) The file name to display in the dataset.  
- `"blob"`: (Optional) The binary content of the file to upload.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
dataset = rag_object.create_dataset(name="kb_name")
dataset.upload_documents([{"display_name": "1.txt", "blob": "<BINARY_CONTENT_OF_THE_DOC>"}, {"display_name": "2.pdf", "blob": "<BINARY_CONTENT_OF_THE_DOC>"}])
```

---

## Update document

```python
Document.update(update_message:dict)
```

Updates configurations for the current document.

### Parameters

#### update_message: `dict[str, str|dict[]]`, *Required*

A dictionary representing the attributes to update, with the following keys:

- `"name"`: `str` The name of the document to update.
- `"parser_config"`: `dict[str, Any]` The parsing configuration for the document:
  - `"chunk_token_count"`: Defaults to `128`.
  - `"layout_recognize"`: Defaults to `True`.
  - `"delimiter"`: Defaults to `'\n!?。；！？'`.
  - `"task_page_size"`: Defaults to `12`.
- `"chunk_method"`: `str` The parsing method to apply to the document.
  - `"naive"`: General
  - `"manual`: Manual
  - `"qa"`: Q&A
  - `"table"`: Table
  - `"paper"`: Paper
  - `"book"`: Book
  - `"laws"`: Laws
  - `"presentation"`: Presentation
  - `"picture"`: Picture
  - `"one"`: One
  - `"knowledge_graph"`: Knowledge Graph
  - `"email"`: Email

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.list_datasets(id='id')
dataset = dataset[0]
doc = dataset.list_documents(id="wdfxb5t547d")
doc = doc[0]
doc.update([{"parser_config": {"chunk_token_count": 256}}, {"chunk_method": "manual"}])
```

---

## Download document

```python
Document.download() -> bytes
```

Downloads the current document.

### Returns

The downloaded document in bytes.

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.list_datasets(id="id")
dataset = dataset[0]
doc = dataset.list_documents(id="wdfxb5t547d")
doc = doc[0]
open("~/ragflow.txt", "wb+").write(doc.download())
print(doc)
```

---

## List documents

```python
Dataset.list_documents(id:str =None, keywords: str=None, offset: int=0, limit:int = 1024,order_by:str = "create_time", desc: bool = True) -> list[Document]
```

Retrieves a list of documents from the current dataset.

### Parameters

#### id: `str`

The ID of the document to retrieve. Defaults to `None`.

#### keywords: `str`

The keywords to match document titles. Defaults to `None`.

#### offset: `int`

The starting index for the documents to retrieve. Typically used in confunction with `limit`. Defaults to `0`.

#### limit: `int`

The maximum number of documents to retrieve. Defaults to `1024`. A value of `-1` indicates that all documents should be returned.

#### orderby: `str`

The field by which documents should be sorted. Available options:

- `"create_time"` (default)
- `"update_time"`

#### desc: `bool`

Indicates whether the retrieved documents should be sorted in descending order. Defaults to `True`.

### Returns

- Success: A list of `Document` objects.
- Failure: `Exception`.

A `Document` object contains the following attributes:

- `id`: The document ID. Defaults to `""`.
- `name`: The document name. Defaults to `""`.
- `thumbnail`: The thumbnail image of the document. Defaults to `None`.
- `knowledgebase_id`: The dataset ID associated with the document. Defaults to `None`.
- `chunk_method` The chunk method name. Defaults to `""`. ?????naive??????
- `parser_config`: `ParserConfig` Configuration object for the parser. Defaults to `{"pages": [[1, 1000000]]}`.
- `source_type`: The source type of the document. Defaults to `"local"`.
- `type`: Type or category of the document???????????. Defaults to `""`.
- `created_by`: `str` The creator of the document. Defaults to `""`.
- `size`: `int` The document size in bytes. Defaults to `0`.
- `token_count`: `int` The number of tokens in the document. Defaults to `0`.
- `chunk_count`: `int` The number of chunks that the document is split into. Defaults to `0`.
- `progress`: `float` The current processing progress as a percentage. Defaults to `0.0`.
- `progress_msg`: `str` A message indicating the current progress status. Defaults to `""`.
- `process_begin_at`: `datetime` The start time of document processing. Defaults to `None`.
- `process_duation`: `float` Duration of the processing in seconds or minutes.??????? Defaults to `0.0`.
- `run`: `str` ?????????????????? Defaults to `"0"`.
- `status`: `str` ??????????????????? Defaults to `"1"`.

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag.create_dataset(name="kb_1")

filename1 = "~/ragflow.txt"
blob = open(filename1 , "rb").read()
dataset.upload_documents([{"name":filename1,"blob":blob}])
for doc in dataset.list_documents(keywords="rag", offset=0, limit=12):
    print(doc)
```

---

## Delete documents

```python
DataSet.delete_documents(ids: list[str] = None)
```

Deletes documents by ID.

### Parameters

#### ids: `list[list]`

The IDs of the documents to delete. Defaults to `None`. If not specified, all documents in the dataset will be deleted.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.list_datasets(name="kb_1")
dataset = dataset[0]
dataset.delete_documents(ids=["id_1","id_2"])
```

---

## Parse documents

```python
DataSet.async_parse_documents(document_ids:list[str]) -> None
```

### Parameters

#### document_ids: `list[str]`, *Required*

The IDs of the documents to parse.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.create_dataset(name="dataset_name")
documents = [
    {'name': 'test1.txt', 'blob': open('./test_data/test1.txt',"rb").read()},
    {'name': 'test2.txt', 'blob': open('./test_data/test2.txt',"rb").read()},
    {'name': 'test3.txt', 'blob': open('./test_data/test3.txt',"rb").read()}
]
dataset.upload_documents(documents)
documents = dataset.list_documents(keywords="test")
ids = []
for document in documents:
    ids.append(document.id)
dataset.async_parse_documents(ids)
print("Async bulk parsing initiated.")
```

---

## Stop parsing documents

```python
DataSet.async_cancel_parse_documents(document_ids:list[str])-> None
```

### Parameters

#### document_ids: `list[str]`, *Required*

The IDs of the documents for which parsing should be stopped.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.create_dataset(name="dataset_name")
documents = [
    {'name': 'test1.txt', 'blob': open('./test_data/test1.txt',"rb").read()},
    {'name': 'test2.txt', 'blob': open('./test_data/test2.txt',"rb").read()},
    {'name': 'test3.txt', 'blob': open('./test_data/test3.txt',"rb").read()}
]
dataset.upload_documents(documents)
documents = dataset.list_documents(keywords="test")
ids = []
for document in documents:
    ids.append(document.id)
dataset.async_parse_documents(ids)
print("Async bulk parsing initiated.")
dataset.async_cancel_parse_documents(ids)
print("Async bulk parsing cancelled.")
```

---

## List chunks

```python
Document.list_chunks(keywords: str = None, offset: int = 0, limit: int = -1, id : str = None) -> list[Chunk]
```

Retrieves a list of document chunks.

### Parameters

#### keywords: `str`  
  
List chunks whose name has the given keywords. Defaults to `None`

#### offset: `int`

The starting index for the chunks to retrieve. Defaults to `1`

#### limit  

The maximum number of chunks to retrieve.  Default: `30`

#### id

The ID of the chunk to retrieve. Default: `None`

### Returns

- Success: A list of `Chunk` objects.
- Failure: `Exception`.

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.list_datasets("123")
dataset = dataset[0]
dataset.async_parse_documents(["wdfxb5t547d"])
for chunk in doc.list_chunks(keywords="rag", offset=0, limit=12):
    print(chunk)
```

## Add chunk

```python
Document.add_chunk(content:str) -> Chunk
```

### Parameters

#### content: *Required*

The text content of the chunk.

#### important_keywords :`list[str]`

List the key terms or phrases that are significant or central to the chunk's content.

### Returns

chunk

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag.list_datasets(id="123")
dtaset = dataset[0]
doc = dataset.list_documents(id="wdfxb5t547d")
doc = doc[0]
chunk = doc.add_chunk(content="xxxxxxx")
```

---

## Delete chunk

```python
Document.delete_chunks(chunk_ids: list[str])
```

Deletes chunks by ID.

### Parameters

#### chunk_ids: `list[str]`

The IDs of the chunks to delete. Defaults to `None`. If not specified, all chunks of the current document will be deleted.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
ds = rag.list_datasets(id="123")
ds = ds[0]
doc = ds.list_documents(id="wdfxb5t547d")
doc = doc[0]
chunk = doc.add_chunk(content="xxxxxxx")
doc.delete_chunks(["id_1","id_2"])
```

---

## Update chunk

```python
Chunk.update(update_message: dict)
```

Updates content or configurations for the current chunk.

### Parameters

#### update_message: `dict[str, str|list[str]|int]` *Required*

A dictionary representing the attributes to update, with the following keys:

- `"content"`: `str` Content of the chunk.
- `"important_keywords"`: `list[str]` A list of key terms to attach to the chunk.
- `"available"`: `int` The chunk's availability status in the dataset. Value options:
  - `0`: Unavailable
  - `1`: Available

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
dataset = rag_object.list_datasets(id="123")
dataset = dataset[0]
doc = dataset.list_documents(id="wdfxb5t547d")
doc = doc[0]
chunk = doc.add_chunk(content="xxxxxxx")
chunk.update({"content":"sdfx..."})
```

---

## Retrieve chunks

```python
RAGFlow.retrieve(question:str="", datasets:list[str]=None, document=list[str]=None, offset:int=1, limit:int=30, similarity_threshold:float=0.2, vector_similarity_weight:float=0.3, top_k:int=1024,rerank_id:str=None,keyword:bool=False,higlight:bool=False) -> list[Chunk]
```

### Parameters

#### question: `str` *Required*

The user query or query keywords. Defaults to `""`.

#### datasets: `list[str]`, *Required*

The datasets to search from.

#### document: `list[str]`

The documents to search from. `None` means no limitation. Defaults to `None`.

#### offset: `int`

The starting index for the documents to retrieve. Defaults to `0`??????.

#### limit: `int`

The maximum number of chunks to retrieve. Defaults to `6`.

#### Similarity_threshold: `float`

The minimum similarity score. Defaults to `0.2`.

#### similarity_threshold_weight: `float`

The weight of vector cosine similarity. Defaults to `0.3`. If x represents the vector cosine similarity, then (1 - x) is the term similarity weight.

#### top_k: `int`

The number of chunks engaged in vector cosine computaton. Defaults to `1024`.

#### rerank_id

The ID of the rerank model.  Defaults to `None`.

#### keyword

Indicates whether keyword-based matching is enabled:

- `True`: Enabled.
- `False`: Disabled.

#### highlight:`bool`

Specifying whether to enable highlighting of matched terms in the results (True) or not (False).

### Returns

- Success: A list of `Chunk` objects representing the document chunks.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
ds = rag_object.list_datasets(name="ragflow")
ds = ds[0]
name = 'ragflow_test.txt'
path = './test_data/ragflow_test.txt'
rag_object.create_document(ds, name=name, blob=open(path, "rb").read())
doc = ds.list_documents(name=name)
doc = doc[0]
ds.async_parse_documents([doc.id])
for c in rag_object.retrieve(question="What's ragflow?", 
             datasets=[ds.id], documents=[doc.id], 
             offset=1, limit=30, similarity_threshold=0.2, 
             vector_similarity_weight=0.3,
             top_k=1024
             ):
    print(c)
```

---

:::tip API GROUPING
Chat Assistant Management
:::

---

## Create chat assistant

```python
RAGFlow.create_chat(
    name: str, 
    avatar: str = "", 
    knowledgebases: list[str] = [], 
    llm: Chat.LLM = None, 
    prompt: Chat.Prompt = None
) -> Chat
```

Creates a chat assistant.

### Parameters

The following shows the attributes of a `Chat` object:

#### name: *Required*

The name of the chat assistant. Defaults to `"assistant"`.

#### avatar

Base64 encoding of the avatar. Defaults to `""`.

#### knowledgebases: `list[str]` 

The IDs of the associated datasets. Defaults to `[""]`.

#### llm

The llm of the created chat. Defaults to `None`. When the value is `None`, a dictionary with the following values will be generated as the default.

An `LLM` object contains the following attributes:

- `model_name`, `str`  
  The chat model name. If it is `None`, the user's default chat model will be returned.  
- `temperature`, `float`  
  Controls the randomness of the model's predictions. A lower temperature increases the model's conficence in its responses; a higher temperature increases creativity and diversity. Defaults to `0.1`.  
- `top_p`, `float`  
  Also known as “nucleus sampling”, this parameter sets a threshold to select a smaller set of words to sample from. It focuses on the most likely words, cutting off the less probable ones. Defaults to `0.3`  
- `presence_penalty`, `float`  
  This discourages the model from repeating the same information by penalizing words that have already appeared in the conversation. Defaults to `0.2`.
- `frequency penalty`, `float`  
  Similar to the presence penalty, this reduces the model’s tendency to repeat the same words frequently. Defaults to `0.7`.
- `max_token`, `int`  
  This sets the maximum length of the model’s output, measured in the number of tokens (words or pieces of words). Defaults to `512`.

#### Prompt

Instructions for the LLM to follow.  A `Prompt` object contains the following attributes:

- `"similarity_threshold"`: `float` A similarity score to evaluate distance between two lines of text. It's weighted keywords similarity and vector cosine similarity. If the similarity between query and chunk is less than this threshold, the chunk will be filtered out. Defaults to `0.2`.
- `"keywords_similarity_weight"`: `float` It's weighted keywords similarity and vector cosine similarity or rerank score (0~1). Defaults to `0.7`.
- `"top_n"`: `int` Not all the chunks whose similarity score is above the 'similarity threshold' will be feed to LLMs. LLM can only see these 'Top N' chunks. Defaults to `8`.
- `"variables"`: `list[dict[]]` If you use dialog APIs, the variables might help you chat with your clients with different strategies. The variables are used to fill in the 'System' part in prompt in order to give LLM a hint. The 'knowledge' is a very special variable which will be filled-in with the retrieved chunks. All the variables in 'System' should be curly bracketed. Defaults to `[{"key": "knowledge", "optional": True}]`
- `"rerank_model"`: `str` If it is not specified, vector cosine similarity will be used; otherwise, reranking score will be used. Defaults to `""`.
- `"empty_response"`: `str` If nothing is retrieved in the dataset for the user's question, this will be used as the response. To allow the LLM to improvise when nothing is retrieved, leave this blank. Defaults to `None`.
- `"opener"`: `str` The opening greeting for the user. Defaults to `"Hi! I am your assistant, can I help you?"`.
- `"show_quote`: `bool` Indicates whether the source of text should be displayed Defaults to `True`.
- `"prompt"`: `str` The prompt content. Defaults to `You are an intelligent assistant. Please summarize the content of the dataset to answer the question. Please list the data in the knowledge base and answer in detail. When all knowledge base content is irrelevant to the question, your answer must include the sentence "The answer you are looking for is not found in the knowledge base!" Answers need to consider chat history.
      Here is the knowledge base:
      {knowledge}
      The above is the knowledge base.`.

### Returns

- Success: A `Chat` object representing the chat assistant.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
kbs = rag.list_datasets(name="kb_1")
list_kb=[]
for kb in kbs:
    list_kb.append(kb.id)
assi = rag.create_chat("Miss R", knowledgebases=list_kb)
```

---

## Update chat

```python
Chat.update(update_message: dict)
```

Updates configurations for the current chat assistant.

### Parameters

#### update_message: `dict[str, str|list[str]|dict[]]`, *Required*

A dictionary representing the attributes to update, with the following keys:

- `"name"`: `str` The name of the chat assistant to update.
- `"avatar"`: `str` Base64 encoding of the avatar. Defaults to `""`
- `"knowledgebases"`: `list[str]` The datasets to update.
- `"llm"`: `dict` The LLM settings:
  - `"model_name"`, `str` The chat model name.
  - `"temperature"`, `float` Controls the randomness of the model's predictions.  
  - `"top_p"`, `float` Also known as “nucleus sampling”, this parameter sets a threshold to select a smaller set of words to sample from.  
  - `"presence_penalty"`, `float` This discourages the model from repeating the same information by penalizing words that have appeared in the conversation.
  - `"frequency penalty"`, `float` Similar to presence penalty, this reduces the model’s tendency to repeat the same words.
  - `"max_token"`, `int` This sets the maximum length of the model’s output, measured in the number of tokens (words or pieces of words).
- `"prompt"` : Instructions for the LLM to follow.
  - `"similarity_threshold"`: `float` A score to evaluate distance between two lines of text. It's weighted keywords similarity and vector cosine similarity. If the similarity between query and chunk is less than this threshold, the chunk will be filtered out. Defaults to `0.2`.
  - `"keywords_similarity_weight"`: `float` It's weighted keywords similarity and vector cosine similarity or rerank score (0~1). Defaults to `0.7`.
  - `"top_n"`: `int` Not all the chunks whose similarity score is above the 'similarity threshold' will be feed to LLMs. LLM can only see these 'Top N' chunks. Defaults to `8`.
  - `"variables"`: `list[dict[]]` If you use dialog APIs, the variables might help you chat with your clients with different strategies. The variables are used to fill in the 'System' part in prompt in order to give LLM a hint. The 'knowledge' is a very special variable which will be filled-in with the retrieved chunks. All the variables in 'System' should be curly bracketed. Defaults to `[{"key": "knowledge", "optional": True}]`
  - `"rerank_model"`: `str` If it is not specified, vector cosine similarity will be used; otherwise, reranking score will be used. Defaults to `""`.
  - `"empty_response"`: `str` If nothing is retrieved in the dataset for the user's question, this will be used as the response. To allow the LLM to improvise when nothing is retrieved, leave this blank. Defaults to `None`.
  - `"opener"`: `str` The opening greeting for the user. Defaults to `"Hi! I am your assistant, can I help you?"`.
  - `"show_quote`: `bool` Indicates whether the source of text should be displayed Defaults to `True`.
  - `"prompt"`: `str` The prompt content. Defaults to `You are an intelligent assistant. Please summarize the content of the knowledge base to answer the question. Please list the data in the knowledge base and answer in detail. When all knowledge base content is irrelevant to the question, your answer must include the sentence "The answer you are looking for is not found in the knowledge base!" Answers need to consider chat history.
      Here is the knowledge base:
      {knowledge}
      The above is the knowledge base.`.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
knowledge_base = rag.list_datasets(name="kb_1")
assistant = rag.create_chat("Miss R", knowledgebases=knowledge_base)
assistant.update({"name": "Stefan", "llm": {"temperature": 0.8}, "prompt": {"top_n": 8}})
```

---

## Delete chats

```python
RAGFlow.delete_chats(ids: list[str] = None)
```

Deletes chat assistants by ID.

### Parameters

#### ids: `list[str]`

The IDs of the chat assistants to delete. Defaults to `None`. If not specified, all chat assistants in the system will be deleted.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
rag.delete_chats(ids=["id_1","id_2"])
```

---

## List chats

```python
RAGFlow.list_chats(
    page: int = 1, 
    page_size: int = 1024, 
    orderby: str = "create_time", 
    desc: bool = True,
    id: str = None,
    name: str = None
) -> list[Chat]
```

Retrieves a list of chat assistants.

### Parameters

#### page

Specifies the page on which the chat assistants will be displayed. Defaults to `1`.

#### page_size

The number of chat assistants on each page. Defaults to `1024`.

#### order_by

The attribute by which the results are sorted. Defaults to `"create_time"`.

#### desc

Indicates whether the retrieved chat assistants should be sorted in descending order. Defaults to `True`.

#### id: `string`  

The ID of the chat to retrieve. Defaults to `None`.

#### name: `string`  

The name of the chat to retrieve. Defaults to `None`.

### Returns

- Success: A list of `Chat` objects.
- Failure: `Exception`.

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
for assistant in rag_object.list_chats():
    print(assistant)
```

---

:::tip API GROUPING
Chat-session APIs
:::

---

## Create session

```python
Chat.create_session(name: str = "New session") -> Session
```

Creates a chat session.

### Parameters

#### name

The name of the chat session to create.

### Returns

- Success: A `Session` object containing the following attributes:
  - `id`: `str` The auto-generated unique identifier of the created session.
  - `name`: `str` The name of the created session.
  - `message`: `list[Message]` The messages of the created session assistant. Default: `[{"role": "assistant", "content": "Hi! I am your assistant，can I help you?"}]`
  - `chat_id`: `str` The ID of the associated chat assistant.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
assistant = rag.list_chats(name="Miss R")
assistant = assistant[0]
session = assistant.create_session()
```

## Update session

```python
Session.update(update_message: dict)
```

Updates the current session name.

### Parameters

#### update_message: `dict[str, Any]`, *Required*

A dictionary representing the attributes to update, with only one key:

- `"name"`: `str` The name of the session to update.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
assistant = rag.list_chats(name="Miss R")
assistant = assistant[0]
session = assistant.create_session("session_name")
session.update({"name": "updated_name"})
```

---

## Chat

```python
Session.ask(question: str, stream: bool = False) -> Optional[Message, iter[Message]]
```

Asks a question to start a conversation.

### Parameters

#### question *Required*

The question to start an AI chat. Defaults to `None`.

#### stream

Indicates whether to output responses in a streaming way:

- `True`: Enable streaming.
- `False`: (Default) Disable streaming.

### Returns

- A `Message` object containing the response to the question if `stream` is set to `False`
- An iterator containing multiple `message` objects (`iter[Message]`) if `stream` is set to `True`

The following shows the attributes of a `Message` object:

#### id: `str`

The auto-generated message ID.

#### content: `str`

The content of the message. Defaults to `"Hi! I am your assistant, can I help you?"`.

#### reference: `list[Chunk]`

A list of `Chunk` objects representing references to the message, each containing the following attributes:

- `id` `str`  
  The chunk ID.
- `content` `str`  
  The content of the chunk.
- `image_id` `str`  
  The ID of the snapshot of the chunk.
- `document_id` `str`  
  The ID of the referenced document.
- `document_name` `str`  
  The name of the referenced document.
- `position` `list[str]`  
  The location information of the chunk within the referenced document.
- `knowledgebase_id` `str`  
  The ID of the dataset to which the referenced document belongs.
- `similarity` `float`
  A composite similarity score of the chunk ranging from `0` to `1`, with a higher value indicating greater similarity.
- `vector_similarity` `float`  
  A vector similarity score of the chunk ranging from `0` to `1`, with a higher value indicating greater similarity between vector embeddings.
- `term_similarity` `float`  
  A keyword similarity score of the chunk ranging from `0` to `1`, with a higher value indicating greater similarity between keywords.


### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
assistant = rag.list_chats(name="Miss R")
assistant = assistant[0]
session = assistant.create_session()    

print("\n==================== Miss R =====================\n")
print(assistant.get_prologue())

while True:
    question = input("\n==================== User =====================\n> ")
    print("\n==================== Miss R =====================\n")
    
    cont = ""
    for ans in session.ask(question, stream=True):
        print(answer.content[len(cont):], end='', flush=True)
        cont = answer.content
```

---

## List sessions

```python
Chat.list_sessions(
    page: int = 1, 
    page_size: int = 1024, 
    orderby: str = "create_time", 
    desc: bool = True,
    id: str = None,
    name: str = None
) -> list[Session]
```

Lists sessions associated with the current chat assistant.

### Parameters

#### page

Specifies the page on which the sessions will be displayed. Defaults to `1`.

#### page_size

The number of sessions on each page. Defaults to `1024`.

#### orderby

The field by which sessions should be sorted. Available options:

- `"create_time"` (default)
- `"update_time"`

#### desc

Indicates whether the retrieved sessions should be sorted in descending order. Defaults to `True`.

#### id

The ID of the chat session to retrieve. Defaults to `None`.

#### name

The name of the chat to retrieve. Defaults to `None`.

### Returns

- Success: A list of `Session` objects associated with the current chat assistant.
- Failure: `Exception`.

### Examples

```python
from ragflow import RAGFlow

rag_object = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
assistant = rag_object.list_chats(name="Miss R")
assistant = assistant[0]
for session in assistant.list_sessions():
    print(session)
```

---

## Delete sessions

```python
Chat.delete_sessions(ids:list[str] = None)
```

Deletes sessions by ID.

### Parameters

#### ids: `list[str]`

The IDs of the sessions to delete. Defaults to `None`. If not specified, all sessions associated with the current chat assistant will be deleted.

### Returns

- Success: No value is returned.
- Failure: `Exception`

### Examples

```python
from ragflow import RAGFlow

rag = RAGFlow(api_key="<YOUR_API_KEY>", base_url="http://<YOUR_BASE_URL>:9380")
assistant = rag.list_chats(name="Miss R")
assistant = assistant[0]
assistant.delete_sessions(ids=["id_1","id_2"])
```