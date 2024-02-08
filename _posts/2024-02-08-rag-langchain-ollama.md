---
layout: single
title: "RAG with Langchain and Ollama"
tag: [rag, langchain, ollama]
categories: [ml]
toc: true
sidebar:
    nav: "docs"
---

### import

```python
from langchain import hub
from langchain.llms import Ollama
from langchain.schema import StrOutputParser
from langchain.vectorstores import Chroma
from langchain.callbacks.manager import CallbackManager
from langchain.callbacks.streaming_stdout import StreamingStdOutCallbackHandler
from langchain.embeddings.sentence_transformer import SentenceTransformerEmbeddings
from langchain_core.runnables import RunnablePassthrough
from langchain.document_loaders import WebBaseLoader
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import CharacterTextSplitter
from langchain.text_splitter import RecursiveCharacterTextSplitter
```

### class - RAG with Langchain 

```python
class LCRAG:
    def __init__(
        self, 
        rag_prompt="rlm/rag-prompt", 
        llm_model=Ollama(model="dolphin-phi")
    ):
        # set rag prompt
        self.prompt = hub.pull(rag_prompt)
        
        # create the open-source embedding function
        self.set_embedding_function()
        
        # set LLM
        self.set_llm(llm_model)

    def set_embedding_function(self, model_name="all-MiniLM-L6-v2"):
        self.embedding_function = SentenceTransformerEmbeddings(model_name=model_name)

    def set_llm(self, model):
        self.llm = model

    def load_webdocs(self, web_paths):
        self.loader = WebBaseLoader(web_paths=web_paths)

    def load_pdf(self, pdf_path):
        self.loader = PyPDFLoader(pdf_path)
        
    def split_docs(
        self,
        chunk_size=1000,
        chunk_overlap=200
    ):
        self.text_splitter = RecursiveCharacterTextSplitter(
            chunk_size=chunk_size, chunk_overlap=chunk_overlap)
        self.splits = self.text_splitter.split_documents(self.loader.load())

    def set_vectorstore(self):
        # load it into Chroma
        self.db = Chroma.from_documents(self.splits, self.embedding_function)
        
    def set_rag_chain(self):
        self.retriever = self.db.as_retriever(
            search_type="similarity", search_kwargs={"k": 6})
        
        self.rag_chain = (
            {"context": self.retriever | self.format_docs, 
             "question": RunnablePassthrough()}
            | self.prompt
            | self.llm
            | StrOutputParser()
        )

    def format_docs(self, docs):
        return "\n\n".join(doc.page_content for doc in docs)

    def process(self):
        self.split_docs()
        self.set_vectorstore()
        self.set_rag_chain()

    def process_web(self, web_paths):
        self.load_webdocs(web_paths)
        self.process()

    def process_pdf(self, pdf_paths):
        self.load_pdf(pdf_paths)
        self.process()

    def get_answer(self, question):
        return self.rag_chain.invoke(question)
        
```

### Test

```python
lcrag = LCRAG()
lcrag.process_pdf("./celex_02001L0095_jan.pdf")
answer = lcrag.get_answer("summary the document")
print(answer)
```

> The document focuses on the requirements and mandates for setting product risks, the European standards that should be adopted, and the process for publishing references in the Official Journal of the European Communities. Additionally, it discusses the procedure for implementing the Directive in accordance with the advisory procedure provided for in Article 15(3) and the involvement of a Committee to assist the Commission. It also mentions the period set by Article 5(6) of Decision 1999/468/EC for making decisions regarding product standards. Lastly, it covers other obligations for producers and distributors under the Directive 92/59/EEC. 

```python
lcrag.get_answer("what are the requirements and mandates for setting product risks?")
```

> 'The requirements and mandates for setting product risks include providing consumers with relevant information about inherent risks, adopting measures commensurate with the characteristics of products supplied, requiring due care from distributors to ensure compliance with safety requirements, participating in monitoring the safety of products placed on the market, promptly informing competent authorities about incompatible risks with general safety requirements, conducting safe checks and tests on product safety properties, providing necessary information, labeling products with warnings where needed, issuing special warnings for certain persons or conditions, and ordering warning for dangerous products. Additionally, producers and distributors must provide the required information to competent authorities upon request.'

### ref.

- [jupyter notebook code  (github)](https://github.com/knachinen/rag_langchain_ollama)
- [sample document  (EUR-Lex)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A02001L0095-20100101)
