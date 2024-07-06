



PDF Chat with Multiple Sessions:
This project implements a conversational AI system that can answer questions about a PDF document across multiple chat sessions. It uses various libraries from the LangChain ecosystem to process the PDF, create embeddings, perform retrieval, and generate responses.

Dependencies:

langchain
langchain_groq
langchain_community
langchain_huggingface
langchain_text_splitters
SKLearn

Key Components:

1. The PDF is loaded and split into smaller chunks for processing.
   loader = PyPDFLoader(file_path)
   docs = loader.load()
   text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
   splits = text_splitter.split_documents(docs)

2. Document chunks are embedded and stored in a vector database for efficient retrieval.
   vectorstore = SKLearnVectorStore.from_documents(documents=splits, embedding=HuggingFaceEmbeddings())

3. A retriever that takes chat history into account when fetching relevant documents.
   history_aware_retriever = create_history_aware_retriever(llm, retriever, contextualize_q_prompt)

4. A chain that generates answers based on retrieved documents and the question.
   question_answer_chain = create_stuff_documents_chain(llm, qa_prompt)

5. Combines retrieval and question-answering into a single chain.
   rag_chain = create_retrieval_chain(history_aware_retriever, question_answer_chain)

6. Manages chat history for multiple sessions.
   store = {}
   def get_session_history(session_id: str) -> BaseChatMessageHistory:
       if session_id not in store:
           store[session_id] = ChatMessageHistory()
       return store[session_id]
7. Wraps the RAG chain with session management capabilities.
   conversational_rag_chain = RunnableWithMessageHistory(
    rag_chain,
    get_session_history,
    input_messages_key="input",
    history_messages_key="chat_history",
    output_messages_key="answer",)


To use the system, invoke the conversational_rag_chain with a question and a session ID:

response = conversational_rag_chain.invoke(
    {"input": "Your question here"},
    config={"configurable": {"session_id": "unique_session_id"}}
)
print(response["answer"])
